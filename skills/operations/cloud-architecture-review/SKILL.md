# Cloud Architecture Review Skill

## Purpose

Review cloud architecture for best practices, cost optimization, security posture, and reliability to ensure scalable, secure, and cost-effective cloud infrastructure.

## When to Use

Use this skill when:

- **Pre-Production Review**: Validating cloud architecture before production deployment
- **Cost Optimization**: Cloud costs are higher than expected or growing unsustainably
- **Security Audit**: Ensuring cloud infrastructure meets security and compliance requirements
- **Performance Issues**: Cloud services experiencing latency, throughput, or availability problems
- **Scaling Challenges**: Current architecture cannot handle growth or traffic spikes
- **Migration Planning**: Moving from on-premises to cloud or between cloud providers
- **Multi-Cloud Strategy**: Implementing or optimizing multi-cloud or hybrid cloud architecture
- **Disaster Recovery**: Establishing or improving disaster recovery and business continuity
- **Compliance Requirements**: Meeting regulatory standards (HIPAA, PCI-DSS, SOC2, GDPR)
- **Technology Refresh**: Modernizing legacy cloud architecture with new services
- **Merger/Acquisition**: Assessing and integrating acquired company's cloud infrastructure
- **Incident Post-Mortem**: Reviewing architecture after major outages or security incidents
- **Quarterly Health Check**: Regular architecture review to maintain best practices
- **New Team Onboarding**: Helping new teams understand existing cloud architecture
- **Vendor Lock-in Concerns**: Evaluating portability and multi-cloud options

## When NOT to Use

Avoid this skill when:

- **Active Incident**: Use incident response skills during active outages
- **Detailed Code Review**: Use code review skills for application-level issues
- **Application Architecture**: Use system design skills for application architecture
- **Network Troubleshooting**: Use network debugging skills for connectivity issues
- **Performance Tuning**: Use performance optimization skills for runtime tuning
- **Database Design**: Use data architecture skills for database schema design
- **Security Incident**: Use security incident response during active security events
- **Cost Forecasting Only**: Use financial planning tools for pure cost projection
- **Vendor Selection**: Use technology evaluation skills for choosing cloud providers
- **Training Needs**: Use knowledge transfer skills for team education
- **Documentation Only**: Use documentation skills if no architectural changes needed
- **Proof of Concept**: Use prototyping skills for testing new cloud services
- **Tactical Fixes**: Use troubleshooting skills for immediate tactical issues
- **Contract Negotiation**: Use procurement skills for vendor negotiations
- **Compliance Auditing**: Use audit skills for formal compliance assessments

## Inputs

### Required Inputs

1. **Cloud Infrastructure Inventory**
   - Cloud provider(s) (AWS, Azure, GCP, hybrid, multi-cloud)
   - Compute resources (EC2, Lambda, containers, Kubernetes)
   - Storage services (S3, EBS, databases, data lakes)
   - Networking (VPCs, subnets, load balancers, CDN)
   - Security services (IAM, KMS, WAF, security groups)
   - Monitoring and logging (CloudWatch, Datadog, Splunk)

2. **Architecture Documentation**
   - Architecture diagrams (network, application, data flow)
   - Infrastructure-as-code (Terraform, CloudFormation, ARM)
   - Service dependencies and integration points
   - Data flow and data architecture
   - Disaster recovery and backup strategies

3. **Business Context**
   - Business objectives and priorities
   - User base characteristics (size, geographic distribution)
   - Traffic patterns and growth projections
   - Budget constraints and cost targets
   - Compliance and regulatory requirements
   - SLA and uptime requirements

4. **Current Challenges**
   - Performance issues (latency, throughput, availability)
   - Cost concerns (overspending, waste, inefficiency)
   - Security vulnerabilities or compliance gaps
   - Scalability limitations
   - Operational complexity or maintenance burden

5. **Metrics and Monitoring**
   - Current cloud spending and cost breakdown
   - Performance metrics (latency, error rates, throughput)
   - Resource utilization (CPU, memory, storage, network)
   - Availability and uptime statistics
   - Security events and audit logs

### Optional Inputs

6. **Historical Data**
   - Past incidents and outages
   - Cost trends over time
   - Growth patterns and seasonality
   - Previous architecture reviews or audits
   - Migration history and lessons learned

7. **Team Information**
   - Team size and structure
   - Skills and expertise
   - On-call and support processes
   - Development and deployment workflows
   - Tooling and automation maturity

8. **Future Plans**
   - Planned features or services
   - Expected growth and scaling needs
   - Technology roadmap
   - Budget projections
   - Strategic initiatives (multi-cloud, modernization)

9. **Vendor Relationships**
   - Cloud provider contracts and commitments
   - Reserved instances and savings plans
   - Support agreements and SLAs
   - Third-party service integrations
   - Licensing and subscription details

10. **Compliance Requirements**
    - Regulatory standards (HIPAA, PCI-DSS, SOC2, GDPR)
    - Industry-specific requirements
    - Data residency and sovereignty
    - Audit and reporting needs
    - Certification requirements

## Expected Outputs

### Primary Deliverables

1. **Executive Summary**
   - Overall architecture health score
   - Critical findings and risks
   - Top 3-5 recommendations with business impact
   - Cost optimization opportunities (quick wins)
   - Security and compliance status
   - Estimated effort and timeline for improvements

2. **Detailed Architecture Assessment**
   - Well-Architected Framework evaluation (AWS) or equivalent
   - Pillar-by-pillar analysis (operational excellence, security, reliability, performance, cost)
   - Architecture diagrams (current state, proposed state)
   - Service-by-service review
   - Dependency mapping and risk analysis
   - Single points of failure identification

3. **Cost Optimization Report**
   - Current spending breakdown by service
   - Waste identification (idle resources, over-provisioning)
   - Right-sizing recommendations
   - Reserved instance and savings plan opportunities
   - Architectural changes for cost reduction
   - Estimated monthly and annual savings

4. **Security and Compliance Assessment**
   - Security posture evaluation
   - Compliance gap analysis
   - IAM and access control review
   - Data encryption and protection
   - Network security assessment
   - Vulnerability identification and remediation plan

5. **Reliability and Performance Analysis**
   - Availability and uptime analysis
   - Disaster recovery and backup assessment
   - Performance bottleneck identification
   - Scalability analysis
   - Fault tolerance and resilience review
   - Monitoring and alerting evaluation

### Supporting Deliverables

6. **Prioritized Remediation Roadmap**
   - Categorized recommendations (quick wins, medium-term, long-term)
   - Effort estimation (hours, days, weeks)
   - Risk vs. effort matrix
   - Dependencies and sequencing
   - Resource requirements
   - Success metrics and KPIs

7. **Best Practices Guide**
   - Cloud-specific best practices
   - Architectural patterns and anti-patterns
   - Security hardening checklist
   - Cost optimization strategies
   - Operational excellence guidelines
   - Tagging and resource organization standards

8. **Infrastructure-as-Code Recommendations**
   - IaC maturity assessment
   - Tool recommendations (Terraform, CloudFormation, Pulumi)
   - Module and template suggestions
   - State management and versioning
   - CI/CD integration opportunities
   - Drift detection and remediation

9. **Monitoring and Observability Plan**
   - Monitoring gaps and improvements
   - Dashboard and alerting recommendations
   - Log aggregation and analysis
   - Distributed tracing setup
   - Cost monitoring and budgeting
   - SLO/SLI definition and tracking

10. **Risk Register**
    - Identified risks with severity and likelihood
    - Mitigation strategies
    - Contingency plans
    - Risk ownership and accountability
    - Timeline for risk remediation
    - Residual risk assessment

## Workflow

### Step 1: Preparation and Scoping

**Objective**: Define review scope, objectives, and gather initial information.

**Actions**:
- Schedule kickoff meeting with stakeholders
- Define review scope (services, regions, accounts)
- Identify key stakeholders and subject matter experts
- Gather architecture documentation and diagrams
- Request access to cloud environments (read-only)
- Collect metrics and monitoring data
- Understand business objectives and constraints
- Establish success criteria for the review

**Outputs**:
- Review scope document
- Stakeholder matrix
- Access credentials and permissions
- Initial architecture documentation

**Quality Checks**:
- Scope clearly defined and agreed upon
- All stakeholders identified and engaged
- Necessary access granted
- Documentation collected and reviewed

### Step 2: Discovery and Inventory

**Objective**: Comprehensive inventory of cloud resources and architecture.

**Actions**:
- Inventory all cloud resources (compute, storage, network, security)
- Map service dependencies and data flows
- Review infrastructure-as-code repositories
- Analyze cost and usage reports
- Review monitoring dashboards and alerts
- Examine security configurations and policies
- Document current architecture (diagrams, inventory)
- Identify undocumented or shadow IT resources

**Outputs**:
- Complete resource inventory
- Architecture diagrams (network, application, data)
- Dependency maps
- Cost breakdown by service
- Security configuration summary

**Quality Checks**:
- All resources inventoried
- Dependencies mapped accurately
- Cost data collected and analyzed
- Security configurations documented
- Architecture diagrams created or updated

### Step 3: Well-Architected Framework Assessment

**Objective**: Evaluate architecture against cloud provider best practices.

**Actions**:
- Conduct Well-Architected Framework review (AWS) or equivalent
- Assess each pillar:
  - **Operational Excellence**: Automation, monitoring, incident response
  - **Security**: IAM, encryption, network security, compliance
  - **Reliability**: Fault tolerance, disaster recovery, backup
  - **Performance Efficiency**: Resource selection, monitoring, optimization
  - **Cost Optimization**: Right-sizing, waste elimination, pricing models
  - **Sustainability** (if applicable): Energy efficiency, resource optimization
- Score each pillar (high risk, medium risk, low risk)
- Document findings and evidence
- Identify gaps and improvement opportunities

**Outputs**:
- Well-Architected Framework assessment report
- Pillar-by-pillar scores and findings
- Gap analysis
- Improvement recommendations

**Quality Checks**:
- All pillars assessed thoroughly
- Findings supported by evidence
- Scores justified and documented
- Recommendations actionable and specific

### Step 4: Cost Analysis and Optimization

**Objective**: Identify cost optimization opportunities.

**Actions**:
- Analyze current spending by service, region, and tag
- Identify idle and underutilized resources
- Review instance types and right-sizing opportunities
- Evaluate reserved instances and savings plans
- Assess storage costs and lifecycle policies
- Review data transfer and egress costs
- Identify architectural changes for cost reduction
- Calculate potential savings (monthly, annually)

**Outputs**:
- Cost optimization report
- Waste identification (idle resources, over-provisioning)
- Right-sizing recommendations
- Reserved instance/savings plan analysis
- Estimated savings breakdown

**Quality Checks**:
- All cost categories analyzed
- Waste identified and quantified
- Right-sizing recommendations validated
- Savings estimates realistic and achievable
- Quick wins identified and prioritized

### Step 5: Security and Compliance Review

**Objective**: Assess security posture and compliance gaps.

**Actions**:
- Review IAM policies and access controls
- Assess data encryption (at rest, in transit)
- Evaluate network security (security groups, NACLs, WAF)
- Review logging and audit trails
- Assess compliance with regulatory requirements
- Identify security vulnerabilities and misconfigurations
- Review secret management and key rotation
- Evaluate incident response and security monitoring

**Outputs**:
- Security assessment report
- Compliance gap analysis
- Vulnerability identification
- IAM and access control recommendations
- Encryption and data protection review
- Security remediation plan

**Quality Checks**:
- All security domains assessed
- Compliance gaps identified and documented
- Vulnerabilities prioritized by severity
- Recommendations aligned with compliance requirements
- Remediation plan actionable and time-bound

### Step 6: Reliability and Disaster Recovery Assessment

**Objective**: Evaluate reliability, availability, and disaster recovery capabilities.

**Actions**:
- Review high availability architecture (multi-AZ, multi-region)
- Assess disaster recovery strategy (RTO, RPO)
- Evaluate backup and restore procedures
- Review fault tolerance and resilience mechanisms
- Assess auto-scaling and load balancing
- Identify single points of failure
- Review incident history and lessons learned
- Evaluate monitoring and alerting for reliability

**Outputs**:
- Reliability assessment report
- Disaster recovery evaluation
- Single point of failure identification
- High availability recommendations
- Backup and restore validation
- Incident analysis and improvements

**Quality Checks**:
- All reliability aspects assessed
- Disaster recovery tested and validated
- Single points of failure identified
- Recommendations improve availability
- Backup and restore procedures documented

### Step 7: Performance Analysis

**Objective**: Identify performance bottlenecks and optimization opportunities.

**Actions**:
- Review performance metrics (latency, throughput, error rates)
- Analyze resource utilization (CPU, memory, network, storage)
- Identify performance bottlenecks
- Evaluate caching strategies (CDN, application cache, database cache)
- Review database performance and query optimization
- Assess network latency and data transfer
- Evaluate compute resource selection (instance types, serverless)
- Review auto-scaling policies and thresholds

**Outputs**:
- Performance analysis report
- Bottleneck identification
- Resource utilization analysis
- Caching recommendations
- Database optimization suggestions
- Compute resource recommendations

**Quality Checks**:
- Performance metrics analyzed comprehensively
- Bottlenecks identified and prioritized
- Recommendations improve performance
- Resource selection optimized
- Caching strategies evaluated

### Step 8: Operational Excellence Review

**Objective**: Assess operational maturity and automation.

**Actions**:
- Review infrastructure-as-code maturity
- Assess CI/CD pipelines and deployment automation
- Evaluate monitoring and observability
- Review incident response and on-call processes
- Assess change management and approval workflows
- Evaluate documentation and runbooks
- Review tagging and resource organization
- Assess team skills and training needs

**Outputs**:
- Operational excellence assessment
- Automation opportunities
- Monitoring and observability gaps
- Incident response improvements
- Documentation recommendations
- Tagging and organization standards

**Quality Checks**:
- Operational maturity assessed
- Automation opportunities identified
- Monitoring gaps documented
- Incident response evaluated
- Documentation reviewed and improved

### Step 9: Synthesis and Recommendations

**Objective**: Synthesize findings and create actionable recommendations.

**Actions**:
- Consolidate findings from all assessment areas
- Prioritize recommendations (critical, high, medium, low)
- Categorize by effort (quick wins, medium-term, long-term)
- Estimate effort and resources required
- Calculate business impact and ROI
- Create remediation roadmap with timeline
- Develop risk vs. effort matrix
- Define success metrics and KPIs

**Outputs**:
- Prioritized recommendations
- Remediation roadmap
- Risk vs. effort matrix
- Effort and resource estimates
- Business impact and ROI analysis
- Success metrics and KPIs

**Quality Checks**:
- Recommendations prioritized appropriately
- Roadmap realistic and achievable
- Effort estimates validated
- Business impact quantified
- Success metrics defined

### Step 10: Reporting and Presentation

**Objective**: Communicate findings and recommendations to stakeholders.

**Actions**:
- Create executive summary for leadership
- Prepare detailed technical report
- Develop presentation for stakeholder review
- Schedule review meetings with stakeholders
- Present findings and recommendations
- Gather feedback and adjust recommendations
- Document decisions and next steps
- Establish follow-up and tracking process

**Outputs**:
- Executive summary
- Detailed technical report
- Stakeholder presentation
- Meeting notes and decisions
- Follow-up action plan

**Quality Checks**:
- Executive summary clear and concise
- Technical report comprehensive and accurate
- Presentation tailored to audience
- Stakeholder feedback incorporated
- Next steps documented and assigned

## Decision Framework

### Cloud Provider Selection

**Choose AWS when**:
- Need broadest service portfolio and maturity
- Require extensive global infrastructure
- Want strong ecosystem and community
- Need advanced services (SageMaker, EMR, Redshift)
- Existing AWS expertise and investments

**Choose Azure when**:
- Microsoft ecosystem integration (Active Directory, Office 365)
- Hybrid cloud with on-premises Windows infrastructure
- Enterprise agreements and licensing benefits
- Strong .NET and Windows workloads
- Government and compliance requirements (Azure Government)

**Choose GCP when**:
- Data analytics and machine learning focus (BigQuery, Vertex AI)
- Kubernetes and container-native workloads
- Open-source and multi-cloud strategy
- Cost-effective pricing and sustained use discounts
- Strong networking and global infrastructure

**Choose Multi-Cloud when**:
- Vendor lock-in concerns
- Geographic or regulatory requirements
- Best-of-breed service selection
- Disaster recovery across providers
- Negotiating leverage with vendors

### Compute Service Selection

**Use Virtual Machines (EC2, VMs) when**:
- Need full control over OS and configuration
- Running legacy applications
- Long-running, predictable workloads
- Stateful applications
- Specific hardware or licensing requirements

**Use Containers (ECS, AKS, GKE) when**:
- Microservices architecture
- Need portability across environments
- Efficient resource utilization
- CI/CD and DevOps workflows
- Scaling and orchestration requirements

**Use Serverless (Lambda, Functions, Cloud Functions) when**:
- Event-driven workloads
- Unpredictable or variable traffic
- Short-lived, stateless functions
- Pay-per-use cost model preferred
- Minimal operational overhead desired

**Use Managed Kubernetes when**:
- Complex microservices architecture
- Need container orchestration at scale
- Multi-cloud or hybrid deployment
- Strong DevOps and container expertise
- Require advanced networking and service mesh

### Storage Service Selection

**Use Object Storage (S3, Blob, Cloud Storage) when**:
- Storing unstructured data (images, videos, logs)
- Need durability and availability (11 9's)
- Cost-effective long-term storage
- Static website hosting
- Data lake and analytics

**Use Block Storage (EBS, Managed Disks, Persistent Disks) when**:
- Database storage (IOPS and latency critical)
- Boot volumes for VMs
- High-performance applications
- Need snapshots and backups
- Require encryption and access control

**Use File Storage (EFS, Azure Files, Filestore) when**:
- Shared file system across instances
- NFS or SMB protocol required
- Content management and collaboration
- Legacy application migration
- Multi-instance read/write access

**Use Database Services when**:
- Managed database preferred (RDS, Azure SQL, Cloud SQL)
- Need automatic backups and patching
- High availability and replication
- Specific database engine (PostgreSQL, MySQL, SQL Server)
- Reduce operational overhead

### High Availability Strategy

**Use Single-AZ when**:
- Development and testing environments
- Non-critical workloads
- Cost is primary concern
- Acceptable downtime (minutes to hours)

**Use Multi-AZ when**:
- Production workloads
- High availability required (99.9%+)
- Automatic failover needed
- Minimal downtime tolerance (seconds to minutes)
- Database replication and synchronization

**Use Multi-Region when**:
- Global user base
- Disaster recovery across regions
- Compliance and data residency
- Highest availability (99.99%+)
- Latency optimization for global users

### Disaster Recovery Strategy

**Use Backup and Restore when**:
- RTO: Hours to days
- RPO: Hours
- Cost is primary concern
- Infrequent disasters acceptable
- Simple recovery process

**Use Pilot Light when**:
- RTO: Minutes to hours
- RPO: Minutes
- Core services always running
- Cost-effective for most workloads
- Moderate recovery complexity

**Use Warm Standby when**:
- RTO: Minutes
- RPO: Seconds to minutes
- Scaled-down replica always running
- Quick recovery required
- Higher cost acceptable

**Use Multi-Site Active-Active when**:
- RTO: Seconds
- RPO: Near-zero
- Zero downtime required
- Global load balancing
- Highest cost, highest availability

## Quality Checklist

### Scope and Planning
- [ ] Review scope clearly defined and agreed upon
- [ ] Stakeholders identified and engaged
- [ ] Access to cloud environments granted
- [ ] Architecture documentation collected
- [ ] Success criteria established

### Discovery and Inventory
- [ ] All cloud resources inventoried
- [ ] Service dependencies mapped
- [ ] Architecture diagrams created or updated
- [ ] Cost data collected and analyzed
- [ ] Security configurations documented

### Well-Architected Assessment
- [ ] All pillars assessed (operational excellence, security, reliability, performance, cost)
- [ ] Findings supported by evidence
- [ ] Scores justified and documented
- [ ] Gaps identified and prioritized
- [ ] Recommendations actionable and specific

### Cost Optimization
- [ ] Current spending analyzed by service
- [ ] Idle and underutilized resources identified
- [ ] Right-sizing recommendations provided
- [ ] Reserved instance/savings plan opportunities identified
- [ ] Estimated savings calculated (monthly, annually)
- [ ] Quick wins prioritized

### Security and Compliance
- [ ] IAM and access controls reviewed
- [ ] Data encryption assessed (at rest, in transit)
- [ ] Network security evaluated
- [ ] Compliance gaps identified
- [ ] Vulnerabilities prioritized by severity
- [ ] Remediation plan created

### Reliability and Disaster Recovery
- [ ] High availability architecture reviewed
- [ ] Disaster recovery strategy assessed (RTO, RPO)
- [ ] Backup and restore procedures validated
- [ ] Single points of failure identified
- [ ] Fault tolerance mechanisms evaluated
- [ ] Incident history analyzed

### Performance
- [ ] Performance metrics analyzed
- [ ] Bottlenecks identified
- [ ] Resource utilization assessed
- [ ] Caching strategies evaluated
- [ ] Database performance reviewed
- [ ] Compute resource selection optimized

### Operational Excellence
- [ ] Infrastructure-as-code maturity assessed
- [ ] CI/CD and automation evaluated
- [ ] Monitoring and observability reviewed
- [ ] Incident response processes assessed
- [ ] Documentation and runbooks evaluated
- [ ] Tagging and organization standards defined

### Recommendations and Roadmap
- [ ] Recommendations prioritized (critical, high, medium, low)
- [ ] Effort estimated (quick wins, medium-term, long-term)
- [ ] Business impact quantified
- [ ] Remediation roadmap created
- [ ] Success metrics defined
- [ ] Risk vs. effort matrix developed

### Reporting and Communication
- [ ] Executive summary created
- [ ] Detailed technical report completed
- [ ] Stakeholder presentation prepared
- [ ] Findings presented and discussed
- [ ] Feedback incorporated
- [ ] Next steps documented and assigned

## Common Mistakes

### Assessment Mistakes

1. **Insufficient Scope Definition**
   - **Mistake**: Starting review without clear scope, leading to scope creep or missed areas
   - **Impact**: Incomplete assessment, wasted effort, stakeholder dissatisfaction
   - **Solution**: Define scope upfront, get stakeholder agreement, document boundaries
   - **Prevention**: Use scoping template, involve stakeholders early, set clear expectations

2. **Ignoring Business Context**
   - **Mistake**: Focusing only on technical aspects without understanding business objectives
   - **Impact**: Recommendations not aligned with business priorities, low adoption
   - **Solution**: Understand business goals, constraints, and priorities before technical review
   - **Prevention**: Start with business context, involve business stakeholders, align recommendations

3. **Incomplete Resource Inventory**
   - **Mistake**: Missing resources, shadow IT, or undocumented services
   - **Impact**: Incomplete assessment, missed cost optimization, security gaps
   - **Solution**: Use automated discovery tools, review all accounts and regions, involve teams
   - **Prevention**: Use cloud provider inventory tools, check all regions, review billing data

4. **Superficial Assessment**
   - **Mistake**: Surface-level review without deep dive into critical areas
   - **Impact**: Missed issues, incorrect recommendations, low value
   - **Solution**: Allocate sufficient time, involve experts, use checklists and frameworks
   - **Prevention**: Plan adequate time, use Well-Architected Framework, engage specialists

5. **Ignoring Cost Data**
   - **Mistake**: Not analyzing cost and usage reports thoroughly
   - **Impact**: Missed cost optimization opportunities, inaccurate savings estimates
   - **Solution**: Analyze cost data by service, region, tag; identify trends and anomalies
   - **Prevention**: Request cost reports early, use cost analysis tools, involve finance team

### Recommendation Mistakes

6. **Unrealistic Recommendations**
   - **Mistake**: Suggesting changes that are too complex, costly, or time-consuming
   - **Impact**: Low adoption, stakeholder pushback, wasted effort
   - **Solution**: Prioritize practical, achievable recommendations; consider constraints
   - **Prevention**: Validate recommendations with teams, estimate effort realistically, prioritize quick wins

7. **No Prioritization**
   - **Mistake**: Providing long list of recommendations without prioritization
   - **Impact**: Teams overwhelmed, unclear where to start, low adoption
   - **Solution**: Prioritize by impact, effort, and risk; categorize quick wins vs. long-term
   - **Prevention**: Use prioritization framework (risk vs. effort matrix), categorize recommendations

8. **Ignoring Dependencies**
   - **Mistake**: Not considering dependencies between recommendations
   - **Impact**: Implementation failures, wasted effort, incorrect sequencing
   - **Solution**: Map dependencies, sequence recommendations appropriately
   - **Prevention**: Create dependency map, validate sequencing with teams, document prerequisites

9. **Vague Recommendations**
   - **Mistake**: Providing high-level recommendations without specific actions
   - **Impact**: Teams don't know what to do, low adoption, unclear success criteria
   - **Solution**: Make recommendations specific, actionable, and measurable
   - **Prevention**: Use SMART criteria (Specific, Measurable, Achievable, Relevant, Time-bound)

10. **No Cost-Benefit Analysis**
    - **Mistake**: Not quantifying business impact or ROI of recommendations
    - **Impact**: Stakeholders can't prioritize, unclear value, low buy-in
    - **Solution**: Estimate savings, effort, and business impact for each recommendation
    - **Prevention**: Calculate ROI, estimate savings, quantify business impact

### Security Mistakes

11. **Overlooking IAM Complexity**
    - **Mistake**: Not thoroughly reviewing IAM policies, roles, and permissions
    - **Impact**: Security vulnerabilities, excessive permissions, compliance violations
    - **Solution**: Review all IAM policies, identify overly permissive access, apply least privilege
    - **Prevention**: Use IAM analysis tools, review permissions regularly, enforce least privilege

12. **Ignoring Data Encryption**
    - **Mistake**: Not verifying encryption at rest and in transit
    - **Impact**: Data breaches, compliance violations, security incidents
    - **Solution**: Verify encryption for all data stores, enable encryption in transit
    - **Prevention**: Use encryption checklist, enable default encryption, audit regularly

13. **Missing Security Monitoring**
    - **Mistake**: Not reviewing security monitoring, logging, and alerting
    - **Impact**: Delayed incident detection, compliance violations, security breaches
    - **Solution**: Review CloudTrail, GuardDuty, Security Hub; ensure comprehensive logging
    - **Prevention**: Enable security services, configure alerts, review logs regularly

### Cost Mistakes

14. **Ignoring Tagging**
    - **Mistake**: Not reviewing resource tagging and cost allocation
    - **Impact**: Difficult to track costs, no accountability, inefficient optimization
    - **Solution**: Establish tagging standards, enforce tagging policies, use cost allocation tags
    - **Prevention**: Define tagging strategy, automate tagging, enforce with policies

15. **Overlooking Data Transfer Costs**
    - **Mistake**: Not analyzing data transfer and egress costs
    - **Impact**: Unexpected costs, inefficient architecture, missed optimization
    - **Solution**: Review data transfer patterns, optimize cross-region/cross-AZ traffic
    - **Prevention**: Analyze data transfer costs, use VPC endpoints, optimize architecture

### Operational Mistakes

16. **No Follow-Up Plan**
    - **Mistake**: Delivering report without follow-up or tracking
    - **Impact**: Recommendations not implemented, wasted effort, no improvement
    - **Solution**: Establish follow-up process, track progress, measure success
    - **Prevention**: Define success metrics, schedule follow-up reviews, assign ownership

17. **Poor Communication**
    - **Mistake**: Not tailoring communication to different audiences
    - **Impact**: Stakeholders don't understand findings, low buy-in, unclear next steps
    - **Solution**: Create executive summary for leadership, technical details for engineers
    - **Prevention**: Tailor communication to audience, use visuals, simplify complex topics

18. **Ignoring Team Feedback**
    - **Mistake**: Not involving teams in review or incorporating their feedback
    - **Impact**: Missed context, incorrect assumptions, low adoption
    - **Solution**: Involve teams throughout review, gather feedback, incorporate insights
    - **Prevention**: Schedule team interviews, validate findings, incorporate feedback

19. **No Documentation**
    - **Mistake**: Not documenting findings, decisions, and rationale
    - **Impact**: Lost knowledge, difficult to track progress, unclear decisions
    - **Solution**: Document all findings, decisions, and rationale; maintain review artifacts
    - **Prevention**: Use templates, document as you go, maintain review repository

20. **One-Time Review**
    - **Mistake**: Treating architecture review as one-time activity
    - **Impact**: Architecture degrades over time, new issues emerge, no continuous improvement
    - **Solution**: Establish regular review cadence (quarterly, annually), track improvements
    - **Prevention**: Schedule recurring reviews, track metrics over time, continuous improvement

## Examples

### Example 1: E-Commerce Platform Cost Optimization

**Context**:
A fast-growing e-commerce company with $50,000/month AWS spending noticed costs increasing 20% month-over-month without corresponding traffic growth. The platform consists of web application (EC2), product catalog (RDS PostgreSQL), image storage (S3), CDN (CloudFront), and search (Elasticsearch). The team has limited cloud expertise and no cost optimization practices.

**Review Findings**:

**Cost Analysis**:
- Current spending: $50,000/month
- Breakdown: EC2 (40%), RDS (25%), S3 (15%), Data Transfer (10%), Other (10%)
- Identified waste: $18,000/month (36%)

**Key Issues**:
1. **Over-provisioned EC2 instances**: Running m5.2xlarge instances with 10-15% CPU utilization
2. **Idle resources**: 12 stopped instances still incurring EBS costs ($800/month)
3. **No reserved instances**: All on-demand pricing despite predictable baseline load
4. **Inefficient S3 storage**: 80% of images in Standard tier, rarely accessed
5. **Cross-region data transfer**: Unnecessary replication to unused region ($2,000/month)
6. **Oversized RDS instance**: db.r5.4xlarge with 20% CPU utilization
7. **No lifecycle policies**: Old logs and backups accumulating in S3

**Recommendations**:

**Quick Wins (1-2 weeks, $8,000/month savings)**:
1. Right-size EC2 instances: m5.2xlarge → m5.large (save $3,500/month)
2. Delete stopped instances and unused EBS volumes (save $800/month)
3. Implement S3 lifecycle policies: Standard → Intelligent-Tiering (save $1,500/month)
4. Remove unnecessary cross-region replication (save $2,000/month)
5. Delete old logs and backups older than 90 days (save $200/month)

**Medium-Term (1-3 months, $7,000/month savings)**:
6. Purchase reserved instances for baseline load (save $4,000/month)
7. Right-size RDS instance: db.r5.4xlarge → db.r5.xlarge (save $2,500/month)
8. Implement CloudFront caching optimization (save $500/month)

**Long-Term (3-6 months, $3,000/month savings)**:
9. Migrate to Auto Scaling Groups with mixed instance types (save $2,000/month)
10. Implement image optimization and compression (save $1,000/month)

**Total Estimated Savings**: $18,000/month (36% reduction)
**Effort**: 40 hours over 3 months
**ROI**: 450:1 (savings vs. effort cost)

**Implementation Roadmap**:
- Week 1-2: Quick wins implementation
- Month 1: Reserved instance purchase, RDS right-sizing
- Month 2-3: Auto Scaling Groups, image optimization
- Ongoing: Monthly cost review, continuous optimization

**Results After 3 Months**:
- Monthly spending reduced from $50,000 to $32,000 (36% reduction)
- Annual savings: $216,000
- Improved resource utilization (CPU 40-60%, memory 50-70%)
- Established cost optimization culture and practices

---

### Example 2: Healthcare SaaS Security and Compliance Review

**Context**:
A healthcare SaaS company preparing for HIPAA compliance audit needed comprehensive security review. The platform handles protected health information (PHI) for 50,000 patients across 200 healthcare providers. Infrastructure includes web application (ECS), API (Lambda), database (RDS), file storage (S3), and analytics (Redshift). Previous security audit identified critical gaps.

**Review Findings**:

**Security Assessment**:
- Overall security posture: Medium risk
- Critical findings: 8
- High findings: 15
- Medium findings: 23
- HIPAA compliance gaps: 12

**Critical Issues**:
1. **Unencrypted data at rest**: RDS and S3 buckets not encrypted
2. **Overly permissive IAM**: Admin access granted to multiple developers
3. **No audit logging**: CloudTrail disabled, no access logs
4. **Public S3 buckets**: PHI accessible without authentication
5. **Weak password policies**: No MFA, 90-day password expiration not enforced
6. **Unencrypted data in transit**: HTTP endpoints exposed
7. **No network segmentation**: All resources in single VPC, no isolation
8. **Missing backup encryption**: RDS backups not encrypted

**Recommendations**:

**Critical (Immediate, 1 week)**:
1. Enable encryption at rest for all RDS instances and S3 buckets
2. Remove public access from S3 buckets, implement bucket policies
3. Enable CloudTrail and S3 access logging
4. Enforce HTTPS for all endpoints, disable HTTP
5. Implement least privilege IAM, remove admin access
6. Enable MFA for all users, especially privileged accounts
7. Encrypt RDS backups and snapshots
8. Implement VPC security groups and NACLs

**High Priority (1-2 weeks)**:
9. Implement network segmentation (separate VPCs for prod, staging, dev)
10. Enable AWS Config for compliance monitoring
11. Implement AWS GuardDuty for threat detection
12. Set up AWS Security Hub for centralized security view
13. Implement KMS for key management
14. Enable VPC Flow Logs for network monitoring
15. Implement AWS WAF for web application protection

**Medium Priority (1 month)**:
16. Implement AWS Secrets Manager for credential rotation
17. Set up AWS Macie for data classification and protection
18. Implement AWS Inspector for vulnerability scanning
19. Establish security incident response plan
20. Conduct security awareness training for team
21. Implement automated compliance checks
22. Establish security review process for code changes
23. Implement data loss prevention (DLP) policies

**HIPAA Compliance Roadmap**:
- **Administrative Safeguards**: Security management process, workforce security, access management
- **Physical Safeguards**: Facility access controls, workstation security, device controls
- **Technical Safeguards**: Access control, audit controls, integrity, transmission security
- **Organizational Requirements**: Business associate agreements, policies and procedures
- **Documentation**: Security policies, incident response, disaster recovery

**Implementation Plan**:
- Week 1: Critical security fixes (encryption, access control, logging)
- Week 2: High priority security enhancements (network segmentation, monitoring)
- Week 3-4: Medium priority improvements (automation, training, processes)
- Month 2: HIPAA compliance documentation and policies
- Month 3: Third-party HIPAA audit preparation

**Results After 3 Months**:
- All critical and high security findings remediated
- HIPAA compliance achieved, audit passed
- Security posture improved from medium to high
- Zero security incidents or data breaches
- Established security-first culture and practices
- Automated compliance monitoring and reporting

---

### Example 3: Global SaaS Platform Multi-Region Reliability

**Context**:
A global SaaS platform serving 1 million users across North America, Europe, and Asia experiencing frequent outages (3-4 per month, 30-60 minutes each). Single-region architecture (us-east-1) causing latency issues for international users (500-1000ms). Business requires 99.9% uptime SLA and <200ms latency globally. Current architecture: web tier (EC2 Auto Scaling), API (ECS), database (RDS Multi-AZ), cache (ElastiCache), storage (S3).

**Review Findings**:

**Reliability Assessment**:
- Current uptime: 99.5% (below 99.9% SLA)
- Average latency: 150ms (US), 600ms (Europe), 800ms (Asia)
- Single points of failure: 7
- Disaster recovery: None (RTO: unknown, RPO: unknown)

**Key Issues**:
1. **Single-region architecture**: All resources in us-east-1, no geographic redundancy
2. **No disaster recovery**: No backup region, no failover plan
3. **Single-AZ components**: ElastiCache, NAT Gateway in single AZ
4. **No global load balancing**: Users routed to us-east-1 regardless of location
5. **Database replication**: No cross-region replication
6. **Insufficient monitoring**: No global health checks, limited alerting
7. **Manual failover**: No automated failover procedures

**Recommendations**:

**Multi-Region Architecture Design**:

**Primary Region (us-east-1)**:
- Web tier: EC2 Auto Scaling across 3 AZs
- API: ECS Fargate across 3 AZs
- Database: RDS Multi-AZ with cross-region read replicas
- Cache: ElastiCache Redis cluster mode across 3 AZs
- Storage: S3 with cross-region replication

**Secondary Regions (eu-west-1, ap-southeast-1)**:
- Web tier: EC2 Auto Scaling across 3 AZs (warm standby)
- API: ECS Fargate across 3 AZs (warm standby)
- Database: RDS read replicas (promote to primary on failover)
- Cache: ElastiCache Redis cluster mode
- Storage: S3 replicas

**Global Traffic Management**:
- Route 53 latency-based routing
- Health checks for each region
- Automatic failover to healthy regions
- CloudFront for static content (global edge locations)

**Disaster Recovery Strategy**:
- **RTO**: 15 minutes (automated failover)
- **RPO**: 5 minutes (database replication lag)
- **Strategy**: Warm standby in secondary regions
- **Failover**: Automated via Route 53 health checks
- **Failback**: Manual after primary region recovery

**Implementation Roadmap**:

**Phase 1 (Month 1): Single-Region Reliability**
- Eliminate single-AZ components (ElastiCache, NAT Gateway)
- Implement comprehensive monitoring and alerting
- Establish incident response procedures
- Document architecture and runbooks

**Phase 2 (Month 2): Multi-AZ Hardening**
- Implement Auto Scaling across all AZs
- Set up cross-AZ load balancing
- Implement database failover testing
- Establish backup and restore procedures

**Phase 3 (Month 3-4): Multi-Region Deployment**
- Deploy warm standby in eu-west-1
- Set up cross-region database replication
- Implement S3 cross-region replication
- Configure Route 53 latency-based routing

**Phase 4 (Month 5): Asia-Pacific Region**
- Deploy warm standby in ap-southeast-1
- Extend cross-region replication
- Update Route 53 routing policies
- Implement global health checks

**Phase 5 (Month 6): Optimization and Testing**
- Conduct disaster recovery testing
- Optimize costs (right-sizing, reserved instances)
- Implement automated failover procedures
- Establish regular DR drills

**Cost Impact**:
- Current monthly cost: $30,000
- Multi-region cost: $55,000 (83% increase)
- Cost per 9 of availability: $8,333 (from 99.5% to 99.9%)
- Business justification: SLA penalties avoided ($50,000/month), customer retention

**Results After 6 Months**:
- Uptime improved from 99.5% to 99.95%
- Latency reduced: US (150ms → 120ms), Europe (600ms → 180ms), Asia (800ms → 200ms)
- Zero major outages (previously 3-4/month)
- Automated failover tested and validated (RTO: 12 minutes)
- Customer satisfaction improved (NPS +15 points)
- Revenue impact: $200,000/month from improved reliability and performance

---

### Example 4: Startup Migration from Monolith to Microservices

**Context**:
A fast-growing startup with monolithic Rails application on EC2 experiencing scaling challenges. Application serves 100,000 daily active users with 5x traffic growth in 6 months. Current architecture: single EC2 instance (m5.4xlarge), PostgreSQL RDS, Redis ElastiCache, S3 for assets. Team of 15 engineers struggling with deployment velocity (1 deploy/week), frequent downtime during deployments, and increasing costs.

**Review Findings**:

**Architecture Assessment**:
- Monolithic application (single codebase, 200,000 lines of code)
- Vertical scaling only (larger instances)
- No horizontal scaling capability
- Single point of failure (single EC2 instance)
- Deployment downtime (15-30 minutes)
- Resource inefficiency (70% memory, 30% CPU utilization)

**Challenges**:
1. **Scaling limitations**: Cannot scale individual components
2. **Deployment risk**: Entire application redeployed for small changes
3. **Development velocity**: Merge conflicts, long CI/CD pipelines
4. **Cost inefficiency**: Over-provisioned for peak load
5. **Technology constraints**: Locked into Rails stack
6. **Team bottlenecks**: All engineers working in single codebase

**Recommendations**:

**Migration Strategy: Strangler Fig Pattern**

Instead of big-bang rewrite, gradually extract services from monolith:

**Phase 1 (Month 1-2): Foundation**
- Set up containerization (Docker)
- Implement API gateway (Kong, AWS API Gateway)
- Establish service mesh (Istio, AWS App Mesh)
- Set up Kubernetes cluster (EKS)
- Implement centralized logging (ELK, CloudWatch)
- Establish monitoring and tracing (Datadog, X-Ray)

**Phase 2 (Month 3-4): First Microservice**
- Extract authentication service (high value, well-defined boundary)
- Implement as Node.js microservice
- Deploy to Kubernetes
- Route authentication traffic through API gateway
- Maintain backward compatibility with monolith
- Monitor performance and reliability

**Phase 3 (Month 5-6): Additional Services**
- Extract notification service (email, SMS, push)
- Extract payment processing service
- Implement event-driven architecture (SQS, SNS, EventBridge)
- Establish service-to-service communication (gRPC, REST)

**Phase 4 (Month 7-9): Core Services**
- Extract user management service
- Extract product catalog service
- Extract order management service
- Implement database per service pattern
- Establish data consistency patterns (saga, event sourcing)

**Phase 5 (Month 10-12): Monolith Decomposition**
- Continue extracting services
- Reduce monolith size and complexity
- Migrate remaining functionality
- Decommission monolith (if feasible)

**Target Architecture**:

**Microservices**:
- Authentication Service (Node.js, DynamoDB)
- User Service (Go, PostgreSQL RDS)
- Product Service (Node.js, MongoDB Atlas)
- Order Service (Java Spring Boot, PostgreSQL RDS)
- Payment Service (Node.js, Stripe API)
- Notification Service (Python, SQS, SNS)
- Search Service (Elasticsearch)
- Analytics Service (Python, Redshift)

**Infrastructure**:
- Kubernetes (EKS) for container orchestration
- API Gateway for routing and rate limiting
- Service mesh (Istio) for service-to-service communication
- Event bus (EventBridge) for asynchronous communication
- Centralized logging (CloudWatch Logs, ELK)
- Distributed tracing (AWS X-Ray, Jaeger)
- CI/CD per service (GitHub Actions, ArgoCD)

**Benefits**:
- **Scalability**: Scale services independently based on demand
- **Deployment velocity**: Deploy services independently (10+ deploys/day)
- **Technology flexibility**: Choose best technology per service
- **Team autonomy**: Teams own services end-to-end
- **Fault isolation**: Service failures don't bring down entire system
- **Cost optimization**: Right-size resources per service

**Challenges and Mitigations**:

**Challenge 1: Data Consistency**
- **Issue**: Distributed transactions across services
- **Mitigation**: Implement saga pattern, eventual consistency, event sourcing

**Challenge 2: Increased Complexity**
- **Issue**: More services to manage, monitor, and debug
- **Mitigation**: Invest in observability, automation, and platform engineering

**Challenge 3: Network Latency**
- **Issue**: Service-to-service calls add latency
- **Mitigation**: Optimize service boundaries, implement caching, use async communication

**Challenge 4: Team Skills**
- **Issue**: Team lacks microservices and Kubernetes expertise
- **Mitigation**: Training, hiring, pair programming, gradual adoption

**Cost Impact**:
- Current cost: $5,000/month (monolith)
- Target cost: $12,000/month (microservices)
- Increase: $7,000/month (140%)
- Justification: Improved scalability, velocity, and reliability
- ROI: Faster feature delivery, reduced downtime, better customer experience

**Results After 12 Months**:
- 8 microservices extracted from monolith
- Deployment frequency: 1/week → 50+/week
- Deployment downtime: 15-30 min → 0 (zero-downtime deployments)
- Incident recovery time: 2 hours → 15 minutes
- Team velocity: 2x increase in feature delivery
- Customer satisfaction: NPS +20 points
- Revenue impact: $500,000/year from faster feature delivery

## Related Skills

### Prerequisites
- **cloud-fundamentals**: Understanding of cloud computing concepts
- **networking-basics**: VPC, subnets, routing, load balancing
- **security-fundamentals**: IAM, encryption, access control

### Commonly Followed By
- **cost-optimization-implementation**: Implementing cost optimization recommendations
- **security-hardening**: Implementing security improvements
- **disaster-recovery-planning**: Establishing DR strategy
- **performance-optimization**: Optimizing application and infrastructure performance
- **migration-planning**: Planning cloud migration or modernization

### Alternative To
- **well-architected-review**: AWS-specific framework review
- **security-audit**: Focused security assessment
- **cost-audit**: Focused cost analysis

### Works With
- **infrastructure-as-code**: Implementing infrastructure automation
- **ci-cd-design**: Establishing deployment pipelines
- **observability-design**: Implementing monitoring and logging
- **capacity-planning**: Planning for growth and scaling
- **compliance-assessment**: Ensuring regulatory compliance

## Skill Composition

### Cloud Optimization Workflow

```
cloud-architecture-review (this skill)
      ↓
cost-optimization-implementation
      ↓
security-hardening
      ↓
disaster-recovery-planning
      ↓
performance-optimization
```

### Cloud Migration Workflow

```
cloud-architecture-review (assess current state)
      ↓
migration-planning (plan migration)
      ↓
infrastructure-as-code (automate infrastructure)
      ↓
ci-cd-design (establish pipelines)
      ↓
observability-design (implement monitoring)
```

## Evaluation Criteria

### Excellent
- Comprehensive assessment across all pillars (operational excellence, security, reliability, performance, cost)
- Actionable, prioritized recommendations with business impact
- Cost optimization opportunities identified (>20% savings)
- Security and compliance gaps addressed
- Disaster recovery strategy established (RTO, RPO defined)
- Clear remediation roadmap with timeline and effort estimates
- Executive summary and detailed technical report
- Stakeholder buy-in and commitment to implement
- Follow-up plan and success metrics defined
- Regular review cadence established

### Good
- Assessment covers most pillars
- Recommendations provided with some prioritization
- Cost optimization opportunities identified (10-20% savings)
- Major security gaps identified
- Disaster recovery considerations documented
- Remediation plan with high-level timeline
- Technical report completed
- Some stakeholder engagement
- Follow-up discussed

### Needs Improvement
- Incomplete assessment (missing pillars)
- Generic recommendations without prioritization
- Limited cost optimization (<10% savings)
- Security gaps not fully identified
- Disaster recovery not addressed
- No clear remediation plan
- Report lacks detail or clarity
- Limited stakeholder engagement
- No follow-up plan

## Tags

`cloud`, `architecture`, `aws`, `azure`, `gcp`, `cost-optimization`, `security`, `reliability`, `performance`, `compliance`, `well-architected`, `disaster-recovery`, `multi-cloud`, `infrastructure`, `operations`, `review`, `assessment`, `audit`

## Version

**1.0.0** — Initial release