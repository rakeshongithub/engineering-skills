# E2E Strategy Workflow

## Purpose

Design a focused E2E program that validates critical user journeys with appropriate data, environment, browser, reliability, and release controls.

## Inputs

- User journeys, acceptance criteria, and business risk
- Application architecture and integration boundaries
- Supported browsers, devices, locales, and environments
- Existing test history, escaped defects, CI limits, and release policy

## Workflow

1. `testing-strategy` defines the overall test pyramid and risk model.
2. `frontend-testing-strategy` maps frontend behavior and critical journeys to test levels.
3. `e2e-test-design` selects journeys, failure cases, authorization boundaries, and recovery paths.
4. `e2e-test-data-management` defines isolated data, roles, namespaces, seeding, and cleanup.
5. `e2e-test-environment` defines dependencies, provisioning, readiness, virtualization, and reset.
6. `e2e-cross-browser-testing` defines smoke and regression platform tiers.
7. Add `frontend-backend-contract-testing` when independently released interfaces can drift.
8. Add `mobile-native-e2e-testing`, `visual-regression-testing`, or `performance-journey-testing` for the relevant product risks.
9. `e2e-release-gating` maps evidence to pull-request, merge, scheduled, and release gates.

## Quality Gates

- Every E2E scenario has a user outcome and risk-based justification.
- Data and environment setup are deterministic and privacy-safe.
- Browser coverage reflects supported users and meaningful risk.
- Blocking policy matches suite reliability and release impact.

## HITL Checkpoints

- **Journey approval:** Product or service owners approve the critical journey inventory and its risk justification.
- **Privacy and data approval:** Data or security owners approve roles, namespaces, test data, and retention controls when sensitive flows are in scope.
- **Gate policy approval:** The release or quality owner approves which suites block pull requests, deployments, and releases.

## Outputs

- E2E journey inventory and coverage map
- Data and environment strategy
- Browser matrix and suite tiers
- Release-gating and ownership policy
