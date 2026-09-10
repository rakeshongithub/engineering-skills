# E2E Test Reliability Examples

## Flaky Checkout Test

A test passes after retry because it races a payment-status update. Replace the fixed delay with a bounded assertion on the visible order state and retain the original trace for diagnosis.

## Shared Account Collision

Parallel profile tests modify one user. Assign a unique user per worker and make cleanup idempotent instead of increasing retry counts.
