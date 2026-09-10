# E2E Test Environment

## Purpose

Define reproducible environments and service controls that let E2E tests run safely and meaningfully.

## When to Use

- Setting up a new E2E suite or CI environment
- Managing APIs, queues, databases, identity, and third parties
- Reproducing failures across local, preview, staging, and release environments
- Reducing environment-specific false failures

## When NOT to Use

- For production infrastructure design alone
- For test data rules without environment dependencies
- For a one-off local smoke test

## Inputs

- Application architecture and service dependencies
- Journey scenarios, data requirements, and browser matrix
- Environment provisioning, secrets, feature flags, and network rules
- External-service contracts and failure simulation needs

## Expected Outputs

- Environment topology and dependency contract
- Provisioning and teardown instructions
- Service virtualization and external integration strategy
- Configuration, secret, and readiness checks

## Workflow

1. Map every service, queue, database, identity provider, and external dependency used by target journeys.
2. Define the minimum reproducible environment and ownership for each dependency.
3. Provision isolated data stores, accounts, secrets, flags, and network access.
4. Virtualize or safely sandbox external services while preserving important failure behavior.
5. Add readiness, health, version, clock, locale, and cleanup checks.
6. Document local, preview, CI, and staging differences.
7. Verify environment drift and record unsupported conditions.

## Decision Framework

Use real dependencies when their contract and behavior are the subject of the test; virtualize expensive, nondeterministic, or destructive dependencies. Never hide a meaningful integration failure behind an unconditional mock.

## Quality Checklist

- [ ] Dependencies and owners are documented
- [ ] Environment can be provisioned and reset predictably
- [ ] Secrets are injected securely and never committed
- [ ] Readiness and version checks run before tests
- [ ] External failures can be simulated intentionally
- [ ] Environment differences are visible in test reports

## Common Mistakes

- Treating staging as immutable or identical to production
- Sharing mutable services across unrelated test runs
- Mocking the system under test instead of a dependency
- Skipping readiness checks and blaming tests for environment failure

## Related Skills

- **Requires:** e2e-test-design, e2e-test-data-management
- **Works with:** deployment-strategy, production-readiness, e2e-test-automation
- **Commonly followed by:** e2e-test-reliability

## Evaluation Criteria

An environment strategy is successful when a new run can be reproduced, dependencies are explicit, and failures distinguish product defects from environment defects.
