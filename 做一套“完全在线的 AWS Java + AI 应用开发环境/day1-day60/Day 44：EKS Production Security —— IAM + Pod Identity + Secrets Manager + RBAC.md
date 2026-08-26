# Day 44：EKS Production Security —— IAM + Pod Identity + Secrets Manager + RBAC

Day 43 已经打通：

```text
Internet
 ↓
Route 53
 ↓
HTTPS / ACM
 ↓
ALB
 ↓
Ingress
 ↓
EKS
 ↓
Spring Boot
```

今天解决一个生产环境最重要的问题：

> **Pod 到底凭什么访问 Bedrock、S3、Secrets Manager、RDS？**

最终做到：

```text
Pod
 ↓
EKS Pod Identity
 ↓
IAM Role
 ↓
AWS Services
```

**绝不在 Java、Docker、GitHub、Kubernetes YAML 中保存 AWS Access Key。**

---

# 1. Day 44 最终安全架构

```text
                         Internet
                            │
                            ▼
                         AWS WAF
                            │
                            ▼
                           ALB
                            │
                            ▼
                        Ingress
                            │
                            ▼
                    ┌───────────────┐
                    │ EKS Namespace │
                    │    ai-prod     │
                    └───────┬───────┘
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
           AI API          MCP          Worker
              │
              ▼
       EKS Pod Identity
              │
              ▼
           IAM Role
       ┌──────┼─────────┐
       ▼      ▼         ▼
   Bedrock    S3    Secrets Manager
                         │
                         ▼
                     DB Secret
                         │
                         ▼
                    RDS PostgreSQL
```

---

# 2. 今天学习 10 个核心概念

```text
① IAM
② IAM Role
③ EKS Pod Identity
④ ServiceAccount
⑤ Secrets Manager
⑥ External Secrets
⑦ RBAC
⑧ NetworkPolicy
⑨ Security Group
⑩ Least Privilege
```

---

# 3. 第一原则：不要保存 AWS Access Key

错误架构：

```text
Java
 ↓
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
 ↓
Bedrock
```

更错误：

```text
application.yml
 ↓
AWS Secret
```

或者：

```text
GitHub
 ↓
AWS Secret Key
```

生产应该：

```text
Pod
 ↓
Pod Identity
 ↓
IAM Role
 ↓
AWS
```

---

# 4. IAM Role

IAM Role 可以理解为：

> **一个 AWS 身份权限集合。**

例如：

```text
AIAPIRole
```

允许：

```text
Bedrock
S3
Secrets Manager
```

---

# 5. 不同服务使用不同 Role

不要：

```text
所有 Pod
 ↓
同一个 IAM Role
 ↓
Administrator
```

应该：

```text
AI API
 ↓
AIAPIRole

MCP
 ↓
MCPRole

Worker
 ↓
WorkerRole
```

---

# 6. AI API Role

例如：

```text
AIAPIRole
│
├── Bedrock InvokeModel
├── S3 GetObject
└── Secrets Manager GetSecretValue
```

不要给：

```text
EC2 *
RDS *
IAM *
```

---

# 7. Worker Role

Worker：

```text
S3
 ↓
Worker
```

所以：

```text
WorkerRole
│
├── S3 GetObject
├── SQS ReceiveMessage
├── SQS DeleteMessage
└── Bedrock InvokeModel
```

如果 Worker 不需要修改 S3：

```text
不要给 PutObject
```

---

# 8. MCP Role

MCP Server 最危险的地方是：

```text
Tool Calling
```

所以：

```text
MCPRole
```

必须非常严格。

例如：

```text
允许：
S3 Read

不允许：
S3 Delete
```

除非业务真的需要。

---

# 9. EKS Pod Identity

今天重点。

传统 Kubernetes：

```text
Pod
 ↓
AWS credentials
```

不推荐。

EKS Pod Identity：

```text
Pod
 ↓
ServiceAccount
 ↓
EKS Pod Identity
 ↓
IAM Role
 ↓
AWS
```

---

# 10. ServiceAccount

创建：

```yaml id="b05c8b"
apiVersion: v1
kind: ServiceAccount

metadata:
  name: ai-api
  namespace: ai-prod
```

然后：

```text id="x8dhgi"
Deployment
 ↓
serviceAccountName: ai-api
```

---

# 11. 为什么 ServiceAccount 很重要？

因为：

```text id="7h0b9d"
Pod
```

本身不应该直接决定：

```text id="43a5ed"
你可以访问什么 AWS API
```

而是：

```text id="r6d0j7"
ServiceAccount
 ↓
IAM Role
```

决定。

---

# 12. AI API Identity

最终：

```text id="v64y9r"
ai-api Pod
     │
     ▼
ServiceAccount
     │
     ▼
AIAPIRole
     │
 ┌───┼────────┐
 ▼   ▼        ▼
S3 Bedrock Secrets
```

---

# 13. Bedrock 权限

例如只允许：

```text id="r0d6yo"
bedrock:InvokeModel
```

不要一开始：

```text id="h8xv0f"
bedrock:*
```

如果使用的 Bedrock API 需要其他 Action，再按实际 API 补充。

---

# 14. S3 权限

例如：

```text id="o0w4qj"
arn:aws:s3:::company-ai-documents-prod/documents/*
```

只允许：

```text id="j0x5fi"
s3:GetObject
```

不要：

```text id="v9g1q2"
s3:*
```

---

# 15. Secrets Manager

例如：

```text id="5r4p7q"
arn:aws:secretsmanager:us-west-2:123456789012:secret:prod/ai/*
```

允许：

```text id="d6i8th"
secretsmanager:GetSecretValue
```

---

# 16. Secrets Manager 保存什么？

例如：

```text id="3x4m9q"
prod/ai/database
```

内容：

```json id="1q9f3a"
{
  "username": "aiapp",
  "password": "********",
  "host": "prod-db.xxxxx.rds.amazonaws.com",
  "port": 5432,
  "database": "ai_platform"
}
```

---

# 17. Secret 不进入 Git

错误：

```text id="6p9n2h"
values/prod.yaml
```

不要：

```yaml id="hbr5h0"
dbPassword: "mypassword"
```

正确：

```text id="j7l3me"
AWS Secrets Manager
        │
        ▼
External Secrets
        │
        ▼
Kubernetes Secret
```

---

# 18. External Secrets Operator

架构：

```text id="a8n5qs"
AWS Secrets Manager
          │
          ▼
External Secrets Operator
          │
          ▼
Kubernetes Secret
          │
          ▼
Spring Boot Pod
```

这样：

```text
Secret Source
=
AWS Secrets Manager
```

而不是 Git。

---

# 19. ExternalSecret

示例：

```yaml id="4i7h1b"
apiVersion: external-secrets.io/v1
kind: ExternalSecret

metadata:
  name: ai-db-secret
  namespace: ai-prod

spec:
  refreshInterval: 1h

  secretStoreRef:
    name: aws-secrets
    kind: ClusterSecretStore

  target:
    name: ai-db-secret

  data:
    - secretKey: username
      remoteRef:
        key: prod/ai/database
        property: username

    - secretKey: password
      remoteRef:
        key: prod/ai/database
        property: password
```

---

# 20. Spring Boot 使用 Secret

Deployment：

```yaml id="i4v5gd"
env:
  - name: DB_USERNAME
    valueFrom:
      secretKeyRef:
        name: ai-db-secret
        key: username

  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: ai-db-secret
        key: password
```

Spring Boot：

```yaml id="g8z7y3"
spring:
  datasource:
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
```

---

# 21. 注意一个重要问题

Kubernetes Secret：

```text id="r7c4xq"
不是“绝对安全的密码保险箱”
```

它通常只是：

```text
Base64
+
Kubernetes Secret Storage
```

所以生产：

```text id="j2v6wq"
AWS Secrets Manager
```

才应该作为真正的 Secret Source。

---

# 22. RBAC

Kubernetes 自己还有权限系统：

```text id="1m4j9p"
RBAC
=
Role Based Access Control
```

例如：

```text id="k3r7p2"
Developer
 ↓
dev namespace
```

不能：

```text id="x8n4w6"
prod namespace
```

---

# 23. Role

例如：

```yaml id="45r7a2"
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  name: pod-reader
  namespace: ai-prod

rules:
  - apiGroups: [""]
    resources:
      - pods

    verbs:
      - get
      - list
      - watch
```

这个 Role：

```text id="7y4m9x"
只能查看 Pod
```

---

# 24. RoleBinding

```yaml id="2f9j1m"
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding

metadata:
  name: pod-reader-binding
  namespace: ai-prod

subjects:
  - kind: User
    name: developer

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

# 25. ClusterRole

区别：

```text id="p0k5m9"
Role
=
Namespace 范围

ClusterRole
=
Cluster 范围
```

生产：

> 默认使用 Namespace 级权限。

---

# 26. NetworkPolicy

现在解决网络层。

例如：

```text id="b2y6r8"
AI API
 ↓
MCP
```

允许。

但是：

```text id="q5k9t3"
Internet
 ↓
MCP
```

拒绝。

---

# 27. NetworkPolicy 示例

```yaml id="c9w5s1"
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: ai-api-policy
  namespace: ai-prod

spec:
  podSelector:
    matchLabels:
      app: ai-api

  policyTypes:
    - Ingress
    - Egress
```

之后逐条增加允许规则。

---

# 28. Default Deny

生产可以采用：

```yaml id="f8y3r2"
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: default-deny
  namespace: ai-prod

spec:
  podSelector: {}

  policyTypes:
    - Ingress
    - Egress
```

意思：

```text
默认：
全部拒绝
```

然后：

```text
明确需要什么
 ↓
明确允许什么
```

这是 Zero Trust 思维。

---

# 29. 允许 AI API → MCP

```yaml id="9y5x2n"
policyTypes:
  - Ingress

podSelector:
  matchLabels:
    app: mcp-server

ingress:
  - from:
      - podSelector:
          matchLabels:
            app: ai-api
```

意思：

```text
只有 ai-api
 ↓
mcp-server
```

---

# 30. AI API → RDS

这里有一个关键点：

RDS 不在 Kubernetes NetworkPolicy 里。

网络路径：

```text id="6f0r4w"
Pod
 ↓
Node/VPC
 ↓
Security Group
 ↓
RDS
```

所以需要：

```text id="g4x7s8"
EKS Security Group
 ↓
RDS Security Group
5432
```

---

# 31. Security Layers

现在你已经有：

```text id="4l9z7a"
Layer 1
AWS WAF

Layer 2
ALB Security Group

Layer 3
EKS Security Group

Layer 4
Kubernetes NetworkPolicy

Layer 5
RBAC

Layer 6
IAM

Layer 7
Pod Identity

Layer 8
Application Authorization
```

这才是企业安全。

---

# 32. Multi-Tenant AI Application

你的 AI 平台未来很可能：

```text id="4w7m8q"
Company A
Company B
Company C
```

不能只依赖：

```text id="a2d5x7"
Kubernetes Namespace
```

真正的数据隔离应该：

```text id="j7v3q1"
Tenant ID
 ↓
Application Authorization
 ↓
Database
 ↓
RAG
 ↓
Vector Search
```

---

# 33. RAG Security

这是 AI 项目非常重要的内容。

错误：

```text id="z6m2x9"
User A
 ↓
Vector Search
 ↓
所有 documents
```

正确：

```text id="s8k4p1"
User A
 ↓
tenant_id = A
 ↓
Vector Search
 ↓
Only A documents
```

---

# 34. PostgreSQL RLS

进一步：

```text id="k5x9m3"
PostgreSQL
 ↓
Row Level Security
```

例如：

```text id="f4q7n2"
tenant_id
```

实现数据库级隔离。

这样即使应用层某个查询遗漏 tenant filter，也增加了一层保护。

---

# 35. AI Tool Security

MCP：

```text id="n7m3x5"
LLM
 ↓
Tool
```

不能：

```text id="w5k9q2"
LLM
 ↓
任意 Tool
```

应该：

```text id="p4x8m6"
User
 ↓
Authorization
 ↓
Agent
 ↓
MCP
 ↓
Allowed Tool
```

---

# 36. 高风险 Tool

例如：

```text id="x9m4q7"
delete_customer
refund_payment
send_email
execute_sql
```

不要自动执行。

应该：

```text id="j6p3n8"
Agent
 ↓
Approval
 ↓
Human
 ↓
Execute
```

---

# 37. AI API 的 IAM 权限

例如：

```text id="g4v8m2"
AI API
```

只需要：

```text id="r6x2p9"
bedrock:InvokeModel
s3:GetObject
secretsmanager:GetSecretValue
```

Worker：

```text id="c7n3m5"
sqs:ReceiveMessage
sqs:DeleteMessage
s3:GetObject
```

MCP：

```text id="p2q8v4"
按 Tool 最小授权
```

---

# 38. IAM Policy 思维

永远问：

```text id="7q2m9x"
WHO?
WHAT?
WHERE?
```

例如：

```text
Who?
AI API

What?
s3:GetObject

Where?
company-ai-documents-prod/documents/*
```

而不是：

```text
s3:*
*
```

---

# 39. Day 44 Security Architecture

```text id="c5m8x2"
                         Internet
                            │
                           WAF
                            │
                           ALB
                            │
                         Ingress
                            │
                      Kubernetes
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
            AI API         MCP          Worker
              │
              ▼
        ServiceAccount
              │
              ▼
       EKS Pod Identity
              │
              ▼
           IAM Role
        ┌─────┼────────────┐
        ▼     ▼            ▼
     Bedrock  S3      Secrets Manager
                             │
                             ▼
                        External Secrets
                             │
                             ▼
                      Kubernetes Secret
                             │
                             ▼
                        Spring Boot

Kubernetes RBAC
+
NetworkPolicy
+
RDS Security Group
```

---

# 40. Helm 安全化

Day 42 的：

```text id="j8q4m6"
values.yaml
```

现在增加：

```yaml id="i8g7t3"
serviceAccount:
  create: true
  name: ai-api
```

Deployment：

```yaml id="5t7z2q"
spec:
  serviceAccountName: {{ .Values.serviceAccount.name }}
```

---

# 41. Pod Security

Production：

```yaml id="e5w8x1"
securityContext:
  runAsNonRoot: true

  allowPrivilegeEscalation: false

  readOnlyRootFilesystem: true
```

进一步：

```text id="7m3q9x"
drop Linux capabilities
```

---

# 42. 为什么 `runAsNonRoot`？

不要：

```text id="g5q8n2"
Java
 ↓
root
```

如果应用漏洞：

```text id="p4x7m9"
Attacker
 ↓
Container
 ↓
root
```

风险更高。

生产：

```text id="w2n6c8"
Non-root
```

降低攻击面。

---

# 43. Docker 安全

你的 Docker：

```dockerfile id="m6q3x8"
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/app.jar app.jar

USER 10001

ENTRYPOINT ["java", "-jar", "app.jar"]
```

这样：

```text id="n9p4v2"
Java
 ↓
Non-root
```

---

# 44. Read-only Filesystem

```yaml id="p8x3m7"
securityContext:
  readOnlyRootFilesystem: true
```

如果应用需要临时目录：

```text id="q4m8n2"
/tmp
```

使用：

```yaml id="b5v7k3"
emptyDir: {}
```

---

# 45. Secrets 流程

完整生产方案：

```text id="j9m4q2"
Developer
   X
GitHub
   X
Docker
   X
Helm values
   X

AWS Secrets Manager
        │
        ▼
External Secrets
        │
        ▼
Kubernetes Secret
        │
        ▼
Spring Boot
```

---

# 46. Bedrock 安全流程

```text id="z5x8m3"
Spring Boot Pod
       │
       ▼
ServiceAccount
       │
       ▼
EKS Pod Identity
       │
       ▼
IAM Role
       │
       ▼
Bedrock
```

没有：

```text
API Key
```

没有：

```text
AWS Secret Key
```

---

# 47. S3 安全流程

```text id="r7m3q8"
AI API
 ↓
Pod Identity
 ↓
IAM
 ↓
S3
```

Bucket：

```text id="j4n8x2"
Block Public Access
```

---

# 48. RDS 安全流程

```text id="p6q2m9"
Pod
 ↓
EKS/VPC
 ↓
Security Group
 ↓
RDS
```

只开放：

```text id="a3v7k5"
5432
```

只允许：

```text id="x8m4q2"
EKS App Security Group
```

---

# 49. Production Security Checklist

```text
[ ] No AWS Access Keys in Pods
[ ] No AWS Access Keys in GitHub
[ ] No Passwords in Git
[ ] No Secrets in Helm values
[ ] EKS Pod Identity
[ ] IAM Least Privilege
[ ] Secrets Manager
[ ] External Secrets
[ ] RBAC
[ ] NetworkPolicy
[ ] Security Groups
[ ] Non-root Containers
[ ] Read-only filesystem
[ ] WAF
[ ] HTTPS
[ ] Private EKS Nodes
[ ] RDS Private
[ ] S3 Block Public Access
```

---

# 50. Day 44 实战

今天实际完成以下结构：

```text id="3u7p9k"
EKS
│
├── Namespace: ai-prod
│
├── ServiceAccount: ai-api
│
├── Deployment: ai-api
│
├── Service: ai-api
│
├── NetworkPolicy
│
└── ExternalSecret
```

---

# 51. 创建 Namespace

```bash id="0m6qpy"
kubectl create namespace ai-prod
```

检查：

```bash id="0r4j7h"
kubectl get namespace
```

---

# 52. 创建 ServiceAccount

```yaml id="y0g1cf"
apiVersion: v1
kind: ServiceAccount

metadata:
  name: ai-api
  namespace: ai-prod
```

---

# 53. Deployment 使用它

```yaml id="k5y9c2"
spec:
  template:

    spec:
      serviceAccountName: ai-api
```

现在：

```text id="7n3p8x"
AI API Pod
 ↓
ai-api ServiceAccount
```

---

# 54. Pod Security

加入：

```yaml id="6q2w8n"
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
```

Container：

```yaml id="j7m4p9"
securityContext:
  capabilities:
    drop:
      - ALL
```

具体应用是否支持只读文件系统，要实际测试后再开启。

---

# 55. NetworkPolicy

先：

```text id="x9q3m5"
Default Deny
```

然后允许：

```text id="f8n2v6"
ALB
 ↓
AI API
```

允许：

```text id="q7m4x9"
AI API
 ↓
MCP
```

允许：

```text id="n3p8k2"
AI API
 ↓
RDS
```

网络层面的实际可达性还要同时满足 AWS Security Group / routing。

---

# 56. External Secret

建立：

```text id="y5m8q3"
prod/ai/database
```

然后：

```text id="u7p2x9"
ExternalSecret
 ↓
ai-db-secret
```

查看：

```bash id="q3m6v8"
kubectl get externalsecret \
  -n ai-prod
```

---

# 57. 验证 Secret

```bash id="j4x8p2"
kubectl get secret \
  -n ai-prod
```

不要在终端随便：

```bash id="x5n7m3"
kubectl get secret ... -o yaml
```

然后把输出复制到聊天、GitHub 或日志里。

---

# 58. 验证 IAM

进入 Pod：

```bash id="r8m2q6"
kubectl exec -it \
  <pod> \
  -n ai-prod \
  -- env
```

不要寻找：

```text id="q7n3x5"
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

应用应该通过 AWS SDK 默认 credential chain 获取 Pod Identity 提供的临时凭证。

---

# 59. 验证 Bedrock

Java：

```text id="z2m8p4"
Spring AI
 ↓
AWS SDK
 ↓
Pod Identity
 ↓
IAM
 ↓
Bedrock
```

测试：

```text id="x6q3n9"
Simple Prompt
```

如果成功：

```text
IAM + Pod Identity + Bedrock
```

整个链路就打通了。

---

# 60. Day 44 面试题

### Q1：为什么 Pod 不应该保存 AWS Access Key？

因为：

```text id="t8m4q2"
长期凭证
+
泄漏风险
```

应该：

```text id="j6p9x3"
Pod Identity
 ↓
IAM Role
 ↓
Temporary Credentials
```

---

### Q2：IAM Role 和 RBAC 有什么区别？

```text id="a7q3m8"
IAM
=
AWS 权限

RBAC
=
Kubernetes 权限
```

---

### Q3：Secrets Manager 和 Kubernetes Secret？

```text id="h2m7x4"
Secrets Manager
=
AWS Secret Source

Kubernetes Secret
=
K8s Secret Object
```

生产：

```text id="v8p3n6"
Secrets Manager
 ↓
External Secrets
 ↓
K8s Secret
```

---

### Q4：NetworkPolicy 和 Security Group？

```text id="p4x9m2"
Security Group
=
AWS/VPC 网络层

NetworkPolicy
=
Kubernetes Pod 网络层
```

两者可以叠加。

---

### Q5：为什么 AI API 和 MCP 要不同 IAM Role？

因为：

```text id="c7m3q8"
不同职责
 ↓
不同权限
 ↓
Blast Radius 更小
```

---

# 61. Day 44 最终企业安全架构

```text id="n5x8m2"
                    INTERNET
                       │
                      WAF
                       │
                      ALB
                       │
                    HTTPS
                       │
                    Ingress
                       │
                 Kubernetes
                       │
       ┌───────────────┼───────────────┐
       ▼               ▼               ▼
    AI API            MCP           Worker
       │
       ▼
ServiceAccount
       │
       ▼
EKS Pod Identity
       │
       ▼
IAM Role
   ┌───┼────────┬──────────┐
   ▼   ▼        ▼          ▼
Bedrock S3   Secrets      SQS
             Manager
                │
                ▼
        External Secrets
                │
                ▼
         Kubernetes Secret
                │
                ▼
           Spring Boot

       +
       
RBAC
NetworkPolicy
Security Groups
Non-root
Read-only FS
RDS Private
S3 Private
```

---

# 62. Day 40 → Day 44

你现在已经建立了完整的生产链：

```text id="e7n2m4"
Day 40
CI/CD
   ↓
Day 41
Kubernetes
   ↓
Day 42
Helm
   ↓
Day 43
Ingress + ALB + HTTPS + DNS
   ↓
Day 44
Security
```

最终：

```text id="k3p8m5"
GitHub
 ↓
CI
 ↓
Security Scan
 ↓
AI Evaluation
 ↓
Docker
 ↓
ECR
 ↓
Helm
 ↓
EKS
 ↓
ALB
 ↓
HTTPS
 ↓
Spring Boot
 ↓
RAG / Agent / MCP
 ↓
Pod Identity
 ↓
IAM
 ↓
Bedrock / S3 / Secrets
```

## Day 44 的核心原则

记住这一条就够了：

```text
Pod
  ↓
ServiceAccount
  ↓
Pod Identity
  ↓
IAM Role
  ↓
AWS Service
```

而不是：

```text
Pod
  ↓
AWS Access Key
```

**Day 45** 建议进入 **EKS Observability：CloudWatch + Prometheus + Grafana + OpenTelemetry + Distributed Tracing**，把 `Spring Boot → Agent → RAG → MCP → Bedrock → PostgreSQL → Redis` 整条 AI 请求链做到可监控、可追踪、可定位性能瓶颈。
