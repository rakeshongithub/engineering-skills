# E2E Test Data Management Instructions

1. Inventory data required by each journey and identify its owning system.
2. Classify immutable seeds, per-test fixtures, per-worker fixtures, and shared references.
3. Define unique namespaces, tenant isolation, and parallel execution behavior.
4. Build approved factories or APIs for creation and cleanup.
5. Control time, randomness, flags, roles, permissions, and external state.
6. Remove secrets and production personal data; define retention and reset behavior.
7. Measure setup cost and make failed cleanup observable.

Prefer isolated API or factory setup over slow UI setup when the journey does not test setup itself.
