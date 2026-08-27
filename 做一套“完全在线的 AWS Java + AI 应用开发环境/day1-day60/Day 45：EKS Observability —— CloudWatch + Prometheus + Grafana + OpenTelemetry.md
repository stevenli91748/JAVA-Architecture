# Day 45：EKS Observability —— CloudWatch + Prometheus + Grafana + OpenTelemetry

Day 44 已经完成安全基础：

```text
Pod
 ↓
EKS Pod Identity
 ↓
IAM
 ↓
Bedrock / S3 / Secrets
```

今天解决生产环境另一个核心问题：

> **AI 应用出了问题，怎么知道到底是哪一层出了问题？**

最终实现：

```text
User
 ↓
ALB
 ↓
Spring Boot
 ↓
Agent
 ↓
RAG
 ↓
MCP
 ↓
Bedrock
 ↓
PostgreSQL / Redis
```

整条链路都可以看到。

---

# 1. Day 45 最终目标

今天建立：

```text
Metrics
Logs
Traces
AI Metrics
Alerts
Dashboards
```

最终架构：

```text
                         User
                           │
                           ▼
                          ALB
                           │
                           ▼
                     Spring Boot
                           │
                  ┌────────┼────────┐
                  ▼        ▼        ▼
                Agent     RAG      MCP
                  │        │        │
                  └────────┼────────┘
                           ▼
                        Bedrock
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                PostgreSQL       Redis

                           │
                           ▼
                    OpenTelemetry
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          CloudWatch   Prometheus     Traces
                           │
                           ▼
                         Grafana
```

---

# 2. 三个最重要的 Observability

必须区分：

```text
Logs
Metrics
Traces
```

---

# 3. Logs

回答：

> **发生了什么？**

例如：

```text
ERROR
RAG retrieval failed
```

Spring Boot：

```text
2026-08-20 10:21:33
ERROR
RAG retrieval failed
```

---

# 4. Metrics

回答：

> **系统现在怎么样？**

例如：

```text
CPU = 72%
Memory = 61%
Request Rate = 120/sec
Error Rate = 1.2%
P95 Latency = 850ms
```

---

# 5. Traces

回答：

> **这一次请求到底慢在哪里？**

例如：

```text
Request
 │
 ├── Spring Boot       20ms
 │
 ├── RAG Retrieval    120ms
 │
 ├── MCP Tool          80ms
 │
 ├── Bedrock         1800ms
 │
 └── PostgreSQL        30ms
```

马上可以看到：

```text
Bedrock
=
主要瓶颈
```

---

# 6. 为什么 AI 应用特别需要 Tracing？

普通 Java：

```text
HTTP
 ↓
Service
 ↓
Database
```

AI：

```text
HTTP
 ↓
Agent
 ↓
Planner
 ↓
RAG
 ↓
Vector DB
 ↓
MCP
 ↓
Tool
 ↓
LLM
 ↓
Memory
 ↓
LLM
```

如果没有 Trace：

> 很难知道到底哪里慢。

---

# 7. OpenTelemetry

今天的核心技术：

# OpenTelemetry

简称：

```text
OTel
```

它负责：

```text
Application
 ↓
Telemetry
 ↓
Metrics
Logs
Traces
```

---

# 8. OpenTelemetry Architecture

```text
Spring Boot
     │
     ▼
OpenTelemetry SDK
     │
     ▼
OTel Collector
     │
 ┌───┼────────┐
 ▼   ▼        ▼
Logs Metrics Traces
 │    │        │
 ▼    ▼        ▼
AWS   Prometheus X-Ray
CloudWatch      / Grafana
```

---

# 9. CloudWatch

AWS 原生：

```text
CloudWatch
```

适合：

```text
AWS Metrics
AWS Logs
AWS Alarms
AWS Dashboards
```

例如：

```text
ALB
ECS
EKS
RDS
SQS
Lambda
```

---

# 10. Prometheus

Prometheus 更偏：

```text
Kubernetes
Application
Infrastructure
```

例如：

```text
CPU
Memory
Pod Count
HTTP Requests
Latency
JVM
```

---

# 11. Grafana

Grafana：

> Dashboard / Visualization。

例如：

```text
┌───────────────────────────────┐
│ AI Platform Production        │
├───────────────────────────────┤
│ Requests       1,230 / sec    │
│ Error Rate        0.8%        │
│ P95 Latency      820ms        │
│ CPU               63%         │
│ Memory            71%         │
│ Pods                8         │
└───────────────────────────────┘
```

---

# 12. AWS 推荐方案

你的 AWS 平台可以采用：

```text
EKS
 │
 ├── CloudWatch
 │
 ├── Amazon Managed Service for Prometheus
 │
 ├── Amazon Managed Grafana
 │
 └── AWS X-Ray / OpenTelemetry
```

这样减少自己维护 Prometheus/Grafana 的负担。

---

# 13. Day 45 先理解四个组件

```text
CloudWatch
=
AWS Observability

Prometheus
=
Metrics

Grafana
=
Dashboard

OpenTelemetry
=
Instrumentation / Telemetry
```

---

# 14. EKS Logs

你的 Pod：

```text
ai-api
```

产生：

```text
stdout
stderr
```

例如：

```text
INFO Request started
INFO RAG retrieval
INFO Bedrock call
INFO Request completed
```

这些可以进入：

```text
CloudWatch Logs
```

---

# 15. 不要只写普通日志

错误：

```java
System.out.println(
    "something happened"
);
```

生产应该：

```java
log.info(
    "RAG retrieval completed"
);
```

更好：

```java
log.info(
    "RAG retrieval completed tenant={} documents={} latencyMs={}",
    tenantId,
    documents,
    latency
);
```

但注意：

> **不要把密码、Token、完整用户敏感内容、Authorization Header 写入日志。**

---

# 16. Structured Logging

推荐 JSON：

```json
{
  "timestamp": "2026-08-20T10:20:00Z",
  "level": "INFO",
  "service": "ai-api",
  "traceId": "abc123",
  "operation": "rag.retrieve",
  "latencyMs": 120
}
```

这样 CloudWatch 更容易查询。

---

# 17. Trace ID

非常重要。

例如：

```text
traceId = abc123
```

整个请求：

```text
ALB
 ↓
Spring Boot
 ↓
RAG
 ↓
MCP
 ↓
Bedrock
```

都带：

```text
abc123
```

发生问题时：

```text
CloudWatch
 ↓
traceId=abc123
```

可以找到整条请求。

---

# 18. Trace vs Span

OpenTelemetry：

```text
Trace
└── Span
    ├── Span
    ├── Span
    └── Span
```

例如：

```text
Trace
│
├── HTTP Request
│
├── RAG Retrieval
│
├── MCP Tool
│
└── Bedrock
```

---

# 19. AI Trace

建议每次 AI Request：

```text
Trace
│
├── HTTP
│
├── Agent
│
├── Retrieval
│   ├── Embedding
│   └── Vector Search
│
├── MCP
│   └── Tool
│
├── LLM
│
└── Response
```

---

# 20. Token Metrics

AI 应用不能只看：

```text
CPU
Memory
```

还要：

```text
Input Tokens
Output Tokens
Total Tokens
```

例如：

```text
Request
Input = 3,200
Output = 800
Total = 4,000
```

---

# 21. AI Cost Metrics

进一步：

```text
Model
Input Tokens
Output Tokens
Cost
Latency
```

例如：

```text
Claude
4,000 tokens
$0.02

Model B
4,000 tokens
$0.08
```

这样你可以知道：

> 哪个 Agent / Model 最烧钱。

---

# 22. RAG Metrics

至少监控：

```text
Retrieval Latency
Top-K
Documents Retrieved
Similarity Score
No-Result Rate
Reranking Latency
```

例如：

```text
RAG P95 = 180ms
No Result = 3.2%
```

---

# 23. Agent Metrics

Agent：

```text
Requests
 ↓
Planner
 ↓
Tool
 ↓
LLM
```

监控：

```text
Agent Execution Time
Tool Calls
Steps / Run
Success Rate
Failure Rate
```

特别是：

```text
Average Steps
```

如果：

```text
2 steps → 8 steps
```

成本可能突然增加。

---

# 24. MCP Metrics

MCP：

```text
Tool Call Count
Tool Latency
Tool Error Rate
Authorization Denied
```

例如：

```text
search_customer
1,200 calls
P95 120ms
Error 0.5%
```

---

# 25. JVM Metrics

Java 应用还要看：

```text
Heap
Non-Heap
GC
Threads
CPU
Class Loading
```

Spring Boot Actuator 可以提供大量 JVM 指标。

---

# 26. Micrometer

Spring Boot 通常使用：

```text
Micrometer
```

作为 Metrics abstraction。

架构：

```text
Spring Boot
 ↓
Micrometer
 ↓
Prometheus
```

---

# 27. Spring Boot Actuator

加入：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>
        spring-boot-starter-actuator
    </artifactId>
</dependency>
```

---

# 28. Actuator 配置

```yaml
management:
  endpoints:
    web:
      exposure:
        include:
          - health
          - metrics
          - prometheus
```

生产不要无脑暴露所有 Actuator endpoint 到公网。

---

# 29. Prometheus Endpoint

例如：

```text
/actuator/prometheus
```

返回：

```text
http_server_requests_seconds_count
jvm_memory_used_bytes
process_cpu_usage
```

Prometheus 可以抓取。

---

# 30. Kubernetes Metrics

Prometheus：

```text
EKS
 │
 ├── Node
 ├── Pod
 ├── Container
 └── Application
```

然后：

```text
Prometheus
 ↓
Grafana
```

---

# 31. Grafana Dashboard

建立：

```text
AI Platform Dashboard
```

第一版放：

```text
Requests/sec
Error Rate
P50
P95
P99
CPU
Memory
Pods
```

第二版：

```text
LLM Latency
Token Usage
LLM Cost
RAG Latency
MCP Latency
```

---

# 32. Golden Signals

生产系统必须知道：

```text
Latency
Traffic
Errors
Saturation
```

也叫：

> Four Golden Signals

---

# 33. Latency

例如：

```text
P50 = 300ms
P95 = 900ms
P99 = 2.1s
```

不要只看平均值：

```text
Average = 400ms
```

因为平均值可能隐藏尾延迟。

---

# 34. Traffic

例如：

```text
Requests/sec
```

生产：

```text
100/sec
```

突然：

```text
1,000/sec
```

需要自动扩容。

---

# 35. Errors

例如：

```text
HTTP 5xx
```

：

```text
0.2%
```

突然：

```text
8%
```

应该：

```text
Alert
```

---

# 36. Saturation

例如：

```text
CPU 95%
Memory 90%
DB Connections 95%
SQS Queue 10,000
```

说明系统接近极限。

---

# 37. Alert

例如：

```text
P95 > 2 seconds
```

持续：

```text
5 minutes
```

触发：

```text
Alert
```

---

# 38. 不要 Alert 太多

错误：

```text
CPU > 70%
Alert
```

结果：

```text
每天 100 个 Alert
```

最后：

> 工程师不看 Alert。

应该：

```text
CPU > 90%
AND
持续 10 min
```

或者：

```text
Error Rate > 5%
```

---

# 39. AI Alert

AI 平台可以增加：

```text
LLM Error Rate > 3%
RAG Failure > 5%
MCP Failure > 2%
Token Cost > Budget
P95 Latency > 3 sec
```

---

# 40. Bedrock Observability

你的 Java：

```text
Spring AI
 ↓
AWS SDK
 ↓
Bedrock
```

应该记录：

```text
Model
Latency
Input Tokens
Output Tokens
Error
```

注意：

> 默认不要把完整 Prompt / Response 直接写进生产日志。

---

# 41. Prompt Logging

可以记录：

```text
promptHash
```

而不是：

```text
完整 Prompt
```

例如：

```text
promptHash = 8f9a21...
```

用于：

```text
Debug
Version Tracking
```

同时降低敏感数据泄露风险。

---

# 42. AI Evaluation + Observability

Day 36：

```text
AI Evaluation
```

今天：

```text
Production Observability
```

两者结合：

```text
Offline Evaluation
+
Online Monitoring
```

---

# 43. Offline Evaluation

部署前：

```text
100 Test Questions
 ↓
RAG
 ↓
Evaluation
 ↓
Score
```

例如：

```text
Correctness = 92%
```

---

# 44. Online Evaluation

Production：

```text
Real Requests
 ↓
Sample
 ↓
Evaluation
```

例如：

```text
Groundedness
Relevance
Hallucination
```

但注意成本和隐私。

---

# 45. SLO

今天开始学习：

> Service Level Objective

例如：

```text
API Availability >= 99.9%
```

Latency：

```text
P95 < 2 seconds
```

AI：

```text
Tool Success > 99%
```

---

# 46. SLI

SLI：

> 实际测量指标。

例如：

```text
Successful Requests
/
Total Requests
```

得到：

```text
99.95%
```

---

# 47. SLA / SLO / SLI

必须会：

```text
SLI
=
Measure

SLO
=
Target

SLA
=
Customer Contract
```

例如：

```text
SLI = 99.95%

SLO = 99.9%

SLA = 99.9%
```

---

# 48. Error Budget

如果：

```text
SLO = 99.9%
```

意味着允许：

```text
0.1%
```

错误预算。

一个月大约：

```text
43.2 minutes
```

这就是：

> Error Budget

---

# 49. 为什么 AI 应用需要 Error Budget？

因为你需要在：

```text
Reliability
```

和：

```text
Feature Velocity
```

之间平衡。

例如：

```text
Agent 新功能
```

如果让：

```text
Error Rate
0.2% → 5%
```

应该停止发布。

---

# 50. Day 45 AWS Observability

推荐理解为：

```text
                 AWS
                  │
      ┌───────────┼────────────┐
      ▼           ▼            ▼
 CloudWatch   Prometheus     X-Ray
      │           │            │
      │           ▼            │
      │         Grafana        │
      │                        │
      └──────── OpenTelemetry ─┘
```

---

# 51. OpenTelemetry Collector

生产：

```text
Applications
     │
     ▼
OTel Collector
     │
 ┌───┼─────┐
 ▼   ▼     ▼
Logs Metrics Traces
```

Collector 可以：

```text
Receive
Process
Filter
Export
```

---

# 52. 为什么需要 Collector？

不要：

```text
Java
 ↓
直接发送到 5 个系统
```

而是：

```text
Java
 ↓
OTel Collector
 ├── CloudWatch
 ├── Prometheus
 └── Tracing Backend
```

以后更换 Observability Backend 更容易。

---

# 53. Trace 示例

一次请求：

```text
traceId=abc123
```

完整：

```text
HTTP POST /chat
   │
   ├── agent.run              2.5s
   │
   ├── rag.retrieve           150ms
   │
   ├── mcp.customer.search    120ms
   │
   ├── bedrock.invoke        1.8s
   │
   └── postgres.query          40ms
```

你马上看到：

```text
Bedrock
=
72%
```

---

# 54. 发现数据库问题

例如：

```text
Trace
│
├── Agent       2s
├── RAG         8s  ←
├── Bedrock     1s
└── DB          7.5s ←
```

继续：

```text
PostgreSQL
 ↓
Slow Query
 ↓
Missing Index
```

这就是 Distributed Tracing 的价值。

---

# 55. Day 45 实战：Spring Boot

加入 Actuator：

```xml
<dependency>
    <groupId>
        org.springframework.boot
    </groupId>
    <artifactId>
        spring-boot-starter-actuator
    </artifactId>
</dependency>
```

然后：

```yaml
management:
  endpoints:
    web:
      exposure:
        include:
          - health
          - metrics
          - prometheus
```

---

# 56. 自定义 AI Metrics

例如：

```java
Counter ragRequests;
Timer ragLatency;
Counter mcpFailures;
```

Micrometer：

```java
Counter.builder("ai.rag.requests")
    .register(meterRegistry)
    .increment();
```

---

# 57. RAG Latency

```java
Timer.Sample sample =
    Timer.start(meterRegistry);

try {

    return ragService.retrieve(query);

} finally {

    sample.stop(
        Timer.builder("ai.rag.latency")
            .register(meterRegistry)
    );
}
```

得到：

```text
ai.rag.latency
```

---

# 58. MCP Metrics

例如：

```text
mcp.tool.calls
mcp.tool.errors
mcp.tool.latency
```

Dashboard：

```text
MCP Tool Calls
1,200/min

Error Rate
0.7%

P95
120ms
```

---

# 59. AI Cost Metric

可以定义：

```text
ai.llm.input_tokens
ai.llm.output_tokens
ai.llm.cost
```

例如：

```text
Model = Claude
Input = 10,000
Output = 2,000
Cost = $0.04
```

注意实际计费应根据当时使用的模型和 AWS 定价计算，不要把示例价格当成当前价格。

---

# 60. Dashboard 第一版

建议建立：

```text
┌────────────────────────────────────┐
│       AI PLATFORM PRODUCTION       │
├────────────────────────────────────┤
│ Traffic          820 req/s         │
│ Error Rate       0.7%              │
│ P95              820 ms            │
│ P99              1.8 s             │
├────────────────────────────────────┤
│ Pods              8                 │
│ CPU               63%              │
│ Memory            71%              │
├────────────────────────────────────┤
│ LLM Latency       1.4 s             │
│ RAG Latency       180 ms            │
│ MCP Latency       110 ms            │
│ LLM Tokens        2.4M              │
└────────────────────────────────────┘
```

---

# 61. 第二个 Dashboard：AI

```text
┌────────────────────────────────────┐
│             AI METRICS             │
├────────────────────────────────────┤
│ LLM Requests                       │
│ Token Usage                        │
│ Cost                               │
│ LLM Error Rate                     │
│ RAG Retrieval Rate                 │
│ RAG No Result                      │
│ MCP Calls                          │
│ MCP Failures                       │
│ Agent Steps                        │
│ Agent Failure                      │
└────────────────────────────────────┘
```

---

# 62. 第三个 Dashboard：Infrastructure

```text
EKS Nodes
Pods
CPU
Memory
Network
Disk
RDS Connections
RDS CPU
Redis
SQS Queue
ALB
```

---

# 63. Alert 示例

### API

```text
5xx > 5%
```

### Latency

```text
P95 > 2s
```

### RDS

```text
Connections > 80%
```

### SQS

```text
Queue > 10,000
```

### AI

```text
LLM Error > 3%
```

---

# 64. Kubernetes HPA + Metrics

Day 42：

```text
HPA
```

今天：

```text
Metrics
 ↓
HPA
```

例如：

```text
CPU 80%
 ↓
HPA
 ↓
Pods 2 → 4
```

以后：

```text
Custom Metrics
 ↓
HPA
```

例如：

```text
Requests/sec
```

---

# 65. Worker + Queue

你的 RAG ingestion：

```text
S3
 ↓
SQS
 ↓
Worker
```

监控：

```text
Queue Depth
```

例如：

```text
0
 ↓
100
 ↓
1,000
 ↓
10,000
```

如果 Queue 持续上涨：

```text
Worker 不够
```

---

# 66. KEDA

后面可以：

```text
SQS Queue
 ↓
KEDA
 ↓
Worker Pods
```

例如：

```text
Queue = 0
 ↓
2 Pods
```

```text
Queue = 10,000
 ↓
20 Pods
```

这是 AI/RAG Worker 非常实用的自动扩展方案。

---

# 67. Day 45 生产故障排查流程

以后收到：

> API 很慢。

第一步：

```text
ALB
```

检查：

```text
Request Count
Target Response Time
5xx
```

---

第二步：

```text
EKS
```

检查：

```text
Pod
CPU
Memory
Restart
```

---

第三步：

```text
Trace
```

检查：

```text
Agent
RAG
MCP
LLM
DB
```

---

第四步：

```text
Database
```

检查：

```text
CPU
Connections
Slow Queries
```

---

# 68. AI 慢请求例子

Trace：

```text
Request = 8s

HTTP        20ms
Agent       30ms
RAG        100ms
MCP        200ms
Bedrock   7,000ms
DB          50ms
```

结论：

```text
Bedrock latency
```

而不是：

```text
Java
```

---

# 69. Day 45 项目目录

```text
aws-java-ai-platform/
│
├── backend/
│   └── src/
│
├── helm/
│   └── ai-platform/
│
├── observability/
│   ├── otel/
│   ├── prometheus/
│   ├── grafana/
│   └── dashboards/
│
└── .github/
    └── workflows/
```

---

# 70. Day 45 CI/CD

今天把 Observability 也加入 Pipeline：

```text
GitHub
 ↓
Build
 ↓
Test
 ↓
Security
 ↓
AI Evaluation
 ↓
Docker
 ↓
ECR
 ↓
Helm
 ↓
EKS
 ↓
Smoke Test
 ↓
Observability Check
```

---

# 71. Deployment 后自动检查

部署：

```text
New Version
 ↓
EKS
```

等待：

```text
5 minutes
```

检查：

```text
Error Rate
Latency
Restart
Health
```

如果：

```text
Error Rate > Threshold
```

：

```text
Rollback
```

---

# 72. Day 45 核心架构

```text
                       USER
                         │
                         ▼
                       ALB
                         │
                         ▼
                   Spring Boot
                         │
                  ┌──────┼──────┐
                  ▼      ▼      ▼
                Agent   RAG    MCP
                  │      │      │
                  └──────┼──────┘
                         ▼
                      Bedrock
                         │
                  ┌──────┴──────┐
                  ▼             ▼
               PostgreSQL      Redis

                         │
                         ▼
                  OpenTelemetry
                         │
                ┌────────┼─────────┐
                ▼        ▼         ▼
            Metrics     Logs      Traces
                │        │         │
                ▼        ▼         ▼
           Prometheus CloudWatch  X-Ray
                │
                ▼
             Grafana
                │
                ▼
              Alerts
```

---

# 73. Day 45 必做任务

今天完成这 **10 项**：

```text
[ ] 1. Spring Boot Actuator
[ ] 2. Micrometer
[ ] 3. Prometheus Metrics
[ ] 4. CloudWatch Logs
[ ] 5. OpenTelemetry
[ ] 6. Trace ID
[ ] 7. Distributed Trace
[ ] 8. Grafana Dashboard
[ ] 9. AI Metrics
[ ] 10. Production Alerts
```

---

# 74. Day 45 必会面试题

### Q1：Metrics、Logs、Traces 有什么区别？

```text
Logs
→ 发生了什么

Metrics
→ 系统怎么样

Traces
→ 一次请求经过了什么
```

---

### Q2：为什么 AI 应用需要 Distributed Tracing？

因为：

```text
HTTP
 ↓
Agent
 ↓
RAG
 ↓
MCP
 ↓
LLM
 ↓
DB
```

调用链非常长。

---

### Q3：Prometheus 做什么？

```text
Metrics Collection
```

---

### Q4：Grafana 做什么？

```text
Visualization
Dashboard
```

---

### Q5：OpenTelemetry 做什么？

```text
Instrumentation
Telemetry Collection
Export
```

它本身不是必须等同于某个具体后端存储。

---

# 75. Day 45 最重要的生产指标

你的 AI API 至少监控：

```text
HTTP
├── RPS
├── P50
├── P95
├── P99
├── 4xx
└── 5xx

JVM
├── Heap
├── GC
└── Threads

Kubernetes
├── CPU
├── Memory
├── Pod Restarts
└── Replica Count

AI
├── LLM Latency
├── Token Usage
├── Cost
├── RAG Latency
├── RAG Failure
├── MCP Latency
├── MCP Failure
└── Agent Steps

Database
├── CPU
├── Connections
├── Latency
└── Slow Queries

Queue
└── SQS Depth
```

---

# 76. Day 40 → Day 45

现在你的系统已经形成：

```text
Day 40
CI/CD
   ↓
Day 41
Kubernetes
   ↓
Day 42
Helm
   ↓
Day 43
Networking
   ↓
Day 44
Security
   ↓
Day 45
Observability
```

也就是说已经具备：

```text
Deployment
+
Networking
+
Security
+
Monitoring
```

这已经是一个真正的企业生产平台骨架。

---

## Day 45 → Day 46

下一步建议进入：

# **Day 46：EKS Auto Scaling + HPA + KEDA + SQS**

重点解决 AI 应用的弹性：

```text
正常流量
   ↓
2 Pods

高峰
   ↓
10 Pods

SQS 堆积
   ↓
Worker 自动扩容

流量下降
   ↓
自动缩容
```

并最终把你的 **Spring Boot AI API + RAG Worker + SQS + EKS** 做成真正的自动扩缩容架构。
