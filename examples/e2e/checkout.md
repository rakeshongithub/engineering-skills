# E2E: E-commerce Checkout

## Journey and Risks

A customer adds products, applies a discount, completes payment, and sees an order confirmation. Key risks are duplicate charges, stale inventory, payment decline, timeout recovery, and confirmation loss.

## E2E Plan

Use [e2e-strategy.md](../../workflows/e2e-strategy.md) and [e2e-implementation.md](../../workflows/e2e-implementation.md):

- Design successful checkout, decline, duplicate submission, and recovery journeys.
- Create an isolated cart, customer, inventory reservation, and payment sandbox response per worker.
- Assert order state and customer-visible confirmation, not internal implementation details.
- Capture provider timeout and retry evidence without charging real accounts.
- Block release on successful checkout, decline recovery, and duplicate-submission protection.

## Quality Gates

- No test can create a real charge or share mutable cart data.
- Duplicate submission produces one order and one payment attempt.
- Payment failure leaves a recoverable cart and useful error message.
- Tests run deterministically across the primary supported browsers.
