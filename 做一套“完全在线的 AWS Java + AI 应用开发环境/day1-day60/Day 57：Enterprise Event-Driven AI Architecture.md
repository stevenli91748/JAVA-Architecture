# Day 57：Enterprise Event-Driven AI Architecture

Day 56 已经完成：

```text
S3
 ↓
SQS
 ↓
Document Processing
 ↓
Embedding
 ↓
pgvector
 ↓
RAG
```

今天把系统进一步升级：

> **让 Java + AI 应用能够处理高并发、长时间运行、可重试、可恢复的 Agent 任务。**

核心技术：

```text
Event-Driven
SQS
EventBridge
Kafka
Async Worker
Retry
DLQ
Idempotency
Saga
State Machine
```

---

# 1. 为什么 AI 应用必须异步？

传统 API：

```text
POST /chat
 ↓
Java
 ↓
LLM
 ↓
5 seconds
 ↓
Response
```

适合：

```text
简单问答
```

但企业任务可能：

```text
上传 500 页 PDF
+
RAG
+
SQL
+
10 个 Agent
+
生成报告
```

可能需要：

```text
30 秒
2 分钟
10 分钟
```

不能让 HTTP 一直等待。

---

# 2. 同步 vs 异步

### 同步

```text
User
 ↓
API
 ↓
Agent
 ↓
LLM
 ↓
Response
```

### 异步

```text
User
 ↓
API
 ↓
Create Job
 ↓
Queue
 ↓
Worker
 ↓
Agent
 ↓
LLM
 ↓
Result
```

用户先得到：

```json
{
  "jobId": "job-123",
  "status": "PROCESSING"
}
```

---

# 3. Day 57 最终架构

```text
                         USER
                           │
                           ▼
                      API Gateway
                           │
                           ▼
                     Spring Boot
                           │
                           ▼
                       Job API
                           │
                           ▼
                        SQS
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
             Agent Worker      Document Worker
                  │                 │
                  ▼                 ▼
              AI Gateway           S3
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
       Bedrock  OpenAI   Gemini
                  │
                  ▼
             State Store
                  │
                  ▼
             PostgreSQL
                  │
                  ▼
              EventBridge
                  │
                  ▼
           Notification / Audit
```

---

# 4. Event-Driven Architecture

核心思想：

> **不要让 Service 直接依赖 Service。**

传统：

```text
OrderService
 ↓
ReportService
 ↓
NotificationService
```

更好的：

```text
OrderService
 ↓
OrderCreated Event
 ↓
Event Bus
 ├── Report
 ├── Notification
 └── Analytics
```

---

# 5. Event

统一事件：

```java
public record DomainEvent(

    UUID eventId,

    String eventType,

    String tenantId,

    Instant timestamp,

    Map<String, Object> payload

) {}
```

---

# 6. Example

```json
{
  "eventId": "evt-123",
  "eventType": "CUSTOMER_RISK_ANALYSIS_REQUESTED",
  "tenantId": "tenant-a",
  "timestamp": "2026-08-20T20:00:00Z",
  "payload": {
    "customerId": "C1001"
  }
}
```

---

# 7. 为什么 Event 必须有 eventId？

因为消息系统可能：

```text
重复发送
```

例如：

```text
evt-123
evt-123
```

Worker 必须能够判断：

```text
这个事件是否已经处理？
```

---

# 8. Idempotency

数据库：

```sql
CREATE TABLE processed_events (
    event_id UUID PRIMARY KEY,
    processed_at TIMESTAMP NOT NULL
);
```

Worker：

```java
if (eventRepository.exists(event.eventId())) {
    return;
}

process(event);

eventRepository.save(
    event.eventId()
);
```

这样：

```text
Event
 ↓
第一次 → Execute
第二次 → Ignore
```

---

# 9. SQS

AWS 企业 Java 项目中：

```text
SQS
```

非常适合：

```text
AI Jobs
Document Processing
Agent Tasks
Background Jobs
```

---

# 10. Standard vs FIFO

### Standard Queue

```text
High throughput
At-least-once delivery
```

适合：

```text
Document Processing
Embedding
Independent Agent Tasks
```

### FIFO Queue

```text
Ordering
Deduplication
```

适合：

```text
需要严格顺序的业务操作
```

---

# 11. AI Job Queue

例如：

```text
ai-agent-jobs
```

消息：

```json
{
  "jobId": "job-001",
  "type": "CUSTOMER_RISK_ANALYSIS",
  "tenantId": "tenant-a"
}
```

---

# 12. Job Lifecycle

```text
CREATED
   ↓
QUEUED
   ↓
RUNNING
   ↓
COMPLETED
```

失败：

```text
RUNNING
   ↓
FAILED
   ↓
RETRY
```

最终：

```text
FAILED
   ↓
DLQ
```

---

# 13. Job Table

```sql
CREATE TABLE ai_jobs (
    id UUID PRIMARY KEY,

    tenant_id VARCHAR(100) NOT NULL,

    job_type VARCHAR(100),

    status VARCHAR(30),

    progress INTEGER DEFAULT 0,

    error_message TEXT,

    created_at TIMESTAMP,

    started_at TIMESTAMP,

    completed_at TIMESTAMP
);
```

---

# 14. Job API

### 创建任务

```http
POST /api/v1/jobs
```

返回：

```json
{
  "jobId": "job-001",
  "status": "QUEUED"
}
```

---

# 15. 查询任务

```http
GET /api/v1/jobs/job-001
```

返回：

```json
{
  "jobId": "job-001",
  "status": "RUNNING",
  "progress": 65
}
```

---

# 16. Worker

Spring Boot：

```java
@SqsListener("ai-agent-jobs")
public void handle(JobMessage message) {

    jobService.execute(
        message.jobId()
    );
}
```

Worker 不直接依赖 HTTP。

---

# 17. Worker 扩展

```text
                    SQS
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Worker 1   Worker 2   Worker 3
```

任务多：

```text
100 jobs
```

增加：

```text
Worker 4
Worker 5
Worker 6
```

这就是水平扩展。

---

# 18. ECS / EKS

Day 57：

```text
EKS
 │
 ├── API Pods
 │
 ├── Agent Workers
 │
 ├── Document Workers
 │
 └── Evaluation Workers
```

不同 Worker：

```text
不同 Deployment
```

---

# 19. 为什么 Worker 和 API 分开？

不要：

```text
API Pod
 ├── HTTP
 ├── Agent
 ├── Document
 └── Evaluation
```

更好：

```text
API Deployment
Agent Worker Deployment
Document Worker Deployment
Evaluation Worker Deployment
```

因为负载不同。

---

# 20. Autoscaling

例如：

```text
SQS Queue Depth
        │
        ▼
     KEDA
        │
        ▼
EKS Worker Pods
```

队列：

```text
10 jobs
 ↓
2 Pods
```

队列：

```text
1,000 jobs
 ↓
20 Pods
```

具体最大值需要受数据库、模型 Provider Rate Limit 和成本预算约束。

---

# 21. SQS + KEDA

这是非常值得掌握的企业架构：

```text
SQS
 ↓
Queue Depth
 ↓
KEDA
 ↓
EKS
 ↓
Worker Pods
```

比单纯 CPU Autoscaling 更适合：

```text
Async AI Workloads
```

---

# 22. Retry

AI Worker 失败：

```text
Attempt 1
 ↓
FAIL
 ↓
Attempt 2
 ↓
FAIL
 ↓
Attempt 3
 ↓
FAIL
```

然后：

```text
DLQ
```

---

# 23. Exponential Backoff

不要：

```text
retry
retry
retry
retry
```

应该：

```text
1 sec
 ↓
2 sec
 ↓
4 sec
 ↓
8 sec
```

例如：

```java
long delay =
    Math.min(
        60,
        (long) Math.pow(2, attempt)
    );
```

实际生产环境还应加入 **jitter**，避免大量 Worker 同时重试形成 thundering herd。

---

# 24. Retryable vs Non-Retryable

### Retry

```text
Timeout
429
503
Network Error
Temporary DB Error
```

### Don't Retry

```text
Invalid Request
Unauthorized
Invalid Schema
Tenant Violation
Policy Denied
```

否则：

```text
错误请求
 ↓
Retry 10 次
```

只是浪费资源。

---

# 25. Dead Letter Queue

```text
Main Queue
     │
     ▼
 Worker
     │
 ┌───┴────┐
 ▼        ▼
Success  Failure
           │
           ▼
        Retry
           │
           ▼
          DLQ
```

---

# 26. DLQ 管理

DLQ 不能成为：

```text
垃圾桶
```

必须：

```text
DLQ
 ↓
Monitor
 ↓
Analyze
 ↓
Fix
 ↓
Replay
```

---

# 27. Replay

例如：

```text
DLQ
 ↓
Fix Parser
 ↓
Replay Event
 ↓
Worker
```

这对企业系统非常重要。

---

# 28. EventBridge

SQS 更适合：

```text
Queue / Worker
```

EventBridge 更适合：

```text
Event Routing
```

例如：

```text
CustomerRiskCompleted
```

EventBridge：

```text
             EventBridge
             │    │    │
             ▼    ▼    ▼
           Email Slack Audit
```

---

# 29. EventBridge Rule

```text
Event:
CUSTOMER_RISK_COMPLETED
```

规则：

```text
IF riskLevel = HIGH
```

发送：

```text
Notification
```

---

# 30. Event-Driven AI

例如：

```text
Customer Created
       ↓
EventBridge
       ↓
Risk Analysis Job
       ↓
SQS
       ↓
Agent Worker
       ↓
AI Gateway
       ↓
LLM
       ↓
Risk Result
```

这已经是非常典型的企业 AI 架构。

---

# 31. Agent Workflow

Day 52：

```text
Supervisor
 ↓
Customer
 ↓
Data
 ↓
Research
 ↓
Report
```

Day 57：

```text
Supervisor
 ↓
Create Job
 ↓
SQS
 ↓
Worker
 ↓
Agent Workflow
```

---

# 32. Long-running Agent

例如：

```text
job-001
```

状态：

```text
QUEUED
 ↓
RUNNING
 ↓
CUSTOMER_COMPLETED
 ↓
DATA_COMPLETED
 ↓
RESEARCH_COMPLETED
 ↓
EVALUATION
 ↓
REPORT
 ↓
COMPLETED
```

---

# 33. State Machine

推荐保存：

```text
currentState
```

例如：

```json
{
  "jobId": "job-001",
  "state": "RESEARCH",
  "version": 7
}
```

---

# 34. 为什么需要 State？

Worker 崩溃：

```text
Research Worker
 ↓
Crash
```

恢复后：

```text
Load State
 ↓
RESEARCH
 ↓
Continue
```

而不是：

```text
从头开始
```

---

# 35. State Table

```sql
CREATE TABLE job_states (
    job_id UUID PRIMARY KEY,

    state VARCHAR(50),

    version BIGINT,

    data JSONB,

    updated_at TIMESTAMP
);
```

---

# 36. Optimistic Lock

两个 Worker 同时处理：

```text
Worker A
version = 5

Worker B
version = 5
```

只能一个成功更新：

```sql
UPDATE job_states
SET
    state = :state,
    version = version + 1
WHERE job_id = :jobId
AND version = :expectedVersion;
```

如果：

```text
rows = 0
```

说明版本已经变化。

---

# 37. Saga

企业长任务经常：

```text
A
 ↓
B
 ↓
C
 ↓
D
```

如果 D 失败：

```text
C
B
A
```

怎么办？

这就是：

> Saga Pattern

---

# 38. AI Saga

例如：

```text
Customer Analysis
 ↓
Create Risk Report
 ↓
Store Report
 ↓
Send Notification
```

如果：

```text
Send Notification
```

失败，不一定需要回滚：

```text
Risk Analysis
```

而是：

```text
Retry Notification
```

---

# 39. Compensation

真正需要补偿的场景：

```text
Payment
 ↓
Order
 ↓
Inventory
```

AI Agent 不应该随意做：

```text
Database Rollback
```

而应该定义：

```text
Compensation Action
```

---

# 40. Eventual Consistency

Event-driven 系统通常：

```text
不是立即一致
```

而是：

```text
Event
 ↓
Service A
 ↓
Event
 ↓
Service B
 ↓
Event
 ↓
Service C
```

最终：

```text
Eventually Consistent
```

---

# 41. Kafka vs SQS

Day 57 必须理解这个区别。

|                 | SQS   | Kafka |
| --------------- | ----- | ----- |
| Queue           | ⭐⭐⭐⭐⭐ | ⭐⭐⭐   |
| AWS 托管          | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐  |
| 简单异步任务          | ⭐⭐⭐⭐⭐ | ⭐⭐⭐   |
| Event Streaming | ⭐⭐⭐   | ⭐⭐⭐⭐⭐ |
| Replay          | ⭐⭐⭐   | ⭐⭐⭐⭐⭐ |
| 高吞吐             | ⭐⭐⭐⭐  | ⭐⭐⭐⭐⭐ |
| Consumer Groups | ⭐⭐⭐   | ⭐⭐⭐⭐⭐ |
| 学习复杂度           | 低     | 高     |

---

# 42. 你的项目现在选什么？

目前：

> **优先 SQS + EventBridge。**

因为你的目标是：

```text
AWS
+
Java
+
AI
+
Enterprise
```

先把：

```text
SQS
EventBridge
EKS
```

掌握。

之后再增加：

```text
Amazon MSK / Kafka
```

---

# 43. Kafka 什么时候更值得？

如果需要：

```text
大量 Event Stream
Replay
Multiple Consumers
Ordering
Event Analytics
High Throughput
```

例如：

```text
Millions of events
```

Kafka 的优势会更加明显。

---

# 44. AI Event Types

建立：

```text
DOCUMENT_UPLOADED
DOCUMENT_PROCESSED
EMBEDDING_COMPLETED
RAG_QUERY_CREATED
AGENT_STARTED
AGENT_COMPLETED
AGENT_FAILED
EVALUATION_COMPLETED
SECURITY_BLOCKED
MODEL_FALLBACK
```

---

# 45. Event Catalog

```text
events/
├── document
│   ├── uploaded
│   └── processed
│
├── agent
│   ├── started
│   ├── completed
│   └── failed
│
├── ai
│   ├── model-fallback
│   └── model-error
│
└── security
    └── blocked
```

---

# 46. Event Version

事件也要版本：

```text
CUSTOMER_CREATED.v1
CUSTOMER_CREATED.v2
```

因为消费者可能升级速度不同。

---

# 47. Event Schema

例如：

```json
{
  "eventId": "evt-001",
  "eventType": "AGENT_COMPLETED",
  "version": 1,
  "timestamp": "2026-08-20T20:00:00Z",
  "tenantId": "tenant-a",
  "payload": {
    "jobId": "job-001",
    "agent": "research_agent"
  }
}
```

---

# 48. Outbox Pattern

这是 Day 57 的重点。

问题：

```text
DB Transaction
 ↓
Save Order
 ↓
Publish Event
```

如果：

```text
DB 成功
Event Publish 失败
```

就出现：

```text
DB = SUCCESS
Event = LOST
```

---

# 49. Outbox Pattern

解决：

```text
Transaction
 ├── Business Data
 └── Outbox Event
```

一起提交：

```text
Database
```

然后：

```text
Outbox Worker
 ↓
Publish Event
```

---

# 50. Outbox Table

```sql
CREATE TABLE outbox_events (
    id UUID PRIMARY KEY,

    event_type VARCHAR(200),

    aggregate_id UUID,

    payload JSONB,

    status VARCHAR(30),

    created_at TIMESTAMP,

    published_at TIMESTAMP
);
```

---

# 51. Outbox Flow

```text
Spring Boot
    │
    ▼
DB Transaction
 ┌───────────────┐
 │ Order         │
 │ Outbox Event  │
 └───────────────┘
        │
        ▼
  Outbox Worker
        │
        ▼
   EventBridge
```

这是非常值得掌握的企业 Java 面试知识。

---

# 52. Inbox Pattern

Consumer 端：

```text
Event
 ↓
Inbox
 ↓
Check Duplicate
 ↓
Process
```

配合：

```text
Outbox
+
Inbox
```

可以显著增强事件可靠性。

---

# 53. Outbox + Inbox

```text
Service A
   │
   ▼
Outbox
   │
   ▼
Event Bus
   │
   ▼
Service B
   │
   ▼
Inbox
   │
   ▼
Process
```

---

# 54. AI Platform 最终异步架构

```text
                         USER
                           │
                           ▼
                      API Gateway
                           │
                           ▼
                      Spring Boot
                           │
                           ▼
                        Job API
                           │
                           ▼
                          SQS
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
           Agent Worker        Document Worker
                 │                   │
                 ▼                   ▼
            AI Gateway              S3
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
     Bedrock  OpenAI   Gemini
                 │
                 ▼
             PostgreSQL
                 │
                 ▼
            Outbox Event
                 │
                 ▼
            EventBridge
             │    │    │
             ▼    ▼    ▼
          Audit Alert Notification
```

---

# 55. Day 57 项目结构

```text
event/
│
├── model/
│   ├── DomainEvent.java
│   └── EventType.java
│
├── producer/
│   ├── EventPublisher.java
│   └── EventBridgePublisher.java
│
├── consumer/
│   ├── AgentJobConsumer.java
│   └── DocumentJobConsumer.java
│
├── job/
│   ├── JobService.java
│   ├── JobState.java
│   └── JobRepository.java
│
├── retry/
│   ├── RetryPolicy.java
│   └── DeadLetterHandler.java
│
├── idempotency/
│   └── IdempotencyService.java
│
└── outbox/
    ├── OutboxEvent.java
    └── OutboxPublisher.java
```

---

# 56. Day 57 实战项目

建立：

> **Enterprise AI Async Job Platform**

用户：

```text
分析客户 C1001 最近 30 天订单，
结合公司退货政策生成风险报告。
```

API：

```http
POST /api/v1/jobs
```

立即返回：

```json
{
  "jobId": "job-1001",
  "status": "QUEUED"
}
```

---

# 57. Job Execution

```text
SQS
 ↓
Agent Worker
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
 ↓
COMPLETED
```

---

# 58. Progress

实时保存：

```json
{
  "jobId": "job-1001",
  "state": "RESEARCH",
  "progress": 70
}
```

前端：

```text
Analyzing customer...
██████████████░░░░ 70%
```

---

# 59. Failure Scenario

模拟：

```text
Research Agent
 ↓
Timeout
```

系统：

```text
Retry 1
 ↓
Retry 2
 ↓
Retry 3
 ↓
DLQ
```

用户看到：

```json
{
  "status": "FAILED",
  "reason": "Research service unavailable"
}
```

---

# 60. Recovery

恢复服务以后：

```text
DLQ
 ↓
Replay
 ↓
Agent Worker
 ↓
Research
 ↓
Evaluator
 ↓
Report
```

而不是：

```text
从头重新分析所有内容
```

---

# 61. Day 57 必做清单

```text
[ ] SQS
[ ] EventBridge
[ ] Async Job API
[ ] Agent Worker
[ ] Document Worker
[ ] Job State
[ ] Retry
[ ] Exponential Backoff
[ ] Jitter
[ ] DLQ
[ ] Idempotency
[ ] Outbox
[ ] Inbox
[ ] Event Schema
[ ] Event Version
[ ] Replay
[ ] EKS Worker
[ ] KEDA Autoscaling
[ ] Progress Tracking
[ ] Failure Recovery
[ ] Saga Basics
```

---

# 62. Day 57 验收标准

必须成功完成：

```text
POST /jobs
      ↓
   202 Accepted
      ↓
     SQS
      ↓
    Worker
      ↓
 Multi-Agent
      ↓
   COMPLETED
```

然后测试：

```text
Worker Crash
 ↓
Retry
 ↓
Resume
 ↓
Completed
```

再测试：

```text
Duplicate Event
 ↓
Idempotency
 ↓
Only Execute Once
```

最后：

```text
Permanent Failure
 ↓
DLQ
 ↓
Replay
 ↓
Success
```

---

# 63. Day 57 面试重点

### Q1：为什么 AI Job 应该异步？

因为：

```text
LLM latency
+
Agent workflow
+
Large document
+
Multiple tools
```

可能超过 HTTP 请求合理等待时间。

---

### Q2：SQS 和 EventBridge 有什么区别？

```text
SQS
=
Queue / Work Distribution

EventBridge
=
Event Routing
```

---

### Q3：为什么需要 Idempotency？

因为消息系统可能重复投递。

---

### Q4：Outbox 解决什么问题？

解决：

```text
DB Transaction
+
Event Publishing
```

之间的数据一致性问题。

---

### Q5：为什么需要 DLQ？

保存：

```text
多次重试仍然失败
```

的消息，避免阻塞主队列。

---

### Q6：为什么 AI Worker 需要独立部署？

因为：

```text
API workload
≠
AI workload
```

可以分别：

```text
Scale
Deploy
Monitor
```

---

# 64. Day 57 最重要架构图

```text
                         USER
                           │
                           ▼
                       API Gateway
                           │
                           ▼
                      Spring Boot
                           │
                           ▼
                        Job API
                           │
                           ▼
                          SQS
                           │
                    ┌──────┴──────┐
                    ▼             ▼
               Agent Worker   Doc Worker
                    │
                    ▼
                AI Gateway
                    │
           ┌────────┼────────┐
           ▼        ▼        ▼
        Bedrock   OpenAI   Gemini
                    │
                    ▼
                Job State
                    │
                    ▼
               PostgreSQL
                    │
                 Outbox
                    │
                    ▼
               EventBridge
              ┌─────┼─────┐
              ▼     ▼     ▼
            Audit Alert Notification
```

---

## Day 50 → Day 57

你现在的企业 AI 平台已经形成：

```text
Day 50  Agent + Tool + MCP
        ↓
Day 51  Workflow + State
        ↓
Day 52  Multi-Agent
        ↓
Day 53  Evaluation
        ↓
Day 54  AI Gateway
        ↓
Day 55  AI Security
        ↓
Day 56  AI Data Platform
        ↓
Day 57  Event-Driven AI
```

**Day 58** 下一阶段建议进入 **Observability + AIOps**：把 OpenTelemetry、CloudWatch、Prometheus、Grafana、Distributed Tracing、LLM Token/Cost/Latency、Agent Trace、Error Tracking 和 AI 系统告警统一起来。这样你就能真正回答生产环境最重要的三个问题：**哪里慢、哪里错、哪里烧钱。**
