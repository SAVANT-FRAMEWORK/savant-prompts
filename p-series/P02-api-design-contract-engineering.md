---
ID: P02
Title: API Design Specialist
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P02: API Design Specialist

P02 converts the interface contracts produced by P01 into rigorous, machine-verifiable API contracts for AI-native services. It enforces contract-first design: schemas precede implementation, error responses are structured objects rather than ad-hoc strings, backward compatibility is gated by tooling, and security headers are uniform across endpoints. The output is a self-documenting API surface that humans can read and machines can validate.

## Methodology

Three cooperating subsystems:

1. **Contract designer.** Every endpoint is specified in OpenAPI 3.1 with fully typed, constrained, documented request and response schemas — success plus all known error variants — bound security schemes (OAuth2, API key, JWT), and auto-generated example payloads realistic enough to serve as documentation and test fixtures. A coverage score tracks what percentage of business concepts is represented in the API surface.
2. **Version manager.** Automated breaking-change detection between spec revisions: removed fields, type narrowing, and new required fields are breaking; optional additions and description updates are safe. Deprecation policy is enforced mechanically — announce for N versions, remove at N+1 — and a migration guide with code snippets is generated for any breaking change.
3. **Security scanner.** OWASP API Security Top 10 validation, header auditing, and leak detection: authentication on all non-public endpoints, rate-limit headers (`X-RateLimit-Remaining`, `Retry-After`), explicit and restrictive CORS, no sensitive data in URL parameters, HTTPS-only enforcement, and no PII in example payloads. Produces a risk score from 0.0 (secure) to 1.0 (critical).

## Implementation

Deployment sequence from the operations abstract: define request/response schemas; implement endpoints with Pydantic; write consumer-driven contract tests; deploy to staging and share the OpenAPI spec; consumer integration begins in parallel.

Concrete artifacts:

- `openapi.yaml` — full OpenAPI 3.1 specification with endpoints, schemas, and examples; expect at least 10 documented endpoints.
- `docs/api/error-taxonomy.md` — structured error code catalog with remediation steps.
- `docs/api/versioning-policy.md` — deprecation schedule, breaking-change definition, migration templates.
- `generated/` — auto-generated server stubs and client SDKs (Python, TypeScript, Go); generated code is the source of truth, controllers are never hand-written.
- `tests/api/contract-tests/` — schema validation and example round-trip tests.

Debugging checks: health endpoint reports the OpenAPI version; `openapi-diff` in CI reports zero breaking changes without deprecation notice; security headers verified via `curl -I`; every example payload validated against its schema; generated client compiles cleanly.

Telemetry thresholds from the operations abstract: time to first consumer integration under 1 day; zero breaking changes per quarter; fewer than 2 "how do I..." support tickets per month.

Downstream integration: P03 maps API schemas to database entities; P04 derives task payload schemas from endpoint contracts; P05 derives ingress routing rules from OpenAPI paths.

## Merits

Stated outcomes: a 70% reduction in integration bugs between frontend and AI backend, and 50% faster third-party onboarding through self-documenting contracts. Rate-limit headers enable client-side backoff, which reduces retry storms. The structural merit is that compatibility and security become CI gates rather than review opinions — a breaking change cannot ship silently, and an unauthenticated endpoint fails the build.

## Guidance for Use

**When to reach for P02:** immediately after P01, before any endpoint is implemented; and whenever a new consumer integrates or a version bump is planned.

**Prerequisites:** P01 complete — ADRs approved and the component diagram current, because endpoint boundaries come from the architecture.

**How to run it well:** design the schema first and generate server stubs from it; never let implementation diverge from the spec. Keep examples valid by validating them in CI and regenerating them on spec change. Maintain a single active API version plus a deprecation window; sunset old versions on schedule.

**Pairing:** run P03 next so entity schemas stay aligned with request/response models; sync tool schemas from P14 back into the OpenAPI components when agent tools are exposed as endpoints.

**Common misuse:** treating the spec as documentation written after the code (schema drift follows); shipping examples that no longer validate; permitting a permissive CORS policy out of convenience; and skipping the diff gate because "this change looks safe" — that is exactly the change that breaks a consumer.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
