# Observability Design Skill

## Purpose

Design comprehensive logging, metrics, and distributed tracing systems that provide complete visibility into system behavior, enable rapid incident detection and resolution, and support data-driven operational decisions across distributed architectures.

## When to Use

### Primary Use Cases

1. **New System Architecture**
   - Designing observability for greenfield projects
   - Planning monitoring strategy before implementation
   - Establishing baseline observability requirements
   - Creating observability architecture documents

2. **Distributed Systems**
   - Microservices architectures requiring end-to-end tracing
   - Service mesh implementations needing comprehensive visibility
   - Event-driven systems with complex interaction patterns
   - Multi-region deployments requiring unified observability

3. **Production Readiness**
   - Preparing systems for production deployment
   - Establishing SLI/SLO monitoring frameworks
   - Designing incident detection and alerting systems
   - Creating operational dashboards and runbooks

4. **Performance Optimization**
   - Identifying performance bottlenecks through metrics
   - Analyzing request latency across distributed traces
   - Monitoring resource utilization patterns
   - Tracking application performance indicators

5. **Compliance and Audit**
   - Designing audit logging for regulatory compliance
   - Implementing security event monitoring
   - Creating tamper-evident log systems
   - Establishing data retention and privacy controls

6. **Operational Excellence**
   - Building comprehensive monitoring dashboards
   - Designing alert strategies to reduce noise
   - Creating correlation systems for faster incident resolution
   - Establishing observability best practices

7. **Cost Optimization**
   - Monitoring cloud resource consumption
   - Tracking infrastructure costs by service
   - Identifying optimization opportunities through metrics
   - Implementing cost-aware observability strategies

8. **Capacity Planning**
   - Collecting metrics for growth projections
   - Monitoring resource saturation indicators
   - Tracking usage patterns for scaling decisions
   - Establishing capacity thresholds and alerts

### Specific Scenarios

- Migrating from monolith to microservices
- Implementing chaos engineering practices
- Debugging complex distributed system failures
- Establishing DevOps/SRE practices
- Meeting uptime SLA requirements
- Implementing progressive delivery strategies
- Supporting multi-tenant architectures
- Managing hybrid cloud deployments

## When NOT to Use

### Inappropriate Scenarios

1. **Simple Applications**
   - Single-server applications with minimal complexity
   - Prototype or proof-of-concept projects
   - Internal tools with limited users
   - Static websites without dynamic behavior
   - **Alternative**: Use basic application logging and simple health checks

2. **Resource-Constrained Environments**
   - IoT devices with limited storage/bandwidth
   - Edge computing with intermittent connectivity
   - Embedded systems with strict resource limits
   - **Alternative**: Implement lightweight logging with periodic aggregation

3. **Early Development Stages**
   - Initial prototyping and experimentation
   - Rapid MVP development with frequent changes
   - Exploratory data analysis projects
   - **Alternative**: Use development-mode verbose logging, implement comprehensive observability later

4. **Non-Production Environments**
   - Local development environments
   - Isolated testing environments
   - Temporary demo systems
   - **Alternative**: Use simplified logging and basic metrics

5. **Legacy Systems Without Modification Access**
   - Third-party applications without instrumentation APIs
   - Closed-source systems with limited integration options
   - Systems approaching end-of-life
   - **Alternative**: Implement external monitoring and log scraping where possible

6. **Batch Processing Systems**
   - Offline data processing jobs
   - Scheduled batch operations with deterministic execution
   - ETL pipelines with comprehensive job logs
   - **Alternative**: Use job-level logging and completion metrics

7. **Highly Regulated Environments**
   - Systems where external observability platforms are prohibited
   - Air-gapped networks without internet connectivity
   - Environments with strict data sovereignty requirements
   - **Alternative**: Design on-premises observability solutions

### Warning Signs

- Team lacks expertise in observability tools and practices
- Budget constraints prevent proper tooling investment
- Organization lacks processes for responding to alerts
- System complexity doesn't justify observability overhead
- Data privacy concerns outweigh observability benefits
- Observability implementation would delay critical features

## Inputs

### Required Inputs

1. **System Architecture Documentation**
   - Architecture diagrams (component, deployment, sequence)
   - Service dependency maps
   - Data flow diagrams
   - Infrastructure topology
   - Technology stack specifications

2. **Operational Requirements**
   - SLA/SLO definitions
   - Availability targets (e.g., 99.9%, 99.99%)
   - Performance requirements (latency, throughput)
   - Capacity planning projections
   - Incident response time objectives

3. **Compliance and Security Requirements**
   - Regulatory compliance mandates (GDPR, HIPAA, SOC2, PCI-DSS)
   - Data retention policies
   - Privacy and data masking requirements
   - Audit logging requirements
   - Security event monitoring needs

4. **Business Context**
   - Critical user journeys and workflows
   - Business KPIs and success metrics
   - Revenue-impacting services
   - Customer-facing vs. internal services
   - Peak usage patterns and seasonality

5. **Technical Constraints**
   - Existing observability tools and platforms
   - Budget limitations for tooling and storage
   - Network bandwidth constraints
   - Data residency requirements
   - Integration capabilities and APIs

### Optional Inputs

6. **Historical Data**
   - Past incident reports and postmortems
   - Existing monitoring dashboards
   - Current alert configurations
   - Performance baselines and trends
   - Known pain points and blind spots

7. **Team Context**
   - Team size and expertise levels
   - On-call rotation structure
   - Existing runbooks and procedures
   - Tool preferences and experience
   - Training and documentation needs

8. **Integration Requirements**
   - Incident management platforms (PagerDuty, Opsgenie)
   - ChatOps integrations (Slack, Teams)
   - ITSM systems (ServiceNow, Jira)
   - CI/CD pipelines
   - APM and profiling tools

## Expected Outputs

### Primary Deliverables

1. **Observability Architecture Document**
   - Complete observability strategy and vision
   - Three-pillar design (logs, metrics, traces)
   - Tool selection and justification
   - Data flow and aggregation architecture
   - Storage and retention strategy
   - Cost estimation and optimization plan

2. **Logging Strategy**
   - Log levels and categorization scheme
   - Structured logging format specifications
   - Log aggregation and centralization design
   - Correlation ID and context propagation
   - Sampling and filtering strategies
   - PII masking and security controls
   - Log retention and archival policies

3. **Metrics Framework**
   - Metric taxonomy and naming conventions
   - SLI/SLO definitions and calculations
   - RED metrics (Rate, Errors, Duration) for services
   - USE metrics (Utilization, Saturation, Errors) for resources
   - Business metrics and KPI tracking
   - Custom metric specifications
   - Aggregation and rollup strategies

4. **Distributed Tracing Design**
   - Trace context propagation standards (W3C Trace Context)
   - Span naming and tagging conventions
   - Sampling strategies (head-based, tail-based, adaptive)
   - Service dependency mapping
   - Critical path identification
   - Performance profiling integration

5. **Alerting and Notification Strategy**
   - Alert severity levels and escalation policies
   - Alert routing and notification channels
   - Alert grouping and deduplication rules
   - Runbook automation and response procedures
   - On-call rotation and escalation paths
   - Alert fatigue mitigation strategies

6. **Dashboard and Visualization Design**
   - Executive/business dashboards
   - Service-level operational dashboards
   - Infrastructure monitoring dashboards
   - SLO tracking and error budget visualization
   - Incident response dashboards
   - Capacity planning dashboards

7. **Implementation Roadmap**
   - Phased rollout plan
   - Priority services and components
   - Instrumentation requirements
   - Tool deployment and configuration
   - Team training and onboarding
   - Success metrics and validation criteria

### Supporting Deliverables

8. **Instrumentation Guidelines**
   - Code-level instrumentation patterns
   - Library and SDK recommendations
   - Auto-instrumentation vs. manual instrumentation
   - Performance overhead considerations
   - Testing and validation procedures

9. **Data Governance Framework**
   - Data classification and sensitivity levels
   - Access control and RBAC policies
   - Compliance and audit requirements
   - Data lifecycle management
   - Privacy and security controls

10. **Cost Management Plan**
    - Observability budget allocation
    - Cost per service/team attribution
    - Data volume projections and trends
    - Optimization opportunities
    - Reserved capacity and commitment planning

11. **Runbooks and Procedures**
    - Common troubleshooting workflows
    - Query examples and templates
    - Incident response procedures
    - Escalation and communication protocols
    - Knowledge base and documentation

## Workflow

### Phase 1: Discovery and Requirements (15-20% of effort)

#### Step 1: Understand System Architecture and Context

**Objective**: Build comprehensive understanding of the system, its components, and operational context.

**Activities**:
- Review architecture documentation, diagrams, and technical specifications
- Map all services, components, and dependencies
- Identify synchronous vs. asynchronous communication patterns
- Document data flows and integration points
- Understand deployment topology (regions, availability zones, clusters)
- Identify critical paths and user journeys
- Review technology stack (languages, frameworks, infrastructure)

**Key Questions**:
- What are the system boundaries and external dependencies?
- Which services are stateful vs. stateless?
- What are the communication protocols (HTTP, gRPC, message queues)?
- How does data flow through the system?
- What are the failure modes and cascading failure risks?

**Outputs**:
- Annotated architecture diagrams
- Service dependency graph
- Critical path analysis
- Technology inventory

#### Step 2: Define Observability Objectives and Success Criteria

**Objective**: Establish clear goals, requirements, and constraints for the observability system.

**Activities**:
- Define SLAs and SLOs for critical services
- Identify key business metrics and KPIs
- Establish incident detection and resolution time targets
- Document compliance and regulatory requirements
- Determine data retention and privacy constraints
- Set budget parameters for observability infrastructure
- Define team capabilities and training needs

**Key Questions**:
- What are the availability and performance targets?
- How quickly must we detect and resolve incidents?
- What regulatory compliance requirements apply?
- What is the acceptable cost for observability?
- What are the team's current skills and tool experience?

**Outputs**:
- Observability requirements document
- SLI/SLO definitions
- Compliance requirements matrix
- Budget and resource constraints
- Success metrics and KPIs

#### Step 3: Assess Current State and Identify Gaps

**Objective**: Evaluate existing observability capabilities and identify improvement opportunities.

**Activities**:
- Inventory existing monitoring tools and platforms
- Review current logging practices and coverage
- Assess metric collection and alerting
- Evaluate distributed tracing implementation
- Analyze historical incidents and blind spots
- Identify redundant or unused monitoring
- Document pain points and operational challenges

**Key Questions**:
- What observability tools are currently in use?
- Where do we have visibility gaps or blind spots?
- What incidents were difficult to diagnose and why?
- Are alerts actionable or creating noise?
- Is the current observability cost-effective?

**Outputs**:
- Current state assessment report
- Gap analysis and prioritization
- Tool consolidation opportunities
- Quick wins and immediate improvements

### Phase 2: Design Logging Strategy (20-25% of effort)

#### Step 4: Design Structured Logging Framework

**Objective**: Create comprehensive, consistent, and actionable logging standards.

**Activities**:
- Define log levels and usage guidelines (DEBUG, INFO, WARN, ERROR, FATAL)
- Design structured logging format (JSON, logfmt)
- Establish standard fields (timestamp, service, version, environment, correlation_id)
- Create context propagation strategy (trace_id, span_id, user_id, session_id)
- Define log message templates and patterns
- Specify error logging standards (stack traces, error codes, context)
- Design audit logging for security and compliance

**Key Decisions**:
- Structured format: JSON (machine-readable) vs. logfmt (human-readable)
- Context enrichment: automatic vs. manual field injection
- Sampling strategy: sample all, sample by level, adaptive sampling
- PII handling: masking, redaction, tokenization

**Outputs**:
- Logging standards document
- Structured log schema definitions
- Code examples and templates
- PII masking rules and patterns

#### Step 5: Design Log Aggregation and Storage Architecture

**Objective**: Create scalable, cost-effective log collection and storage infrastructure.

**Activities**:
- Select log aggregation platform (ELK, Splunk, Datadog, CloudWatch, Loki)
- Design log shipping and buffering (Fluentd, Logstash, Vector, agents)
- Plan log indexing and search optimization
- Define retention policies by log type and environment
- Design archival and cold storage strategy
- Plan capacity and scaling requirements
- Estimate costs and optimize data volume

**Key Decisions**:
- Centralized vs. federated log storage
- Push vs. pull log collection
- Hot/warm/cold storage tiers
- Retention periods: operational (7-30 days), compliance (1-7 years)
- Compression and deduplication strategies

**Outputs**:
- Log aggregation architecture diagram
- Tool selection and configuration
- Retention and archival policies
- Capacity planning and cost estimates

#### Step 6: Implement Log Correlation and Context Propagation

**Objective**: Enable tracing requests across distributed services through correlated logs.

**Activities**:
- Design correlation ID generation and propagation
- Implement trace context injection (W3C Trace Context, OpenTelemetry)
- Define context fields for correlation (user_id, session_id, request_id)
- Create log correlation queries and dashboards
- Design cross-service log aggregation views
- Implement baggage propagation for business context

**Key Decisions**:
- Correlation ID format: UUID, snowflake ID, custom
- Context propagation: HTTP headers, message metadata, thread-local storage
- Automatic vs. manual context injection
- Performance overhead tolerance

**Outputs**:
- Context propagation standards
- Correlation ID implementation guide
- Log correlation query examples
- Cross-service tracing dashboards

### Phase 3: Design Metrics and Monitoring (25-30% of effort)

#### Step 7: Define Metrics Taxonomy and SLI/SLO Framework

**Objective**: Establish comprehensive, meaningful metrics that align with business and operational goals.

**Activities**:
- Define SLIs (Service Level Indicators) for each critical service
- Establish SLOs (Service Level Objectives) and error budgets
- Design RED metrics for request-driven services:
  - Rate: requests per second
  - Errors: error rate and count
  - Duration: latency percentiles (p50, p95, p99)
- Design USE metrics for resource monitoring:
  - Utilization: CPU, memory, disk, network
  - Saturation: queue depth, thread pool usage
  - Errors: hardware errors, resource exhaustion
- Define business metrics and KPIs
- Create metric naming conventions and standards
- Design custom metrics for domain-specific monitoring

**Key Decisions**:
- SLO targets: balance ambition with achievability
- Error budget policy: how to spend and track
- Metric cardinality: balance granularity with cost
- Aggregation intervals: real-time vs. periodic

**Outputs**:
- SLI/SLO definitions document
- Metrics catalog and taxonomy
- Naming conventions and standards
- Error budget policy

#### Step 8: Design Metrics Collection and Storage

**Objective**: Create efficient, scalable metrics infrastructure.

**Activities**:
- Select metrics platform (Prometheus, Datadog, CloudWatch, InfluxDB, Grafana Cloud)
- Design metrics collection (push vs. pull, agents, exporters)
- Plan metric aggregation and rollup strategies
- Define retention policies and downsampling
- Design high-cardinality metric handling
- Plan capacity and scaling for metrics storage
- Estimate costs and optimize metric volume

**Key Decisions**:
- Push (StatsD, CloudWatch) vs. Pull (Prometheus) model
- Metric resolution: 1s, 10s, 60s intervals
- Retention: high-resolution (hours/days), aggregated (weeks/months)
- Cardinality management: limit labels, use aggregation

**Outputs**:
- Metrics architecture diagram
- Collection and storage configuration
- Retention and downsampling policies
- Capacity planning and cost estimates

#### Step 9: Design Alerting and Notification Strategy

**Objective**: Create actionable, low-noise alerting that enables rapid incident response.

**Activities**:
- Define alert severity levels (P0/Critical, P1/High, P2/Medium, P3/Low)
- Create alerting rules for SLO violations and error budgets
- Design symptom-based alerts (user impact) vs. cause-based alerts
- Implement alert aggregation and deduplication
- Define notification channels and routing (PagerDuty, Slack, email)
- Create escalation policies and on-call schedules
- Design alert enrichment with context and runbooks
- Implement alert fatigue mitigation (snoozing, maintenance windows)

**Key Decisions**:
- Alert on symptoms (latency, errors) vs. causes (CPU, memory)
- Threshold-based vs. anomaly-based alerting
- Alert grouping: by service, severity, or incident
- Notification preferences: push (page) vs. pull (dashboard)

**Outputs**:
- Alerting strategy document
- Alert definitions and thresholds
- Escalation policies and runbooks
- Notification routing configuration

### Phase 4: Design Distributed Tracing (20-25% of effort)

#### Step 10: Design Distributed Tracing Architecture

**Objective**: Enable end-to-end request tracing across distributed services.

**Activities**:
- Select tracing platform (Jaeger, Zipkin, AWS X-Ray, Datadog APM, Honeycomb)
- Design trace context propagation (W3C Trace Context, B3, custom)
- Define span naming and tagging conventions
- Design sampling strategy (always, probabilistic, adaptive, tail-based)
- Plan trace storage and retention
- Design service dependency mapping
- Create trace analysis and visualization dashboards

**Key Decisions**:
- Sampling strategy: balance coverage with cost
  - Head-based: decide at trace start (simple, but may miss interesting traces)
  - Tail-based: decide after completion (captures errors, but complex)
  - Adaptive: adjust based on traffic and error rates
- Instrumentation approach: auto-instrumentation vs. manual
- Trace storage: hot (recent traces) vs. cold (historical analysis)

**Outputs**:
- Distributed tracing architecture diagram
- Trace context propagation standards
- Sampling strategy and configuration
- Span naming and tagging conventions

#### Step 11: Define Instrumentation Standards and Guidelines

**Objective**: Ensure consistent, comprehensive instrumentation across all services.

**Activities**:
- Define instrumentation libraries and SDKs (OpenTelemetry, vendor SDKs)
- Create code-level instrumentation patterns and examples
- Design automatic instrumentation for frameworks and libraries
- Define custom span creation guidelines
- Specify span attributes and tags (http.method, http.status_code, db.statement)
- Design error and exception capture
- Create instrumentation testing and validation procedures
- Document performance overhead and optimization

**Key Decisions**:
- OpenTelemetry (vendor-neutral) vs. vendor-specific SDKs
- Auto-instrumentation (easy, less control) vs. manual (flexible, more work)
- Instrumentation depth: external calls only vs. internal functions
- Attribute cardinality: balance detail with storage cost

**Outputs**:
- Instrumentation guidelines document
- Code examples and templates
- Library and SDK recommendations
- Performance testing results

### Phase 5: Implementation Planning and Enablement (10-15% of effort)

#### Step 12: Create Dashboards and Visualization Strategy

**Objective**: Design intuitive, actionable dashboards for different audiences and use cases.

**Activities**:
- Design executive/business dashboards (SLO compliance, error budgets, KPIs)
- Create service-level operational dashboards (RED metrics, dependencies)
- Build infrastructure monitoring dashboards (USE metrics, capacity)
- Design incident response dashboards (recent errors, anomalies, traces)
- Create capacity planning and trend analysis dashboards
- Design custom dashboards for specific teams or services
- Implement dashboard templates and standards

**Key Decisions**:
- Dashboard granularity: overview vs. detailed drill-down
- Refresh intervals: real-time vs. periodic
- Visualization types: time series, heatmaps, tables, gauges
- Dashboard organization: by team, service, or function

**Outputs**:
- Dashboard catalog and templates
- Visualization standards and guidelines
- Dashboard access and permissions

#### Step 13: Develop Implementation Roadmap and Rollout Plan

**Objective**: Create phased, risk-managed implementation plan.

**Activities**:
- Prioritize services and components for instrumentation
- Define implementation phases and milestones
- Plan infrastructure deployment and configuration
- Create instrumentation and migration guides
- Design validation and testing procedures
- Plan team training and onboarding
- Define success metrics and acceptance criteria
- Create rollback and contingency plans

**Key Decisions**:
- Rollout strategy: big bang vs. incremental
- Prioritization: critical services first vs. easiest first
- Parallel running: new observability alongside existing
- Migration timeline: aggressive vs. conservative

**Outputs**:
- Implementation roadmap with timeline
- Service prioritization and phasing
- Infrastructure deployment plan
- Training and enablement materials
- Success metrics and validation criteria

#### Step 14: Establish Governance and Continuous Improvement

**Objective**: Ensure observability system remains effective, efficient, and aligned with evolving needs.

**Activities**:
- Define observability ownership and responsibilities
- Create observability review and optimization processes
- Establish cost monitoring and optimization practices
- Design feedback loops from incidents to observability improvements
- Plan regular reviews of SLIs, SLOs, and alerts
- Create knowledge sharing and best practices documentation
- Define metrics for observability effectiveness

**Key Decisions**:
- Centralized vs. federated observability ownership
- Review cadence: weekly, monthly, quarterly
- Cost optimization targets and triggers
- Continuous improvement processes

**Outputs**:
- Governance model and RACI matrix
- Review and optimization procedures
- Cost management and optimization plan
- Continuous improvement framework

## Decision Framework

### Tool Selection Matrix

#### Logging Platforms

| Tool | Best For | Strengths | Limitations | Cost Model |
|------|----------|-----------|-------------|------------|
| **ELK Stack** | Self-hosted, customizable | Open source, flexible, powerful search | Complex setup, resource-intensive | Infrastructure + management |
| **Splunk** | Enterprise, compliance | Mature, powerful analytics, broad integrations | Expensive, complex licensing | Data volume |
| **Datadog Logs** | Cloud-native, integrated observability | Easy setup, unified platform, good UX | Can be expensive at scale | Data ingestion + retention |
| **CloudWatch Logs** | AWS-native workloads | Native AWS integration, simple setup | Limited search, AWS-only | Data ingestion + storage |
| **Grafana Loki** | Cost-effective, Kubernetes | Low cost, Prometheus-like, efficient | Limited search capabilities | Infrastructure |
| **New Relic Logs** | APM integration | Unified platform, good correlation | Can be expensive | Data ingestion |

#### Metrics Platforms

| Tool | Best For | Strengths | Limitations | Cost Model |
|------|----------|-----------|-------------|------------|
| **Prometheus** | Kubernetes, self-hosted | Open source, powerful, pull-based | Scaling challenges, limited long-term storage | Infrastructure |
| **Datadog** | Cloud-native, full-stack | Comprehensive, easy setup, great UX | Expensive at scale | Hosts + custom metrics |
| **CloudWatch** | AWS workloads | Native AWS integration, simple | Limited functionality, AWS-only | Metrics + API calls |
| **InfluxDB** | Time-series, IoT | Purpose-built, efficient, SQL-like | Clustering complexity | Infrastructure or cloud |
| **Grafana Cloud** | Managed Prometheus | Managed, scalable, affordable | Less mature than competitors | Metrics + samples |
| **New Relic** | APM, full-stack | Comprehensive, good analytics | Can be expensive | Data ingestion |

#### Distributed Tracing Platforms

| Tool | Best For | Strengths | Limitations | Cost Model |
|------|----------|-----------|-------------|------------|
| **Jaeger** | Self-hosted, Kubernetes | Open source, mature, scalable | Requires infrastructure management | Infrastructure |
| **Zipkin** | Simple setups, legacy | Open source, simple, widely supported | Less feature-rich | Infrastructure |
| **AWS X-Ray** | AWS workloads | Native AWS integration, easy setup | AWS-only, limited features | Traces + scans |
| **Datadog APM** | Unified observability | Integrated, automatic instrumentation | Expensive at scale | Hosts + spans |
| **Honeycomb** | Complex debugging, high-cardinality | Powerful querying, high-cardinality support | Expensive, learning curve | Events |
| **New Relic APM** | Application monitoring | Mature, comprehensive, good UX | Can be expensive | Data ingestion |
| **Lightstep** | Large-scale, complex systems | Advanced sampling, powerful analysis | Expensive, complex | Spans |

### Architecture Decision Framework

#### Push vs. Pull Metrics Collection

**Use Pull (Prometheus-style) when**:
- Running Kubernetes or containerized workloads
- Need service discovery and dynamic targets
- Want to avoid agent configuration on every service
- Can expose metrics endpoints from services
- Need to scrape metrics on-demand for debugging

**Use Push (StatsD/CloudWatch-style) when**:
- Running short-lived jobs or serverless functions
- Services are behind firewalls or NAT
- Need to send metrics from batch jobs
- Want to decouple metric generation from collection
- Running in environments where pull is impractical

#### Centralized vs. Federated Observability

**Use Centralized when**:
- Single team or organization
- Unified tooling and standards desired
- Easier to manage and maintain
- Cost optimization through consolidation
- Consistent access control and governance

**Use Federated when**:
- Multiple teams with different needs
- Different compliance or data residency requirements
- Teams want autonomy in tool selection
- Very large scale requiring regional deployment
- Acquisitions or mergers with existing systems

#### Sampling Strategy Selection

**Use Head-Based Sampling when**:
- Predictable traffic patterns
- Cost is primary concern
- Simple implementation required
- Acceptable to miss some interesting traces
- Example: Sample 1% of all requests

**Use Tail-Based Sampling when**:
- Need to capture all errors and slow requests
- Can tolerate additional complexity and cost
- Want to sample based on trace characteristics
- Example: Keep all errors, 10% of slow requests, 1% of normal requests

**Use Adaptive Sampling when**:
- Traffic patterns vary significantly
- Want to optimize cost while maintaining coverage
- Can implement dynamic sampling logic
- Example: Increase sampling during incidents, decrease during normal operation

### Cost Optimization Decision Tree

```
Is observability cost >5% of infrastructure cost?
├─ No → Continue current approach, monitor trends
└─ Yes → Investigate optimization opportunities
    ├─ High log volume?
    │   ├─ Implement sampling (keep errors, sample info/debug)
    │   ├─ Reduce log retention for non-critical logs
    │   ├─ Filter noisy or low-value logs
    │   └─ Use cheaper storage tiers for older logs
    ├─ High metric cardinality?
    │   ├─ Reduce label cardinality (aggregate, drop unused labels)
    │   ├─ Implement metric relabeling and dropping
    │   ├─ Use recording rules for expensive queries
    │   └─ Archive or drop unused metrics
    ├─ High trace volume?
    │   ├─ Implement intelligent sampling (tail-based, adaptive)
    │   ├─ Reduce trace retention period
    │   ├─ Sample less critical services more aggressively
    │   └─ Use cheaper storage for older traces
    └─ Inefficient tooling?
        ├─ Consolidate redundant tools
        ├─ Negotiate volume discounts or reserved capacity
        ├─ Consider open-source alternatives
        └─ Optimize data pipeline (compression, deduplication)
```

### Compliance and Security Decision Framework

#### Data Retention Decisions

**Regulatory Requirements**:
- GDPR: Right to deletion, minimize retention
- HIPAA: 6 years minimum for audit logs
- SOC2: 1 year minimum, demonstrate controls
- PCI-DSS: 1 year online, 3 months immediately available
- Financial Services: 7 years for transaction logs

**Operational Needs**:
- Hot storage (fast search): 7-30 days
- Warm storage (slower search): 30-90 days
- Cold storage (archive): 90 days - 7 years
- Real-time (streaming): 1-24 hours

#### PII and Sensitive Data Handling

**Masking Strategies**:
- **Redaction**: Replace with fixed string (e.g., `[REDACTED]`)
- **Hashing**: One-way hash for correlation without exposure
- **Tokenization**: Replace with token, store mapping securely
- **Partial Masking**: Show first/last characters (e.g., `****1234`)
- **Encryption**: Encrypt at rest and in transit

**Decision Matrix**:
- Credit card numbers → Tokenization or partial masking
- Email addresses → Hashing for correlation, redaction for display
- IP addresses → Hashing or partial masking (depends on jurisdiction)
- User IDs → Hashing or pseudonymization
- Health information → Encryption and strict access control

## Quality Checklist

### Design Quality

- [ ] **Comprehensive Coverage**: All critical services and components have observability
- [ ] **Three Pillars**: Logs, metrics, and traces are all addressed
- [ ] **Correlation**: Logs, metrics, and traces can be correlated across services
- [ ] **SLI/SLO Alignment**: Observability supports defined SLIs and SLOs
- [ ] **Business Alignment**: Metrics and dashboards reflect business KPIs
- [ ] **Scalability**: Design can handle projected growth (10x, 100x)
- [ ] **Cost-Effectiveness**: Observability cost is <5% of infrastructure cost
- [ ] **Compliance**: Meets all regulatory and security requirements

### Logging Quality

- [ ] **Structured Format**: All logs use consistent structured format (JSON)
- [ ] **Standard Fields**: All logs include timestamp, service, version, environment, correlation_id
- [ ] **Appropriate Levels**: Log levels are used correctly (DEBUG, INFO, WARN, ERROR, FATAL)
- [ ] **Contextual Information**: Logs include sufficient context for debugging
- [ ] **PII Protection**: Sensitive data is masked or redacted
- [ ] **Correlation IDs**: Request/trace IDs propagate across all services
- [ ] **Error Details**: Errors include stack traces, error codes, and context
- [ ] **Sampling Strategy**: High-volume logs are sampled appropriately
- [ ] **Retention Policy**: Retention aligns with compliance and operational needs

### Metrics Quality

- [ ] **SLI Coverage**: All defined SLIs have corresponding metrics
- [ ] **RED Metrics**: All request-driven services have Rate, Errors, Duration metrics
- [ ] **USE Metrics**: All resources have Utilization, Saturation, Errors metrics
- [ ] **Naming Conventions**: Metrics follow consistent naming standards
- [ ] **Appropriate Cardinality**: Label cardinality is managed and bounded
- [ ] **Business Metrics**: Key business KPIs are tracked
- [ ] **Aggregation Strategy**: Metrics are aggregated appropriately (avg, sum, percentiles)
- [ ] **Retention Policy**: Retention balances cost with analytical needs

### Tracing Quality

- [ ] **End-to-End Coverage**: Critical paths are traced across all services
- [ ] **Context Propagation**: Trace context propagates correctly across all boundaries
- [ ] **Span Naming**: Spans follow consistent naming conventions
- [ ] **Span Attributes**: Spans include relevant tags and attributes
- [ ] **Sampling Strategy**: Sampling balances cost with coverage
- [ ] **Error Capture**: Errors and exceptions are captured in spans
- [ ] **Performance Overhead**: Instrumentation overhead is <5% of request latency
- [ ] **Service Dependencies**: Service dependency map is accurate and complete

### Alerting Quality

- [ ] **Symptom-Based**: Alerts focus on user impact, not just causes
- [ ] **Actionable**: Every alert has a clear action or runbook
- [ ] **Appropriate Severity**: Alert severity matches actual impact
- [ ] **Low Noise**: Alert-to-incident ratio is >50% (most alerts are real)
- [ ] **Escalation Policy**: Clear escalation paths and on-call rotation
- [ ] **Context-Rich**: Alerts include relevant context and links
- [ ] **Deduplication**: Similar alerts are grouped and deduplicated
- [ ] **SLO-Based**: Critical alerts are based on SLO violations

### Dashboard Quality

- [ ] **Audience-Appropriate**: Dashboards are designed for specific audiences
- [ ] **Actionable Insights**: Dashboards enable decision-making and action
- [ ] **Consistent Layout**: Dashboards follow consistent design patterns
- [ ] **Performance**: Dashboards load quickly (<3 seconds)
- [ ] **Drill-Down**: Dashboards enable drilling into details
- [ ] **Context**: Dashboards include relevant context and documentation
- [ ] **SLO Visibility**: SLO compliance and error budgets are visible

### Implementation Quality

- [ ] **Instrumentation Standards**: Code follows instrumentation guidelines
- [ ] **Testing**: Observability instrumentation is tested
- [ ] **Documentation**: Comprehensive documentation and runbooks exist
- [ ] **Training**: Team is trained on observability tools and practices
- [ ] **Automation**: Instrumentation and configuration are automated where possible
- [ ] **Rollout Plan**: Phased rollout with validation at each stage
- [ ] **Rollback Plan**: Clear rollback procedures exist
- [ ] **Success Metrics**: Implementation success is measurable and tracked

### Operational Quality

- [ ] **Ownership**: Clear ownership and responsibilities defined
- [ ] **Runbooks**: Runbooks exist for common scenarios
- [ ] **Incident Integration**: Observability is integrated into incident response
- [ ] **Continuous Improvement**: Regular reviews and optimization occur
- [ ] **Cost Monitoring**: Observability costs are tracked and optimized
- [ ] **Feedback Loops**: Incidents drive observability improvements
- [ ] **Knowledge Sharing**: Best practices are documented and shared

## Common Mistakes

### Design Mistakes

1. **Logging Everything Without Strategy**
   - **Mistake**: Enabling verbose logging everywhere without filtering or sampling
   - **Impact**: Excessive costs, noise, difficulty finding relevant information
   - **Solution**: Implement log levels, sampling, and filtering; log with purpose

2. **Ignoring Cardinality in Metrics**
   - **Mistake**: Creating metrics with unbounded labels (user_id, request_id)
   - **Impact**: Metric explosion, high costs, performance degradation
   - **Solution**: Limit label cardinality, use aggregation, avoid high-cardinality labels

3. **Alert on Everything**
   - **Mistake**: Creating alerts for every possible condition
   - **Impact**: Alert fatigue, ignored alerts, missed critical issues
   - **Solution**: Alert on symptoms (user impact), not causes; ensure alerts are actionable

4. **No Correlation Between Pillars**
   - **Mistake**: Logs, metrics, and traces exist in silos
   - **Impact**: Difficult to correlate issues, slow debugging, incomplete picture
   - **Solution**: Implement correlation IDs, link logs/metrics/traces, unified dashboards

5. **Ignoring Cost from the Start**
   - **Mistake**: Implementing observability without considering cost implications
   - **Impact**: Runaway costs, forced to reduce coverage, budget conflicts
   - **Solution**: Design with cost in mind, implement sampling, monitor and optimize

6. **Over-Engineering for Simple Systems**
   - **Mistake**: Implementing complex distributed tracing for a monolith
   - **Impact**: Wasted effort, unnecessary complexity, poor ROI
   - **Solution**: Match observability complexity to system complexity

7. **Under-Engineering for Complex Systems**
   - **Mistake**: Using basic logging for complex microservices
   - **Impact**: Blind spots, slow incident resolution, poor reliability
   - **Solution**: Invest in comprehensive observability for distributed systems

### Implementation Mistakes

8. **Inconsistent Instrumentation**
   - **Mistake**: Each service implements observability differently
   - **Impact**: Difficult to correlate, inconsistent quality, maintenance burden
   - **Solution**: Create and enforce instrumentation standards, use shared libraries

9. **No Testing of Observability**
   - **Mistake**: Not testing that logs, metrics, and traces work correctly
   - **Impact**: Blind spots discovered during incidents, missing critical data
   - **Solution**: Test observability instrumentation, validate in staging

10. **Blocking on Observability Calls**
    - **Mistake**: Synchronous calls to observability systems that can fail or be slow
    - **Impact**: Application performance degradation, cascading failures
    - **Solution**: Use async logging, buffering, circuit breakers for observability

11. **Logging Sensitive Data**
    - **Mistake**: Logging PII, credentials, or sensitive data without masking
    - **Impact**: Compliance violations, security risks, data breaches
    - **Solution**: Implement automatic PII detection and masking, audit logs

12. **No Sampling Strategy**
    - **Mistake**: Collecting 100% of high-volume logs or traces
    - **Impact**: Excessive costs, storage issues, diminishing returns
    - **Solution**: Implement intelligent sampling (keep errors, sample normal traffic)

### Operational Mistakes

13. **No Runbooks for Alerts**
    - **Mistake**: Alerts without clear action items or investigation steps
    - **Impact**: Slow incident response, inconsistent handling, stress
    - **Solution**: Every alert must have a runbook or clear action

14. **Ignoring Alert Fatigue**
    - **Mistake**: Allowing noisy or non-actionable alerts to persist
    - **Impact**: Alerts ignored, critical issues missed, team burnout
    - **Solution**: Regularly review and tune alerts, remove or fix noisy alerts

15. **No Ownership or Governance**
    - **Mistake**: No clear ownership of observability systems and practices
    - **Impact**: Degradation over time, inconsistent practices, technical debt
    - **Solution**: Assign ownership, establish governance, regular reviews

16. **Not Learning from Incidents**
    - **Mistake**: Not using incidents to improve observability
    - **Impact**: Same blind spots persist, repeated issues, slow improvement
    - **Solution**: Postmortems should identify and address observability gaps

17. **Dashboard Sprawl**
    - **Mistake**: Creating many dashboards without organization or maintenance
    - **Impact**: Difficulty finding relevant dashboards, outdated information
    - **Solution**: Organize dashboards, deprecate unused ones, establish standards

### Architecture Mistakes

18. **Single Point of Failure**
    - **Mistake**: Observability system itself is not reliable or redundant
    - **Impact**: Blind during outages when observability is most needed
    - **Solution**: Make observability infrastructure highly available and resilient

19. **No Data Retention Strategy**
    - **Mistake**: Keeping all data forever or deleting too aggressively
    - **Impact**: High costs or inability to investigate historical issues
    - **Solution**: Tiered retention based on data type, compliance, and value

20. **Vendor Lock-In**
    - **Mistake**: Tightly coupling to proprietary vendor features
    - **Impact**: Difficult to migrate, pricing leverage, limited flexibility
    - **Solution**: Use open standards (OpenTelemetry), design for portability

21. **Ignoring Network and Bandwidth**
    - **Mistake**: Not considering network overhead of observability data
    - **Impact**: Network saturation, increased latency, bandwidth costs
    - **Solution**: Local aggregation, compression, sampling, efficient protocols

22. **No Capacity Planning for Observability**
    - **Mistake**: Not planning for observability system growth and scaling
    - **Impact**: Performance degradation, data loss, emergency scaling
    - **Solution**: Monitor observability system itself, plan for growth

## Examples

### Example 1: E-Commerce Platform Observability Design

#### Context

**System**: Multi-tenant e-commerce platform serving 10,000+ merchants

**Architecture**:
- Microservices architecture (30+ services)
- Technologies: Node.js, Python, Go, React
- Infrastructure: Kubernetes on AWS (EKS)
- Traffic: 50,000 requests/second peak, 5M daily active users
- Regions: US-East, US-West, EU-West

**Requirements**:
- SLO: 99.95% availability, p95 latency <200ms
- Compliance: PCI-DSS for payment processing, GDPR for EU customers
- Business KPIs: Conversion rate, cart abandonment, revenue per user
- Budget: $50,000/month for observability

#### Observability Design

**Logging Strategy**:

1. **Structured Logging Format** (JSON):
```json
{
  "timestamp": "2026-09-09T10:30:45.123Z",
  "level": "INFO",
  "service": "checkout-service",
  "version": "2.3.1",
  "environment": "production",
  "region": "us-east-1",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "merchant_id": "merchant_12345",
  "user_id": "hash_abc123",
  "message": "Order created successfully",
  "order_id": "order_789",
  "order_total": 129.99,
  "payment_method": "credit_card",
  "duration_ms": 245
}
```

2. **Log Levels and Sampling**:
   - ERROR/FATAL: 100% (all errors logged)
   - WARN: 100% (all warnings logged)
   - INFO: 10% sampling for high-volume services (search, product catalog)
   - DEBUG: Disabled in production (enable dynamically for troubleshooting)

3. **PII Masking**:
   - Credit card numbers: Tokenized, only last 4 digits logged
   - Email addresses: Hashed for correlation
   - IP addresses: Partial masking (e.g., `192.168.xxx.xxx`)
   - User IDs: Hashed consistently for correlation

4. **Log Aggregation**:
   - Platform: Datadog Logs (chosen for unified observability)
   - Collection: Datadog agent on each Kubernetes node
   - Retention:
     - Hot (searchable): 15 days
     - Archive (S3): 1 year for operational, 7 years for PCI-DSS audit logs
   - Cost optimization: Index only ERROR/WARN and sampled INFO, archive all

**Metrics Strategy**:

1. **SLI Definitions**:
   - **Availability SLI**: Percentage of successful requests (HTTP 2xx/3xx)
     - Target: 99.95% (21.6 minutes downtime/month)
   - **Latency SLI**: p95 response time <200ms
   - **Error Rate SLI**: <0.1% of requests result in errors

2. **RED Metrics** (per service):
   - **Rate**: `http_requests_total` (counter)
   - **Errors**: `http_requests_errors_total` (counter)
   - **Duration**: `http_request_duration_seconds` (histogram, p50/p95/p99)

3. **Business Metrics**:
   - `checkout_conversions_total`: Completed checkouts
   - `cart_abandonment_rate`: Percentage of carts not converted
   - `revenue_total`: Total revenue (by merchant, region)
   - `active_users`: Currently active users

4. **Infrastructure Metrics** (USE):
   - **Utilization**: CPU, memory, disk, network per node/pod
   - **Saturation**: Request queue depth, thread pool usage
   - **Errors**: Pod restarts, OOMKills, node failures

5. **Metrics Platform**:
   - Platform: Datadog Metrics (unified with logs and APM)
   - Collection: Datadog agent with Kubernetes integration
   - Cardinality management:
     - Limit labels to: service, environment, region, status_code, method
     - Avoid: user_id, merchant_id, order_id in labels
   - Retention:
     - Full resolution (10s): 15 days
     - Rollup (1 hour): 15 months

**Distributed Tracing Strategy**:

1. **Tracing Platform**: Datadog APM

2. **Instrumentation**:
   - Auto-instrumentation: Express.js, Flask, gRPC, AWS SDK
   - Manual instrumentation: Custom business logic, external API calls
   - Libraries: OpenTelemetry with Datadog exporter (vendor portability)

3. **Trace Context Propagation**:
   - Standard: W3C Trace Context (traceparent header)
   - Propagation across: HTTP, gRPC, Kafka, SQS

4. **Sampling Strategy**:
   - **Adaptive sampling** based on traffic and errors:
     - Errors and slow requests (>1s): 100%
     - Normal requests: 10% during normal traffic, 1% during peak
     - Per-merchant sampling: 100% for new merchants (first 30 days)
   - Expected trace volume: ~5M traces/day (~10% of requests)

5. **Span Naming and Tagging**:
   - Span names: `{service}.{operation}` (e.g., `checkout-service.create_order`)
   - Standard tags:
     - `http.method`, `http.status_code`, `http.url`
     - `db.type`, `db.statement` (sanitized, no PII)
     - `merchant.id`, `order.id` (for business context)
     - `error`, `error.message`, `error.stack` (for errors)

**Alerting Strategy**:

1. **SLO-Based Alerts** (P0 - Critical):
   - **Availability**: Error rate >0.05% for 5 minutes (50% of error budget)
   - **Latency**: p95 >200ms for 5 minutes
   - **Error Budget**: <10% error budget remaining
   - Action: Page on-call engineer immediately

2. **Service Health Alerts** (P1 - High):
   - Service error rate >1% for 5 minutes
   - Service p99 latency >1s for 5 minutes
   - Service dependency failure (payment gateway, inventory)
   - Action: Notify on-call engineer via Slack, escalate if not acknowledged in 15 minutes

3. **Infrastructure Alerts** (P2 - Medium):
   - Pod restart rate >5/hour
   - Node CPU/memory >80% for 15 minutes
   - Disk usage >85%
   - Action: Notify infrastructure team via Slack

4. **Business Alerts** (P1 - High):
   - Conversion rate drops >20% compared to baseline
   - Payment processing failures >5% for 10 minutes
   - Action: Notify on-call engineer and business stakeholders

5. **Alert Routing**:
   - P0: PagerDuty → On-call engineer phone/SMS
   - P1: PagerDuty → On-call engineer push notification + Slack #incidents
   - P2: Slack #alerts-infrastructure
   - Escalation: P0/P1 not acknowledged in 15 minutes → Escalate to senior engineer

**Dashboard Design**:

1. **Executive Dashboard**:
   - SLO compliance and error budget (current month)
   - Availability and latency trends (30 days)
   - Business KPIs: Revenue, conversion rate, active users
   - Incident count and MTTR (mean time to resolution)

2. **Service Dashboard** (per service):
   - RED metrics: Request rate, error rate, latency (p50/p95/p99)
   - Service dependencies and health
   - Recent errors and traces
   - Resource utilization (CPU, memory)

3. **Checkout Flow Dashboard**:
   - Funnel visualization: Cart → Checkout → Payment → Confirmation
   - Conversion rate and abandonment rate
   - Payment method breakdown
   - Error rate by checkout step

4. **Infrastructure Dashboard**:
   - Kubernetes cluster health (nodes, pods)
   - Resource utilization (CPU, memory, disk, network)
   - Pod restarts and failures
   - Capacity and saturation metrics

5. **Incident Response Dashboard**:
   - Recent errors (last 1 hour)
   - Anomalies and spikes
   - Service dependency map with health status
   - Quick links to logs, traces, and runbooks

**Implementation Roadmap**:

**Phase 1 (Weeks 1-2): Foundation**
- Deploy Datadog agents to Kubernetes clusters
- Implement structured logging in top 5 critical services
- Enable auto-instrumentation for APM
- Create basic RED metrics dashboards
- Set up initial SLO-based alerts

**Phase 2 (Weeks 3-4): Expansion**
- Roll out structured logging to all services
- Implement custom instrumentation for business logic
- Create business metrics and dashboards
- Implement PII masking and compliance controls
- Set up log archival to S3

**Phase 3 (Weeks 5-6): Optimization**
- Implement adaptive trace sampling
- Optimize log sampling and filtering
- Create comprehensive runbooks for alerts
- Train team on observability tools and practices
- Establish observability review process

**Phase 4 (Weeks 7-8): Advanced Features**
- Implement tail-based sampling for traces
- Create advanced correlation dashboards
- Set up anomaly detection for business metrics
- Implement cost monitoring and optimization
- Document best practices and standards

**Cost Estimation**:

- Datadog Logs: 500GB/day × $0.10/GB = $15,000/month
- Datadog APM: 100 hosts × $31/host + 5M spans/day × $1.27/M = $9,435/month
- Datadog Infrastructure: 100 hosts × $15/host = $1,500/month
- Datadog Custom Metrics: 500 custom metrics × $5/metric = $2,500/month
- S3 Archive Storage: 15TB × $0.023/GB = $345/month
- **Total**: ~$28,780/month (within $50,000 budget, room for growth)

**Success Metrics**:

- MTTD (Mean Time to Detect): <5 minutes for critical issues
- MTTR (Mean Time to Resolve): <30 minutes for P0 incidents
- SLO compliance: >99.95% availability
- Alert quality: >70% of alerts result in action (low false positive rate)
- Observability cost: <3% of infrastructure cost

---

### Example 2: Banking Platform Observability Design

#### Context

**System**: Core banking platform for regional bank

**Architecture**:
- Hybrid architecture: Legacy mainframe + modern microservices
- Technologies: Java (Spring Boot), .NET, COBOL (mainframe)
- Infrastructure: On-premises data centers + AWS (hybrid cloud)
- Traffic: 10,000 requests/second peak, 2M customers
- Criticality: Tier 1 financial services (extremely high reliability required)

**Requirements**:
- SLO: 99.99% availability (52 minutes downtime/year), p99 latency <500ms
- Compliance: SOC2, PCI-DSS, financial regulations (audit logs 7 years)
- Security: Strict access control, tamper-evident logs, encryption
- Data Residency: All data must remain in specific geographic regions
- Budget: $100,000/month for observability

#### Observability Design

**Logging Strategy**:

1. **Structured Logging Format** (JSON for microservices, structured text for mainframe):

```json
{
  "timestamp": "2026-09-09T10:30:45.123Z",
  "level": "INFO",
  "service": "transaction-service",
  "version": "3.1.2",
  "environment": "production",
  "datacenter": "dc-east-1",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "customer_id": "hash_customer_456",
  "account_id": "hash_account_789",
  "transaction_id": "txn_abc123",
  "transaction_type": "transfer",
  "amount": "***REDACTED***",
  "message": "Transaction processed successfully",
  "duration_ms": 342,
  "audit": true
}
```

2. **Audit Logging**:
   - **Scope**: All financial transactions, authentication, authorization, data access
   - **Format**: Tamper-evident (cryptographic signatures, append-only)
   - **Retention**: 7 years (regulatory requirement)
   - **Storage**: Dedicated audit log system (WORM storage)
   - **Access Control**: Strict RBAC, all access logged

3. **Security Event Logging**:
   - Failed authentication attempts
   - Authorization failures
   - Unusual access patterns
   - Configuration changes
   - Privilege escalations
   - Integration with SIEM (Splunk Enterprise Security)

4. **PII and Sensitive Data Protection**:
   - Account numbers: Hashed with consistent salt for correlation
   - Transaction amounts: Redacted in operational logs, preserved in audit logs
   - Customer names: Redacted, only customer_id (hashed) logged
   - SSN, credit cards: Never logged
   - Automatic scanning for accidental PII exposure

5. **Log Aggregation**:
   - Platform: Splunk Enterprise (on-premises for data residency)
   - Collection:
     - Microservices: Splunk Universal Forwarder
     - Mainframe: Custom log shipping to Splunk
     - Network devices: Syslog to Splunk
   - Retention:
     - Operational logs: 90 days hot, 1 year warm
     - Audit logs: 7 years (compliance)
     - Security logs: 2 years
   - Encryption: TLS in transit, AES-256 at rest

**Metrics Strategy**:

1. **SLI Definitions**:
   - **Availability SLI**: 99.99% (4 nines)
     - Measurement: Successful transaction rate
     - Error budget: 52 minutes/year
   - **Latency SLI**: p99 <500ms for transactions
   - **Correctness SLI**: 100% financial accuracy (zero tolerance for data errors)

2. **Transaction Metrics**:
   - `transactions_total`: Total transactions (by type, status)
   - `transactions_amount_total`: Total transaction volume (revenue metric)
   - `transactions_duration_seconds`: Transaction latency (histogram)
   - `transactions_errors_total`: Failed transactions (by error type)

3. **Security Metrics**:
   - `auth_attempts_total`: Authentication attempts (by result)
   - `auth_failures_total`: Failed authentication (by reason)
   - `suspicious_activity_total`: Flagged suspicious events
   - `access_violations_total`: Authorization failures

4. **Infrastructure Metrics**:
   - **Mainframe**: CPU, memory, DASD (disk), transaction rate (via CICS/IMS)
   - **Microservices**: Standard USE metrics (CPU, memory, disk, network)
   - **Database**: Connection pool, query latency, replication lag
   - **Network**: Latency, packet loss, bandwidth utilization

5. **Metrics Platform**:
   - Platform: Prometheus (on-premises) + Grafana for visualization
   - Collection:
     - Microservices: Prometheus client libraries
     - Mainframe: Custom exporter (CICS/IMS metrics → Prometheus)
     - Infrastructure: Node exporter, cAdvisor
   - Retention:
     - Full resolution (15s): 30 days
     - Aggregated (5 minutes): 2 years
   - High Availability: Prometheus federation across data centers

**Distributed Tracing Strategy**:

1. **Tracing Platform**: Jaeger (on-premises for data residency)

2. **Instrumentation**:
   - Microservices: OpenTelemetry with Jaeger exporter
   - Mainframe integration: Custom trace context injection at API gateway
   - Legacy systems: Correlation IDs propagated, limited span detail

3. **Trace Context Propagation**:
   - Standard: W3C Trace Context
   - Propagation: HTTP headers, message queue metadata, database comments
   - Mainframe: Correlation ID passed to CICS transactions, logged for correlation

4. **Sampling Strategy**:
   - **Head-based sampling** (deterministic for audit):
     - All financial transactions: 100% (audit requirement)
     - Authentication/authorization: 100%
     - Read-only operations: 10%
   - Expected trace volume: ~8M traces/day (~80% of requests)
   - Storage: 30 days hot, 1 year cold (S3-compatible object storage)

5. **Span Attributes**:
   - Standard: `http.method`, `http.status_code`, `db.type`
   - Financial: `transaction.id`, `transaction.type`, `account.id` (hashed)
   - Security: `user.id` (hashed), `auth.method`, `ip.address` (masked)
   - Compliance: `audit.required`, `pii.present` (flags)

**Alerting Strategy**:

1. **SLO-Based Alerts** (P0 - Critical):
   - **Availability**: Error rate >0.01% for 2 minutes (consuming error budget rapidly)
   - **Latency**: p99 >500ms for 5 minutes
   - **Error Budget**: <25% error budget remaining
   - Action: Page on-call engineer + escalate to incident commander

2. **Financial Alerts** (P0 - Critical):
   - Transaction processing failures >0.1% for 5 minutes
   - Data inconsistency detected (reconciliation failures)
   - Mainframe transaction rate drops >50%
   - Action: Page on-call engineer + notify financial operations team

3. **Security Alerts** (P0/P1 - Critical/High):
   - Multiple failed authentication attempts (potential brute force)
   - Unusual access patterns (potential breach)
   - Privilege escalation attempts
   - Configuration changes in production
   - Action: Alert security operations center (SOC), page security on-call

4. **Infrastructure Alerts** (P1/P2):
   - Mainframe CPU >80% for 10 minutes
   - Database replication lag >30 seconds
   - Disk usage >85%
   - Network latency >100ms between data centers
   - Action: Notify infrastructure team, escalate if not resolved in 30 minutes

5. **Alert Routing**:
   - P0: PagerDuty → Multiple on-call engineers + incident commander
   - P1: PagerDuty → On-call engineer + Slack #incidents
   - P2: Slack #alerts-infrastructure
   - Security: Dedicated security alerting (SIEM → SOC)
   - Escalation: P0 not acknowledged in 5 minutes → Escalate to VP Engineering

**Dashboard Design**:

1. **Executive Dashboard**:
   - SLO compliance and error budget (daily, monthly, yearly)
   - Transaction volume and revenue (real-time)
   - System availability (uptime percentage)
   - Incident count and MTTR
   - Security posture (failed auth, suspicious activity)

2. **Transaction Processing Dashboard**:
   - Transaction rate (by type: transfer, payment, withdrawal)
   - Transaction success rate and error rate
   - Transaction latency (p50/p95/p99)
   - Transaction volume and revenue
   - Failed transaction breakdown (by error type)

3. **Mainframe Integration Dashboard**:
   - Mainframe transaction rate (CICS/IMS)
   - Mainframe response time
   - API gateway to mainframe latency
   - Mainframe error rate
   - Resource utilization (CPU, memory, DASD)

4. **Security Operations Dashboard**:
   - Authentication attempts and failures
   - Suspicious activity events
   - Access violations
   - Recent security alerts
   - User access patterns

5. **Incident Response Dashboard**:
   - Current system health (all services)
   - Recent errors and anomalies
   - Service dependency map with health
   - Active alerts and incidents
   - Quick links to runbooks and escalation procedures

**Compliance and Audit**:

1. **Audit Log Requirements**:
   - All financial transactions logged with cryptographic signatures
   - Tamper-evident storage (append-only, WORM)
   - Retention: 7 years minimum
   - Access control: Only authorized auditors and compliance officers
   - All access to audit logs is itself logged

2. **Compliance Reporting**:
   - Automated compliance reports (SOC2, PCI-DSS)
   - Transaction audit trails
   - Access logs and authorization reports
   - Security event summaries
   - Scheduled delivery to compliance team

3. **Data Residency**:
   - All observability data stored on-premises or in approved regions
   - No data transfer outside geographic boundaries
   - Vendor tools deployed on-premises (Splunk, Prometheus, Jaeger)

**Implementation Roadmap**:

**Phase 1 (Weeks 1-3): Audit and Compliance**
- Deploy Splunk Enterprise for audit logging
- Implement tamper-evident audit log system
- Configure 7-year retention and archival
- Implement PII masking and redaction
- Establish access controls and audit log monitoring

**Phase 2 (Weeks 4-6): Core Observability**
- Deploy Prometheus and Grafana
- Implement structured logging in microservices
- Enable OpenTelemetry instrumentation
- Deploy Jaeger for distributed tracing
- Create basic dashboards and alerts

**Phase 3 (Weeks 7-9): Mainframe Integration**
- Develop custom mainframe metric exporters
- Implement correlation ID propagation to mainframe
- Create mainframe integration dashboards
- Test end-to-end tracing (microservices → mainframe)

**Phase 4 (Weeks 10-12): Security and Optimization**
- Integrate with SIEM (Splunk Enterprise Security)
- Implement security event monitoring and alerting
- Optimize log sampling and retention
- Create comprehensive runbooks
- Train teams on observability tools and incident response

**Cost Estimation**:

- Splunk Enterprise: 200GB/day × $150/GB/year ÷ 12 = $2,500/month
- Splunk Enterprise Security: $10,000/month
- Prometheus infrastructure: 10 servers × $500/month = $5,000/month
- Jaeger infrastructure: 5 servers × $500/month = $2,500/month
- Grafana Enterprise: $5,000/month
- Storage (audit logs, traces): 100TB × $0.05/GB = $5,000/month
- **Total**: ~$30,000/month (well within $100,000 budget)

**Success Metrics**:

- SLO compliance: >99.99% availability (4 nines)
- MTTD: <2 minutes for critical financial issues
- MTTR: <15 minutes for P0 incidents
- Audit compliance: 100% of financial transactions logged and retained
- Security: <0.01% false positive rate on security alerts
- Zero data breaches or compliance violations

---

### Example 3: SaaS Platform Observability Design

#### Context

**System**: Multi-tenant SaaS collaboration platform (similar to Slack, Teams)

**Architecture**:
- Microservices architecture (50+ services)
- Technologies: Go, Python, TypeScript (Node.js), React
- Infrastructure: Kubernetes on GCP (GKE), multi-region
- Traffic: 100,000 requests/second peak, 50M daily active users
- Tenants: 500,000 organizations (from 5 to 50,000 users each)

**Requirements**:
- SLO: 99.9% availability per tenant, p95 latency <100ms
- Multi-tenancy: Isolate observability data by tenant where needed
- Scalability: Handle 10x growth over next 2 years
- Cost efficiency: Observability cost <2% of infrastructure cost
- Developer experience: Easy to use, fast debugging
- Budget: $200,000/month for observability

#### Observability Design

**Logging Strategy**:

1. **Structured Logging Format** (JSON):

```json
{
  "timestamp": "2026-09-09T10:30:45.123Z",
  "level": "INFO",
  "service": "message-service",
  "version": "4.2.1",
  "environment": "production",
  "region": "us-central1",
  "cluster": "prod-us-central1-a",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "tenant_id": "tenant_abc123",
  "user_id": "hash_user_456",
  "channel_id": "channel_789",
  "message": "Message sent successfully",
  "message_id": "msg_xyz",
  "duration_ms": 45,
  "websocket_connections": 1523
}
```

2. **Log Levels and Sampling**:
   - ERROR/FATAL: 100%
   - WARN: 100%
   - INFO: Adaptive sampling based on service and traffic:
     - High-volume services (message, presence): 1% sampling
     - Medium-volume services (search, notifications): 10% sampling
     - Low-volume services (admin, billing): 100%
   - DEBUG: Disabled in production, enable per-tenant for troubleshooting

3. **Tenant Isolation**:
   - All logs tagged with `tenant_id`
   - Ability to filter and search logs per tenant
   - Per-tenant log sampling for large tenants (>10,000 users)
   - Tenant-specific debug logging (enable for troubleshooting specific tenant issues)

4. **Log Aggregation**:
   - Platform: Grafana Loki (cost-effective, Kubernetes-native)
   - Collection: Promtail agents on each Kubernetes node
   - Retention:
     - Hot (indexed): 7 days
     - Cold (GCS archive): 30 days
   - Cost optimization:
     - Index only ERROR/WARN and sampled INFO
     - Use label-based filtering (avoid full-text indexing)
     - Compress and archive to GCS

**Metrics Strategy**:

1. **SLI Definitions**:
   - **Availability SLI**: 99.9% per tenant (43 minutes downtime/month)
     - Measurement: Successful API requests per tenant
   - **Latency SLI**: p95 <100ms for message delivery
   - **Real-time SLI**: 99% of messages delivered within 1 second

2. **RED Metrics** (per service, per tenant):
   - `http_requests_total{service, tenant, method, status}`
   - `http_request_duration_seconds{service, tenant, method}`
   - `http_requests_errors_total{service, tenant, error_type}`

3. **Multi-Tenancy Metrics**:
   - `tenant_active_users{tenant}`: Active users per tenant
   - `tenant_message_rate{tenant}`: Messages per second per tenant
   - `tenant_storage_bytes{tenant}`: Storage used per tenant
   - `tenant_api_calls_total{tenant}`: API calls per tenant (for billing)

4. **Real-Time Metrics**:
   - `websocket_connections_total`: Active WebSocket connections
   - `message_delivery_latency_seconds`: End-to-end message delivery time
   - `presence_updates_total`: User presence updates
   - `notification_delivery_total`: Push notifications sent

5. **Metrics Platform**:
   - Platform: Grafana Cloud (managed Prometheus)
   - Collection: Prometheus client libraries in each service
   - Cardinality management:
     - Limit `tenant` label to top 1000 tenants by activity
     - Aggregate small tenants into `tenant=other`
     - Use recording rules for expensive queries
   - Retention:
     - Full resolution (15s): 30 days
     - Aggregated (5 minutes): 13 months

**Distributed Tracing Strategy**:

1. **Tracing Platform**: Grafana Tempo (cost-effective, integrated with Loki and Prometheus)

2. **Instrumentation**:
   - Auto-instrumentation: HTTP, gRPC, database calls
   - Manual instrumentation: Business logic, message processing
   - Libraries: OpenTelemetry (vendor-neutral)

3. **Sampling Strategy**:
   - **Adaptive tail-based sampling**:
     - All errors: 100%
     - Slow requests (>1s): 100%
     - Requests from new tenants (first 7 days): 50%
     - Normal requests: 5%
   - Expected trace volume: ~50M traces/day (~5% of requests)

4. **Trace Attributes**:
   - Standard: `http.method`, `http.status_code`, `rpc.service`
   - Multi-tenancy: `tenant.id`, `tenant.plan` (free, pro, enterprise)
   - Real-time: `websocket.connection_id`, `message.id`
   - Performance: `cache.hit`, `db.query_time`

**Alerting Strategy**:

1. **SLO-Based Alerts** (P1 - High):
   - **Availability**: Error rate >0.1% for 5 minutes
   - **Latency**: p95 >100ms for 10 minutes
   - **Real-time**: Message delivery >1s for 5 minutes
   - Action: Notify on-call engineer via PagerDuty + Slack

2. **Per-Tenant Alerts** (P2 - Medium):
   - Large tenant (>1000 users) experiencing >5% error rate
   - Enterprise tenant SLO violation
   - Action: Notify on-call + customer success team

3. **Infrastructure Alerts** (P2 - Medium):
   - Kubernetes pod restarts >10/hour
   - Node CPU/memory >80% for 15 minutes
   - Database connection pool exhaustion
   - Action: Notify infrastructure team via Slack

4. **Business Alerts** (P1 - High):
   - Message delivery rate drops >20%
   - WebSocket connection failures >5%
   - Payment processing failures >1%
   - Action: Notify on-call + business stakeholders

5. **Alert Routing**:
   - P0: PagerDuty → Phone/SMS to on-call engineer
   - P1: PagerDuty → Push notification + Slack #incidents
   - P2: Slack #alerts
   - Tenant-specific: Slack #customer-success + PagerDuty (for enterprise tenants)

**Dashboard Design**:

1. **Platform Health Dashboard**:
   - Overall SLO compliance and error budget
   - Request rate, error rate, latency (platform-wide)
   - Active users and tenants
   - WebSocket connections and message rate
   - Incident count and MTTR

2. **Service Dashboard** (per service):
   - RED metrics: Request rate, error rate, latency
   - Service dependencies and health
   - Recent errors and traces
   - Resource utilization (CPU, memory)

3. **Tenant Dashboard** (per tenant):
   - Tenant-specific SLO compliance
   - Active users and message rate
   - API usage and rate limits
   - Recent errors and performance issues
   - Storage and bandwidth usage

4. **Real-Time Dashboard**:
   - WebSocket connections (total and per region)
   - Message delivery latency (p50/p95/p99)
   - Presence update rate
   - Push notification delivery rate
   - Real-time error rate

5. **Customer Success Dashboard**:
   - Enterprise tenant health (SLO compliance, errors)
   - Tenant growth and activity trends
   - Support ticket correlation with errors
   - Tenant-specific alerts and incidents

**Cost Optimization**:

1. **Log Cost Reduction**:
   - Aggressive sampling for high-volume services (1%)
   - Index only errors and warnings, archive all
   - Use Loki (label-based) instead of full-text indexing
   - Short retention (7 days hot, 30 days cold)
   - **Savings**: ~80% compared to full-text indexing (e.g., Elasticsearch)

2. **Metric Cost Reduction**:
   - Limit tenant label cardinality (top 1000 tenants)
   - Use recording rules for expensive queries
   - Aggregate small tenants
   - Drop unused metrics
   - **Savings**: ~60% compared to unbounded cardinality

3. **Trace Cost Reduction**:
   - Tail-based sampling (5% average, 100% errors)
   - Short retention (30 days)
   - Use Tempo (cost-effective storage)
   - **Savings**: ~70% compared to 100% sampling

**Implementation Roadmap**:

**Phase 1 (Weeks 1-2): Foundation**
- Deploy Grafana Cloud (Prometheus, Loki, Tempo)
- Implement structured logging in top 10 services
- Enable OpenTelemetry auto-instrumentation
- Create basic RED metrics dashboards
- Set up initial SLO-based alerts

**Phase 2 (Weeks 3-4): Expansion**
- Roll out structured logging to all services
- Implement tenant-specific metrics and dashboards
- Configure adaptive sampling for logs and traces
- Create per-tenant SLO tracking
- Implement cost monitoring and optimization

**Phase 3 (Weeks 5-6): Advanced Features**
- Implement tail-based trace sampling
- Create real-time monitoring dashboards
- Set up tenant-specific alerting
- Integrate with customer success tools (Zendesk, Salesforce)
- Create comprehensive runbooks

**Phase 4 (Weeks 7-8): Optimization and Enablement**
- Optimize cardinality and cost
- Create developer self-service dashboards
- Train teams on observability tools
- Establish observability review process
- Document best practices

**Cost Estimation**:

- Grafana Cloud Metrics: 10M active series × $0.30/series = $3,000/month
- Grafana Cloud Logs: 5TB/month × $0.50/GB = $2,500/month
- Grafana Cloud Traces: 50M spans/day × $0.20/M = $10/day = $300/month
- GCS Archive Storage: 50TB × $0.02/GB = $1,000/month
- **Total**: ~$6,800/month (well within $200,000 budget, significant room for growth)

**Success Metrics**:

- SLO compliance: >99.9% per tenant
- MTTD: <3 minutes for platform issues
- MTTR: <20 minutes for P1 incidents
- Observability cost: <1% of infrastructure cost
- Developer satisfaction: >80% find observability tools helpful
- Customer success: Proactive issue detection before customer reports

---

### Example 4: AI/ML Platform Observability Design

#### Context

**System**: AI/ML platform for training and serving machine learning models

**Architecture**:
- Microservices + ML pipelines
- Technologies: Python (FastAPI, PyTorch, TensorFlow), Go, Kubernetes
- Infrastructure: Kubernetes on AWS (EKS), GPU nodes for training
- Workloads: Model training (batch), model serving (real-time inference), data pipelines
- Scale: 1000+ models, 100,000 inferences/second, 500 training jobs/day

**Requirements**:
- SLO: 99.9% availability for inference, p99 latency <50ms
- ML-specific observability: Model performance, data drift, training metrics
- Cost tracking: GPU utilization, training costs, inference costs
- Experiment tracking: Model versions, hyperparameters, metrics
- Budget: $75,000/month for observability

#### Observability Design

**Logging Strategy**:

1. **Structured Logging Format** (JSON):

```json
{
  "timestamp": "2026-09-09T10:30:45.123Z",
  "level": "INFO",
  "service": "inference-service",
  "version": "2.1.0",
  "environment": "production",
  "region": "us-west-2",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "model_id": "sentiment-classifier-v3",
  "model_version": "3.2.1",
  "inference_id": "inf_abc123",
  "input_size": 512,
  "output_classes": 3,
  "prediction": "positive",
  "confidence": 0.94,
  "latency_ms": 23,
  "gpu_utilization": 0.67,
  "message": "Inference completed successfully"
}
```

2. **ML-Specific Logging**:
   - **Inference Logs**: Model ID, version, input/output, latency, confidence
   - **Training Logs**: Experiment ID, hyperparameters, epoch, loss, metrics
   - **Data Pipeline Logs**: Dataset version, transformations, data quality metrics
   - **Model Deployment Logs**: Model version, deployment status, rollback events

3. **Log Sampling**:
   - Training logs: 100% (critical for debugging training issues)
   - Inference logs: 10% for successful inferences, 100% for errors or low confidence
   - Data pipeline logs: 100% (relatively low volume)

4. **Log Aggregation**:
   - Platform: AWS CloudWatch Logs (native AWS integration)
   - Collection: CloudWatch agent on Kubernetes nodes
   - Retention: 30 days hot, 1 year archive (S3)

**Metrics Strategy**:

1. **SLI Definitions**:
   - **Availability SLI**: 99.9% successful inferences
   - **Latency SLI**: p99 <50ms for inference
   - **Accuracy SLI**: Model accuracy >90% (monitored via validation set)

2. **Inference Metrics**:
   - `inference_requests_total{model, version, status}`: Total inferences
   - `inference_latency_seconds{model, version}`: Inference latency (histogram)
   - `inference_errors_total{model, version, error_type}`: Inference errors
   - `inference_confidence{model, version}`: Prediction confidence (histogram)
   - `inference_batch_size{model, version}`: Batch size for batched inference

3. **Training Metrics**:
   - `training_jobs_total{model, status}`: Training job count
   - `training_duration_seconds{model}`: Training job duration
   - `training_loss{model, epoch}`: Training loss per epoch
   - `training_accuracy{model, epoch}`: Training accuracy per epoch
   - `training_gpu_utilization{model, gpu_id}`: GPU utilization during training
   - `training_cost_dollars{model}`: Estimated training cost

4. **Model Performance Metrics**:
   - `model_accuracy{model, version}`: Model accuracy on validation set
   - `model_precision{model, version, class}`: Precision per class
   - `model_recall{model, version, class}`: Recall per class
   - `model_f1_score{model, version, class}`: F1 score per class
   - `model_auc{model, version}`: AUC-ROC score

5. **Data Drift Metrics**:
   - `data_drift_score{model, feature}`: Data drift detection score
   - `feature_distribution{model, feature}`: Feature distribution statistics
   - `prediction_distribution{model}`: Prediction distribution over time

6. **Resource Metrics**:
   - `gpu_utilization{node, gpu_id}`: GPU utilization
   - `gpu_memory_used_bytes{node, gpu_id}`: GPU memory usage
   - `gpu_temperature_celsius{node, gpu_id}`: GPU temperature
   - `inference_cost_dollars{model}`: Estimated inference cost

7. **Metrics Platform**:
   - Platform: Prometheus + Grafana
   - Collection: Prometheus client libraries, custom exporters for ML metrics
   - Integration: MLflow for experiment tracking (separate from operational metrics)
   - Retention: 30 days full resolution, 1 year aggregated

**Distributed Tracing Strategy**:

1. **Tracing Platform**: AWS X-Ray (native AWS integration)

2. **Instrumentation**:
   - API layer: Auto-instrumentation (FastAPI, Flask)
   - ML inference: Custom spans for model loading, preprocessing, inference, postprocessing
   - Data pipelines: Spans for each pipeline stage

3. **Trace Attributes**:
   - ML-specific: `model.id`, `model.version`, `inference.batch_size`, `gpu.id`
   - Performance: `preprocessing.duration`, `inference.duration`, `postprocessing.duration`
   - Data: `input.size`, `output.size`, `feature.count`

4. **Sampling Strategy**:
   - Errors and slow requests (>100ms): 100%
   - Normal requests: 5%
   - Training jobs: 100% (low volume)

**ML-Specific Observability**:

1. **Experiment Tracking** (MLflow):
   - Track all training experiments
   - Log hyperparameters, metrics, artifacts
   - Model versioning and registry
   - Compare experiments and models
   - Integration with observability (link experiments to production metrics)

2. **Model Performance Monitoring**:
   - Continuous validation on holdout dataset
   - Track accuracy, precision, recall, F1, AUC over time
   - Alert on model degradation (accuracy drop >5%)
   - A/B testing metrics (champion vs. challenger models)

3. **Data Drift Detection**:
   - Monitor feature distributions over time
   - Compare production data to training data
   - Detect concept drift (relationship between features and target changes)
   - Alert on significant drift (KS test, PSI score)

4. **Prediction Monitoring**:
   - Track prediction distribution over time
   - Monitor confidence scores
   - Detect anomalous predictions
   - Correlate predictions with business outcomes

**Alerting Strategy**:

1. **SLO-Based Alerts** (P1 - High):
   - Inference error rate >0.1% for 5 minutes
   - Inference p99 latency >50ms for 10 minutes
   - Action: Notify ML platform on-call engineer

2. **Model Performance Alerts** (P1 - High):
   - Model accuracy drops >5% compared to baseline
   - Significant data drift detected (PSI >0.2)
   - Prediction distribution anomaly
   - Action: Notify ML engineer + data science team

3. **Training Alerts** (P2 - Medium):
   - Training job failures >20% in last hour
   - Training cost exceeds budget threshold
   - GPU utilization <30% during training (inefficiency)
   - Action: Notify ML engineer via Slack

4. **Resource Alerts** (P2 - Medium):
   - GPU utilization >90% for 30 minutes (capacity issue)
   - GPU temperature >85°C (hardware issue)
   - Inference cost trending >20% above budget
   - Action: Notify infrastructure team

**Dashboard Design**:

1. **ML Platform Overview Dashboard**:
   - Inference SLO compliance and error budget
   - Inference rate, error rate, latency (p50/p95/p99)
   - Active models and versions
   - Training job success rate
   - GPU utilization and costs

2. **Model Performance Dashboard** (per model):
   - Inference rate and latency
   - Model accuracy, precision, recall, F1 (over time)
   - Prediction distribution and confidence
   - Data drift scores
   - A/B test results (if applicable)

3. **Training Dashboard**:
   - Active and completed training jobs
   - Training duration and cost
   - GPU utilization during training
   - Training loss and accuracy curves
   - Hyperparameter comparison

4. **Resource Utilization Dashboard**:
   - GPU utilization and memory (per node, per GPU)
   - GPU temperature
   - Inference and training costs (per model, total)
   - Cost trends and projections

5. **Data Quality Dashboard**:
   - Data drift scores (per feature)
   - Feature distribution comparisons
   - Data pipeline success rate
   - Data quality metrics (missing values, outliers)

**Cost Tracking and Optimization**:

1. **Cost Attribution**:
   - Track costs per model (inference and training)
   - Track costs per team or project
   - GPU hour costs (training vs. inference)
   - Storage costs (model artifacts, datasets)

2. **Cost Optimization**:
   - Identify underutilized GPU nodes
   - Optimize batch sizes for inference
   - Use spot instances for training
   - Model compression and quantization for faster, cheaper inference
   - Auto-scaling based on inference demand

**Implementation Roadmap**:

**Phase 1 (Weeks 1-2): Core Observability**
- Deploy Prometheus and Grafana
- Implement structured logging
- Enable AWS X-Ray tracing
- Create basic inference metrics and dashboards
- Set up SLO-based alerts

**Phase 2 (Weeks 3-4): ML-Specific Observability**
- Deploy MLflow for experiment tracking
- Implement model performance monitoring
- Create training metrics and dashboards
- Set up data drift detection
- Implement cost tracking

**Phase 3 (Weeks 5-6): Advanced Features**
- Implement A/B testing metrics
- Create prediction monitoring and anomaly detection
- Set up automated model retraining triggers
- Integrate observability with CI/CD (model deployment)
- Create comprehensive runbooks

**Phase 4 (Weeks 7-8): Optimization and Enablement**
- Optimize GPU utilization and costs
- Create self-service dashboards for data scientists
- Train teams on ML observability best practices
- Establish model performance review process
- Document standards and guidelines

**Cost Estimation**:

- Prometheus infrastructure: 5 servers × $500/month = $2,500/month
- Grafana Cloud: $2,000/month
- AWS CloudWatch Logs: 2TB/month × $0.50/GB = $1,000/month
- AWS X-Ray: 10M traces/month × $5/M = $50/month
- MLflow infrastructure: $1,000/month
- S3 storage (logs, artifacts): 20TB × $0.023/GB = $460/month
- **Total**: ~$7,010/month (well within $75,000 budget)

**Success Metrics**:

- Inference SLO compliance: >99.9%
- Model performance: Detect degradation within 1 hour
- Data drift: Detect significant drift within 24 hours
- Training efficiency: GPU utilization >70% during training
- Cost efficiency: Observability cost <5% of ML infrastructure cost
- Incident response: MTTR <30 minutes for inference issues

## Related Skills

### Prerequisite Skills

1. **system-architecture-design**
   - Understanding system architecture is essential before designing observability
   - Architecture diagrams and component relationships inform observability strategy
   - Use architecture design to identify critical paths and dependencies

2. **requirements-analysis**
   - Observability requirements must align with functional and non-functional requirements
   - SLOs and SLIs are derived from business and technical requirements
   - Use requirements analysis to prioritize observability coverage

3. **technology-selection**
   - Observability tool selection depends on technology stack and constraints
   - Integration capabilities with existing technologies are critical
   - Use technology selection to evaluate observability platform options

### Complementary Skills

4. **incident-response**
   - Observability design directly supports incident detection and resolution
   - Runbooks and alerts are key outputs of both skills
   - Use observability design to enable effective incident response

5. **performance-optimization**
   - Performance metrics and profiling data inform optimization efforts
   - Distributed tracing identifies performance bottlenecks
   - Use observability design to support performance optimization

6. **security-design**
   - Security event logging and monitoring are part of observability
   - Compliance and audit requirements overlap
   - Use observability design to support security monitoring

7. **cost-optimization**
   - Observability provides cost visibility and attribution
   - Cost metrics inform optimization decisions
   - Use observability design to enable cost optimization

### Downstream Skills

8. **sre-practices**
   - SRE practices rely heavily on observability (SLIs, SLOs, error budgets)
   - Observability is foundational for SRE culture
   - Use observability design to enable SRE practices

9. **chaos-engineering**
   - Chaos engineering experiments require comprehensive observability
   - Observability validates system resilience and recovery
   - Use observability design to support chaos engineering

10. **capacity-planning**
    - Capacity planning relies on historical metrics and trends
    - Resource utilization and saturation metrics inform capacity decisions
    - Use observability design to enable capacity planning

## Skill Composition

### Can Be Combined With

1. **observability-design + incident-response**
   - Design observability systems that directly support incident workflows
   - Create incident response dashboards and alert routing
   - Integrate observability with incident management platforms

2. **observability-design + performance-optimization**
   - Design observability to identify and diagnose performance issues
   - Create performance profiling and analysis dashboards
   - Implement latency tracking and bottleneck identification

3. **observability-design + security-design**
   - Design unified observability for both operational and security monitoring
   - Implement security event logging and SIEM integration
   - Create security dashboards and threat detection alerts

4. **observability-design + cost-optimization**
   - Design cost visibility and attribution into observability
   - Create cost tracking dashboards and budget alerts
   - Implement observability cost optimization strategies

5. **observability-design + sre-practices**
   - Design SLI/SLO framework and error budget tracking
   - Create SRE dashboards and on-call runbooks
   - Implement toil reduction through observability automation

### Typical Workflows

1. **New System Development**:
   ```
   requirements-analysis → system-architecture-design → observability-design → implementation → incident-response
   ```
   - Define requirements and architecture first
   - Design observability strategy aligned with architecture
   - Implement observability during development
   - Use observability for incident response in production

2. **Production Readiness**:
   ```
   observability-design → sre-practices → chaos-engineering → capacity-planning
   ```
   - Design comprehensive observability
   - Establish SRE practices (SLOs, on-call)
   - Validate resilience with chaos engineering
   - Plan capacity based on observability data

3. **Incident Investigation**:
   ```
   incident-response → observability-design (gap analysis) → implementation
   ```
   - Respond to incident using existing observability
   - Identify observability gaps during postmortem
   - Enhance observability to prevent recurrence

4. **Performance Optimization**:
   ```
   observability-design → performance-optimization → observability-design (refinement)
   ```
   - Use observability to identify performance issues
   - Optimize based on metrics and traces
   - Refine observability to track optimization impact

## Evaluation Criteria

### Design Quality (30%)

**Excellent (90-100%)**:
- Comprehensive coverage of all critical services and components
- All three pillars (logs, metrics, traces) are well-designed and integrated
- Strong correlation between logs, metrics, and traces
- SLI/SLO framework is well-defined and measurable
- Design is scalable to 10x-100x growth
- Cost-effective (observability cost <5% of infrastructure cost)
- Fully compliant with all regulatory and security requirements

**Good (70-89%)**:
- Good coverage of most critical services
- All three pillars are addressed, with minor gaps
- Basic correlation between logs, metrics, and traces
- SLI/SLO framework is defined but may lack detail
- Design can scale to 5x-10x growth
- Reasonable cost (observability cost <10% of infrastructure cost)
- Mostly compliant with regulatory requirements

**Adequate (50-69%)**:
- Coverage of some critical services, with notable gaps
- Two of three pillars are well-designed, one is weak
- Limited correlation between pillars
- SLI/SLO framework is basic or incomplete
- Design can scale to 2x-5x growth
- Moderate cost (observability cost <15% of infrastructure cost)
- Partially compliant with regulatory requirements

**Needs Improvement (<50%)**:
- Incomplete coverage, major gaps in critical services
- One or more pillars are missing or poorly designed
- No correlation between pillars
- SLI/SLO framework is missing or unclear
- Design cannot scale beyond current load
- High cost (observability cost >15% of infrastructure cost)
- Non-compliant with regulatory requirements

### Technical Implementation (25%)

**Excellent (90-100%)**:
- Instrumentation standards are comprehensive and well-documented
- Consistent implementation across all services
- Appropriate use of auto-instrumentation and manual instrumentation
- Performance overhead is minimal (<5% latency impact)
- Observability instrumentation is tested and validated
- Code examples and templates are provided
- Integration with CI/CD and deployment pipelines

**Good (70-89%)**:
- Instrumentation standards are defined and documented
- Mostly consistent implementation across services
- Good balance of auto and manual instrumentation
- Acceptable performance overhead (<10% latency impact)
- Basic testing of observability instrumentation
- Some code examples provided
- Partial integration with CI/CD

**Adequate (50-69%)**:
- Basic instrumentation standards exist
- Inconsistent implementation across services
- Over-reliance on auto-instrumentation or manual instrumentation
- Noticeable performance overhead (<20% latency impact)
- Limited testing of observability instrumentation
- Few code examples
- Minimal CI/CD integration

**Needs Improvement (<50%)**:
- No instrumentation standards
- Highly inconsistent or ad-hoc implementation
- Inappropriate instrumentation approach
- Significant performance overhead (>20% latency impact)
- No testing of observability instrumentation
- No code examples or documentation
- No CI/CD integration

### Operational Effectiveness (25%)

**Excellent (90-100%)**:
- Alerts are actionable, low-noise, and symptom-based
- Alert-to-incident ratio >70% (most alerts are real)
- Comprehensive runbooks for all alerts
- Dashboards are intuitive, fast, and enable quick decision-making
- Clear ownership and escalation policies
- Regular reviews and continuous improvement processes
- Strong feedback loops from incidents to observability improvements

**Good (70-89%)**:
- Alerts are mostly actionable with some noise
- Alert-to-incident ratio >50%
- Runbooks exist for most alerts
- Dashboards are functional and mostly useful
- Ownership and escalation policies are defined
- Periodic reviews occur
- Some feedback loops exist

**Adequate (50-69%)**:
- Alerts are sometimes actionable, with notable noise
- Alert-to-incident ratio >30%
- Runbooks exist for some alerts
- Dashboards are basic and somewhat useful
- Ownership is unclear or informal
- Infrequent reviews
- Limited feedback loops

**Needs Improvement (<50%)**:
- Alerts are noisy and often non-actionable
- Alert-to-incident ratio <30% (high false positive rate)
- No runbooks or documentation
- Dashboards are confusing or unhelpful
- No clear ownership
- No review processes
- No feedback loops

### Documentation and Enablement (20%)

**Excellent (90-100%)**:
- Comprehensive observability architecture documentation
- Detailed instrumentation guidelines with code examples
- Complete runbooks for common scenarios
- Training materials and onboarding guides
- Well-organized dashboard catalog
- Regular knowledge sharing and best practices documentation
- Self-service capabilities for developers

**Good (70-89%)**:
- Good observability architecture documentation
- Instrumentation guidelines with some examples
- Runbooks for most common scenarios
- Basic training materials
- Dashboard catalog exists
- Some knowledge sharing
- Limited self-service capabilities

**Adequate (50-69%)**:
- Basic observability documentation
- Minimal instrumentation guidelines
- Runbooks for a few scenarios
- Informal training (on-the-job)
- Dashboards are discoverable but not cataloged
- Occasional knowledge sharing
- No self-service capabilities

**Needs Improvement (<50%)**:
- Little or no documentation
- No instrumentation guidelines
- No runbooks
- No training or onboarding
- Dashboards are hard to find
- No knowledge sharing
- No self-service capabilities

### Overall Evaluation

**Excellent (90-100%)**:
- All criteria are met at excellent or good level
- Observability system is comprehensive, effective, and well-operated
- Team is enabled and empowered to use observability effectively
- Continuous improvement processes are in place
- Observability is a competitive advantage

**Good (70-89%)**:
- Most criteria are met at good level
- Observability system is functional and mostly effective
- Team can use observability for most scenarios
- Some improvement processes exist
- Observability supports business objectives

**Adequate (50-69%)**:
- Some criteria are met at adequate level
- Observability system has gaps but provides value
- Team can use observability for basic scenarios
- Limited improvement processes
- Observability is functional but not optimized

**Needs Improvement (<50%)**:
- Most criteria are not met
- Observability system has major gaps or is ineffective
- Team struggles to use observability
- No improvement processes
- Observability does not support business objectives
