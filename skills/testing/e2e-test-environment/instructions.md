# E2E Test Environment Instructions

1. Map every service, queue, database, identity provider, and external dependency used by target journeys.
2. Define the minimum reproducible environment and an owner for each dependency.
3. Provision isolated stores, accounts, secrets, flags, and network access.
4. Virtualize or sandbox expensive or destructive external services while preserving important failures.
5. Add readiness, version, clock, locale, and cleanup checks.
6. Document local, preview, CI, and staging differences.
7. Verify drift and record unsupported conditions.

Do not mock away the system behavior that the E2E test is intended to validate.
