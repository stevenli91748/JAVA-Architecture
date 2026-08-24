# Day 18：Terraform + GitHub CI/CD

今天把 Terraform 接入 CI/CD。

目标是以后你修改 AWS 基础设施代码，不再手动：

```text
terraform plan
terraform apply
```

而是：

```text
GitHub
   ↓
Pull Request
   ↓
Terraform fmt
   ↓
Terraform validate
   ↓
Terraform plan
   ↓
Code Review
   ↓
Merge
   ↓
Terraform Apply
   ↓
AWS
```

---

# 1. 今天完成什么

```text
Day 18
│
├── GitHub Actions
├── Terraform CI
├── terraform fmt
├── terraform validate
├── terraform plan
├── AWS OIDC
├── IAM Role
├── Terraform Apply
└── DEV 自动部署
```

今天先实现：

```text
PR → Plan
main → Apply DEV
```

**PROD 今天不要自动 Apply。**

---

# 2. 为什么不用 AWS Access Key

不要在 GitHub Secrets 中长期保存：

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

更推荐：

```text
GitHub
   ↓
OIDC
   ↓
AWS IAM Role
   ↓
Terraform
```

也就是说：

```text
GitHub
不保存 AWS 长期密码
```

---

# 3. 最终 CI/CD 架构

```text
                    GitHub
                       │
              ┌────────┴────────┐
              │                 │
             PR               main
              │                 │
              ▼                 ▼
        Terraform Plan    Terraform Apply
              │                 │
              ▼                 ▼
           Review              AWS
                                │
              ┌─────────────────┼───────────────┐
              ▼                 ▼               ▼
             VPC               ECS             RDS
```

---

# 4. 创建 GitHub Workflow

项目：

```text
.github/
└── workflows/
    └── terraform.yml
```

创建：

```bash
mkdir -p .github/workflows
```

---

# 5. Terraform CI

```yaml id="w6v9m3"
name: Terraform

on:
  pull_request:
    paths:
      - "infrastructure/terraform/**"

  push:
    branches:
      - main
    paths:
      - "infrastructure/terraform/**"

permissions:
  contents: read
  id-token: write

jobs:

  terraform:
    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: infrastructure/terraform/environments/dev

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate

      - name: Terraform Plan
        run: terraform plan
```

---

# 6. 现在测试

```bash
git add .github/workflows/terraform.yml

git commit -m \
"ci: add terraform validation"

git push
```

打开：

```text
GitHub
 → Actions
```

应该看到：

```text
Terraform
 ├── Checkout
 ├── Terraform Init
 ├── Terraform Format
 ├── Terraform Validate
 └── Terraform Plan
```

---

# 7. 这里有一个关键问题

现在 GitHub：

```text
Terraform Plan
       ↓
AWS
```

需要 AWS 权限。

所以必须配置：

```text
GitHub OIDC
       ↓
AWS IAM
```

---

# 8. AWS IAM OIDC Provider

进入：

[AWS IAM Console](https://console.aws.amazon.com/iam/?utm_source=chatgpt.com)

选择：

```text
Identity providers
 → Add provider
```

Provider：

```text id="sqyg8q"
OpenID Connect
```

URL：

```text
https://token.actions.githubusercontent.com
```

Audience：

```text
sts.amazonaws.com
```

创建。

---

# 9. 创建 GitHub Terraform IAM Role

IAM：

```text
Roles
 → Create role
```

Trusted entity：

```text
Web identity
```

Identity provider：

```text
token.actions.githubusercontent.com
```

Audience：

```text
sts.amazonaws.com
```

---

# 10. Trust Policy

企业环境不要：

```text id="3rmivg"
repo:*
```

应该限制到你的 GitHub Repository。

例如：

```json id="q4n7m2"
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated":
          "arn:aws:iam::ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud":
            "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub":
            "repo:YOUR_GITHUB_USER/YOUR_REPO:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

把：

```text id="u8m2x4"
ACCOUNT_ID
YOUR_GITHUB_USER
YOUR_REPO
```

替换成实际值。

---

# 11. 为什么限制 Repository

错误：

```text id="7f2m9x"
任何 GitHub Repository
        ↓
AWS
```

正确：

```text id="n4k8p2"
你的 GitHub Repo
        ↓
OIDC
        ↓
Terraform Role
        ↓
AWS
```

这是供应链安全的重要一环。

---

# 12. Terraform Role 权限

开发环境可以暂时给 Terraform 所需要的权限。

但是不要长期使用：

```text id="5p8x3m"
AdministratorAccess
```

更好的做法：

```text id="q7m4n2"
Terraform DEV Role
```

只允许管理：

```text
VPC
ECS
ECR
ALB
RDS
S3
CloudFront
Route53
IAM
Secrets Manager
```

而且最好进一步限制：

```text
Environment = dev
```

---

# 13. GitHub Workflow 添加 AWS Credentials

修改：

```yaml id="g3m8q1"
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::ACCOUNT_ID:role/github-terraform-dev
    aws-region: us-west-2
```

然后：

```yaml id="x7p4m9"
- name: Terraform Plan
  run: terraform plan
```

Terraform 就可以通过：

```text id="m2c8v5"
GitHub OIDC
 ↓
IAM Role
 ↓
STS Temporary Credentials
 ↓
AWS
```

获得临时权限。

---

# 14. 这里不要保存 AWS Secret

GitHub：

```text id="f6n3k8"
AWS_ACCESS_KEY_ID
❌

AWS_SECRET_ACCESS_KEY
❌
```

只需要：

```text id="j8m4p2"
OIDC
```

AWS 给 GitHub：

```text id="v7q3x9"
Temporary Credentials
```

---

# 15. PR 工作流

现在开发：

```bash
git checkout -b feature/vpc-update
```

修改：

```text
modules/network/
```

然后：

```bash
git add .
git commit -m "infra: update network"
git push
```

创建：

```text
Pull Request
```

GitHub 自动：

```text
terraform fmt
        ↓
terraform validate
        ↓
terraform plan
```

---

# 16. 最重要的一步：把 Plan 显示到 PR

可以让 Workflow 输出：

```text
Terraform Plan

Plan:
3 to add
1 to change
0 to destroy
```

尤其要注意：

```text
0 to destroy
```

如果看到：

```text
Plan:
0 to add
2 to change
5 to destroy
```

**不要 Merge。**

---

# 17. 为什么 Terraform Destroy 特别危险

例如你只是修改：

```text
RDS
```

Terraform 却显示：

```text
destroy aws_db_instance
```

这可能意味着：

```text
State
≠
Terraform Code
```

或者：

```text
Resource configuration changed
```

生产环境必须人工确认。

---

# 18. DEV 自动 Apply

现在：

```text
PR
 ↓
Plan
 ↓
Review
 ↓
Merge main
 ↓
Apply DEV
```

修改 Workflow：

```yaml id="5g8x3m"
- name: Terraform Apply
  if: github.ref == 'refs/heads/main'
  run: terraform apply -auto-approve
```

完整逻辑：

```text
Pull Request
    ↓
Plan only

main
    ↓
Plan
    ↓
Apply
```

---

# 19. 不建议 PROD 自动 Apply

生产：

```text
GitHub
 ↓
Terraform Plan
 ↓
Human Approval
 ↓
Terraform Apply
```

而不是：

```text
GitHub
 ↓
git push
 ↓
PROD
```

---

# 20. GitHub Environment

创建：

```text
GitHub
 → Settings
 → Environments
```

建立：

```text id="t8m3q6"
dev
test
prod
```

PROD：

```text
Required reviewers
```

设置至少一个审批人。

---

# 21. PROD 工作流

以后：

```text
main
 ↓
Terraform Plan
 ↓
PROD Environment
 ↓
Approval
 ↓
Terraform Apply
```

这就是：

```text
Manual Approval Gate
```

---

# 22. DEV / TEST / PROD AWS Role

建议：

```text id="k5x8m2"
github-terraform-dev
github-terraform-test
github-terraform-prod
```

不要：

```text id="z7q3n9"
github-terraform-all
```

一个 Role 管所有环境。

---

# 23. 为什么要分 Role

假设：

```text
DEV Role
```

被泄露。

理想情况下：

```text
DEV
✓

TEST
✗

PROD
✗
```

这样 Blast Radius 最小。

---

# 24. Terraform State 也分开

今天 Day 16：

```text id="w4m8p2"
S3

java-ai-platform/dev/terraform.tfstate
```

现在：

```text id="q8x3n6"
S3
│
├── dev/
│   └── terraform.tfstate
│
├── test/
│   └── terraform.tfstate
│
└── prod/
    └── terraform.tfstate
```

更重要的是：

```text
DEV Role
   ↓
只能访问 DEV State
```

---

# 25. Terraform Plan 输出

以后 PR：

```text id="p5n8x2"
Terraform Plan

+ aws_subnet.private_ecs
+ aws_security_group.ecs

~ aws_ecs_service.backend

- 0 resources
```

Code Review 就可以直接看：

```text
增加什么
修改什么
删除什么
```

---

# 26. 今天顺便加入 Checkov

Terraform 做安全扫描。

GitHub Workflow：

```yaml id="c6m2x9"
- name: Run Checkov
  uses: bridgecrewio/checkov-action@v12
  with:
    directory: infrastructure/terraform
```

它可以检查：

```text
S3 Public
Security Group 0.0.0.0/0
RDS Public
Encryption
IAM
```

---

# 27. Terraform + Security

于是：

```text
Developer
   ↓
Terraform Code
   ↓
Checkov
   ↓
Terraform Validate
   ↓
Terraform Plan
```

这比单纯：

```text
terraform plan
```

安全很多。

---

# 28. 后面加入 TFLint

还可以：

```text id="r4m7x2"
TFLint
```

检查 Terraform：

```text
错误配置
AWS Resource 配置问题
命名问题
```

未来 CI：

```text
terraform fmt
terraform validate
tflint
checkov
terraform plan
```

---

# 29. Java CI/CD 和 Terraform CI/CD 分开

非常重要。

现在：

```text id="a7m3x9"
Application CI/CD
│
├── Java
├── React
├── Docker
├── ECR
└── ECS
```

另外：

```text id="x8q4p2"
Infrastructure CI/CD
│
├── Terraform
├── VPC
├── RDS
├── ECS
├── ALB
├── S3
└── CloudFront
```

不要全部塞进一个 Workflow。

---

# 30. 最终 CI/CD

```text
                         GitHub
                            │
              ┌─────────────┴─────────────┐
              │                           │
       Application Code             Terraform Code
              │                           │
              ▼                           ▼
        Java / React                  fmt
              │                      validate
              ▼                      Checkov
          Docker Build                  │
              │                         ▼
              ▼                       Plan
             ECR                         │
              │                         ▼
              ▼                      Review
             ECS                         │
                                        ▼
                                     Apply
                                        │
                                        ▼
                                       AWS
```

---

# 31. Day 18 完整 Workflow

建议最终 `.github/workflows/terraform.yml`：

```yaml id="f8n3m7"
name: Terraform

on:
  pull_request:
    paths:
      - "infrastructure/terraform/**"

  push:
    branches:
      - main
    paths:
      - "infrastructure/terraform/**"

permissions:
  contents: read
  id-token: write

jobs:

  terraform-plan:

    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory:
          infrastructure/terraform/environments/dev

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v3

      - name: Configure AWS
        uses:
          aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume:
            arn:aws:iam::ACCOUNT_ID:role/github-terraform-dev
          aws-region: us-west-2

      - name: Terraform Init
        run: terraform init

      - name: Terraform Format
        run: terraform fmt -check -recursive

      - name: Terraform Validate
        run: terraform validate

      - name: Checkov
        uses:
          bridgecrewio/checkov-action@v12
        with:
          directory:
            infrastructure/terraform

      - name: Terraform Plan
        run: terraform plan
```

---

# 32. Apply Job

然后增加：

```yaml id="v2m8q4"
  terraform-apply:

    if:
      github.ref == 'refs/heads/main'

    needs:
      - terraform-plan

    runs-on: ubuntu-latest

    environment:
      name: dev

    defaults:
      run:
        working-directory:
          infrastructure/terraform/environments/dev

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Terraform
        uses:
          hashicorp/setup-terraform@v3

      - name: Configure AWS
        uses:
          aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume:
            arn:aws:iam::ACCOUNT_ID:role/github-terraform-dev
          aws-region: us-west-2

      - name: Terraform Init
        run: terraform init

      - name: Terraform Apply
        run: terraform apply -auto-approve
```

---

# 33. 现在的完整流程

你修改：

```text id="f3q8m2"
vpc.tf
```

然后：

```bash id="k7x3p9"
git checkout -b feature/network-update
```

修改完成：

```bash id="w5m2q8"
git add .
git commit -m "infra: update vpc"
git push
```

---

# 34. GitHub PR

自动：

```text id="h9x4m3"
✓ Terraform Format
✓ Terraform Validate
✓ Checkov
✓ Terraform Plan
```

如果：

```text id="s7p2n8"
Plan:
2 add
1 change
0 destroy
```

Review。

---

# 35. Merge

Merge：

```text id="x3m8q5"
feature/network-update
        ↓
main
```

触发：

```text id="n6p4k2"
Terraform Apply
```

AWS：

```text id="c9x3m7"
VPC
 ↓
AWS
```

完成。

---

# 36. Day 18 最重要的安全原则

```text id="w2k7p4"
GitHub
   │
   │ OIDC
   ▼
AWS STS
   │
   │ Temporary Credentials
   ▼
IAM Role
   │
   ▼
Terraform
   │
   ▼
AWS
```

**不要再使用长期 AWS Access Key 放在 GitHub。**

---

# 37. Day 18 检查清单

```text id="m7x3q9"
[ ] GitHub Actions
[ ] Terraform Init
[ ] Terraform Format
[ ] Terraform Validate
[ ] Checkov
[ ] Terraform Plan
[ ] GitHub OIDC
[ ] IAM OIDC Provider
[ ] Terraform DEV Role
[ ] Temporary Credentials
[ ] PR → Plan
[ ] main → Apply DEV
[ ] GitHub Environment
[ ] PROD Manual Approval
[ ] DEV/TEST/PROD Role 分离
[ ] Terraform State 分离
```

---

# Day 18 完成后的企业架构

```text id="s8m3q7"
                         GitHub
                            │
              ┌─────────────┴─────────────┐
              │                           │
        Application                    Terraform
              │                           │
        Java + React               fmt / validate
              │                       Checkov
          Docker Build                  │
              │                         ▼
             ECR                      Plan
              │                         │
             ECS                   Code Review
              │                         │
              │                     OIDC / STS
              │                         │
              └──────────────┬──────────┘
                             ▼
                            AWS
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
         ALB                VPC                RDS
          │                  │
          ▼                  ▼
       Private ECS       Private Network
          │
     ┌────┼───────────┐
     ▼    ▼           ▼
   RAG  Bedrock   Secrets Manager
```

**Day 19** 建议进入应用 CI/CD：把 **Java Spring Boot → Docker → ECR → ECS** 全自动化，并加入单元测试、Maven、Docker Build、ECR Push、ECS Rolling Deployment。这样 Day 18 的 Terraform CI/CD 和应用 CI/CD 就真正形成完整的企业 DevOps Pipeline。
