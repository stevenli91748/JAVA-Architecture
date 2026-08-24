# Day 17：Terraform 企业级 VPC 网络

今天把之前为了学习而使用的：

```text
Internet
   ↓
ALB
   ↓
Public ECS
   ↓
RDS
```

升级成真正企业常用的：

```text
Internet
    │
    ▼
  ALB
Public Subnet
    │
    ▼
 Private ECS
    │
    ▼
 Private RDS
```

并通过：

```text
NAT Gateway
VPC Endpoints
Security Groups
```

控制出站和内部访问。

---

# 1. 今天最终架构

```text
                         Internet
                            │
                            ▼
                    ┌──────────────┐
                    │     ALB      │
                    │ Public AZ-a  │
                    │ Public AZ-b  │
                    └──────┬───────┘
                           │ :8080
                ┌──────────┴──────────┐
                ▼                     ▼
          Private ECS-A         Private ECS-B
                │                     │
                └──────────┬──────────┘
                           │
                           ▼
                    Private RDS
                     AZ-a / AZ-b

Private ECS
    │
    ▼
 NAT Gateway
    │
    ▼
 Internet
```

---

# 2. CIDR 规划

今天使用：

```text
VPC
10.10.0.0/16
```

划分：

```text
Public
├── 10.10.1.0/24
└── 10.10.2.0/24

Private ECS
├── 10.10.11.0/24
└── 10.10.12.0/24

Private RDS
├── 10.10.21.0/24
└── 10.10.22.0/24
```

结构：

```text
10.10.0.0/16
│
├── 10.10.1.0/24   Public-A
├── 10.10.2.0/24   Public-B
│
├── 10.10.11.0/24  ECS-A
├── 10.10.12.0/24  ECS-B
│
├── 10.10.21.0/24  RDS-A
└── 10.10.22.0/24  RDS-B
```

---

# 3. 为什么 ECS 必须放 Private Subnet

生产环境不应该：

```text
Internet
   ↓
ECS Public IP
```

因为攻击面更大。

应该：

```text
Internet
   ↓
ALB
   ↓
Private ECS
```

ECS 没有公网 IP。

---

# 4. Public Subnet 只有什么？

主要：

```text
ALB
NAT Gateway
```

例如：

```text
Public-A
10.10.1.0/24

ALB
NAT Gateway
```

---

# 5. Private ECS Subnet

```text
ECS-A
10.10.11.0/24

ECS-B
10.10.12.0/24
```

ECS：

```text
Public IP:
Disabled
```

---

# 6. Private RDS Subnet

```text
RDS-A
10.10.21.0/24

RDS-B
10.10.22.0/24
```

RDS：

```text
Public access:
No
```

---

# 7. Terraform Network Module

进入：

```text
infrastructure/terraform/modules/network/
```

最终：

```text
network/
├── main.tf
├── variables.tf
└── outputs.tf
```

---

# 8. variables.tf

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

variable "availability_zones" {
  type = list(string)
}
```

---

# 9. VPC

`main.tf`：

```hcl
resource "aws_vpc" "this" {

  cidr_block = var.vpc_cidr

  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "${var.project_name}-${var.environment}-vpc"
  }
}
```

---

# 10. Internet Gateway

```hcl
resource "aws_internet_gateway" "this" {

  vpc_id = aws_vpc.this.id

  tags = {
    Name =
      "${var.project_name}-${var.environment}-igw"
  }
}
```

---

# 11. Public Subnets

```hcl
resource "aws_subnet" "public" {

  count = length(var.availability_zones)

  vpc_id = aws_vpc.this.id

  cidr_block = cidrsubnet(
    var.vpc_cidr,
    8,
    count.index
  )

  availability_zone =
    var.availability_zones[count.index]

  map_public_ip_on_launch = false

  tags = {
    Name =
      "${var.project_name}-${var.environment}-public-${count.index + 1}"
  }
}
```

注意：

```text
ALB
```

不要求每个实例拥有 Public IP。

ALB 本身通过 Internet Gateway 提供公网访问。

---

# 12. Public Route Table

```hcl
resource "aws_route_table" "public" {

  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"

    gateway_id =
      aws_internet_gateway.this.id
  }

  tags = {
    Name =
      "${var.project_name}-${var.environment}-public-rt"
  }
}
```

---

# 13. Associate Public Subnets

```hcl
resource "aws_route_table_association" "public" {

  count = length(var.availability_zones)

  subnet_id =
    aws_subnet.public[count.index].id

  route_table_id =
    aws_route_table.public.id
}
```

现在：

```text
Internet
   ↓
IGW
   ↓
Public Subnet
   ↓
ALB
```

---

# 14. NAT Gateway

Private ECS 如果需要访问：

```text
Bedrock
ECR
AWS APIs
Internet
```

可能需要出站 Internet。

标准架构：

```text
Private ECS
     ↓
NAT Gateway
     ↓
Internet Gateway
     ↓
Internet
```

---

# 15. NAT Gateway 成本

这里要特别注意。

NAT Gateway 会产生：

```text
Hourly charge
+
Data processing charge
```

所以学习环境不建议随意创建：

```text
2 AZ × NAT Gateway
```

生产：

```text
NAT-A
NAT-B
```

高可用。

DEV：

```text
1 NAT
```

可以节约成本。

---

# 16. Day 17 DEV 使用一个 NAT

```text
Public-A
   │
   ▼
NAT Gateway
```

Private：

```text
ECS-A
ECS-B
```

都通过：

```text
NAT-A
```

生产以后：

```text
Private-A → NAT-A
Private-B → NAT-B
```

---

# 17. Elastic IP

NAT Gateway 需要 Elastic IP。

```hcl
resource "aws_eip" "nat" {

  domain = "vpc"

  tags = {
    Name =
      "${var.project_name}-${var.environment}-nat-eip"
  }
}
```

---

# 18. NAT Gateway

```hcl
resource "aws_nat_gateway" "this" {

  allocation_id =
    aws_eip.nat.id

  subnet_id =
    aws_subnet.public[0].id

  depends_on = [
    aws_internet_gateway.this
  ]

  tags = {
    Name =
      "${var.project_name}-${var.environment}-nat"
  }
}
```

---

# 19. Private ECS Subnets

```hcl
resource "aws_subnet" "private_ecs" {

  count = length(var.availability_zones)

  vpc_id = aws_vpc.this.id

  cidr_block = cidrsubnet(
    var.vpc_cidr,
    8,
    count.index + 10
  )

  availability_zone =
    var.availability_zones[count.index]

  tags = {
    Name =
      "${var.project_name}-${var.environment}-ecs-private-${count.index + 1}"
  }
}
```

生成：

```text
10.10.11.0/24
10.10.12.0/24
```

---

# 20. Private ECS Route Table

```hcl
resource "aws_route_table" "private_ecs" {

  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"

    nat_gateway_id =
      aws_nat_gateway.this.id
  }

  tags = {
    Name =
      "${var.project_name}-${var.environment}-ecs-private-rt"
  }
}
```

关联：

```hcl
resource "aws_route_table_association" "private_ecs" {

  count = length(var.availability_zones)

  subnet_id =
    aws_subnet.private_ecs[count.index].id

  route_table_id =
    aws_route_table.private_ecs.id
}
```

---

# 21. RDS Subnets

```hcl
resource "aws_subnet" "private_rds" {

  count = length(var.availability_zones)

  vpc_id = aws_vpc.this.id

  cidr_block = cidrsubnet(
    var.vpc_cidr,
    8,
    count.index + 20
  )

  availability_zone =
    var.availability_zones[count.index]

  tags = {
    Name =
      "${var.project_name}-${var.environment}-rds-private-${count.index + 1}"
  }
}
```

生成：

```text
10.10.21.0/24
10.10.22.0/24
```

---

# 22. RDS Route Table

RDS 不需要 Internet。

所以：

```text
RDS
 ↓
Private Subnet
 ↓
No Internet Route
```

可以创建独立 Route Table：

```hcl
resource "aws_route_table" "private_rds" {

  vpc_id = aws_vpc.this.id

  tags = {
    Name =
      "${var.project_name}-${var.environment}-rds-private-rt"
  }
}
```

---

# 23. RDS Association

```hcl
resource "aws_route_table_association" "private_rds" {

  count = length(var.availability_zones)

  subnet_id =
    aws_subnet.private_rds[count.index].id

  route_table_id =
    aws_route_table.private_rds.id
}
```

注意：

RDS Subnet 本身仍然可以访问 VPC 内部资源。

只是没有：

```text
0.0.0.0/0 → Internet
```

---

# 24. Network Outputs

`outputs.tf`：

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}

output "public_subnet_ids" {
  value = aws_subnet.public[*].id
}

output "private_ecs_subnet_ids" {
  value = aws_subnet.private_ecs[*].id
}

output "private_rds_subnet_ids" {
  value = aws_subnet.private_rds[*].id
}
```

以后：

```text
ECS Module
    ↓
private_ecs_subnet_ids

RDS Module
    ↓
private_rds_subnet_ids

ALB Module
    ↓
public_subnet_ids
```

---

# 25. Security Groups

这是今天第二个重点。

创建：

```text
modules/security/
```

最终：

```text
ALB-SG
ECS-SG
RDS-SG
```

---

# 26. ALB Security Group

```hcl
resource "aws_security_group" "alb" {

  name =
    "${var.project_name}-${var.environment}-alb-sg"

  vpc_id = var.vpc_id

  ingress {
    description = "HTTPS"
    protocol    = "tcp"
    from_port   = 443
    to_port     = 443
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTP redirect"
    protocol    = "tcp"
    from_port   = 80
    to_port     = 80
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

# 27. ECS Security Group

最重要：

```text
ECS
只允许
ALB
```

```hcl
resource "aws_security_group" "ecs" {

  name =
    "${var.project_name}-${var.environment}-ecs-sg"

  vpc_id = var.vpc_id

  ingress {
    description     = "ALB to ECS"
    protocol        = "tcp"
    from_port       = 8080
    to_port         = 8080

    security_groups = [
      aws_security_group.alb.id
    ]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

所以：

```text
Internet
   X
   │
   ▼
ECS :8080
```

而：

```text
ALB
 │
 ▼
ECS :8080
```

允许。

---

# 28. RDS Security Group

```hcl
resource "aws_security_group" "rds" {

  name =
    "${var.project_name}-${var.environment}-rds-sg"

  vpc_id = var.vpc_id

  ingress {
    description     = "PostgreSQL from ECS"
    protocol        = "tcp"
    from_port       = 5432
    to_port         = 5432

    security_groups = [
      aws_security_group.ecs.id
    ]
  }

  egress {
    protocol    = "-1"
    from_port   = 0
    to_port     = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

最终：

```text
Internet
   X
   │
   ▼
RDS :5432
```

只有：

```text
ECS-SG
   ↓
RDS-SG :5432
```

允许。

---

# 29. 最小权限网络

现在：

```text
Internet
    │
    │ 443
    ▼
 ALB-SG
    │
    │ 8080
    ▼
 ECS-SG
    │
    │ 5432
    ▼
 RDS-SG
```

这就是今天最重要的概念：

**Security Group 是基于身份/安全组引用进行最小权限控制，而不是简单依赖 IP 白名单。**

---

# 30. VPC Endpoint

现在 ECS：

```text
Private ECS
   ↓
NAT Gateway
   ↓
Internet
   ↓
AWS Service
```

其实很多 AWS 服务根本不需要经过公网。

例如：

```text
ECS
 ↓
ECR
S3
CloudWatch
Secrets Manager
```

可以通过 VPC Endpoint。

---

# 31. 为什么 VPC Endpoint 有价值

减少：

```text
Private ECS
 ↓
NAT
 ↓
Internet
 ↓
AWS
```

变成：

```text
Private ECS
 ↓
VPC Endpoint
 ↓
AWS Service
```

优势：

```text
更安全
更少公网依赖
部分场景降低 NAT 流量成本
```

---

# 32. Day 17 推荐 Endpoint

对于你的 Java + AI 平台，重点考虑：

```text
ECR API
ECR DKR
S3 Gateway
CloudWatch Logs
Secrets Manager
STS
Bedrock Runtime
```

不过不同 AWS Service 的 PrivateLink 支持和 Region 可用性需要按当前 Region 核实。

---

# 33. S3 Gateway Endpoint

例如：

```hcl
resource "aws_vpc_endpoint" "s3" {

  vpc_id = var.vpc_id

  service_name =
    "com.amazonaws.${var.aws_region}.s3"

  vpc_endpoint_type = "Gateway"

  route_table_ids = [
    aws_route_table.private_ecs.id
  ]
}
```

这样：

```text
Private ECS
   ↓
S3
```

可以不经过 NAT。

---

# 34. Secrets Manager Endpoint

可以创建 Interface Endpoint：

```text
Private ECS
    ↓
VPC Endpoint
    ↓
Secrets Manager
```

但 Interface Endpoint 会产生额外费用。

所以：

### DEV

```text
NAT Gateway
```

先够用。

### PROD

根据流量和安全要求决定：

```text
NAT
+
VPC Endpoints
```

---

# 35. Bedrock

你的应用：

```text
Spring AI
    ↓
AWS SDK
    ↓
Bedrock
```

DEV：

```text
Private ECS
 ↓
NAT
 ↓
Bedrock
```

后续企业生产环境可以研究：

```text
VPC Endpoint / PrivateLink
```

以减少对公网路径的依赖。

---

# 36. 修改 ECS

Day 7：

```text
ECS
Public IP:
Enabled
```

今天改成：

```text
ECS
Public IP:
Disabled
```

Subnet：

```text
Private ECS-A
Private ECS-B
```

Security Group：

```text
ECS-SG
```

最终：

```text
ALB
 ↓
Private ECS
```

---

# 37. 修改 ECS Terraform

ECS Service：

```hcl
network_configuration {

  subnets =
    module.network.private_ecs_subnet_ids

  security_groups = [
    module.security.ecs_security_group_id
  ]

  assign_public_ip = false
}
```

这句：

```hcl
assign_public_ip = false
```

非常重要。

---

# 38. ALB

ALB 使用：

```hcl
subnets =
  module.network.public_subnet_ids
```

所以：

```text
ALB
 ↓
Public Subnets
```

---

# 39. RDS

RDS 使用：

```hcl
subnet_ids =
  module.network.private_rds_subnet_ids
```

并：

```text
publicly_accessible = false
```

最终：

```text
RDS
Public:
NO
```

---

# 40. Terraform Plan

进入：

```bash
cd infrastructure/terraform/environments/dev
```

执行：

```bash
terraform fmt -recursive
```

然后：

```bash
terraform validate
```

然后：

```bash
terraform plan
```

重点检查：

```text
ECS
public IP:
false

RDS
public:
false

ALB
public subnets

ECS
private subnets

RDS
private subnets
```

---

# 41. 今天不要直接改现有环境

这一点再次强调。

如果现在 Terraform 是：

```text
New DEV VPC
```

就：

```bash
terraform apply
```

但是如果 Terraform 正在管理：

```text
Existing Production VPC
```

看到：

```text
Plan:
destroy aws_vpc...
```

**立即停止。**

生产网络绝对不能因为 Terraform 初次导入错误而被 destroy。

---

# 42. 迁移现有 AWS 资源

以后如果要把现有资源纳入 Terraform：

```text
AWS Existing Resource
        ↓
terraform import
        ↓
Terraform State
        ↓
Terraform Code
```

例如：

```bash
terraform import \
aws_vpc.main \
vpc-xxxxxxxx
```

然后：

```bash
terraform plan
```

直到：

```text
No changes
```

再继续。

---

# 43. Day 17 最终架构

```text
                         Internet
                            │
                       HTTPS :443
                            │
                            ▼
                    ┌──────────────┐
                    │     ALB      │
                    │   Public     │
                    └──────┬───────┘
                           │
                     SG: ALB-SG
                           │
                        :8080
                           │
                           ▼
              ┌────────────────────────┐
              │       Private ECS       │
              │                        │
              │   Spring Boot          │
              │   Spring AI            │
              │   RAG                  │
              └───────┬────────┬───────┘
                      │        │
                  :5432│        │ AWS APIs
                      │        │
                      ▼        ▼
                ┌─────────┐  NAT/VPC
                │   RDS   │  Endpoint
                │Private  │
                │pgvector │
                └─────────┘
```

---

# 44. Day 17 检查清单

```text
[ ] VPC 10.10.0.0/16
[ ] Public Subnet A
[ ] Public Subnet B
[ ] Private ECS A
[ ] Private ECS B
[ ] Private RDS A
[ ] Private RDS B
[ ] Internet Gateway
[ ] NAT Gateway
[ ] Public Route Table
[ ] Private ECS Route Table
[ ] Private RDS Route Table
[ ] ALB Security Group
[ ] ECS Security Group
[ ] RDS Security Group
[ ] ECS Public IP = false
[ ] RDS Public = false
[ ] S3 VPC Endpoint
[ ] Terraform plan
```

---

# Day 17 完成后

你的网络已经从：

```text
❌ 学习环境

Internet
   ↓
ALB
   ↓
Public ECS
   ↓
RDS
```

升级成：

```text
✅ 企业架构

Internet
   ↓
Route 53
   ↓
CloudFront / ALB
   ↓
Public Subnet
   ↓
Private ECS
   ↓
Private RDS
```

同时：

```text
Private ECS
   │
   ├── Secrets Manager
   ├── ECR
   ├── CloudWatch
   ├── Bedrock
   └── S3
```

通过：

```text
NAT Gateway
+
VPC Endpoints
```

进行受控访问。

**Day 18** 下一步做 **Terraform + CI/CD**：让 GitHub Pull Request 自动执行 `terraform fmt → validate → plan`，合并到 `main` 后再审批 `terraform apply`，正式建立 **Infrastructure CI/CD**。
