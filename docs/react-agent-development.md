# React Agent Development Guide

This guide shows how to use the repository's skills and workflows with an existing React agent team:

- `planner`
- `review`
- `implementer`
- `deliver`
- `figma`
- `peacock`

The client also provides MCP servers for Figma and the Peacock design system.

## Operating Model

Use each layer for a different purpose:

| Layer            | Responsibility                                                |
| ---------------- | ------------------------------------------------------------- |
| Agents           | Decide, investigate, implement, review, and prepare delivery  |
| Skills           | Provide focused engineering expertise                         |
| Workflows        | Define the sequence, quality gates, and expected artifacts    |
| MCP servers      | Provide project-specific design and component-system evidence |
| HITL checkpoints | Control important decisions and irreversible actions          |

The general rule is: agents can gather evidence and prepare changes autonomously, but a human approves design scope, authoritative artifacts, risk acceptance, migration cutovers, and production releases.

## Agent Mapping

| Client agent  | Recommended repository workflows and skills                                                                                                                       | Primary responsibility                                      |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| `planner`     | `frontend-feature`, `requirements-analysis`, `frontend-architecture`, `component-design`, `frontend-state-management`                                             | Turn a request into an implementable feature packet         |
| `figma`       | Figma MCP, `responsive-design`, `component-design`, `frontend-accessibility-review`                                                                               | Extract design evidence and interaction requirements        |
| `peacock`     | Peacock MCP, `design-system`, `component-design`, `frontend-architecture`                                                                                         | Map the design to existing components, variants, and tokens |
| `implementer` | `frontend-feature`, `e2e-implementation`, `frontend-testing-strategy`, `frontend-performance-analysis`                                                            | Implement the approved feature packet                       |
| `review`      | `frontend-architecture-review`, `code-review`, `frontend-accessibility-review`, `frontend-security-review`, `frontend-performance-analysis`, `e2e-failure-triage` | Find defects, regressions, risks, and missing coverage      |
| `deliver`     | `e2e-release-gate`, `production-readiness`, `deployment-strategy`, `frontend-migration`                                                                           | Prepare release evidence and recommend go/no-go             |

## Standard Feature Flow

```text
planner
  |
  +--> figma + Figma MCP
  |
  +--> peacock + Peacock MCP
  |
  +--> HITL: design approval
  |
  +--> implementer
  |
  +--> review
  |
  +--> HITL: implementation and risk approval
  |
  +--> deliver
  |
  +--> HITL: release approval
```

Use [Frontend Feature Workflow](../workflows/frontend-feature.md) as the default recipe for a new React feature.

## 1. Planning

The planner should use the `frontend-feature` workflow and produce a feature packet containing:

- User outcome
- Acceptance criteria
- Critical states and edge cases
- Affected routes and components
- State ownership
- API assumptions
- Accessibility requirements
- Responsive requirements
- Test scenarios
- Open questions
- Required Figma and Peacock evidence

Recommended planner instruction:

```text
Use the frontend-feature workflow.

Analyze this feature request:
<feature request>

Produce a feature packet containing:
- user outcomes
- acceptance criteria
- edge cases
- affected routes and components
- state ownership
- API dependencies
- accessibility requirements
- responsive requirements
- test scenarios
- questions requiring human clarification

Do not write implementation code.
Do not invent design-system components or tokens.
```

## 2. Figma Evidence

The Figma agent should use the Figma MCP to inspect the source design and return evidence, not only a visual summary.

It should capture:

- Relevant frames and node IDs
- Layout and spacing rules
- Typography and color usage
- Assets
- Responsive behavior
- Interaction states
- Loading, empty, error, disabled, and validation states
- Accessibility concerns
- Differences or unanswered questions

Recommended instruction:

```text
Inspect the referenced Figma design using the Figma MCP.

Return an implementation specification containing:
- relevant frames and node IDs
- layout structure
- spacing and sizing
- typography
- colors and assets
- responsive behavior
- interaction states
- accessibility concerns
- unresolved design questions

Do not create React code.
Do not replace existing Peacock components with custom components without evidence.
```

Figma evidence informs implementation; it does not override product decisions or the existing application architecture.

## 3. Peacock Mapping

The Peacock agent should use the Peacock MCP to map the approved design to the existing design system.

It should identify:

- Existing components
- Variants and supported props
- Design tokens
- Composition patterns
- Accessibility behavior
- Responsive behavior
- Deprecated or unsafe usage
- Missing primitives
- Whether a new design-system contribution is actually required

Recommended instruction:

```text
Use the Peacock design-system MCP to map this feature to existing components and tokens.

Return:
- recommended components
- recommended variants and props
- token mappings
- composition structure
- accessibility behavior
- responsive behavior
- unavailable requirements
- whether a new design-system contribution is required

Prefer existing Peacock primitives.
Flag any mismatch instead of inventing an API.
```

## HITL Gate: Design Approval

Before implementation starts, a product or frontend owner approves:

- Feature scope
- Figma interpretation
- Peacock component mapping
- Acceptance criteria
- Accessibility expectations
- Known design compromises
- API assumptions

Use the repository's [HITL Checkpoint Contract](../workflows/hitl-checkpoint-contract.md). A design approval can be represented as:

```yaml
checkpoint:
  id: feature-design-approval
  trigger: planner, Figma, and Peacock packets are complete
  risk_level: medium
  decision_owner: product-and-frontend-owner
  required_evidence:
    - feature packet
    - Figma implementation specification
    - Peacock component mapping
    - resolved open questions
  proposed_action: begin implementation
  allowed_before_approval:
    - read-only investigation
    - reversible prototype analysis
  prohibited_before_approval:
    - merge implementation
    - publish new design-system APIs
  decision: approve
  rationale: <decision summary>
  conditions: <optional follow-up controls>
  expires_at: <scope or release expiry>
  audit_reference: <ticket or pull request>
  follow_up_owner: <owner>
```

## 4. Implementation

The implementer consumes the approved feature packet and should:

- Follow the existing React architecture
- Use Peacock components and tokens where mapped
- Avoid inventing design-system APIs
- Implement loading, empty, error, disabled, and responsive states
- Add focused tests for the approved acceptance criteria
- Avoid unrelated refactoring

Recommended instruction:

```text
Implement the approved feature packet below.

Constraints:
- Use the existing React architecture.
- Use Peacock components and tokens where mapped.
- Do not invent design-system APIs.
- Preserve existing public behavior.
- Implement loading, empty, error, disabled, and responsive states.
- Add focused tests for the approved acceptance criteria.
- Do not modify unrelated files.

Feature packet:
<approved packet>
```

The implementer should return:

- Files changed
- Behavior implemented
- Tests added and executed
- Peacock components used
- Figma deviations
- Remaining risks
- Manual verification steps

## 5. Review

The review agent should review the implementation against the approved feature packet and use the applicable skills:

```text
code-review
frontend-accessibility-review
frontend-security-review
frontend-performance-analysis
e2e-test-reliability
```

Recommended review focus:

1. Correctness and regressions
2. Accessibility
3. State and interaction completeness
4. Peacock usage
5. Responsive behavior
6. Security
7. Performance
8. Test quality

Return findings ordered by severity with:

- File and symbol
- Problem
- Impact
- Evidence
- Recommended fix
- Whether human approval is required

Review blockers normally include missing keyboard or screen-reader behavior, incorrect design-system usage, missing failure states, unapproved design deviations, new dependencies without approval, security issues, and tests that miss the important user journey.

## HITL Gate: Implementation Approval

Before merge, a human reviewer approves the evidence package:

- Code diff
- Test output
- Accessibility results
- Performance results
- Figma deviations
- Peacock usage
- Generated tests or visual baselines
- Known risks and exceptions

The implementer should not silently approve its own work.

## 6. Delivery

The deliver agent should use:

- `e2e-release-gate`
- `production-readiness`
- `deployment-strategy`

It should verify:

- Critical E2E journeys pass
- Accessibility and security blockers are resolved
- Performance budgets are acceptable
- Monitoring and rollback are available
- Exceptions have an owner and expiry
- Release evidence matches the current commit

Recommended instruction:

```text
Prepare this feature for delivery using e2e-release-gate and production-readiness.

Check:
- critical journey evidence
- test and browser coverage
- accessibility and security findings
- performance evidence
- rollback plan
- monitoring and support readiness
- open risks and exceptions

Return:
- release checklist
- blockers
- conditional approvals
- rollback plan
- recommended go/no-go decision

Do not deploy or promote without explicit release approval.
```

## HITL Gate: Release Approval

The deliver agent prepares evidence but cannot self-approve production promotion. A release owner records one of:

- `go`
- `go with conditions`
- `no-go`

Explicit approval is also required for migration cutovers, accepted high-risk findings, E2E quarantine, weakened release gates, and breaking design-system changes.

## Reusable Task Packet

Pass a structured packet between agents instead of asking every agent to reconstruct context:

```text
Task Packet
- task_id
- request
- user_outcome
- acceptance_criteria
- affected_routes
- affected_components
- Figma_evidence
- Peacock_evidence
- approved_constraints
- test_scenarios
- review_findings
- decisions
- open_risks
- current_status
- next_owner
- audit_reference
```

Keep the packet in the issue, pull request, or task record. Each agent should update it with new evidence and decisions.

## Other Reusable Flows

### Existing React Architecture Review

Use [Frontend Architecture Review](../workflows/frontend-architecture-review.md) to inspect routing, rendering, state ownership, component boundaries, Peacock adoption, accessibility, performance, security, and testing. Return prioritized findings and a remediation roadmap without changing code.

### Design-System Component

Use [Design System Workflow](../workflows/design-system.md). Inspect the intended component with Figma MCP, check Peacock MCP for an existing primitive or variant, and require foundation approval before publishing a shared or breaking component change.

### Bug Fix

Use the smallest applicable workflow. Reproduce the affected journey, implement the smallest fix, add regression coverage, and run review. Do not expand the task into unrelated refactoring.

### Frontend Migration

Use [Frontend Migration Workflow](../workflows/frontend-migration.md). Require a pilot scope, rollback trigger, critical-journey baseline, accessibility and performance comparison, coexistence evidence, and human approval before traffic expansion or final cutover.

## Operating Rules

1. The planner creates context; the implementer consumes approved context.
2. Figma MCP supplies design evidence; it does not determine feature scope.
3. Peacock MCP supplies component and token truth; it does not determine product requirements.
4. Review remains independent from implementation.
5. Deliver prepares release evidence but cannot self-approve production promotion.
6. Every agent returns structured artifacts, not only prose.
7. Parallelize independent work such as Figma inspection, Peacock mapping, accessibility analysis, and API analysis.
8. Keep HITL at decisions rather than every task: design scope, authoritative artifacts, risk acceptance, shared design-system changes, migration cutover, and release.
