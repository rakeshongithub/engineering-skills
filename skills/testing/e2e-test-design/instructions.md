# E2E Test Design Instructions

1. Inventory critical journeys and rank them by impact, frequency, and failure cost.
2. Map roles, integrations, state transitions, data, and recovery behavior.
3. Select happy paths, high-value failures, authorization boundaries, and recovery cases.
4. Define observable outcomes, preconditions, actions, assertions, data, environment, and cleanup.
5. Assign each scenario to E2E, integration, component, or unit coverage.
6. Review duplication, independence, maintenance cost, and release value.

Prefer a small set of independent, high-signal journeys over broad UI traversal.
