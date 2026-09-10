# Frontend: Design System Rollout

## Scenario

A SaaS company has three products with duplicated buttons, forms, dialogs, and spacing rules. It wants a shared design system without blocking product delivery.

## Constraints

- Products currently use different framework versions
- Existing screens cannot be rewritten at once
- Accessibility behavior must improve, not merely become visually consistent
- Teams need versioned packages, migration guidance, and ownership

## Recommended Workflow

Use [design-system.md](../../workflows/design-system.md):

1. `requirements-analysis` identifies repeated patterns, consumers, and adoption outcomes.
2. `frontend-architecture` defines package boundaries, versioning, and compatibility strategy.
3. `component-design` defines primitive APIs, states, composition, and non-goals.
4. `responsive-design` defines shared layout and content rules.
5. `frontend-accessibility-review` establishes semantic and keyboard contracts.
6. `frontend-testing-strategy` adds visual, accessibility, interaction, and compatibility coverage.
7. `frontend-performance-analysis` protects consumer bundle and runtime budgets.

## Adoption Plan

Start with buttons, form controls, and dialogs. Publish usage examples and codemods, migrate one product slice, measure defects and adoption friction, then expand the component set.

## Quality Gates

- Shared components contain no product-specific business logic.
- API and visual changes are versioned with migration guidance.
- Components include accessible states and representative content examples.
- Consumers can adopt incrementally without duplicating the package.
