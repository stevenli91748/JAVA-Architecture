# Day 60：Enterprise Customer Intelligence AI Platform

今天不是学习一个新框架，而是把 **Day 1–59 全部整合成一个完整企业级项目**。

最终目标：

> **做出一个可以放到 GitHub、用于美国 AI Engineer / Java AI Engineer 求职的完整 AWS 项目。**

项目名称：

# Enterprise Customer Intelligence AI Platform

---

## 1. 今天最终要做什么？

用户在 React 前端输入：

> “分析客户 C1001 最近 30 天的订单、退货记录和公司政策，判断客户风险，并生成一份报告。”

系统自动完成：

```text
React
 ↓
Spring Boot
 ↓
OAuth2 / JWT
 ↓
AI Gateway
 ↓
Agent Supervisor
 ↓
 ┌───────────────┐
 │ Customer Agent│
 │ Data Agent    │
 │ RAG Agent     │
 │ Research Agent│
 └───────────────┘
 ↓
MCP / Tools
 ↓
PostgreSQL
S3
pgvector
 ↓
LLM
 ↓
Evaluation
 ↓
Report
 ↓
React
```

---

# 2. 完整 AWS 架构

```text
                         INTERNET
                            │
                            ▼
                       CloudFront
                            │
                            ▼
                           WAF
                            │
                            ▼
                    Application Load Balancer
                            │
                            ▼
                         EKS
                  ┌─────────┼─────────┐
                  ▼         ▼         ▼
              Frontend   API       Workers
              React      Spring     Agent
                         Boot       Document
                                    Evaluation
                                      │
                                      ▼
                                  AI Gateway
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
                 Bedrock           OpenAI            Gemini
                    │
                    ▼
               Multi-Agent
                    │
             ┌──────┼──────┐
             ▼      ▼      ▼
           RAG    Tools   MCP
             │      │      │
             ▼      ▼      ▼
          pgvector PostgreSQL APIs
             │
             ▼
             S3
             
        ─────────────────────────────
              Async Layer
        ─────────────────────────────
                    │
                   SQS
                    │
                 Workers
                    │
                EventBridge

        ─────────────────────────────
             Observability
        ─────────────────────────────
                    │
             OpenTelemetry
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
    CloudWatch   Prometheus   Grafana
```

---

# 3. 技术栈

## Frontend

```text
React
Next.js
TypeScript
Tailwind CSS
```

## Backend

```text
Java 21
Spring Boot
Spring Security
Spring AI
Spring Data JPA
PostgreSQL
Redis
```

## AI

```text
LLM
RAG
Embedding
Multi-Agent
MCP
Tool Calling
AI Gateway
Evaluation
Guardrails
```

## AWS

```text
EKS
ECR
S3
RDS
ElastiCache
SQS
EventBridge
CloudWatch
IAM
WAF
CloudFront
Secrets Manager
```

## DevOps

```text
Docker
Terraform
GitHub Actions
Argo CD
Kubernetes
OpenTelemetry
Prometheus
Grafana
```

---

# 4. GitHub Repository

今天首先建立真正的项目结构：

```text
enterprise-customer-ai/
│
├── frontend/
│   ├── app/
│   ├── components/
│   ├── hooks/
│   └── services/
│
├── backend/
│   ├── api/
│   ├── auth/
│   ├── customer/
│   ├── order/
│   ├── ai/
│   ├── agent/
│   ├── rag/
│   ├── mcp/
│   ├── tool/
│   ├── evaluation/
│   ├── security/
│   └── observability/
│
├── workers/
│   ├── agent-worker/
│   ├── document-worker/
│   └── evaluation-worker/
│
├── infrastructure/
│   └── terraform/
│
├── deployment/
│   ├── helm/
│   └── argocd/
│
├── evaluation/
│   ├── datasets/
│   └── benchmarks/
│
├── docs/
│   ├── architecture.md
│   ├── security.md
│   ├── api.md
│   └── deployment.md
│
└── .github/
    └── workflows/
```

---

# 5. 前端

React 页面：

```text
Dashboard
│
├── Customers
│
├── Customer Detail
│
├── AI Analysis
│
├── Knowledge Base
│
├── Jobs
│
├── Reports
│
└── Admin
```

---

# 6. Customer Dashboard

显示：

```text
Customer
C1001

Risk Score
82 / 100

Risk Level
HIGH

Orders
24

Returns
6

AI Confidence
94%

Last Analysis
Today
```

---

# 7. AI Analysis 页面

用户：

```text
Customer ID:
C1001

Question:

Analyze this customer's
risk based on orders,
returns and company policy.
```

点击：

```text
[ Analyze ]
```

前端：

```http
POST /api/v1/ai/jobs
```

---

# 8. 后端立即返回

不要等待 Agent 完成。

```json
{
  "jobId": "job-10001",
  "status": "QUEUED"
}
```

HTTP：

```text
202 Accepted
```

---

# 9. Job Architecture

```text
POST /ai/jobs
       │
       ▼
Create Job
       │
       ▼
PostgreSQL
       │
       ▼
SQS
       │
       ▼
Agent Worker
```

---

# 10. Agent Supervisor

```text
Supervisor Agent
       │
       ├── Customer Agent
       │
       ├── Order Agent
       │
       ├── RAG Agent
       │
       ├── Research Agent
       │
       └── Report Agent
```

---

# 11. Customer Agent

查询：

```text
Customer
Profile
Account
History
```

Tool：

```java
customerService.getCustomer(
    customerId
);
```

---

# 12. Order Agent

调用：

```text
getOrders()
getReturns()
getPurchases()
```

例如：

```json
{
  "customerId": "C1001",
  "orders": 24,
  "returns": 6,
  "returnRate": 0.25
}
```

---

# 13. RAG Agent

查询：

```text
Company Return Policy
Fraud Policy
Customer Risk Policy
```

使用：

```text
S3
 ↓
Chunk
 ↓
Embedding
 ↓
pgvector
 ↓
Hybrid Retrieval
 ↓
Reranker
```

---

# 14. Research Agent

如果需要外部信息：

```text
Research Agent
 ↓
MCP
 ↓
Approved External Tool
```

必须经过：

```text
Authorization
 ↓
Policy
 ↓
Tool Execution
```

---

# 15. Report Agent

最后：

```text
Customer Data
+
Order Data
+
Policy
+
Research
```

生成：

```text
Risk Report
```

---

# 16. Report

例如：

```text
Customer Risk Report

Customer:
C1001

Risk:
HIGH

Score:
82

Key Findings:

1. High return rate
2. Multiple high-value orders
3. Return behavior exceeds policy baseline

Evidence:

Order Data
[Orders DB]

Company Policy
[Return Policy, Page 14]

Recommendation:

Manual review recommended.
```

---

# 17. 必须有 Citation

不能只：

```text
Risk = HIGH
```

应该：

```text
Risk = HIGH

Sources:
[Customer DB]
[Order DB]
[Return Policy p.14]
```

---

# 18. Security

Day 55 的安全体系全部接入。

```text
User
 ↓
OAuth2
 ↓
JWT
 ↓
Tenant
 ↓
RBAC
 ↓
Agent
 ↓
Tool
```

---

# 19. RBAC

角色：

```text
ADMIN
ANALYST
MANAGER
USER
```

权限：

```text
ADMIN
  └── Everything

MANAGER
  ├── Customer
  ├── Reports
  └── AI Analysis

ANALYST
  ├── Customer
  └── AI Analysis

USER
  └── Limited
```

---

# 20. AI Authorization

非常重要：

```text
User
 ↓
Can user access customer?
 ↓
Can user execute this tool?
 ↓
Can agent access this data?
```

不能只检查：

```text
JWT valid
```

---

# 21. Data Security

Customer：

```text
tenant_id
```

所有查询：

```text
tenant_id = currentTenant
```

例如：

```sql
SELECT *
FROM customers
WHERE id = :customerId
AND tenant_id = :tenantId;
```

---

# 22. Knowledge Security

RAG 也必须：

```text
tenant_id
+
classification
+
ACL
```

否则：

```text
Tenant A
 ↓
RAG
 ↓
Tenant B Document
```

这是严重安全漏洞。

---

# 23. AI Gateway

所有 LLM：

```text
Agent
 ↓
AI Gateway
```

不要：

```text
Agent
 ├── OpenAI
 ├── Gemini
 └── Bedrock
```

直接调用。

---

# 24. AI Gateway

统一：

```text
Model Routing
Token Tracking
Cost Tracking
Retry
Fallback
Rate Limit
Guardrails
Observability
```

---

# 25. Model Routing

例如：

```text
Simple Query
 ↓
Cheap Model

Complex Analysis
 ↓
Premium Model

High Security
 ↓
Approved Model
```

---

# 26. Fallback

```text
Primary Model
      │
      ▼
    Error
      │
      ▼
Fallback Model
```

例如：

```text
Bedrock
 ↓
Timeout
 ↓
OpenAI
```

---

# 27. RAG

今天完整接入：

```text
Document Upload
 ↓
S3
 ↓
SQS
 ↓
Parser
 ↓
Chunk
 ↓
Embedding
 ↓
pgvector
```

查询：

```text
Question
 ↓
Hybrid Search
 ↓
Reranker
 ↓
Context
 ↓
LLM
```

---

# 28. Async Architecture

所有长任务：

```text
AI Job
Document Processing
Evaluation
Report Generation
```

都：

```text
API
 ↓
SQS
 ↓
Worker
```

---

# 29. Job State

```text
CREATED
 ↓
QUEUED
 ↓
RUNNING
 ↓
ANALYZING
 ↓
GENERATING_REPORT
 ↓
COMPLETED
```

失败：

```text
FAILED
 ↓
RETRY
 ↓
DLQ
```

---

# 30. EventBridge

事件：

```text
AI_ANALYSIS_STARTED
AI_ANALYSIS_COMPLETED
AI_ANALYSIS_FAILED
REPORT_GENERATED
SECURITY_BLOCKED
MODEL_FALLBACK
```

---

# 31. Observability

每个请求：

```text
traceId
requestId
jobId
tenantId
```

例如：

```text
traceId=t-100
jobId=j-200
tenant=tenant-a
```

---

# 32. Trace

完整：

```text
API
 │
 ▼
AI Gateway
 │
 ▼
Supervisor
 │
 ├── Customer Agent
 │
 ├── Order Agent
 │
 ├── RAG Agent
 │    ├── Embedding
 │    ├── Vector Search
 │    └── Reranker
 │
 └── Report Agent
      │
      ▼
     LLM
```

---

# 33. AI Metrics

Dashboard：

```text
Requests
Errors
P95
P99
Tokens
Cost
Fallback
Agent Runs
Tool Calls
RAG Quality
Citation Accuracy
```

---

# 34. CI/CD

代码：

```text
GitHub
 ↓
Pull Request
 ↓
GitHub Actions
```

自动：

```text
Compile
 ↓
Unit Test
 ↓
Integration Test
 ↓
Security Scan
 ↓
AI Evaluation
 ↓
Docker
 ↓
ECR
```

---

# 35. Deployment

```text
ECR
 ↓
EKS
 ↓
Staging
 ↓
Canary
 ↓
Production
```

---

# 36. Canary

```text
Stable 95%
Canary 5%
```

监控：

```text
Error
Latency
Cost
AI Quality
```

正常：

```text
5%
 ↓
25%
 ↓
50%
 ↓
100%
```

异常：

```text
Rollback
```

---

# 37. Infrastructure as Code

Terraform 管理：

```text
VPC
EKS
RDS
Redis
S3
SQS
EventBridge
ECR
IAM
CloudWatch
```

所以：

```text
AWS Console
```

不再是主要部署方式。

---

# 38. Production Architecture

最终：

```text
                 ┌──────────────┐
                 │    React     │
                 └──────┬───────┘
                        │
                   CloudFront
                        │
                       WAF
                        │
                      ALB
                        │
               ┌────────┴────────┐
               │       EKS       │
               │                 │
               │ Spring Boot     │
               │ Agent Workers   │
               │ Document Worker │
               └───────┬─────────┘
                       │
                 ┌─────┴─────┐
                 │ AI Gateway│
                 └─────┬─────┘
                       │
            ┌──────────┼──────────┐
            ▼          ▼          ▼
         Bedrock     OpenAI    Gemini
                       │
                       ▼
                  PostgreSQL
                       │
                 ┌─────┴─────┐
                 ▼           ▼
               pgvector      S3
                 │
                 ▼
                RAG

             SQS / EventBridge

          OpenTelemetry
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
   CloudWatch Prometheus Grafana
```

---

# 39. Day 60 需要真正写的代码

今天不要再大量学习理论。

实际完成：

### Backend

```text
[ ] Customer API
[ ] Order API
[ ] AI Job API
[ ] RAG API
[ ] Report API
```

### AI

```text
[ ] Supervisor Agent
[ ] Customer Agent
[ ] Order Agent
[ ] RAG Agent
[ ] Report Agent
```

### AWS

```text
[ ] S3
[ ] SQS
[ ] RDS
[ ] ECR
[ ] EKS
```

### Security

```text
[ ] OAuth2
[ ] JWT
[ ] RBAC
[ ] Tenant Isolation
[ ] Tool Authorization
```

### DevOps

```text
[ ] Docker
[ ] Terraform
[ ] GitHub Actions
[ ] Canary
[ ] Rollback
```

### Observability

```text
[ ] OpenTelemetry
[ ] CloudWatch
[ ] Prometheus
[ ] Grafana
```

---

# 40. Day 60 API

最终 API：

```text
POST   /api/v1/auth/login

GET    /api/v1/customers
GET    /api/v1/customers/{id}

GET    /api/v1/orders
GET    /api/v1/orders/{id}

POST   /api/v1/ai/jobs
GET    /api/v1/ai/jobs/{id}

POST   /api/v1/knowledge/documents
GET    /api/v1/knowledge/search

GET    /api/v1/reports/{id}
```

---

# 41. 最重要的 API

```http
POST /api/v1/ai/jobs
```

Request：

```json
{
  "customerId": "C1001",
  "task": "CUSTOMER_RISK_ANALYSIS"
}
```

Response：

```json
{
  "jobId": "job-10001",
  "status": "QUEUED"
}
```

---

# 42. 查询结果

```http
GET /api/v1/ai/jobs/job-10001
```

```json
{
  "jobId": "job-10001",
  "status": "COMPLETED",
  "progress": 100,
  "reportId": "report-5001"
}
```

---

# 43. Report API

```http
GET /api/v1/reports/report-5001
```

返回：

```json
{
  "riskScore": 82,
  "riskLevel": "HIGH",
  "confidence": 0.94,
  "summary": "...",
  "findings": [],
  "citations": []
}
```

---

# 44. Frontend → Backend

```text
React
  │
  │ POST /ai/jobs
  ▼
Spring Boot
  │
  │ 202
  ▼
React
  │
  │ GET /ai/jobs/{id}
  ▼
Spring Boot
  │
  ▼
Job Status
```

可以使用：

```text
Polling
```

或者进一步：

```text
WebSocket / SSE
```

今天先用 Polling，降低复杂度。

---

# 45. AI Job UI

```text
Customer C1001

AI Analysis

┌──────────────────────────────┐
│ Customer Analysis            │
│                              │
│ ████████████████░░░░ 80%     │
│                              │
│ ✓ Customer Data              │
│ ✓ Order Analysis             │
│ ✓ Policy Retrieval           │
│ → Risk Evaluation            │
│ ○ Report Generation          │
└──────────────────────────────┘
```

---

# 46. 最终用户体验

用户只看到：

```text
Customer C1001
       │
       ▼
[ Analyze ]
       │
       ▼
Analyzing...
       │
       ▼
Risk Score: 82
       │
       ▼
HIGH RISK
       │
       ▼
View Report
```

背后其实运行：

```text
API
 ↓
SQS
 ↓
Supervisor
 ↓
5 Agents
 ↓
Tools
 ↓
RAG
 ↓
LLM
 ↓
Evaluation
 ↓
Report
```

这就是企业 AI 应用最重要的特点：

> **复杂性隐藏在平台里面，而不是暴露给用户。**

---

# 47. Day 60 Evaluation Dataset

建立：

```text
evaluation/
└── datasets/
    └── customer-risk.json
```

例如：

```json
[
  {
    "customerId": "C1001",
    "question": "Assess customer risk",
    "expectedRisk": "HIGH"
  },
  {
    "customerId": "C1002",
    "question": "Assess customer risk",
    "expectedRisk": "LOW"
  }
]
```

至少准备：

```text
50–100 cases
```

第一版即可。

---

# 48. Evaluation

每次 PR：

```text
50 Cases
 ↓
Agent
 ↓
LLM
 ↓
Evaluation
```

检查：

```text
Risk Accuracy
Citation
Faithfulness
Safety
Latency
Cost
```

---

# 49. GitHub README

这个项目的 README 非常重要。

建议：

```text
# Enterprise Customer Intelligence AI Platform

## Architecture

## Features

## Tech Stack

## AWS Architecture

## AI Architecture

## Security

## RAG

## Multi-Agent

## MCP

## CI/CD

## Observability

## Evaluation

## Deployment

## Demo

## Screenshots
```

---

# 50. Architecture Diagram

GitHub README 放：

```text
Frontend
   ↓
Backend
   ↓
AI Gateway
   ↓
Agents
   ↓
RAG
   ↓
LLM
```

然后：

```text
AWS Infrastructure
```

和：

```text
CI/CD Pipeline
```

三张核心架构图。

---

# 51. 美国 AI Engineer 求职价值

这个项目可以覆盖很多 JD 关键词：

```text
Java
Spring Boot
Python
LLM
RAG
Agents
MCP
Vector Database
AWS
EKS
Docker
Kubernetes
Terraform
CI/CD
GitHub Actions
PostgreSQL
Redis
SQS
EventBridge
OpenTelemetry
Prometheus
Grafana
AI Evaluation
AI Security
```

比单纯：

```text
"ChatGPT Chatbot"
```

更有工程价值。

---

# 52. 简历项目写法

不要写：

> Built an AI chatbot.

应该写成：

> **Enterprise Customer Intelligence AI Platform** — Designed and implemented a production-oriented Java/Spring Boot and AWS AI platform using multi-agent orchestration, RAG, MCP tools, PostgreSQL/pgvector, SQS-based asynchronous workflows, EKS, Terraform, GitHub Actions, OpenTelemetry, and automated AI evaluation.

第二条：

> Implemented secure multi-tenant AI access control, model routing, fallback, tool authorization, prompt/version tracking, citation-aware RAG, asynchronous agent jobs, retry/DLQ, and production observability.

第三条：

> Built CI/CD quality gates combining unit/integration testing, security scanning, AI evaluation, container scanning, canary deployment, monitoring, and automated rollback.

---

# 53. Day 60 最终验收

如果你能完成下面这个流程：

```text
Browser
   ↓
Login
   ↓
Customer C1001
   ↓
AI Analysis
   ↓
202 Accepted
   ↓
SQS
   ↓
Agent Worker
   ↓
Customer Agent
   ↓
Order Agent
   ↓
RAG Agent
   ↓
Policy Retrieval
   ↓
LLM
   ↓
Risk Evaluation
   ↓
Report Agent
   ↓
PostgreSQL
   ↓
Frontend
```

并且：

```text
Trace
Metrics
Logs
Cost
Security
Evaluation
```

全部能够看到，

那么你的 **Day 1–60 第一阶段已经真正完成。**

---

# 54. Day 1 → Day 60 总路线

```text
                    AWS JAVA + AI
                         │
 ┌───────────────────────┼────────────────────────┐
 │                       │                        │
 ▼                       ▼                        ▼
Java/Spring           AWS Cloud                 AI
 │                       │                        │
 ▼                       ▼                        ▼
Backend                EKS                      LLM
Security               S3                       RAG
Database               RDS                      Agent
Redis                  SQS                      MCP
API                    EventBridge               Eval
 │                       │                        │
 └───────────────────────┼────────────────────────┘
                         ▼
                    AI Platform
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
          Security    DevOps      Observability
             │           │           │
             └───────────┼───────────┘
                         ▼
                    Production
```

---

## Day 60 之后

接下来不建议继续单纯“Day 61、Day 62 学一个新技术”。

应该进入 **第二阶段：真正把这个项目做出来**。

建议路线：

```text
Day 61–65
完整 Backend
        ↓
Day 66–70
React Frontend
        ↓
Day 71–75
RAG + pgvector
        ↓
Day 76–80
Multi-Agent + MCP
        ↓
Day 81–85
AWS + EKS
        ↓
Day 86–88
CI/CD + Terraform
        ↓
Day 89
Security
        ↓
Day 90
Production Demo + GitHub + Resume
```

**最终 Day 90 的目标不是“学完 90 天”，而是拿出一个真正可以运行、可以演示、可以压测、可以部署到 AWS、可以放 GitHub、可以写进美国 AI Engineer 简历的项目。**
