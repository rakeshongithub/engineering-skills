# Cloud Architecture Review

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 1-3 weeks

---

## Quick Reference

Review cloud architecture for best practices, cost optimization, security posture, and reliability to ensure scalable, secure, and cost-effective cloud infrastructure.

---

## When to Use

✅ **Use this skill when:**
- Pre-production architecture validation
- Cloud costs growing unsustainably
- Security or compliance audit needed
- Performance or availability issues
- Planning cloud migration or modernization
- Quarterly architecture health check
- Post-incident architecture review

❌ **Don't use when:**
- Active incident response needed
- Application-level code review required
- Pure cost forecasting (no architecture changes)
- Vendor selection or contract negotiation

---

## Key Inputs

**Required:**
- Cloud infrastructure inventory
- Architecture documentation
- Business context and objectives
- Current challenges and pain points
- Metrics (cost, performance, availability)

**Optional:**
- Historical data and incident history
- Team information and capabilities
- Future plans and growth projections
- Compliance requirements

---

## Key Outputs

**Primary:**
- Executive summary with health score
- Well-Architected Framework assessment
- Cost optimization report (savings estimates)
- Security and compliance assessment
- Reliability and disaster recovery analysis
- Prioritized remediation roadmap

**Supporting:**
- Best practices guide
- Infrastructure-as-code recommendations
- Monitoring and observability plan
- Risk register with mitigation strategies

---

## 10-Step Workflow

1. **Preparation and Scoping** - Define scope, objectives, gather documentation
2. **Discovery and Inventory** - Comprehensive resource inventory and architecture mapping
3. **Well-Architected Assessment** - Evaluate against cloud best practices (5 pillars)
4. **Cost Analysis** - Identify waste, right-sizing, reserved instances, storage optimization
5. **Security Review** - IAM, encryption, network security, logging, compliance
6. **Reliability Assessment** - High availability, disaster recovery, fault tolerance
7. **Performance Analysis** - Resource utilization, bottlenecks, caching, database optimization
8. **Operational Excellence** - IaC maturity, CI/CD, monitoring, incident management
9. **Synthesis and Recommendations** - Prioritize findings, create roadmap, estimate ROI
10. **Reporting and Presentation** - Executive summary, technical report, stakeholder presentation

---

## Well-Architected Framework Pillars

### 1. Operational Excellence
- Infrastructure-as-code maturity
- CI/CD and deployment automation
- Monitoring and observability
- Incident response and runbooks

### 2. Security
- IAM and access control (least privilege)
- Data encryption (at rest, in transit)
- Network security (security groups, WAF)
- Logging and audit trails

### 3. Reliability
- High availability (multi-AZ, multi-region)
- Disaster recovery (RTO, RPO)
- Backup and restore procedures
- Fault tolerance and resilience

### 4. Performance Efficiency
- Resource selection and utilization
- Caching strategies (CDN, application, database)
- Database performance and optimization
- Auto-scaling and load balancing

### 5. Cost Optimization
- Right-sizing (eliminate over-provisioning)
- Idle resource elimination
- Reserved instances and savings plans
- Storage lifecycle and tiering

---

## Common Findings

### Cost Optimization (Typical 20-40% savings)
- 💸 Over-provisioned instances (40-60% CPU utilization)
- 💸 Idle resources (stopped instances, unattached volumes)
- 💸 No reserved instances (all on-demand pricing)
- 💸 Inefficient storage (no lifecycle policies, old snapshots)
- 💸 Excessive data transfer costs

### Security
- 🔒 Unencrypted data (RDS, S3, EBS)
- 🔒 Overly permissive IAM (admin access, wildcards)
- 🔒 No audit logging (CloudTrail disabled)
- 🔒 Public resources (S3 buckets, security groups 0.0.0.0/0)
- 🔒 No MFA for privileged accounts

### Reliability
- ⚠️ Single-AZ resources (no redundancy)
- ⚠️ No disaster recovery plan
- ⚠️ Single points of failure
- ⚠️ No automated backups
- ⚠️ Insufficient monitoring and alerting

---

## Quick Decision Guide

### Compute Selection
- **VMs** (EC2, Azure VMs): Full control, long-running workloads
- **Containers** (ECS, AKS, GKE): Microservices, portability
- **Serverless** (Lambda, Functions): Event-driven, variable traffic
- **Managed K8s**: Complex microservices, multi-cloud

### Storage Selection
- **Object** (S3, Blob): Unstructured data, durability
- **Block** (EBS, Managed Disks): Databases, high IOPS
- **File** (EFS, Azure Files): Shared file system
- **Database** (RDS, Azure SQL): Managed databases

### High Availability
- **Single-AZ**: Dev/test, cost-sensitive
- **Multi-AZ**: Production, 99.9%+ uptime
- **Multi-Region**: Global users, 99.99%+ uptime

### Disaster Recovery
- **Backup/Restore**: RTO hours, RPO hours, low cost
- **Pilot Light**: RTO minutes, RPO minutes, medium cost
- **Warm Standby**: RTO minutes, RPO seconds, higher cost
- **Multi-Site**: RTO seconds, RPO near-zero, highest cost

---

## Typical Results

### Cost Savings
- **Quick Wins** (1-2 weeks): 10-15% savings
- **Medium-Term** (1-3 months): 15-25% savings
- **Total** (6 months): 20-40% savings
- **ROI**: 300-500% (first year)

### Security Improvements
- Critical findings: 5-10 → 0
- Encryption coverage: 50-70% → 100%
- MFA adoption: 30-50% → 100%
- Compliance: Non-compliant → Compliant

### Reliability Improvements
- Uptime: 99.5% → 99.9%+
- RTO: Hours → Minutes
- RPO: Hours → Minutes
- Single points of failure: 5-10 → 0

---

## Tools and Resources

### Cloud Provider Tools
- **AWS**: Well-Architected Tool, Trusted Advisor, Cost Explorer, Compute Optimizer
- **Azure**: Advisor, Cost Management, Security Center, Monitor
- **GCP**: Recommender, Cloud Asset Inventory, Security Command Center

### Third-Party Tools
- **Cost Management**: CloudHealth, CloudCheckr, Apptio Cloudability
- **Security**: Prisma Cloud, Dome9, Lacework
- **Multi-Cloud**: Terraform, CloudFormation, Pulumi
- **Monitoring**: Datadog, New Relic, Dynatrace

### Automation Scripts
```bash
# AWS resource inventory
aws resourcegroupstaggingapi get-resources

# Find unencrypted resources
aws rds describe-db-instances --query 'DBInstances[?StorageEncrypted==`false`]'
aws ec2 describe-volumes --query 'Volumes[?Encrypted==`false`]'

# Find idle resources
aws ec2 describe-instances --filters Name=instance-state-name,Values=stopped
aws ec2 describe-volumes --filters Name=status,Values=available

# Cost analysis
aws ce get-cost-and-usage --time-period Start=2024-01-01,End=2024-01-31 --granularity MONTHLY --metrics BlendedCost --group-by Type=DIMENSION,Key=SERVICE
```

---

## Success Metrics

### Immediate (1 month)
- ✅ All critical findings identified
- ✅ Quick wins implemented (10-15% cost savings)
- ✅ Roadmap approved and resourced

### Short-Term (3 months)
- ✅ Security posture improved (high → low risk)
- ✅ Cost savings achieved (20-30%)
- ✅ Disaster recovery plan established

### Long-Term (6-12 months)
- ✅ All recommendations implemented
- ✅ Compliance achieved and maintained
- ✅ Operational excellence maturity improved
- ✅ Regular review cadence established

---

## Common Mistakes to Avoid

❌ **Insufficient scope definition** - Start without clear boundaries  
✅ Define scope upfront, get stakeholder agreement

❌ **Ignoring business context** - Focus only on technical aspects  
✅ Understand business goals and align recommendations

❌ **Incomplete inventory** - Miss shadow IT or undocumented resources  
✅ Use automated discovery tools, check all regions

❌ **Unrealistic recommendations** - Suggest overly complex changes  
✅ Prioritize practical, achievable recommendations

❌ **No prioritization** - Provide long list without priority  
✅ Use risk vs. effort matrix, categorize quick wins

❌ **No follow-up** - Deliver report and disappear  
✅ Establish tracking, schedule reviews, measure success

---

## Related Skills

**Prerequisites:**
- cloud-fundamentals
- networking-basics
- security-fundamentals

**Commonly Followed By:**
- cost-optimization-implementation
- security-hardening
- disaster-recovery-planning
- performance-optimization
- migration-planning

**Works With:**
- infrastructure-as-code
- ci-cd-design
- observability-design
- capacity-planning
- compliance-assessment

---

## Additional Resources

### Documentation
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [Azure Well-Architected Framework](https://docs.microsoft.com/en-us/azure/architecture/framework/)
- [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework)

### Training
- AWS Well-Architected Training
- Azure Architecture Certification
- GCP Professional Cloud Architect

### Community
- Cloud Architecture Slack communities
- AWS re:Invent, Azure Ignite, Google Cloud Next
- FinOps Foundation

---

## Version

**1.0.0** — Initial release

---

## Files

- **SKILL.md** - Comprehensive skill documentation (16,000+ words)
- **skill.json** - Machine-readable metadata
- **instructions.md** - Detailed step-by-step instructions
- **examples.md** - 4 comprehensive real-world examples
- **README.md** - This quick reference guide