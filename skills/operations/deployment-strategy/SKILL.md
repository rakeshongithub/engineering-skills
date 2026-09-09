# Deployment Strategy

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 3-6 hours

---

## Purpose

Select and implement deployment strategies (blue/green, canary, rolling, feature flags) that minimize risk, enable zero-downtime deployments, and provide fast rollback capabilities.

---

## When to Use

- Designing deployment approach for new services
- Improving existing deployment process (too risky, too slow, causes downtime)
- Migrating to zero-downtime deployments
- Implementing gradual rollout capabilities
- Reducing deployment risk for critical systems
- Enabling faster rollback mechanisms
- Implementing A/B testing or experimentation
- Decoupling deployment from feature release
- Improving deployment confidence and safety

---

## When NOT to Use

- **For development or staging environments** — simpler strategies often sufficient
- **Without monitoring and observability** — can't validate deployment success
- **For stateful systems without migration strategy** — database changes need careful planning
- **Without rollback plan** — deployment strategy requires rollback capability
- **For one-time deployments** — overhead not justified
- **Without traffic control** — canary and blue/green require traffic routing
- **For systems with no users** — internal tools may not need sophisticated strategies

---

## Inputs

### Required

- **Application architecture** — monolith, microservices, serverless
- **Deployment target** — Kubernetes, cloud services, VMs, containers
- **Downtime tolerance** — acceptable downtime (zero, seconds, minutes)
- **Risk tolerance** — how much risk is acceptable
- **Rollback requirements** — time to rollback, automation level
- **Traffic routing capability** — load balancer, service mesh, DNS

### Optional

- **Current deployment process** — existing strategy and pain points
- **User base characteristics** — geographic distribution, usage patterns
- **Compliance requirements** — change approval, audit logs
- **Resource constraints** — budget, infrastructure capacity
- **Team capabilities** — skills, tools, automation maturity
- **Monitoring capabilities** — metrics, alerts, dashboards
- **Database migration strategy** — backward compatibility, rollback

---

## Expected Outputs

### Primary Deliverables

1. **Deployment Strategy Document**
   - Selected strategy (blue/green, canary, rolling, feature flags)
   - Rationale for selection
   - Traffic shifting plan
   - Rollback procedure
   - Success criteria

2. **Traffic Management Plan**
   - Traffic routing configuration
   - Gradual rollout percentages
   - Monitoring during rollout
   - Rollback triggers

3. **Rollback Procedure**
   - Automated rollback triggers
   - Manual rollback steps
   - Database rollback strategy
   - Communication plan

4. **Validation Criteria**
   - Health checks
   - Success metrics
   - Error thresholds
   - Performance benchmarks

### Supporting Artifacts

- **Infrastructure configuration** — load balancer, service mesh, DNS setup
- **Deployment automation** — scripts, pipelines, runbooks
- **Monitoring dashboards** — deployment-specific metrics
- **Testing plan** — smoke tests, integration tests during deployment
- **Communication templates** — deployment notifications, incident alerts

---

## Workflow

### Step 1: Assess Requirements and Constraints

**Objective:** Understand deployment requirements and constraints.

**Actions:**
- Define downtime tolerance (zero, < 1 min, < 5 min, acceptable)
- Assess risk tolerance (low, medium, high)
- Determine rollback time requirement (< 1 min, < 5 min, < 15 min)
- Identify resource constraints (budget, infrastructure capacity)
- Evaluate team capabilities (automation maturity, skills)
- Review compliance requirements (change approval, audit logs)

**Quality Check:**
- [ ] Downtime tolerance clearly defined
- [ ] Risk tolerance assessed
- [ ] Rollback requirements documented
- [ ] Resource constraints identified
- [ ] Team capabilities evaluated
- [ ] Compliance requirements understood

### Step 2: Evaluate Deployment Strategy Options

**Objective:** Compare deployment strategies and select the best fit.

**Actions:**
- Review deployment strategy options:
  - **Rolling Deployment:** Update instances gradually
  - **Blue/Green Deployment:** Deploy to new environment, switch traffic
  - **Canary Deployment:** Deploy to small subset, gradually increase
  - **Feature Flags:** Deploy code, control feature visibility
- Compare strategies against requirements (downtime, risk, rollback)
- Consider hybrid approaches (canary + feature flags)
- Assess infrastructure requirements for each strategy
- Evaluate complexity and maintenance overhead

**Quality Check:**
- [ ] All deployment strategies evaluated
- [ ] Strategies compared against requirements
- [ ] Infrastructure requirements assessed
- [ ] Complexity and overhead considered
- [ ] Decision documented with rationale

### Step 3: Design Traffic Management

**Objective:** Plan how traffic will be routed during deployment.

**Actions:**
- Identify traffic routing mechanism (load balancer, service mesh, DNS)
- Design traffic shifting plan (percentages, duration)
- Define traffic routing rules (header-based, geographic, random)
- Plan traffic monitoring (request rate, error rate, latency)
- Design rollback traffic routing (instant switch back)
- Consider session affinity and sticky sessions

**Quality Check:**
- [ ] Traffic routing mechanism selected
- [ ] Traffic shifting plan defined (percentages, duration)
- [ ] Routing rules documented
- [ ] Monitoring plan established
- [ ] Rollback routing designed
- [ ] Session handling considered

### Step 4: Define Rollback Procedure

**Objective:** Establish clear rollback process.

**Actions:**
- Define automated rollback triggers (error rate, latency, health checks)
- Document manual rollback steps (when to rollback, who decides)
- Plan database rollback strategy (backward-compatible migrations)
- Establish rollback communication plan (notify stakeholders)
- Define rollback success criteria (system restored, users unaffected)
- Test rollback procedure regularly

**Quality Check:**
- [ ] Automated rollback triggers defined
- [ ] Manual rollback steps documented
- [ ] Database rollback strategy planned
- [ ] Communication plan established
- [ ] Success criteria defined
- [ ] Rollback tested regularly

### Step 5: Establish Validation Criteria

**Objective:** Define how to validate deployment success.

**Actions:**
- Define health checks (application health, dependencies)
- Establish success metrics (error rate < 1%, latency < 200ms)
- Set error thresholds (critical errors = 0, total errors < 10)
- Define performance benchmarks (throughput, response time)
- Plan smoke tests (critical user flows)
- Establish monitoring duration (how long to monitor before promoting)

**Quality Check:**
- [ ] Health checks defined
- [ ] Success metrics established (error rate, latency)
- [ ] Error thresholds set
- [ ] Performance benchmarks defined
- [ ] Smoke tests planned
- [ ] Monitoring duration established

### Step 6: Configure Infrastructure

**Objective:** Set up infrastructure to support deployment strategy.

**Actions:**
- Configure load balancer (traffic routing, health checks)
- Set up service mesh (if using canary or advanced routing)
- Configure DNS (if using DNS-based routing)
- Provision additional resources (for blue/green, canary)
- Set up monitoring and alerting (deployment-specific metrics)
- Configure feature flag service (if using feature flags)

**Quality Check:**
- [ ] Load balancer configured
- [ ] Service mesh set up (if needed)
- [ ] DNS configured (if needed)
- [ ] Additional resources provisioned
- [ ] Monitoring and alerting configured
- [ ] Feature flag service set up (if needed)

### Step 7: Implement Deployment Automation

**Objective:** Automate deployment process.

**Actions:**
- Write deployment scripts (deploy, traffic shift, rollback)
- Integrate with CI/CD pipeline (trigger deployment, monitor)
- Implement automated health checks (verify deployment success)
- Automate traffic shifting (gradual rollout)
- Implement automated rollback (trigger on failure)
- Add deployment notifications (Slack, email, dashboard)

**Quality Check:**
- [ ] Deployment scripts written and tested
- [ ] CI/CD integration complete
- [ ] Automated health checks implemented
- [ ] Automated traffic shifting working
- [ ] Automated rollback implemented
- [ ] Notifications configured

### Step 8: Test Deployment Strategy

**Objective:** Validate deployment strategy in non-production environment.

**Actions:**
- Test deployment in staging environment
- Verify traffic routing works correctly
- Test rollback procedure (automated and manual)
- Validate monitoring and alerting
- Test edge cases (network failures, partial failures)
- Conduct load testing during deployment

**Quality Check:**
- [ ] Deployment tested in staging
- [ ] Traffic routing verified
- [ ] Rollback tested (automated and manual)
- [ ] Monitoring and alerting validated
- [ ] Edge cases tested
- [ ] Load testing conducted

### Step 9: Document and Train

**Objective:** Ensure team understands deployment strategy.

**Actions:**
- Write deployment strategy document (strategy, rationale, procedures)
- Create deployment runbook (step-by-step instructions)
- Document rollback procedure (when, how, who)
- Create troubleshooting guide (common issues, solutions)
- Conduct team training (deployment process, rollback)
- Establish support process (on-call, escalation)

**Quality Check:**
- [ ] Strategy document written
- [ ] Deployment runbook created
- [ ] Rollback procedure documented
- [ ] Troubleshooting guide written
- [ ] Team training conducted
- [ ] Support process established

### Step 10: Execute and Monitor

**Objective:** Deploy to production and monitor closely.

**Actions:**
- Execute deployment according to plan
- Monitor key metrics (error rate, latency, throughput)
- Validate health checks passing
- Gradually shift traffic (if canary or blue/green)
- Communicate deployment status to stakeholders
- Be prepared to rollback if issues detected

**Quality Check:**
- [ ] Deployment executed successfully
- [ ] Metrics monitored closely
- [ ] Health checks passing
- [ ] Traffic shifted gradually
- [ ] Stakeholders notified
- [ ] Rollback ready if needed

---

## Decision Framework

### Deployment Strategy Selection

**Use Rolling Deployment when:**
- Downtime tolerance: Brief downtime acceptable (< 1 min)
- Risk tolerance: Medium
- Resources: Limited (1x infrastructure)
- Complexity: Low
- Rollback speed: Slow (5-15 min)
- Example: Internal tools, non-critical services

**Use Blue/Green Deployment when:**
- Downtime tolerance: Zero downtime required
- Risk tolerance: Low
- Resources: Available (2x infrastructure)
- Complexity: Medium
- Rollback speed: Instant (< 1 min)
- Example: E-commerce, payment systems, customer-facing apps

**Use Canary Deployment when:**
- Downtime tolerance: Zero downtime required
- Risk tolerance: Very low
- Resources: Limited (1.1x infrastructure)
- Complexity: High
- Rollback speed: Fast (< 5 min)
- Example: High-traffic services, critical systems, gradual rollout needed

**Use Feature Flags when:**
- Downtime tolerance: Zero downtime required
- Risk tolerance: Very low
- Resources: Minimal (1x infrastructure)
- Complexity: High (code changes)
- Rollback speed: Instant (toggle off)
- Example: A/B testing, gradual feature rollout, trunk-based development

### Traffic Shifting Strategy

**Immediate (100%):**
- Use for: Low-risk changes, well-tested features
- Example: Bug fixes, minor updates

**Gradual (10% → 25% → 50% → 100%):**
- Use for: Medium-risk changes, new features
- Example: New API endpoints, UI changes

**Very Gradual (1% → 5% → 10% → 25% → 50% → 100%):**
- Use for: High-risk changes, major features
- Example: Payment processing changes, authentication changes

### Rollback Decision Criteria

**Automatic Rollback Triggers:**
- Error rate > 1% (or 2x baseline)
- Critical errors > 0
- Latency > 2x baseline (p95)
- Health checks fail for 3 consecutive checks
- Throughput drops > 20%

**Manual Rollback Triggers:**
- User reports of critical issues
- Business metrics degradation (conversion rate, revenue)
- Security vulnerability detected
- Data corruption detected

---

## Quality Checklist

### Strategy Selection
- [ ] Deployment strategy selected based on requirements
- [ ] Rationale documented
- [ ] Trade-offs understood (downtime, risk, resources, complexity)
- [ ] Team buy-in obtained

### Traffic Management
- [ ] Traffic routing mechanism configured
- [ ] Traffic shifting plan defined
- [ ] Monitoring in place for traffic metrics
- [ ] Session handling addressed

### Rollback
- [ ] Automated rollback triggers defined
- [ ] Manual rollback procedure documented
- [ ] Rollback tested regularly
- [ ] Database rollback strategy planned
- [ ] Rollback time meets requirements

### Validation
- [ ] Health checks defined and implemented
- [ ] Success metrics established
- [ ] Error thresholds set
- [ ] Smoke tests automated

### Infrastructure
- [ ] Load balancer configured
- [ ] Additional resources provisioned (if needed)
- [ ] Monitoring and alerting set up
- [ ] Feature flag service configured (if needed)

### Automation
- [ ] Deployment automated
- [ ] Traffic shifting automated
- [ ] Rollback automated
- [ ] Notifications configured

### Documentation
- [ ] Strategy document written
- [ ] Deployment runbook created
- [ ] Rollback procedure documented
- [ ] Team trained

---

## Common Mistakes

### 1. No Rollback Strategy

**Problem:** Deploy without rollback plan, prolonged outage when issues occur.

**Solution:** Always design rollback first, test rollback regularly.

### 2. Insufficient Monitoring

**Problem:** Deploy without monitoring, don't detect issues until users complain.

**Solution:** Set up comprehensive monitoring (error rate, latency, throughput), monitor during deployment.

### 3. All-or-Nothing Deployment

**Problem:** Deploy to 100% of traffic immediately, high risk.

**Solution:** Use gradual rollout (canary, blue/green with gradual shift).

### 4. Ignoring Database Migrations

**Problem:** Deploy code with incompatible database schema, application breaks.

**Solution:** Use backward-compatible migrations, deploy in phases (schema first, code second).

### 5. No Automated Rollback

**Problem:** Manual rollback takes too long, prolonged outage.

**Solution:** Implement automated rollback triggers (error rate, health checks).

### 6. Insufficient Resources for Blue/Green

**Problem:** Can't afford 2x infrastructure, blue/green not feasible.

**Solution:** Use canary or rolling deployment instead.

### 7. No Session Handling

**Problem:** Users lose sessions during deployment, poor user experience.

**Solution:** Use sticky sessions, session replication, or graceful shutdown.

### 8. Deploying During Peak Hours

**Problem:** Issues affect maximum number of users.

**Solution:** Deploy during off-peak hours, use maintenance windows.

### 9. No Communication Plan

**Problem:** Stakeholders unaware of deployment, confusion when issues occur.

**Solution:** Notify stakeholders before deployment, provide status updates.

### 10. Not Testing Rollback

**Problem:** Rollback fails when needed, can't recover.

**Solution:** Test rollback regularly (monthly), include in deployment checklist.

---

## Examples

### Example 1: E-commerce Platform (Blue/Green Deployment)

**Context:**
- High-traffic e-commerce platform
- Zero downtime required
- Fast rollback critical (< 1 min)
- 2x infrastructure available

**Strategy:**
- Deploy to green environment (identical to blue)
- Run smoke tests on green
- Switch load balancer to green
- Monitor for 15 minutes
- If issues, switch back to blue (instant rollback)
- If successful, keep green as production

**Traffic Shifting:**
```
Blue (current): 100% → 0%
Green (new):      0% → 100%
Duration: Instant switch
```

**Rollback:**
- Automated: Error rate > 1% → switch back to blue
- Manual: Engineering manager approval → switch back to blue
- Time: < 1 minute

### Example 2: Payment Service (Canary Deployment)

**Context:**
- Critical payment processing service
- Zero downtime required
- Very low risk tolerance
- Gradual rollout needed

**Strategy:**
- Deploy to canary instances (5% of traffic)
- Monitor for 10 minutes
- If successful, increase to 25%
- Monitor for 10 minutes
- If successful, increase to 50%
- Monitor for 10 minutes
- If successful, increase to 100%

**Traffic Shifting:**
```
Stable: 100% → 95% → 75% → 50% → 0%
Canary:   0% →  5% → 25% → 50% → 100%
Duration: 30 minutes total
```

**Rollback:**
- Automated: Error rate > 0.5% OR critical errors > 0 → rollback to stable
- Manual: Payment failures detected → rollback to stable
- Time: < 2 minutes

### Example 3: SaaS Application (Feature Flags)

**Context:**
- SaaS application with A/B testing
- Decouple deployment from feature release
- Gradual feature rollout to users

**Strategy:**
- Deploy code with feature flag disabled
- Enable feature for internal users (1%)
- Monitor for 24 hours
- Enable for beta users (10%)
- Monitor for 48 hours
- Gradually increase to 100%

**Traffic Shifting:**
```
Feature Disabled: 100% → 99% → 90% → 50% → 0%
Feature Enabled:    0% →  1% → 10% → 50% → 100%
Duration: 7 days
```

**Rollback:**
- Automated: Error rate > 2% → disable feature flag
- Manual: User complaints → disable feature flag
- Time: Instant (toggle off)

### Example 4: Microservices Platform (Rolling Deployment)

**Context:**
- Internal microservices platform
- Brief downtime acceptable (< 1 min)
- Limited resources (1x infrastructure)

**Strategy:**
- Update instances one at a time
- Wait for health check before updating next
- Continue until all instances updated

**Traffic Shifting:**
```
Old Version: 100% → 80% → 60% → 40% → 20% → 0%
New Version:   0% → 20% → 40% → 60% → 80% → 100%
Duration: 10 minutes (5 instances, 2 min each)
```

**Rollback:**
- Manual: Redeploy previous version (rolling update)
- Time: 10 minutes

---

## Related Skills

### Prerequisites
- **ci-cd-design** — Automated deployment pipelines
- **infrastructure-as-code** — Infrastructure automation
- **observability-design** — Monitoring and alerting

### Commonly Followed By
- **incident-analysis** — Analyze deployment failures
- **production-readiness** — Validate deployment readiness
- **disaster-recovery** — Plan for deployment disasters

### Related Skills
- **capacity-planning** — Ensure resources for blue/green
- **performance-optimization** — Optimize deployment performance
- **reliability-analysis** — Assess deployment reliability

---

## Skill Composition

### Zero-Downtime Deployment Workflow

```
ci-cd-design (automated pipeline)
      ↓
deployment-strategy (this skill)
      ↓
observability-design (monitor deployment)
      ↓
incident-analysis (if deployment fails)
```

---

## Evaluation Criteria

### Excellent
- Zero downtime deployments
- Automated rollback (< 1 min)
- Gradual traffic shifting (canary or blue/green)
- Comprehensive monitoring during deployment
- Rollback tested regularly (monthly)
- Deployment success rate > 98%
- Clear documentation and runbooks

### Good
- Minimal downtime (< 1 min)
- Manual rollback (< 5 min)
- Some traffic control (rolling or blue/green)
- Basic monitoring during deployment
- Rollback tested occasionally
- Deployment success rate > 90%

### Needs Improvement
- Frequent downtime (> 5 min)
- Slow rollback (> 15 min)
- All-or-nothing deployment
- No monitoring during deployment
- Rollback never tested
- Deployment success rate < 85%

---

## Tags

`operations`, `deployment`, `blue-green`, `canary`, `rolling`, `feature-flags`, `zero-downtime`, `rollback`, `traffic-management`, `release-engineering`, `devops`, `sre`

---

## Version

**1.0.0** — Initial release
