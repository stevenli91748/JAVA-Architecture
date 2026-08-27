# Day 48：企业 RAG 文档处理流水线 —— S3 → SQS → EKS → Embedding → pgvector

Day 47 已经完成数据库层：

```text
EKS
 ↓
Spring Boot
 ↓
RDS PostgreSQL
 ↓
pgvector
+
Redis
```

今天把**完整企业 RAG ingestion pipeline** 做出来。

最终实现：

```text
用户上传 PDF / Word / Excel
          ↓
         S3
          ↓
      EventBridge
          ↓
         SQS
          ↓
     EKS RAG Worker
          ↓
     文档解析
          ↓
       Chunking
          ↓
      Embedding
          ↓
     pgvector
          ↓
     RAG Search
          ↓
      Spring AI
          ↓
        LLM
```

---

# 1. Day 48 的最终目标

今天完成这条完整链路：

```text
PDF
 │
 ▼
S3
 │
 ▼
EventBridge
 │
 ▼
SQS
 │
 ▼
KEDA
 │
 ▼
RAG Worker
 │
 ├── PDF Parser
 ├── Text Extraction
 ├── Chunking
 ├── Metadata
 └── Embedding
       │
       ▼
   PostgreSQL
    + pgvector
       │
       ▼
   Vector Search
```

然后：

```text
User Question
      ↓
Spring Boot
      ↓
Embedding
      ↓
pgvector
      ↓
Top-K Chunks
      ↓
Prompt
      ↓
LLM
      ↓
Answer
```

---

# 2. 为什么不能让 API 直接处理 PDF？

不推荐：

```text
User
 ↓
Spring Boot
 ↓
Upload PDF
 ↓
Parse
 ↓
Chunk
 ↓
Embedding
 ↓
PostgreSQL
```

因为 PDF 可能：

```text
1 MB
10 MB
100 MB
500 MB
```

处理时间可能：

```text
几秒
几分钟
```

API 会：

```text
Timeout
Memory ↑
CPU ↑
Pod 被拖垮
```

---

# 3. 企业正确架构

```text
User
 ↓
API
 ↓
S3
 ↓
SQS
 ↓
Worker
```

API 只负责：

```text
上传
+
创建 Job
```

Worker 负责：

```text
解析
+
Embedding
+
Vector DB
```

---

# 4. Upload 流程

```text
Browser
   │
   ▼
Spring Boot
   │
   ▼
Pre-signed URL
   │
   ▼
S3
```

浏览器实际上可以：

```text
Browser
   │
   │ PUT
   ▼
S3
```

不需要：

```text
Browser
 ↓
Spring Boot
 ↓
S3
```

这样 API 不需要搬运大文件。

---

# 5. Pre-signed URL

Spring Boot：

```text
POST /api/documents/upload-url
```

返回：

```json
{
  "uploadUrl": "...",
  "documentId": "12345"
}
```

Browser：

```text
PUT uploadUrl
```

直接上传：

```text
S3
```

---

# 6. S3 Bucket

例如：

```text
company-ai-documents-prod
```

目录：

```text
tenant-a/
    documents/
        doc-001/
            v1/
                handbook.pdf
```

不要：

```text
company-ai-documents-prod/
    random.pdf
```

多租户系统一定要有明确的数据隔离设计。

---

# 7. S3 Object Key

推荐：

```text
{tenantId}/documents/{documentId}/v{version}/{filename}
```

例如：

```text
tenant-a/documents/123/v1/handbook.pdf
```

这样 Worker 收到事件后可以直接知道：

```text
Tenant
Document
Version
File
```

---

# 8. S3 安全

必须：

```text
Block Public Access
```

并且：

```text
S3
 ↓
IAM
 ↓
Only Worker / API
```

不要：

```text
Internet
 ↓
Public S3
```

---

# 9. S3 Encryption

生产：

```text
S3
 ↓
Encryption
```

可以使用：

```text
SSE-S3
```

或者企业需要更强密钥控制时：

```text
SSE-KMS
```

如果使用 KMS，还需要正确配置：

```text
IAM
+
KMS Key Policy
```

---

# 10. S3 Event

文件上传：

```text
S3
 ↓
ObjectCreated
```

然后：

```text
EventBridge
```

负责事件路由。

---

# 11. 为什么推荐 EventBridge？

可以：

```text
S3
 ↓
EventBridge
 ├── SQS
 ├── Lambda
 ├── Step Functions
 └── Other Targets
```

以后系统扩展很方便。

例如：

```text
Document Uploaded
        │
        ├── RAG Queue
        ├── Malware Scan
        ├── Audit
        └── Notification
```

---

# 12. SQS

建立：

```text
rag-ingestion
```

以及：

```text
rag-ingestion-dlq
```

架构：

```text
S3
 ↓
EventBridge
 ↓
SQS
 ├── Main Queue
 └── DLQ
```

---

# 13. 为什么必须有 DLQ？

假设：

```text
Bad PDF
```

Worker：

```text
Parse
 ↓
ERROR
```

如果无限重试：

```text
SQS
 ↓
Worker
 ↓
ERROR
 ↓
SQS
 ↓
Worker
 ↓
ERROR
```

浪费：

```text
CPU
LLM Cost
Embedding Cost
```

所以：

```text
Retry
 ↓
Retry
 ↓
Retry
 ↓
DLQ
```

---

# 14. SQS Message

建议事件消息最终转换成业务消息，例如：

```json
{
  "eventType": "DOCUMENT_UPLOADED",
  "tenantId": "tenant-a",
  "documentId": "12345",
  "version": 1,
  "bucket": "company-ai-documents-prod",
  "objectKey": "tenant-a/documents/12345/v1/handbook.pdf"
}
```

---

# 15. 为什么不要把 PDF 放进 SQS？

错误：

```text
PDF
 ↓
SQS Message
```

正确：

```text
S3
 ↓
SQS
 ↓
S3 Object Key
```

SQS 只传：

```text
Metadata
```

文件仍然在：

```text
S3
```

---

# 16. Worker

EKS：

```text
rag-worker
```

负责：

```text
SQS
 ↓
Download S3
 ↓
Parse
 ↓
Chunk
 ↓
Embedding
 ↓
PostgreSQL
```

---

# 17. Worker 架构

```text
              SQS
               │
               ▼
          RAG Worker
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
      S3      Parser   Metadata
               │
               ▼
            Chunking
               │
               ▼
           Embedding
               │
               ▼
          pgvector
```

---

# 18. Worker 不需要 Ingress

这是一个非常重要的架构原则。

AI API：

```text
Internet
 ↓
ALB
 ↓
Ingress
 ↓
Service
 ↓
Pod
```

Worker：

```text
SQS
 ↓
Pod
```

Worker 不需要：

```text
ALB
Ingress
Public IP
```

---

# 19. KEDA

Day 46 已经学过：

```text
SQS
 ↓
KEDA
 ↓
Worker
```

今天正式用到。

例如：

```text
Queue = 0
 ↓
2 Workers
```

上传大量文件：

```text
Queue = 1000
 ↓
KEDA
 ↓
10 Workers
```

---

# 20. Worker IAM

Worker：

```text
S3
SQS
Bedrock
RDS
```

但是严格最小权限。

例如：

```text
S3:
GetObject

SQS:
ReceiveMessage
DeleteMessage
GetQueueAttributes

Bedrock:
InvokeModel
```

---

# 21. Worker 不需要 S3 Delete

如果 Worker 只是：

```text
Download
```

就只给：

```text
s3:GetObject
```

不要：

```text
s3:DeleteObject
```

---

# 22. Document Processing State

PostgreSQL：

```text
documents
```

增加：

```text
status
```

例如：

```text
UPLOADED
PROCESSING
COMPLETED
FAILED
```

---

# 23. documents 表

```sql
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    name TEXT NOT NULL,

    object_key TEXT NOT NULL,

    version INTEGER NOT NULL DEFAULT 1,

    status VARCHAR(30) NOT NULL,

    error_message TEXT,

    created_at TIMESTAMP NOT NULL,

    updated_at TIMESTAMP NOT NULL
);
```

---

# 24. 完整状态机

```text
             UPLOADED
                │
                ▼
            PROCESSING
           /          \
          ▼            ▼
     COMPLETED        FAILED
                        │
                        ▼
                      Retry
                        │
                        ▼
                   PROCESSING
```

---

# 25. 为什么需要状态？

用户上传：

```text
handbook.pdf
```

UI 可以显示：

```text
Processing...
```

然后：

```text
Completed
```

如果失败：

```text
Failed
```

用户可以：

```text
Retry
```

---

# 26. PDF Parsing

Java 常用：

```text
Apache PDFBox
```

流程：

```text
PDF
 ↓
PDFBox
 ↓
Text
```

例如：

```java
PDDocument document =
    Loader.loadPDF(file);

PDFTextStripper stripper =
    new PDFTextStripper();

String text =
    stripper.getText(document);
```

生产环境还需要考虑：

```text
Scanned PDF
Encrypted PDF
Corrupt PDF
Large PDF
Tables
Images
```

---

# 27. Word

可以使用：

```text
Apache POI
```

支持：

```text
DOC
DOCX
```

流程：

```text
DOCX
 ↓
Apache POI
 ↓
Text
```

---

# 28. Excel

Apache POI：

```text
XLS
XLSX
```

然后：

```text
Sheet
 ↓
Rows
 ↓
Cells
 ↓
Text
```

但 Excel 不应该简单粗暴地：

```text
所有 Cell
 ↓
拼成一个巨大 String
```

最好保留：

```text
Workbook
Sheet
Row
Column
```

等 metadata。

---

# 29. Chunking

原始文档：

```text
100 pages
```

不能：

```text
100 pages
 ↓
LLM
```

需要：

```text
Document
 ↓
Chunks
```

例如：

```text
Chunk 1
Chunk 2
Chunk 3
...
Chunk 500
```

---

# 30. Chunk 大小

例如：

```text
Chunk Size = 500~1000 tokens
Overlap = 50~150 tokens
```

这只是**实验起点**。

真正参数应该通过：

```text
Retrieval Evaluation
```

确定。

---

# 31. 为什么需要 Overlap？

例如：

```text
Chunk A
```

结尾：

```text
"Employees are eligible after..."
```

下一段：

```text
"...after 90 days of employment."
```

如果没有 overlap：

```text
语义被切开
```

Overlap：

```text
Chunk A
      ↓
     overlap
      ↓
Chunk B
```

可以减少语义断裂。

---

# 32. Chunk 不应该只按字符切

错误：

```text
String.substring(
    0,
    1000
)
```

可能：

```text
句子被切断
表格被切断
代码被切断
```

更好的策略：

```text
Document
 ↓
Section
 ↓
Paragraph
 ↓
Sentence
 ↓
Token Limit
```

---

# 33. Metadata

每个 Chunk 建议保存：

```text
tenant_id
document_id
version
chunk_index
content
page
section
source
created_at
```

例如：

```json
{
  "page": 12,
  "section": "Vacation Policy",
  "source": "handbook.pdf"
}
```

---

# 34. Embedding

Chunk：

```text
"Employees receive 15 days..."
```

发送：

```text
Embedding Model
```

得到：

```text
Vector
```

例如：

```text
[0.012, -0.32, 0.88, ...]
```

然后：

```text
PostgreSQL
 ↓
pgvector
```

---

# 35. Embedding 不要一次发送整个文档

错误：

```text
100 pages
 ↓
Embedding
```

正确：

```text
Document
 ↓
500 Chunks
 ↓
Embedding
 ↓
500 Vectors
```

---

# 36. Embedding Batch

可以：

```text
100 chunks
 ↓
Embedding API
```

批量处理通常比：

```text
1 chunk
 ↓
1 API request
```

更有效率。

但要遵守模型的输入限制和 API 限额。

---

# 37. Embedding Cache

如果同一个 chunk：

```text
重复处理
```

不要重新计算：

```text
Embedding
```

可以使用：

```text
Redis
```

或者：

```text
content_hash
```

---

# 38. Content Hash

例如：

```text
SHA-256
```

：

```text
content
 ↓
SHA-256
 ↓
abc123...
```

数据库：

```text
content_hash
```

如果：

```text
hash 相同
```

说明内容没有变化。

---

# 39. Document Versioning

用户上传：

```text
handbook.pdf
```

第一版：

```text
v1
```

修改后：

```text
v2
```

数据库：

```text
document_id = 123
version = 2
```

S3：

```text
v1/
v2/
```

---

# 40. 为什么需要 Version？

避免：

```text
旧 Vector
+
新 Vector
```

混在一起。

检索时：

```text
WHERE
document_id = ?
AND
version = current_version
```

或者在 ingestion 完成后切换 active version。

---

# 41. 更好的 Version Strategy

```text
Document
│
├── v1
│   └── chunks
│
├── v2
│   └── chunks
│
└── v3
    └── chunks
```

数据库：

```text
active_version = 3
```

RAG：

```text
Only query active version
```

---

# 42. RAG 查询

用户：

> What is the vacation policy?

流程：

```text
Question
 ↓
Embedding
 ↓
pgvector
 ↓
Top 20
 ↓
Reranker
 ↓
Top 5
 ↓
Prompt
 ↓
LLM
```

---

# 43. Prompt

最终：

```text
Answer the question using the provided context.

Context:
[Chunk 1]

[Chunk 2]

[Chunk 3]

Question:
What is the vacation policy?
```

---

# 44. 必须保留 Citation

RAG 企业应用不要只返回：

```text
"You receive 15 days."
```

应该：

```text
"You receive 15 days of vacation."

Source:
Employee Handbook
Page 12
```

---

# 45. Chunk Citation

每个 Chunk 保存：

```text
document_id
page
section
source
```

LLM 回答时：

```text
Answer
+
Sources
```

例如：

```text
Source:
Employee Handbook
Page 12
```

这对企业 AI 非常重要。

---

# 46. Hallucination 控制

Prompt：

```text
Only answer using the provided context.

If the answer is not found,
say that the information
is not available.
```

再配合：

```text
Retrieval score
+
Reranker
+
Evaluation
```

减少幻觉。

---

# 47. RAG Pipeline 最终版本

```text
                      DOCUMENT
                         │
                         ▼
                         S3
                         │
                         ▼
                    EventBridge
                         │
                         ▼
                        SQS
                         │
                         ▼
                       KEDA
                         │
                         ▼
                     RAG Worker
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
           Parse       Chunk      Metadata
             │           │           │
             └───────────┼───────────┘
                         ▼
                    Embedding
                         │
                         ▼
                   PostgreSQL
                         │
                      pgvector
                         │
                         ▼
                      Indexed
                         │
                         │
                  USER QUESTION
                         │
                         ▼
                  Spring Boot
                         │
                         ▼
                    Embedding
                         │
                         ▼
                    Vector Search
                         │
                         ▼
                      Reranker
                         │
                         ▼
                        LLM
                         │
                         ▼
                      Answer
                         │
                         ▼
                      Citation
```

---

# 48. SQS Worker 的幂等性

Day 47 已经提到：

```text
SQS
 ↓
Worker
```

可能重复消息。

所以：

```text
documentId
+
version
```

可以作为 ingestion identity。

数据库：

```sql
CREATE UNIQUE INDEX
idx_document_version
ON documents(
    tenant_id,
    id,
    version
);
```

Chunk 也需要唯一约束设计。

---

# 49. 更好的 Job ID

建议：

```text
ingestion_job_id
```

例如：

```text
job-8f29...
```

数据库：

```text
ingestion_jobs
```

保存：

```text
job_id
document_id
version
status
started_at
completed_at
error_message
```

---

# 50. ingestion_jobs

```sql
CREATE TABLE ingestion_jobs (
    id UUID PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    document_id BIGINT NOT NULL,

    version INTEGER NOT NULL,

    status VARCHAR(30) NOT NULL,

    error_message TEXT,

    started_at TIMESTAMP,

    completed_at TIMESTAMP,

    created_at TIMESTAMP NOT NULL
);
```

---

# 51. Worker 流程

伪代码：

```java
public void process(IngestionMessage message) {

    if (alreadyProcessed(message)) {
        return;
    }

    markProcessing(message);

    try {

        File file =
            s3.download(message.objectKey());

        String text =
            parser.parse(file);

        List<Chunk> chunks =
            chunker.split(text);

        List<Vector> vectors =
            embeddingService.embed(chunks);

        repository.save(
            message,
            chunks,
            vectors
        );

        markCompleted(message);

    } catch (Exception e) {

        markFailed(message);

        throw e;
    }
}
```

---

# 52. 注意事务边界

不要把：

```text
S3 Download
+
Embedding API
+
PostgreSQL
```

全部塞进一个超长数据库 Transaction。

正确：

```text
S3
 ↓
Parse
 ↓
Embedding
 ↓
DB Transaction
 ↓
Commit
```

---

# 53. DB Transaction

最终保存：

```text
documents
+
document_chunks
+
ingestion_job
```

应该尽可能原子。

例如：

```text
BEGIN

save chunks
update job
update document status

COMMIT
```

如果失败：

```text
ROLLBACK
```

---

# 54. Embedding API Failure

如果：

```text
Embedding API
 ↓
429
```

不要直接：

```text
FAILED
```

可以：

```text
Retry
 ↓
Exponential Backoff
```

例如：

```text
1 sec
2 sec
4 sec
8 sec
```

并设置最大次数。

---

# 55. 永久错误

例如：

```text
Invalid PDF
```

重试没有意义。

应该：

```text
FAILED
 ↓
DLQ
```

---

# 56. Temporary Error

例如：

```text
Network timeout
429
5xx
```

适合：

```text
Retry
```

所以 Worker 应该区分：

```text
Transient Error
Permanent Error
```

---

# 57. S3 → EventBridge

逻辑：

```text
ObjectCreated
 ↓
EventBridge Rule
 ↓
SQS
```

Rule 可以过滤：

```text
bucket
+
prefix
+
event type
```

例如只处理：

```text
tenant-a/documents/
```

或者：

```text
.pdf
```

---

# 58. 文件类型

支持：

```text
PDF
DOCX
XLSX
TXT
CSV
```

第一版不要同时做：

```text
PDF
Word
Excel
PPT
HTML
Images
Audio
Video
```

建议：

```text
Day 48
PDF + TXT

后续
DOCX + XLSX
```

---

# 59. Scanned PDF

普通 PDF：

```text
PDF
 ↓
Text Extraction
```

扫描 PDF：

```text
Image
 ↓
OCR
 ↓
Text
```

可以增加：

```text
Amazon Textract
```

或者其他 OCR 服务。

架构：

```text
S3
 ↓
Worker
 ↓
Detect Scanned PDF
 ↓
OCR
 ↓
Chunk
```

---

# 60. 大文件

如果：

```text
PDF = 500MB
```

不要：

```text
S3
 ↓
Memory
 ↓
String
```

应该：

```text
S3
 ↓
Streaming / Temporary File
 ↓
Parser
```

并设置：

```text
Pod Memory
Ephemeral Storage
```

---

# 61. Worker Resource

例如起点：

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "1Gi"

  limits:
    cpu: "2"
    memory: "4Gi"
```

大 PDF 可能需要更高。

必须通过真实文件压测确定。

---

# 62. Worker 的自动扩容

```text
SQS
 ↓
KEDA
 ↓
Workers
```

例如：

```text
Queue = 0
Workers = 2
```

：

```text
Queue = 500
Workers = 10
```

：

```text
Queue = 5000
Workers = 20
```

再由：

```text
Karpenter
```

提供 Node。

---

# 63. Day 48 完整 AWS 架构

```text
                          USER
                            │
                            ▼
                      Spring Boot API
                            │
                     Pre-signed URL
                            │
                            ▼
                           S3
                            │
                     Object Created
                            │
                            ▼
                       EventBridge
                            │
                            ▼
                           SQS
                            │
                            ▼
                          KEDA
                            │
                            ▼
                       EKS Worker
                            │
             ┌──────────────┼──────────────┐
             ▼              ▼              ▼
            S3            Parser         Metadata
                            │
                            ▼
                         Chunking
                            │
                            ▼
                        Embedding
                            │
                            ▼
                     RDS PostgreSQL
                            │
                         pgvector
                            │
                            ▼
                       Vector Index
                            │
                            ▼
                       RAG Search
                            │
                            ▼
                           LLM
                            │
                            ▼
                         Answer
                            │
                            ▼
                         Sources
```

---

# 64. Day 48 项目目录

```text
aws-java-ai-platform/
│
├── backend/
│   └── src/
│
├── rag-worker/
│   ├── parser/
│   │   ├── PdfParser.java
│   │   ├── DocxParser.java
│   │   └── XlsxParser.java
│   │
│   ├── chunking/
│   │   └── DocumentChunker.java
│   │
│   ├── embedding/
│   │   └── EmbeddingService.java
│   │
│   ├── ingestion/
│   │   └── IngestionService.java
│   │
│   └── queue/
│       └── SqsConsumer.java
│
├── infrastructure/
│   └── terraform/
│       ├── s3/
│       ├── sqs/
│       ├── rds/
│       └── iam/
│
├── helm/
│   └── ai-platform/
│       └── templates/
│           ├── ai-api.yaml
│           ├── rag-worker.yaml
│           └── keda.yaml
│
└── .github/
    └── workflows/
```

---

# 65. Day 48 实战任务

### Task 1

创建：

```text
S3 Bucket
```

---

### Task 2

创建：

```text
SQS Main Queue
SQS DLQ
```

---

### Task 3

配置：

```text
S3
 ↓
EventBridge
 ↓
SQS
```

---

### Task 4

创建：

```text
RAG Worker
```

---

### Task 5

配置：

```text
KEDA
 ↓
SQS
 ↓
Worker
```

---

### Task 6

实现：

```text
PDF
 ↓
PDFBox
 ↓
Text
```

---

### Task 7

实现：

```text
Text
 ↓
Chunk
```

---

### Task 8

实现：

```text
Chunk
 ↓
Embedding
```

---

### Task 9

写入：

```text
PostgreSQL
+
pgvector
```

---

### Task 10

实现：

```text
Question
 ↓
Vector Search
 ↓
Top-K
 ↓
LLM
```

---

# 66. Day 48 验收测试

上传：

```text
employee-handbook.pdf
```

应该看到：

```text
S3
 ↓
EventBridge
 ↓
SQS
 ↓
Worker
```

然后：

```text
documents.status
```

从：

```text
UPLOADED
```

变成：

```text
PROCESSING
```

最后：

```text
COMPLETED
```

---

# 67. 查询数据库

```sql
SELECT
    id,
    name,
    status
FROM documents
ORDER BY created_at DESC;
```

看到：

```text
123 | employee-handbook.pdf | COMPLETED
```

然后：

```sql
SELECT COUNT(*)
FROM document_chunks
WHERE document_id = 123;
```

应该大于：

```text
0
```

---

# 68. RAG 最终测试

问：

> What is the vacation policy?

系统：

```text
Question
 ↓
Embedding
 ↓
pgvector
 ↓
Top-K
 ↓
LLM
```

返回：

```text
Employees receive ...
```

并：

```text
Source:
Employee Handbook
Page 12
```

---

# 69. Day 48 必会面试题

### Q1：为什么 S3 + SQS + Worker？

```text
S3
→ File Storage

SQS
→ Async Queue

Worker
→ Heavy Processing
```

---

### Q2：为什么不能让 API 直接做 Embedding？

因为：

```text
Heavy Work
 ↓
Timeout
Memory
CPU
Latency
```

应该异步化。

---

### Q3：为什么 SQS Message 不保存文件？

```text
SQS
→ Metadata

S3
→ File
```

---

### Q4：为什么需要 DLQ？

处理永久失败消息：

```text
Retry
 ↓
Retry
 ↓
DLQ
```

---

### Q5：为什么 RAG 要保存 Metadata？

为了：

```text
Citation
Filtering
Tenant Isolation
Debugging
```

---

# 70. Day 48 最重要的架构原则

记住：

```text
API
=
Fast Request

S3
=
File

SQS
=
Async Work

Worker
=
Heavy Processing

PostgreSQL
=
Source of Truth

pgvector
=
Vector Search

Redis
=
Cache

LLM
=
Reasoning / Generation
```

不要让：

```text
Spring Boot API
```

变成：

```text
Upload
+
OCR
+
Parsing
+
Chunking
+
Embedding
+
Vector DB
```

全部同步执行。

---

# 71. Day 40 → Day 48

现在已经从一个简单的：

```text
Spring Boot
 ↓
LLM
```

升级到了：

```text
                       AWS
                        │
                     Route 53
                        │
                       ALB
                        │
                      EKS
                        │
         ┌──────────────┼──────────────┐
         ▼              ▼              ▼
       AI API          MCP          RAG Worker
         │              │              │
        HPA             │            KEDA
         │              │              │
         └──────────────┼──────────────┘
                        │
                     Karpenter
                        │
              ┌─────────┼─────────┐
              ▼         ▼         ▼
           Bedrock     Redis      RDS
                                PostgreSQL
                                    │
                                 pgvector
                                    ▲
                                    │
S3 → EventBridge → SQS ────────────┘
```

这已经是一套完整的 **企业级 AWS Java + AI + RAG 平台核心架构**。

---

## Day 49

下一步最值得做的是：

**Day 49：Spring AI + RAG Production —— 把 Day 48 的 pgvector 真正接入 Spring Boot，实现 `Retrieval → Prompt → LLM → Citation`，并加入 Hybrid Search、Metadata Filtering、Reranking、Conversation Memory。**

到 Day 49，你就会从“搭 AWS 基础设施”正式进入**企业 AI 应用核心代码层**。
