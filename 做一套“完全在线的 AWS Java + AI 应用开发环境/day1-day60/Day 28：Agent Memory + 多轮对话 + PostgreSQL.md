# Day 28：Agent Memory + 多轮对话 + PostgreSQL

今天解决 AI Agent 的一个核心问题：

> **用户说了上一句话，Agent 下一句话能不能记住？**

Day 27：

```text
User
 ↓
Agent
 ↓
Tools
 ↓
RAG / DB / API
 ↓
Bedrock
```

Day 28：

```text
User
 ↓
Conversation
 ↓
Memory
 ↓
Agent
 ├── RAG
 ├── Tools
 └── Bedrock
```

最终实现一个真正的：

**多轮企业 AI Assistant。**

---

# 1. 今天要完成什么

```text
[✓] Conversation
[✓] Message
[✓] PostgreSQL Memory
[✓] Session ID
[✓] User ID
[✓] Tenant ID
[✓] Conversation History
[✓] Short-term Memory
[✓] Long-term Memory 基础
[✓] RAG 与 Memory 分离
[✓] Token Window
[✓] Conversation Summary
[✓] Agent Memory
[✓] Multi-turn Chat
```

---

# 2. 最重要的三个概念

不要混在一起：

```text
Conversation Memory
Knowledge Base
User Profile
```

分别是：

```text
Conversation Memory
= 用户这次聊了什么

Knowledge Base
= 企业有什么知识

User Profile
= 用户长期偏好/信息
```

---

# 3. 举例

用户：

> 我们公司的后端使用 Spring Boot。

这是：

```text
Knowledge / Conversation
```

用户：

> 我喜欢回答简洁一点。

这是：

```text
User Preference
```

公司内部：

> Spring Boot 服务运行在 ECS Fargate。

这是：

```text
Knowledge Base
```

三者不能混为一谈。

---

# 4. 最终架构

```text
                         React
                           │
                           ▼
                    Spring Boot API
                           │
                           ▼
                     Agent Service
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          Memory        RAG          Tools
              │            │            │
              ▼            ▼            ▼
         PostgreSQL    pgvector       RDS/API
              │
              ▼
       Conversation History
              │
              ▼
            Bedrock
```

---

# 5. Session ID

每一次对话：

```text
conversationId
```

例如：

```text
conv-001
```

第一句话：

```text
User:
What is ECS?
```

第二句话：

```text
User:
How much does it cost?
```

Agent 能知道：

```text
"it" = ECS
```

因为：

```text
conv-001
```

对应之前的消息。

---

# 6. 数据库设计

建立：

```sql
CREATE TABLE conversations (
    id UUID PRIMARY KEY,
    tenant_id VARCHAR(100) NOT NULL,
    user_id VARCHAR(100) NOT NULL,
    title VARCHAR(255),
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

---

# 7. Messages

```sql
CREATE TABLE conversation_messages (
    id BIGSERIAL PRIMARY KEY,

    conversation_id UUID NOT NULL,

    role VARCHAR(30) NOT NULL,

    content TEXT NOT NULL,

    created_at TIMESTAMP NOT NULL
);
```

Role：

```text
USER
ASSISTANT
SYSTEM
TOOL
```

第一版建议至少：

```text
USER
ASSISTANT
```

---

# 8. 为什么 Conversation 要单独一张表

关系：

```text
conversations
       │
       │ 1
       ▼
conversation_messages
       │
       ├── User
       ├── Assistant
       ├── User
       └── Assistant
```

例如：

```text
Conversation A
│
├── Message 1
├── Message 2
├── Message 3
└── Message 4
```

---

# 9. Tenant Isolation

这是企业系统必须做的。

查询：

```sql
SELECT *
FROM conversations
WHERE id = :conversationId
AND tenant_id = :tenantId;
```

不能：

```text
只根据 conversationId
```

因为：

```text
Company A
 ↓
可能猜到
 ↓
Company B conversation
```

所以：

**conversationId + tenantId 必须同时验证。**

---

# 10. User Isolation

进一步：

```sql
SELECT *
FROM conversations
WHERE id = :conversationId
AND tenant_id = :tenantId
AND user_id = :userId;
```

这样：

```text
Tenant A
 ├── User 1
 └── User 2
```

User 1：

```text
只能看到自己的 Conversation
```

---

# 11. Chat API

现在：

```text
POST /api/chat
```

Request：

```json
{
  "conversationId": "conv-001",
  "message": "What is ECS?"
}
```

如果第一次：

```text
conversationId = null
```

服务器创建：

```text
conv-001
```

---

# 12. Response

```json
{
  "conversationId": "conv-001",
  "answer": "Amazon ECS is...",
  "sources": []
}
```

前端保存：

```text
conversationId
```

下一次继续发送：

```text
conversationId = conv-001
```

---

# 13. Chat Flow

完整：

```text
User Message
     │
     ▼
Authenticate
     │
     ▼
Get Conversation
     │
     ▼
Load Memory
     │
     ▼
Agent
     │
     ├── RAG
     ├── Tool
     └── Bedrock
     │
     ▼
Save Assistant Message
     │
     ▼
Response
```

---

# 14. Spring Service

```java
public ChatResponse chat(
        UUID conversationId,
        String message) {

    Conversation conversation =
        conversationService.getOrCreate(
            conversationId
        );

    memoryService.saveUserMessage(
        conversation.getId(),
        message
    );

    List<Message> history =
        memoryService.loadHistory(
            conversation.getId()
        );

    String answer =
        agentService.run(
            history,
            message
        );

    memoryService.saveAssistantMessage(
        conversation.getId(),
        answer
    );

    return new ChatResponse(
        conversation.getId(),
        answer
    );
}
```

---

# 15. 但是不能把全部历史给 LLM

假设用户聊了：

```text
1000 messages
```

全部发送：

```text
1000 messages
 ↓
Bedrock
```

问题：

```text
Token ↑
Cost ↑
Latency ↑
Context noise ↑
```

所以需要：

**Memory Window。**

---

# 16. Short-Term Memory

例如：

```text
最近 10–20 条消息
```

发送：

```text
Recent Conversation
```

而不是整个历史。

例如：

```text
Message 991
Message 992
...
Message 1010
```

---

# 17. Token Window

比“消息数量”更好的方式是：

```text
Token Budget
```

例如：

```text
Conversation Memory
最大：
4000 tokens
```

从最新消息向前加载：

```text
4000 tokens
```

---

# 18. 为什么不用固定 20 条

因为：

```text
Message A = 30 tokens
```

但：

```text
Message B = 2000 tokens
```

20 条可能：

```text
5000 tokens
```

也可能：

```text
50,000 tokens
```

所以最好基于：

**Token Budget。**

---

# 19. Memory Window

推荐第一版：

```text
Recent History:
3000–5000 tokens
```

例如：

```text
System Prompt
+
Recent Conversation
+
RAG Context
+
Tool Results
+
Current Question
```

---

# 20. Prompt 结构

建议：

```text
SYSTEM
  ↓
Conversation Summary
  ↓
Recent Conversation
  ↓
Retrieved Knowledge
  ↓
Tool Results
  ↓
Current User Question
```

最终：

```text
Prompt
├── System
├── Summary
├── History
├── RAG Context
├── Tool Results
└── Question
```

---

# 21. Conversation Summary

如果对话越来越长：

```text
Message 1–50
```

可以压缩成：

```text
Summary
```

例如：

```text
The user is building an AWS Java AI
platform using Spring Boot, ECS,
RDS PostgreSQL, pgvector and Bedrock.
```

然后：

```text
Summary
+
Recent Messages
```

---

# 22. Summary Architecture

```text
Conversation
│
├── Old Messages
│       ↓
│    Summarizer
│       ↓
│    Summary
│
└── Recent Messages
```

最终：

```text
Summary
+
Recent 10 messages
```

---

# 23. Conversation Summary 表

可以增加：

```sql
ALTER TABLE conversations
ADD COLUMN summary TEXT;

ALTER TABLE conversations
ADD COLUMN summary_updated_at TIMESTAMP;
```

或者单独建立：

```text
conversation_summaries
```

第一版直接放在 `conversations` 足够。

---

# 24. Summary 什么时候更新

例如：

```text
每 20 messages
```

或者：

```text
token > threshold
```

触发：

```text
Summarization
```

例如：

```text
100 messages
 ↓
Summary
 ↓
Recent 10 messages
```

---

# 25. Memory 和 RAG 不要混

这是 Day 28 最重要的一条。

Memory：

```text
用户之前说了什么
```

RAG：

```text
企业文档有什么
```

例如：

用户：

> 我昨天上传了 AWS ECS 文档。

Memory：

```text
User uploaded AWS ECS document.
```

但具体 ECS 内容：

```text
RAG
```

---

# 26. Memory + RAG

用户：

> 我刚才说的那个 ECS 服务怎么部署？

Agent：

```text
Memory
 ↓
知道 "那个 ECS 服务"
```

然后：

```text
RAG
 ↓
找 ECS deployment documentation
```

最后：

```text
Memory
+
RAG
+
LLM
```

---

# 27. User Profile

第三类：

```text
Long-Term Memory
```

例如：

```text
User prefers concise answers.
```

这是：

```text
User Profile
```

不要每次：

```text
Conversation
 ↓
全部永久保存
```

---

# 28. User Profile 表

例如：

```sql
CREATE TABLE user_preferences (
    id BIGSERIAL PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    user_id VARCHAR(100) NOT NULL,

    preference_key VARCHAR(100) NOT NULL,

    preference_value TEXT NOT NULL,

    updated_at TIMESTAMP NOT NULL
);
```

例如：

```text
user_id = user-123
preference_key = response_style
preference_value = concise
```

---

# 29. 三层 Memory

最终：

```text
Memory
│
├── Conversation Memory
│   └── 当前对话
│
├── User Profile
│   └── 长期偏好
│
└── Knowledge Base
    └── 企业知识
```

这是企业 AI 系统非常重要的架构边界。

---

# 30. Agent Context

最终 Agent 输入：

```text
System
+
User Profile
+
Conversation Summary
+
Recent Messages
+
RAG Context
+
Tool Results
+
Current Question
```

但不要每次全部加载。

应该：

```text
Context Builder
```

动态决定需要什么。

---

# 31. Context Builder

建立：

```text
ContextBuilder
```

负责：

```java
public AgentContext build(
        User user,
        Conversation conversation,
        String question) {
```

内部：

```text
loadProfile()
loadSummary()
loadRecentMessages()
retrieveRag()
executeTools()
```

---

# 32. Context Budget

例如：

```text
Context Budget = 12,000 tokens
```

分配：

```text
System          1,000
Profile           300
Summary           800
History         3,000
RAG             4,000
Tool Results    2,000
Current Q         300
```

总计：

```text
11,400
```

还留有余量。

---

# 33. 不要无限增加 Context

错误：

```text
More context = Better AI
```

不是。

过多 Context：

```text
Noise ↑
Cost ↑
Latency ↑
Attention dilution ↑
```

目标：

**Relevant Context。**

---

# 34. Spring AI Memory

Spring AI 提供对对话记忆的支持。

常见思路：

```text
ChatMemory
```

以及：

```text
MessageWindowChatMemory
```

可以帮助管理：

```text
Conversation
Message History
Window
```

具体类名和 API 随 Spring AI 版本变化，开发时以你项目当前版本的官方 API 为准。

---

# 35. PostgreSQL Memory

今天我们不把 Memory 放：

```text
Java List
```

因为：

```text
ECS Task restart
 ↓
Memory 消失
```

应该：

```text
ECS
 ↓
PostgreSQL
 ↓
Conversation
```

---

# 36. 为什么 PostgreSQL 很适合

你已经有：

```text
RDS PostgreSQL
```

所以可以同时保存：

```text
documents
document_chunks
conversations
conversation_messages
user_preferences
agent_runs
```

统一数据库。

---

# 37. 但不要全部放一张表

错误：

```text
ai_data
```

里面：

```text
messages
documents
users
embeddings
tools
```

全部混在一起。

推荐：

```text
documents
document_chunks

conversations
conversation_messages

user_preferences

agent_runs
agent_tool_calls
```

---

# 38. Conversation Repository

```java
public interface ConversationRepository
        extends JpaRepository<
            Conversation,
            UUID> {

    Optional<Conversation>
    findByIdAndTenantIdAndUserId(
        UUID id,
        String tenantId,
        String userId
    );
}
```

这个查询本身就实现了：

```text
Tenant Isolation
+
User Isolation
```

---

# 39. Message Repository

```java
List<ConversationMessage>
findTop20ByConversationIdOrderByCreatedAtDesc(
    UUID conversationId
);
```

然后：

```text
Newest → Oldest
```

再 reverse：

```text
Oldest → Newest
```

给 LLM。

---

# 40. Conversation API

增加：

```text
GET /api/conversations
```

返回：

```json
[
  {
    "id": "conv-001",
    "title": "AWS ECS",
    "updatedAt": "2026-08-20T10:20:00"
  }
]
```

---

# 41. 获取历史

```text
GET /api/conversations/{id}
```

返回：

```json
{
  "id": "conv-001",
  "title": "AWS ECS",
  "messages": [
    {
      "role": "USER",
      "content": "What is ECS?"
    },
    {
      "role": "ASSISTANT",
      "content": "ECS is..."
    }
  ]
}
```

---

# 42. React Chat UI

今天前端升级：

```text
┌───────────────────────────────────┐
│ Conversations                     │
│                                   │
│ AWS ECS                           │
│ RAG Architecture                  │
│ Spring AI                         │
│                                   │
├───────────────────────────────────┤
│                                   │
│ User: What is ECS?                │
│                                   │
│ AI: ECS is...                     │
│                                   │
│ User: How much does it cost?      │
│                                   │
│ AI: ...                           │
│                                   │
│ [ Type message...        ] [Send] │
└───────────────────────────────────┘
```

---

# 43. Streaming

下一步可以把：

```text
Bedrock
 ↓
完整 Response
 ↓
React
```

升级成：

```text
Bedrock
 ↓
Token Stream
 ↓
SSE
 ↓
React
```

用户看到：

```text
AI is building...
```

逐字出现。

今天可以先设计接口，Day 29 再实现 Streaming。

---

# 44. Conversation Title

第一次消息：

```text
"What is ECS Fargate?"
```

Agent 可以生成：

```text
AWS ECS Fargate
```

作为：

```text
conversation.title
```

但是标题生成不是核心功能。

可以先：

```text
first message.substring(...)
```

后面再让 LLM 自动生成。

---

# 45. Agent Memory + Tool

用户：

> How many documents do we have?

Agent：

```text
getDocumentStats()
```

然后用户：

> What about the AWS ones?

Agent 应该理解：

```text
"What about"
```

指的是：

```text
documents
```

而不是重新从零开始。

这就是：

```text
Conversation Memory
+
Agent
```

---

# 46. Agent Memory + RAG

用户：

> Explain ECS.

Agent：

```text
RAG
```

用户：

> How does it fit into our architecture?

Agent：

```text
Memory
 ↓
"ECS"
```

再：

```text
RAG
 ↓
搜索企业架构文档
```

最终：

```text
Memory
+
RAG
```

---

# 47. Agent Memory + Tool

用户：

> What's the current document count?

Agent：

```text
getDocumentStats()
```

返回：

```text
127
```

用户：

> How many were added today?

Agent：

```text
getDocumentStats(today)
```

这里：

```text
Conversation Memory
```

可以帮助理解：

```text
"were added"
```

指：

```text
documents
```

---

# 48. Memory Security

千万不要：

```text
User A
 ↓
Conversation Memory
 ↓
User B
```

每次查询必须：

```text
tenantId
+
userId
+
conversationId
```

三重验证。

---

# 49. Sensitive Data

Conversation 里可能出现：

```text
password
API key
credit card
PII
```

不要默认：

```text
全部永久保存
```

建议：

```text
Sensitive Data Detection
 ↓
Mask
 ↓
Store
```

例如：

```text
sk-abc123...
```

存储前：

```text
[REDACTED]
```

---

# 50. Conversation Retention

企业环境要考虑：

```text
30 days
90 days
1 year
```

之后：

```text
Archive
Delete
Anonymize
```

不要：

```text
Forever
```

默认保存所有对话。

具体保留期取决于业务、合同和合规要求。

---

# 51. Database Index

增加：

```sql
CREATE INDEX idx_conversation_messages_conversation
ON conversation_messages(conversation_id, created_at);
```

Conversation：

```sql
CREATE INDEX idx_conversations_user
ON conversations(tenant_id, user_id, updated_at);
```

这样：

```text
List Conversations
```

更快。

---

# 52. Agent Run

Day 27：

```text
agent_runs
```

Day 28 可以关联：

```text
conversation_id
```

例如：

```text
agent_runs
│
├── conversation_id
├── user_id
├── tenant_id
├── question
├── status
└── latency_ms
```

---

# 53. Trace

最终：

```text
/api/chat
│
├── conversation.load
│
├── memory.load
│
├── agent
│   ├── tool.searchKnowledge
│   └── tool.getDocumentStats
│
├── llm
│
└── memory.save
```

这样可以分析：

```text
Memory latency
Agent latency
Tool latency
LLM latency
```

---

# 54. Day 28 实战

今天完成一个真正的多轮对话：

### 第一句

```text
What is ECS?
```

### 第二句

```text
How does it work?
```

### 第三句

```text
How does it fit into our AWS architecture?
```

### 第四句

```text
How many ECS-related documents do we have?
```

Agent 应该能够：

```text
Memory
+
RAG
+
Tool
```

协同工作。

---

# 55. 测试 1：Memory

```text
User:
My project uses Spring Boot.

User:
What framework does my project use?
```

应该：

```text
Spring Boot
```

---

# 56. 测试 2：RAG

```text
User:
What is ECS Fargate?
```

应该：

```text
RAG
```

返回：

```text
Answer
+
Source
```

---

# 57. 测试 3：Tool

```text
User:
How many documents are in my knowledge base?
```

应该：

```text
getDocumentStats()
```

---

# 58. 测试 4：综合

```text
User:
Explain ECS.

User:
How does it fit into our architecture?

User:
How many ECS documents do we have?
```

应该：

```text
Memory
 +
RAG
 +
Tool
```

---

# 59. Day 28 项目结构

```text
backend/
│
├── conversation/
│   ├── Conversation.java
│   ├── ConversationMessage.java
│   ├── ConversationService.java
│   └── ConversationController.java
│
├── memory/
│   ├── ChatMemoryService.java
│   ├── ConversationSummaryService.java
│   └── ContextBuilder.java
│
├── agent/
│   └── AiAgentService.java
│
├── tools/
│   ├── KnowledgeTool.java
│   └── DocumentStatsTool.java
│
├── rag/
│   ├── RagService.java
│   └── HybridSearchService.java
│
├── repository/
│   ├── ConversationRepository.java
│   └── MessageRepository.java
│
└── security/
    └── TenantContext.java
```

---

# 60. Day 28 最终架构

```text
                           React
                             │
                             ▼
                      Spring Boot API
                             │
                             ▼
                       Authentication
                             │
                             ▼
                       Conversation
                             │
                  ┌──────────┴──────────┐
                  ▼                     ▼
               Memory                  Agent
                  │                     │
                  ▼            ┌────────┼────────┐
             PostgreSQL        ▼        ▼        ▼
                           RAG Tool   DB Tool   API Tool
                              │         │         │
                              ▼         ▼         ▼
                           pgvector    RDS      REST
                              │
                              ▼
                           Context
                              │
                              ▼
                           Bedrock
                              │
                              ▼
                            Answer
                              │
                  ┌───────────┴───────────┐
                  ▼                       ▼
             Save Message             Sources
                  │
                  ▼
              PostgreSQL
```

---

# 61. Day 28 检查清单

```text
[ ] Conversation
[ ] Session ID
[ ] User ID
[ ] Tenant ID
[ ] Conversation Message
[ ] PostgreSQL Memory
[ ] Short-Term Memory
[ ] Token Window
[ ] Conversation Summary
[ ] User Profile 基础
[ ] Memory / RAG 分离
[ ] Memory / Tool 协同
[ ] Conversation API
[ ] React Conversation List
[ ] Conversation History
[ ] Sensitive Data Masking
[ ] Retention Policy
[ ] Conversation Index
[ ] Agent Run
[ ] OpenTelemetry
[ ] Multi-turn Test
```

---

# Day 24 → Day 28

现在你的系统已经具备四个核心 AI 能力：

```text
                 AI Platform
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
       RAG          Agent         Memory
        │             │             │
        ▼             ▼             ▼
   pgvector         Tools       PostgreSQL
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                   Bedrock
                      │
                      ▼
                  Enterprise
                  AI Assistant
```

**Day 29** 下一步应该做 **Streaming Chat + SSE/WebSocket**：让 Bedrock 的回答像 ChatGPT 一样实时逐 token 返回到 React，同时把 Agent Tool Calling、RAG、Memory 的中间状态做好流式事件处理。
