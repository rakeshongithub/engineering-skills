# AI Agent: Support Ticket Triage

## Scenario

A support organization wants an agent to classify incoming tickets, retrieve relevant account and product context, draft a response, and route sensitive or uncertain cases to a human.

## Context and Constraints

- Tickets may contain personal, billing, and security-sensitive information
- The agent may read approved knowledge sources and ticket metadata
- It may apply labels and propose routing, but may not issue refunds or change accounts
- High-confidence classification target: at least 90% on the evaluation set
- Human review is required for security, legal, billing disputes, and low-confidence cases
- Every decision and tool call must be traceable for quality review

## Recommended Workflow

Use [agent-development.md](../../workflows/agent-development.md):

1. `requirements-analysis` defines supported intents, escalation categories, and success measures.
2. `agent-task-decomposition` separates classification, retrieval, drafting, validation, and escalation.
3. `agent-workflow-design` defines the state machine, retries, timeout behavior, and human handoff.
4. `agent-context-engineering` specifies ticket, account, policy, and knowledge context with source boundaries.
5. `agent-instruction-design` defines structured outputs, refusal behavior, and validation steps.
6. `agent-tool-selection` limits tools to read-only retrieval, labeling, and queue routing.
7. `agent-handoff-design` defines the human review packet and re-entry behavior.
8. `agent-guardrails` and `security-architecture-review` address privacy, prompt injection, authorization, and data retention.
9. `agent-evaluation` and `testing-strategy` cover normal, ambiguous, adversarial, and stale-knowledge cases.
10. `agent-observability` and `production-readiness` validate traces, cost, latency, alerts, and escalation operations.

## Example Handoff Packet

```yaml
classification: billing_dispute
confidence: 0.62
recommended_queue: billing-specialists
reason: "Customer disputes a charge and requests a refund"
source_documents:
  - billing/refund-policy-v3
blocked_actions:
  - issue_refund
human_action_required: true
```

## Expected Outputs

- Agent state model and scope boundaries
- Context and instruction contract
- Least-privilege tool inventory
- Guardrail and escalation policy
- Evaluation dataset, scorecard, trace schema, and launch decision

## Quality Gates

- The agent cannot perform actions outside its explicit permission set.
- Sensitive categories always route to the required human queue.
- Unsupported or low-confidence requests are escalated rather than guessed.
- Outputs cite approved sources and preserve data-minimization rules.
- Evaluation and telemetry can detect regressions in accuracy, latency, cost, and escalation behavior.
