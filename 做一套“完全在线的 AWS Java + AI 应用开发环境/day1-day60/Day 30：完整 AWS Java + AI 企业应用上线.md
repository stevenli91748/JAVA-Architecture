# Day 30：完整 AWS Java + AI 企业应用上线

今天是第 1–30 天的**总集成日**。

目标不是再学一个新框架，而是把前 29 天全部串起来，做成一个真正可以运行的：

> **AWS + Java + React + RAG + Agent + Memory + Streaming 企业级 AI Assistant**

---

# 1. Day 30 最终系统

```text
                         ┌──────────────┐
                         │    React     │
                         │   Frontend   │
                         └──────┬───────┘
                                │ HTTPS / SSE
                                ▼
                         ┌──────────────┐
                         │     ALB      │
                         └──────┬───────┘
                                │
                 ┌──────────────┴──────────────┐
                 ▼                             ▼
          ECS API Service              ECS Worker Service
          Spring Boot                  Ingestion Worker
                 │                             │
        ┌────────┼────────┐                    │
        ▼        ▼        ▼                    ▼
      Agent     RAG     Tools                  SQS
        │        │        │                     ▲
        │        ▼        │                     │
        │    pgvector     │                     │
        │        │        │                     │
        └────────┼────────┘                     │
                 ▼                              │
             Bedrock                            │
                                                │
S3 Knowledge ──────── S3 Event ────────────────┘

                 PostgreSQL/RDS
        ┌────────┼──────────────┐
        ▼        ▼              ▼
   Documents  Conversations   Agent Runs

                 │
                 ▼
             CloudWatch
                 │
                 ▼
           Logs / Metrics / Alarms

GitHub
   │
   ▼
CI/CD
   │
   ▼
Docker
   │
   ▼
ECR
   │
   ▼
ECS Fargate
```

AWS 官方支持通过 CodePipeline/CodeBuild 构建容器镜像并部署到 ECS；ECS 标准部署使用 `imagedefinitions.json`，也可以使用 CodeDeploy 做 ECS blue/green deployment。([AWS Documentation][1])

---

# 2. 今天完成的 10 个目标

```text
[1] React 前端
[2] Spring Boot API
[3] Docker
[4] ECR
[5] ECS Fargate
[6] RDS PostgreSQL + pgvector
[7] S3 + SQS
[8] Bedrock
[9] CloudWatch
[10] CI/CD
```

最终：

```text
Git Push
   ↓
Build
   ↓
Test
   ↓
Docker
   ↓
ECR
   ↓
ECS
   ↓
Production
```

---

# 3. 项目最终目录

建议从 Day 30 开始整理成 Monorepo：

```text
aws-java-ai-platform/
│
├── frontend/
│   ├── src/
│   ├── package.json
│   └── Dockerfile
│
├── backend/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
│
├── ingestion-worker/
│   ├── src/
│   ├── pom.xml
│   └── Dockerfile
│
├── infrastructure/
│   ├── terraform/
│   └── environments/
│       ├── dev/
│       ├── test/
│       └── prod/
│
├── docs/
│   ├── architecture.md
│   ├── api.md
│   └── deployment.md
│
├── tests/
│   └── rag/
│
├── docker-compose.yml
├── buildspec.yml
└── README.md
```

---

# 4. 三个 Java 服务

不要把所有东西放一个 Spring Boot。

建议：

```text
backend-api
     │
     ├── REST API
     ├── Agent
     ├── RAG
     ├── Memory
     └── SSE

ingestion-worker
     │
     ├── SQS
     ├── S3
     ├── PDF Parser
     ├── Chunk
     └── Embedding

evaluation
     │
     └── RAG Evaluation
```

生产环境：

```text
ECS
├── api-service
└── ingestion-worker
```

---

# 5. Docker：Spring Boot

`backend/Dockerfile`：

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Java 版本建议统一：

```text
Java 21
```

---

# 6. Docker：Worker

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/worker.jar worker.jar

ENTRYPOINT ["java", "-jar", "worker.jar"]
```

Worker 不需要：

```text
ALB
Public IP
```

---

# 7. Docker：React

生产环境可以：

```text
React
 ↓
npm build
 ↓
Nginx
```

例如：

```dockerfile
FROM nginx:alpine

COPY dist/ /usr/share/nginx/html/

EXPOSE 80
```

---

# 8. 本地 Docker 测试

Day 30 第一件事：

```bash
docker build \
  -t java-ai-backend \
  ./backend
```

运行：

```bash
docker run \
  -p 8080:8080 \
  java-ai-backend
```

测试：

```text
http://localhost:8080/actuator/health
```

应该：

```json
{
  "status": "UP"
}
```

---

# 9. Docker Compose

开发环境：

```text
docker-compose
│
├── frontend
├── backend
├── worker
├── postgres
└── redis(optional)
```

例如：

```yaml
services:

  postgres:
    image: pgvector/pgvector:pg16
    ports:
      - "5432:5432"

  backend:
    build: ./backend
    ports:
      - "8080:8080"
    depends_on:
      - postgres

  worker:
    build: ./ingestion-worker
    depends_on:
      - postgres
```

注意：具体 pgvector 镜像标签要根据你实际使用的 PostgreSQL/pgvector 版本选择。

---

# 10. AWS ECR

建立：

```text
ECR
├── java-ai-backend
├── java-ai-worker
└── java-ai-frontend
```

例如：

```text
123456789.dkr.ecr.us-west-2.amazonaws.com/
java-ai-backend
```

---

# 11. ECR Push

基本流程：

```bash
aws ecr get-login-password \
  --region us-west-2 |
docker login \
  --username AWS \
  --password-stdin \
  <account>.dkr.ecr.us-west-2.amazonaws.com
```

然后：

```bash
docker tag \
  java-ai-backend:latest \
  <ECR_URI>/java-ai-backend:latest
```

Push：

```bash
docker push \
  <ECR_URI>/java-ai-backend:latest
```

---

# 12. 不要生产环境使用 latest

开发：

```text
latest
```

可以。

生产：

```text
latest
```

不推荐。

使用：

```text
git commit SHA
```

例如：

```text
java-ai-backend:8f32a91
```

这样可以知道：

```text
当前 ECS
运行的是哪个 Git Commit。
```

---

# 13. ECS Fargate

创建：

```text
ECS Cluster
└── java-ai-platform
```

Service：

```text
api-service
worker-service
```

Fargate 的好处是你不需要管理 ECS EC2 主机；AWS 的 ECS/CodeDeploy 文档也将 Fargate 作为无需自行管理容器实例基础设施的部署方式。([AWS Documentation][2])

---

# 14. API Task Definition

例如：

```json
{
  "family": "java-ai-api",
  "cpu": "1024",
  "memory": "2048",
  "containerDefinitions": [
    {
      "name": "backend",
      "image": "ECR_IMAGE",
      "portMappings": [
        {
          "containerPort": 8080
        }
      ]
    }
  ]
}
```

第一版：

```text
1 vCPU
2 GB RAM
```

作为起点。

实际生产值要根据 CPU、Memory、RPS 和 LLM latency 压测调整。

---

# 15. Worker Task Definition

例如：

```text
CPU = 1 vCPU
Memory = 2 GB
Desired Count = 1
```

Worker：

```text
Private Subnet
```

不需要公网访问。

---

# 16. ECS 网络

最终：

```text
VPC
│
├── Public Subnet
│     └── ALB
│
├── Private Subnet
│     ├── API ECS
│     └── Worker ECS
│
└── Database Subnet
      └── RDS
```

这是 Day 17 的网络架构在 Day 30 的最终落地。

---

# 17. Security Group

### ALB

允许：

```text
Internet
 ↓
443
```

### API ECS

只允许：

```text
ALB
 ↓
8080
```

### RDS

只允许：

```text
API ECS
Worker ECS
 ↓
5432
```

不要：

```text
Internet
 ↓
RDS 5432
```

---

# 18. RDS PostgreSQL

最终数据库：

```text
RDS PostgreSQL
│
├── documents
├── document_chunks
├── conversations
├── conversation_messages
├── user_preferences
├── agent_runs
└── agent_tool_calls
```

并启用：

```text
pgvector
```

---

# 19. S3

最终：

```text
S3
└── knowledge/
    ├── aws/
    ├── java/
    ├── spring/
    └── uploaded/
```

安全：

```text
Block Public Access = ON
```

Browser：

```text
Presigned URL
```

---

# 20. SQS

最终：

```text
S3
 ↓
SQS
 ↓
Worker
```

以及：

```text
Ingestion DLQ
```

如果 Worker 失败：

```text
Retry
 ↓
Retry
 ↓
Retry
 ↓
DLQ
```

---

# 21. Bedrock

Agent 最终调用：

```text
Amazon Bedrock
```

用于：

```text
Chat
RAG Answer
Agent
Embedding
```

具体模型根据区域、价格、上下文窗口和任务要求选择。

Bedrock 的原生 Runtime 支持普通 `InvokeModel`，也支持 `InvokeModelWithResponseStream` 做流式推理。([AWS Documentation][3])

---

# 22. Streaming 最终链路

```text
Bedrock
   ↓
Spring AI
   ↓
Flux
   ↓
SSE
   ↓
ALB
   ↓
React
```

用户体验：

```text
AI：
Amazon ECS is a...
```

不是：

```text
等待 5 秒
 ↓
突然出现整段文字
```

---

# 23. 如果使用 Bedrock Agent

如果以后直接使用 Amazon Bedrock Agents，其 Agent Runtime 也支持通过 streaming configuration 返回多个 response chunks；AWS 文档指出需要相应的 `bedrock:InvokeModelWithResponseStream` 权限。([AWS Documentation][4])

不过你当前 Day 30 的架构：

```text
Spring AI Agent
+
Bedrock Model
```

已经足够。

不要同时把：

```text
Spring AI Agent
+
Bedrock Agent
```

全部堆进去。

---

# 24. CI/CD

最终：

```text
GitHub
   ↓
CI/CD
   ↓
Build
   ↓
Unit Test
   ↓
Docker Build
   ↓
ECR
   ↓
ECS Deploy
   ↓
Health Check
```

AWS 官方提供 CodePipeline + CodeBuild + ECR + ECS 的完整 CD 模式。([AWS Documentation][1])

---

# 25. 推荐 Git Branch

```text
main
develop
feature/*
```

流程：

```text
feature
   ↓
Pull Request
   ↓
Tests
   ↓
develop
   ↓
Dev
   ↓
main
   ↓
Production
```

---

# 26. CI Pipeline

```text
Git Push
   ↓
Compile
   ↓
Unit Test
   ↓
Integration Test
   ↓
Docker Build
   ↓
Security Scan
   ↓
Push ECR
```

---

# 27. CD Pipeline

```text
ECR
 ↓
Deploy DEV
 ↓
Smoke Test
 ↓
Deploy TEST
 ↓
Integration Test
 ↓
Approval
 ↓
PROD
```

企业环境建议把：

```text
Build
Test
Deploy
```

分开。

---

# 28. Blue/Green Deployment

生产环境推荐：

```text
             ALB
              │
       ┌──────┴──────┐
       ▼             ▼
     Blue           Green
   Version 1       Version 2
```

测试：

```text
Green
 ↓
Health Check
 ↓
Smoke Test
```

成功：

```text
Traffic
 ↓
Green
```

失败：

```text
Traffic
 ↓
Blue
```

AWS CodeDeploy 支持 ECS blue/green deployment；其流程可以先运行 replacement task set，再切换生产流量。([AWS Documentation][5])

---

# 29. Health Check

Spring Boot：

```text
/actuator/health
```

ALB：

```text
GET /actuator/health
```

正常：

```json
{
  "status": "UP"
}
```

如果：

```text
Database down
```

应该：

```text
Health = DOWN
```

让 ECS/ALB 能发现问题。

---

# 30. Readiness

建议区分：

```text
Liveness
Readiness
```

例如：

```text
Liveness
= JVM 是否活着

Readiness
= 是否准备好接受请求
```

---

# 31. CloudWatch

最终监控：

```text
CloudWatch
│
├── Application Logs
├── ECS Metrics
├── ALB Metrics
├── RDS Metrics
├── SQS Metrics
└── Custom AI Metrics
```

---

# 32. AI Metrics

今天已经有：

```text
agent.request.count
agent.tool.call.count
rag.retrieval.latency
stream.first_token_latency
```

继续增加：

```text
ai.request.count
ai.token.input
ai.token.output
ai.request.latency
ai.error.count
ai.cost.estimated
```

---

# 33. CloudWatch Alarm

至少建立：

```text
CPU > 80%
Memory > 80%
5XX > threshold
ALB unhealthy targets > 0
SQS queue depth > threshold
RDS CPU > threshold
RDS storage low
AI error rate > threshold
```

---

# 34. SQS Alarm

例如：

```text
Queue
 ↓
Messages Visible > 100
```

说明：

```text
Worker
处理不过来
```

可以：

```text
ECS Auto Scaling
```

增加：

```text
Worker
1
 ↓
3
 ↓
5
```

---

# 35. ECS Auto Scaling

API：

```text
CPU
Memory
Request Count
```

Worker：

```text
SQS Queue Depth
```

这是两个完全不同的 Scaling Signal。

---

# 36. API Scaling

例如：

```text
CPU > 60%
```

增加：

```text
ECS Task
```

Worker：

```text
Queue > 50
```

增加：

```text
Worker Task
```

---

# 37. RAG Cost

今天一定开始计算：

```text
每次 Chat
 ↓
Embedding
 ↓
Retrieval
 ↓
LLM
```

统计：

```text
Input Tokens
Output Tokens
Embedding Tokens
```

然后：

```text
Cost / Request
Cost / User
Cost / Tenant
Cost / Month
```

---

# 38. Agent Cost

Agent：

```text
Question
 ↓
Tool 1
 ↓
LLM
 ↓
Tool 2
 ↓
LLM
 ↓
Answer
```

比普通 Chat：

```text
成本更高
```

所以要控制：

```text
Max Tool Calls
Max Tokens
Timeout
Rate Limit
```

---

# 39. 安全最终检查

Day 30 必须逐项检查：

```text
[✓] HTTPS
[✓] JWT
[✓] Tenant Isolation
[✓] User Authorization
[✓] Private ECS
[✓] Private RDS
[✓] S3 Private
[✓] Secrets Manager
[✓] IAM Least Privilege
[✓] SQS DLQ
[✓] Input Validation
[✓] Tool Authorization
[✓] Prompt Injection Defense
[✓] Rate Limit
```

---

# 40. Secrets

不要：

```text
application.yml

password=xxx
apiKey=xxx
```

生产：

```text
Secrets Manager
```

例如：

```text
DATABASE_URL
DATABASE_USERNAME
DATABASE_PASSWORD
```

ECS Task：

```text
Secrets Manager
 ↓
ECS Environment
```

---

# 41. IAM

最终至少：

```text
API Task Role
Worker Task Role
CodeBuild Role
CodePipeline Role
CodeDeploy Role
```

不要：

```text
One Super Admin Role
```

所有服务共用。

---

# 42. API Task Role

只允许：

```text
Bedrock InvokeModel
Secrets Manager Read
S3 specific read
CloudWatch logs
```

Worker：

```text
S3 GetObject
SQS Receive/Delete
Bedrock
Secrets
```

---

# 43. RAG 安全

最重要：

```text
tenantId
```

必须来自：

```text
JWT / Authentication Context
```

而不是：

```text
User Request
```

错误：

```json
{
  "tenantId": "company-B"
}
```

正确：

```text
JWT
 ↓
company-A
 ↓
Database Query
```

---

# 44. Agent Tool 安全

Agent：

```text
Tool
 ↓
Authorization
 ↓
Execute
```

不要：

```text
LLM
 ↓
直接 Database
```

特别是：

```text
delete
update
transfer
send
```

这些写操作必须有额外保护。

---

# 45. Prompt Injection

知识库：

```text
PDF
 ↓
Chunk
 ↓
RAG
 ↓
Agent
```

所有检索内容都应该视为：

```text
UNTRUSTED DATA
```

不能把：

```text
Retrieved Text
```

当：

```text
System Instruction
```

---

# 46. 性能测试

Day 30 必须测试：

```text
10 users
50 users
100 users
```

观察：

```text
P50
P95
P99
```

至少测：

```text
/api/chat
/api/chat/stream
/api/documents
/api/documents/upload-url
```

---

# 47. AI Latency

拆成：

```text
Total
│
├── Auth
├── Memory
├── Embedding
├── Vector Search
├── Keyword Search
├── Tool
├── Rerank
├── LLM
└── SSE
```

例如：

```text
Total = 4.2 sec

Memory       20ms
Embedding   120ms
Vector       30ms
RAG          50ms
Tool         80ms
LLM        3.5s
SSE         20ms
```

这样才能优化。

---

# 48. 故障测试

今天主动制造故障。

### Test 1

停止 RDS。

检查：

```text
API
 ↓
正确返回错误
```

不能：

```text
500 stack trace
```

---

### Test 2

Bedrock 不可用。

应该：

```text
AI_SERVICE_ERROR
```

---

### Test 3

SQS Worker 停止。

检查：

```text
Queue ↑
```

恢复 Worker：

```text
Queue ↓
```

---

### Test 4

上传坏 PDF。

应该：

```text
PROCESSING
 ↓
FAILED
 ↓
DLQ
```

---

# 49. RAG Evaluation

Day 26：

```text
20 questions
```

Day 30：

扩大到：

```text
100 questions
```

测试：

```text
Precision
Recall
Faithfulness
Relevance
Latency
Cost
```

最终记录：

```text
RAG Scorecard
```

---

# 50. Production Readiness Score

你可以建立：

| 项目             | 状态 |
| -------------- | -- |
| React          | ✅  |
| Spring Boot    | ✅  |
| RAG            | ✅  |
| Hybrid Search  | ✅  |
| Agent          | ✅  |
| Tool Calling   | ✅  |
| Memory         | ✅  |
| SSE            | ✅  |
| Docker         | ✅  |
| ECR            | ✅  |
| ECS            | ✅  |
| RDS            | ✅  |
| S3             | ✅  |
| SQS            | ✅  |
| Bedrock        | ✅  |
| IAM            | ✅  |
| Secrets        | ✅  |
| CloudWatch     | ✅  |
| CI/CD          | ✅  |
| Load Test      | ⬜  |
| Security Test  | ⬜  |
| RAG Evaluation | ⬜  |

---

# 51. Day 30 最终 CI/CD

```text
                 GitHub
                    │
                    ▼
                 Pull Request
                    │
                    ▼
              Unit / Integration
                    │
                    ▼
                Docker Build
                    │
                    ▼
                  ECR
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
        DEV                  TEST
          │                   │
          └─────────┬─────────┘
                    ▼
                 Approval
                    │
                    ▼
                 PROD
                    │
              Blue / Green
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
       Healthy             Failed
          │                   │
          ▼                   ▼
      New Version          Rollback
```

---

# 52. Day 30 最终 AWS 架构

```text
                         INTERNET
                            │
                            ▼
                       CloudFront
                            │
                            ▼
                           ALB
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
             React/API             SSE/API
                 │                     │
                 └──────────┬──────────┘
                            ▼
                     ECS Fargate API
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
            Agent          RAG           Tools
              │             │             │
              │       ┌─────┴─────┐       │
              │       ▼           ▼       │
              │   pgvector      Search    │
              │                         RDS/API
              │
              ▼
           Bedrock
              │
              ▼
          AI Response
              │
              ▼
             SSE
              │
              ▼
            React


             S3
              │
         Object Created
              ▼
             SQS
              │
              ▼
       ECS Ingestion Worker
              │
        ┌─────┼─────┐
        ▼     ▼     ▼
       PDF  Chunk  Embedding
              │
              ▼
           pgvector


       CloudWatch
          ▲
          │
  ECS / ALB / RDS / SQS / AI
```

---

# 53. 30 天之后，你已经掌握什么

你的技术栈现在可以写成：

### Backend

```text
Java 21
Spring Boot
Spring AI
Spring WebFlux
Spring Security
JPA
PostgreSQL
pgvector
```

### AI

```text
LLM
RAG
Hybrid Search
Embedding
Reranking
Agent
Tool Calling
Memory
Prompt Engineering
Evaluation
```

### AWS

```text
ECS Fargate
ECR
ALB
RDS
S3
SQS
Bedrock
Secrets Manager
IAM
CloudWatch
CodeBuild
CodePipeline
CodeDeploy
```

### DevOps

```text
Docker
GitHub
CI/CD
Blue/Green
Observability
Tracing
Metrics
Load Testing
Security
```

---

# 54. 你的第一个完整项目

现在可以正式把这个项目命名为：

## **AWS Enterprise AI Knowledge Assistant**

功能：

```text
1. 用户注册 / 登录
2. 多租户
3. PDF 上传
4. S3 存储
5. SQS 异步 ingestion
6. PDF parsing
7. Chunking
8. Embedding
9. pgvector
10. Hybrid Search
11. Reranking
12. RAG
13. Agent
14. Tool Calling
15. Conversation Memory
16. SSE Streaming
17. Source Citation
18. Admin Dashboard
19. CloudWatch
20. CI/CD
```

这已经不是简单的“Java AI Demo”，而是一个非常适合放进 **AI Engineer / Java AI Engineer GitHub Portfolio** 的完整项目。

---

# 55. Day 1 → Day 30 技术路线

```text
Day 1–5
AWS + Java + Docker
        ↓
Day 6–10
Spring Boot + React + PostgreSQL
        ↓
Day 11–15
AWS Networking + ECS + RDS
        ↓
Day 16–20
Security + CI/CD + Observability
        ↓
Day 21–24
Bedrock + Spring AI + RAG
        ↓
Day 25
S3 + SQS + Knowledge Ingestion
        ↓
Day 26
Hybrid RAG + Evaluation
        ↓
Day 27
Agent + Tool Calling
        ↓
Day 28
Memory + Multi-turn
        ↓
Day 29
Streaming + SSE
        ↓
Day 30
Production Deployment
```

---

# 56. 最重要的成果

你现在应该能够独立解释这条完整链路：

```text
React
  ↓
ALB
  ↓
ECS Fargate
  ↓
Spring Boot
  ↓
Spring AI Agent
  ↓
┌──────────────┬───────────────┐
│              │               │
Memory         RAG            Tools
│              │               │
RDS         pgvector        RDS/API
│              │               │
└──────────────┴───────────────┘
               ↓
            Bedrock
               ↓
          Streaming/SSE
               ↓
             React
```

同时：

```text
PDF
 ↓
S3
 ↓
SQS
 ↓
ECS Worker
 ↓
Parse
 ↓
Chunk
 ↓
Embedding
 ↓
pgvector
```

再通过：

```text
GitHub
 ↓
CI/CD
 ↓
Docker
 ↓
ECR
 ↓
ECS
```

自动部署。

---

# 57. Day 30 毕业标准

如果你能不看教程完成下面 10 个任务，就可以认为第一阶段真正完成：

```text
□ 1. 自己创建 Spring Boot API
□ 2. 自己 Dockerize
□ 3. 自己推送 ECR
□ 4. 自己部署 ECS
□ 5. 自己连接 RDS
□ 6. 自己实现 S3 → SQS → Worker
□ 7. 自己实现 RAG
□ 8. 自己实现 Agent Tool
□ 9. 自己实现 SSE Streaming
□ 10. 自己做 GitHub → ECS CI/CD
```

**到这里，Day 1–30 的“AWS Java + AI 应用开发环境”第一阶段就完整了。**

下一阶段最值得做的不是继续堆功能，而是进入 **Day 31–60：AI Engineer 实战阶段**——重点转向 **Spring AI Agent 深度开发、MCP、Advanced RAG、Reranker、Agent Evaluation、AI Security、Redis、Kafka、微服务、Kubernetes/EKS，以及一个可放 GitHub/面试的完整企业 AI 项目**。

[1]: https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html?utm_source=chatgpt.com "Tutorial: Amazon ECS Standard Deployment with CodePipeline - AWS CodePipeline"
[2]: https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html?utm_source=chatgpt.com "Tutorial: Create a pipeline with an Amazon ECR source and ECS-to-CodeDeploy deployment - AWS CodePipeline"
[3]: https://docs.aws.amazon.com/us_en/bedrock/latest/userguide/inference-api.html?utm_source=chatgpt.com "Inference using Invoke API - Amazon Bedrock"
[4]: https://docs.aws.amazon.com/bedrock/latest/userguide/agents-invoke-agent.html?utm_source=chatgpt.com "Invoke an agent from your application - Amazon Bedrock"
[5]: https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-steps-ecs.html?utm_source=chatgpt.com "Deployments on an Amazon ECS Compute Platform - AWS CodeDeploy"
