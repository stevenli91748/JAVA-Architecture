# Day 58：Enterprise AI Observability + AIOps

Day 57 已经完成：

```text
API
 ↓
SQS
 ↓
AI Worker
 ↓
Multi-Agent
 ↓
AI Gateway
 ↓
LLM
 ↓
EventBridge
```

今天解决生产环境最关键的三个问题：

> **哪里慢？哪里错？哪里烧钱？**

今天重点：

```text
OpenTelemetry
Prometheus
Grafana
CloudWatch
Distributed Tracing
Logs
Metrics
Traces
LLM Observability
AI Cost
Alerting
AIOps
```

---

# 1. Day 58 最终架构

```text
                         USER
                           │
                           ▼
                       CloudFront
                           │
                           ▼
                          WAF
                           │
                           ▼
                     API Gateway
                           │
                           ▼
                      Spring Boot
                           │
                           ▼
                     OpenTelemetry
                           │
                ┌──────────┼──────────┐
                ▼          ▼          ▼
              Logs       Metrics     Traces
                │          │          │
                └──────────┼──────────┘
                           ▼
                    Observability
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
         CloudWatch    Prometheus      Grafana
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                         Alerts
                           │
                           ▼
                          AIOps
```

---

# 2. 为什么 AI 系统必须做 Observability？

普通 Java 应用：

```text
Request
 ↓
Service
 ↓
Database
```

AI 应用：

```text
Request
 ↓
Agent
 ↓
RAG
 ↓
Vector Search
 ↓
Tool
 ↓
LLM
 ↓
Model Gateway
 ↓
Fallback
 ↓
Another LLM
```

问题复杂很多。

---

# 3. 三大支柱

Observability 的核心：

```text
Logs
Metrics
Traces
```

简称：

> **LMT**

---

# 4. Logs

回答：

> 发生了什么？

例如：

```text
2026-08-20 11:30:20
ERROR
Agent execution failed
jobId=job-1001
agent=research
error=TIMEOUT
```

---

# 5. Metrics

回答：

> 发生了多少？

例如：

```text
AI Requests = 10,000
Errors = 120
Latency P95 = 2.3s
Tokens = 15M
Cost = $240
```

---

# 6. Traces

回答：

> 请求到底经过了哪里？

例如：

```text
Request
 │
 ├── Authentication 20ms
 │
 ├── RAG 350ms
 │    ├── Embedding 80ms
 │    └── Vector Search 100ms
 │
 ├── Agent 1.2s
 │
 └── LLM 2.1s
```

---

# 7. Trace

一个完整请求：

```text
Trace ID
    │
    ├── API Span
    │
    ├── Agent Span
    │
    ├── RAG Span
    │
    ├── Tool Span
    │
    └── LLM Span
```

这就是：

> Distributed Tracing

---

# 8. OpenTelemetry

今天最重要的技术：

```text
OpenTelemetry
```

它提供统一的：

```text
Tracing
Metrics
Logs
```

你的 Java 应用：

```text
Spring Boot
     │
     ▼
OpenTelemetry
     │
     ├── Trace
     ├── Metrics
     └── Logs
```

---

# 9. 为什么选择 OpenTelemetry？

因为你不希望：

```text
Java
 ↓
某一个监控厂商
```

把代码全部绑定死。

OpenTelemetry：

```text
Application
 ↓
OTel
 ↓
CloudWatch
Grafana
Prometheus
Other Backends
```

---

# 10. Spring Boot

加入：

```text
OpenTelemetry Java Agent
```

通常比大量手写 instrumentation 更简单。

例如启动：

```bash
java \
-javaagent:opentelemetry-javaagent.jar \
-jar app.jar
```

---

# 11. 环境变量

典型：

```bash
OTEL_SERVICE_NAME=ai-gateway

OTEL_EXPORTER_OTLP_ENDPOINT=http://otel-collector:4317

OTEL_RESOURCE_ATTRIBUTES=\
deployment.environment=production
```

---

# 12. Service Name

每个服务应该有独立：

```text
service.name
```

例如：

```text
api-service
ai-gateway
agent-worker
document-worker
rag-service
evaluation-worker
```

---

# 13. Trace ID

每个请求：

```text
traceId=abc123
```

所有服务传递：

```text
API
 ↓ abc123
AI Gateway
 ↓ abc123
Agent
 ↓ abc123
RAG
 ↓ abc123
LLM
```

这样一个 Trace 就能串起来。

---

# 14. Span

例如：

```text
Trace: abc123

Span:
├── HTTP POST /chat
├── model-routing
├── retrieval
├── embedding
├── llm-call
└── response
```

---

# 15. AI Trace

AI 应用建议增加：

```text
model
provider
promptVersion
agent
tool
tokens
latency
cost
```

例如：

```json id="7z6eqg"
{
  "traceId": "abc123",
  "agent": "research-agent",
  "provider": "bedrock",
  "model": "model-x",
  "inputTokens": 1200,
  "outputTokens": 800,
  "latencyMs": 1850
}
```

---

# 16. 不要记录完整敏感 Prompt

Day 55 已经做了安全。

因此：

```text
❌ Full Prompt
❌ SSN
❌ Password
❌ API Key
❌ Customer Secrets
```

可以记录：

```text
requestId
promptHash
promptVersion
tokenCount
metadata
```

---

# 17. Metrics 设计

今天至少建立：

```text
ai_requests_total
ai_errors_total
ai_latency_ms
ai_input_tokens_total
ai_output_tokens_total
ai_cost_total
ai_fallback_total
ai_cache_hit_total
ai_tool_calls_total
ai_security_blocks_total
```

---

# 18. Counter

例如：

```text
ai_requests_total
```

每次：

```text
Request
 ↓
+1
```

Prometheus：

```text
ai_requests_total 100230
```

---

# 19. Gauge

表示当前状态：

```text
active_ai_jobs
queue_depth
running_agents
```

例如：

```text
active_ai_jobs = 82
```

---

# 20. Histogram

非常重要。

用于：

```text
Latency
Token Size
Request Duration
```

例如：

```text
ai_request_duration_seconds
```

可以计算：

```text
P50
P90
P95
P99
```

---

# 21. 为什么不能只看 Average？

例如：

```text
Requests:

1s
1s
1s
1s
20s
```

Average：

```text
4.8s
```

但是：

```text
P95
```

可能非常高。

所以生产 AI：

> **重点看 P95 / P99。**

---

# 22. Golden Signals

生产系统常用：

```text
Latency
Traffic
Errors
Saturation
```

AI 系统再增加：

```text
Token
Cost
Model Quality
```

所以：

```text
Golden Signals
+
AI Signals
```

---

# 23. AI Golden Signals

建议 Dashboard：

```text
Requests
Errors
P95 Latency
Token Usage
Cost
Fallback Rate
Cache Hit Rate
Queue Depth
```

---

# 24. Prometheus

Kubernetes：

```text
EKS
 │
 ├── Spring Boot
 ├── Agent Worker
 └── Document Worker
        │
        ▼
   Prometheus
```

Prometheus 采集：

```text
/actuator/prometheus
```

---

# 25. Spring Boot Actuator

增加：

```text
Spring Boot Actuator
```

例如：

```text
/actuator/health
/actuator/metrics
/actuator/prometheus
```

生产环境要注意不要把管理端点无保护地暴露到公网。

---

# 26. Prometheus Metric

例如：

```text
http_server_requests_seconds
```

可以查看：

```text
API latency
```

AI 自定义：

```text
ai_requests_total
```

---

# 27. Grafana

架构：

```text
Prometheus
     │
     ▼
  Grafana
```

Dashboard：

```text
┌──────────────────────────────┐
│ AI Platform                  │
├────────┬────────┬────────────┤
│Requests│ Errors │ P95        │
│ 120K   │ 1.2%   │ 2.3 sec    │
├────────┴────────┴────────────┤
│ Token Usage                  │
│ ████████████████             │
├──────────────────────────────┤
│ AI Cost                      │
│ $1,240                       │
├──────────────────────────────┤
│ Model Errors                 │
│ OpenAI  ███                  │
│ Bedrock █                    │
└──────────────────────────────┘
```

---

# 28. CloudWatch

AWS 环境：

```text
CloudWatch
```

负责：

```text
Logs
Metrics
Alarms
Dashboards
```

例如：

```text
EKS
 ↓
CloudWatch
```

---

# 29. CloudWatch vs Prometheus

|                    | CloudWatch | Prometheus |
| ------------------ | ---------: | ---------: |
| AWS 集成             |      ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |
| Logs               |      ⭐⭐⭐⭐⭐ |         ⭐⭐ |
| AWS Services       |      ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |
| Kubernetes Metrics |       ⭐⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| PromQL             |          — |      ⭐⭐⭐⭐⭐ |
| Grafana            |       ⭐⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |

你的 AWS 项目：

> **两者可以同时使用。**

---

# 30. OpenTelemetry Collector

生产环境推荐：

```text
Application
 ↓
OpenTelemetry
 ↓
OTel Collector
 ↓
┌───────────┬───────────┐
▼           ▼           ▼
CloudWatch Prometheus Grafana
```

Collector 做：

```text
Receive
Process
Filter
Batch
Export
```

---

# 31. Collector

```yaml id="0x5w6q"
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  batch:

exporters:
  otlp:
    endpoint: backend:4317

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [otlp]
```

这是理解 OTel Collector 配置结构的最小示例。

---

# 32. AI Gateway Metrics

Day 54：

```text
AI Gateway
```

现在增加：

```text
ai_gateway_requests_total
ai_gateway_errors_total
ai_gateway_latency
ai_gateway_fallback_total
```

---

# 33. Model Metrics

每个 Provider：

```text
model_requests_total
model_errors_total
model_latency
model_tokens
model_cost
```

例如：

```text
Bedrock
 ├── Requests
 ├── Errors
 ├── Latency
 └── Cost

OpenAI
 ├── Requests
 ├── Errors
 ├── Latency
 └── Cost
```

---

# 34. Model Health

建立：

```text
Model Health Score
```

例如：

```text
Bedrock
Health = 99.8%

OpenAI
Health = 98.9%

Gemini
Health = 99.2%
```

计算因素：

```text
Availability
Latency
Error Rate
Rate Limit
```

---

# 35. Model Router + Observability

Day 54：

```text
Model Router
```

Day 58：

```text
Model Router
    │
    ├── Health
    ├── Latency
    ├── Cost
    └── Error Rate
```

于是：

> Model Router 可以根据实时健康状态动态选择模型。

---

# 36. Dynamic Routing

例如：

```text
Model A
Error = 15%
```

Model B：

```text
Error = 1%
```

Router：

```text
Model A
 ↓
UNHEALTHY
 ↓
Model B
```

这已经接近：

> **AIOps-driven Model Routing**

---

# 37. Queue Metrics

Day 57：

```text
SQS
```

今天监控：

```text
ApproximateNumberOfMessagesVisible
ApproximateNumberOfMessagesNotVisible
AgeOfOldestMessage
```

---

# 38. Queue Alert

例如：

```text
Queue Depth > 1000
```

触发：

```text
ALERT
```

因为可能：

```text
Worker 不够
Provider 限流
Database 慢
Agent 卡住
```

---

# 39. Worker Metrics

```text
worker_active_jobs
worker_completed_jobs
worker_failed_jobs
worker_retry_total
worker_processing_seconds
```

---

# 40. Agent Metrics

非常重要：

```text
agent_runs_total
agent_failures_total
agent_latency
agent_tool_calls
agent_tokens
agent_cost
```

例如：

```text
Research Agent
Runs       10,000
Failures   1.2%
P95        4.2s
Cost       $380
```

---

# 41. Tool Metrics

Day 50：

```text
Tools
```

现在：

```text
tool_calls_total
tool_failures_total
tool_latency
tool_denied_total
```

例如：

```text
customer_search
100,000 calls
0.3% errors

refund_order
5,000 calls
2.1% denied
```

---

# 42. RAG Metrics

Day 56：

```text
RAG
```

增加：

```text
retrieval_latency
retrieval_results
reranker_latency
citation_rate
no_context_rate
```

---

# 43. RAG Quality

不要只监控：

```text
Latency
```

还需要：

```text
Context Relevance
Faithfulness
Citation Correctness
Answer Relevance
```

这些来自 Day 53 Evaluation。

---

# 44. AI Quality Dashboard

```text
RAG Quality
─────────────────────────
Context Relevance     91%
Faithfulness          94%
Citation Correctness  96%
Answer Relevance      92%
```

---

# 45. AI Cost Dashboard

Day 54 已经有 Cost Tracking。

今天做 Dashboard：

```text
Total Cost
   ↓
Provider
   ↓
Model
   ↓
Tenant
   ↓
Agent
   ↓
User
```

例如：

```text
Total = $3,200

Bedrock      $1,500
OpenAI       $1,100
Gemini         $600
```

---

# 46. Cost per Agent

```text
Research Agent    $800
Data Agent        $450
Customer Agent    $200
Report Agent      $1,200
```

发现：

```text
Report Agent
```

最烧钱。

进一步：

```text
Report Agent
 ↓
Model
 ↓
Prompt
 ↓
Token
```

找到成本来源。

---

# 47. Cost Anomaly

正常：

```text
$100/day
```

突然：

```text
$800/day
```

应该自动：

```text
ALERT
```

---

# 48. Anomaly Detection

简单版本：

```text
Current Cost
>
Average × 2
```

例如：

```text
Average = $100
Current = $250
```

触发：

```text
Cost Anomaly
```

生产环境可以进一步采用 CloudWatch / 数据分析系统的异常检测能力。

---

# 49. Error Tracking

例如：

```text
AI_ERROR
```

分类：

```text
PROVIDER_ERROR
TIMEOUT
RATE_LIMIT
TOOL_ERROR
RAG_ERROR
SECURITY_BLOCK
DATABASE_ERROR
```

---

# 50. Error Taxonomy

建立：

```java id="1f5d1u"
public enum AiErrorType {

    PROVIDER_ERROR,
    RATE_LIMIT,
    TIMEOUT,
    TOOL_ERROR,
    RAG_ERROR,
    SECURITY_ERROR,
    VALIDATION_ERROR,
    DATABASE_ERROR
}
```

这样 Dashboard 可以：

```text
Error
 ↓
分类
 ↓
Root Cause
```

---

# 51. Alerting

建议至少：

```text
P95 Latency > threshold
Error Rate > threshold
Queue Depth > threshold
Provider Error > threshold
Cost Spike
Security Violation
Worker Crash
Database Connection Pool
```

---

# 52. Alert Example

```text
ALERT

AI Error Rate > 5%

Current:
8.4%

Provider:
Bedrock

Model:
Model-X

Last 10 minutes:
1,284 failures
```

这比：

```text
"AI 出错了"
```

有用得多。

---

# 53. Alert Severity

```text
INFO
WARNING
CRITICAL
```

例如：

```text
WARNING
P95 > 3s

CRITICAL
Error > 10%
```

---

# 54. SLO

企业生产系统必须开始理解：

> **SLO**

例如：

```text
AI Chat Availability
99.9%
```

意味着：

```text
允许的故障时间
```

必须根据你的实际业务和服务等级定义，不要盲目照搬 99.9%。

---

# 55. AI SLO

建议：

```text
Availability
Latency
Error Rate
Quality
Cost
```

例如：

```text
Availability ≥ 99.9%
P95 latency ≤ 5s
Error rate ≤ 1%
Citation correctness ≥ 95%
```

具体目标应该由产品和业务要求决定。

---

# 56. SLI

SLO：

```text
目标
```

SLI：

```text
实际测量
```

例如：

```text
SLI:
successful_requests / total_requests
```

---

# 57. Error Budget

如果 SLO：

```text
99.9%
```

允许：

```text
0.1%
```

错误预算。

如果已经用了：

```text
90%
```

就应该：

```text
减少高风险发布
```

---

# 58. AI Release Gate

Day 53：

```text
Evaluation
```

Day 58：

```text
Production SLO
```

部署：

```text
New Model
 ↓
Evaluation
 ↓
Security Test
 ↓
Canary
 ↓
Observability
 ↓
SLO
 ↓
Production
```

---

# 59. AIOps

现在开始进入：

> **AIOps**

传统：

```text
Metric
 ↓
Alert
 ↓
Human
 ↓
Investigate
```

AIOps：

```text
Metric
 ↓
Detection
 ↓
Correlation
 ↓
Root Cause
 ↓
Recommendation
```

---

# 60. Example

系统发现：

```text
P95 latency ↑
```

AIOps：

```text
Trace
 ↓
LLM latency ↑
 ↓
Bedrock latency ↑
 ↓
Region = us-east-1
```

结论：

```text
Likely Model Provider Latency
```

---

# 61. AI Incident Analysis

可以让 LLM 分析：

```text
Logs
+
Metrics
+
Traces
```

生成：

```text
Incident Summary
Root Cause
Impact
Recommended Action
```

但：

> **AIOps Agent 不应该未经授权自动执行高风险修复。**

---

# 62. AIOps Agent

```text
CloudWatch
Prometheus
OpenTelemetry
       │
       ▼
 Incident Collector
       │
       ▼
 AIOps Agent
       │
       ├── Analyze
       ├── Correlate
       └── Recommend
```

---

# 63. 自动修复

低风险：

```text
Restart unhealthy worker
```

可以考虑自动化。

高风险：

```text
Delete database
Change IAM
Modify production network
```

必须：

```text
Approval
```

---

# 64. Day 55 + Day 58

安全：

```text
Tool Authorization
```

现在 AIOps 也必须：

```text
AIOps Agent
 ↓
Tool Policy
 ↓
ALLOW / APPROVAL / DENY
```

不能因为是 AI Agent 就绕过 Day 55 的安全体系。

---

# 65. Day 57 + Day 58

Day 57：

```text
SQS
 ↓
Worker
```

Day 58：

```text
SQS
 ↓
Queue Metrics
 ↓
Autoscaling
 ↓
Alert
```

于是：

```text
Queue ↑
 ↓
KEDA Scale Out
 ↓
Worker ↑
 ↓
Queue ↓
```

---

# 66. Day 58 Dashboard

建议建立 5 个 Dashboard。

### Dashboard 1：Executive

```text
Requests
Users
Availability
Cost
Quality
```

### Dashboard 2：Application

```text
API
Errors
Latency
Database
Redis
```

### Dashboard 3：AI

```text
Models
Tokens
Latency
Fallback
Cost
```

### Dashboard 4：Agent

```text
Runs
Tools
Failures
Latency
Cost
```

### Dashboard 5：RAG

```text
Retrieval
Reranking
Quality
Citation
Latency
```

---

# 67. Dashboard 1

```text
┌───────────────────────────────┐
│ Enterprise AI                │
├────────┬────────┬─────────────┤
│ Users  │Requests│ Availability│
│ 12,430 │ 2.1M   │ 99.95%      │
├────────┴────────┴─────────────┤
│ AI Cost                       │
│ $18,420                       │
├───────────────────────────────┤
│ Quality                       │
│ 94.2%                         │
└───────────────────────────────┘
```

---

# 68. Dashboard 2

```text
API P95
2.1 sec

Error
0.8%

DB Connections
62%

Redis
Hit Rate 87%

SQS
2,300 messages
```

---

# 69. Dashboard 3

```text
Model            Requests   P95    Cost
────────────────────────────────────────
Bedrock           500K      2.1s   $4,200
OpenAI            300K      1.8s   $3,100
Gemini            100K      1.5s   $900
```

---

# 70. Dashboard 4

```text
Agent             Runs      Error    Cost
──────────────────────────────────────────
Research          120K      1.1%    $1,200
Data               80K      0.8%      $800
Customer          150K      0.5%      $600
Report             60K      2.2%    $1,500
```

---

# 71. Dashboard 5

```text
RAG

Retrieval P95       180ms
Reranker P95        300ms
Context Relevance   92%
Faithfulness        95%
Citation             97%
```

---

# 72. Day 58 Java 项目结构

```text
observability/
│
├── tracing/
│   ├── TraceService.java
│   └── SpanService.java
│
├── metrics/
│   ├── AiMetrics.java
│   ├── AgentMetrics.java
│   ├── RagMetrics.java
│   └── CostMetrics.java
│
├── logging/
│   ├── StructuredLogger.java
│   └── LogSanitizer.java
│
├── alert/
│   └── AlertService.java
│
├── incident/
│   ├── IncidentService.java
│   └── RootCauseAnalyzer.java
│
└── cost/
    └── CostMonitor.java
```

---

# 73. Structured Logging

不要：

```java id="e2i3w5"
log.info("AI failed " + exception);
```

建议：

```java id="2qvlz8"
log.error(
    "AI request failed",
    kv("requestId", requestId),
    kv("model", model),
    kv("provider", provider),
    kv("errorType", errorType)
);
```

这样日志系统更容易搜索和聚合。

---

# 74. Correlation ID

至少：

```text
traceId
requestId
jobId
tenantId
```

例如：

```text id="3ntb1f"
traceId = t-100
requestId = r-200
jobId = j-300
tenantId = tenant-a
```

---

# 75. 一次完整 Trace

```text id="2z6dph"
traceId=t-100

API
 │ 20ms
 ▼
AI Gateway
 │ 10ms
 ▼
Agent
 │ 100ms
 ├── RAG
 │    ├── Embedding 80ms
 │    └── Vector 120ms
 │
 ├── Tool
 │    └── CRM 300ms
 │
 └── LLM
      └── 2.1s
```

最终：

```text
Total = 2.63s
```

你马上知道：

> LLM 是主要 latency source。

---

# 76. Day 58 实战任务

建立：

> **Enterprise AI Observability Platform**

必须做到：

```text
Spring Boot
 ↓
OpenTelemetry
 ↓
Trace
 ↓
Metrics
 ↓
Logs
```

然后：

```text
EKS
 ↓
Prometheus
 ↓
Grafana
```

同时：

```text
AWS
 ↓
CloudWatch
```

---

# 77. 必做 Metrics

```text
[ ] ai_requests_total
[ ] ai_errors_total
[ ] ai_latency
[ ] ai_input_tokens
[ ] ai_output_tokens
[ ] ai_cost
[ ] ai_fallback
[ ] ai_cache_hit
[ ] agent_runs
[ ] agent_errors
[ ] tool_calls
[ ] tool_errors
[ ] rag_latency
[ ] rag_quality
[ ] queue_depth
[ ] worker_jobs
```

---

# 78. 必做 Traces

至少：

```text
[ ] API
[ ] AI Gateway
[ ] Agent
[ ] RAG
[ ] Embedding
[ ] Tool
[ ] LLM
[ ] Database
[ ] Redis
[ ] SQS
```

---

# 79. 必做 Alerts

```text
[ ] Error Rate
[ ] P95 Latency
[ ] P99 Latency
[ ] SQS Queue Depth
[ ] DLQ Messages
[ ] Provider Failure
[ ] Model Fallback
[ ] Cost Spike
[ ] Security Block
[ ] Worker Crash
```

---

# 80. Day 58 验收测试

### Test 1

发送：

```text
POST /ai/chat
```

必须能够通过 Trace 查看：

```text
API
 ↓
AI Gateway
 ↓
Model
```

---

### Test 2

RAG：

```text
Question
 ↓
Embedding
 ↓
Vector
 ↓
Reranker
 ↓
LLM
```

必须在一个 Trace 中看到。

---

### Test 3

模拟：

```text
LLM timeout
```

Dashboard：

```text
AI Error ↑
```

并且：

```text
Trace
 ↓
LLM
 ↓
TIMEOUT
```

---

### Test 4

模拟：

```text
SQS = 2,000
```

应该：

```text
Queue Depth ↑
 ↓
Alert
```

---

### Test 5

模拟：

```text
AI Cost
$100/day
 ↓
$500/day
```

触发：

```text
Cost Anomaly
```

---

# 81. Day 58 面试题

### Q1：Logs、Metrics、Traces 区别？

```text
Logs
= 发生了什么

Metrics
= 发生了多少

Traces
= 请求经过哪里
```

---

### Q2：为什么 AI 应用尤其需要 Tracing？

因为：

```text
Agent
+
RAG
+
Tools
+
LLM
```

调用链复杂。

---

### Q3：为什么 P95 比 Average 更重要？

因为 Average 会隐藏：

```text
Tail Latency
```

生产系统用户体验通常更受 P95/P99 影响。

---

### Q4：为什么不能只监控 CPU？

AI 系统可能：

```text
CPU 正常
```

但：

```text
LLM latency ↑
Token cost ↑
RAG quality ↓
```

所以必须加入 AI-specific metrics。

---

### Q5：AIOps Agent 能不能直接修改生产系统？

**不能默认允许。**

应该：

```text
AIOps
 ↓
Policy
 ↓
Low Risk → Auto
High Risk → Approval
Dangerous → Deny
```

---

# 82. Day 58 最终架构

```text id="v2y49u"
                         ENTERPRISE AI
                               │
                               ▼
                          APPLICATION
                               │
                               ▼
                        OpenTelemetry
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
              Logs           Metrics         Traces
                │              │              │
                ▼              ▼              ▼
          CloudWatch       Prometheus       OTel
                │              │              │
                └──────────────┼──────────────┘
                               ▼
                            Grafana
                               │
                ┌──────────────┼──────────────┐
                ▼              ▼              ▼
             Alerts          SLO           Cost
                │              │              │
                └──────────────┼──────────────┘
                               ▼
                             AIOps
                               │
                  ┌────────────┼────────────┐
                  ▼            ▼            ▼
                Detect       Analyze      Recommend
                                             │
                                             ▼
                                          Policy
                                      ┌──────┼──────┐
                                      ▼      ▼      ▼
                                    Allow Approval Deny
```

---

## Day 50 → Day 58

现在已经从“写 AI 应用”进入真正的 **Enterprise AI Platform Engineering**：

```text
Day 50  Agent / Tool / MCP
Day 51  Workflow / State
Day 52  Multi-Agent
Day 53  Evaluation
Day 54  AI Gateway
Day 55  Security
Day 56  AI Data Platform
Day 57  Event-Driven Architecture
Day 58  Observability + AIOps
```

**Day 59** 下一步进入 **CI/CD + DevSecOps for Java + AI on AWS**：GitHub → GitHub Actions → Docker → ECR → EKS → Terraform → Automated Test → Security Scan → AI Evaluation → Canary Deployment → Rollback，把前面 58 天的系统真正变成一条可以持续交付到 AWS Production 的企业级流水线。
