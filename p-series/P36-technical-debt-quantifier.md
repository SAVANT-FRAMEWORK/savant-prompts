---
ID: P36
Title: Technical Debt Quantifier
Registry: SAVANT FRAMEWORK Operations (P-series, commercial tier; canonical KEY-016..KEY-050)
License: Savant-Commercial-1.0 (prompt body withheld — documentation release)
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P36: Technical Debt Quantifier

*Tier 8 — Strategic of the SAVANT FRAMEWORK operations prompt registry.*

P36 makes technical debt legible to the business with the end-state objective that debt is visible, prioritized, and paid down systematically. It converts the diffuse engineering complaint of 'debt' into a register of scored, costed items with an explicit interest rate — the ongoing cost per sprint of leaving each item unpaid.

The reference deployment runs in five phases: inventory all debt (code, architectural, test, documentation); score each item by risk (probability × impact × fix cost); calculate interest as current cost per sprint; build a refactoring roadmap; and allocate 20% of sprint capacity to paydown. The deployment artifact set is a debt register, a risk heat map, and a roadmap.

Real-world scenario: a team uses P36 to justify a two-week refactoring effort to leadership. After the refactoring, feature delivery speed increases by 30%.

> Prompt body: WITHHELD (commercial registry). This file is the public documentation release: abstract, methodology, and merits. Licensing inquiries via the GitHub organization.

## Methodology

- **Complete inventory.** Debt is catalogued across four categories — code, architecture, test, and documentation — so the register reflects the whole liability, not only the parts engineers complain about.
- **Risk scoring.** Each item is scored as probability of harm × impact of harm × cost of fix, yielding a comparable ordering across unlike items.
- **Interest computation.** Each item carries a measured carrying cost per sprint (slower delivery, defect handling, onboarding friction), which is the quantity that makes the register meaningful to leadership.
- **Roadmap construction.** Paydown is sequenced by interest-to-fix-cost ratio, retiring the most expensive debt first rather than the most irritating.
- **Capacity allocation.** A fixed 20% of sprint capacity is reserved for paydown, converting debt reduction from a negotiation that happens every sprint into a policy that happens once.

## Merits

Reported optimization telemetry from the source registry:
- Debt-related incidents reduced 50% per quarter.
- Sprint velocity variance held below 10%, making delivery predictable.
- Team morale (survey) up 20%, attributed to reduced firefighting.
The principal merit is translation: debt stops being an engineering grievance and becomes a financial statement that leadership can act on without trusting anyone's intuition.

## Guidance for Use

Intended for engineering managers and technical leads who must justify maintenance investment to non-technical leadership. The interest figures must be measured (cycle time, incident cost), not asserted; the register's authority depends on its numbers surviving scrutiny.
Protect the 20% allocation explicitly in planning. The common failure mode is not disagreement about debt but the quiet reallocation of paydown capacity to features under deadline pressure.
[Commercial licensing terms — inquiries via the GitHub organization]

---

C0 informational classification; confers no clinical, legal, financial, or safety authority.
