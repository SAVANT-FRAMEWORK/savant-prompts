---
ID: P09
Title: Hybrid Search Engineer
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P09: Hybrid Search Engineer

P09 builds the retrieval execution layer of the RAG pipeline: dense vector search and sparse keyword search running in parallel, fused into a single ranked candidate set. It replaces pure-semantic retrieval — where a user searching "heart attack" misses documents written as "myocardial infarction," and a user searching an exact part number misses documents that only paraphrase it — with a hybrid engine whose fusion strategy is benchmarked against both failure modes. The objective is that users find answers whether they use technical jargon or plain language.

## Methodology

Three cooperating subsystems:

1. **Dense retriever.** Semantic similarity search over the P08 vector index using the query embedding endpoint from P07. Handles paraphrase, synonymy, and conceptual queries; weak on exact strings, rare tokens, and keyword-dominant queries.
2. **Sparse retriever.** BM25 / TF-IDF keyword retrieval over the same corpus. Handles exact terminology, identifiers, and keyword-heavy queries; weak on semantic nuance. Metadata pre-filtering (date, source, tenant) applies to both retrievers before fusion, so constraints harden results rather than merely re-ordering them.
3. **Fusion engine.** Parallel dense and sparse result lists are combined via Reciprocal Rank Fusion or a learned weighted combination, with the fusion strategy selected by A/B test rather than assumption. Exact and near-duplicate chunks — including overlap duplicates produced by P06 chunking — are removed after fusion so repeated content does not inflate the candidate set. Every plan variant carries a measured latency cost; fusion overhead is budgeted, not discovered in production.

## Implementation

Deployment sequence from the operations abstract:

- Week 1: implement dense retrieval (semantic).
- Week 2: implement sparse retrieval (BM25/keyword).
- Week 3: design the fusion strategy (RRF or learned).
- Week 4: A/B test against pure semantic retrieval.
- Week 5: deploy the winner; monitor both metric families (keyword and semantic hit rates) permanently.

Concrete artifacts:

- `deliverables/p9-hybrid-search-pipeline.yml` — retriever configuration, metadata filters, fusion strategy and weights.
- `deliverables/p9-fusion-ab-results.json` — A/B comparison of pure semantic, pure sparse, and fused retrieval on a validation set.
- `deliverables/p9-dedup-policy.md` — exact and near-duplicate thresholds, parent-chunk reference handling from P06.
- `deliverables/p9-latency-budget.md` — per-retriever and fusion latency allocation.

Debugging checks: hybrid retrieval outperforms pure vector on keyword-heavy queries in the validation suite; under 5% duplicate content in the final candidate set; fusion adds less than 20 ms over single-retriever latency; tenant-filtered queries return zero cross-tenant chunks.

Telemetry thresholds from the operations abstract: keyword-query hit rate above 80% (the canonical case measured under 40% with pure semantic retrieval); semantic-query hit rate maintained above 85% after fusion — a fusion strategy that rescues keyword queries but degrades semantic ones is a failed configuration, not a trade-off.

Downstream integration: P10 consumes the fused, deduplicated candidate set for reranking; P11 receives the ranked output via P10; P12 runs the A/B comparisons and tracks both hit-rate families as regression metrics.

## Merits

Stated outcomes: keyword-query hit rate rising from under 40% to above 80% when fusion is added to a pure-semantic baseline, with semantic hit rate preserved above 85% and fusion latency overhead under 20 ms. The operations abstract's reference case is a medical database where clinicians searching "myocardial infarction" and patients searching "heart attack" retrieve the same relevant articles. The structural merit is robustness across query vocabularies: retrieval quality no longer depends on whether the user's words match the corpus's words.

## Guidance for Use

**When to reach for P09:** after P06–P08 are operational; when keyword or identifier queries fail despite a healthy vector index; and when duplicate chunks from overlap inflate candidate sets.

**Prerequisites:** P06 (chunks indexed and queryable), P07 (query embedding endpoint), P08 (index built with sparse retrieval and metadata filtering enabled).

**How to run it well:** tune RRF's k parameter or learned fusion weights on a validation set, never by intuition; A/B test fused retrieval against pure semantic on your own query mix before deploying; keep deduplication thresholds strict enough to catch overlap duplicates (use P06 parent-chunk references); and monitor keyword and semantic hit rates as separate metrics — a single blended number hides the exact failure this prompt exists to prevent.

**Pairing:** P10 sits directly upstream and downstream of judgment here — it classifies and rewrites the queries P09 executes, then reranks P09's fused candidates. P12's evaluation framework should run the fusion A/B tests; P08's hybrid query planner is the index-level counterpart of this engine.

**Common misuse:** pure semantic retrieval shipped because "the demo worked"; fusion weights left at defaults; deduplication so loose that overlap duplicates survive into the context window; and treating metadata filters as post-retrieval re-ranking instead of pre-retrieval constraints, which silently returns results that should have been excluded.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
