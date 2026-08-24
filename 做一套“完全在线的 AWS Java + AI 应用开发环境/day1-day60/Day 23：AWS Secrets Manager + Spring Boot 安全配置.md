# Day 23：AWS Secrets Manager + Spring Boot 安全配置

今天解决一个企业应用最重要的问题之一：

> **密码、API Key、JWT Secret 到底放在哪里？**

今天完成后，下面这些东西都不应该出现在 GitHub、Docker Image 或代码里：

```text
DB_PASSWORD
DB_USERNAME
JWT_SECRET
OPENAI_API_KEY
第三方 API Key
AWS Access Key
```

最终架构：

```text
                 AWS Secrets Manager
                         │
                         │ IAM
                         ▼
                  ECS Task Role
                         │
                         ▼
                  Spring Boot
                   │           │
                   ▼           ▼
                 RDS        Bedrock
```

---

# 1. 今天的目标

完成：

```text
[✓] AWS Secrets Manager
[✓] ECS Task Role
[✓] Secrets 权限
[✓] Spring Boot Secrets
[✓] RDS Password
[✓] JWT Secret
[✓] DEV / TEST / PROD 隔离
[✓] Docker 不保存 Secret
[✓] GitHub 不保存生产 Secret
[✓] Secret Rotation 基础
```

---

# 2. 先理解三个东西

今天最容易混淆的是：

### IAM Role

决定：

> **谁可以访问 Secret？**

### Secrets Manager

负责：

> **Secret 存在哪里？**

### ECS Task Definition

负责：

> **把 Secret 安全地注入到容器。**

---

# 3. 错误架构

不要：

```text
GitHub
   │
   ▼
application.yml
   │
   ▼
DB_PASSWORD=123456
   │
   ▼
Docker Image
   │
   ▼
ECS
```

也不要：

```text
docker run \
-e DB_PASSWORD=xxxx
```

更不要：

```text
GitHub Secrets
   ↓
生产环境全部 Secret
```

---

# 4. 正确架构

```text
                    Secrets Manager
                         │
                  Secret: DB
                         │
                         ▼
                     ECS Task
                         │
                    Task Role
                         │
                         ▼
                  Spring Boot
                         │
                         ▼
                    PostgreSQL
```

---

# 5. DEV / TEST / PROD

今天直接建立环境隔离：

```text
Secrets
│
├── dev/
│   ├── database
│   └── jwt
│
├── test/
│   ├── database
│   └── jwt
│
└── prod/
    ├── database
    └── jwt
```

例如：

```text
java-ai-platform/dev/database
java-ai-platform/test/database
java-ai-platform/prod/database
```

---

# 6. 不要让 DEV Role 访问 PROD

这是企业安全的核心。

```text
DEV ECS
   │
   ├── dev secrets ✓
   │
   ├── test secrets ✗
   │
   └── prod secrets ✗
```

PROD：

```text
PROD ECS
   │
   ├── prod secrets ✓
   ├── dev secrets ✗
   └── test secrets ✗
```

---

# 7. 创建 Secret

进入：

[AWS Secrets Manager Console](https://console.aws.amazon.com/secretsmanager/?utm_source=chatgpt.com)

选择：

```text
Store a new secret
```

选择：

```text
Other type of secret
```

例如：

```json
{
  "username": "appuser",
  "password": "CHANGE_ME"
}
```

名称：

```text
java-ai-platform/dev/database
```

---

# 8. Secret 不要写进 Terraform

这一点非常重要。

错误：

```hcl
resource "aws_secretsmanager_secret_version" "db" {
  secret_string = "password123"
}
```

因为 Terraform State 可能包含 Secret。

更安全的方式是：

```text
Terraform
 ↓
创建 Secret 容器
```

Secret 真正的值：

```text
AWS Console
或者
安全的 CI/CD / Secret provisioning 流程
```

写入。

---

# 9. Terraform 创建 Secret

```hcl
resource "aws_secretsmanager_secret" "database" {

  name = "${var.project_name}/${var.environment}/database"

  description = "Database credentials"

  recovery_window_in_days = 7

  tags = {
    Environment = var.environment
    Service     = "backend"
  }
}
```

得到：

```text
java-ai-platform/dev/database
```

---

# 10. 为什么 recovery window

如果误删除：

```text
Secret
 ↓
Delete
```

AWS 默认可以保留一段恢复窗口。

DEV：

```text
7 days
```

PROD：

```text
7–30 days
```

根据组织策略决定。

---

# 11. JWT Secret

创建：

```text
java-ai-platform/dev/jwt
```

内容：

```json
{
  "secret": "LONG_RANDOM_SECRET"
}
```

不要：

```text
"secret": "123456"
```

JWT Secret 应该使用密码学安全的随机值。

---

# 12. 生成随机 Secret

本地可以：

```bash
openssl rand -base64 48
```

例如：

```text
Jr9...random...
```

然后写入 Secrets Manager。

不要把结果：

```text
git add
```

提交进 Git。

---

# 13. ECS Task Role

现在建立：

```text
github-backend-dev
```

和：

```text
ecs-backend-dev-task-role
```

注意：

**GitHub Role 和 ECS Task Role 完全不同。**

---

# 14. GitHub Role

用于：

```text
GitHub Actions
```

主要：

```text
ECR
ECS
```

---

# 15. ECS Task Role

用于：

```text
Java Application
```

例如：

```text
Bedrock
Secrets Manager
S3
```

---

# 16. Secrets IAM Policy

ECS Task Role：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": [
        "arn:aws:secretsmanager:us-west-2:ACCOUNT_ID:secret:java-ai-platform/dev/database-*",
        "arn:aws:secretsmanager:us-west-2:ACCOUNT_ID:secret:java-ai-platform/dev/jwt-*"
      ]
    }
  ]
}
```

注意：

```text
dev
```

Role 只能访问 DEV Secret。

---

# 17. 不要使用

```json
{
  "Effect": "Allow",
  "Action": "secretsmanager:*",
  "Resource": "*"
}
```

这会给应用过大的权限。

---

# 18. ECS 注入 Secret

ECS Task Definition：

```json
{
  "name": "DB_PASSWORD",
  "valueFrom": "arn:aws:secretsmanager:..."
}
```

例如：

```json
{
  "name": "DB_PASSWORD",
  "valueFrom":
    "arn:aws:secretsmanager:us-west-2:ACCOUNT_ID:secret:java-ai-platform/dev/database:password::"
}
```

---

# 19. 更完整的 ECS Environment

最终：

```text
Environment
│
├── DB_HOST
├── DB_PORT
├── DB_NAME
└── DB_USERNAME
```

Secret：

```text
Secrets
│
├── DB_PASSWORD
└── JWT_SECRET
```

---

# 20. Spring Boot

`application.yml`：

```yaml
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:${DB_PORT}/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

JWT：

```yaml
security:
  jwt:
    secret: ${JWT_SECRET}
```

Spring Boot 不知道：

```text
Secret 从哪里来
```

它只知道：

```text
Environment Variable
```

这就是好的设计。

---

# 21. 为什么 Spring Boot 不直接调用 Secrets Manager

可以，但 Day 23 推荐先使用：

```text
Secrets Manager
 ↓
ECS
 ↓
Environment Variable
 ↓
Spring Boot
```

优点：

```text
简单
清晰
ECS 原生支持
代码无需 AWS SDK
```

---

# 22. Secret 注入流程

完整：

```text
ECS
 │
 │ Task Role
 ▼
Secrets Manager
 │
 │ secret value
 ▼
Container Environment
 │
 ▼
Spring Boot
```

---

# 23. Secret 什么时候读取？

ECS 启动 Container 时读取。

因此：

```text
Secret 修改
```

不会自动让已经运行的 Container 获得新值。

需要：

```text
ECS Deployment
```

重新启动 Task。

---

# 24. Secret Rotation

例如：

```text
DB Password
old
 ↓
rotation
 ↓
new
```

ECS 旧 Task：

```text
仍然使用 old
```

新 Task：

```text
使用 new
```

所以：

```text
Rotate Secret
 ↓
Deploy ECS
```

非常重要。

---

# 25. DB Secret 推荐结构

可以统一：

```json
{
  "username": "appuser",
  "password": "********",
  "engine": "postgres",
  "host": "database.internal",
  "port": 5432,
  "dbname": "java_ai"
}
```

不过如果你让 RDS 管理 Secret，AWS 可以生成和维护数据库凭据；实际项目可优先考虑 RDS/Secrets Manager 的集成，而不是手工维护密码。

---

# 26. RDS + Secrets Manager

更企业化：

```text
RDS
 │
 └── Managed Master Credentials
             │
             ▼
       Secrets Manager
```

应用：

```text
ECS
 ↓
Secret
 ↓
DB connection
```

这样数据库密码不用由开发人员知道。

---

# 27. Spring Boot Profile

建议：

```text
application.yml
application-dev.yml
application-test.yml
application-prod.yml
```

例如：

```yaml
spring:
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:dev}
```

ECS：

```text
SPRING_PROFILES_ACTIVE=dev
```

TEST：

```text
SPRING_PROFILES_ACTIVE=test
```

PROD：

```text
SPRING_PROFILES_ACTIVE=prod
```

---

# 28. 不要把密码写进 Profile

错误：

```yaml
spring:
  datasource:
    password: mypassword123
```

正确：

```yaml
spring:
  datasource:
    password: ${DB_PASSWORD}
```

---

# 29. GitHub Actions

今天的原则：

```text
GitHub Actions
        │
        ├── Build
        ├── Test
        ├── Docker
        └── Deploy
```

不要：

```text
GitHub Actions
        │
        ▼
生产 DB_PASSWORD
```

除非某个具体 provisioning 流程确实需要，而且应使用最小权限与受控 Secret。

---

# 30. Dockerfile 检查

你的 Dockerfile：

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY app.jar .

EXPOSE 8080

ENTRYPOINT [
  "java",
  "-jar",
  "app.jar"
]
```

不要：

```dockerfile
ENV DB_PASSWORD=xxxx
```

不要：

```dockerfile
COPY .env .
```

---

# 31. `.dockerignore`

创建：

```text
backend/.dockerignore
```

内容：

```text
.git
.github
.env
.env.*
*.log
target
node_modules
```

尤其：

```text
.env
```

不要进入 Docker Image。

---

# 32. `.gitignore`

确认：

```text
.env
.env.*
*.pem
*.key
secrets/
```

不要提交：

```text
application-prod.yml
```

如果里面有 Secret。

---

# 33. 今天做 Secret Leak 检查

本地：

```bash
git grep -n \
"password\|api_key\|secret\|access_key"
```

人工检查结果。

更专业：

```text
Gitleaks
```

Day 20 已经提过。

今天把它真正放进 Pipeline。

---

# 34. GitHub Secret Scan

建议：

```text
GitHub
 ↓
Push
 ↓
Secret Scan
 ↓
Maven
 ↓
Docker
```

发现：

```text
AWS Secret
API Key
Private Key
```

直接：

```text
FAIL
```

---

# 35. AI 应用尤其要注意

你的 AI 应用以后可能使用：

```text
Bedrock
OpenAI
Anthropic
Google
Vector DB
Search API
```

不要：

```text
Java Code
 ↓
API Key
```

而应该：

```text
ECS Task Role
 ↓
AWS Service
```

AWS 原生服务优先使用 IAM Role。

---

# 36. Bedrock 特别推荐

如果使用：

```text
AWS Bedrock
```

通常不要：

```text
AWS_ACCESS_KEY
AWS_SECRET_KEY
```

直接调用。

推荐：

```text
Spring AI
 ↓
AWS SDK
 ↓
ECS Task Role
 ↓
Bedrock
```

也就是：

**没有 AWS Access Key。**

---

# 37. S3 也是一样

错误：

```text
S3
 ↓
Access Key
```

正确：

```text
Spring Boot
 ↓
AWS SDK
 ↓
ECS Task Role
 ↓
S3
```

---

# 38. ECS Task Role 最终权限

你的 AI Backend 可能：

```text
ECS Task Role
│
├── secretsmanager:GetSecretValue
│
├── bedrock:InvokeModel
│
├── s3:GetObject
│
└── xray:PutTraceSegments
```

但只允许：

```text
指定 Secret
指定 S3 Bucket
指定 Bedrock Model
```

---

# 39. 权限设计

最终：

```text
GitHub Role
│
├── ECR
└── ECS

ECS Task Role
│
├── Secrets Manager
├── Bedrock
├── S3
└── X-Ray

Frontend Role
│
├── S3
└── CloudFront

Terraform Role
│
└── Infrastructure
```

这就是：

**职责分离。**

---

# 40. DEV / TEST / PROD IAM

推荐：

```text
github-terraform-dev
github-backend-dev
github-frontend-dev

github-terraform-test
github-backend-test
github-frontend-test

github-terraform-prod
github-backend-prod
github-frontend-prod
```

实际生产环境中可以进一步减少 Role 数量，但原则是：

**不同环境不能拥有不必要的跨环境权限。**

---

# 41. Secret 命名规范

统一：

```text
java-ai-platform/dev/database
java-ai-platform/dev/jwt

java-ai-platform/test/database
java-ai-platform/test/jwt

java-ai-platform/prod/database
java-ai-platform/prod/jwt
```

以后：

```text
java-ai-platform/prod/openai
java-ai-platform/prod/stripe
java-ai-platform/prod/search
```

也很容易管理。

---

# 42. Secret Tags

建议：

```text
Environment=dev
Service=backend
ManagedBy=terraform
```

例如：

```hcl
tags = {
  Environment = var.environment
  Service     = "backend"
  ManagedBy   = "terraform"
}
```

方便以后：

```text
成本管理
权限管理
审计
```

---

# 43. CloudTrail

Secrets Manager 的访问应该可以通过：

```text
CloudTrail
```

审计：

```text
谁
什么时候
访问了什么
```

例如：

```text
ECS Task Role
 ↓
GetSecretValue
 ↓
CloudTrail
```

生产环境建议启用并集中保留审计日志。

---

# 44. 今天的 Terraform Module

建议：

```text
infrastructure/terraform/modules/
│
├── network/
├── security/
├── ecs/
├── rds/
├── observability/
└── secrets/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

---

# 45. secrets/main.tf

```hcl
resource "aws_secretsmanager_secret" "database" {

  name = "${var.project_name}/${var.environment}/database"

  recovery_window_in_days = 7

  tags = {
    Environment = var.environment
    Service     = "backend"
  }
}

resource "aws_secretsmanager_secret" "jwt" {

  name = "${var.project_name}/${var.environment}/jwt"

  recovery_window_in_days = 7

  tags = {
    Environment = var.environment
    Service     = "backend"
  }
}
```

---

# 46. Outputs

```hcl
output "database_secret_arn" {
  value = aws_secretsmanager_secret.database.arn
}

output "jwt_secret_arn" {
  value = aws_secretsmanager_secret.jwt.arn
}
```

然后 ECS：

```text
ECS Module
    ↓
Secrets Module
    ↓
Secret ARN
```

---

# 47. ECS Terraform

概念：

```hcl
secrets = [
  {
    name = "DB_PASSWORD"

    valueFrom =
      module.secrets.database_secret_arn
  },

  {
    name = "JWT_SECRET"

    valueFrom =
      module.secrets.jwt_secret_arn
  }
]
```

如果一个 Secret 是 JSON，使用 ECS/Secrets Manager 支持的 JSON key 引用方式把单个字段注入到容器。

---

# 48. Terraform Apply

今天：

```bash
cd infrastructure/terraform/environments/dev
```

执行：

```bash
terraform fmt -recursive
terraform validate
terraform plan
```

重点检查：

```text
Secrets Manager
IAM
ECS Task Definition
```

确认没有：

```text
Secret Value
```

出现在 Terraform Plan。

---

# 49. 部署后验证

进入 ECS：

```text
ECS
 → Cluster
 → Service
 → Task
 → Configuration
```

确认：

```text
Task Role:
ecs-backend-dev-task-role
```

然后 CloudWatch Logs：

```text
Spring Boot started
```

确认：

```text
Database connection successful
```

---

# 50. 不要打印 Environment

不要为了测试：

```java
System.out.println(
    System.getenv("DB_PASSWORD")
);
```

这会把 Secret 写入：

```text
CloudWatch Logs
```

等于：

```text
Secrets Manager
     ↓
CloudWatch
     ↓
Secret 泄露
```

---

# 51. Secret Rotation

今天先理解：

```text
Secret
 ↓
Version 1
```

Rotation：

```text
Version 2
```

最终：

```text
Current
Previous
```

生产数据库建议逐步实现自动 Rotation。

对于 RDS，优先考虑 AWS 原生的 Secrets Manager 集成/轮换能力，而不是自己写密码轮换脚本。

---

# 52. 一个非常重要的区别

### Secret

```text
DB Password
JWT Secret
API Key
```

放：

```text
Secrets Manager
```

### Configuration

```text
SERVER_PORT=8080
LOG_LEVEL=INFO
AWS_REGION=us-west-2
```

可以：

```text
Environment Variables
SSM Parameter Store
```

不要所有东西都塞 Secrets Manager。

---

# 53. Configuration 分类

建议：

```text
Configuration
├── Non-sensitive
│   └── Environment Variable / SSM
│
└── Sensitive
    └── Secrets Manager
```

例如：

```text
DB_HOST
→ Configuration

DB_PASSWORD
→ Secret
```

---

# 54. Day 23 最终架构

```text
                         GitHub
                            │
                       CI/CD Build
                            │
                            ▼
                           ECR
                            │
                            ▼
                     ECS Fargate
                            │
                     ECS Task Role
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
      Secrets Manager     Bedrock             S3
          │
     ┌────┴─────┐
     ▼          ▼
 Database     JWT
 Secret       Secret
     │          │
     └────┬─────┘
          ▼
      Spring Boot
          │
     ┌────┴─────┐
     ▼          ▼
    RDS       AI/RAG
```

---

# 55. Day 23 检查清单

```text
[ ] Secrets Manager
[ ] DEV Secret
[ ] TEST Secret
[ ] PROD Secret
[ ] ECS Task Role
[ ] GetSecretValue
[ ] Least Privilege
[ ] DB Password 不在 Git
[ ] JWT Secret 不在 Git
[ ] API Key 不在 Git
[ ] Docker 不包含 .env
[ ] Docker 不包含 Secret
[ ] Spring Boot ${ENV_VAR}
[ ] Bedrock 使用 IAM Role
[ ] S3 使用 IAM Role
[ ] CloudTrail
[ ] Secret Rotation 基础
[ ] Gitleaks
[ ] Terraform 不保存 Secret Value
```

---

# Day 1 → Day 23

现在已经形成：

```text
                    GitHub
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      Terraform      Backend      Frontend
          │            │            │
          ▼            ▼            ▼
         AWS          ECR           S3
                       │             │
                       ▼             ▼
                      ECS        CloudFront
                       │
          ┌────────────┼─────────────┐
          ▼            ▼             ▼
         RDS        Bedrock      Secrets Manager
          │            │             │
          └────────────┼─────────────┘
                       ▼
                 Spring Boot AI
                       │
              ┌────────┴────────┐
              ▼                 ▼
             RAG             Observability
              │                 │
          pgvector          OTel/X-Ray
                                │
                           CloudWatch
                                │
                              SNS
```

**Day 24** 下一步建议做 **AWS RDS PostgreSQL + pgvector + Spring AI RAG**：正式把数据库、向量数据库、Embedding、Chunking、Similarity Search 和 Spring AI 串起来。这样你的 AWS Java 环境就开始从“企业 Web 应用”进入真正的 **Java + AI/RAG 应用开发平台**。
