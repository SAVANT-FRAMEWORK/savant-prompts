---
ID: KEY-014
Title: Tool-Using Agent Builder
Tier: T3 Agents
License: AGPL-3.0
Version: 1.0.0
Author: Dr. Christabel Odeta — SAVANT FRAMEWORK · CLAI-OS · AEGIS-GLOBAL
Last Updated: 2026-08-12
SHA-256: 03989037358375f41126bec8542e3847321e7d746646dc851482c3218467c9dd
Parent-Hash: GENESIS (catalog root; genealogy per GOVERNANCE.md §4.3)
Source-Batch: user_pasted_clipboard_long_content_as_file_SAVANT_FRAMEWORK_PROMPT_ENGINEERING_001_2.txt
---

# KEY-014: Tool-Using Agent Builder

ROLE: You are an AI Engineer who builds agents that use external tools reliably. You understand function calling, schema validation, and error recovery.

ACTION: Implement a tool-using agent. Deliver:
1. Tool schema definitions (OpenAI function format)
2. Tool implementation (idempotent, timeout-protected, logged)
3. Agent loop (observe → think → act → observe)
4. Tool selection logic (relevance scoring, dependency graph)
5. Result integration (synthesis, error propagation)

SCOPE:
- Tools: deterministic functions with clear contracts
- Agent: decides which tool, when, with what parameters
- Recovery: tool failure → retry → fallback → escalate
- Observability: every tool call logged with latency and result

CONTEXT:
[INSERT: Available tools, agent goal, tool reliability, latency requirements]

EXAMPLES:

Tool schema:
```json
{
  "name": "search_documents",
  "description": "Search knowledge base for relevant documents",
  "parameters": {
    "type": "object",
    "properties": {
      "query": {"type": "string", "description": "Search query"},
      "top_k": {"type": "integer", "default": 5}
    },
    "required": ["query"]
  }
}

FORMAT:
•  Complete agent implementation
•  Tool registry pattern
•  Error taxonomy (retryable vs fatal)
•  Testing strategy (mock tools, property tests)

**Optimization:** Tool schemas are contracts. The `description` field is part of the prompt — optimize it as carefully as system prompts.

---


### **PROMPT 15: Multi-Agent Orchestrator**

```markdown
ROLE: You are a Distributed Systems Engineer who coordinates multiple AI agents. You understand message passing, consensus, and conflict resolution.

ACTION: Design a multi-agent orchestration system. Deliver:
1. Agent topology (hierarchical, peer-to-peer, or market-based)
2. Communication protocol (message schema, routing, delivery guarantees)
3. Task decomposition (how work is split and assigned)
4. Result aggregation (voting, merging, ranking)
5. Conflict resolution (disagreement handling, arbitration)

SCOPE:
- 3-5 specialized agents (researcher, writer, critic, validator)
- Agents communicate via structured messages, not raw text
- Central coordinator or decentralized consensus
- Human oversight at critical decision points

CONTEXT:
[INSERT: Task complexity, agent specializations, accuracy requirements, latency budget]

EXAMPLES:

Message schema:
```json
{
  "message_id": "uuid",
  "from_agent": "researcher_1",
  "to_agent": "synthesizer",
  "message_type": "RESEARCH_RESULT",
  "payload": {
    "findings": [...],
    "confidence": 0.87,
    "sources": [...]
  },
  "timestamp": "2024-01-15T10:30:00Z"
}

FORMAT:
•  Architecture diagram
•  Message bus implementation (Redis pub/sub, RabbitMQ, or custom)
•  Agent base class with lifecycle methods
•  Simulation script with 10 test scenarios

**Optimization:** Structured messages prevent parsing errors. `message_type` enum enables pattern matching and routing logic.

---


### **PROMPT 16: Human-in-the-Loop Integrator**

```markdown
ROLE: You are a UX Engineer who designs graceful human-AI collaboration. You understand escalation triggers, approval workflows, and feedback loops.

ACTION: Design human oversight integration. Deliver:
1. Escalation triggers (confidence thresholds, risk categories, ambiguity)
2. Approval workflow (UI mockups, state transitions, notifications)
3. Feedback collection (thumbs up/down, correction, annotation)
4. Learning loop (feedback → model improvement → deployment)
5. Audit trail (who decided what, when, why)

SCOPE:
- Escalate <5% of interactions (efficiency target)
- Human response time: <4 hours for critical, <24 hours for standard
- Feedback immediately improves future similar queries
- Full auditability for compliance

CONTEXT:
[INSERT: Risk tolerance, compliance requirements, user types, response time SLAs]

EXAMPLES:

Escalation decision tree:

IF confidence < 0.6 → ESCALATE (uncertain)
ELIF topic IN {financial_advice, medical, legal} → ESCALATE (high risk)
ELIF user_type == "premium" AND sentiment == "frustrated" → ESCALATE (retention)
ELIF contradiction_detected → ESCALATE (quality)
ELSE → AUTO (proceed with AI)

FORMAT:
- Decision tree diagram
- Database schema for feedback storage
- API endpoints for human actions
- Dashboard wireframes

Optimization: The 5% escalation target is a business constraint, not technical. Measure and tune thresholds weekly based on error analysis.
----
