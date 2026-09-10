# Frontend Backend Contract Testing

## Purpose

Verify that frontend and backend interfaces remain compatible across schemas, semantics, errors, authorization, and evolution.

## When to Use

- Multiple teams release frontend and backend independently
- APIs, events, or generated clients change frequently
- E2E failures reveal interface drift late in delivery
- Backward compatibility and consumer-driven expectations matter

## When NOT to Use

- As a replacement for business journey E2E tests
- For testing backend internals without a consumer contract
- When the interface is private and fully versioned within one change

## Inputs

- API, event, or GraphQL schemas and examples
- Frontend requests, generated clients, and consumer expectations
- Authentication, authorization, error, pagination, and compatibility rules
- Provider and consumer release process

## Expected Outputs

- Consumer and provider contract inventory
- Request, response, error, and compatibility expectations
- Contract test suite and verification pipeline
- Versioning and breaking-change policy

## Workflow

1. Inventory frontend consumers, endpoints, events, and critical fields.
2. Capture meaningful requests, responses, errors, permissions, and edge cases.
3. Define contracts around consumer needs rather than provider implementation.
4. Generate or implement provider verification against the real schema and behavior.
5. Test missing fields, extra fields, nullability, enum evolution, errors, and authorization.
6. Run contracts in consumer and provider pipelines with version compatibility checks.
7. Escalate breaking changes with migration, deprecation, and rollout guidance.

## Decision Framework

Use contract tests for interface compatibility and E2E tests for complete user outcomes. Prefer additive evolution, explicit nullable behavior, stable error contracts, and provider verification that does not require the full production system.

## Quality Checklist

- [ ] Critical consumers and interfaces are inventoried
- [ ] Success, error, auth, and boundary cases are covered
- [ ] Contracts distinguish required, optional, nullable, and unknown fields
- [ ] Provider verification runs before incompatible release
- [ ] Versioning and deprecation ownership are explicit
- [ ] Contract tests complement, not replace, E2E journeys

## Common Mistakes

- Contract-testing only status codes and ignoring semantics
- Generating contracts from provider schemas without consumer expectations
- Allowing breaking changes because E2E runs later
- Treating mocks as proof that the provider behaves correctly

## Related Skills

- **Requires:** api-design-review, frontend-testing-strategy
- **Works with:** e2e-test-design, testing-strategy, migration-planning
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

Contract testing is successful when interface drift is detected before release and both consumers and providers understand compatibility obligations.
