# Frontend Testing Strategy

## Purpose

Design a risk-based frontend testing strategy covering behavior, accessibility, visual output, integration, and real user journeys.

## When to Use

- Starting a frontend feature or application
- Changing shared components, routing, state, or rendering
- Establishing visual or accessibility regression coverage
- When defects escape despite high code coverage

## When NOT to Use

- For writing one isolated test without strategy decisions
- For debugging a specific failing test
- When the change has no observable frontend behavior

## Inputs

- User journeys, acceptance criteria, and risk areas
- Component, route, state, and integration boundaries
- Supported browsers, devices, assistive technologies, and locales
- CI constraints, test data, and release expectations

## Expected Outputs

- Test pyramid and coverage map
- Component, integration, visual, accessibility, and end-to-end cases
- Test data and environment strategy
- CI execution plan and defect triage rules

## Workflow

1. Map acceptance criteria and critical user journeys to observable outcomes.
2. Classify risks across behavior, state, network, browser, accessibility, visual, and performance dimensions.
3. Assign each risk to the cheapest reliable test level.
4. Define deterministic fixtures, network boundaries, time, storage, and authentication setup.
5. Add component and integration tests for state and interaction behavior.
6. Add accessibility, visual, and end-to-end coverage for critical journeys.
7. Define browser matrix, CI parallelization, quarantine policy, and maintenance ownership.
8. Review escaped defects and adjust the strategy.

## Decision Framework

Prefer tests that assert user-visible behavior. Use unit tests for pure logic, component tests for interaction and state, integration tests for boundaries, and end-to-end tests for a small set of critical journeys. Add visual tests where layout or styling is a product contract.

## Quality Checklist

- [ ] Critical journeys have deterministic end-to-end coverage
- [ ] Interactive states and failure paths are tested
- [ ] Keyboard and accessibility assertions are included
- [ ] Network, time, storage, and authentication are controlled
- [ ] Browser and viewport coverage reflects supported users
- [ ] Flaky tests have ownership and a resolution policy

## Common Mistakes

- Measuring coverage without validating user journeys
- Testing implementation details instead of behavior
- Making every test end-to-end and slow
- Ignoring loading, empty, error, offline, and permission states

## Related Skills

- **Requires:** testing-strategy, component-design
- **Works with:** frontend-accessibility-review, frontend-performance-analysis, frontend-security-review
- **Commonly followed by:** production-readiness

## Evaluation Criteria

A strategy is successful when critical risks map to reliable tests, failures are diagnosable, and the suite gives useful release confidence without excessive cost or flakiness.
