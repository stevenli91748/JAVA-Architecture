# Day 9：RAG + PostgreSQL + pgvector + Spring AI

今天把昨天的普通 AI Chat 升级成**企业知识库 RAG**。

昨天：

```text
React
 ↓
Spring Boot
 ↓
Bedrock
 ↓
LLM
```

今天：

```text
PDF / TXT / 企业文档
        ↓
Document
        ↓
Chunk
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
回答
```

最终效果：

> 用户问：“公司的 PTO 政策是什么？”
>
> AI 先搜索企业知识库，再根据搜索结果回答，而不是只依赖模型自己的知识。

---

# 1. 今天完成

```text
Day 9
│
├── PostgreSQL pgvector
├── documents 表
├── document_chunks 表
├── Embedding
├── Vector Search
├── RAG Service
├── Spring AI
├── Bedrock
└── /api/rag/query
```

---

# 2. 今天的最终架构

```text
                    React
                      │
                      ▼
              /api/rag/query
                      │
                      ▼
                Spring Boot
                      │
                      ▼
                 RAG Service
                 /         \
                /           \
               ▼             ▼
       PostgreSQL          Bedrock
        + pgvector             │
               │               │
               └───────┬───────┘
                       ▼
                    Answer
```

---

# 3. 为什么用 pgvector

我们已经有：

```text
Amazon RDS PostgreSQL
```

所以第一阶段不需要额外部署：

```text
Pinecone
Weaviate
Milvus
OpenSearch
```

直接：

```text
PostgreSQL
+
pgvector
```

就可以完成第一套 RAG。

这样架构更简单，成本也更低。

---

# 4. 先检查 PostgreSQL 是否支持 pgvector

进入 Codespaces：

```bash
docker exec -it java-ai-postgres \
psql -U app_user -d enterprise_ai
```

执行：

```sql
SELECT * FROM pg_available_extensions
WHERE name = 'vector';
```

如果看到：

```text
vector
```

说明当前 PostgreSQL 镜像支持。

---

# 5. 如果没有 pgvector

把：

```yaml
image: postgres:17
```

改成支持 pgvector 的镜像，例如：

```yaml
image: pgvector/pgvector:pg17
```

然后：

```bash
docker compose down
docker compose pull
docker compose up -d
```

重新检查。

---

# 6. 在 PostgreSQL 启用 pgvector

执行：

```sql
CREATE EXTENSION IF NOT EXISTS vector;
```

检查：

```sql
SELECT extname
FROM pg_extension;
```

应该看到：

```text
vector
```

---

# 7. Flyway Migration

不要手工长期修改数据库。

创建：

```text
backend/src/main/resources/db/migration/
```

新增：

```text
V2__create_rag_tables.sql
```

内容：

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,
    filename VARCHAR(500) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE document_chunks (
    id BIGSERIAL PRIMARY KEY,
    document_id BIGINT NOT NULL,
    content TEXT NOT NULL,
    chunk_index INTEGER NOT NULL,
    embedding VECTOR(1024),

    CONSTRAINT fk_document_chunks_document
        FOREIGN KEY (document_id)
        REFERENCES documents(id)
        ON DELETE CASCADE
);
```

---

# 8. 为什么是 VECTOR(1024)

这里必须注意：

```text
VECTOR(1024)
```

**必须和你实际使用的 Embedding Model 输出维度一致。**

不要认为所有 embedding 都是 1024。

例如：

```text
Embedding Model A → 1024
Embedding Model B → 1536
Embedding Model C → 3072
```

所以 Day 9 实际运行时：

```text
Bedrock Embedding Model
        ↓
确认 dimensions
        ↓
VECTOR(dimensions)
```

如果你选择的模型不是 1024：

```sql
VECTOR(1024)
```

就必须改成对应维度。

---

# 9. 执行 Flyway

启动 Spring Boot：

```bash
cd backend
./mvnw spring-boot:run
```

Flyway 会自动执行：

```text
V1
 ↓
V2
```

检查：

```sql
SELECT *
FROM flyway_schema_history;
```

应该看到：

```text
V1__create_users
V2__create_rag_tables
```

---

# 10. 创建 Document Entity

创建：

```text
rag/Document.java
```

```java
@Entity
@Table(name = "documents")
public class Document {

    @Id
    @GeneratedValue(
        strategy = GenerationType.IDENTITY
    )
    private Long id;

    @Column(nullable = false)
    private String filename;

    @Column(nullable = false,
            columnDefinition = "TEXT")
    private String content;

    @Column(name = "created_at",
            nullable = false)
    private LocalDateTime createdAt;

    protected Document() {
    }

    public Document(
            String filename,
            String content) {

        this.filename = filename;
        this.content = content;
        this.createdAt =
                LocalDateTime.now();
    }
}
```

---

# 11. Document Chunk

```java
@Entity
@Table(name = "document_chunks")
public class DocumentChunk {

    @Id
    @GeneratedValue(
        strategy = GenerationType.IDENTITY
    )
    private Long id;

    @Column(name = "document_id",
            nullable = false)
    private Long documentId;

    @Column(nullable = false,
            columnDefinition = "TEXT")
    private String content;

    @Column(name = "chunk_index",
            nullable = false)
    private Integer chunkIndex;
}
```

Embedding 字段先由 SQL/JDBC 处理。

这是因为 pgvector 与 JPA 的映射方式会根据你使用的 Spring AI / Hibernate Vector 支持版本而变化。

---

# 12. RAG 的核心：Chunking

假设 PDF：

```text
Company Employee Handbook

Section 1
Vacation...

Section 2
PTO...

Section 3
Healthcare...
```

不要：

```text
整个 PDF
 ↓
一个 Embedding
```

应该：

```text
PDF
 ↓
Text
 ↓
Chunk 1
Chunk 2
Chunk 3
Chunk 4
...
```

例如：

```text
Chunk 1:
PTO policy...

Chunk 2:
Employees receive...

Chunk 3:
Unused PTO...
```

---

# 13. Chunk 大小

第一版建议：

```text
chunk size:
500–1000 tokens

overlap:
100–150 tokens
```

不要一开始追求完美。

RAG 的质量后面主要通过：

```text
Chunk size
Overlap
Embedding model
Top K
Prompt
Reranking
```

调优。

---

# 14. Embedding

RAG 中：

```text
Document
   ↓
Embedding Model
   ↓
Vector
```

例如：

```text
"PTO policy"
```

变成：

```text
[0.012,
 -0.093,
 0.442,
 ...
]
```

然后保存：

```text
pgvector
```

---

# 15. Spring AI Embedding

Spring AI 提供统一的：

```text
EmbeddingModel
```

概念。

结构：

```text
RAG Service
    ↓
EmbeddingModel
    ↓
Bedrock
    ↓
Embedding
```

官方 Spring AI 文档：[Spring AI Reference](https://docs.spring.io/spring-ai/reference/?utm_source=chatgpt.com)

---

# 16. RAG Service

最终逻辑：

```java
public String query(String question) {

    // 1. Convert question → embedding
    float[] embedding =
        embeddingModel.embed(question);

    // 2. Search similar chunks
    List<DocumentChunk> chunks =
        vectorRepository.search(
            embedding,
            5
        );

    // 3. Build context
    String context =
        chunks.stream()
              .map(DocumentChunk::getContent)
              .collect(Collectors.joining("\n"));

    // 4. Send context + question to LLM
    return chatClient.prompt()
            .system("""
                Answer only using
                the provided context.
                If the answer is not
                in the context, say
                you don't know.
            """)
            .user("""
                Context:
                %s

                Question:
                %s
            """.formatted(context, question))
            .call()
            .content();
}
```

这就是最基本的 RAG。

---

# 17. Vector Search

PostgreSQL pgvector 支持向量相似度搜索。

例如：

```sql
SELECT
    id,
    content
FROM document_chunks
ORDER BY embedding <=> CAST(:embedding AS vector)
LIMIT 5;
```

其中：

```text
<=> 
```

可以用于 cosine distance。

我们要做的是：

```text
Question
   ↓
Question Embedding
   ↓
Vector Search
   ↓
Top 5 chunks
```

---

# 18. RAG API

创建：

```text
POST /api/rag/query
```

Request：

```json
{
  "question": "What is the PTO policy?"
}
```

Response：

```json
{
  "answer": "Employees receive...",
  "sources": [
    "employee-handbook.pdf"
  ]
}
```

---

# 19. Controller

```java
@RestController
@RequestMapping("/api/rag")
public class RagController {

    private final RagService ragService;

    public RagController(
            RagService ragService) {

        this.ragService = ragService;
    }

    @PostMapping("/query")
    public RagResponse query(
            @RequestBody RagRequest request) {

        return ragService.query(
                request.question()
        );
    }
}
```

---

# 20. RAG Prompt

不要简单：

```text
Answer the question.
```

建议：

```text
You are an enterprise knowledge assistant.

Answer the user's question using
only the provided context.

If the answer cannot be found
in the context, say:

"I don't have enough information
in the knowledge base."

Do not invent facts.

Context:
{context}

Question:
{question}
```

这可以明显降低：

```text
Hallucination
```

---

# 21. 加 Source Citation

企业 RAG 最好不要只返回：

```json
{
  "answer": "..."
}
```

而是：

```json
{
  "answer": "...",
  "sources": [
    {
      "filename": "employee-handbook.pdf",
      "chunkId": 17
    }
  ]
}
```

以后前端显示：

```text
AI Answer
──────────────
Employees receive...

Sources:
📄 employee-handbook.pdf
📄 HR-policy.pdf
```

这是企业 RAG 非常重要的功能。

---

# 22. React RAG 页面

创建：

```text
frontend/src/pages/RagPage.tsx
```

基本逻辑：

```tsx
const [question, setQuestion] =
    useState("");

const [answer, setAnswer] =
    useState("");

async function ask() {

    const response =
        await apiClient.post(
            "/api/rag/query",
            { question }
        );

    setAnswer(
        response.data.answer
    );
}
```

UI：

```text
Knowledge Base
────────────────────

Ask a question:

[ What is the PTO policy? ]

[ Ask AI ]

────────────────────

Answer:

Employees receive...

Sources:

employee-handbook.pdf
```

---

# 23. 第一个测试文档

先不要马上处理 PDF。

建立：

```text
test-data/company-policy.txt
```

内容：

```text
Company PTO Policy

Employees receive 15 days
of paid time off per year.

Employees may carry over
up to 5 unused PTO days
into the following year.

PTO requests should be
submitted at least 7 days
before the requested date.
```

---

# 24. 手工插入第一条知识

先用 SQL 验证 RAG Pipeline：

```sql
INSERT INTO documents
(filename, content)
VALUES
(
 'company-policy.txt',
 'Employees receive 15 days of paid time off per year.'
);
```

然后：

```sql
INSERT INTO document_chunks
(document_id, content, chunk_index)
VALUES
(
 1,
 'Employees receive 15 days of paid time off per year.',
 0
);
```

真正的 embedding ingestion pipeline，我们接下来再自动化。

---

# 25. 测试 RAG

问：

```text
What is the PTO policy?
```

系统：

```text
Question
   ↓
Embedding
   ↓
Vector Search
   ↓
company-policy.txt
   ↓
Context
   ↓
Bedrock
   ↓
Answer
```

---

# 26. 最重要的测试

问一个知识库里没有的问题：

```text
What is the company's 401(k) matching rate?
```

正确结果应该类似：

```text
I don't have enough information
in the knowledge base.
```

而不是：

```text
The company matches 5%.
```

这就是我们要控制的：

```text
Hallucination
```

---

# 27. Day 9 企业 RAG 架构

```text
                         User
                           │
                           ▼
                         React
                           │
                           ▼
                    Spring Boot
                           │
                    ┌──────┴──────┐
                    │             │
                  Query         Upload
                    │             │
                    ▼             ▼
              RAG Service     Document
                    │             │
          ┌─────────┴─────────┐   │
          │                   │   │
          ▼                   ▼   ▼
     Embedding            PostgreSQL
          │               + pgvector
          ▼                   │
       Bedrock                │
          │                   │
          └─────────┬─────────┘
                    ▼
                  Context
                    │
                    ▼
                  Bedrock
                    │
                    ▼
                  Answer
```

---

# 28. 今天的项目结构

```text
aws-java-ai-platform/
│
├── backend/
│   │
│   └── src/main/java/
│       └── com/example/demo/
│
│           ├── user/
│           │
│           ├── ai/
│           │   ├── ChatController
│           │   └── ChatService
│           │
│           └── rag/
│               ├── Document.java
│               ├── DocumentChunk.java
│               ├── RagController.java
│               ├── RagService.java
│               └── VectorRepository.java
│
├── frontend/
│   └── src/pages/
│       ├── ChatPage.tsx
│       └── RagPage.tsx
│
├── test-data/
│   └── company-policy.txt
│
└── infrastructure/
    └── terraform/
```

---

# 29. Day 9 检查清单

```text
[ ] pgvector
[ ] CREATE EXTENSION vector
[ ] Flyway V2
[ ] documents
[ ] document_chunks
[ ] EmbeddingModel
[ ] Vector Search
[ ] RAG Service
[ ] /api/rag/query
[ ] React RAG Page
[ ] Source Citation
[ ] Hallucination Test
```

---

# 30. 到 Day 9，你的系统已经变成

```text
                 AWS
                  │
        ┌─────────┴─────────┐
        │                   │
     Enterprise             AI
        │                   │
   Spring Boot          Spring AI
        │                   │
       RDS              Bedrock
        │                   │
        └──── pgvector ─────┘
                  │
                 RAG
```

你现在已经具备：

**Java 企业应用 + React + PostgreSQL + AWS ECS + Bedrock + RAG**

下一步 **Day 10** 做整个学习计划里非常关键的一步：

```text
GitHub
   ↓
Pull Request
   ↓
CodeBuild
   ↓
Unit Test
   ↓
Docker Build
   ↓
ECR
   ↓
ECS
   ↓
DEV
   ↓
TEST
```

也就是把前 9 天手工做的事情变成**真正的企业 CI/CD 自动部署流水线**。
