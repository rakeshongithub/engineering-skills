# E2E Test Design

## Purpose

Select and specify end-to-end tests that validate the highest-value user journeys and system risks.

## When to Use

- Choosing which journeys deserve E2E coverage
- Converting requirements into executable browser scenarios
- Reducing a large or redundant E2E suite
- Planning coverage for a new product or critical feature

## When NOT to Use

- For unit or component test design
- For debugging an existing failure
- For testing every UI permutation through the browser

## Inputs

- User journeys, acceptance criteria, and business risk
- System boundaries, integrations, roles, and failure modes
- Supported browsers, devices, locales, and environments
- Existing tests, escaped defects, and release requirements

## Expected Outputs

- Prioritized journey inventory
- Scenario specifications and risk mapping
- Preconditions, actions, assertions, and cleanup rules
- E2E coverage gaps and implementation backlog

## Workflow

1. Inventory critical journeys and rank them by impact, frequency, and failure cost.
2. Map each journey across roles, data, integrations, and state transitions.
3. Select happy paths, high-value failures, authorization boundaries, and recovery paths.
4. Define observable outcomes rather than implementation details.
5. Specify preconditions, actions, assertions, test data, environment needs, and cleanup.
6. Assign each scenario to E2E, integration, component, or unit coverage.
7. Review duplication, maintenance cost, and release value.

## Decision Framework

Use E2E for cross-boundary behavior that users depend on. Keep calculations, permutations, and component states at lower test levels. Prefer a small set of independent, high-signal journeys over broad UI traversal.

## Quality Checklist

- [ ] Every test has a user or business outcome
- [ ] Preconditions and cleanup are deterministic
- [ ] Failure, permission, and recovery cases are represented
- [ ] Assertions verify durable user-visible outcomes
- [ ] Lower-level tests cover details that do not need a browser

## Common Mistakes

- Equating route coverage with user coverage
- Asserting internal selectors or implementation state
- Creating tests without ownership or cleanup
- Making every scenario block every release

## Related Skills

- **Requires:** testing-strategy, frontend-testing-strategy
- **Works with:** e2e-test-data-management, e2e-test-environment
- **Commonly followed by:** e2e-test-automation

## Evaluation Criteria

A design is successful when critical risks map to independent, observable scenarios with clear test-level justification.
