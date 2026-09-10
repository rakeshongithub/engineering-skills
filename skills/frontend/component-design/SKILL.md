# Component Design

## Purpose

Design reusable frontend components with clear responsibilities, states, accessibility behavior, and stable APIs.

## When to Use

- Creating a shared component or feature surface
- Extracting repeated UI safely
- Defining component-library contribution standards
- Reviewing components for reuse, state ownership, and interaction quality

## When NOT to Use

- For page-level architecture or routing decisions
- For purely visual mockup feedback without implementation context
- When a one-off local element has no meaningful reuse boundary

## Inputs

- User journeys and interaction requirements
- Design references, content rules, and responsive constraints
- Existing component library and coding conventions
- Accessibility, testing, and browser-support requirements

## Expected Outputs

- Component responsibility and composition map
- Typed props, events, slots, and state boundaries
- State and interaction model, including failure states
- Accessibility and test requirements

## Workflow

1. Identify the user task and the smallest useful component boundary.
2. Separate content, layout, behavior, and data responsibilities.
3. Define the public API and keep implementation details private.
4. Enumerate default, loading, empty, error, disabled, focus, hover, and responsive states.
5. Define semantic HTML, keyboard behavior, focus movement, and accessible names.
6. Validate composition, reuse, testability, and visual consistency.
7. Document examples and known non-goals before implementation.

## Decision Framework

Prefer composition over configuration-heavy components. Keep domain decisions outside generic primitives, use controlled state when the parent owns the workflow, and use uncontrolled state only when local behavior is truly self-contained.

## Quality Checklist

- [ ] Responsibility and non-goals are clear
- [ ] API is minimal, typed, and composable
- [ ] All meaningful states are specified
- [ ] Keyboard and semantic behavior are defined
- [ ] Component can be tested without implementation-detail coupling
- [ ] Responsive content and long text do not break layout

## Common Mistakes

- Building a generic component before identifying a real reuse need
- Hiding important state transitions inside a visual primitive
- Treating hover as the only feedback state
- Omitting error, empty, loading, or disabled behavior

## Related Skills

- **Requires:** frontend-architecture, responsive-design
- **Works with:** frontend-accessibility-review, frontend-state-management, frontend-testing-strategy
- **Commonly followed by:** design-system-engineering, frontend-feature workflow

## Evaluation Criteria

A component is successful when consumers understand its API, users can operate every state accessibly, and the component remains reusable without accumulating product-specific behavior.
