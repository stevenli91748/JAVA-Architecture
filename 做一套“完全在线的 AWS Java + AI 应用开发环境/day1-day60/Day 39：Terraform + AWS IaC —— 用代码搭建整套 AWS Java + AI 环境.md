# Day 39：Terraform + AWS IaC —— 用代码搭建整套 AWS Java + AI 环境

Day 38 我们设计了生产架构。

今天不再手工点击 AWS Console，而是：

```text
Terraform
    ↓
AWS Infrastructure
    ↓
VPC
ECS
RDS
S3
SQS
IAM
CloudWatch
```

目标是做到：

```text
terraform init
terraform plan
terraform apply
```

几分钟内创建一套可重复的 AWS 环境。

---

# 1. Day 39 目标

今天掌握：

```text
① Terraform
② Provider
③ Variables
④ Outputs
⑤ Modules
⑥ State
⑦ VPC
⑧ Subnets
⑨ Security Groups
⑩ ECR
⑪ S3
⑫ RDS
⑬ ECS
⑭ IAM
⑮ Terraform CI/CD
```

最终：

```text
GitHub
   │
   ▼
Terraform
   │
   ▼
AWS
├── VPC
├── ECS
├── RDS
├── S3
├── SQS
├── IAM
└── CloudWatch
```

---

# 2. 为什么必须学 Terraform

手工：

```text
AWS Console
 ↓
Create VPC
 ↓
Create Subnet
 ↓
Create SG
 ↓
Create ECS
 ↓
Create RDS
 ↓
...
```

问题：

```text
容易出错
不可重复
难以审计
难以回滚
```

Terraform：

```text
Code
 ↓
Review
 ↓
Plan
 ↓
Apply
```

---

# 3. Infrastructure as Code

IaC：

> Infrastructure as Code

你的整个 AWS 环境：

```text
Code
```

例如：

```text
vpc.tf
ecs.tf
rds.tf
s3.tf
iam.tf
```

Git：

```text
commit
 ↓
review
 ↓
deploy
```

---

# 4. Terraform 项目结构

今天建立：

```text
infrastructure/
│
├── environments/
│   ├── dev/
│   ├── staging/
│   └── prod/
│
├── modules/
│   ├── network/
│   ├── security/
│   ├── ecr/
│   ├── ecs/
│   ├── rds/
│   ├── redis/
│   ├── s3/
│   ├── sqs/
│   └── monitoring/
│
└── README.md
```

---

# 5. 第一版可以简单一点

Day 39 不要一开始就做巨大 Terraform Module。

先：

```text
terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── network.tf
├── security.tf
├── ecr.tf
├── s3.tf
├── rds.tf
└── ecs.tf
```

跑通以后再 Module 化。

---

# 6. Terraform Provider

`main.tf`：

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

---

# 7. AWS Region

`variables.tf`：

```hcl
variable "aws_region" {
  type    = string
  default = "us-west-2"
}
```

实际部署前确认你选择的 Region 支持所需 Bedrock 模型及相关服务。

---

# 8. Terraform 初始化

进入：

```bash
cd infrastructure
```

执行：

```bash
terraform init
```

然后：

```bash
terraform version
```

检查：

```text
Terraform
AWS Provider
```

---

# 9. Terraform Plan

```bash
terraform plan
```

它不会创建资源。

它只告诉你：

```text
+ create
~ update
- destroy
```

所以：

```text
plan
=
预览
```

---

# 10. Terraform Apply

```bash
terraform apply
```

确认：

```text
yes
```

Terraform 开始创建：

```text
AWS
 ↓
VPC
 ↓
Subnets
 ↓
Security Groups
...
```

---

# 11. Terraform State

这是非常重要的概念：

```text
terraform.tfstate
```

Terraform 通过 State 知道：

```text
Terraform Code
      ↓
AWS
```

对应关系。

---

# 12. 不要把 State 放 Git

错误：

```text
git add terraform.tfstate
```

因为 State 可能包含：

```text
Sensitive Information
```

必须：

```text
.gitignore
```

加入：

```text
*.tfstate
*.tfstate.*
.terraform/
```

---

# 13. Production State

开发：

```text
Local State
```

生产：

```text
S3 Backend
+
Locking
```

现在 AWS 推荐的 Terraform 状态锁定方式可以采用 S3 backend 的 locking 能力；不要机械照搬旧教程里必须单独创建 DynamoDB lock table 的做法。

---

# 14. S3 Backend

例如：

```hcl
terraform {
  backend "s3" {
    bucket = "company-ai-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-west-2"
  }
}
```

生产环境还应该配合：

```text
S3 Versioning
Encryption
Restricted IAM
```

---

# 15. VPC

创建：

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "ai-platform-vpc"
  }
}
```

---

# 16. Public Subnet

```hcl
resource "aws_subnet" "public_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-west-2a"

  tags = {
    Name = "public-a"
  }
}
```

再创建：

```text
public_b
```

---

# 17. Private App Subnet

```hcl
resource "aws_subnet" "app_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.10.0/24"
  availability_zone = "us-west-2a"

  tags = {
    Name = "app-a"
  }
}
```

再创建：

```text
app_b
```

---

# 18. Private Data Subnet

```hcl
resource "aws_subnet" "data_a" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.20.0/24"
  availability_zone = "us-west-2a"

  tags = {
    Name = "data-a"
  }
}
```

再创建：

```text
data_b
```

---

# 19. Network Architecture

Terraform 最终创建：

```text
VPC
│
├── Public A
├── Public B
│
├── App A
├── App B
│
├── Data A
└── Data B
```

---

# 20. Internet Gateway

```hcl
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
}
```

连接：

```text
Public Subnet
      ↓
Internet Gateway
      ↓
Internet
```

---

# 21. Route Table

Public：

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id  = aws_internet_gateway.main.id
  }
}
```

然后：

```text
Public Subnet
 ↓
Public Route Table
 ↓
Internet Gateway
```

---

# 22. NAT Gateway

生产：

```text
Private App
 ↓
NAT Gateway
 ↓
Internet
```

但是：

> NAT Gateway 会产生额外成本。

Day 39 开发环境先理解架构，正式部署时根据流量和 VPC Endpoint 策略决定 NAT 数量。

---

# 23. Security Group

ECS：

```hcl
resource "aws_security_group" "ecs" {
  name   = "ecs-sg"
  vpc_id = aws_vpc.main.id
}
```

只允许：

```text
ALB SG
 ↓
8080
```

---

# 24. RDS Security Group

```text
ECS
 ↓
5432
 ↓
RDS
```

Terraform：

```hcl
resource "aws_security_group_rule" "rds_from_ecs" {
  type                     = "ingress"
  security_group_id        = aws_security_group.rds.id
  source_security_group_id = aws_security_group.ecs.id

  from_port = 5432
  to_port   = 5432
  protocol  = "tcp"
}
```

---

# 25. ECR

建立 Java API Repository：

```hcl
resource "aws_ecr_repository" "ai_api" {
  name = "ai-api"
}
```

得到：

```text
ECR
└── ai-api
```

Docker：

```text
Spring Boot
 ↓
Docker Image
 ↓
ECR
```

---

# 26. ECR 镜像

本地：

```bash
docker build -t ai-api .
```

登录 ECR：

```bash
aws ecr get-login-password \
  --region us-west-2 |
docker login \
  --username AWS \
  --password-stdin \
  <ACCOUNT>.dkr.ecr.us-west-2.amazonaws.com
```

然后：

```bash
docker tag ai-api:latest \
  <ACCOUNT>.dkr.ecr.us-west-2.amazonaws.com/ai-api:latest
```

Push：

```bash
docker push \
  <ACCOUNT>.dkr.ecr.us-west-2.amazonaws.com/ai-api:latest
```

---

# 27. S3 Terraform

```hcl
resource "aws_s3_bucket" "documents" {
  bucket = "company-ai-documents-prod"
}
```

生产至少考虑：

```text
Block Public Access
Versioning
Encryption
Lifecycle
```

---

# 28. S3 Versioning

```hcl
resource "aws_s3_bucket_versioning" "documents" {
  bucket = aws_s3_bucket.documents.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

---

# 29. S3 Public Access Block

```hcl
resource "aws_s3_bucket_public_access_block" "documents" {
  bucket = aws_s3_bucket.documents.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

---

# 30. RDS

Terraform：

```hcl
resource "aws_db_instance" "postgres" {

  engine = "postgres"

  instance_class = "db.t4g.micro"

  allocated_storage = 20

  db_name  = "ai_platform"
  username = var.db_username
  password = var.db_password

  skip_final_snapshot = true
}
```

⚠️ 这只是 **Day 39 开发环境示例**。

生产环境：

```text
Multi-AZ
Encryption
Backup
Deletion Protection
```

并且不要把数据库密码直接写在 `.tf` 文件里。

---

# 31. pgvector

RDS PostgreSQL 创建以后：

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

然后：

```text
documents
 ↓
chunks
 ↓
embedding
 ↓
pgvector
```

---

# 32. Terraform Variables

```hcl
variable "db_username" {
  type      = string
  sensitive = true
}

variable "db_password" {
  type      = string
  sensitive = true
}
```

但注意：

> `sensitive = true` 只会隐藏 CLI 输出，不等于秘密自动安全存储。

生产应该使用：

```text
AWS Secrets Manager
```

---

# 33. ECS Cluster

```hcl
resource "aws_ecs_cluster" "main" {
  name = "ai-platform"
}
```

得到：

```text
ECS Cluster
└── ai-platform
```

---

# 34. ECS Task Definition

Java：

```text
Container
 ↓
8080
```

Terraform：

```hcl
resource "aws_ecs_task_definition" "ai_api" {
  family                   = "ai-api"
  requires_compatibilities = ["FARGATE"]
  network_mode              = "awsvpc"

  cpu    = "1024"
  memory = "2048"

  container_definitions = jsonencode([
    {
      name  = "ai-api"
      image = var.ai_api_image

      portMappings = [
        {
          containerPort = 8080
          protocol      = "tcp"
        }
      ]
    }
  ])
}
```

---

# 35. ECS Service

```hcl
resource "aws_ecs_service" "ai_api" {
  name            = "ai-api"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.ai_api.arn

  desired_count = 2

  launch_type = "FARGATE"
}
```

---

# 36. ECS Network

```text
ALB
 ↓
Private App Subnet
 ↓
ECS
```

ECS：

```text
assign_public_ip = false
```

不要给生产 ECS Task：

```text
Public IP
```

---

# 37. ALB

Terraform：

```hcl
resource "aws_lb" "main" {
  name               = "ai-platform-alb"
  load_balancer_type = "application"

  subnets         = var.public_subnet_ids
  security_groups = [aws_security_group.alb.id]
}
```

---

# 38. Target Group

```hcl
resource "aws_lb_target_group" "ai_api" {
  name        = "ai-api"
  port        = 8080
  protocol    = "HTTP"
  target_type = "ip"
  vpc_id      = aws_vpc.main.id

  health_check {
    path = "/actuator/health"
  }
}
```

---

# 39. ALB Listener

生产：

```text
443
 ↓
TLS
 ↓
Target Group
```

不要长期使用：

```text
HTTP only
```

HTTPS Certificate：

```text
ACM
```

---

# 40. IAM Task Role

ECS Agent 需要：

```text
Bedrock
S3
Secrets Manager
CloudWatch
```

但必须最小权限。

例如：

```text
ECS Task Role
│
├── Bedrock Invoke
├── S3 GetObject
└── SecretsManager GetSecretValue
```

---

# 41. Terraform Output

`outputs.tf`：

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}

output "ecr_repository_url" {
  value = aws_ecr_repository.ai_api.repository_url
}

output "ecs_cluster_name" {
  value = aws_ecs_cluster.main.name
}
```

执行：

```bash
terraform output
```

得到：

```text
vpc_id
ecr_repository_url
ecs_cluster_name
```

---

# 42. Terraform Dependency Graph

Terraform 会自动分析：

```text
VPC
 ↓
Subnet
 ↓
Security Group
 ↓
ECS
 ↓
ALB
```

你不需要自己：

```text
先创建 A
再创建 B
```

Terraform 会根据资源依赖建立 Graph。

---

# 43. `terraform plan`

执行：

```bash
terraform plan
```

你应该看到：

```text
Plan: 25 to add,
0 to change,
0 to destroy.
```

具体数量取决于你写了哪些资源。

---

# 44. `terraform apply`

```bash
terraform apply
```

成功：

```text
Apply complete!
```

然后：

```bash
terraform output
```

检查资源。

---

# 45. 修改基础设施

例如：

```text
ECS
CPU
1 vCPU
 ↓
2 vCPU
```

只修改：

```hcl
cpu = "2048"
```

然后：

```bash
terraform plan
```

Terraform：

```text
~ update
```

再：

```bash
terraform apply
```

---

# 46. Terraform Destroy

开发环境：

```bash
terraform destroy
```

Terraform：

```text
VPC
ECS
RDS
S3
...
```

全部删除。

所以：

> **生产环境千万不要随便执行 `terraform destroy`。**

---

# 47. Production Protection

生产：

```text
Deletion Protection
```

例如 RDS：

```text
Deletion Protection = true
```

并且 Terraform：

```hcl
lifecycle {
  prevent_destroy = true
}
```

---

# 48. Terraform Module

Day 39 后半段开始 Module。

例如：

```text
modules/
└── network/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

主配置：

```hcl
module "network" {
  source = "../../modules/network"

  vpc_cidr = "10.0.0.0/16"
}
```

---

# 49. 为什么需要 Module

否则：

```text
dev/main.tf
staging/main.tf
prod/main.tf
```

大量重复。

Module：

```text
Network Module
      │
 ┌────┼────┐
 ▼    ▼    ▼
DEV STG  PROD
```

---

# 50. Environment

最终：

```text
environments/
│
├── dev/
│   └── main.tf
│
├── staging/
│   └── main.tf
│
└── prod/
    └── main.tf
```

共用：

```text
modules/
```

---

# 51. Dev

```text
ECS = 1
RDS = small
Multi-AZ = false
```

---

# 52. Staging

```text
ECS = 2
RDS = medium
Multi-AZ = true
```

---

# 53. Production

```text
ECS >= 2
RDS Multi-AZ
Backup
Encryption
Deletion Protection
Monitoring
```

---

# 54. Terraform CI/CD

最终：

```text
GitHub
   │
   ▼
Pull Request
   │
   ▼
terraform fmt
   │
   ▼
terraform validate
   │
   ▼
terraform plan
   │
   ▼
Review
   │
   ▼
terraform apply
```

---

# 55. GitHub Actions

```yaml id="h6t4mw"
name: Terraform

on:
  pull_request:

jobs:
  terraform:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3

      - run: terraform fmt -check

      - run: terraform init

      - run: terraform validate

      - run: terraform plan
```

生产 `apply` 建议通过受保护环境、审批以及短期 AWS 身份凭证完成，而不是把长期 AWS Access Key 放进 GitHub Secrets。

---

# 56. AWS 身份认证

推荐：

```text
GitHub Actions
       │
       ▼
OIDC
       │
       ▼
AWS IAM Role
```

不要：

```text
GitHub
 ↓
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

长期 Access Key 不应该作为 CI/CD 的默认方案。

---

# 57. Terraform + Day 37 Security

现在安全变成：

```text
Terraform
 ↓
IAM
 ↓
Security Groups
 ↓
Private Subnet
 ↓
Secrets Manager
 ↓
ECS Task Role
```

整个 Infrastructure 也进入：

```text
Security as Code
```

---

# 58. Terraform + Day 36 Observability

Terraform 创建：

```text
CloudWatch
 ↓
Log Group
 ↓
Metrics
 ↓
Alarm
```

例如：

```text
ECS CPU > 80%
```

Alarm。

---

# 59. Terraform + Day 35 Memory

Terraform：

```text
RDS
 ↓
PostgreSQL
 ↓
pgvector
```

Memory：

```text
PostgreSQL
```

---

# 60. Terraform + Day 34 Workflow

Terraform：

```text
ECS
 ↓
Agent API
 ↓
Workflow
```

Checkpoint：

```text
RDS
```

Approval：

```text
RDS
```

---

# 61. Terraform + Day 33 MCP

Terraform：

```text
ECS
 ├── AI API
 └── MCP Server
```

建议：

```text
AI API
 ↓
Private Network
 ↓
MCP Server
```

MCP Server 不直接暴露 Internet。

---

# 62. Terraform + Day 32 RAG

```text
S3
 ↓
SQS
 ↓
ECS Worker
 ↓
RDS PostgreSQL
 ↓
pgvector
```

整个 RAG ingestion pipeline 都可以 IaC 化。

---

# 63. Day 39 完整 AWS Architecture

```text
                              GitHub
                                 │
                        Terraform / CI/CD
                                 │
                                 ▼
                              AWS
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                   VPC                    CloudWatch
                    │
          ┌─────────┴─────────┐
          │                   │
       Public              Private
          │                   │
          ▼                   ▼
         ALB              ECS Fargate
                              │
                 ┌────────────┼────────────┐
                 ▼            ▼            ▼
              AI API         MCP        Worker
                 │
          ┌──────┼──────┐
          ▼      ▼      ▼
         RAG   Memory  Bedrock
          │      │
          └──┬───┘
             ▼
       PostgreSQL
        + pgvector

S3
 │
 └── SQS → Worker

Redis
 │
 └── Cache / Rate Limit

Secrets Manager
 │
 └── ECS

ECR
 │
 └── ECS Images
```

---

# 64. Day 39 最终项目结构

```text
aws-java-ai-platform/
│
├── frontend/
│
├── backend/
│
├── mcp-server/
│
├── ingestion-worker/
│
├── infrastructure/
│   │
│   ├── modules/
│   │   ├── network/
│   │   ├── security/
│   │   ├── ecr/
│   │   ├── ecs/
│   │   ├── rds/
│   │   ├── redis/
│   │   ├── s3/
│   │   ├── sqs/
│   │   └── monitoring/
│   │
│   └── environments/
│       ├── dev/
│       ├── staging/
│       └── prod/
│
├── docker/
│
└── .github/
    └── workflows/
        ├── terraform.yml
        ├── backend.yml
        └── frontend.yml
```

---

# 65. Day 39 实战顺序

今天不要一次性创建所有东西。

按这个顺序：

```text
Step 1
Terraform 安装
       ↓
Step 2
AWS CLI
       ↓
Step 3
Provider
       ↓
Step 4
VPC
       ↓
Step 5
Subnets
       ↓
Step 6
Route Tables
       ↓
Step 7
Security Groups
       ↓
Step 8
ECR
       ↓
Step 9
S3
       ↓
Step 10
RDS
       ↓
Step 11
ECS
       ↓
Step 12
ALB
       ↓
Step 13
IAM
       ↓
Step 14
CloudWatch
       ↓
Step 15
terraform plan
       ↓
Step 16
terraform apply
```

---

# 66. Day 39 必做练习

### Exercise 1

创建：

```text
VPC
10.0.0.0/16
```

---

### Exercise 2

创建：

```text
2 Public
2 App
2 Data
```

---

### Exercise 3

Security：

```text
ALB → ECS
ECS → RDS
ECS → Redis
```

---

### Exercise 4

创建：

```text
ECR
ai-api
```

---

### Exercise 5

创建：

```text
S3
documents
```

---

### Exercise 6

创建：

```text
RDS PostgreSQL
```

---

### Exercise 7

创建：

```text
ECS Fargate
```

运行：

```text
Spring Boot
```

---

### Exercise 8

访问：

```text
https://your-domain/actuator/health
```

看到：

```json
{
  "status": "UP"
}
```

就完成了 Day 39 最重要的目标。

---

# 67. Day 39 验收标准

最终必须做到：

```text
[✓] terraform init
[✓] terraform validate
[✓] terraform plan
[✓] terraform apply
[✓] VPC created
[✓] Private subnet created
[✓] ECR created
[✓] Docker image pushed
[✓] ECS running
[✓] ALB healthy
[✓] RDS connected
[✓] S3 accessible
[✓] Secrets Manager accessible
[✓] CloudWatch logs working
[✓] Bedrock callable
```

---

# 68. Day 31 → Day 39

你现在的学习路线已经形成一条完整链：

```text
Day 31
Advanced RAG
        ↓
Day 32
Enterprise Retrieval
        ↓
Day 33
MCP
        ↓
Day 34
Agent Workflow
        ↓
Day 35
Memory
        ↓
Day 36
Observability
        ↓
Day 37
Security
        ↓
Day 38
AWS Production Architecture
        ↓
Day 39
Terraform / Infrastructure as Code
```

最终不是单纯学习：

```text
Java
+
AI
```

而是在构建：

```text
                    Enterprise AI Platform

React
  │
  ▼
CloudFront
  │
  ▼
ALB
  │
  ▼
ECS Fargate
  │
  ├── Spring Boot
  ├── Spring AI
  ├── Agent Workflow
  ├── Memory
  ├── RAG
  └── MCP
        │
        ├── Bedrock
        ├── PostgreSQL + pgvector
        ├── Redis
        ├── S3
        └── SQS

        +
        
Security
Observability
Evaluation
Terraform
CI/CD
```

**Day 40** 就可以进入一个非常关键的阶段：**CI/CD + Dev → Staging → Production 自动部署**。届时会把 `GitHub → Maven Test → Docker → ECR → ECS → Health Check → Rolling/Blue-Green Deployment → Rollback` 整条企业 Java + AI 发布流水线真正打通。
