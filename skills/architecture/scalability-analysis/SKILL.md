# Scalability Analysis

## Purpose

Analyze system scalability to identify bottlenecks, capacity limits, and design solutions for handling growth.

## When to Use

- When planning for significant user or data growth (2x-10x or more)
- Before major product launches or marketing campaigns
- When current system is showing performance degradation under load
- During architecture reviews focused on growth readiness
- When evaluating whether current architecture can meet future scale requirements
- Before making infrastructure investment decisions
- When designing new systems that need to scale

## When NOT to Use

- For systems with stable, predictable load that won't grow significantly
- For performance optimization of existing load (use performance-tuning instead)
- For reliability analysis (use reliability-analysis instead)
- For cost optimization without growth concerns (use cost-optimization instead)
- When you need general architecture review (use architecture-review instead)

## Inputs

- **architecture**: Current system architecture diagrams and documentation
- **current-load**: Current traffic patterns, data volumes, transaction rates
- **expected-growth**: Projected growth in users, data, transactions over time
- **performance-metrics**: Current performance data (latency, throughput, resource utilization)
- **constraints**: Budget, timeline, technology constraints

## Expected Outputs

- **bottlenecks**: Identified components that will limit scalability
- **capacity-analysis**: Current capacity vs. projected needs for each component
- **scaling-strategy**: Recommended approach (horizontal, vertical, functional decomposition)
- **recommendations**: Specific actions to improve scalability with effort estimates
- **capacity-plan**: Timeline and milestones for scaling improvements

## Workflow

### 1. Understand Growth Requirements

**Define growth metrics:**
- **Users**: Current active users → projected users (timeframe)
- **Data volume**: Current storage → projected storage
- **Transaction rate**: Current requests/sec → projected requests/sec
- **Geographic distribution**: Current regions → planned expansion

**Identify growth drivers:**
- New features or products
- Marketing campaigns
- Business expansion
- Seasonal patterns
- Viral growth potential

**Set target timeframes:**
- 3 months
- 6 months
- 1 year
- 2-3 years

### 2. Analyze Current Architecture

**Map system components:**
- **Frontend**: Web, mobile, CDN
- **API layer**: Load balancers, API servers
- **Application layer**: Business logic services
- **Data layer**: Databases, caches, message queues
- **Background jobs**: Workers, schedulers
- **External dependencies**: Third-party APIs, services

**Document current capacity:**
- Requests per second each component can handle
- Data storage capacity
- Network bandwidth
- CPU and memory resources

**Identify current bottlenecks:**
- Components at >70% capacity
- Single points of failure
- Components that cannot scale horizontally
- Expensive operations (N+1 queries, large data transfers)

### 3. Perform Load Analysis

**Analyze traffic patterns:**
- Peak vs. average load
- Daily/weekly/seasonal patterns
- Geographic distribution
- User behavior patterns

**Calculate capacity requirements:**
- Peak load × growth factor × safety margin (typically 1.5-2x)
- Example: 1000 req/s peak × 10x growth × 1.5 safety = 15,000 req/s needed

**Identify scaling dimensions:**
- **Compute**: CPU-bound operations
- **Memory**: In-memory caching, session storage
- **Storage**: Database, file storage
- **Network**: Bandwidth, latency
- **I/O**: Disk operations, database queries

### 4. Identify Bottlenecks

**For each component, assess:**

**Database:**
- Can it handle projected query load?
- Is storage capacity sufficient?
- Are there slow queries that will worsen with data growth?
- Can it scale horizontally (sharding, read replicas)?

**Application servers:**
- Can they scale horizontally?
- Are there stateful components preventing scaling?
- Are there memory leaks or resource exhaustion issues?

**Caching:**
- Is cache hit rate acceptable?
- Will cache be effective at higher scale?
- Is cache invalidation strategy sound?

**Message queues:**
- Can they handle projected message volume?
- Are there queue depth issues?
- Is processing keeping up with production?

**External dependencies:**
- Do third-party APIs have rate limits?
- Will external services scale with your growth?
- Are there SLAs that might be violated?

### 5. Design Scaling Strategy

**Horizontal Scaling (Scale Out):**
- Add more instances of stateless components
- Implement load balancing
- Use auto-scaling based on metrics
- **Best for**: Stateless services, read-heavy workloads

**Vertical Scaling (Scale Up):**
- Increase resources (CPU, memory) of existing instances
- Upgrade to larger instance types
- **Best for**: Databases, stateful components (short-term solution)

**Functional Decomposition:**
- Split monolith into microservices
- Separate read and write paths (CQRS)
- Extract high-traffic features into dedicated services
- **Best for**: Complex systems with different scaling needs per feature

**Data Scaling:**
- **Read scaling**: Read replicas, caching, CDN
- **Write scaling**: Sharding, partitioning, write-through caching
- **Storage scaling**: Object storage, data archival, compression

**Caching Strategy:**
- **Application-level**: In-memory caches (Redis, Memcached)
- **Database-level**: Query result caching
- **CDN**: Static assets, API responses
- **Client-side**: Browser caching, service workers

### 6. Develop Recommendations

**For each bottleneck:**

**Problem Statement:**
- What is the bottleneck?
- At what scale will it become critical?
- What is the impact if not addressed?

**Recommendation:**
- What specific action should be taken?
- Why is this the right approach?
- What are the alternatives?

**Effort Estimate:**
- Small (days): Configuration changes, adding instances
- Medium (weeks): Adding caching, read replicas, optimization
- Large (months): Sharding, microservices extraction, major refactoring

**Priority:**
- **Critical**: Will block growth within 3 months
- **High**: Needed within 6 months
- **Medium**: Needed within 1 year
- **Low**: Future-proofing beyond 1 year

**Expected Impact:**
- Capacity increase (e.g., "Handles 10x current load")
- Performance improvement (e.g., "Reduces latency by 50%")
- Cost implications (increase/decrease)

### 7. Create Capacity Plan

**Immediate (0-3 months):**
- Address critical bottlenecks
- Quick wins (caching, indexing, configuration)
- Monitoring and alerting improvements

**Short-term (3-6 months):**
- Horizontal scaling implementation
- Database optimization and read replicas
- Caching layer enhancements

**Long-term (6-12 months):**
- Architectural changes (sharding, microservices)
- Geographic distribution
- Advanced scaling patterns

**Monitoring and validation:**
- Define metrics to track
- Set up capacity alerts
- Plan load testing schedule
- Establish review cadence

## Decision Framework

### Choosing Scaling Strategy

**Use Horizontal Scaling when:**
- Components are stateless or can be made stateless
- Need high availability and fault tolerance
- Growth is unpredictable (can auto-scale)
- Cost efficiency is important (scale down when not needed)

**Use Vertical Scaling when:**
- Component is inherently stateful (e.g., database)
- Quick short-term solution needed
- Horizontal scaling requires significant refactoring
- Licensing costs favor fewer, larger instances

**Use Functional Decomposition when:**
- Different features have vastly different scaling needs
- Monolith is becoming unmanageable
- Team structure supports microservices
- Need to scale development teams

### Prioritizing Bottlenecks

**Critical Priority:**
- Will prevent growth within 3 months
- Single point of failure at scale
- No workaround available
- High business impact

**High Priority:**
- Will prevent growth within 6 months
- Significant performance degradation
- Affects user experience
- Moderate effort to fix

**Medium Priority:**
- Will prevent growth within 1 year
- Manageable with workarounds
- Incremental improvement
- High effort to fix

**Low Priority:**
- Future-proofing beyond 1 year
- Nice-to-have improvements
- Can be deferred without significant risk

## Quality Checklist

- [ ] Growth requirements are clearly defined with specific metrics and timeframes
- [ ] All major system components are analyzed for scalability
- [ ] Current capacity is measured and documented
- [ ] Bottlenecks are identified with supporting data
- [ ] Capacity calculations account for peak load and safety margins
- [ ] Scaling strategy is appropriate for each component
- [ ] Recommendations are specific and actionable
- [ ] Effort estimates and priorities are provided
- [ ] Cost implications are considered
- [ ] Capacity plan includes timeline and milestones
- [ ] Monitoring and validation approach is defined
- [ ] Analysis is validated with engineering team

## Common Mistakes

- **Optimizing prematurely**: Scaling before understanding actual bottlenecks
- **Ignoring data growth**: Focusing only on traffic, not data volume
- **Underestimating safety margins**: Planning for average load instead of peak
- **Over-engineering**: Building for 100x scale when 10x is the realistic need
- **Ignoring costs**: Recommending expensive solutions without ROI analysis
- **Forgetting monitoring**: Not setting up metrics to validate scaling improvements
- **Single dimension focus**: Only scaling compute while ignoring database or network
- **No load testing**: Making assumptions without validating under realistic load
- **Ignoring external dependencies**: Assuming third-party services will scale with you
- **No incremental plan**: Trying to solve all scaling issues at once

## Examples

See [examples.md](examples.md) for detailed examples of scalability analysis for various scenarios.

## Related Skills

- **Requires**: 
  - architecture-discovery (to understand current architecture)
- **Commonly followed by**: 
  - architecture-decision (to decide on scaling approach)
  - capacity-planning (to plan infrastructure)
  - performance-optimization (to optimize before scaling)
- **Alternative to**: None (this is the primary scalability analysis skill)
- **Works with**: 
  - architecture-review (for comprehensive architecture assessment)
  - reliability-analysis (to ensure scaling doesn't compromise reliability)
  - cost-optimization (to balance scaling with cost efficiency)
  - load-testing (to validate scaling assumptions)

## Skill Composition

Typical workflow:

```
architecture-discovery
        ↓
scalability-analysis
        ↓
architecture-decision
        ↓
capacity-planning
        ↓
load-testing (validation)
```

Alternative workflow for existing systems:

```
architecture-review
        ↓
scalability-analysis
        ↓
performance-optimization
        ↓
architecture-decision
```

## Evaluation Criteria

### Completeness
- Are growth requirements clearly defined?
- Are all major components analyzed?
- Are bottlenecks identified with supporting data?
- Is a capacity plan provided?

### Accuracy
- Are capacity calculations realistic?
- Are bottlenecks correctly identified?
- Are effort estimates reasonable?
- Are scaling strategies appropriate?

### Actionability
- Are recommendations specific and clear?
- Are priorities well-defined?
- Can the team act on the recommendations?
- Is there a clear roadmap?

### Feasibility
- Are recommendations realistic given constraints?
- Are cost implications considered?
- Is the timeline achievable?
- Does the team have necessary expertise?
