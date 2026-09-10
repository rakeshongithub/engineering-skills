# Frontend Testing Strategy Instructions

1. Map acceptance criteria and critical journeys to observable outcomes.
2. Classify risks across behavior, state, network, browser, accessibility, visual, and performance dimensions.
3. Assign each risk to the cheapest reliable test level.
4. Define deterministic fixtures for network, time, storage, authentication, and permissions.
5. Cover component interactions, integration boundaries, accessibility behavior, visual contracts, and critical end-to-end flows.
6. Define browser, viewport, locale, CI, parallelization, and retry policies.
7. Review escaped defects and flaky tests, assigning owners and updating the strategy.

Prefer user-visible assertions over implementation-detail assertions.
