# Day 40：CI/CD 自动部署 —— GitHub → AWS ECS → Production

Day 39 已经把 AWS 基础设施代码化：

```text
Terraform
 ↓
VPC
ECS
RDS
S3
IAM
CloudWatch
```

今天把**代码发布流程自动化**。

最终做到：

```text
Developer
   │
   ▼
Git Push
   │
   ▼
GitHub
   │
   ▼
CI
├── Compile
├── Unit Test
├── Integration Test
├── Security Scan
└── Docker Build
   │
   ▼
ECR
   │
   ▼
Staging ECS
   │
   ▼
Smoke Test
   │
   ▼
Production Approval
   │
   ▼
Production ECS
   │
   ▼
Health Check
   │
   ▼
Success / Rollback
```

---

# 1. Day 40 学习目标

今天掌握：

```text
① GitHub Actions
② CI
③ CD
④ Maven
⑤ Docker
⑥ ECR
⑦ ECS Deployment
⑧ OIDC
⑨ AWS IAM Role
⑩ Environment
⑪ Staging
⑫ Production
⑬ Health Check
⑭ Rolling Deployment
⑮ Rollback
```

---

# 2. CI/CD 到底是什么

### CI

Continuous Integration：

```text
Git Push
 ↓
Build
 ↓
Test
 ↓
Security Scan
```

### CD

Continuous Delivery / Deployment：

```text
Build
 ↓
Docker
 ↓
ECR
 ↓
ECS
 ↓
Deploy
```

---

# 3. 今天的最终 Pipeline

```text
                   GitHub
                      │
                      ▼
                 Pull Request
                      │
                      ▼
                    CI
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
      Maven         Tests        Security
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                  Docker Build
                      │
                      ▼
                     ECR
                      │
                      ▼
                  Staging ECS
                      │
                      ▼
                  Smoke Test
                      │
                      ▼
               Production Approval
                      │
                      ▼
                 Production ECS
                      │
                      ▼
                 Health Check
                      │
                 ┌────┴────┐
                 ▼         ▼
               PASS      FAIL
                 │         │
                 ▼         ▼
              Success    Rollback
```

---

# 4. Repository 结构

今天整理成：

```text
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
│
├── docker/
│
└── .github/
    └── workflows/
        ├── ci.yml
        ├── staging.yml
        └── production.yml
```

---

# 5. Git Branch Strategy

建议：

```text
main
 │
 ├── develop
 │
 └── feature/*
```

例如：

```text
feature/rag-improvement
feature/mcp-tool
feature/memory
```

流程：

```text
feature
 ↓
Pull Request
 ↓
CI
 ↓
develop
 ↓
Staging
 ↓
main
 ↓
Production
```

---

# 6. Pull Request

开发：

```bash
git checkout -b feature/memory-improvement
```

修改：

```text
Memory
RAG
Agent
```

然后：

```bash
git add .
git commit -m "Improve memory retrieval"
git push
```

GitHub：

```text
Pull Request
```

---

# 7. CI 必须先运行

PR：

```text
Pull Request
 ↓
GitHub Actions
 ↓
Build
 ↓
Test
```

如果：

```text
Test Failed
```

就：

```text
❌ PR blocked
```

不能合并。

---

# 8. GitHub Actions 基础

`.github/workflows/ci.yml`

```yaml
name: CI

on:
  pull_request:
    branches:
      - main
      - develop

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'

      - name: Test
        run: ./mvnw test

      - name: Package
        run: ./mvnw package
```

---

# 9. Maven Pipeline

执行：

```bash
./mvnw clean verify
```

包含：

```text
clean
 ↓
compile
 ↓
test
 ↓
package
 ↓
verify
```

---

# 10. Unit Test

例如：

```java
@Test
void shouldCalculateScore() {

    var result =
        service.calculate("AWS");

    assertEquals(
        "AWS",
        result
    );
}
```

CI：

```text
Unit Test
 ↓
PASS
```

---

# 11. Integration Test

企业 AI 应用不能只做 Unit Test。

需要：

```text
Spring Boot
 ↓
PostgreSQL
 ↓
Redis
```

测试：

```text
RAG
Memory
Workflow
MCP
```

---

# 12. Test Pyramid

建议：

```text
              E2E
             /  \
          Integration
         /          \
      Unit          Unit
```

比例大致：

```text
Unit          70%
Integration   20%
E2E           10%
```

不是绝对规则，但非常适合作为第一版目标。

---

# 13. Docker Build

测试成功：

```text
Maven
 ↓
JAR
 ↓
Docker
```

Dockerfile：

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT [
  "java",
  "-jar",
  "app.jar"
]
```

---

# 14. Docker Tag

不要只用：

```text
latest
```

生产建议：

```text
ai-api:git-abc123
```

例如：

```text
ai-api:8f42c91
```

这样可以知道：

> 当前 ECS 到底运行哪一次代码。

---

# 15. Git SHA

GitHub Actions：

```yaml
env:
  IMAGE_TAG: ${{ github.sha }}
```

然后：

```text
ECR
 ├── 8f42c91
 ├── 5e22aa1
 └── 92bd331
```

---

# 16. ECR Login

GitHub：

```yaml
- name: Login to ECR
  uses: aws-actions/amazon-ecr-login@v2
```

然后：

```text
Docker
 ↓
ECR
```

---

# 17. AWS OIDC

这是今天非常重要的内容。

不要：

```text
GitHub
 ↓
AWS Access Key
```

推荐：

```text
GitHub Actions
       │
       ▼
     OIDC
       │
       ▼
AWS IAM Role
       │
       ▼
AWS
```

---

# 18. 为什么 OIDC 更好

传统：

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

需要长期保存。

OIDC：

```text
GitHub Job
 ↓
Temporary Token
 ↓
AWS IAM
```

短期凭证。

安全性明显更好。

---

# 19. GitHub IAM Role

建立：

```text
GitHubActionsDeploymentRole
```

允许：

```text
ECR
ECS
CloudWatch
```

但：

> 生产环境不要直接给 `AdministratorAccess`。

---

# 20. GitHub OIDC Trust Policy

概念：

```json
{
  "Effect": "Allow",
  "Principal": {
    "Federated": "github-actions"
  },
  "Action": "sts:AssumeRoleWithWebIdentity"
}
```

实际 Trust Policy 必须限制：

```text
repository
branch
environment
```

例如只允许：

```text
main
```

部署 Production。

---

# 21. Environment

GitHub 建立：

```text
dev
staging
production
```

Production：

```text
Required Reviewers
```

于是：

```text
main
 ↓
GitHub
 ↓
Production Approval
 ↓
Deploy
```

---

# 22. Staging

每次：

```text
develop
 ↓
CI
 ↓
Docker
 ↓
ECR
 ↓
Staging ECS
```

自动部署。

---

# 23. Production

只有：

```text
main
```

才能：

```text
Production
```

流程：

```text
main
 ↓
CI
 ↓
Docker
 ↓
ECR
 ↓
Production Approval
 ↓
ECS
```

---

# 24. ECS Deployment

ECS Task Definition：

```text
image:
123456.dkr.ecr.../ai-api:8f42c91
```

更新：

```text
Task Definition
 ↓
ECS Service
```

ECS：

```text
Old Task
+
New Task
```

---

# 25. Rolling Deployment

例如：

```text
Desired Count = 4
```

旧：

```text
A B C D
```

更新：

```text
A B C
+
New E
```

继续：

```text
A B
+
E F
```

最终：

```text
E F G H
```

---

# 26. Rolling Deployment 参数

例如：

```text
minimumHealthyPercent = 100
maximumPercent = 200
```

意思：

```text
最少保持 100% Healthy
最多可以达到 200%
```

生产环境具体值根据启动时间、容量和成本调整。

---

# 27. Health Check

ALB：

```text
GET /actuator/health
```

Spring Boot：

```json
{
  "status": "UP"
}
```

如果：

```text
HTTP 200
```

ECS：

```text
Healthy
```

---

# 28. Deployment Failure

如果新版本：

```text
/actuator/health
```

返回：

```text
500
```

ALB：

```text
UNHEALTHY
```

ECS：

```text
Deployment Failed
```

这时候不能继续扩大流量。

---

# 29. 自动 Rollback

ECS Deployment Circuit Breaker：

```text
New Version
 ↓
Health Check
 ↓
FAIL
 ↓
Circuit Breaker
 ↓
Rollback
```

这是生产环境非常重要的能力。

---

# 30. Rollback

假设：

```text
Current:
8f42c91
```

部署：

```text
New:
ab99231
```

发现：

```text
RAG Bug
```

Rollback：

```text
ab99231
 ↓
8f42c91
```

---

# 31. 为什么 Git SHA 很重要

如果只：

```text
ai-api:latest
```

你不知道：

```text
到底是哪一个版本。
```

使用：

```text
ai-api:8f42c91
```

可以直接追踪：

```text
Git
 ↓
Docker
 ↓
ECR
 ↓
ECS
```

---

# 32. Git → Production Traceability

最终做到：

```text
Git Commit
     │
     ▼
GitHub Actions
     │
     ▼
Docker Image
     │
     ▼
ECR
     │
     ▼
ECS Task Definition
     │
     ▼
Production
```

这就是企业 DevOps 很重要的：

> **Artifact Traceability**

---

# 33. 完整 CI

```yaml
name: CI

on:
  pull_request:
    branches:
      - main
      - develop

jobs:
  test:

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - name: Java 21
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'

      - name: Test
        run: ./mvnw clean verify

      - name: Docker Build
        run: docker build -t ai-api:${{ github.sha }} .
```

---

# 34. 完整 Staging Pipeline

```text
develop
   │
   ▼
CI
   │
   ▼
Test
   │
   ▼
Docker
   │
   ▼
ECR
   │
   ▼
ECS Staging
   │
   ▼
Smoke Test
```

---

# 35. Smoke Test

部署后：

```bash
curl https://staging.example.com/actuator/health
```

检查：

```json
{
  "status": "UP"
}
```

再测试：

```text
POST /api/chat
```

例如：

```json
{
  "message": "What is ECS?"
}
```

应该：

```text
HTTP 200
```

---

# 36. AI Smoke Test

普通 Health Check 不够。

AI 应用还应该：

```text
Health
 ↓
LLM
 ↓
RAG
 ↓
Memory
 ↓
MCP
```

至少测试：

```text
1. Simple LLM
2. RAG
3. Tool Calling
```

---

# 37. AI Deployment Verification

例如：

```text
Test 1
What is ECS?

Test 2
Explain our production architecture.

Test 3
Check order-api status.
```

确保：

```text
LLM ✓
RAG ✓
Memory ✓
MCP ✓
```

---

# 38. Production Pipeline

```text
main
 │
 ▼
CI
 │
 ├── Unit Test
 ├── Integration Test
 ├── Security Scan
 └── Docker Build
 │
 ▼
ECR
 │
 ▼
Approval
 │
 ▼
ECS Production
 │
 ▼
Health Check
 │
 ▼
Smoke Test
 │
 ├── PASS → SUCCESS
 │
 └── FAIL → ROLLBACK
```

---

# 39. Security Scan

今天加入：

```text
Container Scan
Dependency Scan
Secret Scan
```

检查：

```text
Java dependencies
Docker image
Git repository
```

例如：

```text
Spring dependency
 ↓
Known CVE
 ↓
Pipeline FAIL
```

---

# 40. Dependency Scan

可以加入：

```text
OWASP Dependency-Check
```

或者 GitHub 自带的：

```text
Dependabot
CodeQL
Secret Scanning
```

企业环境可以组合使用。

---

# 41. Container Scan

ECR 可以对容器镜像进行漏洞扫描。

Pipeline：

```text
Docker
 ↓
ECR
 ↓
Vulnerability Scan
```

如果：

```text
CRITICAL
```

生产部署应该：

```text
BLOCK
```

具体阻断阈值按企业安全政策制定。

---

# 42. AI Security Test

Day 37 的安全测试今天进入 CI：

```text
Prompt Injection
RAG Injection
Tenant Isolation
Tool Authorization
Secret Leakage
```

例如：

```text
CI
 ↓
Security Tests
 ↓
PASS
```

才能部署。

---

# 43. AI Evaluation 也进入 CI

Day 36：

```text
Evaluation
```

今天：

```text
Git Push
 ↓
Evaluation
```

例如：

```text
Groundedness >= 90%
Correctness >= 90%
```

否则：

```text
❌ Deployment Blocked
```

---

# 44. 这一步非常重要

普通 Java：

```text
Test
 ↓
Deploy
```

AI 应用：

```text
Test
 ↓
Security
 ↓
RAG Evaluation
 ↓
Agent Evaluation
 ↓
Cost Check
 ↓
Deploy
```

这叫：

> **AI CI/CD / LLMOps**

---

# 45. Production Quality Gate

建立：

```text
Quality Gate
```

例如：

```text
Unit Test       = PASS
Integration     = PASS
Security        = PASS
Groundedness    >= 90%
Correctness     >= 90%
Tool Accuracy   >= 95%
```

任何一项失败：

```text
STOP
```

---

# 46. Cost Gate

例如：

```text
Average Cost / Request
<
$0.05
```

如果新版本：

```text
$0.05
 ↓
$0.18
```

Pipeline：

```text
FAIL
```

这对 Agent 特别重要。

---

# 47. Day 40 Pipeline

最终：

```text
Git Push
    │
    ▼
GitHub Actions
    │
    ▼
Compile
    │
    ▼
Unit Test
    │
    ▼
Integration Test
    │
    ▼
Security Scan
    │
    ▼
AI Evaluation
    │
    ▼
Docker Build
    │
    ▼
ECR
    │
    ▼
Staging
    │
    ▼
Smoke Test
    │
    ▼
Production Approval
    │
    ▼
Production ECS
    │
    ▼
Health Check
    │
 ┌──┴──┐
 ▼     ▼
PASS  FAIL
 │     │
 ▼     ▼
DONE  ROLLBACK
```

---

# 48. GitHub Actions Production 示例

核心结构：

```yaml
name: Production

on:
  push:
    branches:
      - main

jobs:

  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'

      - run: ./mvnw clean verify


  docker:
    needs: test
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build image
        run: |
          docker build \
            -t ai-api:${{ github.sha }} .


  deploy:
    needs: docker
    runs-on: ubuntu-latest
    environment: production

    steps:
      - name: Deploy
        run: echo "Deploy to ECS"
```

这里先理解 Pipeline 结构；真正部署时要补上 AWS OIDC、ECR push、ECS task-definition 更新和 service deployment。

---

# 49. Production Approval

GitHub：

```text
Settings
 ↓
Environments
 ↓
production
 ↓
Required reviewers
```

于是：

```text
main
 ↓
CI
 ↓
Waiting
 ↓
Engineer Approves
 ↓
Production
```

---

# 50. Blue/Green

Day 40 先理解：

```text
BLUE
Current Production

GREEN
New Version
```

部署：

```text
BLUE
 │
 ├───────────────┐
 │               │
 ▼               ▼
Old             New
             GREEN
```

测试：

```text
GREEN
 ↓
Smoke Test
```

成功：

```text
Traffic
 ↓
GREEN
```

失败：

```text
Traffic
 ↓
BLUE
```

---

# 51. Rolling vs Blue/Green

|       | Rolling | Blue/Green |
| ----- | ------- | ---------- |
| 成本    | 较低      | 较高         |
| 部署复杂度 | 低       | 高          |
| 回滚    | 较慢      | 快          |
| 生产安全  | 高       | 更高         |
| 推荐    | 一般生产    | 关键生产       |

你的学习顺序：

```text
Day 40
Rolling

以后
Blue/Green
```

---

# 52. Database Migration

这里有一个非常容易踩坑的问题。

部署：

```text
Java v1
 ↓
Database v1
```

然后：

```text
Java v2
 ↓
Database v2
```

不能直接：

```text
破坏旧 Schema
```

推荐：

```text
Expand
 ↓
Deploy
 ↓
Migrate
 ↓
Contract
```

---

# 53. Flyway

Java 项目加入：

```text
Flyway
```

例如：

```text
V1__create_users.sql
V2__create_memories.sql
V3__create_agent_runs.sql
```

部署：

```text
Application
 ↓
Flyway
 ↓
PostgreSQL
```

---

# 54. 不要在生产手工改 DB

错误：

```text
AWS Console
 ↓
RDS
 ↓
手工 SQL
```

应该：

```text
Git
 ↓
Migration
 ↓
CI/CD
 ↓
Production
```

这样数据库也进入版本控制。

---

# 55. Day 40 完整 DevOps 架构

```text
                           GitHub
                              │
                       Pull Request
                              │
                              ▼
                            CI
                              │
          ┌───────────────────┼──────────────────┐
          ▼                   ▼                  ▼
       Maven               Security          AI Eval
          │                   │                  │
          └───────────────────┼──────────────────┘
                              ▼
                         Docker Build
                              │
                              ▼
                             ECR
                              │
                              ▼
                          STAGING
                              │
                              ▼
                        Smoke Testing
                              │
                              ▼
                       Production Gate
                              │
                              ▼
                         PRODUCTION
                              │
                              ▼
                         ECS Fargate
                              │
                    ┌─────────┼─────────┐
                    ▼         ▼         ▼
                  RAG      Memory      MCP
                    │         │         │
                    └─────────┼─────────┘
                              ▼
                           Bedrock
                              │
                              ▼
                         CloudWatch
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
                 Metrics              Trace
```

---

# 56. Day 40 项目目录

最终：

```text
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
│
├── docker/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── e2e/
│   └── ai-evaluation/
│
└── .github/
    └── workflows/
        ├── ci.yml
        ├── staging.yml
        └── production.yml
```

---

# 57. Day 40 必做任务

### Task 1

建立：

```text
.github/workflows/ci.yml
```

实现：

```text
Checkout
 ↓
Java 21
 ↓
Maven Test
 ↓
Package
```

---

### Task 2

Docker：

```text
Spring Boot
 ↓
Docker Image
```

---

### Task 3

Push：

```text
Docker
 ↓
ECR
```

Tag：

```text
git SHA
```

---

### Task 4

ECS：

```text
ECR
 ↓
ECS Staging
```

---

### Task 5

Health Check：

```text
/actuator/health
```

---

### Task 6

Smoke Test：

```text
/api/chat
```

---

### Task 7

Production：

```text
main
 ↓
Approval
 ↓
ECS
```

---

### Task 8

Rollback：

```text
Bad Version
 ↓
Previous Version
```

---

# 58. Day 40 验收标准

今天完成后，你应该能做到：

```text
[✓] git push
[✓] GitHub Actions 自动启动
[✓] Maven 编译
[✓] Unit Test
[✓] Integration Test
[✓] Security Scan
[✓] Docker Build
[✓] ECR Push
[✓] ECS Staging Deploy
[✓] Health Check
[✓] AI Smoke Test
[✓] Production Approval
[✓] Production Deploy
[✓] ECS Health Check
[✓] Rollback
```

---

# 59. 现在你的平台已经非常完整

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
Memory
        ↓
Day 36
Observability
        ↓
Day 37
Security
        ↓
Day 38
AWS Production Architecture
        ↓
Day 39
Terraform
        ↓
Day 40
CI/CD + LLMOps
```

最终：

```text
                 ENTERPRISE AI PLATFORM

                         GitHub
                           │
                        CI/CD
                           │
                       Terraform
                           │
                           ▼
                          AWS
                           │
                     CloudFront
                           │
                          ALB
                           │
                     ECS Fargate
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
    Spring Boot           MCP              Worker
        │
   ┌────┼─────┐
   ▼    ▼     ▼
  RAG Memory Bedrock
   │    │
   └────┼─────┘
        ▼
 PostgreSQL
 + pgvector

        +
        
Security
Observability
Evaluation
Auto Scaling
Rollback
```

### Day 40 最核心的一句话

以前是：

```text
Developer
 ↓
手工部署
```

现在变成：

```text
Developer
 ↓
Git Push
 ↓
Automated Test
 ↓
Security
 ↓
AI Evaluation
 ↓
Docker
 ↓
ECR
 ↓
Staging
 ↓
Approval
 ↓
Production
 ↓
Health Check
 ↓
Automatic Rollback
```

这就是一套真正可以用于 **美国企业 Java + AI Engineer 项目开发/求职面试** 的 CI/CD + LLMOps 基础能力。

**Day 41** 下一阶段建议开始做 **Kubernetes / Amazon EKS：把 ECS Fargate 架构升级到 Kubernetes，学习 Pod、Deployment、Service、Ingress、ConfigMap、Secret、HPA、Helm，并比较 EKS vs ECS，最终知道企业什么时候应该选择 EKS。**
