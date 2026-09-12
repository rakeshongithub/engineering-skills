# Production Readiness Workflow

## Purpose

Determine whether a system or change can be released safely and define the evidence and actions required before launch.

## Use When

Use this recipe before a first production launch, a high-risk change, a major migration increment, or a material change to operational risk. It is a pre-release assessment, not a substitute for active incident response.

## Inputs

- Approved design, requirements, and release scope
- Deployment, rollback, and migration plans
- Test results and known defects
- Security assessment, threat model, and compliance requirements
- SLOs, dashboards, alerts, runbooks, on-call ownership, and capacity assumptions

## Workflow

1. Use `production-readiness` to assess functionality, reliability, security, operations, support, and governance.
2. Use `testing-strategy` to confirm risk-based coverage and evidence for critical paths.
3. Use `security-architecture-review` and `code-review` for unresolved security or implementation risks.
4. Use `reliability-analysis` and `scalability-analysis` for availability, failure, load, and capacity risks.
5. Use `observability-design` to verify logs, metrics, traces, dashboards, and actionable alerts.
6. Use `ci-cd-design` and `deployment-strategy` to validate automation, promotion controls, staged rollout, and rollback.
7. Use `backup-recovery` or `disaster-recovery` when data recovery or regional failure is in scope.
8. Close findings, assign accepted risks, and repeat the readiness review until the decision is explicit.

Steps 2-7 may run in parallel when their evidence is available.

## Quality Gates

- Critical and high risks are fixed, mitigated, or formally accepted by accountable owners.
- Rollback, recovery, and incident escalation paths have been tested or have an approved exception.
- Monitoring can detect user-impacting failures and route alerts to an on-call owner.
- Capacity, security, data protection, and compliance evidence is current.
- The final decision is `go`, `go with conditions`, or `no-go`, with expiry and follow-up dates.

## HITL Checkpoints

- **Risk acceptance:** The accountable risk owner approves any unresolved high or critical risk with conditions, expiry, and compensating controls.
- **Release decision:** The release or service owner records `go`, `go with conditions`, or `no-go` after reviewing the evidence package.
- **Promotion boundary:** Deployment automation must not promote to production until the release decision is valid for the current scope and evidence.

## Outputs

- Readiness checklist and evidence links
- Open-risk register with owners and due dates
- Go/no-go decision
- Launch, rollback, recovery, communication, and post-launch validation plan
