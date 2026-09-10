# Frontend: Framework Migration

## Scenario

A customer portal is migrating from a legacy client-rendered framework to a supported hybrid-rendering platform while continuing weekly feature delivery.

## Constraints

- 120 routes and 15 critical customer journeys
- One team must support old and new routes during migration
- Authentication, analytics, and file uploads cross the framework boundary
- No broad rewrite without characterization tests and rollback

## Recommended Workflow

Use [frontend-migration.md](../../workflows/frontend-migration.md):

1. `architecture-discovery` maps routes, dependencies, shared state, build behavior, and runtime integrations.
2. `technical-debt-analysis` prioritizes obsolete patterns and migration blockers.
3. `frontend-architecture` defines target boundaries and coexistence rules.
4. `frontend-testing-strategy` creates characterization, contract, accessibility, visual, and end-to-end coverage.
5. `frontend-migration` sequences a pilot route, shared shell migration, and route-by-route cutover.
6. `frontend-performance-analysis` compares old and new route baselines.
7. `frontend-accessibility-review` and `frontend-security-review` verify control continuity.
8. `deployment-strategy` and `production-readiness` gate each cohort rollout.

## Quality Gates

- The pilot route has measurable parity and an approved rollback path.
- Authentication, file uploads, analytics, and error reporting work across the boundary.
- Critical journeys are tested in both old and new paths during coexistence.
- Performance and accessibility do not regress at any migration stage.
