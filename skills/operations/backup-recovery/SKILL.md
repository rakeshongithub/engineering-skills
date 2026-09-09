# Backup and Recovery

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 4-8 hours

---

## Purpose

Design and implement backup and recovery systems to protect data from loss, corruption, or deletion by defining backup strategies, retention policies, recovery procedures, and automation for reliable data protection.

---

## When to Use

- Designing backup strategy for new systems or databases
- Improving existing backup capabilities (slow recovery, incomplete backups)
- Meeting compliance requirements (GDPR, HIPAA, SOC 2, PCI DSS)
- Implementing data protection policies
- Planning for data loss prevention
- Establishing recovery time objectives (RTO) and recovery point objectives (RPO)
- Testing and validating backup restoration
- Automating backup processes
- Implementing 3-2-1 backup rule (3 copies, 2 different media, 1 offsite)

---

## When NOT to Use

- **For disaster recovery planning** — use disaster-recovery skill instead
- **For high availability design** — use reliability-analysis skill
- **For data replication** — use disaster-recovery skill
- **Without data inventory** — understand what data needs backup first
- **For stateless applications** — no data to backup
- **Without recovery testing** — backups without tested recovery are useless
- **For real-time replication** — use disaster-recovery with synchronous replication

---

## Inputs

### Required

- **Data inventory** — databases, file systems, object storage, configurations
- **Recovery requirements** — RTO (recovery time), RPO (data loss tolerance)
- **Data criticality** — which data is mission-critical vs. nice-to-have
- **Compliance requirements** — regulatory backup and retention requirements
- **Storage infrastructure** — available backup storage (S3, EBS, Glacier, tape)
- **Current backup capabilities** — existing backup systems, if any

### Optional

- **Budget constraints** — backup storage and operational costs
- **Data growth rate** — projected data growth for capacity planning
- **Geographic requirements** — data residency, offsite backup requirements
- **Retention policies** — how long to retain backups (7 days, 30 days, 1 year)
- **Encryption requirements** — backup encryption at rest and in transit
- **Team capabilities** — skills, tools, automation capabilities

---

## Expected Outputs

### Primary Deliverables

1. **Backup Strategy Document**
   - Backup methods (full, incremental, differential, continuous)
   - Backup schedules (hourly, daily, weekly, monthly)
   - Retention policies (7 days, 30 days, 1 year, 7 years)
   - Recovery procedures
   - 3-2-1 backup rule implementation

2. **Backup Architecture**
   - Backup infrastructure (storage, tools, automation)
   - Backup workflows (data → backup → storage → retention)
   - Encryption configuration (at rest, in transit)
   - Network configuration (bandwidth, compression)

3. **Recovery Runbooks**
   - Step-by-step recovery procedures
   - Recovery validation procedures
   - Troubleshooting guides
   - Recovery time estimates

4. **Backup Automation**
   - Automated backup scripts
   - Backup monitoring and alerting
   - Backup validation automation
   - Retention policy automation

### Supporting Artifacts

- **Backup testing plan** — test scenarios, frequency, success criteria
- **Cost estimates** — backup storage and operational costs
- **Compliance documentation** — backup compliance evidence
- **Training materials** — backup and recovery training for team

---

## Workflow

### Step 1: Inventory Data Assets

**Objective:** Identify all data that requires backup.

**Actions:**
- Identify databases (RDS, PostgreSQL, MySQL, MongoDB)
- Identify file systems (EBS volumes, NFS, local storage)
- Identify object storage (S3 buckets, Blob storage)
- Identify configurations (infrastructure as code, application configs)
- Identify logs and audit trails
- Categorize data by criticality (critical, important, normal)

**Quality Check:**
- [ ] All data sources identified
- [ ] Data categorized by criticality
- [ ] Data sizes documented
- [ ] Data growth rates estimated
- [ ] Data ownership documented
- [ ] Compliance requirements identified

### Step 2: Define Recovery Requirements

**Objective:** Establish RTO and RPO for each data asset.

**Actions:**
- Define RTO per data asset (time to recover)
- Define RPO per data asset (acceptable data loss)
- Align RTO/RPO with business requirements
- Document financial impact of data loss
- Obtain stakeholder approval

**Quality Check:**
- [ ] RTO defined for each data asset
- [ ] RPO defined for each data asset
- [ ] Business impact documented
- [ ] Stakeholder approval obtained

### Step 3: Select Backup Strategy

**Objective:** Choose appropriate backup methods.

**Actions:**
- Evaluate backup methods:
  - **Full Backup:** Complete copy of all data (slow, large storage)
  - **Incremental Backup:** Only changed data since last backup (fast, efficient)
  - **Differential Backup:** Changed data since last full backup (medium speed)
  - **Continuous Backup:** Real-time backup (near-zero RPO, high cost)
  - **Snapshot Backup:** Point-in-time copy (fast, storage-efficient)
- Match backup method to RTO/RPO requirements
- Consider cost vs. recovery time trade-offs

**Quality Check:**
- [ ] Backup methods evaluated
- [ ] Backup method selected per data asset
- [ ] Cost vs. recovery time analyzed
- [ ] Decision documented with rationale

### Step 4: Design Backup Schedule

**Objective:** Define backup frequency and timing.

**Actions:**
- Define backup schedules:
  - **Hourly:** Critical databases (RPO < 1 hour)
  - **Daily:** Important data (RPO < 24 hours)
  - **Weekly:** Normal data (RPO < 7 days)
  - **Monthly:** Archival data (RPO < 30 days)
- Schedule backups during low-traffic periods
- Implement backup windows (maintenance windows)
- Plan for backup duration and resource usage

**Quality Check:**
- [ ] Backup schedules defined
- [ ] Backup windows planned
- [ ] Resource usage estimated
- [ ] Backup duration calculated

### Step 5: Define Retention Policies

**Objective:** Establish how long to retain backups.

**Actions:**
- Define retention policies:
  - **Short-term:** 7 days (operational recovery)
  - **Medium-term:** 30 days (compliance, audit)
  - **Long-term:** 1-7 years (regulatory compliance)
- Implement tiered storage (hot, warm, cold, archive)
- Plan for backup deletion and cleanup
- Document compliance requirements (GDPR, HIPAA, SOC 2)

**Quality Check:**
- [ ] Retention policies defined
- [ ] Tiered storage planned
- [ ] Compliance requirements met
- [ ] Cleanup automation planned

### Step 6: Implement Backup Automation

**Objective:** Automate backup processes.

**Actions:**
- Implement automated backups (cron, AWS Backup, scripts)
- Configure backup tools (mysqldump, pg_dump, AWS Backup)
- Implement backup encryption (at rest, in transit)
- Set up backup monitoring and alerting
- Automate backup validation (test restore)
- Implement backup reporting (success/failure, size, duration)

**Quality Check:**
- [ ] Automated backups implemented
- [ ] Backup encryption enabled
- [ ] Monitoring and alerting configured
- [ ] Backup validation automated
- [ ] Reporting implemented

### Step 7: Implement 3-2-1 Backup Rule

**Objective:** Ensure backup resilience.

**Actions:**
- **3 copies:** Production data + 2 backups
- **2 different media:** Local storage + cloud storage (or disk + tape)
- **1 offsite:** Cross-region replication or offsite tape storage
- Implement cross-region replication (S3, Azure Blob)
- Verify backup copies are independent

**Quality Check:**
- [ ] 3 copies of data maintained
- [ ] 2 different storage media used
- [ ] 1 offsite backup configured
- [ ] Backup independence verified

### Step 8: Create Recovery Runbooks

**Objective:** Document recovery procedures.

**Actions:**
- Write recovery runbooks (step-by-step procedures)
- Document recovery validation procedures
- Create troubleshooting guides
- Include recovery time estimates
- Document contact information (on-call, escalation)

**Quality Check:**
- [ ] Recovery runbooks written
- [ ] Validation procedures documented
- [ ] Troubleshooting guides created
- [ ] Recovery time estimates included
- [ ] Contact information documented

### Step 9: Test Backup Recovery

**Objective:** Validate backups are restorable.

**Actions:**
- Perform test restores (monthly or quarterly)
- Validate data integrity after restore
- Measure recovery time (actual vs. RTO)
- Document test results and lessons learned
- Update runbooks based on test findings

**Quality Check:**
- [ ] Test restores performed
- [ ] Data integrity validated
- [ ] Recovery time measured
- [ ] Test results documented
- [ ] Runbooks updated

### Step 10: Monitor and Optimize

**Objective:** Ensure backup health and optimize costs.

**Actions:**
- Monitor backup success/failure rates
- Monitor backup storage usage and costs
- Optimize backup schedules and retention
- Implement backup compression and deduplication
- Review and update backup strategy quarterly

**Quality Check:**
- [ ] Backup monitoring implemented
- [ ] Storage costs tracked
- [ ] Backup strategy optimized
- [ ] Compression and deduplication enabled
- [ ] Quarterly reviews scheduled

---

## Decision Framework

### Backup Method Selection

**Full Backup:**
- **When:** Weekly or monthly backups
- **Pros:** Simple, complete copy, easy restore
- **Cons:** Slow, large storage, high bandwidth
- **Use for:** Small databases, weekly archival

**Incremental Backup:**
- **When:** Daily or hourly backups
- **Pros:** Fast, efficient storage, low bandwidth
- **Cons:** Complex restore (need all incrementals), slower recovery
- **Use for:** Large databases, frequent backups

**Differential Backup:**
- **When:** Daily backups
- **Pros:** Faster restore than incremental, efficient storage
- **Cons:** Larger than incremental, slower than full
- **Use for:** Medium databases, daily backups

**Continuous Backup:**
- **When:** Mission-critical data (RPO < 1 minute)
- **Pros:** Near-zero RPO, real-time protection
- **Cons:** High cost, high resource usage
- **Use for:** Financial transactions, payment processing

**Snapshot Backup:**
- **When:** Virtual machines, block storage
- **Pros:** Fast, storage-efficient, point-in-time
- **Cons:** Limited to snapshot-capable systems
- **Use for:** EC2 instances, EBS volumes, VMs

### Retention Policy Matrix

| Data Type | Retention | Storage Tier | Compliance |
|-----------|-----------|--------------|------------|
| **Critical Databases** | 30 days + 1 year archive | Hot (7 days) + Glacier (1 year) | SOC 2, HIPAA |
| **Important Files** | 30 days | Hot (7 days) + Cold (30 days) | GDPR |
| **Normal Data** | 7 days | Hot (7 days) | None |
| **Logs/Audit Trails** | 7 years | Glacier (7 years) | PCI DSS, SOX |
| **Configurations** | 90 days | Hot (90 days) | None |

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

### 1. No Backup Testing

**Problem:** Backups never tested, fail when needed.

**Solution:** Test backup restoration monthly, validate data integrity.

### 2. Single Backup Copy

**Problem:** Only one backup copy, vulnerable to corruption or deletion.

**Solution:** Implement 3-2-1 backup rule (3 copies, 2 media, 1 offsite).

### 3. No Offsite Backup

**Problem:** All backups in same location, vulnerable to site failure.

**Solution:** Implement cross-region replication or offsite tape storage.

### 4. Inadequate Retention

**Problem:** Backups deleted too soon, can't recover from old corruption.

**Solution:** Define retention policies based on compliance and business needs.

### 5. No Backup Encryption

**Problem:** Backups unencrypted, vulnerable to data breach.

**Solution:** Enable encryption at rest and in transit for all backups.

### 6. Backup Without Monitoring

**Problem:** Backup failures go unnoticed, no backups when needed.

**Solution:** Implement backup monitoring and alerting, review daily.

### 7. Slow Recovery

**Problem:** Backup restoration takes too long, exceeds RTO.

**Solution:** Test recovery time, optimize backup method and storage.

### 8. No Automation

**Problem:** Manual backups, prone to human error and missed backups.

**Solution:** Automate all backup processes, use backup tools.

### 9. Ignoring Costs

**Problem:** Backup storage costs exceed budget.

**Solution:** Implement tiered storage, compression, deduplication, retention cleanup.

### 10. Stale Backup Documentation

**Problem:** Backup procedures outdated, don't reflect current architecture.

**Solution:** Update backup documentation after every architecture change.

---

## Examples

### Example 1: Continuous Backup for Database

**RTO:** 30 minutes  
**RPO:** 5 minutes  
**Strategy:** AWS RDS automated backups + point-in-time recovery

**Configuration:**
- Automated daily snapshots (retained 30 days)
- Transaction log backups every 5 minutes
- Point-in-time recovery to any second within retention period
- Cross-region snapshot copy (us-east-1 → us-west-2)

**Cost:** ~$50/month for 100GB database

### Example 2: Snapshot-Based Backup for Infrastructure

**RTO:** 2 hours  
**RPO:** 24 hours  
**Strategy:** AWS EBS snapshots + AMIs

**Configuration:**
- Daily EBS snapshots (retained 7 days)
- Weekly AMI creation (retained 30 days)
- Automated snapshot lifecycle management
- Cross-region snapshot copy

**Cost:** ~$20/month for 500GB EBS volume

### Example 3: Incremental Backup for File Storage

**RTO:** 4 hours  
**RPO:** 24 hours  
**Strategy:** AWS S3 + lifecycle policies

**Configuration:**
- S3 versioning enabled
- Lifecycle policy: transition to Glacier after 30 days
- Cross-region replication (us-east-1 → us-west-2)
- MFA delete protection

**Cost:** ~$25/month for 1TB file storage

### Example 4: Full Backup for Compliance

**RTO:** 7 days  
**RPO:** 7 days  
**Strategy:** Weekly full backup to AWS Glacier

**Configuration:**
- Weekly full database dump
- Upload to S3, immediate transition to Glacier
- Retained for 7 years (compliance requirement)
- Vault lock for immutability

**Cost:** ~$10/month for 500GB backup

---

## Related Skills

### Prerequisites
- **architecture-discovery** — Understand system architecture and data flows
- **infrastructure-as-code** — Automate backup infrastructure

### Commonly Followed By
- **disaster-recovery** — Implement DR using backups
- **production-readiness** — Validate backup readiness
- **compliance-audit** — Document backup compliance

### Related Skills
- **disaster-recovery** — DR strategy and failover
- **observability-design** — Monitor backup health
- **security-architecture-review** — Backup encryption and access control

---

## Skill Composition

### Data Protection Workflow

```
architecture-discovery (understand data)
      ↓
backup-recovery (this skill)
      ↓
disaster-recovery (DR strategy)
      ↓
observability-design (monitor backups)
      ↓
production-readiness (validate readiness)
```

---

## Evaluation Criteria

### Excellent
- RTO/RPO requirements met consistently
- Backup recovery tested monthly
- 3-2-1 backup rule implemented
- Automated backup and recovery
- Zero data loss in tests
- Comprehensive documentation
- Backup encryption enabled

### Good
- RTO/RPO requirements met most of the time
- Backup recovery tested quarterly
- 2 backup copies (missing offsite)
- Automated backup, manual recovery
- Minimal data loss in tests
- Basic documentation

### Needs Improvement
- RTO/RPO requirements not met
- Backup recovery never tested or tested annually
- Single backup copy
- Manual backup and recovery
- Significant data loss in tests
- Outdated or missing documentation

---

## Tags

`operations`, `backup`, `recovery`, `data-protection`, `rto`, `rpo`, `3-2-1-backup`, `disaster-recovery`, `compliance`, `automation`, `retention`, `encryption`

---

## Version

**1.0.0** — Initial release