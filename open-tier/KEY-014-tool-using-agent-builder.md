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

PROMPT 15 (KEY-015): PENDING — disclosed as ledger row per honest-absence policy.

PROMPT 16 (KEY-016): PENDING — disclosed as ledger row per honest-absence policy.
