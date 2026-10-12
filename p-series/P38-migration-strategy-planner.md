---
ID: P38
Title: Migration Strategy Planner
Registry: SAVANT FRAMEWORK Operations (P-series, commercial tier; canonical KEY-016..KEY-050)
License: Savant-Commercial-1.0 (prompt body withheld — documentation release)
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P38: Migration Strategy Planner

*Tier 8 — Strategic of the SAVANT FRAMEWORK operations prompt registry.*

P38 plans legacy-system migrations with the end-state objective that legacy systems are replaced incrementally, with zero downtime and rollback capability at every step. It rejects the big-bang cutover in favor of a strangler-fig approach in which the new system grows around the old until the old can be retired without ceremony.

The reference deployment proceeds in five steps: assess complexity and risk; design the strangler-fig approach; implement dual-write/dual-read; verify with automated comparison; and decommission the old system. The deployment artifact set is a migration plan, a verification suite, and rollback procedures.

Real-world scenario: a bank uses P38 to migrate its core banking system — a six-month migration completed with zero downtime, zero data loss, and zero customer impact.

> Prompt body: WITHHELD (commercial registry). This file is the public documentation release: abstract, methodology, and merits. Licensing inquiries via the GitHub organization.

## Methodology

- **Risk-front-loaded assessment.** Complexity and risk are assessed before any code moves, identifying the seams where the legacy system can be intercepted and the data whose corruption would be unrecoverable.
- **Strangler-fig routing.** New functionality is built in the new system; traffic is routed incrementally from old to new at defined seams, so the migration is a sequence of small, reversible steps.
- **Dual-write, dual-read.** During transition, writes go to both systems and reads are compared, making divergence detectable the moment it occurs rather than at reconciliation.
- **Automated verification.** Old and new outputs are compared continuously and automatically; equivalence is demonstrated with evidence, not assumed from successful testing.
- **Rollback as a first-class artifact.** Every increment has a tested rollback path with a defined execution time, so each step's worst case is a brief return to the previous state.
- **Deliberate decommissioning.** The old system is retired explicitly — access revoked, infrastructure released, knowledge archived — preventing the undead-legacy failure mode in which both systems run forever.

## Merits

Reported optimization telemetry from the canonical registry:
- Downtime during migration: zero.
- Rollback time under five minutes.
- Data consistency 100%, continuously verified.
The principal merit is the conversion of an existential-risk event (core-system replacement) into a routine engineering program: risk is distributed across hundreds of reversible increments instead of concentrated in a single cutover weekend.

## Guidance for Use

Intended for teams replacing business-critical legacy systems. Do not begin dual-write until the comparison harness exists; the verification suite is what makes the strangler pattern safe rather than merely slow.
Budget for decommissioning as a deliverable with an owner. Migrations that end at 'new system live' leave the legacy system — and its cost, risk, and confusion — in place.
[Commercial licensing terms — inquiries via the GitHub organization]

---

C0 informational classification; confers no clinical, legal, financial, or safety authority.
