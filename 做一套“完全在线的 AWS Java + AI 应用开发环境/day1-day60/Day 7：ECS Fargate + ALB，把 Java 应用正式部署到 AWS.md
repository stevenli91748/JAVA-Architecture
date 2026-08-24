# Day 7：ECS Fargate + ALB，把 Java 应用正式部署到 AWS

今天是第一个真正的**云部署日**。

昨天：

```text
Spring Boot
   ↓
Docker
   ↓
ECR
```

今天：

```text
ECR
 ↓
ECS Fargate
 ↓
ALB
 ↓
Spring Boot
 ↓
RDS PostgreSQL
```

最终浏览器可以访问：

```text
https://你的域名/api/users
```

---

# 1. 今天的最终架构

```text
                    Internet
                       │
                       ▼
                ┌─────────────┐
                │     ALB     │
                │ Port 80/443 │
                └──────┬──────┘
                       │
                       ▼
             ┌──────────────────┐
             │   ECS Fargate    │
             │                  │
             │ Spring Boot      │
             │ Port 8080        │
             └────────┬─────────┘
                      │
                      ▼
              ┌──────────────┐
              │ RDS          │
              │ PostgreSQL   │
              └──────────────┘
```

---

# 2. 今天完成

```text
[1] ECS Cluster
[2] IAM Task Role
[3] ECS Task Definition
[4] ECS Service
[5] ALB
[6] Target Group
[7] Security Groups
[8] ECS → RDS
[9] Health Check
[10] 公网访问
```

---

# 3. 先检查 RDS

进入：

```text
RDS
 → Databases
 → java-ai-postgres
```

确认：

```text
Status:
Available
```

今天不要停止 RDS。

---

# 4. 创建 ECS Cluster

进入：

[Amazon ECS Console](https://console.aws.amazon.com/ecs/?utm_source=chatgpt.com)

选择：

```text
Clusters
 → Create cluster
```

名称：

```text id="2q7m5p"
java-ai-cluster
```

Infrastructure：

```text
AWS Fargate
```

创建。

最终：

```text
ECS
└── java-ai-cluster
```

---

# 5. 建立 ECS Security Group

进入：

```text
EC2
 → Security Groups
 → Create security group
```

名称：

```text id="k3r8x1"
java-ai-ecs-sg
```

VPC：

```text
java-ai-vpc
```

Inbound：

```text
Type:
Custom TCP

Port:
8080

Source:
java-ai-alb-sg
```

注意：

**不要开放 `0.0.0.0/0:8080`。**

最终：

```text
Internet
   ↓
ALB
   ↓ 8080
ECS
```

---

# 6. 建立 ALB Security Group

再建立：

```text id="w6p2n9"
java-ai-alb-sg
```

Inbound：

```text
HTTP
Port 80
Source:
0.0.0.0/0
```

暂时先这样。

Day 9 加 HTTPS：

```text
HTTPS
443
```

---

# 7. 修改 RDS Security Group

昨天 RDS 允许：

```text
你的IP → 5432
```

今天应该增加：

```text
ECS Security Group → 5432
```

进入：

```text
EC2
 → Security Groups
 → java-ai-rds-sg
```

Inbound：

```text
PostgreSQL
5432
Source:
java-ai-ecs-sg
```

以后：

```text
ECS
 │
 │ 5432
 ▼
RDS
```

不再需要：

```text
Internet → RDS
```

**这是今天非常重要的一步。**

---

# 8. 创建 IAM Task Execution Role

进入：

```text
IAM
 → Roles
 → Create role
```

Trusted entity：

```text
AWS service
```

Use case：

```text
Elastic Container Service
```

选择：

```text
Elastic Container Service Task
```

添加：

```text
AmazonECSTaskExecutionRolePolicy
```

Role name：

```text id="r2m7x5"
java-ai-ecs-execution-role
```

这个 Role 负责：

```text
ECS
 ↓
Pull ECR Image
 ↓
CloudWatch Logs
```

---

# 9. 创建 Task Role

再创建一个：

```text id="p8k4z2"
java-ai-ecs-task-role
```

这个 Role 给你的 Java 程序使用。

以后：

```text
Spring Boot
     │
     ├── Bedrock
     ├── S3
     ├── Secrets Manager
     └── other AWS APIs
```

都通过：

```text
IAM Role
```

访问。

今天先不给它太多权限。

---

# 10. 创建 CloudWatch Log Group

进入：

```text id="f4x9c6"
CloudWatch
 → Logs
 → Log groups
```

创建：

```text id="d8q2m5"
/aws/ecs/java-ai-backend
```

Retention：

```text
7 days
```

学习环境没必要永久保存日志。

---

# 11. 创建 ECS Task Definition

进入：

```text id="n7v3k8"
ECS
 → Task definitions
 → Create
```

选择：

```text
Fargate
```

名称：

```text id="y5p2q9"
java-ai-backend
```

---

# 12. Task Size

学习环境：

```text id="w2m8c4"
CPU:
0.5 vCPU

Memory:
1 GB
```

够我们现在的 Spring Boot Demo 使用。

---

# 13. Task Role

Execution role：

```text id="r6x1p8"
java-ai-ecs-execution-role
```

Task role：

```text id="c9m4z7"
java-ai-ecs-task-role
```

---

# 14. Container

Container name：

```text id="q8v2n5"
java-ai-backend
```

Image URI：

```text
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.0.0
```

Port:

```text
8080
TCP
```

---

# 15. Environment Variables

在 Task Definition 里面设置：

```text id="g4k9x1"
DB_HOST
YOUR_RDS_ENDPOINT

DB_NAME
enterprise_ai

DB_USERNAME
app_admin

DB_PASSWORD
YOUR_PASSWORD
```

### 但是这里有一个问题

**不要长期把 DB_PASSWORD 直接放 Environment Variables。**

Day 7 为了先跑通可以这样做。

Day 9 我们会改成：

```text
ECS
 ↓
Secrets Manager
 ↓
DB_PASSWORD
```

---

# 16. Log Configuration

Container logs：

```text id="h2m7c5"
awslogs
```

Log group：

```text
/aws/ecs/java-ai-backend
```

Region：

```text
us-west-2
```

Stream prefix：

```text
ecs
```

---

# 17. 创建 Task Definition

点击：

```text id="n6r1x8"
Create
```

现在：

```text
ECR
  │
  ▼
Task Definition
```

已经建立。

---

# 18. 创建 Target Group

进入：

```text id="p4z7m2"
EC2
 → Target Groups
 → Create target group
```

选择：

```text
Target type:
IP addresses
```

名称：

```text id="c8x3v5"
java-ai-tg
```

Protocol：

```text
HTTP
```

Port：

```text
8080
```

Health check：

```text
Path:
/actuator/health
```

Expected：

```text
HTTP 200
```

---

# 19. 创建 ALB

进入：

```text id="v6q2n8"
EC2
 → Load Balancers
 → Create Load Balancer
```

选择：

```text
Application Load Balancer
```

Name：

```text id="j3k8m1"
java-ai-alb
```

Scheme：

```text
Internet-facing
```

IP type：

```text
IPv4
```

---

# 20. ALB Network

VPC：

```text
java-ai-vpc
```

选择两个：

```text
Public Subnet
```

例如：

```text
us-west-2a
us-west-2b
```

Security Group：

```text
java-ai-alb-sg
```

Listener：

```text
HTTP
80
```

Forward to：

```text
java-ai-tg
```

创建。

---

# 21. 创建 ECS Service

进入：

```text id="x9c4k6"
ECS
 → java-ai-cluster
 → Create service
```

Compute：

```text
Fargate
```

Task Definition：

```text
java-ai-backend
```

Service name：

```text
java-ai-backend-service
```

Desired tasks：

```text
1
```

---

# 22. Network

VPC：

```text
java-ai-vpc
```

这里有一个关键选择。

为了 Day 7 先把系统跑起来：

```text
Subnets:
Public Subnets
```

Task security group：

```text
java-ai-ecs-sg
```

Public IP：

```text
Enabled
```

这样：

```text
ALB
 ↓
ECS
 ↓
RDS
```

先跑通。

---

# 23. 更标准的企业架构

最终我们会改成：

```text
ALB
 │
 ▼
Private ECS
 │
 ▼
Private RDS
```

也就是：

```text
Public Subnet
     │
     ▼
    ALB
     │
     ▼
Private Subnet
     │
     ▼
    ECS
     │
     ▼
Private Subnet
     │
     ▼
    RDS
```

**Day 7 先验证功能，Day 9/10 再做企业级网络收紧。**

---

# 24. ECS Load Balancing

Service 配置：

```text
Load balancing:
Application Load Balancer
```

选择：

```text
java-ai-alb
```

Container：

```text
java-ai-backend:8080
```

Target Group：

```text
java-ai-tg
```

---

# 25. 创建 Service

点击：

```text
Create
```

等待：

```text
ECS Service
Status:
Active
```

然后：

```text
Tasks
```

应该：

```text
Running:
1
Desired:
1
```

---

# 26. 检查 Task

点击 Task。

确认：

```text
Last status:
RUNNING
```

如果：

```text
STOPPED
```

不要猜。

进入：

```text
Stopped reason
```

通常检查：

```text
ECR permission
Image
Environment variable
Security Group
RDS connection
Application startup
```

---

# 27. 查看 CloudWatch Logs

进入：

```text
CloudWatch
 → Log groups
 → /aws/ecs/java-ai-backend
```

打开最新 Stream。

应该看到：

```text
Spring Boot
...
Tomcat started on port 8080
```

如果看到：

```text
Connection refused
```

重点检查：

```text
RDS Security Group
DB_HOST
DB_PASSWORD
DB_NAME
```

---

# 28. 检查 Target Group

进入：

```text
EC2
 → Target Groups
 → java-ai-tg
 → Targets
```

你希望看到：

```text
Status:
Healthy
```

如果：

```text
Unhealthy
```

检查：

```text
/actuator/health
```

以及：

```text
ECS Container Port:
8080
```

---

# 29. 获取 ALB DNS

进入：

```text id="r1w6k9"
EC2
 → Load Balancers
 → java-ai-alb
```

找到：

```text
DNS name
```

类似：

```text
java-ai-alb-123456.us-west-2.elb.amazonaws.com
```

浏览器访问：

```text
http://java-ai-alb-123456.us-west-2.elb.amazonaws.com/actuator/health
```

应该：

```json id="k8p2v4"
{
  "status": "UP"
}
```

🎉 **这一步成功，就代表你的 Spring Boot 已经真正跑在 AWS ECS Fargate。**

---

# 30. 测试 Users API

浏览器或 Codespaces：

```bash id="m4x7q1"
curl \
http://YOUR_ALB_DNS/api/users
```

应该得到：

```json id="q3n8v6"
[
  {
    "id": 1,
    "name": "AWS User",
    "email": "aws@example.com"
  }
]
```

然后：

```bash id="z7p2c9"
curl -X POST \
http://YOUR_ALB_DNS/api/users \
-H "Content-Type: application/json" \
-d '{"name":"ECS User","email":"ecs@example.com"}'
```

如果成功：

```text id="w6k3m1"
Browser
   ↓
ALB
   ↓
ECS Fargate
   ↓
Spring Boot
   ↓
RDS PostgreSQL
```

**整个链路跑通。**

---

# 31. 今天真正完成了什么？

你现在的架构已经从：

```text
Codespaces
   ↓
Spring Boot
   ↓
RDS
```

升级成：

```text
Internet
   │
   ▼
   ALB
   │
   ▼
ECS Fargate
   │
   ▼
Spring Boot
   │
   ▼
RDS PostgreSQL
```

而 Docker：

```text
Docker
   ↓
ECR
   ↓
ECS
```

也已经完全打通。

---

# 32. Day 7 完成检查

```text
[ ] ECS Cluster
[ ] IAM Execution Role
[ ] IAM Task Role
[ ] CloudWatch Log Group
[ ] Task Definition
[ ] ECR Image
[ ] ECS Service
[ ] ECS Task RUNNING
[ ] Target Group
[ ] Target Healthy
[ ] ALB
[ ] ALB DNS
[ ] /actuator/health = UP
[ ] /api/users 正常
[ ] ECS → RDS 正常
```

---

# 33. 当前完整环境

```text
                         Internet
                            │
                            ▼
                    ┌──────────────┐
                    │     ALB      │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ ECS Fargate  │
                    │              │
                    │ Spring Boot  │
                    │ Java 21      │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ RDS          │
                    │ PostgreSQL   │
                    └──────────────┘

Developer
    │
    ▼
GitHub Codespaces
    │
    ▼
Docker
    │
    ▼
ECR
    │
    ▼
ECS
```

### 下一步 Day 8：真正接入 AI

我们开始：

```text
Spring Boot
     │
     ▼
Spring AI
     │
     ▼
Amazon Bedrock
     │
     ├── Claude
     ├── Amazon Nova
     └── Embedding Model
```

然后做第一个真正的 **AI Chat API**：

```text
React Chat UI
      ↓
Spring Boot /api/chat
      ↓
Spring AI
      ↓
Amazon Bedrock
      ↓
AI Response
      ↓
React
```

到 Day 8，你的 AWS Java 环境就正式进入 **Java + AI Application Development** 阶段。
