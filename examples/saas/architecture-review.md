# SaaS: Multi-tenant Analytics Architecture Review

## Scenario

A SaaS analytics platform is adding enterprise tenants, but large customers are causing query contention and increasing the risk of cross-tenant data exposure.

## Context and Evidence

- Web API, query service, ingestion workers, PostgreSQL, and a columnar warehouse
- Shared database with tenant identifiers on application tables
- 2,000 tenants; the largest tenant generates 35% of query workload
- p95 dashboard latency increased from 2 seconds to 9 seconds
- A recent incident found missing tenant filters in an internal reporting query
- Enterprise customers require tenant isolation, audit logs, and 99.9% availability

## Recommended Workflow

Use [architecture-review.md](../../workflows/architecture-review.md):

1. `architecture-discovery` verifies runtime paths, ownership, tenant boundaries, and query behavior.
2. `architecture-review` assesses coupling, isolation, operability, and fitness for enterprise scale.
3. Run `scalability-analysis`, `reliability-analysis`, `security-architecture-review`, and `data-architecture-review` in parallel.
4. `api-design-review` checks pagination, query limits, authorization, and export behavior.
5. `technical-debt-analysis` quantifies unsafe query patterns and missing platform controls.
6. `tradeoff-analysis` compares shared, partitioned, and dedicated tenant storage.
7. `architecture-decision` records the target isolation and scaling strategy.

## Findings to Validate

- Tenant authorization should be enforced at a policy boundary, not only by query authors.
- Workload isolation may require queue quotas, warehouse resource groups, or dedicated capacity for high-volume tenants.
- Query budgets and maximum export sizes should be explicit API contracts.
- Tenant-filter tests and policy-aware query tests should run against representative schemas.

## Expected Outputs

- Evidence-backed current-state architecture
- Prioritized security, scalability, reliability, and data findings
- Tenant isolation options with cost and migration consequences
- Target-state decision record and remediation roadmap
- Metrics for p95 latency, noisy-neighbor impact, authorization failures, and export volume

## Quality Gates

- Cross-tenant access is denied and tested at every data access path.
- Noisy-neighbor controls have measurable thresholds and owner escalation.
- Capacity assumptions are tied to tenant growth and workload distributions.
- The roadmap separates immediate containment from durable architecture changes.
