# Production Readiness - Step-by-Step Instructions

## Overview

This skill helps you assess whether a system is ready for production deployment across operational, security, and reliability dimensions.

## Step-by-Step Workflow

### Step 1: Understand Requirements and Context (30-60 minutes)

**Actions:**
1. Define SLA targets (availability, latency, throughput)
2. Understand expected load (users, requests, data volume)
3. Assess business impact of downtime
4. Review compliance requirements
5. Identify acceptable risk level
6. Review system architecture and critical components
7. Identify dependencies (internal and external)
8. Understand deployment model
9. Identify stakeholders and on-call team

**Outputs:**
- Requirements document with SLA targets, load expectations, and risk tolerance
- System overview with architecture and dependencies
- Stakeholder list with roles and responsibilities

### Step 2: Assess Operational Readiness (2-3 hours)

**Actions:**

**Monitoring and Observability:**
1. Verify golden signals monitored (latency, traffic, errors, saturation)
2. Check business metrics tracking (signups, conversions, revenue)
3. Verify infrastructure metrics collection (CPU, memory, disk, network)
4. Review custom application metrics
5. Verify metrics retention policy
6. Check structured logging implementation
7. Verify logs are centralized and searchable
8. Review log retention policy
9. Verify PII not logged or properly masked
10. Check distributed tracing (for microservices)
11. Review dashboards (system health, service-level)
12. Verify dashboards include key SLIs

**Alerting:**
1. Verify alerts for SLA violations
2. Check alerts for error rate spikes
3. Verify alerts for resource exhaustion
4. Check alerts for dependency failures
5. Verify alerts for security events
6. Review alert quality (actionable, appropriate severity)
7. Verify alerts have runbook links
8. Check alert routing and escalation
9. Verify alert acknowledgment and resolution tracking

**Runbooks and Documentation:**
1. Review runbooks for common incidents
2. Verify runbooks include symptoms, diagnosis, remediation
3. Check runbooks are easily accessible
4. Verify architecture diagrams are up-to-date
5. Review deployment procedures
6. Review rollback procedures
7. Check disaster recovery plan
8. Verify on-call rotation and escalation documented

**On-Call and Incident Response:**
1. Verify on-call rotation established
2. Check on-call engineers are trained
3. Verify on-call schedule is published
4. Review incident response process
5. Check incident severity levels defined
6. Verify communication channels established
7. Review post-mortem process

**Outputs:**
- Operational readiness checklist with status for each item
- List of gaps in monitoring, alerting, documentation, and incident response

### Step 3: Assess Reliability and Resilience (2-3 hours)

**Actions:**

**High Availability:**
1. Identify single points of failure
2. Verify critical components have redundancy
3. Check multi-AZ or multi-region deployment (if required)
4. Verify load balancing configured
5. Check auto-scaling configured
6. Review graceful degradation implementation
7. Verify circuit breakers implemented
8. Check retries with exponential backoff
9. Verify timeouts configured appropriately
10. Review bulkheads to isolate failures

**Data Durability:**
1. Verify database backups automated
2. Check backup retention policy
3. Verify backup restoration tested
4. Check point-in-time recovery available (if required)
5. Verify data replication configured

**Disaster Recovery:**
1. Verify RTO and RPO defined
2. Check DR plan documented
3. Verify DR plan tested
4. Check backups stored in separate region/account
5. Verify backup encryption enabled
6. Review failover procedures
7. Check DNS failover configured (if multi-region)

**Outputs:**
- Reliability and resilience checklist with status
- List of single points of failure and redundancy gaps
- DR readiness assessment

### Step 4: Assess Performance and Scalability (1-2 hours)

**Actions:**

**Performance:**
1. Review performance benchmarks
2. Verify latency targets defined (p50, p95, p99)
3. Check throughput targets defined
4. Verify performance tested under expected load
5. Review database query optimization
6. Check indexes for common queries
7. Verify caching implemented where appropriate
8. Check CDN configured for static assets
9. Review API response times

**Scalability:**
1. Verify stateless components can scale horizontally
2. Check auto-scaling policies configured
3. Verify load testing performed
4. Review scaling limits
5. Check current capacity measured
6. Verify expected growth projected
7. Check capacity headroom sufficient (typically 2x expected peak)
8. Review scaling triggers

**Outputs:**
- Performance benchmarks and test results
- Scalability assessment with capacity headroom analysis
- List of performance and scalability gaps

### Step 5: Assess Security (1-2 hours)

**Actions:**
1. Verify authentication implemented and tested
2. Check authorization checks at every layer
3. Verify principle of least privilege followed
4. Check secrets managed securely (not in code)
5. Verify data encrypted in transit (TLS 1.2+)
6. Check data encrypted at rest (if required)
7. Verify PII handling compliant with regulations
8. Check data retention and deletion policies
9. Verify firewall rules configured
10. Check security groups follow least privilege
11. Verify DDoS protection enabled (if public-facing)
12. Check WAF configured (if applicable)
13. Verify dependencies scanned for vulnerabilities
14. Check security patches applied
15. Review penetration testing results (if performed)
16. Verify security review completed

**Outputs:**
- Security checklist with status
- List of security gaps and vulnerabilities
- Compliance status

### Step 6: Assess Deployment and Rollback (1-2 hours)

**Actions:**

**Deployment:**
1. Verify deployment strategy defined (blue-green, canary, rolling)
2. Check deployment automated (CI/CD)
3. Verify deployment tested in staging
4. Review database migrations tested
5. Check feature flags configured
6. Verify configuration validated
7. Check dependencies verified
8. Review smoke tests automated
9. Verify health checks configured
10. Check monitoring verified post-deployment

**Rollback:**
1. Verify rollback procedures documented
2. Check rollback tested
3. Verify rollback time acceptable (typically < 15 minutes)
4. Review database rollback strategy (if schema changes)

**Outputs:**
- Deployment readiness checklist
- Rollback plan with procedures and timelines
- List of deployment and rollback gaps

### Step 7: Assess Compliance and Governance (30-60 minutes)

**Actions:**
1. Identify compliance requirements (GDPR, HIPAA, SOC 2, etc.)
2. Verify compliance controls implemented
3. Check audit logging enabled
4. Verify data residency requirements met
5. Review change approval process followed
6. Verify stakeholders notified
7. Check maintenance window scheduled (if required)
8. Review communication plan
9. Verify cost estimates calculated
10. Check cost monitoring configured
11. Verify cost alerts set up
12. Review cost optimization opportunities

**Outputs:**
- Compliance checklist with status
- Change management verification
- Cost analysis and monitoring plan

### Step 8: Categorize Gaps and Make Go/No-Go Decision (1-2 hours)

**Actions:**
1. Compile all gaps from previous steps
2. Categorize gaps by severity:
   - **Blockers**: Must fix before launch
   - **High Priority**: Should fix before launch
   - **Medium Priority**: Can launch with plan to fix
   - **Low Priority**: Nice to have

3. For each gap, document:
   - Description
   - Impact
   - Mitigation (if any)
   - Effort to fix
   - Owner
   - Timeline

4. Make go/no-go decision:
   - **GO**: No blockers, high-priority items have mitigation plans
   - **NO-GO**: Blockers present, high-priority items without mitigation
   - **CONDITIONAL GO**: Launch to limited audience with increased monitoring

5. Define launch strategy:
   - Full launch
   - Phased launch (10%, 50%, 100%)
   - Beta launch
   - Delay launch

6. Create action plan:
   - Immediate actions (before launch)
   - Post-launch actions (within 1 week, 1 month)
   - Long-term improvements

**Outputs:**
- Categorized gap list with severity, impact, and mitigation
- Go/No-Go decision with clear rationale
- Launch strategy
- Action plan with owners and timelines

### Step 9: Document and Present (1-2 hours)

**Actions:**
1. **Executive Summary** (1 page):
   - Overall readiness status (Red/Yellow/Green)
   - Go/No-Go decision
   - Top 3-5 critical gaps
   - Recommended launch strategy
   - Key action items

2. **Detailed Assessment**:
   - Operational readiness
   - Reliability and resilience
   - Performance and scalability
   - Security
   - Deployment and rollback
   - Compliance and governance

3. **Gap Analysis**:
   - All gaps categorized by severity
   - Impact and mitigation for each gap
   - Effort estimates and owners

4. **Action Plan**:
   - Immediate actions (before launch)
   - Post-launch actions
   - Long-term improvements

5. **Launch Checklist**:
   - Final checklist to verify before deployment
   - Sign-off from stakeholders

6. Review with stakeholders and team
7. Get approval to proceed (or delay)

**Outputs:**
- Complete production readiness report
- Presentation deck
- Launch checklist
- Action tracking document

## Tips for Success

- **Be thorough**: Don't skip any dimension of readiness
- **Be realistic**: Don't assume things work without verifying
- **Test everything**: Especially backups, rollbacks, and failovers
- **Focus on critical path**: Prioritize what's essential for launch
- **Get team buy-in**: Ensure everyone agrees on readiness status
- **Document everything**: Runbooks, procedures, decisions
- **Plan for failure**: Assume things will go wrong and prepare
- **Monitor closely**: Increase monitoring during and after launch
- **Have rollback ready**: Be prepared to revert quickly if needed
- **Communicate clearly**: Keep stakeholders informed

## Common Pitfalls to Avoid

- Skipping load testing and assuming the system will handle production load
- Deploying without a tested rollback plan
- Launching without adequate monitoring and alerting
- Not having runbooks for common incident scenarios
- Ignoring security in favor of speed
- Not preparing for disaster recovery
- Committing to SLAs the system cannot meet
- Launching without 24/7 on-call coverage
- Assuming backups work without testing restore
- Missing critical alerts for failure scenarios
- Running at capacity with no headroom for growth
- Skipping staging and deploying directly to production

## Validation Checklist

- [ ] All operational dimensions assessed
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
- [ ] Stakeholders aligned on decision
- [ ] Launch checklist created