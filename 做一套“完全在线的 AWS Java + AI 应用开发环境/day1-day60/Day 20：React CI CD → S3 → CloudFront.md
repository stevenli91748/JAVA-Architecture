# Day 20：React CI/CD → S3 → CloudFront

今天把前端也接入自动部署。

Day 19 是：

```text
Java
 ↓
Maven
 ↓
Docker
 ↓
ECR
 ↓
ECS
```

今天：

```text
React
 ↓
npm test
 ↓
npm build
 ↓
S3
 ↓
CloudFront
 ↓
https://app.yourdomain.com
```

最终整个应用变成：

```text
                 GitHub
              ┌────┴────┐
              │         │
           Backend   Frontend
              │         │
              ▼         ▼
             ECR       S3
              │         │
             ECS    CloudFront
              │         │
              └────┬────┘
                   ▼
                  AWS
```

---

# 1. Day 20 学习目标

今天完成：

```text
[✓] React CI
[✓] npm ci
[✓] npm test
[✓] npm run build
[✓] S3 Upload
[✓] CloudFront Invalidation
[✓] GitHub OIDC
[✓] AWS IAM Role
[✓] DEV 自动部署
[✓] Production 部署基础
```

---

# 2. 前端项目确认

进入：

```bash
cd frontend
```

检查：

```bash
node -v
npm -v
```

建议：

```text
Node.js 22 LTS
npm 10+
```

---

# 3. package.json

确认至少有：

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "test": "vitest run",
    "preview": "vite preview"
  }
}
```

如果你使用 Jest，则保留：

```json
"test": "jest"
```

不要为了 CI/CD 强行换测试框架。

---

# 4. 本地测试

```bash
npm ci
```

然后：

```bash
npm test
```

必须：

```text
PASS
```

然后：

```bash
npm run build
```

生成：

```text
dist/
├── index.html
└── assets/
```

---

# 5. Production API 地址

`.env.production`：

```env
VITE_API_BASE_URL=https://api.yourdomain.com
```

不要：

```env
VITE_API_BASE_URL=http://localhost:8080
```

否则线上 React 会继续访问你的本机。

---

# 6. 注意 Vite 环境变量

Vite：

```text
VITE_*
```

会被打包进前端 JavaScript。

所以：

```env
VITE_API_BASE_URL=...
```

可以。

但是：

```env
VITE_OPENAI_API_KEY=...
VITE_AWS_SECRET=...
VITE_DATABASE_PASSWORD=...
```

**绝对不能出现。**

因为浏览器用户可以直接看到。

---

# 7. S3 前端 Bucket

Day 14 已经建立：

```text
yourdomain-com-frontend
```

现在由 GitHub Actions 自动上传。

架构：

```text
GitHub
 ↓
npm build
 ↓
dist/
 ↓
S3
 ↓
CloudFront
```

---

# 8. 创建 GitHub Workflow

创建：

```text
.github/workflows/frontend.yml
```

---

# 9. 基础 React CI

```yaml
name: Frontend CI/CD

on:
  pull_request:
    paths:
      - "frontend/**"

  push:
    branches:
      - main
    paths:
      - "frontend/**"

permissions:
  contents: read
  id-token: write

jobs:

  build:

    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: frontend

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "22"
          cache: npm
          cache-dependency-path: frontend/package-lock.json

      - name: Install
        run: npm ci

      - name: Test
        run: npm test

      - name: Build
        run: npm run build
```

---

# 10. 为什么使用 npm ci

CI/CD 不建议：

```bash
npm install
```

推荐：

```bash
npm ci
```

因为：

```text
package-lock.json
       ↓
精确安装依赖
```

可以减少：

```text
开发环境
≠
CI 环境
```

的问题。

---

# 11. Build Artifact

运行：

```bash
npm run build
```

结果：

```text
dist/
├── index.html
├── assets/
│   ├── index-xxxx.js
│   └── index-xxxx.css
```

这个 `dist` 就是部署包。

---

# 12. GitHub OIDC

Day 18/19 已经使用：

```text
GitHub
 ↓
OIDC
 ↓
AWS IAM Role
```

今天创建：

```text
github-frontend-dev
```

不要直接复用：

```text
github-terraform-dev
```

原因：

Terraform Role 应该管理：

```text
VPC
ECS
RDS
ALB
```

Frontend Role 只需要：

```text
S3
CloudFront
```

这就是：

**Least Privilege。**

---

# 13. Frontend IAM Role

权限至少需要：

```text
s3:PutObject
s3:DeleteObject
s3:ListBucket
cloudfront:CreateInvalidation
```

不要：

```text
AdministratorAccess
```

---

# 14. S3 IAM Policy

例如：

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::yourdomain-com-frontend"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource":
        "arn:aws:s3:::yourdomain-com-frontend/*"
    }
  ]
}
```

---

# 15. CloudFront Permission

增加：

```json
{
  "Effect": "Allow",
  "Action": [
    "cloudfront:CreateInvalidation"
  ],
  "Resource": "*"
}
```

CloudFront API 对 Distribution 资源授权有一些动作级限制，实际 Policy 以当前 AWS IAM 文档和你的 Distribution ARN 为准。

---

# 16. GitHub 配置 AWS

Workflow：

```yaml
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-frontend-dev
    aws-region: us-west-2
```

---

# 17. Upload S3

增加：

```yaml
- name: Upload to S3
  run: |
    aws s3 sync dist/ \
      s3://yourdomain-com-frontend \
      --delete
```

这里：

```text
--delete
```

非常重要。

它会让：

```text
S3
```

与：

```text
dist/
```

保持一致。

但是生产使用前要理解：

**如果你错误地 build 出一个不完整的 dist，`--delete` 会删除 S3 中原有文件。**

因此生产最好先经过测试和 artifact 验证。

---

# 18. CloudFront Invalidation

上传以后：

```yaml
- name: Invalidate CloudFront
  run: |
    aws cloudfront create-invalidation \
      --distribution-id ${{ vars.CLOUDFRONT_DISTRIBUTION_ID }} \
      --paths "/*"
```

---

# 19. GitHub Repository Variable

进入：

```text
GitHub
 → Settings
 → Secrets and variables
 → Actions
 → Variables
```

创建：

```text
CLOUDFRONT_DISTRIBUTION_ID
```

例如：

```text
E123ABC456XYZ
```

这不是 Secret，可以使用 Repository Variable。

---

# 20. 为什么需要 Invalidation

假设 CloudFront 缓存：

```text
index.html
```

你上传了新版本：

```text
index.html
```

CloudFront 可能仍然返回旧缓存。

所以：

```text
S3 Upload
   ↓
CloudFront Invalidation
   ↓
用户获取最新版本
```

---

# 21. 完整 Frontend Workflow

```yaml
name: Frontend CI/CD

on:
  pull_request:
    paths:
      - "frontend/**"

  push:
    branches:
      - main
    paths:
      - "frontend/**"

permissions:
  contents: read
  id-token: write

env:
  AWS_REGION: us-west-2
  S3_BUCKET: yourdomain-com-frontend

jobs:

  build:

    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: frontend

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "22"
          cache: npm
          cache-dependency-path: frontend/package-lock.json

      - name: Install
        run: npm ci

      - name: Test
        run: npm test

      - name: Build
        run: npm run build

      - name: Configure AWS
        if: github.event_name == 'push'
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-frontend-dev
          aws-region: ${{ env.AWS_REGION }}

      - name: Upload to S3
        if: github.event_name == 'push'
        run: |
          aws s3 sync dist/ \
            s3://${{ env.S3_BUCKET }} \
            --delete

      - name: CloudFront Invalidation
        if: github.event_name == 'push'
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ vars.CLOUDFRONT_DISTRIBUTION_ID }} \
            --paths "/*"
```

---

# 22. PR 行为

Pull Request：

```text
React Code
   ↓
npm ci
   ↓
npm test
   ↓
npm build
```

不会：

```text
S3 Upload
CloudFront Invalidation
```

这是正确的。

---

# 23. Main 行为

Merge 到：

```text
main
```

以后：

```text
npm ci
 ↓
npm test
 ↓
npm build
 ↓
S3
 ↓
CloudFront
```

完成。

---

# 24. 前端部署过程

现在：

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
GitHub Actions
   │
   ├── npm ci
   ├── npm test
   └── npm build
          │
          ▼
         S3
          │
          ▼
     CloudFront
          │
          ▼
app.yourdomain.com
```

---

# 25. React Router

如果用户访问：

```text
https://app.yourdomain.com/login
```

CloudFront 必须：

```text
404
 ↓
/index.html
 ↓
React Router
 ↓
LoginPage
```

Day 14 已经配置：

```text
403 → /index.html
404 → /index.html
```

今天一定测试。

---

# 26. 测试三个 URL

首页：

```text
https://app.yourdomain.com/
```

Login：

```text
https://app.yourdomain.com/login
```

AI：

```text
https://app.yourdomain.com/chat
```

全部应该正常。

---

# 27. 测试 API

Browser DevTools：

```text
Network
```

应该看到：

```text
POST
https://api.yourdomain.com/api/auth/login
```

而不是：

```text
http://localhost:8080
```

---

# 28. CORS

现在：

```text
Origin:
https://app.yourdomain.com
```

API：

```text
https://api.yourdomain.com
```

Spring Boot：

```yaml
app:
  cors:
    allowed-origins: https://app.yourdomain.com
```

不要：

```text
*
```

---

# 29. 前端不能保存 Secret

浏览器里面：

```text
VITE_API_BASE_URL
```

可以看到。

这是正常的。

但是：

```text
AWS_ACCESS_KEY
OPENAI_API_KEY
DB_PASSWORD
JWT_SECRET
```

不能出现在：

```text
dist/
```

---

# 30. 检查 dist

执行：

```bash
grep -R \
"OPENAI_API_KEY\|AWS_SECRET\|DB_PASSWORD\|JWT_SECRET" \
dist/
```

应该：

```text
没有结果
```

这是非常值得加入 CI 的安全检查。

---

# 31. 加入 Secret Scan

可以在 Workflow 增加：

```text
Gitleaks
```

扫描：

```text
Git repository
```

防止：

```text
API Key
AWS Key
Password
Token
```

被提交。

---

# 32. React CI/CD + Java CI/CD

现在已经形成：

```text
                    GitHub
                       │
             ┌─────────┴─────────┐
             │                   │
         Backend              Frontend
             │                   │
        Maven Test            npm test
             │                   │
        Docker Build         npm build
             │                   │
             ▼                   ▼
            ECR                  S3
             │                   │
             ▼                   ▼
            ECS              CloudFront
             │                   │
             └─────────┬─────────┘
                       ▼
                     Users
```

---

# 33. Terraform CI/CD 也独立

所以现在实际上有三条 Pipeline：

### Infrastructure

```text
Terraform
 ↓
Plan
 ↓
Apply
```

### Backend

```text
Java
 ↓
Maven
 ↓
Docker
 ↓
ECR
 ↓
ECS
```

### Frontend

```text
React
 ↓
npm
 ↓
S3
 ↓
CloudFront
```

---

# 34. Day 20 最终架构

```text
                           GitHub
                              │
             ┌────────────────┼────────────────┐
             │                │                │
        Terraform          Backend          Frontend
             │                │                │
        fmt/validate        Maven             npm
        Checkov             Test              Test
        Plan                Docker            Build
             │                │                │
             ▼                ▼                ▼
            AWS              ECR              S3
             │                │                │
             │                ▼                ▼
             │               ECS          CloudFront
             │                │                │
             └───────────┬────┴────────────────┘
                         │
                         ▼
                    Production App
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
        app.yourdomain.com    api.yourdomain.com
              │                     │
         CloudFront                ALB
              │                     │
              ▼                     ▼
              S3                Private ECS
                                     │
                           ┌─────────┼─────────┐
                           ▼         ▼         ▼
                          RDS      Bedrock    RAG
```

---

# 35. Day 20 检查清单

```text
[ ] Node.js 22
[ ] npm ci
[ ] npm test
[ ] npm run build
[ ] Production API URL
[ ] S3 private bucket
[ ] CloudFront
[ ] GitHub OIDC
[ ] Frontend IAM Role
[ ] S3 least privilege
[ ] CloudFront invalidation
[ ] PR → test/build
[ ] main → S3
[ ] main → CloudFront
[ ] React Router
[ ] CORS
[ ] dist Secret Scan
[ ] 不在前端保存 API Key
```

---

# Day 1 → Day 20

现在已经形成一套完整的 **AWS Java + AI 企业应用开发平台**：

```text
                         GitHub
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                    │
 Terraform CI/CD       Java CI/CD          React CI/CD
       │                    │                    │
       ▼                    ▼                    ▼
 Terraform              Maven              npm
       │                 Docker              Build
       ▼                    │                    │
      AWS                    ▼                    ▼
       │                   ECR                   S3
       │                    │                    │
       │                    ▼                CloudFront
       │                   ECS                   │
       │                    │                    │
       └──────────────┬─────┴───────────────────┘
                      │
                      ▼
                    ALB
                      │
                Private ECS
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
         RDS        Bedrock      RAG
       pgvector
          │
          ▼
      Secrets Manager
```

**Day 21** 建议开始做一个非常关键的企业能力：**可观测性 Observability**——CloudWatch Logs、Metrics、Alarms、Spring Boot Actuator、ECS CPU/Memory、ALB 5xx/Latency、RDS 指标，以及把 Java 应用的日志和健康状态统一起来。完成后，你的系统就不只是“能部署”，而是开始具备**发现故障、定位故障、报警**的能力。
