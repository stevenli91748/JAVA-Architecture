# Day 5：AWS VPC + RDS PostgreSQL

今天开始真正进入 AWS。

昨天：

```text
React
  ↓
Spring Boot
  ↓
PostgreSQL Docker
```

今天变成：

```text
React
  ↓
Spring Boot
  ↓
AWS RDS PostgreSQL
```

最终：

```text
GitHub Codespaces
        │
        │
        ▼
     AWS VPC
        │
   ┌────┴─────┐
   │          │
 Public     Private
 Subnet     Subnet
               │
               ▼
        RDS PostgreSQL
```

---

# 1. 今天完成 6 件事

```text
Day 5
│
├── VPC
├── Subnet
├── Security Group
├── RDS PostgreSQL
├── Spring Boot → RDS
└── Flyway → RDS
```

**今天先不创建 ECS。**

---

# 2. 为什么先做 VPC

AWS 企业应用基本都建立在 VPC 里面。

```text
VPC
│
├── Public Subnet
│
├── Private Subnet
│
└── Security Groups
```

以后：

```text
Internet
   │
   ▼
ALB
   │
   ▼
ECS
   │
   ▼
RDS
```

RDS 放在 Private Subnet。

---

# 3. 创建 VPC

进入 AWS Console：

[AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com)

确认 Region：

```text
US West (Oregon)
us-west-2
```

搜索：

```text
VPC
```

进入：

```text
VPC Dashboard
```

选择：

```text
Create VPC
```

选择：

```text
Resources to create:
VPC and more
```

设置：

```text
Name:
java-ai-vpc

IPv4 CIDR:
10.0.0.0/16

Availability Zones:
2

Public subnets:
2

Private subnets:
2

NAT gateways:
None
```

然后：

```text
Create VPC
```

---

# 4. 为什么现在 NAT Gateway = None

因为今天我们只需要：

```text
Codespaces
       ↓
RDS
```

如果马上建立 NAT Gateway，会产生持续费用。

等 Day 7 做 ECS Fargate 时，再决定是否建立 NAT Gateway。

**不要为了学习一开始就开大量收费资源。**

---

# 5. 查看 VPC

建立以后应该看到：

```text
java-ai-vpc
CIDR:
10.0.0.0/16
```

Subnet：

```text
Public:
10.0.0.0/20
10.0.16.0/20

Private:
10.0.128.0/20
10.0.144.0/20
```

具体 CIDR 可能不同，以 AWS 自动生成的为准。

---

# 6. 创建 RDS Security Group

进入：

```text id="zh9m3b"
EC2
 ↓
Security Groups
 ↓
Create security group
```

名称：

```text id="9xg58n"
java-ai-rds-sg
```

VPC：

```text id="l5t0j6"
java-ai-vpc
```

Inbound Rules：

```text id="a5cz8o"
Type:
PostgreSQL

Port:
5432
```

### 这里非常重要

不要：

```text id="1o7t2x"
Source:
0.0.0.0/0
```

否则全球 Internet 都可以尝试连接数据库。

---

# 7. Day 5 的特殊情况

我们的 Java 程序现在运行在：

```text
GitHub Codespaces
```

而不是 AWS VPC 内。

所以：

```text
Codespaces
      │
      │ Internet
      ▼
AWS RDS
```

如果 RDS 完全 Private：

```text
Codespaces ──X──> Private RDS
```

今天为了完成开发环境联调，我们可以采用**临时开发方案**：

```text
RDS
Public access:
Yes

Security Group:
只允许你的当前公网 IP
```

这不是最终生产架构。

---

# 8. 查询你的公网 IP

在 Codespaces Terminal：

```bash id="o8n1c2"
curl https://checkip.amazonaws.com
```

得到类似：

```text id="l3s1v8"
123.45.67.89
```

然后 RDS Security Group：

```text id="8k6r9j"
PostgreSQL
TCP
5432
Source:
你的IP/32
```

例如：

```text id="j1y3e8"
123.45.67.89/32
```

**不要使用：**

```text
0.0.0.0/0
```

---

# 9. 创建 RDS PostgreSQL

进入：

```text id="4f4mna"
RDS
 ↓
Databases
 ↓
Create database
```

选择：

```text id="b1c9ws"
Standard create
```

Engine：

```text id="o9x8sl"
PostgreSQL
```

版本：

选择 AWS 当前支持的稳定 PostgreSQL 版本即可。

---

# 10. Template

学习环境选择：

```text id="x8p4zo"
Free tier
```

如果你的账户/区域没有 Free Tier，就选择：

```text id="x4r2hc"
Dev/Test
```

---

# 11. Database Settings

设置：

```text id="b5m1u7"
DB identifier:

java-ai-postgres
```

Master username：

```text
app_admin
```

Password：

不要使用：

```text
app_password
```

生产环境应该使用：

```text
AWS Secrets Manager
```

今天可以先生成一个强密码并保存到安全位置。

---

# 12. Instance

学习环境：

```text id="s7a0lm"
db.t3.micro
```

或者 AWS 当前提供的低成本等效实例。

Storage：

```text
20 GB
```

暂时关闭：

```text
Storage autoscaling
```

如果只是 Day 5 学习，不需要大量磁盘。

---

# 13. Connectivity

选择：

```text id="1f1lbr"
VPC:
java-ai-vpc
```

Subnet group：

```text id="y6k0sf"
Create new DB subnet group
```

选择两个 Private Subnet。

然后：

```text id="b7p9yz"
Public access:
Yes
```

**再次强调：这是为了让今天的 Codespaces 能连接 RDS。**

Security Group：

```text id="w3x8v9"
java-ai-rds-sg
```

Port：

```text
5432
```

---

# 14. 创建 RDS

点击：

```text
Create database
```

等待：

```text
Status:
Available
```

---

# 15. 找 RDS Endpoint

进入：

```text id="9d1hpk"
RDS
 ↓
Databases
 ↓
java-ai-postgres
```

找到：

```text
Endpoint
```

类似：

```text
java-ai-postgres.xxxxxxxxx.us-west-2.rds.amazonaws.com
```

记录下来。

以后：

```text
Spring Boot
      ↓
java-ai-postgres.xxxxx.rds.amazonaws.com
      ↓
RDS PostgreSQL
```

---

# 16. 从 Codespaces 测试 RDS

安装 PostgreSQL Client。

Debian/Ubuntu 环境：

```bash id="vq3y2a"
sudo apt-get update
sudo apt-get install -y postgresql-client
```

然后：

```bash id="e2z1g7"
psql \
-h YOUR_RDS_ENDPOINT \
-U app_admin \
-d postgres \
-p 5432
```

输入密码。

如果看到：

```text id="jy5l9n"
postgres=>
```

成功。

---

# 17. 创建数据库

进入：

```sql id="s4f0x8"
CREATE DATABASE enterprise_ai;
```

然后：

```sql id="p2h8kh"
\l
```

应该看到：

```text id="7kq4x1"
enterprise_ai
```

退出：

```sql id="r3v0hz"
\q
```

---

# 18. 修改 Spring Boot

昨天：

```yaml id="0xq1qg"
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/enterprise_ai
```

今天改成：

```yaml id="q6f6p1"
spring:
  datasource:
    url: jdbc:postgresql://${DB_HOST}:5432/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}

  jpa:
    hibernate:
      ddl-auto: validate

  flyway:
    enabled: true
```

---

# 19. 设置环境变量

在 Codespaces Terminal：

```bash id="0z8u3g"
export DB_HOST="YOUR_RDS_ENDPOINT"
export DB_NAME="enterprise_ai"
export DB_USERNAME="app_admin"
export DB_PASSWORD="YOUR_PASSWORD"
```

检查：

```bash id="l4g7l9"
echo $DB_HOST
```

**不要执行：**

```bash
echo $DB_PASSWORD
```

---

# 20. 启动 Spring Boot

```bash id="wx8z1p"
cd backend
./mvnw spring-boot:run
```

如果成功：

```text id="h5q9w2"
Flyway
Successfully applied migration
```

然后：

```text id="g0j7x2"
Tomcat started on port 8080
```

---

# 21. 检查 RDS

连接：

```bash id="k8v0q2"
psql \
-h YOUR_RDS_ENDPOINT \
-U app_admin \
-d enterprise_ai \
-p 5432
```

查看：

```sql id="j4s9tc"
\dt
```

应该看到：

```text id="z1y5n6"
users
flyway_schema_history
```

这意味着：

```text
Spring Boot
     ↓
Flyway
     ↓
RDS PostgreSQL
```

已经成功。

---

# 22. 再测试 API

运行：

```bash id="n4g3f7"
curl -X POST http://localhost:8080/api/users \
-H "Content-Type: application/json" \
-d '{"name":"AWS User","email":"aws@example.com"}'
```

然后：

```bash id="z9g5s2"
curl http://localhost:8080/api/users
```

如果返回：

```json id="k3c9z2"
[
  {
    "id": 1,
    "name": "AWS User",
    "email": "aws@example.com"
  }
]
```

说明：

**你的 Java API 已经真正使用 AWS RDS。**

---

# 23. 今天的架构

昨天：

```text id="2j4l6f"
React
  ↓
Spring Boot
  ↓
PostgreSQL Docker
```

今天：

```text id="4u3t9r"
React
  ↓
Spring Boot
  │
  │ Internet
  ▼
AWS VPC
  │
  ▼
RDS PostgreSQL
```

---

# 24. 但是生产环境不是这样

今天的：

```text id="q9h2h1"
Codespaces
      ↓
Internet
      ↓
Public RDS
```

只是**开发阶段临时方案**。

最终企业架构：

```text id="7r0m2d"
Internet
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
   │ Private Network
   ▼
RDS PostgreSQL
```

即：

```text id="g6n8e1"
RDS
↓
Private Subnet
↓
No Public Access
```

Day 7 我们部署 ECS 后，会把 RDS 改成真正的 Private Architecture。

---

# 25. 今天非常重要：关闭 RDS

如果你今天做完实验，**不要让 RDS 长期开着**。

如果只是学习：

```text
RDS
 ↓
Actions
 ↓
Stop temporarily
```

但需要注意，RDS 是否支持停止以及停止后的自动重启规则取决于实例类型/配置；如果只是为了避免持续成本，也可以在完成当天实验后删除开发数据库。

**删除前先确认不需要数据。**

---

# 26. Day 5 检查表

```text id="7m1t2r"
[ ] VPC 创建成功
[ ] 2 Availability Zones
[ ] Public Subnets
[ ] Private Subnets
[ ] RDS Security Group
[ ] 5432 未开放给全世界
[ ] RDS PostgreSQL
[ ] RDS Available
[ ] RDS Endpoint
[ ] psql 可以连接
[ ] enterprise_ai 数据库
[ ] Flyway 成功
[ ] users 表成功
[ ] Spring Boot → RDS 成功
[ ] React → Spring Boot → RDS 成功
```

---

# Day 5 最终成果

你现在已经有：

```text
              GitHub Codespaces
                     │
                     │
              Spring Boot
                     │
                     ▼
              ┌─────────────┐
              │    AWS VPC  │
              │             │
              │     RDS     │
              │ PostgreSQL  │
              └─────────────┘
```

下一步 **Day 6** 是非常关键的一天：

```text
Spring Boot
     ↓
Docker
     ↓
Docker Image
     ↓
Amazon ECR
```

然后我们把你的 Java 应用从“Codespaces 里运行”，变成**AWS 可以正式部署的 Docker Container**。
