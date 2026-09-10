# E2E: Authentication and MFA

## Journey and Risks

A user signs in, completes MFA, refreshes the session, signs out, and recovers from an expired session. Risks include redirect loops, token leakage, incorrect role routing, and insecure recovery behavior.

## E2E Plan

- Use dedicated test identities and a controlled MFA provider or test channel.
- Cover valid login, invalid credentials, expired code, recovery, session timeout, and logout.
- Assert URL, accessible error, authenticated content, and post-logout protection.
- Run the smoke journey on every pull request and recovery cases before release.
- Use the [E2E failure triage workflow](../../workflows/e2e-failure-triage.md) for redirect or provider failures.

## Quality Gates

- No credentials or MFA codes appear in logs or artifacts.
- Protected routes remain inaccessible after logout and session expiry.
- MFA failures are recoverable without bypassing authentication.
- Redirect and role behavior is stable across supported browsers.
