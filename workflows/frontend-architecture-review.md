# Frontend Architecture Review Workflow

## Purpose

Assess an existing frontend for maintainability, user experience quality, accessibility, performance, security, and delivery risk.

## Inputs

- Repository, route map, dependency graph, and deployment model
- Critical user journeys and product quality targets
- Real-user performance, defect, accessibility, and incident evidence
- Browser support and organizational ownership constraints

## Workflow

1. `architecture-discovery` maps routes, modules, rendering, dependencies, ownership, and runtime behavior.
2. `frontend-architecture` evaluates boundaries, rendering, routing, state, and delivery choices.
3. Run `frontend-accessibility-review`, `frontend-performance-analysis`, `frontend-security-review`, and `frontend-testing-strategy` in parallel.
4. `technical-debt-analysis` prioritizes structural duplication, fragile patterns, and obsolete dependencies.
5. `tradeoff-analysis` compares remediation options by user impact, effort, risk, and reversibility.
6. `architecture-decision` records target-state choices and rejected alternatives.
7. `technical-design-document` publishes the remediation roadmap and measurable quality targets.

## Quality Gates

- Findings cite code or production evidence and user impact.
- Accessibility, performance, security, testing, and architecture risks are separated but correlated.
- Immediate containment is distinguished from target-state improvement.
- Every accepted recommendation has an owner, measure, and validation plan.

## HITL Checkpoints

- **Finding validation:** The frontend or product owner reviews material findings and user-impact assumptions before publication.
- **Risk acceptance:** Security, accessibility, or product owners explicitly approve accepted high-risk findings with conditions and expiry.
- **Remediation approval:** The accountable owner approves the sequenced roadmap before implementation begins.

## Outputs

- Current-state frontend architecture
- Prioritized findings and risk register
- Target-state options and ADRs
- Sequenced remediation roadmap
