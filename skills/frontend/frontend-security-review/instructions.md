# Frontend Security Review Instructions

1. Map browser trust boundaries, sensitive data, external content, and privileged actions.
2. Review rendering, encoding, URLs, redirects, forms, uploads, and user-generated content.
3. Review authentication, authorization, token storage, session expiry, and CSRF defenses.
4. Inspect browser storage, logs, analytics, error reports, source maps, and data retention.
5. Inventory dependencies, third-party scripts, CSP, security headers, iframe, and framing policy.
6. Test XSS, clickjacking, open redirect, token exposure, and confused-deputy scenarios.
7. Record severity, exploitability, required controls, regression tests, and residual risk.

Treat client-side checks as experience controls; server-side authorization remains authoritative.
