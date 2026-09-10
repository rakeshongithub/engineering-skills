# Workflow Recipes

Workflow recipes compose the library's individual skills into repeatable engineering paths. Use a recipe when the problem matches a common delivery or operating scenario; use `skills/meta/skill-orchestrator/` when the problem does not fit one of these paths.

## Available Recipes

| Recipe                                          | Use for                                        | Primary outcome                               |
| ----------------------------------------------- | ---------------------------------------------- | --------------------------------------------- |
| [New Feature](new-feature.md)                   | Delivering a user-facing feature safely        | Implementable design and release plan         |
| [System Design](system-design.md)               | Designing a new system or major capability     | Reviewed system design                        |
| [Architecture Review](architecture-review.md)   | Assessing an existing architecture             | Prioritized findings and decisions            |
| [Legacy Modernization](legacy-modernization.md) | Modernizing an existing platform incrementally | Sequenced modernization roadmap               |
| [Production Readiness](production-readiness.md) | Preparing a change or system for production    | Go/no-go decision and launch plan             |
| [Agent Development](agent-development.md)       | Building an agentic engineering system         | Evaluated, guarded, observable agent workflow |
| [Incident Response](incident-response.md)       | Learning from a production incident            | Incident report and prevention backlog        |

## How to Run a Recipe

1. Confirm the entry criteria and collect the listed inputs.
2. Execute each skill in order, preserving the handoff artifact from the previous step.
3. Run parallel steps only when their inputs are complete and independent.
4. Stop at a quality gate when required evidence is missing or a blocking finding is discovered.
5. Record decisions, assumptions, owners, and follow-up work in the final output.

Recipes are starting points, not mandatory chains. The skill orchestrator may add, remove, or reorder steps when constraints or risk justify it.
