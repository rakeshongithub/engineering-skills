# E2E Advanced Quality Workflow

## Purpose

Extend a mature E2E program with mobile, visual, performance, contract, intelligence, and controlled automation capabilities.

## Inputs

- Existing E2E suite, journey inventory, reliability history, and release policy
- Mobile platform, browser, visual, performance, and API compatibility requirements
- Test artifacts, CI metadata, escaped defects, and ownership information
- Automation or agent boundaries and review controls

## Workflow

1. `e2e-test-design` confirms the journey and risk scope.
2. Run `mobile-native-e2e-testing`, `visual-regression-testing`, `performance-journey-testing`, and `frontend-backend-contract-testing` in parallel for the relevant surfaces.
3. `test-intelligence-and-failure-analytics` combines execution evidence, failure trends, ownership, and business risk.
4. `automated-e2e-test-generation` proposes new or maintained tests from approved journeys and evidence.
5. `e2e-test-reliability` validates generated and advanced suites before release use.
6. `e2e-release-gating` decides which evidence blocks pull requests, deployments, and releases.

## Quality Gates

- Advanced coverage is tied to a user, platform, compatibility, or release risk.
- Generated tests and visual baselines receive human review.
- Performance and analytics data retain enough context to reproduce failures.
- Mobile, visual, contract, and generated suites do not weaken core journey coverage.
- Exceptions have owners, expiry, rationale, and compensating controls.

## HITL Checkpoints

- **Generated artifact review:** A qualified human reviews generated tests and visual baselines before release use.
- **Coverage decision:** Product, platform, or quality owners approve advanced coverage and its relationship to core journeys.
- **Exception approval:** An accountable risk owner approves exceptions or quarantines with expiry and compensating controls.

## Outputs

- Advanced E2E coverage map
- Mobile, visual, performance, and contract test plans
- Reliability and failure-intelligence dashboards
- Reviewed automation proposals
- Updated release evidence and risk policy
