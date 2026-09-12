# E2E Release Gate Workflow

## Purpose

Establish E2E evidence and blocking rules for pull requests, deployments, and production releases.

## Inputs

- Critical journey inventory and E2E suite
- Reliability, duration, and coverage metrics
- Deployment strategy, environments, and rollback capability
- Release risk, exception policy, and accountable owners

## Workflow

1. `e2e-test-design` maps journeys to business impact and release stages.
2. `e2e-test-reliability` verifies that candidate blocking suites are trustworthy.
3. `e2e-cross-browser-testing` defines fast and broad platform tiers.
4. Add mobile, visual, performance, and contract evidence when those risks are in scope.
5. `test-intelligence-and-failure-analytics` summarizes reliability, coverage, and uncertainty.
6. `e2e-release-gating` defines blocking thresholds, artifacts, exceptions, and ownership.
7. `deployment-strategy` connects suites to promotion and rollback behavior.
8. `production-readiness` confirms the E2E gate is one part of complete launch readiness.

## Quality Gates

- Blocking checks are reliable, high-signal, and tied to critical journeys.
- Failures publish artifacts and route to an owner.
- Exceptions and quarantine have expiry, rationale, and compensating controls.
- Rollback and risk-acceptance paths are explicit.

## HITL Checkpoints

- **Gate policy approval:** The release or quality owner approves blocking thresholds and the journeys they protect.
- **Exception approval:** An accountable risk owner approves every quarantine, exception, or retry policy that weakens a release gate, with expiry and compensating controls.
- **Release promotion:** The release owner reviews the current evidence and authorizes promotion when the gate is satisfied or a valid exception exists.

## Outputs

- Suite tiers and pipeline execution plan
- Blocking, exception, retry, and quarantine policy
- Release evidence checklist
- E2E gate effectiveness measures
