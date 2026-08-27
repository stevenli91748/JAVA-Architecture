# Day 47：AWS RDS PostgreSQL + pgvector + Redis 生产架构

Day 46 已经完成：

```text
HPA
KEDA
Karpenter
SQS
自动扩缩容
```

今天把 **数据层** 做成生产级。

最终目标：

```text
                    EKS
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       AI API       RAG       Worker
          │          │          │
          └─────┬────┴──────────┘
                │
        ┌───────┴────────┐
        ▼                ▼
   RDS PostgreSQL      Redis
        │
    pgvector
        │
        ▼
   Vector Search
```

---

# 1. 今天学习什么

```text
① RDS PostgreSQL
② Multi-AZ
③ Private Subnet
④ Security Group
⑤ HikariCP
⑥ Connection Pool
⑦ RDS Proxy
⑧ Read Replica
⑨ pgvector
⑩ Vector Index
⑪ Redis
⑫ Cache
⑬ RAG Database Architecture
```

---

# 2. 为什么 AI 应用需要 PostgreSQL？

你的企业 AI 应用至少需要保存：

```text
Users
Tenants
Documents
Chunks
Embeddings
Conversations
Messages
Agent Runs
Tool Calls
Evaluations
Audit Logs
```

所以：

```text
PostgreSQL
```

非常适合作为核心业务数据库。

---

# 3. 最终数据库架构

```text
                         EKS
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
          AI API         RAG         Worker
             │            │            │
             └────────────┼────────────┘
                          │
                    ┌─────┴─────┐
                    ▼           ▼
                  Redis        RDS
                              PostgreSQL
                                  │
                              pgvector
```

---

# 4. Redis 和 PostgreSQL 不要混淆

简单记：

```text
PostgreSQL
=
Source of Truth

Redis
=
Fast Cache
```

例如：

```text
User Profile
 ↓
PostgreSQL
```

而：

```text
Popular Query
 ↓
Redis
```

---

# 5. RDS 不要放 Public Subnet

生产：

```text
Internet
   X
   │
   X
RDS
```

正确：

```text
Internet
 ↓
ALB
 ↓
EKS
 ↓
Private Network
 ↓
RDS Private Subnet
```

---

# 6. VPC 架构

推荐：

```text
VPC
│
├── Public Subnet
│   └── ALB
│
├── Private App Subnet
│   └── EKS Nodes
│
└── Private DB Subnet
    └── RDS
```

---

# 7. RDS Security Group

不要：

```text
0.0.0.0/0
 ↓
5432
```

应该：

```text
EKS-SG
   │
   │ TCP 5432
   ▼
RDS-SG
```

只允许应用层访问。

---

# 8. Redis Security Group

同理：

```text
EKS-SG
   │
   │ TCP 6379
   ▼
Redis-SG
```

不要：

```text
Internet
 ↓
6379
```

---

# 9. RDS Multi-AZ

生产数据库：

```text
RDS
 │
 ├── AZ-a
 │
 └── AZ-b
```

Primary：

```text
AZ-a
```

Standby：

```text
AZ-b
```

发生故障：

```text
Primary
   X
   ↓
Failover
   ↓
Standby
```

---

# 10. Multi-AZ ≠ Read Replica

这个非常重要。

### Multi-AZ

主要解决：

```text
Availability
```

### Read Replica

主要解决：

```text
Read Scaling
```

不要混为一谈。

---

# 11. Read Replica

架构：

```text
Primary
   │
   ├────────► Replica 1
   │
   └────────► Replica 2
```

写：

```text
INSERT
UPDATE
DELETE
```

进入：

```text
Primary
```

读：

```text
SELECT
```

可以部分分流到：

```text
Read Replica
```

---

# 12. AI RAG 是否需要 Read Replica？

早期：

```text
100 users
```

不一定需要。

规模增加：

```text
10,000 users
```

大量：

```text
Conversation
Metadata
Document
```

查询变多以后，可以考虑。

---

# 13. pgvector

RAG 最重要的组件之一：

```text
PostgreSQL
 +
pgvector
```

支持：

```text
Vector
Similarity Search
```

---

# 14. RAG 数据结构

例如：

```text
documents
```

```text
id
tenant_id
title
source
created_at
```

然后：

```text
document_chunks
```

```text
id
document_id
tenant_id
content
embedding
metadata
```

---

# 15. Vector

例如 embedding：

```text
[0.0123,
 0.8391,
 -0.1822,
 ...]
```

维度取决于你使用的 embedding model。

例如：

```text
1536 dimensions
```

只是一个常见示例，不应硬编码为所有模型都一样。

---

# 16. PostgreSQL pgvector

安装/启用：

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

然后：

```sql
CREATE TABLE document_chunks (
    id BIGSERIAL PRIMARY KEY,

    tenant_id VARCHAR(100),

    document_id BIGINT,

    content TEXT,

    embedding VECTOR(1536),

    created_at TIMESTAMP
);
```

这里的 `1536` 必须与你实际 embedding model 的维度一致。

---

# 17. Similarity Search

例如：

```sql
SELECT
    id,
    content
FROM document_chunks
WHERE tenant_id = 'company-a'
ORDER BY embedding <=> :query_embedding
LIMIT 5;
```

这里：

```text
<=> 
```

常用于 cosine distance。

---

# 18. Tenant Isolation

绝对不要：

```text
SELECT *
FROM document_chunks
ORDER BY embedding <=> ...
```

生产必须：

```text
WHERE tenant_id = ?
```

也就是：

```text
User
 ↓
Tenant
 ↓
Vector Search
 ↓
Only Tenant Documents
```

---

# 19. Vector Index

数据量小：

```text
10,000 chunks
```

直接搜索可能够用。

数据量变成：

```text
10M chunks
```

就需要认真设计 Index。

pgvector 常见：

```text
HNSW
IVFFlat
```

---

# 20. HNSW

一般可以理解为：

```text
高质量近似最近邻搜索
```

特点：

```text
Query Performance
+
Recall
```

通常是 pgvector 里非常值得优先考虑的索引方案之一。

---

# 21. HNSW 示例

```sql
CREATE INDEX document_chunks_embedding_hnsw
ON document_chunks
USING hnsw (embedding vector_cosine_ops);
```

然后：

```sql
ORDER BY embedding <=> :query_embedding
```

数据库利用索引进行近似最近邻搜索。

---

# 22. 但别马上建巨大 Index

因为：

```text
Vector Index
```

会消耗：

```text
Memory
CPU
Disk
Build Time
```

应该：

```text
数据量
 ↓
Benchmark
 ↓
Index
```

而不是：

```text
看到 RAG
 ↓
马上 HNSW
```

---

# 23. RAG 查询流程

完整：

```text
User Query
    │
    ▼
Embedding Model
    │
    ▼
Query Vector
    │
    ▼
PostgreSQL pgvector
    │
    ▼
Top-K Chunks
    │
    ▼
Reranker
    │
    ▼
LLM
```

---

# 24. Top-K

例如：

```text
Top K = 5
```

数据库：

```text
100,000 chunks
 ↓
Top 5
```

再交给：

```text
LLM
```

不要：

```text
100,000 chunks
 ↓
LLM
```

---

# 25. Reranking

更好的 RAG：

```text
Vector Search
 ↓
Top 20
 ↓
Reranker
 ↓
Top 5
 ↓
LLM
```

例如：

```text
100,000
 ↓
20
 ↓
5
```

这样通常比单纯 Top-5 vector retrieval 更有机会提升检索质量，但需要实际评测。

---

# 26. Redis 在 RAG 中做什么？

Redis 可以缓存：

```text
Query
 ↓
Embedding
```

以及：

```text
Query
 ↓
Retrieved Documents
```

例如：

```text
"What is our vacation policy?"
```

第一次：

```text
Embedding
 ↓
Vector Search
 ↓
100ms
```

第二次：

```text
Redis
 ↓
5ms
```

---

# 27. Redis Cache Architecture

```text
User
 ↓
Spring Boot
 ↓
Redis
 │
 ├── HIT
 │
 └── MISS
       ↓
    PostgreSQL
       ↓
    Redis SET
```

---

# 28. Cache Key

不要：

```text
vacation policy
```

建议：

```text
tenant:A:
rag:
hash(query):
embeddingModelVersion
```

例如：

```text
tenant:A:rag:v3:8f82ab...
```

---

# 29. 为什么要 Tenant ID？

否则：

```text
Company A
 ↓
Cache
 ↓
Company B
```

可能拿到：

```text
Company A Data
```

这是严重的数据隔离问题。

所以：

```text
Cache Key
=
Tenant + Query + Version
```

---

# 30. Spring Boot + Redis

依赖：

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>

    <artifactId>
        spring-boot-starter-data-redis
    </artifactId>
</dependency>
```

---

# 31. Redis Cache

简单：

```java
@Cacheable(
    value = "rag",
    key = "#tenantId + ':' + #query"
)
public List<DocumentChunk> retrieve(
        String tenantId,
        String query) {

    return vectorSearch(
        tenantId,
        query
    );
}
```

生产中建议进一步使用规范化后的 query hash 和 embedding/model 版本。

---

# 32. Redis TTL

不要永久缓存：

```text
RAG Result
```

例如：

```text
TTL = 5 min
```

或者：

```text
TTL = 1 hour
```

根据数据更新频率决定。

---

# 33. Cache Invalidation

如果文档更新：

```text
Document Updated
 ↓
Old Cache
```

必须考虑：

```text
Invalidate
```

否则用户可能看到旧数据。

更好的方式：

```text
Document Version
```

例如：

```text
doc:v12
```

更新：

```text
v13
```

新查询自然使用：

```text
v13
```

---

# 34. HikariCP

Spring Boot 默认常用：

```text
HikariCP
```

负责：

```text
DB Connection Pool
```

架构：

```text
Spring Boot
     │
 HikariCP
     │
 ┌───┼───┐
 ▼   ▼   ▼
DB Connection
```

---

# 35. 为什么 Connection Pool 很重要？

错误：

```text
每个 Request
 ↓
New DB Connection
 ↓
Query
 ↓
Close
```

高并发：

```text
1000 requests
 ↓
1000 connections
```

RDS 很容易崩。

---

# 36. Hikari Pool

例如：

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
```

**这些不是万能的生产固定值。**

必须根据：

```text
RDS max_connections
Pod 数量
Query latency
CPU
```

进行计算和压测。

---

# 37. 最危险的错误

假设：

```text
10 Pods
```

每个：

```text
maximumPoolSize = 50
```

那么理论上：

```text
10 × 50
=
500 connections
```

如果 RDS：

```text
max_connections = 300
```

就可能出问题。

---

# 38. Connection Budget

必须计算：

```text
Total DB Connections
≈
Pod Count × Pool Size
```

例如：

```text
10 Pods
×
20 connections
=
200
```

然后留出：

```text
Admin
Monitoring
Migration
Other Services
```

的空间。

---

# 39. RDS Proxy

当：

```text
EKS Pods
 ↓
大量 connections
 ↓
RDS
```

可以考虑：

```text
EKS
 ↓
RDS Proxy
 ↓
RDS
```

---

# 40. RDS Proxy 作用

主要价值之一：

```text
Connection Pooling
```

架构：

```text
Pod 1 ─┐
Pod 2 ─┤
Pod 3 ─┤
Pod 4 ─┤
       ▼
   RDS Proxy
       │
       ▼
      RDS
```

可以减少数据库直接承受大量连接建立/断开的压力。

---

# 41. 什么时候考虑 RDS Proxy？

例如：

```text
Pod 数量动态变化
```

或者：

```text
Lambda + EKS
```

同时连接数据库。

或者：

```text
Connection Churn
```

明显。

不是所有系统都必须使用 RDS Proxy。

---

# 42. RDS Proxy + HikariCP

不要理解成：

```text
Hikari
没用了
```

而是：

```text
Spring Boot
 ↓
HikariCP
 ↓
RDS Proxy
 ↓
RDS
```

两层连接管理。

但要合理配置池大小，避免过度堆积连接。

---

# 43. RDS Proxy 的另一个价值

可以帮助：

```text
Failover
```

减少应用对数据库连接切换的感知。

但应用仍然必须正确处理：

```text
Connection Errors
Retries
Transactions
```

---

# 44. RDS Backup

生产：

```text
Automated Backup
```

同时：

```text
Point-in-Time Recovery
```

非常重要。

---

# 45. RDS Snapshot

在：

```text
Production Migration
Major Schema Change
```

之前：

```text
Snapshot
```

但：

> Snapshot 不能替代完整的备份和恢复演练。

---

# 46. Recovery Test

企业环境不能只说：

```text
"We have backup."
```

应该真正测试：

```text
Backup
 ↓
Restore
 ↓
Application
 ↓
Verify
```

---

# 47. RDS Monitoring

Day 45 的 Observability：

```text
RDS
├── CPU
├── Memory
├── Connections
├── Read IOPS
├── Write IOPS
├── Latency
└── Storage
```

---

# 48. PostgreSQL Slow Query

开启：

```text
Performance Insights
```

以及 PostgreSQL 相应日志/监控。

找：

```text
Slow Query
```

例如：

```sql
SELECT ...
FROM document_chunks
WHERE tenant_id = ?
ORDER BY embedding <=> ?
LIMIT 5;
```

检查：

```text
EXPLAIN
EXPLAIN ANALYZE
```

---

# 49. 不要盲目使用 EXPLAIN ANALYZE

生产：

```text
EXPLAIN ANALYZE
```

会真正执行查询。

所以在高风险查询上：

```text
Staging
 ↓
EXPLAIN ANALYZE
```

更安全。

---

# 50. RAG Database Schema

推荐第一版：

```text
users
tenants
documents
document_chunks
conversations
messages
agent_runs
tool_calls
evaluations
```

---

# 51. documents

```sql
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    name TEXT NOT NULL,

    source TEXT,

    version INTEGER DEFAULT 1,

    created_at TIMESTAMP NOT NULL
);
```

---

# 52. document_chunks

```sql
CREATE TABLE document_chunks (
    id BIGSERIAL PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    document_id BIGINT NOT NULL,

    chunk_index INTEGER NOT NULL,

    content TEXT NOT NULL,

    embedding VECTOR(1536),

    metadata JSONB,

    created_at TIMESTAMP NOT NULL
);
```

---

# 53. Metadata

JSONB 很适合：

```json
{
  "page": 10,
  "section": "Benefits",
  "source": "employee-handbook.pdf"
}
```

可以保存：

```text
Page
Section
Author
Source
Tags
```

---

# 54. Tenant Index

至少：

```sql
CREATE INDEX idx_chunks_tenant
ON document_chunks(tenant_id);
```

文档：

```sql
CREATE INDEX idx_documents_tenant
ON documents(tenant_id);
```

---

# 55. Composite Index

如果查询经常：

```text
tenant_id
+
document_id
```

可以：

```sql
CREATE INDEX idx_chunks_tenant_document
ON document_chunks(
    tenant_id,
    document_id
);
```

最终索引必须基于实际查询模式设计。

---

# 56. pgvector + Tenant

需要特别注意：

```text
WHERE tenant_id = ?
ORDER BY embedding <=> ?
```

数据规模变大后：

```text
Tenant Filter
+
Vector Index
```

的查询计划需要实际 benchmark。

对于多租户大型系统，可能需要进一步考虑：

```text
Partitioning
Tenant-specific indexes
Separate vector stores
```

具体方案取决于 tenant 数量和数据规模。

---

# 57. RAG Pipeline

今天最终：

```text
                 User Query
                     │
                     ▼
               Spring Boot
                     │
                     ▼
                   Redis
                  /     \
               HIT       MISS
                         │
                         ▼
                  Embedding Model
                         │
                         ▼
                   pgvector
                         │
                       Top-K
                         │
                         ▼
                     Reranker
                         │
                         ▼
                       LLM
                         │
                         ▼
                      Answer
```

---

# 58. Cache Strategy

可以分三层：

```text
L1
Application Memory

L2
Redis

L3
PostgreSQL
```

例如：

```text
Request
 ↓
Local Cache
 ↓ miss
Redis
 ↓ miss
PostgreSQL
```

但不要为了“多层缓存”而复杂化。先证明 Redis 能解决真实瓶颈。

---

# 59. Redis 适合缓存什么？

非常适合：

```text
Session
Rate Limit
Short-lived Results
Frequently Accessed Metadata
Distributed Locks
Idempotency Keys
```

RAG：

```text
Embedding Cache
Retrieval Cache
```

也可以。

---

# 60. Redis 不应该作为唯一数据库

错误：

```text
Documents
 ↓
Redis only
```

正确：

```text
Documents
 ↓
PostgreSQL
      │
      ▼
Redis Cache
```

---

# 61. AI Rate Limiting

Redis 还可以：

```text
User
 ↓
Redis Counter
 ↓
100 requests/min
```

超过：

```text
429
```

这和 Day 43 的 API 安全结合起来了。

---

# 62. Idempotency

AI API：

```text
POST /documents
```

用户重复点击：

```text
2 requests
```

可能产生：

```text
2 documents
```

可以使用：

```text
Idempotency-Key
```

Redis：

```text
idempotency:<key>
```

防止重复执行。

---

# 63. Database + Queue

RAG ingestion：

```text
S3
 ↓
SQS
 ↓
Worker
 ↓
PostgreSQL
```

Worker 必须考虑：

```text
Transaction
+
Idempotency
```

---

# 64. 一个重要问题

Worker：

```text
Save DB
 ↓
Delete SQS Message
```

如果：

```text
DB Save 成功
 ↓
Delete SQS 失败
```

消息会再次出现。

所以 Worker 必须设计成：

> **Idempotent**

重复处理同一个 document 不应该造成错误数据。

---

# 65. 推荐 Idempotency Key

例如：

```text
document_id
+
version
+
chunk_index
```

数据库：

```text
UNIQUE
```

例如：

```sql
CREATE UNIQUE INDEX
idx_chunk_unique
ON document_chunks(
    tenant_id,
    document_id,
    chunk_index
);
```

---

# 66. RDS + Redis + SQS

现在三个核心组件职责非常清晰：

```text
RDS
=
Persistent Data

Redis
=
Fast / Temporary Data

SQS
=
Async Work
```

---

# 67. 最终企业数据层

```text
                     EKS
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        AI API       RAG        Worker
          │           │           │
          │           ▼           │
          │        Redis           │
          │           │           │
          └───────────┼───────────┘
                      ▼
                 RDS Proxy
                      │
                      ▼
              RDS PostgreSQL
                      │
                    pgvector
```

---

# 68. RDS 高可用

```text
                  RDS
                   │
             Multi-AZ
             ┌─────┴─────┐
             ▼           ▼
          AZ-a         AZ-b
         Primary      Standby
```

应用：

```text
EKS
 ↓
RDS Endpoint
```

不要把：

```text
具体 AZ IP
```

硬编码进 Java。

---

# 69. Redis 高可用

生产可以考虑：

```text
Amazon ElastiCache for Redis
```

或者 Redis-compatible managed service。

架构：

```text
EKS
 ↓
Managed Redis
```

而不是：

```text
EKS Pod
 ↓
自己部署 Redis
```

生产管理成本会低很多。

---

# 70. RDS Credentials

继续使用 Day 44：

```text
AWS Secrets Manager
       │
       ▼
External Secrets
       │
       ▼
Kubernetes Secret
       │
       ▼
Spring Boot
```

数据库密码绝不放：

```text
GitHub
Dockerfile
Helm values
application.yml
```

---

# 71. Spring Boot 配置

```yaml
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:5432/ai_platform
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}

    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 30000
```

生产中还要根据实际环境设置 SSL/TLS、超时、连接验证等参数。

---

# 72. RDS Proxy

如果使用：

```text
RDS Proxy
```

则：

```yaml
spring:
  datasource:
    url: jdbc:postgresql://${RDS_PROXY_ENDPOINT}:5432/ai_platform
```

---

# 73. Database Migration

不要手工：

```text
Production
 ↓
SQL
 ↓
Copy/Paste
```

使用：

```text
Flyway
```

或者：

```text
Liquibase
```

推荐 Java 项目：

```text
Spring Boot
+
Flyway
```

---

# 74. Flyway

例如：

```text
db/migration/
│
├── V1__create_tenants.sql
├── V2__create_documents.sql
├── V3__create_chunks.sql
└── V4__create_indexes.sql
```

CI/CD：

```text
Deploy
 ↓
Migration
 ↓
Application
```

生产要特别设计向后兼容的 schema migration。

---

# 75. Zero-Downtime Migration

错误：

```text
DROP COLUMN
 ↓
Deploy
```

正确：

```text
Step 1
Add new column

Step 2
Deploy code supporting both

Step 3
Backfill

Step 4
Switch traffic

Step 5
Remove old column later
```

---

# 76. Day 47 实战顺序

今天按照这个顺序：

```text
1. RDS PostgreSQL
      ↓
2. Private Subnet
      ↓
3. Security Group
      ↓
4. Multi-AZ
      ↓
5. Secrets Manager
      ↓
6. Spring Boot → RDS
      ↓
7. pgvector
      ↓
8. RAG schema
      ↓
9. Redis
      ↓
10. HikariCP
      ↓
11. SQS Worker → RDS
      ↓
12. Flyway
```

---

# 77. Day 47 验收标准

完成后：

```text
[✓] RDS Private
[✓] Multi-AZ
[✓] SG
[✓] Secrets Manager
[✓] Spring Boot → RDS
[✓] HikariCP
[✓] pgvector
[✓] Vector Search
[✓] Tenant Filtering
[✓] Redis
[✓] Cache
[✓] SQS Worker → PostgreSQL
[✓] Idempotency
[✓] Flyway
[✓] Backup
[✓] Monitoring
```

---

# 78. Day 47 面试必会

### RDS Multi-AZ vs Read Replica

```text
Multi-AZ
→ High Availability

Read Replica
→ Read Scaling
```

### Redis vs PostgreSQL

```text
Redis
→ Cache / Fast Data

PostgreSQL
→ Persistent Source of Truth
```

### HikariCP 为什么需要？

```text
Connection Pool
→ Reduce connection overhead
→ Control DB connections
```

### RDS Proxy 为什么？

```text
Application
 ↓
RDS Proxy
 ↓
RDS
```

主要用于连接管理、连接复用和更平滑的数据库故障切换场景。

---

# 79. Day 47 最重要的一张图

```text
                         USER
                           │
                           ▼
                          ALB
                           │
                           ▼
                         EKS
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           AI API         RAG         Worker
              │            │            │
              │            ▼            │
              │          Redis          │
              │            │            │
              └────────────┼────────────┘
                           ▼
                      HikariCP
                           │
                           ▼
                       RDS Proxy
                           │
                           ▼
                  RDS PostgreSQL
                           │
                      ┌────┴────┐
                      ▼         ▼
                  Business   pgvector
                    Data        │
                                ▼
                           Vector Search
```

---

# 80. 到 Day 47，你的平台已经变成

```text
GitHub
   ↓
CI/CD
   ↓
Docker
   ↓
ECR
   ↓
Helm
   ↓
EKS
   │
   ├── ALB / HTTPS
   ├── IAM / Pod Identity
   ├── Observability
   ├── HPA
   ├── KEDA
   └── Karpenter
          │
          ├── Spring Boot AI API
          ├── RAG Worker
          └── MCP
                 │
                 ├── Bedrock
                 ├── Redis
                 ├── RDS PostgreSQL
                 ├── pgvector
                 └── SQS
```

**Day 48** 下一步进入 **AWS S3 + SQS + RAG Document Ingestion Pipeline**：从用户上传 PDF/Word/Excel 开始，到 **S3 → Event → SQS → EKS Worker → 文档解析 → Chunking → Embedding → pgvector → RAG 可检索**，把你的 AI 应用真正做成一个完整的企业 RAG 系统。
