---
ID: P01
Title: Universal System Architect
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P01: Universal System Architect

P01 is the foundation prompt of the SAVANT FRAMEWORK operations registry. It produces a documented, defensible system architecture for production AI infrastructure — one that an engineer can implement without returning for clarification. The prompt forces explicit separation of deterministic, LLM, and hybrid components, complete failure-mode enumeration, cost models grounded in token math and infrastructure assumptions, and scalability claims justified by load evidence rather than assertion. Every subsequent prompt in the P-series consumes P01 outputs, so it is always the starting point for a new deployment.

## Methodology

The prompt drives a requirements-first decomposition:

1. **Requirements decomposition.** Translate a high-level brief ("AI chatbot for customer support") into quantified targets: concurrency (peak simultaneous sessions), throughput (requests/minute), latency ceilings (time-to-first-token, total response), data retention policy, and compliance jurisdiction. Scale is bounded at three levels — minimum viable, expected, and stress-test.
2. **Component typing.** Every component is labeled deterministic (D), LLM (L), or hybrid (H). LLMs are used only where they provide unique value (ambiguity, creativity, reasoning); all other logic is deterministic code. This labeling prevents architecture bloat and accidental placement of sensitive classification work inside an LLM call.
3. **Failure-mode enumeration (FMEA-style).** For each component: what can go wrong specifically, likelihood (from provider SLAs and historical data), impact (user-facing, compliance), detection mechanism (metric, alert, health check), and recovery path (retry, fallback, circuit breaker, graceful degradation).
4. **Cost modeling.** Token-based inference cost plus infrastructure cost per component, with every assumption citing a source (published provider cost documentation, vendor docs, or load-test data). Includes breakeven analysis.
5. **Scalability justification.** Horizontal scaling (statelessness verified), vertical headroom, database scaling strategy (replicas, sharding, connection pools), and external API rate-limit and fallback planning. Each component gets a headroom multiplier before redesign is required.

## Implementation

Deployment sequence from the operations abstract:

- Week 1: sketch architecture on paper, obtain team sign-off.
- Week 2: build a proof of concept covering one happy path.
- Week 3: add error handling, load test.
- Week 4: document, hand off to the implementation team.

Concrete artifacts:

- `docs/architecture/decision-records.md` — Architecture Decision Records (ADRs), each with context, decision, consequences, and status; expect at least 5.
- `docs/architecture/component-diagram.mermaid` — live Mermaid diagram, kept current against deployed infrastructure (auto-generate from Terraform and diff in CI).
- `docs/architecture/failure-matrix.csv` — FMEA matrix; expect at least 15 rows.
- `docs/architecture/cost-model.json` — token and infrastructure model with sourced assumptions.
- `docs/architecture/scalability-report.md` — headroom analysis citing load tests or queue theory (at least 3 load-test references).
- `terraform/` — infrastructure-as-code matching the diagram.

Telemetry thresholds from the operations abstract: onboarding time for a new engineer under 2 hours; fewer than 1 architecture change per sprint; zero production incidents traceable to design flaws. Debugging checks include verifying ADR count, failure-matrix row count, cost-model assumption sourcing, and diagram drift against `terraform graph`.

Downstream integration: P02 consumes interface contracts; P03 consumes scale bounds and retention policy; P04 consumes retry and timeout specifications; P05 consumes the component diagram and cost model.

## Merits

Source-stated outcomes: elimination of the majority of architectural rework between prototype and production, and materially fewer cost surprises through upfront calculator discipline (the source claims an 80% rework reduction and 60% cost-surprise reduction). The operational merit is that failure modes, costs, and scaling limits are written down and traceable before code exists — decisions are linked to commits, assumptions to sources, and diagrams to deployed infrastructure. The deterministic/LLM labeling discipline also removes a class of compliance errors, such as routing PII classification through a third-party LLM endpoint.

## Guidance for Use

**When to reach for P01:** at project start, before any endpoint, schema, or pipeline work; and again when the deployed system has drifted from its documentation.

**Prerequisites:** basic Python (functions, imports, exception handling), basic HTTP/REST (status codes, JSON payloads), and basic Git. No prior prompt in the series is required — P01 is the entry point.

**How to run it well:** fill the context block with real numbers (user volume, latency requirements, compliance needs, existing stack) before invoking; vague context produces vague architecture. Treat the LLM's first output as a draft to adapt, not a deliverable. Produce the four core artifacts — component diagram (with D/L/H labels and at least two fallback paths), component specification (tokens and latency per component), decision log (each decision with context, options, rationale, trade-off, reversibility — including at least one decision explaining why an LLM was not used), and failure-mode analysis (trigger, impact, mitigation, monitoring alert per risk).

**Pairing:** P02–P05 should be run immediately after; each consumes P01 outputs directly. For clinical or regulated deployments, lock data residency at this layer and validate with the governance prompts in the P51 range.

**Common misuse:** making every component an LLM (routing, caching, and validation should be deterministic); omitting fallback paths; leaving component types unlabeled; summing latencies across parallel branches; and letting the diagram rot — regenerate it from infrastructure-as-code and diff in CI rather than maintaining it by hand.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
