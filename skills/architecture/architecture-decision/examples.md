# Architecture Decision - Examples

## Example 1: Database Selection for New Microservice

### Decision Context
**Question:** Which database should we use for the new user profile service?

**Why it matters:**
- Core service that other services depend on
- Will store millions of user records
- Difficult to change later

**Stakeholders:** Backend team, DevOps, Product

### Decision Criteria

| Criterion | Weight | Why |
|-----------|--------|-----|
| Performance (read-heavy) | High | 90% reads, 10% writes |
| Scalability | High | Must handle 10M+ users |
| Query flexibility | High | Complex user searches |
| Team expertise | Medium | Faster development |
| Cost | Medium | Budget constraints |
| Operational complexity | Low | We have DevOps support |

### Alternatives

**Option 1: PostgreSQL**
- Pros: Team expertise, ACID guarantees, flexible queries, proven at scale
- Cons: Requires read replicas for scale, more operational overhead
- Effort: Low (team knows it well)

**Option 2: MongoDB**
- Pros: Flexible schema, horizontal scaling, good for user profiles
- Cons: Less team expertise, eventual consistency challenges
- Effort: Medium (learning curve)

**Option 3: DynamoDB**
- Pros: Fully managed, excellent scalability, low operational overhead
- Cons: Limited query flexibility, vendor lock-in, learning curve
- Effort: Medium (new technology)

### Evaluation

| Criterion | Weight | PostgreSQL | MongoDB | DynamoDB |
|-----------|--------|------------|---------|----------|
| Performance | High | 8/10 | 9/10 | 9/10 |
| Scalability | High | 7/10 | 9/10 | 10/10 |
| Query flexibility | High | 10/10 | 8/10 | 5/10 |
| Team expertise | Medium | 10/10 | 5/10 | 4/10 |
| Cost | Medium | 7/10 | 7/10 | 8/10 |
| Ops complexity | Low | 6/10 | 6/10 | 10/10 |

### Decision: PostgreSQL

**Rationale:**
- Query flexibility is critical for user search features
- Team expertise reduces risk and speeds development
- Can scale with read replicas and connection pooling
- Proven at scale by many companies

### ADR-015: Use PostgreSQL for User Profile Service

**Status:** Accepted
**Date:** 2026-09-08

**Context:**
We need to choose a database for the new user profile service, which will store user data for 10M+ users and support complex search queries. The service is read-heavy (90% reads) and will be a core dependency for other services.

**Decision:**
We will use PostgreSQL with read replicas for the user profile service.

**Alternatives Considered:**
- **MongoDB**: Good scalability but less query flexibility and team expertise
- **DynamoDB**: Excellent scalability but limited query capabilities

**Consequences:**

*Positive:*
- Leverage team's existing PostgreSQL expertise
- Full SQL query capabilities for complex searches
- ACID guarantees for data consistency
- Proven at scale

*Negative:*
- Need to manage read replicas for scale
- More operational overhead than managed NoSQL
- Schema migrations require planning

**Risks:**
- **Risk:** Performance issues at scale
  - **Mitigation:** Implement read replicas, connection pooling, and caching
- **Risk:** Schema changes become difficult
  - **Mitigation:** Use database migration tools, plan schema carefully

---

## Example 2: Monolith vs. Microservices

### Decision Context
**Question:** Should we build the new platform as a monolith or microservices?

**Why it matters:**
- Foundational architectural decision
- Affects development speed, scalability, and operational complexity
- Difficult to change later

**Stakeholders:** Engineering leadership, Product, DevOps

### Decision Criteria

| Criterion | Weight | Why |
|-----------|--------|-----|
| Time to market | High | Need to launch in 6 months |
| Team size | High | Only 5 engineers |
| Scalability needs | Medium | Moderate growth expected |
| Deployment independence | Medium | Would be nice but not critical |
| Operational complexity | High | Small DevOps team |

### Alternatives

**Option 1: Monolith**
- Pros: Faster development, simpler deployment, easier debugging
- Cons: Harder to scale specific components, all-or-nothing deployment
- Effort: Low

**Option 2: Microservices**
- Pros: Independent scaling, independent deployment, technology flexibility
- Cons: Operational complexity, distributed system challenges, slower initial development
- Effort: High

**Option 3: Modular Monolith**
- Pros: Fast development, clear boundaries, can extract services later
- Cons: Still coupled deployment, requires discipline
- Effort: Low-Medium

### Decision: Modular Monolith

**Rationale:**
- Small team needs to move fast
- Can extract microservices later if needed
- Clear module boundaries enable future migration
- Lower operational complexity

### ADR-001: Start with Modular Monolith

**Status:** Accepted
**Date:** 2026-09-08

**Context:**
We're building a new platform with a team of 5 engineers and need to launch in 6 months. We need to decide between monolith and microservices architecture.

**Decision:**
We will build a modular monolith with clear bounded contexts that can be extracted into microservices later if needed.

**Alternatives Considered:**
- **Pure Monolith**: Faster but harder to evolve
- **Microservices**: Better scalability but too complex for our team size and timeline

**Consequences:**

*Positive:*
- Faster time to market
- Simpler deployment and operations
- Easier debugging and testing
- Lower infrastructure costs
- Clear module boundaries for future extraction

*Negative:*
- Cannot scale modules independently
- All-or-nothing deployment
- Requires discipline to maintain module boundaries

**Risks:**
- **Risk:** Modules become tightly coupled
  - **Mitigation:** Enforce module boundaries, code reviews, architecture reviews
- **Risk:** Difficult to extract services later
  - **Mitigation:** Design with bounded contexts, use interfaces between modules

**Implementation Notes:**
- Use package/module structure to enforce boundaries
- Each module should have clear interfaces
- Avoid shared database access across modules
- Plan for eventual service extraction

---

## Example 3: Authentication Strategy

### Decision Context
**Question:** How should we implement authentication for our SaaS application?

**Why it matters:**
- Security-critical decision
- Affects user experience
- Difficult to change later

**Stakeholders:** Security team, Product, Engineering

### Decision Criteria

| Criterion | Weight | Why |
|-----------|--------|-----|
| Security | High | Critical for SaaS |
| User experience | High | Affects adoption |
| Enterprise features | High | Target market |
| Development effort | Medium | Limited engineering resources |
| Maintenance burden | Medium | Small team |

### Alternatives

**Option 1: Build Custom Auth**
- Pros: Full control, custom features
- Cons: Security risk, high effort, ongoing maintenance
- Effort: High (3-4 months)

**Option 2: Auth0**
- Pros: Fully managed, enterprise features (SSO, MFA), proven security
- Cons: Cost, vendor lock-in
- Effort: Low (1-2 weeks)

**Option 3: AWS Cognito**
- Pros: Low cost, integrates with AWS, managed service
- Cons: Limited customization, less mature than Auth0
- Effort: Low-Medium (2-3 weeks)

### Decision: Auth0

**Rationale:**
- Security is critical, don't want to build custom
- Enterprise features (SSO, MFA) are must-haves
- Fast implementation allows focus on core product
- Cost is acceptable for value provided

### ADR-008: Use Auth0 for Authentication

**Status:** Accepted
**Date:** 2026-09-08

**Context:**
We need to implement authentication for our SaaS application. Our target customers are enterprises that require SSO and MFA. Security is critical, and we have limited engineering resources.

**Decision:**
We will use Auth0 for authentication and authorization.

**Alternatives Considered:**
- **Custom Auth**: Too risky and time-consuming
- **AWS Cognito**: Less mature, missing some enterprise features

**Consequences:**

*Positive:*
- Enterprise features out of the box (SSO, MFA, social login)
- Proven security and compliance (SOC 2, GDPR)
- Fast implementation (1-2 weeks vs. 3-4 months)
- Ongoing security updates managed by Auth0
- Excellent documentation and support

*Negative:*
- Monthly cost ($200-500/month at our scale)
- Vendor lock-in
- Less control over authentication flow
- Customization requires Auth0 rules/hooks

**Risks:**
- **Risk:** Auth0 pricing increases significantly
  - **Mitigation:** Design abstraction layer, monitor usage and costs
- **Risk:** Auth0 outage affects our service
  - **Mitigation:** Monitor Auth0 status, implement graceful degradation

**Implementation Notes:**
- Create abstraction layer for auth to reduce lock-in
- Use Auth0 organizations for multi-tenancy
- Implement Auth0 rules for custom logic
- Set up monitoring for Auth0 API calls