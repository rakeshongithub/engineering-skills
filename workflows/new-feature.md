# New Feature Workflow

## Purpose

Turn a new product or platform capability into an implementable, tested, secure, and operable change.

## Use When

- A feature crosses more than one component or team.
- Requirements, risks, or operational impact are not yet clear.
- The change needs a shared design and release decision.

For a small, isolated change with clear acceptance criteria, use the relevant individual skills instead.

## Inputs

- Problem statement and user outcomes
- Functional and non-functional requirements
- Existing architecture and affected components
- Constraints, dependencies, compliance needs, and target date

## Workflow

1. **Clarify the requirement** with `requirements-analysis`. Produce acceptance criteria, assumptions, constraints, and open questions.
2. **Design the solution** with `system-design`. Produce component boundaries, interfaces, data flows, failure behavior, and a rollout outline.
3. **Review the architecture** with `architecture-review`. Identify coupling, scalability, reliability, and maintainability risks.
4. **Review exposed interfaces** with `api-design-review` when APIs, events, or contracts change.
5. **Review data changes** with `data-architecture-review` when schemas, storage, retention, or consistency change.
6. **Review security** with `security-architecture-review` for identity, authorization, sensitive data, or trust-boundary changes.
7. **Define validation** with `testing-strategy`. Map acceptance criteria and failure modes to automated and manual tests.
8. **Document the design** with `technical-design-document`, including decisions and unresolved risks.
9. **Prepare the launch** with `production-readiness`; add `ci-cd-design` or `deployment-strategy` when pipeline or rollout behavior changes.

Steps 4-6 may run in parallel after the system design is stable.

## Quality Gates

- Requirements have testable acceptance criteria.
- No critical architecture, security, data, or reliability finding is unresolved.
- The design identifies ownership, observability, rollback, and migration behavior.
- Tests cover happy paths, boundaries, failures, authorization, and compatibility.
- Production readiness has an explicit owner and go/no-go outcome.

## Outputs

- Approved technical design
- API and data change specifications, when applicable
- Test strategy and acceptance mapping
- Risk register and architecture decisions
- Implementation, rollout, rollback, and operational plan

## Common Variations

- **API-only feature:** emphasize `api-design-review`, security, compatibility, and contract testing.
- **Data-heavy feature:** add `data-architecture-review`, migration planning, and backup/recovery checks.
- **High-risk release:** add `reliability-analysis`, `scalability-analysis`, and a staged `deployment-strategy`.
