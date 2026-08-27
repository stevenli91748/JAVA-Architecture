# Day 59：AWS Java + AI CI/CD + DevSecOps

Day 58 已经完成：

```text
OpenTelemetry
Prometheus
Grafana
CloudWatch
Tracing
Metrics
Logs
AIOps
```

今天解决最后一个关键问题：

> **代码写完以后，如何自动测试 → 安全扫描 → AI Evaluation → Docker → AWS → Canary → 自动回滚？**

最终形成：

```text
GitHub
   ↓
Pull Request
   ↓
CI
   ↓
Test
   ↓
Security Scan
   ↓
AI Evaluation
   ↓
Docker
   ↓
ECR
   ↓
EKS
   ↓
Canary
   ↓
Observability
   ↓
Production
```

---

# 1. Day 59 最终 CI/CD 架构

```text
Developer
    │
    ▼
GitHub
    │
    ▼
Pull Request
    │
    ▼
GitHub Actions
    │
    ├── Java Test
    ├── Integration Test
    ├── Security Scan
    ├── AI Evaluation
    ├── Docker Build
    └── Image Scan
             │
             ▼
            ECR
             │
             ▼
            EKS
             │
       ┌─────┴─────┐
       ▼           ▼
    Canary       Stable
       │
       ▼
 OpenTelemetry
       │
       ▼
   Metrics
       │
       ▼
    Quality
      Gate
       │
   ┌───┴────┐
   ▼        ▼
Success   Failure
   │        │
   ▼        ▼
100%      Rollback
```

---

# 2. CI 和 CD

### CI

Continuous Integration：

```text
Code
 ↓
Build
 ↓
Test
 ↓
Security
 ↓
Quality
```

### CD

Continuous Delivery/Deployment：

```text
Build
 ↓
Deploy
 ↓
Canary
 ↓
Production
```

---

# 3. Git Branch

推荐：

```text
main
develop
feature/*
release/*
```

简单企业项目：

```text
main
feature/*
```

就够了。

---

# 4. Pull Request

开发：

```bash
git checkout -b feature/ai-gateway
```

完成：

```bash
git push origin feature/ai-gateway
```

然后：

```text
GitHub
 ↓
Pull Request
 ↓
CI
```

---

# 5. PR Quality Gate

PR 必须通过：

```text
[✓] Compile
[✓] Unit Test
[✓] Integration Test
[✓] Security Scan
[✓] AI Evaluation
[✓] Code Quality
```

否则：

```text
Merge = BLOCKED
```

---

# 6. Java Build

使用：

```text
Java 21
Spring Boot
Maven
```

CI：

```bash
./mvnw clean verify
```

---

# 7. Maven Pipeline

```text
mvn clean
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

# 8. Unit Test

例如：

```java
@Test
void shouldRouteAgentToPremiumModel() {

    AiRequest request =
        new AiRequest(
            "tenant-a",
            "user-1",
            "agent",
            null,
            messages,
            0.2,
            1000
        );

    ModelConfig model =
        router.route(request);

    assertEquals(
        "premium",
        model.name()
    );
}
```

---

# 9. Integration Test

不要只测试：

```text
Java method
```

还需要：

```text
Spring Boot
+
PostgreSQL
+
Redis
+
SQS
```

一起测试。

---

# 10. Testcontainers

非常适合你的 Java 项目。

测试：

```text
JUnit
   │
   ├── PostgreSQL Container
   ├── Redis Container
   └── Application
```

例如：

```java
@Testcontainers
class RagIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres =
        new PostgreSQLContainer<>(
            "postgres:17"
        );
}
```

---

# 11. 为什么 Testcontainers？

避免：

```text
开发电脑
 ↓
我的 PostgreSQL
```

导致：

```text
Works on my machine
```

Testcontainers：

```text
CI
 ↓
Fresh PostgreSQL
 ↓
Test
```

环境更加一致。

---

# 12. AI Integration Test

不要每次 CI 都大量调用真实 LLM。

否则：

```text
PR
 ↓
100 AI Calls
 ↓
$$$$
```

推荐：

```text
Unit
 ↓
Mock LLM
 ↓
Integration
 ↓
Small AI Evaluation Dataset
```

---

# 13. AI Evaluation

Day 53：

```text
Question
Expected
Actual
Score
```

CI：

```text
AI Evaluation
 ↓
Score
 ↓
Quality Gate
```

例如：

```text
Faithfulness >= 0.90
Citation >= 0.95
```

具体阈值应该由你的业务基线决定。

---

# 14. Security Scan

至少：

```text
Dependency Scan
Secret Scan
Container Scan
SAST
```

---

# 15. Secret Scan

防止：

```text
AWS Secret
OpenAI Key
Database Password
```

进入 GitHub。

例如检查：

```text
*.env
application-prod.yml
```

不要提交真实 Secret。

---

# 16. Dependency Scan

Java：

```text
Spring Boot
Jackson
Netty
Log4j
```

任何依赖可能存在 CVE。

CI：

```text
Dependency
 ↓
CVE Scan
 ↓
Risk
 ↓
BLOCK
```

---

# 17. Container Scan

Docker Image：

```text
ai-gateway:1.0.0
```

进入：

```text
ECR
```

然后扫描：

```text
OS Packages
Java Dependencies
Known Vulnerabilities
```

---

# 18. Dockerfile

Java：

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

生产环境进一步建议使用：

```text
非 root 用户
最小 JRE/JDK 镜像
固定基础镜像版本
SBOM
```

---

# 19. Docker Build

```bash
docker build \
  -t ai-gateway:1.0.0 .
```

---

# 20. ECR

AWS：

```text
Amazon ECR
```

保存：

```text
ai-gateway
agent-worker
document-worker
rag-service
```

---

# 21. Image Tag

不要只：

```text
latest
```

建议：

```text
ai-gateway:1.4.2
```

或者：

```text
ai-gateway:${GIT_SHA}
```

例如：

```text
ai-gateway:a82f7c1
```

这样可以准确回滚。

---

# 22. ECR Push

```bash
docker tag \
  ai-gateway:1.0.0 \
  <account>.dkr.ecr.<region>.amazonaws.com/ai-gateway:1.0.0

docker push \
  <account>.dkr.ecr.<region>.amazonaws.com/ai-gateway:1.0.0
```

---

# 23. GitHub Actions

建立：

```text
.github/
└── workflows/
    ├── ci.yml
    ├── security.yml
    └── deploy.yml
```

---

# 24. CI Workflow

```yaml
name: CI

on:
  pull_request:
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

      - name: Test
        run: ./mvnw clean verify
```

---

# 25. CI 完整流程

```text
Checkout
   ↓
Setup Java
   ↓
Maven
   ↓
Unit Test
   ↓
Integration Test
   ↓
Security
   ↓
AI Evaluation
```

---

# 26. GitHub OIDC

非常重要。

不要：

```text
GitHub
 ↓
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

长期保存。

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
ECR / EKS
```

---

# 27. GitHub OIDC

核心思想：

```text
GitHub
证明：
"I'm workflow from repository X"
        │
        ▼
AWS IAM
        │
        ▼
临时 Credentials
```

这样避免长期 AWS Key。

---

# 28. IAM 最小权限

CI Role：

```text
允许：
ECR Push
EKS Deploy
```

不应该：

```text
AdministratorAccess
```

---

# 29. Environment

GitHub：

```text
development
staging
production
```

Production：

```text
approval required
```

推荐：

```text
main
 ↓
staging
 ↓
approval
 ↓
production
```

---

# 30. Terraform

AWS 基础设施不要手动点击创建。

使用：

```text
Terraform
```

管理：

```text
VPC
EKS
ECR
RDS
ElastiCache
S3
SQS
EventBridge
IAM
CloudWatch
```

---

# 31. Terraform Architecture

```text
terraform/
├── modules/
│   ├── vpc
│   ├── eks
│   ├── rds
│   ├── redis
│   ├── s3
│   └── sqs
│
└── environments/
    ├── dev
    ├── staging
    └── prod
```

---

# 32. Terraform Workflow

```text
Developer
 ↓
terraform plan
 ↓
PR
 ↓
Review
 ↓
terraform apply
```

生产：

```text
Apply
 ↓
Approval
```

---

# 33. Terraform State

不要：

```text
local terraform.tfstate
```

生产推荐：

```text
S3
 +
state locking
```

具体 locking 方案应采用当前 Terraform/AWS 推荐配置，而不是沿用旧的 DynamoDB-only 教程。

---

# 34. Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ai-gateway

spec:

  replicas: 3

  selector:
    matchLabels:
      app: ai-gateway

  template:

    metadata:
      labels:
        app: ai-gateway

    spec:

      containers:

        - name: ai-gateway

          image:
            <ECR_IMAGE>

          ports:
            - containerPort: 8080
```

---

# 35. Production 不要只使用 1 Pod

```text
❌
replicas: 1
```

至少：

```text
API
 ├── Pod 1
 ├── Pod 2
 └── Pod 3
```

具体副本数根据负载测试确定。

---

# 36. Readiness

Kubernetes：

```text
Readiness
```

判断：

> 这个 Pod 能不能接收流量？

例如：

```text
/actuator/health/readiness
```

---

# 37. Liveness

判断：

> 这个 Pod 是否已经死掉？

```text
/actuator/health/liveness
```

如果失败：

```text
Kubernetes
 ↓
Restart
```

---

# 38. Deployment Strategy

最基本：

```text
Rolling Update
```

```text
Old
██████████

New
██
```

逐步：

```text
Old ↓
New ↑
```

---

# 39. Canary Deployment

更安全：

```text
Stable
95%

Canary
5%
```

然后：

```text
5%
 ↓
10%
 ↓
25%
 ↓
50%
 ↓
100%
```

---

# 40. Canary 必须结合 Day 58

不是：

```text
5%
 ↓
等待
 ↓
100%
```

而是：

```text
5%
 ↓
Observe
 ↓
Metrics
 ↓
Trace
 ↓
Error
 ↓
Latency
 ↓
AI Quality
 ↓
Decision
```

---

# 41. Canary Metrics

至少：

```text
Error Rate
P95
P99
LLM Failure
Fallback
Token Cost
AI Quality
```

---

# 42. Canary Failure

例如：

```text
Stable Error = 0.5%

Canary Error = 8%
```

自动：

```text
CANARY FAILED
 ↓
Rollback
```

---

# 43. AI Canary

不仅测试：

```text
HTTP
```

还测试：

```text
LLM Quality
```

例如新模型：

```text
Model B
```

Evaluation：

```text
Faithfulness
90%
```

旧模型：

```text
Model A
95%
```

则：

```text
Model B
 ↓
CANARY BLOCK
```

---

# 44. AI Model Deployment Pipeline

```text
New Model
   ↓
Offline Evaluation
   ↓
Security Evaluation
   ↓
5% Traffic
   ↓
Production Metrics
   ↓
AI Quality
   ↓
Approve
   ↓
100%
```

---

# 45. Rollback

应用：

```text
v1.4
 ↓
v1.5
 ↓
Problem
```

回滚：

```text
v1.4
```

因为 Image：

```text
ai-gateway:a82f7c1
```

可以精确定位。

---

# 46. Database Migration

Java 企业应用通常：

```text
Flyway
```

例如：

```text
V1__create_users.sql
V2__create_documents.sql
V3__create_ai_usage.sql
V4__create_jobs.sql
```

---

# 47. Migration Pipeline

```text
Deploy
 ↓
Flyway
 ↓
DB Migration
 ↓
Application
```

生产必须谨慎处理：

```text
Breaking Changes
```

---

# 48. Zero-Downtime Migration

不要：

```text
Drop Column
```

第一步就执行。

推荐：

```text
1. Add new column
2. Deploy code
3. Backfill
4. Switch reads/writes
5. Remove old column later
```

---

# 49. Blue-Green

另一种部署：

```text
Blue = Current

Green = New
```

测试：

```text
Green
```

成功：

```text
Traffic
Blue
 ↓
Green
```

优点：

```text
Rollback Fast
```

缺点：

```text
Infrastructure Cost Higher
```

---

# 50. Canary vs Blue-Green

|          | Canary | Blue-Green |
| -------- | -----: | ---------: |
| 风险控制     |  ⭐⭐⭐⭐⭐ |       ⭐⭐⭐⭐ |
| 成本       |   ⭐⭐⭐⭐ |         ⭐⭐ |
| Rollback |   ⭐⭐⭐⭐ |      ⭐⭐⭐⭐⭐ |
| AI Model |  ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |
| 流量控制     |  ⭐⭐⭐⭐⭐ |        ⭐⭐⭐ |

你的 AI 平台：

> **优先 Canary。**

---

# 51. DevSecOps

今天最终 Pipeline：

```text
Code
 ↓
Build
 ↓
Unit Test
 ↓
Integration Test
 ↓
SAST
 ↓
Dependency Scan
 ↓
Secret Scan
 ↓
AI Evaluation
 ↓
Docker
 ↓
Image Scan
 ↓
ECR
 ↓
Deploy Staging
 ↓
Integration Test
 ↓
Canary
 ↓
Observability
 ↓
Production
```

---

# 52. Quality Gate

每一步都可以：

```text
PASS
FAIL
```

例如：

```text
Unit Test        PASS
Security         PASS
AI Evaluation    FAIL
```

结果：

```text
DEPLOY = BLOCKED
```

---

# 53. AI Quality Gate

例如：

```text
Faithfulness >= 90%
Citation >= 95%
Safety >= 99%
```

如果：

```text
Citation = 87%
```

则：

```text
❌ BLOCK
```

---

# 54. Security Quality Gate

例如：

```text
Critical CVE = 0
High CVE <= policy threshold
Secrets = 0
```

如果：

```text
Secret Detected
```

立即：

```text
❌ BLOCK
```

---

# 55. Container Quality Gate

```text
Image
 ↓
Scan
 ↓
Critical CVE
```

如果存在：

```text
Critical
```

不要：

```text
EKS
```

---

# 56. Supply Chain Security

Day 59 开始进入：

```text
SBOM
Image Signing
Provenance
```

SBOM：

> Software Bill of Materials

知道：

```text
这个 Docker Image
里面到底有哪些依赖？
```

---

# 57. Image Signing

目标：

```text
Build
 ↓
Sign
 ↓
ECR
 ↓
EKS
```

EKS：

```text
只允许可信 Image
```

这是更成熟的企业供应链安全模式。

---

# 58. Secrets

CI：

```text
GitHub
 ↓
OIDC
 ↓
AWS IAM
```

Runtime：

```text
EKS
 ↓
IAM Role
 ↓
Secrets Manager
```

完整：

```text
No Long-lived AWS Keys
```

---

# 59. 环境隔离

推荐：

```text
AWS Account
│
├── Dev
├── Staging
└── Production
```

如果规模允许：

> **Production 最好独立 AWS Account。**

---

# 60. Dev Environment

```text
Developer
 ↓
GitHub
 ↓
CI
 ↓
Dev EKS
```

---

# 61. Staging

```text
main
 ↓
CI
 ↓
Staging
 ↓
Integration
 ↓
AI Evaluation
```

---

# 62. Production

```text
Approval
 ↓
Canary
 ↓
5%
 ↓
Observability
 ↓
25%
 ↓
50%
 ↓
100%
```

---

# 63. GitHub Actions 完整 Pipeline

```yaml
name: AI Platform CI/CD

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

      - name: Unit and Integration Test
        run: ./mvnw clean verify

      - name: AI Evaluation
        run: ./mvnw test -Pai-evaluation

  build:

    needs: test

    runs-on: ubuntu-latest

    steps:

      - uses: actions/checkout@v4

      - name: Build Image
        run: |
          docker build \
            -t ai-gateway:${{ github.sha }} .

      - name: Security Scan
        run: |
          trivy image \
            ai-gateway:${{ github.sha }}
```

---

# 64. Deploy

```text
GitHub Actions
 ↓
OIDC
 ↓
AWS IAM
 ↓
ECR
 ↓
EKS
```

---

# 65. Kubernetes GitOps

进一步升级：

```text
GitHub
 ↓
Application Repo

GitOps Repo
 ↓
Kubernetes Manifest
```

然后：

```text
Argo CD
 ↓
EKS
```

---

# 66. GitOps

目标：

```text
Git
=
Single Source of Truth
```

例如：

```text
deployment.yaml

image:
  tag: a82f7c1
```

Argo CD：

```text
Git
 ↓
Compare
 ↓
Sync
 ↓
EKS
```

---

# 67. Day 59 推荐工具链

```text
Language
Java 21

Framework
Spring Boot

Build
Maven

Source
GitHub

CI
GitHub Actions

Container
Docker

Registry
ECR

Infrastructure
Terraform

Runtime
EKS

Deployment
Argo CD

Observability
OpenTelemetry

Metrics
Prometheus

Dashboard
Grafana

Cloud
AWS
```

---

# 68. 完整 Enterprise Pipeline

```text
                    Developer
                        │
                        ▼
                      GitHub
                        │
                   Pull Request
                        │
                        ▼
                GitHub Actions
                        │
       ┌────────────────┼────────────────┐
       ▼                ▼                ▼
      Test           Security        AI Eval
       │                │                │
       └────────────────┼────────────────┘
                        ▼
                    Quality Gate
                        │
                        ▼
                    Docker Build
                        │
                        ▼
                   Image Scan
                        │
                        ▼
                       ECR
                        │
                        ▼
                     Staging
                        │
                  Integration Test
                        │
                        ▼
                     Canary
                        │
                        ▼
                  Observability
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
           Healthy               Failure
             │                     │
             ▼                     ▼
         Production             Rollback
```

---

# 69. Day 59 实战项目

今天真正完成：

> **AWS Java + AI Continuous Delivery Platform**

至少部署：

```text
ai-gateway
agent-worker
document-worker
```

到：

```text
EKS
```

---

# 70. Repository

建议：

```text
enterprise-ai/
│
├── services/
│   ├── ai-gateway/
│   ├── agent-worker/
│   └── document-worker/
│
├── infrastructure/
│   └── terraform/
│
├── deployment/
│   ├── helm/
│   └── argocd/
│
├── evaluation/
│   └── datasets/
│
└── .github/
    └── workflows/
```

---

# 71. Day 59 必做清单

```text id="o1k2c3"
[ ] GitHub
[ ] Pull Request
[ ] GitHub Actions
[ ] Java 21
[ ] Maven
[ ] Unit Test
[ ] Integration Test
[ ] Testcontainers
[ ] AI Evaluation
[ ] Secret Scan
[ ] Dependency Scan
[ ] SAST
[ ] Docker
[ ] Container Scan
[ ] ECR
[ ] Terraform
[ ] EKS
[ ] GitHub OIDC
[ ] IAM Role
[ ] Kubernetes
[ ] Canary
[ ] Rollback
[ ] Flyway
[ ] OpenTelemetry
[ ] Quality Gate
```

---

# 72. Day 59 验收测试

### Test 1：正常 PR

```text
PR
 ↓
CI
 ↓
Test PASS
 ↓
Security PASS
 ↓
AI Evaluation PASS
```

应该：

```text
MERGE = ALLOWED
```

---

### Test 2：Java Test Failure

```text
Unit Test
 ↓
FAIL
```

结果：

```text
❌ Merge Blocked
```

---

### Test 3：Security Failure

```text
Critical CVE
 ↓
FAIL
```

结果：

```text
❌ Deploy Blocked
```

---

### Test 4：AI Quality Failure

```text
Citation
95% → 87%
```

结果：

```text
❌ Deploy Blocked
```

---

### Test 5：Canary Failure

```text
v1.5
 ↓
5%
 ↓
Error Rate ↑
```

结果：

```text
Rollback
 ↓
v1.4
```

---

# 73. Day 59 面试题

### Q1：CI 和 CD 的区别？

```text
CI
=
Build + Test + Validate

CD
=
Release + Deploy
```

---

### Q2：为什么 GitHub Actions 不应该保存 AWS Secret Key？

因为：

```text
Long-lived Credential
=
Security Risk
```

推荐：

```text
GitHub OIDC
 ↓
AWS IAM Role
```

---

### Q3：为什么 Docker Image 不能只用 latest？

因为无法：

```text
准确追踪
准确回滚
```

推荐：

```text
Git SHA
+
Semantic Version
```

---

### Q4：为什么 AI Application 需要 AI Evaluation Gate？

传统测试只能知道：

```text
HTTP 200
```

但不知道：

```text
答案是否正确
```

所以需要：

```text
AI Evaluation
 ↓
Quality Gate
```

---

### Q5：为什么 Canary 比直接 Production 更安全？

因为：

```text
5%
```

用户暴露范围有限。

如果：

```text
Error
Latency
Quality
```

异常：

```text
Rollback
```

---

# 74. Day 59 最重要的一张图

```text
                       GITHUB
                          │
                          ▼
                     PULL REQUEST
                          │
                          ▼
                  GITHUB ACTIONS
                          │
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
      JAVA              SECURITY          AI EVAL
      TEST               SCAN               │
        │                 │                 │
        └─────────────────┼─────────────────┘
                          ▼
                     QUALITY GATE
                          │
                          ▼
                       DOCKER
                          │
                          ▼
                        ECR
                          │
                          ▼
                       STAGING
                          │
                          ▼
                       CANARY
                          │
                          ▼
                  OPENTELEMETRY
                          │
             ┌────────────┼────────────┐
             ▼            ▼            ▼
           Error        Latency       AI Quality
             │            │            │
             └────────────┼────────────┘
                          ▼
                      DECISION
                     /         \
                    /           \
                 PASS           FAIL
                  │              │
                  ▼              ▼
              PRODUCTION      ROLLBACK
```

---

## Day 50 → Day 59

你现在已经完成一个相当完整的 **AWS Enterprise Java + AI 平台技术栈**：

```text
Day 50  Agent / Tool / MCP
Day 51  Workflow / State
Day 52  Multi-Agent
Day 53  AI Evaluation
Day 54  AI Gateway
Day 55  AI Security
Day 56  AI Data Platform
Day 57  Event-Driven AI
Day 58  Observability / AIOps
Day 59  CI/CD / DevSecOps
```

**Day 60** 应该做一个非常关键的收口项目：把 Day 1–59 全部组合成一个完整的 **Enterprise Customer Intelligence AI Platform**，从 React 前端 → Spring Boot → OAuth2 → AI Gateway → Multi-Agent → MCP → RAG → PostgreSQL/pgvector → S3 → SQS → EKS → CI/CD → Observability → Security → Production，形成你的第一个真正可以放到 GitHub、用于美国 AI Engineer 求职的完整项目。
