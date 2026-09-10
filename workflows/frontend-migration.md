# Frontend Migration Workflow

## Purpose

Migrate a frontend framework, rendering model, state approach, or component system incrementally while protecting user journeys and release safety.

## Inputs

- Current repository, runtime, dependency, and ownership evidence
- Target platform capabilities and compatibility requirements
- Critical user journeys, quality baselines, and migration constraints
- Team capacity, rollout controls, and rollback options

## Workflow

1. `architecture-discovery` maps current routes, components, state, dependencies, and build/runtime behavior.
2. `technical-debt-analysis` identifies migration blockers and high-risk legacy patterns.
3. `frontend-architecture` defines target boundaries, coexistence model, and migration seams.
4. `frontend-migration` sequences pilot, compatibility, strangler, or parallel-run stages.
5. `frontend-testing-strategy` establishes characterization, contract, visual, accessibility, and end-to-end coverage.
6. `frontend-performance-analysis` compares current and target baselines.
7. `frontend-accessibility-review` and `frontend-security-review` verify control continuity.
8. `deployment-strategy` and `production-readiness` gate each rollout increment.

## Quality Gates

- Critical journeys are characterized before migration.
- Each increment has a bounded blast radius, rollback path, and exit criteria.
- Accessibility, security, performance, and browser support do not regress.
- Old and new paths can be observed and compared during coexistence.

## Outputs

- Current and target frontend architecture
- Migration strategy and decision record
- Sequenced roadmap with compatibility boundaries
- Regression, rollout, rollback, and readiness plan
