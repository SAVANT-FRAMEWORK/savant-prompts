---
ID: P31
Title: Code Architecture Oracle
Registry: SAVANT FRAMEWORK Operations (P-series, commercial tier; canonical KEY-016..KEY-050)
License: Savant-Commercial-1.0 (prompt body withheld — documentation release)
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P31: Code Architecture Oracle

*Tier 7 — Specialized of the SAVANT FRAMEWORK operations prompt registry.*

P31 is an architecture guidance prompt whose end-state objective is a codebase that scales with the team and the business rather than against them. It directs the model to act as an architecture oracle: mapping business capabilities to bounded contexts, evaluating candidate architectural styles against stated constraints, and defining a 'golden path' with sanctioned escape hatches so that routine work is fast and exceptional work remains possible.

The reference deployment sequence runs over five weeks: capability-to-context mapping, constraint-driven style evaluation, golden-path definition, core-structure implementation, and a team review-and-iterate cycle. The deployment artifact set is an architecture specification, golden-path examples, and architecture decision records (ADRs).

Real-world scenario: a startup uses P31 to avoid monolith hell. A modular monolith allows five engineers to work independently and the team to scale to twenty without a rewrite.

> Prompt body: WITHHELD (commercial registry). This file is the public documentation release: abstract, methodology, and merits. Licensing inquiries via the GitHub organization.

## Methodology

The prompt operationalizes established software-architecture practice as a disciplined, auditable procedure:
- **Bounded-context mapping.** Business capabilities are enumerated and grouped into bounded contexts before any structural decision is taken, so module boundaries follow the domain rather than the org chart or the framework defaults.
- **Constraint-driven style selection.** Candidate architectural styles (modular monolith, microservices, event-driven, and hybrids) are scored against the organization's actual constraints — team size, deployment maturity, latency and consistency requirements — rather than against fashion.
- **Golden path with escape hatches.** A default, well-lit path is specified for common change types, together with explicitly sanctioned exceptions. This concentrates design effort where variance is legitimate and removes it where it is not.
- **Decision recording.** Every consequential choice is captured as an ADR, making the architecture reviewable, teachable, and reversible.
- **Iterated review.** The structure is reviewed with the implementing team and revised, on the premise that an architecture the team does not understand will not survive contact with the team.

## Merits

Reported optimization telemetry from the source registry:
- Time to add a new feature reduced from over one week to under two days.
- Cross-team coordination required for under 10% of features.
- Technical debt accumulation linear rather than exponential.
The principal merit is option value: the architecture defers irreversible commitments (notably premature distribution into microservices) until the constraints that would justify them actually exist.

## Guidance for Use

Intended for engineering leadership, staff engineers, and architects at the start of a product or at a scaling inflection point. Supply honest constraints — team size, deployment cadence, regulatory obligations — because the style evaluation is only as sound as the constraints it is scored against.
Treat the ADR set as a living artifact: revisit it quarterly and whenever a constraint changes. The prompt guides architectural reasoning; it does not replace domain-modeling expertise or accountability for the decisions recorded.
[Commercial licensing terms — inquiries via the GitHub organization]

---

C0 informational classification; confers no clinical, legal, financial, or safety authority.
