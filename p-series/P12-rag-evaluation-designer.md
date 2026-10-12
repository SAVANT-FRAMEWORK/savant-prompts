---
ID: P12
Title: RAG Evaluation Designer
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P12: RAG Evaluation Designer

P12 closes the RAG tier loop: it evaluates the entire P06–P11 chain with quantitative metrics, attributes failures to specific components, detects regressions before users do, and provides an A/B framework for comparing configurations scientifically. It replaces quality assessment by manual spot-check — where regressions are discovered by users and the breaking change is unidentifiable — with a reproducible golden dataset, automated metrics in CI, and trend monitoring. The objective is confidence that RAG changes actually help, rather than feel right.

## Methodology

Three cooperating subsystems:

1. **Evaluation harness.** The pipeline runs against a managed golden dataset and computes four metric families: retrieval (MRR, NDCG@k, recall@k, precision@k), answer quality (relevance via LLM judge, faithfulness as claim–evidence alignment, correctness against ground truth), latency (p50/p95/p99 per stage: chunking, embedding, retrieval, generation), and cost (tokens and spend per query). A component tracer attributes every metric to P06 through P11, isolating which stage caused a change.
2. **Regression detector.** New results are compared against stored baselines on three rules: hard threshold (metric below minimum acceptable), statistical (outside 3σ of the historical distribution), and trend (three consecutive degradations indicating drift). Alerts are severitized: critical blocks the deploy, warning requires investigation, info is monitored.
3. **A/B test framework.** Two configurations run on the same dataset with randomized ordering to remove position bias, per-query differences computed, and significance tested with correction for multiple comparisons. Power analysis pre-computes the sample size required for 80% power at α = 0.05, and the verdict is winner or an honest "inconclusive."

## Implementation

Deployment sequence from the operations abstract: build a golden dataset of 100+ verified question–answer pairs; implement automated metrics (hit rate, faithfulness); add LLM-as-judge for nuanced evaluation; integrate into CI with a deploy gate on metric drops; stand up a dashboard for trend monitoring.

Concrete artifacts:

- `deliverables/p12-golden-dataset.json` — labeled query–document–answer triples with provenance.
- `deliverables/p12-metric-suite.yml` — retrieval, answer, latency, and cost metrics with formulas.
- `deliverables/p12-regression-policy.md` — thresholds, alert routing, escalation procedures.
- `deliverables/p12-ab-framework.md` — experiment design, statistical methods, winner criteria.
- `deliverables/p12-quality-dashboard.json` — Grafana dashboard configuration.

Debugging checks from the source: golden dataset above 100 labeled pairs with inter-annotator agreement above 0.80; automated metrics matching manual calculation within 1%; an injected 10% degradation alerting within 3 evaluation runs; A/B false-positive rate under 5% and false-negative rate under 20% at expected effect size; component attribution correctly identifying the broken stage over 90% of the time; evaluations running hourly in CI and daily in production.

Telemetry thresholds from the operations abstract: evaluation runtime under 10 minutes in CI; metric drift detection within 24 hours; false-positive (flaky test) rate under 2%.

Downstream integration: P21 consumes evaluation reports as optimization objectives; P28 feeds user feedback into golden dataset augmentation; P35 consumes component traces; P41 uses metric sets as a generational fitness signal.

## Merits

Source-stated outcomes: 50% faster detection of RAG quality regressions and a 30% improvement in answer faithfulness through targeted iteration; the operations abstract reports a legal-tech case where an "improvement" that actually reduced accuracy by 5% was caught before deployment. The structural merit is attribution: because every metric traces to a component, iteration effort goes to the stage that is actually broken rather than to whichever stage someone suspects.

## Guidance for Use

**When to reach for P12:** as soon as P06–P11 are operational — before the first "improvement" is shipped without evidence — and permanently thereafter as the deploy gate for every RAG change.

**Prerequisites:** all of P06–P11 complete, with retrieval traces logged (P10) and context assembly operational (P11).

**How to run it well:** refresh the golden dataset monthly and add synthetic adversarial examples; stratify it by intent using production query logs; tune alert thresholds against historical variance to prevent alert fatigue; calibrate LLM judges against human ratings and rotate judge models to control judge bias; sample evaluation subsets and cache intermediates when full evaluation costs more than the inference it guards.

**Pairing:** run P12 before and after any change to P06–P11 — it is the acceptance test for the entire RAG tier. Wire its outputs to P28 so user feedback continuously augments the golden dataset.

**Common misuse:** evaluating end-to-end only, which hides which component regressed; A/B tests without power analysis, which burn time to reach "inconclusive"; trusting a single LLM judge without calibration; and treating evaluation as a pre-launch checkbox rather than a permanent CI gate — the source pattern is block-on-regression, not measure-once.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
