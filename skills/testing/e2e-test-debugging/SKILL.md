# E2E Test Debugging

## Purpose

Diagnose E2E failures using reproducible evidence and distinguish product, test, data, environment, dependency, and infrastructure causes.

## When to Use

- A test fails locally or in CI
- A failure passes on retry without explanation
- A browser or environment-specific defect is suspected
- Teams need a consistent failure-triage process

## When NOT to Use

- For active production incidents where incident response takes priority
- For changing assertions before understanding the failure
- For replacing root-cause analysis on systemic problems

## Inputs

- Test name, commit, environment, browser, and run metadata
- Trace, screenshot, video, console, network, and application logs
- Test data, timing, retries, and recent changes
- Expected behavior and known environment conditions

## Expected Outputs

- Reproduced failure or bounded non-reproduction
- Evidence-backed failure classification
- Root cause, remediation, and regression test
- Ownership and follow-up record

## Workflow

1. Preserve the complete failure artifact and identify the exact run context.
2. Compare expected and observed user-visible behavior.
3. Inspect timeline, network, console, application, and environment evidence.
4. Re-run with controlled data, browser, timing, and parallelism.
5. Classify the failure and test the smallest hypothesis that distinguishes causes.
6. Fix the product or test at the root cause and add regression coverage.
7. Record unresolved infrastructure issues with an owner and next diagnostic step.

## Decision Framework

Never change a wait, selector, or retry until evidence shows it is the cause. A test failure is a product defect when the user journey is wrong; it is a test defect when the test does not reliably observe correct behavior.

## Quality Checklist

- [ ] Original artifacts and run context are preserved
- [ ] Failure classification is evidence-based
- [ ] Reproduction is attempted under controlled conditions
- [ ] Root cause is separated from symptom
- [ ] Fix includes regression coverage or a justified environment action
- [ ] Ownership and follow-up are recorded

## Common Mistakes

- Debugging from the final error message alone
- Re-running until green without recording failures
- Changing timeouts before examining network and application state
- Closing infrastructure failures without evidence

## Related Skills

- **Requires:** e2e-test-reliability
- **Works with:** root-cause-analysis, incident-analysis, e2e-test-environment
- **Commonly followed by:** e2e-test-reliability

## Evaluation Criteria

Debugging is successful when failures are classified correctly, evidence is preserved, and fixes prevent recurrence.
