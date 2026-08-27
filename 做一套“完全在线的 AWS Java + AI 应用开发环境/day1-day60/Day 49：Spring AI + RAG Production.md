# Day 49：Spring AI + RAG Production

今天正式进入你这套 **AWS Java + AI 企业应用**最核心的一层：

> **把 Day 48 的 S3 → SQS → Worker → Embedding → pgvector，真正接入 Spring Boot，实现生产级 RAG。**

最终做到：

```text
用户问题
   ↓
Spring Boot
   ↓
Query Embedding
   ↓
pgvector
   ↓
Metadata Filter
   ↓
Top-K
   ↓
Reranker
   ↓
Prompt
   ↓
LLM
   ↓
Answer + Citation
```

---

# 1. Day 49 最终架构

```text
                         User
                           │
                           ▼
                    Spring Boot API
                           │
                           ▼
                     RAG Service
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
          Redis        Query Embed    Conversation
             │             │             │
             │             ▼             │
             │        pgvector           │
             │             │             │
             │          Top-K             │
             │             ▼             │
             │         Reranker           │
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                         Prompt
                           │
                           ▼
                        Bedrock
                           │
                           ▼
                  Answer + Citations
```

---

# 2. 今天学习 10 个核心知识

```text
① Spring AI
② ChatClient
③ EmbeddingModel
④ VectorStore
⑤ pgvector
⑥ Metadata Filtering
⑦ Hybrid Search
⑧ Reranking
⑨ Prompt Template
⑩ Citation
```

---

# 3. Spring AI 是什么？

你的 Java AI 应用：

```text
Spring Boot
     │
     ▼
Spring AI
     │
 ┌───┼────────┐
 ▼   ▼        ▼
LLM Embedding VectorStore
```

Spring AI 帮你统一：

```text
Chat
Embedding
Vector Store
Prompt
Tool Calling
Memory
```

---

# 4. 为什么不用直接调用 Bedrock SDK？

当然可以：

```text
Spring Boot
 ↓
AWS SDK
 ↓
Bedrock
```

但应用代码会越来越多：

```text
Model Request
Response
Prompt
Embedding
Vector Store
Tool
Memory
```

Spring AI 可以提供更高层的抽象：

```text
Spring Boot
 ↓
Spring AI
 ↓
AWS
```

对于你现在的 Java 学习路线，非常值得掌握。

---

# 5. 项目依赖

核心：

```xml
<dependency>
    <groupId>org.springframework.ai</groupId>
    <artifactId>spring-ai-starter-model-bedrock-converse</artifactId>
</dependency>
```

Embedding / Vector Store 则根据你选择的模型和 Spring AI 版本添加对应 starter。

这里不要把依赖版本硬编码成教程里的旧版本，实际项目应使用当前 Spring AI BOM。

---

# 6. Spring AI BOM

推荐使用 BOM：

```xml
<dependencyManagement>
    <dependencies>

        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>${spring-ai.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

    </dependencies>
</dependencyManagement>
```

然后：

```xml
<properties>
    <spring-ai.version>当前稳定版本</spring-ai.version>
</properties>
```

---

# 7. AI Model

例如：

```text
Spring AI
   │
   ▼
Amazon Bedrock
   │
   ├── Chat Model
   │
   └── Embedding Model
```

注意：

> Chat Model 和 Embedding Model 是两件不同的事情。

---

# 8. Chat Model

负责：

```text
Question
+
Context
 ↓
LLM
 ↓
Answer
```

---

# 9. Embedding Model

负责：

```text
Text
 ↓
Embedding Model
 ↓
Vector
```

例如：

```text
"What is vacation policy?"
```

变成：

```text
[0.12, -0.34, 0.82, ...]
```

---

# 10. RAG 的核心

传统 LLM：

```text
Question
 ↓
LLM
 ↓
Answer
```

RAG：

```text
Question
 ↓
Retrieve Knowledge
 ↓
Context
 ↓
LLM
 ↓
Answer
```

核心区别：

> **LLM 不直接知道你的企业内部文档；RAG 先把相关资料找出来。**

---

# 11. Query Embedding

用户：

```text
What is our vacation policy?
```

首先：

```text
Query
 ↓
Embedding Model
 ↓
Vector
```

---

# 12. Vector Search

然后：

```text
Query Vector
      │
      ▼
pgvector
      │
      ▼
Similarity Search
      │
      ▼
Top-K
```

例如：

```text
Top 20
```

---

# 13. Metadata Filter

这是企业 RAG 最重要的安全功能之一。

假设：

```text
Tenant A
Tenant B
```

用户属于：

```text
Tenant A
```

查询必须：

```text
tenant_id = A
```

否则可能发生：

```text
Tenant A User
      ↓
Vector Search
      ↓
Tenant B Document
```

这是严重的数据隔离漏洞。

---

# 14. RAG 查询必须带 Tenant

例如：

```java
SearchRequest.builder()
    .query(query)
    .topK(20)
    .filterExpression(
        "tenant_id == 'tenant-a'"
    )
    .build();
```

具体 filter 语法取决于你所使用的 Spring AI VectorStore 实现。

---

# 15. 不要从用户输入直接拼 SQL

错误：

```java
String sql =
    "WHERE tenant_id = '" + tenantId + "'";
```

正确：

```text
Prepared Parameters
+
Validated Tenant Context
```

更好的企业设计：

```text
JWT
 ↓
SecurityContext
 ↓
TenantContext
 ↓
RAG Service
 ↓
Vector Filter
```

用户不能自己提交：

```text
tenantId=other-company
```

来改变搜索范围。

---

# 16. Tenant Context

例如：

```java
public record TenantContext(
    String tenantId
) {}
```

从认证信息得到：

```text
JWT
 ↓
tenant_id
 ↓
TenantContext
```

---

# 17. RAG Service

建议：

```java
@Service
public class RagService {

    public RagResponse ask(
            String question,
            TenantContext tenant) {

        // 1. retrieve
        // 2. rerank
        // 3. build prompt
        // 4. invoke LLM
        // 5. return answer + citations
    }
}
```

---

# 18. 不要把 RAG 全写进 Controller

错误：

```java
@PostMapping("/chat")
public ResponseEntity<?> chat(...) {

    // embedding
    // vector search
    // prompt
    // llm
    // citation
    // ...
}
```

应该：

```text
Controller
 ↓
RagService
 ↓
Retriever
 ↓
Reranker
 ↓
PromptBuilder
 ↓
ChatModel
```

---

# 19. 推荐分层

```text
api/
 └── ChatController

application/
 └── RagService

retrieval/
 ├── VectorRetriever
 └── HybridRetriever

reranking/
 └── Reranker

prompt/
 └── RagPromptBuilder

llm/
 └── ChatService

citation/
 └── CitationService
```

这更接近企业 Java 项目。

---

# 20. Retriever

职责：

```text
Question
 ↓
Search
 ↓
Documents
```

例如：

```java
public interface Retriever {

    List<Document> retrieve(
        String query,
        TenantContext tenant
    );
}
```

---

# 21. VectorRetriever

```text
Question
 ↓
Embedding
 ↓
pgvector
 ↓
Top-K
```

---

# 22. Top-K 不要固定死

例如：

```text
Top-K = 5
```

不是永远最佳。

你应该实验：

```text
K = 3
K = 5
K = 10
K = 20
```

然后使用 Day 36 的 Evaluation：

```text
Retrieval Recall
Answer Correctness
Faithfulness
Latency
Cost
```

选择最佳参数。

---

# 23. Retrieval Pipeline

推荐：

```text
Query
 ↓
Vector Search
 ↓
Top 20
 ↓
Metadata Filter
 ↓
Reranker
 ↓
Top 5
```

注意：

> Metadata Filter 通常应尽可能在检索阶段就完成，而不是先取出大量跨租户数据再在应用层过滤。

---

# 24. Reranking

为什么需要？

Vector Search：

```text
Semantic Similarity
```

不一定意味着：

```text
Best Answer Context
```

所以：

```text
Top 20
 ↓
Reranker
 ↓
Top 5
```

---

# 25. Reranker 输入

例如：

```text
Query:
"What is the vacation policy?"

Document 1:
"Employees receive 15 vacation days..."

Document 2:
"Employees can request sick leave..."

Document 3:
"Vacation requests must be approved..."
```

Reranker：

```text
Score
Document 1 = 0.96
Document 3 = 0.91
Document 2 = 0.31
```

最终：

```text
Document 1
Document 3
```

---

# 26. Hybrid Search

只使用：

```text
Vector Search
```

有时候不够。

企业文档经常包含：

```text
Employee ID
Product Code
Part Number
Policy Number
Error Code
```

例如：

```text
"ERR-50231"
```

这种精确关键词搜索可能比 embedding 更好。

所以：

```text
Hybrid Search
=
Vector Search
+
Keyword Search
```

---

# 27. Hybrid Search 架构

```text
                 Query
                   │
          ┌────────┴────────┐
          ▼                 ▼
     Vector Search      Keyword Search
          │                 │
          └────────┬────────┘
                   ▼
                Fusion
                   │
                   ▼
                Reranker
                   │
                   ▼
                 Top-K
```

---

# 28. PostgreSQL Keyword Search

PostgreSQL 本身支持：

```text
Full Text Search
```

可以使用：

```text
tsvector
tsquery
```

例如：

```sql
SELECT *
FROM document_chunks
WHERE
    tenant_id = :tenant
AND
    to_tsvector('english', content)
    @@ plainto_tsquery('english', :query);
```

---

# 29. Hybrid Search 第一版

可以简单实现：

```text
Vector Top 20
+
Keyword Top 20
 ↓
Deduplicate
 ↓
Reranker
 ↓
Top 5
```

这样已经比单纯 vector search 更接近企业 RAG。

---

# 30. Prompt Template

不要：

```java
String prompt =
    "Answer this question...";
```

到处复制。

建立：

```text
RagPromptBuilder
```

---

# 31. Prompt 模板

```text
You are an enterprise knowledge assistant.

Answer the user's question using ONLY
the provided context.

If the answer is not supported by the
context, say that the information
is not available.

Context:
{context}

Question:
{question}
```

---

# 32. Citation Prompt

进一步：

```text
When answering, cite the relevant
source numbers such as [1], [2].

Do not invent citations.
```

Context：

```text
[1]
Employee Handbook
Page 12
...

[2]
Benefits Guide
Page 8
...
```

---

# 33. LLM Response

最终：

```text
Employees receive 15 days of vacation
after completing the required eligibility
period. [1]

Sources:
[1] Employee Handbook, Page 12
```

---

# 34. Citation 不应该完全依赖 LLM

这是生产设计重点。

不要：

```text
LLM
 ↓
"I think source is Page 12"
```

正确：

```text
Retriever
 ↓
Document Metadata
 ↓
Citation Objects
 ↓
LLM
 ↓
Answer
+
Server-generated citations
```

---

# 35. Citation Object

例如：

```java
public record Citation(
    Long documentId,
    String documentName,
    Integer page,
    Integer chunkIndex
) {}
```

然后：

```text
Answer
+
List<Citation>
```

由后端保证引用来源真实存在。

---

# 36. API Response

推荐：

```json
{
  "answer": "Employees receive 15 days...",
  "citations": [
    {
      "documentId": 123,
      "documentName": "Employee Handbook",
      "page": 12
    }
  ],
  "traceId": "abc123"
}
```

---

# 37. 为什么返回 traceId？

Day 45：

```text
Observability
```

现在连接起来：

```text
User Request
 ↓
traceId
 ↓
RAG
 ↓
Vector Search
 ↓
LLM
```

发生问题：

```text
"为什么回答很慢？"
```

可以通过：

```text
traceId
```

查整个请求。

---

# 38. RAG Trace

理想情况下：

```text
trace
│
├── HTTP
│
├── rag.retrieve
│   ├── embedding
│   ├── vector.search
│   └── rerank
│
├── prompt.build
│
└── llm.invoke
```

这样可以看到：

```text
Vector Search = 120ms
Reranker = 180ms
LLM = 1.4s
```

---

# 39. RAG Metrics

今天把 Day 45 的 Metrics 接上：

```text
rag.requests
rag.retrieval.latency
rag.rerank.latency
rag.llm.latency
rag.tokens.input
rag.tokens.output
rag.errors
```

---

# 40. Retrieval Quality Metrics

生产不能只看：

```text
HTTP 200
```

还要：

```text
Retrieval Recall
Context Precision
Context Recall
Faithfulness
Answer Relevance
```

---

# 41. Retrieval Recall

假设：

```text
Ground Truth
=
3 relevant documents
```

系统找到了：

```text
2
```

那么：

```text
Recall = 2 / 3
       = 66.7%
```

---

# 42. Faithfulness

问题：

> What is the vacation policy?

Context：

```text
15 days
```

LLM：

```text
Employees receive 15 days.
```

高 Faithfulness。

如果 LLM：

```text
Employees receive 30 days.
```

就是：

```text
Hallucination
```

---

# 43. RAG Evaluation Dataset

建立：

```text
rag-eval/
├── questions.json
├── expected_sources.json
└── expected_answers.json
```

例如：

```json
{
  "question": "What is the vacation policy?",
  "expectedSources": [
    "employee-handbook.pdf"
  ]
}
```

---

# 44. 100 个测试问题

第一版：

```text
100 Questions
```

覆盖：

```text
Easy
Medium
Hard
No-answer
Cross-document
Exact-number
Tenant isolation
```

---

# 45. 特别重要：No Answer

测试：

> What is the company policy on flying to Mars?

如果文档没有：

```text
LLM
```

不能：

```text
自己编。
```

应该：

```text
The information is not available
in the provided documents.
```

---

# 46. Conversation Memory

企业 Chat：

```text
User:
What is our vacation policy?

Assistant:
15 days.

User:
What about managers?
```

第二个问题依赖第一个上下文。

所以：

```text
Conversation
 ↓
Memory
```

---

# 47. Memory 存哪里？

生产：

```text
PostgreSQL
=
Long-term Conversation History
```

Redis：

```text
=
Short-term / Hot Cache
```

不要只放 JVM memory：

```text
Pod 1
 ↓
Memory
```

然后：

```text
Pod 1
 ↓
restart
```

历史就没了。

---

# 48. Conversation Schema

```sql
CREATE TABLE conversations (
    id UUID PRIMARY KEY,
    tenant_id VARCHAR(100) NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    created_at TIMESTAMP NOT NULL
);
```

Messages：

```sql
CREATE TABLE messages (
    id BIGSERIAL PRIMARY KEY,
    conversation_id UUID NOT NULL,
    role VARCHAR(30) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL
);
```

---

# 49. Conversation Flow

```text
User
 ↓
Conversation ID
 ↓
Load recent messages
 ↓
RAG
 ↓
LLM
 ↓
Save user message
 ↓
Save assistant message
```

---

# 50. 不要把全部历史都发送给 LLM

例如：

```text
Conversation
=
500 messages
```

不要：

```text
500 messages
 ↓
LLM
```

成本和上下文都会爆炸。

应该：

```text
Recent Messages
+
Conversation Summary
+
Relevant RAG Context
```

---

# 51. Context Assembly

最终 Prompt：

```text
System Instructions
        +
Conversation Summary
        +
Recent Messages
        +
RAG Context
        +
Current Question
```

---

# 52. Context Window

要控制：

```text
Prompt Tokens
+
Retrieved Tokens
+
History Tokens
+
Output Tokens
```

不能超过模型上下文限制。

所以：

```text
Top-K
Chunk Size
Conversation History
```

都需要控制。

---

# 53. RAG Context Budget

例如：

```text
System       500 tokens
History     1000
RAG         3000
Question     100
Output      1000
----------------
Total       5600
```

这是一个简单的预算思维。

实际模型上下文限制和成本取决于具体模型。

---

# 54. Redis Cache

RAG：

```text
Question
 ↓
Hash
 ↓
Redis
```

Cache Key：

```text
rag:
tenant:
modelVersion:
knowledgeVersion:
queryHash
```

为什么加入：

```text
knowledgeVersion
```

因为文档更新以后：

```text
旧答案
```

不能继续使用。

---

# 55. Cache Key 示例

```text
rag:
tenant-a:
knowledge-v12:
model-v3:
8f9a21...
```

这样：

```text
Document Update
 ↓
knowledge-v13
```

自然变成：

```text
Cache Miss
```

---

# 56. Spring AI ChatClient

核心调用方式可以设计成：

```java
String answer = chatClient
    .prompt()
    .system(systemPrompt)
    .user(userPrompt)
    .call()
    .content();
```

具体 API 会随 Spring AI 版本变化，实际项目以当前版本 API 为准。

---

# 57. 更好的设计

不要：

```java
chatClient.prompt(...)
```

散落在项目几十个地方。

统一：

```java
@Service
public class LlmService {

    private final ChatClient chatClient;

    public String generate(
        String system,
        String user
    ) {
        return chatClient
            .prompt()
            .system(system)
            .user(user)
            .call()
            .content();
    }
}
```

---

# 58. RagService

```java
public RagResponse ask(
        String question,
        TenantContext tenant,
        UUID conversationId) {

    var history =
        conversationService
            .recentMessages(conversationId);

    var documents =
        retriever.retrieve(
            question,
            tenant
        );

    var reranked =
        reranker.rerank(
            question,
            documents
        );

    var prompt =
        promptBuilder.build(
            question,
            history,
            reranked
        );

    var answer =
        llmService.generate(prompt);

    return responseBuilder.build(
        answer,
        reranked
    );
}
```

---

# 59. 推荐的生产 RAG Pipeline

```text
1. Authenticate
        ↓
2. Resolve Tenant
        ↓
3. Load Conversation
        ↓
4. Query Rewrite
        ↓
5. Query Embedding
        ↓
6. Metadata Filter
        ↓
7. Vector Search
        ↓
8. Keyword Search
        ↓
9. Fusion
        ↓
10. Reranking
        ↓
11. Context Compression
        ↓
12. Prompt
        ↓
13. LLM
        ↓
14. Citation
        ↓
15. Persist Conversation
        ↓
16. Metrics / Trace
```

---

# 60. Query Rewrite

用户：

> What about managers?

系统可能重写成：

```text
"What is the vacation policy
for managers?"
```

因为：

```text
Previous question:
"What is our vacation policy?"
```

所以：

```text
Conversation
+
Current Question
 ↓
Standalone Query
```

---

# 61. 为什么 Query Rewrite 有用？

原问题：

```text
"What about managers?"
```

直接 embedding：

```text
语义不完整
```

Rewrite：

```text
"What is the vacation policy
for managers?"
```

然后：

```text
Vector Search
```

质量通常会更好，但必须通过你的评测集验证。

---

# 62. Query Rewrite 也有风险

如果 LLM rewrite 错：

```text
"What about managers?"
```

变成：

```text
"What is the manager salary?"
```

检索就会错。

所以：

```text
Rewrite
+
Evaluation
```

非常重要。

---

# 63. Security Pipeline

最终：

```text
JWT
 ↓
Tenant
 ↓
Authorization
 ↓
Metadata Filter
 ↓
Vector Search
```

不要：

```text
Vector Search
 ↓
Authorization
```

才去过滤。

尽可能从检索入口就隔离。

---

# 64. 企业 RAG 的权限模型

例如：

```text
Tenant A
│
├── HR
├── Finance
└── Engineering
```

用户：

```text
Engineering User
```

只能：

```text
tenant=A
department=engineering
```

查询：

```text
WHERE
tenant_id = A
AND
department = engineering
```

---

# 65. 更复杂的 Metadata

Chunk：

```json
{
  "tenant_id": "A",
  "department": "engineering",
  "classification": "internal",
  "document_id": 123
}
```

检索：

```text
tenant_id = A
AND
department = engineering
AND
classification <= user's clearance
```

企业 RAG 的安全性实际上很大一部分来自：

> **正确的 metadata filtering + authorization。**

---

# 66. Day 49 API

推荐：

```http
POST /api/v1/chat
```

Request：

```json
{
  "conversationId": "uuid",
  "message": "What is our vacation policy?"
}
```

Response：

```json
{
  "conversationId": "uuid",
  "answer": "Employees receive...",
  "citations": [
    {
      "documentId": 123,
      "documentName": "Employee Handbook",
      "page": 12
    }
  ],
  "traceId": "abc123"
}
```

---

# 67. Error Handling

例如：

```text
Vector DB unavailable
```

不要返回：

```json
{
  "answer": "I don't know."
}
```

应该明确：

```text
RAG retrieval service unavailable
```

HTTP 可以：

```text
503 Service Unavailable
```

而：

```text
No relevant document found
```

是正常业务结果，不一定是 5xx。

---

# 68. 超时设计

例如：

```text
HTTP
= 30 sec

Retrieval
= 2 sec

Reranker
= 3 sec

LLM
= 20 sec
```

不要：

```text
HTTP 30 sec
LLM 30 sec
```

完全没有余量。

应该给每层：

```text
Timeout
Retry
Circuit Breaker
```

---

# 69. Retry 不要重试所有错误

适合 Retry：

```text
429
503
Network Timeout
```

不适合：

```text
400
401
403
Invalid Request
```

否则会：

```text
Retry
 ↓
Retry
 ↓
Retry
 ↓
成本 ↑
```

---

# 70. Circuit Breaker

例如：

```text
Bedrock
 ↓
大量 5xx
```

Circuit Breaker：

```text
CLOSED
 ↓
OPEN
```

暂时停止请求。

然后：

```text
OPEN
 ↓
HALF OPEN
 ↓
Test
 ↓
CLOSED
```

Java 常用：

```text
Resilience4j
```

---

# 71. Day 49 Observability

每一次 RAG：

```text
traceId
tenantId
conversationId
documentIds
```

但注意：

> 日志中不要直接记录完整用户 Prompt、完整文档内容或敏感数据。

可以记录：

```text
queryHash
documentId
chunkId
latency
tokenCount
```

---

# 72. RAG Dashboard

今天增加：

```text
RAG Requests
RAG P95
Retrieval P95
Reranker P95
LLM P95
No Result Rate
Citation Rate
Token Usage
Cost
```

---

# 73. 最重要的 RAG Metrics

建议：

```text
rag.request.count
rag.request.error
rag.retrieval.latency
rag.retrieval.empty
rag.rerank.latency
rag.llm.latency
rag.input.tokens
rag.output.tokens
rag.citation.count
```

---

# 74. Day 49 项目结构

```text
backend/
└── src/main/java/com/company/ai/
    │
    ├── api/
    │   └── ChatController.java
    │
    ├── application/
    │   └── RagService.java
    │
    ├── retrieval/
    │   ├── VectorRetriever.java
    │   ├── KeywordRetriever.java
    │   └── HybridRetriever.java
    │
    ├── reranking/
    │   └── Reranker.java
    │
    ├── prompt/
    │   └── RagPromptBuilder.java
    │
    ├── llm/
    │   └── LlmService.java
    │
    ├── citation/
    │   └── CitationService.java
    │
    ├── conversation/
    │   ├── ConversationService.java
    │   └── MessageRepository.java
    │
    ├── tenant/
    │   └── TenantContext.java
    │
    └── config/
        ├── AiConfig.java
        ├── VectorStoreConfig.java
        └── RedisConfig.java
```

---

# 75. Day 49 实战任务

今天不要只看理论，完成：

```text
[ ] 1. Spring AI 接入 Bedrock
[ ] 2. 配置 Chat Model
[ ] 3. 配置 Embedding Model
[ ] 4. 连接 PostgreSQL
[ ] 5. 连接 pgvector
[ ] 6. 实现 Vector Search
[ ] 7. 加 Tenant Filter
[ ] 8. 实现 Top-K
[ ] 9. 实现 Prompt Template
[ ] 10. 实现 LLM Answer
[ ] 11. 返回 Citation
[ ] 12. 保存 Conversation
[ ] 13. Redis Cache
[ ] 14. RAG Metrics
[ ] 15. Trace
```

---

# 76. Day 49 验收测试

上传：

```text
employee-handbook.pdf
```

然后：

```text
POST /api/v1/chat
```

问题：

> What is the vacation policy?

必须走：

```text
Question
 ↓
Embedding
 ↓
pgvector
 ↓
Top-K
 ↓
Prompt
 ↓
Bedrock
 ↓
Answer
```

返回：

```json
{
  "answer": "...",
  "citations": [
    {
      "documentName": "employee-handbook.pdf",
      "page": 12
    }
  ]
}
```

---

# 77. 第二个测试：No Answer

问题：

> What is the company's policy for buying a private jet?

如果文档没有：

```text
不能编造。
```

应该：

```text
The information is not available
in the provided documents.
```

---

# 78. 第三个测试：Tenant Isolation

创建：

```text
Tenant A
Tenant B
```

A：

```text
A-handbook.pdf
```

B：

```text
B-handbook.pdf
```

A 用户问：

```text
B-handbook.pdf
```

结果：

```text
找不到 B 的内容。
```

这项测试非常重要。

---

# 79. 第四个测试：Conversation

第一次：

```text
User:
What is the vacation policy?
```

第二次：

```text
User:
What about managers?
```

系统应该理解：

```text
"What about managers?"
```

指的是：

```text
Vacation policy for managers
```

---

# 80. Day 49 最终架构

```text
                           USER
                             │
                             ▼
                           ALB
                             │
                             ▼
                       Spring Boot
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
              Authentication       Redis
                    │                 │
                    ▼                 │
                TenantContext         │
                    │                 │
                    ▼                 │
                 RagService ◄─────────┘
                    │
          ┌─────────┼──────────┐
          ▼         ▼          ▼
       Vector     Keyword   Conversation
       Search     Search       Memory
          │         │
          └────┬────┘
               ▼
             Fusion
               │
               ▼
            Reranker
               │
               ▼
          Context Builder
               │
               ▼
          Prompt Template
               │
               ▼
             Bedrock
               │
               ▼
        Answer + Citation
               │
       ┌───────┴────────┐
       ▼                ▼
  PostgreSQL         Metrics/Trace
       │
    pgvector
```

---

# 81. Day 49 最重要的 5 个概念

一定记住：

```text
1. Retrieval
   找知识

2. Reranking
   找最相关知识

3. Prompt
   告诉 LLM 怎么使用知识

4. Citation
   告诉用户知识来自哪里

5. Evaluation
   判断 RAG 到底好不好
```

---

# 82. Day 40 → Day 49

现在你的 AWS Java + AI 平台已经形成完整闭环：

```text
GitHub
   ↓
CI/CD
   ↓
Docker
   ↓
ECR
   ↓
EKS
   │
   ├── HPA
   ├── KEDA
   ├── Karpenter
   ├── IAM
   └── Observability
         │
         ├── AI API
         ├── MCP
         └── RAG Worker
                │
                ▼
S3 → EventBridge → SQS
                │
                ▼
             Parsing
                │
             Chunking
                │
            Embedding
                │
                ▼
         PostgreSQL
             + pgvector
                │
                ▼
          Vector Search
                │
                ▼
             Rerank
                │
                ▼
             Bedrock
                │
                ▼
        Answer + Citation
```

**Day 50** 建议进入下一层：**Agent + Tool Calling + MCP Production Architecture**——把现在的 RAG Chat 升级成真正的企业 AI Agent，让 Agent 能调用 **数据库、REST API、S3、内部业务系统和 MCP Tools**，同时加入 Tool 权限、审批、Timeout、Retry、Audit 和 Human-in-the-loop。
