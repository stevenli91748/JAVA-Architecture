# Day 56：Enterprise AI Data Platform

Day 55 已经完成安全体系：

```text
Authentication
Authorization
Tenant Isolation
Guardrails
PII / DLP
Tool Security
Audit
```

今天开始进入 **AI Data Platform**。

核心目标：

> 把企业各种 PDF、Word、Excel、网页、数据库和业务数据，变成 AI 可以安全检索、分析和使用的统一知识平台。

---

# 1. Day 56 最终架构

```text
                    Enterprise Data
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
      PDF/Word          Database          APIs
      Excel             CRM               Web
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                   Data Ingestion
                          │
                          ▼
                Document Processing
                          │
                  ┌───────┴───────┐
                  ▼               ▼
                S3            Metadata DB
                  │
                  ▼
               Chunking
                  │
                  ▼
              Embedding
                  │
          ┌───────┴────────┐
          ▼                ▼
      pgvector         OpenSearch
          │                │
          └───────┬────────┘
                  ▼
             Retrieval API
                  │
                  ▼
               RAG Agent
                  │
                  ▼
                 LLM
```

---

# 2. 为什么需要 Data Platform？

企业真实数据不是只有：

```text
PDF
```

而是：

```text
PDF
Word
Excel
CSV
Email
Database
CRM
ERP
API
Web
Images
```

所以不能简单：

```text
PDF
 ↓
Embedding
 ↓
Vector DB
```

应该：

```text
Data Sources
 ↓
Ingestion
 ↓
Processing
 ↓
Metadata
 ↓
Storage
 ↓
Indexing
 ↓
Retrieval
 ↓
AI
```

---

# 3. 今天学习的核心组件

```text
S3
PostgreSQL
pgvector
OpenSearch
SQS
Lambda / ECS
Embedding
Chunking
Metadata
Hybrid Search
```

---

# 4. S3：原始数据层

推荐：

```text
Amazon S3
```

存放：

```text
original/
processed/
chunks/
exports/
```

例如：

```text
s3://company-ai-data/
│
├── raw/
│   ├── hr/
│   ├── finance/
│   ├── legal/
│   └── customer/
│
├── processed/
│
└── exports/
```

---

# 5. 为什么原始文件必须保留？

不要只保存：

```text
Embedding
```

因为以后可能需要：

```text
重新 Chunk
重新 Embedding
更换模型
重新解析
审计
重新建立索引
```

所以：

> **Raw Data 必须可重新处理。**

---

# 6. Metadata Database

PostgreSQL 保存：

```text
document_id
tenant_id
file_name
file_type
version
classification
owner
created_at
updated_at
s3_uri
status
```

例如：

```sql
CREATE TABLE documents (
    id UUID PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    file_name VARCHAR(500),

    file_type VARCHAR(100),

    s3_uri TEXT NOT NULL,

    version INTEGER DEFAULT 1,

    classification VARCHAR(50),

    status VARCHAR(30),

    created_at TIMESTAMP NOT NULL,

    updated_at TIMESTAMP NOT NULL
);
```

---

# 7. Document Processing Pipeline

```text
Upload
  │
  ▼
S3
  │
  ▼
S3 Event
  │
  ▼
SQS
  │
  ▼
Document Worker
  │
  ├── Parse
  ├── Clean
  ├── Metadata
  ├── Chunk
  └── Embed
       │
       ▼
    Vector DB
```

---

# 8. 为什么 SQS？

如果用户上传：

```text
500 MB PDF
```

不要：

```text
HTTP Request
 ↓
等待 2 分钟
```

应该：

```text
Upload
 ↓
S3
 ↓
SQS
 ↓
Async Worker
```

API 立即返回：

```json
{
  "documentId": "abc123",
  "status": "PROCESSING"
}
```

---

# 9. Document Status

建议：

```text
UPLOADED
PROCESSING
PARSED
CHUNKED
EMBEDDED
INDEXED
FAILED
```

完整：

```text
UPLOADED
   ↓
PROCESSING
   ↓
PARSED
   ↓
CHUNKED
   ↓
EMBEDDED
   ↓
INDEXED
```

---

# 10. Document Processing Worker

Java：

```java
@Component
public class DocumentProcessor {

    public void process(Document document) {

        ParsedDocument parsed =
            parser.parse(document);

        List<Chunk> chunks =
            chunker.chunk(parsed);

        List<EmbeddedChunk> embeddings =
            embeddingService.embed(chunks);

        vectorStore.save(embeddings);
    }
}
```

---

# 11. Parser

不要自己处理所有文件格式。

可以设计统一接口：

```java
public interface DocumentParser {

    ParsedDocument parse(
        InputStream input
    );
}
```

实现：

```text
PdfParser
WordParser
ExcelParser
HtmlParser
CsvParser
ImageParser
```

---

# 12. ParsedDocument

```java
public record ParsedDocument(

    UUID documentId,

    String title,

    String text,

    Map<String, Object> metadata

) {}
```

---

# 13. Chunking

这是 RAG 最关键的环节之一。

例如原文：

```text
10,000 words
```

不能直接：

```text
10,000 words
 ↓
Embedding
```

通常需要：

```text
Document
 ↓
Chunks
```

---

# 14. Chunk Example

```text
Document
│
├── Chunk 001
├── Chunk 002
├── Chunk 003
├── Chunk 004
└── Chunk 005
```

---

# 15. Chunk Size

初始实验可以从：

```text
500–1000 tokens
```

附近开始。

Overlap：

```text
50–150 tokens
```

但：

> **不存在适用于所有企业文档的固定最佳 Chunk Size。**

必须用 Day 53 Evaluation Dataset 测试。

---

# 16. 为什么需要 Overlap？

原文：

```text
Chunk A
──────────────
... vacation policy
```

下一块：

```text
Chunk B
──────────────
approval process ...
```

如果完全切断：

```text
policy
```

和：

```text
approval
```

可能失去上下文。

所以：

```text
Chunk A
       █████
       overlap
             █████
             Chunk B
```

---

# 17. Chunk Metadata

每个 Chunk 必须保存：

```text
chunk_id
document_id
tenant_id
page
section
chunk_index
text
classification
```

例如：

```json
{
  "documentId": "doc-001",
  "tenantId": "tenant-a",
  "page": 12,
  "section": "Vacation Policy",
  "chunkIndex": 4
}
```

---

# 18. 为什么 Metadata 很重要？

用户：

> 查询 Finance 部门 2026 年政策。

不要只：

```text
Vector Similarity
```

还要：

```text
tenant_id
+
department
+
year
+
classification
```

---

# 19. pgvector

你的 PostgreSQL 可以直接增加：

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

建立：

```sql
CREATE TABLE document_chunks (
    id UUID PRIMARY KEY,

    document_id UUID NOT NULL,

    tenant_id VARCHAR(100) NOT NULL,

    chunk_index INTEGER,

    content TEXT,

    embedding VECTOR(1536),

    metadata JSONB
);
```

维度：

```text
1536
```

只是示例。

**实际维度必须与你选择的 embedding model 一致。**

---

# 20. Vector Search

概念：

```text
User Query
 ↓
Embedding
 ↓
Vector
 ↓
pgvector
 ↓
Similarity
 ↓
Top K
```

---

# 21. pgvector 查询

例如：

```sql
SELECT
    id,
    content,
    metadata,
    1 - (
        embedding <=> :queryEmbedding
    ) AS similarity
FROM document_chunks
WHERE tenant_id = :tenantId
ORDER BY embedding <=> :queryEmbedding
LIMIT 10;
```

---

# 22. 但是生产 RAG 不应该只做 Vector Search

因为用户可能问：

```text
"EMP-1024"
```

Vector Search 不一定擅长精确匹配：

```text
EMP-1024
```

这种 ID。

所以需要：

```text
Semantic Search
+
Keyword Search
```

---

# 23. Hybrid Search

```text
                   Query
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
    Vector Search          Keyword Search
          │                     │
          └──────────┬──────────┘
                     ▼
                  Fusion
                     │
                     ▼
                  Reranker
                     │
                     ▼
                   Top K
```

这是企业 RAG 很重要的升级。

---

# 24. OpenSearch

AWS 环境可以考虑：

```text
Amazon OpenSearch Service
```

适合：

```text
Full Text Search
Keyword Search
Hybrid Search
Large-scale Search
```

---

# 25. pgvector vs OpenSearch

| 场景                 | pgvector | OpenSearch |
| ------------------ | -------: | ---------: |
| 中小型 RAG            |    ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |
| Java CRUD          |    ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |
| 简单架构               |    ⭐⭐⭐⭐⭐ |         ⭐⭐ |
| 大规模 Search         |      ⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| Full-text Search   |      ⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| Hybrid Search      |      ⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| 企业 Search Platform |      ⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |

---

# 26. 你的项目怎么选？

目前 Day 56：

> **先用 PostgreSQL + pgvector。**

原因：

```text
Spring Boot
+
PostgreSQL
+
pgvector
```

已经足够完成第一版 Enterprise RAG。

等数据规模和搜索需求上来，再增加：

```text
OpenSearch
```

---

# 27. Embedding Pipeline

```text
Document
 ↓
Parser
 ↓
Chunker
 ↓
Embedding Model
 ↓
Vector
 ↓
pgvector
```

---

# 28. Embedding Model

Embedding 模型专门负责：

```text
Text
 ↓
Vector
```

例如：

```text
"Vacation policy"
```

变成：

```text
[0.021, -0.14, 0.73, ...]
```

---

# 29. Embedding 和 Chat Model 不一样

不要混淆：

```text
Chat Model
=
生成答案

Embedding Model
=
生成向量
```

架构：

```text
                    AI Models
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Chat Model         Embedding Model
             │                   │
             ▼                   ▼
          Answer              Vector
```

---

# 30. Embedding Version

一定要保存：

```text
embedding_model
embedding_version
```

因为以后更换模型：

```text
Embedding Model A
 ↓
Embedding Model B
```

向量空间可能不同。

通常需要重新建立索引。

---

# 31. Document Version

文件修改：

```text
Policy v1
```

变成：

```text
Policy v2
```

不要覆盖历史版本。

保存：

```text
document_id
version
```

例如：

```text
doc-001
v1
v2
v3
```

---

# 32. RAG Retrieval API

建立：

```http
POST /api/v1/knowledge/search
```

Request：

```json
{
  "query": "What is the vacation policy?",
  "topK": 10
}
```

Response：

```json
{
  "results": [
    {
      "chunkId": "chunk-001",
      "content": "...",
      "score": 0.92,
      "page": 12,
      "documentId": "doc-001"
    }
  ]
}
```

---

# 33. Retrieval Service

```java
public interface RetrievalService {

    List<RetrievedChunk> search(
        SearchRequest request
    );
}
```

实现：

```text
PgVectorRetrievalService
OpenSearchRetrievalService
HybridRetrievalService
```

---

# 34. Retrieval Pipeline

```text
Question
   ↓
Query Processing
   ↓
Embedding
   ↓
Vector Search
   ↓
Metadata Filter
   ↓
Top 20
   ↓
Reranking
   ↓
Top 5
   ↓
LLM
```

---

# 35. 为什么先 Top 20 再 Top 5？

因为：

```text
Top 5 Vector Search
```

可能漏掉真正相关内容。

可以：

```text
Vector Search
Top 20
 ↓
Reranker
Top 5
```

提高精度。

---

# 36. Reranker

Reranker 重新判断：

```text
Question
+
Candidate Chunk
```

输出：

```text
Relevance Score
```

例如：

```text
Chunk A = 0.91
Chunk B = 0.82
Chunk C = 0.51
```

最终：

```text
A
B
```

进入 LLM。

---

# 37. RAG Context

最终给 LLM：

```text
SYSTEM
You answer using trusted enterprise documents.

CONTEXT

[Document: Employee Handbook]
[Page: 12]

Employees receive 15 vacation days...

QUESTION

What is the vacation policy?
```

---

# 38. Citation

答案：

```text
Employees receive 15 vacation days.
[Employee Handbook, p.12]
```

这就是 Day 53 Evaluation 中的：

```text
Citation Correctness
```

---

# 39. AI Knowledge API

建议最终：

```text
POST /knowledge/documents
POST /knowledge/search
GET  /knowledge/documents/{id}
DELETE /knowledge/documents/{id}
GET  /knowledge/documents/{id}/versions
```

---

# 40. 上传文件

```http
POST /api/v1/knowledge/documents
```

返回：

```json
{
  "documentId": "doc-123",
  "status": "UPLOADED"
}
```

然后：

```text
S3
 ↓
SQS
 ↓
Worker
```

---

# 41. 完整上传流程

```text
Browser
  │
  ▼
Spring Boot
  │
  ▼
Generate Presigned URL
  │
  ▼
S3
  │
  ▼
S3 Event
  │
  ▼
SQS
  │
  ▼
Document Worker
  │
  ▼
Parser
  │
  ▼
Chunker
  │
  ▼
Embedding
  │
  ▼
pgvector
```

---

# 42. Presigned URL

大文件不要：

```text
Browser
 ↓
Spring Boot
 ↓
S3
```

更好：

```text
Browser
 ↓
Spring Boot
 ↓
Presigned URL
 ↓
S3
```

这样 Java Server 不需要承载文件流量。

---

# 43. S3 Event

上传：

```text
document.pdf
```

事件：

```text
S3
 ↓
SQS
```

Message：

```json
{
  "bucket": "company-ai-data",
  "key": "raw/tenant-a/documents/123.pdf"
}
```

---

# 44. Worker

```java
@SqsListener("document-processing")
public void process(
        DocumentMessage message) {

    documentProcessor.process(
        message
    );
}
```

---

# 45. Failure Handling

如果：

```text
PDF Parser
 ↓
FAILED
```

不要：

```text
丢掉
```

应该：

```text
FAILED
 ↓
Error Metadata
 ↓
Retry
```

---

# 46. Dead Letter Queue

```text
SQS
 │
 ├── success → Worker
 │
 └── repeated failure
             ↓
            DLQ
```

例如：

```text
document-processing-dlq
```

管理员之后检查。

---

# 47. Idempotency

非常重要。

同一个文件可能因为：

```text
Retry
Network
Duplicate Event
```

被处理两次。

必须保证：

```text
process(documentId, version)
```

幂等。

---

# 48. Idempotency Key

例如：

```text
tenant-a:doc-123:v2
```

数据库：

```sql
UNIQUE (
    document_id,
    version
);
```

---

# 49. Data Lineage

企业 AI 必须知道：

```text
Answer
 ↓
Chunk
 ↓
Document
 ↓
S3 Object
 ↓
Original Source
```

例如：

```text
Answer
  ↓
chunk-182
  ↓
doc-023
  ↓
Employee-Handbook-v4.pdf
  ↓
S3
```

这就是：

> Data Lineage

---

# 50. AI Data Platform 的四层

```text
┌───────────────────────────────┐
│         AI Application        │
├───────────────────────────────┤
│        Retrieval Layer        │
├───────────────────────────────┤
│      Knowledge/Data Layer     │
├───────────────────────────────┤
│       Raw Storage Layer       │
└───────────────────────────────┘
```

---

# 51. Storage Architecture

```text
                    Data
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
         S3                 PostgreSQL
          │                     │
       Raw Data              Metadata
          │                     │
          └──────────┬──────────┘
                     ▼
                  pgvector
                     │
                     ▼
                 Retrieval
```

---

# 52. 企业数据分类

今天开始建立：

```text
PUBLIC
INTERNAL
CONFIDENTIAL
RESTRICTED
```

然后：

```text
classification
```

进入每个：

```text
Document
Chunk
Retrieval
Model Policy
```

---

# 53. Retrieval Security

搜索：

```text
tenant_id
+
classification
+
department
+
ACL
```

例如：

```sql
WHERE tenant_id = :tenantId
AND classification <= :allowedClassification
```

具体比较逻辑最好由 Policy Service 统一处理，而不是到处散落 SQL 条件。

---

# 54. Day 55 + Day 56

安全：

```text
Tenant
 ↓
Authorization
```

数据：

```text
Tenant
 ↓
Metadata
 ↓
Retrieval
```

因此：

> **Security Metadata 必须进入 Data Platform。**

---

# 55. Day 56 项目结构

```text
knowledge/
│
├── document/
│   ├── Document.java
│   ├── DocumentService.java
│   └── DocumentController.java
│
├── ingestion/
│   ├── DocumentIngestionService.java
│   ├── DocumentProcessor.java
│   └── S3StorageService.java
│
├── parser/
│   ├── DocumentParser.java
│   ├── PdfParser.java
│   └── WordParser.java
│
├── chunk/
│   ├── Chunker.java
│   └── Chunk.java
│
├── embedding/
│   ├── EmbeddingService.java
│   └── EmbeddingModel.java
│
├── retrieval/
│   ├── RetrievalService.java
│   ├── PgVectorService.java
│   └── Reranker.java
│
└── metadata/
    └── MetadataService.java
```

---

# 56. Day 56 数据库

核心：

```text
documents
document_versions
document_chunks
```

关系：

```text
documents
    │
    ├── versions
    │
    └── chunks
          │
          └── embeddings
```

---

# 57. documents

```sql
CREATE TABLE documents (
    id UUID PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    name VARCHAR(500) NOT NULL,

    classification VARCHAR(50),

    status VARCHAR(30),

    created_at TIMESTAMP NOT NULL
);
```

---

# 58. document_chunks

```sql
CREATE TABLE document_chunks (
    id UUID PRIMARY KEY,

    document_id UUID NOT NULL,

    tenant_id VARCHAR(100) NOT NULL,

    chunk_index INTEGER NOT NULL,

    page_number INTEGER,

    content TEXT NOT NULL,

    embedding VECTOR(1536),

    metadata JSONB
);
```

---

# 59. Vector Index

数据量增加以后：

```sql
CREATE INDEX ON document_chunks
USING hnsw (embedding vector_cosine_ops);
```

具体索引类型和参数需要结合数据量、查询延迟和写入模式做 benchmark。

---

# 60. Day 56 完整 RAG

```text
                     USER
                       │
                       ▼
                   RAG Agent
                       │
                       ▼
                Retrieval API
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Vector Search       Keyword Search
             │                   │
             └─────────┬─────────┘
                       ▼
                    Fusion
                       │
                       ▼
                    Reranker
                       │
                       ▼
                    Top 5
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

# 61. Day 56 必做任务

```text
[ ] 1. S3 Raw Storage
[ ] 2. Presigned Upload
[ ] 3. S3 → SQS
[ ] 4. Document Worker
[ ] 5. PDF Parser
[ ] 6. Chunker
[ ] 7. Embedding Service
[ ] 8. PostgreSQL
[ ] 9. pgvector
[ ] 10. Metadata
[ ] 11. Tenant Filter
[ ] 12. Vector Search
[ ] 13. Keyword Search
[ ] 14. Hybrid Search
[ ] 15. Reranker
[ ] 16. Citation
[ ] 17. Document Version
[ ] 18. Idempotency
[ ] 19. DLQ
[ ] 20. Data Lineage
```

---

# 62. Day 56 实战项目

建立：

> **Enterprise Knowledge Platform**

用户：

```text
上传 Employee Handbook.pdf
```

系统：

```text
S3
 ↓
SQS
 ↓
Parser
 ↓
Chunk
 ↓
Embedding
 ↓
pgvector
```

然后：

```text
用户：
What is the vacation policy?
```

系统：

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
LLM
 ↓
Answer + Citation
```

---

# 63. Day 56 验收测试

### Test 1：PDF

```text
Upload PDF
```

必须：

```text
UPLOADED
 ↓
PROCESSING
 ↓
INDEXED
```

---

### Test 2：搜索

```text
What is the vacation policy?
```

返回：

```text
answer
+
source
+
page
```

---

### Test 3：Tenant

Tenant A：

```text
search()
```

不能返回：

```text
Tenant B
```

---

### Test 4：版本

上传：

```text
Policy v1
```

再上传：

```text
Policy v2
```

必须保留：

```text
v1
v2
```

---

### Test 5：失败

上传损坏 PDF：

```text
FAILED
 ↓
Retry
 ↓
DLQ
```

---

### Test 6：重复事件

同一个：

```text
documentId + version
```

处理两次。

结果：

```text
只能产生一份有效索引
```

---

# 64. Day 56 面试题

### Q1：为什么 S3 + PostgreSQL？

```text
S3
=
原始文件

PostgreSQL
=
Metadata + Transaction

pgvector
=
Vector Search
```

---

### Q2：为什么不能只保存 Embedding？

因为以后需要：

```text
重新 Embedding
重新 Chunk
重新 Index
审计
版本管理
```

---

### Q3：为什么 Hybrid Search？

因为：

```text
Vector
=
语义

Keyword
=
精确匹配
```

两者互补。

---

### Q4：为什么 SQS？

把：

```text
文件上传
```

和：

```text
AI Processing
```

解耦。

实现：

```text
Async
Retry
DLQ
Scalability
```

---

### Q5：为什么必须 Tenant Filter？

否则：

```text
Vector Search
```

可能把其他 Tenant 的文档检索出来。

---

# 65. Day 56 最重要架构

```text
                         ENTERPRISE DATA
                               │
             ┌─────────────────┼─────────────────┐
             ▼                 ▼                 ▼
            S3              Database            API
             │
             ▼
            SQS
             │
             ▼
      Document Processor
             │
       ┌─────┼─────┐
       ▼     ▼     ▼
     Parse  Chunk  Metadata
             │
             ▼
         Embedding
             │
       ┌─────┴──────┐
       ▼            ▼
   pgvector      OpenSearch
       │            │
       └─────┬──────┘
             ▼
        Hybrid Retrieval
             │
          Reranker
             │
          Top-K Context
             │
             ▼
           RAG Agent
             │
             ▼
            LLM
             │
             ▼
       Answer + Citation
```

---

# 66. Day 50 → Day 56

现在你的 AWS Java + AI 企业平台已经开始形成完整闭环：

```text
Day 50
Agent + Tool + MCP
        ↓
Day 51
Workflow + State + Checkpoint
        ↓
Day 52
Multi-Agent
        ↓
Day 53
Evaluation + Regression
        ↓
Day 54
AI Gateway + Model Routing
        ↓
Day 55
Security + Guardrails
        ↓
Day 56
Enterprise AI Data Platform
```

**Day 57** 建议进入 **Event-Driven AI Architecture：Kafka/SQS/EventBridge + AI Agent Async Workflow + Spring Cloud + ECS/EKS Worker + 长任务 + Retry/DLQ + Saga/状态机**。这一步会把目前的同步 Java AI 应用升级成真正能处理企业级高并发、长时间 Agent 任务的异步架构。
