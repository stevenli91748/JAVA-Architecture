# Day 10：GitHub → CodeBuild → ECR → ECS 自动 CI/CD

今天是前 9 天的汇总。

目标是以后你只需要：

```text
git push
```

然后 AWS 自动完成：

```text
GitHub
   ↓
CodePipeline
   ↓
CodeBuild
   ├── Maven Test
   ├── Docker Build
   └── Push ECR
          ↓
        ECS
          ↓
     新版本上线
```

AWS 官方目前仍提供 GitHub → CodeBuild → ECR → ECS 的标准流水线方案；CodeBuild 的 `buildspec.yml` 负责构建/测试和推送镜像，ECS 部署动作使用 `imagedefinitions.json` 更新服务。([AWS Documentation][1])

---

# 1. Day 10 最终架构

```text
                 GitHub
                    │
                 git push
                    │
                    ▼
              CodePipeline
                    │
                    ▼
               CodeBuild
                    │
          ┌─────────┴─────────┐
          │                   │
       Maven Test         Docker Build
          │                   │
          ▼                   ▼
       JUnit              Docker Image
                              │
                              ▼
                             ECR
                              │
                              ▼
                         ECS Fargate
                              │
                              ▼
                         Spring Boot
                              │
                              ▼
                        RDS PostgreSQL
```

前端：

```text
GitHub
   ↓
React Build
   ↓
S3
   ↓
CloudFront
```

AI：

```text
ECS
 ↓
Spring AI
 ↓
Bedrock
```

---

# 2. 今天先整理 Git 分支

现在 GitHub：

```text
main
```

再创建：

```text
develop
```

建议：

```text
feature/*
    ↓
develop
    ↓
TEST
    ↓
main
    ↓
PROD
```

今天为了先跑通 CI/CD：

```text
main
 ↓
AWS DEV ECS
```

以后再拆：

```text
develop → DEV
main → PROD
```

---

# 3. 添加 `buildspec.yml`

放在 GitHub Repository **根目录**：

```text
aws-java-ai-platform/
├── buildspec.yml
├── backend/
├── frontend/
├── infrastructure/
└── ...
```

AWS CodeBuild 默认会从源码根目录寻找 `buildspec.yml`。([AWS Documentation][2])

内容：

```yaml
version: 0.2

env:
  variables:
    AWS_DEFAULT_REGION: "us-west-2"
    IMAGE_REPO_NAME: "java-ai-backend"

phases:

  install:
    runtime-versions:
      java: corretto21

  pre_build:
    commands:
      - echo "Logging in to Amazon ECR..."
      - ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
      - ECR_URI=$ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_URI
      - IMAGE_TAG=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)

  build:
    commands:
      - echo "Running Maven tests..."
      - cd backend
      - ./mvnw clean test

      - echo "Building Spring Boot application..."
      - ./mvnw clean package -DskipTests

      - echo "Building Docker image..."
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .

      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $ECR_URI/$IMAGE_REPO_NAME:$IMAGE_TAG

  post_build:
    commands:
      - echo "Pushing Docker image..."
      - docker push $ECR_URI/$IMAGE_REPO_NAME:$IMAGE_TAG

      - echo "Creating imagedefinitions.json..."
      - printf '[{"name":"java-ai-backend","imageUri":"%s"}]' "$ECR_URI/$IMAGE_REPO_NAME:$IMAGE_TAG" > ../imagedefinitions.json

artifacts:
  files:
    - imagedefinitions.json
```

这个流程符合 AWS 的标准模式：CodeBuild 构建 Docker 镜像、推送 ECR，并生成 ECS 部署使用的 `imagedefinitions.json`。([AWS Documentation][1])

---

# 4. 为什么使用 Git Commit 作为 Image Tag

不要让 CI/CD 永远使用：

```text
latest
```

我们使用：

```text
Git Commit
```

例如：

```text
a81f92c
```

最终：

```text
ECR
└── java-ai-backend
      ├── a81f92c
      ├── b9217fd
      └── c381aa2
```

这样：

```text
Production
   ↓
Docker Image
   ↓
Git Commit
   ↓
源代码
```

以后出现问题，可以准确知道生产运行的是哪一份代码。

---

# 5. 修改 Dockerfile

Day 6 的 Dockerfile可以继续使用：

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# 6. 本地先测试 buildspec 逻辑

先：

```bash
git add .
git commit -m "Day 10: add CI/CD buildspec"
git push
```

确认 GitHub 上看到：

```text
buildspec.yml
```

---

# 7. 创建 CodeBuild

进入：

[AWS CodeBuild Console](https://console.aws.amazon.com/codesuite/codebuild/home?utm_source=chatgpt.com)

选择：

```text
Create build project
```

Project name：

```text
java-ai-backend-build
```

---

# 8. Source

选择：

```text
GitHub
```

连接你的 GitHub Repository：

```text
aws-java-ai-platform
```

Branch：

```text
main
```

CodeBuild 支持 GitHub 等源码提供商，并可以从 Repository 中读取 `buildspec.yml`。([AWS Documentation][3])

---

# 9. Environment

选择：

```text
Managed image
```

Operating system：

```text
Amazon Linux
```

Runtime：

```text
Standard
```

选择 AWS 当前可用的标准 CodeBuild image。

Compute：

```text
Small
```

学习环境先用小规格。

---

# 10. 最重要：开启 Privileged Mode

因为 CodeBuild 要：

```text
docker build
```

所以：

```text
Privileged
    ✓ Enabled
```

AWS 官方明确说明，如果 CodeBuild 项目需要构建 Docker image，需要启用 privileged mode。([AWS Documentation][3])

---

# 11. Buildspec

选择：

```text
Use a buildspec file
```

文件：

```text
buildspec.yml
```

Artifacts：

```text
No artifacts
```

虽然我们会生成：

```text
imagedefinitions.json
```

CodePipeline 会处理这个输出。

---

# 12. CodeBuild Service Role

创建新的：

```text
codebuild-java-ai-backend-role
```

然后进入：

```text
IAM
 ↓
Roles
 ↓
codebuild-java-ai-backend-role
```

---

# 13. CodeBuild 权限

CodeBuild 至少需要：

```text
ECR
CloudWatch Logs
```

学习阶段可以先给：

```text
AmazonEC2ContainerRegistryPowerUser
```

AWS 官方 ECS CI/CD 教程也是要求 CodeBuild Service Role 拥有 ECR 相关权限。([AWS Documentation][1])

不过以后我们会改成：

```text
Least Privilege
```

只允许：

```text
ECR Push
ECR Describe
CloudWatch Logs
```

---

# 14. 第一次运行 CodeBuild

进入：

```text
CodeBuild
 ↓
Build projects
 ↓
java-ai-backend-build
 ↓
Start build
```

等待：

```text
Phase
```

依次：

```text
DOWNLOAD_SOURCE
INSTALL
PRE_BUILD
BUILD
POST_BUILD
UPLOAD_ARTIFACTS
```

最终：

```text
BUILD SUCCEEDED
```

---

# 15. 检查 ECR

进入：

```text
ECR
 ↓
java-ai-backend
```

应该看到：

```text
Image tag
a81f92c
```

类似：

```text
java-ai-backend:a81f92c
```

现在 Docker Build 已经不需要你在 Codespaces 手工做了。

---

# 16. 创建 CodePipeline

进入：

[AWS CodePipeline Console](https://console.aws.amazon.com/codesuite/codepipeline/home?utm_source=chatgpt.com)

选择：

```text
Create pipeline
```

名称：

```text
java-ai-pipeline
```

---

# 17. Pipeline Type

选择：

```text
V2
```

AWS 当前提供 CodePipeline V2 类型，并有专门的 ECR Build and Publish → ECS 部署教程。([AWS Documentation][4])

---

# 18. Source Stage

选择：

```text
GitHub
```

Repository：

```text
aws-java-ai-platform
```

Branch：

```text
main
```

Trigger：

```text
Push
```

以后：

```text
git push
```

就自动触发。

---

# 19. Build Stage

选择：

```text
AWS CodeBuild
```

Project：

```text
java-ai-backend-build
```

这样：

```text
GitHub
   ↓
CodePipeline
   ↓
CodeBuild
```

---

# 20. Deploy Stage

Deployment provider：

```text
Amazon ECS
```

Cluster：

```text
java-ai-cluster
```

Service：

```text
java-ai-backend-service
```

Image definitions file：

```text
imagedefinitions.json
```

AWS 的 ECS Standard Deployment Action 正是通过 `imagedefinitions.json` 告诉 ECS 使用新的镜像。([AWS Documentation][5])

---

# 21. 创建 Pipeline

点击：

```text
Create pipeline
```

第一次会自动运行：

```text
Source
   ↓
Build
   ↓
Deploy
```

---

# 22. 最重要的测试

现在不要在 AWS Console 手工部署。

修改 Java：

```java
return "Hello AWS Java AI Platform v2!";
```

然后：

```bash
git add .
git commit -m "test: automatic deployment"
git push
```

然后打开：

```text
CodePipeline
```

你应该看到：

```text
Source
  ✓

Build
  ✓

Deploy
  ✓
```

---

# 23. ECS 自动更新

CodePipeline 完成以后：

```text
ECS
 ↓
Task Definition Revision
 ↓
New Docker Image
 ↓
New Task
 ↓
Old Task
```

ECS 会进行滚动更新。

最终：

```text
ECS Service
Desired:
1

Running:
1
```

然后 Target Group：

```text
Healthy
```

---

# 24. 验证公网 API

打开：

```text
http://YOUR_ALB_DNS/actuator/health
```

应该：

```json
{
  "status": "UP"
}
```

然后：

```text
GET /api/users
```

再测试：

```text
POST /api/chat
```

最后：

```text
POST /api/rag/query
```

---

# 25. 现在整个系统已经自动化

```text
Developer
    │
    │ git push
    ▼
 GitHub
    │
    ▼
CodePipeline
    │
    ▼
CodeBuild
    │
    ├── Maven
    ├── JUnit
    ├── Docker
    │
    ▼
   ECR
    │
    ▼
   ECS
    │
    ├── Spring Boot
    ├── Spring AI
    │
    ├──────────────┐
    ▼              ▼
   RDS          Bedrock
```

这就是一个真正的企业 CI/CD 基础。

---

# 26. 现在前端也可以自动化

后面我们把：

```text
frontend/
```

加入第二条 Pipeline：

```text
GitHub
   ↓
npm install
   ↓
npm test
   ↓
npm run build
   ↓
S3
   ↓
CloudFront
```

最终：

```text
              GitHub
                 │
       ┌─────────┴─────────┐
       │                   │
    Backend             Frontend
       │                   │
   CodeBuild            npm build
       │                   │
      ECR                   S3
       │                   │
      ECS              CloudFront
       │                   │
       └─────────┬─────────┘
                 │
              Browser
```

---

# 27. Day 10 之后的企业版

我们现在的系统已经可以运行，但还有几个地方要升级：

### Security

```text
Secrets Manager
       ↓
ECS
```

而不是：

```text
DB_PASSWORD
```

直接写在 Task Definition。

### Network

最终：

```text
ALB
 ↓
Private ECS
 ↓
Private RDS
```

而不是今天学习环境里的：

```text
Public ECS
```

### Authentication

加入：

```text
Spring Security
     ↓
JWT / OAuth2
```

### Observability

加入：

```text
CloudWatch
     +
Metrics
     +
Tracing
```

### AI

加入：

```text
Bedrock
 ↓
RAG
 ↓
Agent
 ↓
Tool Calling
```

---

# 28. 你的 Day 1–10 已经形成完整体系

```text
Day 1
AWS Account
IAM
Billing
       ↓
Day 2
GitHub Codespaces
Java 21
Node
Docker
Terraform
       ↓
Day 3
Spring Boot
PostgreSQL
JPA
Flyway
       ↓
Day 4
React
REST
Axios
       ↓
Day 5
VPC
RDS
Security Group
       ↓
Day 6
Docker
ECR
       ↓
Day 7
ECS Fargate
ALB
       ↓
Day 8
Spring AI
Bedrock
AI Chat
       ↓
Day 9
pgvector
RAG
Knowledge Base
       ↓
Day 10
GitHub
CodePipeline
CodeBuild
ECR
ECS
```

---

# Day 10 完成标准

```text
[ ] buildspec.yml
[ ] CodeBuild
[ ] Maven Test 自动执行
[ ] Docker Build 自动执行
[ ] ECR 自动 Push
[ ] imagedefinitions.json
[ ] CodePipeline
[ ] ECS 自动 Deploy
[ ] git push 自动触发
[ ] ECS Task 自动更新
[ ] ALB Healthy
[ ] /actuator/health = UP
[ ] /api/users 正常
[ ] /api/chat 正常
[ ] /api/rag/query 正常
```

**做到这里，你已经不再是在“学习 AWS 服务”，而是在运行一套完整的 Java + AI 企业应用开发平台。**

下一阶段建议进入 **Day 11–20：企业级安全 + Spring Security + JWT + Secrets Manager + HTTPS + Route 53 + CloudFront + Terraform**，把目前“能跑”的系统升级成**真正接近生产环境的系统**。

[1]: https://docs.aws.amazon.com/codepipeline/latest/userguide/ecs-cd-pipeline.html?utm_source=chatgpt.com "Tutorial: Amazon ECS Standard Deployment with CodePipeline - AWS CodePipeline"
[2]: https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html?utm_source=chatgpt.com "Build specification reference for CodeBuild - AWS CodeBuild"
[3]: https://docs.aws.amazon.com/codebuild/latest/userguide/create-project.html?utm_source=chatgpt.com "Create a build project in AWS CodeBuild - AWS CodeBuild"
[4]: https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecr-build-publish.html?utm_source=chatgpt.com "Tutorial: Build and push a Docker image to Amazon ECR with CodePipeline (V2 type) - AWS CodePipeline"
[5]: https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-ECS.html?utm_source=chatgpt.com "Amazon Elastic Container Service deploy action reference - AWS CodePipeline"
