# Day 52：Multi-Agent 企业架构

今天把 Day 51 的：

```text
单 Agent
   ↓
Workflow
```

升级成：

```text
Multi-Agent System
```

核心目标：

> **一个 Supervisor Agent 负责协调多个专业 Agent，每个 Agent 只负责自己擅长的任务。**

---

# 1. 今天最终架构

```text
                         User
                           │
                           ▼
                    Supervisor Agent
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
        Research Agent  Data Agent   Report Agent
             │             │             │
             ▼             ▼             ▼
            RAG          Database       S3
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                       Evaluator
                           │
                           ▼
                      Final Answer
```

---

# 2. 为什么需要 Multi-Agent？

一个 Agent 如果同时负责：

```text
RAG
SQL
CRM
代码
报告
邮件
审批
```

会越来越复杂：

```text
Agent
 ├── 50 Tools
 ├── 20 Instructions
 ├── 10 workflows
 └── huge context
```

容易出现：

```text
Tool selection 错误
Prompt 复杂
权限复杂
成本增加
```

Multi-Agent 的思路：

```text
Supervisor
│
├── Research Agent
├── Data Agent
├── Customer Agent
└── Report Agent
```

---

# 3. Agent 的职责应该单一

### Research Agent

```text
搜索知识
分析文档
```

### Data Agent

```text
SQL
数据分析
统计
```

### Customer Agent

```text
CRM
客户资料
订单
```

### Report Agent

```text
生成报告
```

---

# 4. Supervisor Agent

Supervisor 不应该自己做所有事情。

它主要负责：

```text
理解任务
 ↓
拆分任务
 ↓
选择 Agent
 ↓
传递任务
 ↓
汇总结果
 ↓
决定下一步
```

---

# 5. Supervisor Workflow

例如用户：

> 分析客户 C1001 最近 30 天订单，并结合公司退货政策生成风险报告。

Supervisor：

```text
User Request
     │
     ▼
Supervisor
     │
     ├── Customer Agent
     │
     ├── Data Agent
     │
     ├── Research Agent
     │
     └── Report Agent
```

---

# 6. Agent Graph

```text
                    START
                      │
                      ▼
                 Supervisor
                      │
           ┌──────────┼──────────┐
           ▼          ▼          ▼
       Customer      Data      Research
           │          │          │
           └──────────┼──────────┘
                      ▼
                   Merge
                      │
                      ▼
                  Evaluator
                      │
                      ▼
                  Report Agent
                      │
                      ▼
                    FINAL
```

---

# 7. Multi-Agent 的核心不是“多个 LLM”

重点是：

```text
Agent
+
Specialization
+
Routing
+
Shared State
+
Permissions
+
Evaluation
```

不是：

```text
3 个 LLM
=
Multi-Agent
```

---

# 8. Agent Contract

每个 Agent 应该有明确 Contract：

```java
public interface SpecialistAgent {

    String name();

    String description();

    AgentResult execute(
        AgentTask task,
        AgentState state
    );
}
```

例如：

```java
@Component
public class ResearchAgent
        implements SpecialistAgent {

    @Override
    public String name() {
        return "research_agent";
    }

    @Override
    public String description() {
        return """
            Search and analyze enterprise
            knowledge documents.
            """;
    }
}
```

---

# 9. Agent Registry

不要：

```java
if ("research".equals(type)) {
    ...
}
```

到处写。

建立：

```java
public interface AgentRegistry {

    SpecialistAgent get(String name);

    List<SpecialistAgent> availableAgents(
        UserContext user
    );
}
```

---

# 10. Agent Registry

最终：

```text
AgentRegistry
│
├── ResearchAgent
├── DataAgent
├── CustomerAgent
├── ReportAgent
└── CodingAgent
```

Supervisor 可以看到：

```text
Agent Name
Description
Allowed Capabilities
```

---

# 11. Supervisor 不应该看到所有内部细节

例如：

```text
Research Agent
```

Supervisor 只需要知道：

```text
"Search enterprise knowledge"
```

而不是：

```text
pgvector
Embedding
Chunk
Reranker
PostgreSQL
```

这样可以减少 Context。

---

# 12. Agent Capability

建议给 Agent 定义：

```java
public record AgentCapability(
    String name,
    String description,
    Set<String> permissions
) {}
```

例如：

```text
Research Agent
permissions:
    READ_KNOWLEDGE
```

Data Agent：

```text
READ_ANALYTICS
```

Customer Agent：

```text
READ_CUSTOMER
READ_ORDER
```

---

# 13. Agent 权限隔离

这是 Multi-Agent 最重要的安全设计之一。

不要：

```text
Supervisor
 ↓
所有 Agent
 ↓
所有 Tools
```

应该：

```text
Supervisor
│
├── Research Agent
│     └── RAG Tools
│
├── Data Agent
│     └── Analytics Tools
│
└── Customer Agent
      └── CRM Tools
```

---

# 14. Agent → Tool 权限

最终形成：

```text
User
 ↓
Supervisor
 ↓
Agent Authorization
 ↓
Tool Authorization
 ↓
API
```

有两层：

```text
Agent Permission
+
Tool Permission
```

---

# 15. Shared State

Multi-Agent 最大的问题：

> Agent 之间如何共享结果？

例如：

```text
Customer Agent
      ↓
customer data

Data Agent
      ↓
order statistics

Research Agent
      ↓
return policy
```

最后：

```text
Shared State
```

---

# 16. Shared State

```java
public class MultiAgentState {

    private UUID runId;

    private String userRequest;

    private Map<String, Object> outputs;

    private List<AgentResult> results;

    private List<Citation> citations;

    private int step;

    private AgentStatus status;
}
```

---

# 17. 不要让 Agent 直接互相读数据库

错误：

```text
CustomerAgent
 ↓
UPDATE shared DB
 ↓
DataAgent
 ↓
SELECT
```

更好：

```text
CustomerAgent
 ↓
AgentResult
 ↓
Shared State
 ↓
DataAgent
```

---

# 18. Agent Result

统一格式：

```java
public record AgentResult(

    String agentName,

    String status,

    Object output,

    List<Citation> citations,

    Map<String, Object> metadata

) {}
```

例如：

```json
{
  "agentName": "data_agent",
  "status": "SUCCESS",
  "output": {
    "orderCount": 12,
    "totalAmount": 6200
  }
}
```

---

# 19. Supervisor 的输入

Supervisor 看到：

```text
Available Agents:

1. research_agent
   Search company knowledge.

2. data_agent
   Analyze customer/order data.

3. customer_agent
   Retrieve customer information.

4. report_agent
   Generate reports.
```

然后决定：

```text
customer_agent
→ data_agent
→ research_agent
→ report_agent
```

---

# 20. Agent Routing

最简单：

```text
User
 ↓
Supervisor
 ↓
Agent
```

复杂：

```text
Supervisor
 ↓
Customer Agent
 ↓
Data Agent
 ↓
Research Agent
 ↓
Evaluator
 ↓
Report Agent
```

---

# 21. Conditional Routing

例如：

```text
Data Agent
     ↓
totalAmount
     │
     ├── <= $5,000
     │       ↓
     │     FINAL
     │
     └── > $5,000
             ↓
        Research Agent
             ↓
        Risk Analysis
```

---

# 22. Parallel Agents

如果：

```text
Customer Agent
Data Agent
Research Agent
```

相互独立：

```text
              Supervisor
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
   Customer      Data       Research
       │           │           │
       └───────────┼───────────┘
                   ▼
                 Merge
```

可以并行。

---

# 23. Parallel Agent Java

```java
CompletableFuture<AgentResult> customer =
    CompletableFuture.supplyAsync(
        () -> customerAgent.execute(task)
    );

CompletableFuture<AgentResult> data =
    CompletableFuture.supplyAsync(
        () -> dataAgent.execute(task)
    );

CompletableFuture<AgentResult> research =
    CompletableFuture.supplyAsync(
        () -> researchAgent.execute(task)
    );
```

然后：

```java
CompletableFuture.allOf(
    customer,
    data,
    research
).join();
```

---

# 24. Agent Dependency

如果：

```text
Data Agent
```

需要：

```text
Customer Agent
```

结果：

```text
Customer
 ↓
Data
```

不能并行。

所以 Supervisor 必须理解：

```text
Dependencies
```

---

# 25. DAG

Multi-Agent Workflow 最终可以形成：

```text
A
├── B
├── C
│
└── D
    ↑
    B + C
```

这就是：

> Directed Acyclic Graph

简称：

```text
DAG
```

---

# 26. 为什么 DAG 很重要？

因为企业任务经常不是：

```text
A → B → C
```

而是：

```text
       ┌→ B ─┐
A ─────┤     ├→ D
       └→ C ─┘
```

B、C 可以并行。

---

# 27. Evaluator Agent

完成后不要直接：

```text
Final
```

增加：

```text
Evaluator Agent
```

负责：

```text
检查结果是否完整
检查事实是否支持
检查引用
检查业务规则
```

---

# 28. Evaluator

例如：

```text
Customer Agent ✓
Data Agent ✓
Research Agent ✓
```

Evaluator：

```text
Are all required fields present?
Are sources valid?
Does analysis meet requirements?
```

---

# 29. Evaluator 输出

```json
{
  "status": "PASS",
  "missing": [],
  "issues": []
}
```

或者：

```json
{
  "status": "RETRY",
  "missing": [
    "return policy"
  ]
}
```

然后：

```text
Evaluator
 ↓
Research Agent
```

---

# 30. Evaluator 不应该无限重试

设置：

```text
maxAgentRetries = 2
```

例如：

```text
Research
 ↓
Evaluator
 ↓
FAIL
 ↓
Research
 ↓
Evaluator
 ↓
FAIL
 ↓
STOP
```

---

# 31. Multi-Agent Memory

需要区分：

### User Memory

```text
用户长期偏好
```

### Conversation Memory

```text
当前对话
```

### Agent State

```text
当前任务状态
```

### Shared State

```text
多个 Agent 共享的任务结果
```

不要混在一起。

---

# 32. Memory Architecture

```text
                    Memory
                       │
       ┌───────────────┼───────────────┐
       ▼               ▼               ▼
 User Memory    Conversation       Agent State
                    Memory
                                       │
                                       ▼
                                  Shared State
```

---

# 33. Multi-Agent + RAG

Research Agent：

```text
Query
 ↓
Embedding
 ↓
pgvector
 ↓
Rerank
 ↓
Citation
```

它只负责：

```text
Knowledge Retrieval
```

不负责：

```text
CRM
SQL
Payment
```

---

# 34. Multi-Agent + MCP

Customer Agent：

```text
Customer Agent
 ↓
MCP Client
 ↓
Customer MCP Server
 ↓
CRM API
```

Data Agent：

```text
Data Agent
 ↓
MCP Client
 ↓
Analytics MCP
 ↓
Data Warehouse
```

---

# 35. Multi-Agent + MCP 最终结构

```text
                    Supervisor
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
     Research          Data          Customer
       Agent           Agent            Agent
         │               │               │
        MCP             MCP             MCP
         │               │               │
         ▼               ▼               ▼
    Knowledge        Analytics          CRM
```

---

# 36. Multi-Agent Security

每一个 Agent 都应该有：

```text
Agent Identity
Agent Role
Allowed Tools
Allowed Data
Tenant
```

例如：

```text
research-agent
tenant=A
READ_KNOWLEDGE
```

Data Agent：

```text
data-agent
tenant=A
READ_ANALYTICS
```

---

# 37. Tenant Isolation

Multi-Agent 中尤其重要。

错误：

```text
Supervisor
 ↓
Agent
 ↓
Cross Tenant Data
```

正确：

```text
User Tenant
 ↓
Supervisor
 ↓
Agent Context
 ↓
Tool Filter
 ↓
Database
```

每个 Agent 都必须继承：

```text
tenantId
userId
authorization
```

不能让 Agent 自己决定 tenant。

---

# 38. Agent-to-Agent Communication

不推荐：

```text
Agent A
 ↓
直接调用 Agent B Java Method
```

更好的方式：

```text
Supervisor
 ↓
Task
 ↓
Agent B
 ↓
Result
 ↓
Supervisor
```

这样更容易：

```text
Audit
Retry
Authorization
Observability
```

---

# 39. Agent Task

统一：

```java
public record AgentTask(

    UUID runId,

    String taskId,

    String objective,

    Map<String, Object> inputs,

    String tenantId

) {}
```

---

# 40. Agent Task Example

```json
{
  "runId": "abc",
  "taskId": "task-002",
  "objective":
    "Analyze customer C1001 orders",
  "inputs": {
    "customerId": "C1001",
    "days": 30
  }
}
```

---

# 41. Supervisor → Agent

Supervisor：

```text
task:
"Analyze customer C1001 orders"
```

Data Agent：

```text
AgentResult
```

Supervisor：

```text
Shared State
```

然后继续。

---

# 42. 不要让 Agent 返回自然语言作为唯一结果

错误：

```text
Data Agent:
"The customer seems to have spent
around six thousand dollars..."
```

更好：

```json
{
  "orderCount": 12,
  "totalAmount": 6200,
  "currency": "USD"
}
```

然后 Supervisor 再让 LLM 生成自然语言。

---

# 43. Structured Output

Agent 输出尽量：

```text
JSON
Schema
Typed Object
```

而不是：

```text
free-form text
```

例如：

```java
public record SalesAnalysis(
    int orderCount,
    BigDecimal totalAmount,
    String currency,
    boolean suspicious
) {}
```

---

# 44. 为什么 Structured Output 很重要？

因为：

```text
Agent A
 ↓
Agent B
```

如果 A 返回：

```text
"Looks like the customer spent
quite a lot recently..."
```

B 很难可靠处理。

如果：

```json
{
  "totalAmount": 6200,
  "suspicious": true
}
```

B 就可以确定处理。

---

# 45. Multi-Agent Cost

Multi-Agent 很容易变贵。

例如：

```text
Supervisor = 2 calls
Research = 3 calls
Data = 2 calls
Customer = 2 calls
Evaluator = 2 calls
Report = 2 calls
```

一次任务：

```text
13 LLM calls
```

所以：

> **Agent 数量不是越多越好。**

---

# 46. 推荐原则

优先：

```text
1 Supervisor
+
2–4 Specialist Agents
```

不要：

```text
20 Agents
```

除非经过真实业务验证。

---

# 47. Model Routing

不同 Agent 不一定使用同一个模型。

例如：

```text
Supervisor
→ 高能力模型

Research
→ 中等模型

Classification
→ 小模型

Report
→ 高能力模型
```

架构：

```text
Agent
 ↓
Model Router
 ↓
Model
```

---

# 48. Model Router

```java
public ChatModel select(
        AgentType agent,
        TaskComplexity complexity) {

    if (agent == AgentType.SUPERVISOR) {
        return premiumModel;
    }

    if (complexity == TaskComplexity.LOW) {
        return fastModel;
    }

    return standardModel;
}
```

---

# 49. 为什么 Model Routing 可以省钱？

例如：

```text
1000 requests
```

如果全部：

```text
Premium Model
```

成本可能很高。

如果：

```text
80%
Small/Fast Model
20%
Premium
```

通常可以显著降低成本，同时保持关键任务质量。

具体收益必须用你的真实模型价格和评测数据计算。

---

# 50. Multi-Agent Observability

Trace：

```text
trace
│
├── supervisor
│
├── customer_agent
│   └── crm_tool
│
├── data_agent
│   ├── sql_tool
│   └── analytics
│
├── research_agent
│   └── rag
│
├── evaluator
│
└── report_agent
```

这样可以知道：

> 到底哪个 Agent 最慢、最贵、最容易失败。

---

# 51. Metrics

增加：

```text
multi_agent.run.count
multi_agent.agent.count
multi_agent.agent.latency
multi_agent.agent.failure
multi_agent.routing.count
multi_agent.parallel.duration
multi_agent.retry.count
multi_agent.evaluation.failure
```

---

# 52. Multi-Agent Audit

审计记录：

```text
run_id
agent_name
task_id
tool_name
input_hash
output_hash
status
latency
model
tokens
```

敏感原文不建议全部记录。

---

# 53. Day 52 数据库

增加：

```sql
CREATE TABLE agent_tasks (
    id UUID PRIMARY KEY,

    run_id UUID NOT NULL,

    agent_name VARCHAR(100),

    objective TEXT,

    status VARCHAR(30),

    started_at TIMESTAMP,

    completed_at TIMESTAMP
);
```

---

# 54. Agent Results

```sql
CREATE TABLE agent_results (
    id BIGSERIAL PRIMARY KEY,

    task_id UUID NOT NULL,

    agent_name VARCHAR(100),

    result JSONB,

    status VARCHAR(30),

    created_at TIMESTAMP
);
```

---

# 55. Shared State

```sql
CREATE TABLE agent_state (
    run_id UUID PRIMARY KEY,

    state JSONB NOT NULL,

    version BIGINT NOT NULL,

    updated_at TIMESTAMP NOT NULL
);
```

---

# 56. Day 52 项目结构

```text
agent/
│
├── supervisor/
│   └── SupervisorAgent.java
│
├── specialists/
│   ├── ResearchAgent.java
│   ├── DataAgent.java
│   ├── CustomerAgent.java
│   └── ReportAgent.java
│
├── registry/
│   └── AgentRegistry.java
│
├── routing/
│   └── AgentRouter.java
│
├── state/
│   ├── MultiAgentState.java
│   └── AgentResult.java
│
├── task/
│   └── AgentTask.java
│
├── evaluation/
│   └── EvaluatorAgent.java
│
└── security/
    └── AgentAuthorization.java
```

---

# 57. Day 52 实战项目

做一个：

> **Enterprise Customer Risk Agent**

用户输入：

```text
分析客户 C1001 最近 30 天的订单，
结合公司退货政策判断是否存在风险，
如果风险较高，生成风险报告。
```

---

# 58. Workflow

```text
                 USER
                   │
                   ▼
              SUPERVISOR
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
    Customer      Data      Research
      Agent       Agent       Agent
        │          │          │
        └──────────┼──────────┘
                   ▼
                EVALUATOR
                   │
              ┌────┴────┐
              ▼         ▼
            PASS      RETRY
              │
              ▼
         Report Agent
              │
              ▼
            FINAL
```

---

# 59. Day 52 必做任务

```text
[ ] 1. Supervisor Agent
[ ] 2. Research Agent
[ ] 3. Data Agent
[ ] 4. Customer Agent
[ ] 5. Report Agent
[ ] 6. Agent Registry
[ ] 7. Shared State
[ ] 8. Agent Task
[ ] 9. Structured Agent Result
[ ] 10. Conditional Routing
[ ] 11. Parallel Agents
[ ] 12. Evaluator
[ ] 13. Agent Authorization
[ ] 14. Agent Audit
[ ] 15. Agent Metrics
```

---

# 60. Day 52 验收测试

### Test 1：简单问题

```text
公司退货政策是什么？
```

应该：

```text
Supervisor
 ↓
Research Agent
 ↓
RAG
 ↓
Answer
```

---

### Test 2：客户数据

```text
查询 C1001 最近订单。
```

应该：

```text
Supervisor
 ↓
Customer/Data Agent
 ↓
CRM/API
 ↓
Answer
```

---

### Test 3：复杂任务

```text
分析 C1001 最近30天订单，
结合退货政策判断风险。
```

应该：

```text
Supervisor
 ├── Customer
 ├── Data
 └── Research
       ↓
     Evaluator
       ↓
     Final
```

---

# 61. Test 4：Parallel

确认：

```text
Customer Agent
Data Agent
Research Agent
```

能够同时运行，并在最后：

```text
Merge
```

---

# 62. Test 5：Agent Failure

让：

```text
Research Agent
```

返回失败。

Supervisor：

```text
Evaluator
 ↓
Retry
 ↓
Research
```

最多：

```text
2 retries
```

---

# 63. Test 6：权限

Data Agent 尝试：

```text
DELETE customer
```

必须：

```text
DENIED
```

---

# 64. Test 7：Checkpoint

```text
Supervisor
 ↓
Customer Agent ✓
 ↓
Data Agent ✓
 ↓
Pod Crash
```

重新启动：

```text
Load State
 ↓
Research Agent
```

而不是从头开始。

---

# 65. Day 52 面试重点

### Q1：Multi-Agent 和单 Agent 的区别？

```text
Single Agent
=
一个 Agent 负责全部任务

Multi-Agent
=
多个专业 Agent
+
Supervisor / Workflow
```

---

### Q2：为什么要 Specialist Agent？

```text
降低 Prompt Complexity
降低 Tool Complexity
增强权限隔离
提高可维护性
```

---

### Q3：Agent 可以并行吗？

可以，但必须满足：

```text
任务之间没有依赖
```

---

### Q4：Agent 如何共享数据？

推荐：

```text
Shared State
+
Structured Agent Result
```

---

### Q5：为什么 Agent Result 应该 Structured？

因为：

```text
Agent A
 ↓
Agent B
```

需要机器可靠解析，而不是依赖自然语言。

---

# 66. Day 52 最重要架构

```text
                         USER
                           │
                           ▼
                     SUPERVISOR
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
         CUSTOMER         DATA        RESEARCH
          AGENT           AGENT         AGENT
             │             │             │
             ▼             ▼             ▼
            CRM          Database        RAG
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                      SHARED STATE
                           │
                           ▼
                       EVALUATOR
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                  RETRY          PASS
                    │             │
                    └──────┐      ▼
                           │   REPORT
                           │      │
                           └──────┤
                                  ▼
                                FINAL
```

---

# 67. Day 50 → Day 52 的进化

```text
Day 50
Single Agent
     ↓
Tool Calling
     ↓
MCP

Day 51
Single Agent
     ↓
Workflow
     ↓
State Machine
     ↓
Checkpoint

Day 52
Multi-Agent
     ↓
Supervisor
     ↓
Specialist Agents
     ↓
Parallel Execution
     ↓
Evaluator
```

到这里，你已经具备了构建**企业级 Agent 平台**的核心架构。

**Day 53** 下一步应该进入 **AI Agent Evaluation & Testing**：建立一套真正能测试 Supervisor、RAG、Tool Calling、Multi-Agent Routing、Hallucination、Prompt Injection、权限隔离和成本的自动化评测系统，而不是只靠“问几个问题看看回答好不好”。
