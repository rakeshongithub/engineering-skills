# Performance Journey Testing

## Purpose

Measure end-to-end user journey performance under representative devices, networks, data volumes, and system load.

## When to Use

- Defining performance budgets for critical user journeys
- Validating frontend and backend changes together
- Testing slow devices, networks, large tenants, or high concurrency
- Preventing regressions in load, interaction, and completion time

## When NOT to Use

- For isolated backend load testing without user journeys
- For optimizing a metric without a baseline or user outcome
- During active incidents where stabilization comes first

## Inputs

- Critical journeys and user-centered performance goals
- Device, network, data, traffic, and concurrency profiles
- Frontend performance budgets and backend service targets
- Observability, tracing, and test-environment capabilities

## Expected Outputs

- Journey performance model and workload profiles
- Baseline and percentile measurements
- Bottleneck and budget analysis
- Regression thresholds and monitoring plan

## Workflow

1. Select journeys by business impact, frequency, and performance sensitivity.
2. Define realistic personas, data volumes, devices, networks, and concurrency.
3. Instrument journey start, meaningful milestones, completion, errors, and resource use.
4. Capture baseline p50, p75, p95, tail, and failure measurements.
5. Run normal, slow-device, degraded-network, peak, and recovery scenarios.
6. Correlate browser, API, database, and infrastructure evidence.
7. Set budgets and regression gates tied to user outcomes.
8. Re-measure after changes and document tradeoffs.

## Decision Framework

Prioritize the slowest meaningful user outcome, not the largest raw metric. Separate load capacity, page performance, interaction latency, and journey completion reliability. Never improve one milestone by silently degrading completion or accessibility.

## Quality Checklist

- [ ] Workloads represent real users and data distributions
- [ ] Journey milestones and completion are measurable
- [ ] Tail latency and failure rate are included
- [ ] Device and network constraints are represented
- [ ] Cross-layer traces support diagnosis
- [ ] Budgets protect user outcomes over time

## Common Mistakes

- Testing only synthetic fast hardware
- Measuring page load without task completion
- Using unrealistic empty datasets
- Treating average latency as the performance contract

## Related Skills

- **Requires:** e2e-test-design, frontend-performance-analysis
- **Works with:** scalability-analysis, observability-design, e2e-test-environment
- **Commonly followed by:** e2e-release-gating

## Evaluation Criteria

A performance journey program is successful when it predicts user experience under representative conditions and catches regressions with actionable evidence.
