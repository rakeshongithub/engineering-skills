# Frontend Performance Analysis

## Purpose

Identify and prioritize frontend performance problems across loading, rendering, interaction, runtime, and network behavior.

## When to Use

- Before launching a high-traffic frontend
- When Core Web Vitals or user-perceived speed regress
- After adding large dependencies, data flows, or visual effects
- When slow devices or networks expose usability problems

## When NOT to Use

- For backend-only latency investigations
- For optimization without a user journey or measurement baseline
- When a production incident requires immediate stabilization first

## Inputs

- Critical user journeys and performance budgets
- Real-user and lab measurements by device and network
- Build output, dependency graph, route behavior, and runtime traces
- Rendering strategy, caching rules, and third-party scripts

## Expected Outputs

- Baseline and bottleneck analysis
- Prioritized improvements with expected impact
- Updated budgets and measurement plan
- Regression checks for build and runtime performance

## Workflow

1. Define user-centered budgets for loading, interaction, layout stability, and resource use.
2. Capture representative lab and real-user baselines across device classes.
3. Analyze critical request chains, bundle composition, caching, and third-party cost.
4. Inspect rendering, hydration, re-rendering, main-thread work, memory, and layout shifts.
5. Prioritize changes by user impact, confidence, effort, and regression risk.
6. Implement one class of improvement at a time and measure again.
7. Add automated budgets, dashboards, and release gates.

## Decision Framework

Optimize the critical user journey first. Prefer reducing work and bytes over adding caching complexity. Treat performance as a product requirement with budgets, ownership, and regression detection rather than a final polish step.

## Quality Checklist

- [ ] Baselines represent real devices, networks, and content
- [ ] Critical loading and interaction paths have explicit budgets
- [ ] Bundle, image, font, third-party, and data costs are measured
- [ ] Layout stability and accessibility are preserved
- [ ] Improvements are verified with before-and-after evidence
- [ ] CI or production monitoring detects regressions

## Common Mistakes

- Optimizing synthetic scores without checking real-user impact
- Measuring only fast developer hardware
- Adding memoization or caching without identifying the bottleneck
- Improving first load while harming interaction or accessibility

## Related Skills

- **Requires:** frontend-architecture, responsive-design
- **Works with:** scalability-analysis, frontend-state-management, frontend-testing-strategy, observability-design
- **Commonly followed by:** production-readiness

## Evaluation Criteria

An analysis is successful when bottlenecks are evidence-based, improvements have measurable targets, and performance budgets protect critical user journeys over time.
