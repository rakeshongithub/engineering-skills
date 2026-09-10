# E2E Test Data Management

## Purpose

Create isolated, deterministic, privacy-safe test data and lifecycle rules for end-to-end tests.

## When to Use

- Tests require users, tenants, orders, permissions, or seeded records
- Parallel execution causes data collisions
- Tests depend on external or stateful systems
- Test data has become stale, slow, or difficult to clean up

## When NOT to Use

- For production data migrations
- For unit fixtures with no environment dependency
- For data-model design unrelated to test execution

## Inputs

- Journey scenarios and data dependencies
- Environment reset and seeding capabilities
- Roles, permissions, tenancy, and lifecycle rules
- Privacy, retention, and parallelism requirements

## Expected Outputs

- Data factory and fixture strategy
- Isolation, naming, seeding, and cleanup rules
- Account and permission model
- Privacy and retention controls

## Workflow

1. Inventory data required by each journey and identify the owning system.
2. Classify data as immutable seed, per-test fixture, per-worker fixture, or shared reference data.
3. Define unique namespaces, tenant isolation, and parallel execution behavior.
4. Build APIs or factories for creation and cleanup rather than UI setup where safe.
5. Control time, randomness, feature flags, permissions, and external state.
6. Remove secrets and production personal data; define retention and reset behavior.
7. Measure setup cost and document recovery when cleanup fails.

## Decision Framework

Prefer API or database setup with approved boundaries over long UI setup flows. Use shared data only when immutable and read-only. Isolation is more important than minimizing fixture creation time for tests that run in parallel.

## Quality Checklist

- [ ] Tests do not depend on execution order
- [ ] Parallel workers cannot collide
- [ ] Data is deterministic, minimal, and privacy-safe
- [ ] Cleanup is idempotent and observable
- [ ] Roles and permissions are explicit
- [ ] Failed cleanup does not contaminate future runs

## Common Mistakes

- Reusing a single mutable test account
- Using production data in lower environments
- Creating all data through slow UI flows
- Ignoring timezone, locale, and clock dependencies

## Related Skills

- **Requires:** e2e-test-design, e2e-test-environment
- **Works with:** e2e-test-automation, e2e-test-reliability
- **Commonly followed by:** e2e-test-environment

## Evaluation Criteria

A data strategy is successful when tests are isolated, repeatable, privacy-safe, and fast enough for their intended execution layer.
