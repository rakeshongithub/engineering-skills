# Frontend Architecture

## Purpose

Define a maintainable frontend architecture covering rendering, routing, module boundaries, data ownership, and delivery constraints.

## When to Use

- Starting a web application or major frontend surface
- Choosing between client, server, static, or hybrid rendering
- Restructuring a frontend with growing coupling
- Introducing a new platform, framework, or deployment model

## When NOT to Use

- For a single isolated component or styling change
- For visual design critique without implementation concerns
- For backend architecture decisions that do not affect the client

## Inputs

- User journeys and product requirements
- Browser, device, SEO, accessibility, and performance targets
- Existing frontend code, dependencies, APIs, and deployment model
- Team ownership, release constraints, and observability needs

## Expected Outputs

- Frontend context and container/module boundaries
- Rendering, routing, data, and state strategy
- Technology decisions with tradeoffs
- Quality budgets and migration or implementation plan

## Workflow

1. Establish user journeys, constraints, browser support, and quality targets.
2. Map the current or proposed application shell, routes, modules, and integration points.
3. Decide rendering and delivery strategy for each route or surface.
4. Assign ownership for server data, URL state, local UI state, and shared client state.
5. Define component, feature, and platform boundaries with dependency direction.
6. Validate accessibility, security, performance, and testing implications.
7. Record consequential choices in an ADR and publish the implementation plan.

## Decision Framework

Prefer the simplest rendering model that meets SEO, latency, personalization, and hosting requirements. Keep state close to its owner, make server data distinct from UI state, and use explicit boundaries where independent release or ownership justifies them.

## Quality Checklist

- [ ] Routes, modules, dependencies, and ownership are explicit
- [ ] Rendering strategy is justified per user journey
- [ ] Loading, error, empty, and offline states are designed
- [ ] Accessibility, security, performance, and testing constraints are measurable
- [ ] Migration and rollback paths exist for architecture changes

## Common Mistakes

- Choosing a framework before clarifying user and delivery constraints
- Treating every state value as global state
- Ignoring browser and accessibility requirements until implementation
- Creating micro-frontends without independent ownership or release needs

## Related Skills

- **Requires:** requirements-analysis, system-design
- **Works with:** component-design, responsive-design, frontend-state-management, frontend-testing-strategy
- **Commonly followed by:** architecture-decision, technical-design-document

## Evaluation Criteria

A successful architecture makes ownership and tradeoffs clear, supports the required user journeys, and gives implementation teams testable boundaries and measurable quality targets.
