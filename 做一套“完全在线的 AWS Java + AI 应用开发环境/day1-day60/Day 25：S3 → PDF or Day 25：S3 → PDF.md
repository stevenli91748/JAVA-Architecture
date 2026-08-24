# Day 25：S3 → PDF/Markdown → Chunk → Embedding → pgvector 自动知识库

今天把 Day 24 的“手工 RAG”升级成真正可用的 **自动知识库 ingestion pipeline**。

最终实现：

```text
用户上传 PDF / Markdown
          ↓
         S3
          ↓
   Ingestion Service
          ↓
      Document Parser
          ↓
       Chunking
          ↓
      Embedding
          ↓
      pgvector
          ↓
      Knowledge Base
          ↓
    Spring AI RAG
          ↓
       Bedrock
```

---

# 1. 今天最终目标

完成：

```text
[✓] S3 knowledge bucket
[✓] PDF / Markdown 上传
[✓] S3 Event
[✓] SQS
[✓] Java Ingestion Worker
[✓] Document Parsing
[✓] Chunking
[✓] Metadata
[✓] Embedding
[✓] pgvector
[✓] RAG 查询
[✓] 删除文档
[✓] 可观测性
```

今天的核心思想：

> **不要让用户请求线程直接负责 PDF → Embedding → Vector DB。**

应该异步处理。

---

# 2. 为什么使用 S3 + SQS

不要：

```text
POST /upload
    ↓
PDF
    ↓
Parse
    ↓
Embedding
    ↓
pgvector
    ↓
Response
```

因为 PDF 可能：

```text
50 MB
200 pages
```

处理可能需要几十秒。

正确：

```text
Upload
 ↓
S3
 ↓
SQS
 ↓
Worker
 ↓
Embedding
 ↓
pgvector
```

用户上传以后立即得到：

```json
{
  "status": "PROCESSING"
}
```

---

# 3. 最终架构

```text
                         React
                           │
                           ▼
                     Spring Boot API
                           │
                           ▼
                          S3
                           │
                    Object Created
                           │
                           ▼
                         SQS
                           │
                           ▼
                ECS Ingestion Worker
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
        PDF Parser      Chunker       Metadata
                           │
                           ▼
                       Embedding
                           │
                           ▼
                      PostgreSQL
                       pgvector
                           │
                           ▼
                       RAG Query
                           │
                           ▼
                       Bedrock
```

---

# 4. S3 Bucket

建立：

```text
java-ai-platform-dev-knowledge
```

目录：

```text
knowledge/
├── aws/
├── java/
├── spring/
├── company/
└── uploaded/
```

例如：

```text
knowledge/aws/ecs-guide.pdf
```

---

# 5. S3 不开放公网

保持：

```text
Block Public Access = ON
```

不要：

```text
S3
 ↓
Public
```

访问方式：

```text
React
 ↓
Spring Boot
 ↓
Pre-signed URL
 ↓
S3
```

---

# 6. 为什么不用 React 直接传 AWS Credentials

错误：

```text
React
 ↓
AWS Access Key
 ↓
S3
```

正确：

```text
React
 ↓
Spring Boot
 ↓
Pre-signed URL
 ↓
S3
```

这样浏览器永远不拿 AWS 长期密钥。

---

# 7. Upload API

创建：

```text
POST /api/documents/upload-url
```

请求：

```json
{
  "fileName": "aws-ecs.pdf",
  "contentType": "application/pdf"
}
```

返回：

```json
{
  "uploadUrl": "https://...",
  "documentId": "doc-123"
}
```

React：

```text
Browser
 ↓
POST /upload-url
 ↓
Spring Boot
 ↓
S3 Presigned URL
 ↓
Browser PUT
 ↓
S3
```

---

# 8. 为什么使用 Pre-signed URL

优点：

```text
Spring Boot
```

不需要接收：

```text
100 MB PDF
```

数据路径变成：

```text
Browser
   │
   └──────────────→ S3
```

而不是：

```text
Browser
   ↓
ECS
   ↓
S3
```

降低 ECS 带宽和 CPU 压力。

---

# 9. S3 Object Metadata

上传时可以设置：

```text
documentId
tenantId
contentType
uploadedBy
```

例如：

```text
doc-123
tenant-a
application/pdf
user-456
```

不过真正的授权信息建议同时保存在数据库，不能只依赖可由客户端控制的 metadata。

---

# 10. S3 Event

文件：

```text
s3://knowledge/knowledge/aws/ecs.pdf
```

上传：

```text
ObjectCreated
```

触发：

```text
S3
 ↓
SQS
```

推荐：

**S3 → SQS → Worker**

而不是：

```text
S3
 ↓
Lambda
 ↓
整个 PDF RAG
```

---

# 11. 为什么 SQS

SQS 可以提供：

```text
Queue
Retry
Visibility Timeout
Dead Letter Queue
```

例如：

```text
PDF 处理失败
      ↓
Retry
      ↓
还是失败
      ↓
DLQ
```

这比同步调用可靠很多。

---

# 12. SQS

创建：

```text
java-ai-platform-dev-ingestion
```

同时：

```text
java-ai-platform-dev-ingestion-dlq
```

结构：

```text
S3
 ↓
SQS
 │
 ├── Success → Worker
 │
 └── Failure → Retry
                  │
                  ▼
                 DLQ
```

---

# 13. Visibility Timeout

假设：

```text
PDF ingestion
需要 120 秒
```

SQS：

```text
Visibility Timeout
```

应该明显大于最大处理时间。

例如：

```text
5 minutes
```

具体值根据实际 ingestion duration 设置。

---

# 14. Java Worker

这里有两个选择。

### 方案 A

Spring Boot 主服务：

```text
API
+
Worker
```

### 方案 B

独立 ECS Service：

```text
ECS
├── backend-api
└── ingestion-worker
```

推荐：

**方案 B。**

---

# 15. 为什么 Worker 独立

API：

```text
低延迟
```

Worker：

```text
高 CPU
长时间
异步
```

如果混在一起：

```text
PDF processing
 ↓
CPU 100%
 ↓
Chat API
 ↓
变慢
```

独立以后：

```text
API ECS
    │
    └── 用户请求

Worker ECS
    │
    └── PDF ingestion
```

---

# 16. Worker 架构

```text
SQS
 ↓
Ingestion Worker
 │
 ├── Download S3
 ├── Parse
 ├── Clean
 ├── Chunk
 ├── Embedding
 └── pgvector
```

---

# 17. Spring Boot Worker

建立：

```text
IngestionWorker
```

监听 SQS。

使用 AWS SDK / Spring Cloud AWS 等方式消费消息。

例如概念：

```java
@SqsListener("java-ai-platform-dev-ingestion")
public void process(String message) {

    ingestionService.process(message);
}
```

具体注解和依赖随你采用的 Spring Cloud AWS 版本而变化。

---

# 18. SQS Message

建议消息：

```json
{
  "eventType": "DOCUMENT_CREATED",
  "documentId": "doc-123",
  "bucket": "java-ai-platform-dev-knowledge",
  "key": "knowledge/aws/ecs.pdf",
  "tenantId": "tenant-a"
}
```

不要只传：

```text
filename
```

需要：

```text
documentId
bucket
key
tenantId
```

---

# 19. Document 状态表

今天非常建议增加：

```sql
CREATE TABLE documents (
    id UUID PRIMARY KEY,
    tenant_id VARCHAR(100) NOT NULL,
    s3_key TEXT NOT NULL,
    file_name TEXT NOT NULL,
    status VARCHAR(30) NOT NULL,
    error_message TEXT,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

状态：

```text
UPLOADED
PROCESSING
COMPLETED
FAILED
DELETED
```

---

# 20. 为什么需要状态

用户上传：

```text
document.pdf
```

前端显示：

```text
Processing...
```

后台：

```text
UPLOADED
 ↓
PROCESSING
 ↓
COMPLETED
```

失败：

```text
PROCESSING
 ↓
FAILED
```

这样用户知道：

```text
PDF 到底有没有进入知识库。
```

---

# 21. Chunk 表

可以单独建立：

```sql
CREATE TABLE document_chunks (
    id BIGSERIAL PRIMARY KEY,

    document_id UUID NOT NULL,

    chunk_index INTEGER NOT NULL,

    content TEXT NOT NULL,

    metadata JSONB,

    embedding VECTOR(1536),

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

这里：

```text
VECTOR(1536)
```

依然只是示例。

必须匹配你的 Embedding Model。

---

# 22. Document 和 Chunk

关系：

```text
documents
    │
    │ 1
    ▼
document_chunks
    │
    ├── chunk 0
    ├── chunk 1
    ├── chunk 2
    └── ...
```

一个 PDF：

```text
AWS ECS.pdf
```

可能：

```text
1 Document
+
120 Chunks
```

---

# 23. PDF Parsing

Java 可以使用：

```text
Apache PDFBox
```

基本流程：

```text
PDF
 ↓
PDFBox
 ↓
Text
 ↓
Document
```

Maven：

```xml
<dependency>
    <groupId>org.apache.pdfbox</groupId>
    <artifactId>pdfbox</artifactId>
</dependency>
```

版本请跟随当前稳定版本。

---

# 24. Markdown

Markdown 简单很多：

```text
.md
 ↓
Text
 ↓
Chunk
```

保留：

```text
Heading
Paragraph
Code Block
```

对于技术文档特别重要。

---

# 25. 不要简单按字符切 PDF

错误：

```text
每 1000 characters
切一次
```

容易把：

```text
标题
代码
表格
段落
```

拆坏。

推荐：

```text
Document
 ↓
Paragraph
 ↓
Section
 ↓
Chunk
```

---

# 26. 第一版 Chunk 策略

今天先：

```text
Chunk:
600–800 tokens

Overlap:
80–120 tokens
```

例如：

```text
Chunk 1
tokens 1–700

Chunk 2
tokens 601–1300

Chunk 3
tokens 1201–1900
```

以后通过评估调整。

---

# 27. Metadata

每个 Chunk：

```json
{
  "documentId": "doc-123",
  "tenantId": "tenant-a",
  "source": "aws-ecs.pdf",
  "page": 12,
  "chunk": 5,
  "category": "aws"
}
```

非常重要：

```text
tenantId
```

以后做多租户安全时必须用。

---

# 28. PDF Page Metadata

PDF：

```text
Page 1
Page 2
Page 3
```

Chunk：

```text
Chunk 0
page = 1

Chunk 1
page = 1

Chunk 2
page = 2
```

最终用户看到：

```text
Source:
aws-ecs.pdf
Page 12
```

比单纯：

```text
Source:
aws-ecs.pdf
```

有用得多。

---

# 29. Embedding

流程：

```text
Chunk
 ↓
Embedding Model
 ↓
Vector
```

例如：

```text
chunk
 ↓
[0.12, -0.33, 0.77, ...]
```

然后：

```text
pgvector
```

---

# 30. Batch Embedding

不要：

```text
1000 chunks
 ↓
1000 HTTP requests
```

最好：

```text
1000 chunks
 ↓
Batch
 ↓
Embedding API
```

可以明显减少：

```text
Latency
Network overhead
```

并降低失败概率。

具体 batch size 要根据 Embedding API 的请求限制调整。

---

# 31. Ingestion Service

建议：

```java
public void ingest(DocumentFile file) {

    updateStatus(PROCESSING);

    List<Document> documents =
        parse(file);

    List<Document> chunks =
        chunk(documents);

    vectorStore.add(chunks);

    updateStatus(COMPLETED);
}
```

如果失败：

```java
catch (Exception e) {

    updateStatus(FAILED);

    throw e;
}
```

让 SQS 负责 retry。

---

# 32. 幂等性

这是今天非常重要的一点。

假设：

```text
SQS Message
```

被处理两次。

如果：

```text
Embedding
 ↓
pgvector
```

执行两遍：

```text
重复 Chunks
```

所以必须做：

**Idempotency。**

---

# 33. Document ID

例如：

```text
documentId = UUID
```

Chunk：

```text
documentId
+
chunkIndex
```

建立唯一约束：

```sql
CREATE UNIQUE INDEX
document_chunk_unique
ON document_chunks(document_id, chunk_index);
```

这样：

```text
doc-123 + chunk-5
```

只能出现一次。

---

# 34. Re-ingestion

如果用户重新上传：

```text
aws-ecs.pdf
```

不要简单：

```text
INSERT
```

而应该：

```text
Old chunks
 ↓
Delete
 ↓
New chunks
 ↓
Embedding
```

流程：

```text
Document Version 1
        ↓
Re-upload
        ↓
Version 2
        ↓
Re-ingest
```

---

# 35. Document Version

建议：

```text
documents
```

增加：

```text
version
```

例如：

```text
doc-123
version 1

doc-123
version 2
```

这样以后可以：

```text
rollback
audit
compare
```

---

# 36. 删除文档

用户：

```text
Delete document
```

应该：

```text
S3 Object
 ↓
Delete
```

同时：

```text
pgvector chunks
 ↓
Delete
```

不要只删除 S3。

否则：

```text
文件不存在
但 RAG 仍然回答旧内容
```

---

# 37. Delete Flow

```text
DELETE /api/documents/{id}
           │
           ▼
       PostgreSQL
           │
      Mark DELETED
           │
     ┌─────┴─────┐
     ▼           ▼
    S3        Vector DB
  Delete       Delete
```

---

# 38. RAG Query

现在查询：

```text
POST /api/chat
```

流程：

```text
Question
 ↓
Embedding
 ↓
Metadata Filter
 ↓
Vector Search
 ↓
Top-K
 ↓
Context
 ↓
Bedrock
```

---

# 39. Tenant Filter

例如：

```text
tenantId = tenant-a
```

查询：

```text
tenant-a
 ↓
Vector Search
 ↓
Only tenant-a chunks
```

千万不要：

```text
Vector Search
 ↓
Top 5
 ↓
再检查 tenant
```

应该：

```text
Metadata Filter
 ↓
Vector Search
```

从查询层面限制。

---

# 40. S3 Security

Worker Task Role：

```text
s3:GetObject
```

只允许：

```text
arn:aws:s3:::java-ai-platform-dev-knowledge/knowledge/*
```

不要：

```text
s3:*
Resource: *
```

---

# 41. Worker IAM

Worker 需要：

```text
SQS ReceiveMessage
SQS DeleteMessage
SQS ChangeMessageVisibility
S3 GetObject
Secrets Manager GetSecretValue
Bedrock InvokeModel
RDS connectivity
X-Ray
```

但不需要：

```text
ECS UpdateService
CloudFront
Route53
```

职责分离。

---

# 42. Worker 的网络

Worker：

```text
Private ECS
```

访问：

```text
S3
SQS
Bedrock
Secrets Manager
```

可以通过：

```text
VPC Endpoint
```

或：

```text
NAT Gateway
```

Day 17 的网络设计可以继续使用。

生产环境可以逐步减少不必要的 NAT 出站流量。

---

# 43. SQS DLQ

如果：

```text
PDF
 ↓
Parsing failed
```

SQS：

```text
Retry 1
Retry 2
Retry 3
```

仍然失败：

```text
DLQ
```

例如：

```text
java-ai-platform-dev-ingestion-dlq
```

---

# 44. CloudWatch Alarm

今天增加：

```text
ApproximateNumberOfMessagesVisible
```

如果：

```text
Queue > 100
```

说明：

```text
Worker 处理不过来
```

报警：

```text
SQS
 ↓
CloudWatch
 ↓
SNS
```

---

# 45. Worker Auto Scaling

后面可以：

```text
Queue depth
 ↓
ECS Service Auto Scaling
```

例如：

```text
Queue = 10
Worker = 1

Queue = 1000
Worker = 5
```

这就是：

**Event-driven scaling。**

---

# 46. 一个非常重要的设计

不要：

```text
PDF upload
 ↓
API ECS
 ↓
Processing
```

推荐：

```text
PDF upload
 ↓
S3
 ↓
SQS
 ↓
Worker ECS
```

API：

```text
低延迟
```

Worker：

```text
异步
可扩展
可重试
```

这已经开始进入企业级架构。

---

# 47. RAG Observability

Day 22 已经有 Trace。

今天加：

```text
document.ingestion
```

Trace：

```text
SQS message
 │
 ├── s3.download
 ├── pdf.parse
 ├── chunk
 ├── embedding
 └── vector.insert
```

例如：

```text
Total ingestion: 8.4s

S3 download: 0.5s
PDF parse:    1.2s
Chunk:        0.3s
Embedding:    5.9s
DB insert:    0.5s
```

这样很容易找到瓶颈。

---

# 48. AI 成本监控

今天开始记录：

```text
document
chunks
embedding tokens
embedding requests
```

例如：

```text
Document:
AWS Guide

Chunks:
350

Embedding:
350 requests / batches

Estimated tokens:
220K
```

以后可以统计：

```text
Cost / Document
Cost / Tenant
Cost / Month
```

---

# 49. 推荐数据库设计

最终：

```text
documents
│
├── id
├── tenant_id
├── file_name
├── s3_key
├── version
├── status
└── timestamps

document_chunks
│
├── id
├── document_id
├── chunk_index
├── content
├── metadata
├── embedding
└── timestamps
```

---

# 50. API 设计

今天可以建立：

```text
POST   /api/documents/upload-url

GET    /api/documents

GET    /api/documents/{id}

DELETE /api/documents/{id}

POST   /api/chat
```

例如：

```text
GET /api/documents
```

返回：

```json
[
  {
    "id": "doc-123",
    "fileName": "aws-ecs.pdf",
    "status": "COMPLETED"
  }
]
```

---

# 51. React 页面

今天前端可以先做：

```text
Knowledge Base
```

页面：

```text
┌──────────────────────────────────┐
│ Knowledge Base                   │
│                                  │
│ [ Upload PDF ]                  │
│                                  │
│ aws-ecs.pdf       COMPLETED     │
│ spring-ai.pdf     PROCESSING    │
│ rag-guide.pdf     FAILED        │
│                                  │
└──────────────────────────────────┘
```

---

# 52. Upload UX

流程：

```text
Click Upload
      ↓
Select PDF
      ↓
Get Presigned URL
      ↓
Upload S3
      ↓
Document = PROCESSING
      ↓
Poll / WebSocket
      ↓
COMPLETED
```

第一版：

```text
Polling
```

就够了。

以后再做：

```text
WebSocket
SSE
```

---

# 53. RAG 测试

上传：

```text
aws-ecs.pdf
```

等待：

```text
COMPLETED
```

然后问：

```text
"What is ECS Fargate?"
```

应该：

```text
Vector Search
 ↓
找到 aws-ecs.pdf
 ↓
Bedrock
 ↓
Answer
```

Response：

```json
{
  "answer": "...",
  "sources": [
    {
      "fileName": "aws-ecs.pdf",
      "page": 8
    }
  ]
}
```

---

# 54. 测试“知识库没有答案”

问：

```text
"What is the capital of Mars?"
```

如果知识库没有相关内容，不应该：

```text
LLM 自由发挥
```

应该让系统：

```text
No relevant context
```

然后：

```text
"I don't have enough information in the knowledge base."
```

这是 RAG 非常重要的 **grounding**。

---

# 55. Retrieval Threshold

例如：

```text
similarity score
```

低于：

```text
0.7
```

不进入 Context。

但这个：

```text
0.7
```

只是起始实验值。

必须根据你的 Embedding Model 和数据集做评估。

---

# 56. RAG Evaluation

今天先建立意识：

```text
RAG
 ≠
能搜索
```

真正需要评估：

```text
Retrieval Precision
Retrieval Recall
Answer Faithfulness
Answer Relevance
Latency
Cost
```

以后可以建立：

```text
Golden Dataset
```

例如：

```text
Question
Expected Source
Expected Answer
```

自动测试 RAG。

---

# 57. Day 25 最终架构

```text
                           User
                             │
                             ▼
                           React
                             │
                             ▼
                    Spring Boot API
                             │
                    Presigned URL
                             │
                             ▼
                            S3
                             │
                      Object Created
                             │
                             ▼
                            SQS
                             │
                             ▼
                    Ingestion Worker
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
           Download        Parse          Chunk
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                         Embedding
                             │
                             ▼
                        pgvector
                             │
                             ▼
                       Knowledge Base
                             │
                             ▼
                         RAG Query
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
               Vector Search       Bedrock
                    │                 │
                    └────────┬────────┘
                             ▼
                           Answer
                             │
                             ▼
                            React
```

---

# 58. Day 25 检查清单

```text
[ ] S3 Knowledge Bucket
[ ] Private S3
[ ] Presigned URL
[ ] Upload API
[ ] S3 Event
[ ] SQS
[ ] DLQ
[ ] Ingestion Worker
[ ] PDFBox
[ ] Markdown Parser
[ ] Chunking
[ ] Metadata
[ ] Embedding
[ ] pgvector
[ ] Document Status
[ ] Idempotency
[ ] Document Version
[ ] Delete Flow
[ ] Tenant ID
[ ] S3 Least Privilege
[ ] SQS Least Privilege
[ ] Worker IAM
[ ] Queue Alarm
[ ] RAG Trace
[ ] Source Citation
[ ] Retrieval Threshold
```

---

# Day 25 完成后

你的平台已经从：

```text
AWS Java Web Application
```

升级成：

```text
AWS Java AI Platform
```

核心能力：

```text
React
  ↓
Spring Boot
  ↓
Spring AI
  ↓
RAG
  ↓
pgvector
  ↓
Bedrock
```

知识库：

```text
PDF / Markdown
      ↓
S3
      ↓
SQS
      ↓
Java Worker
      ↓
Chunk
      ↓
Embedding
      ↓
pgvector
```

而且已经具备：

```text
CI/CD
Security
Secrets
Observability
Tracing
Async Processing
```

**Day 26** 建议进入一个非常关键的企业能力：**RAG 检索质量优化**——Hybrid Search（Vector + PostgreSQL Full-Text Search）、Reranking、Metadata Filtering、Top-K、Chunk Size、Embedding 对比，并建立第一套 RAG Evaluation Dataset。这样你的 RAG 才会从“能回答”进入“回答得准”。
