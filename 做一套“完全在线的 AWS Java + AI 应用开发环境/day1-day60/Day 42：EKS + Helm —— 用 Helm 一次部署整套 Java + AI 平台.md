# Day 42：EKS + Helm —— 用 Helm 一次部署整套 Java + AI 平台

Day 41 学了 Kubernetes 基础：

```text
EKS
 ↓
Pod
 ↓
Deployment
 ↓
Service
 ↓
Ingress
 ↓
HPA
```

今天解决一个实际问题：

> **如果你的平台有 10～30 个 Kubernetes YAML，怎么一次性部署、升级和回滚？**

答案：

# Helm

---

# 1. Day 42 最终目标

今天完成：

```text
GitHub
   ↓
Docker
   ↓
ECR
   ↓
Helm
   ↓
EKS
   │
   ├── AI API
   ├── MCP Server
   ├── RAG Worker
   ├── Service
   ├── Ingress
   ├── HPA
   └── Config
```

最终可以：

```bash
helm install ai-platform ./helm/ai-platform
```

升级：

```bash
helm upgrade ai-platform ./helm/ai-platform
```

回滚：

```bash
helm rollback ai-platform 1
```

---

# 2. 为什么需要 Helm？

没有 Helm：

```text
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
kubectl apply -f configmap.yaml
kubectl apply -f hpa.yaml
...
```

文件越来越多：

```text
deployment.yaml
service.yaml
ingress.yaml
configmap.yaml
secret.yaml
hpa.yaml
...
```

很难管理。

---

# 3. Helm 的思维

Helm：

```text
Chart
 ↓
Templates
 ↓
Values
 ↓
Kubernetes YAML
```

也就是说：

```text
Helm
=
Kubernetes Package Manager
```

---

# 4. 创建 Helm Chart

执行：

```bash
helm create ai-platform
```

得到：

```text
ai-platform/
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── hpa.yaml
│   └── ...
└── .helmignore
```

---

# 5. 企业项目结构

我们不要直接使用默认结构，整理成：

```text
helm/
└── ai-platform/
    │
    ├── Chart.yaml
    ├── values.yaml
    │
    ├── templates/
    │   ├── ai-api-deployment.yaml
    │   ├── ai-api-service.yaml
    │   ├── mcp-deployment.yaml
    │   ├── mcp-service.yaml
    │   ├── worker-deployment.yaml
    │   ├── ingress.yaml
    │   ├── hpa.yaml
    │   └── configmap.yaml
    │
    └── values/
        ├── dev.yaml
        ├── staging.yaml
        └── prod.yaml
```

---

# 6. Chart.yaml

```yaml
apiVersion: v2

name: ai-platform

description: Enterprise Java AI Platform

type: application

version: 1.0.0

appVersion: "1.0.0"
```

这里：

```text
version
```

是 Helm Chart 版本。

而：

```text
appVersion
```

是你的应用版本。

---

# 7. values.yaml

这是 Helm 最重要的文件之一。

```yaml
replicaCount: 2

image:
  repository: 123456789.dkr.ecr.us-west-2.amazonaws.com/ai-api
  tag: latest
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80
  targetPort: 8080

resources:
  requests:
    cpu: 500m
    memory: 512Mi

  limits:
    cpu: "1"
    memory: 1Gi
```

---

# 8. 为什么 values.yaml 很重要？

你不需要修改：

```text
deployment.yaml
```

只修改：

```text
values.yaml
```

例如：

### Dev

```yaml
replicaCount: 1
```

### Production

```yaml
replicaCount: 4
```

同一个 Chart：

```text
AI Platform
   │
   ├── DEV
   ├── STAGING
   └── PROD
```

---

# 9. Deployment Template

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: {{ include "ai-platform.fullname" . }}

spec:
  replicas: {{ .Values.replicaCount }}

  selector:
    matchLabels:
      app: ai-api

  template:
    metadata:
      labels:
        app: ai-api

    spec:
      containers:
        - name: ai-api

          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"

          ports:
            - containerPort: 8080
```

---

# 10. Helm Template 是什么？

例如：

```yaml
replicas: {{ .Values.replicaCount }}
```

如果：

```yaml
replicaCount: 3
```

Helm 生成：

```yaml
replicas: 3
```

所以：

```text
values.yaml
     ↓
Template
     ↓
Kubernetes YAML
```

---

# 11. Helm Template

先不要部署。

执行：

```bash
helm template ai-platform ./helm/ai-platform
```

它会把：

```text
Helm Template
```

转换成：

```text
Kubernetes YAML
```

这一步非常适合 Debug。

---

# 12. Helm Lint

执行：

```bash
helm lint ./helm/ai-platform
```

如果：

```text
1 chart(s) linted, 0 chart(s) failed
```

说明 Chart 基本正确。

---

# 13. Helm Install

```bash
helm install \
  ai-platform \
  ./helm/ai-platform
```

查看：

```bash
helm list
```

得到：

```text
NAME           STATUS
ai-platform    deployed
```

---

# 14. 查看 Kubernetes

```bash
kubectl get pods
```

例如：

```text
ai-platform-ai-api-xxx
ai-platform-ai-api-yyy
```

查看：

```bash
kubectl get svc
```

---

# 15. Helm Upgrade

修改：

```yaml
replicaCount: 4
```

执行：

```bash
helm upgrade \
  ai-platform \
  ./helm/ai-platform
```

Kubernetes：

```text
2 Pods
 ↓
4 Pods
```

---

# 16. Helm History

```bash
helm history ai-platform
```

例如：

```text
REVISION   STATUS
1          deployed
2          deployed
3          deployed
```

这就是 Helm 自己的 Release 历史。

---

# 17. Helm Rollback

如果 Revision 3 有问题：

```bash
helm rollback \
  ai-platform \
  2
```

回到：

```text
Revision 2
```

然后：

```bash
helm status ai-platform
```

---

# 18. Helm Release

非常重要：

```text
Chart
=
安装包

Release
=
安装后的实例
```

例如：

```text
Chart:
ai-platform

Release:
ai-platform-prod
```

你可以：

```text
同一个 Chart
       │
 ┌─────┼─────┐
 ▼     ▼     ▼
dev   stage  prod
```

---

# 19. Production Values

建立：

```text
values/prod.yaml
```

```yaml
replicaCount: 4

image:
  repository: 123456789.dkr.ecr.us-west-2.amazonaws.com/ai-api
  tag: "8f42c91"

resources:
  requests:
    cpu: "1"
    memory: 1Gi

  limits:
    cpu: "2"
    memory: 2Gi
```

---

# 20. Dev Values

```yaml
replicaCount: 1

image:
  tag: latest

resources:
  requests:
    cpu: 250m
    memory: 256Mi

  limits:
    cpu: 500m
    memory: 512Mi
```

---

# 21. Staging

```yaml
replicaCount: 2

image:
  tag: "8f42c91"

resources:
  requests:
    cpu: 500m
    memory: 512Mi
```

---

# 22. Production Deployment

```bash
helm upgrade --install \
  ai-platform-prod \
  ./helm/ai-platform \
  -f ./helm/ai-platform/values/prod.yaml
```

这条命令非常重要：

```text
upgrade --install
```

意思：

```text
不存在 → install

存在 → upgrade
```

---

# 23. AI API

你的平台现在：

```text
EKS
│
├── ai-api
│
├── mcp-server
│
└── ingestion-worker
```

Helm 管理全部。

---

# 24. MCP Deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: mcp-server

spec:
  replicas: 2

  selector:
    matchLabels:
      app: mcp-server

  template:
    metadata:
      labels:
        app: mcp-server

    spec:
      containers:
        - name: mcp-server
          image: "{{ .Values.mcp.image.repository }}:{{ .Values.mcp.image.tag }}"
```

---

# 25. MCP Service

```yaml
apiVersion: v1
kind: Service

metadata:
  name: mcp-server

spec:
  selector:
    app: mcp-server

  ports:
    - port: 8080
      targetPort: 8080
```

AI API：

```text
ai-api
 ↓
mcp-server:8080
```

---

# 26. Worker

RAG Worker：

```text
S3
 ↓
SQS
 ↓
Worker
```

Kubernetes：

```text
Deployment
    ↓
Worker Pods
```

例如：

```yaml
replicaCount: 2
```

---

# 27. Worker 不应该暴露 Service

AI API：

```text
需要 Service
```

Worker：

```text
不需要外部 Service
```

因为：

```text
Worker
 ↓
SQS
```

它主动消费消息。

---

# 28. Ingress

生产：

```text
Internet
 ↓
ALB
 ↓
Ingress
 ↓
ai-api-service
```

Ingress 示例：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: ai-platform

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

# 29. AWS Load Balancer Controller

EKS：

```text
Ingress
 ↓
AWS Load Balancer Controller
 ↓
ALB
```

它负责把 Kubernetes Ingress 转换成 AWS ALB 配置。

这是 EKS 生产环境必须掌握的组件之一。

---

# 30. HPA + Helm

values：

```yaml
autoscaling:
  enabled: true

  minReplicas: 2
  maxReplicas: 10

  targetCPUUtilizationPercentage: 70
```

模板：

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: ai-api

spec:
  minReplicas: {{ .Values.autoscaling.minReplicas }}
  maxReplicas: {{ .Values.autoscaling.maxReplicas }}
```

---

# 31. AI 应用的 HPA

传统 Java：

```text
CPU
 ↓
HPA
```

AI：

```text
CPU
+
Memory
+
Request Rate
+
Latency
+
Queue Depth
```

以后可以：

```text
KEDA
```

根据：

```text
SQS Queue
```

自动扩展 Worker。

---

# 32. KEDA

RAG：

```text
S3
 ↓
SQS
 ↓
Queue = 1000
```

KEDA：

```text
Worker
2 Pods
 ↓
10 Pods
```

Queue：

```text
1000
 ↓
200
 ↓
0
```

然后：

```text
10 Pods
 ↓
2 Pods
```

这非常适合你的 AI ingestion pipeline。

---

# 33. External Secrets

生产：

```text
AWS Secrets Manager
        │
        ▼
External Secrets Operator
        │
        ▼
Kubernetes Secret
        │
        ▼
Spring Boot
```

例如：

```text
DB_PASSWORD
JWT_SECRET
```

不进入：

```text
GitHub
Helm values
Docker image
```

---

# 34. 不要这样写

错误：

```yaml
env:
  DB_PASSWORD: "MyPassword123"
```

也不要：

```yaml
password: abc123
```

因为：

```text
Git
 ↓
Helm
 ↓
Repository
```

会泄露。

---

# 35. Helm + Security

生产：

```text
Helm
 ↓
Kubernetes
 ↓
RBAC
 ↓
NetworkPolicy
 ↓
Pod Identity
 ↓
Secrets Manager
```

---

# 36. Helm + Bedrock

AI API：

```text
Pod
 ↓
EKS Pod Identity
 ↓
IAM Role
 ↓
Bedrock
```

不要：

```text
Pod
 ↓
AWS_ACCESS_KEY_ID
```

---

# 37. Helm + RDS

```text
AI API Pod
     │
     ▼
RDS PostgreSQL
     │
     ▼
pgvector
```

Helm 不负责创建 PostgreSQL。

RDS：

```text
Terraform
```

Kubernetes：

```text
Helm
```

这是非常好的职责分离：

```text
Terraform
=
AWS Infrastructure

Helm
=
Kubernetes Application
```

---

# 38. Terraform vs Helm

| Terraform      | Helm           |
| -------------- | -------------- |
| AWS VPC        | K8s Deployment |
| RDS            | K8s Service    |
| EKS            | K8s Ingress    |
| S3             | HPA            |
| IAM            | ConfigMap      |
| Security Group | K8s Secret     |
| ECR            | Application    |

记住：

> **Terraform 管 AWS 基础设施，Helm 管 Kubernetes 应用。**

---

# 39. 完整部署流程

现在：

```text
Developer
    │
    ▼
GitHub
    │
    ▼
GitHub Actions
    │
    ├── Maven
    ├── Test
    ├── Security
    └── AI Evaluation
            │
            ▼
           Docker
            │
            ▼
           ECR
            │
            ▼
        Helm Upgrade
            │
            ▼
           EKS
            │
       ┌────┼────┐
       ▼    ▼    ▼
     API   MCP  Worker
       │
       ▼
    Bedrock
```

---

# 40. Helm CI/CD

GitHub Actions：

```yaml
- name: Helm Lint
  run: |
    helm lint ./helm/ai-platform

- name: Helm Template
  run: |
    helm template \
      ai-platform \
      ./helm/ai-platform

- name: Deploy
  run: |
    helm upgrade --install \
      ai-platform-prod \
      ./helm/ai-platform \
      -f ./helm/ai-platform/values/prod.yaml
```

---

# 41. Deployment Gate

生产不要直接：

```text
Git Push
 ↓
Production
```

应该：

```text
Git Push
 ↓
CI
 ↓
Security
 ↓
AI Evaluation
 ↓
ECR
 ↓
Staging
 ↓
Smoke Test
 ↓
Approval
 ↓
Helm
 ↓
Production
```

---

# 42. Helm Rollback + ECS Rollback

现在你同时具备：

### ECS

```text
ECS Deployment
 ↓
Rollback
```

### Kubernetes

```text
Helm
 ↓
Rollback
```

以后企业遇到：

```text
Bad Deployment
```

你就知道两种处理方式。

---

# 43. Day 42 实战

执行：

```bash
helm version
```

然后：

```bash
helm create ai-platform
```

修改：

```text
values.yaml
deployment.yaml
service.yaml
```

验证：

```bash
helm lint ./ai-platform
```

然后：

```bash
helm template ai-platform ./ai-platform
```

最后：

```bash
helm install ai-platform ./ai-platform
```

检查：

```bash
kubectl get pods
kubectl get svc
kubectl get deployment
```

---

# 44. Day 42 升级测试

修改：

```yaml
replicaCount: 3
```

执行：

```bash
helm upgrade ai-platform ./ai-platform
```

检查：

```bash
kubectl get pods
```

应该：

```text
3 Pods
```

---

# 45. Day 42 Rollback 测试

查看：

```bash
helm history ai-platform
```

然后：

```bash
helm rollback ai-platform 1
```

确认：

```bash
kubectl get pods
```

---

# 46. Day 42 最终项目结构

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
│   └── terraform/
│
├── helm/
│   └── ai-platform/
│       │
│       ├── Chart.yaml
│       ├── values.yaml
│       │
│       ├── values/
│       │   ├── dev.yaml
│       │   ├── staging.yaml
│       │   └── prod.yaml
│       │
│       └── templates/
│           ├── ai-api-deployment.yaml
│           ├── ai-api-service.yaml
│           ├── mcp-deployment.yaml
│           ├── mcp-service.yaml
│           ├── worker-deployment.yaml
│           ├── ingress.yaml
│           ├── hpa.yaml
│           └── configmap.yaml
│
└── .github/
    └── workflows/
```

---

# 47. Day 42 验收标准

今天完成：

```text
[✓] Helm 安装
[✓] Chart 创建
[✓] values.yaml
[✓] Deployment Template
[✓] Service Template
[✓] Ingress Template
[✓] HPA Template
[✓] Dev Values
[✓] Staging Values
[✓] Production Values
[✓] helm lint
[✓] helm template
[✓] helm install
[✓] helm upgrade
[✓] helm history
[✓] helm rollback
```

---

# 48. Day 42 面试必会

### Q1：Helm 是什么？

```text
Kubernetes Package Manager
```

---

### Q2：Terraform 和 Helm 区别？

```text
Terraform
→ Infrastructure

Helm
→ Kubernetes Application
```

---

### Q3：values.yaml 是什么？

```text
配置参数
```

用于：

```text
同一 Chart
→ Dev
→ Staging
→ Production
```

---

### Q4：Helm Release 是什么？

```text
Chart
 ↓
Install
 ↓
Release
```

---

### Q5：怎么回滚？

```bash
helm rollback RELEASE REVISION
```

---

# 49. Day 31 → Day 42

现在你的技术栈已经形成：

```text
Java 21
   │
Spring Boot
   │
Spring AI
   │
RAG
   │
MCP
   │
Agent Workflow
   │
Memory
   │
Observability
   │
Security
   │
Terraform
   │
AWS
   │
ECS
   │
EKS
   │
Kubernetes
   │
Helm
   │
CI/CD
```

而且已经不是“学几个工具”，而是一条完整的企业部署链：

```text
Code
 ↓
Maven
 ↓
Test
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
Spring Boot
 ↓
RAG / Agent / MCP
 ↓
Bedrock
 ↓
RDS + pgvector
 ↓
Redis
 ↓
S3 / SQS
 ↓
CloudWatch
```

## Day 42 最重要的三个概念

```text
Terraform
=
AWS 基础设施

Kubernetes
=
Container 编排

Helm
=
Kubernetes 应用发布
```

**Day 43** 下一步建议进入 **EKS Production Networking + AWS Load Balancer Controller + Ingress + Route 53 + ACM HTTPS + ExternalDNS**，把 `api.yourdomain.com → HTTPS → AWS ALB → EKS → Spring Boot` 真正打通。
