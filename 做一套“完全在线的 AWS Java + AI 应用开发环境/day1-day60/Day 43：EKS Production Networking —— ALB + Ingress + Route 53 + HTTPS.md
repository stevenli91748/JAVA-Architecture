# Day 43：EKS Production Networking —— ALB + Ingress + Route 53 + HTTPS

Day 42 已经完成 Helm。

今天把真正的公网访问链路打通：

```text
User
 ↓
https://api.example.com
 ↓
Route 53
 ↓
AWS ALB
 ↓
EKS Ingress
 ↓
Kubernetes Service
 ↓
Spring Boot Pod
```

这是从“会 Kubernetes”走向“能部署企业生产系统”的关键一天。

---

# 1. Day 43 学习目标

今天掌握：

```text
① Route 53
② DNS
③ ACM
④ HTTPS / TLS
⑤ AWS Load Balancer Controller
⑥ Kubernetes Ingress
⑦ ALB
⑧ Target Group
⑨ Health Check
⑩ ExternalDNS
⑪ EKS Network
⑫ Security Group
⑬ Private Subnet
⑭ Production DNS Architecture
```

---

# 2. 最终架构

```text
                    Internet
                       │
                       ▼
               api.example.com
                       │
                       ▼
                  Route 53
                       │
                       ▼
                  AWS ALB
                  HTTPS :443
                       │
                       ▼
                EKS Ingress
                       │
                       ▼
              ai-api-service
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
           Pod 1     Pod 2     Pod 3
             │
             ▼
        Spring Boot
             │
      ┌──────┼──────┐
      ▼      ▼      ▼
     RAG    MCP    Bedrock
```

---

# 3. 为什么不能直接暴露 Pod？

错误：

```text
Internet
   ↓
Pod
```

Pod：

```text
IP
 ↓
可能变化
```

所以：

```text
Internet
 ↓
ALB
 ↓
Service
 ↓
Pod
```

---

# 4. Route 53

Route 53 负责：

```text
DNS
```

例如：

```text
api.example.com
```

解析到：

```text
ALB
```

最终：

```text
Browser
 ↓
api.example.com
 ↓
ALB
```

---

# 5. DNS 记录

生产：

```text
api.example.com
```

通常使用：

```text
A / AAAA Alias
```

指向：

```text
ALB
```

AWS Alias 记录适合 AWS 资源。

---

# 6. ACM

HTTPS Certificate 使用：

```text
AWS Certificate Manager
```

例如：

```text
api.example.com
```

申请：

```text
TLS Certificate
```

然后：

```text
ACM
 ↓
ALB
```

---

# 7. HTTPS 架构

```text
Client
  │
  │ HTTPS :443
  ▼
ALB
  │
  │ HTTP :8080
  ▼
EKS Pod
```

第一版可以让：

```text
Internet → ALB
```

使用 HTTPS。

内部：

```text
ALB → Pod
```

是否也使用 HTTPS，要根据你的内部安全要求决定。

---

# 8. TLS Termination

最常见：

```text
Client
   │
 HTTPS
   ▼
 ALB
   │
 TLS Termination
   │
 HTTP
   ▼
EKS
```

ALB 负责：

```text
TLS
Certificate
HTTPS
```

Java 应用不需要自己管理公网 TLS Certificate。

---

# 9. ALB Listener

生产：

```text
443
 ↓
HTTPS
 ↓
Target Group
```

HTTP：

```text
80
 ↓
Redirect
 ↓
443
```

所以：

```text
HTTP
 ↓
301/302
 ↓
HTTPS
```

---

# 10. EKS 中的 Ingress

我们之前：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
```

今天进一步配置：

```yaml
metadata:
  annotations:
```

告诉 AWS：

> 我要创建一个 ALB。

---

# 11. AWS Load Balancer Controller

架构：

```text
Kubernetes Ingress
       │
       ▼
AWS Load Balancer Controller
       │
       ▼
AWS ALB
```

它运行在 EKS 中。

---

# 12. 为什么需要 Controller？

Kubernetes 本身并不知道：

```text
AWS ALB
```

怎么创建。

Controller 负责监听：

```text
Ingress
```

然后：

```text
Ingress
 ↓
Controller
 ↓
ALB
 ↓
Target Group
 ↓
Listener
```

---

# 13. Controller

在 EKS 中：

```text
aws-load-balancer-controller
```

它可以管理：

```text
ALB
NLB
Target Group
Security Group
```

具体能力取决于配置和 AWS Load Balancer Controller 版本。

---

# 14. Controller 权限

它需要 AWS 权限：

```text
EC2
ELB
IAM
Security Groups
```

但不要：

```text
AdministratorAccess
```

生产：

```text
IAM
+
IRSA / EKS Pod Identity
```

进行最小权限授权。

---

# 15. 推荐使用 EKS Pod Identity

架构：

```text
Controller Pod
      │
      ▼
EKS Pod Identity
      │
      ▼
IAM Role
      │
      ▼
AWS APIs
```

这样：

```text
AWS Access Key
```

不需要进入 Pod。

---

# 16. Ingress 配置

例如：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: ai-api

  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip

spec:
  ingressClassName: alb

  rules:
    - host: api.example.com

      http:
        paths:
          - path: /
            pathType: Prefix

            backend:
              service:
                name: ai-api
                port:
                  number: 80
```

---

# 17. `internet-facing`

```yaml
alb.ingress.kubernetes.io/scheme: internet-facing
```

意思：

```text
Internet
 ↓
ALB
```

如果：

```text
internal
```

则：

```text
VPC
 ↓
Internal ALB
```

---

# 18. `target-type: ip`

```yaml
alb.ingress.kubernetes.io/target-type: ip
```

意思：

```text
ALB
 ↓
Pod IP
```

而不是：

```text
ALB
 ↓
Node
 ↓
Pod
```

EKS + AWS Load Balancer Controller 常用 `ip` 模式。

---

# 19. ALB Target Group

最终：

```text
ALB
 │
 └── Target Group
       │
       ├── Pod 1
       ├── Pod 2
       └── Pod 3
```

当 Pod：

```text
UNHEALTHY
```

ALB：

```text
停止发送流量
```

---

# 20. Health Check

Spring Boot：

```text
/actuator/health
```

ALB：

```text
GET /actuator/health
```

例如：

```json
{
  "status": "UP"
}
```

---

# 21. 更好的 Spring Boot Health

Spring Boot 应该区分：

```text
liveness
readiness
```

例如：

```text
/actuator/health/liveness
/actuator/health/readiness
```

Kubernetes：

```text
Readiness
 ↓
决定是否接收流量
```

---

# 22. ALB Health Check

Ingress：

```yaml
annotations:
  alb.ingress.kubernetes.io/healthcheck-path: /actuator/health/readiness
```

这样：

```text
ALB
 ↓
readiness
 ↓
Healthy
```

---

# 23. Security Group

最终：

```text
Internet
 ↓
ALB-SG
443
 ↓
EKS
 ↓
Pod
```

不要：

```text
Internet
 ↓
Node :8080
```

---

# 24. EKS Node Security Group

只允许：

```text
ALB
 ↓
EKS
```

例如：

```text
ALB-SG
 ↓
EKS-SG
```

而不是：

```text
0.0.0.0/0
 ↓
Node
```

---

# 25. Route 53

假设：

```text
example.com
```

创建：

```text
api.example.com
```

DNS：

```text
api.example.com
        │
        ▼
       ALB
```

---

# 26. DNS 流程

用户输入：

```text
https://api.example.com
```

DNS：

```text
Route 53
 ↓
ALB DNS
```

ALB：

```text
443
 ↓
Ingress
```

然后：

```text
Service
 ↓
Pod
```

---

# 27. ACM Certificate

申请：

```text
*.example.com
```

或者：

```text
api.example.com
```

推荐如果你的系统未来会有：

```text
api.example.com
app.example.com
admin.example.com
```

可以考虑：

```text
*.example.com
```

但 Certificate 范围要按实际安全需求最小化。

---

# 28. DNS Validation

ACM：

```text
Request Certificate
 ↓
DNS Validation
```

Route 53：

```text
CNAME
 ↓
ACM
```

验证：

```text
Certificate
 ↓
ISSUED
```

---

# 29. ALB HTTPS

Ingress：

```yaml
annotations:
  alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS":443}]'

  alb.ingress.kubernetes.io/certificate-arn: arn:aws:acm:...
```

于是：

```text
HTTPS
 ↓
ALB
 ↓
EKS
```

---

# 30. HTTP → HTTPS

配置：

```yaml
alb.ingress.kubernetes.io/ssl-redirect: '443'
```

最终：

```text
http://api.example.com
        ↓
      443
        ↓
https://api.example.com
```

---

# 31. Helm 化

不要把：

```text
certificate ARN
domain
```

硬编码。

`values.yaml`：

```yaml
ingress:
  enabled: true

  host: api.example.com

  certificateArn: ""

  scheme: internet-facing
```

Production：

```yaml
ingress:
  host: api.example.com

  certificateArn: arn:aws:acm:...
```

---

# 32. Dev / Staging / Production

### Dev

```yaml
ingress:
  host: api-dev.example.com
```

### Staging

```yaml
ingress:
  host: api-staging.example.com
```

### Production

```yaml
ingress:
  host: api.example.com
```

同一个 Helm Chart。

---

# 33. ExternalDNS

手动：

```text
ALB
 ↓
复制 DNS Name
 ↓
Route 53
 ↓
创建 A Record
```

很麻烦。

可以使用：

```text
ExternalDNS
```

让 Kubernetes 自动管理 DNS。

---

# 34. ExternalDNS 架构

```text
Kubernetes Ingress
       │
       ▼
ExternalDNS
       │
       ▼
Route 53
       │
       ▼
api.example.com
```

当 Ingress 创建：

```text
api.example.com
```

ExternalDNS：

```text
Route 53
 ↓
自动创建 DNS Record
```

---

# 35. ExternalDNS 权限

只允许：

```text
Route53
 ├── ListHostedZones
 ├── ListResourceRecordSets
 └── ChangeResourceRecordSets
```

不要：

```text
AdministratorAccess
```

---

# 36. DNS 最终自动化

现在：

```text
Helm
 ↓
Ingress
 ↓
AWS Load Balancer Controller
 ↓
ALB
```

同时：

```text
Ingress
 ↓
ExternalDNS
 ↓
Route 53
```

---

# 37. 最终公网架构

```text
                    User
                      │
                      ▼
              api.example.com
                      │
                      ▼
                  Route 53
                      │
                      ▼
                     ALB
                  HTTPS :443
                      │
                      ▼
            AWS Load Balancer
                Controller
                      │
                      ▼
                  Ingress
                      │
                      ▼
               AI API Service
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
        Pod 1       Pod 2       Pod 3
          │
          ▼
      Spring Boot
          │
    ┌─────┼──────┐
    ▼     ▼      ▼
   RAG   MCP   Bedrock
```

---

# 38. Frontend 也可以独立

你的 React：

```text
React
 ↓
S3
 ↓
CloudFront
 ↓
app.example.com
```

API：

```text
Spring Boot
 ↓
ALB
 ↓
EKS
 ↓
api.example.com
```

最终：

```text
app.example.com
       │
       ▼
    CloudFront
       │
       ▼
       S3


api.example.com
       │
       ▼
      ALB
       │
       ▼
      EKS
```

这是非常典型的企业前后端分离架构。

---

# 39. CORS

Frontend：

```text
https://app.example.com
```

Backend：

```text
https://api.example.com
```

浏览器会涉及：

```text
CORS
```

Spring Boot：

```text
Allowed Origin:
https://app.example.com
```

不要生产：

```text
*
```

如果 API 涉及认证或敏感数据，允许来源应明确限定。

---

# 40. API Gateway 要不要用？

你的架构目前：

```text
CloudFront
+
ALB
+
EKS
```

已经可以。

API Gateway 更适合：

```text
API Management
Rate Limit
API Keys
Authorizer
Versioning
Serverless
```

不是所有 EKS API 都必须经过 API Gateway。

---

# 41. WAF

生产可以进一步：

```text
Internet
 ↓
CloudFront / ALB
 ↓
AWS WAF
 ↓
EKS
```

WAF 可以帮助防护：

```text
SQL Injection
XSS
Bad Bots
IP Rules
Rate Rules
```

AI API 还可以考虑：

```text
Request Size
Rate Limit
Abuse Protection
```

---

# 42. AI API 的 Rate Limit

例如：

```text
User
 ↓
100 requests/min
```

超过：

```text
429 Too Many Requests
```

可以使用：

```text
Redis
```

实现分布式限流：

```text
EKS
 │
 ├── Pod 1
 ├── Pod 2
 └── Pod 3
       │
       ▼
     Redis
```

---

# 43. SSE / Streaming

你的 AI Chat 很可能需要：

```text
POST /api/chat
```

然后：

```text
Token
 ↓
Token
 ↓
Token
 ↓
Token
```

也就是：

```text
Streaming
```

通常可以采用：

```text
SSE
```

架构：

```text
Browser
 ↓
ALB
 ↓
EKS
 ↓
Spring Boot
 ↓
Bedrock
```

要特别测试：

```text
Idle Timeout
Connection Timeout
Buffering
```

否则 AI 输出可能出现：

> 用户一直等，最后一次性返回全部答案。

---

# 44. WebSocket

如果未来使用：

```text
WebSocket
```

ALB 也可以支持。

但对于普通 AI Token Streaming：

```text
SSE
```

通常更简单。

---

# 45. Day 43 Terraform 与 Helm 分工

现在非常清楚：

### Terraform

```text
Route 53
ACM
VPC
EKS
IAM
ALB 相关基础设施
RDS
S3
Redis
SQS
```

### Helm

```text
Deployment
Service
Ingress
HPA
ConfigMap
Kubernetes application config
```

### ExternalDNS

```text
Ingress → Route 53 DNS
```

### AWS Load Balancer Controller

```text
Ingress → ALB
```

---

# 46. Day 43 完整架构

```text
                           Internet
                              │
                              ▼
                     ┌─────────────────┐
                     │    Route 53     │
                     │ api.example.com │
                     └────────┬────────┘
                              │
                              ▼
                     ┌─────────────────┐
                     │   AWS WAF       │
                     └────────┬────────┘
                              │
                              ▼
                     ┌─────────────────┐
                     │      ALB        │
                     │    HTTPS :443   │
                     └────────┬────────┘
                              │
                              ▼
                 AWS Load Balancer Controller
                              │
                              ▼
                         Kubernetes
                           Ingress
                              │
                              ▼
                      AI API Service
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
           Pod 1            Pod 2            Pod 3
             │
             ▼
        Spring Boot
             │
       ┌─────┼─────┬─────────┐
       ▼     ▼     ▼         ▼
      RAG   MCP  Memory    Bedrock
       │     │     │
       └─────┼─────┘
             ▼
       PostgreSQL
        + pgvector

ExternalDNS
    │
    ▼
Route 53

ACM
    │
    ▼
ALB Certificate
```

---

# 47. Day 43 实战顺序

今天严格按照：

```text
1. Route 53
       ↓
2. ACM
       ↓
3. EKS
       ↓
4. AWS Load Balancer Controller
       ↓
5. Ingress
       ↓
6. ALB
       ↓
7. HTTPS
       ↓
8. DNS
       ↓
9. ExternalDNS
       ↓
10. Smoke Test
```

---

# 48. Smoke Test

最终执行：

```bash id="n0sq3m"
curl https://api.example.com/actuator/health
```

返回：

```json
{
  "status": "UP"
}
```

再：

```bash id="7fsl66"
curl https://api.example.com/api/chat
```

测试 AI API。

---

# 49. 最终验收标准

Day 43 完成：

```text
[✓] Route 53
[✓] DNS
[✓] ACM
[✓] HTTPS
[✓] ALB
[✓] AWS Load Balancer Controller
[✓] Kubernetes Ingress
[✓] Target Group
[✓] Health Check
[✓] EKS Private Subnet
[✓] Security Groups
[✓] ExternalDNS
[✓] Helm Ingress
[✓] api.example.com
[✓] Spring Boot Health
[✓] AI API Smoke Test
```

---

# 50. Day 43 必背的企业架构

面试如果问：

> “你如何把一个 Spring Boot AI 应用部署到 AWS EKS？”

你可以回答：

```text
GitHub
 ↓
CI/CD
 ↓
Docker
 ↓
ECR
 ↓
Helm
 ↓
EKS
 ↓
Deployment
 ↓
Service
 ↓
Ingress
 ↓
AWS Load Balancer Controller
 ↓
ALB
 ↓
ACM HTTPS
 ↓
Route 53
 ↓
Spring Boot
 ↓
RAG / MCP / Agent
 ↓
Bedrock
 ↓
RDS PostgreSQL + pgvector
```

这已经是一套非常完整的 **AWS Java + AI Enterprise Architecture**。

---

## Day 43 → Day 44

下一步进入 **EKS Production Security + IAM + Pod Identity + Secrets Manager + External Secrets + RBAC + NetworkPolicy**。

重点是把：

```text
EKS
 ↓
IAM
 ↓
Pod
 ↓
Bedrock
 ↓
S3
 ↓
RDS
 ↓
Secrets Manager
```

全部做到**最小权限、无 AWS Access Key、无明文 Secret、网络隔离**。

这一步完成后，你的 AWS Java + AI 平台才真正具备企业生产环境的安全基础。
