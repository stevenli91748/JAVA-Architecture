# Day 41：Kubernetes / Amazon EKS

Day 40 已经完成：

```text
GitHub
 ↓
CI/CD
 ↓
Docker
 ↓
ECR
 ↓
ECS Fargate
 ↓
Production
```

今天开始学习 **Kubernetes（K8s）和 Amazon EKS**。

核心目标：

> 把你现在的 AWS Java + AI 应用，从 ECS 架构扩展到企业级 Kubernetes 架构。

---

# 1. 为什么学习 EKS？

ECS：

```text
AWS
 ↓
ECS Fargate
 ↓
Container
```

EKS：

```text
AWS
 ↓
Kubernetes
 ↓
Pod
 ↓
Container
```

EKS 的优势主要在于：

```text
复杂微服务
多团队
多环境
高级调度
Service Mesh
Kubernetes 生态
多云/混合云
```

---

# 2. ECS vs EKS

|              | ECS Fargate | EKS  |
| ------------ | ----------- | ---- |
| 学习难度         | ⭐⭐          | ⭐⭐⭐⭐ |
| AWS 集成       | 很好          | 很好   |
| Kubernetes   | ❌           | ✅    |
| 运维复杂度        | 低           | 高    |
| 微服务          | ✅           | ✅    |
| AI Workloads | ✅           | ✅    |
| GPU          | ✅           | ✅    |
| 企业招聘         | 高           | 很高   |
| 多云能力         | 一般          | 很强   |
| 推荐初学         | **ECS**     | 第二阶段 |

对你现在的学习路线：

```text
ECS
 ↓
EKS
```

是正确顺序。

---

# 3. 今天最终架构

```text
                       Internet
                           │
                           ▼
                      ALB / Ingress
                           │
                           ▼
                    Amazon EKS Cluster
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
   Spring Boot          MCP Server       Worker
        │
   ┌────┼────────┐
   ▼    ▼        ▼
  RAG  Memory   Agent
   │     │        │
   └─────┼────────┘
         ▼
   PostgreSQL
   + pgvector
         │
         ├── Redis
         ├── S3
         ├── SQS
         └── Bedrock
```

---

# 4. Kubernetes 最核心的概念

今天只需要真正理解：

```text
Cluster
Node
Pod
Deployment
Service
Ingress
ConfigMap
Secret
Namespace
HPA
```

---

# 5. Cluster

Kubernetes：

```text
Cluster
│
├── Control Plane
│
└── Worker Nodes
```

EKS：

```text
AWS
│
└── EKS Cluster
```

AWS 负责 Kubernetes Control Plane 的管理。

---

# 6. Node

Node 就是：

> 实际运行 Pod 的计算节点。

例如：

```text
EKS
│
├── Node 1
├── Node 2
└── Node 3
```

Node 可以是：

```text
EC2
```

也可以使用：

```text
EKS Auto Mode
```

或其他 AWS 管理的计算方式。

---

# 7. Pod

Pod 是 Kubernetes 最基本的运行单位。

```text
Pod
└── Container
```

例如：

```text
Pod
└── Spring Boot
```

通常一个 Java 微服务：

```text
1 Pod
=
1 Java Container
```

---

# 8. Pod 不等于 Container

这是面试经常问的问题。

```text
Container
```

是：

> Docker 容器。

而：

```text
Pod
```

是：

> Kubernetes 调度和管理 Container 的最小单位。

通常：

```text
Pod
└── 1 Container
```

但也可以：

```text
Pod
├── App Container
└── Sidecar Container
```

---

# 9. Deployment

不要直接管理 Pod。

错误：

```text
Pod
```

生产：

```text
Deployment
 ↓
Pod
```

例如：

```text
Deployment
replicas = 3
```

Kubernetes 自动：

```text
Pod 1
Pod 2
Pod 3
```

---

# 10. Pod 挂掉怎么办？

例如：

```text
Pod 1
   X
```

Deployment：

```text
Desired = 3
Current = 2
```

Kubernetes：

```text
Create Pod 4
```

最终：

```text
Pod 2
Pod 3
Pod 4
```

这就是 Kubernetes 的核心能力之一：

> **Desired State**

---

# 11. Service

Pod IP 会变化。

例如：

```text
Pod A
10.0.1.15
```

重启以后：

```text
Pod B
10.0.2.33
```

所以不能让其他服务直接访问 Pod IP。

需要：

```text
Service
 ↓
Pods
```

---

# 12. Service

例如：

```text
spring-api-service
```

对应：

```text
Pod 1
Pod 2
Pod 3
```

访问：

```text
http://spring-api-service:8080
```

Kubernetes 自动负载均衡。

---

# 13. Ingress

外部：

```text
Internet
 ↓
Ingress
 ↓
Service
 ↓
Pod
```

例如：

```text
api.example.com
        ↓
Ingress
        ↓
spring-api-service
        ↓
Spring Boot
```

AWS 通常使用：

```text
AWS Load Balancer Controller
```

将 Kubernetes Ingress 与 AWS ALB/NLB 集成。

---

# 14. Namespace

你的平台至少：

```text
dev
staging
prod
```

例如：

```text
EKS
│
├── dev
├── staging
└── prod
```

更大的企业：

```text
EKS
│
├── team-ai
├── team-order
├── team-payment
└── team-data
```

---

# 15. 第一个 Kubernetes YAML

创建：

```text
deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: ai-api

spec:
  replicas: 2

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
          image: <ECR_IMAGE>

          ports:
            - containerPort: 8080
```

---

# 16. 这个 YAML 是什么意思？

```text
Deployment
│
├── replicas = 2
│
├── Pod
│    └── ai-api
│
└── Pod
     └── ai-api
```

也就是：

```text
2 Java Containers
```

---

# 17. Kubernetes Service

创建：

```text
service.yaml
```

```yaml
apiVersion: v1
kind: Service

metadata:
  name: ai-api-service

spec:
  selector:
    app: ai-api

  ports:
    - port: 80
      targetPort: 8080

  type: ClusterIP
```

---

# 18. 流量

```text
Client
  │
  ▼
Ingress
  │
  ▼
ai-api-service
  │
  ├── Pod 1
  │
  └── Pod 2
```

---

# 19. ConfigMap

不要把普通配置直接写：

```text
Java Code
```

可以：

```text
ConfigMap
```

例如：

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: ai-api-config

data:
  SPRING_PROFILES_ACTIVE: prod
  LOG_LEVEL: INFO
```

---

# 20. Secret

敏感信息：

```text
DB Password
API Key
JWT Secret
```

不要放 ConfigMap。

使用：

```text
Secret
```

但在 AWS 企业生产环境，更推荐：

```text
AWS Secrets Manager
+
External Secrets Operator
```

这样 Secret 的真实生命周期仍由 AWS Secrets Manager 管理。

---

# 21. Kubernetes Secret

概念：

```yaml
apiVersion: v1
kind: Secret
```

但是：

> Kubernetes Secret 默认是 Base64 编码，不等于真正加密安全。

所以企业生产不要把敏感 Secret 明文提交到 Git。

---

# 22. AWS Secrets Manager

推荐：

```text
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

这会成为后面 EKS Security 的重要内容。

---

# 23. Java + Kubernetes

Spring Boot：

```text
Spring Boot
 ↓
Container
 ↓
Pod
 ↓
Deployment
 ↓
Service
```

所以：

```text
Java Developer
+
Docker
+
Kubernetes
```

是现在企业 Java 后端非常重要的一套组合。

---

# 24. Spring Boot Health Check

Kubernetes 不应该只知道：

```text
Container running
```

而应该知道：

```text
Application healthy
```

Spring Boot：

```text
/actuator/health
```

---

# 25. Readiness Probe

Readiness：

> 我现在能不能接收流量？

```yaml
readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 8080

  initialDelaySeconds: 10
  periodSeconds: 10
```

---

# 26. Liveness Probe

Liveness：

> 这个应用是不是已经卡死？

```yaml
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 8080

  initialDelaySeconds: 30
  periodSeconds: 20
```

---

# 27. Readiness vs Liveness

面试一定要会：

```text
Readiness
=
暂时不能接流量

Liveness
=
应用可能已经死掉
```

例如：

```text
数据库暂时不可用
```

不一定需要杀掉 Java。

所以不能简单地：

```text
任何错误
 ↓
Restart
```

---

# 28. Resource Requests

生产 Pod：

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"

  limits:
    cpu: "1"
    memory: "1Gi"
```

意思：

```text
requests
=
我至少需要多少资源

limits
=
最多允许使用多少资源
```

---

# 29. 为什么必须设置 Resource

没有限制：

```text
Pod
 ↓
Memory Leak
 ↓
吃掉 Node
 ↓
其他服务受影响
```

生产：

```text
CPU
Memory
```

都应该有合理的 requests / limits。

---

# 30. HPA

Horizontal Pod Autoscaler：

```text
CPU ↑
 ↓
Pod 2
 ↓
Pod 3
 ↓
Pod 4
```

例如：

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: ai-api

spec:
  minReplicas: 2
  maxReplicas: 10

  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ai-api

  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

---

# 31. AI 应用为什么需要 HPA？

例如：

```text
上午：
20 requests/sec

中午：
200 requests/sec
```

Kubernetes：

```text
2 Pods
 ↓
4 Pods
 ↓
8 Pods
```

流量下降：

```text
8
 ↓
4
 ↓
2
```

---

# 32. 但是 AI Agent 不能只看 CPU

这是 AI 应用和传统 Java 应用的区别。

更好的指标：

```text
CPU
Memory
Requests/sec
Latency
Queue Depth
LLM Token Usage
Agent Execution Time
```

未来可以做：

```text
Custom Metrics
```

---

# 33. AI Worker

你的 RAG ingestion：

```text
S3
 ↓
SQS
 ↓
Worker
```

Kubernetes：

```text
SQS
 ↓
Kubernetes Worker
 ↓
Pod
```

Worker：

```text
1 Pod
2 Pod
5 Pod
```

根据：

```text
Queue Depth
```

扩展。

这比单纯 CPU HPA 更适合 RAG ingestion。

---

# 34. AI API 与 Worker 分离

不要：

```text
AI API
+
Worker
```

全部放一个 Container。

推荐：

```text
EKS
│
├── ai-api
│
├── mcp-server
│
├── ingestion-worker
│
└── evaluation-worker
```

分别扩展。

---

# 35. MCP Server

今天：

```text
EKS
│
└── MCP Server
```

Service：

```text
mcp-service
```

AI API：

```text
ai-api
 ↓
mcp-service
```

不要直接：

```text
Internet
 ↓
MCP
```

除非业务明确要求，并且做好强认证和授权。

---

# 36. EKS Namespace

最终：

```text
EKS
│
├── ai-dev
│
├── ai-staging
│
└── ai-prod
```

Production：

```text
ai-prod
│
├── ai-api
├── mcp-server
├── ingestion-worker
└── evaluation-worker
```

---

# 37. EKS 网络

和 Day 38 一样：

```text
VPC
│
├── Public Subnet
│
└── Private Subnet
      │
      └── EKS Nodes
```

生产不要把 Worker Node 暴露在公网。

---

# 38. EKS + ALB

架构：

```text
Internet
   │
   ▼
AWS ALB
   │
   ▼
Ingress
   │
   ▼
Kubernetes Service
   │
   ▼
Pod
```

---

# 39. EKS + RDS

数据库继续使用：

```text
RDS PostgreSQL
```

不要因为用了 Kubernetes 就把 PostgreSQL 也塞进 Pod。

推荐：

```text
EKS
 │
 └── Spring Boot
          │
          ▼
      RDS PostgreSQL
```

---

# 40. EKS + Redis

同样：

```text
EKS
 │
 └── Spring Boot
          │
          ▼
    ElastiCache Redis
```

而不是：

```text
Redis Pod
```

作为第一版生产方案。

---

# 41. EKS + S3

```text
Spring Boot
     │
     ▼
     S3
```

通过：

```text
IAM
```

授权。

---

# 42. EKS + Bedrock

最终：

```text
Spring Boot Pod
      │
      ▼
   Bedrock
```

不要把：

```text
AWS Access Key
```

写进 Pod。

---

# 43. EKS Pod Identity

推荐学习：

```text
EKS Pod Identity
```

架构：

```text
Pod
 │
 ▼
EKS Pod Identity
 │
 ▼
IAM Role
 │
 ├── S3
 ├── Bedrock
 └── Secrets Manager
```

这比在 Kubernetes Secret 中保存 AWS Access Key 更安全。

---

# 44. Day 41 Security

现在安全模型变成：

```text
User
 ↓
ALB
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
IAM
 ↓
AWS Services
```

再加：

```text
NetworkPolicy
RBAC
Pod Security
Secrets
```

---

# 45. Kubernetes RBAC

例如：

```text
Developer
 ↓
Namespace: dev
```

不能：

```text
prod
```

Production：

```text
Admin
 ↓
ai-prod
```

权限最小化。

---

# 46. NetworkPolicy

例如：

```text
ai-api
 ↓
mcp-server
```

允许。

但是：

```text
internet
 ↓
mcp-server
```

拒绝。

这就是：

> Kubernetes NetworkPolicy。

---

# 47. Day 41 最重要的 Kubernetes 思维

不要记 YAML。

真正理解：

```text
Desired State
```

例如：

```text
我需要 3 个 ai-api Pods
```

Kubernetes：

```text
不断检查实际状态
 ↓
偏离 Desired State
 ↓
自动修复
```

---

# 48. ECS → Kubernetes 对照

| ECS             | Kubernetes              |
| --------------- | ----------------------- |
| Cluster         | Cluster                 |
| Task            | Pod                     |
| Task Definition | Deployment/Pod Spec     |
| Service         | Service                 |
| ALB             | Ingress + ALB           |
| Auto Scaling    | HPA                     |
| Task Role       | Pod Identity / IAM      |
| CloudWatch      | CloudWatch / Prometheus |
| ECR             | ECR                     |
| Fargate         | Fargate / Nodes         |

注意：

> Kubernetes 的 Pod 和 ECS Task 不是严格一一对应，但作为入门理解非常有帮助。

---

# 49. Day 41 实战

先安装：

```bash
kubectl version --client
```

然后：

```bash
aws eks update-kubeconfig \
  --region us-west-2 \
  --name ai-platform
```

检查：

```bash
kubectl get nodes
```

如果成功：

```text
NAME
ip-10-0-1-xx
ip-10-0-2-xx
```

---

# 50. 部署 Java API

```bash
kubectl apply \
  -f deployment.yaml
```

查看：

```bash
kubectl get pods
```

应该：

```text
NAME                       READY
ai-api-7c9d8d9c-x1k2       1/1
ai-api-7c9d8d9c-x3m4       1/1
```

---

# 51. 查看 Deployment

```bash
kubectl get deployment
```

得到：

```text
NAME      READY   UP-TO-DATE
ai-api    2/2     2
```

---

# 52. 查看 Service

```bash
kubectl get svc
```

：

```text
ai-api-service
```

---

# 53. 查看 Pod Logs

```bash
kubectl logs \
  deployment/ai-api
```

Java：

```text
Started Application
```

---

# 54. 进入 Pod

```bash
kubectl exec \
  -it <pod-name> \
  -- /bin/sh
```

用于：

```text
Debug
```

但是生产环境不要把 `exec` 当常规运维手段。

---

# 55. 更新 Java

修改：

```text
Spring Boot
```

然后：

```text
Docker Build
 ↓
ECR
 ↓
Update Deployment
```

例如：

```bash
kubectl set image \
  deployment/ai-api \
  ai-api=<ECR_IMAGE>:8f42c91
```

---

# 56. Kubernetes Rolling Update

执行：

```bash
kubectl rollout status \
  deployment/ai-api
```

查看：

```text
deployment successfully rolled out
```

---

# 57. Rollback

如果新版本错误：

```bash
kubectl rollout undo \
  deployment/ai-api
```

查看：

```bash
kubectl rollout history \
  deployment/ai-api
```

这就是：

```text
Deploy
 ↓
Fail
 ↓
Rollback
```

---

# 58. Day 41 CI/CD

现在从 Day 40：

```text
GitHub
 ↓
Docker
 ↓
ECR
 ↓
ECS
```

升级成：

```text
GitHub
 ↓
CI
 ↓
Docker
 ↓
ECR
 ↓
EKS
 ↓
Deployment
 ↓
Service
 ↓
Ingress
```

---

# 59. 最终 EKS AI 平台

```text
                         GitHub
                            │
                           CI/CD
                            │
                            ▼
                           ECR
                            │
                            ▼
                     Amazon EKS
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
      AI API              MCP                Worker
        │                   │                   │
        └──────────────┬────┘                   │
                       ▼                        ▼
                     Agent                    SQS
                       │                        │
                ┌──────┼──────┐                │
                ▼      ▼      ▼                │
               RAG   Memory  Bedrock            │
                │      │
                └──┬───┘
                   ▼
             PostgreSQL
              + pgvector

                   +
                   
              Redis
              S3
              CloudWatch
```

---

# 60. Day 41 必做任务

今天不要试图把整个 EKS 都学完。

完成下面 **10 个任务**即可：

```text
[ ] 1. 理解 Cluster
[ ] 2. 理解 Node
[ ] 3. 理解 Pod
[ ] 4. 创建 Deployment
[ ] 5. 创建 Service
[ ] 6. 创建 Namespace
[ ] 7. 配置 Readiness Probe
[ ] 8. 配置 Liveness Probe
[ ] 9. 配置 Resource Request/Limit
[ ] 10. 配置 HPA
```

---

# 61. Day 41 面试题

### Q1：Pod 和 Container 有什么区别？

```text
Container
=
运行应用

Pod
=
Kubernetes 调度最小单位
```

---

### Q2：为什么不能直接访问 Pod IP？

因为：

```text
Pod 会被销毁/重新创建
IP 会变化
```

所以：

```text
Service
```

提供稳定访问入口。

---

### Q3：Deployment 做什么？

```text
管理 Pod
保证 Desired State
支持 Rolling Update
支持 Rollback
```

---

### Q4：Readiness vs Liveness？

```text
Readiness
→ 能不能接流量

Liveness
→ 是不是应该重启
```

---

### Q5：HPA 是什么？

```text
根据 Metrics
自动增加/减少 Pod
```

---

### Q6：为什么 RDS 不放 Kubernetes？

因为：

```text
RDS
=
Managed Database
```

AWS 已经负责：

```text
Backup
HA
Patch
Storage
```

没有必要自己运营 PostgreSQL StatefulSet 作为第一版生产方案。

---

# 62. Day 41 最重要的架构认知

你现在有两套 AWS 生产方案：

### 方案 A：ECS

```text
ALB
 ↓
ECS Fargate
 ↓
Spring Boot
```

### 方案 B：EKS

```text
ALB
 ↓
Ingress
 ↓
EKS
 ↓
Deployment
 ↓
Pod
 ↓
Spring Boot
```

底层数据服务仍然：

```text
RDS
Redis
S3
SQS
Bedrock
```

---

# 63. Day 41 → Day 42

Day 41：

```text
Kubernetes Fundamentals
```

Day 42 建议进入：

> **EKS + Helm + Kubernetes Production Deployment**

重点学习：

```text
Helm
Chart
values.yaml
Ingress
AWS Load Balancer Controller
External Secrets
HPA
PodDisruptionBudget
Rolling Update
EKS Auto Scaling
```

最终做到：

```text
helm install ai-platform
```

一次性部署：

```text
AI API
MCP Server
Worker
Service
Ingress
Config
HPA
```

这一步完成后，你的 **Java + AI + AWS + Kubernetes** 技术栈就真正开始接近美国企业 AI Engineer / Backend Engineer 的生产级要求。
