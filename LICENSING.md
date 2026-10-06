---
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-08-12
SHA-256: [PENDING-FIRST-RELEASE]
---

# Prompt Catalog Licensing

Canonical rule (Dr. Christabel Odeta, 2026-08-12):

- **KEY-001 through KEY-020 (Tiers 1–4):** AGPL-3.0. Open.
- **KEY-021 through KEY-075 (Tiers 5–15):** Savant-Commercial-1.0. Commercial.
- **CLAI-OS components P51–P70:** AGPL-3.0 (recorded in the clai-os repository).

This split overrides any earlier tier-level statement. The repository root LICENSE
(dual AGPL-3.0 OR Savant-Commercial-1.0) governs per-file according to this map;
each prompt file's header declares its own license.

## Falsifiability Test
This file fails if any prompt file's License header contradicts the rule above.

## Amendment 2026-10-06 (Dr. Christabel Odeta — canonical, overrides where in conflict)

- **S-001 through S-020 (S-Tier meta-constitutional engines):** AGPL-3.0. Open.
- **S-021 through S-063:** Savant-Commercial-1.0. Commercial.
- **PRIME-001 (SAVANT PRIME — constitutional orchestration layer):** Savant-Commercial-1.0.
  Public governance documentation (routing architecture, gate semantics, output contract)
  is published in savant-docs; the Master Prompt text itself is commercial.
- **Deployment agents CLAI-DEPLOY-01..10 and AEGIS-DEPLOY-01..10:** Savant-Commercial-1.0.
  Catalog entries and the SIP trigger matrix are public; full RASCEF agent bodies are commercial.
- **P-071 through P-110 (Clinical Human Good; Precision Oncology & Global Health):**
  Savant-Commercial-1.0. (P51–P70 remain AGPL-3.0, recorded in the clai-os repository.)

Unchanged: KEY-001..KEY-020 AGPL-3.0; KEY-021..KEY-075 Savant-Commercial-1.0;
AEGIS node firmware AGPL-3.0; AEGIS manufacturing specifications CC-BY-SA 4.0.

### Falsifiability Test (amendment)
This amendment fails if any S-001..S-020 file's License header is not AGPL-3.0, or if any
S-021..S-063, PRIME, deployment-agent, or P-071..P-110 file ships a full body in a public repository.
