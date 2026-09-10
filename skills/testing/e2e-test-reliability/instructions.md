# E2E Test Reliability Instructions

1. Measure pass rate, reruns, duration, timeouts, and failure clustering.
2. Classify failures as product, test synchronization, data, environment, dependency, or infrastructure.
3. Reproduce with traces and controlled repetition before changing retries.
4. Fix selectors, waits, isolation, clocks, network controls, and readiness at the cause.
5. Use bounded retries only for known transient infrastructure conditions.
6. Quarantine only with an owner, reason, expiry, and compensating coverage.
7. Review reliability trends in CI.

A retry may reduce noise; it must never turn a product defect into a pass.
