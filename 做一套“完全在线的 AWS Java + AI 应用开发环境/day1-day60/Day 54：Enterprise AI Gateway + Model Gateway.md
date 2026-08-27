# Day 54：Enterprise AI Gateway + Model Gateway

Day 53 解决的是：

```text
AI 是否可靠？
```

Day 54 解决：

> **企业里有多个 AI 模型时，Java 应用到底怎么统一管理？**

今天把：

```text
Spring Boot
   ↓
OpenAI
```

升级成：

```text
                 Java Application
                        │
                        ▼
                  AI Gateway
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       Bedrock        OpenAI        Gemini
          │             │             │
          ▼             ▼             ▼
       Model A        Model B       Model C
```

---

# 1. 为什么需要 AI Gateway？

如果每个 Service 自己调用模型：

```text
Order Service → OpenAI
Customer Service → Bedrock
RAG Service → Gemini
Agent Service → OpenAI
```

很快会出现：

```text
API Key 分散
模型配置分散
成本无法统计
Rate Limit 难管理
Fallback 困难
模型切换困难
```

企业应该：

```text
所有 AI Request
       ↓
   AI Gateway
       ↓
 Model Router
       ↓
实际模型
```

---

# 2. 今天最终架构

```text
                       USER
                         │
                         ▼
                   Spring Boot
                         │
                         ▼
                    AI Gateway
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
          Model Router  Policy     Cache
              │
       ┌──────┼────────┐
       ▼      ▼        ▼
   Bedrock  OpenAI   Gemini
       │      │        │
       └──────┼────────┘
              ▼
         AI Response
              │
       ┌──────┼─────────┐
       ▼      ▼         ▼
    Metrics  Audit     Cost
```

---

# 3. AI Gateway 的 10 个核心功能

今天完成：

```text
① Model Abstraction
② Model Routing
③ Fallback
④ Retry
⑤ Rate Limit
⑥ Circuit Breaker
⑦ Token Tracking
⑧ Cost Tracking
⑨ Caching
⑩ A/B Testing
```

---

# 4. 第一原则：业务代码不能绑定具体模型

不要：

```java
OpenAiChatModel model;
```

散落在业务代码。

应该：

```java
AiGateway gateway;
```

业务：

```java
String answer =
    aiGateway.chat(request);
```

---

# 5. AI Gateway Interface

```java
public interface AiGateway {

    AiResponse chat(
        AiRequest request
    );

    Flux<String> stream(
        AiRequest request
    );
}
```

---

# 6. AiRequest

```java
public record AiRequest(

    String tenantId,

    String userId,

    String useCase,

    String model,

    List<Message> messages,

    Double temperature,

    Integer maxTokens

) {}
```

---

# 7. AiResponse

```java
public record AiResponse(

    String requestId,

    String model,

    String content,

    Usage usage,

    long latencyMs

) {}
```

---

# 8. Usage

```java
public record Usage(

    long inputTokens,

    long outputTokens,

    long totalTokens

) {}
```

以后成本计算：

```text
Input Tokens
+
Output Tokens
+
Model Pricing
```

---

# 9. Model Registry

建立：

```text
Model Registry
```

例如：

```text
model:
  gpt-x
  claude-x
  gemini-x
  bedrock-x
```

每个模型记录：

```text
Provider
Model ID
Region
Capabilities
Price
Context Window
Enabled
```

---

# 10. ModelConfig

```java
public record ModelConfig(

    String provider,

    String modelId,

    String region,

    Set<String> capabilities,

    boolean enabled

) {}
```

---

# 11. Capability

不要只写：

```text
model = xxx
```

应该知道：

```text
TEXT
VISION
TOOL_CALLING
JSON
EMBEDDING
LONG_CONTEXT
```

例如：

```text
Model A
├── TEXT
├── TOOL_CALLING
└── JSON
```

---

# 12. Model Router

这是今天最核心的组件。

```text
Request
   ↓
Model Router
   ↓
选择最佳模型
```

根据：

```text
Use Case
Cost
Latency
Capability
Availability
Tenant
```

选择。

---

# 13. Model Routing Example

```text
Simple Classification
        ↓
Small / Cheap Model
```

复杂 Agent：

```text
Complex Reasoning
        ↓
Premium Model
```

RAG：

```text
RAG Answer
        ↓
Model with strong grounding
```

Vision：

```text
Image
 ↓
Vision Model
```

---

# 14. Routing Rule

```java
public ModelConfig route(
        AiRequest request) {

    if ("classification"
            .equals(request.useCase())) {

        return fastModel;
    }

    if ("agent"
            .equals(request.useCase())) {

        return reasoningModel;
    }

    return defaultModel;
}
```

生产环境再逐步升级成 Policy Engine。

---

# 15. 更好的 Routing

例如：

```text
Request
 │
 ├── useCase
 ├── complexity
 ├── latencyRequirement
 ├── budget
 └── capabilities
```

Router：

```text
Model A
Model B
Model C
```

打分：

```text
Score =
quality
+
availability
-
cost
-
latency
```

---

# 16. Model Fallback

最重要的生产能力之一：

```text
Primary Model
      │
      ▼
   FAILURE
      │
      ▼
Fallback Model
```

例如：

```text
OpenAI
 ↓
429
 ↓
Bedrock
 ↓
Success
```

---

# 17. Fallback Chain

```text
Model A
  ↓
Model B
  ↓
Model C
  ↓
FAIL
```

但是：

> **不是所有错误都应该 Fallback。**

---

# 18. 可以 Fallback 的错误

例如：

```text
429
502
503
504
Timeout
Provider unavailable
```

通常适合考虑 Fallback。

---

# 19. 不应该 Fallback 的错误

例如：

```text
Invalid Request
Invalid API Key
Permission Denied
Invalid Schema
```

否则：

```text
Bad Request
 ↓
Model B
 ↓
Model C
```

只是在浪费钱。

---

# 20. Circuit Breaker

假设：

```text
OpenAI
```

连续：

```text
10 failures
```

不要继续请求。

Circuit：

```text
CLOSED
   ↓
FAILURES
   ↓
OPEN
   ↓
WAIT
   ↓
HALF_OPEN
   ↓
SUCCESS
   ↓
CLOSED
```

---

# 21. 为什么需要 Circuit Breaker？

否则：

```text
OpenAI
 ↓
失败
 ↓
Retry
 ↓
失败
 ↓
Retry
 ↓
失败
```

会造成：

```text
Latency ↑
Cost ↑
Queue ↑
System overload
```

---

# 22. Spring Cloud Circuit Breaker

如果你的企业 Java 项目已经使用 Spring Cloud，可以采用：

```text
Spring Cloud Circuit Breaker
+
Resilience4j
```

架构：

```text
AI Gateway
 ↓
Circuit Breaker
 ↓
Model Provider
```

---

# 23. Retry + Circuit Breaker

推荐：

```text
Request
 ↓
Retry
 ↓
Circuit Breaker
 ↓
Model
```

但实际顺序需要根据框架和策略仔细设计。

核心原则：

```text
Retry 有上限
Circuit 防止持续打爆
```

---

# 24. Rate Limit

假设：

```text
Tenant A
```

限制：

```text
100 requests/min
```

Tenant B：

```text
1,000 requests/min
```

AI Gateway：

```text
Request
 ↓
Rate Limiter
 ↓
Allow / Reject
```

---

# 25. Token Rate Limit

仅限制：

```text
requests/min
```

不够。

因为：

```text
Request A
= 500 tokens

Request B
= 100,000 tokens
```

成本完全不同。

企业还应该考虑：

```text
tokens/min
```

---

# 26. Tenant Quota

例如：

```text
Tenant A
Monthly Budget = $1,000

Tenant B
Monthly Budget = $10,000
```

AI Gateway：

```text
Request
 ↓
Budget Check
 ↓
Allowed?
```

---

# 27. Budget Control

```text
Current Spend
+
Estimated Request Cost
```

如果：

```text
>$1,000
```

可以：

```text
Reject
```

或者：

```text
Downgrade Model
```

例如：

```text
Premium
 ↓
Budget exceeded
 ↓
Cheap Model
```

---

# 28. Model Downgrade

例如：

```text
Premium Model
```

预算不足：

```text
Standard Model
```

再不足：

```text
Small Model
```

这是：

> **Cost-aware Model Routing**

---

# 29. Token Tracking

每一次 AI Request 保存：

```text
requestId
tenantId
userId
model
inputTokens
outputTokens
totalTokens
latency
status
```

---

# 30. AI Usage Table

```sql
CREATE TABLE ai_usage (
    id BIGSERIAL PRIMARY KEY,

    request_id UUID NOT NULL,

    tenant_id VARCHAR(100),

    user_id VARCHAR(100),

    provider VARCHAR(50),

    model VARCHAR(100),

    input_tokens BIGINT,

    output_tokens BIGINT,

    total_tokens BIGINT,

    latency_ms BIGINT,

    status VARCHAR(30),

    created_at TIMESTAMP
);
```

---

# 31. Cost Tracking

建立价格配置：

```text
Model
Input Price
Output Price
```

然后：

```text
cost =
inputTokens × inputPrice
+
outputTokens × outputPrice
```

**不要把价格硬编码在业务代码里。**

价格会变化。

---

# 32. Cost Dashboard

```text
AI Cost
────────────────────
OpenAI        $420
Bedrock       $280
Gemini        $150
Other          $70
────────────────────
Total         $920
```

按：

```text
Tenant
Application
Agent
Model
User
Use Case
```

分别统计。

---

# 33. Agent Cost

Day 52：

```text
Supervisor
 ├── Customer
 ├── Data
 ├── Research
 └── Report
```

今天可以看到：

```text
Supervisor       $0.10
Customer Agent   $0.02
Data Agent       $0.04
Research Agent   $0.08
Report Agent     $0.12
```

于是可以找到：

> 哪个 Agent 最烧钱。

---

# 34. AI Cache

有些问题重复率很高：

```text
"What is the vacation policy?"
```

每次调用 LLM：

```text
$$$$
```

可以：

```text
Request
 ↓
Cache
 ↓
Hit → Return
 ↓
Miss
 ↓
LLM
```

---

# 35. Redis AI Cache

你的 Day 48 已经有 Redis。

可以：

```text
Redis
  │
  └── ai:cache:{hash}
```

Key：

```text
hash(
 model
 +
 systemPromptVersion
 +
 userPrompt
 +
 parameters
)
```

---

# 36. Cache 注意事项

不能缓存：

```text
Personalized
Real-time
Financial
Sensitive
```

例如：

```text
"What's my current account balance?"
```

不应该简单缓存。

---

# 37. Semantic Cache

普通 Cache：

```text
"Vacation policy?"
```

和：

```text
"How many vacation days do I get?"
```

属于不同字符串。

Semantic Cache：

```text
Embedding
 ↓
Similarity
 ↓
找到相似问题
```

然后返回之前结果。

这是后面的高级内容。

---

# 38. Streaming

企业聊天需要：

```text
Token
Token
Token
Token
```

而不是：

```text
等待 10 秒
 ↓
一次返回
```

架构：

```text
Client
 ↓
SSE
 ↓
AI Gateway
 ↓
Model
```

---

# 39. SSE

Spring WebFlux：

```java
@GetMapping(
    value = "/chat/stream",
    produces = MediaType.TEXT_EVENT_STREAM_VALUE
)
public Flux<String> stream(...) {

    return aiGateway.stream(request);
}
```

---

# 40. Streaming Gateway

```text
Browser
 │
 ▼
API Gateway
 │
 ▼
Spring Boot
 │
 ▼
AI Gateway
 │
 ▼
Model
 │
 └── Token Stream
       ↓
      SSE
       ↓
    Browser
```

---

# 41. API Key 管理

不要：

```java
String apiKey =
    "sk-xxxxxxxx";
```

绝对不要提交到 Git。

应该：

```text
AWS Secrets Manager
        │
        ▼
    AI Gateway
```

---

# 42. AWS Secrets Manager

保存：

```text
OPENAI_API_KEY
ANTHROPIC_API_KEY
```

或者：

```text
Bedrock IAM Role
```

对于 AWS Bedrock，优先使用：

```text
IAM Role
```

而不是自己保存长期 AWS Access Key。

---

# 43. Secrets Architecture

```text
EKS Pod
  │
  ▼
IAM Role
  │
  ▼
Secrets Manager
  │
  ▼
AI Gateway
```

---

# 44. Multi-Provider

最终：

```text
AI Gateway
│
├── Amazon Bedrock
│
├── OpenAI
│
├── Anthropic
│
└── Google Gemini
```

业务代码：

```text
完全不知道
```

到底是哪一个 Provider。

---

# 45. Provider Adapter

设计：

```java
public interface ModelProvider {

    AiResponse chat(
        AiRequest request,
        ModelConfig config
    );
}
```

实现：

```text
OpenAiProvider
BedrockProvider
GeminiProvider
AnthropicProvider
```

---

# 46. Provider Registry

```java
public interface ProviderRegistry {

    ModelProvider get(
        String provider
    );
}
```

这样：

```text
Model Router
 ↓
Provider Registry
 ↓
Provider Adapter
 ↓
Actual Model
```

---

# 47. 完整调用链

```text
POST /api/v1/ai/chat
        │
        ▼
Authentication
        │
        ▼
Authorization
        │
        ▼
Rate Limit
        │
        ▼
Budget Check
        │
        ▼
Cache
        │
        ▼
Model Router
        │
        ▼
Circuit Breaker
        │
        ▼
Provider Adapter
        │
        ▼
LLM
        │
        ▼
Usage Tracking
        │
        ▼
Cost Tracking
        │
        ▼
Audit
        │
        ▼
Response
```

---

# 48. AI Gateway 项目结构

```text
ai/
├── gateway/
│   ├── AiGateway.java
│   ├── AiGatewayImpl.java
│   └── AiRequest.java
│
├── model/
│   ├── ModelConfig.java
│   ├── ModelRegistry.java
│   └── ModelRouter.java
│
├── provider/
│   ├── ModelProvider.java
│   ├── OpenAiProvider.java
│   ├── BedrockProvider.java
│   └── GeminiProvider.java
│
├── resilience/
│   ├── RetryPolicy.java
│   ├── CircuitBreaker.java
│   └── RateLimiter.java
│
├── cache/
│   └── AiCacheService.java
│
├── cost/
│   ├── UsageTracker.java
│   └── CostCalculator.java
│
└── security/
    └── ApiKeyService.java
```

---

# 49. Model Router 示例

```java
@Component
public class ModelRouter {

    public ModelConfig route(
            AiRequest request) {

        if ("agent".equals(
                request.useCase())) {

            return reasoningModel();
        }

        if ("classification".equals(
                request.useCase())) {

            return fastModel();
        }

        return defaultModel();
    }
}
```

---

# 50. Fallback 示例

```java
public AiResponse execute(
        AiRequest request) {

    try {
        return primary.call(request);

    } catch (RetryableProviderException e) {

        return fallback.call(request);
    }
}
```

生产环境要进一步加入：

```text
Retry
Circuit Breaker
Timeout
Observability
```

---

# 51. AI Gateway + Day 52

现在：

```text
Supervisor
   │
   ▼
AI Gateway
   │
   ├── Premium Model
   │
   ├── Fast Model
   │
   └── Cheap Model
```

不同 Agent 可以使用不同模型。

---

# 52. Model Routing 示例

```text
Supervisor
→ Premium

Research Agent
→ Standard

Data Agent
→ Fast

Evaluator
→ Standard

Report Agent
→ Premium
```

这就是：

> **Agent-aware Model Routing**

---

# 53. AI Gateway + Day 53

Evaluation 可以测试：

```text
Model A
vs
Model B
```

例如：

```text
                    Dataset
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
         Model A              Model B
             │                   │
             ▼                   ▼
          Evaluate            Evaluate
             │                   │
             └─────────┬─────────┘
                       ▼
                   Compare
```

---

# 54. A/B Testing

例如：

```text
90%
Model A

10%
Model B
```

比较：

```text
Quality
Cost
Latency
Conversion
```

---

# 55. Canary Model

上线新模型：

```text
Current Model
      │
      ├── 95%
      │
      └── 5% New Model
```

如果：

```text
Quality ↓
Errors ↑
Cost ↑
```

自动：

```text
New Model
 ↓
Traffic = 0
```

---

# 56. Model Policy

建议：

```yaml
models:

  premium:
    provider: bedrock
    model: xxx

  fast:
    provider: bedrock
    model: xxx

  fallback:
    provider: openai
    model: xxx
```

不要让业务代码写：

```text
"model=xxx"
```

---

# 57. Tenant Model Policy

例如：

```text
Tenant A
→ only Bedrock

Tenant B
→ Bedrock + OpenAI

Tenant C
→ premium only
```

AI Gateway：

```text
Tenant
 ↓
Policy
 ↓
Allowed Models
```

---

# 58. Data Residency

企业客户可能要求：

```text
EU Data
→ EU Region
```

或者：

```text
US Data
→ US Region
```

因此 Model Router 也可以考虑：

```text
tenant
region
compliance
```

---

# 59. AI Gateway Security

必须做到：

```text
Authentication
Authorization
Rate Limit
Secrets
Audit
Data Filtering
Tenant Isolation
```

---

# 60. Prompt Logging

不要简单把：

```text
所有 Prompt
```

原文全部写日志。

因为可能包含：

```text
PII
Customer Data
Secrets
Business Data
```

建议：

```text
requestId
hash
metadata
token count
```

必要时对敏感内容：

```text
Mask
Redact
Encrypt
```

---

# 61. Day 54 AWS 架构

```text
                         AWS
                          │
                         EKS
                          │
                  ┌───────┴────────┐
                  ▼                ▼
             AI Gateway        Agent Worker
                  │                │
                  └───────┬────────┘
                          ▼
                    Model Router
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
           Bedrock      OpenAI      Gemini
              │
              ▼
          CloudWatch
              │
              ▼
        Cost / Metrics
```

---

# 62. Redis

```text
Redis
│
├── AI Cache
├── Rate Limit
├── Distributed Lock
└── Short-lived State
```

---

# 63. PostgreSQL

```text
PostgreSQL
│
├── ai_usage
├── ai_cost
├── evaluation_runs
├── agent_runs
├── checkpoints
└── audit
```

---

# 64. SQS

```text
SQS
│
└── Long-running AI Jobs
```

例如：

```text
Agent
 ↓
SQS
 ↓
Agent Worker
 ↓
AI Gateway
 ↓
Model
```

---

# 65. Day 54 必做项目

建立：

> **Enterprise AI Gateway**

支持：

```text
OpenAI
+
Amazon Bedrock
```

先做两个 Provider 就够了。

然后：

```text
Model Router
Fallback
Rate Limit
Token Tracking
Cost Tracking
Redis Cache
Circuit Breaker
```

---

# 66. API

### Chat

```http
POST /api/v1/ai/chat
```

### Streaming

```http
POST /api/v1/ai/chat/stream
```

### Usage

```http
GET /api/v1/ai/usage
```

### Models

```http
GET /api/v1/ai/models
```

---

# 67. Chat Request

```json
{
  "useCase": "agent",
  "messages": [
    {
      "role": "user",
      "content": "Analyze customer C1001"
    }
  ]
}
```

---

# 68. Gateway Response

```json
{
  "requestId": "abc123",
  "model": "premium",
  "content": "...",
  "usage": {
    "inputTokens": 1200,
    "outputTokens": 800,
    "totalTokens": 2000
  },
  "latencyMs": 1850
}
```

---

# 69. Day 54 测试

### Test 1

```text
Request
 ↓
AI Gateway
 ↓
Bedrock
 ↓
Success
```

---

### Test 2

模拟：

```text
Bedrock = 503
```

应该：

```text
Bedrock
 ↓
Fallback
 ↓
OpenAI
```

---

### Test 3

连续失败：

```text
Provider
 ↓
10 failures
```

Circuit：

```text
CLOSED
 ↓
OPEN
```

---

### Test 4

超过：

```text
100 requests/min
```

返回：

```text
429 Too Many Requests
```

---

### Test 5

超过预算：

```text
$1,000
```

返回：

```text
Budget Exceeded
```

或者根据你的策略自动降级模型。

---

### Test 6

重复请求：

```text
"What is the vacation policy?"
```

第二次：

```text
Redis Cache HIT
```

---

# 70. Day 54 必做清单

```text
[ ] AiGateway
[ ] AiRequest
[ ] AiResponse
[ ] ModelRegistry
[ ] ModelRouter
[ ] ProviderAdapter
[ ] BedrockProvider
[ ] OpenAiProvider
[ ] Fallback
[ ] Retry
[ ] CircuitBreaker
[ ] RateLimiter
[ ] TokenTracking
[ ] CostTracking
[ ] RedisCache
[ ] SecretsManager
[ ] TenantQuota
[ ] Streaming
[ ] Audit
[ ] Metrics
[ ] A/B Model Routing
```

---

# 71. Day 54 面试题

### Q1：为什么企业需要 AI Gateway？

```text
统一：
Model
Security
Cost
Rate Limit
Fallback
Observability
```

---

### Q2：Model Router 做什么？

根据：

```text
Use Case
Capability
Cost
Latency
Availability
Tenant Policy
```

选择模型。

---

### Q3：Fallback 和 Retry 的区别？

```text
Retry
=
同一个 Provider 再试

Fallback
=
切换另一个 Provider / Model
```

---

### Q4：Circuit Breaker 解决什么？

防止：

```text
持续调用已经故障的 Provider
```

导致：

```text
系统级联故障
```

---

### Q5：为什么 Token Tracking 很重要？

因为 AI 成本主要与：

```text
Input Tokens
+
Output Tokens
```

有关。

---

### Q6：为什么 Rate Limit 还不够？

因为：

```text
100 requests
```

并不代表：

```text
100 × 相同成本
```

所以还需要：

```text
Token Quota
+
Budget
```

---

# 72. Day 54 最重要架构图

```text
                         APPLICATION
                              │
                              ▼
                         AI GATEWAY
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
     Authentication       Rate Limit          Budget
                              │
                              ▼
                            Cache
                              │
                              ▼
                        Model Router
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
           Bedrock          OpenAI          Gemini
              │               │               │
              └───────────────┼───────────────┘
                              ▼
                       Circuit Breaker
                              │
                              ▼
                           RESPONSE
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
          Usage             Cost             Audit
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                         PostgreSQL
```

---

## Day 54 → Day 55

现在你的架构已经有：

```text
Day 50  Agent + MCP
Day 51  Workflow + Checkpoint
Day 52  Multi-Agent
Day 53  Evaluation
Day 54  AI Gateway
```

**Day 55 下一步应该进入 AI Security：**

```text
IAM
 ↓
OAuth2 / OIDC
 ↓
JWT
 ↓
Tenant Isolation
 ↓
Prompt Injection Defense
 ↓
PII Detection
 ↓
Data Loss Prevention
 ↓
Tool Authorization
 ↓
AI Guardrails
 ↓
Audit
```

这会把目前的 AI Demo/Prototype，进一步升级成真正可以放进 **AWS 企业生产环境**的安全架构。
