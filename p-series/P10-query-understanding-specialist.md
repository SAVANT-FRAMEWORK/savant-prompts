---
ID: P10
Title: Query Understanding Specialist
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P10: Query Understanding Specialist

P10 transforms raw user queries into retrieval-ready form and reranks what comes back. It replaces verbatim query-to-index handling — where "how do I reset password" can miss the "password reset" document — with intent classification, query rewriting and expansion, structured filter extraction, and cross-encoder reranking. The objective is that the system understands what users want even when they do not say it clearly, and that the final candidate set is ordered by relevance to the actual question rather than by raw similarity.

## Methodology

Two cooperating subsystems:

1. **Query processor.** Incoming queries are classified (navigational / informational / transactional) to select a retrieval strategy, then rewritten for semantic coverage — synonym expansion and pronoun resolution against conversation history, so "reset password" also reaches "password reset," "forgot password," and "change credentials." Sparse or ambiguous queries are expanded via HyDE pseudo-document generation or sub-query decomposition, with a coherence gate that rejects expansions drifting off-topic. Filter extraction pulls structured constraints (date, author, category) out of natural language so P09 can apply them as pre-retrieval metadata filters. Expansion quality is measured as recall improvement over the raw query.
2. **Reranker.** A cross-encoder scores query–chunk relevance on the fused candidate set from P09; diversity injection (maximal marginal relevance) penalizes chunks too similar to those already selected; a recency boost applies when the query implies temporal need. Output is the top-budget chunk set ordered by composite score. Rerank gain is tracked as NDCG improvement over raw retrieval order — if reranking stops adding measurable gain, the cross-encoder needs domain fine-tuning, not more tolerance.

## Implementation

Deployment steps from the operations abstract: analyze 100 real user queries and categorize the patterns; build an intent classifier (regex + LLM hybrid); implement query expansion (synonyms, sub-queries); add filter extraction (date, author, category); test on held-out queries and iterate.

Concrete artifacts:

- `deliverables/p10-intent-taxonomy.md` — query intent categories with labeled examples from real query logs.
- `deliverables/p10-query-rewrites.json` — validated rewrite patterns with recall improvement metrics.
- `deliverables/p10-reranker-model.md` — cross-encoder specification, training data, performance report.
- `deliverables/p10-latency-budget.md` — per-stage latency allocation: classification, rewrite, expansion, rerank.

Debugging checks: intent classification accuracy above 90% on held-out queries; query expansion recall improvement above 20% (and above 10% specifically on ambiguous queries); filter extraction precision above 85%; NDCG@5 improvement above 15% after reranking versus raw retrieval order; p95 end-to-end latency under 200 ms from raw query to ranked chunks.

Telemetry thresholds from the operations abstract: intent classification accuracy above 90%; query expansion recall improvement above 20%; filter extraction precision above 85%. The abstract's reference case is an e-commerce search where "cheap running shoes for flat feet" expanded to include "overpronation," "stability," and "budget-friendly," raising relevant results from 12 to 47.

Downstream integration: P11 consumes the ranked, relevance-scored chunks for window budgeting; P12 consumes query-processing and reranking traces as evaluation data; P28 closes the loop by feeding user feedback into rewriter improvement.

## Merits

Stated outcomes for the query-processing pipeline: a 40% improvement in answer relevance scores and a 30% reduction in irrelevant context injection, with expansion improving recall by over 20% in the reference deployment. The structural merit is that query-side quality is measured per stage — classification, rewrite, expansion, filter extraction, rerank — so a precision failure is attributable to a stage rather than blamed on the index or the embeddings, which are usually innocent.

## Guidance for Use

**When to reach for P10:** after P09 is executing hybrid retrieval; when precision is poor despite good embeddings and indexes (the failure is usually query-side); and when users phrase the same need in many different ways.

**Prerequisites:** P06–P08 operational; P09 running, since the reranker consumes its fused candidates.

**How to run it well:** build the intent taxonomy from at least 100 real user queries, not imagined ones; give the classifier an explicit "unknown" fallback strategy so misclassified queries degrade gracefully; cache expansions and use a lighter bi-encoder first stage if expansion plus reranking breaches the latency budget; fine-tune the cross-encoder on domain query–chunk pairs whenever rerank gain falls under 5%.

**Pairing:** P09 executes the retrieval this pipeline prepares and supplies the candidates it reranks; P11's budget allocation consumes these relevance scores directly; P12's golden dataset should include the ambiguous and keyword-heavy queries this prompt is built to rescue; P28 turns user feedback into rewriter training signal.

**Common misuse:** sending queries to the index verbatim; expansion without a coherence gate, which lets HyDE drift off-topic and actively hurt recall; accepting raw similarity order and skipping reranking; filter extraction without precision monitoring, so wrong filters silently exclude the right documents; and an intent taxonomy built from assumptions rather than query logs.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
