# E2E Test Debugging Examples

## Intermittent Login Failure

Compare trace, network response, clock, and identity-provider logs. If the application is correct but the test starts before the provider is ready, fix environment readiness rather than adding a retry.

## Wrong Order Status

Use the timeline to distinguish a delayed eventual-consistency update from an incorrect state transition, then add the appropriate bounded wait or product regression test.
