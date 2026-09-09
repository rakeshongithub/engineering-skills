# Backup and Recovery - Quick Reference

**Design and implement backup and recovery systems to protect data from loss, corruption, or deletion by defining backup strategies, retention policies, recovery procedures, and automation for reliable data protection.**

---

## Quick Start

### When to Use This Skill

✅ Designing backup strategy for new systems  
✅ Meeting compliance requirements (GDPR, HIPAA, SOC 2, PCI DSS)  
✅ Implementing data protection policies  
✅ Establishing RTO/RPO requirements  
✅ Automating backup processes  
✅ Implementing 3-2-1 backup rule  

### When NOT to Use

❌ For disaster recovery planning  
❌ For high availability design  
❌ For data replication  
❌ Without data inventory  
❌ For stateless applications  

---

## Backup Strategy Comparison

| Strategy | Speed | Storage | Recovery | Best For |
|----------|-------|---------|----------|----------|
| **Full Backup** | Slow | Large | Fast | Small databases, weekly archival |
| **Incremental Backup** | Fast | Small | Slow | Large databases, frequent backups |
| **Differential Backup** | Medium | Medium | Medium | Medium databases, daily backups |
| **Continuous Backup** | Real-time | Large | Fast | Mission-critical (RPO < 1 min) |
| **Snapshot Backup** | Fast | Efficient | Fast | VMs, block storage, EBS volumes |

---

## 3-2-1 Backup Rule

✅ **3 copies** of data (1 production + 2 backups)  
✅ **2 different media** (local storage + cloud, or disk + tape)  
✅ **1 offsite** backup (cross-region replication or offsite tape)  

---

## Retention Policy Matrix

| Data Type | Retention | Storage Tier | Compliance |
|-----------|-----------|--------------|------------|
| **Critical Databases** | 30 days + 1 year | Hot (7d) + Glacier (1y) | SOC 2, HIPAA |
| **Important Files** | 30 days | Hot (7d) + Cold (30d) | GDPR |
| **Normal Data** | 7 days | Hot (7d) | None |
| **Logs/Audit Trails** | 7 years | Glacier (7y) | PCI DSS, SOX |
| **Configurations** | 90 days | Hot (90d) | None |

---

## 10-Step Workflow

1. **Inventory Data Assets** (30-60 min) — Identify all data requiring backup
2. **Define Recovery Requirements** (30-45 min) — Establish RTO/RPO for each data asset
3. **Select Backup Strategy** (30-45 min) — Choose backup methods (full, incremental, continuous)
4. **Design Backup Schedule** (30-45 min) — Define backup frequency (hourly, daily, weekly)
5. **Define Retention Policies** (30-45 min) — Establish retention periods and tiered storage
6. **Implement Backup Automation** (60-120 min) — Automate backups, encryption, monitoring
7. **Implement 3-2-1 Backup Rule** (30-60 min) — Ensure 3 copies, 2 media, 1 offsite
8. **Create Recovery Runbooks** (45-90 min) — Document recovery procedures
9. **Test Backup Recovery** (60-120 min) — Perform test restores, validate data integrity
10. **Monitor and Optimize** (30-60 min) — Monitor backup health, optimize costs

**Total Time:** 4-8 hours

---

## Quick Examples

### Example 1: Continuous Backup for Database

**RTO:** 30 min | **RPO:** 5 min  
**Strategy:** AWS RDS automated backups + point-in-time recovery  
**Cost:** ~$50/month for 100GB database  

### Example 2: Snapshot-Based Backup for Infrastructure

**RTO:** 2 hours | **RPO:** 24 hours  
**Strategy:** AWS EBS snapshots + AMIs  
**Cost:** ~$20/month for 500GB EBS volume  

### Example 3: Incremental Backup for File Storage

**RTO:** 4 hours | **RPO:** 24 hours  
**Strategy:** AWS S3 + lifecycle policies  
**Cost:** ~$25/month for 1TB file storage  

### Example 4: Full Backup for Compliance

**RTO:** 7 days | **RPO:** 7 days  
**Strategy:** Weekly full backup to AWS Glacier  
**Cost:** ~$10/month for 500GB backup  

---

## Quality Checklist

### Planning
- [ ] Data inventory complete
- [ ] RTO/RPO defined for all data assets
- [ ] Backup strategy selected
- [ ] Retention policies defined
- [ ] Cost estimates approved

### Implementation
- [ ] Backup automation implemented
- [ ] Backup encryption enabled
- [ ] 3-2-1 backup rule implemented
- [ ] Monitoring and alerting configured

### Documentation
- [ ] Backup strategy document written
- [ ] Recovery runbooks created
- [ ] Compliance documentation complete
- [ ] Training materials prepared

### Testing
- [ ] Backup recovery tested
- [ ] Data integrity validated
- [ ] RTO/RPO requirements met
- [ ] Lessons learned documented

---

## Common Mistakes

❌ **No backup testing** — Backups fail when needed  
✅ **Test backup restoration monthly** — Validate data integrity

❌ **Single backup copy** — Vulnerable to corruption  
✅ **Implement 3-2-1 backup rule** — 3 copies, 2 media, 1 offsite

❌ **No offsite backup** — Vulnerable to site failure  
✅ **Cross-region replication** — Protect against regional outages

❌ **No backup encryption** — Vulnerable to data breach  
✅ **Enable encryption** — At rest and in transit

❌ **No backup monitoring** — Failures go unnoticed  
✅ **Implement monitoring and alerting** — Review daily

---

## Success Metrics

### Excellent
- RTO/RPO requirements met consistently
- Backup recovery tested monthly
- 3-2-1 backup rule implemented
- Automated backup and recovery
- Zero data loss in tests

### Good
- RTO/RPO requirements met most of the time
- Backup recovery tested quarterly
- 2 backup copies (missing offsite)
- Automated backup, manual recovery
- Minimal data loss in tests

### Needs Improvement
- RTO/RPO requirements not met
- Backup recovery never tested
- Single backup copy
- Manual backup and recovery
- Significant data loss in tests

---

## Related Skills

**Prerequisites:**
- `architecture-discovery` — Understand system architecture and data flows
- `infrastructure-as-code` — Automate backup infrastructure

**Commonly Followed By:**
- `disaster-recovery` — Implement DR using backups
- `production-readiness` — Validate backup readiness
- `compliance-audit` — Document backup compliance

**Works With:**
- `disaster-recovery` — DR strategy and failover
- `observability-design` — Monitor backup health

---

## Additional Resources

**Documentation:**
- [SKILL.md](./SKILL.md) — Comprehensive skill documentation
- [instructions.md](./instructions.md) — Step-by-step workflow guide
- [examples.md](./examples.md) — Real-world implementation examples

**Books:**
- "Backup & Recovery" by W. Curtis Preston
- "Site Reliability Engineering" by Google

---

**Version:** 1.0.0  
**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 4-8 hours