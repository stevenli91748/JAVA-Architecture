# Day 15：Terraform — 把整个 AWS 环境代码化

前 14 天我们已经手工搭建了：

```text
React
Spring Boot
RDS
VPC
ALB
ECS
ECR
Bedrock
RAG
Secrets Manager
Route 53
ACM
CloudFront
S3
CI/CD
```

今天开始进入真正的 **Infrastructure as Code（IaC）**。

目标：

```text
Terraform
    ↓
AWS Infrastructure
```

以后不用一个个点 AWS Console。

---

# 1. Day 15 最终目标

最终项目：

```text
aws-java-ai-platform/
│
├── backend/
├── frontend/
├── infrastructure/
│   └── terraform/
│       ├── providers.tf
│       ├── variables.tf
│       ├── outputs.tf
│       ├── vpc.tf
│       ├── security.tf
│       ├── rds.tf
│       ├── ecr.tf
│       ├── ecs.tf
│       ├── alb.tf
│       ├── iam.tf
│       ├── s3.tf
│       ├── cloudfront.tf
│       └── route53.tf
│
└── buildspec.yml
```

---

# 2. Terraform 是什么

以前：

```text
AWS Console
 ↓
Create VPC
 ↓
Create RDS
 ↓
Create ECS
 ↓
Create ALB
```

现在：

```text
Terraform Code
      ↓
terraform plan
      ↓
terraform apply
      ↓
AWS
```

Terraform 会记录：

```text
当前 AWS 状态
        ↕
Terraform State
        ↕
Terraform Code
```

官方文档：

[Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs?utm_source=chatgpt.com)

---

# 3. 为什么企业一定要学 Terraform

因为以后你会有：

```text
DEV
TEST
STAGING
PROD
```

如果全部手工创建：

```text
DEV → 2天
TEST → 2天
PROD → 2天
```

而 Terraform：

```text
DEV
 ↓
terraform apply

TEST
 ↓
terraform apply

PROD
 ↓
terraform apply
```

基础设施可以重复。

---

# 4. 今天不要一口气重建生产环境

非常重要。

你现在已经有一个可以运行的 AWS 环境。

**Day 15 不要直接执行：**

```bash
terraform apply
```

去覆盖现有生产资源。

今天先：

```text
学习 Terraform
 ↓
建立代码
 ↓
terraform init
 ↓
terraform plan
 ↓
创建一个测试资源
```

然后逐步把现有架构迁移到 Terraform。

---

# 5. 创建 Terraform Directory

Codespaces：

```bash
cd aws-java-ai-platform

mkdir -p infrastructure/terraform

cd infrastructure/terraform
```

创建：

```text
providers.tf
variables.tf
outputs.tf
```

---

# 6. providers.tf

```hcl
terraform {
  required_version = ">= 1.6.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

这里：

```text
id="j7m3x8"
aws
```

就是 AWS Terraform Provider。

---

# 7. variables.tf

```hcl
variable "aws_region" {
  type    = string
  default = "us-west-2"
}

variable "project_name" {
  type    = string
  default = "java-ai-platform"
}

variable "environment" {
  type    = string
  default = "dev"
}
```

---

# 8. outputs.tf

```hcl
output "aws_region" {
  value = var.aws_region
}

output "project_name" {
  value = var.project_name
}
```

---

# 9. Terraform 初始化

运行：

```bash
terraform init
```

应该看到：

```text
Initializing the backend...

Initializing provider plugins...

Terraform has been successfully initialized!
```

---

# 10. 第一个 Terraform Resource

今天先创建一个：

```text
AWS S3 Bucket
```

创建：

```text
test.tf
```

```hcl
resource "aws_s3_bucket" "terraform_test" {

  bucket = "java-ai-platform-terraform-test-123456"
}
```

注意：

S3 Bucket 名称必须全球唯一。

---

# 11. Terraform Format

```bash
terraform fmt
```

然后：

```bash
terraform validate
```

应该：

```text
Success! The configuration is valid.
```

---

# 12. Terraform Plan

```bash
terraform plan
```

你应该看到：

```text
Plan: 1 to add, 0 to change, 0 to destroy.
```

这一步非常重要。

Terraform 的核心习惯：

```text
terraform plan
        ↓
确认变化
        ↓
terraform apply
```

不要直接盲目 `apply`。

---

# 13. Apply

确认没问题以后：

```bash
terraform apply
```

Terraform 会要求：

```text
Do you want to perform these actions?
```

输入：

```text
yes
```

然后：

```text
Apply complete!
```

---

# 14. AWS Console 检查

进入：

[Amazon S3 Console](https://console.aws.amazon.com/s3/?utm_source=chatgpt.com)

应该看到：

```text
java-ai-platform-terraform-test-123456
```

这证明：

```text
Codespaces
 ↓
Terraform
 ↓
AWS
```

已经成功。

---

# 15. Terraform State

现在目录应该出现：

```text
terraform.tfstate
```

它非常重要。

Terraform 用它记录：

```text
Terraform
    │
    ▼
terraform.tfstate
    │
    ▼
AWS Resources
```

---

# 16. 不要把 State 上传 GitHub

`.gitignore`：

```gitignore
*.tfstate
*.tfstate.*
.terraform/
.terraform.lock.hcl
```

这里建议保留：

```text
.terraform.lock.hcl
```

实际上更好的做法是**提交 lock file**，因为它锁定 Provider 版本。

所以：

```gitignore
.terraform/
*.tfstate
*.tfstate.*
```

不要忽略：

```text
.terraform.lock.hcl
```

---

# 17. 为什么不能把 tfstate 放 GitHub

State 可能包含：

```text
Resource IDs
ARNs
Configuration
Sensitive values
```

所以：

```text
GitHub
    ❌ terraform.tfstate
```

---

# 18. 企业正确方式：Remote State

最终：

```text
Terraform
    ↓
S3
    ↓
Terraform State
```

再使用：

```text
DynamoDB
```

或者 AWS 当前推荐的 Terraform state locking 方案。

现代 Terraform + S3 backend 可以使用 S3 的原生锁机制配置 `use_lockfile`，不必再为简单 state locking 强制创建 DynamoDB 表。

最终：

```text
S3
 └── terraform.tfstate
```

---

# 19. 今天先不要改 Remote Backend

原因很简单：

我们现在是在学习 Terraform。

先理解：

```text
local state
```

然后 Day 16：

```text
remote state
```

这样比较容易理解。

---

# 20. 删除测试资源

测试完成后：

```bash
terraform destroy
```

Plan：

```text
Plan: 0 to add,
      0 to change,
      1 to destroy.
```

确认：

```text
yes
```

测试 Bucket 删除。

---

# 21. 接下来开始 VPC Terraform

删除：

```text
test.tf
```

创建：

```text
vpc.tf
```

我们暂时先定义 VPC。

```hcl
resource "aws_vpc" "main" {

  cidr_block = "10.0.0.0/16"

  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "${var.project_name}-vpc"
  }
}
```

---

# 22. Terraform VPC 架构

我们最终希望：

```text
VPC
10.0.0.0/16

├── Public Subnet
│   ├── ALB
│   └── NAT Gateway
│
└── Private Subnet
    ├── ECS
    └── RDS
```

但今天先理解：

```text
VPC
 ↓
Subnets
 ↓
Route Tables
 ↓
Internet Gateway
 ↓
NAT Gateway
```

---

# 23. Public Subnet

```hcl
resource "aws_subnet" "public_a" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.1.0/24"

  availability_zone =
    "${var.aws_region}a"

  map_public_ip_on_launch = true

  tags = {
    Name =
      "${var.project_name}-public-a"
  }
}
```

再创建：

```hcl
resource "aws_subnet" "public_b" {

  vpc_id = aws_vpc.main.id

  cidr_block = "10.0.2.0/24"

  availability_zone =
    "${var.aws_region}b"

  map_public_ip_on_launch = true

  tags = {
    Name =
      "${var.project_name}-public-b"
  }
}
```

---

# 24. Private Subnet

ECS：

```text
10.0.11.0/24
10.0.12.0/24
```

RDS：

```text
10.0.21.0/24
10.0.22.0/24
```

最终：

```text
VPC 10.0.0.0/16

Public
├── 10.0.1.0/24
└── 10.0.2.0/24

Private ECS
├── 10.0.11.0/24
└── 10.0.12.0/24

Private RDS
├── 10.0.21.0/24
└── 10.0.22.0/24
```

---

# 25. 为什么至少两个 AZ

生产环境不要：

```text
ECS
 ↓
AZ-a
```

应该：

```text
              ALB
             /   \
          AZ-a   AZ-b
            │      │
           ECS    ECS
```

RDS：

```text
AZ-a
 +
AZ-b
```

这样一个 Availability Zone 出问题，服务仍有机会继续运行。

---

# 26. Internet Gateway

```hcl
resource "aws_internet_gateway" "main" {

  vpc_id = aws_vpc.main.id

  tags = {
    Name =
      "${var.project_name}-igw"
  }
}
```

架构：

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
VPC
```

---

# 27. Public Route Table

```hcl
resource "aws_route_table" "public" {

  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"

    gateway_id =
      aws_internet_gateway.main.id
  }

  tags = {
    Name =
      "${var.project_name}-public-rt"
  }
}
```

关联：

```hcl
resource "aws_route_table_association" "public_a" {

  subnet_id =
    aws_subnet.public_a.id

  route_table_id =
    aws_route_table.public.id
}
```

---

# 28. Day 15 暂时不要创建 NAT Gateway

NAT Gateway 很重要，但它会产生持续费用。

最终企业架构：

```text
Private ECS
    │
    ▼
NAT Gateway
    │
    ▼
Internet
```

但是 Day 15 学习阶段：

**先不要为了测试创建多个 NAT Gateway。**

Day 16 我们专门讲：

```text
NAT Gateway
VPC Endpoint
成本
Private ECS
```

---

# 29. Terraform Resource Dependency

这是 Terraform 非常重要的概念。

例如：

```hcl
resource "aws_subnet" "public_a" {

  vpc_id =
    aws_vpc.main.id
}
```

Terraform 自动知道：

```text
VPC
 ↓
Subnet
```

所以：

```bash
terraform apply
```

它会自动按依赖关系创建。

---

# 30. Terraform Plan

现在：

```bash
terraform fmt
terraform validate
terraform plan
```

你应该看到：

```text
VPC
Internet Gateway
Subnets
Route Table
Associations
```

---

# 31. 今天不要直接迁移旧 VPC

你的 AWS 现在已经有：

```text
旧 VPC
RDS
ECS
ALB
```

Terraform 新建：

```text
新 VPC
```

两者会同时存在。

**这是故意的。**

我们先让 Terraform 创建一个新的 DEV 环境。

---

# 32. 企业环境建议

最终目录：

```text
terraform/
│
├── modules/
│   ├── network/
│   ├── ecs/
│   ├── rds/
│   ├── alb/
│   └── frontend/
│
└── environments/
    ├── dev/
    ├── test/
    └── prod/
```

例如：

```text
dev
 ↓
terraform apply

test
 ↓
terraform apply

prod
 ↓
terraform apply
```

---

# 33. Day 15 项目结构升级

```text
aws-java-ai-platform/
│
├── backend/
│
├── frontend/
│
├── infrastructure/
│   │
│   └── terraform/
│       │
│       ├── providers.tf
│       ├── variables.tf
│       ├── outputs.tf
│       ├── vpc.tf
│       ├── security.tf
│       ├── rds.tf
│       ├── ecs.tf
│       ├── alb.tf
│       ├── ecr.tf
│       ├── s3.tf
│       ├── cloudfront.tf
│       └── route53.tf
│
└── buildspec.yml
```

---

# 34. 今天真正掌握的 8 个 Terraform 命令

```bash
terraform init
```

初始化。

```bash
terraform fmt
```

格式化。

```bash
terraform validate
```

检查语法。

```bash
terraform plan
```

查看变化。

```bash
terraform apply
```

创建/修改。

```bash
terraform destroy
```

删除。

```bash
terraform output
```

查看输出。

```bash
terraform state list
```

查看 Terraform 管理的资源。

---

# 35. Day 15 最重要的工作习惯

以后任何 Terraform 变更：

```text
修改 .tf
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
人工检查
    ↓
terraform apply
```

不要：

```text
修改
 ↓
terraform apply
```

---

# 36. Day 15 检查清单

```text
[ ] Terraform 安装
[ ] AWS Provider
[ ] terraform init
[ ] terraform fmt
[ ] terraform validate
[ ] terraform plan
[ ] terraform apply
[ ] Terraform State
[ ] .gitignore
[ ] VPC
[ ] Public Subnets
[ ] Internet Gateway
[ ] Route Table
[ ] GitHub 不保存 tfstate
[ ] 理解 Terraform dependency
```

---

# 37. Day 15 完成后的架构

你现在有两套东西：

```text
              Existing AWS
                   │
            手工创建环境
                   │
                   ▼
            Java + AI App


              Terraform
                   │
                   ▼
             New DEV VPC
                   │
        ┌──────────┴──────────┐
        │                     │
     Public                 Private
        │                     │
       ALB                  ECS/RDS
```

接下来逐步把后面的资源全部 Terraform 化。

---

## Day 16：Terraform 企业化

下一天重点不是继续堆 `.tf` 文件，而是解决真正的企业问题：

```text
Terraform Remote State
        ↓
S3 Backend
        ↓
State Locking
        ↓
DEV / TEST / PROD
        ↓
Terraform Modules
        ↓
VPC
 ├── Public Subnet
 ├── Private ECS
 ├── Private RDS
 └── NAT / VPC Endpoint
```

并开始把 **ECS + ALB + RDS + ECR + IAM** 从手工创建迁移成 Terraform 管理。
