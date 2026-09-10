# Frontend Security Review Examples

## Support Ticket Portal

Review rich-text rendering, attachment previews, redirect handling, session expiry, and third-party analytics. Require safe rendering, approved origins, short-lived sessions, and redaction in telemetry.

## Payment Form

Confirm raw card data never enters application state or logs, provider-hosted fields are framed under an approved policy, and authorization remains server-side for every payment action.
