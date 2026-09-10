# Frontend: Accessible Checkout

## Scenario

An e-commerce team is adding a guest checkout flow with address validation, payment-provider fields, and order confirmation.

## Constraints

- Must work by keyboard and with screen readers
- Mobile traffic is 70% of usage
- Payment details must never enter application state or logs
- Validation errors must preserve user input and identify recovery actions
- Checkout must support slow networks and provider timeouts

## Recommended Workflow

Use [frontend-feature.md](../../workflows/frontend-feature.md):

1. `frontend-architecture` defines route, rendering, payment boundary, and data ownership.
2. `component-design` defines form fields, error summaries, dialog behavior, and confirmation states.
3. `responsive-design` defines mobile-first layout and touch behavior.
4. `frontend-state-management` separates form, server, payment, and submission state.
5. Run `frontend-accessibility-review` and `frontend-security-review` before implementation is considered complete.
6. `frontend-testing-strategy` covers keyboard flows, validation, provider failure, and responsive layout.
7. `frontend-performance-analysis` verifies the critical checkout path on a slow mobile network.

## Quality Gates

- Every field has a programmatic label and recoverable error.
- Focus moves to the first actionable error without trapping the user.
- Payment-provider fields are isolated from application state.
- The primary action remains usable at narrow widths and high zoom.
- Loading, timeout, retry, and confirmation states are observable and tested.
