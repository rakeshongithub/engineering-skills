# E2E Test Data Management Examples

## SaaS Tenant Isolation

Create a unique tenant and user per worker, seed only the records needed for the journey, and verify one tenant cannot see another worker's records.

## Payment Failure

Create a fresh order and use a provider sandbox response fixture for decline and timeout cases. Clean up order and payment records by test namespace.
