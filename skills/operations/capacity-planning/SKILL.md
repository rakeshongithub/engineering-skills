# Capacity Planning Skill

## Purpose

Estimate and plan infrastructure capacity to meet current and future demand while optimizing costs, performance, and reliability.

## When to Use

Use this skill when:

- **Planning New Infrastructure**: Sizing infrastructure for new applications or services before launch
- **Scaling Existing Systems**: Current infrastructure is approaching capacity limits or experiencing performance degradation
- **Cost Optimization**: Need to right-size infrastructure to reduce unnecessary spending
- **Growth Planning**: Anticipating future demand based on business growth projections
- **Performance Issues**: Addressing bottlenecks and resource constraints affecting user experience
- **Seasonal Preparation**: Planning for predictable traffic spikes (holidays, events, campaigns)
- **Migration Planning**: Estimating capacity requirements for cloud migration or platform changes
- **SLA Compliance**: Ensuring infrastructure can meet service level agreements and performance targets
- **Multi-Region Expansion**: Planning capacity for geographic expansion or disaster recovery
- **Technology Refresh**: Replacing aging infrastructure with appropriately sized modern alternatives
- **Mergers and Acquisitions**: Integrating infrastructure from acquired companies
- **Compliance Requirements**: Meeting regulatory requirements for capacity and redundancy
- **Budget Planning**: Creating accurate infrastructure budget forecasts for upcoming fiscal periods
- **Architecture Changes**: Evaluating capacity implications of architectural decisions (microservices, serverless, etc.)
- **Disaster Recovery**: Sizing backup and failover infrastructure for business continuity

## When NOT to Use

Avoid this skill when:

- **Immediate Incidents**: Use incident response skills during active outages or performance crises
- **Application Optimization**: Focus on application performance tuning before adding capacity
- **Code Debugging**: Address software bugs and inefficiencies rather than adding resources
- **Database Tuning**: Optimize database queries and indexes before scaling database infrastructure
- **Network Troubleshooting**: Diagnose and fix network issues rather than assuming capacity problems
- **Security Incidents**: Use security response skills for security-related performance issues
- **Configuration Issues**: Fix misconfigurations before concluding capacity is insufficient
- **Vendor Selection**: Use technology evaluation skills for choosing infrastructure providers
- **Architecture Design**: Use architecture design skills for fundamental system design decisions
- **Cost Analysis Only**: Use financial analysis skills if only analyzing costs without capacity planning
- **Monitoring Setup**: Use observability skills to establish monitoring before capacity planning
- **Proof of Concept**: Use prototyping skills for small-scale experiments
- **Load Testing Only**: Use performance testing skills if only validating current capacity
- **Procurement Process**: Use vendor management skills for purchasing and contracting
- **Simple Resource Adjustments**: Routine scaling operations don't require full capacity planning

## Inputs

### Required Inputs

1. **Current Infrastructure Inventory**
   - Complete list of compute resources (VMs, containers, serverless functions)
   - Storage systems (databases, object storage, file systems, caches)
   - Network infrastructure (load balancers, CDN, bandwidth allocation)
   - Current resource utilization metrics (CPU, memory, disk, network)
   - Existing capacity and performance baselines

2. **Historical Usage Data**
   - Traffic patterns over time (hourly, daily, weekly, monthly, yearly)
   - Resource utilization trends (CPU, memory, storage, network)
   - Peak usage periods and magnitudes
   - Growth rates over historical periods
   - Seasonal variations and cyclical patterns

3. **Business Requirements**
   - Expected growth projections (user growth, transaction volume, data volume)
   - Service level agreements (SLAs) and performance targets
   - Availability requirements (uptime targets, redundancy needs)
   - Geographic distribution requirements
   - Compliance and regulatory constraints

4. **Application Characteristics**
   - Application architecture (monolith, microservices, serverless)
   - Resource consumption patterns per user/transaction
   - Concurrency and parallelism capabilities
   - Stateful vs. stateless components
   - Database and storage access patterns

5. **Performance Metrics**
   - Response time requirements (p50, p95, p99)
   - Throughput requirements (requests per second, transactions per minute)
   - Latency tolerances
   - Error rate thresholds
   - Resource saturation points

### Optional Inputs

6. **Cost Constraints**
   - Budget limitations and cost targets
   - Cost optimization priorities
   - Reserved capacity vs. on-demand preferences
   - Multi-year financial planning requirements
   - Cost allocation and chargeback models

7. **Technology Constraints**
   - Preferred cloud providers or platforms
   - Technology stack limitations
   - Licensing constraints
   - Vendor lock-in considerations
   - Legacy system integration requirements

8. **Business Context**
   - Product roadmap and feature plans
   - Marketing campaigns and launch schedules
   - Seasonal business patterns
   - Competitive landscape and market dynamics
   - Merger and acquisition plans

9. **Risk Tolerance**
   - Acceptable downtime windows
   - Performance degradation tolerance
   - Data loss tolerance (RPO)
   - Recovery time objectives (RTO)
   - Overprovisioning vs. underprovisioning preferences

10. **Operational Context**
    - Team size and expertise
    - Operational runbooks and procedures
    - Monitoring and alerting capabilities
    - Incident response processes
    - Change management procedures

## Expected Outputs

### Primary Deliverables

1. **Capacity Plan Document**
   - Executive summary with key recommendations
   - Current state capacity assessment
   - Future capacity requirements and projections
   - Gap analysis between current and required capacity
   - Recommended infrastructure changes and timeline

2. **Resource Sizing Recommendations**
   - Compute resource specifications (instance types, counts, configurations)
   - Storage capacity requirements (size, IOPS, throughput)
   - Network capacity requirements (bandwidth, connections, latency)
   - Database sizing (instance types, read replicas, sharding strategy)
   - Cache sizing and configuration recommendations

3. **Growth Projections**
   - Traffic growth forecasts (6-month, 1-year, 3-year horizons)
   - Resource utilization projections over time
   - Capacity headroom calculations
   - Trigger points for next capacity expansion
   - Seasonal and cyclical demand predictions

4. **Cost Analysis**
   - Current infrastructure costs breakdown
   - Projected costs for recommended capacity
   - Cost-benefit analysis of capacity options
   - ROI calculations for capacity investments
   - Cost optimization opportunities

5. **Risk Assessment**
   - Single points of failure identification
   - Capacity-related risks and mitigation strategies
   - Performance degradation scenarios
   - Disaster recovery capacity requirements
   - Compliance and regulatory risk analysis

### Supporting Deliverables

6. **Implementation Roadmap**
   - Phased capacity expansion plan
   - Timeline and milestones
   - Dependencies and prerequisites
   - Rollback and contingency plans
   - Testing and validation procedures

7. **Monitoring and Alerting Strategy**
   - Key capacity metrics to track
   - Alert thresholds and escalation procedures
   - Dashboard designs for capacity visibility
   - Reporting cadence and stakeholders
   - Capacity review schedule

8. **Scaling Policies**
   - Auto-scaling configurations and triggers
   - Manual scaling procedures and runbooks
   - Scale-up and scale-down criteria
   - Cooldown periods and rate limits
   - Emergency scaling procedures

9. **Architecture Recommendations**
   - Architectural changes to improve scalability
   - Caching strategies and CDN usage
   - Database optimization opportunities
   - Microservices decomposition suggestions
   - Serverless adoption opportunities

10. **Documentation and Knowledge Transfer**
    - Capacity planning methodology documentation
    - Assumptions and constraints documentation
    - Data sources and calculation methods
    - Stakeholder communication materials
    - Training materials for operations team

## Workflow

### Step 1: Data Collection and Current State Assessment

**Objective**: Gather comprehensive data about current infrastructure and usage patterns.

**Actions**:
- Inventory all infrastructure components (compute, storage, network, databases)
- Collect historical performance metrics (CPU, memory, disk, network utilization)
- Analyze traffic patterns and usage trends over time
- Document current resource allocations and configurations
- Identify current performance bottlenecks and constraints
- Review existing monitoring dashboards and alerts
- Interview stakeholders to understand current pain points
- Document current costs and cost allocation

**Outputs**:
- Infrastructure inventory spreadsheet
- Historical metrics reports and visualizations
- Current state capacity assessment document
- Bottleneck analysis report

**Quality Checks**:
- All major infrastructure components are documented
- Metrics data covers sufficient historical period (minimum 3-6 months)
- Peak usage periods are identified and analyzed
- Current utilization baselines are established

### Step 2: Business Requirements Analysis

**Objective**: Understand business growth plans and capacity drivers.

**Actions**:
- Interview business stakeholders about growth projections
- Review product roadmap and feature plans
- Analyze marketing plans and campaign schedules
- Understand seasonal business patterns and cyclical demand
- Document SLA requirements and performance targets
- Identify compliance and regulatory requirements
- Assess geographic expansion plans
- Review budget constraints and cost targets

**Outputs**:
- Business requirements document
- Growth projection assumptions
- SLA and performance requirements matrix
- Compliance requirements checklist

**Quality Checks**:
- Business growth projections are specific and measurable
- SLA requirements are clearly defined
- Stakeholder alignment on priorities is achieved
- Budget constraints are understood and documented

### Step 3: Workload Characterization

**Objective**: Understand application resource consumption patterns.

**Actions**:
- Analyze resource consumption per user/transaction
- Identify resource-intensive operations and workflows
- Characterize peak vs. average workload patterns
- Measure concurrency and parallelism capabilities
- Analyze database query patterns and data access
- Profile application performance under various loads
- Identify stateful vs. stateless components
- Document application scaling characteristics

**Outputs**:
- Workload characterization report
- Resource consumption models
- Application performance profiles
- Scaling characteristics documentation

**Quality Checks**:
- Resource consumption is measured per relevant unit (user, transaction, request)
- Peak workload scenarios are identified and quantified
- Application bottlenecks are understood
- Scaling limitations are documented

### Step 4: Demand Forecasting

**Objective**: Project future capacity requirements based on growth and usage patterns.

**Actions**:
- Apply statistical forecasting methods to historical data
- Incorporate business growth projections into models
- Account for seasonal and cyclical variations
- Model impact of planned features and product changes
- Consider marketing campaigns and special events
- Create multiple scenarios (conservative, expected, aggressive)
- Project demand over multiple time horizons (6mo, 1yr, 3yr)
- Validate forecasts with stakeholders

**Outputs**:
- Demand forecast models and projections
- Growth scenario analysis
- Traffic and usage projections over time
- Confidence intervals and uncertainty analysis

**Quality Checks**:
- Forecasting methodology is appropriate for data patterns
- Multiple scenarios account for uncertainty
- Seasonal and cyclical patterns are incorporated
- Forecasts are validated against business expectations

### Step 5: Capacity Modeling

**Objective**: Translate demand forecasts into infrastructure capacity requirements.

**Actions**:
- Calculate required compute capacity (CPU, memory, instances)
- Determine storage capacity needs (size, IOPS, throughput)
- Estimate network capacity requirements (bandwidth, connections)
- Size database infrastructure (instances, replicas, sharding)
- Model cache requirements and hit rates
- Account for redundancy and high availability needs
- Include capacity headroom for unexpected spikes
- Consider auto-scaling capabilities and limits

**Outputs**:
- Capacity requirement calculations
- Infrastructure sizing recommendations
- Capacity headroom analysis
- Redundancy and failover capacity specifications

**Quality Checks**:
- Capacity calculations are based on validated workload models
- Redundancy requirements are incorporated
- Headroom is appropriate for growth uncertainty
- Calculations account for all infrastructure layers

### Step 6: Cost Analysis and Optimization

**Objective**: Evaluate costs of capacity options and identify optimization opportunities.

**Actions**:
- Calculate costs for recommended capacity configurations
- Compare reserved vs. on-demand pricing options
- Identify cost optimization opportunities (right-sizing, spot instances)
- Analyze cost-performance tradeoffs
- Model costs over multiple time horizons
- Compare costs across cloud providers or platforms
- Evaluate total cost of ownership (TCO)
- Create cost allocation and chargeback models

**Outputs**:
- Cost analysis spreadsheets and models
- Cost-benefit analysis for capacity options
- Cost optimization recommendations
- TCO comparison across alternatives

**Quality Checks**:
- All relevant cost factors are included (compute, storage, network, licenses)
- Reserved capacity opportunities are evaluated
- Cost projections align with budget constraints
- Cost optimization recommendations are actionable

### Step 7: Risk Assessment and Mitigation

**Objective**: Identify capacity-related risks and develop mitigation strategies.

**Actions**:
- Identify single points of failure in capacity plan
- Assess risks of underprovisioning vs. overprovisioning
- Evaluate disaster recovery capacity requirements
- Analyze performance degradation scenarios
- Assess compliance and regulatory risks
- Identify dependencies and external constraints
- Develop risk mitigation strategies
- Create contingency plans for capacity shortfalls

**Outputs**:
- Risk assessment matrix
- Risk mitigation strategies
- Disaster recovery capacity plan
- Contingency procedures

**Quality Checks**:
- All significant capacity risks are identified
- Mitigation strategies are specific and actionable
- Disaster recovery requirements are addressed
- Contingency plans are realistic and testable

### Step 8: Implementation Planning

**Objective**: Create detailed plan for implementing capacity recommendations.

**Actions**:
- Develop phased implementation roadmap
- Define milestones and success criteria
- Identify dependencies and prerequisites
- Estimate implementation timelines
- Assign responsibilities and ownership
- Plan testing and validation procedures
- Create rollback and contingency plans
- Define communication and stakeholder management approach

**Outputs**:
- Implementation roadmap and timeline
- Milestone definitions and success criteria
- Responsibility assignment matrix (RACI)
- Testing and validation plan

**Quality Checks**:
- Roadmap is realistic and achievable
- Dependencies are identified and managed
- Success criteria are measurable
- Rollback plans are defined

### Step 9: Monitoring and Alerting Design

**Objective**: Establish monitoring to track capacity utilization and trigger alerts.

**Actions**:
- Define key capacity metrics to monitor
- Set alert thresholds for capacity warnings
- Design capacity dashboards for visibility
- Establish capacity review cadence
- Create reporting templates for stakeholders
- Define escalation procedures for capacity issues
- Plan for continuous capacity monitoring
- Integrate with existing monitoring systems

**Outputs**:
- Capacity monitoring strategy
- Alert threshold definitions
- Dashboard designs and mockups
- Capacity reporting templates

**Quality Checks**:
- Key capacity metrics are identified and measurable
- Alert thresholds provide adequate warning time
- Dashboards provide actionable insights
- Monitoring integrates with existing tools

### Step 10: Documentation and Knowledge Transfer

**Objective**: Document capacity plan and enable team to execute and maintain it.

**Actions**:
- Create comprehensive capacity planning document
- Document assumptions, constraints, and methodology
- Prepare executive summary and presentations
- Develop operational runbooks for capacity management
- Create training materials for operations team
- Conduct knowledge transfer sessions
- Establish capacity planning review schedule
- Define continuous improvement process

**Outputs**:
- Capacity planning document
- Executive presentation
- Operational runbooks
- Training materials

**Quality Checks**:
- Documentation is clear and comprehensive
- Assumptions and constraints are explicitly stated
- Operational procedures are actionable
- Knowledge transfer is completed successfully

## Decision Framework

### Forecasting Method Selection

**Use Time Series Analysis when**:
- Have sufficient historical data (minimum 12-24 months)
- Usage patterns show clear trends and seasonality
- Business is relatively stable with predictable growth
- Need to project current trends into future
- Want to identify seasonal patterns automatically

**Use Regression Analysis when**:
- Can identify clear drivers of capacity demand (users, transactions, etc.)
- Have data on both capacity usage and driving factors
- Relationships between variables are relatively linear
- Want to model impact of specific business changes
- Need to explain capacity requirements to stakeholders

**Use Scenario Planning when**:
- High uncertainty about future growth
- Multiple possible business trajectories
- Planning for major changes (launches, acquisitions, pivots)
- Need to prepare for wide range of outcomes
- Want to stress-test capacity plans

**Use Machine Learning when**:
- Have large volumes of historical data
- Complex, non-linear usage patterns
- Multiple interacting factors affecting capacity
- Need highly accurate short-term forecasts
- Have expertise and tools for ML implementation

### Capacity Headroom Strategy

**Use Conservative Headroom (50-100%) when**:
- High cost of capacity shortfalls (revenue loss, SLA penalties)
- Rapid, unpredictable growth expected
- Limited ability to scale quickly
- High availability requirements (99.99%+)
- Seasonal peaks are large and unpredictable

**Use Moderate Headroom (20-50%) when**:
- Balanced cost and risk tolerance
- Moderate growth with some predictability
- Can scale within hours to days
- Standard availability requirements (99.9%)
- Seasonal patterns are understood

**Use Minimal Headroom (10-20%) when**:
- Cost optimization is high priority
- Slow, predictable growth
- Can scale very quickly (minutes)
- Lower availability requirements acceptable
- Strong auto-scaling capabilities

**Use Just-in-Time Capacity when**:
- Using serverless or highly elastic infrastructure
- Pay-per-use pricing model
- Sub-minute scaling capabilities
- Workload is highly variable and unpredictable
- Cost optimization is critical

### Scaling Strategy Selection

**Use Vertical Scaling when**:
- Application doesn't support horizontal scaling
- Database or stateful workloads
- Simpler to implement and manage
- Limited number of instances to manage
- Licensing costs favor fewer, larger instances

**Use Horizontal Scaling when**:
- Application is stateless or can be made stateless
- Need high availability and fault tolerance
- Want to scale incrementally
- Cost-effective with commodity instances
- Can distribute load across instances

**Use Auto-Scaling when**:
- Workload varies significantly over time
- Can define clear scaling triggers
- Application scales quickly (startup time < 5 minutes)
- Cost optimization is important
- Have monitoring and metrics in place

**Use Manual Scaling when**:
- Capacity changes are infrequent and predictable
- Scaling requires careful coordination
- Application has long startup or warmup times
- Want human oversight for scaling decisions
- Auto-scaling complexity outweighs benefits

### Cloud Provider Selection for Capacity

**Choose AWS when**:
- Need widest range of instance types and services
- Want mature auto-scaling and capacity management tools
- Require global presence with many regions
- Need extensive marketplace and third-party integrations
- Have existing AWS expertise and investments

**Choose Azure when**:
- Integrating with Microsoft ecosystem (Windows, .NET, Office)
- Need hybrid cloud capabilities
- Want strong enterprise support and SLAs
- Require specific Azure-native services
- Have existing Microsoft licensing agreements

**Choose Google Cloud when**:
- Need advanced data analytics and ML capabilities
- Want competitive pricing for compute and storage
- Require strong Kubernetes support (GKE)
- Need high-performance networking
- Prefer Google's technology and innovation

**Choose Multi-Cloud when**:
- Need geographic coverage beyond single provider
- Want to avoid vendor lock-in
- Require specific services from multiple providers
- Have compliance requirements for data residency
- Want to optimize costs across providers

### Database Scaling Approach

**Use Read Replicas when**:
- Read-heavy workload (>70% reads)
- Can tolerate eventual consistency for reads
- Need to scale read capacity independently
- Want to offload reporting and analytics queries
- Database supports replication

**Use Sharding when**:
- Dataset is very large (multi-TB)
- Write-heavy workload that can't be scaled vertically
- Can partition data by clear criteria (user ID, geography, etc.)
- Need to scale beyond single database limits
- Have expertise to manage sharded architecture

**Use Database Clustering when**:
- Need high availability and automatic failover
- Want to scale both reads and writes
- Can afford clustering overhead and costs
- Require strong consistency guarantees
- Database supports native clustering

**Use Caching when**:
- Frequently accessed data changes infrequently
- Can tolerate slightly stale data
- Database is bottleneck for read performance
- Want to reduce database load
- Have clear cache invalidation strategy

### Storage Tier Selection

**Use High-Performance Storage when**:
- Database workloads requiring high IOPS
- Latency-sensitive applications
- Frequently accessed data (hot data)
- Can justify premium storage costs
- Need consistent performance guarantees

**Use Standard Storage when**:
- General-purpose workloads
- Balanced performance and cost requirements
- Moderately accessed data (warm data)
- Standard performance is sufficient
- Most common use case

**Use Cold Storage when**:
- Infrequently accessed data (accessed < monthly)
- Long-term retention and archival
- Compliance and regulatory requirements
- Cost optimization is priority
- Can tolerate retrieval delays

**Use Archival Storage when**:
- Rarely accessed data (accessed < yearly)
- Long-term backup and disaster recovery
- Regulatory retention requirements
- Lowest cost is critical
- Can tolerate hours for retrieval

## Quality Checklist

### Data Collection Quality

- [ ] Infrastructure inventory is complete and current
- [ ] Historical metrics cover sufficient time period (minimum 6 months)
- [ ] Peak usage periods are identified and analyzed
- [ ] Current utilization baselines are established
- [ ] Bottlenecks and constraints are documented
- [ ] Data sources are reliable and validated
- [ ] Metrics granularity is appropriate for analysis
- [ ] Data gaps and limitations are identified
- [ ] Current costs are accurately documented
- [ ] Stakeholder interviews are completed

### Requirements Quality

- [ ] Business growth projections are specific and measurable
- [ ] SLA requirements are clearly defined
- [ ] Performance targets are quantified
- [ ] Compliance requirements are documented
- [ ] Budget constraints are understood
- [ ] Geographic requirements are specified
- [ ] Availability requirements are defined
- [ ] Risk tolerance is articulated
- [ ] Stakeholder priorities are aligned
- [ ] Requirements are validated with stakeholders

### Forecasting Quality

- [ ] Forecasting methodology is appropriate for data patterns
- [ ] Multiple scenarios account for uncertainty
- [ ] Seasonal and cyclical patterns are incorporated
- [ ] Business growth assumptions are validated
- [ ] Confidence intervals are calculated
- [ ] Forecasts are reviewed by stakeholders
- [ ] Assumptions are explicitly documented
- [ ] Forecast accuracy is assessed against historical data
- [ ] External factors are considered
- [ ] Forecasts cover appropriate time horizons

### Capacity Modeling Quality

- [ ] Capacity calculations are based on validated workload models
- [ ] All infrastructure layers are sized (compute, storage, network)
- [ ] Redundancy requirements are incorporated
- [ ] Headroom is appropriate for growth uncertainty
- [ ] Auto-scaling capabilities are considered
- [ ] Database capacity is properly sized
- [ ] Cache requirements are calculated
- [ ] Network capacity is adequate
- [ ] Storage IOPS and throughput are specified
- [ ] Calculations are peer-reviewed

### Cost Analysis Quality

- [ ] All relevant cost factors are included
- [ ] Reserved capacity opportunities are evaluated
- [ ] Cost projections align with budget constraints
- [ ] Cost optimization recommendations are actionable
- [ ] TCO is calculated over multiple years
- [ ] Cost-performance tradeoffs are analyzed
- [ ] Cost allocation models are defined
- [ ] Pricing assumptions are documented
- [ ] Cost comparisons across alternatives are fair
- [ ] Financial stakeholders review cost analysis

### Risk Assessment Quality

- [ ] All significant capacity risks are identified
- [ ] Mitigation strategies are specific and actionable
- [ ] Disaster recovery requirements are addressed
- [ ] Single points of failure are identified
- [ ] Performance degradation scenarios are analyzed
- [ ] Compliance risks are assessed
- [ ] Contingency plans are realistic and testable
- [ ] Risk likelihood and impact are quantified
- [ ] Risk owners are assigned
- [ ] Risks are communicated to stakeholders

### Implementation Planning Quality

- [ ] Roadmap is realistic and achievable
- [ ] Dependencies are identified and managed
- [ ] Success criteria are measurable
- [ ] Rollback plans are defined
- [ ] Responsibilities are clearly assigned
- [ ] Timeline includes buffer for contingencies
- [ ] Testing and validation procedures are specified
- [ ] Communication plan is established
- [ ] Resource requirements are identified
- [ ] Stakeholders approve implementation plan

### Monitoring Quality

- [ ] Key capacity metrics are identified and measurable
- [ ] Alert thresholds provide adequate warning time
- [ ] Dashboards provide actionable insights
- [ ] Monitoring integrates with existing tools
- [ ] Reporting cadence is appropriate
- [ ] Escalation procedures are defined
- [ ] Capacity review schedule is established
- [ ] Metrics align with business objectives
- [ ] Monitoring covers all infrastructure layers
- [ ] Automated alerting is configured

### Documentation Quality

- [ ] Documentation is clear and comprehensive
- [ ] Assumptions and constraints are explicitly stated
- [ ] Methodology is documented and reproducible
- [ ] Operational procedures are actionable
- [ ] Knowledge transfer is completed successfully
- [ ] Executive summary is concise and compelling
- [ ] Technical details are accurate and complete
- [ ] Visualizations are clear and informative
- [ ] Documentation is accessible to stakeholders
- [ ] Version control and updates are managed

## Common Mistakes

### Planning Mistakes

1. **Insufficient Historical Data**
   - **Mistake**: Basing capacity plans on inadequate historical data (< 3 months)
   - **Impact**: Inaccurate forecasts, missed seasonal patterns, poor capacity decisions
   - **Solution**: Collect minimum 6-12 months of historical data; supplement with business projections
   - **Prevention**: Establish continuous monitoring early; maintain historical data archives

2. **Ignoring Seasonal Patterns**
   - **Mistake**: Failing to account for seasonal or cyclical demand variations
   - **Impact**: Capacity shortfalls during peak seasons; overprovisioning during slow periods
   - **Solution**: Analyze multi-year data to identify seasonal patterns; plan for peak capacity
   - **Prevention**: Use time-series analysis; consult with business on seasonal drivers

3. **Overlooking Growth Drivers**
   - **Mistake**: Extrapolating trends without considering business changes (launches, campaigns, features)
   - **Impact**: Capacity plans don't align with business reality; unexpected shortfalls or waste
   - **Solution**: Incorporate business roadmap and growth initiatives into forecasts
   - **Prevention**: Regular stakeholder engagement; align capacity planning with business planning

4. **Inadequate Headroom**
   - **Mistake**: Planning for exact forecasted capacity without buffer for uncertainty
   - **Impact**: Frequent capacity shortfalls; performance degradation; emergency scaling
   - **Solution**: Include appropriate headroom (20-50%) based on growth uncertainty and scaling speed
   - **Prevention**: Define headroom policy; account for forecast uncertainty

5. **Single Point of Failure**
   - **Mistake**: Not accounting for redundancy and high availability in capacity planning
   - **Impact**: Capacity loss during failures; inability to meet SLAs; extended outages
   - **Solution**: Plan for N+1 or N+2 redundancy; include failover capacity
   - **Prevention**: Include availability requirements in capacity planning; test failover scenarios

### Forecasting Mistakes

6. **Linear Extrapolation of Non-Linear Growth**
   - **Mistake**: Using simple linear projections for exponential or non-linear growth patterns
   - **Impact**: Severe underestimation of future capacity needs; capacity crises
   - **Solution**: Use appropriate forecasting methods (exponential, polynomial) for growth patterns
   - **Prevention**: Analyze growth patterns; use multiple forecasting methods; validate with business

7. **Ignoring External Factors**
   - **Mistake**: Not considering external factors (market changes, competition, economy, regulations)
   - **Impact**: Forecasts don't reflect business reality; capacity plans become obsolete
   - **Solution**: Include external factors in scenario planning; create multiple forecast scenarios
   - **Prevention**: Engage with business stakeholders; monitor market and competitive landscape

8. **Overconfidence in Forecasts**
   - **Mistake**: Treating forecasts as certainties rather than probabilistic estimates
   - **Impact**: Inadequate contingency planning; inflexible capacity plans
   - **Solution**: Communicate forecast uncertainty; create multiple scenarios; plan for variability
   - **Prevention**: Calculate confidence intervals; use scenario planning; maintain flexibility

9. **Neglecting Outliers and Anomalies**
   - **Mistake**: Removing or ignoring outliers without understanding their causes
   - **Impact**: Missing important capacity drivers; underestimating peak requirements
   - **Solution**: Investigate outliers; determine if they represent future patterns or one-time events
   - **Prevention**: Analyze outliers carefully; consult with business on unusual events

10. **Stale Forecasts**
    - **Mistake**: Not updating forecasts as business conditions change
    - **Impact**: Capacity plans become obsolete; decisions based on outdated assumptions
    - **Solution**: Establish regular forecast review and update cadence (quarterly or semi-annually)
    - **Prevention**: Schedule regular capacity planning reviews; monitor actual vs. forecast

### Implementation Mistakes

11. **Big Bang Capacity Additions**
    - **Mistake**: Adding large capacity increments infrequently rather than gradual scaling
    - **Impact**: Overprovisioning and waste; large capital expenditures; integration risks
    - **Solution**: Implement phased capacity additions aligned with demand growth
    - **Prevention**: Plan incremental scaling; use auto-scaling where possible

12. **Ignoring Lead Times**
    - **Mistake**: Not accounting for procurement, provisioning, and deployment lead times
    - **Impact**: Capacity arrives too late; performance degradation or outages
    - **Solution**: Include lead times in capacity planning; trigger procurement early
    - **Prevention**: Document lead times for all capacity types; set early warning thresholds

13. **No Testing or Validation**
    - **Mistake**: Deploying capacity without testing performance and integration
    - **Impact**: Capacity doesn't perform as expected; integration issues; rollback required
    - **Solution**: Test capacity additions in staging; validate performance before production
    - **Prevention**: Include testing phase in implementation plan; define validation criteria

14. **Poor Change Management**
    - **Mistake**: Implementing capacity changes without proper change management procedures
    - **Impact**: Unexpected outages; configuration errors; rollback difficulties
    - **Solution**: Follow change management processes; plan rollback procedures
    - **Prevention**: Integrate capacity changes into change management; require approvals

15. **Lack of Monitoring Post-Implementation**
    - **Mistake**: Not monitoring capacity utilization after implementation
    - **Impact**: Can't validate capacity plan; miss issues or optimization opportunities
    - **Solution**: Implement comprehensive monitoring; compare actual vs. planned utilization
    - **Prevention**: Define monitoring strategy upfront; establish review cadence

### Cost Mistakes

16. **Ignoring Total Cost of Ownership**
    - **Mistake**: Focusing only on infrastructure costs, ignoring operational and licensing costs
    - **Impact**: Underestimating true costs; budget overruns
    - **Solution**: Calculate TCO including all cost factors (infrastructure, operations, licenses, support)
    - **Prevention**: Use comprehensive cost models; include all stakeholders in cost analysis

17. **Not Leveraging Reserved Capacity**
    - **Mistake**: Using only on-demand pricing for predictable, steady-state capacity
    - **Impact**: Unnecessary costs; missed savings opportunities
    - **Solution**: Use reserved instances or committed use discounts for baseline capacity
    - **Prevention**: Analyze usage patterns; calculate reserved vs. on-demand breakeven

18. **Overprovisioning Without Justification**
    - **Mistake**: Adding excessive capacity "just in case" without cost-benefit analysis
    - **Impact**: Wasted resources; unnecessary costs; poor ROI
    - **Solution**: Justify capacity with data; balance cost and risk; use auto-scaling
    - **Prevention**: Require business case for capacity additions; review utilization regularly

19. **Ignoring Cost Optimization Opportunities**
    - **Mistake**: Not right-sizing resources or using cost-effective alternatives
    - **Impact**: Higher costs than necessary; poor cost efficiency
    - **Solution**: Regularly review resource utilization; right-size instances; use spot/preemptible instances
    - **Prevention**: Include cost optimization in capacity planning; establish cost review process

20. **No Cost Tracking or Chargeback**
    - **Mistake**: Not tracking capacity costs by team, product, or service
    - **Impact**: No accountability for capacity costs; difficulty optimizing spend
    - **Solution**: Implement cost allocation and chargeback; tag resources appropriately
    - **Prevention**: Design cost tracking into capacity plan; use cloud cost management tools

### Operational Mistakes

21. **Inadequate Documentation**
    - **Mistake**: Poor documentation of capacity plan, assumptions, and procedures
    - **Impact**: Knowledge loss; difficulty executing plan; repeated mistakes
    - **Solution**: Create comprehensive documentation; maintain runbooks; conduct knowledge transfer
    - **Prevention**: Make documentation a deliverable; assign documentation ownership

22. **No Capacity Ownership**
    - **Mistake**: Unclear ownership and accountability for capacity management
    - **Impact**: Reactive capacity management; finger-pointing during issues; poor execution
    - **Solution**: Assign clear ownership for capacity planning and execution
    - **Prevention**: Define roles and responsibilities; include in RACI matrix

23. **Siloed Capacity Planning**
    - **Mistake**: Planning capacity in isolation without cross-functional collaboration
    - **Impact**: Misaligned plans; missed dependencies; suboptimal decisions
    - **Solution**: Involve all stakeholders (engineering, business, finance, operations)
    - **Prevention**: Establish cross-functional capacity planning process

24. **No Continuous Improvement**
    - **Mistake**: Treating capacity planning as one-time exercise rather than ongoing process
    - **Impact**: Plans become stale; missed optimization opportunities; reactive management
    - **Solution**: Establish regular capacity review cadence; track actual vs. plan; iterate
    - **Prevention**: Schedule recurring capacity planning reviews; track metrics over time

25. **Ignoring Application Optimization**
    - **Mistake**: Adding capacity without optimizing application efficiency
    - **Impact**: Higher costs; masking performance issues; technical debt accumulation
    - **Solution**: Optimize application before adding capacity; address inefficiencies
    - **Prevention**: Include application performance review in capacity planning

## Examples

### Example 1: E-Commerce Platform Capacity Planning for Holiday Season

**Context**:
A mid-sized e-commerce company with 500,000 active users needs to plan capacity for the upcoming holiday season (Black Friday through New Year). Current infrastructure runs on AWS with a microservices architecture (15 services), PostgreSQL databases, Redis caching, and CloudFront CDN. Last year's holiday season experienced performance degradation and some outages due to insufficient capacity. The company expects 50% user growth and is launching a mobile app that will increase traffic by an additional 30%.

**Current State**:
- **Infrastructure**: 50 EC2 instances (m5.xlarge), 5 RDS PostgreSQL instances (db.r5.2xlarge), 3 ElastiCache Redis clusters
- **Baseline traffic**: 10,000 requests/second, 2 million daily active users
- **Peak traffic (normal)**: 25,000 requests/second during evening hours
- **Last year's holiday peak**: 60,000 requests/second (caused performance issues)
- **Current costs**: $45,000/month
- **SLA target**: 99.9% uptime, p95 response time < 500ms

**Requirements**:
- Support 50% user growth + 30% from mobile app = 95% total traffic increase
- Handle Black Friday peak (estimated 5x normal peak = 125,000 requests/second)
- Maintain SLA during entire holiday season
- Optimize costs (avoid overprovisioning after holidays)
- Zero downtime during capacity scaling

**Capacity Planning Process**:

**Step 1: Historical Analysis**
```
Analyzed 2 years of historical data:
- Identified holiday season pattern: 3x normal traffic (Nov-Dec)
- Black Friday peak: 5x normal peak for 6-hour window
- Cyber Monday peak: 4x normal peak for 8-hour window
- Sustained elevated traffic: 2x normal for 6 weeks
- Post-holiday drop: Returns to baseline in early January
```

**Step 2: Demand Forecasting**
```
Forecasted holiday season demand:

Baseline (current): 10,000 req/s average, 25,000 req/s peak
Growth factor: 1.95x (50% user growth + 30% mobile)

New baseline: 19,500 req/s average, 48,750 req/s peak

Holiday multipliers:
- Sustained (6 weeks): 2x = 39,000 req/s average, 97,500 req/s peak
- Black Friday: 5x peak = 243,750 req/s
- Cyber Monday: 4x peak = 195,000 req/s

Added 30% headroom for uncertainty: 316,875 req/s target capacity
```

**Step 3: Workload Characterization**
```
Resource consumption per 1,000 req/s:
- Compute: 2 m5.xlarge instances (8 vCPU, 16 GB RAM)
- Database: 50 connections, 1,000 IOPS
- Cache: 2 GB memory, 10,000 ops/s
- Network: 100 Mbps bandwidth

Bottleneck analysis:
- Database connections hitting limits at 60,000 req/s
- Cache memory exhaustion at 80,000 req/s
- Application instances CPU at 70% at 50,000 req/s
```

**Step 4: Capacity Sizing**
```
Target: 316,875 req/s (Black Friday peak with headroom)

Compute (Application Servers):
- Required: 634 m5.xlarge instances
- Strategy: Baseline 100 instances + auto-scaling to 650
- Configuration: Target CPU 60%, scale-out at 70%, scale-in at 40%

Database:
- Bottleneck: Connection limits
- Solution: Add 10 read replicas (db.r5.2xlarge)
- Connection pooling: PgBouncer with 10,000 connection limit
- Write capacity: Upgrade master to db.r5.8xlarge

Cache:
- Required: 634 GB memory
- Strategy: 20 ElastiCache Redis nodes (r5.4xlarge, 32 GB each)
- Configuration: Cluster mode enabled for distribution

CDN:
- Increase CloudFront cache hit ratio from 70% to 85%
- Pre-warm cache for popular products
- Reduce origin requests by 50%

Load Balancing:
- Upgrade ALB to handle 300,000 req/s
- Add 2 additional ALBs for redundancy

Network:
- Bandwidth: 31,687 Mbps (31.7 Gbps)
- VPC: Increase NAT Gateway capacity
```

**Step 5: Cost Analysis**
```
Current monthly cost: $45,000

Holiday season capacity (2 months):

Compute:
- Baseline (100 instances): $7,300/month (reserved instances)
- Auto-scaling (550 instances average): $40,150/month (on-demand)
- Total: $47,450/month

Database:
- Master upgrade: $2,500/month
- Read replicas (10): $12,000/month
- Total: $14,500/month

Cache:
- Redis clusters (20 nodes): $18,000/month

CDN:
- Increased traffic: $8,000/month

Load Balancers:
- ALBs (3): $750/month

Total holiday cost: $88,700/month
Holiday season (2 months): $177,400

Post-holiday scaling down:
- Return to baseline + 95% growth = $65,000/month

Annual cost:
- Pre-holiday (9 months): $45,000 × 9 = $405,000
- Holiday (2 months): $177,400
- Post-holiday (1 month): $65,000
- Total: $647,400/year

Cost increase: $647,400 - ($45,000 × 12) = $107,400 (20% increase)
```

**Step 6: Implementation Plan**
```
Phased rollout (8 weeks before Black Friday):

Week 1-2: Infrastructure preparation
- Set up auto-scaling groups
- Configure CloudWatch alarms
- Create AMIs for quick scaling
- Set up monitoring dashboards

Week 3-4: Database scaling
- Upgrade master database (maintenance window)
- Add read replicas (2 per week)
- Implement connection pooling
- Test failover procedures

Week 5-6: Cache scaling
- Add Redis nodes incrementally
- Enable cluster mode
- Pre-warm cache with popular products
- Test cache performance

Week 7: Load testing
- Simulate Black Friday traffic (150,000 req/s)
- Validate auto-scaling behavior
- Test database and cache performance
- Identify and fix bottlenecks

Week 8: Final preparations
- Review and adjust configurations
- Conduct disaster recovery drill
- Brief operations team
- Establish war room for Black Friday
```

**Step 7: Monitoring and Alerting**
```
Key metrics and thresholds:

Application:
- Request rate: Alert at 200,000 req/s (80% of capacity)
- Response time p95: Alert at 400ms (80% of SLA)
- Error rate: Alert at 0.5%
- CPU utilization: Alert at 75%

Database:
- Connection count: Alert at 8,000 (80% of limit)
- Replication lag: Alert at 5 seconds
- IOPS: Alert at 80% of provisioned
- CPU: Alert at 75%

Cache:
- Memory usage: Alert at 80%
- Eviction rate: Alert at 1,000/s
- Hit rate: Alert if below 80%

Dashboards:
- Executive dashboard: High-level metrics, SLA compliance
- Operations dashboard: Detailed metrics, auto-scaling status
- Capacity dashboard: Utilization vs. capacity, headroom

Escalation:
- Warning alerts: Notify on-call engineer
- Critical alerts: Page on-call + notify manager
- SLA breach: Escalate to VP Engineering
```

**Results**:
- Successfully handled Black Friday peak of 238,000 requests/second
- Maintained 99.97% uptime during holiday season (exceeded 99.9% SLA)
- p95 response time stayed below 450ms (met 500ms target)
- Zero customer-impacting incidents
- Auto-scaling worked smoothly, scaling from 100 to 620 instances during peak
- Cost came in 5% under budget ($168,000 vs. $177,400 for 2 months)
- Post-holiday scale-down executed successfully
- Mobile app launch successful with no capacity issues

**Key Success Factors**:
- Comprehensive historical analysis identified seasonal patterns
- Adequate headroom (30%) absorbed unexpected spikes
- Phased implementation allowed testing and validation
- Auto-scaling enabled cost-effective capacity management
- Load testing validated capacity before peak season
- Monitoring and alerting provided early warning

---

### Example 2: SaaS Platform Database Capacity Planning

**Context**:
A B2B SaaS platform providing project management software has 5,000 enterprise customers and 250,000 active users. The platform uses a multi-tenant PostgreSQL database architecture with customer data segregated by schema. Database performance has been degrading, with query response times increasing from 50ms to 300ms over the past 6 months. The company is planning to onboard 50 large enterprise customers (50,000+ users each) over the next year, which will triple the user base.

**Current State**:
- **Database**: PostgreSQL 14 on AWS RDS (db.r5.12xlarge: 48 vCPU, 384 GB RAM)
- **Storage**: 5 TB provisioned IOPS SSD (20,000 IOPS)
- **Connections**: 2,000 active connections (approaching max of 5,000)
- **Query volume**: 50,000 queries/second
- **Data growth**: 100 GB/month
- **Current cost**: $8,500/month (database only)
- **Performance issues**: Slow queries, connection exhaustion, replication lag

**Requirements**:
- Support 3x user growth (250,000 to 750,000 users)
- Maintain query response time p95 < 100ms
- Handle 50 large enterprise customers with data isolation
- Plan for 5-year growth to 2 million users
- Minimize downtime during scaling
- Optimize costs while meeting performance requirements

**Capacity Planning Process**:

**Step 1: Root Cause Analysis**
```
Performance investigation:

1. Connection exhaustion:
   - 2,000 active connections approaching 5,000 limit
   - Many idle connections holding resources
   - No connection pooling implemented

2. Query performance:
   - Identified 20 slow queries accounting for 60% of database time
   - Missing indexes on frequently queried columns
   - Inefficient joins and subqueries

3. Storage IOPS:
   - Hitting 20,000 IOPS limit during peak hours
   - Causing query queueing and latency

4. Replication lag:
   - Read replicas lagging 10-30 seconds
   - Insufficient write capacity on master

Conclusion: Need both optimization AND capacity scaling
```

**Step 2: Optimization First**
```
Implemented optimizations before scaling:

1. Connection pooling:
   - Deployed PgBouncer
   - Reduced active connections from 2,000 to 400
   - Improved connection efficiency by 5x

2. Query optimization:
   - Added missing indexes (15 indexes)
   - Rewrote 20 slow queries
   - Implemented query result caching
   - Reduced average query time from 300ms to 80ms

3. Read/write splitting:
   - Directed read queries to read replicas
   - Reduced master load by 60%
   - Improved replication lag to < 1 second

Results after optimization:
- Query response time p95: 80ms (met 100ms target)
- Connections: 400 active (80% headroom)
- IOPS utilization: 12,000 (40% headroom)
- Cost: Same ($8,500/month)

Conclusion: Optimization bought time, but still need to plan for 3x growth
```

**Step 3: Workload Modeling**
```
Current workload (post-optimization):
- 250,000 users
- 50,000 queries/second
- 400 active connections
- 12,000 IOPS
- 5 TB storage

Per-user metrics:
- Queries: 0.2 queries/second/user
- Connections: 0.0016 connections/user
- IOPS: 0.048 IOPS/user
- Storage: 20 MB/user

Projected workload (3x growth to 750,000 users):
- Queries: 150,000 queries/second
- Connections: 1,200 active
- IOPS: 36,000
- Storage: 15 TB

Projected workload (5-year: 2 million users):
- Queries: 400,000 queries/second
- Connections: 3,200 active
- IOPS: 96,000
- Storage: 40 TB
```

**Step 4: Scaling Strategy Evaluation**
```
Evaluated three approaches:

Option 1: Vertical Scaling (Single Large Instance)
Pros:
- Simplest to implement
- No application changes required
- Maintains single database

Cons:
- Limited by maximum instance size (db.r5.24xlarge)
- Can't scale beyond 768 GB RAM, 96 vCPU
- Single point of failure
- Expensive for large instances

Capacity: Can handle up to 1 million users
Cost: $17,000/month (db.r5.24xlarge)

Option 2: Read Replicas (Horizontal Read Scaling)
Pros:
- Scales read capacity independently
- Improves availability
- Cost-effective for read-heavy workloads

Cons:
- Doesn't scale write capacity
- Eventual consistency for reads
- Master still bottleneck for writes

Capacity: Can handle 2 million users (read), limited by writes
Cost: $8,500 (master) + $6,000 × 5 replicas = $38,500/month

Option 3: Sharding (Horizontal Read + Write Scaling)
Pros:
- Scales both reads and writes
- No theoretical limit to scale
- Better data isolation for large customers

Cons:
- Complex to implement and manage
- Requires application changes
- Cross-shard queries are difficult
- Higher operational overhead

Capacity: Can handle 10+ million users
Cost: $8,500 × 8 shards = $68,000/month (initial)

Decision: Hybrid approach
- Short-term (1 year): Vertical scaling + read replicas
- Long-term (2-5 years): Migrate to sharding
```

**Step 5: Short-Term Capacity Plan (1 Year)**
```
Phase 1: Immediate (Month 1-3)
- Upgrade master: db.r5.12xlarge → db.r5.16xlarge
  - 48 vCPU → 64 vCPU
  - 384 GB RAM → 512 GB RAM
- Add 3 read replicas (db.r5.8xlarge)
- Increase storage: 5 TB → 10 TB
- Increase IOPS: 20,000 → 30,000

Capacity: 500,000 users
Cost: $11,500 (master) + $18,000 (replicas) = $29,500/month

Phase 2: Mid-term (Month 4-8)
- Upgrade master: db.r5.16xlarge → db.r5.24xlarge
  - 64 vCPU → 96 vCPU
  - 512 GB RAM → 768 GB RAM
- Add 2 more read replicas (total 5)
- Increase storage: 10 TB → 20 TB
- Increase IOPS: 30,000 → 50,000

Capacity: 1 million users
Cost: $17,000 (master) + $30,000 (replicas) = $47,000/month

Phase 3: Long-term prep (Month 9-12)
- Design sharding strategy
- Develop sharding middleware
- Test sharding with pilot customers
- Plan migration to sharded architecture

Capacity: Preparing for 2+ million users
Cost: Same as Phase 2 + development costs
```

**Step 6: Long-Term Capacity Plan (2-5 Years)**
```
Sharding Architecture:

Shard key: Customer ID (tenant-based sharding)
Shards: 8 initially, expandable to 32+

Shard 1-8: Regular customers (1-5,000 users each)
- Database: db.r5.8xlarge per shard
- Storage: 2.5 TB per shard
- IOPS: 10,000 per shard
- Capacity: 250,000 users per shard

Dedicated shards: Large enterprise customers (50,000+ users)
- Database: db.r5.12xlarge per customer
- Storage: 5 TB per customer
- IOPS: 20,000 per customer
- Capacity: 100,000 users per shard

Sharding middleware:
- Routes queries to appropriate shard
- Handles cross-shard queries (rare)
- Manages shard rebalancing
- Provides connection pooling

Migration plan:
- Migrate 10% of customers per month
- Start with smallest customers
- Large customers migrated to dedicated shards
- Zero-downtime migration with dual-write

Capacity: 2 million users (8 shards × 250,000)
Cost: $6,000 × 8 shards = $48,000/month

Expansion: Add 1 shard per 250,000 users
Cost scaling: Linear with user growth
```

**Step 7: Cost Analysis**
```
Current: $8,500/month

Year 1 (750,000 users):
- Month 1-3: $29,500/month × 3 = $88,500
- Month 4-8: $47,000/month × 5 = $235,000
- Month 9-12: $47,000/month × 4 = $188,000
- Total: $511,500
- Average: $42,625/month

Year 2-5 (sharded, 2 million users):
- Sharded architecture: $48,000/month
- Annual: $576,000

Cost per user:
- Current: $0.034/user/month
- Year 1: $0.057/user/month (67% increase)
- Year 2+: $0.024/user/month (29% decrease from current)

ROI:
- Sharding investment: $200,000 (development)
- Payback period: 8 months (cost savings vs. vertical scaling)
- 5-year savings: $1.2 million vs. vertical scaling
```

**Step 8: Risk Mitigation**
```
Risks and mitigations:

Risk 1: Faster than expected growth
Mitigation:
- Monitor growth weekly
- Trigger alerts at 70% capacity
- Pre-approve emergency scaling budget
- Maintain 30% headroom

Risk 2: Sharding migration issues
Mitigation:
- Extensive testing in staging
- Gradual rollout (10% per month)
- Rollback plan for each customer
- Dual-write during migration

Risk 3: Large customer concentration
Mitigation:
- Dedicated shards for large customers
- Isolated capacity per large customer
- Contractual capacity commitments

Risk 4: Database upgrade failures
Mitigation:
- Test upgrades in staging
- Maintenance windows during low traffic
- Automated backups before changes
- Rollback procedures documented

Risk 5: Cost overruns
Mitigation:
- Monthly cost reviews
- Reserved instance purchases for baseline
- Cost alerts at 90% of budget
- Quarterly cost optimization reviews
```

**Results**:
- Successfully onboarded 50 large enterprise customers over 12 months
- User base grew from 250,000 to 820,000 (exceeded 750,000 target)
- Maintained query response time p95 < 80ms (beat 100ms target)
- Zero downtime during capacity scaling
- Sharding migration completed in 10 months (2 months ahead of schedule)
- Final architecture supports 3 million users
- Cost per user decreased by 35% after sharding
- Database performance improved 4x vs. pre-optimization

**Key Success Factors**:
- Optimization before scaling reduced immediate capacity needs
- Phased approach allowed gradual scaling and validation
- Hybrid strategy balanced short-term needs with long-term scalability
- Sharding architecture provided unlimited scale path
- Tenant-based sharding aligned with business model
- Extensive testing prevented migration issues

---

### Example 3: Video Streaming Platform CDN Capacity Planning

**Context**:
A video streaming platform with 10 million monthly active users is experiencing increased costs and performance issues with their CDN. The platform streams educational content with peak usage during evening hours (6 PM - 11 PM). They're planning to expand internationally to 5 new countries and launch a live streaming feature. Current CDN costs are $180,000/month and growing 15% monthly.

**Current State**:
- **CDN**: CloudFront with 50 TB/month data transfer
- **Origin**: S3 buckets in us-east-1
- **Cache hit ratio**: 65%
- **Peak bandwidth**: 500 Gbps
- **Average video bitrate**: 2 Mbps (SD), 5 Mbps (HD), 15 Mbps (4K)
- **Concurrent viewers (peak)**: 500,000
- **Content library**: 100,000 videos, 500 TB total
- **Geographic distribution**: 80% US, 15% Europe, 5% Asia

**Requirements**:
- Expand to 5 new countries (Brazil, India, Japan, Australia, Germany)
- Launch live streaming (expect 100,000 concurrent viewers)
- Improve cache hit ratio to 85%
- Reduce latency to < 100ms globally
- Support 4K streaming for premium users
- Reduce CDN costs by 30%
- Handle 2x traffic growth over next year

**Capacity Planning Process**:

**Step 1: Current Performance Analysis**
```
CDN performance metrics:

Cache hit ratio: 65%
- Cache hits: 32.5 TB/month
- Origin requests: 17.5 TB/month (35% miss rate)
- Origin bandwidth cost: $35,000/month

Latency by region:
- US: 45ms (good)
- Europe: 120ms (acceptable)
- Asia: 280ms (poor)
- South America: 350ms (poor)
- Australia: 400ms (poor)

Bandwidth distribution:
- Peak: 500 Gbps (6-11 PM)
- Average: 150 Gbps
- Peak-to-average ratio: 3.3x

Cost breakdown:
- Data transfer: $100,000/month (50 TB × $2,000/TB)
- Origin requests: $35,000/month
- Regional data transfer: $45,000/month
- Total: $180,000/month

Issues identified:
- Low cache hit ratio (65% vs. 85% target)
- High latency in new target markets
- Expensive origin requests
- Inefficient regional distribution
```

**Step 2: Demand Forecasting**
```
Projected growth:

User growth:
- Current: 10 million MAU
- International expansion: +5 million MAU
- Organic growth: +5 million MAU
- Total: 20 million MAU (2x growth)

Traffic growth:
- Current: 50 TB/month
- User growth: 2x = 100 TB/month
- Live streaming: +20 TB/month
- 4K adoption (10% of users): +15 TB/month
- Total: 135 TB/month (2.7x growth)

Peak bandwidth:
- Current: 500 Gbps
- User growth: 2x = 1,000 Gbps
- Live streaming: +200 Gbps
- 4K streaming: +150 Gbps
- Total: 1,350 Gbps (2.7x growth)

Concurrent viewers (peak):
- Current: 500,000
- Growth: 2x = 1,000,000
- Live streaming: +100,000
- Total: 1,100,000 concurrent viewers

Geographic distribution (projected):
- US: 40% (down from 80%)
- Europe: 20% (up from 15%)
- Asia: 20% (up from 5%)
- South America: 10% (new)
- Australia: 10% (new)
```

**Step 3: CDN Architecture Redesign**
```
Current architecture issues:
- Single origin region (us-east-1)
- No regional origins
- Poor cache configuration
- No live streaming optimization

New architecture:

1. Multi-region origins:
   - US: us-east-1 (primary)
   - Europe: eu-west-1
   - Asia: ap-southeast-1
   - South America: sa-east-1
   - Australia: ap-southeast-2

2. Origin shield:
   - Implement CloudFront Origin Shield
   - Reduces origin load by 80%
   - Improves cache hit ratio

3. Cache optimization:
   - Increase TTL from 1 hour to 24 hours
   - Implement cache key normalization
   - Pre-warm cache for popular content
   - Implement stale-while-revalidate

4. Live streaming:
   - Use AWS MediaLive + MediaPackage
   - Implement HLS/DASH adaptive bitrate
   - Use CloudFront for live stream distribution
   - 30-second DVR window

5. 4K streaming:
   - Implement adaptive bitrate streaming
   - Use HEVC codec for better compression
   - Serve 4K only to premium users
   - Fallback to HD for bandwidth constraints
```

**Step 4: Capacity Sizing**
```
Projected capacity requirements:

Data transfer: 135 TB/month
- With 85% cache hit ratio:
  - Cache hits: 114.75 TB/month
  - Origin requests: 20.25 TB/month (15% miss rate)

Bandwidth: 1,350 Gbps peak
- Regional distribution:
  - US: 540 Gbps (40%)
  - Europe: 270 Gbps (20%)
  - Asia: 270 Gbps (20%)
  - South America: 135 Gbps (10%)
  - Australia: 135 Gbps (10%)

Origin capacity:
- S3 buckets in 5 regions
- 100 TB per region (500 TB total)
- 50 Gbps per region (250 Gbps total)
- Origin Shield in each region

Live streaming:
- MediaLive channels: 10 (for redundancy)
- MediaPackage endpoints: 50
- Concurrent viewers: 100,000
- Bitrate: 5 Mbps average
- Total bandwidth: 500 Gbps

Storage:
- Video library: 500 TB
- Replicated across 5 regions: 2.5 PB total
- S3 Intelligent-Tiering for cost optimization
```

**Step 5: Cost Optimization**
```
Current cost: $180,000/month

Projected cost (without optimization):
- Data transfer: 135 TB × $2,000/TB = $270,000
- Origin requests: $52,500 (15% miss rate)
- Regional data transfer: $90,000
- Total: $412,500/month (2.3x increase)

Optimizations:

1. Improved cache hit ratio (65% → 85%):
   - Reduces origin requests by 57%
   - Savings: $22,500/month

2. Origin Shield:
   - Reduces origin load by 80%
   - Savings: $28,000/month
   - Cost: $5,000/month
   - Net savings: $23,000/month

3. Regional origins:
   - Reduces cross-region data transfer by 70%
   - Savings: $63,000/month
   - Cost: $15,000/month (S3 replication)
   - Net savings: $48,000/month

4. S3 Intelligent-Tiering:
   - Moves infrequently accessed content to cheaper tiers
   - Savings: $12,000/month

5. Reserved capacity:
   - Commit to 100 TB/month for 1 year
   - 20% discount on data transfer
   - Savings: $40,000/month

6. Compression:
   - Enable Brotli compression for text assets
   - Reduces transfer by 15% for web assets
   - Savings: $8,000/month

Optimized cost:
- Base cost: $412,500
- Savings: $153,500
- Optimized cost: $259,000/month
- vs. target (30% reduction): $126,000 savings
- Actual reduction: 37% (exceeded target)

Cost per user:
- Current: $18.00/MAU/month
- Projected (unoptimized): $20.63/MAU/month
- Optimized: $12.95/MAU/month (28% reduction)
```

**Step 6: Implementation Plan**
```
Phased rollout (6 months):

Phase 1: Cache optimization (Month 1)
- Increase TTL to 24 hours
- Implement cache key normalization
- Pre-warm cache for top 1,000 videos
- Deploy Origin Shield
- Expected: 75% cache hit ratio
- Cost: $150,000/month (17% reduction)

Phase 2: Regional origins (Month 2-3)
- Deploy S3 buckets in 4 new regions
- Set up S3 replication
- Configure CloudFront origin groups
- Route traffic to nearest origin
- Expected: 80% cache hit ratio
- Cost: $180,000/month (0% change, but 2x capacity)

Phase 3: Live streaming (Month 4)
- Set up MediaLive and MediaPackage
- Configure CloudFront for live streaming
- Test with beta users (10,000 viewers)
- Launch live streaming feature
- Cost: $200,000/month

Phase 4: International expansion (Month 5)
- Launch in 5 new countries
- Monitor performance and costs
- Optimize based on actual usage
- Expected: 85% cache hit ratio
- Cost: $240,000/month

Phase 5: 4K streaming (Month 6)
- Encode library in 4K HEVC
- Launch 4K for premium users
- Monitor bandwidth impact
- Optimize bitrates
- Cost: $259,000/month

Phase 6: Optimization and tuning (Ongoing)
- Continuous monitoring and optimization
- A/B testing of cache strategies
- Cost optimization reviews
- Target: $259,000/month (stabilized)
```

**Step 7: Monitoring and Alerting**
```
Key metrics:

Performance:
- Cache hit ratio: Target 85%, alert if < 80%
- Latency p95: Target < 100ms, alert if > 150ms
- Error rate: Target < 0.1%, alert if > 0.5%
- Bandwidth utilization: Alert at 80% of capacity

Cost:
- Monthly spend: Budget $259,000, alert at $280,000
- Cost per user: Target $12.95, alert if > $15
- Origin requests: Alert if > 20% of traffic
- Regional cost distribution: Monitor for anomalies

Capacity:
- Concurrent viewers: Alert at 900,000 (80% of 1.1M)
- Peak bandwidth: Alert at 1,100 Gbps (80% of 1,350 Gbps)
- Storage utilization: Alert at 80% of capacity

User experience:
- Video start time: Target < 2s, alert if > 3s
- Rebuffering ratio: Target < 1%, alert if > 2%
- Video quality: Monitor bitrate distribution

Dashboards:
- Executive: Cost, users, performance summary
- Operations: Real-time metrics, alerts, capacity
- Engineering: Detailed performance, cache analytics
```

**Results**:
- Successfully expanded to 5 new countries
- User base grew to 22 million MAU (exceeded 20M target)
- Cache hit ratio improved to 87% (exceeded 85% target)
- Latency reduced to < 80ms globally (beat 100ms target)
- Live streaming launched with 150,000 peak concurrent viewers
- 4K streaming adopted by 12% of premium users
- CDN costs reduced to $245,000/month (36% reduction vs. unoptimized)
- Cost per user decreased to $11.14/MAU (38% reduction)
- Zero performance incidents during expansion

**Key Success Factors**:
- Multi-region architecture reduced latency globally
- Cache optimization significantly reduced origin costs
- Origin Shield reduced origin load by 85%
- Reserved capacity commitment provided cost savings
- Phased rollout allowed validation and optimization
- Continuous monitoring enabled proactive optimization

---

### Example 4: Machine Learning Training Infrastructure Capacity Planning

**Context**:
An AI/ML company trains computer vision models for autonomous vehicles. Training jobs run on GPU clusters, with each training run taking 2-7 days and costing $5,000-$50,000. The data science team has grown from 10 to 50 engineers, and the queue time for GPU resources has increased from hours to weeks. The company needs to plan capacity for the next 2 years to support product development timelines.

**Current State**:
- **GPU cluster**: 64 NVIDIA A100 GPUs (8 nodes × 8 GPUs)
- **Utilization**: 95% (severely oversubscribed)
- **Queue time**: 2-3 weeks for large jobs
- **Training jobs**: 200/month (150 small, 40 medium, 10 large)
- **Data storage**: 500 TB (growing 50 TB/month)
- **Current cost**: $120,000/month (GPU instances + storage)
- **Team**: 50 ML engineers, 10 data engineers

**Requirements**:
- Reduce queue time to < 24 hours for all job sizes
- Support 100 ML engineers (2x growth)
- Enable faster experimentation (more small jobs)
- Support larger models (up to 1 trillion parameters)
- Implement spot instance strategy for cost savings
- Plan for 2-year capacity needs
- Maintain or reduce cost per training job

**Capacity Planning Process**:

**Step 1: Workload Characterization**
```
Training job profiles:

Small jobs (75% of jobs):
- Duration: 4-12 hours
- GPUs: 1-8
- Dataset: 1-10 GB
- Examples: Hyperparameter tuning, small model training
- Frequency: 150/month
- GPU-hours: 1,200/month

Medium jobs (20% of jobs):
- Duration: 1-3 days
- GPUs: 8-32
- Dataset: 10-100 GB
- Examples: Full model training, architecture search
- Frequency: 40/month
- GPU-hours: 2,400/month

Large jobs (5% of jobs):
- Duration: 3-7 days
- GPUs: 32-64
- Dataset: 100-500 GB
- Examples: Large model training, production models
- Frequency: 10/month
- GPU-hours: 3,200/month

Total GPU-hours: 6,800/month
Current capacity: 64 GPUs × 720 hours = 46,080 GPU-hours/month
Utilization: 6,800 / 46,080 = 14.8% (actual utilization)

Paradox: Why is utilization 95% but calculated at 14.8%?
Analysis:
- Large jobs monopolize cluster (32-64 GPUs for days)
- Small jobs wait in queue despite low overall utilization
- Poor scheduling and resource allocation
- Fragmentation: Can't fit jobs into available GPUs
```

**Step 2: Demand Forecasting**
```
Projected growth (2 years):

Team growth:
- Current: 50 ML engineers
- Year 1: 75 ML engineers (50% growth)
- Year 2: 100 ML engineers (100% growth)

Job growth:
- Assume 4 jobs/engineer/month
- Current: 200 jobs/month
- Year 1: 300 jobs/month (50% growth)
- Year 2: 400 jobs/month (100% growth)

Job mix shift (more experimentation):
- Small jobs: 75% → 80% (faster iteration)
- Medium jobs: 20% → 15%
- Large jobs: 5% → 5%

Model size growth:
- Current: Up to 100B parameters
- Year 1: Up to 500B parameters
- Year 2: Up to 1T parameters
- Requires more GPUs per job (64 → 128 GPUs)

GPU-hour projections:
- Year 1: 10,200 GPU-hours/month (50% growth)
- Year 2: 15,000 GPU-hours/month (120% growth)
- Large jobs: Up to 128 GPUs × 168 hours = 21,504 GPU-hours/job
```

**Step 3: Capacity Sizing**
```
Target: < 24 hour queue time for all job sizes

Queuing theory analysis:
- For 95% of jobs to start within 24 hours
- Need 40-50% headroom (not 95% utilization)
- Target utilization: 50-60%

Required capacity:
- Year 1: 10,200 GPU-hours / (720 hours × 0.55) = 26 GPUs (minimum)
- Year 2: 15,000 GPU-hours / (720 hours × 0.55) = 38 GPUs (minimum)

But large jobs need 128 GPUs:
- Must have 128 GPUs available for largest jobs
- Plus capacity for concurrent small/medium jobs
- Plus headroom for queue management

Recommended capacity:
- Year 1: 192 GPUs (3x current)
  - 128 GPUs for large jobs
  - 64 GPUs for small/medium jobs
- Year 2: 256 GPUs (4x current)
  - 128 GPUs for large jobs
  - 128 GPUs for small/medium jobs

Cluster configuration:
- Node type: p4d.24xlarge (8× A100 GPUs, 320 GB GPU memory)
- Year 1: 24 nodes (192 GPUs)
- Year 2: 32 nodes (256 GPUs)
```

**Step 4: Cost Optimization Strategy**
```
Current cost: $120,000/month (64 GPUs)
Projected cost (on-demand):
- Year 1: 192 GPUs = $360,000/month (3x)
- Year 2: 256 GPUs = $480,000/month (4x)

Cost optimization strategies:

1. Spot instances for small/medium jobs:
   - 70-80% discount vs. on-demand
   - Acceptable for fault-tolerant jobs
   - Checkpointing every 1 hour
   - Year 1 savings: $120,000/month
   - Year 2 savings: $180,000/month

2. Reserved instances for baseline:
   - 1-year commitment for 64 GPUs (baseline)
   - 40% discount vs. on-demand
   - Savings: $48,000/month

3. Scheduled scaling:
   - Scale down 50% during nights/weekends
   - Savings: $30,000/month

4. Job optimization:
   - Mixed precision training (FP16/BF16)
   - Gradient accumulation for smaller batches
   - Reduce GPU-hours by 20%
   - Savings: $60,000/month

5. Data pipeline optimization:
   - Pre-process data to reduce training time
   - Efficient data loading (DALI, tf.data)
   - Reduce training time by 15%
   - Savings: $45,000/month

Optimized cost:
- Year 1: $360,000 - $120,000 - $48,000 - $30,000 - $60,000 - $45,000 = $57,000 + $120,000 (baseline) = $177,000/month
- Year 2: $480,000 - $180,000 - $48,000 - $30,000 - $60,000 - $45,000 = $117,000 + $120,000 (baseline) = $237,000/month

Cost per training job:
- Current: $120,000 / 200 = $600/job
- Year 1: $177,000 / 300 = $590/job (2% reduction)
- Year 2: $237,000 / 400 = $593/job (1% reduction)
```

**Step 5: Architecture Design**
```
Multi-tier GPU architecture:

Tier 1: On-demand baseline (64 GPUs)
- Always available
- Reserved instances (1-year)
- For critical production training
- Cost: $120,000/month

Tier 2: Spot instances (128 GPUs Year 1, 192 GPUs Year 2)
- 70-80% discount
- For fault-tolerant jobs
- Automatic checkpointing
- Cost: $57,000/month (Year 1), $117,000/month (Year 2)

Tier 3: Burst capacity (on-demand)
- For urgent jobs
- Pay premium for immediate access
- Auto-scaling based on queue depth
- Cost: Variable, budgeted $20,000/month

Scheduling:
- Priority queue for production jobs
- Fair-share scheduling for teams
- Preemption for spot instances
- Backfill scheduling for small jobs

Checkpointing:
- Automatic checkpoints every 1 hour
- Resume from checkpoint on spot interruption
- S3 for checkpoint storage
- Minimal overhead (< 5% training time)

Data pipeline:
- Pre-processed datasets in S3
- FSx for Lustre for high-performance access
- Data caching on local NVMe
- Parallel data loading
```

**Step 6: Implementation Plan**
```
Phased rollout (6 months):

Phase 1: Infrastructure setup (Month 1-2)
- Provision 24 p4d.24xlarge nodes (192 GPUs)
- Set up FSx for Lustre (100 TB)
- Configure VPC and networking
- Set up monitoring and logging
- Cost: $177,000/month

Phase 2: Scheduling and orchestration (Month 3)
- Deploy Kubernetes + Kubeflow
- Configure job scheduling (Volcano, Yunikorn)
- Implement priority queues
- Set up fair-share policies
- Cost: Same

Phase 3: Spot instance integration (Month 4)
- Configure spot instance pools
- Implement checkpointing framework
- Test spot interruption handling
- Migrate fault-tolerant jobs to spot
- Cost: $177,000/month (optimized)

Phase 4: Optimization (Month 5)
- Implement mixed precision training
- Optimize data pipelines
- Enable gradient accumulation
- Tune hyperparameters for efficiency
- Cost: $177,000/month (further optimized)

Phase 5: Scaling and automation (Month 6)
- Implement auto-scaling
- Set up cost monitoring and alerts
- Create self-service portal for users
- Establish capacity review process
- Cost: $177,000/month (stabilized)

Phase 6: Year 2 expansion (Month 12)
- Add 8 more nodes (64 GPUs)
- Total: 32 nodes (256 GPUs)
- Cost: $237,000/month
```

**Step 7: Monitoring and Optimization**
```
Key metrics:

Utilization:
- Target: 50-60% (allows < 24h queue time)
- Alert if > 70% (queue building)
- Alert if < 40% (overprovisioned)

Queue time:
- Target: < 24 hours for all jobs
- Alert if > 48 hours for any job
- Track p50, p95, p99 queue times

Job success rate:
- Target: > 95% (including spot interruptions)
- Track checkpoint/resume success
- Alert if < 90%

Cost:
- Budget: $177,000/month (Year 1), $237,000/month (Year 2)
- Alert at 90% of budget
- Track cost per GPU-hour
- Track cost per training job

Spot instance metrics:
- Interruption rate
- Cost savings vs. on-demand
- Resume success rate

Performance:
- Training throughput (samples/second)
- GPU utilization per job
- Data loading bottlenecks
- Network bandwidth utilization

Dashboards:
- Executive: Cost, utilization, queue time
- ML engineers: Job status, queue position, resource usage
- Platform team: Detailed metrics, capacity planning
```

**Results**:
- Queue time reduced from 2-3 weeks to < 12 hours (exceeded 24h target)
- Team scaled to 110 ML engineers (exceeded 100 target)
- Training jobs increased to 440/month (exceeded 400 target)
- Successfully trained models up to 800B parameters
- Spot instance adoption: 75% of jobs (exceeded 70% target)
- Cost per training job: $580/job (3% reduction)
- GPU utilization: 58% (optimal for queue management)
- Zero production training delays
- Spot interruption recovery: 98% success rate

**Key Success Factors**:
- Multi-tier architecture balanced cost and availability
- Spot instances provided 75% cost savings
- Checkpointing enabled fault-tolerant training
- Optimized scheduling reduced queue times
- Job optimization reduced GPU-hours by 20%
- Continuous monitoring enabled proactive capacity management

## Related Skills

- **Infrastructure Provisioning**: Implements capacity plan by provisioning infrastructure
- **Performance Testing**: Validates capacity through load and stress testing
- **Cost Optimization**: Identifies opportunities to reduce infrastructure costs
- **Monitoring and Observability**: Provides data for capacity planning and tracking
- **Disaster Recovery Planning**: Determines capacity requirements for DR scenarios
- **Auto-Scaling Configuration**: Implements dynamic capacity adjustments
- **Database Optimization**: Optimizes database performance before scaling capacity
- **Architecture Design**: Informs capacity requirements and scalability approaches
- **Cloud Migration**: Estimates capacity needs for cloud migration
- **Incident Response**: Addresses capacity-related incidents and outages

## Skill Composition

### Prerequisites

- **Infrastructure Knowledge**: Understanding of compute, storage, network, and database infrastructure
- **Monitoring Fundamentals**: Ability to collect and analyze performance metrics
- **Basic Statistics**: Understanding of forecasting, trend analysis, and statistical methods
- **Business Acumen**: Understanding of business drivers and growth patterns
- **Cost Awareness**: Knowledge of infrastructure pricing and cost models

### Complementary Skills

- **Data Analysis**: Analyzing historical data and identifying patterns
- **Forecasting**: Projecting future demand using statistical methods
- **Financial Modeling**: Creating cost models and ROI calculations
- **Risk Assessment**: Identifying and mitigating capacity-related risks
- **Project Management**: Planning and executing capacity implementation

### Advanced Combinations

- **Capacity Planning + Auto-Scaling**: Dynamic capacity management based on real-time demand
- **Capacity Planning + FinOps**: Cost-optimized capacity with financial governance
- **Capacity Planning + SRE**: Reliability-focused capacity with SLO/SLA alignment
- **Capacity Planning + ML/AI**: Predictive capacity planning using machine learning
- **Capacity Planning + Sustainability**: Carbon-aware capacity planning and optimization

## Evaluation Criteria

### Planning Quality (25%)

- **Data Completeness**: Sufficient historical data and current state assessment
- **Forecast Accuracy**: Projections align with actual growth and usage patterns
- **Methodology Rigor**: Appropriate forecasting methods and capacity calculations
- **Scenario Coverage**: Multiple scenarios account for uncertainty and variability
- **Stakeholder Alignment**: Requirements and priorities are validated with stakeholders

### Technical Soundness (25%)

- **Capacity Calculations**: Accurate sizing based on workload characteristics
- **Architecture Appropriateness**: Scaling strategy fits application and business needs
- **Performance Validation**: Capacity meets performance and SLA requirements
- **Redundancy**: High availability and disaster recovery capacity is included
- **Scalability**: Plan supports future growth beyond immediate horizon

### Cost Effectiveness (20%)

- **Cost Accuracy**: Realistic cost projections and TCO calculations
- **Optimization**: Cost optimization opportunities are identified and implemented
- **ROI**: Capacity investments have clear business justification
- **Efficiency**: Resources are right-sized and utilized effectively
- **Budget Alignment**: Costs align with budget constraints and expectations

### Risk Management (15%)

- **Risk Identification**: Capacity-related risks are comprehensively identified
- **Mitigation Strategies**: Practical mitigation approaches for key risks
- **Contingency Planning**: Fallback plans for capacity shortfalls or issues
- **Testing**: Capacity plan is validated through testing and simulation
- **Monitoring**: Early warning systems detect capacity issues proactively

### Implementation Success (15%)

- **Execution**: Capacity plan is implemented on time and within budget
- **Validation**: Actual capacity meets projected requirements
- **Documentation**: Comprehensive documentation and knowledge transfer
- **Adoption**: Teams effectively use and manage new capacity
- **Continuous Improvement**: Ongoing monitoring and optimization processes

### Success Metrics

**Capacity Metrics**:
- Headroom: 20-50% capacity headroom maintained
- Utilization: 50-80% average utilization (varies by strategy)
- Queue time: < 24 hours for resource requests (where applicable)
- Availability: Meets or exceeds SLA targets

**Performance Metrics**:
- Response time: Meets p95/p99 latency targets
- Throughput: Supports required requests/transactions per second
- Error rate: < 0.1% capacity-related errors
- Scalability: Handles projected peak load

**Cost Metrics**:
- Cost per user/transaction: Decreases or stays flat with growth
- Budget variance: Within 10% of projected costs
- Optimization savings: 10-30% cost reduction through optimization
- ROI: Positive return on capacity investments

**Business Metrics**:
- Growth support: Infrastructure supports business growth without bottlenecks
- Time to market: Capacity doesn't delay product launches or features
- Customer satisfaction: No capacity-related customer complaints
- Revenue impact: No revenue loss due to capacity constraints
