---
ID: P13
Title: Agent State Machine Designer
Registry: SAVANT FRAMEWORK Operations (P-series, open tier — AGPL-3.0; canonical KEY-001..KEY-015)
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-10-12
---

# P13: Agent State Machine Designer

P13 opens the agents tier by replacing imperative agent scripts — logic scattered across if/else chains, flags, and timers — with formally specified state machines. Agent behavior becomes declarative: named states with entry and exit actions, event-driven transitions with guard conditions, composite states with history and parallelism, and timeouts that fire automatically. The objective is agents that never get stuck, never loop forever, and always make progress, with behavior that is observable, verifiable, and reusable.

## Methodology

Three cooperating subsystems:

1. **State machine engine.** Events are dispatched through the current state: guard conditions evaluate in priority order, the exit action of the current state runs, the transition action runs, the entry action of the target state runs, and the transition is recorded in an immutable history. Typical state inventory: Idle, Planning, Executing, Reviewing, Error. Current state is always queryable, including entry time, elapsed duration, and timeout deadline.
2. **State designer.** Behavior requirements compile to a formal specification (SCXML, JSON, or Python export) covering atomic states, composite (nested) states, parallel regions, transitions with trigger event, guard predicate, and action sequence, in-state timeouts with automatic transition, and shallow or deep history for composite re-entry. The validator checks four properties: reachability (all states reachable from initial), deadlock freedom (no non-terminal state without outgoing transitions), liveness (no infinite loops without progress), and determinism (no conflicting transitions for the same event in the same state).
3. **Event broker.** Ordered, deduplicated, prioritized event delivery per agent: causal ordering preserved (happens-before), identical event IDs processed exactly once, and user-facing events scheduled ahead of background maintenance. The event log is immutable for debugging and audit, and the broker rides on P04 queue infrastructure for persistence under load.

## Implementation

Deployment steps from the operations abstract: map all states and transitions on paper; implement the state machine with explicit guards; add iteration limits and timeouts; test with adversarial inputs and edge cases; add observability with state transitions logged.

Concrete artifacts:

- `deliverables/p13-state-machine-spec.scxml` — formal specification with states, transitions, and guards.
- `deliverables/p13-validation-report.md` — reachability, deadlock, liveness, and determinism proofs.
- `deliverables/p13-event-schema.yml` — event types, payloads, ordering constraints.
- `deliverables/p13-visual-diagram.puml` — PlantUML state diagram rendered in CI, auto-refreshed from runtime state.

Debugging checks from the source: 100% of states reachable from initial with zero deadlocks; zero conflicting transitions for any (state, event) pair; 100 adversarial events processed in causal order with zero out-of-order transitions; automatic timeout transition firing within 1100 ms of a 1000 ms deadline (no stuck agents); live diagram matching runtime state.

Telemetry thresholds from the operations abstract: infinite-loop rate 0% under hard limits; average path length to completion under 5 states; state transition latency under 50 ms. The abstract's reference case is a customer-service bot where explicit states broke the "I didn't understand, please repeat" death spiral and cut escalation to humans by 60%.

Downstream integration: P14 consumes entry/exit actions as tool call schemas; P15 consumes agent states for orchestration; P16 consumes guard conditions as approval gates; P04 brokers the events.

## Merits

Source-stated outcomes: a 60% reduction in agent logic bugs and 80% faster onboarding of new agent behaviors through declarative definitions. The structural merit is verifiability: reachability, deadlock freedom, and determinism are proven against the spec in CI before the agent runs, and the immutable event history makes any misbehavior replayable rather than a log-grep exercise.

## Guidance for Use

**When to reach for P13:** before building any agent beyond a single-turn script; when agents exhibit stuck states, race conditions, or "what state is it in?" debugging sessions; and as the shared foundation for P14–P16.

**Prerequisites:** P04 (event-driven worker infrastructure) and P12 (quality metrics and regression detection) operational.

**How to run it well:** validate the spec in CI on every change — reachability and determinism failures should block merges; enforce event serialization per agent through the broker to eliminate concurrent-event corruption; refactor into composite states when a flat machine grows past roughly 50 states for a simple workflow; and add timeout transitions to every state that waits on anything.

**Pairing:** P13 is the substrate for the rest of the agents tier — P14 tools fire as state actions, P15 orchestrates multiple P13 machines, and P16 approval gates attach as guard conditions.

**Common misuse:** imperative scripts with implicit state; missing timeouts (the "agent stuck for 5 minutes" symptom); conflicting transitions resolved nondeterministically at runtime; over-granular flat state enumeration instead of hierarchy; and visualization maintained by hand until it diverges from the runtime — push diagram updates from the live state instead.

C0 informational classification; confers no clinical, legal, financial, or safety authority.
