# Day 26：RAG 检索质量优化

今天开始解决 RAG 最核心的问题：

> **不是“能不能找到文档”，而是“能不能找到真正有用的文档”。**

Day 25：

```text
PDF
 ↓
S3
 ↓
SQS
 ↓
Chunk
 ↓
Embedding
 ↓
pgvector
```

Day 26 升级成：

```text
Question
   │
   ▼
┌─────────────────────────────┐
│ Hybrid Retrieval            │
│                             │
│ Vector Search               │
│      +                      │
│ PostgreSQL Full-Text Search │
└──────────────┬──────────────┘
               ▼
          Top 20 candidates
               │
               ▼
           Reranker
               │
               ▼
          Top 5 Context
               │
               ▼
             LLM
```

---

# 1. 今天的核心技术

今天学习：

```text
1. Chunk Optimization
2. Metadata Filtering
3. Vector Search
4. Full-Text Search
5. Hybrid Search
6. Reranking
7. Top-K
8. Similarity Threshold
9. RAG Evaluation
10. Retrieval Precision / Recall
```

---

# 2. 为什么 Vector Search 不够

假设用户搜索：

```text
"What is IAM role?"
```

Vector Search 可能找到：

```text
IAM permissions
AWS authentication
AWS security
IAM policy
```

但如果用户搜索：

```text
"bedrock:InvokeModel"
```

这是一个**精确技术字符串**。

关键词搜索反而可能更好。

所以：

```text
Vector Search
+
Keyword Search
```

通常比单独 Vector Search 更可靠。

---

# 3. Vector Search

语义：

```text
"How do I run containers?"
```

可能找到：

```text
"ECS Fargate launches containerized workloads."
```

即使两个句子没有完全相同的关键词。

优势：

```text
语义理解
同义词
自然语言问题
```

---

# 4. Full-Text Search

PostgreSQL：

```text
"What is InvokeModel?"
```

可以通过：

```text
tsvector
tsquery
```

搜索：

```text
InvokeModel
```

优势：

```text
精确词
技术名词
函数名
错误码
产品名称
```

---

# 5. PostgreSQL 增加全文搜索

修改：

```sql
ALTER TABLE document_chunks
ADD COLUMN content_tsv tsvector;
```

然后：

```sql
UPDATE document_chunks
SET content_tsv =
    to_tsvector('english', content);
```

建立索引：

```sql
CREATE INDEX document_chunks_content_tsv_idx
ON document_chunks
USING GIN(content_tsv);
```

---

# 6. 自动维护 tsvector

不要每次查询时：

```sql
to_tsvector(...)
```

生产环境更推荐：

```text
INSERT
 ↓
content_tsv 自动生成
```

可以使用 Generated Column，具体取决于你的 PostgreSQL 版本。

例如：

```sql
content_tsv tsvector
GENERATED ALWAYS AS
(to_tsvector('english', content)) STORED
```

这样：

```text
content
 ↓
自动
 ↓
tsvector
```

---

# 7. Hybrid Search

现在：

```text
Question
   │
   ├───────────────┐
   ▼               ▼
Vector Search   Keyword Search
   │               │
   ▼               ▼
Top 20           Top 20
   │               │
   └───────┬───────┘
           ▼
        Merge
           ▼
       Reranking
           ▼
         Top 5
```

这是今天最重要的架构。

---

# 8. Vector Search SQL

假设：

```text
query_embedding
```

SQL：

```sql
SELECT
    id,
    document_id,
    content,
    metadata,
    1 - (embedding <=> :query_embedding)
        AS similarity
FROM document_chunks
WHERE tenant_id = :tenant_id
ORDER BY embedding <=> :query_embedding
LIMIT 20;
```

这里：

```text
<=> 
```

代表 pgvector 的 cosine distance 运算符。

---

# 9. Keyword Search

```sql
SELECT
    id,
    document_id,
    content,
    metadata,
    ts_rank(
        content_tsv,
        plainto_tsquery('english', :query)
    ) AS keyword_score
FROM document_chunks
WHERE tenant_id = :tenant_id
AND content_tsv @@
    plainto_tsquery('english', :query)
ORDER BY keyword_score DESC
LIMIT 20;
```

---

# 10. 两个结果集

Vector：

```text
id   score
A    0.91
B    0.88
C    0.85
```

Keyword：

```text
id   score
B    0.93
D    0.87
A    0.80
```

不能直接：

```text
0.91 + 0.93
```

因为两个 score 的尺度不一样。

需要：

**Score Normalization / Rank Fusion**

---

# 11. 推荐第一版：RRF

RRF：

**Reciprocal Rank Fusion**

公式：

```text
RRF(d) =
Σ 1 / (k + rank(d))
```

通常：

```text
k = 60
```

例如：

```text
Vector Rank:
A = 1
B = 2
C = 3

Keyword Rank:
B = 1
D = 2
A = 3
```

最终：

```text
B
A
C/D
```

---

# 12. 为什么 RRF 很实用

它不要求：

```text
Vector score
```

和：

```text
Keyword score
```

在同一个数值范围。

只看：

```text
Rank
```

所以非常适合第一版 Hybrid Search。

---

# 13. Java Hybrid Search

建立：

```text
HybridSearchService
```

结构：

```java
public List<Document> search(
        String query,
        String tenantId) {

    List<Document> vectorResults =
        vectorSearch(query, tenantId);

    List<Document> keywordResults =
        keywordSearch(query, tenantId);

    return fuse(
        vectorResults,
        keywordResults
    );
}
```

---

# 14. RRF

概念代码：

```java
Map<String, Double> scores =
    new HashMap<>();

int k = 60;

for (int rank = 1;
     rank <= vectorResults.size();
     rank++) {

    String id =
        vectorResults.get(rank - 1).getId();

    scores.merge(
        id,
        1.0 / (k + rank),
        Double::sum
    );
}
```

Keyword：

```java
for (int rank = 1;
     rank <= keywordResults.size();
     rank++) {

    String id =
        keywordResults.get(rank - 1).getId();

    scores.merge(
        id,
        1.0 / (k + rank),
        Double::sum
    );
}
```

最后：

```text
sort by score DESC
```

---

# 15. Metadata Filtering

这是企业 RAG 必须做的。

例如：

```text
tenantId = company-A
department = engineering
documentType = policy
```

查询：

```text
Vector Search
      │
      ▼
Metadata Filter
      │
      ▼
Similarity
```

而不是：

```text
全部公司文档
 ↓
Vector Search
 ↓
再过滤
```

---

# 16. Metadata Schema

建议：

```json
{
  "tenantId": "company-a",
  "department": "engineering",
  "documentType": "aws",
  "source": "ecs-guide.pdf",
  "page": 15,
  "version": 2
}
```

---

# 17. Chunk Size 实验

Day 25：

```text
600–800 tokens
```

今天开始测试：

```text
300
500
700
1000
1500
```

不要猜。

建立 Evaluation Dataset 后比较。

---

# 18. Chunk 太小

例如：

```text
300 tokens
```

优点：

```text
精确
```

缺点：

```text
上下文不足
语义不完整
```

---

# 19. Chunk 太大

例如：

```text
1500 tokens
```

优点：

```text
上下文完整
```

缺点：

```text
检索不精确
Token 增加
LLM 成本增加
```

所以需要实验。

---

# 20. 推荐第一轮实验

建立：

```text
Chunk Strategy A
500 tokens / 50 overlap

Chunk Strategy B
800 tokens / 100 overlap

Chunk Strategy C
1200 tokens / 150 overlap
```

然后同一套问题测试。

---

# 21. Top-K

不要直接：

```text
Vector Search
LIMIT 5
```

推荐：

```text
Vector Search
LIMIT 20
       ↓
Hybrid
       ↓
Rerank
       ↓
Top 5
```

即：

```text
Candidate K = 20
Final K = 5
```

---

# 22. 为什么先取 20

如果直接：

```text
Top 5
```

可能：

```text
真正相关文档
Rank 7
```

已经被丢掉。

先：

```text
Top 20
```

然后：

```text
Reranker
```

重新排序。

---

# 23. Reranking

Reranker 的作用：

```text
Question
+
Candidate Documents
       ↓
Reranker
       ↓
真正相关性排序
```

例如：

```text
Initial Vector Search

A = 0.91
B = 0.88
C = 0.86
D = 0.84
E = 0.82
```

Reranker：

```text
B = 0.97
D = 0.95
A = 0.72
E = 0.60
C = 0.51
```

最终：

```text
B
D
A
```

---

# 24. Reranker 不等于 LLM

Reranker 专门做：

```text
Question
vs
Document
```

相关性判断。

不是：

```text
Question
 ↓
Generate Answer
```

因此：

```text
Retriever
 ↓
Reranker
 ↓
LLM
```

是很典型的 RAG 架构。

---

# 25. 今天可以先不部署独立 Reranker

如果你刚开始：

```text
Vector
+
Keyword
+
RRF
```

已经足够。

下一阶段再接：

```text
Amazon Bedrock
Reranker
```

或者其他专用 reranking 模型。

不要一开始把架构做得过重。

---

# 26. Retrieval Threshold

例如：

```text
similarity >= 0.75
```

才接受。

但注意：

**0.75 不是通用标准。**

不同 embedding model：

```text
score distribution
```

可能完全不同。

正确方法：

```text
Evaluation Dataset
 ↓
观察 score distribution
 ↓
选择 threshold
```

---

# 27. RAG Evaluation Dataset

今天建立：

```text
evaluation/
└── rag_questions.json
```

例如：

```json
[
  {
    "question": "What is ECS Fargate?",
    "expectedSources": [
      "aws-ecs.pdf"
    ]
  },
  {
    "question": "What is an IAM role?",
    "expectedSources": [
      "aws-iam.pdf"
    ]
  }
]
```

---

# 28. 更完整的 Dataset

建议：

```json
{
  "id": "q001",
  "question": "What is ECS Fargate?",
  "expectedDocuments": [
    "aws-ecs.pdf"
  ],
  "expectedPages": [
    5,
    6
  ],
  "expectedAnswer": "..."
}
```

---

# 29. Retrieval Precision

例如：

```text
Top 5
```

其中：

```text
3 个相关
2 个不相关
```

Precision：

```text
3 / 5 = 60%
```

---

# 30. Retrieval Recall

假设真正相关：

```text
5 documents
```

系统找到：

```text
4
```

Recall：

```text
4 / 5 = 80%
```

---

# 31. 为什么两个都重要

如果：

```text
Precision 很低
```

说明：

```text
找到很多垃圾
```

如果：

```text
Recall 很低
```

说明：

```text
漏掉真正重要的文档
```

所以不要只看：

```text
"LLM answer 看起来不错"
```

---

# 32. Answer Evaluation

至少测试：

```text
Retrieval
+
Answer
```

例如：

```text
Question
   ↓
Retrieved Context
   ↓
Answer
```

评估：

```text
Faithfulness
Relevance
Correctness
```

---

# 33. Faithfulness

问题：

> 回答是不是基于检索到的 Context？

例如 Context：

```text
ECS is a container service.
```

回答：

```text
ECS is a container orchestration service.
```

基本合理。

但如果回答：

```text
ECS was launched in 2014...
```

而 Context 没说：

```text
可能是 hallucination
```

---

# 34. RAG Evaluation Pipeline

最终：

```text
rag_questions.json
        │
        ▼
    Retriever
        │
        ▼
    Top-K Docs
        │
        ▼
       LLM
        │
        ▼
      Answer
        │
        ▼
    Evaluation
        │
        ├── Precision
        ├── Recall
        ├── Faithfulness
        └── Relevance
```

---

# 35. 建立实验记录

例如：

| Strategy | Chunk | Retrieval     | Precision | Recall |
| -------- | ----: | ------------- | --------: | -----: |
| A        |   500 | Vector        |      0.72 |   0.76 |
| B        |   800 | Vector        |      0.75 |   0.81 |
| C        |   800 | Hybrid        |      0.84 |   0.89 |
| D        |  1000 | Hybrid+Rerank |      0.91 |   0.93 |

你最终要找到类似：

```text
Best RAG Configuration
```

而不是凭感觉调整。

---

# 36. Day 26 推荐配置

第一版可以从：

```text
Chunk:
800 tokens

Overlap:
100

Vector Candidate:
20

Keyword Candidate:
20

Fusion:
RRF

Final:
5

Metadata:
tenantId

Reranker:
暂时关闭
```

开始。

然后再实验：

```text
Reranker ON
```

---

# 37. Java 最终 RAG Pipeline

```java
public RagResult query(
        String question,
        String tenantId) {

    List<Document> vector =
        vectorSearch(question, tenantId);

    List<Document> keyword =
        keywordSearch(question, tenantId);

    List<Document> candidates =
        rrf(vector, keyword);

    List<Document> topDocs =
        candidates.stream()
            .limit(5)
            .toList();

    String context =
        buildContext(topDocs);

    String answer =
        llm.generate(
            question,
            context
        );

    return new RagResult(
        answer,
        topDocs
    );
}
```

这就是一个非常清晰的第一版 Hybrid RAG。

---

# 38. 加上 OpenTelemetry

Day 22 的 Trace：

```text
/api/chat
```

今天变成：

```text
/api/chat
│
├── embedding
│
├── vector.search
│
├── keyword.search
│
├── hybrid.fusion
│
├── reranking
│
└── llm.request
```

你可以看到：

```text
Total = 2.8 sec

Embedding = 150ms
Vector = 40ms
Keyword = 15ms
RRF = 2ms
LLM = 2.4s
```

---

# 39. 监控最重要的 RAG Metrics

增加：

```text
rag.retrieval.latency
rag.vector_search.latency
rag.keyword_search.latency
rag.rerank.latency
rag.context.tokens
rag.top_k
rag.empty_retrieval.count
rag.answer.latency
```

---

# 40. 一个非常有用的 Metric

：

```text
rag.empty_retrieval.count
```

如果：

```text
1000 queries
```

其中：

```text
250
```

没有找到有效文档：

```text
25%
```

说明：

```text
知识库
Chunk
Embedding
Query
```

至少有一个地方需要优化。

---

# 41. 另一个重要 Metric

```text
rag.context.tokens
```

例如：

```text
平均 8,000 tokens
```

可能太多。

可以优化：

```text
Top-K
Chunk Size
Reranker
```

如果：

```text
平均 800 tokens
```

可能更高效。

---

# 42. 不要把整个知识库塞给 LLM

错误：

```text
Question
 ↓
全部 1000 chunks
 ↓
LLM
```

正确：

```text
Question
 ↓
Retrieve 20
 ↓
Rerank
 ↓
Top 5
 ↓
LLM
```

---

# 43. Hybrid Search 最终架构

```text
                         Question
                            │
                            ▼
                       Query Embedding
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
        Vector Search              Full Text Search
              │                           │
          Top 20                       Top 20
              │                           │
              └─────────────┬─────────────┘
                            ▼
                         RRF Fusion
                            │
                          Top 20
                            │
                            ▼
                         Reranker
                            │
                           Top 5
                            │
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

# 44. Day 26 实战任务

今天不要只看教程。

请实际完成：

### Task 1

建立：

```text
content_tsv
```

和 GIN Index。

### Task 2

实现：

```text
vectorSearch()
```

### Task 3

实现：

```text
keywordSearch()
```

### Task 4

实现：

```text
rrf()
```

### Task 5

修改：

```text
RagService
```

变成：

```text
Vector
+
Keyword
→
RRF
→
Top 5
→
LLM
```

### Task 6

建立：

```text
rag_questions.json
```

至少：

```text
20 questions
```

### Task 7

记录：

```text
Precision
Recall
Latency
```

---

# 45. Day 26 最终项目结构

```text
backend/
│
├── controller/
│   ├── ChatController.java
│   └── DocumentController.java
│
├── service/
│   ├── ChatService.java
│   ├── RagService.java
│   ├── HybridSearchService.java
│   ├── VectorSearchService.java
│   ├── KeywordSearchService.java
│   └── RerankService.java
│
├── repository/
│   └── DocumentChunkRepository.java
│
├── model/
│   ├── ChatRequest.java
│   ├── RagResult.java
│   └── SearchResult.java
│
├── config/
│   ├── AiConfig.java
│   └── VectorConfig.java
│
└── evaluation/
    └── RagEvaluationService.java
```

---

# 46. Day 26 检查清单

```text
[ ] PostgreSQL Full-Text Search
[ ] tsvector
[ ] GIN Index
[ ] Vector Search
[ ] Keyword Search
[ ] Hybrid Search
[ ] RRF
[ ] Metadata Filtering
[ ] Tenant Isolation
[ ] Chunk Size Experiment
[ ] Top-K Experiment
[ ] Similarity Threshold
[ ] Reranking 基础
[ ] Evaluation Dataset
[ ] Retrieval Precision
[ ] Retrieval Recall
[ ] Faithfulness
[ ] Relevance
[ ] RAG Metrics
[ ] OpenTelemetry
```

---

# Day 24 → Day 26

现在你的 RAG 已经从：

```text
Day 24

Question
 ↓
Vector Search
 ↓
LLM
```

升级成：

```text
Day 26

Question
 ↓
Embedding
 ↓
┌──────────────────┐
│ Vector Search    │
│        +         │
│ Keyword Search  │
└────────┬─────────┘
         ↓
       RRF
         ↓
      Top 20
         ↓
     Reranker
         ↓
       Top 5
         ↓
      Context
         ↓
      Bedrock
         ↓
      Answer
         ↓
     Sources
```

这已经是一个比较标准的 **Production RAG 基础架构**。

**Day 27** 建议做 **Spring AI Agent / Tool Calling**：让 Java AI 应用不再只是“搜索知识库然后回答”，而是能够调用 **数据库、REST API、AWS 服务、搜索工具**，形成真正的 AI Agent。
