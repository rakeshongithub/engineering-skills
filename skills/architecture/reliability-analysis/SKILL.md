# Reliability Analysis

## Purpose

Analyze system reliability to identify failure modes, assess risks, and design resilience mechanisms to meet availability requirements.

## When to Use

- When defining or validating SLA/SLO targets for a system
- Before launching a new system to production
- When experiencing frequent outages or reliability issues
- During architecture reviews focused on availability and resilience
- When planning disaster recovery or business continuity strategies
- Before making architectural changes that could impact reliability
- When regulatory or business requirements demand high availability

## When NOT to Use

- For performance optimization (use performance-tuning instead)
- For scalability analysis (use scalability-analysis instead)
- For security-focused reviews (use security-architecture-review instead)
- For general architecture review (use architecture-review instead)
- When reliability requirements are not critical (e.g., internal tools, prototypes)

## Inputs

- **architecture**: System architecture diagrams and documentation
- **availability-requirements**: Target SLAs, SLOs, error budgets
- **failure-history**: Past incidents, outages, error logs
- **dependencies**: Internal and external service dependencies
- **constraints**: Budget, technology, regulatory requirements

## Expected Outputs

- **failure-modes**: Identified potential failure scenarios and their impact
- **risk-assessment**: Likelihood and impact analysis for each failure mode
- **mitigation-strategies**: Specific resilience mechanisms to implement
- **sla-analysis**: Feasibility assessment of meeting target SLAs
- **action-plan**: Prioritized roadmap for reliability improvements

## Workflow

### 1. Define Reliability Requirements

**Establish SLA targets:**
- **Availability**: 99%, 99.9%, 99.99%, 99.999% ("five nines")
- **Error rate**: Maximum acceptable error percentage
- **Recovery time**: RTO (Recovery Time Objective)
- **Data loss**: RPO (Recovery Point Objective)

**Calculate error budget:**
- 99% availability = 7.2 hours downtime/month
- 99.9% availability = 43 minutes downtime/month
- 99.99% availability = 4.3 minutes downtime/month
- 99.999% availability = 26 seconds downtime/month

**Understand business impact:**
- Revenue loss per minute of downtime
- Customer impact (users affected, trust erosion)
- Regulatory or compliance implications
- Competitive impact

**Define critical user journeys:**
- Which features must always work?
- Which features can degrade gracefully?
- What is acceptable degraded performance?

### 2. Map System Dependencies

**Identify all components:**
- **Frontend**: Web, mobile, CDN
- **API layer**: Load balancers, API gateways, API servers
- **Application layer**: Microservices, workers, schedulers
- **Data layer**: Databases, caches, message queues, object storage
- **Infrastructure**: Compute, network, DNS, certificates
- **External dependencies**: Third-party APIs, payment processors, auth providers

**Document dependency relationships:**
- Which components depend on which?
- What are the critical paths?
- What are the single points of failure?

**Classify dependencies:**
- **Hard dependencies**: System cannot function without it
- **Soft dependencies**: System can degrade gracefully without it
- **Optional dependencies**: Nice-to-have, not critical

### 3. Identify Failure Modes

**For each component, consider:**

**Infrastructure failures:**
- Server/instance failure
- Network partition or latency
- Disk failure or storage exhaustion
- DNS resolution failure
- Certificate expiration
- Cloud provider outage (entire region)

**Application failures:**
- Application crash or memory leak
- Deadlock or infinite loop
- Resource exhaustion (connections, file descriptors)
- Unhandled exceptions
- Configuration errors
- Deployment failures

**Data failures:**
- Database failure or corruption
- Cache failure or invalidation issues
- Message queue backlog or loss
- Data inconsistency
- Backup failure

**Dependency failures:**
- Third-party API downtime
- Third-party API rate limiting
- Third-party API latency spike
- Authentication service failure
- Payment processor failure

**Human errors:**
- Bad configuration deployment
- Accidental data deletion
- Incorrect scaling decisions
- Security misconfigurations

**External events:**
- DDoS attacks
- Traffic spikes
- Coordinated bot activity
- Natural disasters affecting data centers

### 4. Assess Risk for Each Failure Mode

**Likelihood:**
- **High**: Happens monthly or more frequently
- **Medium**: Happens quarterly or annually
- **Low**: Happens rarely (multi-year)
- **Very Low**: Theoretical but unlikely

**Impact:**
- **Critical**: Complete outage, data loss, security breach
- **High**: Major feature unavailable, significant degradation
- **Medium**: Minor feature unavailable, slight degradation
- **Low**: Minimal impact, most users unaffected

**Risk Matrix:**

| Likelihood \ Impact | Low | Medium | High | Critical |
|---------------------|-----|--------|------|----------|
| **High** | Medium | High | Critical | Critical |
| **Medium** | Low | Medium | High | Critical |
| **Low** | Low | Low | Medium | High |
| **Very Low** | Low | Low | Low | Medium |

**Prioritize based on risk level:**
- **Critical risk**: Must address immediately
- **High risk**: Address in next 1-3 months
- **Medium risk**: Address in next 3-6 months
- **Low risk**: Monitor, address if resources allow

### 5. Design Mitigation Strategies

**Redundancy:**
- **Active-active**: Multiple instances serving traffic simultaneously
- **Active-passive**: Standby instances ready to take over
- **Multi-region**: Deploy across multiple geographic regions
- **Multi-cloud**: Use multiple cloud providers (for critical systems)

**Fault tolerance:**
- **Retries**: Automatic retry with exponential backoff
- **Circuit breakers**: Stop calling failing dependencies
- **Timeouts**: Prevent indefinite waiting
- **Bulkheads**: Isolate resources to prevent cascading failures
- **Rate limiting**: Protect against overload

**Graceful degradation:**
- **Feature flags**: Disable non-critical features during issues
- **Fallbacks**: Serve cached or default data when primary fails
- **Partial responses**: Return what's available, skip failing parts
- **Read-only mode**: Allow reads when writes are unavailable

**Data resilience:**
- **Replication**: Synchronous or asynchronous data replication
- **Backups**: Regular automated backups with tested restore
- **Point-in-time recovery**: Ability to restore to any point in time
- **Multi-region replication**: Protect against regional failures

**Monitoring and alerting:**
- **Health checks**: Automated health monitoring
- **Synthetic monitoring**: Simulate user journeys
- **Error tracking**: Centralized error logging and alerting
- **Anomaly detection**: Detect unusual patterns
- **On-call rotation**: 24/7 incident response

**Chaos engineering:**
- **Failure injection**: Deliberately cause failures to test resilience
- **Game days**: Simulate major incidents
- **Chaos experiments**: Regularly test failure scenarios

### 6. Validate SLA Feasibility

**Calculate composite availability:**

For components in series (all must work):
- Composite availability = A1 × A2 × A3 × ... × An
- Example: 99.9% × 99.9% × 99.9% = 99.7%

For components in parallel (any can work):
- Composite availability = 1 - [(1-A1) × (1-A2) × ... × (1-An)]
- Example: 1 - [(1-0.999) × (1-0.999)] = 99.9999%

**Assess current vs. target:**
- What is current availability?
- What is target availability?
- What is the gap?
- What improvements are needed?

**Identify weakest links:**
- Which components have lowest availability?
- Which dependencies are least reliable?
- Where should improvements focus?

### 7. Develop Action Plan

**For each mitigation strategy:**

**Problem Statement:**
- What failure mode are we addressing?
- What is the current risk level?
- What is the impact if not addressed?

**Recommendation:**
- What specific resilience mechanism?
- How does it mitigate the failure mode?
- What are the alternatives?

**Effort Estimate:**
- Small (days): Configuration changes, monitoring setup
- Medium (weeks): Adding redundancy, implementing retries
- Large (months): Multi-region deployment, major refactoring

**Priority:**
- **Critical**: Addresses critical risk, needed immediately
- **High**: Addresses high risk, needed within 1-3 months
- **Medium**: Addresses medium risk, needed within 3-6 months
- **Low**: Addresses low risk, nice-to-have

**Expected Impact:**
- Availability improvement (e.g., "99.5% → 99.9%")
- Risk reduction (e.g., "Critical → Low")
- MTTR improvement (e.g., "30 min → 5 min")

**Cost Implications:**
- Infrastructure costs (redundancy, multi-region)
- Development effort
- Ongoing operational costs

### 8. Document and Present Findings

**Executive Summary:**
- Current availability vs. target
- Top 3-5 critical risks
- Recommended approach
- Timeline and cost estimate

**Detailed Analysis:**
- Reliability requirements
- Failure mode analysis
- Risk assessment
- Mitigation strategies
- SLA feasibility analysis
- Action plan

**Visual Artifacts:**
- Architecture diagram with failure points highlighted
- Dependency graph
- Risk matrix
- Availability improvement timeline

## Decision Framework

### Choosing Mitigation Strategy

**Use Redundancy when:**
- Failure mode is infrastructure or hardware related
- Need to eliminate single points of failure
- Availability target is 99.9% or higher
- Can afford the cost of duplicate resources

**Use Fault Tolerance when:**
- Dealing with transient failures
- Dependencies are unreliable
- Need to handle partial failures gracefully
- Want to improve resilience without adding redundancy

**Use Graceful Degradation when:**
- Some features are more critical than others
- Want to maintain partial functionality during failures
- User experience can tolerate reduced functionality
- Cost of full redundancy is prohibitive

**Use Multi-region when:**
- Availability target is 99.99% or higher
- Need to protect against regional failures
- Regulatory requirements for data residency
- Global user base requires low latency everywhere

### Prioritizing Improvements

**Critical Priority:**
- Addresses critical risk (high likelihood, high impact)
- Single point of failure with no mitigation
- Required to meet regulatory or contractual SLAs
- High business impact (revenue, reputation)

**High Priority:**
- Addresses high risk
- Significant availability improvement
- Reasonable effort and cost
- Unblocks other improvements

**Medium Priority:**
- Addresses medium risk
- Incremental availability improvement
- Moderate effort and cost
- Nice-to-have but not critical

**Low Priority:**
- Addresses low risk
- Minimal availability improvement
- High effort or cost relative to benefit
- Can be deferred without significant impact

## Quality Checklist

- [ ] Reliability requirements (SLA/SLO) are clearly defined
- [ ] All system components and dependencies are mapped
- [ ] Failure modes are comprehensively identified
- [ ] Risk assessment is based on data (incident history, metrics)
- [ ] Mitigation strategies are specific and actionable
- [ ] SLA feasibility is calculated and validated
- [ ] Effort estimates and priorities are provided
- [ ] Cost implications are considered
- [ ] Action plan includes timeline and milestones
- [ ] Monitoring and alerting strategy is defined
- [ ] Analysis is validated with engineering and operations teams
- [ ] Runbooks and incident response procedures are considered

## Common Mistakes

- **Focusing only on infrastructure**: Ignoring application-level failures
- **No data-driven analysis**: Making assumptions without reviewing incident history
- **Over-engineering**: Building for five nines when 99.9% is sufficient
- **Ignoring dependencies**: Not considering third-party reliability
- **No testing**: Not validating that resilience mechanisms actually work
- **Forgetting human factors**: Not considering operational errors
- **No monitoring**: Implementing resilience without visibility
- **Static analysis**: Not updating analysis as system evolves
- **Ignoring costs**: Recommending expensive solutions without ROI analysis
- **No incident response**: Building resilience but no plan for when failures occur

## Examples

See [examples.md](examples.md) for detailed examples of reliability analysis for various scenarios.

## Related Skills

- **Requires**: 
  - architecture-discovery (to understand current architecture)
- **Commonly followed by**: 
  - architecture-decision (to decide on resilience approach)
  - disaster-recovery-planning (to plan for major failures)
  - incident-response-planning (to prepare for failures)
- **Alternative to**: None (this is the primary reliability analysis skill)
- **Works with**: 
  - architecture-review (for comprehensive architecture assessment)
  - scalability-analysis (to ensure scaling doesn't compromise reliability)
  - security-architecture-review (security and reliability often overlap)
  - monitoring-strategy (to implement observability)

## Skill Composition

Typical workflow:

```
architecture-discovery
        ↓
reliability-analysis
        ↓
architecture-decision
        ↓
disaster-recovery-planning
        ↓
incident-response-planning
```

Alternative workflow for existing systems:

```
architecture-review
        ↓
reliability-analysis
        ↓
architecture-decision
        ↓
implementation
        ↓
chaos-engineering (validation)
```

## Evaluation Criteria

### Completeness
- Are reliability requirements clearly defined?
- Are all components and dependencies analyzed?
- Are failure modes comprehensively identified?
- Is an action plan provided?

### Accuracy
- Is risk assessment based on data?
- Are availability calculations correct?
- Are mitigation strategies appropriate?
- Are effort estimates realistic?

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
