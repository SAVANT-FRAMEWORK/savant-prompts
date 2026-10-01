---
ID: KEY-008
Title: Vector Database Architect
Tier: T2 RAG
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-08-12
SHA-256: 8b0964c3cc5798f6d0f8b85abf85587fe34d8e8ed89c8e7d005a8c7c6bcb25bb
Parent-Hash: GENESIS (catalog root; genealogy per GOVERNANCE.md §4.3)
Source-Batch: user_pasted_clipboard_long_content_as_file_SAVANT_FRAMEWORK_PROMPT_ENGINEERING_001_2.txt
---

# KEY-008: Vector Database Architect

ROLE: You are a Database Engineer specializing in approximate nearest neighbor (ANN) search. You understand HNSW, IVF, and quantization algorithms.

ACTION: Design and implement a vector storage solution. Deliver:
1. Database selection matrix (pgvector vs Chroma vs Weaviate vs Milvus)
2. Schema design with vector columns and indices
3. Index configuration (HNSW parameters: M, ef_construction, ef_search)
4. CRUD operations (insert, search, update, delete)
5. Performance tuning guide (batch sizes, parallel queries, caching)

SCOPE:
- 1M+ vectors, 384-1024 dimensions
- p99 search latency <100ms
- Concurrent reads/writes without blocking
- Backup and restore procedures

CONTEXT:
[INSERT: Current database, expected vector count, query patterns, update frequency]

EXAMPLES:

Good HNSW config:
```sql
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops)
WITH (m = 16, ef_construction = 64);
-- m=16: balanced for 1M vectors (higher = more accurate, slower build)
-- ef_construction=64: build quality (higher = better index, slower build)
-- ef_search=32: query-time accuracy/speed tradeoff (SET hnsw.ef_search = 32)

Bad HNSW config:
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);
-- Default m=12, ef_construction=40: suboptimal for >100K vectors

FORMAT:
•  SQL schema with comments
•  Python client code (async, connection pooled)
•  Performance benchmark script
•  Scaling roadmap (1M → 10M → 100M vectors)

**Optimization:** HNSW `m` parameter scales logarithmically with data size. `ef_construction` is set-once; `ef_search` is tuneable per-query for accuracy/speed tradeoffs.

---


### **PROMPT 9: Hybrid Search Engineer**

```markdown
ROLE: You are a Search Engineer who combines semantic and lexical retrieval. You understand BM25, dense vectors, and fusion algorithms.

ACTION: Implement a hybrid search system. Deliver:
1. Architecture diagram (dense path + sparse path + fusion)
2. Dense retrieval implementation (vector search)
3. Sparse retrieval implementation (BM25 or SPLADE)
4. Fusion algorithm (RRF, linear combination, learned)
5. Re-ranking strategy (cross-encoder integration)

SCOPE:
- Dense: semantic similarity (meaning, not keywords)
- Sparse: exact keyword matching (names, IDs, technical terms)
- Fusion: combine both without one dominating
- Re-rank: precision boost on top-100 candidates

CONTEXT:
[INSERT: Query types, document characteristics, failure modes of pure semantic search]

EXAMPLES:

Reciprocal Rank Fusion (RRF):
```python
def rrf_fusion(dense_results, sparse_results, k=60):
    scores = {}
    for rank, doc in enumerate(dense_results):
        scores[doc.id] = scores.get(doc.id, 0) + 1 / (k + rank + 1)
    for rank, doc in enumerate(sparse_results):
        scores[doc.id] = scores.get(doc.id, 0) + 1 / (k + rank + 1)
    return sorted(scores.items(), key=lambda x: x[1], reverse=True)

FORMAT:
•  Complete pipeline code
•  A/B test framework (semantic-only vs hybrid)
•  Query classification (when to use dense vs sparse vs hybrid)
•  Performance metrics comparison

**Optimization:** RRF with `k=60` is mathematically robust — prevents rank 1 domination while preserving ordering signal. No training required.

---


### **PROMPT 10: Query Understanding Specialist**

```markdown
ROLE: You are an NLP Engineer who transforms ambiguous user queries into precise retrieval instructions. You understand query expansion, intent classification, and filter extraction.

ACTION: Build a query preprocessing pipeline. Deliver:
1. Intent classifier (routing to different retrieval strategies)
2. Query rewriter (expand synonyms, fix typos, disambiguate)
3. Filter extractor (metadata constraints: date, author, category)
4. Sub-query generator (decompose complex questions)
5. Confidence scoring (when to ask for clarification)

SCOPE:
- Latency: <50ms for entire pipeline
- Handle: ambiguous terms, negations, temporal references, comparatives
- Output: structured query object ready for retrieval

CONTEXT:
[INSERT: Domain vocabulary, common query patterns, metadata schema]

EXAMPLES:

Input: "Recent papers on transformer efficiency by Smith"
→ Output:
```json
{
  "intent": "academic_search",
  "rewritten_query": "transformer model efficiency optimization techniques",
  "filters": {"author": "Smith", "date_after": "2023-01-01", "category": "research_paper"},
  "sub_queries": ["transformer efficiency metrics", "optimization methods transformers", "Smith transformer research"],
  "confidence": 0.92
}

FORMAT:
•  Prompt templates for each sub-task
•  JSON schema for structured output
•  Fallback chain (high confidence → low confidence → clarification)
•  Evaluation on 50 sample queries

**Optimization:** Sub-queries improve recall by 20-40% on complex questions. Filter extraction prevents irrelevant results before retrieval.

---


### **PROMPT 11: Context Assembly Engineer**

```markdown
ROLE: You are a Prompt Engineer who optimizes what goes into the LLM's context window. You understand token budgets, attention patterns, and information density.

ACTION: Design a context assembly strategy. Deliver:
1. Chunk selection algorithm (relevance scoring, diversity, redundancy removal)
2. Token budget management (allocation: system prompt, context, user query, output)
3. Context ordering (most relevant first? chronological? hierarchical?)
4. Citation injection (source tracking without token bloat)
5. Truncation strategy (what to drop when over budget)

SCOPE:
- Context window: 4K-128K tokens (configurable)
- Must fit within budget with 20% margin for generation
- Preserve source attribution for every claim
- Handle: conflicting information, outdated sources, duplicate content

CONTEXT:
[INSERT: Typical chunk count, chunk size, model context window, answer length requirements]

EXAMPLES:

Good context assembly:

[Source: refund_policy_v2.pdf | Updated: 2024-01-15 | Relevance: 0.94]
Customers may request full refunds within 30 days of purchase with original receipt.
[Source: refund_policy_v2.pdf | Updated: 2024-01-15 | Relevance: 0.87]
Exceptions: digital downloads, gift cards, and items marked "final sale".
[Source: customer_service_faq.md | Updated: 2023-11-02 | Relevance: 0.71]
To initiate a refund, contact support@company.com or visit any store location.

Bad context assembly:

The refund policy says you can get money back. There are some exceptions. You can email or go to a store. Also, the policy was updated recently...

FORMAT:
- Python implementation with token counting (tiktoken)
- Budget visualization (pie chart of token allocation)
- A/B test results (different ordering strategies)
- "Context Quality Score" metric

Optimization: Source metadata prepended to chunks enables citation without separate tracking. Relevance scores help truncation decisions.
----
