# savant-prompts — SAVANT FRAMEWORK Public Prompt Corpus

The public face of the SAVANT FRAMEWORK canonical prompt corpus: **75 prompts
across 15 tiers**, authored by Dr. Christabel Odeta and governed by the
SAVANT FRAMEWORK genealogy canon (every prompt carries ID, version, content
hash, and parent hash).

## Canonical ledger — honest-absence doctrine

The full canonical ledger lives in [`LEDGER.md`](LEDGER.md). Current counts:

- **34 PRESENT** — the prompt exists in the canonical corpus.
- **41 PENDING** — disclosed, honest absences. No placeholders, no
  reconstructions, no stub files. PENDING keys appear as ledger rows only.

## The dual-license gate

| Keys | Tier | License | What is public here |
|------|------|---------|---------------------|
| KEY-001 … KEY-020 | Tiers 1–4 | **AGPL-3.0** | Full prompt text (PRESENT files) under [`open-tier/`](open-tier/) |
| KEY-021 … KEY-075 | Tiers 5–15 | **Savant-Commercial-1.0** | Abstracts + SHA-256 commitment hashes under [`commercial-tier/`](commercial-tier/) |

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
