# Frontend Feature Workflow

## Purpose

Deliver a frontend feature with clear architecture, resilient interaction states, accessibility, security, test coverage, and measurable performance.

## Inputs

- User journeys and acceptance criteria
- Designs, content rules, and supported browsers/devices
- Existing frontend architecture and API contracts
- Accessibility, security, performance, and release constraints

## Workflow

1. `requirements-analysis` defines user outcomes, edge cases, and acceptance criteria.
2. `frontend-architecture` defines route, rendering, module, data, and ownership boundaries.
3. `component-design` defines reusable components, states, and interaction contracts.
4. `responsive-design` defines layout and interaction behavior across viewports and input methods.
5. `frontend-state-management` defines server, URL, form, local, and shared state ownership.
6. Run `frontend-accessibility-review` and `frontend-security-review` in parallel after the design stabilizes.
7. `frontend-testing-strategy` maps risks to component, integration, visual, accessibility, and end-to-end tests.
8. `e2e-test-design` selects the critical browser journeys and failure cases.
9. `e2e-test-automation` implements the approved journeys with deterministic fixtures.
10. `e2e-test-reliability` and `e2e-release-gating` validate signal and CI blocking policy.
11. `frontend-performance-analysis` validates budgets and critical user journeys.
12. `production-readiness` confirms rollout, monitoring, rollback, and support readiness.

## Quality Gates

- Critical journeys and all meaningful states have acceptance criteria.
- Keyboard, responsive, security, and failure behavior are specified before implementation.
- Tests cover user-visible behavior and high-risk integrations.
- Performance budgets and release evidence are explicit.

## HITL Checkpoints

- **Design approval:** The product and frontend owners approve the interaction states, accessibility expectations, and acceptance criteria before implementation.
- **Artifact review:** A qualified reviewer approves generated tests, visual baselines, or other authoritative artifacts before merge.
- **Release decision:** The release owner approves production promotion through `production-readiness`.

## Outputs

- Frontend design and component contracts
- State and responsive behavior model
- Accessibility and security findings
- Test plan, performance baseline, and production launch plan
