# Deployment Strategy - Quick Reference

**Select and implement deployment strategies (blue/green, canary, rolling, feature flags) that minimize risk, enable zero-downtime deployments, and provide fast rollback capabilities.**

---

## Quick Start

### When to Use This Skill

✅ Designing deployment approach for new services  
✅ Improving existing deployment process (too risky, too slow, causes downtime)  
✅ Migrating to zero-downtime deployments  
✅ Implementing gradual rollout capabilities  
✅ Reducing deployment risk for critical systems  
✅ Enabling faster rollback mechanisms  

### When NOT to Use

❌ For development or staging environments  
❌ Without monitoring and observability  
❌ For stateful systems without migration strategy  
❌ Without rollback plan  
❌ For one-time deployments  

---

## Deployment Strategy Comparison

| Strategy | Downtime | Risk | Resources | Complexity | Rollback Speed | Best For |
|----------|----------|------|-----------|------------|----------------|----------|
| **Rolling** | Brief | Medium | 1x | Low | Slow (5-15 min) | Internal tools, non-critical services |
| **Blue/Green** | None | Low | 2x | Medium | Instant (< 1 min) | E-commerce, payment systems, customer-facing apps |
| **Canary** | None | Very Low | 1.1x | High | Fast (< 5 min) | High-traffic services, critical systems, gradual rollout |
| **Feature Flags** | None | Very Low | 1x | High | Instant (toggle) | A/B testing, gradual feature rollout, trunk-based development |

---

## Traffic Shifting Strategies

### Immediate (100%)
**Use for:** Low-risk changes, bug fixes, minor updates

### Gradual (10% → 25% → 50% → 100%)
**Use for:** Medium-risk changes, new features  
**Duration:** 20-30 minutes

### Very Gradual (1% → 5% → 10% → 25% → 50% → 100%)
**Use for:** High-risk changes, major features  
**Duration:** 1-2 hours

---

## Rollback Decision Criteria

### Automatic Rollback Triggers
- Error rate > 1% (or 2x baseline)
- Critical errors > 0
- Latency > 2x baseline (p95)
- Health checks fail for 3 consecutive checks
- Throughput drops > 20%

### Manual Rollback Triggers
- User reports of critical issues
- Business metrics degradation (conversion rate, revenue)
- Security vulnerability detected
- Data corruption detected

---

## 10-Step Workflow

1. **Assess Requirements and Constraints** (20-30 min) — Downtime tolerance, risk tolerance, rollback requirements
2. **Evaluate Deployment Strategy Options** (30-45 min) — Compare rolling, blue/green, canary, feature flags
3. **Design Traffic Management** (30-60 min) — Traffic routing, shifting plan, monitoring
4. **Define Rollback Procedure** (30-45 min) — Automated triggers, manual steps, database strategy
5. **Establish Validation Criteria** (20-30 min) — Health checks, success metrics, error thresholds
6. **Configure Infrastructure** (45-90 min) — Load balancer, service mesh, monitoring
7. **Implement Deployment Automation** (60-90 min) — Scripts, CI/CD integration, notifications
8. **Test Deployment Strategy** (30-60 min) — Staging tests, rollback tests, edge cases
9. **Document and Train** (20-30 min) — Strategy doc, runbooks, team training
10. **Execute and Monitor** (varies) — Deploy, monitor, communicate, rollback if needed

**Total Time:** 3-6 hours

---

## Quick Examples

### Example 1: E-commerce Platform (Blue/Green)

**Strategy:** Deploy to green environment, switch traffic instantly  
**Traffic Shift:** Blue 100% → 0%, Green 0% → 100%  
**Rollback:** Instant (< 1 min) - switch back to blue  
**Monitoring:** 15 minutes post-deployment  

### Example 2: Payment Service (Canary)

**Strategy:** Gradual rollout with monitoring  
**Traffic Shift:** 5% → 25% → 50% → 100% over 30 minutes  
**Rollback:** Automated (< 2 min) if error rate > 0.5%  
**Monitoring:** Continuous during rollout  

### Example 3: SaaS Application (Feature Flags)

**Strategy:** Deploy code, control feature visibility  
**Traffic Shift:** 1% → 10% → 50% → 100% over 7 days  
**Rollback:** Instant (toggle off feature flag)  
**Monitoring:** 24-48 hours per stage  

### Example 4: Microservices (Rolling)

**Strategy:** Update instances one at a time  
**Traffic Shift:** 20% increments over 10 minutes  
**Rollback:** Manual (10 min) - redeploy previous version  
**Monitoring:** Health checks between updates  

---

## Quality Checklist

### Strategy Selection
- [ ] Deployment strategy selected based on requirements
- [ ] Rationale documented
- [ ] Trade-offs understood
- [ ] Team buy-in obtained

### Traffic Management
- [ ] Traffic routing mechanism configured
- [ ] Traffic shifting plan defined
- [ ] Monitoring in place
- [ ] Session handling addressed

### Rollback
- [ ] Automated rollback triggers defined
- [ ] Manual rollback procedure documented
- [ ] Rollback tested regularly
- [ ] Database rollback strategy planned

### Validation
- [ ] Health checks implemented
- [ ] Success metrics established
- [ ] Error thresholds set
- [ ] Smoke tests automated

---

## Common Mistakes

❌ **No rollback strategy** — Prolonged outage when issues occur  
✅ **Always design rollback first** — Test rollback regularly

❌ **Insufficient monitoring** — Don't detect issues until users complain  
✅ **Comprehensive monitoring** — Error rate, latency, throughput

❌ **All-or-nothing deployment** — High risk  
✅ **Gradual rollout** — Canary, blue/green with gradual shift

❌ **Ignoring database migrations** — Application breaks  
✅ **Backward-compatible migrations** — Deploy in phases

❌ **No automated rollback** — Manual rollback takes too long  
✅ **Automated rollback triggers** — Error rate, health checks

---

## Success Metrics

### Excellent
- Zero downtime deployments
- Automated rollback < 1 min
- Gradual traffic shifting
- Deployment success rate > 98%
- Rollback tested monthly

### Good
- Minimal downtime < 1 min
- Manual rollback < 5 min
- Some traffic control
- Deployment success rate > 90%

### Needs Improvement
- Frequent downtime > 5 min
- Slow rollback > 15 min
- All-or-nothing deployment
- Deployment success rate < 85%

---

## Related Skills

**Prerequisites:**
- `ci-cd-design` — Automated deployment pipelines
- `infrastructure-as-code` — Infrastructure automation
- `observability-design` — Monitoring and alerting

**Commonly Followed By:**
- `incident-analysis` — Analyze deployment failures
- `production-readiness` — Validate deployment readiness
- `disaster-recovery` — Plan for deployment disasters

**Works With:**
- `capacity-planning` — Ensure resources for blue/green
- `performance-optimization` — Optimize deployment performance

---

## Additional Resources

**Documentation:**
- [SKILL.md](./SKILL.md) — Comprehensive skill documentation
- [instructions.md](./instructions.md) — Step-by-step workflow guide
- [examples.md](./examples.md) — Real-world implementation examples

**Books:**
- "Continuous Delivery" by Jez Humble and David Farley
- "Site Reliability Engineering" by Google

---

**Version:** 1.0.0  
**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 3-6 hours
