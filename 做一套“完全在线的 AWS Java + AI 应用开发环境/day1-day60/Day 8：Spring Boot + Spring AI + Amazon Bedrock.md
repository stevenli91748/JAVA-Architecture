# Day 8：Spring Boot + Spring AI + Amazon Bedrock

今天开始进入真正的 **Java + AI 开发**。

昨天：

```text
Internet
   ↓
ALB
   ↓
ECS Fargate
   ↓
Spring Boot
   ↓
RDS
```

今天增加：

```text
Spring Boot
   ↓
Spring AI
   ↓
Amazon Bedrock
   ↓
LLM
```

最终做出：

```text
React
  ↓
POST /api/chat
  ↓
Spring Boot
  ↓
Spring AI
  ↓
Amazon Bedrock
  ↓
AI Answer
  ↓
React
```

---

# 1. 今天的目标

今天完成：

```text
[1] Bedrock 权限
[2] Java AWS SDK
[3] Spring AI
[4] Bedrock Chat Model
[5] /api/chat
[6] Docker
[7] ECR
[8] ECS 更新
[9] React Chat UI
```

---

# 2. 先启用 Amazon Bedrock Model Access

进入 AWS Console：

[Amazon Bedrock Console](https://console.aws.amazon.com/bedrock/?utm_source=chatgpt.com)

确认 Region：

```text
us-west-2
```

进入：

```text
Bedrock
 ↓
Model access
```

AWS 不同 Region 可用模型不同，所以**不要死记某一个模型 ID**；直接在你当前 Region 的 Model access 页面查看可用模型。

建议先选择一个支持 Converse API 的模型。

例如如果你的账户/Region 可用：

```text
Amazon Nova
```

或者：

```text
Anthropic Claude
```

---

# 3. 为什么选择 Bedrock

我们不需要在 ECS 里面自己运行：

```text
Llama
Mistral
Claude
```

而是：

```text
ECS
 │
 ▼
AWS Bedrock
 │
 ▼
Foundation Model
```

优点：

```text
不管理 GPU
不管理模型服务器
不用下载模型
不用维护推理服务
```

非常适合企业 Java 应用。

---

# 4. 给 ECS Task Role 增加 Bedrock 权限

进入：

```text id="9x0q4k"
IAM
 ↓
Roles
 ↓
java-ai-ecs-task-role
```

添加权限。

学习阶段可以先使用：

```text id="8c3v1p"
AmazonBedrockFullAccess
```

但是注意：

**生产环境不要使用 FullAccess。**

最终我们会改成最小权限，例如只允许：

```text id="h4k8z6"
bedrock:InvokeModel
bedrock:Converse
```

以及指定模型资源。

---

# 5. 一个关键概念

昨天：

```text
ECS Execution Role
```

负责：

```text
ECR
CloudWatch
```

今天：

```text
ECS Task Role
```

负责：

```text
Spring Boot
   ↓
AWS APIs
   ↓
Bedrock
```

一定要区分：

```text id="c4z7m1"
Execution Role
       │
       └── ECS 自己使用

Task Role
       │
       └── Java Application 使用
```

---

# 6. 添加 Spring AI

进入：

```text id="p7m3x2"
backend/pom.xml
```

加入 Spring AI BOM。

当前 Spring AI 版本会随 Spring Boot 兼容矩阵变化，因此建议以官方文档当前推荐版本为准。[Spring AI 官方文档](https://docs.spring.io/spring-ai/reference/?utm_source=chatgpt.com)

典型配置：

```xml id="w8k4n2"
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.ai</groupId>
            <artifactId>spring-ai-bom</artifactId>
            <version>${spring-ai.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

然后加入 Bedrock starter。

Spring AI 的具体 Bedrock starter artifact 名称会随版本变化，因此这里**不要盲目复制旧教程里的 dependency**；使用当前 Spring AI 官方 Bedrock 文档中的 starter。

---

# 7. 更稳妥的方案：先用 AWS SDK

为了让你今天一定跑通，我们先直接使用：

```text
AWS SDK for Java 2.x
```

调用：

```text
Amazon Bedrock Runtime
```

AWS 官方 Java SDK 文档：[AWS SDK for Java 2.x](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/home.html?utm_source=chatgpt.com)

Maven：

```xml id="5q1v8r"
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>bedrockruntime</artifactId>
</dependency>
```

这样我们先验证：

```text
Java
 ↓
AWS SDK
 ↓
Bedrock
```

成功以后，再换成：

```text
Spring AI
```

这是企业项目里比较稳妥的开发方式。

---

# 8. 配置 Region

`application.yml`：

```yaml id="2g6n9v"
aws:
  region: us-west-2
```

但是**不要在 Java 代码中写 AWS Access Key/Secret Key**。

ECS：

```text
IAM Task Role
      ↓
AWS SDK
      ↓
Temporary Credentials
```

AWS SDK 会自动获取。

---

# 9. 创建 Bedrock Service

创建：

```text id="m8c3x7"
ai/
├── ChatService.java
└── ChatController.java
```

核心逻辑：

```java id="p4y9k2"
@Service
public class ChatService {

    private final BedrockRuntimeClient client;

    public ChatService() {
        this.client = BedrockRuntimeClient.builder()
                .region(Region.US_WEST_2)
                .build();
    }
}
```

注意：

```text id="q2h7v5"
不要：

AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

AWS SDK 会通过 ECS Task Role 获取临时凭证。

---

# 10. 使用 Converse API

Bedrock 推荐使用统一的 Converse API 来调用支持该接口的模型。[Amazon Bedrock Converse API 官方文档](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html?utm_source=chatgpt.com)

核心代码：

```java id="d8v3q1"
public String chat(String message) {

    Message userMessage = Message.builder()
            .role(ConversationRole.USER)
            .content(
                ContentBlock.builder()
                    .text(message)
                    .build()
            )
            .build();

    ConverseRequest request =
            ConverseRequest.builder()
                    .modelId(MODEL_ID)
                    .messages(userMessage)
                    .build();

    ConverseResponse response =
            client.converse(request);

    return response.output()
            .message()
            .content()
            .get(0)
            .text();
}
```

`MODEL_ID` 不要直接照抄旧教程。

去：

```text id="e7x1k9"
Bedrock
 ↓
Models
```

复制你当前 `us-west-2` 实际可用的模型 ID。

---

# 11. 创建 Chat Controller

```java id="k5q8w3"
@RestController
@RequestMapping("/api/chat")
public class ChatController {

    private final ChatService chatService;

    public ChatController(ChatService chatService) {
        this.chatService = chatService;
    }

    @PostMapping
    public ChatResponse chat(
            @RequestBody ChatRequest request) {

        String answer =
                chatService.chat(request.message());

        return new ChatResponse(answer);
    }
}
```

DTO：

```java id="m3x7c9"
public record ChatRequest(
        String message
) {
}
```

```java id="r8v2n5"
public record ChatResponse(
        String answer
) {
}
```

---

# 12. 本地测试

如果你的 Codespaces 当前没有 AWS credentials：

```bash id="h4n7c2"
aws sts get-caller-identity
```

需要先完成 AWS 身份认证。

然后：

```bash id="z1p6k8"
./mvnw spring-boot:run
```

测试：

```bash id="c7m2v9"
curl -X POST \
http://localhost:8080/api/chat \
-H "Content-Type: application/json" \
-d '{"message":"Explain Java Spring Boot in simple terms"}'
```

成功以后：

```text id="b8q3w1"
Spring Boot
     ↓
AWS SDK
     ↓
Bedrock
     ↓
LLM
     ↓
Response
```

---

# 13. 再接入 Spring AI

现在 AWS SDK 已经验证。

接下来使用 Spring AI。

Spring AI 的核心优势是：

```text id="s5k9x3"
Application
     ↓
ChatClient
     ↓
Model
```

你的代码不需要到处关心：

```text
Bedrock API
OpenAI API
Anthropic API
```

以后可以更容易切换模型提供商。

---

# 14. Spring AI 架构

最终：

```text id="j4m8q2"
ChatController
      ↓
ChatService
      ↓
Spring AI ChatClient
      ↓
ChatModel
      ↓
Amazon Bedrock
```

Controller 不应该直接调用 Bedrock。

不要：

```java id="q6v2x9"
Controller
   ↓
BedrockRuntimeClient
```

应该：

```text id="c1n7m4"
Controller
   ↓
Service
   ↓
ChatClient
   ↓
Model
```

这样以后容易加入：

```text
RAG
Memory
Tools
Agent
Guardrails
```

---

# 15. 创建统一 AI Service

推荐：

```text id="v5p9k2"
ai/
├── ChatController
├── ChatService
├── ChatRequest
├── ChatResponse
└── AiConfig
```

以后：

```text id="m2x8q6"
ai/
├── chat/
├── rag/
├── embedding/
├── memory/
└── agent/
```

这是我们后面 Day 9–10 的基础。

---

# 16. React 加 Chat 页面

创建：

```text id="c8n3v7"
frontend/src/pages/ChatPage.tsx
```

简单版本：

```tsx id="z4m7q1"
import { useState } from "react";
import { apiClient } from "../api/client";

export default function ChatPage() {

  const [message, setMessage] = useState("");
  const [answer, setAnswer] = useState("");

  async function sendMessage() {

    const response =
      await apiClient.post(
        "/api/chat",
        { message }
      );

    setAnswer(response.data.answer);
  }

  return (
    <div>

      <h1>AI Chat</h1>

      <textarea
        value={message}
        onChange={(e) =>
          setMessage(e.target.value)
        }
      />

      <br />

      <button onClick={sendMessage}>
        Send
      </button>

      <hr />

      <div>
        {answer}
      </div>

    </div>
  );
}
```

---

# 17. 修改 App

```tsx id="n6q2v8"
import ChatPage from "./pages/ChatPage";

function App() {
  return <ChatPage />;
}

export default App;
```

现在：

```text id="w8m4k1"
Browser
   ↓
React Chat
   ↓
POST /api/chat
   ↓
Spring Boot
   ↓
Bedrock
```

---

# 18. 最重要的企业架构变化

我们现在从：

```text id="a4x7m2"
Java Enterprise
```

开始进入：

```text id="r9k3v6"
Java Enterprise
       +
      AI
```

完整结构：

```text
                    React
                      │
             ┌────────┴────────┐
             │                 │
          User API          Chat API
             │                 │
             ▼                 ▼
        Spring Boot       Spring AI
             │                 │
             ▼                 ▼
            JPA              Bedrock
             │                 │
             ▼                 ▼
           RDS             Foundation Model
```

---

# 19. Docker 更新

因为代码发生变化：

```bash id="q8v4m1"
cd backend

./mvnw clean package -DskipTests
```

重新 Build：

```bash id="c2x7n9"
docker build \
-t java-ai-backend:1.1.0 .
```

Tag：

```bash id="v7m3p5"
docker tag \
java-ai-backend:1.1.0 \
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.1.0
```

Push：

```bash id="k1n6q8"
docker push \
YOUR_ACCOUNT_ID.dkr.ecr.us-west-2.amazonaws.com/java-ai-backend:1.1.0
```

---

# 20. 更新 ECS

进入：

```text id="m5q8v2"
ECS
 ↓
Task Definitions
 ↓
java-ai-backend
 ↓
Create new revision
```

修改 Image：

```text id="z3k7p1"
java-ai-backend:1.1.0
```

然后：

```text id="q9x4m6"
ECS Service
 ↓
Update
 ↓
New Task Definition
```

选择：

```text id="u7v2k5"
revision
```

部署。

---

# 21. ECS → Bedrock

这一步非常重要。

你的架构：

```text id="b4m8q2"
ECS
 │
 │ IAM Task Role
 ▼
Bedrock
 │
 ▼
LLM
```

**不需要：**

```text
AWS_ACCESS_KEY
AWS_SECRET_KEY
```

也不需要：

```text
OPENAI_API_KEY
```

如果使用 Bedrock。

---

# 22. CloudWatch 查看 AI 请求

进入：

```text id="q7n3m5"
CloudWatch
 ↓
/aws/ecs/java-ai-backend
```

应该看到：

```text
POST /api/chat
```

你可以记录：

```text
request time
model
latency
success
error
```

但是：

**不要把用户完整 Prompt、敏感信息、API Token 全部写入 CloudWatch。**

---

# 23. Day 8 最终架构

```text
                         Internet
                            │
                            ▼
                          ALB
                            │
                            ▼
                     ECS Fargate
                            │
                  ┌─────────┴─────────┐
                  │                   │
                  ▼                   ▼
             Spring Boot         Spring AI
                  │                   │
                  ▼                   ▼
                 RDS              Bedrock
                                      │
                                      ▼
                                     LLM
```

Frontend：

```text
React
 │
 ├── /api/users
 │
 └── /api/chat
```

---

# 24. Day 8 检查清单

```text id="c3m8x7"
[ ] Bedrock Model Access
[ ] ECS Task Role
[ ] Bedrock permission
[ ] AWS SDK for Java
[ ] Bedrock Runtime
[ ] Converse API
[ ] /api/chat
[ ] React Chat UI
[ ] Docker 1.1.0
[ ] ECR 1.1.0
[ ] ECS 新 revision
[ ] ECS → Bedrock
[ ] AI 返回结果
```

---

## 今天完成后的能力

你现在已经有：

```text id="2z7p4k"
                    AWS
                     │
          ┌──────────┴──────────┐
          │                     │
       Enterprise            AI
       Application           Application
          │                     │
     Spring Boot            Spring AI
          │                     │
       PostgreSQL            Bedrock
          │                     │
          └──────────┬──────────┘
                     │
                 ECS Fargate
```

**Day 9 我建议直接做 RAG，而不是继续做普通 Chat。**

我们会加入：

```text
PDF / Word
    ↓
Document Loader
    ↓
Chunking
    ↓
Embedding
    ↓
PostgreSQL + pgvector
    ↓
Vector Search
    ↓
Spring AI
    ↓
Bedrock
    ↓
带知识库的 AI 回答
```

这一步完成后，你的环境就开始具备真正的**企业 AI 应用开发能力**。
