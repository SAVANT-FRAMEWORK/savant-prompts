---
ID: P04
Title: Async Programming Master
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P04: Async Programming Master

P04 builds the resilient asynchronous processing layer that keeps AI workloads alive under transient failure and traffic spikes. It replaces synchronous request handling — where a client holds a connection for the full duration of LLM inference and a timeout loses the request — with a queued, idempotent, observable task pipeline with automatic worker scaling. The design goal is a 99.9% task completion rate despite transient failures.

## Methodology

Three cooperating subsystems:

1. **Pipeline orchestrator.** Clients submit tasks and receive tickets immediately. Each task carries an idempotency key (duplicate submissions return the same ticket), a priority tier (critical / standard / background), a TTL bounding queue wait plus execution time, and a retry policy. Status queries return pending / running / completed / failed / dead-letter with queue position, worker ID, and attempt count. Results are delivered by webhook callback or polling against the P03 result store.
2. **Retry engine.** Errors are classified before any retry decision: retryable (timeout, rate limit, 5xx, network blip) versus terminal (4xx, schema validation failure, safety violation). Backoff is exponential with full jitter to prevent thundering-herd retries; poison messages are quarantined to a dead-letter queue after 3 attempts and require manual release.
3. **Observability injector.** OpenTelemetry trace context propagates from the client through enqueue and every worker hop. Prometheus metrics cover queue wait time, processing time, token count, and per-task compute and API spend. Structured logs correlate by task ID with error sampling. Pipeline health exposes queue depth, worker utilization, error rate, and p50/p95/p99 latency.

## Implementation

Deployment steps from the operations abstract: profile existing synchronous code to find blocking calls; refactor I/O (database, HTTP, cache) to async; add connection pooling sized by load test; implement circuit breakers for external services; load test to verify linear scaling.

Concrete artifacts:

- `workers/` — Celery tasks or Temporal workflows with retry configuration, idempotency, and tracing.
- `infra/queue.tf` — Terraform for Redis, SQS, or RabbitMQ.
- `infra/workers.tf` — Kubernetes Deployment plus HPA for worker autoscaling.
- `dashboards/pipeline.json` — Grafana dashboard: queue depth, latency, error rate, per-task spend.
- `docs/pipeline/runbooks.md` — incident runbooks for queue backup, dead-letter surge, and worker crash loops.

PromQL debugging checks from the source: task completion rate above 0.999; retries under 10% of failures (most tasks succeed on first attempt); average queue depth below worker count × 10; per-task spend within 10% of the P01 cost model; dead-letter count at zero or documented.

Telemetry thresholds from the operations abstract: sustained throughput above 1000 requests per second per core on the async path (versus under 50 synchronous); error rate during traffic spikes below 0.1%; flat memory under load (no connection leaks).

Downstream integration: P05 consumes worker definitions as Kubernetes manifests; P06+ submit background chunking and embedding tasks; P17 and P35 consume the latency metrics and trace context.

## Merits

Source-stated outcomes: 99.9% task completion despite transient failures, and a 40% reduction in idle compute spend through queue-depth-driven worker scaling. The structural merit is that failure becomes a first-class, inspectable state — dead letters are quarantined with context rather than crashing workers in a loop, and every retry decision is classified and bounded instead of left to defaults.

## Guidance for Use

**When to reach for P04:** whenever LLM inference or other slow I/O sits behind a user-facing request path; before any workload where task volume will spike unpredictably; and when synchronous timeouts start losing requests.

**Prerequisites:** P01 (failure modes enumerated, scale bounds set), P02 (async endpoint schemas defined), and P03 (task result persistence schema ready).

**How to run it well:** enforce idempotency keys at the API layer, not just the worker; scale workers on queue depth rather than CPU; use spot instances for worker pools where interruption is tolerable; set result TTLs and purge after callback delivery to prevent worker memory growth; and validate trace injection at enqueue and worker start so spans connect end to end.

**Pairing:** P05 for autoscaling manifests; P11 and P17 consume its metrics; P13's event broker rides on this queue infrastructure, so get P04 stable before building agent systems.

**Common misuse:** retrying everything (4xx and validation errors are terminal — retrying them is wasted work and can amplify cost); backoff without jitter (synchronized retries trip provider rate limits); static worker pools sized for peak; and treating dead letters as noise rather than as investigated, documented exceptions.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
