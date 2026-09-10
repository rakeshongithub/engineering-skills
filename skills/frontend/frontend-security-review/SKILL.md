# Frontend Security Review

## Purpose

Review browser-facing code and delivery controls for client-side vulnerabilities, unsafe data handling, and authorization mistakes.

## When to Use

- Before releasing authentication, payments, uploads, or sensitive workflows
- When adding third-party scripts, embedded content, or browser storage
- When changing token, session, CSP, or API integration behavior
- After a client-side security incident or dependency concern

## When NOT to Use

- As a replacement for backend authorization or threat modeling
- For dependency scanning alone
- For active incident containment, where incident response takes priority

## Inputs

- Frontend code, routes, forms, and API integration
- Authentication, authorization, session, and token model
- Content sources, user-generated data, and browser storage use
- CSP, security headers, dependencies, build, and deployment configuration

## Expected Outputs

- Client-side threat and trust-boundary findings
- Vulnerability severity, exploitability, and user impact
- Required controls, tests, and configuration changes
- Residual risk and verification plan

## Workflow

1. Map browser trust boundaries, sensitive data, external content, and privileged actions.
2. Review output encoding, HTML rendering, URL handling, redirects, and user-generated content.
3. Review authentication, authorization checks, token storage, session expiry, and CSRF defenses.
4. Inspect browser storage, logs, analytics, error reports, and source maps for data leakage.
5. Review third-party scripts, dependencies, CSP, security headers, uploads, and iframe policies.
6. Test abuse cases such as XSS, clickjacking, open redirect, token theft, and confused-deputy behavior.
7. Record findings, remediation owners, regression tests, and residual risk.

## Decision Framework

Keep authorization authoritative on the server and treat all client checks as user experience controls. Prefer secure platform primitives, strict content policies, short-lived credentials, and minimal sensitive data in the browser.

## Quality Checklist

- [ ] Privileged actions are authorized server-side
- [ ] User-controlled content is safely rendered and encoded
- [ ] Tokens and sensitive data are not exposed unnecessarily
- [ ] CSRF, clickjacking, redirect, and framing risks are addressed
- [ ] Third-party scripts and dependencies are inventoried
- [ ] CSP, security headers, logging, and source-map policy are reviewed

## Common Mistakes

- Storing long-lived sensitive tokens in unrestricted browser storage
- Trusting hidden controls or route guards as authorization
- Allowing arbitrary HTML or URLs without a clear sanitization policy
- Adding third-party scripts without ownership, integrity, or privacy review

## Related Skills

- **Requires:** security-architecture-review, frontend-architecture
- **Works with:** frontend-testing-strategy, frontend-accessibility-review, production-readiness
- **Commonly followed by:** production-readiness

## Evaluation Criteria

A review is successful when browser threats and data flows are explicit, high-impact issues have tested controls, and the remaining risk is accepted by an accountable owner.
