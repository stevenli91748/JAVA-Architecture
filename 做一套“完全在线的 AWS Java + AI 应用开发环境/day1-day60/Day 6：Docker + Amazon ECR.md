# Day 6：Docker + Amazon ECR

今天把昨天的 Spring Boot 应用变成**标准 Docker 镜像**，然后上传到 AWS ECR。

最终：

```text id="0e9t2m"
Spring Boot
    ↓
Maven Package
    ↓
Docker Image
    ↓
Amazon ECR
    ↓
Day 7 → ECS Fargate
```

今天先不部署 ECS。

---

## 1. 今天完成

```text id="4e7v1n"
[1] Spring Boot 打包
[2] Dockerfile
[3] 本地 Docker 运行
[4] ECR Repository
[5] AWS IAM / ECR 登录
[6] Push Docker Image
[7] 从 ECR 验证 Image
```

---

# 2. 确认项目

Codespaces Terminal：

```bash id="v9k4ps"
cd ~/aws-java-ai-platform/backend
```

检查：

```bash id="j0m8az"
ls
```

应该：

```text id="0i7v4d"
pom.xml
src
mvnw
```

---

# 3. Maven 打包

先：

```bash id="v1l6xu"
./mvnw clean test
```

成功：

```text id="0u8b7c"
BUILD SUCCESS
```

然后：

```bash id="1h7x0e"
./mvnw clean package -DskipTests
```

查看：

```bash id="1kq2w6"
ls target
```

应该看到：

```text id="c5m3p9"
xxx-0.0.1-SNAPSHOT.jar
```

---

# 4. 创建 Dockerfile

在：

```text id="a0c9x2"
backend/Dockerfile
```

写：

```dockerfile id="ajqf8p"
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

这里使用：

```text id="2j9q1a"
Java 21
JRE
```

不需要把完整 JDK 放进最终生产镜像。

---

# 5. 创建 `.dockerignore`

```text id="f0n4r3"
backend/.dockerignore
```

内容：

```text id="k5v2e9"
target/
.git/
.gitignore
.idea/
*.log
```

---

# 6. Build Docker Image

进入：

```bash id="6x8m5j"
cd backend
```

运行：

```bash id="c9h3k7"
docker build \
-t java-ai-backend:1.0.0 .
```

检查：

```bash id="x4q7d1"
docker images
```

应该看到：

```text id="z2m8n4"
java-ai-backend
1.0.0
```

---

# 7. 本地运行 Docker

先注意：

你的 Docker 容器不能使用：

```text id="x6v9c3"
localhost:5432
```

因为 Docker Container 里的 `localhost` 是**容器自己**。

如果今天只是验证 Spring Boot 本身，可以先运行：

```bash id="n5y2r8"
docker run \
-p 8080:8080 \
-e DB_HOST=YOUR_RDS_ENDPOINT \
-e DB_NAME=enterprise_ai \
-e DB_USERNAME=app_admin \
-e DB_PASSWORD=YOUR_PASSWORD \
java-ai-backend:1.0.0
```

然后：

```bash id="s2k6v0"
curl http://localhost:8080/actuator/health
```

如果得到：

```json id="h8q1m3"
{
  "status": "UP"
}
```

说明：

```text id="q4t7x9"
Docker
  ↓
Spring Boot
  ↓
RDS
```

已经可以工作。

---

# 8. 一个重要问题

现在：

```text id="j9c1w5"
Docker
   ↓
Internet
   ↓
RDS
```

这只是 Day 5/6 开发环境。

最终：

```text id="d4m7v2"
ECS Fargate
      │
      │ Private Subnet
      ▼
RDS
      │
      └── Private
```

Day 7 会解决。

---

# 9. 创建 ECR Repository

打开 AWS Console：

[Amazon ECR Console](https://console.aws.amazon.com/ecr/?utm_source=chatgpt.com)

Region：

```text id="p3v6x8"
us-west-2
```

进入：

```text id="z7c5m1"
Elastic Container Registry
 ↓
Repositories
 ↓
Create repository
```

Repository name：

```text id="b8n2q4"
java-ai-backend
```

建议：

```text id="w3f5s7"
Visibility:
Private
```

然后：

```text id="r1k6d9"
Create repository
```

---

# 10. 获取 ECR URI

进入：

```text id="v2c8n5"
java-ai-backend
```

你会看到类似：

```text id="0q7p3x"
123456789012.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend
```

记住：

```text id="s8m1k4"
ACCOUNT_ID
REGION
ECR_URI
```

---

# 11. AWS CLI 登录 ECR

在 Codespaces：

```bash id="e6n9v2"
aws sts get-caller-identity
```

如果还没有 AWS 身份认证，这一步会失败。

**这是正常的。**

Day 2 我们没有创建永久 AWS Access Key。

现在我们需要解决：

```text id="a4q8j6"
Codespaces
      ↓
AWS IAM
```

---

# 12. 推荐的认证方式

对于长期使用的 Codespaces：

```text id="k7d2p5"
GitHub Codespaces
        ↓
GitHub OIDC
        ↓
AWS IAM Role
```

而不是：

```text id="u3n8c1"
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

这是我们后面企业化 CI/CD 的方向。

不过为了**今天先把 ECR 跑通**，如果你现在还没有配置 GitHub → AWS OIDC，可以使用临时开发凭证完成 Day 6，然后 Day 10 再正式改成 OIDC。

---

# 13. 如果你已经有 AWS CLI Credentials

执行：

```bash id="p0c7y4"
aws sts get-caller-identity
```

正常应该返回：

```json id="q8v2n6"
{
    "UserId": "...",
    "Account": "123456789012",
    "Arn": "..."
}
```

确认：

```text id="x5m9r1"
Account
=
你的 AWS Account
```

---

# 14. ECR Login

运行：

```bash id="d3h7k2"
aws ecr get-login-password \
--region us-west-2 \
| docker login \
--username AWS \
--password-stdin \
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com
```

成功：

```text id="f8p1z6"
Login Succeeded
```

---

# 15. 给 Docker Image 打 ECR Tag

例如：

```text id="c4m8q2"
LOCAL:

java-ai-backend:1.0.0
```

变成：

```text id="b7n3v9"
ECR:

123456789012.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.0.0
```

命令：

```bash id="x1s6k4"
docker tag \
java-ai-backend:1.0.0 \
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.0.0
```

检查：

```bash id="h9q2m7"
docker images
```

---

# 16. Push 到 ECR

```bash id="r5k8c3"
docker push \
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.0.0
```

看到类似：

```text id="n2v6x9"
Layer already exists
...
pushed
```

就成功了。

---

# 17. AWS Console 验证

进入：

```text id="c7m1z4"
ECR
 ↓
Repositories
 ↓
java-ai-backend
```

应该看到：

```text id="j3p8q5"
Image tag

1.0.0
```

点击进去可以看到：

```text id="f6n2w8"
Image digest
Image size
Push time
Tag
```

这说明你的 Java Docker 镜像已经进入 AWS。

---

# 18. 现在你的架构已经发生变化

昨天：

```text id="w4r7t1"
Codespaces
   │
   ▼
Spring Boot
   │
   ▼
RDS
```

今天：

```text id="u9c3k6"
Codespaces
   │
   ▼
Spring Boot
   │
   ▼
Docker
   │
   ▼
ECR
```

同时：

```text id="q2m5v8"
Docker
   │
   ▼
RDS
```

也可以测试。

---

# 19. Docker Image 为什么重要？

以后 ECS 不需要：

```text id="n6t1p3"
安装 Java
安装 Maven
复制 JAR
配置环境
```

ECS 只需要：

```text id="y8k4s2"
Pull:

java-ai-backend:1.0.0

↓

Run Container
```

也就是：

```text id="m7v2q9"
ECR
 │
 │ docker image
 ▼
ECS Fargate
 │
 ▼
Spring Boot
```

这就是我们 Day 7 要完成的。

---

# 20. 给镜像加版本管理

以后不要全部使用：

```text id="3a7m1q"
latest
```

推荐：

```text id="h5v8k2"
1.0.0
1.0.1
1.1.0
2.0.0
```

例如：

```text id="z4p9n6"
java-ai-backend:1.0.0
java-ai-backend:1.0.1
java-ai-backend:1.1.0
```

这样 ECS 可以明确知道运行哪个版本。

---

# 21. 建议加入 Git SHA

企业 CI/CD 更进一步：

```text id="k2r7m4"
java-ai-backend:
    8f4a21c
```

即：

```text id="c6v9x1"
Git Commit
     ↓
Docker Image
     ↓
ECR
     ↓
ECS
```

这样发生生产问题时可以追踪：

```text id="b3q8m5"
Production
   ↓
Image
   ↓
Commit
   ↓
Developer Change
```

Day 10 CI/CD 我们会自动做。

---

# 22. Day 6 最终架构

```text id="p9x4c7"
                   GitHub
                      │
                      ▼
              GitHub Codespaces
                      │
                      ▼
                Spring Boot
                      │
                 mvn package
                      │
                      ▼
                    Docker
                      │
               docker build
                      │
                      ▼
                    ECR
                      │
               java-ai-backend
                      │
                      ▼
              ┌──── Day 7 ────┐
              │                │
              ▼                ▼
          ECS Fargate        ALB
              │
              ▼
             RDS
```

---

# Day 6 检查清单

```text id="s5k2m8"
[ ] mvn clean test
[ ] mvn package
[ ] Dockerfile
[ ] docker build
[ ] docker run
[ ] /actuator/health
[ ] ECR Repository
[ ] AWS CLI
[ ] aws sts get-caller-identity
[ ] ECR login
[ ] docker tag
[ ] docker push
[ ] ECR 中看到 1.0.0
```

## 今天最重要的成果

你现在已经完成：

```text
Day 1
AWS Account / IAM
       ↓
Day 2
Online Cloud IDE
       ↓
Day 3
Spring Boot / PostgreSQL
       ↓
Day 4
React / Spring Boot
       ↓
Day 5
AWS RDS
       ↓
Day 6
Docker / ECR
```

**Day 7 就是整个项目第一个真正的 AWS 部署日：**

```text
ECR
 ↓
ECS Fargate
 ↓
ALB
 ↓
Spring Boot
 ↓
RDS
```

最终你会拿到一个真正的公网 URL，在浏览器访问你的 Java 企业应用。
