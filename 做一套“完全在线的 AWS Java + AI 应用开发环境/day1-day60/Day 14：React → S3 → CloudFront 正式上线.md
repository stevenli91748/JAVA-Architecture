# Day 14：React → S3 → CloudFront 正式上线

今天把前端从：

```text id="x1m5k7"
localhost:5173
```

升级成：

```text id="p7q2v9"
https://app.yourdomain.com
```

最终架构：

```text id="4h8n2c"
                    Internet
                       │
                       ▼
              app.yourdomain.com
                       │
                       ▼
                  CloudFront
                       │
                       ▼
                     S3
                       │
                       │
              React Static Files
                       │
                       ▼
                API HTTPS
                       │
                       ▼
          api.yourdomain.com
                       │
                       ▼
                     ALB
                       │
                       ▼
                 ECS Fargate
```

---

# 1. 今天完成

```text id="r4m8x2"
[1] React production build
[2] S3 Bucket
[3] S3 private access
[4] CloudFront
[5] Origin Access Control
[6] ACM certificate
[7] Route 53
[8] app.yourdomain.com
[9] React → API HTTPS
```

---

# 2. React 配置生产 API

进入：

```text id="f6n2q8"
frontend/.env.production
```

设置：

```env id="b9k4x1"
VITE_API_BASE_URL=https://api.yourdomain.com
```

不要：

```env id="j3m7p5"
VITE_API_BASE_URL=http://...
```

生产环境全部 HTTPS。

---

# 3. React Build

进入：

```bash id="x8q2m6"
cd frontend
```

安装：

```bash id="p4n7v3"
npm install
```

运行测试：

```bash id="m9k3x5"
npm test
```

然后：

```bash id="r7v2c8"
npm run build
```

Vite 通常生成：

```text id="z5m8q1"
dist/
├── index.html
└── assets/
```

检查：

```bash id="k2x9m4"
ls -lah dist
```

---

# 4. 本地测试 Production Build

先：

```bash id="h7q3n1"
npm run preview
```

打开：

```text id="x4m8p2"
http://localhost:4173
```

测试：

```text id="q9v3k5"
Login
 ↓
Users
 ↓
AI Chat
 ↓
RAG
```

确认 API 指向：

```text id="j6m2x8"
https://api.yourdomain.com
```

---

# 5. 创建 S3 Bucket

进入：

[Amazon S3 Console](https://console.aws.amazon.com/s3/?utm_source=chatgpt.com)

选择：

```text id="u8n3m7"
Create bucket
```

名称必须全球唯一，例如：

```text id="f4q8x2"
yourdomain-com-frontend
```

---

# 6. S3 设置

Region：

```text id="c7m2p9"
us-west-2
```

Object Ownership：

```text id="v5x8n3"
Bucket owner enforced
```

Block Public Access：

```text id="s4k9m2"
全部开启
```

**不要为了让 CloudFront 访问而把 S3 设置成 Public。**

正确架构是：

```text id="e7p3x5"
Internet
   ↓
CloudFront
   ↓
Private S3
```

---

# 7. 为什么 S3 不公开

错误：

```text id="x5n8q2"
Internet
   ↓
S3 Public
```

正确：

```text id="m7p3k9"
Internet
   ↓
CloudFront
   ↓
Origin Access Control
   ↓
Private S3
```

这样用户不能绕过 CloudFront 直接读取 S3。

---

# 8. 上传 React

AWS CLI：

```bash id="w4m8x1"
aws s3 sync dist/ \
s3://yourdomain-com-frontend
```

检查：

```bash id="p9q3k6"
aws s3 ls \
s3://yourdomain-com-frontend
```

应该看到：

```text id="n2x7m4"
index.html
assets/
```

---

# 9. 不要使用 S3 Website Endpoint

以前很多教程：

```text id="h6m3q9"
S3 Static Website Hosting
```

现在对于生产架构，我们更推荐：

```text id="v8k2p5"
CloudFront
   ↓
Private S3
```

不需要让 S3 本身公开。

---

# 10. 创建 CloudFront Distribution

进入：

[Amazon CloudFront Console](https://console.aws.amazon.com/cloudfront/?utm_source=chatgpt.com)

选择：

```text id="x3n8q6"
Create distribution
```

Origin：

选择你的：

```text id="f7m2k4"
S3 bucket
```

例如：

```text id="s1c8v5"
yourdomain-com-frontend
```

---

# 11. Origin Access Control

选择：

```text id="j4p9x2"
Origin access control settings
```

创建：

```text id="q8m3n7"
OAC
```

Signing behavior：

```text id="w5k2v9"
Sign requests
```

Signing protocol：

```text id="c3x7m1"
SigV4
```

这样：

```text id="g9p4q2"
CloudFront
   ↓
Signed Request
   ↓
S3
```

---

# 12. S3 Bucket Policy

CloudFront 创建以后，会提示你更新 S3 Bucket Policy。

进入：

```text id="r6m2x8"
S3
 → Bucket
 → Permissions
 → Bucket Policy
```

使用 CloudFront 给出的 Policy。

不要自己随便写：

```text id="k5n9p3"
Principal: "*"
```

CloudFront OAC 的 Bucket Policy 应该只允许对应 CloudFront Distribution。

---

# 13. Default Root Object

CloudFront：

```text id="z7q3m8"
Settings
```

设置：

```text id="v4p9x2"
Default root object:

index.html
```

否则访问：

```text id="y2m7c5"
https://app.yourdomain.com/
```

可能出现：

```text id="p8x3n6"
403
```

---

# 14. SPA 最重要的配置

React 是 SPA：

```text id="k6m2q9"
/
 /login
 /users
 /chat
 /rag
```

但是 CloudFront 如果用户直接访问：

```text id="w3x7p1"
https://app.yourdomain.com/chat
```

S3 可能找不到：

```text id="r8m4c2"
chat
```

返回：

```text id="d5p9x7"
404
```

所以需要把：

```text id="v2k8m3"
404
403
```

返回：

```text id="a9q4x6"
index.html
```

---

# 15. CloudFront Error Response

进入：

```text id="f3m8x1"
CloudFront
 → Distribution
 → Error responses
```

创建：

```text id="y7p2k5"
HTTP 403
```

Response page：

```text id="q4x9m3"
/index.html
```

Response code：

```text id="c8m2v7"
200
```

再做：

```text id="n6x3p9"
HTTP 404
```

同样：

```text id="w2k8m5"
/index.html

Response:
200
```

这样 React Router 可以正常工作。

---

# 16. ACM Certificate

如果你的：

```text id="api.yourdomain.com"
```

已经有 Certificate。

现在还需要：

```text id="x7m3q9"
app.yourdomain.com
```

创建 ACM Certificate。

进入：

[AWS Certificate Manager Console](https://console.aws.amazon.com/acm/?utm_source=chatgpt.com)

选择：

```text id="j5n8k2"
Request certificate
```

Domain：

```text id="q4x7m1"
app.yourdomain.com
```

Validation：

```text id="p9m3c6"
DNS
```

---

# 17. 非常重要：CloudFront ACM 必须在 us-east-1

这里和 ALB 不一样。

### ALB

```text id="w6m2x8"
ACM
us-west-2
```

### CloudFront

```text id="c3q7n5"
ACM
us-east-1
```

这是 CloudFront 的特殊要求。

所以：

```text id="h9x4m2"
CloudFront Certificate
=
us-east-1
```

不要在 `us-west-2` 创建 CloudFront Certificate。

---

# 18. DNS Validation

Route 53：

```text id="p7m3x9"
app.yourdomain.com
```

创建 ACM 提供的 CNAME。

等待：

```text id="k4n8q2"
Issued
```

---

# 19. CloudFront Custom Domain

回到：

```text id="s2x7m5"
CloudFront
 → Distribution
 → Settings
```

Alternate domain name：

```text id="j8q3v6"
app.yourdomain.com
```

Certificate：

```text id="f4m9x2"
app.yourdomain.com
```

---

# 20. Viewer Protocol Policy

设置：

```text id="v7p2n5"
Redirect HTTP to HTTPS
```

所以：

```text id="n6m3x8"
http://app.yourdomain.com
        ↓
https://app.yourdomain.com
```

---

# 21. Route 53

进入：

```text id="x8q4m1"
Route 53
 → Hosted zones
 → yourdomain.com
```

创建：

```text id="m3p7x9"
Name:
app
```

Type：

```text id="c5n8q2"
A
```

Alias：

```text id="v2m6k4"
Yes
```

Route traffic to：

```text id="r7x3p9"
CloudFront distribution
```

选择你的 Distribution。

---

# 22. 最终 DNS

```text id="j8m4q2"
yourdomain.com
│
├── app.yourdomain.com
│        ↓
│     CloudFront
│        ↓
│       S3
│
└── api.yourdomain.com
         ↓
        ALB
         ↓
        ECS
         ↓
        RDS
```

这就是非常标准的：

**Frontend / Backend 分离架构。**

---

# 23. 测试前端

浏览器：

```text id="q3m7x9"
https://app.yourdomain.com
```

应该看到：

```text id="8k2p5m"
Login
```

---

# 24. 测试 Login

```text id="f6n3x8"
React
 ↓
https://api.yourdomain.com/api/auth/login
 ↓
ALB
 ↓
ECS
 ↓
Spring Security
 ↓
JWT
```

登录成功以后：

```text id="m9q4p2"
JWT
 ↓
Axios
 ↓
Authorization: Bearer ...
```

---

# 25. 测试 AI

打开：

```text id="c7x2m8"
AI Chat
```

测试：

```text id="j4n8p3"
Explain Spring Boot
```

请求：

```text id="s5m2x7"
app.yourdomain.com
        ↓
api.yourdomain.com
        ↓
ECS
        ↓
Spring AI
        ↓
Bedrock
```

---

# 26. 测试 RAG

测试：

```text id="v8p3m6"
What is our PTO policy?
```

流程：

```text id="k2x7n4"
React
 ↓
CloudFront
 ↓
API
 ↓
ECS
 ↓
RAG
 ↓
pgvector
 ↓
Bedrock
 ↓
Answer
```

---

# 27. CORS 最终配置

现在：

```text id="w6m3q8"
Frontend:

https://app.yourdomain.com
```

Backend：

```text id="p4n7x2"
https://api.yourdomain.com
```

Spring Boot：

```yaml id="x8q3m5"
app:
  cors:
    allowed-origins: https://app.yourdomain.com
```

不要：

```text id="j7m2v9"
*
```

---

# 28. CloudFront Cache

React：

```text id="m5x8q3"
index.html
JS
CSS
images
```

可以缓存。

但是：

```text id="k2p7n4"
API
```

不要通过 CloudFront 缓存。

我们目前：

```text id="f8m3x6"
CloudFront → S3
```

和：

```text id="r4q9p2"
ALB → ECS
```

完全分开。

这是非常清晰的架构。

---

# 29. React 静态文件 Cache

Vite 生成：

```text id="y7m3k8"
assets/index-abc123.js
```

这种带 hash 的文件：

```text id="v2p9x4"
可以长期缓存
```

例如：

```text id="n6m3q7"
Cache-Control:
public,max-age=31536000,immutable
```

但：

```text id="c4x8p2"
index.html
```

不要缓存一年。

否则部署新版本后用户可能继续拿旧的 `index.html`。

---

# 30. 部署新 React 版本

修改：

```text id="q8m3x5"
frontend/src/
```

然后：

```bash id="j4p7n2"
npm run build
```

上传：

```bash id="v6x3m8"
aws s3 sync dist/ \
s3://yourdomain-com-frontend
```

然后 CloudFront：

```text id="r9m2k5"
Create invalidation
```

Path：

```text id="x7p4n8"
/*
```

这样用户马上拿到新版本。

---

# 31. 今天最重要的安全架构

不要：

```text id="q3m8x1"
Browser
   ↓
S3 Public
```

而是：

```text id="k7p2m9"
Browser
   ↓
CloudFront HTTPS
   ↓
OAC
   ↓
Private S3
```

Backend：

```text id="v4x8n2"
Browser
   ↓
HTTPS
   ↓
ALB
   ↓
ECS
```

Database：

```text id="m6q3p9"
ECS
   ↓
Private
   ↓
RDS
```

---

# 32. Day 14 最终架构

```text id="7gk2wq"
                         Internet
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
    app.yourdomain.com            api.yourdomain.com
             │                             │
             ▼                             ▼
        CloudFront                       ALB
             │                          HTTPS
             ▼                             │
        Private S3                         ▼
             │                         ECS Fargate
             │                             │
             │                    ┌────────┼────────┐
             │                    │        │        │
             │                    ▼        ▼        ▼
             │                 Security  Spring   Spring AI
             │                   │       Boot       │
             │                    │        │         ▼
             │                    │        │       Bedrock
             │                    │        │
             │                    │        ▼
             │                    │       RDS
             │                    │      pgvector
             │                    │
             └────────────────────┘
```

---

# 33. Day 14 检查清单

```text id="q9m3x7"
[ ] React npm run build
[ ] S3 Bucket
[ ] S3 Block Public Access
[ ] CloudFront
[ ] Origin Access Control
[ ] Private S3
[ ] Default root object
[ ] SPA 403 → index.html
[ ] SPA 404 → index.html
[ ] ACM us-east-1
[ ] app.yourdomain.com
[ ] Route 53 Alias
[ ] HTTPS
[ ] React → API
[ ] CORS
[ ] Login
[ ] AI Chat
[ ] RAG
```

---

# Day 1 → Day 14

现在你的平台已经是：

```text id="4x9m2p"
                         AWS
                          │
          ┌───────────────┴────────────────┐
          │                                │
       FRONTEND                          BACKEND
          │                                │
     CloudFront                            ALB
          │                                │
        S3                            ECS Fargate
                                           │
                              ┌────────────┼────────────┐
                              │            │            │
                         Spring Boot   Spring AI    Security
                              │            │            │
                              │         Bedrock       JWT
                              │
                              ▼
                           RDS
                         PostgreSQL
                           + pgvector
                              │
                              ▼
                             RAG

                   Secrets Manager
                         │
                         ▼
                    ECS Secrets

GitHub
   ↓
CodePipeline
   ↓
CodeBuild
   ↓
ECR
   ↓
ECS
```

### Day 15

下一步建议做 **Terraform Infrastructure as Code**：

```text
Terraform
   ↓
VPC
RDS
ECS
ALB
ECR
S3
CloudFront
Route 53
IAM
Secrets Manager
```

这样你前 14 天手工创建的 AWS 基础设施，可以全部变成：

```bash
terraform plan
terraform apply
```

以后换 AWS Region、建立 DEV/TEST/PROD 环境，都可以自动重建，而不是一个个在 Console 里点击。
