---
ID: P06
Title: Document Chunking Strategist
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P06: Document Chunking Strategist

P06 is the entry point of the RAG tier. It replaces fixed-size chunking with an intelligent router that selects a segmentation strategy per document type, preserving semantic coherence and document structure while maximizing retrieval precision. The objective is that retrieved chunks contain the answer, not merely the keywords — tables stay atomic, code stays function-bounded, and headers stay attached to their body text.

## Methodology

Three cooperating subsystems:

1. **Chunking router.** Documents are classified by MIME type and content sniffing, then routed to a type-appropriate strategy: Markdown splits on header hierarchy with list integrity preserved; code splits on AST-based function and class blocks respecting brace boundaries; PDFs use layout-aware column and region extraction with table isolation; JSON and CSV split along schema paths and row groups; narrative prose splits on paragraph and sentence boundaries with controlled overlap.
2. **Boundary detector.** Proposed split points are scored on three axes: semantic (embedding similarity of the fragments on each side — high similarity across the split indicates a bad cut), syntactic (sentence, paragraph, and section ends preferred), and layout (column breaks, table boundaries, figure captions). Split indices are chosen to maximize boundary scores within target size constraints.
3. **Metadata preserver.** Every chunk carries document metadata (source URL, title, author, date), structural metadata (section heading, page number, list position), and lineage (parent document ID, chunk sequence, overlap references). Section headers are injected into body chunks so each chunk is coherent in isolation. Coverage tracking verifies that 100% of document text appears in at least one chunk.

## Implementation

Deployment sequence from the operations abstract: analyze document types and design the strategy; implement the chunker on a sample corpus; evaluate retrieval quality on a golden dataset; tune chunk size and overlap against metrics; deploy and monitor hit rate.

Concrete artifacts:

- `deliverables/p6-chunking-strategies.yml` — strategy definitions per document type with parameters.
- `deliverables/p6-boundary-eval.json` — boundary detection benchmark on a labeled corpus.
- `deliverables/p6-type-detector.md` — detection heuristics, accuracy report, failure modes.
- `deliverables/p6-metadata-schema.yml` — chunk metadata schema with lineage and provenance.

Debugging checks from the source: average boundary score above 0.80; type detection accuracy above 95% on a labeled corpus; metadata coverage at 1.0 (every chunk carries source and structural metadata); token overhead from overlap and metadata under 5%; coverage completeness at 100% against the original text.

Telemetry thresholds from the operations abstract: Hit Rate@5 above 85% (the answer appears in the top 5 chunks); human-rated chunk coherence above 0.8; processing throughput above 100 documents per minute.

Chunking runs as a P04 background task. Downstream integration: P07 consumes chunk type, language, and domain metadata for model routing; P08 consumes the chunk schema for index mapping; P09 uses parent-chunk references for deduplication; P11 uses per-chunk token counts for window budgeting.

## Merits

Source-stated outcomes: a 35% improvement in RAG answer relevance and a 50% reduction in token waste from oversized chunks; the operations abstract reports a contract-analysis case where boundary-aware chunking lifted retrieval from 60% to 92% hit rate. The structural merit is that chunking decisions are type-aware, scored, and auditable rather than a global constant nobody can justify — and no downstream stage ever needs to re-chunk.

## Guidance for Use

**When to reach for P06:** at the start of any RAG build, before embedding anything; and whenever retrieval misses trace to split tables, fragmented code, or severed headers.

**Prerequisites:** P01 (document volume and type profile), P02 (ingestion endpoint), P03 (document and chunk storage schema), P04 (background chunking worker pool).

**How to run it well:** keep tables as atomic chunks; reduce overlap to 10–20% and compress metadata to essential fields when token counts inflate; improve type detection with file-extension plus content heuristics rather than content sniffing alone; validate coverage with a diff against the original document, not a sample.

**Pairing:** P07 through P11 all consume P06 chunk metadata directly — a chunking change propagates through the whole RAG chain, so re-run P12 evaluation after any strategy adjustment.

**Common misuse:** one chunk size for every document type; arbitrary overlap with no boundary detection; discarding provenance (chunks without source URL or page make citation impossible downstream); and excluding headers and footers, which silently produces coverage gaps.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
