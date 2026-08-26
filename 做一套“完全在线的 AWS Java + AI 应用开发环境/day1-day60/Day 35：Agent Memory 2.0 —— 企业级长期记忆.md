# Day 35：Agent Memory 2.0 —— 企业级长期记忆

今天解决 Day 34 的一个核心问题：

> **Agent 每次对话结束后，如何“记住”真正有价值的信息，而不是把所有聊天记录全部塞进 Prompt？**

今天把 Memory 从简单的：

```text id="4r4j5m"
Conversation History
```

升级成：

```text id="6tq8pv"
                 Agent Memory
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   Short-Term      Long-Term      Semantic
     Memory          Memory         Memory
        │             │               │
        ▼             ▼               ▼
   Conversation    User Facts      Vector Search
        │             │               │
        └─────────────┼───────────────┘
                      ▼
                Memory Retrieval
                      │
                      ▼
                     Agent
```

---

# 1. 今天掌握 6 类 Memory

```text id="9y7k2c"
1. Short-Term Memory
2. Long-Term Memory
3. Semantic Memory
4. Episodic Memory
5. User Profile Memory
6. Working Memory
```

最终形成：

```text id="0z1j8s"
Agent
 │
 ├── Working Memory
 ├── Conversation Memory
 ├── Semantic Memory
 ├── Episodic Memory
 └── User Profile
```

---

# 2. Short-Term Memory

就是当前对话：

```text id="t7p3xm"
User:
What is ECS?

AI:
ECS is...

User:
How does it compare with EKS?
```

Agent 需要知道：

```text id="e4k9w1"
"it" = ECS
```

所以保存：

```text id="1s5j8q"
Recent Messages
```

通常只需要最近：

```text id="c8n2vy"
10–30 messages
```

而不是整个历史。

---

# 3. Long-Term Memory

长期记忆保存：

```text id="y2m7s4"
重要事实
用户偏好
项目上下文
长期目标
```

例如企业 Agent：

```text id="5g3w9k"
User works on:
Java + AWS

Preferred stack:
Spring Boot

Project:
Enterprise AI Assistant
```

这些信息以后还会使用。

---

# 4. Semantic Memory

Semantic Memory：

> **Agent 对用户/企业的长期事实认知。**

例如：

```text id="m6k4q8"
User prefers Java.
User uses AWS.
Project uses PostgreSQL.
Production runs on ECS.
```

可以存到：

```text id="n3x7p1"
PostgreSQL
+
pgvector
```

---

# 5. Episodic Memory

Episodic Memory：

> **过去发生过的重要事件。**

例如：

```text id="v8q2m5"
2026-08-20

Production order-api
CPU reached 91%.

Agent diagnosed:
JVM GC pressure.

Human approved restart.

CPU dropped to 42%.
```

以后：

> “上次 order-api CPU 高是怎么解决的？”

Agent 可以检索这个事件。

---

# 6. User Profile Memory

保存：

```text id="r4n8y2"
User Preferences
```

例如：

```json id="k7m3x9"
{
  "preferredLanguage": "English",
  "timezone": "America/Los_Angeles",
  "technicalStack": [
    "Java",
    "Spring Boot",
    "AWS"
  ]
}
```

注意：

**只保存真正需要长期使用的信息。**

---

# 7. Working Memory

Workflow 当前运行过程中的状态：

```text id="c5p8v2"
Workflow
 ↓
State
```

例如：

```json id="f3n9m6"
{
  "intent": "AWS",
  "service": "order-api",
  "cpu": 91,
  "rootCause": "JVM GC",
  "approvalRequired": true
}
```

这不是长期 Memory。

Workflow 完成后：

```text id="x8k4q1"
State
 ↓
Discard / Persist Important Event
```

---

# 8. 五层 Memory Architecture

今天推荐最终：

```text id="h2v7m9"
                   Agent
                     │
              ┌──────┴──────┐
              ▼             ▼
          Working       Memory Service
           State              │
                              │
            ┌─────────────────┼─────────────────┐
            ▼                 ▼                 ▼
       Short-Term       Semantic Memory    Episodic Memory
            │                 │                 │
            ▼                 ▼                 ▼
       PostgreSQL          pgvector          pgvector
                              │
                              ▼
                        User Profile
```

---

# 9. 不要把所有 Memory 放 Vector DB

错误：

```text id="v5q1x8"
Everything
 ↓
Embedding
 ↓
pgvector
```

例如：

```text id="n7k3p4"
userId
email
role
tenantId
timezone
```

不应该全部变成 Vector。

结构化数据：

```text id="z2m8q6"
PostgreSQL columns
```

语义数据：

```text id="j4p9x1"
pgvector
```

---

# 10. 推荐数据库

继续使用：

```text id="q6v3m8"
RDS PostgreSQL
+
pgvector
```

不需要今天引入新的 Vector Database。

---

# 11. Memory Database

增加：

```sql id="6k9t4b"
CREATE TABLE memories (
    id UUID PRIMARY KEY,
    tenant_id UUID NOT NULL,
    user_id UUID NOT NULL,
    memory_type VARCHAR(30) NOT NULL,
    content TEXT NOT NULL,
    embedding VECTOR(...),
    importance DOUBLE PRECISION,
    source VARCHAR(50),
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

`VECTOR(...)` 的维度必须与你实际使用的 embedding 模型一致。

---

# 12. Memory Type

```java id="p5x8r2"
public enum MemoryType {

    SEMANTIC,
    EPISODIC,
    PREFERENCE,
    PROJECT,
    FACT
}
```

---

# 13. Memory Example

### Semantic

```text id="q3m7v9"
User works primarily with Java and AWS.
```

### Preference

```text id="b8k2n4"
User prefers concise technical explanations.
```

### Project

```text id="x6p4s1"
AI platform uses Spring Boot,
PostgreSQL, pgvector and Bedrock.
```

### Episodic

```text id="r9v3m5"
Production order-api was restarted
after high CPU investigation.
```

---

# 14. Memory Importance

不是所有 Memory 都一样重要。

增加：

```text id="h4q7x2"
importance
```

例如：

```text id="y8n3m6"
0.1 = low
0.5 = medium
0.9 = critical
```

---

# 15. Importance 示例

```text id="k5m2p9"
"Hello"
```

```text id="e8x4q1"
importance = 0.01
```

而：

```text id="v7n3c6"
"Production uses ECS Fargate."
```

```text id="j2q8m4"
importance = 0.9
```

---

# 16. Memory Retrieval

用户：

> “我们生产环境现在用什么部署方式？”

Agent：

```text id="p3k7x5"
Question
 ↓
Memory Search
 ↓
Semantic Similarity
 ↓
Importance
 ↓
Recency
 ↓
Top Memory
```

得到：

```text id="c9m4q2"
Production uses ECS Fargate.
```

---

# 17. Memory Scoring

不要只使用：

```text id="f8v2m6"
vector similarity
```

可以：

```text id="9q3k7x"
Score =
0.60 × semantic_similarity
+
0.20 × importance
+
0.20 × recency
```

这是一个初始设计，实际权重通过评估数据调整。

---

# 18. Recency

最近发生的事件通常更重要。

例如：

```text id="s5m8q2"
2026
Production uses ECS
```

和：

```text id="j4n7x9"
2023
Production used EC2
```

用户问：

> “现在生产环境怎么部署？”

最新信息应该优先。

---

# 19. Memory Retrieval

最终：

```text id="z2p6m8"
Memory Query
      │
      ▼
Vector Similarity
      │
      ▼
Importance
      │
      ▼
Recency
      │
      ▼
Access Control
      │
      ▼
Top K
```

---

# 20. Tenant Isolation

Memory 必须：

```text id="w7q3n5"
tenant_id
+
user_id
```

查询：

```sql id="n6x2v9"
WHERE tenant_id = :tenantId
AND user_id = :userId
```

不能：

```text id="p8m4k1"
Vector Search
 ↓
全公司 Memory
```

---

# 21. Project Memory

企业 Agent 不只是记住用户。

还需要：

```text id="a4v8q2"
Project Memory
```

例如：

```text id="r5m3n7"
Project:
order-platform

Architecture:
ECS Fargate
RDS PostgreSQL
SQS
Bedrock
```

这类信息应该：

```text id="j9q2x4"
project_id
```

而不是只绑定：

```text id="k7m5p1"
user_id
```

---

# 22. Memory Scope

非常重要：

```text id="z8n4q3"
GLOBAL
TENANT
PROJECT
USER
CONVERSATION
```

例如：

```text id="w2m7k5"
Tenant Memory
```

所有该公司用户都可以使用。

而：

```text id="x9q3v6"
User Memory
```

只有用户自己可以使用。

---

# 23. Memory Scope 表

| Scope        | 用途    |
| ------------ | ----- |
| Global       | 系统级知识 |
| Tenant       | 公司级事实 |
| Project      | 项目架构  |
| User         | 用户偏好  |
| Conversation | 当前对话  |

这是企业 AI 很重要的设计。

---

# 24. Memory Retrieval 顺序

用户：

> “为什么我们的服务最近又变慢？”

先：

```text id="n3k8v2"
Conversation Memory
```

找不到：

```text id="f5m2q7"
User Memory
```

再：

```text id="x7p4n9"
Project Memory
```

最后：

```text id="c2v6m8"
Tenant Memory
```

可以形成：

```text id="h8q3m5"
Conversation
 ↓
User
 ↓
Project
 ↓
Tenant
```

---

# 25. Memory Service

建立：

```java id="q5x8n2"
public interface MemoryService {

    List<Memory> retrieve(
        String query,
        MemoryContext context
    );

    void save(Memory memory);

    void delete(UUID memoryId);
}
```

---

# 26. Memory Context

```java id="n7m3v9"
public record MemoryContext(

    UUID tenantId,

    UUID userId,

    UUID projectId,

    UUID conversationId
) {}
```

---

# 27. Agent 调 Memory

Workflow：

```text id="f4q9m2"
START
 ↓
Load Memory
 ↓
Intent
 ↓
RAG / MCP
 ↓
Analyze
 ↓
Answer
```

Load Memory：

```java id="t8p2k6"
List<Memory> memories =
    memoryService.retrieve(
        state.question(),
        context
    );
```

然后加入：

```text id="j3m7x5"
AgentState
```

---

# 28. Memory 不应该全部放 Prompt

错误：

```text id="w6q2p9"
全部 Memory
 ↓
Prompt
```

如果：

```text id="z5m8n3"
10,000 memories
```

会：

```text id="k2x7q4"
Token ↑
Cost ↑
Latency ↑
Noise ↑
```

正确：

```text id="v9p3m6"
Memory Retrieval
 ↓
Top 5
 ↓
Prompt
```

---

# 29. Memory Compression

Conversation：

```text id="h7n4q2"
100 messages
```

不要全部保存到 Prompt。

建立：

```text id="c8m3x5"
Conversation Summary
```

例如：

```text
User is investigating high CPU
on production order-api.
The service runs on ECS Fargate
and uses PostgreSQL.
```

---

# 30. Conversation Summary

数据库：

```sql id="b4q9m7"
CREATE TABLE conversation_summaries (
    conversation_id UUID PRIMARY KEY,
    summary TEXT NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

每隔：

```text id="j6n2x8"
10–20 messages
```

重新总结。

具体阈值通过 token budget 和效果测试调整。

---

# 31. Memory Write

不是：

```text id="u3p7m5"
每句话
 ↓
Memory
```

应该：

```text id="z8k4q2"
Conversation
 ↓
Memory Extraction
 ↓
Important?
 ├── NO → discard
 └── YES
       ↓
      Save
```

---

# 32. Memory Extraction

让 LLM 判断：

```text id="m5v9x3"
Is this information worth
remembering long-term?
```

例如：

> “我今天问了 ECS。”

```text id="q7n2k4"
NO
```

> “我们的生产环境统一使用 ECS Fargate。”

```text id="a8p4m6"
YES
```

---

# 33. Memory Extraction Output

不要让 LLM 返回自由文本。

使用结构化 JSON：

```json id="w4x9p2"
{
  "shouldRemember": true,
  "type": "PROJECT",
  "content": "Production runs on ECS Fargate.",
  "importance": 0.9
}
```

然后 Java：

```text id="f3k8m1"
Validate
 ↓
Policy
 ↓
Save
```

---

# 34. Memory Write Policy

重要：

```text id="y6q2v9"
LLM
 ↓
Memory Candidate
 ↓
Validation
 ↓
Authorization
 ↓
Deduplication
 ↓
Save
```

不要：

```text id="n8m3x5"
LLM
 ↓
直接写 DB
```

---

# 35. Memory Deduplication

用户重复说：

> “Production uses ECS.”

十次。

不能：

```text id="c7v2m9"
10 Memory Records
```

应该：

```text id="p4x8q1"
Existing Memory
        ↓
Similarity
        ↓
Duplicate
        ↓
Update
```

---

# 36. Memory Update

例如旧：

```text id="h5m9q3"
Production uses EC2.
```

新：

```text id="k8x2v6"
Production uses ECS Fargate.
```

应该：

```text id="q3n7m4"
Old Memory
 ↓
Superseded
 ↓
New Memory
```

不要同时认为：

```text id="z6p2x8"
EC2
+
ECS
```

都是当前事实。

---

# 37. Memory Version

可以增加：

```text id="t4m8q2"
version
is_current
superseded_by
```

例如：

```text id="n7x3p5"
Memory A
version=1
is_current=false

Memory B
version=2
is_current=true
```

---

# 38. Episodic Memory

Agent Workflow 完成：

```text id="c5q8m2"
Incident
 ↓
Diagnosis
 ↓
Approval
 ↓
Action
 ↓
Result
```

生成：

```text id="x7n3p9"
Episode
```

例如：

```text
Production incident:
order-api CPU 91%.

Diagnosis:
JVM GC pressure.

Action:
Restarted service after approval.

Result:
CPU reduced to 42%.
```

以后：

> “上次类似问题怎么解决的？”

直接检索 Episode。

---

# 39. Episode Schema

```sql id="v9m4q2"
CREATE TABLE agent_episodes (
    id UUID PRIMARY KEY,
    tenant_id UUID NOT NULL,
    project_id UUID,
    workflow_id UUID,
    summary TEXT NOT NULL,
    outcome TEXT,
    embedding VECTOR(...),
    created_at TIMESTAMP NOT NULL
);
```

同样，向量维度取决于 embedding 模型。

---

# 40. Memory 与 RAG 的区别

非常重要。

### RAG

```text id="w3k8m1"
External Knowledge
```

例如：

```text
AWS documentation
Company policies
Architecture docs
```

### Memory

```text id="p6n2x9"
Agent/User History
```

例如：

```text
User preferences
Previous incidents
Project decisions
Conversation
```

---

# 41. 两者可以一起用

用户：

> “按照我们之前确定的架构，ECS 应该怎么部署？”

Agent：

```text id="m8q3v7"
Memory
 ↓
Previous Architecture Decision
```

然后：

```text id="k5n9x2"
RAG
 ↓
Current AWS Documentation
```

最终：

```text id="a7p4m6"
Memory
+
RAG
+
Agent
```

---

# 42. Memory + RAG 最终架构

```text id="c3x8m1"
                    User
                      │
                      ▼
                  Agent State
                      │
              ┌───────┴────────┐
              ▼                ▼
           Memory              RAG
              │                │
      ┌───────┼───────┐        │
      ▼       ▼       ▼        ▼
 Conversation User  Project   Knowledge
      │       │       │        │
      └───────┼───────┘        │
              ▼                ▼
         Memory Retrieval   RAG Retrieval
              │                │
              └───────┬────────┘
                      ▼
                   Context
                      │
                      ▼
                   Bedrock
```

---

# 43. Memory Security

企业环境必须：

```text id="e7p3k9"
tenant isolation
user authorization
project authorization
```

例如：

```text id="x4n8m2"
Company A
 └── User A

Company B
 └── User B
```

User A：

```text id="z9q3v7"
不能检索 User B
```

更不能：

```text id="w6m2k5"
Tenant A
 ↓
Tenant B Memory
```

---

# 44. Sensitive Memory

Memory 特别容易意外保存敏感信息。

例如：

```text id="p5x9n3"
Password
API Key
Credit Card
Secret
Token
```

必须：

```text id="j7q2m8"
Detect
 ↓
Redact
 ↓
Reject
```

不要长期保存。

---

# 45. Memory TTL

有些 Memory 不需要永久保存。

例如：

```text id="k4m8p2"
Temporary debugging state
```

可以：

```text id="n7x3q9"
TTL = 7 days
```

而：

```text id="w2m6v5"
Project architecture decision
```

可以长期保存。

---

# 46. Memory Lifecycle

最终：

```text id="q8n3m7"
Created
   ↓
Retrieved
   ↓
Updated
   ↓
Superseded
   ↓
Archived
   ↓
Deleted
```

---

# 47. Memory Retrieval API

增加：

```text id="f5k9x2"
GET /api/memory
```

管理端可以：

```text id="m7q3p8"
查看
编辑
删除
```

用户可以：

> “Forget that I use ECS.”

系统：

```text id="a2n8v4"
Memory Search
 ↓
Find Memory
 ↓
Delete / Mark Deleted
```

---

# 48. Memory Governance

企业环境建议：

```text id="x6p2m9"
Memory Owner
Memory Source
Created By
Updated By
Retention
Access Level
```

这样以后做：

```text id="k3v8q1"
Audit
Compliance
```

会容易很多。

---

# 49. Day 35 Coding Task

今天完成：

```text id="r5n8m2"
□ MemoryService
□ MemoryContext
□ Semantic Memory
□ Episodic Memory
□ User Preference Memory
□ Project Memory
□ Memory Retrieval
□ Importance
□ Recency
□ Deduplication
□ Memory Extraction
□ Memory Update
□ Conversation Summary
□ Memory TTL
□ Tenant Isolation
□ Memory Audit
```

---

# 50. Java 项目结构

```text id="j7m3x9"
backend/
│
├── memory/
│   ├── Memory.java
│   ├── MemoryType.java
│   ├── MemoryScope.java
│   ├── MemoryContext.java
│   ├── MemoryService.java
│   ├── MemoryRetrievalService.java
│   ├── MemoryExtractionService.java
│   ├── MemoryDeduplicationService.java
│   ├── ConversationSummaryService.java
│   └── EpisodicMemoryService.java
│
├── workflow/
│   ├── AgentState.java
│   ├── CheckpointService.java
│   └── WorkflowExecutor.java
│
├── rag/
│
├── mcp/
│
└── security/
    └── MemoryAuthorizationService.java
```

---

# 51. Memory Retrieval Code

概念：

```java id="m8q2v5"
public List<Memory> retrieve(
        String query,
        MemoryContext context) {

    Vector queryVector =
        embeddingService.embed(query);

    return repository.search(
        context.tenantId(),
        context.userId(),
        context.projectId(),
        queryVector
    );
}
```

然后排序：

```text id="k4n7x3"
Similarity
+
Importance
+
Recency
```

---

# 52. Memory Extraction Code

```java id="q5v8m2"
MemoryCandidate candidate =
    memoryExtractionService
        .extract(conversation);

if (candidate.shouldRemember()) {

    policyService.validate(candidate);

    memoryService.save(
        candidate.toMemory()
    );
}
```

---

# 53. Day 35 测试

### Test 1

用户：

> “Our production system uses ECS Fargate.”

之后问：

> “What do we use in production?”

应该回答：

```text id="n2x7m4"
ECS Fargate
```

---

### Test 2

用户：

> “I prefer Spring Boot for backend services.”

新对话：

> “What backend framework should we use?”

Memory 应该被检索。

---

### Test 3

重复：

> “Production uses ECS.”

10 次。

数据库不能产生：

```text id="p8m3x7"
10 duplicates
```

---

### Test 4

Tenant A 写入：

```text id="w5q9n2"
Production uses ECS.
```

Tenant B 查询：

```text id="k3v7m1"
```

必须：

```text id="d8x2p5"
NOT FOUND
```

---

### Test 5

用户要求：

> “Forget our production architecture memory.”

系统：

```text id="q4m8n6"
Find
 ↓
Delete / Archive
 ↓
Audit
```

---

# 54. Memory Evaluation

建立：

```text id="t7x2m9"
memory-evaluation.json
```

测试：

```text id="p4n8q3"
Recall
Precision
Relevance
Freshness
Duplicate Rate
Privacy Leakage
```

特别关注：

### Memory Recall

应该找到相关记忆。

### Memory Precision

不要把无关记忆塞进 Prompt。

---

# 55. Memory Cost

如果：

```text id="r8m3q6"
每次对话
 ↓
Memory Embedding
 ↓
Memory Search
```

成本会增加。

所以：

```text id="x2p7n4"
只对候选 Memory 做 embedding
```

不是每个 token 都 embedding。

---

# 56. Memory Write Pipeline

最终：

```text id="b5n9m2"
Conversation
     │
     ▼
Memory Extraction
     │
     ▼
Importance
     │
     ▼
Security Filter
     │
     ▼
PII/Secret Detection
     │
     ▼
Deduplication
     │
     ▼
Embedding
     │
     ▼
PostgreSQL + pgvector
```

---

# 57. Memory Read Pipeline

```text id="v7q2n5"
User Question
      │
      ▼
Memory Query
      │
      ▼
Tenant/User/Project Filter
      │
      ▼
Vector Search
      │
      ▼
Importance
      │
      ▼
Recency
      │
      ▼
Top K
      │
      ▼
Context Builder
      │
      ▼
Agent
```

---

# 58. Day 35 最终架构

```text id="a8m3q7"
                           User
                             │
                             ▼
                       Agent Workflow
                             │
                           State
                             │
                ┌────────────┴────────────┐
                ▼                         ▼
             Memory                       RAG
                │                         │
      ┌─────────┼──────────┐              │
      ▼         ▼          ▼              ▼
  Short-Term Semantic   Episodic       Knowledge
      │         │          │              │
      ▼         ▼          ▼              ▼
   Recent     pgvector   pgvector       pgvector
   Messages
                │
                ▼
        Memory Retrieval
                │
                └─────────────┐
                              ▼
                           Context
                              │
                              ▼
                         Agent / MCP
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

# 59. Day 31 → Day 35

现在你的 AI 系统已经经历了 5 次升级：

```text id="n5m8q2"
Day 31
Advanced RAG
Query Rewrite
Reranker

        ↓

Day 32
Parent-Child
Metadata
Multi-Hop

        ↓

Day 33
MCP
Tools
Resources

        ↓

Day 34
Agent Workflow
State
Checkpoint
Human Approval

        ↓

Day 35
Memory 2.0
Semantic
Episodic
Project
User Memory
```

最终：

```text id="x3q7m9"
              Enterprise AI Agent
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
      Memory           RAG            MCP
        │              │              │
        └──────────────┼──────────────┘
                       ▼
                 Agent Workflow
                       │
                ┌──────┴──────┐
                ▼             ▼
             Policy        Human
                │          Approval
                └──────┬──────┘
                       ▼
                    Bedrock
                       │
                       ▼
                      SSE
                       │
                       ▼
                     React
```

**Day 36** 建议进入 **Agent Observability & Evaluation**：建立完整的 **Trace → Span → Agent Run → LLM Call → RAG Retrieval → MCP Tool → Token/Cost → Evaluation Score** 链路，用 OpenTelemetry + CloudWatch 把 Agent 从“能运行”升级到“能定位问题、能量化质量、能控制成本”。
