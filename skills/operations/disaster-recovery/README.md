# Disaster Recovery - Quick Reference

**Design and implement disaster recovery (DR) plans and procedures to ensure business continuity by defining recovery objectives, failover strategies, backup automation, and recovery procedures for catastrophic failures.**

---

## Quick Start

### When to Use This Skill

✅ Designing DR strategy for new systems  
✅ Meeting compliance requirements (SOC 2, HIPAA, PCI DSS)  
✅ Planning for multi-region deployments  
✅ Implementing business continuity plans  
✅ Preparing for catastrophic failures  
✅ Establishing RTO/RPO requirements  

### When NOT to Use

❌ For high availability (HA) design  
❌ For backup strategy only  
❌ For incident response  
❌ Without business impact analysis  
❌ For development or test environments  

---

## DR Strategy Comparison

| Strategy | RTO | RPO | Cost | Best For |
|----------|-----|-----|------|----------|
| **Backup/Restore** | Hours-Days | Hours | Very Low | Non-critical systems |
| **Pilot Light** | Hours | Minutes | Low | Important systems |
| **Warm Standby** | Minutes | Seconds | Medium | Critical systems (e-commerce, SaaS) |
| **Hot Standby** | Seconds | Near-zero | High (2x) | Mission-critical (payment, healthcare) |
| **Multi-Site Active/Active** | None | None | Very High (2x+) | Global services, zero-downtime |

---

## RTO/RPO Decision Matrix

| System Criticality | RTO | RPO | DR Strategy |
|--------------------|-----|-----|-------------|
| **Mission-Critical** | < 1 min | < 1 min | Hot Standby or Multi-Site |
| **Critical** | < 1 hour | < 15 min | Warm Standby |
| **Important** | < 4 hours | < 1 hour | Pilot Light |
| **Normal** | < 24 hours | < 24 hours | Backup/Restore |

---

## 10-Step Workflow

1. **Define RTO and RPO Requirements** (30-60 min) — Business impact analysis, recovery objectives
2. **Select DR Strategy** (30-45 min) — Backup/restore, pilot light, warm standby, hot standby, multi-site
3. **Design DR Architecture** (60-120 min) — DR site, data replication, network, DNS failover
4. **Implement Backup Automation** (60-90 min) — Automated backups, schedules, retention, encryption
5. **Implement Failover Mechanisms** (60-90 min) — DNS failover, load balancer, database failover
6. **Create Recovery Runbooks** (45-90 min) — Failover, recovery, failback, validation procedures
7. **Establish DR Testing Plan** (30-45 min) — Test scenarios, frequency, success criteria
8. **Implement Monitoring and Alerting** (30-60 min) — Backup monitoring, replication lag, DR site health
9. **Document and Train** (45-90 min) — DR plan, training materials, on-call rotation
10. **Test and Validate** (varies) — Execute DR test, measure RTO/RPO, document lessons learned

**Total Time:** 6-12 hours

---

## Quick Examples

### Example 1: E-commerce Platform (Warm Standby)

**RTO:** 15 minutes | **RPO:** 5 minutes  
**Strategy:** Warm standby in secondary region (50% capacity)  
**Failover:** Automatic via Route 53 health checks  
**Cost:** ~60% of primary infrastructure  

### Example 2: Payment Processing (Hot Standby)

**RTO:** 30 seconds | **RPO:** Near-zero  
**Strategy:** Hot standby with synchronous replication  
**Failover:** Instant via global load balancer  
**Cost:** ~100% of primary (2x total)  

### Example 3: SaaS Application (Pilot Light)

**RTO:** 2 hours | **RPO:** 30 minutes  
**Strategy:** Minimal DR infrastructure (database replica only)  
**Failover:** Manual provisioning + DNS update  
**Cost:** ~10% of primary infrastructure  

### Example 4: Internal Tools (Backup/Restore)

**RTO:** 24 hours | **RPO:** 24 hours  
**Strategy:** Daily backups to S3, restore on demand  
**Failover:** Manual infrastructure provisioning + restore  
**Cost:** Backup storage only (~$50/month)  

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

❌ **No DR testing** — DR plan fails when needed  
✅ **Test DR quarterly** — Document results, update plan

❌ **Unrealistic RTO/RPO** — Not achievable with current strategy  
✅ **Align RTO/RPO with DR strategy** — Upgrade if needed

❌ **No backup validation** — Backups corrupted, can't restore  
✅ **Test backup restoration monthly** — Validate backups work

❌ **Single region deployment** — Region outage causes total failure  
✅ **Multi-region for critical systems** — Deploy to multiple regions

❌ **No failback plan** — Can't return to primary site  
✅ **Document failback procedure** — Test regularly

---

## Success Metrics

### Excellent
- RTO/RPO requirements met consistently
- DR tested quarterly
- Automated failover and failback
- Multi-region deployment
- Zero data loss in tests

### Good
- RTO/RPO requirements met most of the time
- DR tested semi-annually
- Manual failover, automated backup
- Single DR region
- Minimal data loss in tests

### Needs Improvement
- RTO/RPO requirements not met
- DR never tested or tested annually
- Manual failover and recovery
- No DR infrastructure
- Significant data loss in tests

---

## Related Skills

**Prerequisites:**
- `architecture-discovery` — Understand system architecture
- `observability-design` — Monitoring and alerting
- `infrastructure-as-code` — Automate DR infrastructure

**Commonly Followed By:**
- `incident-analysis` — Analyze DR events
- `production-readiness` — Validate DR readiness
- `capacity-planning` — Plan DR capacity

**Works With:**
- `backup-recovery` — Backup strategy
- `reliability-analysis` — High availability design

---

## Additional Resources

**Documentation:**
- [SKILL.md](./SKILL.md) — Comprehensive skill documentation
- [instructions.md](./instructions.md) — Step-by-step workflow guide
- [examples.md](./examples.md) — Real-world implementation examples

**Books:**
- "Site Reliability Engineering" by Google
- "Designing Data-Intensive Applications" by Martin Kleppmann

---

**Version:** 1.0.0  
**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 6-12 hours
