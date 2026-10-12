---
ID: P11
Title: Context Assembly Engineer
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P11: Context Assembly Engineer

P11 packs retrieved chunks into the LLM's context window for maximum information density without truncation artifacts. It replaces naive concatenation — chunks appended until the window fills, the system prompt squeezed out, the final chunk cut mid-sentence — with explicit token budgeting, priority-based selection, and structured layout with source attribution. The objective is that the model's answer uses exactly the right evidence, no more and no less.

## Methodology

Three cooperating subsystems:

1. **Context assembler.** The window is budgeted before any chunk is placed: the system prompt receives a fixed reserve (for example 20% of the window) that is never truncated, the user query receives a variable reserve based on complexity, and chunks receive the remainder. Utilization is tracked as the percentage of the window filled with relevant content.
2. **Priority allocator.** Chunks are selected greedily by composite score: base relevance from the P10 reranker, a diversity penalty against already-selected chunks (maximal marginal relevance), a recency boost when the query implies temporal need, and intelligent compression when a chunk exceeds the remaining budget — extractive summarization that preserves numbers, dates, and names rather than blind truncation.
3. **Layout engine.** Selected chunks are formatted with explicit separators and attribution (for example `--- Source: {url} | Page: {page} ---`), ordered by relevance or chronology per query intent, and truncated only at sentence boundaries. A readability score measures how distinctly the boundaries and citations parse for the model.

## Implementation

Deployment phases from the operations abstract: implement the token budget calculator; build chunk relevance scoring; design context ordering (most relevant first); add citation injection for source tracking; test truncation strategies — what drops when over budget.

Concrete artifacts:

- `deliverables/p11-budget-policy.yml` — token allocation rules per model and query type.
- `deliverables/p11-layout-template.md` — formatting template with separators and attribution.
- `deliverables/p11-priority-algorithm.md` — selection scoring formula with weights.
- `deliverables/p11-utilization-report.json` — utilization, truncation, and diversity metrics per query type.

Debugging checks: average utilization above 0.80 with truncation rate below 0.05; under 5% of truncations cutting mid-sentence (all at natural boundaries); 100% of chunks carrying a source URL or document identifier; system prompt fully preserved at every window size tested (4K, 8K, 16K, 32K); at least 3 distinct sources per query where available.

Telemetry thresholds from the operations abstract: token utilization above 85%; citation accuracy above 95% (correct source attached to each claim); answer completeness above 90% with no critical omissions. The abstract's reference case reduced "I don't know" responses from 25% to 8% without increasing hallucination.

Downstream integration: P12 evaluates answer quality against the assembled context; P17 uses context token counts for latency prediction; P21 treats the layout template as an optimization target.

## Merits

Stated outcomes: 30% more relevant context per prompt and a 50% reduction in token waste from poor ordering. The structural merit is that truncation becomes a scored decision — the lowest-value content drops first, at a clean boundary, with the system prompt guaranteed present — rather than an accident of concatenation order. Full attribution on every chunk is also what makes citation accuracy measurable downstream.

## Guidance for Use

**When to reach for P11:** after P10 delivers ranked, scored chunks; when answers cite incomplete facts (a mid-sentence truncation symptom); when the model ignores instructions (a system-prompt eviction symptom); or when window utilization is low and answers miss available evidence.

**Prerequisites:** P06 (chunk token counts), P07 (model context length), P08 (retrieval latency within budget), P10 (ranked chunks with relevance scores).

**How to run it well:** lock the system-prompt budget and validate its presence in the final assembled context, not just in the template; prefer compression over exclusion when utilization is low; preserve extractive fidelity — numbers, dates, names — in compressed chunks; and strengthen separator design the moment the model conflates two sources.

**Pairing:** P12 should evaluate answers against this assembled context, since assembly quality and generation quality are otherwise indistinguishable in end-to-end metrics; P17 consumes the resulting token counts.

**Common misuse:** fixed context allocation regardless of query complexity; arbitrary character-level truncation; missing separators, which lets the model blend documents; recency-blind selection for time-sensitive queries; and abstractive compression that paraphrases away the specific figures the user asked about.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
