# E2E Test Automation

## Purpose

Implement maintainable browser-based E2E tests with reliable selectors, synchronization, fixtures, assertions, and failure artifacts.

## When to Use

- Turning approved journey specifications into automated tests
- Establishing E2E project structure and conventions
- Refactoring brittle browser tests
- Standardizing automation across teams

## When NOT to Use

- Before journeys and risk priorities are defined
- For testing pure business logic better covered below the browser
- As a substitute for environment or data strategy

## Inputs

- Approved E2E scenarios and acceptance criteria
- Application routes, accessibility semantics, and API boundaries
- Test data and environment contracts
- Browser matrix, CI limits, and artifact requirements

## Expected Outputs

- Automated journey tests and reusable fixtures
- Selector, synchronization, and assertion conventions
- Failure artifact configuration
- Maintenance and review guidance

## Workflow

1. Organize tests by user journey and business capability.
2. Define stable semantic selectors and page or feature abstractions.
3. Build deterministic fixtures for authentication, data, time, and cleanup.
4. Synchronize on observable application state, not arbitrary delays.
5. Assert user-visible outcomes, URL/state transitions, and persisted results.
6. Capture traces, screenshots, video, console, and network evidence on failure.
7. Run locally and in CI across the agreed matrix, then review maintainability.

## Decision Framework

Use the test framework already supported by the repository. Prefer accessible roles, labels, and test identifiers with clear ownership. Keep abstractions close to the domain journey and avoid giant page objects that hide intent.

## Quality Checklist

- [ ] Tests use stable, user-oriented selectors
- [ ] No arbitrary sleeps hide synchronization problems
- [ ] Fixtures isolate users and data
- [ ] Assertions prove outcomes rather than implementation details
- [ ] Failure artifacts are available and actionable
- [ ] Tests can run locally and in CI with documented setup

## Common Mistakes

- Using CSS structure or generated classes as the primary contract
- Retrying every failure until a broken test passes
- Sharing mutable accounts across parallel tests
- Hiding too much behavior inside generic page objects

## Related Skills

- **Requires:** e2e-test-design, e2e-test-data-management, e2e-test-environment
- **Works with:** e2e-test-reliability, e2e-cross-browser-testing
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

Automation is successful when tests express user intent, fail for meaningful reasons, and remain stable as implementation details evolve.
