# Day 38：AWS Production Architecture —— 把 Java + AI 平台真正部署到 AWS

Day 37 已经完成 AI Security。

今天开始进入 **AWS 企业生产部署阶段**。

目标不是简单地：

```text
Java → EC2
```

而是搭建：

```text
Internet
   ↓
CloudFront
   ↓
ALB
   ↓
ECS Fargate
   ↓
┌───────────────┬───────────────┐
│ Java AI API   │ MCP Server    │
└───────────────┴───────────────┘
        │
   ┌────┼───────────────┐
   ▼    ▼               ▼
  RDS  ElastiCache      S3
   │
   ▼
pgvector
   │
   ▼
Bedrock
```

---

# 1. 今天学习目标

```text
① AWS VPC
② Public / Private Subnet
③ Internet Gateway
④ NAT Gateway
⑤ ALB
⑥ ECS Fargate
⑦ ECR
⑧ RDS PostgreSQL
⑨ ElastiCache
⑩ S3
⑪ Secrets Manager
⑫ IAM
⑬ CloudWatch
⑭ Security Groups
⑮ Production Network Architecture
```

---

# 2. 最终 AWS 架构

```text
                         Internet
                            │
                            ▼
                       CloudFront
                            │
                            ▼
                           ALB
                            │
                  ┌─────────┴─────────┐
                  ▼                   ▼
             ECS Fargate         Frontend
              Private             S3
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
      RDS      Redis       S3
   PostgreSQL
      │
   pgvector

                  ECS
                   │
                   ▼
                Bedrock

                  ECS
                   │
                   ▼
               MCP Server
```

---

# 3. 为什么 ECS Fargate

你的 Java AI API：

```text
Spring Boot
```

需要：

```text
Docker
Container
Scaling
Load Balancing
```

ECS Fargate：

```text
Docker Container
       ↓
ECS
       ↓
AWS 管理底层服务器
```

你不用自己管理：

```text
EC2
OS Patching
Server Capacity
```

对于目前这套企业 AI 项目，非常适合作为第一版生产部署目标。

---

# 4. AWS Region

建议先选择：

```text
us-west-2
```

或者：

```text
us-east-1
```

实际选择原则：

```text
用户位置
+
Bedrock Model Availability
+
服务可用性
+
成本
+
合规要求
```

**不要为了“离用户近”就直接决定 Region，先确认你需要的 Bedrock 模型和 AWS 服务都在该 Region 可用。**

---

# 5. VPC

创建：

```text
10.0.0.0/16
```

例如：

```text id="9f8q2a"
VPC
10.0.0.0/16
```

可容纳：

```text
65,536 IPs
```

企业项目足够。

---

# 6. Availability Zones

至少：

```text
AZ-A
AZ-B
```

例如：

```text
us-west-2a
us-west-2b
```

生产：

```text id="m1r8c4"
Multi-AZ
```

不要：

```text id="b5w2k9"
Single AZ
```

---

# 7. Subnet

推荐：

```text
VPC
│
├── Public Subnet A
├── Public Subnet B
│
├── Private App Subnet A
├── Private App Subnet B
│
├── Private Data Subnet A
└── Private Data Subnet B
```

---

# 8. Public Subnet

放：

```text id="v7c3n1"
ALB
NAT Gateway
```

不建议放：

```text id="h2m9q6"
RDS
ECS
```

---

# 9. Private App Subnet

放：

```text id="a8q4m7"
ECS Fargate
```

例如：

```text id="6t1x9v"
10.0.10.0/24
10.0.11.0/24
```

---

# 10. Private Data Subnet

放：

```text id="p4k8m2"
RDS
ElastiCache
```

例如：

```text id="3y7q1n"
10.0.20.0/24
10.0.21.0/24
```

---

# 11. 完整 VPC

```text
                    VPC
               10.0.0.0/16
                     │
        ┌────────────┴────────────┐
        │                         │
       AZ-A                      AZ-B
        │                         │
 ┌──────┼──────┐           ┌──────┼──────┐
 ▼      ▼      ▼           ▼      ▼      ▼
Public App    Data       Public App    Data
 │     │       │           │     │       │
ALB   ECS     RDS         ALB   ECS     RDS
      │                    │
      └────────┬───────────┘
               ▼
            Bedrock
```

---

# 12. Internet Gateway

Public subnet：

```text id="k7r2p8"
Internet
 ↓
Internet Gateway
 ↓
Public Subnet
```

ALB：

```text id="s5x9m3"
Internet-facing
```

---

# 13. NAT Gateway

Private ECS 如果需要访问：

```text id="p3v8k2"
Internet
AWS APIs
External APIs
```

可以：

```text id="j6m4q9"
Private Subnet
 ↓
NAT Gateway
 ↓
Internet Gateway
 ↓
Internet
```

---

# 14. 注意 NAT Gateway 成本

NAT Gateway 是生产环境常见成本来源。

开发环境：

```text id="x9c4m2"
不要盲目创建多个 NAT Gateway
```

生产：

```text id="f5n8q1"
Multi-AZ
```

需要根据：

```text Traffic
Availability
Cost
```

平衡。

---

# 15. AWS VPC Endpoint

如果 ECS 访问 AWS 服务：

```text id="t2m7k5"
S3
ECR
CloudWatch
Secrets Manager
Bedrock
```

应该评估：

```text id="v8q3n6"
VPC Endpoint
```

这样很多流量不需要：

```text id="j4x9p2"
Private Subnet
 ↓
NAT
 ↓
Internet
```

---

# 16. Security Group

推荐：

```text id="w3k8m1"
ALB-SG
ECS-SG
RDS-SG
Redis-SG
```

---

# 17. ALB Security Group

允许：

```text id="m6p2x8"
Internet
 ↓
443
```

例如：

```text
Inbound:
443 from 0.0.0.0/0
```

如果 HTTP：

```text
80 → redirect 443
```

---

# 18. ECS Security Group

不要：

```text id="a7n3q5"
0.0.0.0/0 → 8080
```

应该：

```text id="v2k8m4"
ALB-SG
 ↓
ECS-SG
8080
```

---

# 19. RDS Security Group

只允许：

```text id="f8q3m1"
ECS-SG
 ↓
RDS-SG
5432
```

不要：

```text id="d4p7n2"
Internet
 ↓
5432
```

这是非常重要的生产安全原则。

---

# 20. Redis Security Group

只允许：

```text id="w9m4x6"
ECS-SG
 ↓
Redis-SG
6379
```

---

# 21. RDS PostgreSQL

你的 AI 平台：

```text id="n6q2v8"
RDS PostgreSQL
```

用于：

```text
Users
Documents
Memory
Workflow
Audit
Evaluation
```

同时：

```text
pgvector
```

用于：

```text
RAG
Memory
```

---

# 22. RDS 推荐

生产：

```text id="j3x7m5"
Multi-AZ
Automated Backup
Encryption
Performance Insights
CloudWatch
```

开发：

```text id="p8q4n2"
Single-AZ
```

即可降低成本。

---

# 23. PostgreSQL Schema

今天最终：

```text id="c7m2v9"
PostgreSQL
│
├── users
├── tenants
├── documents
├── document_chunks
├── document_parents
├── memories
├── agent_runs
├── agent_checkpoints
├── agent_approvals
├── rag_runs
├── mcp_tool_calls
└── evaluations
```

已经形成：

> **AI Application Database**

---

# 24. pgvector

用于：

```text id="a4n8x2"
RAG
+
Memory
```

例如：

```text id="r7m3q5"
document_chunks
     │
     └── embedding

memories
     │
     └── embedding
```

---

# 25. S3

S3 保存：

```text id="x2k8m5"
PDF
DOCX
TXT
CSV
Images
Raw documents
```

架构：

```text id="u6p3n9"
User Upload
     ↓
S3
     ↓
SQS
     ↓
Ingestion Worker
     ↓
Parse
     ↓
Chunk
     ↓
Embedding
     ↓
PostgreSQL
```

---

# 26. 为什么不要把 PDF 存 PostgreSQL

错误：

```text id="v5q9m2"
PDF binary
 ↓
PostgreSQL
```

推荐：

```text id="j8x4n7"
PDF
 ↓
S3
```

数据库只保存：

```text id="c2m6p8"
s3_key
metadata
```

---

# 27. S3 Bucket

例如：

```text id="a3v7q9"
company-ai-documents-prod
```

结构：

```text
documents/
    tenant-a/
        project-1/
            architecture.pdf

        project-2/
            api.pdf
```

---

# 28. S3 Security

必须：

```text id="y6n2m8"
Block Public Access
Encryption
Versioning
IAM
```

不要：

```text id="q4p8x3"
Public Bucket
```

---

# 29. ECR

Java Docker Image：

```text id="f9m3k7"
Docker
 ↓
ECR
 ↓
ECS
```

例如：

```text id="v2x8n5"
123456789.dkr.ecr.../ai-api
```

---

# 30. Docker Image

Day 38 开始把：

```text id="u7k4m2"
Spring Boot
```

变成：

```text id="a8p3x9"
Docker Image
```

Dockerfile：

```dockerfile id="d9l2v7"
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# 31. Java Production Image

推荐最终使用：

```text id="m5q8n2"
JRE
```

而不是完整 JDK。

进一步可以使用：

```text id="x3v7p1"
Distroless
```

减少攻击面。

---

# 32. ECS Task

例如：

```text id="k8m4q2"
CPU:
1 vCPU

Memory:
2 GB
```

第一版可以从：

```text id="s6n9x3"
1 vCPU
2 GB
```

开始。

然后根据 CloudWatch：

```text id="g2p7m5"
CPU
Memory
Latency
```

调整。

---

# 33. ECS Service

```text id="a5x8m3"
Desired Count = 2
```

部署：

```text id="q7n2v4"
AZ-A
AZ-B
```

这样：

```text
ECS Task 1 → AZ-A
ECS Task 2 → AZ-B
```

---

# 34. Auto Scaling

根据：

```text id="c9m4x7"
CPU
Memory
Request Count
Latency
```

自动：

```text id="n6p2k8"
2
 ↓
3
 ↓
4
```

流量下降：

```text id="v3q7m9"
4
 ↓
2
```

---

# 35. ALB Health Check

Spring Boot：

```text id="p8x4m2"
/actuator/health
```

ALB：

```text id="j5n7q3"
GET /actuator/health
```

如果：

```text id="x2m8v6"
HTTP 200
```

Task：

```text id="k9p3r5"
Healthy
```

---

# 36. Actuator

依赖：

```xml id="z3n7q8"
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>
        spring-boot-starter-actuator
    </artifactId>
</dependency>
```

配置：

```yaml id="p6m2x9"
management:
  endpoints:
    web:
      exposure:
        include:
          - health
          - metrics
```

生产环境不要无脑暴露所有 actuator endpoint。

---

# 37. Secrets Manager

保存：

```text id="m4q8n2"
DATABASE_PASSWORD
DATABASE_USERNAME
JWT_SECRET
EXTERNAL_API_KEY
```

应用：

```text id="v7p3x5"
ECS Task
 ↓
IAM Task Role
 ↓
Secrets Manager
 ↓
Secret
```

---

# 38. 不要把 Secret 放这里

不要：

```text id="c8m2v6"
application.yml
```

不要：

```text id="j5n9q3"
Dockerfile
```

不要：

```text id="p7x4m8"
GitHub
```

---

# 39. IAM Role

ECS Task Role：

```text id="z2q6n4"
AI API
```

只允许需要的：

```text
bedrock:InvokeModel
s3:GetObject
secretsmanager:GetSecretValue
```

具体 ARN 和 Action 按实际服务进一步收紧。

---

# 40. IAM Task Execution Role

这是：

```text id="g8m3v5"
ECS
```

用于：

```text id="u2p7x9"
Pull ECR Image
Write CloudWatch Logs
```

不要把：

```text id="n4q8m2"
Task Role
```

和：

```text id="c7x3v6"
Execution Role
```

混为一谈。

---

# 41. Bedrock

你的 Java Agent：

```text id="a5m9q2"
Spring AI
 ↓
AWS Bedrock
```

不需要：

```text id="v8x3n6"
OpenAI API key
```

如果整个应用都运行 AWS：

```text id="j2p7m4"
IAM
 ↓
Bedrock
```

非常适合企业环境。

---

# 42. Bedrock Network

ECS：

```text id="w6n3q8"
Private Subnet
```

访问 Bedrock：

```text id="f4m9x2"
AWS Network
```

可以根据 Region 和服务能力选择：

```text id="r7p3k5"
VPC Endpoint
```

或者经过：

```text id="m2x8q6"
NAT
```

具体方案要结合你的 AWS Region 和 Bedrock endpoint 能力确认。

---

# 43. ElastiCache

今天先作为：

```text id="k4n7x2"
Redis
```

使用。

适合：

```text id="m8p3q6"
Session
Cache
Rate Limit
Short-lived Workflow State
Distributed Lock
```

---

# 44. Redis 不负责长期 Memory

不要：

```text id="c2v9m5"
Agent Memory
 ↓
Redis
```

长期 Memory：

```text id="x7n4q8"
PostgreSQL
+
pgvector
```

Redis：

```text id="j3m8p2"
短期 / 高速
```

---

# 45. 完整数据流

用户上传：

```text id="f6q2v9"
PDF
 ↓
S3
 ↓
SQS
 ↓
ECS Worker
 ↓
Parser
 ↓
Contextual Chunking
 ↓
Parent/Child
 ↓
Embedding
 ↓
pgvector
```

用户提问：

```text id="n8m3x5"
Question
 ↓
ALB
 ↓
ECS AI API
 ↓
Memory
 ↓
RAG
 ↓
MCP
 ↓
Bedrock
 ↓
SSE
 ↓
React
```

---

# 46. SQS

Day 38 正式加入：

```text id="p4x7m2"
Amazon SQS
```

为什么？

上传 PDF 后不要：

```text id="v9q3n6"
HTTP Request
 ↓
Parse PDF
 ↓
Embedding
 ↓
返回
```

这样可能等待几十秒。

正确：

```text id="a8m2q5"
Upload
 ↓
S3
 ↓
SQS
 ↓
202 Accepted
```

后台异步处理。

---

# 47. Ingestion Worker

```text id="k6p3x8"
SQS
 ↓
ECS Worker
 ↓
Download S3
 ↓
Parse
 ↓
Chunk
 ↓
Embedding
 ↓
PostgreSQL
```

失败：

```text id="m4n8q2"
Retry
 ↓
Dead Letter Queue
```

---

# 48. Dead Letter Queue

如果：

```text id="c7x2m9"
PDF
 ↓
Parser Error
```

重试：

```text id="j5p8n3"
1
2
3
```

仍失败：

```text id="v9m4q7"
DLQ
```

然后人工处理。

---

# 49. Production Network

最终：

```text id="n3x8m5"
                         Internet
                            │
                        CloudFront
                            │
                            ▼
                           ALB
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
          Frontend                      ECS
              │                           │
              │                 ┌─────────┼─────────┐
              │                 ▼         ▼         ▼
              │               Agent      MCP      Worker
              │                 │
              │                 ▼
              │               Bedrock
              │
              └─────────────── S3

ECS
 │
 ├── RDS PostgreSQL
 │      └── pgvector
 │
 ├── ElastiCache Redis
 │
 ├── S3
 │
 └── SQS
```

---

# 50. Public / Private 设计

最终原则：

```text id="q5m8x2"
PUBLIC
├── ALB
└── CloudFront

PRIVATE
├── ECS
├── Worker
├── RDS
└── Redis
```

---

# 51. AWS Architecture 三层

```text id="p7n3v8"
Presentation
│
├── CloudFront
└── S3

Application
│
├── ALB
├── ECS
└── MCP

Data
│
├── RDS
├── Redis
└── S3
```

---

# 52. CI/CD

今天开始建立：

```text id="x4m9q2"
GitHub
 ↓
GitHub Actions
 ↓
Maven Test
 ↓
Docker Build
 ↓
ECR
 ↓
ECS Deployment
```

---

# 53. CI Pipeline

```yaml id="z8p3m5"
name: Build

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          java-version: '21'

      - run: ./mvnw test

      - run: ./mvnw package
```

今天先理解流程。

---

# 54. CD Pipeline

```text id="h2q7n4"
GitHub
 ↓
Test
 ↓
Build
 ↓
Docker
 ↓
ECR
 ↓
ECS
 ↓
Health Check
 ↓
Production
```

---

# 55. 不要直接部署 Production

建议：

```text id="m5x8q2"
DEV
 ↓
TEST
 ↓
STAGING
 ↓
PRODUCTION
```

每个环境：

```text id="n7p3v9"
不同配置
不同数据库
不同 Secrets
```

---

# 56. Environment

```text id="q4m8x2"
application-dev.yml
application-test.yml
application-prod.yml
```

但不要把 Secret 写里面。

：

```text id="k8p3n6"
Config
+
Secrets Manager
```

---

# 57. Deployment Strategy

ECS 推荐：

```text id="v5m2q9"
Rolling Deployment
```

进一步：

```text id="a7x3p8"
Blue/Green
```

生产环境特别适合：

```text id="j4n8m5"
New Version
 ↓
Test
 ↓
Traffic Switch
```

---

# 58. Day 38 实战任务

今天真正搭建：

```text id="s2q7m4"
AWS
│
├── VPC
├── 2 AZ
├── Public Subnet
├── Private App Subnet
├── Private Data Subnet
│
├── ALB
├── ECS Fargate
├── ECR
│
├── RDS PostgreSQL
├── ElastiCache
├── S3
├── SQS
├── Secrets Manager
│
└── CloudWatch
```

---

# 59. Day 38 项目结构

```text id="p8m3x7"
aws-java-ai-platform/
│
├── frontend/
│
├── backend/
│
├── mcp-server/
│
├── ingestion-worker/
│
├── infrastructure/
│   ├── network/
│   ├── ecs/
│   ├── rds/
│   ├── redis/
│   ├── s3/
│   ├── sqs/
│   ├── iam/
│   └── monitoring/
│
├── docker/
│
└── .github/
    └── workflows/
```

---

# 60. Day 38 AWS 检查清单

```text id="v6n2q8"
[ ] AWS Region
[ ] VPC
[ ] 2 Availability Zones
[ ] Public Subnet
[ ] Private App Subnet
[ ] Private Data Subnet
[ ] Internet Gateway
[ ] NAT Gateway
[ ] VPC Endpoint
[ ] ALB
[ ] ECS Fargate
[ ] ECR
[ ] RDS PostgreSQL
[ ] pgvector
[ ] ElastiCache
[ ] S3
[ ] SQS
[ ] DLQ
[ ] Secrets Manager
[ ] IAM
[ ] Bedrock
[ ] CloudWatch
[ ] Security Groups
[ ] Auto Scaling
[ ] Health Check
[ ] GitHub Actions
```

---

# 61. Day 38 最终架构

```text id="x9m3q7"
                               INTERNET
                                   │
                                   ▼
                              CLOUDFRONT
                                   │
                    ┌──────────────┴──────────────┐
                    ▼                             ▼
                  S3                         Application
               Frontend                         │
                                                ▼
                                               ALB
                                                │
                                  ┌─────────────┴─────────────┐
                                  ▼                           ▼
                             ECS AI API                  ECS MCP
                                  │
                         ┌────────┼────────┐
                         ▼        ▼        ▼
                       RAG      Memory    Bedrock
                         │        │
                         └────┬───┘
                              ▼
                         RDS PostgreSQL
                              │
                           pgvector

S3 Upload
   │
   ▼
  SQS
   │
   ▼
ECS Ingestion Worker
   │
   ▼
RDS / pgvector

ECS
 │
 ├── Redis
 ├── Secrets Manager
 ├── CloudWatch
 └── IAM
```

---

## Day 38 最核心的 5 条原则

**1. Internet 不应该直接访问 ECS。**

```text
Internet → ALB → ECS
```

**2. ECS、RDS、Redis 放 Private Subnet。**

**3. RDS 只允许 ECS Security Group 访问。**

**4. Secret 不进入 Git、Docker Image 或 Prompt。**

**5. AI 应用、RAG、MCP、Memory 都应该成为 AWS 可独立扩展的组件。**

到这里，你已经从 Day 1 的 AWS Java 开发环境，真正走到了：

```text
Java 21
+
Spring Boot
+
Spring AI
+
Bedrock
+
RAG
+
MCP
+
Agent Workflow
+
Memory
+
Security
+
Observability
+
AWS Production Architecture
```

**Day 39** 下一步就是把今天的架构真正“写成基础设施”：**Terraform + AWS IaC**，从零创建 VPC、Subnets、Security Groups、ECR、ECS、RDS、S3、SQS、IAM，并做到 `terraform plan → apply → destroy`，以后换 AWS 环境不需要手工点击 Console。
