# Day 21：AWS Observability 可观测性

今天把系统从：

```text
能开发
 ↓
能测试
 ↓
能部署
```

升级到：

```text
能监控
 ↓
能报警
 ↓
能定位故障
```

今天重点：

```text
Spring Boot Actuator
CloudWatch Logs
CloudWatch Metrics
ECS Metrics
ALB Metrics
RDS Metrics
CloudWatch Alarms
```

---

# 1. 今天最终架构

```text
                         AWS
                          │
                ┌─────────┼─────────┐
                │         │         │
               ALB       ECS       RDS
                │         │         │
                │         │         │
                ▼         ▼         ▼
             Metrics    Metrics   Metrics
                │         │         │
                └────┬────┴────┬────┘
                     │         │
                     ▼         ▼
                CloudWatch   CloudWatch
                   Logs       Alarms
                     │           │
                     └─────┬─────┘
                           ▼
                       SNS / Email
```

Java：

```text
Spring Boot
    │
    ├── /actuator/health
    ├── /actuator/metrics
    └── Logs
          │
          ▼
     CloudWatch
```

---

# 2. 为什么 Observability 很重要

假设用户告诉你：

> AI Chat 很慢。

以前你只能：

```text
看代码
看服务器
猜原因
```

现在可以：

```text
ALB
 ↓
Latency = 3.8 sec

ECS
 ↓
CPU = 92%

RDS
 ↓
CPU = 35%

Bedrock
 ↓
Latency = 2.9 sec
```

马上知道：

```text
ECS CPU
```

还是：

```text
RDS
```

还是：

```text
LLM
```

---

# 3. 三个核心概念

## Metrics

数字：

```text
CPU = 72%
Memory = 68%
Requests = 1200/min
Latency = 250ms
5xx = 10
```

## Logs

事件：

```text
ERROR
Database connection timeout
```

## Traces

请求路径：

```text
React
 ↓
ALB
 ↓
Spring Boot
 ↓
RAG
 ↓
RDS
 ↓
Bedrock
```

今天先做：

**Metrics + Logs**

Trace 后面再做 OpenTelemetry。

---

# 4. Spring Boot Actuator

进入：

```text
backend/pom.xml
```

添加：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

如果使用 Spring Boot 3.x，这就是标准方式。

---

# 5. application.yml

```yaml
management:
  endpoints:
    web:
      exposure:
        include:
          - health
          - info
          - metrics

  endpoint:
    health:
      probes:
        enabled: true
```

不要直接：

```yaml
include: "*"
```

生产环境不建议把所有 Actuator Endpoint 暴露出去。

---

# 6. Health Endpoint

现在：

```text
GET /actuator/health
```

返回：

```json
{
  "status": "UP"
}
```

ALB 可以使用：

```text
/actuator/health
```

作为 Health Check。

---

# 7. ECS Health Check

架构：

```text
ALB
 │
 │ GET /actuator/health
 ▼
ECS
 │
 ▼
Spring Boot
 │
 ▼
200 OK
```

如果：

```text
500
```

ALB：

```text
Unhealthy
```

然后 ECS 可以启动新的 Task。

---

# 8. 更好的 Health Check

生产环境建议区分：

```text
Liveness
Readiness
```

Spring Boot：

```text
/actuator/health/liveness
/actuator/health/readiness
```

配置：

```yaml
management:
  endpoint:
    health:
      probes:
        enabled: true
```

这样：

```text
Liveness
=
应用进程是否还活着

Readiness
=
应用是否准备好接收流量
```

---

# 9. ECS Health Check

ECS Task：

```text
Container
   │
   ▼
Health Check
   │
   ▼
/actuator/health
```

例如：

```json
{
  "command": [
    "CMD-SHELL",
    "curl -f http://localhost:8080/actuator/health || exit 1"
  ],
  "interval": 30,
  "timeout": 5,
  "retries": 3,
  "startPeriod": 60
}
```

如果你的 Runtime Image 没有 `curl`，不要直接照抄；可以改用 wget，或者主要依靠 ALB Target Group Health Check。

---

# 10. Spring Boot 日志

Java 应用：

```java
private static final Logger log =
        LoggerFactory.getLogger(ChatService.class);

log.info("Processing AI request");

log.error("AI request failed", exception);
```

不要：

```java
System.out.println(...)
```

企业项目统一使用：

```text
SLF4J
 +
Logback
```

Spring Boot 默认已经提供。

---

# 11. JSON Logging

AI 应用以后日志很多，推荐结构化 JSON。

例如：

```json
{
  "timestamp": "2026-08-20T18:30:00Z",
  "level": "INFO",
  "service": "java-ai-backend",
  "traceId": "abc123",
  "message": "AI request completed",
  "latencyMs": 842
}
```

这样 CloudWatch 查询会容易很多。

---

# 12. ECS → CloudWatch Logs

ECS Task Definition：

```text
Container
   │
   ▼
awslogs
   │
   ▼
CloudWatch Logs
```

配置：

```json
{
  "logConfiguration": {
    "logDriver": "awslogs",
    "options": {
      "awslogs-group": "/ecs/java-ai-platform-dev",
      "awslogs-region": "us-west-2",
      "awslogs-stream-prefix": "backend"
    }
  }
}
```

---

# 13. Terraform 创建 Log Group

Day 17/18 的 Terraform 中加入：

```hcl
resource "aws_cloudwatch_log_group" "backend" {

  name =
    "/ecs/${var.project_name}-${var.environment}"

  retention_in_days = 14

  tags = {
    Service = "backend"
  }
}
```

DEV：

```text
14 days
```

PROD：

```text
30–90 days
```

根据合规和成本要求决定。

---

# 14. ECS Task Role

注意：

```text
Task Execution Role
```

和：

```text
Task Role
```

不是一回事。

### Execution Role

ECS Agent 使用：

```text
ECR Pull
CloudWatch Logs
Secrets Manager
```

### Task Role

Java 应用本身使用：

```text
Bedrock
S3
Secrets Manager
```

---

# 15. Java AI 应用 Task Role

例如 Spring AI 调用 Bedrock：

```text
Spring Boot
   │
   ▼
AWS SDK
   │
   ▼
Task Role
   │
   ▼
Bedrock
```

**不要把 AWS Access Key 写进 application.yml。**

---

# 16. ECS Metrics

ECS/Fargate 常见指标：

```text
CPUUtilization
MemoryUtilization
RunningTaskCount
DesiredTaskCount
```

最重要：

```text
CPU
Memory
Running Tasks
```

---

# 17. ALB Metrics

重点关注：

```text
RequestCount
TargetResponseTime
HTTPCode_Target_2XX_Count
HTTPCode_Target_4XX_Count
HTTPCode_Target_5XX_Count
HealthyHostCount
UnHealthyHostCount
```

尤其：

```text
TargetResponseTime
HTTPCode_Target_5XX_Count
UnHealthyHostCount
```

---

# 18. RDS Metrics

重点：

```text
CPUUtilization
DatabaseConnections
FreeStorageSpace
FreeableMemory
ReadLatency
WriteLatency
```

如果 PostgreSQL：

```text
Connections
CPU
Memory
Storage
```

非常重要。

---

# 19. CloudWatch Alarm

现在开始真正做报警。

例如：

```text
ECS CPU > 80%
持续 5 分钟
        ↓
Alarm
```

---

# 20. Terraform ECS CPU Alarm

```hcl
resource "aws_cloudwatch_metric_alarm" "ecs_cpu_high" {

  alarm_name =
    "${var.project_name}-${var.environment}-ecs-cpu-high"

  comparison_operator =
    "GreaterThanThreshold"

  evaluation_periods = 2

  metric_name = "CPUUtilization"

  namespace = "AWS/ECS"

  period = 300

  statistic = "Average"

  threshold = 80

  dimensions = {
    ClusterName = var.cluster_name
    ServiceName = var.service_name
  }

  alarm_description =
    "ECS CPU is above 80%"
}
```

---

# 21. ECS Memory Alarm

```hcl
resource "aws_cloudwatch_metric_alarm" "ecs_memory_high" {

  alarm_name =
    "${var.project_name}-${var.environment}-ecs-memory-high"

  comparison_operator =
    "GreaterThanThreshold"

  evaluation_periods = 2

  metric_name = "MemoryUtilization"

  namespace = "AWS/ECS"

  period = 300

  statistic = "Average"

  threshold = 80

  dimensions = {
    ClusterName = var.cluster_name
    ServiceName = var.service_name
  }
}
```

---

# 22. ALB 5xx Alarm

```hcl
resource "aws_cloudwatch_metric_alarm" "alb_5xx" {

  alarm_name =
    "${var.project_name}-${var.environment}-alb-5xx"

  comparison_operator =
    "GreaterThanThreshold"

  evaluation_periods = 2

  metric_name =
    "HTTPCode_Target_5XX_Count"

  namespace = "AWS/ApplicationELB"

  period = 300

  statistic = "Sum"

  threshold = 10

  dimensions = {
    LoadBalancer =
      var.alb_arn_suffix
  }
}
```

---

# 23. RDS CPU Alarm

```hcl
resource "aws_cloudwatch_metric_alarm" "rds_cpu" {

  alarm_name =
    "${var.project_name}-${var.environment}-rds-cpu"

  comparison_operator =
    "GreaterThanThreshold"

  evaluation_periods = 2

  metric_name =
    "CPUUtilization"

  namespace = "AWS/RDS"

  period = 300

  statistic = "Average"

  threshold = 80

  dimensions = {
    DBInstanceIdentifier =
      var.db_instance_identifier
  }
}
```

---

# 24. Alarm 不等于通知

现在：

```text
CloudWatch Alarm
```

只知道：

```text
ALARM
```

还需要：

```text
SNS
 ↓
Email
```

---

# 25. SNS

Terraform：

```hcl
resource "aws_sns_topic" "alerts" {

  name =
    "${var.project_name}-${var.environment}-alerts"
}
```

订阅：

```hcl
resource "aws_sns_topic_subscription" "email" {

  topic_arn =
    aws_sns_topic.alerts.arn

  protocol = "email"

  endpoint = var.alert_email
}
```

然后你的邮箱会收到确认邮件。

必须点击：

```text
Confirm subscription
```

---

# 26. Alarm → SNS

```hcl
alarm_actions = [
  aws_sns_topic.alerts.arn
]
```

完整链路：

```text
ECS CPU
  >
80%
  ↓
CloudWatch Alarm
  ↓
SNS
  ↓
Email
```

---

# 27. 推荐第一批 Alarm

你的 Java + AI 应用建议：

```text
ECS CPU > 80%
ECS Memory > 80%
ECS Running Tasks < Desired
ALB 5xx > 10
ALB UnhealthyHostCount > 0
ALB Latency > 2 sec
RDS CPU > 80%
RDS FreeStorageSpace low
RDS Connections high
```

---

# 28. AI 应用额外监控

你的系统不是普通 Java 应用。

还需要关注：

```text
LLM Latency
LLM Errors
Token Usage
RAG Latency
Embedding Latency
Vector Search Latency
```

例如：

```text
User
 ↓
RAG
 ↓
Vector Search = 120ms
 ↓
Bedrock = 2.4s
 ↓
Total = 2.7s
```

---

# 29. 自定义 Metrics

Spring Boot 可以产生：

```text
ai.request.count
ai.request.latency
rag.search.latency
llm.request.latency
llm.error.count
```

推荐使用：

```text
Micrometer
```

例如概念上：

```java
Timer.builder("ai.request.latency")
    .register(meterRegistry);
```

以后可以把这些指标送到 CloudWatch / OpenTelemetry。

---

# 30. 不要把 Prompt 放进普通日志

例如不要：

```text
log.info("Prompt={}", userPrompt);
```

因为用户 Prompt 可能包含：

```text
个人信息
公司机密
Token
敏感数据
```

应该记录：

```text
requestId
userId hash
model
latency
token count
status
```

而不是完整 Prompt。

---

# 31. CloudWatch Logs Insights

进入 CloudWatch：

```text
Logs Insights
```

例如搜索：

```text
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 50
```

可以快速找到：

```text
ERROR
```

---

# 32. 查询慢请求

如果 JSON 日志中有：

```text
latencyMs
```

可以：

```text
fields @timestamp, latencyMs, message
| filter latencyMs > 2000
| sort latencyMs desc
| limit 50
```

马上找到：

```text
超过 2 秒的请求
```

---

# 33. 推荐日志字段

统一：

```json
{
  "timestamp": "...",
  "level": "INFO",
  "service": "backend",
  "environment": "dev",
  "requestId": "...",
  "traceId": "...",
  "endpoint": "/api/chat",
  "status": 200,
  "latencyMs": 1280
}
```

AI：

```json
{
  "model": "your-bedrock-model",
  "inputTokens": 1200,
  "outputTokens": 500,
  "llmLatencyMs": 980
}
```

---

# 34. Request ID

非常重要。

请求：

```text
Request ID:
abc-123
```

整个系统都使用：

```text
React
 ↓
ALB
 ↓
Spring Boot
 ↓
RAG
 ↓
Bedrock
```

都记录：

```text
abc-123
```

以后发生问题：

> 用户说刚才 AI 回复失败。

你可以搜索：

```text
abc-123
```

直接定位。

---

# 35. Day 21 暂时不要做 Distributed Tracing

今天先：

```text
Logs
Metrics
Alarms
```

Day 22 再做：

```text
OpenTelemetry
 ↓
Trace
 ↓
React
 ↓
ALB
 ↓
Spring Boot
 ↓
RAG
 ↓
Bedrock
```

这样学习路径比较清楚。

---

# 36. Day 21 Terraform 结构

建议增加：

```text
infrastructure/terraform/
│
├── modules/
│   ├── monitoring/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│
└── environments/
    └── dev/
        └── main.tf
```

Monitoring Module 管：

```text
CloudWatch Log Group
CloudWatch Alarm
SNS
```

---

# 37. Terraform Module 调用

```hcl
module "monitoring" {

  source = "../../modules/monitoring"

  project_name = "java-ai-platform"

  environment = "dev"

  cluster_name =
    module.ecs.cluster_name

  service_name =
    module.ecs.service_name

  alert_email =
    var.alert_email
}
```

---

# 38. 运行 Terraform

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

确认：

```text
CloudWatch Log Group
CloudWatch Alarms
SNS Topic
SNS Subscription
```

再：

```bash
terraform apply
```

---

# 39. 第一个报警测试

不要等真的出故障。

先测试：

```text
CloudWatch Alarm
```

选择：

```text
Actions
→ Set state
→ Alarm
```

然后：

```text
SNS
 ↓
Email
```

应该收到测试报警。

---

# 40. Day 21 推荐 Dashboard

创建：

```text
java-ai-platform-dev
```

Dashboard：

```text
┌────────────────────────────────────┐
│ ECS CPU          42%               │
│ ECS Memory       55%               │
├────────────────────────────────────┤
│ ALB Requests     1,240             │
│ ALB Latency      240ms             │
│ ALB 5xx          0                 │
├────────────────────────────────────┤
│ RDS CPU          31%               │
│ RDS Connections  18                │
├────────────────────────────────────┤
│ AI Latency       1.8s              │
│ RAG Latency      120ms             │
│ LLM Errors       0                 │
└────────────────────────────────────┘
```

---

# 41. 真正的企业故障排查流程

以后用户说：

> API 很慢。

第一步：

```text
ALB
 ↓
TargetResponseTime
```

如果高：

```text
↓
ECS
```

检查：

```text
CPU
Memory
Tasks
```

如果正常：

```text
↓
RDS
```

检查：

```text
CPU
Connections
Latency
```

如果 RDS 正常：

```text
↓
RAG
```

再检查：

```text
Vector Search
Embedding
```

最后：

```text
↓
Bedrock
```

检查：

```text
LLM latency
errors
throttling
```

这就是 Observability 的价值。

---

# 42. Day 21 最终架构

```text
                         User
                           │
                           ▼
                       CloudFront
                           │
                           ▼
                           ALB
                           │
                           ▼
                     Private ECS
                           │
            ┌──────────────┼──────────────┐
            │              │              │
            ▼              ▼              ▼
           RDS            RAG           Bedrock
            │              │              │
            └──────────────┼──────────────┘
                           │
                           ▼
                      Application
                       Telemetry
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
          CloudWatch     Metrics       Logs
              │
              ▼
           Alarms
              │
              ▼
             SNS
              │
              ▼
            Email
```

---

# 43. Day 21 检查清单

```text
[ ] Spring Boot Actuator
[ ] /actuator/health
[ ] Liveness
[ ] Readiness
[ ] ECS Health Check
[ ] ALB Health Check
[ ] CloudWatch Log Group
[ ] ECS Logs
[ ] ECS CPU Alarm
[ ] ECS Memory Alarm
[ ] ALB 5xx Alarm
[ ] ALB Latency Alarm
[ ] RDS CPU Alarm
[ ] RDS Storage Alarm
[ ] SNS
[ ] Email Alert
[ ] CloudWatch Dashboard
[ ] Logs Insights
[ ] Request ID
[ ] AI Latency Metrics
[ ] 不记录完整 Prompt/Secret
```

---

# Day 1 → Day 21

现在你的系统已经具备：

```text
① 开发
Java + React + AI
        ↓
② Infrastructure
Terraform
        ↓
③ Network
VPC + ALB + Private ECS + RDS
        ↓
④ Backend CI/CD
Maven → Docker → ECR → ECS
        ↓
⑤ Frontend CI/CD
npm → S3 → CloudFront
        ↓
⑥ Security
OIDC + IAM + Secrets
        ↓
⑦ Observability
CloudWatch + Metrics + Logs + Alarms
```

**Day 22** 最值得继续做的是 **OpenTelemetry + Distributed Tracing**，把一次 AI 请求完整串起来：

```text
React
 ↓
ALB
 ↓
Spring Boot
 ↓
Controller
 ↓
RAG
 ↓
Embedding
 ↓
pgvector
 ↓
Bedrock
 ↓
Response
```

这样你就能知道一个 AI 请求到底慢在哪一层，而不仅仅是知道“ECS CPU 很高”。
