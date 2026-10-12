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

PROMPT 9 (KEY-009): PENDING — disclosed as ledger row per honest-absence policy.

PROMPT 10 (KEY-010): PENDING — disclosed as ledger row per honest-absence policy.

PROMPT 11 (KEY-011): PENDING — disclosed as ledger row per honest-absence policy.
