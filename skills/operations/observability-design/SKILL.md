# Observability Design

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 4-8 hours

---

## Purpose

Design comprehensive observability systems that provide visibility into system behavior through logs, metrics, and traces to enable effective monitoring, debugging, and optimization.

---

## When to Use

- Designing observability for new systems or services
- Improving existing monitoring and debugging capabilities
- After incidents reveal observability gaps
- When system complexity makes debugging difficult
- Before production deployment (production readiness)
- When implementing microservices or distributed systems
- When SLOs/SLAs require better visibility
- When troubleshooting time is too high

---

## When NOT to Use

- **For simple, single-component systems** — basic logging may suffice
- **Without clear requirements** — understand what you need to observe first
- **As an afterthought** — observability should be designed upfront
- **Without considering cost** — comprehensive observability can be expensive
- **Without team buy-in** — requires cultural adoption
- **For compliance-only** — observability is for operational excellence, not just compliance

---

## Inputs

### Required

- **System architecture** — components, services, dependencies
- **SLOs/SLAs** — service level objectives and agreements
- **User journeys** — critical paths through the system
- **Failure modes** — known and potential failure scenarios
- **Team capabilities** — skills and tools available

### Optional

- **Existing monitoring** — current observability setup
- **Incident history** — past incidents and debugging challenges
- **Performance requirements** — latency, throughput targets
- **Compliance requirements** — regulatory logging needs
- **Budget constraints** — cost limitations
- **Tool preferences** — existing tooling investments

---

## Expected Outputs

### Primary Deliverables

1. **Observability Strategy Document**
   - Logging strategy
   - Metrics strategy
   - Tracing strategy
   - Alerting strategy

2. **Instrumentation Plan**
   - What to instrument
   - How to instrument
   - Standardization guidelines

3. **Dashboard Designs**
   - Service health dashboards
   - Business metrics dashboards
   - SLO dashboards

4. **Alert Definitions**
   - Critical alerts
   - Warning alerts
   - Alert routing

### Supporting Artifacts

- **Tool selection** — recommended observability tools
- **Implementation guide** — step-by-step instrumentation
- **Runbooks** — how to use observability for debugging
- **Cost estimates** — observability infrastructure costs
- **Training plan** — team enablement

---

## Workflow

### Step 1: Define Observability Requirements

**Objective:** Understand what needs to be observed and why.

**Actions:**
- Identify critical user journeys
- Define SLOs for each service
- List known failure modes
- Identify debugging pain points
- Define compliance requirements
- Establish observability goals

**Quality Check:**
- [ ] Critical paths identified
- [ ] SLOs defined
- [ ] Failure modes documented
- [ ] Goals are measurable

### Step 2: Design Logging Strategy

**Objective:** Define what, how, and where to log.

**Actions:**
- Define log levels (DEBUG, INFO, WARN, ERROR)
- Establish structured logging format (JSON)
- Define log retention policies
- Identify sensitive data to redact
- Choose centralized logging system
- Define log aggregation strategy

**Quality Check:**
- [ ] Log levels defined
- [ ] Structured format chosen
- [ ] Retention policy set
- [ ] PII handling defined
- [ ] Centralization planned

### Step 3: Design Metrics Strategy

**Objective:** Define metrics to collect and monitor.

**Actions:**
- Identify RED metrics (Rate, Errors, Duration)
- Define USE metrics (Utilization, Saturation, Errors)
- Establish business metrics
- Define metric naming conventions
- Choose metrics storage (Prometheus, Datadog, etc.)
- Define metric retention

**Quality Check:**
- [ ] RED metrics defined
- [ ] USE metrics defined
- [ ] Business metrics identified
- [ ] Naming conventions set
- [ ] Storage chosen

### Step 4: Design Tracing Strategy

**Objective:** Enable distributed request tracing.

**Actions:**
- Choose tracing standard (OpenTelemetry)
- Define trace sampling strategy
- Identify critical traces to capture
- Design trace context propagation
- Choose tracing backend (Jaeger, Zipkin)
- Define trace retention

**Quality Check:**
- [ ] Tracing standard chosen
- [ ] Sampling strategy defined
- [ ] Critical traces identified
- [ ] Context propagation designed
- [ ] Backend selected

### Step 5: Design Alerting Strategy

**Objective:** Define when and how to alert.

**Actions:**
- Define alert categories (critical, warning, info)
- Establish alert thresholds
- Design alert routing (who gets what)
- Define alert fatigue prevention
- Create alert templates
- Establish on-call rotation

**Quality Check:**
- [ ] Alert categories defined
- [ ] Thresholds set
- [ ] Routing configured
- [ ] Fatigue prevention planned
- [ ] On-call rotation established

### Step 6: Design Dashboards

**Objective:** Create visual representations of system health.

**Actions:**
- Design service health dashboards
- Create SLO dashboards
- Build business metrics dashboards
- Design debugging dashboards
- Establish dashboard standards
- Plan dashboard organization

**Quality Check:**
- [ ] Service dashboards designed
- [ ] SLO dashboards created
- [ ] Business dashboards planned
- [ ] Standards established
- [ ] Organization logical

### Step 7: Select Observability Tools

**Objective:** Choose the right tools for the job.

**Actions:**
- Evaluate logging tools (ELK, Splunk, CloudWatch)
- Evaluate metrics tools (Prometheus, Datadog, New Relic)
- Evaluate tracing tools (Jaeger, Zipkin, Lightstep)
- Consider all-in-one platforms (Datadog, New Relic, Dynatrace)
- Evaluate cost vs. capabilities
- Plan tool integration

**Quality Check:**
- [ ] Tools evaluated
- [ ] Cost analyzed
- [ ] Capabilities matched to needs
- [ ] Integration planned
- [ ] Decision documented

### Step 8: Create Instrumentation Standards

**Objective:** Standardize how to instrument code.

**Actions:**
- Define logging standards (what to log, how to log)
- Define metric standards (naming, labels)
- Define tracing standards (span naming, attributes)
- Create code examples
- Build instrumentation libraries
- Document best practices

**Quality Check:**
- [ ] Logging standards documented
- [ ] Metric standards defined
- [ ] Tracing standards set
- [ ] Examples provided
- [ ] Libraries created

### Step 9: Plan Implementation

**Objective:** Create rollout plan for observability.

**Actions:**
- Prioritize services to instrument
- Define implementation phases
- Assign ownership
- Estimate effort
- Plan training
- Define success criteria

**Quality Check:**
- [ ] Services prioritized
- [ ] Phases defined
- [ ] Owners assigned
- [ ] Effort estimated
- [ ] Training planned

### Step 10: Document and Communicate

**Objective:** Share observability strategy and standards.

**Actions:**
- Write observability strategy document
- Create instrumentation guide
- Build runbooks for common scenarios
- Conduct team training
- Publish documentation
- Gather feedback

**Quality Check:**
- [ ] Strategy documented
- [ ] Guide created
- [ ] Runbooks written
- [ ] Training conducted
- [ ] Documentation published

---

## Decision Framework

### Logging vs. Metrics vs. Tracing

**Use Logs for:**
- Detailed event information
- Debugging specific requests
- Audit trails
- Error details

**Use Metrics for:**
- Aggregated data
- Trends over time
- Alerting
- SLO tracking

**Use Traces for:**
- Request flow across services
- Latency breakdown
- Dependency mapping
- Performance optimization

### Sampling Strategy

**Always Sample:**
- Errors and exceptions
- Slow requests (> threshold)
- Critical user journeys

**Sample Randomly:**
- Normal requests (1-10%)
- Background jobs

**Never Sample:**
- Security events
- Compliance-required logs

---

## Quality Checklist

### Logging
- [ ] Structured logging implemented
- [ ] Log levels used appropriately
- [ ] Sensitive data redacted
- [ ] Correlation IDs included
- [ ] Centralized aggregation
- [ ] Retention policy defined

### Metrics
- [ ] RED metrics captured
- [ ] USE metrics captured
- [ ] Business metrics defined
- [ ] Naming conventions followed
- [ ] Labels used appropriately
- [ ] Cardinality controlled

### Tracing
- [ ] Distributed tracing enabled
- [ ] Trace context propagated
- [ ] Sampling strategy implemented
- [ ] Critical paths traced
- [ ] Span attributes meaningful

### Alerting
- [ ] Alerts actionable
- [ ] Thresholds appropriate
- [ ] Routing configured
- [ ] Runbooks linked
- [ ] Alert fatigue prevented

### Dashboards
- [ ] Service health visible
- [ ] SLOs tracked
- [ ] Business metrics shown
- [ ] Dashboards organized
- [ ] Access controlled

---

## Common Mistakes

### 1. Logging Everything

**Problem:** Excessive logging increases cost and noise.

**Solution:** Log strategically. Use appropriate log levels.

### 2. High Cardinality Metrics

**Problem:** Too many unique label combinations explode storage.

**Solution:** Limit label cardinality. Use logs for high-cardinality data.

### 3. No Sampling Strategy

**Problem:** Tracing everything is expensive and unnecessary.

**Solution:** Sample intelligently. Always trace errors and slow requests.

### 4. Alert Fatigue

**Problem:** Too many alerts lead to ignored alerts.

**Solution:** Alert only on actionable issues. Use warning vs. critical.

### 5. No Correlation IDs

**Problem:** Can't trace requests across services.

**Solution:** Generate and propagate correlation IDs.

### 6. Ignoring Cost

**Problem:** Observability costs spiral out of control.

**Solution:** Monitor observability costs. Optimize retention and sampling.

### 7. No Standardization

**Problem:** Each service instruments differently.

**Solution:** Create and enforce instrumentation standards.

### 8. Observability as Afterthought

**Problem:** Hard to add observability after the fact.

**Solution:** Design observability from the start.

### 9. No Training

**Problem:** Team doesn't know how to use observability tools.

**Solution:** Provide training and documentation.

### 10. No SLOs

**Problem:** Don't know what to monitor or alert on.

**Solution:** Define SLOs first, then design observability around them.

---

## Examples

### Example 1: E-commerce Platform Observability

**Requirements:**
- 99.9% availability SLO
- < 500ms p95 latency
- Microservices architecture (15 services)

**Logging Strategy:**
- Structured JSON logs
- Centralized in ELK stack
- 30-day retention
- Correlation ID in all logs

**Metrics Strategy:**
- Prometheus for metrics
- RED metrics for all services
- Business metrics (orders/min, revenue)
- 90-day retention

**Tracing Strategy:**
- OpenTelemetry
- 10% sampling for normal requests
- 100% sampling for errors
- Jaeger backend

**Alerting:**
- Critical: Error rate > 1%, Latency > 1s
- Warning: Error rate > 0.5%, Latency > 500ms
- PagerDuty for critical, Slack for warnings

### Example 2: SaaS Application Observability

**Requirements:**
- Multi-tenant SaaS
- 99.95% availability SLO
- Compliance (SOC 2, GDPR)

**Logging Strategy:**
- Structured logs with tenant ID
- PII redaction
- Splunk for centralization
- 1-year retention for compliance

**Metrics Strategy:**
- Datadog for metrics and APM
- Per-tenant metrics
- Resource utilization metrics
- 1-year retention

**Tracing Strategy:**
- Datadog APM
- 5% sampling
- 100% for slow requests (> 2s)
- 30-day retention

**Alerting:**
- Critical: Service down, Error rate > 2%
- Warning: Latency degradation, Resource saturation
- Opsgenie for on-call

### Example 3: Microservices Platform

**Requirements:**
- 50+ microservices
- Kubernetes deployment
- High request volume (10K req/s)

**Logging Strategy:**
- Fluentd for log collection
- Elasticsearch for storage
- 7-day retention (cost optimization)
- Structured JSON with service name, pod ID

**Metrics Strategy:**
- Prometheus + Thanos for long-term storage
- RED metrics for all services
- USE metrics for infrastructure
- 90-day retention

**Tracing Strategy:**
- OpenTelemetry
- 1% sampling (high volume)
- 100% for errors and slow requests
- Tempo backend

**Alerting:**
- SLO-based alerting
- Error budget alerts
- Prometheus Alertmanager
- PagerDuty integration

### Example 4: Monolith to Microservices Migration

**Challenge:** Adding observability during migration

**Approach:**
1. Instrument monolith with basic observability
2. Add distributed tracing at API gateway
3. Instrument new microservices with full observability
4. Gradually improve monolith observability

**Key Decisions:**
- Start with critical paths
- Use OpenTelemetry for future-proofing
- Implement correlation IDs early
- Build dashboards showing both monolith and microservices

---

## Related Skills

### Prerequisites
- **architecture-discovery** — understand system structure
- **system-design** — design observability into system

### Commonly Followed By
- **incident-analysis** — use observability for incident response
- **production-readiness** — validate observability before production
- **capacity-planning** — use metrics for capacity planning

### Related Skills
- **agent-observability** — observability for AI agent workflows
- **reliability-analysis** — use observability data for reliability
- **performance-optimization** — use observability to find bottlenecks

---

## Skill Composition

### Production-Ready System Workflow

```
System Design
      ↓
observability-design (this skill)
      ↓
Implement Instrumentation
      ↓
production-readiness
      ↓
Deploy to Production
      ↓
incident-analysis (when incidents occur)
```

---

## Evaluation Criteria

### Excellent
- Comprehensive observability across all three pillars (logs, metrics, traces)
- SLO-based alerting
- Low alert fatigue
- Fast debugging (< 30 min to identify root cause)
- Cost-effective

### Good
- Solid logging and metrics
- Basic tracing
- Alerts mostly actionable
- Debugging time reasonable (< 1 hour)

### Needs Improvement
- Gaps in observability
- High alert fatigue
- Long debugging time (> 2 hours)
- High cost
- No standardization

---

## Tags

`operations`, `observability`, `monitoring`, `logging`, `metrics`, `tracing`, `alerting`, `debugging`, `sre`, `production`, `instrumentation`

---

## Version

**1.0.0** — Initial release
