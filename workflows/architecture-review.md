# Architecture Review Workflow

## Purpose

Evaluate an existing architecture against business goals, quality attributes, and operational realities, then produce prioritized actions.

## Use When

Use this recipe for a periodic review, a major change, a platform risk assessment, or a system whose behavior and ownership are poorly understood. During an active outage, use [Incident Response](incident-response.md) first.

## Inputs

- Current architecture diagrams and repository or deployment evidence
- Business goals, requirements, and quality targets
- Traffic, cost, reliability, security, and incident data
- Known constraints, risks, and planned changes

## Workflow

1. Use `architecture-discovery` to establish the as-is architecture, dependencies, ownership, and evidence gaps.
2. Use `architecture-review` to assess structure, boundaries, coupling, maintainability, and fitness for purpose.
3. Run `scalability-analysis`, `reliability-analysis`, `security-architecture-review`, and `data-architecture-review` as applicable.
4. Review interfaces with `api-design-review` when service or public contracts are part of the risk.
5. Use `technical-debt-analysis` to quantify structural debt and distinguish symptoms from root causes.
6. Use `tradeoff-analysis` to compare remediation options by impact, effort, risk, and reversibility.
7. Use `architecture-decision` to record decisions and `technical-design-document` to publish the target state.
8. Convert accepted recommendations into an owned, sequenced remediation backlog.

The specialist reviews in step 3 may run in parallel after discovery.

## Quality Gates

- Findings cite observed evidence and affected quality attributes.
- Risks are prioritized by impact, likelihood, and urgency.
- Recommendations identify owners, dependencies, and measurable outcomes.
- The review distinguishes immediate controls from longer-term redesign.
- Disputed assumptions and evidence gaps are explicitly recorded.

## HITL Checkpoints

- **Finding validation:** The accountable system owner reviews material findings, disputed assumptions, and evidence gaps before recommendations become commitments.
- **Risk acceptance:** An accountable risk owner explicitly approves any accepted high or critical risk, including conditions and expiry.
- **Remediation approval:** Owners approve the prioritized remediation backlog and its sequencing before execution.

## Outputs

- Current-state architecture summary
- Prioritized findings and risk register
- Target-state options and decision record
- Remediation roadmap with owners and validation measures
