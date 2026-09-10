# E2E Implementation Workflow

## Purpose

Implement maintainable E2E automation from approved journey specifications through reliable CI execution.

## Inputs

- E2E scenario specifications and acceptance criteria
- Data factories, environment contract, and browser matrix
- Application selectors, routes, APIs, and authentication model
- CI capacity and failure-artifact requirements

## Workflow

1. `e2e-test-design` confirms scenario boundaries and observable assertions.
2. `e2e-test-data-management` implements isolated factories, accounts, and cleanup.
3. `e2e-test-environment` provisions dependencies and readiness checks.
4. `e2e-test-automation` implements fixtures, selectors, synchronization, assertions, and artifacts.
5. `e2e-cross-browser-testing` runs the agreed smoke and regression matrix.
6. Add `visual-regression-testing`, `performance-journey-testing`, or `mobile-native-e2e-testing` for the approved coverage.
7. `frontend-backend-contract-testing` verifies independently released interfaces.
8. `e2e-test-reliability` measures failures, duration, retries, and nondeterminism.
9. `e2e-release-gating` connects stable suites to CI/CD stages.

## Quality Gates

- Tests express user intent and use stable selectors.
- No arbitrary waits or shared mutable accounts create hidden nondeterminism.
- Failures include enough evidence to diagnose the cause.
- Browser and CI execution is reproducible locally and remotely.

## Outputs

- Automated E2E suite and fixtures
- Data, environment, and browser configuration
- Reliability baseline and remediation backlog
- CI execution and release-gating configuration
