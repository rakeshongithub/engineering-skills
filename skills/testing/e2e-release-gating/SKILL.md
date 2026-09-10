# E2E Release Gating

## Purpose

Define how E2E evidence controls pull requests, deployments, and production releases without creating blind spots or unnecessary friction.

## When to Use

- Adding E2E checks to CI/CD
- Defining smoke, regression, and release suites
- Deciding which failures block deployment
- Reviewing release confidence after escaped defects

## When NOT to Use

- For selecting journeys without risk analysis
- For hiding flaky tests behind non-blocking status
- For production readiness across the whole system alone

## Inputs

- Critical journeys and business risk
- E2E suite reliability, duration, and coverage
- Deployment strategy, environments, and rollback capability
- Release frequency, ownership, and acceptable risk

## Expected Outputs

- E2E suite tiers and blocking policy
- CI/CD execution and artifact plan
- Exception, quarantine, and risk-acceptance process
- Release evidence and review checklist

## Workflow

1. Map tests to critical journeys, failure impact, and release stages.
2. Define fast pull-request smoke tests, merge checks, scheduled regression, and pre-release suites.
3. Set blocking thresholds for product failures, infrastructure failures, and known exceptions.
4. Ensure failures publish actionable artifacts and ownership.
5. Define rollback, retry, quarantine, and risk-acceptance behavior.
6. Pilot the gate and measure duration, false failures, escaped defects, and deployment impact.
7. Review the policy after incidents, architecture changes, and reliability trends.

## Decision Framework

Block on reliable evidence for critical journeys. Keep noisy infrastructure checks visible but separate until fixed. Every exception needs an owner, expiry, rationale, and compensating control.

## Quality Checklist

- [ ] Blocking tests are reliable and high-signal
- [ ] Suite tiers match pipeline speed and risk
- [ ] Failures include artifacts and ownership
- [ ] Exceptions and quarantine have expiry rules
- [ ] Rollback and risk-acceptance paths are explicit
- [ ] Gate effectiveness is measured over time

## Common Mistakes

- Making the entire suite blocking regardless of signal
- Allowing permanent bypasses
- Treating a green suite as proof of all production readiness
- Ignoring test duration and developer feedback loops

## Related Skills

- **Requires:** e2e-test-design, e2e-test-reliability, production-readiness
- **Works with:** deployment-strategy, e2e-cross-browser-testing, e2e-test-debugging
- **Commonly followed by:** production-readiness

## Evaluation Criteria

A release gate is successful when it blocks meaningful regressions, exposes uncertainty honestly, and supports delivery without normalized bypass behavior.
