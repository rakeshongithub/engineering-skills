# agent-observability

**Purpose:** Implement comprehensive monitoring, logging, and debugging infrastructure for AI agent workflows to ensure visibility, diagnose issues quickly, and optimize performance.

---

## When to Use

Use this skill when:
- Deploying agents to production environments that require operational visibility
- Debugging complex multi-agent workflows where failures are difficult to diagnose
- Optimizing agent performance based on runtime metrics (latency, throughput, error rates)
- Meeting compliance requirements for audit trails and operational transparency
- Implementing SLAs/SLOs that require continuous monitoring and alerting
- Diagnosing intermittent failures or performance degradation in agent systems
- Understanding agent behavior patterns and resource consumption
- Building dashboards for stakeholders to track agent health and performance
- Implementing proactive alerting for agent failures or performance issues
- Conducting post-incident analysis and root cause investigation

---

## When NOT to Use

Do NOT use this skill when:
- Building proof-of-concept agents where observability overhead is not justified
- Working with simple, single-agent workflows that can be debugged with basic logging
- Operating in environments where logging/monitoring infrastructure is unavailable
- Observability requirements are minimal (e.g., batch jobs with no SLA)
- The cost of observability infrastructure exceeds the value of the agent system
- Privacy/security constraints prohibit detailed logging or monitoring
- You need to implement the agent workflow first (use agent-workflow-design instead)
- You need to evaluate agent output quality (use agent-evaluation instead)

---

## Inputs

| Input | Description | Required | Format |
|-------|-------------|----------|--------|
| agent_workflow | Complete agent workflow design including tasks, handoffs, and dependencies | Yes | Workflow diagram, task definitions, handoff specifications |
| monitoring_requirements | Specific observability needs (SLAs, compliance, debugging) | Yes | Requirements document with metrics, SLOs, compliance needs |
| infrastructure_constraints | Available logging/monitoring tools and platforms | Yes | List of available tools (e.g., CloudWatch, Datadog, Prometheus) |
| performance_targets | Target metrics for latency, throughput, error rates | Yes | Quantitative targets (e.g., p95 latency < 2s, error rate < 1%) |
| compliance_requirements | Audit, retention, and privacy requirements | No | Compliance framework (GDPR, SOC 2, HIPAA) |
| budget_constraints | Cost limits for observability infrastructure | No | Monthly budget for logging, metrics, tracing |

---

## Expected Outputs

| Output | Description | Format |
|--------|-------------|--------|
| observability_plan | Comprehensive plan for logging, metrics, tracing, and alerting | Document with architecture, tool selection, implementation steps |
| logging_configuration | Structured logging setup with log levels, formats, and destinations | Configuration files, code snippets |
| metrics_instrumentation | Code and configuration for collecting performance metrics | Instrumentation code, metric definitions |
| tracing_setup | Distributed tracing configuration for multi-agent workflows | Tracing configuration, span definitions |
| dashboards | Operational dashboards for monitoring agent health and performance | Dashboard JSON/YAML, screenshots |
| alerts | Alert rules for proactive notification of issues | Alert definitions with thresholds and notification channels |
| runbooks | Operational runbooks for common issues and debugging procedures | Documentation with step-by-step troubleshooting guides |

---

## Workflow

### Step 1: Identify Observability Needs

**Objective:** Understand what needs to be monitored and why.

**Actions:**
1. Review agent workflow to identify critical paths and failure points
2. Identify SLAs/SLOs that require monitoring (e.g., response time, availability)
3. Determine compliance requirements (audit trails, data retention)
4. Identify debugging needs (common failure modes, performance bottlenecks)
5. Define stakeholder visibility requirements (dashboards, reports)

**Decision Points:**
- What are the critical success metrics for this agent system?
- What failure modes are most likely or most impactful?
- What compliance or audit requirements must be met?
- Who needs visibility into agent operations and what do they need to see?

**Outputs:**
- List of critical metrics to monitor
- List of failure scenarios to detect
- Compliance and audit requirements
- Stakeholder visibility requirements

**Quality Checks:**
- [ ] All SLAs/SLOs have corresponding metrics
- [ ] All critical failure modes have detection mechanisms
- [ ] Compliance requirements are documented
- [ ] Stakeholder needs are clearly defined

---

### Step 2: Select Observability Tools

**Objective:** Choose the right tools for logging, metrics, tracing, and alerting.

**Actions:**
1. Evaluate available logging platforms (CloudWatch, Datadog, ELK, Splunk)
2. Evaluate metrics collection tools (Prometheus, CloudWatch Metrics, Datadog)
3. Evaluate distributed tracing tools (Jaeger, X-Ray, Zipkin, Datadog APM)
4. Evaluate alerting platforms (PagerDuty, Opsgenie, CloudWatch Alarms)
5. Consider unified observability platforms vs. best-of-breed tools
6. Assess cost, integration complexity, and feature fit

**Decision Framework:**

**Use Unified Platform (e.g., Datadog, New Relic) when:**
- You need integrated logging, metrics, and tracing
- You want simplified vendor management
- You have budget for premium tools
- You need out-of-the-box dashboards and integrations

**Use Best-of-Breed Tools when:**
- You have specific requirements not met by unified platforms
- You want to minimize costs (e.g., open-source tools)
- You have existing investments in specific tools
- You need maximum flexibility and customization

**Outputs:**
- Selected logging platform
- Selected metrics platform
- Selected tracing platform
- Selected alerting platform
- Tool integration plan

**Quality Checks:**
- [ ] Selected tools meet all functional requirements
- [ ] Tools integrate with existing infrastructure
- [ ] Cost is within budget constraints
- [ ] Tools support required retention and compliance policies

---

### Step 3: Design Logging Strategy

**Objective:** Define what to log, how to structure logs, and where to send them.

**Actions:**
1. Define log levels (DEBUG, INFO, WARN, ERROR, CRITICAL)
2. Design structured log format (JSON with standard fields)
3. Identify what to log at each workflow step
4. Define correlation IDs for tracing requests across agents
5. Implement log sampling for high-volume scenarios
6. Configure log destinations and retention policies

**Log Structure:**
```json
{
  "timestamp": "2026-09-08T10:45:23.123Z",
  "level": "INFO",
  "correlation_id": "req-12345",
  "agent_id": "code-review-agent-1",
  "task_id": "task-67890",
  "message": "Code review completed",
  "duration_ms": 1234,
  "metadata": {
    "files_reviewed": 5,
    "issues_found": 3
  }
}
```

**What to Log:**
- **Task Start/End:** Every agent task with correlation ID, inputs, outputs
- **Handoffs:** Agent-to-agent transitions with context transfer details
- **Errors:** All exceptions with stack traces and context
- **Performance:** Duration of critical operations
- **Business Events:** Key workflow milestones (e.g., "review approved")

**Outputs:**
- Log structure specification
- Log level guidelines
- List of log events for each workflow step
- Log retention policy

**Quality Checks:**
- [ ] Logs include correlation IDs for request tracing
- [ ] Structured format enables easy querying
- [ ] Sensitive data is redacted or excluded
- [ ] Log volume is manageable (sampling if needed)

---

### Step 4: Design Metrics Collection

**Objective:** Define metrics to track agent performance and health.

**Actions:**
1. Identify key performance metrics (latency, throughput, error rate)
2. Identify resource metrics (CPU, memory, API calls, cost)
3. Identify business metrics (tasks completed, success rate)
4. Define metric dimensions (agent_id, task_type, environment)
5. Set up metric aggregation and retention policies

**Key Metrics:**

**Performance Metrics:**
- `agent.task.duration` (histogram): Time to complete tasks
- `agent.task.count` (counter): Number of tasks executed
- `agent.task.error_rate` (gauge): Percentage of failed tasks
- `agent.handoff.latency` (histogram): Time for agent-to-agent handoffs

**Resource Metrics:**
- `agent.cpu.usage` (gauge): CPU utilization
- `agent.memory.usage` (gauge): Memory consumption
- `agent.api.calls` (counter): External API calls made
- `agent.cost` (counter): Estimated cost per task

**Business Metrics:**
- `agent.workflow.completion_rate` (gauge): % of workflows completed successfully
- `agent.sla.compliance` (gauge): % of tasks meeting SLA

**Metric Dimensions:**
- `agent_id`: Specific agent instance
- `task_type`: Type of task (e.g., "code_review", "testing")
- `environment`: prod, staging, dev
- `workflow_id`: Specific workflow instance

**Outputs:**
- Metric definitions with types and dimensions
- Metric collection code/configuration
- Aggregation and retention policies

**Quality Checks:**
- [ ] All SLAs have corresponding metrics
- [ ] Metrics have appropriate dimensions for filtering
- [ ] High-cardinality dimensions are avoided
- [ ] Metric retention aligns with analysis needs

---

### Step 5: Implement Distributed Tracing

**Objective:** Enable end-to-end request tracing across multi-agent workflows.

**Actions:**
1. Select tracing standard (OpenTelemetry, AWS X-Ray, Datadog APM)
2. Instrument agent code to create spans for each operation
3. Propagate trace context across agent handoffs
4. Define span attributes (task_id, agent_id, inputs, outputs)
5. Configure trace sampling to manage volume
6. Set up trace storage and querying

**Trace Structure:**
```
Workflow Trace (trace_id: abc123)
├─ Agent 1: Task Decomposition (span_id: span1, duration: 500ms)
│  ├─ Read Requirements (span_id: span2, duration: 100ms)
│  └─ Generate Tasks (span_id: span3, duration: 400ms)
├─ Handoff: Agent 1 → Agent 2 (span_id: span4, duration: 50ms)
├─ Agent 2: Code Generation (span_id: span5, duration: 2000ms)
│  ├─ Load Context (span_id: span6, duration: 200ms)
│  ├─ Generate Code (span_id: span7, duration: 1500ms)
│  └─ Validate Output (span_id: span8, duration: 300ms)
└─ Handoff: Agent 2 → Agent 3 (span_id: span9, duration: 50ms)
```

**Span Attributes:**
- `agent_id`: Which agent executed this span
- `task_id`: Task identifier
- `task_type`: Type of task
- `input_size`: Size of input data
- `output_size`: Size of output data
- `error`: Error message if span failed

**Outputs:**
- Tracing instrumentation code
- Trace context propagation mechanism
- Trace sampling configuration
- Trace query examples

**Quality Checks:**
- [ ] Trace context propagates across all agent handoffs
- [ ] Spans capture all critical operations
- [ ] Span attributes enable effective filtering
- [ ] Sampling rate balances cost and visibility

---

### Step 6: Create Operational Dashboards

**Objective:** Build dashboards for monitoring agent health and performance.

**Actions:**
1. Design dashboard layout for different audiences (ops, business, devs)
2. Create real-time health dashboard (error rates, latency, throughput)
3. Create performance dashboard (p50/p95/p99 latency, resource usage)
4. Create business metrics dashboard (completion rate, SLA compliance)
5. Create debugging dashboard (recent errors, slow requests, failed handoffs)
6. Set up auto-refresh and time range controls

**Dashboard Examples:**

**Health Dashboard:**
- Error rate (last 1 hour)
- Task completion rate (last 1 hour)
- Active agents count
- Recent alerts

**Performance Dashboard:**
- Task duration percentiles (p50, p95, p99)
- Handoff latency
- Resource usage (CPU, memory)
- API call rates

**Business Dashboard:**
- Workflows completed today
- SLA compliance %
- Cost per workflow
- Top failure reasons

**Outputs:**
- Dashboard definitions (JSON/YAML)
- Dashboard screenshots
- Dashboard access controls

**Quality Checks:**
- [ ] Dashboards show real-time data
- [ ] Critical metrics are prominently displayed
- [ ] Dashboards are accessible to intended audiences
- [ ] Dashboards have appropriate time ranges and filters

---

### Step 7: Configure Alerts and Notifications

**Objective:** Set up proactive alerts for failures and performance degradation.

**Actions:**
1. Define alert conditions based on metrics and logs
2. Set alert thresholds (static, dynamic, anomaly-based)
3. Configure notification channels (email, Slack, PagerDuty)
4. Implement alert routing based on severity
5. Set up alert deduplication and suppression
6. Define on-call escalation policies

**Alert Examples:**

**Critical Alerts (Page Immediately):**
- Error rate > 5% for 5 minutes
- No tasks completed in last 15 minutes (system down)
- p95 latency > 10s for 10 minutes

**Warning Alerts (Notify, No Page):**
- Error rate > 2% for 10 minutes
- p95 latency > 5s for 15 minutes
- Memory usage > 80% for 20 minutes

**Info Alerts (Log Only):**
- Deployment completed
- Configuration changed
- Scaling event occurred

**Alert Routing:**
- **Critical:** PagerDuty → On-call engineer
- **Warning:** Slack #agent-alerts channel
- **Info:** CloudWatch Logs

**Outputs:**
- Alert rule definitions
- Notification channel configurations
- Escalation policies
- Alert runbooks

**Quality Checks:**
- [ ] All critical failure modes have alerts
- [ ] Alert thresholds are tuned to avoid false positives
- [ ] Notification channels are tested and working
- [ ] On-call rotation is defined and documented

---

### Step 8: Implement Debugging Tools

**Objective:** Provide tools and processes for diagnosing agent failures.

**Actions:**
1. Create log query templates for common debugging scenarios
2. Implement request replay capability for reproducing failures
3. Create trace visualization for understanding workflow execution
4. Implement performance profiling for identifying bottlenecks
5. Create debugging runbooks for common failure modes
6. Set up access controls for debugging tools

**Debugging Capabilities:**

**Log Queries:**
```
# Find all errors for a specific workflow
fields @timestamp, level, message, metadata
| filter correlation_id = "req-12345" and level = "ERROR"
| sort @timestamp desc

# Find slow tasks
fields @timestamp, agent_id, task_id, duration_ms
| filter duration_ms > 5000
| sort duration_ms desc
```

**Request Replay:**
- Capture original request inputs
- Replay request through agent workflow
- Compare original vs. replay outputs
- Identify non-deterministic behavior

**Trace Visualization:**
- Waterfall view of workflow execution
- Identify slow spans and bottlenecks
- Visualize agent handoffs and dependencies

**Outputs:**
- Log query library
- Request replay tool
- Trace visualization setup
- Debugging runbooks

**Quality Checks:**
- [ ] Debugging tools are accessible to authorized users
- [ ] Common failure scenarios have documented debugging procedures
- [ ] Request replay works for all workflow types
- [ ] Trace visualization clearly shows execution flow

---

### Step 9: Test Observability Infrastructure

**Objective:** Validate that observability infrastructure works as expected.

**Actions:**
1. Generate test traffic to produce logs, metrics, and traces
2. Verify logs are structured correctly and queryable
3. Verify metrics are collected and aggregated properly
4. Verify traces capture end-to-end workflow execution
5. Verify dashboards display correct data
6. Trigger test alerts to verify notification delivery
7. Test debugging tools with known failure scenarios

**Test Scenarios:**

**Happy Path:**
- Execute successful workflow
- Verify logs show all steps
- Verify metrics show successful completion
- Verify trace shows complete execution

**Error Scenarios:**
- Trigger agent failure
- Verify error logs are captured
- Verify error metrics increment
- Verify alert fires and notification is sent

**Performance Scenarios:**
- Execute slow workflow
- Verify latency metrics are accurate
- Verify trace shows slow spans
- Verify performance alert fires if threshold exceeded

**Outputs:**
- Test results documentation
- List of issues found and fixed
- Validated observability configuration

**Quality Checks:**
- [ ] All logs are queryable and structured correctly
- [ ] All metrics are collected and accurate
- [ ] All traces capture complete workflow execution
- [ ] All dashboards display correct data
- [ ] All alerts fire and deliver notifications
- [ ] Debugging tools work for test scenarios

---

### Step 10: Document and Train

**Objective:** Ensure team can effectively use observability infrastructure.

**Actions:**
1. Document observability architecture and design decisions
2. Create operational runbooks for common tasks (debugging, incident response)
3. Document dashboard usage and interpretation
4. Document alert response procedures
5. Train team on using observability tools
6. Establish continuous improvement process for observability

**Documentation:**
- **Architecture:** Logging, metrics, tracing setup
- **Runbooks:** Step-by-step procedures for common tasks
- **Dashboards:** What each dashboard shows and how to use it
- **Alerts:** What each alert means and how to respond
- **Debugging:** How to use logs, metrics, traces to diagnose issues

**Training Topics:**
- How to query logs for debugging
- How to interpret metrics and dashboards
- How to use distributed tracing to understand workflow execution
- How to respond to alerts
- How to use debugging tools

**Outputs:**
- Observability documentation
- Operational runbooks
- Training materials
- Team training sessions completed

**Quality Checks:**
- [ ] Documentation covers all observability components
- [ ] Runbooks are tested and accurate
- [ ] Team is trained on observability tools
- [ ] Continuous improvement process is established

---

## Decision Framework

### Logging Strategy Selection

**Use Verbose Logging when:**
- Debugging complex issues in development/staging
- Compliance requires detailed audit trails
- Cost of storage is low

**Use Sampled Logging when:**
- Production traffic is high-volume
- Storage costs are significant
- Most requests are routine and don't need detailed logs

**Use Structured Logging when:**
- You need to query logs programmatically
- You want to aggregate and analyze log data
- You need to correlate logs across services

---

### Metrics Granularity

**Use High-Granularity Metrics (1-second intervals) when:**
- Debugging performance issues
- Monitoring real-time systems with tight SLAs
- Short-lived workflows (< 1 minute)

**Use Medium-Granularity Metrics (1-minute intervals) when:**
- Monitoring typical production workloads
- Balancing cost and visibility
- Workflows run for several minutes

**Use Low-Granularity Metrics (5-15 minute intervals) when:**
- Monitoring long-running batch jobs
- Cost optimization is critical
- Real-time visibility is not required

---

### Tracing Sampling Rate

**Use 100% Sampling when:**
- Development/staging environments
- Low-volume production traffic (< 100 requests/min)
- Debugging specific issues

**Use Adaptive Sampling when:**
- High-volume production traffic
- You want to capture all errors but sample successes
- Cost optimization is important

**Example Adaptive Sampling:**
- Sample 100% of errors
- Sample 100% of slow requests (> p95 latency)
- Sample 1-10% of normal requests

---

### Alert Threshold Selection

**Use Static Thresholds when:**
- Metrics have predictable, stable baselines
- SLAs define specific numeric targets
- Simplicity is preferred

**Use Dynamic Thresholds when:**
- Metrics vary by time of day or day of week
- Traffic patterns are seasonal
- You want to detect anomalies relative to historical baselines

**Use Anomaly Detection when:**
- Metrics have complex, unpredictable patterns
- You want to detect unusual behavior automatically
- You have ML-based alerting tools available

---

## Quality Checklist

Before considering observability implementation complete, verify:

### Logging
- [ ] All agent tasks emit start/end logs with correlation IDs
- [ ] All errors are logged with stack traces and context
- [ ] Logs use structured format (JSON) for easy querying
- [ ] Sensitive data is redacted from logs
- [ ] Log retention policy meets compliance requirements
- [ ] Logs are queryable in centralized logging platform

### Metrics
- [ ] All SLA metrics are collected (latency, error rate, throughput)
- [ ] Resource metrics are collected (CPU, memory, API calls, cost)
- [ ] Business metrics are collected (completion rate, success rate)
- [ ] Metrics have appropriate dimensions for filtering
- [ ] Metrics are visualized in dashboards
- [ ] Metric retention aligns with analysis needs

### Tracing
- [ ] Distributed tracing captures end-to-end workflow execution
- [ ] Trace context propagates across all agent handoffs
- [ ] Spans include relevant attributes (agent_id, task_id, inputs, outputs)
- [ ] Trace sampling balances cost and visibility
- [ ] Traces are queryable and visualizable

### Dashboards
- [ ] Health dashboard shows real-time error rates and throughput
- [ ] Performance dashboard shows latency percentiles and resource usage
- [ ] Business dashboard shows completion rates and SLA compliance
- [ ] Dashboards are accessible to intended audiences
- [ ] Dashboards auto-refresh and support time range selection

### Alerts
- [ ] Critical failure modes have alerts with appropriate thresholds
- [ ] Alerts route to correct notification channels
- [ ] Alert deduplication and suppression are configured
- [ ] On-call escalation policies are defined
- [ ] Alerts have been tested and deliver notifications

### Debugging
- [ ] Log query templates exist for common debugging scenarios
- [ ] Request replay capability is available
- [ ] Trace visualization is set up
- [ ] Debugging runbooks are documented
- [ ] Debugging tools are accessible to authorized users

### Documentation and Training
- [ ] Observability architecture is documented
- [ ] Operational runbooks are created and tested
- [ ] Dashboard usage is documented
- [ ] Alert response procedures are documented
- [ ] Team is trained on observability tools
- [ ] Continuous improvement process is established

---

## Common Mistakes

### 1. Logging Too Much or Too Little

**Mistake:** Logging every variable assignment (too much) or only logging errors (too little).

**Why It's a Problem:**
- Too much logging: High storage costs, noisy logs, hard to find relevant information
- Too little logging: Insufficient context to diagnose issues

**How to Avoid:**
- Log task start/end, handoffs, errors, and key business events
- Use log levels appropriately (DEBUG for detailed info, INFO for normal events, ERROR for failures)
- Implement log sampling for high-volume scenarios

---

### 2. Ignoring Correlation IDs

**Mistake:** Not including correlation IDs in logs, making it impossible to trace requests across agents.

**Why It's a Problem:**
- Cannot reconstruct end-to-end workflow execution
- Difficult to diagnose issues that span multiple agents

**How to Avoid:**
- Generate correlation ID at workflow start
- Propagate correlation ID through all agent handoffs
- Include correlation ID in all log entries

---

### 3. High-Cardinality Metric Dimensions

**Mistake:** Using high-cardinality dimensions like user_id or request_id in metrics.

**Why It's a Problem:**
- Explodes metric storage costs
- Degrades query performance
- May hit platform limits on unique metric combinations

**How to Avoid:**
- Use low-cardinality dimensions (agent_id, task_type, environment)
- Avoid unique identifiers as dimensions
- Use logs or traces for high-cardinality data

---

### 4. Alert Fatigue

**Mistake:** Setting alert thresholds too sensitive, causing frequent false positives.

**Why It's a Problem:**
- Team ignores alerts ("boy who cried wolf")
- Real issues get missed
- Wastes time investigating non-issues

**How to Avoid:**
- Tune alert thresholds based on historical data
- Use dynamic thresholds or anomaly detection
- Implement alert deduplication and suppression
- Review and adjust alerts regularly

---

### 5. No Trace Context Propagation

**Mistake:** Not propagating trace context across agent handoffs.

**Why It's a Problem:**
- Traces are fragmented, showing only individual agent execution
- Cannot visualize end-to-end workflow
- Difficult to identify handoff bottlenecks

**How to Avoid:**
- Use standard trace context propagation (W3C Trace Context, OpenTelemetry)
- Include trace context in handoff payloads
- Verify trace context propagation in tests

---

### 6. Dashboards Without Context

**Mistake:** Creating dashboards with raw metrics but no context or thresholds.

**Why It's a Problem:**
- Viewers don't know if metrics are good or bad
- No actionable insights
- Wasted dashboard real estate

**How to Avoid:**
- Add SLA/SLO thresholds to charts
- Use color coding (green/yellow/red) for status
- Include trend lines and comparisons (current vs. previous period)
- Add annotations for deployments and incidents

---

### 7. Logging Sensitive Data

**Mistake:** Logging PII, credentials, or other sensitive data.

**Why It's a Problem:**
- Compliance violations (GDPR, HIPAA)
- Security risk if logs are compromised
- Legal liability

**How to Avoid:**
- Implement automatic redaction of sensitive fields
- Review log output for sensitive data
- Use structured logging with explicit field inclusion
- Encrypt logs at rest and in transit

---

### 8. No Observability Testing

**Mistake:** Deploying observability infrastructure without testing it.

**Why It's a Problem:**
- Discover issues during actual incidents (worst time)
- Logs/metrics/traces may not capture needed information
- Alerts may not fire or deliver notifications

**How to Avoid:**
- Test observability infrastructure with synthetic traffic
- Trigger test failures and verify detection
- Conduct "game day" exercises to validate incident response
- Review observability data regularly to ensure quality

---

## Examples

See [examples.md](./examples.md) for detailed examples:
1. **Logging Implementation** - Structured logging with correlation IDs and log aggregation
2. **Metrics Collection** - Latency, throughput, and error rate tracking with dashboards
3. **Distributed Tracing** - End-to-end request tracing across multi-agent workflows
4. **Debugging Workflow** - Using logs, metrics, and traces to diagnose a production issue

---

## Related Skills

### Required Before This Skill
- **agent-workflow-design**: Need workflow design to know what to monitor
- **agent-handoff-design**: Need handoff design to instrument trace context propagation

### Commonly Followed By
- **agent-evaluation**: Use observability data to evaluate agent performance
- **agentic-workflow-review**: Use observability insights to optimize workflows

### Works Well With
- **agent-guardrails**: Observability detects guardrail violations
- **agent-task-decomposition**: Observability validates task boundaries and performance

### Alternative To
- None (observability is essential for production agent systems)

---

## Skill Composition

### Pattern: Production-Ready Agent System

```
agent-workflow-design
    ↓
agent-handoff-design
    ↓
agent-guardrails
    ↓
agent-observability (this skill)
    ↓
agent-evaluation
    ↓
agentic-workflow-review
```

**Use this pattern when:** Deploying agents to production with operational excellence.

---

### Pattern: Incident Response and Debugging

```
agent-observability (this skill)
    ↓
Logs + Metrics + Traces
    ↓
Root Cause Analysis
    ↓
agentic-workflow-review
    ↓
Optimization
```

**Use this pattern when:** Diagnosing and fixing production issues.

---

## Evaluation Criteria

### Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Mean Time to Detect (MTTD) | < 5 minutes | Time from issue occurrence to alert |
| Mean Time to Resolve (MTTR) | < 30 minutes | Time from alert to resolution |
| Alert Accuracy | > 95% | % of alerts that are actionable |
| Observability Coverage | 100% | % of workflow steps with logging/metrics/tracing |
| Dashboard Availability | > 99.9% | % uptime of dashboards |
| Log Query Performance | < 5 seconds | Time to execute common log queries |

### Quality Indicators

**High-Quality Observability:**
- All critical paths have logging, metrics, and tracing
- Correlation IDs enable end-to-end request tracing
- Dashboards provide actionable insights
- Alerts fire proactively before user impact
- Debugging tools enable rapid root cause analysis
- Team can diagnose issues without escalation

**Low-Quality Observability:**
- Gaps in logging/metrics/tracing coverage
- Cannot trace requests across agents
- Dashboards show data but no insights
- Alerts fire after user impact or not at all
- Debugging requires manual log inspection
- Frequent escalations to senior engineers

---

## Advanced Topics

### Cost Optimization

**Strategies:**
- Use log sampling for high-volume scenarios
- Implement metric aggregation to reduce data points
- Use adaptive trace sampling (100% errors, 1-10% successes)
- Set appropriate retention policies (e.g., 7 days for logs, 30 days for metrics)
- Use tiered storage (hot/warm/cold) for cost-effective retention

**Example:**
- Logs: 7-day hot storage, 90-day cold storage
- Metrics: 1-second granularity for 1 day, 1-minute granularity for 30 days, 1-hour granularity for 1 year
- Traces: 100% sampling for 1 day, 10% sampling for 7 days

---

### Compliance and Privacy

**Considerations:**
- **GDPR**: Redact PII, implement data retention limits, support data deletion requests
- **HIPAA**: Encrypt logs at rest and in transit, implement access controls, maintain audit trails
- **SOC 2**: Implement comprehensive audit logging, maintain log integrity, control access to logs

**Implementation:**
- Automatic PII redaction in logs
- Encryption for logs and metrics
- Role-based access control for observability tools
- Audit logging for access to sensitive data

---

### Multi-Region Observability

**Challenges:**
- Logs/metrics/traces distributed across regions
- Need unified view for global workflows
- Latency and cost of cross-region data transfer

**Solutions:**
- Centralized logging/metrics platform with regional ingestion
- Distributed tracing with global trace IDs
- Regional dashboards with global rollup
- Careful data retention policies to manage costs

---

### Observability as Code

**Benefits:**
- Version-controlled observability configuration
- Automated deployment of dashboards, alerts, and instrumentation
- Consistency across environments

**Implementation:**
- Store dashboard definitions in Git (JSON/YAML)
- Store alert rules in Git (Terraform, CloudFormation)
- Use infrastructure-as-code tools (Terraform, Pulumi) to deploy observability infrastructure
- Include observability configuration in CI/CD pipelines

**Example:**
```yaml
# dashboard.yaml
dashboard:
  name: "Agent Health Dashboard"
  widgets:
    - type: metric
      title: "Error Rate"
      metric: agent.task.error_rate
      threshold: 0.05
    - type: metric
      title: "p95 Latency"
      metric: agent.task.duration
      percentile: 95
      threshold: 5000
```

---

## Tags

observability, monitoring, logging, metrics, tracing, debugging, dashboards, alerts, distributed-tracing, structured-logging, performance-monitoring, incident-response, sla, slo, operational-excellence

---

## Metadata

- **Complexity:** Advanced
- **Estimated Time:** 2-4 hours
- **Last Updated:** 2026-09-08
- **Version:** 1.0.0
