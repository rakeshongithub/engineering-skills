# Agent Development Workflow

## Purpose

Design, build, evaluate, and operate an AI agent or multi-agent workflow with explicit boundaries and measurable quality.

## Use When

Use this recipe for an agent that performs engineering, business, or operational work. For a deterministic automation with no model-driven decisions, use the relevant software or operations workflow instead.

## Inputs

- User problem, desired outcomes, and unacceptable outcomes
- Tasks the agent may perform and decisions requiring human approval
- Available data, tools, systems, permissions, and trust boundaries
- Quality, latency, cost, privacy, and compliance targets
- Representative test cases and failure scenarios

## Workflow

1. Use `requirements-analysis` to define outcomes, scope, constraints, and measurable acceptance criteria.
2. Use `agent-task-decomposition` to split the work into agent-sized tasks and identify human checkpoints.
3. Use `agent-workflow-design` to choose orchestration, state, retries, concurrency, and failure recovery patterns.
4. Use `agent-context-engineering` and `agent-instruction-design` to define context packages, instructions, outputs, and validation steps.
5. Use `agent-tool-selection` to minimize permissions and choose reliable tools and fallbacks.
6. Use `agent-handoff-design` when multiple agents or human handoffs exchange work.
7. Use `agent-guardrails` and `security-architecture-review` to define safety, privacy, authorization, and resource controls.
8. Use `agent-evaluation` and `testing-strategy` to create a representative evaluation set and regression suite.
9. Use `agent-observability` to instrument traces, tool calls, costs, latency, and failure reasons.
10. Use `production-readiness` and `agentic-workflow-review` before launch and after initial operating evidence.

Steps 7-9 can proceed in parallel once the workflow and interfaces are stable.

## Quality Gates

- The agent has a narrow, testable purpose and explicit out-of-scope behavior.
- Every tool has least-privilege access, validation, timeout, and failure handling.
- Human approval is required for irreversible or high-impact actions.
- Evaluation covers normal, ambiguous, adversarial, and degraded inputs.
- Observability supports tracing a user request through decisions, tools, handoffs, and outcomes.

## HITL Checkpoints

- **Boundary approval:** The accountable owner approves the agent's scope, permissions, prohibited actions, and identified human checkpoints before evaluation or launch.
- **Critical finding review:** A qualified human reviews critical findings or ambiguous outcomes before they trigger changes or external actions.
- **Action approval:** The agent must pause for approval before irreversible or high-impact actions; the decision follows the HITL Checkpoint Contract.

## Outputs

- Agent workflow and state model
- Context and instruction specifications
- Tool and permission inventory
- Guardrail and human-escalation policy
- Evaluation suite, operational telemetry, and production-readiness decision
