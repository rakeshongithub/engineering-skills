# Incident Response Workflow

## Purpose

Turn a completed production incident into a factual account, validated root cause, and prioritized prevention work.

## Use When

Use this recipe after service has been stabilized, or alongside active response when analysis can proceed without distracting from mitigation. During an outage, follow the operational incident command process first.

## Inputs

- Incident timeline, impact, detection, and response records
- Logs, metrics, traces, deployment history, and configuration changes
- Customer and stakeholder communications
- Recovery actions, workarounds, and unresolved symptoms

## Workflow

1. Use `incident-analysis` to establish scope, impact, timeline, response effectiveness, and contributing conditions.
2. Use `root-cause-analysis` to test causal hypotheses against evidence and distinguish root causes from triggers and symptoms.
3. Use `reliability-analysis` to identify missing resilience controls and failure-mode improvements.
4. Use `observability-design` to address detection, diagnosis, alert quality, and telemetry gaps.
5. Use `security-architecture-review` when the incident involved trust boundaries, data exposure, or unauthorized behavior.
6. Use `architecture-review` or `technical-debt-analysis` when structural conditions contributed to the incident.
7. Use `architecture-decision` for consequential remediation choices.
8. Create a prevention backlog with owners, priority, due dates, and validation evidence; review it through `production-readiness` when changes are ready to launch.

Steps 3-6 may run in parallel after the incident facts and causal analysis are stable.

## Quality Gates

- The timeline separates observed facts from assumptions and memory.
- Root-cause claims are supported by evidence and explain why defenses failed.
- The report avoids blame and identifies actionable system and process conditions.
- Prevention actions have owners, success measures, and verification dates.
- Customer, security, compliance, and reliability follow-ups are explicitly tracked.

## Outputs

- Incident report and impact summary
- Evidence-backed causal analysis
- Detection, resilience, and process findings
- Prioritized prevention backlog and decision records
- Follow-up review date and closure criteria
