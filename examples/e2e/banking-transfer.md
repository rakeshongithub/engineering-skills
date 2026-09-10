# E2E: Banking Transfer

## Journey and Risks

An authenticated customer creates a transfer, completes step-up authentication, and receives a final status. Risks include unauthorized transfer, duplicate submission, insufficient funds, timeout, and incorrect ledger or notification state.

## E2E Plan

- Design authorized, rejected, expired-authentication, duplicate, and timeout journeys.
- Seed an isolated account and permission set per test; never use production identities.
- Control the clock for authentication expiry and settlement windows.
- Assert the visible transfer status and ledger summary through approved read paths.
- Run cross-browser keyboard coverage because transfer completion is a critical accessible journey.

## Quality Gates

- Authorization and step-up authentication are verified end to end.
- A retry cannot create duplicate transfers.
- Failure artifacts contain no account secrets or sensitive tokens.
- Release gates block on authorization and ledger correctness failures.
