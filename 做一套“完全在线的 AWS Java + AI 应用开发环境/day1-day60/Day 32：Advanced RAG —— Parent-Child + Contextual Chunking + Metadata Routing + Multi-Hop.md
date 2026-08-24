
# Day 32：Advanced RAG —— Parent-Child + Contextual Chunking + Metadata Routing + Multi-Hop

今天解决 Day 31 之后最重要的问题：

> **普通 Chunking 为什么会导致 RAG 找到“正确的句子”，却回答不完整？**

例如 PDF 中：

```text
Chapter 5
  ↓
5.2 ECS Deployment
  ↓
5.2.1 Fargate
  ↓
一段 800 字说明
```

用户问：

> Fargate 的 CPU、Memory 和网络配置有什么要求？

普通 RAG 可能只找到其中一句：

```text
"Fargate tasks use ENI..."
```

但真正回答需要：

```text
CPU
+
Memory
+
Network
+
Deployment Context
```

今天把 RAG 升级成：

```text
Document
   ↓
Contextual Chunking
   ↓
Parent / Child
   ↓
Embedding Child
   ↓
Retrieve Child
   ↓
Expand Parent
   ↓
Metadata Filter
   ↓
Multi-Hop Retrieval
   ↓
Reranker
   ↓
LLM
```

---

# 1. Day 32 四个核心技术

今天重点：

```text
① Contextual Chunking
② Parent-Child Retrieval
③ Metadata Routing
④ Multi-Hop RAG
```

最终：

```text
                 User
                  │
                  ▼
              Query Router
                  │
                  ▼
             Query Rewrite
                  │
                  ▼
           Metadata Routing
                  │
                  ▼
           Hybrid Retrieval
                  │
                  ▼
              Child Chunks
                  │
                  ▼
             Parent Expand
                  │
                  ▼
               Reranker
                  │
                  ▼
            Multi-Hop Search
                  │
                  ▼
                LLM
```

---

# 2. 为什么普通 Chunking 有问题

假设原文：

```text
AWS ECS Deployment

Amazon ECS allows users to deploy
containerized applications...

Fargate provides serverless compute...

CPU and memory are configured
at task definition level...

Networking uses ENI...
```

如果简单：

```text
chunk_size = 500
```

可能变成：

```text
Chunk 1
Amazon ECS allows...

Chunk 2
Fargate provides...

Chunk 3
CPU and memory...

Chunk 4
Networking...
```

用户问：

> Fargate 怎么配置 CPU、Memory 和 Networking？

检索：

```text
Chunk 3
```

可能没有：

```text
Chunk 4
```

于是答案不完整。

---

# 3. Parent-Child Retrieval

解决方法：

```text
Parent
│
├── Child 1
├── Child 2
├── Child 3
└── Child 4
```

Embedding：

```text
Child
```

Search：

```text
Child
```

最终返回：

```text
Parent
```

---

# 4. 为什么这样更好

例如：

```text
Parent:
ECS Fargate Deployment

Child 1:
CPU configuration

Child 2:
Memory configuration

Child 3:
Networking

Child 4:
Task definition
```

用户：

> Fargate CPU、Memory 和 Network 怎么配置？

检索：

```text
Child 1
Child 2
Child 3
```

然后：

```text
Child
 ↓
Parent
```

得到完整上下文：

```text
ECS Fargate Deployment
```

---

# 5. 数据库结构

Day 26 的：

```text
document_chunks
```

升级。

推荐：

```sql
CREATE TABLE document_parents (
    id UUID PRIMARY KEY,
    document_id UUID NOT NULL,
    parent_index INT NOT NULL,
    content TEXT NOT NULL,
    metadata JSONB,
    created_at TIMESTAMP NOT NULL
);
```

Child：

```sql
CREATE TABLE document_chunks (
    id UUID PRIMARY KEY,
    parent_id UUID NOT NULL,
    document_id UUID NOT NULL,
    chunk_index INT NOT NULL,
    content TEXT NOT NULL,
    embedding VECTOR(1536),
    metadata JSONB,
    created_at TIMESTAMP NOT NULL
);
```

注意：

实际 embedding dimension 必须与你选择的 embedding model 一致，不要固定照搬 `1536`。

---

# 6. 数据关系

```text
Document
   │
   ├── Parent 1
   │      ├── Child 1
   │      ├── Child 2
   │      └── Child 3
   │
   ├── Parent 2
   │      ├── Child 4
   │      ├── Child 5
   │      └── Child 6
   │
   └── Parent 3
```

---

# 7. Parent 和 Child 的大小

一个实用起点：

```text
Parent:
1,500–3,000 tokens

Child:
300–700 tokens
```

例如：

```text
Parent = 2,000 tokens

Child:
500
500
500
500
```

然后：

```text
Embedding → Child
```

不是：

```text
Embedding → Parent
```

---

# 8. 为什么 Child 更适合 Embedding

Embedding 需要：

```text
语义集中
```

如果：

```text
Parent = 3,000 tokens
```

里面包含：

```text
CPU
Memory
Networking
IAM
Deployment
Logging
```

Embedding 会变得模糊。

Child：

```text
CPU Configuration
```

语义更加明确。

---

# 9. Parent Expansion

Search：

```text
Question
 ↓
Child Search
 ↓
Child 8
Child 21
Child 44
```

然后：

```text
Child
 ↓
parent_id
 ↓
Parent
```

最终：

```text
Parent 2
Parent 5
Parent 8
```

---

# 10. Java Service

创建：

```text
ParentChildRetrievalService
```

```java
public List<DocumentParent> retrieve(
        String query,
        String tenantId) {

    List<DocumentChunk> children =
        childSearch.search(
            query,
            tenantId
        );

    return parentRepository
        .findByIds(
            children.stream()
                .map(DocumentChunk::getParentId)
                .distinct()
                .toList()
        );
}
```

---

# 11. 注意去重

假设：

```text
Child 1 → Parent A
Child 2 → Parent A
Child 3 → Parent B
```

不要返回：

```text
Parent A
Parent A
Parent B
```

应该：

```text
Parent A
Parent B
```

所以：

```java
.distinct()
```

非常重要。

---

# 12. Parent 不代表全部原文

不要：

```text
整个 100 页 PDF
```

作为 Parent。

Parent 应该是：

```text
Section
Subsection
Logical Block
```

例如：

```text
5.2 ECS Deployment
```

而不是：

```text
整个 Chapter
```

---

# 13. Contextual Chunking

普通：

```text
Chunk = 500 tokens
```

Contextual Chunk：

```text
Document Title
+
Section
+
Subsection
+
Original Chunk
```

例如原始：

```text
CPU is configured at task level.
```

Contextual：

```text
Document: AWS ECS Guide
Section: ECS Fargate
Topic: Task Definition
Content:
CPU is configured at task level.
```

Embedding 会更准确。

---

# 14. Contextual Chunk Schema

数据库：

```text
content
```

保存：

```text
document_title
section_title
subsection_title
content
```

例如：

```json
{
  "document": "AWS ECS Guide",
  "section": "Fargate",
  "subsection": "Task Definition",
  "content": "CPU is configured..."
}
```

---

# 15. 为什么 Context 很重要

原始：

> CPU is configured at task level.

单独拿出来：

```text
CPU 是什么 CPU？
```

加入上下文：

```text
AWS ECS
Fargate
Task Definition
CPU is configured at task level.
```

语义清楚很多。

---

# 16. Chunking 不要只按字符切

错误：

```text
substring(0, 1000)
substring(1000, 2000)
```

可能把：

```text
标题
表格
代码
列表
```

切断。

应该尽量按：

```text
Document
 ↓
Chapter
 ↓
Section
 ↓
Paragraph
 ↓
Sentence
```

进行。

---

# 17. 技术文档 Chunking

Java/Spring/AWS 文档尤其建议保留：

```text
Code Block
API Signature
Table
Heading
Bullet List
```

例如：

````text
## ECS Task Definition

```yaml
cpu: 1024
memory: 2048
````

````

代码块不能随便切开。

---

# 18. Markdown Chunking

如果 PDF 转成 Markdown：

```text
# AWS ECS

## Fargate

### CPU

...

### Memory

...

## Networking
````

可以直接利用：

```text
Heading Hierarchy
```

建立：

```text
Parent
Child
```

这比简单 token splitting 更好。

---

# 19. Metadata Routing

Day 31：

```text
Question
 ↓
Hybrid Search
```

Day 32：

```text
Question
 ↓
Metadata Router
 ↓
Search relevant subset
```

例如：

```text
User:
What is our Java deployment architecture?
```

Metadata：

```text
technology = java
environment = production
document_type = architecture
```

---

# 20. Metadata

建议至少：

```text
tenant_id
document_id
document_type
department
technology
environment
version
language
created_at
updated_at
access_level
```

例如：

```json
{
  "tenant_id": "company-a",
  "technology": "java",
  "environment": "production",
  "document_type": "architecture"
}
```

---

# 21. Metadata Filter

用户：

> 查询生产环境 Java 部署架构。

先：

```text
technology = java
environment = production
```

再：

```text
Vector Search
```

而不是：

```text
全公司所有文档
 ↓
Vector Search
```

---

# 22. Metadata Routing 的好处

```text
10,000,000 chunks
```

先过滤：

```text
technology = Java
```

剩：

```text
800,000
```

再：

```text
environment = production
```

剩：

```text
100,000
```

再：

```text
Vector Search
```

效果：

```text
Recall ↑
Precision ↑
Latency ↓
```

当然，过度过滤会损失 Recall，所以 Metadata Filter 也需要评估。

---

# 23. Metadata 不应该完全由 LLM 控制

错误：

```text
LLM:
tenant_id = company-B
```

正确：

```text
JWT
 ↓
tenant_id
 ↓
Security Context
```

而：

```text
technology
environment
document_type
```

这些业务过滤条件可以由 Router/LLM 提取，但必须经过：

```text
Allowed Values
Validation
```

---

# 24. Metadata Enum

例如：

```java
public enum Environment {

    DEV,
    TEST,
    STAGING,
    PROD
}
```

LLM 输出：

```text
production
```

转换：

```text
PROD
```

如果：

```text
super-production
```

直接拒绝。

---

# 25. Multi-Hop RAG

今天最重要的第四部分。

用户：

> 我们的 Spring Boot 服务运行在哪个 AWS 环境？它连接什么数据库？数据库使用什么网络架构？

这个问题实际上是：

```text
Question 1:
Spring Boot → AWS environment

Question 2:
Service → Database

Question 3:
Database → Network
```

需要多个检索步骤。

---

# 26. Multi-Hop

```text
Question
   │
   ▼
Hop 1
Spring Boot
   │
   ▼
ECS Fargate
   │
   ▼
Hop 2
Database
   │
   ▼
RDS PostgreSQL
   │
   ▼
Hop 3
Network
   │
   ▼
Private Subnet
```

最后：

```text
Hop 1
+
Hop 2
+
Hop 3
 ↓
Final Answer
```

---

# 27. 普通 RAG vs Multi-Hop

普通：

```text
Question
 ↓
Search
 ↓
Top 5
 ↓
Answer
```

Multi-Hop：

```text
Question
 ↓
Search
 ↓
Intermediate Entity
 ↓
New Query
 ↓
Search
 ↓
New Entity
 ↓
Search
 ↓
Answer
```

---

# 28. Multi-Hop Example

问题：

> Who owns the service that uses PostgreSQL?

第一步：

```text
Search:
PostgreSQL
```

找到：

```text
Order Service
```

第二步：

```text
Search:
Order Service owner
```

找到：

```text
Team A
```

第三步：

```text
Answer:
Team A owns the Order Service.
```

---

# 29. Multi-Hop Service

建立：

```java
public interface MultiHopRetriever {

    RagResult retrieve(
        String question,
        String tenantId
    );
}
```

内部：

```text
Hop 1
 ↓
Entity Extraction
 ↓
Hop 2
 ↓
Entity Extraction
 ↓
Final Context
```

---

# 30. Multi-Hop 最大风险

无限：

```text
Hop 1
 ↓
Hop 2
 ↓
Hop 3
 ↓
Hop 4
 ↓
...
```

所以：

```text
MAX_HOPS = 3
```

第一版：

```text
1–3 hops
```

已经足够。

---

# 31. Multi-Hop 不要什么问题都使用

简单：

> What is ECS?

不要：

```text
Hop 1
Hop 2
Hop 3
```

应该：

```text
Direct Retrieval
```

复杂：

> What AWS service hosts our Java API, what database does it use, and how is that database secured?

才适合：

```text
Multi-Hop
```

---

# 32. Query Router 升级

Day 31：

```text
SIMPLE
COMPLEX
```

Day 32：

```text
SIMPLE
COMPLEX
MULTI_HOP
```

例如：

```text
                 Question
                    │
                    ▼
               Query Router
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    SIMPLE       COMPLEX      MULTI-HOP
       │            │            │
    Search      Rewrite+RAG   Hop1→Hop2→Hop3
```

---

# 33. RAG Pipeline 最终升级

```text
Question
   ↓
Memory
   ↓
Query Router
   ↓
Metadata Routing
   ↓
Query Rewrite
   ↓
Hybrid Search
   ↓
Child Retrieval
   ↓
Parent Expansion
   ↓
RRF
   ↓
Reranker
   ↓
Multi-Hop
   ↓
Context Builder
   ↓
Bedrock
```

---

# 34. 注意 Multi-Hop 和 Rerank 的顺序

不是绝对固定。

一个实用版本：

```text
Hop
 ↓
Retrieve
 ↓
Rerank
 ↓
Extract Entity
 ↓
Next Hop
```

而不是：

```text
10,000 chunks
 ↓
Multi-Hop
```

每一步都应该：

```text
Retrieve
 ↓
Rerank
 ↓
Select
```

---

# 35. Context Builder

最终：

```java
public AgentContext build(
        String question,
        UserContext user) {

    QueryPlan plan =
        router.route(question);

    List<Document> documents =
        retrievalService.retrieve(plan);

    return contextBuilder.build(
        question,
        documents
    );
}
```

---

# 36. QueryPlan

推荐定义一个结构化对象：

```java
public record QueryPlan(

    QueryType type,

    String rewrittenQuery,

    List<String> queries,

    Map<String, String> filters,

    int maxHops
) {}
```

例如：

```json
{
  "type": "MULTI_HOP",
  "rewrittenQuery": "Java ECS PostgreSQL architecture",
  "filters": {
    "technology": "java",
    "environment": "prod"
  },
  "maxHops": 3
}
```

---

# 37. QueryPlan 的好处

以前：

```text
if
else
if
else
```

越来越复杂。

现在：

```text
Question
 ↓
QueryPlan
 ↓
Retriever
```

后面可以扩展：

```text
QueryPlan
├── SEARCH
├── SQL
├── API
├── MULTI_HOP
└── HYBRID
```

---

# 38. Enterprise Metadata

你的企业 AI 项目建议：

```text
document_metadata
```

至少：

```text
tenantId
documentId
documentType
department
project
technology
environment
version
language
securityLevel
```

例如：

```json
{
  "technology": "spring-boot",
  "project": "order-platform",
  "environment": "prod",
  "securityLevel": "internal"
}
```

---

# 39. Access Control + RAG

这是企业 RAG 最容易出问题的地方。

假设：

```text
Document A
securityLevel = INTERNAL
```

用户：

```text
INTERN
```

不能检索。

所以：

```text
User Authorization
 ↓
Metadata Filter
 ↓
RAG
```

而不是：

```text
RAG
 ↓
Authorization
```

**先过滤，后检索。**

---

# 40. Row-Level Security 思路

最终：

```text
User
 ↓
JWT
 ↓
tenant_id
role
department
securityLevel
 ↓
RAG Filter
 ↓
Search
```

例如：

```text
tenant_id = A
AND
security_level <= INTERNAL
```

---

# 41. Parent Retrieval 也必须过滤

错误：

```text
Child Search
 ↓
Parent
```

正确：

```text
Child Search
WHERE tenant_id = A
AND access_level <= user_level
 ↓
Parent
WHERE tenant_id = A
AND access_level <= user_level
```

不能只给 Child 加权限。

---

# 42. Contextual Chunking 实战

假设 Markdown：

```markdown
# AWS ECS

## Fargate

### CPU

Fargate tasks can be configured
with specific CPU values.

### Memory

Memory is configured at task level.

### Networking

Tasks use ENI networking.
```

建立：

```text
Parent 1:
Fargate

Child 1:
CPU

Child 2:
Memory

Child 3:
Networking
```

Metadata：

```json
{
  "document": "AWS ECS",
  "section": "Fargate",
  "subsection": "CPU"
}
```

---

# 43. Parent-Child 检索实例

用户：

> Fargate 的网络配置是什么？

Search：

```text
Child:
Networking
```

得到：

```text
parent_id = Fargate
```

扩展：

```text
Parent:
Fargate
```

最终 Context：

```text
Fargate
+
Networking
```

而不是只有一句：

```text
Tasks use ENI networking.
```

---

# 44. Parent Context 不要太大

如果：

```text
Parent = 10,000 tokens
```

检索一个 Child：

```text
500 tokens
```

结果把：

```text
10,000 tokens
```

全部塞给 LLM。

这是错误设计。

建议：

```text
Child
+
Parent Summary
+
Relevant Parent Sections
```

而不是整个 Parent。

---

# 45. Context Window

Day 28：

```text
12,000 tokens
```

今天继续控制：

```text
System             1,000
Memory             2,000
RAG                5,000
Tools              2,000
Question             500
Buffer             1,500
```

总计：

```text
12,000
```

具体预算应按模型上下文能力和成本目标调整。

---

# 46. RAG 最终性能优化

```text
Metadata Filter
       ↓
Candidate ↓
       ↓
Hybrid Search
       ↓
RRF
       ↓
Rerank
       ↓
Parent Expansion
       ↓
Context Compression
       ↓
LLM
```

核心原则：

> **越靠近数据库，候选越多；越靠近 LLM，数据越少、越精确。**

---

# 47. Day 32 Evaluation

今天增加：

```text
Parent-Child
Contextual Chunking
Metadata
Multi-Hop
```

建立实验：

| Version | Parent-Child | Metadata | Multi-Hop |   Recall | Precision |
| ------- | ------------ | -------- | --------- | -------: | --------: |
| V1      | ❌            | ❌        | ❌         | baseline |  baseline |
| V2      | ✅            | ❌        | ❌         |        ↑ |         ↑ |
| V3      | ✅            | ✅        | ❌         |        ↑ |         ↑ |
| V4      | ✅            | ✅        | ✅         |       ↑↑ |        ↑↑ |

不要假设一定提升。

**用你的 100-question dataset 实测。**

---

# 48. Day 32 Coding Task

今天完成：

```text
□ Parent Document
□ Child Chunk
□ Parent ID
□ Contextual Metadata
□ Metadata Filter
□ Parent Expansion
□ QueryPlan
□ Multi-Hop Retriever
□ Max Hop = 3
□ Authorization Filter
□ RAG Evaluation
```

---

# 49. Day 32 项目结构

```text
backend/
│
├── agent/
│   ├── AiAgentService.java
│   └── QueryRouter.java
│
├── rag/
│   ├── RagService.java
│   ├── QueryPlan.java
│   ├── QueryRewriteService.java
│   ├── HybridSearchService.java
│   ├── RrfService.java
│   ├── RerankService.java
│   │
│   ├── parent/
│   │   ├── ParentRetriever.java
│   │   └── ParentExpansionService.java
│   │
│   ├── metadata/
│   │   ├── MetadataRouter.java
│   │   └── MetadataFilter.java
│   │
│   └── multihop/
│       ├── MultiHopRetriever.java
│       └── HopPlanner.java
│
├── ingestion/
│   ├── ContextualChunker.java
│   └── ParentChildBuilder.java
│
└── security/
    └── RagAuthorizationService.java
```

---

# 50. Day 32 最终架构

```text
                         User
                           │
                           ▼
                        Memory
                           │
                           ▼
                     Query Router
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
           SIMPLE       COMPLEX      MULTI-HOP
              │            │            │
              │       Query Rewrite    Hop 1
              │            │            │
              │       Multi Query      ▼
              │            │          Search
              └────────────┼───────────┤
                           ▼            │
                    Metadata Filter     │
                           │            │
                           ▼            ▼
                    Hybrid Search    Rerank
                           │            │
                           ▼            ▼
                        RRF / Top K    Hop 2
                           │            │
                           ▼            ▼
                     Child Retrieval  Search
                           │            │
                           ▼            ▼
                     Parent Expand    Rerank
                           │            │
                           └──────┬─────┘
                                  ▼
                              Context
                                  │
                                  ▼
                               Bedrock
                                  │
                                  ▼
                                 SSE
                                  │
                                  ▼
                                React
```

---

# 51. Day 32 你真正要掌握的东西

不要只记：

```text
Parent-Child
```

真正要理解的是：

```text
Retrieval Architecture
```

即：

```text
                    Retrieval
                        │
       ┌────────────────┼─────────────────┐
       ▼                ▼                 ▼
   Candidate         Precision          Context
   Retrieval         Ranking            Building
       │                │                 │
       ▼                ▼                 ▼
Vector/Keyword      Reranker        Parent Expansion
Metadata            RRF             Compression
Multi Query
```

---

# Day 31 → Day 32

昨天：

```text
Query Rewrite
+
Hybrid
+
Reranker
```

今天：

```text
Query Rewrite
+
Hybrid
+
Reranker
+
Parent-Child
+
Contextual Chunking
+
Metadata Routing
+
Multi-Hop
```

你的 RAG 已经从：

**“向量数据库搜索”**

开始进入：

**“企业级 Retrieval System”。**

**Day 33** 建议进入 **MCP（Model Context Protocol）+ Spring AI MCP**：把你的 Java 企业系统能力标准化成 MCP Tools/Resources，让 Agent 可以连接数据库、RAG、内部 REST API 和 AWS 服务；同时学习 **MCP Client / MCP Server / Tool / Resource / Prompt** 的区别，以及企业环境下的 MCP 安全边界。
