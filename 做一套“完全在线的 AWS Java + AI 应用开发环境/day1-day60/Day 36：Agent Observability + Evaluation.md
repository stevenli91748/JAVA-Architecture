# Day 36：Agent Observability + Evaluation

今天解决一个生产环境最现实的问题：

> **Agent 出问题时，到底是哪一步出了问题？**

以前你看到：

```text
User
 ↓
Agent
 ↓
Answer
```

但生产环境真正需要看到：

```text
User
 ↓
Workflow
 ↓
Memory
 ↓
Router
 ↓
RAG
 ├─ Query Rewrite
 ├─ Vector Search
 ├─ Keyword Search
 ├─ RRF
 └─ Reranker
 ↓
MCP Tool
 ↓
LLM
 ↓
Answer
```

并且每一步都知道：

```text
Latency
Token
Cost
Status
Error
Input
Output
Quality
```

---

# 1. Day 36 学习目标

今天完成：

```text
1. Observability
2. Trace
3. Span
4. Correlation ID
5. Agent Run
6. LLM Call Tracking
7. RAG Tracking
8. MCP Tool Tracking
9. Token / Cost Tracking
10. Error Tracking
11. Evaluation
12. Production Dashboard
```

最终架构：

```text
                    Agent
                      │
                      ▼
                OpenTelemetry
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
      Trace          Logs         Metrics
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                 CloudWatch
                      │
                      ▼
                 Evaluation
                      │
                      ▼
                AI Dashboard
```

---

# 2. 什么是 Observability

简单理解：

```text
Monitoring
=
系统有没有出问题？
```

Observability：

```text
为什么出问题？
```

例如：

```text
Agent latency = 8.2 sec
```

Monitoring 告诉你：

> 慢。

Observability 告诉你：

```text
LLM = 2.1 sec
RAG = 1.4 sec
MCP = 3.8 sec
DB = 0.5 sec
```

马上知道：

> MCP Tool 是瓶颈。

---

# 3. Trace

一次完整 Agent 请求：

```text
Trace
│
├── Agent Run
│
├── Memory Retrieval
│
├── Query Rewrite
│
├── Vector Search
│
├── Reranker
│
├── MCP Tool
│
├── Bedrock
│
└── Final Answer
```

整个过程拥有：

```text
traceId
```

例如：

```text
traceId = 8fa91c...
```

---

# 4. Span

Trace 里面的每一步叫：

```text
Span
```

例如：

```text
Trace
│
├── agent.run                 8000ms
│
├── memory.retrieve            220ms
├── rag.retrieve               850ms
├── rag.rerank                 310ms
├── mcp.getEcsStatus          1200ms
├── bedrock.chat              2800ms
└── response                   100ms
```

所以：

```text
Trace = 整条链
Span = 链上的一个步骤
```

---

# 5. 你的 Agent Trace

推荐最终：

```text
agent.run
│
├── workflow.classify
│
├── memory.retrieve
│
├── rag.retrieve
│   ├── query.rewrite
│   ├── vector.search
│   ├── keyword.search
│   ├── rrf
│   └── rerank
│
├── mcp.tool
│
├── llm.call
│
└── answer.generate
```

这就是 Day 36 最核心的架构。

---

# 6. Correlation ID

除了 Trace ID，再建立：

```text
requestId
conversationId
workflowId
userId
tenantId
```

例如：

```json
{
  "traceId": "abc123",
  "requestId": "req456",
  "conversationId": "conv789",
  "workflowId": "wf001"
}
```

这样可以从：

```text
用户请求
```

一直追踪到：

```text
Bedrock
```

---

# 7. Enterprise Trace Context

推荐：

```text
User
 ↓
API Gateway / ALB
 ↓
Spring Boot
 ↓
Agent Workflow
 ↓
MCP
 ↓
RAG
 ↓
Bedrock
```

Trace Context 必须贯穿整个链路。

---

# 8. OpenTelemetry

今天使用：

```text
OpenTelemetry
```

核心：

```text
Traces
Metrics
Logs
```

架构：

```text
Spring Boot
    │
    ▼
OpenTelemetry
    │
    ▼
OTel Collector
    │
    ▼
CloudWatch / AWS Observability
```

具体 exporter 和 AWS 组件可以根据你的部署方式选择。

---

# 9. 为什么不用自己写所有日志

错误：

```java
System.out.println(
    "Agent started"
);
```

生产环境需要：

```text
Structured Logging
+
Trace ID
+
Span ID
```

例如：

```json
{
  "level": "INFO",
  "service": "ai-api",
  "event": "agent.run.started",
  "traceId": "abc123",
  "workflowId": "wf001"
}
```

---

# 10. Structured Logging

推荐 JSON：

```json
{
  "timestamp": "...",
  "level": "INFO",
  "service": "ai-api",
  "event": "mcp.tool.completed",
  "tool": "getEcsServiceStatus",
  "latencyMs": 1200,
  "status": "SUCCESS",
  "traceId": "abc123"
}
```

而不是：

```text
MCP tool completed successfully after 1200ms
```

---

# 11. Agent Run

今天增加：

```text
agent_runs
```

数据库：

```sql
CREATE TABLE agent_runs (
    id UUID PRIMARY KEY,
    tenant_id UUID NOT NULL,
    user_id UUID NOT NULL,
    conversation_id UUID,
    workflow_id UUID,
    status VARCHAR(30),
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    latency_ms BIGINT
);
```

---

# 12. Agent Run 状态

```text
RUNNING
COMPLETED
FAILED
CANCELLED
WAITING_FOR_APPROVAL
```

与 Day 34 Workflow Status 对齐。

---

# 13. LLM Call Tracking

每次调用 Bedrock：

```text
llm_calls
```

记录：

```text
model
provider
input_tokens
output_tokens
latency_ms
status
error
```

例如：

```json
{
  "model": "your-bedrock-model",
  "inputTokens": 4200,
  "outputTokens": 700,
  "latencyMs": 2100
}
```

---

# 14. 为什么必须记录 Token

因为：

```text
LLM Cost
≈
Input Tokens
+
Output Tokens
```

如果 Agent 一天：

```text
10,000 requests
```

而每次：

```text
10,000 input tokens
```

成本可能快速增长。

所以必须知道：

```text
哪个 Workflow
哪个 Agent
哪个 Tool
哪个用户
```

产生了多少 Token。

---

# 15. Token Budget

Day 35 Memory：

```text
Memory Top 5
```

Day 36：

```text
Memory Token Budget
```

例如：

```text
Memory        2,000
RAG           5,000
Tool Result   2,000
Conversation  2,000
System        1,000
```

总：

```text
12,000 tokens
```

不要无限增加 Context。

---

# 16. Cost Tracking

建立：

```text
llm_cost
```

概念：

```java
BigDecimal cost =
    inputTokens
        .multiply(inputPrice)
        .add(
            outputTokens
                .multiply(outputPrice)
        );
```

实际价格应从你使用的模型当前官方定价配置中读取，不要硬编码过时价格。

---

# 17. Cost Attribution

最终可以知道：

```text
Tenant A
$42.30

Tenant B
$17.20
```

甚至：

```text
Project A
$15.20

Project B
$44.30
```

以及：

```text
RAG
$8

MCP
$3

LLM
$48
```

---

# 18. RAG Observability

Day 32 的 RAG 现在必须记录：

```text
query
rewrittenQuery
candidateCount
rerankedCount
finalContextCount
latency
```

例如：

```json
{
  "event": "rag.completed",
  "candidateCount": 30,
  "rerankedCount": 10,
  "contextCount": 5,
  "latencyMs": 840
}
```

---

# 19. RAG Debugging

用户：

> ECS 网络配置是什么？

结果错误。

Trace：

```text
Query Rewrite
    ↓
"container networking"
```

然后：

```text
Vector Search
    ↓
20 documents
```

Reranker：

```text
Top 5
```

发现：

```text
3 documents = EKS
```

那么问题可能是：

```text
Query Rewrite
或
Metadata Filter
或
Reranker
```

不是 LLM。

---

# 20. Retrieval Evaluation

今天开始记录：

```text
retrieval_precision
retrieval_recall
MRR
NDCG
```

例如：

```json
{
  "retrievalPrecision": 0.91,
  "retrievalRecall": 0.94,
  "mrr": 0.89
}
```

---

# 21. Answer Evaluation

RAG 找到了正确文档：

```text
Precision = 0.95
```

但是 LLM 答错了。

所以还需要：

```text
Answer Evaluation
```

指标：

```text
Correctness
Relevance
Groundedness
Completeness
```

---

# 22. Groundedness

用户问：

> What is our ECS architecture?

Context：

```text
ECS Fargate
RDS PostgreSQL
SQS
```

LLM：

> We use ECS Fargate, RDS PostgreSQL, SQS and Kafka.

如果 Context 没有 Kafka：

```text
Groundedness = FAIL
```

这就是：

> **Hallucination Detection**

---

# 23. Evaluation Pipeline

```text
Question
   │
   ▼
Agent
   │
   ▼
Answer
   │
   ▼
Evaluator
   │
   ├── Correctness
   ├── Relevance
   ├── Groundedness
   └── Completeness
```

---

# 24. LLM-as-a-Judge

可以让另一个 LLM 评价：

```text
Question
Reference
Answer
Context
```

输出：

```json
{
  "correctness": 0.9,
  "groundedness": 0.95,
  "relevance": 0.92
}
```

注意：

**LLM Judge 不是绝对真理。**

最好结合：

```text
Human Evaluation
+
Automated Evaluation
```

---

# 25. Evaluation Dataset

Day 31 已经有：

```text
100 questions
```

今天升级：

```text
evaluation/
├── questions.json
├── expected_answers.json
├── expected_documents.json
└── evaluation_results.json
```

每次修改：

```text
RAG
Prompt
Model
Memory
Workflow
```

都重新跑。

---

# 26. Regression Test

这是企业 AI 很重要的一步。

例如：

```text
Version 1
Groundedness = 91%

Version 2
Groundedness = 95%

Version 3
Groundedness = 83%
```

Version 3：

```text
FAIL
```

不能因为：

> “新模型回答看起来更聪明”

就直接上线。

---

# 27. Agent Evaluation

今天开始评估整个 Agent：

```text
                    Agent Evaluation
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
      Retrieval          Tool              Answer
         │                 │                 │
      Recall            Accuracy         Groundedness
      Precision          Success          Relevance
      MRR                Latency          Correctness
```

---

# 28. Tool Evaluation

例如：

```text
getEcsServiceStatus()
```

测试：

```text
正确 Tool = 95%
错误 Tool = 5%
```

指标：

```text
Tool Selection Accuracy
```

---

# 29. Tool Argument Accuracy

Tool 选对了：

```text
getEcsServiceStatus
```

但是参数：

```json
{
  "service": "wrong-service"
}
```

仍然是错误。

所以：

```text
Tool Selection
+
Tool Argument
```

都要测试。

---

# 30. MCP Observability

Day 33 的 MCP：

```text
mcp.tool.call
```

记录：

```text
toolName
serverName
latency
status
inputSize
outputSize
error
```

例如：

```json
{
  "tool": "getEcsServiceStatus",
  "server": "aws-mcp",
  "latencyMs": 1220,
  "status": "SUCCESS"
}
```

---

# 31. MCP Error 分类

```text
AUTH_ERROR
VALIDATION_ERROR
TIMEOUT
RATE_LIMIT
SERVER_ERROR
BUSINESS_ERROR
```

这样 CloudWatch Alarm 可以针对不同问题。

---

# 32. Workflow Observability

Day 34：

```text
Workflow
 ↓
Node
 ↓
Node
 ↓
Node
```

现在：

```text
workflow.started

workflow.node.started
workflow.node.completed

workflow.approval.required

workflow.resumed

workflow.completed
```

---

# 33. Workflow Trace

例如：

```text
agent.run
│
├── classify                 80ms
│
├── memory.retrieve         210ms
│
├── rag.retrieve            820ms
│
├── mcp.getEcsStatus       1200ms
│
├── analyze                2100ms
│
├── approval               30s
│
├── mcp.restartEcs          900ms
│
└── verify                  700ms
```

你马上可以发现：

```text
approval = 30 sec
```

不是系统慢，而是：

> 人工审批等待。

---

# 34. Latency Breakdown

最终 Dashboard：

```text
Total Latency
= 6.2 sec

Memory
= 220 ms

RAG
= 840 ms

MCP
= 1.2 sec

LLM
= 3.1 sec

Other
= 840 ms
```

然后优化：

```text
LLM
3.1 sec
 ↓
Streaming
 ↓
Parallel calls
 ↓
Model optimization
```

---

# 35. Parallel Tool Calls

例如：

```text
ECS Status
CloudWatch
RAG
```

如果串行：

```text
1.2 sec
+
1.0 sec
+
0.8 sec
=
3.0 sec
```

并行：

```text
max(
1.2,
1.0,
0.8
)
≈ 1.2 sec
```

这是 Agent 性能优化的重点。

---

# 36. Trace Parallel Execution

```text
agent.run
│
├── ECS Tool ───────── 1.2s
│
├── CloudWatch ─────── 1.0s
│
└── RAG ─────────────── 0.8s
```

而不是：

```text
ECS
 ↓
CloudWatch
 ↓
RAG
```

---

# 37. CloudWatch Dashboard

今天建立：

```text
AI Agent Dashboard
```

至少：

```text
Requests
Errors
P95 Latency
P99 Latency
LLM Tokens
LLM Cost
RAG Latency
MCP Latency
Tool Errors
Workflow Failures
Approval Wait
```

---

# 38. P95 / P99

不要只看平均值。

例如：

```text
Average = 1.2 sec
P95     = 4.8 sec
P99     = 9.5 sec
```

说明：

> 大部分请求还可以，但长尾很严重。

企业生产系统必须关注：

```text
P95
P99
```

---

# 39. AI Dashboard

最终：

```text
┌──────────────────────────────────────┐
│       Enterprise AI Dashboard        │
├──────────────────────────────────────┤
│ Requests       12,830                │
│ Error Rate       0.8%                │
│ P95 Latency      3.2s                │
│                                      │
│ LLM Tokens       18.2M               │
│ LLM Cost         $84.30              │
│                                      │
│ RAG Precision     92%                │
│ Groundedness      95%                │
│                                      │
│ MCP Success       99.1%              │
│ Workflow Success  97.8%              │
└──────────────────────────────────────┘
```

---

# 40. Alert

例如：

```text
IF
Error Rate > 5%
```

Alarm：

```text
AI Agent Error Rate High
```

---

### RAG Alarm

```text
IF
Groundedness < 85%
```

触发：

```text
RAG Quality Degraded
```

---

### Cost Alarm

```text
IF
Daily LLM Cost > $500
```

触发：

```text
AI Cost Threshold Exceeded
```

---

# 41. 一个非常重要的原则

不要只做：

```text
Infrastructure Monitoring
```

例如：

```text
CPU
Memory
Disk
```

还必须：

```text
AI Monitoring
```

包括：

```text
Token
Cost
Groundedness
Retrieval
Tool Selection
Hallucination
```

---

# 42. Day 36 数据模型

今天可以增加：

```text
agent_runs
llm_calls
rag_runs
mcp_tool_calls
evaluations
```

关系：

```text
agent_run
   │
   ├── llm_calls
   ├── rag_runs
   ├── mcp_tool_calls
   └── evaluation
```

---

# 43. `rag_runs`

```sql
CREATE TABLE rag_runs (
    id UUID PRIMARY KEY,
    agent_run_id UUID NOT NULL,
    query TEXT,
    rewritten_query TEXT,
    candidate_count INT,
    reranked_count INT,
    context_count INT,
    latency_ms BIGINT,
    created_at TIMESTAMP
);
```

---

# 44. `mcp_tool_calls`

```sql
CREATE TABLE mcp_tool_calls (
    id UUID PRIMARY KEY,
    agent_run_id UUID NOT NULL,
    server_name VARCHAR(100),
    tool_name VARCHAR(100),
    status VARCHAR(30),
    latency_ms BIGINT,
    error_type VARCHAR(100),
    created_at TIMESTAMP
);
```

---

# 45. `evaluations`

```sql
CREATE TABLE evaluations (
    id UUID PRIMARY KEY,
    agent_run_id UUID NOT NULL,
    correctness DOUBLE PRECISION,
    relevance DOUBLE PRECISION,
    groundedness DOUBLE PRECISION,
    completeness DOUBLE PRECISION,
    judge_model VARCHAR(100),
    created_at TIMESTAMP
);
```

---

# 46. Java Observability Service

建立：

```java
public interface AgentObservabilityService {

    void agentStarted(AgentState state);

    void agentCompleted(
        AgentState state
    );

    void recordLlmCall(
        LlmCallMetrics metrics
    );

    void recordRagRun(
        RagMetrics metrics
    );

    void recordMcpTool(
        McpToolMetrics metrics
    );
}
```

---

# 47. 不要让业务代码到处写 Metrics

错误：

```java
ragService.search();

cloudWatch.putMetric(...);

logger.info(...);

trace.add(...);
```

散落在所有代码里。

应该：

```text
Business Service
       │
       ▼
Observability Layer
       │
 ┌─────┼─────┐
 ▼     ▼     ▼
Trace Metrics Logs
```

---

# 48. Trace Naming

建议统一：

```text
agent.run
agent.workflow
agent.node
memory.retrieve
rag.retrieve
rag.rerank
mcp.tool
llm.call
evaluation.run
```

这样 Dashboard 很容易聚合。

---

# 49. PII 和日志

千万不要：

```text
logger.info(
    "user prompt = " + prompt
);
```

因为 Prompt 可能包含：

```text
PII
Secrets
Internal Information
```

应该：

```text
promptHash
inputTokenCount
classification
```

必要时做：

```text
redaction
```

---

# 50. Prompt Logging

开发环境：

```text
DEBUG
```

可以有限记录。

生产环境：

```text
INFO
```

建议：

```text
不直接记录完整 Prompt
```

除非：

```text
经过脱敏
+
明确的数据治理策略
```

---

# 51. Day 36 实战项目

今天做：

## Agent Observability Platform

输入：

```text
Why is production order-api slow?
```

系统执行：

```text
Workflow
 ↓
Memory
 ↓
RAG
 ↓
MCP
 ↓
Bedrock
 ↓
Answer
```

然后 Dashboard 显示完整 Trace。

---

# 52. Trace Example

```text
Trace: abc123

agent.run                       5.8s
│
├── memory.retrieve             220ms
│
├── intent.classify              80ms
│
├── rag.retrieve                840ms
│   ├── query.rewrite           180ms
│   ├── vector.search            90ms
│   ├── keyword.search           70ms
│   └── rerank                  320ms
│
├── mcp.getEcsStatus           1200ms
│
├── mcp.getCloudWatchMetrics    900ms
│
├── llm.analyze                2100ms
│
└── answer.generate             460ms
```

---

# 53. 找性能瓶颈

看 Trace：

```text
LLM = 2.1s
MCP = 1.2s
RAG = 0.84s
```

第一优化目标：

```text
LLM
```

第二：

```text
MCP
```

而不是盲目优化：

```text
PostgreSQL
```

---

# 54. 找质量问题

如果：

```text
Answer incorrect
```

Trace：

```text
RAG Precision = 0.45
```

说明：

```text
Retriever 问题
```

如果：

```text
RAG Precision = 0.95
Groundedness = 0.90
Correctness = 0.70
```

更可能：

```text
Prompt
Model
Reasoning
```

---

# 55. 今天的 Evaluation Matrix

建立：

| Component | Metric              |
| --------- | ------------------- |
| Agent     | Success Rate        |
| Workflow  | Completion Rate     |
| Memory    | Recall / Precision  |
| RAG       | Recall / MRR / NDCG |
| Reranker  | NDCG                |
| MCP       | Tool Accuracy       |
| LLM       | Correctness         |
| Answer    | Groundedness        |
| System    | P95 / P99           |
| Cost      | $ / Request         |

---

# 56. Day 36 Coding Task

完成：

```text
□ OpenTelemetry
□ Trace
□ Span
□ Correlation ID
□ Structured Logging
□ Agent Run
□ Workflow Trace
□ LLM Metrics
□ Token Tracking
□ Cost Tracking
□ RAG Metrics
□ MCP Metrics
□ Evaluation
□ Groundedness
□ Tool Accuracy
□ P95/P99
□ CloudWatch Dashboard
□ CloudWatch Alarms
□ Regression Evaluation
```

---

# 57. Day 36 项目结构

```text
backend/
│
├── observability/
│   ├── AgentObservabilityService.java
│   ├── TraceService.java
│   ├── MetricsService.java
│   ├── CostTrackingService.java
│   └── AuditLogService.java
│
├── evaluation/
│   ├── EvaluationService.java
│   ├── RagEvaluator.java
│   ├── AnswerEvaluator.java
│   ├── ToolEvaluator.java
│   └── EvaluationResult.java
│
├── agent/
├── workflow/
├── memory/
├── rag/
├── mcp/
└── security/
```

---

# 58. Day 36 最终架构

```text
                              React
                                │
                               SSE
                                │
                                ▼
                         Spring Boot API
                                │
                                ▼
                         Agent Workflow
                                │
                 ┌──────────────┼──────────────┐
                 ▼              ▼              ▼
              Memory           RAG            MCP
                 │              │              │
                 └──────────────┼──────────────┘
                                ▼
                              LLM
                                │
                                ▼
                              Answer
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
             OpenTelemetry              Evaluation
                    │                       │
          ┌─────────┼─────────┐             │
          ▼         ▼         ▼             ▼
       Trace      Metrics     Logs       Quality
          │         │         │             │
          └─────────┼─────────┘             │
                    ▼                       ▼
                CloudWatch             AI Metrics
                    │                       │
                    └───────────┬───────────┘
                                ▼
                         AI Dashboard
```

---

# 59. Day 31 → Day 36

现在你的系统已经形成：

```text
Day 31
Advanced RAG
        ↓
Day 32
Enterprise Retrieval
        ↓
Day 33
MCP
        ↓
Day 34
Agent Workflow
        ↓
Day 35
Agent Memory
        ↓
Day 36
Observability + Evaluation
```

也就是说，现在已经不再是简单的：

```text
Java + LLM
```

而是：

```text
Java Enterprise AI Platform
```

具备：

```text
RAG
MCP
Agent
Workflow
Memory
Security
Observability
Evaluation
```

---

## Day 36 最重要的三个原则

### ① 每一次 Agent Run 都必须可追踪

```text
User
 → Workflow
 → Memory
 → RAG
 → MCP
 → LLM
 → Answer
```

### ② 每一次质量下降都必须能定位

```text
Retrieval?
Reranker?
Memory?
Tool?
Prompt?
Model?
```

### ③ 每一次上线都必须可量化

```text
Quality ↑
Latency ↓
Cost ↓
Error ↓
```

**Day 37** 下一步建议进入 **Agent Security：Prompt Injection、Indirect Prompt Injection、Tool Poisoning、MCP Security、RAG Data Leakage、Tenant Isolation、PII/Secret Detection、Guardrails 和 AWS IAM**。这一课会把目前的 Agent 从“能运行”提升到“可以安全进入企业生产环境”。
