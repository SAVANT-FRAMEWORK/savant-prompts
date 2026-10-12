---
ID: P20
Title: Prompt Security Guardian
Registry: SAVANT FRAMEWORK Operations (P-series, commercial tier; canonical KEY-016..KEY-050)
License: Savant-Commercial-1.0 (prompt body withheld — documentation release)
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P20: Prompt Security Guardian

P20 is the prompt-security module of the SAVANT FRAMEWORK operations registry and the closing module of the Production tier (P17–P20). Its end-state objective is a system that resists attacks its operators have not thought of yet — security as an ongoing adversarial discipline rather than a fixed checklist. The methodology starts from the known attack surface (the OWASP LLM Top 10), builds input and output defenses around it, and then subjects those defenses to structured adversarial testing, culminating in an independent red-team penetration test. Security under P20 is measured by block rates and false-positive rates together, so defenses cannot pass by simply blocking everything.

> Prompt body: WITHHELD (commercial registry). This file is the public documentation release: abstract, methodology, and merits. Licensing inquiries via the GitHub organization.

## Methodology

### Deployment Sequence

1. **Catalog known attack vectors.** Begin from the OWASP LLM Top 10 as the enumerated baseline of the known threat surface.
2. **Implement input guards.** Deploy both pattern-based and semantic input screening; pattern guards catch known signatures, semantic guards catch paraphrased and novel phrasings of known intents.
3. **Add output filtering.** Screen outputs for personally identifiable information and harmful content, so a successful input-side evasion does not automatically become a successful exfiltration.
4. **Build an adversarial test suite.** Assemble a suite of 100 or more distinct attacks and run it as a regression artifact, so defensive capability is re-verified as the system changes.
5. **Penetration test with a red team.** Commission adversarial testing by operators independent of the defense designers; defenses that have only faced their own designers' attacks are unverified.

### Deployment Artifacts

- `security_model` — the threat catalog and the defenses mapped to each vector.
- `test_suite` — the 100+-attack adversarial regression suite.
- `incident_response` — the procedures governing detection, containment, and response when a defense fails.

### Target Telemetry (registry-specified)

- **Prompt injection block rate:** above 99%.
- **False positive rate (legitimate requests blocked):** below 0.1% — defenses must remain near-invisible to legitimate users.
- **Security incident response time:** under 15 minutes from detection.

## Merits

- **Dual-sided telemetry.** Block rate and false-positive rate are tracked together, foreclosing the trivial "defense" of indiscriminate blocking; a guardian that degrades legitimate use fails its own metrics.
- **Defense verified by adversaries, not authors.** The escalation from an internal 100+-attack suite to an independent red-team test reflects the registry's position that self-tested defenses carry systematic blind spots.
- **Layered input and output screening.** Pattern plus semantic input guards, backed by PII and harmful-content output filtering, give each attack class more than one opportunity to be caught.
- **Documented production outcome.** The registry's reference scenario — a banking chatbot — reports a red team attempting 50 injection attacks: 49 blocked immediately, the remaining one detected within two conversational turns, with no data exfiltrated. This illustrates the designed assumption that some attacks will penetrate the first layer, and that detection depth matters as much as the block rate.

## Guidance for Use

**When to deploy.** Deploy P20 on any system exposed to untrusted user input — which is to say, effectively every production LLM deployment — and treat it as mandatory before handling sensitive data (financial, medical, personal). It closes the Production tier because performance, cost, and reliability work is meaningless if the system can be induced to misbehave or leak.

**Prerequisites.** A defined data-sensitivity inventory (what must never appear in outputs), logging adequate to detect and investigate incidents, and an incident-response channel with real ownership. The red-team phase requires access to testers independent of the build team.

**Recommended pairings.** P20 pairs with P29 (Adversarial Resilience Designer), which extends the same discipline from known attack catalogs toward novel and coordinated attack campaigns with deeper layering and honeypot detection; with P19 (Reliability Engineer), whose runbooks should incorporate the incident-response procedures; and with P25 (Hallucination Immunizer) where output integrity and output safety overlap.

**Misuse and limitations.** Do not treat the OWASP catalog as complete — it is the floor of the known, and the module's stated objective is explicitly about attacks not yet catalogued. Do not run the adversarial suite once and archive it; defenses decay as models, prompts, and attack techniques change, so the suite must run as a regression artifact. Security guarding constrains adversarial misuse of the prompt layer; it does not replace application-level security, access control, or compliance review.

---

C0 informational classification; confers no clinical, legal, financial, or safety authority.
