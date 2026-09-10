# Frontend: Analytics Dashboard Performance

## Scenario

A multi-tenant analytics dashboard has slow first load and delayed chart interactions for large customers on mid-range laptops.

## Evidence

- Initial JavaScript payload is 2.4 MB compressed
- Main-thread work blocks interaction for 1.8 seconds after navigation
- The largest tenant loads 40 charts and 100,000 table rows
- Real-user p95 interaction latency is 1.9 seconds
- Mobile users need summary metrics but not every desktop visualization

## Recommended Workflow

Use [frontend-architecture-review.md](../../workflows/frontend-architecture-review.md):

1. `architecture-discovery` maps routes, chart modules, data requests, and ownership.
2. `frontend-performance-analysis` establishes route, interaction, memory, and layout budgets.
3. `frontend-state-management` separates URL filters, server query cache, and local panel state.
4. `responsive-design` prioritizes summary metrics and defines narrow-screen chart behavior.
5. `frontend-testing-strategy` adds performance, visual, responsive, and critical interaction regression cases.
6. `frontend-architecture` evaluates route-level code splitting and server/client rendering boundaries.

## Likely Actions

Split chart modules by route, virtualize large tables, defer below-the-fold visualizations, request aggregated data, reserve chart dimensions, and add real-user performance dashboards.

## Quality Gates

- The critical dashboard task meets an agreed interaction budget on target hardware.
- Large tenants cannot create unbounded browser memory or main-thread work.
- Mobile users can access the primary insight without loading desktop-only features.
- Before-and-after measurements show improvement without accessibility regression.
