# Disaster Recovery

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 6-12 hours

---

## Purpose

Design and implement disaster recovery (DR) plans and procedures to ensure business continuity by defining recovery objectives, failover strategies, backup automation, and recovery procedures for catastrophic failures.

---

## When to Use

- Designing DR strategy for new systems or services
- Improving existing DR capabilities (slow recovery, untested procedures)
- Meeting compliance requirements (SOC 2, HIPAA, PCI DSS)
- Planning for multi-region or multi-cloud deployments
- Implementing business continuity plans
- Preparing for catastrophic failures (region outage, data center loss)
- Establishing RTO/RPO requirements
- Testing and validating DR procedures
- Improving recovery time and data loss prevention

---

## When NOT to Use

- **For high availability (HA) design** — use reliability-analysis instead
- **For backup strategy only** — use backup-recovery skill
- **For incident response** — use incident-analysis skill
- **Without business impact analysis** — understand criticality first
- **For development or test environments** — DR typically for production only
- **Without stakeholder buy-in** — DR requires organizational commitment
- **For systems with no data** — stateless services may not need DR

---

## Inputs

### Required

- **System architecture** — components, dependencies, data flows
- **Business criticality** — which systems are mission-critical
- **RTO requirements** — Recovery Time Objective (acceptable downtime)
- **RPO requirements** — Recovery Point Objective (acceptable data loss)
- **Data inventory** — databases, file storage, object storage
- **Compliance requirements** — regulatory DR requirements

### Optional

- **Current DR capabilities** — existing backups, failover mechanisms
- **Budget constraints** — DR infrastructure costs
- **Geographic requirements** — data residency, latency constraints
- **Incident history** — past outages and their impact
- **Dependency map** — external services, third-party dependencies
- **Team capabilities** — skills, on-call availability

---

## Expected Outputs

### Primary Deliverables

1. **Disaster Recovery Plan**
   - RTO/RPO definitions per system
   - DR strategy (backup/restore, pilot light, warm standby, hot standby, multi-site)
   - Failover procedures
   - Recovery procedures
   - Communication plan

2. **DR Architecture**
   - Primary site configuration
   - DR site configuration
   - Data replication strategy
   - Failover mechanisms
   - Network configuration

3. **Recovery Runbooks**
   - Step-by-step recovery procedures
   - Failover procedures
   - Failback procedures
   - Validation procedures
   - Troubleshooting guides

4. **DR Testing Plan**
   - Test scenarios
   - Test frequency
   - Success criteria
   - Test documentation
   - Lessons learned process

### Supporting Artifacts

- **Backup automation** — automated backup scripts, schedules
- **Monitoring and alerting** — DR-specific monitoring
- **Cost estimates** — DR infrastructure and operational costs
- **Training materials** — DR training for team
- **Compliance documentation** — DR compliance evidence

---

## Workflow

### Step 1: Define RTO and RPO Requirements

**Objective:** Establish recovery objectives for each system.

**Actions:**
- Conduct business impact analysis (BIA)
- Define RTO per system (time to recover)
- Define RPO per system (acceptable data loss)
- Categorize systems by criticality (critical, important, normal)
- Document financial impact of downtime
- Align RTO/RPO with business requirements

**Quality Check:**
- [ ] Business impact analysis completed
- [ ] RTO defined for each system
- [ ] RPO defined for each system
- [ ] Systems categorized by criticality
- [ ] Financial impact documented
- [ ] Stakeholder approval obtained

### Step 2: Select DR Strategy

**Objective:** Choose appropriate DR strategy based on RTO/RPO.

**Actions:**
- Evaluate DR strategies:
  - **Backup/Restore:** Low cost, slow recovery (RTO: hours/days, RPO: hours)
  - **Pilot Light:** Minimal DR site, medium recovery (RTO: hours, RPO: minutes)
  - **Warm Standby:** Scaled-down DR site, fast recovery (RTO: minutes, RPO: seconds)
  - **Hot Standby:** Full DR site, instant recovery (RTO: seconds, RPO: near-zero)
  - **Multi-Site Active/Active:** No failover, instant (RTO: none, RPO: none)
- Match strategy to RTO/RPO requirements
- Consider cost vs. recovery time trade-offs
- Assess multi-region vs. multi-cloud options

**Quality Check:**
- [ ] DR strategies evaluated
- [ ] Strategy selected per system
- [ ] Cost vs. recovery time analyzed
- [ ] Multi-region/multi-cloud assessed
- [ ] Decision documented with rationale

### Step 3: Design DR Architecture

**Objective:** Design DR infrastructure and data replication.

**Actions:**
- Design DR site architecture (region, availability zones)
- Plan data replication (synchronous, asynchronous, snapshot-based)
- Design network connectivity (VPN, Direct Connect, peering)
- Plan DNS failover (Route 53, Traffic Manager, Cloud DNS)
- Design load balancer failover
- Plan database replication (read replicas, multi-region)

**Quality Check:**
- [ ] DR site architecture designed
- [ ] Data replication strategy defined
- [ ] Network connectivity planned
- [ ] DNS failover designed
- [ ] Load balancer failover planned
- [ ] Database replication configured

### Step 4: Implement Backup Automation

**Objective:** Automate backup processes.

**Actions:**
- Implement automated backups (databases, file storage, configurations)
- Define backup schedules (hourly, daily, weekly, monthly)
- Configure backup retention (7 days, 30 days, 1 year)
- Implement backup encryption (at rest, in transit)
- Set up backup monitoring and alerting
- Test backup restoration regularly

**Quality Check:**
- [ ] Automated backups implemented
- [ ] Backup schedules defined
- [ ] Retention policies configured
- [ ] Backup encryption enabled
- [ ] Monitoring and alerting set up
- [ ] Restoration tested regularly

### Step 5: Implement Failover Mechanisms

**Objective:** Automate failover to DR site.

**Actions:**
- Implement DNS failover (health checks, automatic failover)
- Configure load balancer failover
- Set up database failover (automatic or manual)
- Implement application failover logic
- Configure monitoring for failover triggers
- Test failover procedures

**Quality Check:**
- [ ] DNS failover implemented
- [ ] Load balancer failover configured
- [ ] Database failover set up
- [ ] Application failover logic implemented
- [ ] Monitoring configured
- [ ] Failover tested

### Step 6: Create Recovery Runbooks

**Objective:** Document step-by-step recovery procedures.

**Actions:**
- Write failover runbook (trigger failover, validate)
- Write recovery runbook (restore from backup, validate)
- Write failback runbook (return to primary site)
- Document validation procedures (health checks, smoke tests)
- Create troubleshooting guides (common issues, solutions)
- Include contact information (on-call, escalation)

**Quality Check:**
- [ ] Failover runbook written
- [ ] Recovery runbook written
- [ ] Failback runbook written
- [ ] Validation procedures documented
- [ ] Troubleshooting guides created
- [ ] Contact information included

### Step 7: Establish DR Testing Plan

**Objective:** Plan regular DR testing.

**Actions:**
- Define test scenarios (region outage, data center loss, data corruption)
- Establish test frequency (quarterly, semi-annually, annually)
- Plan test execution (full failover, tabletop exercise, partial test)
- Define success criteria (RTO met, RPO met, no data loss)
- Document test results and lessons learned
- Update DR plan based on test findings

**Quality Check:**
- [ ] Test scenarios defined
- [ ] Test frequency established
- [ ] Test execution planned
- [ ] Success criteria defined
- [ ] Documentation process established
- [ ] Update process defined

### Step 8: Implement Monitoring and Alerting

**Objective:** Monitor DR readiness and trigger failover.

**Actions:**
- Monitor backup success/failure
- Monitor data replication lag
- Monitor DR site health
- Set up alerts for backup failures
- Set up alerts for replication lag
- Monitor RTO/RPO compliance

**Quality Check:**
- [ ] Backup monitoring implemented
- [ ] Replication lag monitoring set up
- [ ] DR site health monitoring configured
- [ ] Backup failure alerts configured
- [ ] Replication lag alerts set up
- [ ] RTO/RPO compliance tracked

### Step 9: Document and Train

**Objective:** Ensure team understands DR procedures.

**Actions:**
- Write DR plan document (strategy, procedures, contacts)
- Create DR training materials (presentations, videos)
- Conduct DR training (runbooks, failover, recovery)
- Establish on-call rotation (DR responsibilities)
- Create communication templates (stakeholder notifications)
- Document compliance evidence (for audits)

**Quality Check:**
- [ ] DR plan document written
- [ ] Training materials created
- [ ] Team training conducted
- [ ] On-call rotation established
- [ ] Communication templates created
- [ ] Compliance documentation complete

### Step 10: Test and Validate

**Objective:** Execute DR test and validate procedures.

**Actions:**
- Schedule DR test (communicate to stakeholders)
- Execute failover to DR site
- Validate application functionality
- Measure RTO and RPO achieved
- Execute failback to primary site
- Document test results and lessons learned

**Quality Check:**
- [ ] DR test scheduled and communicated
- [ ] Failover executed successfully
- [ ] Application functionality validated
- [ ] RTO and RPO measured
- [ ] Failback executed successfully
- [ ] Results documented, lessons learned captured

---

## Decision Framework

### DR Strategy Selection

**Backup/Restore:**
- RTO: Hours to days
- RPO: Hours
- Cost: Very low
- Use for: Non-critical systems, development environments

**Pilot Light:**
- RTO: Hours
- RPO: Minutes
- Cost: Low
- Use for: Important systems, moderate criticality

**Warm Standby:**
- RTO: Minutes
- RPO: Seconds
- Cost: Medium
- Use for: Critical systems, e-commerce, SaaS

**Hot Standby:**
- RTO: Seconds
- RPO: Near-zero
- Cost: High (2x infrastructure)
- Use for: Mission-critical systems, payment processing, healthcare

**Multi-Site Active/Active:**
- RTO: None (no failover)
- RPO: None
- Cost: Very high (2x+ infrastructure)
- Use for: Global services, zero-downtime requirements

### RTO/RPO Matrix

| System Criticality | RTO | RPO | DR Strategy |
|--------------------|-----|-----|-------------|
| Mission-Critical | < 1 min | < 1 min | Hot Standby or Multi-Site |
| Critical | < 1 hour | < 15 min | Warm Standby |
| Important | < 4 hours | < 1 hour | Pilot Light |
| Normal | < 24 hours | < 24 hours | Backup/Restore |

---

## Quality Checklist

### Planning
- [ ] RTO/RPO defined for all systems
- [ ] DR strategy selected per system
- [ ] DR architecture designed
- [ ] Cost estimates approved

### Implementation
- [ ] Backup automation implemented
- [ ] Data replication configured
- [ ] Failover mechanisms implemented
- [ ] DR site provisioned

### Documentation
- [ ] DR plan document written
- [ ] Recovery runbooks created
- [ ] Training materials prepared
- [ ] Compliance documentation complete

### Testing
- [ ] DR test plan established
- [ ] DR test executed successfully
- [ ] RTO/RPO requirements met
- [ ] Lessons learned documented

---

## Common Mistakes

### 1. No DR Testing

**Problem:** DR plan never tested, fails when needed.

**Solution:** Test DR quarterly, document results, update plan.

### 2. Unrealistic RTO/RPO

**Problem:** RTO/RPO not achievable with current DR strategy.

**Solution:** Align RTO/RPO with DR strategy, upgrade if needed.

### 3. No Backup Validation

**Problem:** Backups corrupted or incomplete, can't restore.

**Solution:** Test backup restoration regularly (monthly).

### 4. Single Region Deployment

**Problem:** Region outage causes total failure.

**Solution:** Deploy to multiple regions for critical systems.

### 5. No Failback Plan

**Problem:** Can't return to primary site after DR event.

**Solution:** Document failback procedure, test regularly.

### 6. Ignoring Dependencies

**Problem:** Application depends on external services that also fail.

**Solution:** Map dependencies, plan DR for critical dependencies.

### 7. No Communication Plan

**Problem:** Stakeholders unaware of DR event, confusion.

**Solution:** Create communication templates, establish notification process.

### 8. Stale DR Documentation

**Problem:** DR plan outdated, doesn't reflect current architecture.

**Solution:** Update DR plan after every architecture change.

### 9. Insufficient Monitoring

**Problem:** Don't detect backup failures or replication lag.

**Solution:** Monitor backups, replication, DR site health.

### 10. No Cost Planning

**Problem:** DR costs exceed budget, DR infrastructure shut down.

**Solution:** Estimate DR costs upfront, get budget approval.

---

## Examples

### Example 1: E-commerce Platform (Warm Standby)

**RTO:** 15 minutes  
**RPO:** 5 minutes  
**Strategy:** Warm standby in secondary region

**Architecture:**
- Primary: us-east-1 (full capacity)
- DR: us-west-2 (50% capacity, auto-scaling)
- Database: PostgreSQL with streaming replication
- DNS: Route 53 health checks, automatic failover

**Failover:**
1. Route 53 detects primary region failure
2. Automatically routes traffic to us-west-2
3. DR site auto-scales to 100% capacity
4. Database promotes read replica to primary
5. Validate application functionality

**Cost:** ~60% of primary infrastructure

### Example 2: Payment Processing (Hot Standby)

**RTO:** 30 seconds  
**RPO:** Near-zero  
**Strategy:** Hot standby with synchronous replication

**Architecture:**
- Primary: us-east-1 (full capacity)
- DR: us-west-2 (full capacity, active)
- Database: PostgreSQL with synchronous replication
- Load Balancer: Global load balancer with health checks

**Failover:**
1. Load balancer detects primary region failure
2. Instantly routes all traffic to us-west-2
3. No database promotion needed (already active)
4. Validate payment processing

**Cost:** ~100% of primary infrastructure (2x total)

### Example 3: SaaS Application (Pilot Light)

**RTO:** 2 hours  
**RPO:** 30 minutes  
**Strategy:** Pilot light with minimal DR infrastructure

**Architecture:**
- Primary: us-east-1 (full capacity)
- DR: us-west-2 (database read replica only)
- Backups: Hourly snapshots to S3

**Failover:**
1. Detect primary region failure
2. Provision application servers in us-west-2 (30 min)
3. Promote database read replica to primary (5 min)
4. Update DNS to point to us-west-2 (5 min)
5. Validate application functionality (15 min)
6. Total: ~55 minutes

**Cost:** ~10% of primary infrastructure

### Example 4: Internal Tools (Backup/Restore)

**RTO:** 24 hours  
**RPO:** 24 hours  
**Strategy:** Daily backups, restore on demand

**Architecture:**
- Primary: us-east-1
- Backups: Daily snapshots to S3 (cross-region replication)
- No DR infrastructure

**Recovery:**
1. Detect primary region failure
2. Provision infrastructure in us-west-2 (2 hours)
3. Restore database from latest backup (4 hours)
4. Deploy application (1 hour)
5. Update DNS (5 min)
6. Validate functionality (1 hour)
7. Total: ~8 hours

**Cost:** Backup storage only (~$50/month)

---

## Related Skills

### Prerequisites
- **architecture-discovery** — Understand system architecture
- **observability-design** — Monitoring and alerting
- **infrastructure-as-code** — Automate DR infrastructure

### Commonly Followed By
- **incident-analysis** — Analyze DR events
- **production-readiness** — Validate DR readiness
- **capacity-planning** — Plan DR capacity

### Related Skills
- **backup-recovery** — Backup strategy
- **reliability-analysis** — High availability design
- **security-architecture-review** — DR security

---

## Skill Composition

### Business Continuity Workflow

```
architecture-discovery (understand system)
      ↓
disaster-recovery (this skill)
      ↓
backup-recovery (backup automation)
      ↓
observability-design (monitor DR)
      ↓
incident-analysis (DR event analysis)
```

---

## Evaluation Criteria

### Excellent
- RTO/RPO requirements met consistently
- DR tested quarterly
- Automated failover and failback
- Multi-region deployment
- Zero data loss in tests
- Comprehensive documentation
- Team trained and prepared

### Good
- RTO/RPO requirements met most of the time
- DR tested semi-annually
- Manual failover, automated backup
- Single DR region
- Minimal data loss in tests
- Basic documentation

### Needs Improvement
- RTO/RPO requirements not met
- DR never tested or tested annually
- Manual failover and recovery
- No DR infrastructure
- Significant data loss in tests
- Outdated or missing documentation

---

## Tags

`operations`, `disaster-recovery`, `business-continuity`, `rto`, `rpo`, `failover`, `backup`, `replication`, `multi-region`, `high-availability`, `resilience`, `sre`

---

## Version

**1.0.0** — Initial release
