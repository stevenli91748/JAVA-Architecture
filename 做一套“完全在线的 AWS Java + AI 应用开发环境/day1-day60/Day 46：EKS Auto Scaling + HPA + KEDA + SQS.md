# Day 46：EKS Auto Scaling + HPA + KEDA + SQS

Day 45 已经完成 Observability：

```text
Metrics
Logs
Traces
Alerts
```

今天解决生产环境的另一个核心问题：

> **流量增加时自动扩容，流量下降时自动缩容；RAG Worker 根据 SQS 队列长度自动扩容。**

最终做到：

```text
                         Internet
                            │
                            ▼
                           ALB
                            │
                            ▼
                         EKS
                            │
                    ┌───────┴────────┐
                    ▼                ▼
                 AI API           Worker
                    │                │
                    │                ▼
                    │               SQS
                    │                ▲
                    │                │
                    └──────────────► Queue
```

---

# 1. 今天要掌握什么

```text
① HPA
② CPU/Memory Scaling
③ Custom Metrics
④ KEDA
⑤ SQS Scaling
⑥ Cluster Autoscaler / Karpenter
⑦ Scale Out
⑧ Scale In
⑨ Min/Max Replicas
⑩ Cooldown / Stabilization
```

---

# 2. 三层自动扩缩容

这是今天最重要的概念。

你的 EKS 平台实际上有 **3 层 Scaling**：

```text
                 Application
                     │
                     ▼
              ┌──────────────┐
              │ HPA / KEDA   │
              └───────┬──────┘
                      │
                      ▼
                   Pods
                      │
                      ▼
              ┌──────────────┐
              │ Node Scaling │
              └───────┬──────┘
                      │
                      ▼
                 EC2 Nodes
```

分别是：

```text
HPA
=
Pod 扩缩容

KEDA
=
事件驱动 Pod 扩缩容

Karpenter
=
Node 扩缩容
```

---

# 3. AI API 使用 HPA

例如：

```text
正常：
2 Pods

流量增加：
4 Pods

高峰：
8 Pods
```

HPA：

```text
CPU / Memory / Custom Metrics
              ↓
             HPA
              ↓
             Pods
```

---

# 4. RAG Worker 使用 KEDA

RAG ingestion：

```text
S3
 ↓
SQS
 ↓
Worker
```

如果：

```text
SQS = 10 messages
```

可能：

```text
2 Workers
```

如果：

```text
SQS = 10,000 messages
```

自动：

```text
2
 ↓
5
 ↓
10
 ↓
20 Workers
```

这就是 KEDA 最有价值的地方。

---

# 5. HPA vs KEDA

|              | HPA     | KEDA     |
| ------------ | ------- | -------- |
| CPU          | ✅       | ✅        |
| Memory       | ✅       | ✅        |
| SQS          | ❌ 原生不方便 | ✅        |
| Kafka        | ❌       | ✅        |
| Redis        | ❌       | ✅        |
| Prometheus   | 可配      | ✅        |
| Event-driven | 一般      | **很强**   |
| AI Worker    | 一般      | **非常适合** |

---

# 6. AI API 的 Scaling

AI API：

```text
Browser
 ↓
ALB
 ↓
EKS
 ↓
AI API
```

假设：

```text
100 requests/sec
```

2 Pods：

```text
Pod 1
Pod 2
```

突然：

```text
500 requests/sec
```

HPA：

```text
2
 ↓
4
 ↓
8
```

---

# 7. HPA 最简单版本

```yaml id="0v4q4a"
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

metadata:
  name: ai-api
  namespace: ai-prod

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ai-api

  minReplicas: 2
  maxReplicas: 10

  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

意思：

```text
最少 2
最多 10
CPU 目标 70%
```

---

# 8. Memory HPA

也可以：

```yaml id="7v1s6r"
metrics:

  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 75
```

但不要简单地把 CPU 和 Memory 越多越好地堆进去。

应该根据实际负载测试确定指标。

---

# 9. HPA 的计算思路

简单理解：

```text
Current Utilization
        │
        ▼
Target Utilization
        │
        ▼
Desired Replicas
```

例如：

```text
Current = 140%
Target = 70%
```

大约：

```text
2 Pods
 ×
140 / 70
=
4 Pods
```

实际 HPA 还会受到取整、稳定窗口和其他控制逻辑影响。

---

# 10. 为什么 AI API 不能只看 CPU？

因为：

```text
AI API
```

可能 CPU 只有：

```text
30%
```

但是：

```text
LLM latency
=
5 seconds
```

原因可能是：

```text
Bedrock
 ↓
等待模型
```

CPU 并没有很高。

所以 AI 应用更适合考虑：

```text
CPU
+
Memory
+
Requests/sec
+
Concurrency
+
Latency
```

---

# 11. Custom Metrics

例如：

```text
http_requests_per_second
```

或者：

```text
active_requests
```

然后：

```text
Prometheus
 ↓
Custom Metrics
 ↓
HPA
```

---

# 12. 更适合 AI 的指标

我建议你的 AI API 后续重点监控：

```text
ai_active_requests
ai_requests_per_second
ai_request_latency_p95
ai_llm_latency
```

例如：

```text
Active Requests
      │
      ▼
     HPA
      │
      ▼
Pods
```

---

# 13. 但今天先不要复杂化

Day 46 第一阶段：

```text
AI API
 ↓
CPU HPA
```

第二阶段：

```text
AI API
 ↓
Custom Metrics
```

Worker：

```text
SQS
 ↓
KEDA
```

这样学习最合理。

---

# 14. KEDA

KEDA：

> Kubernetes Event-driven Autoscaling

核心思想：

```text
Event
 ↓
Scaler
 ↓
Kubernetes
 ↓
Pods
```

---

# 15. SQS + KEDA

你的 RAG pipeline：

```text
             Documents
                 │
                 ▼
                 S3
                 │
                 ▼
                 SQS
                 │
                 ▼
                KEDA
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
    Worker 1  Worker 2  Worker 3
                 │
                 ▼
            Embedding
                 │
                 ▼
          PostgreSQL
           + pgvector
```

---

# 16. KEDA ScaledObject

核心配置：

```yaml id="s3e1n5"
apiVersion: keda.sh/v1alpha1
kind: ScaledObject

metadata:
  name: ingestion-worker
  namespace: ai-prod

spec:
  scaleTargetRef:
    name: ingestion-worker

  minReplicaCount: 2
  maxReplicaCount: 20

  triggers:

    - type: aws-sqs-queue

      metadata:
        queueURL: https://sqs.us-west-2.amazonaws.com/123456789012/rag-ingestion
        queueLength: "10"
        awsRegion: us-west-2
```

---

# 17. 这个配置是什么意思？

```text
SQS Queue
```

如果：

```text
10 messages
```

大约：

```text
1 Worker
```

如果：

```text
100 messages
```

大约：

```text
10 Workers
```

具体比例由：

```text
queueLength
```

以及 KEDA 的实际计算逻辑决定。

---

# 18. 为什么 KEDA 很适合 RAG？

RAG ingestion 的负载不是：

```text
CPU
```

而是：

```text
Documents
 ↓
Queue
```

例如：

```text
普通时间：

SQS = 0
```

不需要大量 Worker。

突然上传：

```text
100,000 documents
```

：

```text
SQS
 ↓
100,000 messages
```

KEDA：

```text
Worker
2
 ↓
10
 ↓
20
```

---

# 19. KEDA Scale to Zero

KEDA 可以进一步：

```text
SQS = 0
 ↓
Worker = 0
```

然后：

```text
SQS = 100
 ↓
Worker
0 → 10
```

但是生产是否使用 Scale-to-Zero，要考虑：

```text
Cold Start
Latency
Availability
```

对于关键生产 Worker，我更建议先：

```text
minReplicaCount = 1 or 2
```

---

# 20. AI API 不建议轻易 Scale to Zero

因为：

```text
User
 ↓
Request
 ↓
Pod启动
 ↓
Java启动
 ↓
Spring Boot
 ↓
LLM
```

Cold Start 会增加延迟。

所以：

```text
AI API
min = 2
```

更合理。

---

# 21. Worker 可以 Scale to Zero

例如：

```text
Evaluation Worker
```

非实时：

```text
Queue = 0
```

可以：

```text
0 Pods
```

有任务：

```text
Queue > 0
 ↓
Pods
```

这种场景非常适合 KEDA。

---

# 22. KEDA 与 IAM

KEDA 需要访问：

```text
SQS
```

不要：

```text
AWS Access Key
```

应该：

```text
KEDA
 ↓
EKS Pod Identity
 ↓
IAM Role
 ↓
SQS
```

---

# 23. Worker IAM

Worker：

```text
SQS
```

只给：

```text
sqs:ReceiveMessage
sqs:DeleteMessage
sqs:GetQueueAttributes
```

如果需要：

```text
SQS SendMessage
```

再增加。

---

# 24. Queue Visibility Timeout

这是 Worker 很重要的概念。

Worker：

```text
Receive Message
 ↓
Processing
```

如果 Worker：

```text
10 minutes
```

而 SQS：

```text
Visibility Timeout = 30 seconds
```

消息可能重新出现。

所以：

```text
Visibility Timeout
>
Maximum Processing Time
```

通常还要结合 heartbeat / visibility extension 等机制设计。

---

# 25. AI Document Processing

例如：

```text
PDF
 ↓
OCR
 ↓
Chunk
 ↓
Embedding
 ↓
Vector DB
```

可能需要：

```text
30 sec
```

甚至：

```text
5 min
```

因此：

```text
SQS Visibility Timeout
```

必须根据实际处理时间设置。

---

# 26. Dead Letter Queue

Worker 失败：

```text
Message
 ↓
Worker
 ↓
FAIL
```

不要无限重试。

应该：

```text
Retry
 ↓
Retry
 ↓
Retry
 ↓
DLQ
```

---

# 27. DLQ

```text
SQS
 │
 ├── Main Queue
 │
 └── Dead Letter Queue
```

例如：

```text
rag-ingestion
rag-ingestion-dlq
```

---

# 28. 为什么 AI Worker 特别需要 DLQ？

例如：

```text
Bad PDF
```

或者：

```text
Corrupted document
```

或者：

```text
Embedding API failure
```

如果无限重试：

```text
CPU ↑
Cost ↑
Queue ↑
```

所以：

```text
maxReceiveCount
```

必须合理配置。

---

# 29. Karpenter

现在进入第三层：

```text
Pod
 ↓
Node
```

如果：

```text
Pods
2 → 20
```

但是 Node：

```text
只有 2 台
```

可能：

```text
Pod Pending
```

因为：

```text
没有 CPU / Memory
```

---

# 30. Karpenter

Karpenter：

> 根据 Pod 的资源需求自动提供合适的计算节点。

架构：

```text
HPA / KEDA
      │
      ▼
Pods
      │
      ▼
Pending
      │
      ▼
Karpenter
      │
      ▼
EC2 Nodes
```

---

# 31. 三层 Scaling

一定记住：

```text
                    Traffic
                       │
                       ▼
                     HPA
                       │
                       ▼
                     Pods
                       │
                 ┌─────┴─────┐
                 │           │
               Ready       Pending
                             │
                             ▼
                          Karpenter
                             │
                             ▼
                           Nodes
```

而 Worker：

```text
SQS
 ↓
KEDA
 ↓
Worker Pods
 ↓
Karpenter
 ↓
Nodes
```

---

# 32. 为什么需要 Karpenter？

例如：

```text
当前 Nodes = 3
```

突然：

```text
Worker Pods
2 → 20
```

现有 Node 放不下。

Kubernetes：

```text
Pod Pending
```

Karpenter：

```text
Provision Node
```

然后：

```text
Pod
 ↓
Node
 ↓
Running
```

---

# 33. Node Scaling 不是 Pod Scaling

这是面试重点：

```text
HPA
=
Pod

KEDA
=
Event → Pod

Karpenter
=
Node
```

---

# 34. AI GPU Workload

以后如果你做：

```text
Local LLM
Inference
Vision
Robotics
```

可能：

```text
GPU
```

Karpenter 可以根据：

```text
Pod
resources:
  nvidia.com/gpu: 1
```

选择合适节点。

你的 AI Robotics 路线后面会用到这个概念。

---

# 35. Java API Resource

Day 44：

```yaml id="ztdg6v"
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"

  limits:
    cpu: "1"
    memory: "1Gi"
```

Day 46：

> 这些 requests 是 Autoscaling 的基础。

---

# 36. 为什么 Requests 很重要？

假设：

```text
Pod Request
CPU = 1
```

Node：

```text
4 CPU
```

理论：

```text
4 Pods
```

如果：

```text
Request = 2 CPU
```

则：

```text
2 Pods
```

所以：

> Resource Request 会直接影响 Node Capacity。

---

# 37. HPA + Karpenter

完整：

```text
Traffic
 ↓
HPA
 ↓
Pod 2 → 10
 ↓
Node Capacity 不够
 ↓
Karpenter
 ↓
Add Node
 ↓
Pods Scheduled
```

这是真正的自动扩容。

---

# 38. Scale In

流量下降：

```text
10 Pods
 ↓
HPA
 ↓
4 Pods
```

Karpenter：

```text
Node
 ↓
Empty
 ↓
Consolidate
```

释放 Node。

---

# 39. Scale In 不要太快

如果：

```text
流量
100
 ↓
50
 ↓
100
```

如果马上：

```text
Scale Down
```

可能：

```text
2 Pods
 ↓
10 Pods
 ↓
2 Pods
 ↓
10 Pods
```

不断抖动。

叫：

> **Thrashing**

---

# 40. Stabilization Window

HPA 可以：

```text
scaleDown:
stabilizationWindowSeconds
```

例如：

```yaml id="01v27t"
behavior:
  scaleDown:
    stabilizationWindowSeconds: 300
```

意思：

```text
Scale Down
 ↓
等待 5 min
```

避免快速抖动。

---

# 41. Scale Up 可以更快

生产一般思路：

```text
Scale Up
=
快

Scale Down
=
慢
```

因为：

```text
宁愿短时间多几个 Pod
```

也不要：

```text
用户突然遇到大量 5xx
```

---

# 42. AI API 的推荐初始参数

不是固定答案，但作为实验起点：

```text id="9klx4f"
minReplicas = 2
maxReplicas = 10

CPU Target = 60~70%

Scale Down
= 5 min stabilization
```

然后通过压测调整。

---

# 43. RAG Worker 推荐初始参数

例如：

```text id="t9q3w6"
min = 1~2
max = 20

Queue Target
= 10 messages / worker
```

但真实值必须通过：

```text
Processing Time
Queue Arrival Rate
Worker Throughput
```

计算。

---

# 44. Worker Capacity 计算

假设：

```text
1 Worker
=
每分钟处理 10 documents
```

现在：

```text
Queue
=
1,000 documents
```

需要：

```text
100 Worker-minutes
```

如果希望：

```text
10 minutes
```

完成：

```text
100 / 10
=
10 Workers
```

所以：

```text
maxReplicaCount >= 10
```

---

# 45. 更实际的公式

可以用：

```text
Required Workers
≈
Incoming Rate
/
Worker Processing Rate
```

例如：

```text
Incoming
= 100 docs/min

Worker
= 10 docs/min
```

需要：

```text
10 Workers
```

再考虑：

```text
Safety Margin
```

例如：

```text
12~15 Workers
```

---

# 46. Day 46 实战：HPA

先确保：

```bash id="q2w5at"
kubectl get deployment \
  -n ai-prod
```

创建：

```text id="0y9x6f"
ai-api-hpa.yaml
```

然后：

```bash id="2z7r4p"
kubectl apply \
  -f ai-api-hpa.yaml
```

查看：

```bash id="w0m6ca"
kubectl get hpa \
  -n ai-prod
```

---

# 47. HPA 输出

例如：

```text id="2ox9f4"
NAME     REFERENCE            TARGET
ai-api   Deployment/ai-api    42%/70%
```

表示：

```text
Current CPU = 42%
Target = 70%
```

---

# 48. 压测

可以使用：

```text id="yb8y6k"
k6
```

或者：

```text id="3n5f7p"
Apache JMeter
```

第一阶段：

```text
100 requests/sec
```

然后：

```text
300
500
1000
```

观察：

```text
Pods
CPU
Latency
Error Rate
```

---

# 49. 压测不要直接打 Production

应该：

```text id="1tx3qk"
Dev
 ↓
Staging
 ↓
Load Test
```

不要：

```text id="x8f6m2"
Production
 ↓
1000 req/sec
```

除非已经有严格的容量测试方案和受控窗口。

---

# 50. Day 46 KEDA 实战

安装 KEDA 后：

```text id="s8q2l4"
kubectl get pods \
  -n keda
```

确认：

```text id="e7n3p5"
keda-operator
```

运行。

---

# 51. KEDA ScaledObject

```yaml id="f5k8q1"
apiVersion: keda.sh/v1alpha1
kind: ScaledObject

metadata:
  name: ingestion-worker
  namespace: ai-prod

spec:
  scaleTargetRef:
    name: ingestion-worker

  minReplicaCount: 2
  maxReplicaCount: 20

  pollingInterval: 30

  cooldownPeriod: 300

  triggers:
    - type: aws-sqs-queue

      metadata:
        queueURL: <SQS_QUEUE_URL>
        queueLength: "10"
        awsRegion: us-west-2
```

---

# 52. KEDA 参数

### pollingInterval

```text
30 seconds
```

KEDA 多久检查一次。

---

### cooldownPeriod

```text
300 seconds
```

没有事件后等待多久再缩容。

---

### minReplicaCount

```text
2
```

最少 Worker。

---

### maxReplicaCount

```text
20
```

最大 Worker。

---

# 53. KEDA Scaling

例如：

```text
SQS
100 messages
```

：

```text
Workers
2 → 10
```

然后：

```text
SQS
100 → 0
```

：

```text
Workers
10 → 2
```

---

# 54. SQS + DLQ

完整：

```text id="08b8z8"
                  S3
                   │
                   ▼
              Main Queue
                   │
                   ▼
                 KEDA
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Worker   Worker   Worker
          │
          ▼
       Embedding
          │
          ▼
    PostgreSQL/pgvector
          
Failure
   │
   ▼
 Retry
   │
   ▼
   DLQ
```

---

# 55. AI Evaluation Worker

不仅 RAG 可以：

```text id="qk0qtl"
Evaluation Queue
 ↓
KEDA
 ↓
Evaluation Workers
```

例如：

```text
100,000 test cases
```

自动：

```text
2
 ↓
20
 ↓
50
```

完成以后：

```text
50
 ↓
2
```

---

# 56. Agent Async Jobs

同样：

```text
User Request
 ↓
SQS
 ↓
Agent Worker
 ↓
LLM
 ↓
Result
```

可以实现：

```text
异步 Agent
```

例如：

```text
长时间 Research
Document Analysis
Batch Summarization
```

不必让 HTTP Request 一直保持连接。

---

# 57. 同步 vs 异步 AI

### 同步

```text
User
 ↓
API
 ↓
LLM
 ↓
Response
```

适合：

```text
Chat
Q&A
```

### 异步

```text
User
 ↓
API
 ↓
SQS
 ↓
Worker
 ↓
LLM
 ↓
Result
```

适合：

```text
Long-running Agent
Batch
Document Processing
```

---

# 58. Day 46 最终架构

```text id="6p8k3m"
                         INTERNET
                            │
                            ▼
                           ALB
                            │
                            ▼
                         EKS
                            │
                    ┌───────┴────────┐
                    ▼                ▼
                 AI API           Worker
                    │                │
                  HPA              KEDA
                    │                │
                2 → 10 Pods      SQS Queue
                    │                │
                    │          0 → 20 Pods
                    │                │
                    └────────┬───────┘
                             │
                             ▼
                        Karpenter
                             │
                             ▼
                         EC2 Nodes
```

---

# 59. 三个自动扩容器

今天一定记住：

```text
HPA
→ Application Pods

KEDA
→ Event-driven Pods

Karpenter
→ Infrastructure Nodes
```

---

# 60. Day 46 企业级 AI 架构

最终：

```text id="fb5a4g"
                         AWS
                          │
             ┌────────────┴────────────┐
             │                         │
           ALB                       SQS
             │                         │
             ▼                         ▼
           EKS                       KEDA
             │                         │
      ┌──────┴──────┐                  │
      ▼             ▼                  ▼
    AI API         MCP              Workers
      │             │                  │
     HPA            │                  │
      │             │                  │
      └──────┬──────┴──────────────────┘
             │
             ▼
         Karpenter
             │
             ▼
          EC2 Nodes
             │
       ┌─────┼─────┐
       ▼     ▼     ▼
    Bedrock RDS   S3
```

---

# 61. Day 46 必做任务

```text
[ ] 1. 配置 AI API HPA
[ ] 2. 设置 minReplicas
[ ] 3. 设置 maxReplicas
[ ] 4. 配置 CPU Target
[ ] 5. 设置 Scale Down Stabilization
[ ] 6. 安装 KEDA
[ ] 7. 创建 SQS Queue
[ ] 8. 创建 Worker Deployment
[ ] 9. 创建 KEDA ScaledObject
[ ] 10. 测试 Queue → Worker 自动扩容
[ ] 11. 配置 DLQ
[ ] 12. 理解 Karpenter
```

---

# 62. Day 46 验收

最终测试：

### Test A

```text
AI API
2 Pods
```

增加流量：

```text
Load Test
 ↓
CPU ↑
 ↓
HPA
 ↓
4 Pods
```

---

### Test B

```text
SQS
0
```

：

```text
Worker
2
```

增加：

```text
100 messages
```

：

```text
KEDA
 ↓
Worker
```

自动增加。

---

### Test C

清空：

```text
SQS
100 → 0
```

等待：

```text
Cooldown
```

Worker：

```text
10 → 2
```

---

# 63. Day 46 面试题

### Q1：HPA 和 KEDA 区别？

```text
HPA
→ Kubernetes Resource / Metrics Scaling

KEDA
→ Event-driven Scaling
```

---

### Q2：为什么 SQS Worker 更适合 KEDA？

因为核心负载：

```text
Queue Depth
```

而不是：

```text
CPU
```

---

### Q3：HPA 扩容了 Pod，但是 Node 没资源怎么办？

```text
Pod Pending
 ↓
Karpenter
 ↓
New Node
 ↓
Pod Scheduled
```

---

### Q4：为什么 Scale Down 要慢一点？

防止：

```text
Traffic Spike
 ↓
Scale Up
 ↓
Traffic Drop
 ↓
Scale Down
 ↓
Traffic Spike
 ↓
再次 Scale Up
```

也就是：

> Scaling Thrashing。

---

### Q5：为什么 AI API 不一定适合 Scale to Zero？

因为：

```text
Java/Spring Boot
+
Model/SDK Initialization
+
Connection Initialization
```

可能产生明显 Cold Start。

---

# 64. Day 46 最核心的一张图

```text
                  TRAFFIC
                     │
                     ▼
                    HPA
                     │
                     ▼
                  AI Pods
                     │
                     ▼
              ┌──────┴──────┐
              │             │
           Capacity       Pending
                            │
                            ▼
                        Karpenter
                            │
                            ▼
                           Nodes


                  SQS QUEUE
                     │
                     ▼
                    KEDA
                     │
                     ▼
                 Worker Pods
                     │
                     ▼
                  Karpenter
                     │
                     ▼
                    Nodes
```

你现在已经完成：

```text
Day 40  CI/CD
Day 41  Kubernetes
Day 42  Helm
Day 43  Networking
Day 44  Security
Day 45  Observability
Day 46  Auto Scaling
```

下一步 **Day 47** 建议进入 **AWS RDS PostgreSQL + pgvector + Redis 的生产级数据库架构**：包括 Multi-AZ、connection pool、HikariCP、Read Replica、RDS Proxy、pgvector、Vector Index、Redis Cache，以及如何让 **Spring Boot + RAG + EKS** 在高并发下稳定运行。
