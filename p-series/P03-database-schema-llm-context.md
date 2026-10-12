---
ID: P03
Title: Database Schema Engineer
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P03: Database Schema Engineer

P03 produces a production database schema optimized for LLM context storage, retrieval, and migration safety. It replaces flat-JSON context storage with normalized, indexed, partitioned relational design coordinated with vector storage, and it makes schema evolution a versioned, reversible, tested process rather than a manual ALTER statement executed under pressure. The design target is a schema that handles tenfold data growth without migration emergencies.

## Methodology

Three cooperating subsystems:

1. **Schema designer.** Normalized entities (3NF, with denormalization only where justified) for conversations (metadata, participants, tenant isolation), messages (content, role, token counts, embedding reference, ordering), embeddings (vector, source-text hash, model version, JSONB metadata), sessions (auth context, rate-limit state, expiration), and an immutable audit log of all context access. Expected query patterns are enumerated up front with frequency and latency requirements.
2. **Index optimizer.** Index selection driven by EXPLAIN plan analysis: B-tree for primary lookups, GIN for full-text and JSONB path queries, BRIN for append-only time data, and IVFFlat or HNSW for vector similarity. Covering indexes enable index-only scans for hot paths. Index overhead (size ratio, write amplification) is measured and budgeted.
3. **Migration safety.** All changes flow through Liquibase or Flyway changelogs. Forward migrations order additive changes before modifications; every change ships with a tested rollback script; post-migration verification queries check data integrity; risk is scored by change type (additive < modify < drop < retype).

## Implementation

Deployment phases from the operations abstract: model core entities with domain-expert review; add indexes for predicted query patterns; load test at 10× expected volume; partition tables exceeding 1M rows; set monitoring alerts for table bloat and slow queries.

Concrete artifacts:

- `migrations/` — versioned, reversible Liquibase changelogs or Flyway SQL.
- `schema/erd.mermaid` — entity-relationship diagram rendered in GitHub.
- `docs/db/query-patterns.md` — expected query shapes with index justification.
- `docs/db/partitioning-strategy.md` — partition key rationale (tenant, time, or hash), archive policy, retention schedule.
- `tests/db/migration-tests/` — automated rollback and data-integrity validation.

Debugging checks: no query above 100 ms at p95 in the slow log; zero unused indexes in `pg_stat_user_indexes` (or documented exceptions); `liquibase rollbackCount 1` clean in staging; EXPLAIN confirms partition pruning rather than full table scans; vector index recall above 95% on nearest-neighbor queries.

Telemetry thresholds from the operations abstract: query p99 under 100 ms at 10× scale; zero migration downtime (online migrations); predictable, linear storage growth.

Connection pool sizing comes from P01 scale bounds (max concurrent requests × 2, with PgBouncer if needed). Downstream integration: P04 consumes the task-result persistence schema; P05 runs the changelog via init container; P06 reads the messages table as chunking input; P08 consumes the embedding schema for index tuning.

## Merits

Stated outcomes: 50% faster context retrieval for RAG pipelines and a 90% reduction in migration-related outages. The structural merit is reversibility: every migration can be rolled back and proven clean in staging before it touches production, which converts schema changes from high-risk events into routine operations. Coordinated vector and relational design also prevents the dimension-mismatch class of failures when embedding models change.

## Guidance for Use

**When to reach for P03:** once P01 scale bounds and P02 entity schemas exist, before any table is created; and again when retrieval latency degrades or a table crosses partitioning thresholds.

**Prerequisites:** P01 complete (scale bounds defined, retention policy set) and P02 complete (entity schemas mapped to API models).

**How to run it well:** enforce Liquibase/Flyway as the only migration path — lock direct database access. Version the embedding model in the schema and validate vector dimensions on insert. Test every rollback in staging, not just the forward path. Archive cold partitions to object storage on a schedule rather than letting tables bloat.

**Pairing:** P04 for the result-backend schema; P08 for vector index configuration, which should be derived from the embedding schema defined here rather than designed independently.

**Common misuse:** over-indexing (insert latency degrades; drop unused indexes and defer non-critical builds to off-peak); flat schemas with no partitioning until the table is already slow; unversioned embeddings that silently break similarity search after a model swap; and developers editing the database directly, which desynchronizes the changelog.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
