# Production Readiness

## Purpose

Assess whether a system is ready for production deployment across operational, security, and reliability dimensions.

## When to Use

- Before launching a new system or major feature to production
- Before a major product launch or marketing campaign
- When migrating from beta to general availability (GA)
- Before scaling to a new order of magnitude of users or data
- When onboarding a new enterprise customer with strict SLAs
- After major architecture changes or migrations
- As part of a pre-deployment checklist for critical releases
- When preparing for a technical audit or compliance review

## When NOT to Use

- For development or staging environment deployments
- For minor feature releases with low risk
- For internal tools with no SLA requirements
- When you need architecture review (use architecture-review instead)
- When you need security-specific review (use security-architecture-review instead)
- For post-deployment monitoring (use production-monitoring instead)

## Inputs

- **system**: System architecture, codebase, infrastructure configuration
- **requirements**: Functional and non-functional requirements
- **sla-targets**: Target SLAs for availability, performance, reliability
- **operational-requirements**: Monitoring, alerting, on-call, runbooks
- **deployment-plan**: Deployment strategy, rollback procedures

## Expected Outputs

- **readiness-assessment**: Overall readiness score and summary
- **gaps**: List of gaps categorized by severity and area
- **go-no-go-decision**: Clear recommendation on whether to proceed with deployment
- **action-items**: Prioritized list of items to address before or after launch
- **launch-checklist**: Final checklist to verify before deployment

## Workflow

### 1. Understand Requirements and Context

**Define success criteria:**
- What are the SLA targets? (availability, latency, throughput)
- What is the expected load? (users, requests, data volume)
- What is the business impact of downtime?
- What are the compliance requirements?
- What is the acceptable risk level?

**Understand the system:**
- What is the architecture?
- What are the critical components?
- What are the dependencies?
- What is the deployment model?

**Identify stakeholders:**
- Who is responsible for operations?
- Who is on-call?
- Who are the escalation contacts?
- Who approves the launch?

### 2. Assess Operational Readiness

#### Monitoring and Observability

**Metrics:**
- [ ] Golden signals monitored (latency, traffic, errors, saturation)
- [ ] Business metrics tracked (signups, conversions, revenue)
- [ ] Infrastructure metrics collected (CPU, memory, disk, network)
- [ ] Custom application metrics instrumented
- [ ] Metrics retention policy defined

**Logging:**
- [ ] Structured logging implemented
- [ ] Log levels used appropriately (DEBUG, INFO, WARN, ERROR)
- [ ] Logs centralized and searchable
- [ ] Log retention policy defined
- [ ] PII not logged or properly masked

**Tracing:**
- [ ] Distributed tracing implemented (for microservices)
- [ ] Request IDs propagated across services
- [ ] Critical paths traced
- [ ] Trace sampling configured appropriately

**Dashboards:**
- [ ] System health dashboard created
- [ ] Service-level dashboards created
- [ ] Dashboards accessible to on-call engineers
- [ ] Dashboards include key SLIs (Service Level Indicators)

#### Alerting

**Alert Coverage:**
- [ ] Alerts for SLA violations (availability, latency)
- [ ] Alerts for error rate spikes
- [ ] Alerts for resource exhaustion (CPU, memory, disk)
- [ ] Alerts for dependency failures
- [ ] Alerts for security events

**Alert Quality:**
- [ ] Alerts are actionable (clear what to do)
- [ ] Alerts have appropriate severity levels
- [ ] Alerts have runbook links
- [ ] Alert fatigue minimized (no noisy alerts)
- [ ] Alerts tested and validated

**Alert Routing:**
- [ ] Alerts routed to on-call engineer
- [ ] Escalation policy defined
- [ ] Alert channels configured (PagerDuty, Slack, email)
- [ ] Alert acknowledgment and resolution tracked

#### Runbooks and Documentation

**Runbooks:**
- [ ] Runbooks created for common incidents
- [ ] Runbooks include symptoms, diagnosis, remediation
- [ ] Runbooks tested by team members
- [ ] Runbooks easily accessible (wiki, docs site)

**Documentation:**
- [ ] Architecture diagrams up-to-date
- [ ] Deployment procedures documented
- [ ] Rollback procedures documented
- [ ] Disaster recovery plan documented
- [ ] On-call rotation and escalation documented

#### On-Call and Incident Response

**On-Call:**
- [ ] On-call rotation established
- [ ] On-call engineers trained
- [ ] On-call schedule published
- [ ] On-call handoff process defined

**Incident Response:**
- [ ] Incident response process defined
- [ ] Incident severity levels defined
- [ ] Communication channels established (status page, Slack)
- [ ] Post-mortem process defined
- [ ] Incident commander role defined

### 3. Assess Reliability and Resilience

#### High Availability

**Redundancy:**
- [ ] No single points of failure
- [ ] Critical components have redundancy
- [ ] Multi-AZ or multi-region deployment (if required)
- [ ] Load balancing configured
- [ ] Auto-scaling configured

**Failure Handling:**
- [ ] Graceful degradation implemented
- [ ] Circuit breakers implemented
- [ ] Retries with exponential backoff
- [ ] Timeouts configured appropriately
- [ ] Bulkheads to isolate failures

**Data Durability:**
- [ ] Database backups automated
- [ ] Backup retention policy defined
- [ ] Backup restoration tested
- [ ] Point-in-time recovery available (if required)
- [ ] Data replication configured

#### Disaster Recovery

**Recovery Objectives:**
- [ ] RTO (Recovery Time Objective) defined
- [ ] RPO (Recovery Point Objective) defined
- [ ] DR plan documented
- [ ] DR plan tested

**Backup and Restore:**
- [ ] Backups automated and monitored
- [ ] Backups stored in separate region/account
- [ ] Restore procedures documented and tested
- [ ] Backup encryption enabled

**Failover:**
- [ ] Failover procedures documented
- [ ] Failover tested (if applicable)
- [ ] DNS failover configured (if multi-region)
- [ ] Data synchronization verified

### 4. Assess Performance and Scalability

#### Performance

**Benchmarking:**
- [ ] Performance benchmarks established
- [ ] Latency targets defined (p50, p95, p99)
- [ ] Throughput targets defined
- [ ] Performance tested under expected load

**Optimization:**
- [ ] Database queries optimized
- [ ] Indexes created for common queries
- [ ] Caching implemented where appropriate
- [ ] CDN configured for static assets
- [ ] API response times acceptable

#### Scalability

**Horizontal Scaling:**
- [ ] Stateless components can scale horizontally
- [ ] Auto-scaling policies configured
- [ ] Load testing performed
- [ ] Scaling limits identified and documented

**Capacity Planning:**
- [ ] Current capacity measured
- [ ] Expected growth projected
- [ ] Capacity headroom sufficient (typically 2x expected peak)
- [ ] Scaling triggers defined

### 5. Assess Security

**Authentication and Authorization:**
- [ ] Authentication implemented and tested
- [ ] Authorization checks at every layer
- [ ] Principle of least privilege followed
- [ ] Secrets managed securely (not in code)

**Data Protection:**
- [ ] Data encrypted in transit (TLS 1.2+)
- [ ] Data encrypted at rest (if required)
- [ ] PII handling compliant with regulations
- [ ] Data retention and deletion policies implemented

**Network Security:**
- [ ] Firewall rules configured
- [ ] Security groups follow least privilege
- [ ] DDoS protection enabled (if public-facing)
- [ ] WAF configured (if applicable)

**Vulnerability Management:**
- [ ] Dependencies scanned for vulnerabilities
- [ ] Security patches applied
- [ ] Penetration testing performed (if required)
- [ ] Security review completed

### 6. Assess Deployment and Rollback

#### Deployment

**Deployment Strategy:**
- [ ] Deployment strategy defined (blue-green, canary, rolling)
- [ ] Deployment automated (CI/CD)
- [ ] Deployment tested in staging
- [ ] Deployment rollback plan defined

**Pre-Deployment:**
- [ ] Database migrations tested
- [ ] Feature flags configured
- [ ] Configuration validated
- [ ] Dependencies verified

**Post-Deployment:**
- [ ] Smoke tests automated
- [ ] Health checks configured
- [ ] Monitoring verified
- [ ] Rollback criteria defined

#### Rollback

**Rollback Procedures:**
- [ ] Rollback procedures documented
- [ ] Rollback tested
- [ ] Rollback time acceptable (typically < 15 minutes)
- [ ] Database rollback strategy defined (if schema changes)

### 7. Assess Compliance and Governance

**Compliance:**
- [ ] Compliance requirements identified (GDPR, HIPAA, SOC 2, etc.)
- [ ] Compliance controls implemented
- [ ] Audit logging enabled
- [ ] Data residency requirements met

**Change Management:**
- [ ] Change approval process followed
- [ ] Stakeholders notified
- [ ] Maintenance window scheduled (if required)
- [ ] Communication plan defined

**Cost Management:**
- [ ] Cost estimates calculated
- [ ] Cost monitoring configured
- [ ] Cost alerts set up
- [ ] Cost optimization opportunities identified

### 8. Make Go/No-Go Decision

**Categorize gaps:**

**Blockers (Must fix before launch):**
- Critical security vulnerabilities
- No monitoring or alerting
- No rollback capability
- SLA targets cannot be met
- Compliance violations

**High Priority (Should fix before launch):**
- Missing runbooks for critical scenarios
- Insufficient redundancy
- Performance issues under load
- Missing backups or DR plan

**Medium Priority (Can launch with plan to fix):**
- Incomplete documentation
- Missing non-critical alerts
- Performance optimization opportunities
- Cost optimization opportunities

**Low Priority (Nice to have):**
- Additional dashboards
- Enhanced logging
- Future scalability improvements

**Decision Framework:**

**GO:**
- No blockers
- High-priority items have mitigation plans
- Team confident in launch
- Stakeholders aligned

**NO-GO:**
- Any blockers present
- High-priority items without mitigation
- Team not confident
- Stakeholders not aligned

**CONDITIONAL GO:**
- Launch to limited audience (beta, canary)
- Launch with increased monitoring
- Launch with on-call coverage
- Launch with rollback plan ready

## Decision Framework

### Gap Severity

**Blocker:**
- Prevents meeting SLA commitments
- Creates security or compliance risk
- No way to detect or respond to incidents
- No way to rollback if issues occur

**High:**
- Significantly increases risk of incidents
- Makes incident response difficult
- Violates best practices
- Impacts user experience

**Medium:**
- Increases operational burden
- Missing nice-to-have capabilities
- Suboptimal but functional

**Low:**
- Future improvements
- Optimization opportunities
- Documentation gaps

### Launch Strategy

**Full Launch:**
- All critical gaps addressed
- High confidence in system
- Low-risk deployment

**Phased Launch:**
- Some high-priority gaps remain
- Launch to subset of users (10%, 50%)
- Monitor closely before full rollout

**Beta Launch:**
- Medium-priority gaps remain
- Launch to opt-in users
- Set expectations for stability

**Delay Launch:**
- Blockers present
- High-risk deployment
- Team not ready

## Quality Checklist

- [ ] All critical operational capabilities assessed
- [ ] Monitoring, logging, and alerting verified
- [ ] Runbooks and documentation reviewed
- [ ] On-call and incident response readiness confirmed
- [ ] Reliability and resilience validated
- [ ] Performance and scalability tested
- [ ] Security review completed
- [ ] Deployment and rollback procedures tested
- [ ] Compliance requirements verified
- [ ] Gaps categorized by severity
- [ ] Go/No-Go decision made with clear rationale
- [ ] Action items prioritized with owners and timelines

## Common Mistakes

- **Skipping load testing**: Assuming the system will handle production load
- **No rollback plan**: Deploying without a way to quickly revert
- **Insufficient monitoring**: Not knowing when things go wrong
- **No runbooks**: On-call engineers don't know how to respond to incidents
- **Ignoring security**: Focusing only on functionality and performance
- **No disaster recovery**: Not prepared for major failures
- **Optimistic SLAs**: Committing to SLAs the system cannot meet
- **No on-call coverage**: Launching without 24/7 support
- **Untested backups**: Assuming backups work without testing restore
- **Missing alerts**: Not alerting on critical failure scenarios
- **No capacity headroom**: Running at capacity with no room for growth or spikes
- **Skipping staging**: Deploying directly to production without testing

## Examples

See [examples.md](examples.md) for detailed examples of production readiness assessments for various scenarios.

## Related Skills

- **Requires**: 
  - system-design (to understand the system architecture)
- **Commonly followed by**: 
  - deployment (to actually deploy the system)
  - production-monitoring (to monitor the system in production)
- **Alternative to**: None (this is the primary production readiness skill)
- **Works with**: 
  - architecture-review (for architecture assessment)
  - security-architecture-review (for security assessment)
  - reliability-analysis (for reliability assessment)
  - performance-testing (for performance validation)
  - disaster-recovery-planning (for DR readiness)

## Skill Composition

Typical workflow:

```
system-design
        ↓
architecture-review
        ↓
production-readiness
        ↓
go/no-go decision
        ↓
deployment
```

Comprehensive pre-launch workflow:

```
system-design
        ↓
architecture-review
        ↓
security-architecture-review
        ↓
reliability-analysis
        ↓
performance-testing
        ↓
production-readiness
        ↓
go/no-go decision
```

## Evaluation Criteria

### Completeness
- Are all operational dimensions assessed?
- Are monitoring, alerting, and runbooks verified?
- Are reliability and resilience validated?
- Is security reviewed?
- Are deployment and rollback procedures tested?

### Accuracy
- Are gaps correctly identified?
- Are severity levels appropriate?
- Are SLA targets realistic?
- Are risks properly assessed?

### Actionability
- Are gaps specific and clear?
- Are action items prioritized?
- Are owners and timelines assigned?
- Can the team act on the recommendations?

### Risk-Focused
- Are critical risks identified?
- Is the go/no-go decision well-reasoned?
- Are mitigation plans defined for known risks?
- Is the launch strategy appropriate for the risk level?