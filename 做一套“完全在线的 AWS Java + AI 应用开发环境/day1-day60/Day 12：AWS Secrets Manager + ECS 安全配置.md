# Day 12：AWS Secrets Manager + ECS 安全配置

今天专门解决 Day 11 留下的两个问题：

```text id="e8x4r1"
DB_PASSWORD
JWT_SECRET
```

**不能继续放在：**

```text id="c2m7v9"
GitHub
Dockerfile
application.yml
Task Definition 明文 Environment Variables
```

今天改成：

```text id="n5k3q8"
AWS Secrets Manager
        │
        ▼
    ECS Task
        │
   ┌────┴─────┐
   ▼          ▼
DB_PASSWORD  JWT_SECRET
```

---

# 1. 今天完成什么

```text id="r7m2x9"
Day 12
│
├── Secrets Manager
├── DB credentials
├── JWT secret
├── ECS Task Role
├── ECS Secrets injection
├── Spring Boot environment variables
└── Secret rotation 基础
```

最终：

```text
GitHub
   │
   │ ❌ 没有密码
   ▼
Docker Image
   │
   │ ❌ 没有密码
   ▼
ECR
   │
   ▼
ECS
   │
   ├──────────────┐
   ▼              ▼
Secrets Manager   RDS
   │
   └── secrets
```

---

# 2. 先检查有没有 Secret 泄露

在项目根目录：

```bash id="g5v2m9"
git grep -n "DB_PASSWORD"
```

再：

```bash id="x3q8k1"
git grep -n "JWT_SECRET"
```

检查：

```bash id="m9r4p2"
git grep -n "password:"
```

如果看到：

```yaml id="f7c3n8"
password: myRealPassword
```

**立即删除。**

---

# 3. `.gitignore`

确认：

```text id="k8x2m5"
backend/.env
.env
.env.*
!.env.example
```

但注意：

`.gitignore` 只能防止以后提交。

如果密码已经进入 Git 历史：

```text id="w4p7n1"
Git history
```

那么仅仅删除当前文件**不够**。

生产 Secret 如果已经进入 GitHub：

**应该立即更换。**

---

# 4. 创建 Secrets Manager

进入：

[AWS Secrets Manager Console](https://console.aws.amazon.com/secretsmanager/?utm_source=chatgpt.com)

选择：

```text id="x5m8q3"
Store a new secret
```

---

# 5. 创建数据库 Secret

Secret type：

```text id="s7n2k4"
Other type of secret
```

选择：

```text id="q3v8m1"
Key/value pairs
```

输入：

```text id="j5c9x2"
username:
app_admin

password:
你的RDS密码

host:
你的RDS endpoint

port:
5432

dbname:
enterprise_ai
```

最终：

```json id="w8m3p6"
{
  "username": "app_admin",
  "password": "********",
  "host": "java-ai-postgres.xxxxx.rds.amazonaws.com",
  "port": "5432",
  "dbname": "enterprise_ai"
}
```

---

# 6. Secret Name

命名：

```text id="n6k4r8"
prod/java-ai/database
```

如果现在只是 DEV：

```text id="c2p7x9"
dev/java-ai/database
```

我建议从现在开始建立：

```text id="1v5m8q"
dev/
test/
prod/
```

例如：

```text id="y4k9n2"
dev/java-ai/database
test/java-ai/database
prod/java-ai/database
```

这样以后环境不会混在一起。

---

# 7. 创建 JWT Secret

再次：

```text id="m7x3k8"
Store a new secret
```

选择：

```text id="p4n9c2"
Other type of secret
```

Key：

```text id="f8m2q5"
JWT_SECRET
```

Value：

生成一个真正随机的高强度 Secret。

不要使用：

```text id="g3x7v1"
123456
secret
mysecret
```

---

# 8. 用 OpenSSL 生成 JWT Secret

Codespaces：

```bash id="s8q4m2"
openssl rand -base64 48
```

得到类似：

```text id="n7c3p9"
q8X...random...7K=
```

把这个值放入：

```text id="r4m8x2"
dev/java-ai/jwt
```

Secret：

```json id="c7p2n5"
{
  "JWT_SECRET": "YOUR_RANDOM_SECRET"
}
```

---

# 9. Secrets 命名

现在：

```text id="m2x7q4"
Secrets Manager
│
├── dev/java-ai/database
│
└── dev/java-ai/jwt
```

以后：

```text id="k8p3n6"
Secrets Manager
│
├── dev/
│   ├── java-ai/database
│   └── java-ai/jwt
│
├── test/
│   ├── java-ai/database
│   └── java-ai/jwt
│
└── prod/
    ├── java-ai/database
    └── java-ai/jwt
```

---

# 10. IAM 权限

现在 ECS 必须有权限：

```text id="x5m9k2"
ECS Task
   ↓
Secrets Manager
```

进入：

```text id="j7c3v8"
IAM
 → Roles
 → java-ai-ecs-task-role
```

添加一个**自定义 Policy**。

不要直接给：

```text id="q2m8p4"
SecretsManagerFullAccess
```

生产环境不应该这样。

---

# 11. 最小权限 Policy

类似：

```json id="w4n7x1"
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": [
        "arn:aws:secretsmanager:us-west-2:ACCOUNT_ID:secret:dev/java-ai/database-*",
        "arn:aws:secretsmanager:us-west-2:ACCOUNT_ID:secret:dev/java-ai/jwt-*"
      ]
    }
  ]
}
```

把：

```text id="e9x3m7"
ACCOUNT_ID
```

换成你的 AWS Account ID。

---

# 12. 为什么 ARN 后面有 `-*`

Secrets Manager Secret ARN 通常包含 AWS 生成的后缀。

所以：

```text id="p6m2k9"
dev/java-ai/database-xxxxx
```

而不是只写：

```text id="c8x4v1"
dev/java-ai/database
```

---

# 13. ECS Task Definition

进入：

```text id="v7n3m5"
ECS
 → Task Definitions
 → java-ai-backend
 → Create new revision
```

找到：

```text id="x2q8k4"
Container
 → Environment variables
```

以前：

```text id="f9m3p7"
DB_PASSWORD = ********
JWT_SECRET = ********
```

删除。

---

# 14. ECS Secrets

在 Container：

```text id="c5v8n2"
Environment variables
 → Secrets
```

添加：

```text id="q7m4x9"
Name:
DB_PASSWORD
```

Secret：

```text id="j3n8p5"
dev/java-ai/database:password::
```

再：

```text id="k2x7m4"
Name:
DB_HOST
```

Secret：

```text id="r8p3c6"
dev/java-ai/database:host::
```

再：

```text id="v5n9q2"
Name:
DB_USERNAME
```

Secret：

```text id="w3m7k1"
dev/java-ai/database:username::
```

再：

```text id="a9x4p8"
Name:
DB_NAME
```

Secret：

```text id="t6k2m5"
dev/java-ai/database:dbname::
```

最后：

```text id="e4q8n3"
Name:
JWT_SECRET
```

Secret：

```text id="y7m2v9"
dev/java-ai/jwt:JWT_SECRET::
```

---

# 15. Spring Boot 不需要修改

昨天：

```yaml id="r2m7x5"
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:5432/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}

jwt:
  secret: ${JWT_SECRET}
```

今天仍然这样。

区别是：

```text id="j9x3m6"
ECS
 ↓
Environment variable
 ↓
Spring Boot
```

而 Secret 来源变成：

```text id="p8k4v2"
Secrets Manager
```

所以代码完全不知道 Secret 存在哪里。

---

# 16. 非常重要：不要把 Secret 放 Docker Image

检查 Docker：

```bash id="z6m3q8"
docker history java-ai-backend:1.1.0
```

不能看到：

```text id="n4p7x2"
DB_PASSWORD
JWT_SECRET
```

Docker Image 应该是：

```text id="c9m5v1"
Java Application
+
Dependencies
+
Config structure
```

而不是：

```text id="x7k2p4"
Java Application
+
Passwords
+
API Keys
```

---

# 17. ECS 新 Revision

保存 Task Definition：

```text id="v8m3q5"
Revision:
New
```

然后更新 Service：

```text id="j4n7x2"
ECS
 → Cluster
 → Service
 → Update
```

选择新的 Task Definition。

---

# 18. Deploy

ECS：

```text id="q2m8x5"
Old Task
    ↓
Stopping

New Task
    ↓
Starting
```

CloudWatch：

```text id="n6v3k9"
Spring Boot
Tomcat started
```

如果：

```text id="f4x8m2"
Task STOPPED
```

查看：

```text id="y7p3q5"
Stopped reason
```

重点检查：

```text id="k3m8v1"
IAM Permission
Secret ARN
Secret name
RDS connection
```

---

# 19. 测试数据库

访问：

```text id="r5x9m2"
GET /api/users
```

如果：

```text id="p7k3v8"
200 OK
```

说明：

```text id="h2m6q4"
ECS
 ↓
Secrets Manager
 ↓
DB Credentials
 ↓
RDS
```

成功。

---

# 20. 测试 JWT

登录：

```bash id="x3n8q5"
POST /api/auth/login
```

如果成功：

```json id="j7m2v9"
{
  "accessToken": "eyJ..."
}
```

说明：

```text id="c8p4x6"
ECS
 ↓
Secrets Manager
 ↓
JWT_SECRET
 ↓
Spring Security
 ↓
JWT
```

成功。

---

# 21. 最重要的安全原则

以后所有环境都遵守：

```text id="g4m9x2"
Source Code
    ❌ password

GitHub
    ❌ password

Dockerfile
    ❌ password

Docker Image
    ❌ password

ECR
    ❌ password

Task Definition
    ❌ password

Secrets Manager
    ✅ password
```

---

# 22. Bedrock API Key 呢？

如果使用：

```text id="x7p2m4"
ECS
 ↓
IAM Task Role
 ↓
Bedrock
```

那么：

**不需要 API Key。**

这是 AWS Bedrock + ECS 的一个重要优势。

```text id="m9k3v7"
Spring AI
   ↓
AWS SDK
   ↓
IAM Task Role
   ↓
Bedrock
```

---

# 23. 如果以后接 OpenAI / Anthropic API

那就：

```text id="c4x8m2"
Secrets Manager

OPENAI_API_KEY
ANTHROPIC_API_KEY
```

然后：

```text id="v7p3n9"
ECS
 ↓
Secrets Manager
 ↓
Environment Variable
 ↓
Spring AI
```

仍然不把 Key 放 GitHub。

---

# 24. Secret Rotation

数据库密码以后可以做：

```text id="n5k8q3"
Secrets Manager
       ↓
Rotation
       ↓
New Password
       ↓
RDS
```

但今天**不要急着开启自动 Rotation**。

因为真正生产环境还需要设计：

```text id="w2m7x4"
Application
      ↓
Secret Version
      ↓
Connection Pool
      ↓
Password Rotation
```

否则简单轮换密码可能导致现有连接异常。

Day 12 先理解机制即可。

---

# 25. 一个很重要的改进：RDS Credentials

更标准的生产架构是：

```text id="a8x3m6"
RDS
 ↓
AWS-managed credentials
 ↓
Secrets Manager
```

而不是：

```text id="k4p9v2"
Developer
 ↓
手工创建密码
 ↓
复制到多个地方
```

以后我们会进一步把 RDS Credentials 和 Secrets Manager 的生命周期统一起来。

---

# 26. Day 12 最终架构

```text id="u5m8q2"
                         Internet
                            │
                            ▼
                           ALB
                            │
                            ▼
                      ECS Fargate
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
       Spring Security   Spring AI       RAG
             │              │              │
             │              ▼              │
             │           Bedrock            │
             │                             │
             ▼                             ▼
        Secrets Manager               PostgreSQL
             │                        + pgvector
             │
       ┌─────┴──────┐
       │            │
 DB_PASSWORD    JWT_SECRET
```

---

# 27. Day 12 检查清单

```text id="q7m3x9"
[ ] Secrets Manager
[ ] DB Secret
[ ] JWT Secret
[ ] IAM least privilege
[ ] ECS Secrets
[ ] DB_PASSWORD 不在 GitHub
[ ] JWT_SECRET 不在 GitHub
[ ] Docker Image 没有 Secret
[ ] Task Definition 没有明文密码
[ ] ECS → Secrets Manager
[ ] ECS → RDS
[ ] JWT Login 正常
[ ] /api/users 正常
```

---

# Day 1–12 的架构已经非常完整

```text id="h4x9m2"
                         GitHub
                           │
                           ▼
                    CodePipeline
                           │
                           ▼
                      CodeBuild
                           │
                           ▼
                          ECR
                           │
                           ▼
                    ECS Fargate
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
        Spring Boot     Spring AI    Spring Security
              │            │            │
              │            ▼            ▼
              │         Bedrock        JWT
              │
       ┌──────┴─────────────┐
       │                    │
       ▼                    ▼
      RDS              Secrets Manager
       │
   pgvector
       │
      RAG
```

## Day 13

下一步应该做：

```text id="d8m3x7"
HTTPS + Route 53 + ACM
```

把现在的：

```text
http://xxxx.elb.amazonaws.com
```

升级成：

```text
https://api.yourdomain.com
```

并同时配置：

```text
Route 53
    ↓
ACM SSL Certificate
    ↓
ALB HTTPS :443
    ↓
ECS
```

这样你的 Java + AI 应用才开始真正接近**生产级公网服务**。
