# E2E Test Automation Instructions

1. Organize tests by user journey and business capability.
2. Define stable semantic selectors and domain-focused abstractions.
3. Build fixtures for authentication, data, time, and cleanup.
4. Synchronize on observable application state rather than arbitrary delays.
5. Assert user-visible outcomes, durable state changes, and meaningful transitions.
6. Capture traces, screenshots, video, console, and network evidence on failure.
7. Run locally and in CI across the agreed browser and environment matrix.

Keep abstractions close to the journey and avoid giant page objects that hide intent.
