# Day 34：Agentic Workflow —— State + Node + Edge + Human-in-the-Loop

今天开始从：

```text
Agent
 ↓
LLM 自己决定 Tool
```

升级到：

```text
Agent Workflow
 ↓
State
 ↓
Node
 ↓
Conditional Edge
 ↓
Tool
 ↓
Validation
 ↓
Human Approval
 ↓
Final Answer
```

这是从 **“AI Agent Demo” → “企业级 Agent 系统”** 的关键一步。

---

# 1. 今天的目标

今天掌握 7 个概念：

```text
1. State
2. Node
3. Edge
4. Conditional Routing
5. Retry
6. Checkpoint
7. Human-in-the-Loop
```

最终做一个：

> **Enterprise Support Agent Workflow**

---

# 2. 最终 Workflow

例如用户问：

> “为什么生产环境的 Java ECS 服务响应变慢？”

系统：

```text
User
 ↓
START
 ↓
Load Memory
 ↓
Classify Intent
 ↓
 ┌───────────────┬────────────────┐
 │               │                │
 ▼               ▼                ▼
Knowledge       Database         AWS
 │               │                │
 └───────────────┼────────────────┘
                 ▼
             Gather Data
                 ↓
             Analyze
                 ↓
          Need More Evidence?
             /          \
           YES           NO
            │             │
            ▼             ▼
        More Search    Generate
            │             │
            └──────┬──────┘
                   ▼
               Validate
                   │
            High Risk Action?
              /          \
            YES           NO
             │             │
             ▼             ▼
        Human Approval   Execute
             │             │
             └──────┬──────┘
                    ▼
               Final Answer
                    ↓
                   END
```

---

# 3. 为什么需要 Workflow

简单 Agent：

```text
User
 ↓
LLM
 ↓
Tool
 ↓
LLM
 ↓
Answer
```

问题：

```text
LLM 决策不可预测
Tool 可能重复调用
错误恢复困难
难以审计
难以暂停
```

企业系统需要：

```text
确定性
可恢复
可审计
可测试
可控制
```

所以：

```text
Agent
+
Workflow
```

---

# 4. State 是什么

State：

> **整个 Agent Workflow 当前状态。**

例如：

```java
public class AgentState {

    String conversationId;

    String userQuestion;

    String intent;

    List<String> searchQueries;

    List<Document> documents;

    List<ToolResult> toolResults;

    String draftAnswer;

    boolean needsApproval;

    String finalAnswer;
}
```

---

# 5. State 的核心

整个 Workflow：

```text
Node A
 ↓
State
 ↓
Node B
 ↓
State
 ↓
Node C
```

不是：

```text
Node A
 ↓
Node B
 ↓
Node C
```

每个 Node 都读取和修改 State。

---

# 6. Node

Node：

> **执行一个明确任务的步骤。**

例如：

```text
LoadMemoryNode
ClassifyIntentNode
RetrieveNode
ToolNode
AnalyzeNode
ValidateNode
ApprovalNode
AnswerNode
```

每个 Node 尽量：

```text
单一职责
```

---

# 7. Node 示例

```java
public interface AgentNode {

    AgentState execute(
        AgentState state
    );
}
```

例如：

```java
public class LoadMemoryNode
        implements AgentNode {

    @Override
    public AgentState execute(
            AgentState state) {

        // load conversation memory

        return state;
    }
}
```

---

# 8. Edge

Edge：

> **决定 Workflow 下一步去哪里。**

例如：

```text
ClassifyIntent
      │
      ├── KNOWLEDGE → RAG
      ├── DATABASE  → DB Tool
      └── AWS       → AWS Tool
```

---

# 9. Conditional Edge

核心：

```java
if (state.intent().equals("KNOWLEDGE")) {
    return "RAG";
}

if (state.intent().equals("DATABASE")) {
    return "DATABASE";
}

return "AWS";
```

生产系统不要到处写这种：

```text
if
else
if
else
```

应该集中到：

```text
Router
```

---

# 10. Query Router

Day 32：

```text
SIMPLE
COMPLEX
MULTI_HOP
```

今天升级成：

```text
IntentRouter
│
├── KNOWLEDGE
├── DATABASE
├── BUSINESS_API
├── AWS
├── ANALYTICS
└── HUMAN
```

---

# 11. Workflow 示例

用户：

> “公司生产环境 ECS 最近 CPU 很高，为什么？”

Router：

```text
intent = AWS
```

Workflow：

```text
START
 ↓
Load Memory
 ↓
AWS Diagnostics
 ↓
CloudWatch
 ↓
RAG
 ↓
Analyze
 ↓
Answer
```

---

# 12. State 示例

执行过程中：

```json
{
  "intent": "AWS",
  "documents": [
    "ecs-architecture.pdf"
  ],
  "metrics": {
    "cpu": 87
  },
  "draftAnswer": null
}
```

继续：

```text
AnalyzeNode
```

修改：

```json
{
  "intent": "AWS",
  "cpu": 87,
  "rootCause": "CPU saturation"
}
```

最后：

```text
AnswerNode
```

---

# 13. Workflow Graph

今天建立：

```text
START
  │
  ▼
Memory
  │
  ▼
Intent
  │
  ├─────────────┬─────────────┐
  ▼             ▼             ▼
RAG            DB            AWS
  │             │             │
  └─────────────┼─────────────┘
                ▼
             Analyze
                │
                ▼
             Validate
                │
         ┌──────┴──────┐
         ▼             ▼
       Retry          Answer
         │
         ▼
      Retrieve
```

这就是 Graph 思维。

---

# 14. Spring AI vs LangGraph

你现在使用 Java。

建议理解：

### Spring AI

```text
Java
Spring Boot
Enterprise
MCP
Tool Calling
LLM
```

非常适合你的主项目。

### LangGraph

```text
Python
Graph
State
Node
Edge
Checkpoint
Human-in-loop
```

非常适合学习 Agent Workflow 思维。

---

# 15. 不建议马上重写成 Python

你的主项目继续：

```text
Java
+
Spring AI
```

同时理解：

```text
LangGraph 的 Graph/State 思想
```

这样以后看 Python Agent 项目不会陌生。

---

# 16. Workflow Engine

今天先不要引入复杂框架。

先自己实现：

```text
Workflow
Node
Edge
State
```

例如：

```java
public interface WorkflowNode {

    AgentState run(
        AgentState state
    );
}
```

然后：

```java
public interface WorkflowRouter {

    String next(
        AgentState state
    );
}
```

---

# 17. Workflow Executor

```java
public AgentState execute(
        AgentState state) {

    String node = "START";

    while (!node.equals("END")) {

        AgentNode current =
            nodes.get(node);

        state =
            current.execute(state);

        node =
            router.next(state);
    }

    return state;
}
```

这就是最基本的 Agent Graph Engine。

---

# 18. 不要让 Workflow 无限循环

错误：

```text
Retrieve
 ↓
Analyze
 ↓
Retrieve
 ↓
Analyze
 ↓
Retrieve
 ↓
...
```

必须：

```java
int maxSteps = 10;
```

或者：

```java
state.stepCount()
```

超过：

```text
MAX_STEPS
```

直接：

```text
FAILED
```

---

# 19. Retry

例如 Bedrock：

```text
Request
 ↓
Timeout
```

不能立即：

```text
FAILED
```

可以：

```text
Retry 1
 ↓
Retry 2
 ↓
Retry 3
 ↓
FAILED
```

---

# 20. Retry 不适用于所有错误

可以 Retry：

```text
Timeout
429
Temporary network error
503
```

不要 Retry：

```text
401
403
Invalid input
Unauthorized
Tool not found
```

---

# 21. Exponential Backoff

例如：

```text
Retry 1
→ 1 sec

Retry 2
→ 2 sec

Retry 3
→ 4 sec
```

加：

```text
Jitter
```

避免大量服务同时重试。

---

# 22. Retry Policy

Java：

```java
public record RetryPolicy(
    int maxAttempts,
    Duration initialDelay,
    double multiplier
) {}
```

例如：

```text
maxAttempts = 3
initialDelay = 500ms
multiplier = 2
```

---

# 23. Checkpoint

这是今天非常重要的概念。

如果：

```text
Workflow
 ↓
Node 1
 ↓
Node 2
 ↓
Node 3
 ↓
Crash
```

如果没有 Checkpoint：

```text
全部重新执行
```

如果有：

```text
Checkpoint
```

可以：

```text
Node 3
 ↓
Resume
```

---

# 24. Checkpoint

保存：

```text
workflowId
conversationId
state
currentNode
status
updatedAt
```

数据库：

```sql
CREATE TABLE agent_checkpoints (
    id UUID PRIMARY KEY,
    workflow_id VARCHAR(100),
    conversation_id VARCHAR(100),
    current_node VARCHAR(100),
    state JSONB,
    status VARCHAR(30),
    updated_at TIMESTAMP
);
```

---

# 25. 为什么企业 Agent 必须 Checkpoint

想象：

```text
Agent
 ↓
调用 3 个 API
 ↓
分析 30 秒
 ↓
服务器 Crash
```

没有 Checkpoint：

```text
重新执行
```

可能造成：

```text
重复 API
重复成本
重复写操作
```

有 Checkpoint：

```text
Resume
```

---

# 26. Human-in-the-Loop

这是今天最重要的企业功能之一。

例如：

```text
Agent
 ↓
发现异常
 ↓
准备执行高风险操作
```

不能：

```text
直接执行
```

应该：

```text
Agent
 ↓
Approval Required
 ↓
Human
 ↓
Approve / Reject
```

---

# 27. 什么操作需要 Human Approval

建议：

```text
delete
cancel
refund
send email
deploy
restart production
modify database
financial action
```

都可以考虑：

```text
Human Approval
```

---

# 28. Approval State

```text
state.needsApproval = true
```

Workflow：

```text
Validate
 ↓
Approval Required
 ↓
PAUSED
```

保存：

```text
Checkpoint
```

然后：

```text
Workflow
= WAITING_FOR_HUMAN
```

---

# 29. Approval API

增加：

```text
POST /api/agent/approvals/{id}/approve
```

和：

```text
POST /api/agent/approvals/{id}/reject
```

例如：

```json
{
  "approvalId": "apr-123",
  "decision": "APPROVED"
}
```

---

# 30. Workflow Resume

用户点击：

```text
Approve
```

系统：

```text
Approval
 ↓
Load Checkpoint
 ↓
Restore State
 ↓
Resume Node
 ↓
Execute
 ↓
END
```

这就是：

**Human-in-the-loop + Checkpoint + Resume**

---

# 31. Approval Database

```sql
CREATE TABLE agent_approvals (
    id UUID PRIMARY KEY,
    workflow_id UUID NOT NULL,
    user_id UUID NOT NULL,
    action VARCHAR(100),
    payload JSONB,
    status VARCHAR(30),
    created_at TIMESTAMP,
    resolved_at TIMESTAMP
);
```

状态：

```text
PENDING
APPROVED
REJECTED
EXPIRED
```

---

# 32. Approval 不应该由 LLM 决定

错误：

```text
LLM:
This action seems safe.
```

然后直接执行。

正确：

```text
Policy Engine
 ↓
Risk Level
 ↓
HIGH
 ↓
Human Approval
```

---

# 33. Policy Engine

建立：

```text
PolicyEngine
```

例如：

```java
RiskLevel evaluate(
    ToolCall toolCall,
    UserContext user
);
```

返回：

```text
LOW
MEDIUM
HIGH
```

---

# 34. Tool Risk

例如：

```text
searchKnowledge()
    LOW

getOrder()
    LOW

updateOrder()
    MEDIUM

cancelOrder()
    HIGH

deleteCustomer()
    HIGH
```

---

# 35. Workflow Security

最终：

```text
User
 ↓
Agent
 ↓
Policy Engine
 ↓
Authorization
 ↓
Risk Evaluation
 ↓
Approval
 ↓
Tool
```

不是：

```text
User
 ↓
LLM
 ↓
Tool
```

---

# 36. Agent State 建议重新设计

```java
public record AgentState(

    String workflowId,

    String conversationId,

    String userId,

    String tenantId,

    String question,

    String intent,

    List<String> searchQueries,

    List<Document> documents,

    List<ToolResult> toolResults,

    String analysis,

    String draftAnswer,

    boolean needsApproval,

    String approvalId,

    int stepCount

) {}
```

---

# 37. State 必须包含 Tenant

非常重要：

```text
tenantId
```

必须来自：

```text
Authentication Context
```

不是：

```text
LLM
```

也不是：

```text
User Input
```

---

# 38. Workflow 状态

定义：

```java
public enum WorkflowStatus {

    RUNNING,
    WAITING_FOR_APPROVAL,
    COMPLETED,
    FAILED,
    CANCELLED
}
```

---

# 39. Workflow Node 状态

例如：

```text
START
 ↓
LOAD_MEMORY
 ↓
CLASSIFY
 ↓
RETRIEVE
 ↓
ANALYZE
 ↓
VALIDATE
 ↓
APPROVAL
 ↓
EXECUTE
 ↓
ANSWER
 ↓
END
```

---

# 40. Workflow API

增加：

```text
POST /api/agent/run
```

返回：

```json
{
  "workflowId": "wf-123",
  "status": "RUNNING"
}
```

查询：

```text
GET /api/agent/workflows/wf-123
```

返回：

```json
{
  "workflowId": "wf-123",
  "status": "WAITING_FOR_APPROVAL",
  "currentNode": "APPROVAL"
}
```

---

# 41. 与 Day 29 SSE 结合

今天之后：

```text
Workflow
 ↓
SSE
```

实时发送：

```text
START
STATUS
NODE_STARTED
TOOL_STARTED
TOOL_RESULT
APPROVAL_REQUIRED
NODE_COMPLETED
TOKEN
DONE
```

例如：

```text
🔎 Searching knowledge...

☁ Checking ECS metrics...

⚠ Production restart requires approval.

[Approve]
[Reject]
```

---

# 42. React Workflow UI

最终界面：

```text
┌──────────────────────────────────────┐
│ AI Enterprise Assistant              │
├──────────────────────────────────────┤
│ User                                 │
│ Restart the production ECS service   │
│                                      │
│ Agent                                │
│ ✓ Analyze request                    │
│ ✓ Check ECS status                   │
│ ⚠ Approval required                  │
│                                      │
│ Production restart                   │
│ Service: order-api                   │
│                                      │
│ [ Approve ]       [ Reject ]         │
└──────────────────────────────────────┘
```

---

# 43. Agent Workflow + MCP

Day 33：

```text
Agent
 ↓
MCP Client
 ↓
MCP Server
```

Day 34：

```text
Workflow
 ↓
Agent
 ↓
MCP
 ↓
Tool
```

完整：

```text
Workflow
   │
   ▼
Intent
   │
   ▼
Policy
   │
   ▼
MCP Tool
   │
   ▼
Approval
   │
   ▼
MCP Tool Execute
```

---

# 44. Agent Workflow + RAG

Day 32：

```text
Advanced RAG
```

今天：

```text
Workflow
 ↓
RAG Node
 ↓
Retrieve
 ↓
Rerank
 ↓
Analyze
```

所以 RAG 是：

```text
Node
```

而不是整个 Agent。

---

# 45. 最终 Enterprise Agent

```text
                         User
                           │
                           ▼
                     Agent Workflow
                           │
                         State
                           │
                           ▼
                    Intent Router
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
            RAG           MCP           API
             │             │             │
             ▼             ▼             ▼
         Retrieval       Tools        Business
             │             │
             └──────┬──────┘
                    ▼
                Analyze
                    │
                    ▼
               Policy Engine
                    │
             ┌──────┴──────┐
             ▼             ▼
           LOW           HIGH
             │             │
             │       Human Approval
             │             │
             └──────┬──────┘
                    ▼
                 Execute
                    │
                 Checkpoint
                    │
                    ▼
                Final Answer
                    │
                    ▼
                   SSE
                    │
                    ▼
                  React
```

---

# 46. 今天的 Java 项目结构

```text
backend/
│
├── agent/
│   ├── AgentState.java
│   ├── AgentNode.java
│   ├── AgentWorkflow.java
│   ├── IntentRouter.java
│   └── WorkflowExecutor.java
│
├── workflow/
│   ├── WorkflowStatus.java
│   ├── WorkflowNode.java
│   ├── WorkflowRouter.java
│   ├── RetryPolicy.java
│   └── CheckpointService.java
│
├── nodes/
│   ├── LoadMemoryNode.java
│   ├── ClassifyIntentNode.java
│   ├── RetrieveNode.java
│   ├── ToolNode.java
│   ├── AnalyzeNode.java
│   ├── ValidateNode.java
│   ├── ApprovalNode.java
│   └── AnswerNode.java
│
├── approval/
│   ├── ApprovalService.java
│   └── ApprovalController.java
│
├── policy/
│   └── PolicyEngine.java
│
├── mcp/
│
└── rag/
```

---

# 47. Day 34 实战项目

今天做：

## Production Incident Agent

用户输入：

> “检查 production order-api 为什么 CPU 很高，如果确定是服务异常就重启。”

Workflow：

```text
START
 ↓
Classify
 ↓
AWS Diagnostic
 ↓
CloudWatch
 ↓
RAG
 ↓
Analyze
 ↓
Risk Check
 ↓
Approval
 ↓
Restart ECS
 ↓
Verify
 ↓
Answer
```

---

# 48. Step 1：获取 ECS 状态

MCP Tool：

```text
getEcsServiceStatus()
```

返回：

```json
{
  "service": "order-api",
  "desiredCount": 3,
  "runningCount": 3,
  "cpu": 91
}
```

---

# 49. Step 2：CloudWatch

Tool：

```text
getCpuMetrics()
```

结果：

```text
CPU:
91%

Duration:
15 minutes
```

---

# 50. Step 3：RAG

搜索：

```text
production
order-api
high CPU
JVM
GC
```

找到：

```text
Java JVM troubleshooting
ECS performance guide
Order API architecture
```

---

# 51. Step 4：Analyze

LLM：

```text
Evidence:
CPU 91%
GC activity high
Memory pressure moderate

Likely cause:
JVM garbage collection pressure
```

注意：

这只是：

```text
Hypothesis
```

不是事实。

---

# 52. Step 5：Policy

Workflow：

```text
restartEcsService
```

Policy：

```text
Risk = HIGH
```

所以：

```text
WAITING_FOR_APPROVAL
```

---

# 53. Step 6：Human

React：

```text
⚠ Restart production order-api?

Reason:
High CPU + GC pressure

[Approve]
[Reject]
```

---

# 54. Step 7：Approve

用户：

```text
Approve
```

Workflow：

```text
Checkpoint
 ↓
Resume
 ↓
restartEcsService
```

---

# 55. Step 8：验证

Restart 后：

```text
getEcsServiceStatus()
```

检查：

```text
runningCount = 3
CPU = 42%
```

---

# 56. Step 9：最终回答

```text
Production order-api was experiencing
high CPU utilization caused by JVM GC pressure.

The service was restarted after approval.

Current CPU:
42%

Running tasks:
3/3
```

---

# 57. 这个项目真正学到什么

不是：

```text
如何调用 LLM
```

而是：

```text
如何让 AI
安全地执行企业任务。
```

这就是 AI Engineer 和普通：

```text
Chatbot Developer
```

之间很重要的区别。

---

# 58. Day 34 测试

### Test 1

正常查询：

```text
What is ECS?
```

流程：

```text
RAG
 ↓
Answer
```

---

### Test 2

数据库查询：

```text
How many production documents?
```

流程：

```text
MCP
 ↓
DB
 ↓
Answer
```

---

### Test 3

AWS Diagnostic：

```text
Why is order-api CPU high?
```

流程：

```text
AWS
 ↓
CloudWatch
 ↓
RAG
 ↓
Analyze
```

---

### Test 4

高风险：

```text
Restart production order-api.
```

必须：

```text
Approval Required
```

不能直接执行。

---

### Test 5

Reject：

```text
Reject
```

Workflow：

```text
CANCELLED
```

---

### Test 6

服务 Crash：

```text
Node
 ↓
Checkpoint
 ↓
Restart
 ↓
Resume
```

---

# 59. Day 34 最终检查清单

```text
[ ] Agent State
[ ] Node
[ ] Edge
[ ] Conditional Edge
[ ] Workflow Graph
[ ] Workflow Executor
[ ] Intent Router
[ ] Retry
[ ] Exponential Backoff
[ ] Checkpoint
[ ] Resume
[ ] Workflow Status
[ ] Human-in-the-Loop
[ ] Approval API
[ ] Approval UI
[ ] Policy Engine
[ ] Risk Level
[ ] MCP Integration
[ ] RAG Integration
[ ] SSE Workflow Events
[ ] Audit Log
[ ] Multi-Tenant Security
```

---

# 60. Day 34 最终架构

```text
                         ┌───────────────┐
                         │     React     │
                         └───────┬───────┘
                                 │
                                SSE
                                 │
                                 ▼
                         Spring Boot API
                                 │
                                 ▼
                         Agent Workflow
                                 │
                              State
                                 │
                                 ▼
                         Intent Router
                                 │
             ┌───────────────────┼───────────────────┐
             ▼                   ▼                   ▼
            RAG                  MCP                API
             │                   │                   │
             ▼                   ▼                   ▼
       Advanced RAG         MCP Servers         Business
             │                   │
             └──────────────┬────┘
                            ▼
                         Analyze
                            │
                            ▼
                       Policy Engine
                            │
                    ┌───────┴────────┐
                    ▼                ▼
                  LOW              HIGH
                    │                │
                    │         Human Approval
                    │                │
                    └───────┬────────┘
                            ▼
                         Execute
                            │
                         Checkpoint
                            │
                            ▼
                         Verify
                            │
                            ▼
                       Final Answer
                            │
                            ▼
                          Bedrock
```

---

## Day 34 最核心的一句话

Day 33 是：

```text
让 Agent 能使用企业工具
```

Day 34 是：

```text
让 Agent 按照可控的流程，
安全地使用企业工具。
```

这一步之后，你的架构已经开始具备真正的 **Enterprise Agent Platform** 雏形。

**Day 35** 建议做 **Agent Memory 2.0：短期记忆、长期记忆、Semantic Memory、Episodic Memory、User Profile、Memory Retrieval，以及 PostgreSQL + pgvector 的企业级 Memory Architecture**。
