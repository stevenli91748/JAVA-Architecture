# Day 22：OpenTelemetry + Distributed Tracing

今天把 Day 21 的 **Logs + Metrics** 升级成真正的 **Distributed Tracing**。

目标是：用户点击一次 AI Chat 后，你可以看到完整链路：

```text
React
  ↓
CloudFront
  ↓
ALB
  ↓
Spring Boot
  ↓
RAG
  ├── Embedding
  ├── pgvector
  └── Retrieval
  ↓
Bedrock
  ↓
Response
```

并且每一层都有：

```text
Trace ID
Span ID
Duration
Error
```

---

# 1. 为什么 Day 22 很重要

假设用户说：

> AI 回复很慢。

Day 21 只能看到：

```text
ALB latency = 3.2s
```

但不知道为什么。

Day 22 可以看到：

```text
Total Request                 3.20s
│
├── Spring Controller          3.15s
│
├── RAG                        1.10s
│   ├── Embedding              0.20s
│   ├── pgvector search        0.15s
│   └── Context building       0.10s
│
└── Bedrock                    2.00s
```

马上知道：

**真正慢的是 LLM，不是 RDS。**

---

# 2. 今天技术栈

今天使用：

```text
OpenTelemetry
       ↓
OTel Java Agent
       ↓
Spring Boot
       ↓
OTLP
       ↓
AWS X-Ray / CloudWatch
```

AWS 当前推荐的可观测性路线之一是 OpenTelemetry + AWS X-Ray/CloudWatch。

---

# 3. 最终架构

```text
                         User
                           │
                           ▼
                        React
                           │
                           ▼
                          ALB
                           │
                           ▼
                    Spring Boot
                           │
                    Trace Context
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
         RAG              RDS            Bedrock
          │
     ┌────┼────┐
     ▼    ▼    ▼
Embedding Search Context
          │
          ▼
      OpenTelemetry
          │
          ▼
       Collector
          │
          ▼
      AWS X-Ray
          │
          ▼
      CloudWatch
```

---

# 4. OpenTelemetry 是什么

OpenTelemetry，简称：

```text
OTel
```

负责：

```text
Traces
Metrics
Logs
```

今天重点：

```text
Trace
```

核心概念：

```text
Trace
 ├── Span
 ├── Span
 ├── Span
 └── Span
```

---

# 5. Trace 和 Span

例如：

```text
Trace ID
abc123
```

一次完整请求：

```text
Trace
│
├── HTTP POST /api/chat
│
├── RAG
│
├── Vector Search
│
└── Bedrock
```

每一个：

```text
Span
```

都有：

```text
start time
end time
duration
attributes
status
```

---

# 6. 今天最简单的方法：Java Agent

不要一开始大量修改 Spring Boot 代码。

使用：

```text
OpenTelemetry Java Agent
```

优点：

```text
不需要大量修改 Java Code
自动 instrumentation
Spring MVC
HTTP Client
JDBC
Redis
等等
```

---

# 7. 下载 OTel Java Agent

在 Dockerfile 中下载 Agent。

例如：

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar \
    /otel/opentelemetry-javaagent.jar

COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT [
  "java",
  "-javaagent:/otel/opentelemetry-javaagent.jar",
  "-jar",
  "app.jar"
]
```

不过生产环境**不要长期依赖 `latest` URL**。

应该固定版本：

```text
OpenTelemetry Java Agent 2.x
```

例如：

```dockerfile
ARG OTEL_AGENT_VERSION=2.x.x
```

然后固定下载 URL。

这样 CI/CD 可以做到：

```text
同一个 Git Commit
=
同一个 OTel Agent
```

---

# 8. OTel Agent 自动做什么

Spring Boot：

```text
Controller
 ↓
Service
 ↓
JDBC
```

Agent 可以自动产生：

```text
HTTP Span
JDBC Span
HTTP Client Span
```

因此你不需要：

```java
startTrace();
endTrace();
```

到处写代码。

---

# 9. 环境变量

ECS Task Definition：

```text
OTEL_SERVICE_NAME
OTEL_EXPORTER_OTLP_ENDPOINT
OTEL_RESOURCE_ATTRIBUTES
```

例如：

```text
OTEL_SERVICE_NAME=java-ai-backend
```

环境：

```text
OTEL_RESOURCE_ATTRIBUTES=
deployment.environment=dev
```

---

# 10. OTLP

OpenTelemetry 使用：

```text
OTLP
```

传输 telemetry。

一般：

```text
Application
    ↓
OTLP
    ↓
Collector
    ↓
Backend
```

---

# 11. 为什么使用 Collector

不要让：

```text
Spring Boot
 ↓
AWS X-Ray
```

完全绑定。

更好的架构：

```text
Spring Boot
      ↓
OTel Collector
      ↓
┌─────┴─────┐
▼           ▼
X-Ray     CloudWatch
```

以后还可以：

```text
Grafana
Datadog
New Relic
```

所以 Collector 是一个很好的抽象层。

---

# 12. ECS 部署 Collector

推荐：

```text
ECS Task
├── Java Container
└── OTel Collector
```

叫：

```text
Sidecar Container
```

架构：

```text
┌────────────────────────────┐
│ ECS Task                   │
│                            │
│ ┌──────────────────────┐   │
│ │ Spring Boot          │   │
│ │ :8080                │   │
│ └──────────┬───────────┘   │
│            │ OTLP           │
│            ▼                │
│ ┌──────────────────────┐   │
│ │ OTel Collector       │   │
│ │ :4317 / :4318        │   │
│ └──────────┬───────────┘   │
└────────────┼────────────────┘
             ▼
          AWS X-Ray
```

这是今天推荐你实际实现的方式。

---

# 13. OTel Collector Image

可以使用官方 OpenTelemetry Collector Contrib Image。

例如：

```text
otel/opentelemetry-collector-contrib
```

生产环境不要：

```text
latest
```

固定版本。

---

# 14. Collector Config

创建：

```text
otel-collector-config.yaml
```

基础版本：

```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

      http:
        endpoint: 0.0.0.0:4318

processors:
  batch:

exporters:
  awsxray:

service:
  pipelines:
    traces:
      receivers:
        - otlp
      processors:
        - batch
      exporters:
        - awsxray
```

最终：

```text
Spring Boot
 ↓
OTLP
 ↓
Collector
 ↓
AWS X-Ray
```

---

# 15. X-Ray IAM Role

Collector 需要：

```text
xray:PutTraceSegments
xray:PutTelemetryRecords
```

不要：

```text
AdministratorAccess
```

最小权限。

---

# 16. ECS Task Role

把：

```text
X-Ray permissions
```

添加给 Collector 所使用的 Task Role。

如果整个 ECS Task 使用同一个 Task Role：

```text
Spring Boot
+
Collector
```

都可以获得该权限。

更严格的场景可以进一步拆分 workload。

---

# 17. ECS Task Definition

结构：

```json
{
  "containerDefinitions": [
    {
      "name": "backend",
      "image": "ECR/backend:COMMIT_SHA"
    },
    {
      "name": "otel-collector",
      "image": "otel/opentelemetry-collector-contrib:VERSION"
    }
  ]
}
```

---

# 18. Spring Boot Container

添加：

```text
OTEL_SERVICE_NAME
```

```text
OTEL_EXPORTER_OTLP_ENDPOINT
```

例如：

```text
OTEL_SERVICE_NAME=java-ai-backend
```

以及：

```text
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
```

因为：

```text
Java Container
     │
localhost:4317
     │
     ▼
OTel Collector
```

---

# 19. ECS Task 网络

Sidecar：

```text
Spring Boot
     │
localhost
     │
     ▼
Collector
```

不需要经过：

```text
ALB
NAT
Internet
```

这样更简单，也更安全。

---

# 20. 启动顺序

Collector：

```text
启动
 ↓
等待 OTLP
```

Spring Boot：

```text
启动
 ↓
发送 Trace
```

所以 Collector 应该有：

```text
Health Check
```

---

# 21. Collector Health Check

Collector 可以提供：

```text
:13133
```

Health endpoint。

ECS：

```text
Health Check
 ↓
Collector :13133
```

这样 Collector 挂掉时可以被发现。

---

# 22. 第一次 Trace

用户：

```text
POST /api/chat
```

Spring：

```text
HTTP POST /api/chat
```

产生：

```text
Span 1
```

然后：

```text
RAG
```

产生：

```text
Span 2
```

然后：

```text
pgvector
```

产生：

```text
Span 3
```

然后：

```text
Bedrock
```

产生：

```text
Span 4
```

---

# 23. 最终 Trace

你希望看到：

```text
Trace: 8f42ab...

POST /api/chat                 3.2s
│
├── RAG                         1.0s
│   ├── Embedding               0.2s
│   ├── pgvector search         0.3s
│   └── Context building        0.5s
│
└── Bedrock                     2.0s
```

这就是 Day 22 的核心成果。

---

# 24. RAG 自定义 Span

Java Agent 可以自动监控很多框架，但你的 RAG 业务逻辑最好自己增加业务 Span。

例如：

```java
Tracer tracer =
    openTelemetry.getTracer("rag-service");
```

创建：

```java
Span span =
    tracer.spanBuilder("rag.search")
          .startSpan();

try {
    return vectorSearch(query);
} finally {
    span.end();
}
```

---

# 25. AI Span

类似：

```java
Span span =
    tracer.spanBuilder("llm.request")
          .startSpan();

try {
    return chatClient.call(prompt);
} finally {
    span.end();
}
```

这样：

```text
Trace
 ├── rag.search
 └── llm.request
```

---

# 26. Span Attributes

不要记录 Prompt。

可以记录：

```java
span.setAttribute(
    "ai.model",
    modelName
);

span.setAttribute(
    "ai.input_tokens",
    inputTokens
);

span.setAttribute(
    "ai.output_tokens",
    outputTokens
);

span.setAttribute(
    "ai.latency_ms",
    latency
);
```

---

# 27. 不要记录这些东西

不要：

```java
span.setAttribute(
    "ai.prompt",
    prompt
);
```

不要：

```java
span.setAttribute(
    "db.password",
    password
);
```

不要：

```java
span.setAttribute(
    "authorization",
    token
);
```

Trace 数据也属于生产数据。

---

# 28. Trace Context

现在：

```text
React
 ↓
ALB
 ↓
Spring Boot
```

OpenTelemetry 使用：

```text
W3C Trace Context
```

例如：

```text
traceparent:
00-abc123-def456-01
```

Spring Boot 可以继续传播：

```text
Spring
 ↓
RAG
 ↓
HTTP Client
```

于是整个请求保持：

```text
Trace ID = abc123
```

---

# 29. Trace ID + Logs

这是非常强大的组合。

日志：

```json
{
  "traceId": "abc123",
  "message": "Bedrock request completed"
}
```

Trace：

```text
abc123
```

你可以：

```text
Trace
 ↓
找到慢 Span
 ↓
点击相关 Logs
```

最终实现：

```text
Trace
+
Logs
```

关联分析。

---

# 30. Day 21 + Day 22

现在：

```text
                    Observability
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
        Logs          Metrics         Traces
          │              │              │
          ▼              ▼              ▼
     CloudWatch      CloudWatch      X-Ray
          │              │              │
          └──────────────┼──────────────┘
                         ▼
                       Alarm
                         │
                         ▼
                        SNS
```

这已经是完整的 Observability 基础。

---

# 31. ALB → ECS Trace

有一个现实问题：

ALB 本身不是你 Java 应用里的 Span。

但它会产生：

```text
ALB Access Logs
ALB Metrics
```

而 Spring Boot 产生：

```text
Application Trace
```

两者可以通过：

```text
Request ID
Trace ID
时间
```

辅助关联。

---

# 32. Database Trace

Java Agent 对 JDBC 有自动 instrumentation。

例如：

```text
Spring Boot
 ↓
JDBC
 ↓
PostgreSQL
```

可以看到：

```text
SELECT
INSERT
UPDATE
```

但生产环境不要把 SQL 参数中可能存在的敏感数据全部采集。

重点观察：

```text
SQL Duration
Database Call
Error
```

---

# 33. Bedrock Trace

Bedrock 调用属于关键业务路径。

你至少需要：

```text
Span:
llm.request

Attributes:
model
latency
status
token usage
```

例如：

```text
llm.request
Duration: 2.13s
Model: xxx
Status: OK
```

---

# 34. RAG Trace

完整：

```text
/api/chat
   │
   ├── auth
   │
   ├── rag.retrieve
   │      │
   │      ├── embedding
   │      └── vector.search
   │
   ├── prompt.build
   │
   └── llm.request
```

这会非常适合你后面开发 AI Agent。

---

# 35. Agent Trace

未来 Day 30+：

```text
Agent
 │
 ├── Planning
 │
 ├── Tool Call
 │      └── Search
 │
 ├── RAG
 │
 ├── Database
 │
 └── LLM
```

每个 Tool 都可以成为：

```text
Span
```

最终：

```text
Agent Trace
```

非常清晰。

---

# 36. Terraform 增加 Monitoring Module

目录：

```text
modules/
└── observability/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

管理：

```text
CloudWatch
SNS
Alarms
```

另外：

```text
modules/
└── ecs/
```

增加：

```text
OTel Collector
```

---

# 37. Dockerfile 最终结构

你的 Java Dockerfile 最终：

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY pom.xml .

RUN mvn dependency:go-offline -B

COPY src ./src

RUN mvn clean package -DskipTests


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build \
    /app/target/*.jar \
    app.jar

COPY opentelemetry-javaagent.jar \
    /otel/opentelemetry-javaagent.jar

EXPOSE 8080

ENTRYPOINT [
  "java",
  "-javaagent:/otel/opentelemetry-javaagent.jar",
  "-jar",
  "app.jar"
]
```

---

# 38. 生产环境版本固定

不要：

```dockerfile
ADD .../releases/latest/...
```

推荐：

```dockerfile
ARG OTEL_AGENT_VERSION=2.x.x
```

然后：

```text
Git
 ↓
Docker
 ↓
固定 OTel Agent
```

这样生产部署可以复现。

---

# 39. ECS Task 最终结构

```text
ECS Task
│
├── backend
│   │
│   ├── Spring Boot
│   ├── Spring AI
│   ├── RAG
│   └── OTel Java Agent
│
└── otel-collector
    │
    ├── OTLP :4317
    ├── Batch
    └── AWS X-Ray Exporter
```

---

# 40. 今天测试方法

部署以后：

```text
POST /api/chat
```

发送：

```json
{
  "message": "What is AWS ECS?"
}
```

然后进入：

```text
AWS Console
 → X-Ray
 → Traces
```

找到：

```text
/api/chat
```

应该看到 Trace。

---

# 41. 如果看不到 Trace

按这个顺序检查：

```text
1. OTel Java Agent 是否启动
        ↓
2. OTEL_SERVICE_NAME
        ↓
3. OTLP endpoint
        ↓
4. Collector 是否运行
        ↓
5. Collector :4317
        ↓
6. Task IAM Role
        ↓
7. X-Ray permissions
        ↓
8. Collector logs
```

---

# 42. Collector Logs

CloudWatch：

```text
/ecs/java-ai-platform-dev
```

查：

```text
error
export
otlp
xray
```

如果看到：

```text
AccessDenied
```

优先检查：

```text
Task Role
```

而不是修改 Java。

---

# 43. Day 22 最重要的概念

记住这张图：

```text
                   Trace
                     │
          ┌──────────┴──────────┐
          │                     │
        Span                  Span
          │                     │
        RAG                  Bedrock
          │
      ┌───┴────┐
      ▼        ▼
   Vector    Embedding
   Search
```

**Trace = 一次完整请求**

**Span = 请求中的一个步骤**

---

# 44. Day 22 检查清单

```text
[ ] OpenTelemetry
[ ] OTel Java Agent
[ ] OTLP
[ ] OTel Collector
[ ] ECS Sidecar
[ ] X-Ray
[ ] Trace ID
[ ] Span ID
[ ] Spring HTTP Trace
[ ] JDBC Trace
[ ] RAG Span
[ ] LLM Span
[ ] AI latency
[ ] Token metrics
[ ] Trace + Logs
[ ] 不记录 Prompt
[ ] 不记录 Secret
[ ] IAM 最小权限
```

---

# Day 22 完成后的架构

```text
                         User
                           │
                           ▼
                        React
                           │
                           ▼
                      CloudFront
                           │
                           ▼
                          ALB
                           │
                           ▼
                 ┌───────────────────┐
                 │    ECS Task       │
                 │                   │
                 │ Spring Boot       │
                 │   │               │
                 │   ├── RAG         │
                 │   ├── PostgreSQL  │
                 │   └── Bedrock     │
                 │                   │
                 │ OTel Java Agent   │
                 │        │          │
                 │        ▼          │
                 │ OTel Collector    │
                 └────────┬──────────┘
                          │
                         OTLP
                          │
                          ▼
                       AWS X-Ray
                          │
                    ┌─────┴─────┐
                    ▼           ▼
               CloudWatch    Trace
                  Logs
                    │
                    ▼
                  Alarm
                    │
                    ▼
                   SNS
```

### Day 23

下一步建议做 **AWS Secrets Manager + IAM + Spring Boot 安全配置**，把：

```text
DB Password
JWT Secret
API Credentials
Bedrock 配置
第三方 API Key
```

全部从代码、Docker、GitHub、ECS Environment 中清理出去，建立：

```text
Secrets Manager
        ↓
ECS Task
        ↓
Spring Boot
```

并进一步实现 **DEV / TEST / PROD 三套完全隔离的 Secret 管理**。
