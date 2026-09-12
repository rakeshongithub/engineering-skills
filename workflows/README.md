# Workflow Recipes

Workflow recipes compose the library's individual skills into repeatable engineering paths. Use a recipe when the problem matches a common delivery or operating scenario; use `skills/meta/skill-orchestrator/` when the problem does not fit one of these paths.

## Available Recipes

| Recipe                                                          | Use for                                         | Primary outcome                                              |
| --------------------------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| [New Feature](new-feature.md)                                   | Delivering a user-facing feature safely         | Implementable design and release plan                        |
| [System Design](system-design.md)                               | Designing a new system or major capability      | Reviewed system design                                       |
| [Architecture Review](architecture-review.md)                   | Assessing an existing architecture              | Prioritized findings and decisions                           |
| [Legacy Modernization](legacy-modernization.md)                 | Modernizing an existing platform incrementally  | Sequenced modernization roadmap                              |
| [Production Readiness](production-readiness.md)                 | Preparing a change or system for production     | Go/no-go decision and launch plan                            |
| [Agent Development](agent-development.md)                       | Building an agentic engineering system          | Evaluated, guarded, observable agent workflow                |
| [Incident Response](incident-response.md)                       | Learning from a production incident             | Incident report and prevention backlog                       |
| [Frontend Feature](frontend-feature.md)                         | Delivering a frontend feature safely            | Accessible, tested, performant feature                       |
| [Frontend Architecture Review](frontend-architecture-review.md) | Assessing frontend structure and quality        | Prioritized frontend remediation roadmap                     |
| [Design System](design-system.md)                               | Creating reusable frontend foundations          | Governed component and token system                          |
| [Frontend Migration](frontend-migration.md)                     | Modernizing frontend architecture incrementally | Sequenced migration and rollback plan                        |
| [E2E Strategy](e2e-strategy.md)                                 | Designing an end-to-end testing program         | Journey, data, environment, and gate strategy                |
| [E2E Implementation](e2e-implementation.md)                     | Automating browser journeys                     | Maintainable CI-ready E2E suite                              |
| [E2E Failure Triage](e2e-failure-triage.md)                     | Diagnosing E2E failures                         | Root cause and regression action                             |
| [E2E Release Gate](e2e-release-gate.md)                         | Connecting E2E evidence to releases             | Blocking and exception policy                                |
| [E2E Advanced Quality](e2e-advanced-quality.md)                 | Extending mature E2E programs                   | Mobile, visual, performance, contract, and intelligence plan |

## How to Run a Recipe

1. Confirm the entry criteria and collect the listed inputs.
2. Execute each skill in order, preserving the handoff artifact from the previous step.
3. Run parallel steps only when their inputs are complete and independent.
4. Stop at a quality gate when required evidence is missing or a blocking finding is discovered.
5. Record decisions, assumptions, owners, and follow-up work in the final output.

Recipes are starting points, not mandatory chains. The skill orchestrator may add, remove, or reorder steps when constraints or risk justify it.

## Human-in-the-Loop Policy

Workflows may run analysis and evidence gathering autonomously, but they must pause at a human checkpoint before high-impact decisions or irreversible actions. Use [HITL Checkpoint Contract](hitl-checkpoint-contract.md) to define the boundary.

Require an explicit human decision for:

- Production release, migration cutover, rollback exceptions, or other irreversible changes
- Security, privacy, authorization, data ownership, or compliance decisions
- Accepted high or critical risks, quarantined checks, and release exceptions
- Agent-generated code, tests, designs, or baselines that will be adopted as authoritative

Every checkpoint records the required evidence, approver role, decision, rationale, expiry, audit reference, and follow-up owner. A missing, expired, or rejected decision blocks the dependent step and routes to the documented escalation path.
