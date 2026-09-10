# Frontend Architecture Examples

## SaaS Dashboard

**Inputs:** SEO is not required, dashboards are personalized, enterprise tenants need strict isolation, and the app must work on slow laptops.

**Application:** Use hybrid rendering for the shell and route-level server data loading. Keep tenant context server-authoritative, put shareable filters in the URL, and keep transient panel state local.

**Output:** Route boundary map, data ownership table, performance budgets, and an ADR explaining the rendering choice.

## Public Commerce Catalog

**Inputs:** Search visibility, fast first load, localized content, and frequent catalog updates.

**Application:** Prefer static or incremental rendering for catalog pages, progressively enhance filtering, and define cache invalidation ownership.

**Output:** Rendering matrix, cache policy, responsive and accessibility constraints, and rollout plan.
