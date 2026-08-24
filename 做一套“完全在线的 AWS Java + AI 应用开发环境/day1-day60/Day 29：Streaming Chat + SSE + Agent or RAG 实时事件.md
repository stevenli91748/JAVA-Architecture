# Day 29：Streaming Chat + SSE + Agent/RAG 实时事件

今天把 Day 28 的企业 AI Assistant 从：

```text
用户提问
   ↓
等待 3～10 秒
   ↓
一次性返回完整答案
```

升级成：

```text
用户提问
   ↓
Agent
   ↓
实时事件
   ├── 思考/状态
   ├── Tool Calling
   ├── RAG Search
   └── LLM Token Stream
          ↓
       React 实时显示
```

目标效果就是类似 ChatGPT：

```text
AI：Amazon ECS is a ...
                 ↑
        内容不断实时出现
```

---

# 1. 今天最终架构

```text
                         React
                           │
                           │ POST /api/chat/stream
                           ▼
                    Spring Boot API
                           │
                           ▼
                       SSE Stream
                           │
                           ▼
                     Agent Service
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
          Memory          RAG           Tools
            │              │              │
            ▼              ▼              ▼
        PostgreSQL      pgvector        RDS/API
                           │
                           ▼
                        Bedrock
                           │
                     Token Streaming
                           │
                           ▼
                          SSE
                           │
                           ▼
                         React
```

---

# 2. 为什么使用 SSE

今天推荐：

**SSE = Server-Sent Events**

数据方向：

```text
Browser  ─────────────→  Server
Browser  ←─────────────  Server
```

非常适合：

```text
AI Token Streaming
```

因为：

```text
Server
 ↓
token
 ↓
token
 ↓
token
 ↓
token
```

浏览器可以持续接收。

---

# 3. SSE vs WebSocket

### SSE

```text
Server → Browser
```

非常适合：

```text
AI Response
Streaming
Notifications
Progress
```

### WebSocket

```text
Browser ↔ Server
```

适合：

```text
实时协作
游戏
双向实时通信
机器人控制
```

今天的 AI Chat：

**SSE 足够。**

---

# 4. API

新增：

```text id="q9e7af"
POST /api/chat/stream
```

Request：

```json id="t0xv6n"
{
  "conversationId": "conv-001",
  "message": "Explain AWS ECS"
}
```

Response：

```text id="g2m3k7"
text/event-stream
```

---

# 5. SSE Event

不要只发送：

```text id="k2l9s4"
data: hello
```

建议定义自己的 Event Schema。

例如：

```json id="y7q4v2"
{
  "type": "TOKEN",
  "content": "Amazon"
}
```

---

# 6. Event 类型

今天定义：

```text id="w4c8r5"
START
STATUS
TOOL_START
TOOL_RESULT
SOURCE
TOKEN
ERROR
DONE
```

完整流程：

```text id="m5n8x2"
START
  ↓
STATUS
  ↓
TOOL_START
  ↓
TOOL_RESULT
  ↓
SOURCE
  ↓
TOKEN
  ↓
TOKEN
  ↓
TOKEN
  ↓
DONE
```

---

# 7. Event JSON

### START

```json id="7e0v6d"
{
  "type": "START",
  "conversationId": "conv-001"
}
```

### STATUS

```json id="d4r9k2"
{
  "type": "STATUS",
  "status": "SEARCHING_KNOWLEDGE"
}
```

---

# 8. Tool Event

```json id="9h1x5m"
{
  "type": "TOOL_START",
  "tool": "searchKnowledge"
}
```

结束：

```json id="g8v3n7"
{
  "type": "TOOL_RESULT",
  "tool": "searchKnowledge",
  "success": true
}
```

注意：

**不要把内部敏感参数直接发送给前端。**

---

# 9. Source Event

```json id="x6p8q3"
{
  "type": "SOURCE",
  "source": {
    "document": "aws-ecs.pdf",
    "page": 12
  }
}
```

React 可以实时显示：

```text
Sources
 └── aws-ecs.pdf — Page 12
```

---

# 10. TOKEN Event

```json id="m8q4z1"
{
  "type": "TOKEN",
  "content": "Amazon"
}
```

然后：

```json id="k3n7v5"
{
  "type": "TOKEN",
  "content": " ECS"
}
```

React：

```text
Amazon
```

变成：

```text
Amazon ECS
```

---

# 11. DONE

```json id="v7x2p8"
{
  "type": "DONE"
}
```

React 收到：

```text id="f2k5m9"
DONE
```

停止：

```text id="2y7w4c"
loading
```

---

# 12. Spring Boot Controller

推荐使用：

```java id="z0k8w3"
@PostMapping(
    value = "/api/chat/stream",
    produces = MediaType.TEXT_EVENT_STREAM_VALUE
)
public Flux<ServerSentEvent<ChatEvent>>
streamChat(
    @RequestBody ChatRequest request) {

    return chatService.stream(request);
}
```

如果你使用 Spring WebFlux，这是非常自然的实现方式。

---

# 13. 为什么推荐 WebFlux

因为 Streaming：

```text id="q4j7m2"
Request
 ↓
保持连接
 ↓
不断发送
 ↓
完成
```

Reactive：

```text id="n6p2v9"
Flux<T>
```

非常适合这种场景。

---

# 14. ChatService

概念：

```java id="m8v5q3"
public Flux<ServerSentEvent<ChatEvent>>
stream(ChatRequest request) {

    return Flux.create(sink -> {

        sink.next(
            event("START")
        );

        // Agent
        // RAG
        // Tool
        // LLM stream

        sink.next(
            event("DONE")
        );

        sink.complete();
    });
}
```

实际项目中建议把 Agent、RAG、LLM Stream 都做成 Reactive Pipeline，而不是把所有逻辑塞进 `Flux.create()`。

---

# 15. Bedrock Streaming

Day 27：

```text id="b3k9r7"
Bedrock
 ↓
Complete Response
```

Day 29：

```text id="n4w6s2"
Bedrock
 ↓
Token Stream
 ↓
Spring AI
 ↓
Flux<String>
```

然后：

```text id="p5c8x1"
Flux<String>
 ↓
SSE
 ↓
React
```

---

# 16. Spring AI Streaming

Spring AI 的 `ChatClient` 支持流式调用。

概念：

```java id="g6m2q9"
Flux<String> stream =
    chatClient
        .prompt()
        .user(question)
        .stream()
        .content();
```

具体 API 以当前 Spring AI 版本为准。

---

# 17. 最简单的 Streaming

```java id="u2p7m4"
return chatClient
    .prompt()
    .user(request.message())
    .stream()
    .content()
    .map(token ->
        ServerSentEvent.builder(
            new ChatEvent(
                "TOKEN",
                token
            )
        ).build()
    );
```

最终：

```text id="y5r9n2"
Bedrock
 ↓
Token
 ↓
Spring AI
 ↓
SSE
 ↓
React
```

---

# 18. 但 Agent Streaming 更复杂

因为现在不是：

```text id="8z2k5x"
LLM
 ↓
Token
```

而是：

```text id="j3n8p6"
Agent
│
├── Memory
├── Tool
├── RAG
├── Tool
└── LLM
```

所以需要：

**Event Stream。**

---

# 19. Agent Event Stream

推荐统一：

```java id="s7c4m1"
Flux<AgentEvent>
```

例如：

```text id="n4k9q2"
AgentEvent
│
├── START
├── STATUS
├── TOOL_START
├── TOOL_RESULT
├── SOURCE
├── TOKEN
├── ERROR
└── DONE
```

然后：

```text id="r5v8j3"
AgentEvent
 ↓
SSE Event
```

---

# 20. Agent Pipeline

```text id="q7n2x6"
START
  ↓
LOAD_MEMORY
  ↓
AGENT_DECISION
  ↓
TOOL_START
  ↓
TOOL_RESULT
  ↓
RAG_SOURCE
  ↓
LLM_TOKEN
  ↓
DONE
```

前端完全不需要知道 Agent 内部实现。

---

# 21. React EventSource 的问题

浏览器原生：

```javascript id="4y7q8m"
new EventSource("/api/chat/stream")
```

主要用于：

```text
GET
```

而我们的 Chat：

```text
POST
```

需要发送：

```json id="7m5q3x"
{
  "message": "..."
}
```

因此实际 React 项目更方便使用：

```text id="2f6n8k"
fetch()
 +
ReadableStream
```

来接收 SSE。

---

# 22. React Streaming

概念：

```javascript id="n9x4c7"
const response = await fetch(
  "/api/chat/stream",
  {
    method: "POST",
    headers: {
      "Content-Type":
        "application/json"
    },
    body: JSON.stringify({
      conversationId,
      message
    })
  }
);
```

然后：

```javascript id="z8p3m5"
const reader =
  response.body.getReader();
```

持续读取：

```text id="w5k2q9"
read()
 ↓
decode
 ↓
parse SSE
 ↓
update UI
```

---

# 23. Frontend State

React：

```text id="q4n7v2"
messages
```

当前 AI：

```text id="m9x5k3"
assistantMessage
```

每收到一个：

```text id="y7p2n8"
TOKEN
```

执行：

```javascript id="v3k8m4"
setAssistantMessage(
  previous => previous + token
);
```

所以用户看到：

```text id="k2n6q9"
A
Am
Ama
Amaz
Amazon
Amazon ECS
...
```

---

# 24. React UI

建议：

```text id="d8m4x7"
┌─────────────────────────────────────┐
│ AWS AI Assistant                    │
├─────────────────────────────────────┤
│                                     │
│ User                                │
│ Explain ECS                         │
│                                     │
│ AI                                  │
│ Amazon ECS is a...                  │
│                                     │
│ Sources                             │
│ 📄 aws-ecs.pdf  p.12                │
│                                     │
├─────────────────────────────────────┤
│ Ask anything...              [Send] │
└─────────────────────────────────────┘
```

---

# 25. Tool Status UI

当 Agent 调 Tool：

```text id="a3n8v6"
🔎 Searching knowledge...
```

完成：

```text id="z6q2m5"
✓ Knowledge search completed
```

然后：

```text id="w8k3p1"
Generating answer...
```

这比让用户：

```text id="j5n9x2"
一直等
```

体验好很多。

---

# 26. RAG Status

可以发送：

```json id="c7v4m9"
{
  "type": "STATUS",
  "status": "SEARCHING_KNOWLEDGE"
}
```

然后：

```json id="p3n8x5"
{
  "type": "STATUS",
  "status": "FOUND_5_SOURCES"
}
```

最后：

```json id="r6k2q7"
{
  "type": "STATUS",
  "status": "GENERATING"
}
```

---

# 27. Tool Status

例如：

```text id="m8v4q2"
🔧 getDocumentStats
```

React 不显示：

```text id="w1n5z7"
SQL:
SELECT * FROM...
```

而显示：

```text id="q9k3m6"
Checking document statistics...
```

内部细节：

```text id="u5x7p2"
CloudWatch
Trace
```

保存。

---

# 28. Error Event

如果 Bedrock 出错：

```json id="b4n8v6"
{
  "type": "ERROR",
  "code": "AI_SERVICE_ERROR",
  "message": "AI service temporarily unavailable"
}
```

然后：

```text id="s7m2q5"
DONE
```

前端：

```text id="d6p9x3"
AI service temporarily unavailable.
Please try again.
```

不要返回：

```text id="y2w5k8"
AWS internal exception stack trace
```

---

# 29. Connection Timeout

Streaming 最大的问题之一：

```text id="t5n8m2"
HTTP Connection
```

保持时间可能很长。

需要配置：

```text id="p7q4x1"
ALB
ECS
Spring Boot
CloudFront
```

的 timeout。

特别注意：

```text id="k9m3v6"
ALB idle timeout
```

如果默认值太低：

```text id="n2x7q4"
Agent
 ↓
等待
 ↓
Connection closed
```

---

# 30. ALB

你的架构：

```text id="f3w8k2"
CloudFront
 ↓
ALB
 ↓
ECS
```

SSE：

```text id="m7q5n1"
CloudFront
 ↓
ALB
 ↓
ECS
 ↓
持续 Streaming
```

要确保代理链不会过早关闭连接。

---

# 31. 禁止错误缓存

Streaming：

```text id="z4n8q2"
不能被缓存成一个完整 Response。
```

响应：

```http id="c9p6m3"
Content-Type: text/event-stream
Cache-Control: no-cache
Connection: keep-alive
```

---

# 32. SSE Event ID

建议：

```json id="u3m7q5"
{
  "id": "evt-1024",
  "type": "TOKEN",
  "content": "ECS"
}
```

好处：

```text id="h5n9k2"
debug
reconnect
trace
```

---

# 33. Heartbeat

如果 Agent：

```text id="r8q3m6"
20 seconds
```

没有 token：

```text id="b7v2n5"
浏览器
 ↓
是不是断了？
```

可以发送：

```text id="q6k9m3"
event: heartbeat
data: {}
```

例如每：

```text id="w2x8p4"
10–15 seconds
```

一次。

---

# 34. Streaming + Memory

顺序：

```text id="z7n4k2"
START
 ↓
Save User Message
 ↓
Load Memory
 ↓
Agent
 ↓
Token Stream
 ↓
Collect Full Answer
 ↓
Save Assistant Message
 ↓
DONE
```

注意：

**不能每收到一个 Token 就写一次 PostgreSQL。**

错误：

```text id="m8q3v7"
Token 1 → DB
Token 2 → DB
Token 3 → DB
...
```

---

# 35. 正确保存 Assistant

先：

```text id="x2n7k5"
StringBuilder answer
```

不断：

```text id="j4m8q1"
append(token)
```

完成：

```text id="p6v3n9"
DONE
 ↓
save(answer)
```

一次写入数据库。

---

# 36. Streaming + RAG

完整流程：

```text id="q3w8m6"
User
 ↓
Memory
 ↓
Agent
 ↓
searchKnowledge()
 ↓
Sources
 ↓
Context
 ↓
Bedrock Streaming
 ↓
TOKEN TOKEN TOKEN
 ↓
React
```

---

# 37. Streaming + Tool Calling

例如：

```text id="a5k2m8"
User:
How many documents do I have?
```

流程：

```text id="r9n4x6"
START
 ↓
TOOL_START
 ↓
getDocumentStats
 ↓
TOOL_RESULT
 ↓
TOKEN
 ↓
TOKEN
 ↓
DONE
```

前端：

```text id="f6p3q9"
Checking document statistics...
127 documents.
```

---

# 38. Streaming + Memory

例如：

```text id="d8m5q2"
User:
What is ECS?

AI:
ECS is...
```

下一句：

```text id="g3n7x4"
User:
How does it fit into our architecture?
```

Agent：

```text id="p9v2m5"
Memory
 ↓
ECS
 ↓
RAG
 ↓
Streaming
```

---

# 39. Streaming API 数据模型

Java：

```java id="k4m8q2"
public record ChatEvent(
    String id,
    EventType type,
    String content,
    Map<String, Object> metadata
) {}
```

Enum：

```java id="v7n3p9"
public enum EventType {

    START,
    STATUS,
    TOOL_START,
    TOOL_RESULT,
    SOURCE,
    TOKEN,
    ERROR,
    DONE
}
```

---

# 40. Event Metadata

例如：

```json id="w2m7q4"
{
  "type": "SOURCE",
  "metadata": {
    "document": "aws-ecs.pdf",
    "page": 12
  }
}
```

Tool：

```json id="c6n9x3"
{
  "type": "TOOL_START",
  "metadata": {
    "tool": "searchKnowledge"
  }
}
```

---

# 41. 不要暴露 Chain of Thought

前端可以显示：

```text id="p8k3m5"
Searching knowledge...
```

不要发送模型的：

```text id="q4n7z2"
内部推理过程
```

也不要设计 API 去输出隐藏的 chain-of-thought。

只返回：

```text id="s6m2x8"
Status
Tool Events
Sources
Answer
```

---

# 42. Security

SSE 请求必须经过：

```text id="g7n4q2"
JWT
 ↓
Authentication
 ↓
Tenant
 ↓
Conversation Authorization
```

不能：

```text id="k2m8v5"
conversationId
 ↓
直接 stream
```

必须验证：

```text id="q5x3n9"
conversation belongs to user
```

---

# 43. Streaming Authorization

特别注意：

```text id="f9p2m7"
POST /api/chat/stream
```

和：

```text id="s3k8q5"
GET /api/conversations/{id}
```

必须使用相同的：

```text id="n6v4x2"
Tenant Authorization
```

---

# 44. Rate Limit

Agent Streaming 很容易被滥用。

例如：

```text id="y7m3q9"
User
 ↓
1000 requests
 ↓
Bedrock
```

成本可能快速增加。

所以：

```text id="p4n8k2"
Rate Limit
+
Token Limit
+
Max Tool Calls
```

---

# 45. 推荐初始限制

DEV 可以：

```text id="v8m2q6"
10 requests/minute
```

生产：

根据业务容量设置，例如：

```text id="c5x9n3"
20–60 requests/minute/user
```

并增加：

```text id="j7q4m1"
Daily token budget
```

具体数值应该根据你的 Bedrock 配额、成本预算和用户规模测试确定。

---

# 46. Day 29 Metrics

增加：

```text id="m3v7q8"
stream.request.count
stream.duration
stream.first_token_latency
stream.completed.count
stream.error.count
stream.disconnect.count
stream.tokens
```

特别重要：

### First Token Latency

```text id="h2q5n8"
User Submit
 ↓
First Token
```

例如：

```text id="k7m4x1"
850ms
```

用户体验通常比：

```text id="b3n9q6"
Total = 4.5 sec
```

更敏感。

---

# 47. Token Throughput

记录：

```text id="p8v3m6"
tokens / second
```

例如：

```text id="z4k7q2"
35 tokens/sec
```

可以帮助判断：

```text id="n6m2x9"
LLM
Network
Application
```

瓶颈在哪里。

---

# 48. Day 29 实战

完成：

### Task 1

建立：

```text id="f3n8m5"
POST /api/chat/stream
```

### Task 2

定义：

```text id="w7q2k9"
ChatEvent
```

### Task 3

实现：

```text id="m4x8p3"
START
TOKEN
DONE
```

### Task 4

加入：

```text id="q6n3v7"
SOURCE
```

### Task 5

加入：

```text id="z9m5k2"
TOOL_START
TOOL_RESULT
```

### Task 6

React：

```text id="a8p4x6"
ReadableStream
```

### Task 7

实现：

```text id="y3k7m9"
First Token Latency
```

---

# 49. Day 29 测试

### Test 1

```text id="s8m2q5"
Explain ECS.
```

预期：

```text id="e7v3n9"
START
 ↓
SOURCE
 ↓
TOKEN...
 ↓
DONE
```

---

### Test 2

```text id="q4n8x2"
How many documents do I have?
```

预期：

```text id="m7p3k5"
START
 ↓
TOOL_START
 ↓
TOOL_RESULT
 ↓
TOKEN...
 ↓
DONE
```

---

### Test 3

连续对话：

```text id="z6x2m8"
What is ECS?

How does it fit into our architecture?
```

预期：

```text id="r3k7q5"
Memory
 ↓
RAG
 ↓
Streaming
```

---

# 50. Day 29 最终架构

```text id="p9q4m7"
                            React
                              │
                              │ POST
                              ▼
                       /api/chat/stream
                              │
                              ▼
                       Spring WebFlux
                              │
                              ▼
                         Agent Stream
                              │
          ┌───────────────────┼──────────────────┐
          ▼                   ▼                  ▼
       Memory                RAG                Tools
          │                   │                  │
          ▼                   ▼                  ▼
      PostgreSQL          pgvector            RDS/API
                              │
                              ▼
                           Bedrock
                              │
                         Token Stream
                              │
                              ▼
                         ChatEvent
                              │
                              ▼
                             SSE
                              │
                              ▼
                            React
```

---

# 51. Day 29 检查清单

```text id="j5n8q3"
[ ] SSE
[ ] Spring WebFlux
[ ] POST /api/chat/stream
[ ] ChatEvent
[ ] START
[ ] STATUS
[ ] TOOL_START
[ ] TOOL_RESULT
[ ] SOURCE
[ ] TOKEN
[ ] ERROR
[ ] DONE
[ ] Bedrock Streaming
[ ] Spring AI Streaming
[ ] React ReadableStream
[ ] Conversation Memory
[ ] RAG Streaming
[ ] Tool Calling Streaming
[ ] First Token Latency
[ ] Token Throughput
[ ] Heartbeat
[ ] Timeout
[ ] Rate Limit
[ ] Tenant Authorization
[ ] No Chain-of-Thought Exposure
```

---

# Day 24 → Day 29

现在已经形成一个比较完整的 **Java Enterprise AI Assistant**：

```text
                         ┌───────────────┐
                         │     React     │
                         └───────┬───────┘
                                 │
                              SSE Stream
                                 │
                                 ▼
                     ┌─────────────────────┐
                     │   Spring Boot       │
                     │   WebFlux API       │
                     └──────────┬──────────┘
                                │
                         ┌──────▼──────┐
                         │ AI Agent     │
                         └──────┬──────┘
                                │
             ┌──────────────────┼─────────────────┐
             ▼                  ▼                 ▼
          Memory               RAG              Tools
             │                  │                 │
             ▼                  ▼                 ▼
        PostgreSQL          pgvector           RDS/API
             │                  │                 │
             └──────────────────┼─────────────────┘
                                ▼
                           Amazon Bedrock
                                │
                          Token Streaming
                                │
                                ▼
                               SSE
                                │
                                ▼
                              React
```

**Day 30** 建议做第一个完整的 **Production AI Assistant 项目收尾**：把 Day 1–29 的 AWS + Java + React + RAG + Agent + Memory + Streaming 全部整合，完成 **Docker → ECR → ECS Fargate → RDS → S3 → SQS → Bedrock → CloudWatch → CI/CD** 的端到端部署，并做一次真正的生产级安全、性能、成本和故障测试。
