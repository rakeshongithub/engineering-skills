# Disaster Recovery - Step-by-Step Instructions

This guide provides detailed instructions for designing and implementing disaster recovery (DR) plans and procedures to ensure business continuity.

**Estimated Time:** 6-12 hours  
**Complexity:** Advanced  
**Prerequisites:** Architecture discovery, observability design, infrastructure as code

---

## Overview

Disaster recovery planning is critical for ensuring business continuity in the face of catastrophic failures. This skill covers how to define recovery objectives (RTO/RPO), select DR strategies, design DR architecture, implement failover mechanisms, and test DR procedures.

**Key Outcomes:**
- Clear RTO/RPO requirements for all systems
- Appropriate DR strategy selected (backup/restore, pilot light, warm standby, hot standby, multi-site)
- Automated failover and recovery procedures
- Regular DR testing and validation
- Comprehensive DR documentation

---

## Step 1: Define RTO and RPO Requirements (60-90 minutes)

### Objective

Establish recovery objectives for each system based on business impact.

### Actions

#### 1.1 Conduct Business Impact Analysis (BIA)

**Questions to Answer:**
- What is the business impact of each system being unavailable?
- How much revenue is lost per hour of downtime?
- What are the regulatory/compliance implications?
- What is the impact on customer trust and brand reputation?

**Document:**
```markdown
## Business Impact Analysis

### E-commerce Platform
**Business Impact:** $50,000/hour revenue loss
**Customer Impact:** High (customers cannot purchase)
**Regulatory Impact:** None
**Brand Impact:** High (negative reviews, lost trust)
**Criticality:** Mission-Critical
```

#### 1.2 Define RTO per System

**RTO (Recovery Time Objective):** Maximum acceptable downtime

**RTO Categories:**
- **Mission-Critical:** < 1 minute (e.g., payment processing)
- **Critical:** < 1 hour (e.g., e-commerce platform)
- **Important:** < 4 hours (e.g., reporting system)
- **Normal:** < 24 hours (e.g., internal tools)

**Document:**
```markdown
## RTO Requirements

| System | Criticality | RTO | Rationale |
|--------|-------------|-----|------------|
| Payment Processing | Mission-Critical | 30 seconds | $100,000/hour revenue loss |
| E-commerce Platform | Critical | 15 minutes | $50,000/hour revenue loss |
| Reporting System | Important | 4 hours | Non-real-time, can tolerate delay |
| Internal Tools | Normal | 24 hours | Low business impact |
```

#### 1.3 Define RPO per System

**RPO (Recovery Point Objective):** Maximum acceptable data loss

**RPO Categories:**
- **Near-Zero:** < 1 minute (e.g., financial transactions)
- **Low:** < 15 minutes (e.g., e-commerce orders)
- **Medium:** < 1 hour (e.g., user-generated content)
- **High:** < 24 hours (e.g., analytics data)

**Document:**
```markdown
## RPO Requirements

| System | Data Type | RPO | Rationale |
|--------|-----------|-----|------------|
| Payment Processing | Financial transactions | Near-zero | Cannot lose payment data |
| E-commerce Platform | Orders, inventory | 5 minutes | Minimize lost orders |
| Reporting System | Analytics data | 1 hour | Can re-process data |
| Internal Tools | User data | 24 hours | Low data change frequency |
```

#### 1.4 Categorize Systems by Criticality

**Criticality Tiers:**
- **Tier 1 (Mission-Critical):** RTO < 1 min, RPO < 1 min
- **Tier 2 (Critical):** RTO < 1 hour, RPO < 15 min
- **Tier 3 (Important):** RTO < 4 hours, RPO < 1 hour
- **Tier 4 (Normal):** RTO < 24 hours, RPO < 24 hours

**Document:**
```markdown
## System Criticality Tiers

**Tier 1 (Mission-Critical):**
- Payment Processing Service
- Authentication Service

**Tier 2 (Critical):**
- E-commerce Platform
- Customer Database

**Tier 3 (Important):**
- Reporting System
- Analytics Pipeline

**Tier 4 (Normal):**
- Internal Admin Tools
- Development Environments
```

#### 1.5 Document Financial Impact

**Cost of Downtime:**
- Revenue loss per hour
- Customer acquisition cost impact
- Regulatory fines (if applicable)
- Brand reputation damage

**Document:**
```markdown
## Financial Impact of Downtime

### Payment Processing (Tier 1)
**Revenue Loss:** $100,000/hour
**Customer Impact:** 10,000 failed transactions/hour
**Regulatory Fines:** Potential PCI DSS penalties
**Total Impact:** $100,000+/hour

### E-commerce Platform (Tier 2)
**Revenue Loss:** $50,000/hour
**Customer Impact:** 5,000 lost sales/hour
**Brand Damage:** Negative reviews, social media complaints
**Total Impact:** $50,000-75,000/hour
```

#### 1.6 Obtain Stakeholder Approval

**Stakeholders:**
- Executive leadership (CEO, CFO)
- Engineering leadership (CTO, VP Engineering)
- Product management
- Finance (budget approval)

**Approval Document:**
```markdown
## RTO/RPO Approval

**Approved By:** Jane Doe (CEO), John Smith (CTO)
**Date:** 2026-09-09
**Budget Approved:** $200,000/year for DR infrastructure

**Approved RTO/RPO:**
- Payment Processing: RTO 30s, RPO near-zero
- E-commerce Platform: RTO 15min, RPO 5min
- Reporting System: RTO 4hr, RPO 1hr
- Internal Tools: RTO 24hr, RPO 24hr
```

### Quality Checklist

- [ ] Business impact analysis completed for all systems
- [ ] RTO defined for each system (< 1 min, < 1 hr, < 4 hr, < 24 hr)
- [ ] RPO defined for each system (< 1 min, < 15 min, < 1 hr, < 24 hr)
- [ ] Systems categorized by criticality (Tier 1-4)
- [ ] Financial impact documented (revenue loss, fines, brand damage)
- [ ] Stakeholder approval obtained (CEO, CTO, budget)

### Common Mistakes

❌ **Unrealistic RTO/RPO** — Set RTO 1 minute without infrastructure to support it  
✅ **Align RTO/RPO with DR strategy** — Ensure DR strategy can meet requirements

❌ **No business impact analysis** — Set RTO/RPO arbitrarily  
✅ **Quantify business impact** — Calculate revenue loss per hour

---

## Step 2: Select DR Strategy (45-90 minutes)

### Objective

Choose appropriate DR strategy based on RTO/RPO requirements and budget.

### Actions

#### 2.1 Evaluate DR Strategy Options

**DR Strategy Spectrum:**

**1. Backup/Restore:**
- **RTO:** Hours to days
- **RPO:** Hours
- **Cost:** Very low (backup storage only)
- **Use Case:** Non-critical systems, development environments
- **Infrastructure:** No DR infrastructure, restore on-demand

**2. Pilot Light:**
- **RTO:** Hours
- **RPO:** Minutes
- **Cost:** Low (minimal DR infrastructure)
- **Use Case:** Important systems, moderate criticality
- **Infrastructure:** Database replication, minimal compute (scaled up on failover)

**3. Warm Standby:**
- **RTO:** Minutes
- **RPO:** Seconds
- **Cost:** Medium (scaled-down DR site)
- **Use Case:** Critical systems, e-commerce, SaaS
- **Infrastructure:** Scaled-down DR site (50% capacity), auto-scaling on failover

**4. Hot Standby:**
- **RTO:** Seconds
- **RPO:** Near-zero
- **Cost:** High (full DR site, 2x infrastructure)
- **Use Case:** Mission-critical systems, payment processing, healthcare
- **Infrastructure:** Full DR site (100% capacity), synchronous replication

**5. Multi-Site Active/Active:**
- **RTO:** None (no failover)
- **RPO:** None
- **Cost:** Very high (2x+ infrastructure)
- **Use Case:** Global services, zero-downtime requirements
- **Infrastructure:** Multiple active sites, global load balancing

#### 2.2 Match Strategy to RTO/RPO

**Decision Matrix:**

| RTO | RPO | DR Strategy | Cost | Example |
|-----|-----|-------------|------|----------|
| < 1 min | < 1 min | Hot Standby or Multi-Site | High | Payment processing |
| < 1 hour | < 15 min | Warm Standby | Medium | E-commerce platform |
| < 4 hours | < 1 hour | Pilot Light | Low | Reporting system |
| < 24 hours | < 24 hours | Backup/Restore | Very Low | Internal tools |

**Document:**
```markdown
## DR Strategy Selection

### Payment Processing (Tier 1)
**RTO:** 30 seconds  
**RPO:** Near-zero  
**Selected Strategy:** Hot Standby  
**Rationale:** RTO/RPO requirements mandate full DR site with synchronous replication  
**Cost:** $100,000/year (2x infrastructure)  
**ROI:** Prevents $100,000/hour revenue loss

### E-commerce Platform (Tier 2)
**RTO:** 15 minutes  
**RPO:** 5 minutes  
**Selected Strategy:** Warm Standby  
**Rationale:** RTO/RPO achievable with scaled-down DR site and auto-scaling  
**Cost:** $60,000/year (60% of primary infrastructure)  
**ROI:** Prevents $50,000/hour revenue loss
```

#### 2.3 Consider Multi-Region vs. Multi-Cloud

**Multi-Region (Same Cloud Provider):**
- **Pros:** Easier to manage, consistent tooling, lower latency
- **Cons:** Cloud provider outage affects all regions
- **Use Case:** Most common, good for RTO < 1 hour

**Multi-Cloud (Different Cloud Providers):**
- **Pros:** Protection against cloud provider outage
- **Cons:** Complex to manage, inconsistent tooling, higher cost
- **Use Case:** Mission-critical systems, regulatory requirements

**Document:**
```markdown
## Multi-Region vs. Multi-Cloud

**Decision:** Multi-Region (AWS us-east-1 primary, us-west-2 DR)

**Rationale:**
- AWS region outage is rare (< 0.01% annually)
- Multi-region sufficient for RTO/RPO requirements
- Multi-cloud adds 50% complexity and 30% cost
- Team expertise in AWS, not multi-cloud

**Exception:** Payment processing (Tier 1) will use multi-cloud (AWS + GCP) for maximum resilience
```

#### 2.4 Assess Cost vs. Recovery Time Trade-offs

**Cost Analysis:**

| DR Strategy | Infrastructure Cost | Operational Cost | Total Annual Cost |
|-------------|---------------------|------------------|-------------------|
| Backup/Restore | $500/month | $100/month | $7,200/year |
| Pilot Light | $2,000/month | $500/month | $30,000/year |
| Warm Standby | $5,000/month | $1,000/month | $72,000/year |
| Hot Standby | $10,000/month | $2,000/month | $144,000/year |
| Multi-Site | $15,000/month | $3,000/month | $216,000/year |

**ROI Calculation:**
```markdown
## DR Cost vs. Downtime Cost

### E-commerce Platform (Warm Standby)
**DR Cost:** $72,000/year  
**Downtime Cost:** $50,000/hour  
**Break-Even:** 1.44 hours of prevented downtime per year  
**Expected Outages:** 2-3 hours/year (without DR)  
**ROI:** $100,000-150,000/year savings
```

#### 2.5 Document Decision Rationale

**DR Strategy Document:**
```markdown
## Disaster Recovery Strategy

### Executive Summary
We will implement a tiered DR strategy based on system criticality:
- Tier 1 (Mission-Critical): Hot Standby ($144,000/year)
- Tier 2 (Critical): Warm Standby ($72,000/year)
- Tier 3 (Important): Pilot Light ($30,000/year)
- Tier 4 (Normal): Backup/Restore ($7,200/year)

**Total DR Budget:** $253,200/year  
**Expected ROI:** $500,000+/year (prevented downtime)

### Rationale
- Aligns with RTO/RPO requirements
- Cost-effective (prevents $500,000+ annual downtime cost)
- Meets compliance requirements (SOC 2, PCI DSS)
- Scalable (can upgrade Tier 3 to Tier 2 if needed)
```

### Quality Checklist

- [ ] All DR strategies evaluated (backup/restore, pilot light, warm standby, hot standby, multi-site)
- [ ] Strategy matched to RTO/RPO requirements
- [ ] Cost vs. recovery time analyzed
- [ ] Multi-region vs. multi-cloud assessed
- [ ] ROI calculated (DR cost vs. downtime cost)
- [ ] Decision documented with clear rationale
- [ ] Stakeholder approval obtained

### Common Mistakes

❌ **One-size-fits-all DR** — Use same strategy for all systems  
✅ **Tiered DR strategy** — Match strategy to criticality

❌ **Ignoring cost** — Choose hot standby for all systems  
✅ **Cost-benefit analysis** — Calculate ROI for each tier

---

## Step 3: Design DR Architecture (90-180 minutes)

### Objective

Design DR infrastructure and data replication strategy.

### Actions

#### 3.1 Design DR Site Architecture

**Primary Site:**
- Region: us-east-1
- Availability Zones: us-east-1a, us-east-1b, us-east-1c
- Compute: 20 EC2 instances (production)
- Database: RDS PostgreSQL (Multi-AZ)
- Storage: S3 (versioning enabled)

**DR Site (Warm Standby):**
- Region: us-west-2
- Availability Zones: us-west-2a, us-west-2b
- Compute: 10 EC2 instances (50% capacity, auto-scaling)
- Database: RDS PostgreSQL (read replica)
- Storage: S3 (cross-region replication)

**Architecture Diagram:**
```
┌─────────────────────────────────────────────────────────────┐
│                    Route 53 (DNS)                           │
│              Health Checks + Failover                       │
└─────────────────────────────────────────────────────────────┘
                        │
        ┌───────────────┴───────────────┐
        │                               │
┌───────▼────────┐              ┌───────▼────────┐
│  Primary Site  │              │    DR Site     │
│   us-east-1    │              │   us-west-2    │
│                │              │                │
│ ┌────────────┐ │              │ ┌────────────┐ │
│ │    ALB     │ │              │ │    ALB     │ │
│ └────────────┘ │              │ └────────────┘ │
│       │        │              │       │        │
│ ┌─────▼──────┐ │              │ ┌─────▼──────┐ │
│ │ 20 EC2     │ │              │ │ 10 EC2     │ │
│ │ Instances  │ │              │ │ Instances  │ │
│ └────────────┘ │              │ └────────────┘ │
│       │        │              │       │        │
│ ┌─────▼──────┐ │   Async      │ ┌─────▼──────┐ │
│ │ RDS Primary│◄┼──Replication─┤ │ RDS Replica│ │
│ │ PostgreSQL │ │              │ │ PostgreSQL │ │
│ └────────────┘ │              │ └────────────┘ │
│       │        │              │       │        │
│ ┌─────▼──────┐ │   Cross-     │ ┌─────▼──────┐ │
│ │ S3 Bucket  │◄┼──Region      ┤ │ S3 Bucket  │ │
│ │            │ │  Replication │ │            │ │
│ └────────────┘ │              │ └────────────┘ │
└────────────────┘              └────────────────┘
```

#### 3.2 Plan Data Replication

**Database Replication:**

**Synchronous Replication (Hot Standby):**
- **How:** Write to primary and DR simultaneously
- **RPO:** Near-zero (no data loss)
- **Latency Impact:** High (wait for DR write confirmation)
- **Use Case:** Mission-critical systems (payment processing)

**Asynchronous Replication (Warm Standby):**
- **How:** Write to primary, replicate to DR with lag
- **RPO:** Seconds to minutes (replication lag)
- **Latency Impact:** Low (no wait for DR)
- **Use Case:** Critical systems (e-commerce)

**Snapshot-Based Replication (Pilot Light):**
- **How:** Periodic snapshots (hourly, daily)
- **RPO:** Hours (snapshot frequency)
- **Latency Impact:** None
- **Use Case:** Important systems (reporting)

**Document:**
```markdown
## Data Replication Strategy

### Payment Processing (Tier 1)
**Method:** Synchronous replication (PostgreSQL with synchronous_commit = on)
**RPO:** Near-zero
**Replication Lag:** < 1 second
**Configuration:**
```sql
ALTER SYSTEM SET synchronous_commit = 'remote_apply';
ALTER SYSTEM SET synchronous_standby_names = 'dr_replica';
```

### E-commerce Platform (Tier 2)
**Method:** Asynchronous replication (RDS read replica)
**RPO:** 5 minutes
**Replication Lag:** 30-60 seconds (average)
**Configuration:** RDS read replica in us-west-2
```

#### 3.3 Design Network Connectivity

**VPN Connection:**
- AWS VPN between us-east-1 and us-west-2
- Backup connection for failover

**VPC Peering:**
- VPC peering between primary and DR VPCs
- Low-latency data replication

**Direct Connect (Optional):**
- Dedicated network connection
- Lower latency, higher bandwidth
- Higher cost ($300-500/month)

**Document:**
```markdown
## Network Connectivity

**Primary:** VPC Peering (us-east-1 ↔ us-west-2)
**Backup:** AWS VPN (in case VPC peering fails)

**Configuration:**
- VPC Peering ID: pcx-12345678
- Route tables updated for cross-region traffic
- Security groups allow replication traffic
```

#### 3.4 Plan DNS Failover

**Route 53 Health Checks:**
- Monitor primary site health (HTTP/HTTPS endpoint)
- Failover to DR site if health check fails
- TTL: 60 seconds (fast DNS propagation)

**Failover Configuration:**
```json
{
  "Name": "app.example.com",
  "Type": "A",
  "SetIdentifier": "Primary",
  "Failover": "PRIMARY",
  "AliasTarget": {
    "HostedZoneId": "Z1234567890ABC",
    "DNSName": "primary-alb-123456.us-east-1.elb.amazonaws.com",
    "EvaluateTargetHealth": true
  },
  "HealthCheckId": "hc-primary-12345"
}

{
  "Name": "app.example.com",
  "Type": "A",
  "SetIdentifier": "DR",
  "Failover": "SECONDARY",
  "AliasTarget": {
    "HostedZoneId": "Z0987654321XYZ",
    "DNSName": "dr-alb-789012.us-west-2.elb.amazonaws.com",
    "EvaluateTargetHealth": true
  }
}
```

#### 3.5 Design Load Balancer Failover

**Primary Load Balancer:**
- Application Load Balancer (ALB) in us-east-1
- Target group: primary EC2 instances

**DR Load Balancer:**
- Application Load Balancer (ALB) in us-west-2
- Target group: DR EC2 instances

**Failover:**
- Route 53 health check detects primary ALB failure
- DNS automatically routes traffic to DR ALB
- DR instances auto-scale to 100% capacity

#### 3.6 Plan Database Replication

**RDS Read Replica:**
- Create read replica in us-west-2
- Asynchronous replication from primary
- Promote to primary on failover

**Promotion Process:**
```bash
# Promote read replica to standalone instance
aws rds promote-read-replica \
  --db-instance-identifier dr-database-replica \
  --region us-west-2

# Wait for promotion to complete
aws rds wait db-instance-available \
  --db-instance-identifier dr-database-replica \
  --region us-west-2
```

### Quality Checklist

- [ ] DR site architecture designed (region, availability zones)
- [ ] Data replication strategy defined (synchronous, asynchronous, snapshot)
- [ ] Network connectivity planned (VPN, VPC peering, Direct Connect)
- [ ] DNS failover designed (Route 53 health checks)
- [ ] Load balancer failover planned
- [ ] Database replication configured (read replica, synchronous)

### Common Mistakes

❌ **Single region deployment** — No DR site  
✅ **Multi-region deployment** — Primary and DR in different regions

❌ **No network connectivity** — Can't replicate data  
✅ **VPC peering or VPN** — Enable cross-region replication

---

## Steps 4-10 Summary

**Step 4: Implement Backup Automation** — Automated backups, retention policies, encryption  
**Step 5: Implement Failover Mechanisms** — DNS failover, load balancer failover, database failover  
**Step 6: Create Recovery Runbooks** — Failover, recovery, failback procedures  
**Step 7: Establish DR Testing Plan** — Test scenarios, frequency, success criteria  
**Step 8: Implement Monitoring and Alerting** — Backup monitoring, replication lag, DR site health  
**Step 9: Document and Train** — DR plan document, training materials, on-call rotation  
**Step 10: Test and Validate** — Execute DR test, measure RTO/RPO, document lessons learned

---

## Summary

You've now completed the disaster recovery planning process! You should have:

✅ **RTO/RPO defined** — Recovery objectives for all systems  
✅ **DR strategy selected** — Backup/restore, pilot light, warm standby, hot standby, or multi-site  
✅ **DR architecture designed** — Primary site, DR site, data replication, network connectivity  
✅ **Backup automation implemented** — Automated backups, retention, encryption  
✅ **Failover mechanisms implemented** — DNS, load balancer, database failover  
✅ **Recovery runbooks created** — Failover, recovery, failback procedures  
✅ **DR testing plan established** — Test scenarios, frequency, success criteria  
✅ **Monitoring and alerting configured** — Backup, replication, DR site health  
✅ **Documentation complete** — DR plan, training materials, compliance evidence  
✅ **DR tested and validated** — RTO/RPO measured, lessons learned documented

**Next Steps:**
1. Execute first DR test
2. Measure actual RTO/RPO achieved
3. Update DR plan based on test results
4. Schedule regular DR tests (quarterly)
5. Review and update DR plan after architecture changes

**Success Metrics:**
- RTO/RPO requirements met in DR tests
- DR tested quarterly
- Zero data loss in tests
- Team trained and prepared
- Comprehensive documentation maintained
