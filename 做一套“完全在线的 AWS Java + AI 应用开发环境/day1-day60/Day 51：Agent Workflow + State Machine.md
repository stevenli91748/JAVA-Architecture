# Day 51：Agent Workflow + State Machine

Day 50 我们完成了：

```text
Agent
├── RAG
├── Tool Calling
├── MCP
├── Authorization
├── Audit
└── Human Approval
```

今天把它从一个简单的：

```text
LLM → Tool → LLM → Answer
```

升级成**企业级 Agent Workflow**：

```text
User
 ↓
Planner
 ↓
Router
 ├── RAG
 ├── Tool
 ├── Parallel Tools
 └── Human Approval
 ↓
Evaluator
 ↓
Retry / Continue
 ↓
Final Answer
```

---

# 1. 为什么需要 Workflow？

Day 50 的 Agent Loop：

```text
while (!finished) {
    LLM();
    Tool();
}
```

适合简单任务。

但是企业任务可能是：

> 查询客户 → 查询订单 → 查询退货政策 → 判断是否异常 → 生成报告 → 请求经理审批。

这时候最好明确控制：

```text
Step 1
 ↓
Step 2
 ↓
Step 3
 ↓
Decision
 ↓
Approval
 ↓
Final
```

这就是 **Agent Workflow / State Graph**。

---

# 2. 今天的核心概念

```text
① State
② Node
③ Edge
④ Conditional Routing
⑤ Checkpoint
⑥ Retry
⑦ Parallel Execution
⑧ Human-in-the-loop
⑨ Resume
⑩ Long-running Agent
```

---

# 3. 最终架构

```text
                         User
                           │
                           ▼
                        Agent API
                           │
                           ▼
                         State
                           │
                           ▼
                        Planner
                           │
                    ┌──────┼──────┐
                    ▼      ▼      ▼
                   RAG    Tools   API
                    │      │      │
                    └──────┼──────┘
                           ▼
                       Evaluator
                           │
                 ┌─────────┼─────────┐
                 ▼         ▼         ▼
               Retry    Approval    Final
                 │         │
                 └────┬────┘
                      ▼
                  Checkpoint
                      │
                      ▼
                    Resume
```

---

# 4. State 是什么？

State 就是：

> Agent 当前执行到哪里，以及目前掌握了什么信息。

例如：

```java
public class AgentState {

    private UUID runId;

    private String tenantId;

    private String userId;

    private String userRequest;

    private String plan;

    private List<Message> messages;

    private List<ToolResult> toolResults;

    private List<Citation> citations;

    private int step;

    private AgentStatus status;
}
```

---

# 5. 为什么 State 必须持久化？

假设 Agent：

```text
Step 1 ✓
Step 2 ✓
Step 3
```

突然：

```text
EKS Pod
    ↓
Crash
```

如果 State 只在 JVM：

```text
State
 ↓
Lost
```

Agent 必须从头开始。

生产环境应该：

```text
Agent
 ↓
Checkpoint
 ↓
PostgreSQL
```

---

# 6. Checkpoint

例如：

```text
run-123

Step 1 ✓
Step 2 ✓
Step 3 WAITING_APPROVAL
```

保存：

```text
PostgreSQL
```

然后：

```text
Pod Crash
 ↓
New Pod
 ↓
Load checkpoint
 ↓
Resume Step 3
```

这就是：

> **Durable Agent Execution**

---

# 7. State Machine

最简单：

```text
START
  ↓
PLAN
  ↓
EXECUTE
  ↓
EVALUATE
  ↓
FINAL
```

但是企业版：

```text
START
  ↓
PLAN
  ↓
ROUTE
 ├── RAG
 ├── TOOL
 └── MULTI-TOOL
       ↓
    EVALUATE
       │
   ┌───┼────┐
   ▼   ▼    ▼
 RETRY APPROVAL FINAL
   │    │
   └────┴──→ EXECUTE
```

---

# 8. Node

每一个步骤就是一个 Node：

```text
PlannerNode
RetrieverNode
ToolNode
EvaluatorNode
ApprovalNode
FinalNode
```

接口：

```java
public interface AgentNode {

    AgentState execute(
        AgentState state
    );
}
```

---

# 9. Planner Node

```java
@Component
public class PlannerNode
        implements AgentNode {

    @Override
    public AgentState execute(
            AgentState state) {

        String plan =
            planner.plan(
                state.getUserRequest()
            );

        state.setPlan(plan);

        return state;
    }
}
```

---

# 10. Retriever Node

```java
@Component
public class RetrieverNode
        implements AgentNode {

    @Override
    public AgentState execute(
            AgentState state) {

        var documents =
            ragService.search(
                state.getUserRequest(),
                state.getTenantId()
            );

        state.setDocuments(documents);

        return state;
    }
}
```

---

# 11. Tool Node

```java
@Component
public class ToolNode
        implements AgentNode {

    @Override
    public AgentState execute(
            AgentState state) {

        var result =
            toolExecutor.execute(
                state.getCurrentToolCall()
            );

        state.addToolResult(result);

        return state;
    }
}
```

---

# 12. Evaluator Node

Evaluator 判断：

```text
任务完成了吗？
```

例如：

```text
if (answerIsGood) {
    FINAL
} else if (canRetry) {
    RETRY
} else {
    FAILED
}
```

---

# 13. Conditional Routing

这是 Workflow 最重要的能力之一。

```text
Evaluator
   │
   ├── SUCCESS → FINAL
   │
   ├── NEED_MORE_INFO → TOOL
   │
   ├── RETRY → RETRY
   │
   └── APPROVAL → HUMAN
```

---

# 14. Router

```java
public AgentNode route(
        AgentState state) {

    return switch (
        state.getDecision()
    ) {

        case COMPLETE ->
            finalNode;

        case RETRY ->
            retryNode;

        case NEED_TOOL ->
            toolNode;

        case APPROVAL_REQUIRED ->
            approvalNode;
    };
}
```

---

# 15. 一个真实企业流程

用户：

> 分析客户 C1001 最近 30 天订单，如果异常金额超过 $5,000 就创建风险报告并提交经理审批。

Workflow：

```text
START
 ↓
PLAN
 ↓
get_customer
 ↓
get_orders
 ↓
ANALYZE
 ↓
异常？
 ├── NO → FINAL
 │
 └── YES
       ↓
   create_risk_report
       ↓
   amount > $5,000?
       ↓
   HUMAN APPROVAL
       ↓
   FINAL
```

---

# 16. Parallel Tool Calls

这里非常重要。

假设：

```text
get_customer
get_orders
search_policy
```

三个操作互不依赖。

不要：

```text
get_customer
 ↓
get_orders
 ↓
search_policy
```

可以：

```text
        ┌── get_customer ──┐
START ──┼── get_orders ────┼→ ANALYZE
        └── search_policy ─┘
```

---

# 17. 为什么 Parallel？

假设：

```text
get_customer = 300ms
get_orders = 500ms
search_policy = 400ms
```

串行：

```text
300 + 500 + 400
=
1200ms
```

并行：

```text
max(300,500,400)
≈ 500ms
```

实际还会有调度和网络开销，但方向非常明确：

> 独立 I/O 应尽量并行。

---

# 18. Java CompletableFuture

简单实现：

```java
CompletableFuture<Customer> customer =
    CompletableFuture.supplyAsync(
        () -> customerService.get(id)
    );

CompletableFuture<List<Order>> orders =
    CompletableFuture.supplyAsync(
        () -> orderService.list(id)
    );

CompletableFuture<Policy> policy =
    CompletableFuture.supplyAsync(
        () -> policyService.get()
    );
```

然后：

```java
CompletableFuture.allOf(
    customer,
    orders,
    policy
).join();
```

---

# 19. 不要无限创建线程

生产不要：

```java
Executors.newCachedThreadPool()
```

然后 Agent：

```text
100 Tool Calls
 ↓
100 Threads
```

应该使用：

```text
Bounded Thread Pool
```

例如：

```text
Core = 10
Max = 20
Queue = 100
```

具体参数通过压测确定。

---

# 20. Parallel Tool 的限制

只能并行：

```text
Tool A
Tool B
Tool C
```

当：

```text
Tool B
依赖
Tool A
```

就不能并行。

例如：

```text
get_customer
      ↓
get_customer_orders
```

必须：

```text
A → B
```

---

# 21. Dependency Graph

复杂 Agent 可以表示：

```text
A
├── B
├── C
│
└── D
    ↑
    B + C
```

也就是：

```text
A
 ↓
B ──┐
    ├──→ D
C ──┘
```

这就是 Graph Workflow。

---

# 22. LangGraph 思维

即使你用 Java，也应该理解这种架构：

```text
Graph
=
Nodes
+
Edges
+
State
+
Checkpoint
```

Java 不一定非要使用 Python LangGraph。

你的 Java 项目完全可以自己实现：

```text
Agent Graph
```

---

# 23. Spring AI + Workflow

建议：

```text
Spring AI
=
AI Model / Tool / MCP

Your Workflow Engine
=
State / Routing / Retry / Approval
```

分工：

```text
Spring AI
       │
       ├── Chat Model
       ├── Embedding
       └── Tool Calling
       
Application
       │
       ├── State Machine
       ├── Checkpoint
       ├── Authorization
       └── Approval
```

这样架构更清晰。

---

# 24. Retry

Workflow：

```text
Tool
 ↓
FAIL
 ↓
Retry
 ↓
Tool
```

但必须：

```text
maxRetries = 3
```

例如：

```text
1st
 ↓
1 sec

2nd
 ↓
2 sec

3rd
 ↓
4 sec
```

---

# 25. Exponential Backoff

公式：

```text
delay =
base × 2^attempt
```

例如：

```text
attempt 0 → 1 sec
attempt 1 → 2 sec
attempt 2 → 4 sec
```

可以加入：

```text
jitter
```

避免大量 Agent 同时重试。

---

# 26. Retry 哪些错误？

适合：

```text
429
502
503
504
Network timeout
```

不适合：

```text
400
401
403
Invalid input
Business rule violation
```

---

# 27. Retry + Idempotency

尤其是：

```text
POST
Payment
Refund
Create Ticket
```

必须：

```text
Idempotency Key
```

例如：

```text
runId:stepId
```

保证：

```text
Retry
 ↓
不会重复执行业务动作
```

---

# 28. Human Approval Node

Workflow：

```text
Agent
 ↓
Risk Analysis
 ↓
Approval Required
 ↓
WAITING_APPROVAL
```

此时：

> **Agent 不继续执行。**

---

# 29. Waiting State

数据库：

```text
agent_runs

status =
WAITING_APPROVAL
```

保存：

```text
runId
toolName
arguments
riskLevel
requestedAt
```

---

# 30. 用户批准

```http
POST /api/v1/agent/runs/{id}/approve
```

然后：

```text
WAITING_APPROVAL
        ↓
APPROVED
        ↓
RESUME
        ↓
Tool
```

---

# 31. 用户拒绝

```text
WAITING_APPROVAL
        ↓
REJECTED
        ↓
FINAL
```

Agent 可以回答：

> 操作未执行，因为审批被拒绝。

---

# 32. Approval 必须防止越权

例如：

```text
Employee
```

不能批准：

```text
$100,000 payment
```

需要：

```text
Manager
```

甚至：

```text
Finance Director
```

所以：

```text
Risk Amount
 ↓
Approval Policy
 ↓
Required Role
```

---

# 33. Approval Policy

例如：

```text
<$500
→ 自动

$500-$5,000
→ Manager

>$5,000
→ Finance Director
```

这不是 LLM 决定的。

而是：

```text
Policy Engine
```

决定。

---

# 34. LLM 与 Policy 分离

非常重要：

```text
LLM
=
Reasoning

Policy Engine
=
Authority
```

不要：

```text
LLM:
"I think this is okay."
 ↓
Execute
```

应该：

```text
LLM
 ↓
Tool Request
 ↓
Policy Engine
 ↓
ALLOW / DENY / APPROVAL
```

---

# 35. Agent Cancellation

用户：

> 停止刚才的分析。

Workflow：

```text
RUNNING
 ↓
CANCEL_REQUESTED
 ↓
CANCELLED
```

Worker 必须检查：

```text
if (state.isCancellationRequested()) {
    stop();
}
```

---

# 36. Long-running Agent

例如：

> 分析过去三年的销售数据。

可能：

```text
5 minutes
```

架构：

```text
POST /agent-runs
        ↓
202 Accepted
        ↓
SQS
        ↓
Agent Worker
        ↓
Workflow
        ↓
Checkpoint
```

---

# 37. 为什么 SQS？

因为：

```text
HTTP
=
短请求

SQS
=
异步任务
```

这与 Day 48 RAG Worker 完全相同。

---

# 38. Agent Worker

```text
SQS
 ↓
KEDA
 ↓
Agent Worker
 ↓
Workflow
```

如果：

```text
100 Agent Runs
```

KEDA：

```text
2
 ↓
10
```

Worker 自动扩容。

---

# 39. Agent Workflow + KEDA

最终：

```text
Agent Queue
     │
     ▼
    KEDA
     │
     ▼
Agent Workers
     │
     ▼
Workflow
 ├── RAG
 ├── MCP
 ├── Tools
 └── Approval
```

---

# 40. Checkpoint Schema

推荐：

```sql
CREATE TABLE agent_checkpoints (
    run_id UUID PRIMARY KEY,

    step_name VARCHAR(100),

    state JSONB NOT NULL,

    version BIGINT NOT NULL,

    updated_at TIMESTAMP NOT NULL
);
```

---

# 41. 为什么要 version？

两个 Worker 可能同时：

```text
Worker A
 ↓
Checkpoint version 5

Worker B
 ↓
Checkpoint version 5
```

同时更新可能产生：

```text
Lost Update
```

所以：

```text
version
```

可以实现：

```text
Optimistic Locking
```

---

# 42. Optimistic Lock

例如：

```sql
UPDATE agent_checkpoints
SET
    state = :state,
    version = version + 1
WHERE
    run_id = :runId
AND
    version = :expectedVersion;
```

如果：

```text
updated rows = 0
```

说明：

```text
Checkpoint 已被其他 Worker 更新。
```

---

# 43. Agent Exactly-Once？

不要轻易声称：

> Agent exactly once execution。

现实分布式系统里更实用的是：

```text
At-least-once execution
+
Idempotent tools
+
Checkpoint
```

这才是可靠设计。

---

# 44. Workflow 状态

建议：

```java
public enum AgentStatus {

    CREATED,

    RUNNING,

    WAITING_TOOL,

    WAITING_APPROVAL,

    RETRYING,

    COMPLETED,

    FAILED,

    CANCELLED,

    TIMEOUT
}
```

---

# 45. Step 状态

```java
public enum StepStatus {

    PENDING,
    RUNNING,
    SUCCESS,
    FAILED,
    RETRYING,
    SKIPPED
}
```

---

# 46. 完整 Workflow

```text
             START
               │
               ▼
             PLAN
               │
               ▼
             ROUTE
               │
       ┌───────┼────────┐
       ▼       ▼        ▼
      RAG     TOOL    PARALLEL
       │       │        │
       └───────┼────────┘
               ▼
           EVALUATE
               │
       ┌───────┼──────────┐
       ▼       ▼          ▼
    RETRY   APPROVAL    COMPLETE
       │       │          │
       │       ▼          ▼
       │     RESUME      FINAL
       │
       └──────→ ROUTE
```

---

# 47. 一个完整案例

用户：

> 分析客户 C1001 最近 30 天订单，如果总金额超过 $5,000，查询公司的风险政策，生成风险报告并提交经理审批。

Workflow：

```text
START
 ↓
PLAN
 ↓
get_customer
 ↓
get_orders
 ↓
calculate_total
 ↓
total > 5000?
 ├── NO → FINAL
 │
 └── YES
       ↓
search_risk_policy
       ↓
generate_report
       ↓
approval
       ↓
WAITING_APPROVAL
```

---

# 48. 审批后 Resume

经理批准：

```text
APPROVED
 ↓
RESUME
 ↓
submit_risk_report
 ↓
COMPLETED
```

整个过程中：

```text
Pod
可以重启
```

因为：

```text
State
 ↓
PostgreSQL
```

---

# 49. Agent Workflow API

### 创建

```http
POST /api/v1/agent/runs
```

返回：

```json
{
  "runId": "abc123",
  "status": "CREATED"
}
```

### 查询

```http
GET /api/v1/agent/runs/abc123
```

### 审批

```http
POST /api/v1/agent/runs/abc123/approve
```

### 拒绝

```http
POST /api/v1/agent/runs/abc123/reject
```

### 取消

```http
POST /api/v1/agent/runs/abc123/cancel
```

---

# 50. Workflow Engine

可以先自己实现一个轻量版本：

```java
public interface WorkflowNode {

    NodeResult execute(
        AgentState state
    );
}
```

然后：

```java
public interface WorkflowRouter {

    WorkflowNode next(
        AgentState state
    );
}
```

这样：

```text
Node
+
Router
+
State
```

就形成基本 Graph。

---

# 51. 为什么现在不急着引入复杂框架？

因为你正在学习：

```text
Agent Architecture
```

先理解：

```text
State
Node
Edge
Checkpoint
Router
```

之后再使用：

```text
LangGraph
或
其他 Agent Workflow Framework
```

会容易很多。

---

# 52. Java 企业应用推荐架构

你现在的项目可以这样：

```text
Spring Boot
│
├── API
│
├── Agent
│
│   ├── Workflow
│   ├── State
│   ├── Router
│   └── Executor
│
├── AI
│   ├── Chat Model
│   ├── Embedding
│   └── Reranker
│
├── Tools
│
├── MCP
│
├── RAG
│
├── Security
│
└── Persistence
```

---

# 53. Agent Workflow 与 Kubernetes

现在你的整个系统：

```text
                         AWS
                          │
                         EKS
                          │
          ┌───────────────┼────────────────┐
          ▼               ▼                ▼
       AI API         RAG Worker       Agent Worker
          │               │                │
         HPA             KEDA             KEDA
          │               │                │
          └───────────────┼────────────────┘
                          │
                      Karpenter
                          │
                          ▼
                        Nodes
```

---

# 54. Agent Worker 数据层

```text
Agent Worker
     │
     ├── PostgreSQL
     │    ├── agent_runs
     │    ├── checkpoints
     │    ├── tool_calls
     │    └── approvals
     │
     ├── Redis
     │    └── Locks / Cache
     │
     ├── SQS
     │    └── Async Jobs
     │
     └── Bedrock
```

---

# 55. Distributed Lock

两个 Worker：

```text
Worker A ──┐
           ├── run-123
Worker B ──┘
```

不能同时执行同一个 Agent Run。

可以：

```text
Redis
 ↓
Distributed Lock
 ↓
runId
```

例如：

```text
agent:lock:run-123
```

---

# 56. Lock 也不能替代数据库约束

推荐：

```text
Redis Lock
+
Optimistic Lock
+
Idempotency
```

三层保护。

---

# 57. Agent Workflow 的生产级可靠性

最终：

```text
Checkpoint
+
Idempotency
+
Retry
+
Timeout
+
Cancellation
+
Distributed Lock
+
Authorization
+
Audit
```

这才是企业 Agent。

---

# 58. Day 51 Metrics

增加：

```text
agent.run.duration
agent.step.duration
agent.retry.count
agent.approval.wait.duration
agent.checkpoint.count
agent.resume.count
agent.parallel.tasks
agent.max_step_reached
```

特别关注：

```text
WAITING_APPROVAL
```

停留时间。

---

# 59. Agent Dashboard

建议：

```text
Agent Runs
──────────────
Running       12
Waiting       8
Completed     920
Failed        13
Cancelled     4
```

以及：

```text
Average Run Time
P95 Run Time
Tool Failure Rate
Retry Rate
Approval Rate
Token Cost
```

---

# 60. Day 51 项目结构

```text
agent/
│
├── workflow/
│   ├── WorkflowEngine.java
│   ├── WorkflowNode.java
│   ├── WorkflowRouter.java
│   └── WorkflowDefinition.java
│
├── state/
│   ├── AgentState.java
│   └── AgentStatus.java
│
├── nodes/
│   ├── StartNode.java
│   ├── PlannerNode.java
│   ├── RouterNode.java
│   ├── ToolNode.java
│   ├── RagNode.java
│   ├── EvaluatorNode.java
│   ├── ApprovalNode.java
│   └── FinalNode.java
│
├── checkpoint/
│   └── CheckpointService.java
│
├── retry/
│   └── RetryPolicy.java
│
└── approval/
    └── ApprovalService.java
```

---

# 61. Day 51 必做任务

```text
[ ] 1. AgentState
[ ] 2. AgentStatus
[ ] 3. WorkflowNode
[ ] 4. WorkflowRouter
[ ] 5. PlannerNode
[ ] 6. ToolNode
[ ] 7. RAGNode
[ ] 8. EvaluatorNode
[ ] 9. ApprovalNode
[ ] 10. FinalNode
[ ] 11. Retry
[ ] 12. Conditional Routing
[ ] 13. Parallel Tool Calls
[ ] 14. PostgreSQL Checkpoint
[ ] 15. Resume
[ ] 16. Cancellation
[ ] 17. Agent Worker + SQS
[ ] 18. KEDA
```

---

# 62. Day 51 验收测试

### Test A：普通任务

```text
用户
 ↓
Agent
 ↓
RAG
 ↓
Answer
```

---

### Test B：多 Tool

```text
User
 ↓
Agent
 ↓
Customer Tool
 ↓
Order Tool
 ↓
Policy RAG
 ↓
Answer
```

---

### Test C：Parallel

```text
Agent
 ├── Customer
 ├── Orders
 └── Policy
        ↓
      Merge
```

---

### Test D：Approval

```text
Agent
 ↓
Risk > $5,000
 ↓
Approval
 ↓
WAITING_APPROVAL
```

然后：

```text
Approve
 ↓
Resume
 ↓
Execute
 ↓
Complete
```

---

### Test E：Pod Crash

模拟：

```text
Agent
 ↓
Step 3
 ↓
Pod Crash
```

新 Pod：

```text
Load Checkpoint
 ↓
Step 3
 ↓
Resume
```

如果能成功，今天最重要的目标就完成了。

---

# 63. Day 51 面试题

### Q1：为什么 Agent 需要 State？

因为 Agent 是：

```text
Multi-step
Long-running
Potentially interruptible
```

---

### Q2：Checkpoint 解决什么？

```text
Crash Recovery
Resume
Long-running Workflow
```

---

### Q3：为什么 Tool 可以并行？

当：

```text
A 不依赖 B
B 不依赖 C
```

可以并行执行。

---

### Q4：为什么不能所有 Tool 都并行？

存在：

```text
Dependencies
Ordering
Transactions
Side Effects
```

---

### Q5：Agent 如何防止无限执行？

```text
maxSteps
maxRuntime
maxCost
Cancellation
```

---

### Q6：Agent 如何保证重复 Tool 不造成重复业务？

```text
Idempotency Key
+
Business Constraint
+
Checkpoint
```

---

# 64. Day 51 最重要的一张图

```text
                         USER
                           │
                           ▼
                         AGENT
                           │
                           ▼
                         STATE
                           │
                           ▼
                        PLANNER
                           │
                           ▼
                         ROUTER
                           │
            ┌──────────────┼──────────────┐
            ▼              ▼              ▼
           RAG            TOOL         PARALLEL
            │              │              │
            └──────────────┼──────────────┘
                           ▼
                       EVALUATOR
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
           RETRY        APPROVAL       COMPLETE
             │             │             │
             │             ▼             ▼
             │          CHECKPOINT      FINAL
             │             │
             └─────────────┴──→ RESUME
```

---

# 65. 到 Day 51，你的技术体系已经变成

```text
Java
 │
Spring Boot
 │
Spring AI
 │
Agent
 │
Workflow
 │
RAG
 │
MCP
 │
Tool Calling
 │
Human-in-the-loop
 │
PostgreSQL
 │
Redis
 │
SQS
 │
EKS
 │
KEDA
 │
Karpenter
 │
AWS
```

这一步非常重要，因为你已经不再只是开发一个“调用 LLM 的 Java 程序”，而是在构建一个**可恢复、可扩展、可审计的企业 AI Agent 平台**。

### Day 52

下一步建议做 **Multi-Agent Architecture**：

```text
Supervisor Agent
       │
 ┌─────┼─────────┐
 ▼     ▼         ▼
RAG   Data     Coding
Agent Agent     Agent
 │      │         │
 └──────┼─────────┘
        ▼
     Evaluator
        │
        ▼
      Final
```

重点学习 **Supervisor、Specialist Agents、Agent-to-Agent communication、共享 State、并行 Agent、Agent 权限隔离、Multi-Agent Evaluation**。
