# Day 19：Java Spring Boot → Docker → ECR → ECS 自动部署

今天开始做**应用 CI/CD**。

Day 18 做的是：

```text
Terraform CI/CD
```

今天做：

```text
Java Code
   ↓
Test
   ↓
Maven Build
   ↓
Docker Build
   ↓
ECR Push
   ↓
ECS Deploy
   ↓
ALB
```

最终你每次：

```bash
git push
```

就可以自动把新的 Java 版本部署到 AWS。

---

# 1. 今天最终目标

```text
GitHub
   │
   ▼
Pull Request
   │
   ├── Unit Test
   ├── Maven Build
   └── Docker Build
           │
           ▼
          ECR
           │
           ▼
       ECS Fargate
           │
       Rolling Deploy
           │
           ▼
          ALB
           │
           ▼
   api.yourdomain.com
```

---

# 2. 今天创建的 Pipeline

```text
id="3q9x7m"
Code
 ↓
Checkout
 ↓
Java 21
 ↓
Maven Test
 ↓
Maven Package
 ↓
Docker Build
 ↓
ECR Login
 ↓
Docker Push
 ↓
Update ECS
 ↓
Wait Deployment
 ↓
Health Check
```

---

# 3. Java 项目检查

进入：

```bash
cd backend
```

检查：

```bash
java -version
mvn -version
```

推荐：

```text
Java 21
Spring Boot 3.x
Maven 3.9+
```

---

# 4. Maven Test

执行：

```bash
mvn clean test
```

成功：

```text
BUILD SUCCESS
```

如果：

```text
BUILD FAILURE
```

CI/CD 应该停止。

**测试失败绝对不能继续部署。**

---

# 5. Maven Package

```bash
mvn clean package
```

生成：

```text
target/
└── java-ai-backend-0.0.1-SNAPSHOT.jar
```

---

# 6. Dockerfile

创建：

```text
backend/Dockerfile
```

推荐多阶段构建：

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY pom.xml .

RUN mvn dependency:go-offline -B

COPY src ./src

RUN mvn clean package -DskipTests


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build \
    /app/target/*.jar \
    app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# 7. 为什么使用 Multi-stage Build

不要：

```text
Maven
JDK
Source Code
Dependencies
全部放最终 Image
```

最终：

```text
JRE
+
Application JAR
```

这样：

```text
Image 更小
攻击面更小
启动更快
```

---

# 8. 本地构建 Docker

```bash
docker build \
  -t java-ai-backend:dev .
```

检查：

```bash
docker images
```

---

# 9. 本地运行

```bash
docker run \
  -p 8080:8080 \
  java-ai-backend:dev
```

测试：

```bash
curl http://localhost:8080/actuator/health
```

应该：

```json
{
  "status": "UP"
}
```

---

# 10. Docker 环境变量

不要把：

```text
DB_PASSWORD
JWT_SECRET
```

写进 Dockerfile。

Docker 只提供：

```text
Application
```

ECS 提供：

```text
Environment
Secrets
```

所以：

```text
Docker Image
      │
      │ immutable
      ▼
ECS
 ├── DB_HOST
 ├── DB_NAME
 ├── DB_USERNAME
 ├── DB_PASSWORD ← Secrets Manager
 └── JWT_SECRET  ← Secrets Manager
```

---

# 11. ECR

进入：

[Amazon ECR Console](https://console.aws.amazon.com/ecr/?utm_source=chatgpt.com)

Repository：

```text
java-ai-backend
```

如果已经通过 Terraform 创建，就不用手工创建。

最终：

```text
ECR
└── java-ai-backend
```

---

# 12. ECR Image Tag

不要永远：

```text
latest
```

推荐：

```text
Git Commit SHA
```

例如：

```text
java-ai-backend:8f4a91c
```

这样：

```text
Version
=
Git Commit
```

非常容易回滚。

---

# 13. Image Tag Strategy

例如：

```text
Git Commit:

8f4a91c2...
```

Docker：

```text
java-ai-backend:8f4a91c
```

ECS：

```text
Task Definition
    ↓
image:
.../java-ai-backend:8f4a91c
```

这样以后：

```text
Version A
8f4a91c

Version B
9ab723d
```

出现问题：

```text
9ab723d
 ↓
rollback
 ↓
8f4a91c
```

---

# 14. GitHub Actions

创建：

```text
.github/workflows/backend.yml
```

---

# 15. 基础 CI

```yaml
name: Backend CI

on:
  pull_request:
    paths:
      - "backend/**"

  push:
    branches:
      - main
    paths:
      - "backend/**"

jobs:

  test:

    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: backend

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "21"
          cache: maven

      - name: Test
        run: mvn clean test

      - name: Package
        run: mvn package -DskipTests
```

---

# 16. 为什么先 Test

流程：

```text
GitHub
 ↓
Compile
 ↓
Unit Test
 ↓
Package
 ↓
Docker
```

而不是：

```text
GitHub
 ↓
Docker
 ↓
ECS
 ↓
发现代码有 Bug
```

---

# 17. Docker Build Job

加入：

```yaml
- name: Build Docker image
  run: |
    docker build \
      -t java-ai-backend:${{ github.sha }} \
      .
```

然后：

```text
Git SHA
 ↓
Docker Tag
```

---

# 18. GitHub OIDC

Day 18 已经建立：

```text
GitHub
 ↓
OIDC
 ↓
AWS IAM Role
```

今天继续使用。

不要：

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

---

# 19. ECR Login

添加：

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-backend-dev
    aws-region: us-west-2
```

然后：

```yaml
- name: Login to ECR
  id: login-ecr
  uses: aws-actions/amazon-ecr-login@v2
```

---

# 20. ECR Push

设置：

```yaml
env:
  AWS_REGION: us-west-2
  ECR_REPOSITORY: java-ai-backend
```

然后：

```yaml
- name: Build and Push
  env:
    REGISTRY: ${{ steps.login-ecr.outputs.registry }}
    IMAGE_TAG: ${{ github.sha }}
  run: |
    docker build \
      -t $REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG \
      .

    docker push \
      $REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
```

最终：

```text
GitHub
 ↓
Docker
 ↓
ECR
```

---

# 21. ECR Image

例如：

```text
123456789012.dkr.ecr.us-west-2.amazonaws.com/
java-ai-backend:
8f4a91c2
```

这个就是 ECS 要使用的 Image。

---

# 22. ECS Task Definition

ECS Task Definition：

```text
java-ai-backend
```

Container：

```text
name:
backend
```

Image：

```text
ECR_URL/java-ai-backend:8f4a91c
```

Port：

```text
8080
```

---

# 23. ECS 部署方式

不要直接：

```text
停止旧 ECS
启动新 ECS
```

否则会出现：

```text
Downtime
```

应该：

```text
Old Task
    │
    ├────── Running
    │
New Task
    │
    ├────── Starting
    │
    ▼
Health Check
    │
    ▼
Traffic Switch
    │
    ▼
Old Task Stop
```

这就是：

**Rolling Deployment**

---

# 24. ECS Deployment Configuration

推荐：

```text
minimum healthy:
100%
```

```text
maximum:
200%
```

意思：

```text
Old:
2 Tasks

Deployment:

Old 2
+
New 2

↓

New healthy

↓

Old 2 stop
```

---

# 25. 为什么不能只有一个 ECS Task

如果：

```text
desired count = 1
```

部署：

```text
Old Task
 ↓
Stop

New Task
 ↓
Start
```

可能出现：

```text
Downtime
```

生产建议：

```text
desired_count = 2
```

至少：

```text
AZ-a
Task 1

AZ-b
Task 2
```

---

# 26. ECS Health Check

Docker：

```dockerfile
HEALTHCHECK \
  --interval=30s \
  --timeout=5s \
  --retries=3 \
  CMD curl -f \
  http://localhost:8080/actuator/health \
  || exit 1
```

如果基础 Image 没有 curl，可以使用 Spring Boot Actuator + ALB Health Check。

---

# 27. ALB Health Check

Target Group：

```text
Protocol:
HTTP

Port:
traffic port

Path:
/actuator/health
```

成功：

```text
200
```

ALB：

```text
Healthy
```

---

# 28. ECS Deployment 流程

最终：

```text
GitHub
 ↓
Maven Test
 ↓
Docker Build
 ↓
ECR
 ↓
ECS New Task Definition
 ↓
New ECS Tasks
 ↓
ALB Health Check
 ↓
Healthy
 ↓
Old Tasks Stop
```

---

# 29. 更新 ECS Task Definition

GitHub Actions 可以使用：

```text
aws-actions/amazon-ecs-render-task-definition
```

例如：

```yaml
- name: Render ECS Task Definition
  id: task-def
  uses: aws-actions/amazon-ecs-render-task-definition@v1
  with:
    task-definition: backend-task-definition.json
    container-name: backend
    image: ${{ steps.login-ecr.outputs.registry }}/java-ai-backend:${{ github.sha }}
```

---

# 30. Deploy ECS

然后：

```yaml
- name: Deploy to ECS
  uses: aws-actions/amazon-ecs-deploy-task-definition@v2
  with:
    task-definition: ${{ steps.task-def.outputs.task-definition }}
    service: java-ai-backend
    cluster: java-ai-platform-dev
    wait-for-service-stability: true
```

最重要：

```text
wait-for-service-stability: true
```

这样 GitHub 会等待：

```text
ECS Deployment
        ↓
Healthy
```

而不是 ECS 还没启动就宣布：

```text
Success
```

---

# 31. 完整 backend.yml

最终可以先做成：

```yaml
name: Backend CI/CD

on:
  pull_request:
    paths:
      - "backend/**"

  push:
    branches:
      - main
    paths:
      - "backend/**"

permissions:
  contents: read
  id-token: write

env:
  AWS_REGION: us-west-2
  ECR_REPOSITORY: java-ai-backend
  ECS_CLUSTER: java-ai-platform-dev
  ECS_SERVICE: java-ai-backend

jobs:

  build:

    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: backend

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "21"
          cache: maven

      - name: Test
        run: mvn clean test

      - name: Package
        run: mvn package -DskipTests

      - name: Configure AWS
        if: github.event_name == 'push'
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-backend-dev
          aws-region: ${{ env.AWS_REGION }}

      - name: ECR Login
        if: github.event_name == 'push'
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build and Push
        if: github.event_name == 'push'
        env:
          REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build \
            -t $REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG \
            .

          docker push \
            $REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
```

---

# 32. 为什么 PR 不 Push Docker

PR：

```text
Test
Build
Docker Build
```

但不要：

```text
PR
 ↓
Production ECR
```

PR 只是验证代码。

真正：

```text
main
 ↓
ECR
 ↓
ECS
```

---

# 33. 推荐 Pipeline

### Pull Request

```text
PR
 ↓
Checkout
 ↓
Maven Test
 ↓
Maven Package
 ↓
Docker Build
 ↓
Security Scan
```

### Main

```text
Merge
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
```

---

# 34. Docker Image Security

Day 19 加一个扫描：

```text
ECR Image Scan
```

Terraform：

```hcl
image_scanning_configuration {
  scan_on_push = true
}
```

这样：

```text
Docker Push
 ↓
ECR
 ↓
Vulnerability Scan
```

---

# 35. 更进一步：Trivy

GitHub Actions：

```yaml
- name: Trivy Scan
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: java-ai-backend:${{ github.sha }}
    severity: CRITICAL,HIGH
    exit-code: 1
```

如果出现严重漏洞：

```text
CRITICAL
```

Pipeline：

```text
FAIL
```

不要部署。

---

# 36. Java Dependency Scan

还可以使用：

```text
OWASP Dependency Check
```

检查：

```text
Spring
Jackson
Netty
Logback
第三方 libraries
```

企业项目非常有价值。

---

# 37. ECS Rollback

假设：

```text
Version A
8f4a91c
```

上线：

```text
Version B
9ab723d
```

出现：

```text
HTTP 500
```

回滚：

```text
9ab723d
 ↓
8f4a91c
```

所以：

**不要使用 `latest` 作为唯一生产版本标识。**

---

# 38. CloudWatch

ECS：

```text
Spring Boot
    ↓
stdout
    ↓
CloudWatch Logs
```

日志：

```text
/aws/ecs/java-ai-platform
```

建议：

```text
dev
test
prod
```

分开。

---

# 39. Application Metrics

Spring Boot：

```text
/actuator/health
/actuator/metrics
```

生产环境不要把所有 Actuator Endpoint 直接暴露公网。

建议：

```text
/actuator/health
```

只用于 ALB Health Check。

---

# 40. 今天完成后的完整系统

```text
                         GitHub
                            │
                ┌───────────┴───────────┐
                │                       │
               PR                      main
                │                       │
                ▼                       ▼
             Test                    Test
             Build                   Build
                │                       │
                ▼                       ▼
           Docker Build           Docker Build
                                        │
                                        ▼
                                       ECR
                                        │
                                        ▼
                                   ECS Fargate
                                        │
                                  New Tasks
                                        │
                                  ALB Health
                                        │
                                        ▼
                                     Traffic
```

---

# 41. Java + AI 应用现在真正跑起来

最终请求：

```text
Browser
   │
   ▼
https://app.yourdomain.com
   │
   ▼
CloudFront
   │
   ▼
S3
```

API：

```text
React
   │
   ▼
https://api.yourdomain.com
   │
   ▼
ALB
   │
   ▼
Private ECS
   │
   ├── Spring Boot
   ├── Spring Security
   ├── Spring AI
   └── RAG
          │
      ┌───┴────┐
      ▼        ▼
    RDS      Bedrock
  pgvector
```

部署：

```text
GitHub
   │
   ▼
GitHub Actions
   │
   ├── Maven Test
   ├── Docker Build
   ├── Trivy
   └── ECR
          │
          ▼
        ECS
```

---

# 42. Day 19 检查清单

```text
[ ] Java 21
[ ] Maven Test
[ ] Maven Package
[ ] Multi-stage Dockerfile
[ ] Docker local test
[ ] ECR
[ ] Git SHA Image Tag
[ ] GitHub OIDC
[ ] ECR Push
[ ] ECS Task Definition
[ ] ECS Rolling Deployment
[ ] ALB Health Check
[ ] CloudWatch Logs
[ ] ECR Scan
[ ] Trivy
[ ] ECS Deployment Stability
[ ] Rollback 理解
```

---

## Day 20

下一步建议做 **React 前端 CI/CD**：

```text
React
 ↓
npm ci
 ↓
npm test
 ↓
npm run build
 ↓
S3
 ↓
CloudFront
 ↓
Cache Invalidation
```

然后整个应用就形成完整的：

```text
React CI/CD
      +
Java CI/CD
      +
Terraform CI/CD
      +
AWS
      +
AI / RAG
```

这才是你这套 **完全在线 AWS Java + AI 企业应用开发环境**的完整 DevOps 基础。
