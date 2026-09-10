# Test Intelligence and Failure Analytics

## Purpose

Turn E2E execution data into actionable insight about failures, flakiness, duration, coverage, ownership, and release risk.

## When to Use

- Test suites are slow, noisy, or difficult to prioritize
- Teams need failure trends across branches, browsers, or environments
- Release decisions require evidence beyond pass or fail
- Reliability work needs measurable outcomes

## When NOT to Use

- For collecting telemetry without an operational question
- For replacing root-cause investigation with a dashboard
- For ranking tests solely by frequency without business impact

## Inputs

- Test runs, retries, durations, artifacts, and environment metadata
- Journey, ownership, browser, commit, and release context
- Defects, incidents, quarantines, and escaped failures
- Coverage and risk model

## Expected Outputs

- Failure taxonomy and trend dashboards
- Flakiness, duration, coverage, and ownership metrics
- Risk-weighted test prioritization
- Reliability and release recommendations

## Workflow

1. Define questions and metrics tied to test reliability and release risk.
2. Normalize run, test, journey, browser, environment, commit, and artifact identities.
3. Classify failures and distinguish first failures from retries.
4. Analyze clusters by test, journey, owner, browser, environment, and change.
5. Combine technical signal with business criticality and escaped-defect history.
6. Publish actionable dashboards, alerts, and ownership queues.
7. Review outcomes and change the suite, environment, or policy based on evidence.

## Decision Framework

Use risk-weighted signal rather than raw pass rate. Preserve uncertainty and sample size. A high-volume low-risk flaky test may be less urgent than one rare failure in a payment or authorization journey.

## Quality Checklist

- [ ] Failure categories are stable and actionable
- [ ] Retries are not counted as independent passes
- [ ] Metrics include journey, browser, environment, and owner context
- [ ] Dashboards lead to actions, not passive reporting
- [ ] Privacy and retention rules protect artifacts and user data
- [ ] Release decisions include confidence and uncertainty

## Common Mistakes

- Treating rerun success as test reliability
- Ranking work by failure count alone
- Losing artifacts or metadata needed to reproduce failures
- Building dashboards no team owns or reviews

## Related Skills

- **Requires:** e2e-test-reliability, e2e-test-debugging
- **Works with:** agent-observability, incident-analysis, e2e-release-gating
- **Commonly followed by:** e2e-test-reliability

## Evaluation Criteria

Test intelligence is successful when teams identify the highest-risk reliability work quickly and can measure whether interventions improve signal.
