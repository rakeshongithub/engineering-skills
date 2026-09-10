# E2E Test Debugging Instructions

1. Preserve the complete failure artifact and exact run context.
2. Compare expected and observed user-visible behavior.
3. Inspect timeline, network, console, application, and environment evidence.
4. Re-run with controlled data, browser, timing, and parallelism.
5. Classify the failure and test the smallest distinguishing hypothesis.
6. Fix the product or test at the root cause and add regression coverage.
7. Record unresolved infrastructure issues with an owner and next diagnostic step.

Do not change waits, selectors, or retries before examining the evidence.
