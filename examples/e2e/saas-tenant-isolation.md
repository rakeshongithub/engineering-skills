# E2E: SaaS Tenant Isolation

## Journey and Risks

An administrator invites a user, assigns a role, and views tenant analytics. The primary risk is cross-tenant data exposure through routes, filters, exports, or cached responses.

## E2E Plan

- Create two isolated tenants and users per worker.
- Test administrator invitation, role restriction, route access, search, and export behavior.
- Assert tenant A never sees tenant B records, including after navigation and refresh.
- Run the same journey through a primary desktop browser and mobile viewport.
- Retain traces and network evidence for any authorization or data-leak failure.

## Quality Gates

- Cross-tenant access failures block every release.
- Data cleanup removes both tenant namespaces after the run.
- Tests verify permission behavior through user-visible outcomes and server responses where appropriate.
- Cached or persisted state cannot leak between workers.
