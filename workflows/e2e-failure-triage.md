# E2E Failure Triage Workflow

## Purpose

Classify and resolve E2E failures without masking product defects or normalizing flaky automation.

## Inputs

- Failed test, commit, environment, browser, and run metadata
- Trace, screenshot, video, console, network, and application logs
- Test data and environment state
- Expected user behavior and recent changes

## Workflow

1. `e2e-test-debugging` preserves artifacts and establishes the failure context.
2. `e2e-test-reliability` classifies the failure as product, test, data, environment, dependency, or infrastructure.
3. `root-cause-analysis` investigates systemic or recurring causes.
4. `test-intelligence-and-failure-analytics` checks clusters, ownership, recurrence, and release impact.
5. Fix the relevant product, test, data, environment, or infrastructure slice.
6. Add regression coverage or reliability controls.
7. `e2e-release-gating` reviews whether the failure changes blocking policy or risk acceptance.

## Quality Gates

- Original evidence is preserved before reruns or changes.
- Failure classification is supported by reproduction or bounded evidence.
- Retries and quarantine do not replace root-cause remediation.
- Ownership, follow-up, and regression coverage are recorded.

## Outputs

- Failure classification and evidence summary
- Root cause and remediation
- Regression test or environment action
- Updated reliability and release-gating decision
