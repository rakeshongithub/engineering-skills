# Backup and Recovery Strategy Design

> **Design, validate, and optimize comprehensive backup and disaster recovery strategies for modern software systems**

## Quick Overview

**Category**: Operations & Infrastructure  
**Complexity**: Intermediate to Advanced  
**Time Estimate**: 3-8 hours  
**Priority**: MEDIUM

### Purpose

This skill enables AI agents to analyze, design, and implement robust backup and disaster recovery strategies that protect critical data assets, ensure business continuity, meet regulatory compliance requirements, and optimize costs across diverse technology stacks.

### Key Capabilities

- ✅ Assess current backup infrastructure and identify gaps
- ✅ Define RTO/RPO targets aligned with business needs
- ✅ Design multi-tier backup architectures (3-2-1-1-0 rule)
- ✅ Implement disaster recovery strategies for various failure scenarios
- ✅ Configure backup automation and verification workflows
- ✅ Establish compliance controls (GDPR, HIPAA, SOX, PCI DSS)
- ✅ Optimize backup costs through intelligent tiering
- ✅ Conduct recovery testing and DR drills
- ✅ Monitor backup operations and implement alerting

---

## When to Use This Skill

### Ideal Scenarios

- 🚨 **New Infrastructure**: Designing backup strategy for greenfield projects
- 🔍 **Gap Assessment**: Evaluating and improving existing backup solutions
- 📈 **Scaling Up**: Adapting backup strategy for growth (data volume, users, regions)
- ⚖️ **Compliance Requirements**: Meeting new regulatory obligations (GDPR, HIPAA, etc.)
- 💥 **Post-Incident**: Improving backup/DR after experiencing data loss or outage
- 💰 **Cost Optimization**: Reducing backup infrastructure costs
- ☁️ **Cloud Migration**: Transitioning backup strategy to cloud or hybrid model
- 🌍 **Multi-Region Expansion**: Implementing geographic disaster recovery

### Not Suitable For

- ❌ Simple file backups (use standard backup tools directly)
- ❌ One-time backup tasks (this skill focuses on comprehensive strategies)
- ❌ Application-specific backup procedures (consult application documentation)

---

## Core Concepts

### RTO and RPO

**Recovery Time Objective (RTO)**
- Maximum acceptable downtime for service restoration
- Measured from incident detection to full service availability
- Example: "Database must be restored within 4 hours"

**Recovery Point Objective (RPO)**
- Maximum acceptable data loss measured in time
- Determines backup frequency and replication strategy
- Example: "Maximum 15 minutes of transaction data loss acceptable"

### 3-2-1-1-0 Backup Rule

- **3 copies** of data: Production + 2 backups
- **2 different media types**: Disk, tape, cloud, etc.
- **1 offsite copy**: Geographic separation for disaster recovery
- **1 offline/immutable copy**: Air-gapped or write-once storage (ransomware protection)
- **0 errors**: Verified backup integrity through testing

### Backup Types

| Type | Description | Backup Window | Recovery Time | Use Case |
|------|-------------|---------------|---------------|----------|
| **Full** | Complete copy of all data | Long | Fast | Weekly/monthly |
| **Incremental** | Changes since last backup | Short | Slower | Daily/hourly |
| **Differential** | Changes since last full | Medium | Medium | Daily |
| **Continuous** | Real-time replication | Minimal | Fastest | Mission-critical |

### Storage Tiers

```
┌────────────────────────────────────────────────────────────┐
│                    STORAGE TIER STRATEGY                    │
└────────────────────────────────────────────────────────────┘

HOT (0-7 days)        WARM (8-30 days)      COLD (31-365 days)    ARCHIVE (>365 days)
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ NVMe SSD         │  │ SSD Object Store│  │ HDD Object Store│  │ Glacier Deep    │
│ Immediate access │  │ Minutes access  │  │ Hours access    │  │ Archive         │
│ $0.50/GB/month   │  │ $0.15/GB/month  │  │ $0.05/GB/month  │  │ 12-hour retrieval│
│                  │  │                 │  │                 │  │ $0.002/GB/month │
└──────────────────┘  └──────────────────┘  └──────────────────┘  └──────────────────┘
```

---

## Quick Start Guide

### 5-Step Implementation

#### 1. Assess Current State (1-2 weeks)

```bash
# Inventory data assets
- List all databases, file systems, applications
- Classify by criticality (Tier 1-4)
- Calculate total data footprint and growth rate

# Define business requirements
- Conduct business impact analysis
- Define RTO/RPO targets per application
- Document compliance requirements

# Evaluate existing backups
- Review current backup architecture
- Test recovery capabilities
- Identify gaps and risks
```

**Deliverables**: Data inventory, RTO/RPO matrix, gap analysis

---

#### 2. Design Strategy (2-3 weeks)

```yaml
# Design backup architecture
backup_strategy:
  tier_1_critical:
    method: "Continuous Data Protection"
    rto: "4 hours"
    rpo: "15 minutes"
    storage: "Hot + Warm + Cold + Archive"
    geographic: "Primary + DR + Tertiary"
  
  tier_2_important:
    method: "Incremental backups"
    rto: "8 hours"
    rpo: "1 hour"
  
  tier_3_standard:
    method: "Daily backups"
    rto: "24 hours"
    rpo: "24 hours"

# Design DR strategy
dr_strategy:
  scenarios:
    - "Datacenter failure"
    - "Regional disaster"
    - "Ransomware attack"
    - "Data corruption"
  
  failover_architecture:
    primary: "Active (us-east-1)"
    secondary: "Hot Standby (us-west-2)"
    tertiary: "Cold Standby (eu-west-1)"
```

**Deliverables**: Backup architecture, DR plan, security controls

---

#### 3. Implement Automation (1-2 weeks)

```python
# Example: Automated backup orchestration
import boto3
import schedule

class BackupOrchestrator:
    def __init__(self):
        self.rds = boto3.client('rds')
    
    def backup_database(self, db_id, backup_type):
        snapshot_id = f"{db_id}-{backup_type}-{timestamp}"
        
        # Create snapshot
        self.rds.create_db_snapshot(
            DBSnapshotIdentifier=snapshot_id,
            DBInstanceIdentifier=db_id
        )
        
        # Copy to DR region
        self.copy_to_dr_region(snapshot_id)
        
        # Apply retention policy
        self.apply_retention_policy(db_id, backup_type)
        
        # Verify backup
        self.verify_snapshot(snapshot_id)

# Schedule backups
schedule.every().hour.do(backup_database, 'prod-db', 'hourly')
schedule.every().day.at("02:00").do(backup_database, 'prod-db', 'daily')
```

**Deliverables**: Automation scripts, lifecycle policies, monitoring

---

#### 4. Test and Validate (2-3 weeks)

```bash
# Recovery testing schedule
Weekly:   Single file/database recovery
Monthly:  Full application recovery
Quarterly: Disaster recovery drill
Annually:  Full-scale DR exercise

# Test procedure
1. Select backup to restore
2. Provision recovery environment
3. Execute restore
4. Validate data integrity
5. Measure RTO/RPO
6. Document results
7. Update runbooks
```

**Deliverables**: Test results, updated runbooks, action items

---

#### 5. Monitor and Optimize (Ongoing)

```yaml
# Key metrics to monitor
metrics:
  operational:
    - "Backup success rate (target: >99%)"
    - "Recovery success rate (target: 100%)"
    - "Backup window compliance"
  
  performance:
    - "Actual RTO vs target"
    - "Actual RPO vs target"
    - "Mean time to recovery"
  
  efficiency:
    - "Deduplication ratio (target: >10:1)"
    - "Compression ratio (target: >2:1)"
    - "Storage utilization"
  
  cost:
    - "Cost per GB protected"
    - "Monthly storage cost trend"
  
  compliance:
    - "Retention compliance (target: 100%)"
    - "Encryption compliance (target: 100%)"
```

**Deliverables**: Dashboards, cost reports, optimization recommendations

---

## Key Metrics and Targets

| Metric | Target | Measurement |
|--------|--------|-------------|
| **Backup Success Rate** | >99% | (Successful backups / Total jobs) × 100 |
| **Recovery Success Rate** | 100% | (Successful recoveries / Total attempts) × 100 |
| **RTO Achievement** | <Target RTO | Measured recovery time vs. target |
| **RPO Achievement** | <Target RPO | Data loss window vs. target |
| **Deduplication Ratio** | >10:1 | Logical data size / Physical storage |
| **Cost per GB** | Decreasing | Total monthly cost / Total GB protected |
| **Retention Compliance** | 100% | (Compliant backups / Total backups) × 100 |
| **Encryption Compliance** | 100% | (Encrypted backups / Total backups) × 100 |

---

## Common Challenges and Solutions

### Challenge: Meeting Aggressive RTO/RPO Targets

**Problem**: Business requires near-zero RTO and RPO

**Solutions**:
- Implement Continuous Data Protection (CDP)
- Deploy active-active architecture
- Use instant recovery technologies (snapshots, clones)
- Pre-provision recovery infrastructure

---

### Challenge: Backup Storage Costs Spiraling

**Problem**: Costs growing faster than data volume

**Solutions**:
- Implement granular retention policies
- Enable aggressive deduplication and compression
- Use intelligent storage tiering (hot/warm/cold/archive)
- Review and optimize backup frequency

---

### Challenge: Ransomware Encrypting Backups

**Problem**: Ransomware spreading to backup systems

**Solutions**:
- Implement immutable backups (S3 Object Lock, compliance mode)
- Maintain air-gapped backup copies
- Network segmentation for backup infrastructure
- Zero-trust access controls with MFA

---

### Challenge: Recovery Testing Disrupting Production

**Problem**: Can't test recovery without impacting live systems

**Solutions**:
- Use isolated recovery environments
- Clone production for testing (Aurora cloning, VM snapshots)
- Implement chaos engineering practices
- Automate test provisioning and teardown

---

## Tools and Technologies

### Enterprise Backup Platforms
- **Veeam Backup & Replication**: VMware/Hyper-V, cloud integration
- **Commvault**: Multi-cloud, enterprise scale
- **Rubrik**: Modern architecture, instant recovery
- **Veritas NetBackup**: Enterprise-grade, legacy support

### Cloud-Native Backup
- **AWS Backup**: Centralized AWS backup management
- **Azure Backup**: Native Azure integration
- **Google Cloud Backup and DR**: GCP workloads

### Open-Source Tools
- **Restic**: Fast, secure, cloud-agnostic
- **Bacula**: Enterprise features, network backup
- **Duplicati**: User-friendly, encryption, cloud support

### Disaster Recovery
- **VMware Site Recovery Manager**: VMware DR orchestration
- **AWS Elastic Disaster Recovery**: Continuous replication to AWS
- **Azure Site Recovery**: Azure and on-premises DR
- **Zerto**: Continuous data protection, near-zero RPO

---

## Best Practices Checklist

### Backup Strategy
- ☐ Follow 3-2-1-1-0 rule (3 copies, 2 media, 1 offsite, 1 immutable, 0 errors)
- ☐ Automate backup execution and verification
- ☐ Encrypt all backups (at rest and in transit)
- ☐ Implement immutable backups for ransomware protection
- ☐ Use intelligent storage tiering to optimize costs

### Disaster Recovery
- ☐ Document recovery runbooks for all DR scenarios
- ☐ Automate failover and recovery workflows
- ☐ Maintain geographic separation (500+ miles)
- ☐ Test recovery procedures regularly (weekly/monthly/quarterly)
- ☐ Implement tiered recovery (prioritize critical systems)

### Security and Compliance
- ☐ Apply principle of least privilege for backup access
- ☐ Enable comprehensive audit logging
- ☐ Validate compliance controls (GDPR, HIPAA, SOX, PCI DSS)
- ☐ Conduct regular security assessments
- ☐ Maintain chain of custody for backup data

### Monitoring and Optimization
- ☐ Monitor backup success rates and alert on failures
- ☐ Track RTO/RPO achievement
- ☐ Analyze cost trends and optimize storage tiers
- ☐ Review and update retention policies
- ☐ Conduct quarterly performance reviews

---

## Related Skills

- **Infrastructure Monitoring**: Monitor backup infrastructure health
- **Cloud Infrastructure Management**: Manage cloud backup services
- **Database Administration**: Database-specific backup procedures
- **Security and Compliance**: Implement encryption and meet regulations
- **Incident Response**: Coordinate disaster recovery activations

---

## Learning Resources

### Documentation
- [SKILL.md](./SKILL.md) - Comprehensive skill documentation (16,000+ words)
- [instructions.md](./instructions.md) - Step-by-step implementation workflow
- [examples.md](./examples.md) - Real-world implementation examples

### External Resources
- **ISO 22301** - Business Continuity Management standard
- **NIST SP 800-34** - Contingency Planning Guide for IT Systems
- **AWS Well-Architected Framework** - Reliability pillar (backup and DR)
- **"Backup & Recovery" by W. Curtis Preston** - Comprehensive book

### Certifications
- AWS Certified Solutions Architect (backup and DR modules)
- Microsoft Azure Administrator (Azure Backup and Site Recovery)
- Veeam Certified Engineer (VMCE)

---

## Success Criteria

You've successfully implemented this skill when:

✅ **RTO/RPO targets consistently met** (>95% of recovery operations)  
✅ **Backup success rate >99%** with automated alerting  
✅ **Recovery tests passing** (weekly/monthly/quarterly schedule)  
✅ **100% compliance** with regulatory requirements (GDPR, HIPAA, etc.)  
✅ **Cost optimized** through intelligent tiering and lifecycle management  
✅ **Zero critical audit findings** in compliance assessments  
✅ **Comprehensive documentation** (runbooks, architecture diagrams, policies)  
✅ **Team trained** on backup operations and disaster recovery procedures  

---

## Support and Contribution

**Questions or Issues?**
- Review the comprehensive [SKILL.md](./SKILL.md) documentation
- Check [examples.md](./examples.md) for real-world implementations
- Follow [instructions.md](./instructions.md) for step-by-step guidance

**Feedback Welcome**
- Share your implementation experiences
- Suggest improvements or additional examples
- Report issues or gaps in documentation

---

**Skill Version**: 1.0.0  
**Last Updated**: 2024-01-15  
**Complexity**: Intermediate to Advanced  
**Estimated Time**: 3-8 hours for comprehensive strategy design

---

## Quick Reference Commands

### AWS Backup
```bash
# List backup jobs
aws backup list-backup-jobs --max-results 100

# Create backup plan
aws backup create-backup-plan --backup-plan file://plan.json

# Start restore job
aws backup start-restore-job \
  --recovery-point-arn <arn> \
  --iam-role-arn <role-arn>
```

### Azure Backup
```bash
# Enable backup
az backup protection enable-for-vm \
  --resource-group myRG \
  --vault-name myVault \
  --vm myVM \
  --policy-name DefaultPolicy

# List recovery points
az backup recoverypoint list \
  --resource-group myRG \
  --vault-name myVault \
  --container-name myVM \
  --item-name myVM
```

### RDS Snapshots
```bash
# Create snapshot
aws rds create-db-snapshot \
  --db-snapshot-identifier mySnapshot \
  --db-instance-identifier myDB

# Restore from snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier myRestoredDB \
  --db-snapshot-identifier mySnapshot
```

---

**Ready to get started?** Begin with [SKILL.md](./SKILL.md) for comprehensive guidance, then follow [instructions.md](./instructions.md) for step-by-step implementation.
