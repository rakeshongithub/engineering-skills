# E2E Test Design Examples

## Checkout

Select successful checkout, payment decline, duplicate submission, and order-recovery journeys. Keep discount permutations in lower-level tests unless they cross a meaningful service boundary.

## Banking Transfer

Select authorized transfer, insufficient funds, step-up authentication, duplicate submission, and timeout recovery. Map each to an observable ledger or notification outcome.
