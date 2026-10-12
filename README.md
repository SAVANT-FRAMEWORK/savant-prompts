# savant-prompts — SAVANT FRAMEWORK Public Prompt Corpus

The public face of the SAVANT FRAMEWORK canonical prompt corpus, authored by
Dr. Christabel Odeta and governed by the SAVANT FRAMEWORK genealogy canon
(every prompt carries ID, version, content hash, and parent hash).

**Corpus map (canonical, 2026-10-12):** KEY-001–KEY-075 across 15 tiers
(≡ P-001–P-050 plus tier extensions) · **S-Tier S-001–S-063** meta-constitutional
engines · **P-051–P-110** CLAI-OS modules (documented in the `clai-os`
repository — CLAI prompts exist only in the P51–P110 range) · **PRIME-001**
constitutional orchestration layer · **20 deployment agents**
(CLAI-DEPLOY-01..10, AEGIS-DEPLOY-01..10). PRIME governs all corpus execution.

## SAVANT operations registry (P01–P50) — new 2026-10-12

Per-prompt documentation for the SAVANT FRAMEWORK operations registry lives
under [`p-series/`](p-series/):

- **P01–P15 — open tier, AGPL-3.0** (canonical KEY-001..KEY-015): full
  implementation documentation, abstracts, methodology, merits, and usage
  guidance.
- **P16–P50 — commercial tier** (canonical KEY-016..KEY-050): prompt bodies
  and code withheld; names, abstracts, methodologies, and merits published
  as open documentation. [Commercial licensing terms — inquiries via the
  GitHub organization]

## Canonical ledger — honest-absence doctrine

The full canonical ledger lives in [`LEDGER.md`](LEDGER.md). Current counts:

- KEY ledger: **34 PRESENT / 41 PENDING**.
- S-Tier: **S-001–S-020 PRESENT (open, AGPL-3.0)**; S-021–S-063 PENDING.
- PRIME-001 and 20 deployment agents: PRESENT as **commercial abstracts**
  with SHA-256 existence-commitments.
- P-071–P-110: PENDING (domains disclosed per PRIME routing table).
- PENDING entries are disclosed, honest absences. No placeholders, no
  reconstructions, no stub files. PENDING items appear as ledger rows only.

## The dual-license gate

| Keys | Tier | License | What is public here |
|------|------|---------|---------------------|
| KEY-001 … KEY-020 | Tiers 1–4 | **AGPL-3.0** | Full prompt text (PRESENT files) under [`open-tier/`](open-tier/) |
| **S-001 … S-020** | S-Tier meta-constitutional | **AGPL-3.0** | Full prompt text under [`open-tier/`](open-tier/) |
| KEY-021 … KEY-075 | Tiers 5–15 | **Savant-Commercial-1.0** | Abstracts + SHA-256 commitment hashes under [`commercial-tier/`](commercial-tier/) |
| S-021 … S-063 | S-Tier | **Savant-Commercial-1.0** | PENDING — ledger rows only |
| PRIME-001 | Constitutional orchestration layer | **Savant-Commercial-1.0** | Abstract + SHA-256 commitment; governance docs in savant-docs |
| CLAI-DEPLOY-01..10 / AEGIS-DEPLOY-01..10 | Deployment agents | **Savant-Commercial-1.0** | Abstracts + SHA-256 commitments |
| P-071 … P-110 | CLAI-OS extensions | **Savant-Commercial-1.0** | PENDING — ledger rows only |

Commercial-tier prompt bodies are **never** published. Each commercial-tier
file contains only its front-matter (including the canonical SHA-256 hash,
which serves as a public existence-commitment to the exact private text), a
one-sentence functional abstract, and licensing contact information. Full text
is available under a commercial license — inquiries through the GitHub org:
<https://github.com/SAVANT-FRAMEWORK>.

See [`LICENSE`](LICENSE) (constitutional dual license) and
[`LICENSING.md`](LICENSING.md) for the complete terms.

## Verification

Every prompt file's YAML front-matter carries a `SHA-256` field — the hash of
the canonical prompt text. To verify an open-tier file against its header:

```sh
# compare the published hash header with the canonical record
grep '^SHA-256:' open-tier/KEY-001-universal-system-architect.md
```

For commercial-tier files, the published SHA-256 commits to the private full
text: anyone holding a licensed copy can confirm it matches the commitment by
hashing their copy.

## Links

- Org: <https://github.com/SAVANT-FRAMEWORK>
- Docs: <https://savant-framework.github.io/savant-docs/>
- Demo: <https://savant-framework.github.io/savant-demo/>
