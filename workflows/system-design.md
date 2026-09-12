# System Design Workflow

## Purpose

Convert a significant engineering requirement into a coherent system design with explicit interfaces, data behavior, tradeoffs, and operational constraints.

## Use When

Use this recipe for a new system, a major capability, or a change whose component boundaries and quality attributes are uncertain. For a narrow implementation detail, use a focused skill or the New Feature recipe.

## Inputs

- Validated requirements and acceptance criteria
- Users, actors, and critical use cases
- Scale, latency, availability, compliance, and cost targets
- Existing systems, integration points, and organizational constraints

## Workflow

1. Use `requirements-analysis` to establish scope, quality attributes, constraints, and measurable success criteria.
2. Use `architecture-discovery` when the design must fit an existing platform or replace legacy behavior.
3. Use `system-design` to define components, responsibilities, interfaces, data flows, state transitions, and failure handling.
4. Use `tradeoff-analysis` to compare viable alternatives and make assumptions visible.
5. Run `api-design-review` and `data-architecture-review` for interface and persistence decisions.
6. Run `scalability-analysis` and `reliability-analysis` against the stated targets.
7. Run `security-architecture-review` across trust boundaries, identity, data protection, and abuse cases.
8. Use `architecture-decision` to record consequential decisions, rejected alternatives, and reversibility.
9. Use `technical-design-document` to publish the reviewed design and implementation boundaries.

Steps 5-7 can run in parallel after the baseline design exists.

## Quality Gates

- Every major requirement maps to a design element and validation method.
- Capacity assumptions and failure modes are quantified where practical.
- Interfaces, ownership, consistency, and data lifecycle are explicit.
- Security controls are tied to identified threats and trust boundaries.
- Decisions include consequences, alternatives, and an owner.

## HITL Checkpoints

- **Design approval:** A system or domain owner approves the baseline design before implementation or downstream design work treats it as authoritative.
- **Control approval:** Security, data, and compliance owners approve applicable trust-boundary, data-lifecycle, and regulatory decisions.
- **Decision record:** Rejected alternatives, accepted risks, and conditions are recorded with an accountable owner and expiry.

## Outputs

- System context and component design
- API, event, and data contracts
- Capacity and reliability model
- Security findings and required controls
- ADRs and an implementation-ready technical design
