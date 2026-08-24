# Day 24：RDS PostgreSQL + pgvector + Spring AI RAG

今天正式进入这套环境的 **AI 核心部分**。

前 23 天主要解决：

```text
AWS
网络
Java
React
CI/CD
ECS
安全
Observability
```

今天开始把：

```text
Spring Boot
     ↓
Spring AI
     ↓
Embedding
     ↓
PostgreSQL + pgvector
     ↓
Vector Search
     ↓
RAG
     ↓
Amazon Bedrock
```

真正连起来。

---

# 1. 今天最终目标

完成一个最小可运行 RAG：

```text
用户问题
   │
   ▼
Spring Boot
   │
   ▼
Embedding
   │
   ▼
pgvector
   │
   ▼
找 Top-K 文档
   │
   ▼
Context
   │
   ▼
Bedrock LLM
   │
   ▼
最终回答
```

---

# 2. 最终 AWS 架构

```text
                    Internet
                       │
                       ▼
                   CloudFront
                       │
                       ▼
                      React
                       │
                       ▼
                      ALB
                       │
                       ▼
                Private ECS Fargate
                       │
              ┌────────┼─────────┐
              │        │         │
              ▼        ▼         ▼
           Spring    RAG       Bedrock
           Boot       │
                      ▼
                 PostgreSQL
                   pgvector
                      │
                      ▼
                 Vector Search
```

---

# 3. RAG 到底是什么

RAG：

**Retrieval-Augmented Generation**

不是：

```text
User
 ↓
LLM
```

而是：

```text
User Question
      │
      ▼
  Retrieval
      │
      ▼
Relevant Documents
      │
      ▼
   Context
      │
      ▼
     LLM
      │
      ▼
   Answer
```

核心思想：

> **先找资料，再让 LLM 根据资料回答。**

---

# 4. 为什么需要 pgvector

普通 PostgreSQL：

```text
SELECT *
FROM documents
WHERE text LIKE '%AWS%';
```

这是关键词搜索。

RAG 更需要：

```text
Question
   ↓
Embedding Vector
   ↓
Vector Similarity
   ↓
Top-K Documents
```

例如：

```text
"How do I deploy Spring Boot on AWS?"
```

即使数据库文档写的是：

```text
"ECS Fargate application deployment"
```

也可能被找到。

---

# 5. pgvector

`pgvector` 是 PostgreSQL 的向量扩展。

它提供类似：

```text
vector
```

的数据类型，以及：

```text
cosine similarity
L2 distance
inner product
```

等向量检索能力。

对于你现在的学习环境：

**PostgreSQL + pgvector 是非常好的第一套 RAG 数据库。**

---

# 6. RDS PostgreSQL

你 Day 17 已经建立：

```text
Private RDS
```

今天确认：

```text
Engine:
PostgreSQL
```

建议：

```text
PostgreSQL 16/17
```

具体版本以当前 AWS RDS 可用版本为准。

---

# 7. RDS 不开放公网

保持：

```text
RDS
Publicly accessible:
No
```

网络：

```text
Internet
    X
    │
    ▼
  RDS
```

只有：

```text
ECS-SG
   │
   │ 5432
   ▼
RDS-SG
```

允许。

---

# 8. 启用 pgvector

连接 RDS：

```bash id="byf1cl"
psql \
  -h YOUR_RDS_ENDPOINT \
  -U appuser \
  -d java_ai
```

然后：

```sql id="2j9hkl"
CREATE EXTENSION IF NOT EXISTS vector;
```

检查：

```sql id="g23by7"
SELECT extname
FROM pg_extension
WHERE extname = 'vector';
```

应该看到：

```text id="rl2b2y"
vector
```

---

# 9. RDS 参数

pgvector 不一定需要复杂的 RDS Parameter Group。

第一阶段：

```text id="g29l2w"
CREATE EXTENSION vector;
```

即可开始。

后面数据量变大，再考虑：

```text id="33kn6j"
index
memory
connection pool
vacuum
```

---

# 10. 创建 Document 表

最简单：

```sql id="j8n0lw"
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,

    content TEXT NOT NULL,

    metadata JSONB,

    embedding VECTOR(1536),

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

这里：

```text id="x7x9hl"
VECTOR(1536)
```

只是示例。

**维度必须与你选择的 Embedding Model 输出维度一致。**

不要盲目固定 1536。

---

# 11. 更企业化的表结构

推荐：

```sql id="u1j8s4"
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,

    document_id VARCHAR(255),

    chunk_index INTEGER,

    content TEXT NOT NULL,

    metadata JSONB,

    embedding VECTOR(1536),

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

这样一个 PDF：

```text id="k2s7j1"
document.pdf
```

可以拆成：

```text id="w4y5x3"
document-001
document-002
document-003
...
```

---

# 12. 为什么需要 Chunk

不要把一个 100 页 PDF：

```text id="c5c4ef"
100 pages
 ↓
1 embedding
```

推荐：

```text id="6y6q5z"
100 pages
 ↓
Chunk
 ↓
500–1000 tokens/chunk
 ↓
Embedding
```

例如：

```text id="5ctk9d"
Document
│
├── Chunk 1
├── Chunk 2
├── Chunk 3
├── ...
└── Chunk 200
```

---

# 13. Chunking

第一版可以：

```text id="g2xj3k"
Chunk size:
500–800 tokens

Overlap:
50–100 tokens
```

不要把这些数字当成固定标准。

真正最佳值取决于：

```text id="1y2v5a"
文档类型
Embedding Model
查询类型
上下文长度
```

后面需要通过评估调整。

---

# 14. Spring AI

你的 Java 项目：

```text id="9iy6az"
Spring Boot
```

加入 Spring AI。

Spring AI 提供：

```text id="km7y6u"
ChatClient
EmbeddingModel
VectorStore
Document
Retriever / Advisor
```

这样 Java 不需要自己实现所有 RAG 基础设施。

---

# 15. Maven

Spring AI 的版本建议使用官方当前 BOM/依赖管理方式，不要把版本号散落在几十个 dependency 中。

概念：

```xml id="u1p3qa"
<dependencyManagement>
    ...
</dependencyManagement>
```

然后加入对应：

```text id="n8jv9b"
Spring AI
PostgreSQL Vector Store
AWS Bedrock
```

由于 Spring AI 版本迭代比较快，**实际项目应以当前 Spring AI 官方文档对应你所用 Spring Boot 版本的依赖配置为准**。

---

# 16. Spring AI + PostgreSQL

核心关系：

```text id="p6p7or"
Spring AI
   │
   ▼
VectorStore
   │
   ▼
PgVectorStore
   │
   ▼
PostgreSQL
   │
   ▼
pgvector
```

---

# 17. VectorStore

代码概念：

```java id="7m9i2s"
@Autowired
VectorStore vectorStore;
```

插入：

```java id="9uv2yr"
vectorStore.add(documents);
```

搜索：

```java id="4n9p5x"
List<Document> results =
    vectorStore.similaritySearch(
        SearchRequest
            .query("How does ECS work?")
            .withTopK(5)
    );
```

具体 API 会随你使用的 Spring AI 版本变化，因此以当前版本 Javadoc 为准。

---

# 18. Embedding

例如：

```text id="v70dfq"
Text
 ↓
Embedding Model
 ↓
Vector
```

比如：

```text id="xk7k1a"
"Amazon ECS is a container service..."
```

变成：

```text id="c7j2x9"
[0.012,
 -0.342,
 0.771,
 ...
]
```

这个 Vector 存入：

```text id="b4q6r8"
pgvector
```

---

# 19. Embedding Model

今天先不要纠结哪个模型“绝对最好”。

你的 AWS 环境建议优先考虑：

```text id="2n9u0h"
Amazon Bedrock Embeddings
```

这样：

```text id="9j2y5r"
Spring AI
 ↓
AWS SDK
 ↓
Bedrock
```

继续使用：

```text id="3yn7f6"
ECS Task Role
```

不需要 API Key。

---

# 20. AI 权限

ECS Task Role：

```text id="9r2d8v"
bedrock:InvokeModel
```

限制到实际使用的模型 ARN/资源范围。

不要：

```json id="z73f2k"
{
  "Action": "bedrock:*",
  "Resource": "*"
}
```

如果当前服务/API 对某个动作不支持资源级限制，则按 AWS IAM 对该动作的授权模型配置。

---

# 21. RAG 数据导入流程

今天先做：

```text id="e9c6wm"
TXT / PDF
   ↓
读取
   ↓
Clean
   ↓
Chunk
   ↓
Embedding
   ↓
pgvector
```

例如：

```text id="m3b6xq"
AWS ECS Guide
      ↓
Chunk 1
Chunk 2
Chunk 3
...
      ↓
Embedding
      ↓
PostgreSQL
```

---

# 22. Document Loader

Spring AI 可以处理：

```text id="g1g2a3"
Text
PDF
JSON
HTML
```

今天建议先从：

```text id="pp8r9j"
TXT / Markdown
```

开始。

因为 PDF Parsing 会增加额外变量。

---

# 23. 第一个知识库

创建：

```text id="q6i4h8"
knowledge/
├── aws-ecs.md
├── aws-rds.md
├── spring-ai.md
└── aws-bedrock.md
```

例如：

```markdown id="w9b5m3"
# AWS ECS

Amazon ECS is a fully managed container
orchestration service...
```

---

# 24. Import Service

建立：

```text id="y4v2c9"
KnowledgeIngestionService
```

流程：

```java id="8z8z3x"
public void ingest(
        List<Document> documents) {

    vectorStore.add(documents);
}
```

完整逻辑：

```text id="6n6o8a"
File
 ↓
Document
 ↓
Chunk
 ↓
Embedding
 ↓
VectorStore
```

---

# 25. RAG Query

用户：

```text id="f0m9z7"
"What is ECS?"
```

系统：

```text id="v5x8m1"
Question
 ↓
Embedding
 ↓
Similarity Search
 ↓
Top 5 Chunks
```

结果：

```text id="v6h4z2"
Chunk 12
Chunk 48
Chunk 51
Chunk 92
Chunk 105
```

---

# 26. Context

然后：

```text id="0l8k9v"
Question
+
Retrieved Documents
```

组成：

```text id="g3q5k1"
Prompt
```

概念：

```text
Use the following context to answer.

Context:
{retrieved_documents}

Question:
{question}
```

---

# 27. LLM

然后：

```text id="6j6qk7"
Prompt
 ↓
Bedrock
 ↓
LLM
 ↓
Answer
```

最终：

```text id="7q8k3x"
User
 ↓
RAG
 ↓
Context
 ↓
Bedrock
 ↓
Answer
```

---

# 28. Spring AI ChatClient

概念代码：

```java id="1x7c5j"
String answer =
    chatClient
        .prompt()
        .user(question)
        .call()
        .content();
```

RAG 则需要把检索结果加入 Prompt，或者使用你所用 Spring AI 版本提供的 RAG Advisor/VectorStore 集成。

---

# 29. 更推荐的结构

不要把所有东西放：

```text id="qf0qrs"
ChatController
```

应该：

```text id="2kw8k6"
ChatController
      │
      ▼
ChatService
      │
      ▼
RagService
      │
      ├── Embedding
      ├── Vector Search
      └── Context
             │
             ▼
         ChatClient
             │
             ▼
           Bedrock
```

---

# 30. Java 项目结构

今天建议：

```text id="o5o3b5"
backend/
└── src/main/java/com/example/ai/
    │
    ├── controller/
    │   └── ChatController.java
    │
    ├── service/
    │   ├── ChatService.java
    │   ├── RagService.java
    │   └── IngestionService.java
    │
    ├── config/
    │   ├── AiConfig.java
    │   └── VectorStoreConfig.java
    │
    └── model/
        └── ChatRequest.java
```

---

# 31. API

创建：

```text id="b3p9q6"
POST /api/chat
```

Request：

```json id="n8v8s2"
{
  "message": "What is AWS ECS?"
}
```

Response：

```json id="m1v7c4"
{
  "answer": "Amazon ECS is...",
  "sources": [
    "aws-ecs.md"
  ]
}
```

---

# 32. 为什么返回 Sources

RAG 最好不要只返回：

```json id="l9t0yu"
{
  "answer": "..."
}
```

应该：

```json id="k6r3x8"
{
  "answer": "...",
  "sources": [
    {
      "document": "aws-ecs.md",
      "chunk": 12
    }
  ]
}
```

这样：

```text id="l1b7zr"
用户
 ↓
看到答案
 ↓
看到来源
```

可信度更高。

---

# 33. Similarity Search

假设：

```text id="j9y5z7"
Question
```

得到：

```text id="6x0p1q"
Document A
score = 0.91

Document B
score = 0.87

Document C
score = 0.81
```

可以：

```text id="2v8q4a"
Top-K = 5
```

然后：

```text id="fz5w0o"
score < threshold
```

则不使用。

---

# 34. Top-K 不要固定死

开始：

```text id="u6y2y8"
Top-K = 5
```

以后测试：

```text id="t0l8n3"
K=3
K=5
K=8
K=10
```

比较：

```text id="2j7x9k"
Answer Accuracy
Latency
Token Cost
```

找到适合你的值。

---

# 35. RAG 最重要的不是模型

很多初学者：

```text id="uwp9oo"
RAG 不好
 ↓
换更大的 LLM
```

其实很多时候问题是：

```text id="k2t5d4"
Chunking
Embedding
Retrieval
Metadata
```

例如：

```text id="5y7g4p"
错误 Chunk
 ↓
错误 Retrieval
 ↓
错误 Context
 ↓
再好的 LLM
 ↓
还是错误答案
```

---

# 36. Metadata

每个 Chunk 建议：

```json id="x2l8o4"
{
  "source": "aws-ecs.md",
  "documentId": "ecs-guide",
  "chunk": 12,
  "category": "aws",
  "version": "2026"
}
```

以后可以：

```text id="3m8y9n"
Filter
```

例如：

```text id="9f3l7a"
category = aws
```

---

# 37. 企业 RAG

真正企业系统：

```text id="1k0s8v"
User
 ↓
Authentication
 ↓
Tenant
 ↓
Metadata Filter
 ↓
Vector Search
```

例如：

```text id="j2n5x4"
Company A
 ↓
只能看到 Company A Documents
```

不能：

```text id="y8h2p5"
Company A
 ↓
Search
 ↓
Company B documents
```

这是以后必须加入的安全边界。

---

# 38. Multi-Tenant Metadata

例如：

```json id="y4l0n2"
{
  "tenantId": "company-a",
  "documentId": "doc-123",
  "source": "policy.pdf"
}
```

查询：

```text id="7q9n2p"
tenantId = company-a
```

这样：

```text id="6h5g8k"
Vector Search
       │
       ▼
tenant filter
       │
       ▼
Top-K
```

---

# 39. PostgreSQL Index

数据量小：

```text id="7e4k6r"
几千 chunks
```

先不需要过度优化。

数据量变大：

```text id="7z6c4x"
100K
1M
10M
```

再考虑 pgvector Index。

常见：

```text id="8n5k1c"
HNSW
IVFFlat
```

---

# 40. HNSW

对于很多现代向量检索场景，HNSW 是很好的第一选择。

例如：

```sql id="3x7m2p"
CREATE INDEX documents_embedding_idx
ON documents
USING hnsw (embedding vector_cosine_ops);
```

但：

**先确认你的 pgvector 版本和数据规模，再选择索引参数。**

不要在只有 100 条数据时就疯狂优化。

---

# 41. RDS Connection Pool

你的 ECS：

```text id="4l8n5f"
2 Tasks
```

每个 Task：

```text id="5p6y8m"
HikariCP
```

如果：

```text id="w1n8s3"
maximumPoolSize = 20
```

那么：

```text id="m8k2z6"
2 ECS
×
20
=
40 DB connections
```

如果以后：

```text id="k8y3f1"
10 ECS Tasks
```

就是：

```text id="z4c7n1"
200 connections
```

所以 RDS：

```text id="g9n0c4"
max_connections
```

必须一起规划。

---

# 42. Spring Boot Hikari

例如：

```yaml id="2s7j4m"
spring:
  datasource:
    hikari:
      maximum-pool-size: 10
      minimum-idle: 2
      connection-timeout: 30000
```

DEV 可以：

```text id="y8g3j2"
10
```

生产根据：

```text id="z7n6c4"
ECS Task Count
RDS Instance
Query Latency
```

调整。

---

# 43. 今天做第一个测试

插入：

```text id="4s3p9k"
AWS ECS is a container orchestration service.
```

然后：

```text id="0d5h8c"
Question:
"What is ECS?"
```

Vector Search：

```text id="v9c2k4"
Top 1:
AWS ECS is a container orchestration service.
```

然后：

```text id="1f7r3q"
LLM
 ↓
Answer
```

---

# 44. RAG 成功标准

今天不要只看：

```text id="5r6q2v"
HTTP 200
```

至少验证：

```text id="3c5n7x"
[✓] Document inserted
[✓] Embedding generated
[✓] Vector stored
[✓] Similarity search works
[✓] Context returned
[✓] Bedrock responds
[✓] Source returned
```

---

# 45. Observability 接入

Day 22 已经有 Trace。

今天把 RAG Trace 加进去：

```text id="w5j6k3"
POST /api/chat
│
├── rag.retrieve
│     │
│     ├── embedding
│     └── vector.search
│
└── llm.request
```

例如：

```text id="s2f7m4"
Total: 2.8s

Embedding: 150ms
Vector Search: 45ms
Bedrock: 2.3s
```

这样你可以真正优化 RAG。

---

# 46. RAG 性能优化方向

如果：

```text id="6z2h5v"
Vector Search = 50ms
Bedrock = 2.5s
```

不要优化 PostgreSQL。

应该：

```text id="3s5g9d"
LLM latency
```

如果：

```text id="d6m8p4"
Vector Search = 1.5s
```

再优化：

```text id="9v1c8y"
pgvector
Index
RDS
Query
```

这就是 Observability + RAG 的价值。

---

# 47. Day 24 最终代码架构

```text id="w9z1k5"
ChatController
       │
       ▼
ChatService
       │
       ▼
RagService
       │
       ├───────────────┐
       ▼               ▼
EmbeddingModel      VectorStore
       │               │
       ▼               ▼
    Bedrock          pgvector
       │               │
       └───────┬───────┘
               ▼
            Context
               │
               ▼
           ChatClient
               │
               ▼
            Bedrock
```

---

# 48. Day 24 检查清单

```text id="flx3kp"
[ ] RDS PostgreSQL
[ ] Private RDS
[ ] pgvector extension
[ ] documents table
[ ] embedding column
[ ] metadata JSONB
[ ] Chunking
[ ] Embedding Model
[ ] Spring AI
[ ] VectorStore
[ ] PgVectorStore
[ ] Similarity Search
[ ] Top-K
[ ] Metadata Filter
[ ] RAG Context
[ ] Bedrock
[ ] /api/chat
[ ] Sources
[ ] HikariCP
[ ] OTel RAG Span
[ ] AI latency
```

---

# Day 24 完成后的核心系统

```text id="1e5x9a"
                       React
                         │
                         ▼
                        ALB
                         │
                         ▼
                 Spring Boot ECS
                         │
                         ▼
                    ChatService
                         │
                         ▼
                     RAG Service
                    /           \
                   /             \
                  ▼               ▼
            Embedding          pgvector
                │                 │
                ▼                 ▼
             Bedrock          PostgreSQL
                │                 │
                └───────┬─────────┘
                        ▼
                     Context
                        │
                        ▼
                    Bedrock LLM
                        │
                        ▼
                     Answer
                        │
                        ▼
                      React
```

到这里，你已经从 **AWS Java 企业应用平台**正式进入 **Java + RAG + LLM 应用开发平台**。

**Day 25** 下一步建议做 **RAG 文档 ingestion pipeline**：把 PDF/Markdown → 文档解析 → Chunk → Metadata → Embedding → pgvector 全自动化，并接入 S3。这样以后上传一个 PDF，就能自动进入知识库，而不是手工执行 SQL。
