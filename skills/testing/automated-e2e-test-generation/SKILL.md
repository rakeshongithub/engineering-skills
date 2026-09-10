# Automated E2E Test Generation and Maintenance

## Purpose

Use controlled automation or agents to propose, update, and validate E2E tests without weakening user intent, coverage, or review quality.

## When to Use

- Converting structured journeys into initial test drafts
- Updating selectors or flows after intentional UI changes
- Identifying missing coverage from requirements, defects, or traces
- Reducing repetitive maintenance while preserving human ownership

## When NOT to Use

- For blindly generating tests from DOM snapshots
- For auto-accepting changed baselines or assertions
- When requirements, user intent, or test ownership are unclear

## Inputs

- Approved journey specifications and acceptance criteria
- Application semantics, accessibility tree, routes, and API contracts
- Existing tests, failures, traces, screenshots, and defect history
- Generation boundaries, review policy, and repository conventions

## Expected Outputs

- Proposed test or maintenance patch
- Coverage and risk explanation
- Updated selectors, fixtures, or assertions
- Validation results and human review record

## Workflow

1. Define the user intent, risk, scope, and change boundary before generation.
2. Gather approved context and exclude secrets, production data, and unrelated code.
3. Generate or propose tests using semantic selectors and existing fixtures.
4. Validate that assertions describe user-visible outcomes and preserve failure coverage.
5. Run static checks, targeted tests, reliability checks, and relevant visual or contract checks.
6. Present the patch, evidence, limitations, and uncovered cases for human review.
7. Merge only after an owner confirms behavior, maintainability, and release impact.
8. Monitor generated tests for flakiness and revise the generation rules.

## Decision Framework

Automation may propose changes; it must not decide that a changed behavior is correct. Prefer small reviewable patches, explicit provenance, and regression evidence. Reject generated tests that merely reproduce current implementation or remove meaningful assertions.

## Quality Checklist

- [ ] User intent and acceptance criteria are explicit
- [ ] Context excludes secrets and unrelated repository data
- [ ] Generated selectors and assertions are maintainable
- [ ] Existing failure and authorization coverage is preserved
- [ ] Validation evidence accompanies the patch
- [ ] Human ownership and rollback are clear
- [ ] Generated tests are monitored after merge

## Common Mistakes

- Generating tests from screenshots without behavior context
- Replacing stable selectors with brittle generated paths
- Auto-updating snapshots or assertions to make CI green
- Generating many low-value tests instead of a few risk-focused ones

## Related Skills

- **Requires:** e2e-test-design, e2e-test-automation, e2e-test-reliability
- **Works with:** agent-context-engineering, agent-guardrails, agent-evaluation
- **Commonly followed by:** e2e-test-debugging, e2e-release-gating

## Evaluation Criteria

Automated generation is successful when it reduces maintenance effort while preserving intent, coverage, reliability, reviewability, and human accountability.
