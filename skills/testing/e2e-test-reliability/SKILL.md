# E2E Test Reliability

## Purpose

Improve E2E signal by eliminating nondeterminism, classifying failures, and governing retries, quarantine, and maintenance.

## When to Use

- Tests fail intermittently or pass on rerun
- CI duration and rerun rates are increasing
- Teams quarantine tests without fixing them
- Browser, network, or parallel behavior causes inconsistent results

## When NOT to Use

- For hiding a deterministic product failure
- Before checking data and environment isolation
- For performance optimization unrelated to test signal

## Inputs

- Test history, retries, duration, and failure artifacts
- Test code, fixtures, environment, and dependency behavior
- Parallelism, browser matrix, and CI scheduling
- Ownership, severity, and release policy

## Expected Outputs

- Flake taxonomy and evidence
- Reliability remediation plan
- Retry, quarantine, ownership, and expiry policy
- Stability metrics and CI improvements

## Workflow

1. Measure pass rate, rerun rate, duration, timeout, and failure clustering by test and environment.
2. Classify failures as product, test synchronization, data, environment, dependency, or infrastructure.
3. Reproduce with traces and controlled repetition before changing retries.
4. Fix selectors, waits, isolation, clocks, network controls, and environment readiness at the cause.
5. Use bounded retries only for known transient infrastructure conditions.
6. Quarantine with an owner, reason, expiry date, and replacement coverage.
7. Review reliability trends and prevent regressions in CI.

## Decision Framework

A retry may reduce infrastructure noise but must never convert a product defect into a pass. Quarantine is temporary debt, not a status. Prioritize failures that block releases or conceal critical journey regressions.

## Quality Checklist

- [ ] Failures have evidence and a category
- [ ] Root cause is addressed before increasing retries
- [ ] Quarantine has owner and expiry
- [ ] Reliability metrics are visible by suite and environment
- [ ] Parallel and cross-browser behavior is included
- [ ] Critical tests remain high-signal and release-protected

## Common Mistakes

- Adding retries to every test
- Deleting flaky tests instead of diagnosing them
- Blaming the browser without reproducing the condition
- Allowing indefinite quarantine

## Related Skills

- **Requires:** e2e-test-automation, e2e-test-data-management, e2e-test-environment
- **Works with:** e2e-test-debugging, root-cause-analysis
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

A reliable suite fails consistently for product defects, exposes infrastructure noise clearly, and improves its pass rate without weakening coverage.
