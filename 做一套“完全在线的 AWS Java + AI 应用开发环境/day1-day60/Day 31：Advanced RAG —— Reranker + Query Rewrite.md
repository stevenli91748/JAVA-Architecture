# Day 31：Advanced RAG —— Reranker + Query Rewrite

Day 30 已经完成第一套完整的 AWS Java AI 企业应用。

从今天开始进入 **Day 31–60 AI Engineer 实战阶段**。

今天重点解决一个问题：

> **用户的问题不一定适合直接拿去做 Vector Search。**

例如用户问：

> “为什么我们 ECS 上那个 Java 服务最近响应这么慢？”

直接 Embedding 搜索，可能找不到最相关的内容。

今天升级成：

```text id="j3v8xq"
User Question
      ↓
Query Rewrite
      ↓
Better Search Query
      ↓
Hybrid Search
      ↓
Top 20
      ↓
Reranker
      ↓
Top 5
      ↓
LLM
      ↓
Answer
```

---

# 1. 今天学习的核心

```text id="f8k2mq"
1. Query Rewrite
2. Query Expansion
3. Multi-Query Retrieval
4. Hybrid Search
5. Reranking
6. Retrieval Threshold
7. Context Compression
8. RAG Evaluation
```

今天重点：

**Query Rewrite + Reranking。**

---

# 2. 为什么普通 RAG 不够

Day 26：

```text
Question
 ↓
Embedding
 ↓
Vector Search
 ↓
Top 20
 ↓
LLM
```

问题：

用户的问题可能：

```text
口语化
很长
包含上下文
有代词
有错别字
多个问题
```

例如：

```text
“那个我们之前说的 AWS Java 服务为什么突然变慢？”
```

Embedding 未必能找到：

```text
ECS
CPU
Memory
JVM
GC
Latency
```

---

# 3. Query Rewrite

先让 LLM 把问题转换成更适合检索的查询。

用户：

```text
“那个 Java 服务为什么最近变慢？”
```

Rewrite：

```text
“Java Spring Boot ECS service
high latency CPU memory JVM GC”
```

然后：

```text
Rewrite
 ↓
Hybrid Search
```

---

# 4. Query Rewrite 不等于回答

非常重要：

```text
Query Rewrite
=
Search Optimization
```

不是：

```text
Query Rewrite
=
Final Answer
```

流程：

```text
User Question
      ↓
Rewrite
      ↓
Search
      ↓
Context
      ↓
LLM
      ↓
Answer
```

---

# 5. Spring AI Query Rewrite Service

建立：

```text id="q5n8mz"
QueryRewriteService
```

例如：

```java
@Service
public class QueryRewriteService {

    private final ChatClient chatClient;

    public String rewrite(String question) {

        return chatClient
            .prompt()
            .system("""
                Rewrite the user question
                into a concise search query.

                Preserve important technical terms.
                Do not answer the question.
                """)
            .user(question)
            .call()
            .content();
    }
}
```

---

# 6. 加入 Conversation Memory

Day 28 已经有 Memory。

所以：

用户：

> “我们刚才讨论的 ECS 服务”

Agent 可以利用：

```text
Conversation Summary
+
Recent Messages
```

Rewrite：

```text
“Spring Boot ECS service performance
latency investigation”
```

这样 Query Rewrite 就真正开始和 Memory 配合。

---

# 7. Query Rewrite Pipeline

```text
User
 │
 ▼
Memory
 │
 ▼
Query Rewrite
 │
 ▼
Search Query
 │
 ▼
Hybrid Search
```

---

# 8. Query Expansion

另一种方法：

不要生成一个 Query。

生成多个：

```text
Query 1:
ECS Java service latency

Query 2:
Spring Boot ECS performance

Query 3:
JVM GC CPU memory ECS
```

然后：

```text
Query 1 → Search
Query 2 → Search
Query 3 → Search
```

最后：

```text
RRF
 ↓
Rerank
```

---

# 9. Multi-Query Retrieval

架构：

```text
                    User Question
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           Query 1    Query 2    Query 3
              │          │          │
              ▼          ▼          ▼
           Search      Search      Search
              │          │          │
              └──────────┼──────────┘
                         ▼
                        RRF
                         ↓
                       Top 20
                         ↓
                      Reranker
                         ↓
                        Top 5
```

---

# 10. 什么时候使用 Multi-Query

特别适合：

```text id="g8x2wq"
复杂问题
模糊问题
多个表达方式
专业术语
```

例如：

> “ECS 上 Java 服务性能有什么常见问题？”

可以拆成：

```text
ECS performance
Spring Boot performance
JVM performance
Java GC
ECS CPU memory
```

---

# 11. 什么时候不要 Multi-Query

用户：

> “什么是 ECS？”

不需要：

```text
5 queries
```

直接：

```text
Vector + Keyword
```

即可。

否则：

```text
Latency ↑
Cost ↑
```

---

# 12. Reranking

Day 26 已经介绍。

今天正式加入：

```text
Top 20 Candidates
       ↓
Reranker
       ↓
Top 5
```

---

# 13. 为什么需要 Reranker

初始检索：

```text
A 0.91
B 0.89
C 0.87
D 0.85
E 0.84
```

但真正语义相关：

```text
B
D
A
```

Reranker：

```text
B 0.97
D 0.95
A 0.91
C 0.52
E 0.41
```

所以：

```text
Retriever
=
快速召回

Reranker
=
精确排序
```

---

# 14. 两阶段 Retrieval

这是生产 RAG 非常重要的架构：

```text
Stage 1
───────
Fast Retrieval

Vector
+
Keyword
+
Metadata

Top 20–50


Stage 2
───────
Precision Retrieval

Reranker

Top 5
```

---

# 15. 为什么不直接 Rerank 全部文档

假设：

```text
1,000,000 chunks
```

如果：

```text
Reranker
 ↓
1,000,000
```

成本和 latency 都不可接受。

所以：

```text
1,000,000
 ↓
Vector/Keyword
 ↓
20
 ↓
Reranker
 ↓
5
```

---

# 16. 推荐参数

今天先使用：

```text id="5w9zj2"
Vector Candidate = 20

Keyword Candidate = 20

RRF Candidate = 20

Reranker Candidate = 20

Final Context = 5
```

然后用 Evaluation Dataset 调整。

---

# 17. Rerank Service

设计：

```java
public interface RerankService {

    List<SearchResult> rerank(
        String query,
        List<SearchResult> candidates
    );
}
```

这样以后可以替换：

```text
Bedrock Reranker
Cross Encoder
Cohere
其他模型
```

而不会修改整个 RAG。

---

# 18. RAG Service 最终代码结构

```java
public RagResult retrieve(
        String question,
        String tenantId) {

    String searchQuery =
        queryRewriteService.rewrite(question);

    List<SearchResult> vector =
        vectorSearch.search(
            searchQuery,
            tenantId
        );

    List<SearchResult> keyword =
        keywordSearch.search(
            searchQuery,
            tenantId
        );

    List<SearchResult> candidates =
        fusionService.rrf(
            vector,
            keyword
        );

    List<SearchResult> reranked =
        rerankService.rerank(
            question,
            candidates
        );

    return new RagResult(
        reranked.stream()
            .limit(5)
            .toList()
    );
}
```

注意：

**Reranker 应该使用原始用户问题或经过安全处理的查询作为 relevance query，而不是盲目使用 Rewrite 后的 query。**

---

# 19. 为什么 Reranker 可以使用原问题

用户：

```text
“为什么我们那个 Java 服务最近变慢？”
```

Rewrite：

```text
Java ECS performance latency JVM GC
```

Retriever：

```text
找技术文档
```

Reranker：

```text
用户真正想解决的问题
```

因此：

```text
Retrieval Query
≠
Final Relevance Query
```

这是一个很实用的设计。

---

# 20. Context Compression

假设 Top 5：

```text
5 chunks
```

每个：

```text
800 tokens
```

就是：

```text
4000 tokens
```

但真正相关可能只有：

```text
1000 tokens
```

所以可以：

```text
Chunk
 ↓
Relevant Sentence Extraction
 ↓
Compressed Context
```

---

# 21. Context Compression

例如原文：

```text
ECS supports several networking modes...

[500 words]

Fargate tasks use ENI...

[300 words]

CPU utilization can be monitored...
```

用户只问：

> ECS CPU 性能

可以压缩成：

```text
CPU utilization can be monitored...
```

减少：

```text
Token
Latency
Cost
Noise
```

---

# 22. 今天先不急着实现 Compression

推荐：

```text
Day 31
Query Rewrite
+
Hybrid
+
Rerank
```

然后：

```text
Day 32
Advanced RAG
```

再加入：

```text
Context Compression
Parent Document Retrieval
Metadata Routing
```

---

# 23. Query Rewrite 的风险

Query Rewrite 本身也是 LLM。

所以：

```text
User
 ↓
Rewrite LLM
```

会增加：

```text
Latency
Cost
```

例如：

```text
原始：
2.5 sec

Rewrite：
+300ms
```

如果 Query 很简单：

```text
“What is ECS?”
```

可以直接搜索。

---

# 24. Query Complexity Router

建立：

```text id="q2v8ms"
QueryRouter
```

判断：

```text
Simple
Complex
```

例如：

```text
What is ECS?
        ↓
Simple
        ↓
Direct Retrieval
```

复杂：

```text
Compare ECS and EKS
and explain which is
better for our architecture.
        ↓
Complex
        ↓
Query Rewrite
+
Multi Query
+
Rerank
```

---

# 25. Query Router

最终：

```text
                  Question
                      │
                      ▼
                 Query Router
                 /           \
                /             \
          Simple              Complex
             │                   │
             ▼                   ▼
        Direct Search       Query Rewrite
                                 │
                            Multi Query
                                 │
                            Hybrid Search
                                 │
                              Rerank
```

---

# 26. 不要让 Router 无限复杂

第一版只需要：

```text
SIMPLE
COMPLEX
```

以后再：

```text
KNOWLEDGE
DATABASE
API
ANALYTICS
```

最终可以演化成：

```text
Intent Router
```

---

# 27. RAG Evaluation

今天重新测试 Day 26 的：

```text
100 questions
```

比较：

### Baseline

```text
Vector
```

### Version 2

```text
Vector + Keyword
```

### Version 3

```text
Hybrid + RRF
```

### Version 4

```text
Hybrid + RRF + Rerank
```

### Version 5

```text
Rewrite + Hybrid + Rerank
```

---

# 28. 实验表

建立：

```text id="v9m4k2"
rag-experiments.csv
```

记录：

| Version | Rewrite | Hybrid | Rerank | Precision | Recall | Latency |
| ------- | ------- | ------ | ------ | --------: | -----: | ------: |
| V1      | ❌       | ❌      | ❌      |      0.72 |   0.76 |   450ms |
| V2      | ❌       | ✅      | ❌      |      0.81 |   0.84 |   520ms |
| V3      | ❌       | ✅      | ✅      |      0.89 |   0.91 |   780ms |
| V4      | ✅       | ✅      | ✅      |      0.92 |   0.94 |    1.1s |

你的真实数据会不同。

重点是：

> **以后所有 RAG 优化都必须用实验数据证明。**

---

# 29. 关键指标

今天重点观察：

```text
Retrieval Precision
Retrieval Recall
MRR
NDCG
Latency
Cost
```

---

# 30. MRR

MRR：

**Mean Reciprocal Rank**

如果正确文档：

```text
Rank 1
```

得分：

```text
1
```

Rank 2：

```text
0.5
```

Rank 5：

```text
0.2
```

所以：

```text
MRR 越高
→
正确结果越靠前
```

---

# 31. NDCG

NDCG 更适合：

```text
多个结果
不同 relevance
```

例如：

```text
Rank 1 = highly relevant
Rank 2 = relevant
Rank 3 = weak
Rank 4 = irrelevant
```

可以更准确评价：

```text
Reranker
```

---

# 32. Day 31 推荐实验

建立：

```text
20 easy
30 medium
30 complex
20 multi-hop
```

共：

```text
100 questions
```

分类：

```text
Simple
Technical
Multi-hop
Ambiguous
```

---

# 33. Multi-hop Question

例如：

> “我们的 Spring Boot 服务部署在 ECS Fargate，它使用什么数据库？这个数据库运行在哪个 AWS 网络层？”

需要：

```text
Document A
+
Document B
+
Document C
```

这不是普通单文档查询。

以后 Day 32 会重点处理。

---

# 34. Day 31 Agent Integration

最终 Agent：

```text
User
 ↓
Agent
 ↓
Query Router
 ↓
RAG
```

如果：

```text
Knowledge Question
```

进入：

```text
Advanced RAG
```

如果：

```text
Database Question
```

进入：

```text
DB Tool
```

如果：

```text
API Question
```

进入：

```text
API Tool
```

---

# 35. 最终 Agent 架构

```text
                           User
                             │
                             ▼
                           Agent
                             │
                       Intent Router
                             │
          ┌──────────────────┼──────────────────┐
          ▼                  ▼                  ▼
       Knowledge           Database             API
          │                  │                  │
          ▼                  ▼                  ▼
     Advanced RAG          DB Tool           API Tool
          │
    ┌─────┴─────┐
    ▼           ▼
 Query Rewrite  Memory
    │
    ▼
 Multi Query
    │
    ▼
 Hybrid Search
    │
    ▼
 RRF
    │
    ▼
 Reranker
    │
    ▼
 Top 5
    │
    ▼
 Bedrock
```

---

# 36. 今天的 Java 项目结构

```text id="t3q8mw"
backend/
│
├── agent/
│   ├── AiAgentService.java
│   └── QueryRouter.java
│
├── rag/
│   ├── RagService.java
│   ├── QueryRewriteService.java
│   ├── MultiQueryService.java
│   ├── HybridSearchService.java
│   ├── RrfService.java
│   ├── RerankService.java
│   └── ContextBuilder.java
│
├── tools/
│   ├── KnowledgeTool.java
│   ├── DatabaseTool.java
│   └── ApiTool.java
│
├── memory/
│   └── ChatMemoryService.java
│
└── evaluation/
    ├── RagEvaluationService.java
    └── EvaluationResult.java
```

---

# 37. 今天的 Coding Task

### Task 1

创建：

```text
QueryRewriteService
```

---

### Task 2

创建：

```text
MultiQueryService
```

---

### Task 3

创建：

```text
RerankService
```

---

### Task 4

修改：

```text
RagService
```

变成：

```text
Question
 ↓
Router
 ↓
Rewrite
 ↓
Hybrid
 ↓
RRF
 ↓
Rerank
 ↓
Top 5
```

---

### Task 5

建立：

```text
100-question Evaluation Dataset
```

---

### Task 6

比较：

```text
Vector
vs
Hybrid
vs
Hybrid + Rerank
vs
Rewrite + Hybrid + Rerank
```

---

# 38. 今天的测试问题

### 简单

```text
What is Amazon ECS?
```

应该：

```text
Direct Search
```

---

### 技术

```text
How can I reduce JVM memory pressure
on ECS Fargate?
```

应该：

```text
Hybrid
+
Rerank
```

---

### 模糊

```text
Why is the service slow?
```

应该：

```text
Memory
+
Query Rewrite
```

---

### 多问题

```text
Compare ECS and EKS and explain
which one is better for our Java platform.
```

应该：

```text
Multi Query
+
Hybrid
+
Rerank
```

---

# 39. Day 31 最终流程

```text
                    User Question
                          │
                          ▼
                       Memory
                          │
                          ▼
                    Query Router
                     /         \
                    /           \
               Simple          Complex
                  │                │
                  │                ▼
                  │          Query Rewrite
                  │                │
                  │          Multi Query
                  │                │
                  └──────┬─────────┘
                         ▼
                    Hybrid Search
                         │
                         ▼
                         RRF
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
                       Agent
                         │
                         ▼
                      Bedrock
                         │
                         ▼
                       SSE
                         │
                         ▼
                       React
```

---

# 40. Day 31 检查清单

```text
[ ] Query Rewrite
[ ] Query Expansion
[ ] Multi-Query Retrieval
[ ] Query Router
[ ] Hybrid Search
[ ] RRF
[ ] Reranker
[ ] Top-K
[ ] Similarity Threshold
[ ] Context Compression 基础
[ ] Memory + RAG
[ ] Agent + Advanced RAG
[ ] Retrieval Precision
[ ] Retrieval Recall
[ ] MRR
[ ] NDCG
[ ] Latency
[ ] Cost
[ ] 100-question Evaluation
[ ] RAG Experiment Tracking
```

---

## Day 31 的核心认知

Day 26 的 RAG：

```text
Question
 ↓
Vector + Keyword
 ↓
Top K
 ↓
LLM
```

Day 31：

```text
Question
 ↓
Memory
 ↓
Query Router
 ↓
Query Rewrite
 ↓
Multi-Query
 ↓
Hybrid Search
 ↓
RRF
 ↓
Reranker
 ↓
Context
 ↓
Agent
 ↓
Bedrock
 ↓
SSE
```

这一步非常重要：**你已经从“会使用 RAG”进入“会设计和优化 RAG Pipeline”。**

**Day 32** 建议做 **Advanced RAG：Parent-Child Retrieval + Contextual Chunking + Metadata Routing + Multi-Hop RAG**，重点解决长 PDF、技术文档、跨章节问题，以及“一个答案需要多个文档共同推理”的场景。
