# Backup and Recovery - Step-by-Step Instructions

This guide provides detailed instructions for designing and implementing backup and recovery systems to protect data from loss, corruption, or deletion.

**Estimated Time:** 4-8 hours  
**Complexity:** Advanced  
**Prerequisites:** Architecture discovery, infrastructure as code

---

## Overview

Backup and recovery is critical for protecting data and ensuring business continuity. This skill covers how to inventory data assets, define recovery requirements (RTO/RPO), select backup strategies, implement automation, and test recovery procedures.

**Key Outcomes:**
- Complete data inventory with criticality assessment
- Clear RTO/RPO requirements for all data assets
- Appropriate backup strategy selected (full, incremental, differential, continuous, snapshot)
- Automated backup and recovery procedures
- Regular backup testing and validation
- 3-2-1 backup rule implementation
- Comprehensive backup documentation

---

## Step 1: Inventory Data Assets (30-60 minutes)

### Objective

Identify all data that requires backup and categorize by criticality.

### Actions

#### 1.1 Identify Data Sources

**Database Systems:**
- Relational databases (PostgreSQL, MySQL, SQL Server)
- NoSQL databases (MongoDB, DynamoDB, Cassandra)
- Data warehouses (Redshift, Snowflake, BigQuery)
- In-memory databases (Redis, Memcached)

**File Systems:**
- Application servers (EBS volumes, local storage)
- File servers (NFS, SMB, EFS)
- User-generated content (uploads, documents)
- Build artifacts and deployment packages

**Object Storage:**
- S3 buckets, Azure Blob Storage, Google Cloud Storage
- Media files (images, videos, audio)
- Document storage
- Backup archives

**Configurations:**
- Infrastructure as code (Terraform, CloudFormation)
- Application configurations (YAML, JSON, environment variables)
- Secrets and credentials (encrypted)
- CI/CD pipeline configurations

**Logs and Audit Trails:**
- Application logs
- Security logs
- Audit trails (compliance)
- Access logs

**Document:**
```markdown
## Data Asset Inventory

### Databases
1. **Production PostgreSQL**
   - Location: RDS us-east-1
   - Size: 500GB
   - Growth Rate: 50GB/month
   - Criticality: Mission-Critical
   
2. **Analytics MongoDB**
   - Location: EC2 cluster us-east-1
   - Size: 2TB
   - Growth Rate: 200GB/month
   - Criticality: Important

### File Systems
1. **Application EBS Volumes**
   - Location: EC2 instances us-east-1
   - Size: 1TB total
   - Growth Rate: 100GB/month
   - Criticality: Critical

### Object Storage
1. **User Uploads S3 Bucket**
   - Location: s3://company-uploads-prod
   - Size: 5TB
   - Growth Rate: 500GB/month
   - Criticality: Critical
```

#### 1.2 Categorize Data by Criticality

**Criticality Tiers:**
- **Mission-Critical:** Data loss unacceptable (payment transactions, customer data)
- **Critical:** Minimal data loss acceptable (orders, user-generated content)
- **Important:** Some data loss acceptable (analytics, logs)
- **Normal:** Significant data loss acceptable (temporary files, caches)

**Document:**
```markdown
## Data Criticality Assessment

**Mission-Critical:**
- Production PostgreSQL (payment transactions)
- Customer database
- Financial records

**Critical:**
- User uploads S3 bucket
- Application EBS volumes
- Order history database

**Important:**
- Analytics MongoDB
- Application logs (30 days)
- Build artifacts

**Normal:**
- Temporary files
- Cache data
- Development databases
```

#### 1.3 Document Data Sizes and Growth Rates

**Current State:**
- Total data size: 8.5TB
- Monthly growth: 850GB
- Annual growth: 10.2TB

**Projected State (1 year):**
- Total data size: 18.7TB
- Storage cost impact: Plan for 2.2x growth

### Quality Checklist

- [ ] All data sources identified (databases, file systems, object storage, configurations, logs)
- [ ] Data categorized by criticality (mission-critical, critical, important, normal)
- [ ] Data sizes documented
- [ ] Data growth rates estimated
- [ ] Data ownership documented
- [ ] Compliance requirements identified (GDPR, HIPAA, SOC 2, PCI DSS)

### Common Mistakes

❌ **Incomplete inventory** — Missing critical data sources  
✅ **Comprehensive discovery** — Use automated tools to discover all data

❌ **No criticality assessment** — Treat all data equally  
✅ **Risk-based prioritization** — Focus backup resources on critical data

---

## Step 2: Define Recovery Requirements (30-45 minutes)

### Objective

Establish RTO (Recovery Time Objective) and RPO (Recovery Point Objective) for each data asset.

### Actions

#### 2.1 Define RTO per Data Asset

**RTO (Recovery Time Objective):** Maximum acceptable downtime

**RTO Categories:**
- **Near-Instant:** < 1 minute (mission-critical systems)
- **Fast:** < 1 hour (critical systems)
- **Moderate:** < 4 hours (important systems)
- **Slow:** < 24 hours (normal systems)

**Document:**
```markdown
## RTO Requirements

| Data Asset | Criticality | RTO | Rationale |
|------------|-------------|-----|----------|
| Production PostgreSQL | Mission-Critical | 30 minutes | $50,000/hour revenue loss |
| User Uploads S3 | Critical | 2 hours | Customer impact moderate |
| Analytics MongoDB | Important | 8 hours | Non-real-time analytics |
| Application Logs | Normal | 24 hours | Historical data only |
```

#### 2.2 Define RPO per Data Asset

**RPO (Recovery Point Objective):** Maximum acceptable data loss

**RPO Categories:**
- **Near-Zero:** < 1 minute (financial transactions)
- **Low:** < 15 minutes (e-commerce orders)
- **Medium:** < 1 hour (user-generated content)
- **High:** < 24 hours (analytics data)

**Document:**
```markdown
## RPO Requirements

| Data Asset | Data Type | RPO | Rationale |
|------------|-----------|-----|----------|
| Production PostgreSQL | Transactions | 5 minutes | Minimize lost transactions |
| User Uploads S3 | User files | 1 hour | Acceptable user impact |
| Analytics MongoDB | Analytics | 24 hours | Can re-process data |
| Application Logs | Logs | 24 hours | Historical logs acceptable |
```

#### 2.3 Document Financial Impact

**Cost of Data Loss:**
- Revenue loss per hour
- Customer acquisition cost impact
- Regulatory fines (if applicable)
- Brand reputation damage

**Document:**
```markdown
## Financial Impact of Data Loss

### Production PostgreSQL
**Revenue Loss:** $50,000/hour
**Customer Impact:** 1,000 lost transactions/hour
**Regulatory Fines:** Potential PCI DSS penalties
**Total Impact:** $50,000+/hour

### User Uploads S3
**Customer Impact:** User complaints, negative reviews
**Brand Damage:** Social media complaints
**Total Impact:** $5,000-10,000/hour
```

#### 2.4 Obtain Stakeholder Approval

**Stakeholders:**
- Engineering leadership (CTO, VP Engineering)
- Finance (budget approval)
- Compliance (regulatory requirements)
- Product management (customer impact)

**Approval Document:**
```markdown
## RTO/RPO Approval

**Approved By:** Jane Doe (CTO), John Smith (CFO)
**Date:** 2026-09-09
**Budget Approved:** $60,000/year for backup infrastructure

**Approved RTO/RPO:**
- Production PostgreSQL: RTO 30min, RPO 5min
- User Uploads S3: RTO 2hr, RPO 1hr
- Analytics MongoDB: RTO 8hr, RPO 24hr
- Application Logs: RTO 24hr, RPO 24hr
```

### Quality Checklist

- [ ] RTO defined for each data asset
- [ ] RPO defined for each data asset
- [ ] Business impact documented
- [ ] Financial impact calculated
- [ ] Stakeholder approval obtained
- [ ] Budget approved

### Common Mistakes

❌ **Unrealistic RTO/RPO** — Set RTO 1 minute without infrastructure to support it  
✅ **Align with backup strategy** — Ensure backup method can meet requirements

❌ **No business justification** — Set RTO/RPO arbitrarily  
✅ **Quantify business impact** — Calculate revenue loss, customer impact

---

## Step 3: Select Backup Strategy (30-45 minutes)

### Objective

Choose appropriate backup methods for each data asset.

### Actions

#### 3.1 Evaluate Backup Methods

**Full Backup:**
- **Description:** Complete copy of all data
- **Pros:** Simple, complete copy, easy restore
- **Cons:** Slow, large storage, high bandwidth
- **Use Case:** Weekly or monthly backups, small databases
- **Example:** Weekly full backup of 100GB database

**Incremental Backup:**
- **Description:** Only changed data since last backup (any type)
- **Pros:** Fast, efficient storage, low bandwidth
- **Cons:** Complex restore (need all incrementals), slower recovery
- **Use Case:** Daily or hourly backups, large databases
- **Example:** Daily incremental backup of 1TB database (only 50GB changed)

**Differential Backup:**
- **Description:** Changed data since last full backup
- **Pros:** Faster restore than incremental, efficient storage
- **Cons:** Larger than incremental, slower than full
- **Use Case:** Daily backups, medium databases
- **Example:** Daily differential backup (100GB changed since last full)

**Continuous Backup:**
- **Description:** Real-time backup of every change
- **Pros:** Near-zero RPO, real-time protection
- **Cons:** High cost, high resource usage
- **Use Case:** Mission-critical data (RPO < 1 minute)
- **Example:** AWS RDS automated backups with point-in-time recovery

**Snapshot Backup:**
- **Description:** Point-in-time copy of volume or filesystem
- **Pros:** Fast, storage-efficient, point-in-time
- **Cons:** Limited to snapshot-capable systems
- **Use Case:** Virtual machines, block storage, EBS volumes
- **Example:** Daily EBS snapshots of application volumes

#### 3.2 Match Backup Method to RTO/RPO

**Decision Matrix:**

| RTO | RPO | Backup Method | Frequency |
|-----|-----|---------------|----------|
| < 1 hour | < 5 min | Continuous | Real-time |
| < 2 hours | < 1 hour | Incremental | Hourly |
| < 8 hours | < 24 hours | Differential | Daily |
| < 24 hours | < 24 hours | Full | Daily or Weekly |

**Document:**
```markdown
## Backup Strategy Selection

### Production PostgreSQL
**RTO:** 30 minutes  
**RPO:** 5 minutes  
**Selected Method:** Continuous backup (AWS RDS automated backups)  
**Rationale:** RTO/RPO requirements mandate real-time backup  
**Frequency:** Continuous (transaction log backups every 5 minutes)  
**Cost:** $50/month for 500GB database

### User Uploads S3
**RTO:** 2 hours  
**RPO:** 1 hour  
**Selected Method:** S3 versioning + lifecycle policies  
**Rationale:** S3 versioning provides point-in-time recovery  
**Frequency:** Continuous (versioning on every change)  
**Cost:** $25/month for 5TB storage

### Analytics MongoDB
**RTO:** 8 hours  
**RPO:** 24 hours  
**Selected Method:** Daily full backup  
**Rationale:** Daily backup sufficient for analytics data  
**Frequency:** Daily at 2 AM  
**Cost:** $15/month for 2TB database
```

#### 3.3 Consider Cost vs. Recovery Time Trade-offs

**Cost Analysis:**

| Backup Method | Storage Cost | Operational Cost | Recovery Time |
|---------------|--------------|------------------|---------------|
| Full Backup | High (1x data size) | Low | Fast (hours) |
| Incremental | Low (0.1-0.2x) | Medium | Slow (hours-days) |
| Differential | Medium (0.3-0.5x) | Medium | Medium (hours) |
| Continuous | High (1.5-2x) | High | Fast (minutes) |
| Snapshot | Low (0.1-0.3x) | Low | Fast (minutes-hours) |

**ROI Calculation:**
```markdown
## Backup Cost vs. Data Loss Cost

### Production PostgreSQL (Continuous Backup)
**Backup Cost:** $50/month = $600/year  
**Data Loss Cost:** $50,000/hour  
**Break-Even:** 0.012 hours (43 seconds) of prevented data loss per year  
**Expected Data Loss Events:** 2-3 per year (without backup)  
**ROI:** $100,000-150,000/year savings
```

### Quality Checklist

- [ ] Backup methods evaluated (full, incremental, differential, continuous, snapshot)
- [ ] Backup method selected per data asset
- [ ] Cost vs. recovery time analyzed
- [ ] Decision documented with rationale
- [ ] Backup frequency defined
- [ ] Storage requirements calculated

### Common Mistakes

❌ **One-size-fits-all backup** — Use same method for all data  
✅ **Tailored backup strategy** — Match method to RTO/RPO and criticality

❌ **Ignoring costs** — Choose expensive method for all data  
✅ **Cost-benefit analysis** — Calculate ROI for each data asset

---

## Step 4: Design Backup Schedule (30-45 minutes)

### Objective

Define backup frequency and timing.

### Actions

#### 4.1 Define Backup Schedules

**Backup Frequency Options:**
- **Continuous:** Real-time backup (mission-critical)
- **Hourly:** Every hour (critical data, RPO < 1 hour)
- **Daily:** Once per day (important data, RPO < 24 hours)
- **Weekly:** Once per week (normal data, archival)
- **Monthly:** Once per month (long-term archival)

**Document:**
```markdown
## Backup Schedules

### Production PostgreSQL
**Method:** Continuous backup  
**Frequency:** Transaction log backups every 5 minutes  
**Full Backup:** Daily at 2 AM EST  
**Retention:** 30 days (daily) + 1 year (monthly)

### User Uploads S3
**Method:** S3 versioning  
**Frequency:** Continuous (on every change)  
**Lifecycle:** Transition to Glacier after 30 days  
**Retention:** 90 days (current versions) + 1 year (Glacier)

### Analytics MongoDB
**Method:** Full backup  
**Frequency:** Daily at 3 AM EST  
**Retention:** 7 days (daily) + 30 days (weekly)

### Application Logs
**Method:** Log archival  
**Frequency:** Daily at 4 AM EST  
**Retention:** 7 days (hot) + 90 days (cold)
```

#### 4.2 Schedule Backups During Low-Traffic Periods

**Traffic Analysis:**
- Peak hours: 9 AM - 9 PM EST (avoid backups)
- Low-traffic hours: 2 AM - 6 AM EST (ideal for backups)
- Weekend traffic: 50% of weekday traffic

**Backup Windows:**
```markdown
## Backup Windows

**Weekday Backup Window:** 2 AM - 6 AM EST  
**Weekend Backup Window:** 1 AM - 8 AM EST  

**Schedule:**
- 2:00 AM: Production PostgreSQL full backup (30 min)
- 2:30 AM: User Uploads S3 snapshot (10 min)
- 3:00 AM: Analytics MongoDB full backup (60 min)
- 4:00 AM: Application Logs archival (15 min)
- 4:15 AM: Configuration backups (5 min)
```

#### 4.3 Plan for Backup Duration and Resource Usage

**Resource Impact:**
- **CPU:** Backup compression uses 20-30% CPU
- **Memory:** Backup buffers use 2-4GB RAM
- **Network:** Backup transfer uses 50-100 Mbps bandwidth
- **Disk I/O:** Backup reads use 50-80% disk I/O

**Mitigation:**
- Schedule backups during low-traffic periods
- Use backup throttling to limit resource usage
- Implement backup compression to reduce bandwidth
- Use read replicas for database backups (avoid primary impact)

**Document:**
```markdown
## Backup Resource Usage

### Production PostgreSQL Backup
**Duration:** 30 minutes  
**CPU Usage:** 25% (compression)  
**Memory Usage:** 3GB (backup buffer)  
**Network Usage:** 80 Mbps (upload to S3)  
**Disk I/O:** 60% (read from primary)  
**Mitigation:** Use read replica for backups

### User Uploads S3 Snapshot
**Duration:** 10 minutes  
**Network Usage:** 100 Mbps (cross-region replication)  
**Cost:** $0.02/GB transfer  
**Mitigation:** Use S3 Transfer Acceleration
```

### Quality Checklist

- [ ] Backup schedules defined (continuous, hourly, daily, weekly, monthly)
- [ ] Backup windows planned (low-traffic periods)
- [ ] Resource usage estimated (CPU, memory, network, disk I/O)
- [ ] Backup duration calculated
- [ ] Resource mitigation strategies defined

### Common Mistakes

❌ **Backups during peak hours** — Impact production performance  
✅ **Low-traffic backup windows** — Schedule backups at 2-6 AM

❌ **No resource planning** — Backups overwhelm production systems  
✅ **Resource throttling** — Limit backup resource usage

---

## Step 5: Define Retention Policies (30-45 minutes)

### Objective

Establish how long to retain backups.

### Actions

#### 5.1 Define Retention Policies

**Retention Tiers:**
- **Short-term:** 7 days (operational recovery)
- **Medium-term:** 30 days (compliance, audit)
- **Long-term:** 1-7 years (regulatory compliance)

**Document:**
```markdown
## Retention Policies

### Production PostgreSQL
**Short-term:** 30 days (daily backups)  
**Medium-term:** 1 year (monthly backups)  
**Long-term:** 7 years (annual backups for compliance)  
**Compliance:** SOC 2, HIPAA, PCI DSS

### User Uploads S3
**Short-term:** 30 days (current versions)  
**Medium-term:** 90 days (Glacier)  
**Long-term:** 1 year (Deep Archive)  
**Compliance:** GDPR (right to be forgotten)

### Analytics MongoDB
**Short-term:** 7 days (daily backups)  
**Medium-term:** 30 days (weekly backups)  
**Long-term:** None (can re-process data)  
**Compliance:** None

### Application Logs
**Short-term:** 7 days (hot storage)  
**Medium-term:** 90 days (cold storage)  
**Long-term:** 1 year (Glacier)  
**Compliance:** SOC 2 (audit logs)
```

#### 5.2 Implement Tiered Storage

**Storage Tiers:**
- **Hot:** Frequent access, high cost (S3 Standard)
- **Warm:** Infrequent access, medium cost (S3 Infrequent Access)
- **Cold:** Rare access, low cost (S3 Glacier)
- **Archive:** Long-term retention, very low cost (S3 Glacier Deep Archive)

**Cost Comparison:**

| Storage Tier | Cost per GB/month | Retrieval Time | Use Case |
|--------------|-------------------|----------------|----------|
| Hot (S3 Standard) | $0.023 | Instant | 7-30 days |
| Warm (S3 IA) | $0.0125 | Instant | 30-90 days |
| Cold (Glacier) | $0.004 | 3-5 hours | 90 days - 1 year |
| Archive (Deep Archive) | $0.00099 | 12 hours | 1-7 years |

**Lifecycle Policy:**
```json
{
  "Rules": [
    {
      "Id": "Transition-to-IA",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        }
      ]
    },
    {
      "Id": "Transition-to-Glacier",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        }
      ]
    },
    {
      "Id": "Transition-to-Deep-Archive",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    },
    {
      "Id": "Expire-after-7-years",
      "Status": "Enabled",
      "Expiration": {
        "Days": 2555
      }
    }
  ]
}
```

#### 5.3 Plan for Backup Deletion and Cleanup

**Automated Cleanup:**
- Delete backups older than retention period
- Use lifecycle policies for S3
- Use automated snapshot deletion for EBS/RDS
- Implement backup cleanup scripts

**Cleanup Script:**
```bash
#!/bin/bash
# cleanup-old-backups.sh
# Runs daily via cron

set -e

RETENTION_DAYS=30
CUTOFF_DATE=$(date -d "$RETENTION_DAYS days ago" +%Y-%m-%d)

echo "Cleaning up backups older than $CUTOFF_DATE..."

# Delete old RDS snapshots
aws rds describe-db-snapshots \
  --query "DBSnapshots[?SnapshotCreateTime<'$CUTOFF_DATE'].DBSnapshotIdentifier" \
  --output text | while read snapshot; do
  echo "Deleting RDS snapshot: $snapshot"
  aws rds delete-db-snapshot --db-snapshot-identifier "$snapshot"
done

# Delete old EBS snapshots
aws ec2 describe-snapshots --owner-ids self \
  --query "Snapshots[?StartTime<'$CUTOFF_DATE'].SnapshotId" \
  --output text | while read snapshot; do
  echo "Deleting EBS snapshot: $snapshot"
  aws ec2 delete-snapshot --snapshot-id "$snapshot"
done

echo "Backup cleanup complete"
```

#### 5.4 Document Compliance Requirements

**Compliance Mapping:**

| Regulation | Retention Requirement | Data Types |
|------------|----------------------|------------|
| GDPR | Right to be forgotten | Personal data |
| HIPAA | 6 years | Health records |
| SOC 2 | 1 year | Audit logs, access logs |
| PCI DSS | 1 year (3 months online) | Payment card data |
| SOX | 7 years | Financial records |

**Document:**
```markdown
## Compliance Requirements

### GDPR Compliance
**Requirement:** Right to be forgotten  
**Implementation:** Automated deletion of personal data on request  
**Retention:** 30 days (current) + 90 days (Glacier) with deletion capability

### HIPAA Compliance
**Requirement:** 6 years retention  
**Implementation:** 7-year retention for health records  
**Encryption:** AES-256 encryption at rest and in transit

### SOC 2 Compliance
**Requirement:** 1 year audit log retention  
**Implementation:** 1 year retention for audit logs  
**Access Control:** MFA required for backup access

### PCI DSS Compliance
**Requirement:** 1 year retention (3 months online)  
**Implementation:** 3 months hot storage + 9 months Glacier  
**Encryption:** AES-256 encryption, key rotation every 90 days
```

### Quality Checklist

- [ ] Retention policies defined (short-term, medium-term, long-term)
- [ ] Tiered storage planned (hot, warm, cold, archive)
- [ ] Cleanup automation implemented
- [ ] Compliance requirements met (GDPR, HIPAA, SOC 2, PCI DSS)
- [ ] Lifecycle policies configured
- [ ] Cost optimization implemented

### Common Mistakes

❌ **No retention policy** — Keep all backups forever (high cost)  
✅ **Tiered retention** — Short-term hot, long-term cold/archive

❌ **Manual cleanup** — Forget to delete old backups  
✅ **Automated cleanup** — Use lifecycle policies and scripts

---

## Step 6: Implement Backup Automation (60-120 minutes)

### Objective

Automate backup processes to ensure reliability and consistency.

### Actions

#### 6.1 Implement Automated Backups

**AWS RDS Automated Backups:**
```bash
# Enable automated backups for RDS
aws rds modify-db-instance \
  --db-instance-identifier production-db \
  --backup-retention-period 30 \
  --preferred-backup-window "02:00-02:30" \
  --apply-immediately

# Enable point-in-time recovery
aws rds modify-db-instance \
  --db-instance-identifier production-db \
  --enable-cloudwatch-logs-exports '["postgresql"]' \
  --apply-immediately
```

**EBS Snapshot Automation (AWS Backup):**
```json
{
  "BackupPlan": {
    "BackupPlanName": "DailyEBSBackup",
    "Rules": [
      {
        "RuleName": "DailyBackup",
        "TargetBackupVault": "Default",
        "ScheduleExpression": "cron(0 2 * * ? *)",
        "StartWindowMinutes": 60,
        "CompletionWindowMinutes": 120,
        "Lifecycle": {
          "DeleteAfterDays": 30,
          "MoveToColdStorageAfterDays": 7
        }
      }
    ]
  },
  "BackupSelection": {
    "SelectionName": "AllEBSVolumes",
    "IamRoleArn": "arn:aws:iam::123456789012:role/AWSBackupRole",
    "Resources": ["*"],
    "ListOfTags": [
      {
        "ConditionType": "STRINGEQUALS",
        "ConditionKey": "Backup",
        "ConditionValue": "true"
      }
    ]
  }
}
```

**S3 Cross-Region Replication:**
```json
{
  "Role": "arn:aws:iam::123456789012:role/S3ReplicationRole",
  "Rules": [
    {
      "Status": "Enabled",
      "Priority": 1,
      "Filter": {},
      "Destination": {
        "Bucket": "arn:aws:s3:::backup-bucket-us-west-2",
        "ReplicationTime": {
          "Status": "Enabled",
          "Time": {
            "Minutes": 15
          }
        },
        "Metrics": {
          "Status": "Enabled",
          "EventThreshold": {
            "Minutes": 15
          }
        }
      },
      "DeleteMarkerReplication": {
        "Status": "Enabled"
      }
    }
  ]
}
```

**MongoDB Backup Script:**
```bash
#!/bin/bash
# mongodb-backup.sh
# Runs daily at 3 AM via cron

set -e

BACKUP_DATE=$(date +%Y-%m-%d)
BACKUP_DIR="/backups/mongodb/$BACKUP_DATE"
S3_BUCKET="s3://company-backups/mongodb"

echo "[BACKUP] Starting MongoDB backup for $BACKUP_DATE..."

# Create backup directory
mkdir -p "$BACKUP_DIR"

# Dump MongoDB database
mongodump \
  --host mongodb-cluster.example.com \
  --port 27017 \
  --username backup-user \
  --password "$MONGODB_PASSWORD" \
  --authenticationDatabase admin \
  --out "$BACKUP_DIR" \
  --gzip

echo "[BACKUP] MongoDB dump complete"

# Compress backup
tar -czf "$BACKUP_DIR.tar.gz" -C /backups/mongodb "$BACKUP_DATE"

echo "[BACKUP] Backup compressed"

# Upload to S3
aws s3 cp "$BACKUP_DIR.tar.gz" "$S3_BUCKET/$BACKUP_DATE.tar.gz" \
  --storage-class STANDARD_IA

echo "[BACKUP] Backup uploaded to S3"

# Clean up local backup
rm -rf "$BACKUP_DIR" "$BACKUP_DIR.tar.gz"

echo "[BACKUP] MongoDB backup complete for $BACKUP_DATE ✅"
```

#### 6.2 Configure Backup Encryption

**Encryption at Rest:**
- Use AWS KMS for encryption keys
- Enable encryption for RDS, EBS, S3
- Rotate encryption keys every 90 days

**Encryption in Transit:**
- Use TLS 1.2+ for data transfer
- Enable S3 transfer encryption
- Use VPN or AWS PrivateLink for cross-region replication

**KMS Key Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow RDS to use the key",
      "Effect": "Allow",
      "Principal": {
        "Service": "rds.amazonaws.com"
      },
      "Action": [
        "kms:Decrypt",
        "kms:DescribeKey",
        "kms:CreateGrant"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow S3 to use the key",
      "Effect": "Allow",
      "Principal": {
        "Service": "s3.amazonaws.com"
      },
      "Action": [
        "kms:Decrypt",
        "kms:GenerateDataKey"
      ],
      "Resource": "*"
    }
  ]
}
```

#### 6.3 Set Up Backup Monitoring and Alerting

**CloudWatch Alarms:**
```yaml
Alarms:
  - Name: RDS-Backup-Failed
    Metric: BackupRetentionPeriodStorageUsed
    Threshold: 0
    EvaluationPeriods: 1
    ComparisonOperator: LessThanThreshold
    Action: Alert on-call team
    
  - Name: EBS-Snapshot-Failed
    Metric: SnapshotAge
    Threshold: 86400  # 24 hours
    EvaluationPeriods: 1
    ComparisonOperator: GreaterThanThreshold
    Action: Alert on-call team
    
  - Name: S3-Replication-Lag
    Metric: ReplicationLatency
    Threshold: 900  # 15 minutes
    EvaluationPeriods: 2
    ComparisonOperator: GreaterThanThreshold
    Action: Alert on-call team
```

**Backup Monitoring Script:**
```bash
#!/bin/bash
# monitor-backups.sh
# Runs every hour via cron

set -e

echo "[MONITOR] Checking backup health..."

# Check RDS backup age
LAST_RDS_BACKUP=$(aws rds describe-db-snapshots \
  --db-instance-identifier production-db \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].SnapshotCreateTime' \
  --output text)

BACKUP_AGE=$(( ($(date +%s) - $(date -d "$LAST_RDS_BACKUP" +%s)) / 3600 ))

if [ "$BACKUP_AGE" -gt 24 ]; then
  echo "❌ RDS backup is $BACKUP_AGE hours old (threshold: 24 hours)"
  # Send alert
  aws sns publish \
    --topic-arn arn:aws:sns:us-east-1:123456789012:backup-alerts \
    --message "RDS backup is $BACKUP_AGE hours old"
else
  echo "✅ RDS backup is $BACKUP_AGE hours old"
fi

# Check S3 replication lag
REPLICATION_LAG=$(aws s3api get-bucket-replication \
  --bucket company-uploads-prod \
  --query 'ReplicationConfiguration.Rules[0].Destination.Metrics.EventThreshold.Minutes' \
  --output text)

if [ "$REPLICATION_LAG" -gt 15 ]; then
  echo "⚠️  S3 replication lag: $REPLICATION_LAG minutes"
else
  echo "✅ S3 replication lag: $REPLICATION_LAG minutes"
fi

echo "[MONITOR] Backup health check complete"
```

#### 6.4 Automate Backup Validation

**Backup Validation Script:**
```bash
#!/bin/bash
# validate-backup.sh
# Runs weekly to validate backup integrity

set -e

echo "[VALIDATE] Starting backup validation..."

# Restore RDS snapshot to test instance
LATEST_SNAPSHOT=$(aws rds describe-db-snapshots \
  --db-instance-identifier production-db \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].DBSnapshotIdentifier' \
  --output text)

echo "[VALIDATE] Restoring snapshot: $LATEST_SNAPSHOT"

aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier test-restore-$(date +%Y%m%d) \
  --db-snapshot-identifier "$LATEST_SNAPSHOT" \
  --db-instance-class db.t3.small

# Wait for restore to complete
aws rds wait db-instance-available \
  --db-instance-identifier test-restore-$(date +%Y%m%d)

echo "[VALIDATE] Restore complete"

# Validate data integrity
psql -h test-restore-$(date +%Y%m%d).abc123.us-east-1.rds.amazonaws.com \
  -U postgres -d production \
  -c "SELECT COUNT(*) FROM users;" > /tmp/user_count.txt

USER_COUNT=$(cat /tmp/user_count.txt | tail -n 1 | tr -d ' ')

if [ "$USER_COUNT" -gt 0 ]; then
  echo "✅ Data integrity validated: $USER_COUNT users found"
else
  echo "❌ Data integrity validation failed: no users found"
  exit 1
fi

# Clean up test instance
aws rds delete-db-instance \
  --db-instance-identifier test-restore-$(date +%Y%m%d) \
  --skip-final-snapshot

echo "[VALIDATE] Backup validation complete ✅"
```

#### 6.5 Implement Backup Reporting

**Daily Backup Report:**
```bash
#!/bin/bash
# backup-report.sh
# Runs daily at 8 AM to generate backup report

set -e

REPORT_DATE=$(date +%Y-%m-%d)
REPORT_FILE="/tmp/backup-report-$REPORT_DATE.txt"

echo "Backup Report - $REPORT_DATE" > "$REPORT_FILE"
echo "================================" >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# RDS Backups
echo "RDS Backups:" >> "$REPORT_FILE"
aws rds describe-db-snapshots \
  --db-instance-identifier production-db \
  --query 'DBSnapshots[-5:].{Snapshot:DBSnapshotIdentifier,Created:SnapshotCreateTime,Size:AllocatedStorage,Status:Status}' \
  --output table >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# EBS Snapshots
echo "EBS Snapshots:" >> "$REPORT_FILE"
aws ec2 describe-snapshots --owner-ids self \
  --query 'Snapshots[-5:].{Snapshot:SnapshotId,Created:StartTime,Size:VolumeSize,Status:State}' \
  --output table >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# S3 Replication Status
echo "S3 Replication Status:" >> "$REPORT_FILE"
aws s3api get-bucket-replication --bucket company-uploads-prod \
  --query 'ReplicationConfiguration.Rules[0].{Status:Status,Destination:Destination.Bucket}' \
  --output table >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Backup Storage Usage
echo "Backup Storage Usage:" >> "$REPORT_FILE"
aws cloudwatch get-metric-statistics \
  --namespace AWS/RDS \
  --metric-name BackupRetentionPeriodStorageUsed \
  --dimensions Name=DBInstanceIdentifier,Value=production-db \
  --start-time $(date -u -d '1 day ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 86400 \
  --statistics Average \
  --query 'Datapoints[0].Average' \
  --output text >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Email report
aws ses send-email \
  --from backups@company.com \
  --to engineering@company.com \
  --subject "Daily Backup Report - $REPORT_DATE" \
  --text file://"$REPORT_FILE"

echo "Backup report sent to engineering@company.com"
```

### Quality Checklist

- [ ] Automated backups implemented (RDS, EBS, S3, MongoDB)
- [ ] Backup encryption enabled (at rest and in transit)
- [ ] Monitoring and alerting configured
- [ ] Backup validation automated
- [ ] Reporting implemented
- [ ] Backup schedules configured

### Common Mistakes

❌ **Manual backups** — Prone to human error and missed backups  
✅ **Automated backups** — Use AWS Backup, cron jobs, Lambda functions

❌ **No backup monitoring** — Backup failures go unnoticed  
✅ **CloudWatch alarms** — Alert on backup failures, replication lag

---

## Step 7: Implement 3-2-1 Backup Rule (30-60 minutes)

### Objective

Ensure backup resilience by implementing the 3-2-1 backup rule.

### Actions

#### 7.1 Implement 3 Copies of Data

**3 Copies:**
1. **Production data** (primary copy)
2. **Local backup** (same region, different AZ)
3. **Offsite backup** (different region)

**Document:**
```markdown
## 3 Copies Implementation

### Production PostgreSQL
**Copy 1:** Production RDS instance (us-east-1a)
**Copy 2:** RDS automated backup (us-east-1, S3)
**Copy 3:** Cross-region snapshot copy (us-west-2, S3)

### User Uploads S3
**Copy 1:** Production S3 bucket (us-east-1)
**Copy 2:** S3 versioning (us-east-1, same bucket)
**Copy 3:** Cross-region replication (us-west-2)
```

#### 7.2 Implement 2 Different Media

**2 Different Media:**
1. **Local storage** (EBS, S3 in same region)
2. **Cloud storage** (S3 in different region, or Glacier)

**Alternative:**
1. **Disk** (EBS volumes, local disk)
2. **Tape** (AWS Glacier, tape backup service)

**Document:**
```markdown
## 2 Different Media Implementation

### Production PostgreSQL
**Medium 1:** EBS volumes (local storage in us-east-1)
**Medium 2:** S3 Glacier (cloud storage in us-west-2)

### User Uploads S3
**Medium 1:** S3 Standard (us-east-1)
**Medium 2:** S3 Glacier (us-west-2)
```

#### 7.3 Implement 1 Offsite Backup

**1 Offsite:**
- Different geographic region (us-east-1 → us-west-2)
- Different cloud provider (AWS → Azure, GCP)
- Physical offsite location (tape backup, colocation)

**Cross-Region Replication:**
```bash
# Enable cross-region RDS snapshot copy
aws rds copy-db-snapshot \
  --source-db-snapshot-identifier arn:aws:rds:us-east-1:123456789012:snapshot:production-db-2026-09-09 \
  --target-db-snapshot-identifier production-db-2026-09-09-dr \
  --source-region us-east-1 \
  --region us-west-2 \
  --kms-key-id arn:aws:kms:us-west-2:123456789012:key/abcd1234-5678-90ab-cdef-1234567890ab

# Automate cross-region copy with Lambda
# Lambda function triggered on RDS snapshot creation
```

**Lambda Function for Automated Cross-Region Copy:**
```python
import boto3
import os

def lambda_handler(event, context):
    rds_client = boto3.client('rds')
    
    # Get snapshot details from event
    snapshot_id = event['detail']['SourceIdentifier']
    source_region = os.environ['SOURCE_REGION']
    target_region = os.environ['TARGET_REGION']
    kms_key_id = os.environ['KMS_KEY_ID']
    
    # Copy snapshot to target region
    response = rds_client.copy_db_snapshot(
        SourceDBSnapshotIdentifier=f'arn:aws:rds:{source_region}:123456789012:snapshot:{snapshot_id}',
        TargetDBSnapshotIdentifier=f'{snapshot_id}-dr',
        SourceRegion=source_region,
        KmsKeyId=kms_key_id
    )
    
    print(f'Copied snapshot {snapshot_id} to {target_region}')
    return response
```

#### 7.4 Verify Backup Independence

**Independence Checks:**
- Backups stored in different AWS accounts (production vs. backup account)
- Backups encrypted with different KMS keys
- Backups accessible even if primary account compromised
- Backups not dependent on primary infrastructure

**Document:**
```markdown
## Backup Independence

### Production PostgreSQL
**Primary:** Production AWS account (123456789012)
**Backup:** Backup AWS account (987654321098)
**Encryption:** Different KMS keys per account
**Access:** Backup account has independent IAM roles

### User Uploads S3
**Primary:** Production S3 bucket (us-east-1)
**Backup:** Backup S3 bucket in different AWS account (us-west-2)
**Replication:** Cross-account S3 replication
**Access:** Backup account has independent access
```

### Quality Checklist

- [ ] 3 copies of data maintained (production + 2 backups)
- [ ] 2 different storage media used (local + cloud, or disk + tape)
- [ ] 1 offsite backup configured (different region or cloud provider)
- [ ] Backup independence verified (different accounts, encryption keys)
- [ ] Cross-region replication automated

### Common Mistakes

❌ **Single backup copy** — Vulnerable to corruption or deletion  
✅ **3-2-1 backup rule** — 3 copies, 2 media, 1 offsite

❌ **All backups in same region** — Vulnerable to regional outages  
✅ **Cross-region replication** — Protect against regional failures

---

## Step 8: Create Recovery Runbooks (45-90 minutes)

### Objective

Document recovery procedures for each data asset.

### Actions

#### 8.1 Write Recovery Runbooks

**Recovery Runbook Template:**
```markdown
# Recovery Runbook: [Data Asset Name]

## Overview
**Data Asset:** [Name]
**RTO:** [Time]
**RPO:** [Time]
**Backup Method:** [Method]
**Last Updated:** [Date]

## Prerequisites
- [ ] Access to AWS console or CLI
- [ ] IAM permissions for backup restoration
- [ ] On-call team notified
- [ ] Stakeholders notified

## Recovery Procedure

### Step 1: Identify Latest Backup
[Commands to find latest backup]

### Step 2: Restore Backup
[Commands to restore backup]

### Step 3: Validate Restoration
[Commands to validate data integrity]

### Step 4: Update Application Configuration
[Commands to update application to use restored data]

### Step 5: Test Application Functionality
[Commands to test application]

## Validation Criteria
- [ ] Data integrity validated
- [ ] Application functionality tested
- [ ] Performance acceptable
- [ ] No data loss beyond RPO

## Rollback Procedure
[Steps to rollback if recovery fails]

## Contact Information
**On-Call Engineer:** [Phone/Email]
**Escalation:** [CTO Phone/Email]
```

**Example: Production PostgreSQL Recovery Runbook:**
```markdown
# Recovery Runbook: Production PostgreSQL

## Overview
**Data Asset:** Production PostgreSQL Database
**RTO:** 30 minutes
**RPO:** 5 minutes
**Backup Method:** AWS RDS automated backups + point-in-time recovery
**Last Updated:** 2026-09-09

## Prerequisites
- [ ] Access to AWS console or CLI
- [ ] IAM permissions: rds:RestoreDBInstanceFromDBSnapshot, rds:RestoreDBInstanceToPointInTime
- [ ] On-call team notified
- [ ] Stakeholders notified (CTO, VP Engineering)

## Recovery Procedure

### Step 1: Identify Latest Backup (5 minutes)

```bash
# Find latest automated backup
LATEST_SNAPSHOT=$(aws rds describe-db-snapshots \
  --db-instance-identifier production-db \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].DBSnapshotIdentifier' \
  --output text)

echo "Latest snapshot: $LATEST_SNAPSHOT"

# Check snapshot age
SNAPSHOT_AGE=$(aws rds describe-db-snapshots \
  --db-snapshot-identifier "$LATEST_SNAPSHOT" \
  --query 'DBSnapshots[0].SnapshotCreateTime' \
  --output text)

echo "Snapshot created at: $SNAPSHOT_AGE"
```

### Step 2: Restore Backup (15 minutes)

**Option A: Restore from Snapshot (for complete database failure)**
```bash
# Restore from latest snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier production-db-restored \
  --db-snapshot-identifier "$LATEST_SNAPSHOT" \
  --db-instance-class db.r5.4xlarge \
  --db-subnet-group-name production-subnet-group \
  --publicly-accessible false \
  --multi-az true

# Wait for restore to complete
aws rds wait db-instance-available \
  --db-instance-identifier production-db-restored

echo "Database restored from snapshot"
```

**Option B: Point-in-Time Recovery (for data corruption)**
```bash
# Restore to specific point in time (e.g., 1 hour ago)
RESTORE_TIME=$(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S)

aws rds restore-db-instance-to-point-in-time \
  --source-db-instance-identifier production-db \
  --target-db-instance-identifier production-db-pitr \
  --restore-time "$RESTORE_TIME" \
  --db-instance-class db.r5.4xlarge \
  --db-subnet-group-name production-subnet-group \
  --publicly-accessible false \
  --multi-az true

# Wait for restore to complete
aws rds wait db-instance-available \
  --db-instance-identifier production-db-pitr

echo "Database restored to point-in-time: $RESTORE_TIME"
```

### Step 3: Validate Restoration (5 minutes)

```bash
# Get restored database endpoint
DB_ENDPOINT=$(aws rds describe-db-instances \
  --db-instance-identifier production-db-restored \
  --query 'DBInstances[0].Endpoint.Address' \
  --output text)

echo "Restored database endpoint: $DB_ENDPOINT"

# Validate data integrity
psql -h "$DB_ENDPOINT" -U postgres -d production -c "SELECT COUNT(*) FROM users;"
psql -h "$DB_ENDPOINT" -U postgres -d production -c "SELECT COUNT(*) FROM orders;"
psql -h "$DB_ENDPOINT" -U postgres -d production -c "SELECT MAX(created_at) FROM orders;"

echo "Data integrity validated"
```

### Step 4: Update Application Configuration (3 minutes)

```bash
# Update application to use restored database
aws ssm put-parameter \
  --name "/production/database/endpoint" \
  --value "$DB_ENDPOINT" \
  --type "String" \
  --overwrite

# Restart application servers to pick up new endpoint
aws ecs update-service \
  --cluster production-cluster \
  --service production-app \
  --force-new-deployment

echo "Application configuration updated"
```

### Step 5: Test Application Functionality (2 minutes)

```bash
# Test application health
curl -s https://api.company.com/health | jq '.database'

# Test database connectivity
curl -s https://api.company.com/api/users/1 | jq '.id'

echo "Application functionality validated"
```

## Validation Criteria
- [ ] Database restored successfully
- [ ] Data integrity validated (user count, order count match expected)
- [ ] Application connected to restored database
- [ ] Application functionality tested (health check, API calls)
- [ ] Performance acceptable (query latency < 100ms)
- [ ] No data loss beyond RPO (5 minutes)

## Rollback Procedure

If recovery fails:
1. Revert application configuration to original database endpoint
2. Restart application servers
3. Delete failed restored database instance
4. Escalate to CTO

## Contact Information
**On-Call Engineer:** +1-555-0100 / oncall@company.com
**Escalation:** CTO +1-555-0101 / cto@company.com
**Database Team:** db-team@company.com
```

#### 8.2 Document Recovery Validation Procedures

**Validation Checklist:**
```markdown
## Recovery Validation Checklist

### Data Integrity
- [ ] Record counts match expected values
- [ ] Latest records present (check timestamps)
- [ ] No data corruption (spot check critical records)
- [ ] Foreign key constraints intact
- [ ] Indexes rebuilt successfully

### Application Functionality
- [ ] Health check passes
- [ ] Critical API endpoints responding
- [ ] User authentication working
- [ ] Database queries completing successfully
- [ ] Background jobs processing

### Performance
- [ ] Query latency acceptable (< 100ms for simple queries)
- [ ] Connection pool healthy
- [ ] No slow queries detected
- [ ] CPU and memory usage normal

### Data Loss Assessment
- [ ] Data loss within RPO (5 minutes)
- [ ] Missing transactions identified and documented
- [ ] Business impact assessed
- [ ] Stakeholders notified of data loss
```

#### 8.3 Create Troubleshooting Guides

**Common Issues and Solutions:**
```markdown
## Troubleshooting Guide

### Issue 1: Restore Taking Too Long
**Symptom:** Database restore exceeds RTO (30 minutes)
**Cause:** Large database size, slow network
**Solution:**
- Use read replica promotion instead of snapshot restore (faster)
- Increase instance size for faster restore
- Use Multi-AZ failover instead of restore (if available)

### Issue 2: Data Integrity Validation Fails
**Symptom:** Record counts don't match expected values
**Cause:** Backup corruption, incomplete backup
**Solution:**
- Try restoring from previous backup
- Use point-in-time recovery to earlier time
- Escalate to database team for manual recovery

### Issue 3: Application Can't Connect to Restored Database
**Symptom:** Application health check fails, connection errors
**Cause:** Security group misconfiguration, wrong endpoint
**Solution:**
- Verify security group allows application access
- Check database endpoint in application configuration
- Verify database is in correct VPC and subnet
- Check database status (must be "available")

### Issue 4: Performance Degradation After Restore
**Symptom:** Slow queries, high latency
**Cause:** Missing indexes, outdated statistics
**Solution:**
- Rebuild indexes: `REINDEX DATABASE production;`
- Update statistics: `ANALYZE;`
- Increase instance size if needed
- Check for slow queries in logs
```

#### 8.4 Include Recovery Time Estimates

**Recovery Time Breakdown:**
```markdown
## Recovery Time Estimates

### Production PostgreSQL (RTO: 30 minutes)
**Step 1:** Identify latest backup (5 min)
**Step 2:** Restore backup (15 min)
**Step 3:** Validate restoration (5 min)
**Step 4:** Update application configuration (3 min)
**Step 5:** Test application functionality (2 min)
**Total:** 30 minutes ✅

### User Uploads S3 (RTO: 2 hours)
**Step 1:** Identify latest backup (10 min)
**Step 2:** Restore from cross-region replica (60 min)
**Step 3:** Validate restoration (20 min)
**Step 4:** Update application configuration (10 min)
**Step 5:** Test application functionality (20 min)
**Total:** 2 hours ✅

### Analytics MongoDB (RTO: 8 hours)
**Step 1:** Identify latest backup (15 min)
**Step 2:** Restore backup (5 hours)
**Step 3:** Validate restoration (1 hour)
**Step 4:** Update application configuration (30 min)
**Step 5:** Test application functionality (1 hour 15 min)
**Total:** 8 hours ✅
```

### Quality Checklist

- [ ] Recovery runbooks written for all data assets
- [ ] Validation procedures documented
- [ ] Troubleshooting guides created
- [ ] Recovery time estimates included
- [ ] Contact information documented
- [ ] Runbooks tested and validated

### Common Mistakes

❌ **No recovery documentation** — Team doesn't know how to recover  
✅ **Detailed runbooks** — Step-by-step procedures with commands

❌ **Untested runbooks** — Procedures don't work in real scenario  
✅ **Regular testing** — Test recovery procedures quarterly

---

## Step 9: Test Backup Recovery (60-120 minutes)

### Objective

Validate backups are restorable and meet RTO/RPO requirements.

### Actions

#### 9.1 Perform Test Restores

**Test Frequency:**
- **Mission-Critical:** Monthly
- **Critical:** Quarterly
- **Important:** Semi-annually
- **Normal:** Annually

**Test Restore Procedure:**
```bash
#!/bin/bash
# test-restore.sh
# Runs monthly for mission-critical data

set -e

TEST_DATE=$(date +%Y-%m-%d)

echo "[TEST] Starting backup recovery test for $TEST_DATE..."

# Step 1: Find latest backup
LATEST_SNAPSHOT=$(aws rds describe-db-snapshots \
  --db-instance-identifier production-db \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].DBSnapshotIdentifier' \
  --output text)

echo "[TEST] Latest snapshot: $LATEST_SNAPSHOT"

# Step 2: Restore to test instance
START_TIME=$(date +%s)

aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier test-restore-$TEST_DATE \
  --db-snapshot-identifier "$LATEST_SNAPSHOT" \
  --db-instance-class db.t3.medium \
  --db-subnet-group-name test-subnet-group \
  --publicly-accessible false

echo "[TEST] Waiting for restore to complete..."

aws rds wait db-instance-available \
  --db-instance-identifier test-restore-$TEST_DATE

END_TIME=$(date +%s)
RESTORE_TIME=$(( (END_TIME - START_TIME) / 60 ))

echo "[TEST] Restore completed in $RESTORE_TIME minutes"

# Step 3: Validate data integrity
DB_ENDPOINT=$(aws rds describe-db-instances \
  --db-instance-identifier test-restore-$TEST_DATE \
  --query 'DBInstances[0].Endpoint.Address' \
  --output text)

USER_COUNT=$(psql -h "$DB_ENDPOINT" -U postgres -d production -t -c "SELECT COUNT(*) FROM users;" | tr -d ' ')
ORDER_COUNT=$(psql -h "$DB_ENDPOINT" -U postgres -d production -t -c "SELECT COUNT(*) FROM orders;" | tr -d ' ')

echo "[TEST] Data integrity check:"
echo "  Users: $USER_COUNT"
echo "  Orders: $ORDER_COUNT"

if [ "$USER_COUNT" -gt 0 ] && [ "$ORDER_COUNT" -gt 0 ]; then
  echo "✅ Data integrity validated"
else
  echo "❌ Data integrity validation failed"
  exit 1
fi

# Step 4: Measure RPO
LATEST_ORDER=$(psql -h "$DB_ENDPOINT" -U postgres -d production -t -c "SELECT MAX(created_at) FROM orders;")
CURRENT_TIME=$(date -u +"%Y-%m-%d %H:%M:%S")

RPO_SECONDS=$(( $(date -d "$CURRENT_TIME" +%s) - $(date -d "$LATEST_ORDER" +%s) ))
RPO_MINUTES=$(( RPO_SECONDS / 60 ))

echo "[TEST] RPO: $RPO_MINUTES minutes (target: < 5 minutes)"

if [ "$RPO_MINUTES" -lt 5 ]; then
  echo "✅ RPO requirement met"
else
  echo "⚠️  RPO exceeds target: $RPO_MINUTES minutes"
fi

# Step 5: Clean up test instance
aws rds delete-db-instance \
  --db-instance-identifier test-restore-$TEST_DATE \
  --skip-final-snapshot

echo "[TEST] Test instance deleted"

# Step 6: Document results
cat > /tmp/test-restore-report-$TEST_DATE.txt <<EOF
Backup Recovery Test Report - $TEST_DATE
========================================

Snapshot: $LATEST_SNAPSHOT
Restore Time: $RESTORE_TIME minutes (RTO target: 30 minutes)
RPO: $RPO_MINUTES minutes (RPO target: 5 minutes)
User Count: $USER_COUNT
Order Count: $ORDER_COUNT
Data Integrity: PASS
RTO Met: $([ $RESTORE_TIME -lt 30 ] && echo "YES" || echo "NO")
RPO Met: $([ $RPO_MINUTES -lt 5 ] && echo "YES" || echo "NO")

Conclusion: $([ $RESTORE_TIME -lt 30 ] && [ $RPO_MINUTES -lt 5 ] && echo "PASS ✅" || echo "FAIL ❌")
EOF

# Email report
aws ses send-email \
  --from backups@company.com \
  --to engineering@company.com \
  --subject "Backup Recovery Test Report - $TEST_DATE" \
  --text file:///tmp/test-restore-report-$TEST_DATE.txt

echo "[TEST] Backup recovery test complete ✅"
```

#### 9.2 Validate Data Integrity After Restore

**Data Integrity Checks:**
```sql
-- Check record counts
SELECT 'users' AS table_name, COUNT(*) AS record_count FROM users
UNION ALL
SELECT 'orders', COUNT(*) FROM orders
UNION ALL
SELECT 'products', COUNT(*) FROM products;

-- Check for data corruption
SELECT * FROM users WHERE email IS NULL OR email = '';
SELECT * FROM orders WHERE total_amount < 0;

-- Check foreign key integrity
SELECT o.id, o.user_id 
FROM orders o 
LEFT JOIN users u ON o.user_id = u.id 
WHERE u.id IS NULL;

-- Check latest records
SELECT MAX(created_at) AS latest_user FROM users;
SELECT MAX(created_at) AS latest_order FROM orders;
```

#### 9.3 Measure Recovery Time (Actual vs. RTO)

**RTO Measurement:**
```markdown
## RTO Measurement Results

### Production PostgreSQL
**Target RTO:** 30 minutes
**Actual RTO:** 28 minutes ✅
**Breakdown:**
- Identify backup: 4 minutes
- Restore backup: 18 minutes
- Validate data: 4 minutes
- Update config: 1 minute
- Test app: 1 minute

### User Uploads S3
**Target RTO:** 2 hours
**Actual RTO:** 1 hour 45 minutes ✅
**Breakdown:**
- Identify backup: 8 minutes
- Restore backup: 55 minutes
- Validate data: 18 minutes
- Update config: 8 minutes
- Test app: 16 minutes
```

#### 9.4 Document Test Results and Lessons Learned

**Test Results Template:**
```markdown
# Backup Recovery Test Results

**Test Date:** 2026-09-09
**Test Type:** Full restore test
**Data Asset:** Production PostgreSQL
**Tester:** John Doe

## Test Objectives
- [ ] Validate backup is restorable
- [ ] Measure actual RTO vs. target
- [ ] Measure actual RPO vs. target
- [ ] Validate data integrity
- [ ] Test recovery runbook accuracy

## Test Results

### RTO Results
**Target RTO:** 30 minutes
**Actual RTO:** 28 minutes ✅
**Status:** PASS

### RPO Results
**Target RPO:** 5 minutes
**Actual RPO:** 3 minutes ✅
**Status:** PASS

### Data Integrity
**User Count:** 50,000 (expected: 50,000) ✅
**Order Count:** 100,000 (expected: 100,000) ✅
**Latest Order:** 2026-09-09 14:57:00 (3 minutes ago) ✅
**Status:** PASS

### Runbook Accuracy
**Runbook Steps:** 5
**Steps Executed Successfully:** 5 ✅
**Steps Requiring Updates:** 0
**Status:** PASS

## Lessons Learned

1. **Restore faster than expected:** Actual restore time was 18 minutes vs. estimated 20 minutes. Database size has not grown as much as projected.

2. **Runbook accurate:** All runbook steps executed successfully without modifications. No updates needed.

3. **Data integrity excellent:** No data corruption detected. All foreign key constraints intact.

4. **RPO better than target:** Actual RPO was 3 minutes vs. target 5 minutes. Transaction log backups are working well.

## Recommendations

1. ✅ Continue monthly testing
2. ✅ No changes needed to backup strategy
3. ✅ RTO/RPO targets are achievable and being met

## Next Test
**Scheduled:** 2026-10-09 (monthly)
**Type:** Full restore test
```

#### 9.5 Update Runbooks Based on Test Findings

**Runbook Updates:**
- Correct estimated times based on actual measurements
- Add troubleshooting steps for issues encountered
- Update commands if syntax changed
- Add missing validation steps

### Quality Checklist

- [ ] Test restores performed (monthly, quarterly, semi-annually, annually)
- [ ] Data integrity validated
- [ ] Recovery time measured (actual vs. RTO)
- [ ] Test results documented
- [ ] Lessons learned captured
- [ ] Runbooks updated based on findings

### Common Mistakes

❌ **No backup testing** — Backups fail when needed  
✅ **Regular testing** — Test monthly for mission-critical data

❌ **No documentation of results** — Can't track improvements  
✅ **Detailed test reports** — Document RTO/RPO, data integrity, lessons learned

---

## Step 10: Monitor and Optimize (30-60 minutes)

### Objective

Ensure backup health and optimize costs.

### Actions

#### 10.1 Monitor Backup Success/Failure Rates

**CloudWatch Metrics:**
```yaml
Metrics:
  - Name: BackupSuccess
    Namespace: CustomBackups
    Dimensions:
      - Name: DataAsset
        Value: ProductionPostgreSQL
    Statistic: Sum
    Period: 86400  # Daily
    
  - Name: BackupFailure
    Namespace: CustomBackups
    Dimensions:
      - Name: DataAsset
        Value: ProductionPostgreSQL
    Statistic: Sum
    Period: 86400  # Daily
    
  - Name: BackupDuration
    Namespace: CustomBackups
    Dimensions:
      - Name: DataAsset
        Value: ProductionPostgreSQL
    Statistic: Average
    Period: 86400  # Daily
```

**Backup Success Rate Dashboard:**
```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["CustomBackups", "BackupSuccess", {"stat": "Sum"}],
          [".", "BackupFailure", {"stat": "Sum"}]
        ],
        "period": 86400,
        "stat": "Sum",
        "region": "us-east-1",
        "title": "Backup Success/Failure Rate",
        "yAxis": {
          "left": {
            "min": 0
          }
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["CustomBackups", "BackupDuration", {"stat": "Average"}]
        ],
        "period": 86400,
        "stat": "Average",
        "region": "us-east-1",
        "title": "Backup Duration (minutes)",
        "yAxis": {
          "left": {
            "min": 0
          }
        }
      }
    }
  ]
}
```

#### 10.2 Monitor Backup Storage Usage and Costs

**Storage Cost Tracking:**
```bash
#!/bin/bash
# backup-cost-report.sh
# Runs monthly to track backup costs

set -e

REPORT_MONTH=$(date +%Y-%m)

echo "Backup Cost Report - $REPORT_MONTH"
echo "==================================="
echo ""

# RDS backup storage cost
RDS_BACKUP_SIZE=$(aws cloudwatch get-metric-statistics \
  --namespace AWS/RDS \
  --metric-name BackupRetentionPeriodStorageUsed \
  --dimensions Name=DBInstanceIdentifier,Value=production-db \
  --start-time $(date -u -d '1 month ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 2592000 \
  --statistics Average \
  --query 'Datapoints[0].Average' \
  --output text)

RDS_BACKUP_COST=$(echo "$RDS_BACKUP_SIZE * 0.095" | bc)  # $0.095/GB-month

echo "RDS Backup Storage:"
echo "  Size: ${RDS_BACKUP_SIZE}GB"
echo "  Cost: \$${RDS_BACKUP_COST}/month"
echo ""

# S3 backup storage cost
S3_STANDARD_SIZE=$(aws cloudwatch get-metric-statistics \
  --namespace AWS/S3 \
  --metric-name BucketSizeBytes \
  --dimensions Name=BucketName,Value=company-backups,Name=StorageType,Value=StandardStorage \
  --start-time $(date -u -d '1 month ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 2592000 \
  --statistics Average \
  --query 'Datapoints[0].Average' \
  --output text)

S3_STANDARD_GB=$(echo "$S3_STANDARD_SIZE / 1073741824" | bc)  # Convert to GB
S3_STANDARD_COST=$(echo "$S3_STANDARD_GB * 0.023" | bc)  # $0.023/GB-month

echo "S3 Standard Storage:"
echo "  Size: ${S3_STANDARD_GB}GB"
echo "  Cost: \$${S3_STANDARD_COST}/month"
echo ""

# S3 Glacier storage cost
S3_GLACIER_SIZE=$(aws cloudwatch get-metric-statistics \
  --namespace AWS/S3 \
  --metric-name BucketSizeBytes \
  --dimensions Name=BucketName,Value=company-backups,Name=StorageType,Value=GlacierStorage \
  --start-time $(date -u -d '1 month ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 2592000 \
  --statistics Average \
  --query 'Datapoints[0].Average' \
  --output text)

S3_GLACIER_GB=$(echo "$S3_GLACIER_SIZE / 1073741824" | bc)  # Convert to GB
S3_GLACIER_COST=$(echo "$S3_GLACIER_GB * 0.004" | bc)  # $0.004/GB-month

echo "S3 Glacier Storage:"
echo "  Size: ${S3_GLACIER_GB}GB"
echo "  Cost: \$${S3_GLACIER_COST}/month"
echo ""

# Total cost
TOTAL_COST=$(echo "$RDS_BACKUP_COST + $S3_STANDARD_COST + $S3_GLACIER_COST" | bc)

echo "Total Backup Cost: \$${TOTAL_COST}/month"
echo ""

# Cost trend
PREVIOUS_MONTH_COST=$(cat /tmp/backup-cost-previous-month.txt 2>/dev/null || echo "0")
COST_CHANGE=$(echo "$TOTAL_COST - $PREVIOUS_MONTH_COST" | bc)
COST_CHANGE_PERCENT=$(echo "scale=2; ($COST_CHANGE / $PREVIOUS_MONTH_COST) * 100" | bc)

echo "Cost Trend:"
echo "  Previous Month: \$${PREVIOUS_MONTH_COST}"
echo "  Current Month: \$${TOTAL_COST}"
echo "  Change: \$${COST_CHANGE} (${COST_CHANGE_PERCENT}%)"
echo ""

# Save current month cost for next month comparison
echo "$TOTAL_COST" > /tmp/backup-cost-previous-month.txt

echo "Backup cost report complete"
```

#### 10.3 Optimize Backup Schedules and Retention

**Optimization Opportunities:**

1. **Reduce backup frequency for low-criticality data:**
   - Change from daily to weekly backups
   - Save storage and bandwidth costs

2. **Implement tiered retention:**
   - Keep daily backups for 7 days
   - Keep weekly backups for 30 days
   - Keep monthly backups for 1 year
   - Transition to Glacier after 30 days

3. **Use incremental backups instead of full backups:**
   - Reduce backup size by 80-90%
   - Faster backup completion
   - Lower storage costs

4. **Compress backups:**
   - Reduce storage size by 50-70%
   - Lower storage and transfer costs
   - Slightly slower restore (decompression time)

**Optimization Example:**
```markdown
## Backup Optimization Results

### Before Optimization
**Analytics MongoDB:**
- Backup Method: Daily full backup
- Backup Size: 2TB per backup
- Monthly Storage: 60TB (30 days × 2TB)
- Monthly Cost: $1,380 (60TB × $0.023/GB)

### After Optimization
**Analytics MongoDB:**
- Backup Method: Weekly full + daily incremental
- Full Backup Size: 2TB (weekly)
- Incremental Backup Size: 200GB (daily)
- Monthly Storage: 14TB (4 weekly × 2TB + 26 daily × 200GB)
- Monthly Cost: $322 (14TB × $0.023/GB)

**Savings:** $1,058/month (77% reduction) ✅
```

#### 10.4 Implement Backup Compression and Deduplication

**Compression:**
```bash
# PostgreSQL backup with compression
pg_dump -h production-db.example.com -U postgres production \
  | gzip -9 > /backups/production-$(date +%Y-%m-%d).sql.gz

# MongoDB backup with compression
mongodump --host mongodb-cluster.example.com --gzip \
  --out /backups/mongodb-$(date +%Y-%m-%d)

# Compress before uploading to S3
tar -czf backup.tar.gz /data
aws s3 cp backup.tar.gz s3://company-backups/
```

**Deduplication (AWS Backup):**
```json
{
  "BackupPlan": {
    "BackupPlanName": "DedupBackup",
    "AdvancedBackupSettings": [
      {
        "ResourceType": "EBS",
        "BackupOptions": {
          "WindowsVSS": "enabled",
          "Deduplication": "enabled"
        }
      }
    ]
  }
}
```

#### 10.5 Review and Update Backup Strategy Quarterly

**Quarterly Review Checklist:**
```markdown
## Quarterly Backup Strategy Review

**Review Date:** 2026-09-09
**Reviewer:** John Doe
**Next Review:** 2026-12-09

### Data Inventory Changes
- [ ] New data sources added?
- [ ] Data sources removed?
- [ ] Data sizes changed significantly?
- [ ] Data criticality changed?

### RTO/RPO Changes
- [ ] Business requirements changed?
- [ ] RTO/RPO targets still achievable?
- [ ] Actual RTO/RPO meeting targets?

### Backup Strategy Changes
- [ ] Backup methods still appropriate?
- [ ] Backup schedules optimized?
- [ ] Retention policies still compliant?
- [ ] 3-2-1 backup rule maintained?

### Cost Optimization
- [ ] Backup costs within budget?
- [ ] Optimization opportunities identified?
- [ ] Tiered storage implemented?
- [ ] Compression/deduplication enabled?

### Testing and Validation
- [ ] Backup tests performed on schedule?
- [ ] All tests passed?
- [ ] Runbooks updated?
- [ ] Team trained on procedures?

### Compliance
- [ ] GDPR compliance maintained?
- [ ] HIPAA compliance maintained?
- [ ] SOC 2 compliance maintained?
- [ ] PCI DSS compliance maintained?

### Action Items
1. [Action item 1]
2. [Action item 2]
3. [Action item 3]

### Recommendations
1. [Recommendation 1]
2. [Recommendation 2]
3. [Recommendation 3]
```

### Quality Checklist

- [ ] Backup monitoring implemented (success/failure rates, duration)
- [ ] Storage costs tracked (monthly reports)
- [ ] Backup strategy optimized (schedules, retention, compression)
- [ ] Compression and deduplication enabled
- [ ] Quarterly reviews scheduled
- [ ] Cost optimization opportunities identified

### Common Mistakes

❌ **No backup monitoring** — Failures go unnoticed  
✅ **CloudWatch dashboards** — Monitor success rates, duration, costs

❌ **No cost tracking** — Backup costs spiral out of control  
✅ **Monthly cost reports** — Track storage usage and costs

❌ **Set and forget** — Backup strategy becomes outdated  
✅ **Quarterly reviews** — Update strategy based on changes

---

## Summary

You've now completed the backup and recovery planning process! You should have:

✅ **Data inventory complete** — All data sources identified and categorized  
✅ **RTO/RPO defined** — Recovery objectives for all data assets  
✅ **Backup strategy selected** — Appropriate backup methods chosen  
✅ **Backup schedule designed** — Frequency and timing optimized  
✅ **Retention policies defined** — Short-term, medium-term, long-term retention  
✅ **Backup automation implemented** — Automated backups, encryption, monitoring  
✅ **3-2-1 backup rule implemented** — 3 copies, 2 media, 1 offsite  
✅ **Recovery runbooks created** — Step-by-step procedures documented  
✅ **Backup recovery tested** — RTO/RPO validated, data integrity confirmed  
✅ **Monitoring and optimization** — Backup health monitored, costs optimized

**Next Steps:**
1. Execute first backup recovery test
2. Measure actual RTO/RPO achieved
3. Update runbooks based on test results
4. Schedule regular backup tests (monthly, quarterly, annually)
5. Review and update backup strategy quarterly

**Success Metrics:**
- RTO/RPO requirements met in tests
- Backup tests performed on schedule
- Zero data loss in tests
- Team trained and prepared
- Comprehensive documentation maintained
- Backup costs within budget
