# Day 16：Terraform Remote State + Modules + DEV/TEST/PROD

今天开始把 Terraform 从“能创建 AWS 资源”升级成**企业级 Infrastructure as Code**。

昨天：

```text
Terraform
   ↓
VPC
```

今天：

```text
Terraform
   ↓
Remote State
   ↓
Modules
   ↓
DEV / TEST / PROD
```

最终目标：

```text
                    GitHub
                       │
                       ▼
                  Terraform
                       │
              ┌────────┼────────┐
              ▼        ▼        ▼
             DEV      TEST     PROD
              │        │        │
              ▼        ▼        ▼
             AWS      AWS      AWS
```

---

# 1. 今天完成什么

```text
Day 16
│
├── S3 Terraform Backend
├── State Locking
├── Remote State
├── Terraform Modules
├── DEV Environment
├── TEST Environment
├── PROD Environment
└── Terraform Best Practices
```

今天**不建议直接改你现有生产环境**。

我们先建立新的 Terraform 管理方式，然后逐步迁移。

---

# 2. 为什么需要 Remote State

昨天：

```text
terraform.tfstate
```

在 Codespaces 本地。

问题：

```text
Developer A
   ↓
local state

Developer B
   ↓
另外一个 state
```

两个人可能得到不同结果。

企业环境应该：

```text
Developer
   ↓
Terraform
   ↓
S3 Remote State
```

大家共享同一个 State。

---

# 3. Remote State 架构

```text
Developer A ─┐
             │
Developer B ─┼──► Terraform
             │        │
CI/CD ───────┘        ▼
                  S3 Backend
                       │
                       ▼
                terraform.tfstate
```

---

# 4. 创建 Terraform State Bucket

先创建一个专门保存 Terraform State 的 S3 Bucket。

名称：

```text
terraform-state-YOUR_ACCOUNT_ID
```

例如：

```text
terraform-state-123456789012
```

建议：

```text
Region:
us-west-2
```

---

# 5. State Bucket 安全配置

必须：

```text
Block Public Access
    ✓

Bucket Versioning
    ✓

Encryption
    ✓
```

为什么必须开启 Versioning？

如果：

```text
terraform.tfstate
```

被错误修改，可以恢复之前版本。

---

# 6. 开启 S3 Versioning

AWS CLI：

```bash
aws s3api put-bucket-versioning \
  --bucket terraform-state-YOUR_ACCOUNT_ID \
  --versioning-configuration Status=Enabled
```

检查：

```bash
aws s3api get-bucket-versioning \
  --bucket terraform-state-YOUR_ACCOUNT_ID
```

应该：

```json
{
  "Status": "Enabled"
}
```

---

# 7. 开启默认加密

```bash
aws s3api put-bucket-encryption \
  --bucket terraform-state-YOUR_ACCOUNT_ID \
  --server-side-encryption-configuration \
'{
  "Rules": [
    {
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "AES256"
      }
    }
  ]
}'
```

---

# 8. Terraform Backend

创建：

```text
backend.tf
```

```hcl
terraform {
  backend "s3" {
    bucket       = "terraform-state-YOUR_ACCOUNT_ID"
    key          = "java-ai-platform/dev/terraform.tfstate"
    region       = "us-west-2"
    encrypt      = true
    use_lockfile = true
  }
}
```

这里：

```text
key =
java-ai-platform/dev/terraform.tfstate
```

非常重要。

以后：

```text
java-ai-platform/
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

---

# 9. State Locking

Terraform 操作过程中：

```text
Developer A
    ↓
terraform apply
```

如果 Developer B 同时：

```text
terraform apply
```

可能发生冲突。

所以我们需要：

```text
State Lock
```

现在 S3 backend 可以使用：

```hcl
use_lockfile = true
```

来启用 S3 原生锁文件机制。

这也是我们今天不再强制创建 DynamoDB Lock Table 的原因。

---

# 10. 初始化 Remote Backend

先备份：

```bash
cp terraform.tfstate terraform.tfstate.backup
```

然后：

```bash
terraform init
```

如果已经有本地 State，Terraform 会询问：

```text
Do you want to migrate existing state?
```

**今天的新 DEV 环境建议选择迁移；如果 State 里只是 Day 15 的测试资源，则先确认资源是否需要保留。**

---

# 11. 检查 State

```bash
terraform state list
```

应该看到：

```text
aws_vpc.main
aws_subnet.public_a
aws_subnet.public_b
...
```

如果没有：

```text
terraform state list
```

说明当前 State 迁移没有完成。

不要继续 `apply`。

---

# 12. 为什么 State 是敏感文件

State 可能包含：

```text
Resource IDs
ARN
Infrastructure metadata
Sensitive attributes
```

所以：

```text
GitHub
❌ terraform.tfstate

S3
✅ encrypted
✅ versioning
✅ private
```

---

# 13. 现在开始 Terraform Modules

昨天：

```text
vpc.tf
rds.tf
ecs.tf
```

全部放在一起。

项目大了以后会变得非常难维护。

改成：

```text
terraform/
│
├── modules/
│   ├── network/
│   ├── security/
│   ├── database/
│   ├── ecr/
│   ├── ecs/
│   ├── alb/
│   └── frontend/
│
└── environments/
    ├── dev/
    ├── test/
    └── prod/
```

---

# 14. Module 是什么

例如：

```text
modules/network
```

负责：

```text
VPC
Subnets
Route Tables
IGW
NAT
```

然后：

```text
dev
```

调用：

```hcl
module "network" {
  source = "../../modules/network"
}
```

---

# 15. Network Module

创建：

```text
modules/network/
├── main.tf
├── variables.tf
└── outputs.tf
```

---

# 16. Module Variables

`modules/network/variables.tf`

```hcl
variable "project_name" {
  type = string
}

variable "environment" {
  type = string
}

variable "vpc_cidr" {
  type = string
}
```

---

# 17. Network Main

```hcl
resource "aws_vpc" "this" {

  cidr_block = var.vpc_cidr

  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name =
      "${var.project_name}-${var.environment}-vpc"
  }
}
```

---

# 18. Module Output

`outputs.tf`：

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}
```

以后 ECS 可以：

```text
ECS Module
    ↓
module.network.vpc_id
```

---

# 19. DEV Environment

创建：

```text
environments/
└── dev/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    └── providers.tf
```

---

# 20. DEV Provider

```hcl
provider "aws" {
  region = "us-west-2"

  default_tags {
    tags = {
      Project     = "java-ai-platform"
      Environment = "dev"
      ManagedBy   = "terraform"
    }
  }
}
```

这样所有资源自动带：

```text
Project
Environment
ManagedBy
```

非常推荐。

---

# 21. DEV Module

`main.tf`：

```hcl
module "network" {

  source = "../../modules/network"

  project_name = "java-ai-platform"

  environment = "dev"

  vpc_cidr = "10.10.0.0/16"
}
```

---

# 22. TEST

```text
test
```

使用：

```text
10.20.0.0/16
```

例如：

```hcl
module "network" {

  source = "../../modules/network"

  project_name = "java-ai-platform"

  environment = "test"

  vpc_cidr = "10.20.0.0/16"
}
```

---

# 23. PROD

使用：

```text
10.30.0.0/16
```

```hcl
module "network" {

  source = "../../modules/network"

  project_name = "java-ai-platform"

  environment = "prod"

  vpc_cidr = "10.30.0.0/16"
}
```

---

# 24. 三个环境

最终：

```text
DEV
10.10.0.0/16

TEST
10.20.0.0/16

PROD
10.30.0.0/16
```

这样不会发生：

```text
DEV ECS
    ↓
连接 PROD RDS
```

这种危险情况。

---

# 25. Terraform Workspace 还是目录？

你可能会看到：

```bash
terraform workspace new dev
```

这种方式。

对于你的企业 Java + AI 项目，我更推荐：

```text
environments/
├── dev/
├── test/
└── prod/
```

原因：

```text
环境配置明确
权限容易控制
State 清晰
CI/CD 容易管理
生产风险更低
```

不要一开始过度依赖 Workspace。

---

# 26. DEV / TEST / PROD 应该不同

不要只是：

```text
environment = "dev"
```

真正生产应该不同。

例如：

| 配置                  | DEV | TEST |   PROD |
| ------------------- | --: | ---: | -----: |
| ECS Tasks           |   1 |    1 |     2+ |
| RDS                 |   小 |    中 |   多 AZ |
| NAT                 |   1 |    1 |      2 |
| Logs                |  7天 |  14天 | 30–90天 |
| Backup              |  基础 |   基础 |     严格 |
| Deletion Protection | OFF |  OFF |     ON |

---

# 27. ECS Module

以后：

```text
modules/ecs/
├── main.tf
├── variables.tf
└── outputs.tf
```

输入：

```hcl
variable "cluster_name" {
  type = string
}

variable "service_name" {
  type = string
}

variable "container_image" {
  type = string
}
```

---

# 28. ECR Module

```text
modules/ecr/
```

核心：

```hcl
resource "aws_ecr_repository" "backend" {

  name = "${var.project_name}-backend"

  image_scanning_configuration {
    scan_on_push = true
  }
}
```

以后：

```text
GitHub
 ↓
CodeBuild
 ↓
ECR
```

ECR Repository 本身也由 Terraform 管理。

---

# 29. RDS Module

```text
modules/database/
```

最终负责：

```text
RDS
Subnet Group
Security Group
Parameter Group
Backup
Encryption
```

生产：

```hcl
deletion_protection = true
```

开发：

```hcl
deletion_protection = false
```

---

# 30. ALB Module

```text
modules/alb/
```

负责：

```text
ALB
Target Group
HTTP Listener
HTTPS Listener
Security Group
```

最终：

```text
Route 53
 ↓
ALB
 ↓
ECS
```

---

# 31. Secrets 不要让 Terraform 保存密码

这是今天一个非常重要的企业实践。

不要：

```hcl
resource "aws_secretsmanager_secret_version" "db" {
  secret_string = "myPassword123"
}
```

因为 Terraform State 可能保存 Secret value。

正确：

```text
RDS
 ↓
Secrets Manager
```

Secret Value 由：

```text
AWS Console
AWS CLI
CI/CD Secret system
```

管理。

Terraform 只管理：

```text
Secret metadata
IAM permission
Secret ARN
```

而不是把密码写入 `.tf`。

---

# 32. Terraform IAM

最终：

```text
Terraform
 ↓
AWS
```

不要使用：

```text
root
```

开发环境可以使用你的 AWS 身份认证。

CI/CD 应使用：

```text
GitHub Actions / AWS OIDC
```

或者 AWS 内部：

```text
CodePipeline
 ↓
IAM Role
```

Day 18 我们会专门做 Terraform CI/CD 权限。

---

# 33. 今天的 Git Commit

完成后：

```bash
git add infrastructure/terraform
```

然后：

```bash
git commit -m \
"infra: add terraform remote state and modules"
```

推送：

```bash
git push
```

---

# 34. Day 16 测试

进入：

```bash
cd infrastructure/terraform/environments/dev
```

执行：

```bash
terraform init
```

然后：

```bash
terraform fmt -recursive
```

再：

```bash
terraform validate
```

最后：

```bash
terraform plan
```

你应该看到：

```text
Plan: X to add
```

---

# 35. 不要马上 Apply PROD

生产环境：

```text
terraform plan
```

以后应该经过：

```text
Developer
   ↓
Pull Request
   ↓
Terraform Plan
   ↓
Code Review
   ↓
Approval
   ↓
Terraform Apply
```

而不是：

```text
Developer
   ↓
terraform apply prod
```

---

# 36. Day 16 最终架构

```text
                         GitHub
                           │
                           ▼
                      Terraform
                           │
                    Remote State
                           │
                           ▼
                     S3 Backend
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
         DEV              TEST             PROD
          │                │                │
          ▼                ▼                ▼
        VPC              VPC              VPC
          │                │                │
     ┌────┴────┐      ┌────┴────┐      ┌────┴────┐
     ▼         ▼      ▼         ▼      ▼         ▼
    ECS       RDS    ECS       RDS    ECS       RDS
     │         │      │         │      │         │
     └────┬────┘      └────┬────┘      └────┬────┘
          │                │                │
        Bedrock          Bedrock          Bedrock
```

---

# 37. Day 16 检查清单

```text
[ ] S3 Terraform State
[ ] Versioning
[ ] Encryption
[ ] Remote Backend
[ ] S3 State Locking
[ ] terraform init
[ ] terraform state list
[ ] Network Module
[ ] ECR Module
[ ] ECS Module
[ ] RDS Module
[ ] ALB Module
[ ] DEV
[ ] TEST
[ ] PROD
[ ] Environment-specific CIDR
[ ] Terraform tags
[ ] 不把 Secret 写进 Terraform
[ ] 不把 tfstate 放 GitHub
```

---

# Day 1 → Day 16

现在已经从单纯的 AWS Java 开发环境升级成：

```text
                Enterprise Java + AI Platform

                         GitHub
                            │
             ┌──────────────┴──────────────┐
             │                             │
          Application                   Terraform
             │                             │
       ┌─────┴─────┐                 Remote State
       │           │                      │
    Backend     Frontend                 S3
       │           │                      │
      ECS      CloudFront            Modules
       │           │                      │
       ▼           ▼              ┌───────┼───────┐
    Spring       React           DEV     TEST    PROD
       │
 ┌─────┼──────────┐
 │     │          │
RDS  Bedrock     RAG
 │
pgvector
```

**Day 17** 下一步建议做 **Terraform VPC 企业网络完整实现**：Public/Private Subnet、NAT Gateway、VPC Endpoint、ECS Private Subnet、RDS Private Subnet，以及安全组之间的最小权限通信。完成后就可以把目前 Day 7 的“Public ECS”真正升级成生产级的 **ALB → Private ECS → Private RDS**。
