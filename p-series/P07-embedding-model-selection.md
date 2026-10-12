---
ID: P07
Title: Embedding Model Selector
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P07: Embedding Model Selector

P07 replaces the single-embedding-model default with a selection engine that matches models to content type, language, domain, and downstream retrieval task — ranked by benchmarked performance, not popularity. Code is embedded with code-aware models, multilingual corpora with multilingual models, and keyword-dominant tasks with sparse alternatives. The engine treats model selection as a reversible, evidence-based decision with a documented rationale and a swap-out path.

## Methodology

Three cooperating subsystems:

1. **Model selector.** Candidate models in the registry are ranked on four axes: task performance (MRR, NDCG, precision@k on a held-out validation set), content match (language support, domain specialization, type awareness), cost efficiency (cost per million tokens, dimensionality, throughput), and operational fit (latency SLA, availability, deprecation status). Ranking is Pareto-based across quality, cost, and latency, and the recommendation is locked per content hash so identical content always routes identically.
2. **Benchmark engine.** Task-specific evaluation datasets measure retrieval accuracy (relevant chunks in top-k), semantic similarity (cosine correlation with human judgments), and robustness (noisy, abbreviated, or translated queries). Paired significance testing across models yields 95% confidence intervals, so a "better" model is statistically better, not anecdotally better.
3. **Cost optimizer.** Solves for maximum quality subject to a budget constraint and a quality floor, accounting for batching efficiency, instance sharing, and cache hit rates. Produces a forecast with a confidence interval; the acceptance check is that forecast stays within 10% of actual spend.

## Implementation

Deployment steps from the operations abstract: benchmark 3–5 models on your own documents; measure accuracy, latency, memory, and cost; select a winner and document the rationale; implement behind an abstraction layer for future swap-out; monitor drift and re-benchmark quarterly.

Concrete artifacts:

- `deliverables/p7-model-registry.json` — all models with dimensions, languages, domains, cost profiles, and SLAs.
- `deliverables/p7-benchmark-report.md` — cross-model performance on your validation corpus.
- `deliverables/p7-selection-policy.yml` — routing rules: content type → model → fallback.
- `deliverables/p7-cost-model.json` — cost forecast with sensitivity analysis.

Debugging checks: over 90% of selections match expert choice on the validation set; benchmarks refreshed within the last 30 days; predicted versus actual embedding spend within 10%; fallback model activates within 500 ms of primary failure; retrieval parity gap between English and non-Latin scripts under 15%.

Telemetry thresholds from the operations abstract: retrieval accuracy within 2% of the best benchmarked model; inference cost 50% below the naive default choice; model swap time under 1 hour through the abstraction layer.

Downstream integration: P08 consumes embedding dimensions and format for index configuration; P09 consumes the model endpoint for query embedding; P12 consumes the benchmark datasets as evaluation harness inputs.

## Merits

Stated outcomes: a 25% improvement in retrieval precision@10 and a 40% reduction in embedding compute cost through right-sizing; the operations abstract reports a support-bot case where switching from a hosted embedding API to a local model held accuracy while removing the per-call cost. The structural merit is that selection is benchmark-validated, statistically significant, and reversible — a deprecation or outage triggers a tested fallback rather than a pipeline halt.

## Guidance for Use

**When to reach for P07:** immediately after P06, before any embeddings are generated at scale; whenever a provider deprecates a model; and when multilingual or code content underperforms in retrieval.

**Prerequisites:** P06 complete — chunk metadata (type, language, domain) must exist, because it is the routing input.

**How to run it well:** benchmark on your own corpus, not public leaderboards; maintain language-specific indices when the corpus is multilingual; register a fallback model per content type and test failover monthly; auto-refresh benchmarks on a schedule and alert on model version changes, since stale benchmarks hide drift.

**Pairing:** P08's index type depends on the dimensionality chosen here; P12's golden dataset should reuse P07's benchmark evaluation sets. If retrieval quality later degrades, re-run P07 before tuning P09 — a wrong model is not fixable downstream.

**Common misuse:** using one high-dimensional hosted model for everything, including simple keyword retrieval; choosing by popularity rather than task benchmarks; no fallback, so a provider outage stops the pipeline; and non-deterministic routing where identical content receives different embeddings across runs.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
