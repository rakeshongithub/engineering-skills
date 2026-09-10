# Legacy Modernization Workflow

## Purpose

Modernize a legacy system incrementally while protecting current business operations and preserving a path to measurable improvement.

## Use When

Use this recipe for platform renewal, monolith decomposition, infrastructure migration, or replacement of obsolete dependencies. Do not begin with a rewrite plan before understanding the running system and its business constraints.

## Inputs

- Current architecture, code, deployment, and data evidence
- Business capabilities and critical user journeys
- Reliability, performance, cost, and compliance pain points
- Migration constraints, team capacity, budget, and tolerance for downtime

## Workflow

1. Use `architecture-discovery` to map runtime behavior, dependencies, data ownership, and seams for change.
2. Use `technical-debt-analysis` to classify debt by business impact, risk, and remediation cost.
3. Use `architecture-review` to identify structural constraints and target-state gaps.
4. Use `data-architecture-review` to assess ownership, consistency, migration, retention, and rollback concerns.
5. Use `tradeoff-analysis` and `architecture-decision` to compare modernization paths such as strangler, replatform, or replacement.
6. Use `migration-planning` to define phases, compatibility periods, data movement, cutover, rollback, and exit criteria.
7. Use `testing-strategy` to establish characterization, contract, migration, and regression coverage.
8. Use `reliability-analysis` and `security-architecture-review` to validate failure behavior and control continuity.
9. Use `deployment-strategy`, `backup-recovery`, and `production-readiness` to prepare each migration increment for safe release.

## Quality Gates

- Critical behavior is characterized before it is moved or replaced.
- Each increment has a bounded blast radius, rollback path, and measurable exit criteria.
- Data ownership, reconciliation, and compatibility are explicit.
- Security, reliability, and operational controls remain effective during transition.
- The roadmap prioritizes business value and risk reduction, not technology novelty.

## Outputs

- Current-state and target-state architecture
- Modernization option decision and ADRs
- Sequenced migration roadmap
- Data, compatibility, testing, rollout, and rollback plans
- Per-phase readiness criteria and ownership model
