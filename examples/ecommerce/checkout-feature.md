# E-commerce: Checkout Feature

## Scenario

An online retailer wants to add a checkout flow that supports saved payment methods, promotional discounts, and asynchronous payment confirmation without creating duplicate orders.

## Context and Constraints

- Existing services: catalog, cart, order, payment, and notification
- 100,000 daily users and seasonal traffic spikes of 8x
- Payment provider may respond asynchronously or time out
- PCI-sensitive payment data must remain outside the platform
- Checkout must remain backward compatible with the existing cart API
- Target: p95 checkout API latency below 400 ms excluding provider confirmation

## Recommended Workflow

Use [new-feature.md](../../workflows/new-feature.md):

1. `requirements-analysis` defines acceptance criteria for authorization, retries, duplicate submission, discounts, and confirmation states.
2. `system-design` defines the checkout orchestration, order state machine, payment webhook, and idempotency storage.
3. Run `api-design-review`, `data-architecture-review`, and `security-architecture-review` in parallel.
4. `reliability-analysis` models provider timeouts, duplicate webhooks, and notification failure.
5. `testing-strategy` creates contract, integration, concurrency, and end-to-end coverage.
6. `production-readiness` validates dashboards, alerts, rollback, reconciliation, and on-call ownership.

## Key Design Decisions

- The client sends an `Idempotency-Key` for checkout creation.
- The order service owns the order state machine; the payment service owns provider state.
- Payment webhooks are authenticated, deduplicated, and processed through an inbox table.
- The platform stores provider tokens and payment status, never raw card data.
- A reconciliation job compares provider settlements with internal orders.

## Expected Outputs

- Acceptance criteria and checkout state-transition table
- API and webhook contracts with error semantics
- Data model for idempotency, payment attempts, and reconciliation
- Threat and failure-mode findings with mitigations
- Test matrix and staged rollout plan

## Quality Gates

- Repeated requests cannot create duplicate orders or charges.
- Webhook processing is authenticated and idempotent.
- Abandoned, pending, failed, and paid states are observable.
- Rollback does not corrupt order or payment state.
- Load and provider-failure tests meet the stated latency and recovery targets.
