# Observability Design: Step-by-Step Instructions

This guide provides detailed instructions for designing comprehensive observability systems using the three pillars: logs, metrics, and traces.

---

## Overview

**Total Estimated Time:** 4-8 hours  
**Complexity:** Advanced  
**Prerequisites:** System architecture understanding, SLO definitions

---

## Step 1: Define Observability Requirements (30-60 minutes)

### Objective

Understand what needs to be observed and why, establishing clear observability goals aligned with business and operational needs.

### Actions

#### 1.1 Identify Critical User Journeys

**What to do:**
- Map end-to-end user flows through your system
- Identify the most critical paths (e.g., checkout, login, data processing)
- Document dependencies for each journey

**Example:**
```
Critical Journey: E-commerce Checkout
1. User adds item to cart (Frontend → Cart Service)
2. User proceeds to checkout (Frontend → Checkout Service)
3. Payment processing (Checkout → Payment Gateway)
4. Order creation (Checkout → Order Service → Database)
5. Inventory update (Order Service → Inventory Service)
6. Confirmation email (Order Service → Notification Service)
```

#### 1.2 Define SLOs for Each Service

**What to do:**
- Establish availability targets (e.g., 99.9%)
- Define latency targets (e.g., p95 < 500ms)
- Set error rate thresholds (e.g., < 0.1%)
- Document throughput requirements

**Template:**
```
Service: Checkout Service
- Availability: 99.95% (21.6 min downtime/month)
- Latency: p50 < 200ms, p95 < 500ms, p99 < 1s
- Error Rate: < 0.1%
- Throughput: 1000 req/s peak
```

#### 1.3 List Known and Potential Failure Modes

**What to do:**
- Review past incidents
- Identify single points of failure
- Document dependency failures
- List resource exhaustion scenarios

**Categories:**
- Infrastructure failures (server down, network partition)
- Dependency failures (database timeout, third-party API down)
- Resource exhaustion (memory leak, connection pool exhaustion)
- Configuration errors (bad deployment, incorrect settings)
- Data issues (corrupt data, schema mismatch)

#### 1.4 Identify Current Debugging Pain Points

**Questions to ask:**
- How long does it take to identify root cause of incidents?
- What information is missing during debugging?
- Which services are "black boxes"?
- Where do you lack visibility?

#### 1.5 Define Compliance and Regulatory Requirements

**What to consider:**
- Log retention requirements (e.g., SOC 2, GDPR)
- PII handling and redaction
- Audit trail requirements
- Data residency constraints

#### 1.6 Establish Observability Goals

**Example goals:**
- Reduce mean time to detection (MTTD) to < 5 minutes
- Reduce mean time to resolution (MTTR) to < 30 minutes
- Achieve 100% visibility into critical user journeys
- Enable proactive issue detection before user impact

### Quality Checklist

- [ ] All critical user journeys documented with dependencies
- [ ] SLOs defined for all services (availability, latency, error rate)
- [ ] Known failure modes cataloged
- [ ] Debugging pain points identified
- [ ] Compliance requirements documented
- [ ] Observability goals are specific and measurable
- [ ] Stakeholder buy-in obtained

### Common Pitfalls

- **Too broad:** Trying to observe everything leads to noise
- **No SLOs:** Without SLOs, you don't know what to monitor
- **Ignoring compliance:** Leads to expensive retrofitting later

### Outputs

- Observability requirements document
- Critical user journey map
- SLO definitions
- Failure mode catalog

---

## Step 2: Design Logging Strategy (45-90 minutes)

### Objective

Define what, how, and where to log to enable effective debugging and compliance.

### Actions

#### 2.1 Define Log Levels and When to Use Them

**Standard log levels:**

```
DEBUG: Detailed diagnostic information (disabled in production)
  Example: "User query parameters: {params}"

INFO: General informational messages about application flow
  Example: "Order 12345 created successfully"

WARN: Potentially harmful situations that aren't errors
  Example: "Payment gateway response time exceeded 2s"

ERROR: Error events that might still allow the application to continue
  Example: "Failed to send confirmation email, will retry"

FATAL/CRITICAL: Severe errors causing application shutdown
  Example: "Database connection pool exhausted, shutting down"
```

**Guidelines:**
- Use INFO for business events (order created, user registered)
- Use WARN for degraded performance or fallback scenarios
- Use ERROR for exceptions and failures
- Never use DEBUG in production (performance and cost)

#### 2.2 Establish Structured Logging Format

**Why structured logging:**
- Easy to parse and query
- Consistent format across services
- Enables log aggregation and analysis

**Recommended format: JSON**

```json
{
  "timestamp": "2026-09-09T10:15:30.123Z",
  "level": "INFO",
  "service": "checkout-service",
  "environment": "production",
  "correlation_id": "abc123-def456-ghi789",
  "user_id": "user_12345",
  "message": "Order created successfully",
  "order_id": "order_67890",
  "amount": 99.99,
  "currency": "USD",
  "duration_ms": 245
}
```

**Required fields:**
- `timestamp`: ISO 8601 format with milliseconds
- `level`: Log level
- `service`: Service name
- `environment`: production, staging, development
- `correlation_id`: Request trace ID
- `message`: Human-readable message

**Optional but recommended:**
- `user_id`: For user-specific debugging
- `session_id`: For session tracking
- `version`: Service version
- `host`: Hostname or pod ID

#### 2.3 Define Log Retention Policies

**Factors to consider:**
- Compliance requirements (may require 1+ years)
- Debugging needs (typically 7-30 days)
- Storage costs
- Query performance

**Tiered retention strategy:**
```
Hot storage (fast queries): 7 days
Warm storage (slower queries): 30 days
Cold storage (archive): 1 year
Deleted: After 1 year
```

**Exception: Critical logs**
- Security events: Longer retention (1-7 years)
- Audit logs: Per compliance requirements
- Error logs: May warrant longer retention

#### 2.4 Identify Sensitive Data to Redact

**PII and sensitive data:**
- Passwords, API keys, tokens
- Credit card numbers
- Social Security Numbers
- Email addresses (depends on compliance)
- IP addresses (GDPR consideration)
- Health information (HIPAA)

**Redaction strategies:**
```javascript
// Before redaction
{ "credit_card": "4532-1234-5678-9010" }

// After redaction
{ "credit_card": "4532-****-****-9010" }

// Or complete redaction
{ "credit_card": "[REDACTED]" }
```

**Implementation:**
- Use logging library features (e.g., Winston redaction)
- Implement at application level, not log aggregation
- Test redaction thoroughly

#### 2.5 Choose Centralized Logging System

**Options:**

**ELK Stack (Elasticsearch, Logstash, Kibana)**
- Pros: Open source, powerful search, flexible
- Cons: Complex to operate, resource-intensive
- Best for: Self-hosted, high volume

**Splunk**
- Pros: Powerful, enterprise features, excellent UI
- Cons: Expensive, complex licensing
- Best for: Large enterprises, compliance-heavy

**CloudWatch Logs (AWS)**
- Pros: Native AWS integration, simple setup
- Cons: Limited query capabilities, AWS-only
- Best for: AWS-native applications

**Datadog Logs**
- Pros: Integrated with metrics/traces, great UI
- Cons: Can be expensive at scale
- Best for: Unified observability platform

**Grafana Loki**
- Pros: Cost-effective, integrates with Prometheus
- Cons: Limited query features vs. Elasticsearch
- Best for: Kubernetes, cost-conscious

#### 2.6 Define Log Aggregation Strategy

**Collection methods:**

**1. Agent-based (recommended for containers/VMs)**
```
Application → Log File → Fluentd/Filebeat → Centralized System
```

**2. Direct shipping**
```
Application → HTTP/TCP → Centralized System
```

**3. Sidecar pattern (Kubernetes)**
```
Application → stdout → Sidecar Container → Centralized System
```

**Best practices:**
- Use buffering to handle backpressure
- Implement retry logic
- Monitor log shipping pipeline itself
- Use compression for network efficiency

### Quality Checklist

- [ ] Log levels defined with clear usage guidelines
- [ ] Structured logging format (JSON) standardized
- [ ] Required fields documented (timestamp, level, service, correlation_id)
- [ ] Retention policy defined (hot/warm/cold storage)
- [ ] PII and sensitive data redaction implemented
- [ ] Centralized logging system selected
- [ ] Log aggregation pipeline designed
- [ ] Cost estimates calculated
- [ ] Team trained on logging standards

### Common Pitfalls

- **Logging too much:** Increases cost and noise
- **Logging too little:** Missing critical debugging information
- **No correlation IDs:** Can't trace requests across services
- **Logging sensitive data:** Compliance and security risk
- **Inconsistent formats:** Makes aggregation difficult

### Outputs

- Logging strategy document
- Structured logging format specification
- Log retention policy
- PII redaction guidelines
- Centralized logging system selection

---

## Step 3: Design Metrics Strategy (60-90 minutes)

### Objective

Define metrics to collect and monitor for understanding system health, performance, and business outcomes.

### Actions

#### 3.1 Identify RED Metrics (Request-based Services)

**RED = Rate, Errors, Duration**

**Rate:** Request throughput
```
Metric: http_requests_total
Type: Counter
Labels: service, endpoint, method, status_code

Example query (requests per second):
rate(http_requests_total[5m])
```

**Errors:** Request error rate
```
Metric: http_requests_total{status_code=~"5.."}
Type: Counter
Labels: service, endpoint, method, status_code

Example query (error rate):
sum(rate(http_requests_total{status_code=~"5.."}[5m])) 
/ 
sum(rate(http_requests_total[5m]))
```

**Duration:** Request latency
```
Metric: http_request_duration_seconds
Type: Histogram
Labels: service, endpoint, method

Example query (p95 latency):
histogram_quantile(0.95, 
  rate(http_request_duration_seconds_bucket[5m])
)
```

**Apply RED to all request-based services:**
- API services
- Web servers
- RPC services
- Message consumers

#### 3.2 Define USE Metrics (Resource-based)

**USE = Utilization, Saturation, Errors**

**Utilization:** How busy is the resource?
```
CPU: cpu_usage_percent
Memory: memory_usage_percent
Disk: disk_usage_percent
Network: network_bandwidth_utilization_percent
```

**Saturation:** How much queued work?
```
CPU: load_average (> CPU cores indicates saturation)
Memory: swap_usage
Disk: disk_io_queue_length
Network: network_packet_drops
```

**Errors:** Resource errors
```
Disk: disk_read_errors, disk_write_errors
Network: network_errors, network_packet_loss
Memory: oom_kills
```

**Apply USE to:**
- Servers/VMs
- Containers
- Databases
- Message queues
- Load balancers

#### 3.3 Establish Business Metrics

**Why business metrics:**
- Connect technical metrics to business outcomes
- Enable product and business teams to monitor health
- Detect issues that don't show up in technical metrics

**Examples:**

**E-commerce:**
```
orders_created_total (counter)
order_value_dollars (histogram)
checkout_abandonment_rate (gauge)
active_users (gauge)
revenue_per_minute (gauge)
```

**SaaS:**
```
user_signups_total (counter)
active_subscriptions (gauge)
churn_rate (gauge)
feature_usage_total (counter)
api_calls_per_customer (histogram)
```

**Media/Content:**
```
video_plays_total (counter)
video_watch_duration_seconds (histogram)
concurrent_viewers (gauge)
buffering_events_total (counter)
```

#### 3.4 Define Metric Naming Conventions

**Prometheus-style naming (recommended):**

```
<namespace>_<subsystem>_<name>_<unit>

Examples:
http_requests_total
http_request_duration_seconds
database_connections_active
cache_hits_total
queue_messages_pending
```

**Rules:**
- Use snake_case
- Include unit in name (seconds, bytes, total)
- Counters end in `_total`
- Use base units (seconds not milliseconds, bytes not megabytes)
- Be consistent across services

#### 3.5 Define Label Strategy

**Labels add dimensions to metrics:**

```
http_requests_total{
  service="checkout",
  environment="production",
  endpoint="/api/orders",
  method="POST",
  status_code="200"
}
```

**Best practices:**
- Use labels for dimensions you want to filter/group by
- Keep cardinality low (< 100 unique values per label)
- Avoid high-cardinality labels (user_id, request_id)
- Use consistent label names across services

**Common labels:**
- `service`: Service name
- `environment`: production, staging, development
- `region`: AWS region, datacenter
- `version`: Service version
- `endpoint`: API endpoint
- `method`: HTTP method
- `status_code`: HTTP status code

**High-cardinality warning:**
```
❌ BAD: user_id="12345" (millions of users)
✅ GOOD: user_tier="premium" (few tiers)

❌ BAD: request_id="abc-123-def" (unique per request)
✅ GOOD: endpoint="/api/orders" (limited endpoints)
```

#### 3.6 Choose Metrics Storage System

**Options:**

**Prometheus**
- Pros: Open source, powerful query language (PromQL), pull-based
- Cons: Limited long-term storage, single-node scalability
- Best for: Kubernetes, self-hosted, cost-conscious
- Long-term storage: Thanos, Cortex, Mimir

**Datadog**
- Pros: Unified platform (metrics + logs + traces), great UI
- Cons: Can be expensive, vendor lock-in
- Best for: Unified observability, fast setup

**New Relic**
- Pros: Comprehensive APM, good UI, easy setup
- Cons: Expensive at scale
- Best for: APM-focused, application monitoring

**CloudWatch (AWS)**
- Pros: Native AWS integration, simple
- Cons: Limited query capabilities, AWS-only
- Best for: AWS-native applications

**InfluxDB**
- Pros: Time-series optimized, SQL-like query language
- Cons: Less ecosystem than Prometheus
- Best for: IoT, high-frequency metrics

#### 3.7 Define Metric Retention

**Retention strategy:**

```
Raw data (15s resolution): 7 days
Downsampled (1m resolution): 30 days
Downsampled (5m resolution): 90 days
Downsampled (1h resolution): 1 year
```

**Considerations:**
- Query performance degrades with longer retention
- Storage costs increase with retention
- Compliance may require longer retention
- Use downsampling for long-term storage

### Quality Checklist

- [ ] RED metrics defined for all request-based services
- [ ] USE metrics defined for all resources
- [ ] Business metrics identified and documented
- [ ] Metric naming conventions established
- [ ] Label strategy defined with cardinality limits
- [ ] Metrics storage system selected
- [ ] Retention policy defined with downsampling strategy
- [ ] Cost estimates calculated
- [ ] Metric standards documented

### Common Pitfalls

- **High cardinality:** Explodes storage and query performance
- **Too many metrics:** Increases cost and complexity
- **Inconsistent naming:** Makes queries difficult
- **No business metrics:** Can't connect to business outcomes
- **Wrong metric type:** Using gauge instead of counter, etc.

### Outputs

- Metrics strategy document
- RED metrics definition
- USE metrics definition
- Business metrics catalog
- Naming conventions guide
- Label strategy
- Metrics storage selection

---

## Step 4: Design Tracing Strategy (45-75 minutes)

### Objective

Enable distributed request tracing to understand request flow, latency breakdown, and dependencies across services.

### Actions

#### 4.1 Choose Tracing Standard

**Recommended: OpenTelemetry (OTel)**

**Why OpenTelemetry:**
- Vendor-neutral standard
- Wide language support
- Unified instrumentation (metrics, logs, traces)
- Future-proof (industry standard)
- Automatic instrumentation available

**Alternatives:**
- OpenTracing (deprecated, merged into OpenTelemetry)
- Vendor-specific (Datadog APM, New Relic)

**OpenTelemetry components:**
- **SDK:** Instrument your code
- **Auto-instrumentation:** Automatic tracing for frameworks
- **Collector:** Receive, process, export traces
- **Exporters:** Send to backend (Jaeger, Zipkin, Datadog)

#### 4.2 Define Trace Sampling Strategy

**Why sampling:**
- Tracing every request is expensive (storage, network)
- High-volume services generate millions of traces
- Most requests are similar; sampling provides sufficient insight

**Sampling strategies:**

**1. Head-based sampling (decision at trace start):**

```
Always sample (100%):
- Errors (status code 5xx)
- Slow requests (duration > threshold)
- Specific endpoints (critical paths)

Probability-based sampling:
- 10% of normal requests (high volume)
- 50% of normal requests (medium volume)
- 100% of normal requests (low volume)
```

**2. Tail-based sampling (decision after trace completes):**

```
Sample if:
- Trace contains error
- Trace duration > p95 threshold
- Trace contains specific operation
- Random sample (X%)
```

**Recommended approach:**
```
Head-based sampling:
- 100% errors
- 100% slow requests (> p95)
- 10% random sample

Tail-based sampling (if supported):
- 100% traces with errors
- 100% traces > p99 latency
- 5% random sample
```

**Sampling configuration example:**
```yaml
sampling:
  # Always sample errors
  - type: always_on
    condition: status_code >= 500
  
  # Always sample slow requests
  - type: always_on
    condition: duration > 1s
  
  # Sample 10% of everything else
  - type: probabilistic
    rate: 0.1
```

#### 4.3 Identify Critical Traces to Capture

**Critical user journeys:**
- Checkout flow (add to cart → payment → order confirmation)
- User authentication (login → session creation)
- Data processing pipelines (upload → process → store)
- Search and discovery (search → results → detail view)

**For each journey, define:**
- Entry point (e.g., API Gateway)
- All services involved
- Expected latency
- Critical operations to trace

**Example: Checkout flow**
```
Trace: checkout_flow
Entry: POST /api/checkout
Services:
  1. API Gateway (5ms)
  2. Checkout Service (50ms)
  3. Payment Service (200ms)
  4. Order Service (30ms)
  5. Inventory Service (20ms)
  6. Notification Service (10ms)
Total expected: ~315ms

Critical spans:
- payment_gateway_call (external, may be slow)
- database_transaction (may lock)
- inventory_update (may fail)
```

#### 4.4 Design Trace Context Propagation

**What is trace context:**
- Trace ID: Unique identifier for entire request
- Span ID: Unique identifier for operation
- Parent Span ID: Links spans into tree

**Propagation methods:**

**1. HTTP headers (W3C Trace Context standard):**
```
traceparent: 00-{trace-id}-{parent-span-id}-{flags}
tracestate: vendor1=value1,vendor2=value2
```

**2. Message queue metadata:**
```json
{
  "headers": {
    "traceparent": "00-abc123...",
    "tracestate": "..."
  },
  "body": { ... }
}
```

**3. gRPC metadata:**
```
grpc-trace-bin: <binary trace context>
```

**Implementation checklist:**
- [ ] Extract trace context from incoming requests
- [ ] Propagate trace context to downstream services
- [ ] Generate new span for each operation
- [ ] Link spans with parent-child relationships
- [ ] Add span attributes (service, operation, status)

**Code example (Node.js with OpenTelemetry):**
```javascript
const { trace, context } = require('@opentelemetry/api');

// Extract context from incoming request
const extractedContext = propagation.extract(
  context.active(),
  req.headers
);

// Create span in extracted context
const span = tracer.startSpan('process_order', {
  kind: SpanKind.SERVER,
  attributes: {
    'service.name': 'order-service',
    'order.id': orderId,
    'user.id': userId
  }
}, extractedContext);

// Propagate context to downstream call
const headers = {};
propagation.inject(context.active(), headers);
await fetch('http://inventory-service/update', { headers });

span.end();
```

#### 4.5 Choose Tracing Backend

**Options:**

**Jaeger**
- Pros: Open source, CNCF project, good UI, scalable
- Cons: Requires infrastructure management
- Best for: Kubernetes, self-hosted, OpenTelemetry

**Zipkin**
- Pros: Open source, simple, mature
- Cons: Less active development than Jaeger
- Best for: Simple setups, legacy systems

**Datadog APM**
- Pros: Unified platform, excellent UI, automatic instrumentation
- Cons: Expensive, vendor lock-in
- Best for: Unified observability, fast setup

**New Relic**
- Pros: Comprehensive APM, good UI
- Cons: Expensive
- Best for: APM-focused

**AWS X-Ray**
- Pros: Native AWS integration, simple
- Cons: AWS-only, limited features
- Best for: AWS-native applications

**Grafana Tempo**
- Pros: Cost-effective, integrates with Grafana, object storage
- Cons: Newer, fewer features than Jaeger
- Best for: Cost-conscious, Grafana users

**Lightstep**
- Pros: Advanced features, tail-based sampling, excellent performance
- Cons: Expensive
- Best for: Large-scale, high-volume

#### 4.6 Define Trace Retention

**Retention strategy:**
```
Recent traces (full detail): 7 days
Sampled traces: 30 days
Aggregated trace data: 90 days
```

**Considerations:**
- Traces are larger than metrics (more storage)
- Query performance degrades with age
- Compliance may require longer retention
- Use tail-based sampling for important traces

#### 4.7 Define Span Attributes

**Standard attributes:**
```
service.name: "order-service"
service.version: "1.2.3"
deployment.environment: "production"
```

**HTTP spans:**
```
http.method: "POST"
http.url: "/api/orders"
http.status_code: 200
http.user_agent: "..."
```

**Database spans:**
```
db.system: "postgresql"
db.name: "orders_db"
db.operation: "SELECT"
db.statement: "SELECT * FROM orders WHERE id = $1"
```

**Custom attributes:**
```
order.id: "12345"
user.id: "user_67890"
payment.method: "credit_card"
```

**Best practices:**
- Use semantic conventions (OpenTelemetry standard)
- Add business context (order_id, user_id)
- Avoid high-cardinality attributes in sampling decisions
- Redact sensitive data

### Quality Checklist

- [ ] Tracing standard chosen (OpenTelemetry recommended)
- [ ] Sampling strategy defined (always sample errors/slow, probabilistic for normal)
- [ ] Critical user journeys identified for tracing
- [ ] Trace context propagation designed (W3C Trace Context)
- [ ] Tracing backend selected
- [ ] Trace retention policy defined
- [ ] Span attributes standardized (semantic conventions)
- [ ] Sensitive data redaction implemented
- [ ] Cost estimates calculated

### Common Pitfalls

- **No sampling:** Tracing everything is expensive
- **Over-sampling:** Missing important traces due to low sample rate
- **Broken context propagation:** Traces split across services
- **High-cardinality attributes:** Increases storage costs
- **No span attributes:** Traces lack context for debugging

### Outputs

- Tracing strategy document
- Sampling configuration
- Critical traces catalog
- Trace context propagation guide
- Tracing backend selection
- Span attributes standards

---

## Step 5: Design Alerting Strategy (45-75 minutes)

### Objective

Define when and how to alert to ensure timely response to issues while avoiding alert fatigue.

### Actions

#### 5.1 Define Alert Categories

**Critical (P0): Immediate action required**
- Service completely down
- Error rate > threshold (e.g., > 5%)
- SLO breach (availability < target)
- Security incident
- Data loss risk

**Notification:** Page on-call engineer immediately (PagerDuty, Opsgenie)

**Warning (P1): Action required soon**
- Error rate elevated (e.g., > 1%)
- Latency degraded (p95 > threshold)
- Resource saturation (CPU > 80%)
- SLO at risk (error budget burning fast)

**Notification:** Slack, email, ticket

**Info (P2): Awareness only**
- Deployment completed
- Scheduled maintenance
- Configuration change

**Notification:** Slack, email

#### 5.2 Establish Alert Thresholds

**SLO-based alerting (recommended):**

```
SLO: 99.9% availability (43 min downtime/month)
Error budget: 0.1% errors allowed

Alert if:
- Error rate > 1% for 5 minutes (critical)
- Error budget burn rate > 10x (warning)
```

**Threshold guidelines:**

**Availability:**
```
Critical: Service down (0 successful requests in 5 min)
Warning: Availability < 99% over 10 min
```

**Error rate:**
```
Critical: Error rate > 5% for 5 min
Warning: Error rate > 1% for 10 min
```

**Latency:**
```
Critical: p95 latency > 2x SLO for 5 min
Warning: p95 latency > 1.5x SLO for 10 min
```

**Resource utilization:**
```
Critical: CPU > 90% for 10 min, Memory > 95%
Warning: CPU > 80% for 15 min, Memory > 85%
```

**Best practices:**
- Use "for" duration to avoid flapping (e.g., "for 5 minutes")
- Set thresholds based on SLOs, not arbitrary numbers
- Use percentiles (p95, p99) not averages for latency
- Test thresholds with historical data

#### 5.3 Design Alert Routing

**Who gets what alerts:**

```
Critical (P0):
- On-call engineer (page)
- Incident channel (Slack)
- Engineering manager (SMS)

Warning (P1):
- Team channel (Slack)
- Service owner (email)
- Ticket system (Jira)

Info (P2):
- Team channel (Slack)
- Email digest
```

**Routing rules:**
```yaml
routes:
  # Critical alerts
  - match:
      severity: critical
    receiver: pagerduty-oncall
    continue: true
  
  - match:
      severity: critical
    receiver: slack-incidents
  
  # Warning alerts
  - match:
      severity: warning
    receiver: slack-team
    continue: true
  
  - match:
      severity: warning
      service: checkout
    receiver: checkout-team-email
```

#### 5.4 Define Alert Fatigue Prevention

**Strategies:**

**1. Alert on symptoms, not causes:**
```
❌ BAD: Alert on high CPU (cause)
✅ GOOD: Alert on high latency (symptom)

Reason: High CPU may not impact users; high latency does
```

**2. Use alert grouping:**
```
Group related alerts:
- By service
- By time window (5 min)
- By root cause

Result: 10 alerts → 1 grouped alert
```

**3. Implement alert suppression:**
```
Suppress alerts during:
- Scheduled maintenance
- Known incidents (avoid duplicate alerts)
- Deployment windows (if expected)
```

**4. Use escalation policies:**
```
Escalation:
1. Primary on-call (immediate)
2. Secondary on-call (after 10 min)
3. Engineering manager (after 20 min)
```

**5. Regular alert review:**
```
Weekly:
- Review all alerts fired
- Identify noisy alerts
- Tune or remove
- Track alert-to-incident ratio

Goal: > 50% of alerts lead to action
```

#### 5.5 Create Alert Templates

**Alert template structure:**

```yaml
alert: HighErrorRate
severity: critical
description: |
  Error rate for {{ $labels.service }} is {{ $value }}%
  which exceeds the threshold of 5%.
  
runbook: https://wiki.company.com/runbooks/high-error-rate

query: |
  sum(rate(http_requests_total{status_code=~"5.."}[5m])) by (service)
  /
  sum(rate(http_requests_total[5m])) by (service)
  > 0.05

for: 5m

labels:
  severity: critical
  team: platform

annotations:
  summary: "High error rate on {{ $labels.service }}"
  description: "Error rate is {{ $value | humanizePercentage }}"
  dashboard: "https://grafana.company.com/d/service-health"
  runbook: "https://wiki.company.com/runbooks/high-error-rate"
```

**Required fields:**
- Alert name
- Severity
- Description (what's wrong)
- Query (how to detect)
- Threshold
- Duration ("for" clause)
- Runbook link
- Dashboard link

#### 5.6 Establish On-Call Rotation

**On-call schedule:**
```
Rotation: Weekly
Primary on-call: Engineer A (Week 1)
Secondary on-call: Engineer B (Week 1)

Handoff: Monday 9am
Compensation: Time off or pay
```

**On-call responsibilities:**
- Respond to critical alerts within 15 min
- Investigate and mitigate incidents
- Escalate if needed
- Document incident in post-mortem
- Hand off open incidents during rotation change

**On-call best practices:**
- Limit on-call to 1 week at a time
- Provide secondary on-call for backup
- Ensure runbooks are up-to-date
- Conduct on-call training
- Review on-call load regularly (should be < 5 pages/week)

### Quality Checklist

- [ ] Alert categories defined (critical, warning, info)
- [ ] Alert thresholds set based on SLOs
- [ ] Alert routing configured (who gets what)
- [ ] Alert fatigue prevention strategies implemented
- [ ] Alert templates created with runbook links
- [ ] On-call rotation established
- [ ] Escalation policies defined
- [ ] Alert review process in place
- [ ] Runbooks created for common alerts

### Common Pitfalls

- **Too many alerts:** Leads to alert fatigue and ignored alerts
- **Alerts without runbooks:** Engineers don't know how to respond
- **Alerting on causes not symptoms:** Noisy alerts that don't impact users
- **No escalation:** Alerts go unnoticed
- **Static thresholds:** Don't adapt to traffic patterns

### Outputs

- Alerting strategy document
- Alert definitions (critical, warning, info)
- Alert routing configuration
- Alert templates
- On-call rotation schedule
- Runbooks for common alerts

---

## Step 6: Design Dashboards (45-60 minutes)

### Objective

Create visual representations of system health, performance, and business metrics.

### Actions

#### 6.1 Design Service Health Dashboards

**Purpose:** At-a-glance view of service health

**Key metrics:**
```
REDMetrics:
- Request rate (requests/sec)
- Error rate (%)
- Latency (p50, p95, p99)

Resource metrics:
- CPU utilization (%)
- Memory utilization (%)
- Disk usage (%)

Dependency health:
- Database connection pool
- External API response times
- Message queue depth
```

**Dashboard layout:**
```
+----------------------------------+
| Service: Checkout Service        |
+----------------------------------+
| Request Rate | Error Rate | p95  |
| [graph]      | [graph]    | [graph] |
+----------------------------------+
| CPU          | Memory     | Disk |
| [gauge]      | [gauge]    | [gauge] |
+----------------------------------+
| Database     | Payment API | Queue|
| [status]     | [status]    | [graph]|
+----------------------------------+
```

#### 6.2 Create SLO Dashboards

**Purpose:** Track SLO compliance and error budget

**Key metrics:**
```
SLO compliance:
- Current availability (%)
- Error budget remaining (%)
- Error budget burn rate

Historical:
- 7-day availability
- 30-day availability
- SLO breaches (count)
```

**Dashboard example:**
```
+----------------------------------+
| SLO: 99.9% Availability          |
+----------------------------------+
| Current: 99.95% ✅               |
| Error Budget: 85% remaining      |
| Burn Rate: 0.5x (healthy)        |
+----------------------------------+
| [30-day availability graph]      |
+----------------------------------+
| Recent SLO Breaches:             |
| - 2026-09-01: 99.85% (incident)  |
+----------------------------------+
```

#### 6.3 Build Business Metrics Dashboards

**Purpose:** Connect technical metrics to business outcomes

**E-commerce example:**
```
Business metrics:
- Orders per minute
- Revenue per hour
- Checkout conversion rate
- Cart abandonment rate
- Average order value

Technical correlation:
- Checkout latency vs. conversion rate
- Error rate vs. revenue impact
```

**Dashboard layout:**
```
+----------------------------------+
| Business Metrics                 |
+----------------------------------+
| Orders/min | Revenue/hr | AOV    |
| [graph]    | [graph]    | [gauge]|
+----------------------------------+
| Conversion Rate | Abandonment   |
| [graph]         | [graph]       |
+----------------------------------+
| Impact Analysis:                 |
| Latency +100ms → -2% conversion  |
+----------------------------------+
```

#### 6.4 Design Debugging Dashboards

**Purpose:** Deep-dive into specific services or issues

**Content:**
```
Detailed metrics:
- Per-endpoint latency
- Per-endpoint error rate
- Database query performance
- Cache hit rate
- External API latency breakdown

Log integration:
- Recent errors (log stream)
- Slow queries (log stream)

Trace integration:
- Recent slow traces
- Error traces
```

#### 6.5 Establish Dashboard Standards

**Naming conventions:**
```
[Environment] [Service] - [Purpose]

Examples:
Production Checkout - Service Health
Production - SLO Dashboard
Staging API Gateway - Debugging
```

**Color standards:**
```
Green: Healthy (< 1% error rate, < SLO latency)
Yellow: Warning (1-5% error rate, > SLO latency)
Red: Critical (> 5% error rate, service down)
Blue: Informational
```

**Layout standards:**
```
Top row: Most important metrics (RED)
Middle rows: Supporting metrics (USE, dependencies)
Bottom rows: Detailed metrics, logs, traces

Time range: Last 1 hour (default), with selector
Refresh: 30 seconds (auto)
```

#### 6.6 Plan Dashboard Organization

**Folder structure:**
```
Dashboards/
├── Overview/
│   ├── Platform Health
│   └── SLO Dashboard
├── Services/
│   ├── Checkout Service
│   ├── Order Service
│   └── Payment Service
├── Infrastructure/
│   ├── Kubernetes Cluster
│   └── Databases
├── Business/
│   └── E-commerce Metrics
└── Debugging/
    └── Per-service debugging dashboards
```

**Access control:**
```
Public (all engineers):
- Service health dashboards
- SLO dashboards

Team-specific:
- Debugging dashboards
- Infrastructure dashboards

Leadership:
- Business metrics dashboards
- SLO compliance dashboards
```

### Quality Checklist

- [ ] Service health dashboards created for all critical services
- [ ] SLO dashboards track compliance and error budget
- [ ] Business metrics dashboards connect to technical metrics
- [ ] Debugging dashboards enable deep-dive analysis
- [ ] Dashboard naming conventions established
- [ ] Color and layout standards defined
- [ ] Dashboard organization logical (folders)
- [ ] Access control configured
- [ ] Dashboards linked from alerts and runbooks

### Common Pitfalls

- **Too many metrics:** Overwhelming, hard to find signal
- **No business metrics:** Can't connect to business impact
- **Stale dashboards:** Not maintained, out of date
- **No standards:** Inconsistent dashboards across teams
- **No access control:** Sensitive data exposed

### Outputs

- Service health dashboards
- SLO dashboards
- Business metrics dashboards
- Debugging dashboards
- Dashboard standards guide
- Dashboard organization structure

---

## Step 7: Select Observability Tools (30-60 minutes)

### Objective

Choose the right observability tools based on requirements, budget, and team capabilities.

### Actions

#### 7.1 Evaluate Logging Tools

**Options comparison:**

| Tool | Pros | Cons | Best For | Cost |
|------|------|------|----------|------|
| **ELK Stack** | Open source, powerful search, flexible | Complex to operate, resource-intensive | Self-hosted, high volume | Low (infra cost) |
| **Splunk** | Powerful, enterprise features, excellent UI | Expensive, complex licensing | Large enterprises, compliance | High |
| **CloudWatch** | Native AWS integration, simple setup | Limited query capabilities, AWS-only | AWS-native apps | Medium |
| **Datadog** | Integrated platform, great UI | Expensive at scale | Unified observability | High |
| **Grafana Loki** | Cost-effective, integrates with Prometheus | Limited query features | Kubernetes, cost-conscious | Low |

#### 7.2 Evaluate Metrics Tools

**Options comparison:**

| Tool | Pros | Cons | Best For | Cost |
|------|------|------|----------|------|
| **Prometheus** | Open source, powerful PromQL, pull-based | Limited long-term storage | Kubernetes, self-hosted | Low |
| **Datadog** | Unified platform, great UI, easy setup | Expensive | Unified observability | High |
| **New Relic** | Comprehensive APM, good UI | Expensive at scale | APM-focused | High |
| **CloudWatch** | Native AWS integration | Limited query capabilities | AWS-native apps | Medium |
| **InfluxDB** | Time-series optimized, SQL-like queries | Less ecosystem | IoT, high-frequency | Medium |

#### 7.3 Evaluate Tracing Tools

**Options comparison:**

| Tool | Pros | Cons | Best For | Cost |
|------|------|------|----------|------|
| **Jaeger** | Open source, CNCF, good UI, scalable | Requires infrastructure | Kubernetes, OpenTelemetry | Low |
| **Zipkin** | Open source, simple, mature | Less active development | Simple setups | Low |
| **Datadog APM** | Unified platform, excellent UI, auto-instrumentation | Expensive | Unified observability | High |
| **AWS X-Ray** | Native AWS integration | AWS-only, limited features | AWS-native apps | Medium |
| **Grafana Tempo** | Cost-effective, integrates with Grafana | Newer, fewer features | Cost-conscious | Low |
| **Lightstep** | Advanced features, tail-based sampling | Expensive | Large-scale, high-volume | High |

#### 7.4 Consider All-in-One Platforms

**Unified observability platforms:**

**Datadog:**
- Logs, metrics, traces, APM, RUM, synthetics
- Excellent UI and integrations
- Expensive but comprehensive
- Best for: Teams wanting single platform

**New Relic:**
- APM, logs, metrics, traces, browser monitoring
- Good UI, easy setup
- Expensive at scale
- Best for: APM-focused teams

**Dynatrace:**
- Full-stack monitoring, AI-powered insights
- Automatic instrumentation
- Very expensive
- Best for: Large enterprises

**Grafana Stack (Loki + Prometheus + Tempo):**
- Open source, cost-effective
- Unified Grafana UI
- Requires infrastructure management
- Best for: Cost-conscious, self-hosted

#### 7.5 Evaluate Cost vs. Capabilities

**Cost factors:**
```
Data ingestion:
- Logs: $X per GB ingested
- Metrics: $Y per million data points
- Traces: $Z per million spans

Data retention:
- Storage costs increase with retention
- Query costs may apply

Users/seats:
- Per-user licensing (Splunk, Datadog)
- Unlimited users (Prometheus, Grafana)

Infrastructure:
- Self-hosted: Infrastructure costs
- SaaS: Subscription costs
```

**Capability assessment:**
```
Required capabilities:
- [ ] Structured log search
- [ ] Metric aggregation and alerting
- [ ] Distributed tracing
- [ ] Dashboards
- [ ] Alerting
- [ ] Integrations (Slack, PagerDuty)
- [ ] API access
- [ ] RBAC (role-based access control)

Nice-to-have:
- [ ] Anomaly detection (AI/ML)
- [ ] Automatic instrumentation
- [ ] Real user monitoring (RUM)
- [ ] Synthetic monitoring
```

**Cost estimate example:**
```
Datadog (all-in-one):
- 10 services, 100 GB logs/day, 1M metrics/min, 10M spans/day
- Cost: ~$5,000-10,000/month

Open source (Prometheus + Loki + Jaeger):
- Infrastructure: 5 VMs, 500 GB storage
- Cost: ~$500-1,000/month + engineering time
```

#### 7.6 Plan Tool Integration

**Integration points:**
```
Application → Observability Tools:
- Logging library → Log aggregator
- Metrics library → Metrics backend
- Tracing SDK → Tracing backend

Observability Tools → Alerting:
- Prometheus → Alertmanager → PagerDuty
- Datadog → PagerDuty/Opsgenie

Observability Tools → Collaboration:
- Alerts → Slack
- Dashboards → Slack (links)
- Incidents → Jira

Observability Tools → CI/CD:
- Deployment events → Observability (annotations)
- Performance tests → Metrics
```

**Integration checklist:**
- [ ] Logging library supports chosen log aggregator
- [ ] Metrics library supports chosen metrics backend
- [ ] Tracing SDK supports chosen tracing backend
- [ ] Alerting tool integrates with on-call platform
- [ ] Dashboards accessible via SSO
- [ ] API access for automation

### Quality Checklist

- [ ] Logging tool evaluated and selected
- [ ] Metrics tool evaluated and selected
- [ ] Tracing tool evaluated and selected
- [ ] All-in-one platform considered
- [ ] Cost analysis completed (per GB, per metric, per span)
- [ ] Capabilities matched to requirements
- [ ] Integration plan documented
- [ ] Decision documented with rationale
- [ ] Budget approval obtained

### Common Pitfalls

- **Choosing based on hype:** Pick tools that fit your needs, not trends
- **Ignoring cost:** Observability costs can spiral quickly
- **Vendor lock-in:** Consider open standards (OpenTelemetry)
- **Over-engineering:** Don't need enterprise tools for small projects
- **Under-engineering:** Don't skimp on observability for critical systems

### Outputs

- Tool selection document
- Cost analysis
- Capability comparison matrix
- Integration plan
- Decision rationale

---

## Step 8: Create Instrumentation Standards (60-90 minutes)

### Objective

Standardize how to instrument code across all services to ensure consistency and quality.

### Actions

#### 8.1 Define Logging Standards

**What to log:**

```javascript
// ✅ GOOD: Log business events
logger.info('Order created', {
  order_id: order.id,
  user_id: user.id,
  amount: order.total,
  currency: 'USD',
  items_count: order.items.length
});

// ✅ GOOD: Log errors with context
logger.error('Payment processing failed', {
  order_id: order.id,
  payment_method: 'credit_card',
  error_code: error.code,
  error_message: error.message,
  stack_trace: error.stack
});

// ✅ GOOD: Log external API calls
logger.info('Payment gateway request', {
  gateway: 'stripe',
  amount: 99.99,
  duration_ms: 245
});

// ❌ BAD: Logging too much detail
logger.debug('Processing order', {
  full_order_object: order, // Too much data
  full_user_object: user     // Includes PII
});

// ❌ BAD: Logging in tight loops
for (const item of items) {
  logger.debug('Processing item', { item }); // Too noisy
}
```

**Logging standards document:**
```markdown
# Logging Standards

## When to Log

### INFO Level
- Business events (order created, user registered)
- External API calls (with duration)
- Significant state changes
- Startup/shutdown events

### WARN Level
- Degraded performance (slow response)
- Fallback scenarios (cache miss, using default)
- Deprecated API usage
- Configuration issues (non-fatal)

### ERROR Level
- Exceptions and errors
- Failed external API calls
- Data validation failures
- Resource exhaustion

### FATAL Level
- Application crashes
- Unrecoverable errors
- Data corruption

## Required Fields

Every log must include:
- `timestamp`: ISO 8601 with milliseconds
- `level`: Log level
- `service`: Service name
- `correlation_id`: Request trace ID
- `message`: Human-readable message

## Optional but Recommended
- `user_id`: For user-specific debugging
- `session_id`: For session tracking
- `duration_ms`: For performance tracking
- `error_code`: For error categorization

## PII Redaction

Redact the following:
- Passwords, API keys, tokens
- Credit card numbers (show last 4 digits only)
- SSN, passport numbers
- Email addresses (if required by compliance)
- Full names (if required by compliance)

## Examples

See examples/ directory for language-specific examples.
```

#### 8.2 Define Metric Standards

**Metric naming:**
```
# Standard format
<namespace>_<subsystem>_<name>_<unit>

# Examples
http_requests_total
http_request_duration_seconds
http_request_size_bytes
database_connections_active
database_query_duration_seconds
cache_hits_total
cache_misses_total
queue_messages_pending
queue_processing_duration_seconds
```

**Metric types:**
```
Counter: Monotonically increasing value
  Use for: Request counts, error counts, events
  Example: http_requests_total

Gauge: Value that can go up or down
  Use for: Current values, resource usage
  Example: database_connections_active

Histogram: Distribution of values
  Use for: Latencies, request sizes
  Example: http_request_duration_seconds

Summary: Similar to histogram, pre-calculated quantiles
  Use for: Latencies (when histogram not available)
  Example: http_request_duration_seconds (summary)
```

**Label standards:**
```python
# ✅ GOOD: Low cardinality labels
http_requests_total{
  service="checkout",
  environment="production",
  endpoint="/api/orders",  # Limited endpoints
  method="POST",           # Limited methods
  status_code="200"        # Limited status codes
}

# ❌ BAD: High cardinality labels
http_requests_total{
  user_id="12345",         # Millions of users
  request_id="abc-123",    # Unique per request
  ip_address="1.2.3.4"     # Many unique IPs
}
```

**Metric standards document:**
```markdown
# Metric Standards

## Required Metrics (RED)

All request-based services must expose:
- `http_requests_total` (counter)
- `http_request_duration_seconds` (histogram)

## Required Metrics (USE)

All services must expose:
- `process_cpu_seconds_total` (counter)
- `process_resident_memory_bytes` (gauge)

## Naming Conventions

- Use snake_case
- Include unit in name (seconds, bytes, total)
- Counters end in `_total`
- Use base units (seconds not milliseconds)

## Label Guidelines

- Keep cardinality low (< 100 unique values)
- Use consistent label names across services
- Common labels: service, environment, endpoint, method

## Histogram Buckets

For latency histograms:
```
buckets: [0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1, 2.5, 5, 10]
```

For size histograms:
```
buckets: [100, 1000, 10000, 100000, 1000000, 10000000]
```
```

#### 8.3 Define Tracing Standards

**Span naming:**
```
# Format: <operation> <resource>

# Examples
GET /api/orders
POST /api/checkout
SELECT orders
INSERT order_items
Publish order.created
Consume payment.processed
```

**Span attributes:**
```javascript
// HTTP server span
span.setAttributes({
  'http.method': 'POST',
  'http.url': '/api/orders',
  'http.status_code': 200,
  'http.user_agent': req.headers['user-agent'],
  'user.id': user.id,
  'order.id': order.id
});

// Database span
span.setAttributes({
  'db.system': 'postgresql',
  'db.name': 'orders_db',
  'db.operation': 'SELECT',
  'db.statement': 'SELECT * FROM orders WHERE id = $1',
  'db.rows_affected': 1
});

// External API span
span.setAttributes({
  'http.method': 'POST',
  'http.url': 'https://api.stripe.com/v1/charges',
  'http.status_code': 200,
  'external.service': 'stripe',
  'payment.amount': 99.99
});
```

**Tracing standards document:**
```markdown
# Tracing Standards

## Required Spans

All services must create spans for:
- HTTP requests (server-side)
- HTTP requests (client-side, to other services)
- Database queries
- External API calls
- Message queue publish/consume

## Span Naming

Format: `<operation> <resource>`

Examples:
- `GET /api/orders`
- `SELECT orders`
- `Publish order.created`

## Required Attributes

HTTP spans:
- `http.method`, `http.url`, `http.status_code`

Database spans:
- `db.system`, `db.name`, `db.operation`

Message queue spans:
- `messaging.system`, `messaging.destination`, `messaging.operation`

## Context Propagation

Use W3C Trace Context standard:
- Extract context from incoming requests
- Propagate context to downstream services
- Use `traceparent` header

## Sampling

- Always sample errors (status code 5xx)
- Always sample slow requests (> p95)
- Probabilistic sampling for normal requests (10%)
```

#### 8.4 Create Code Examples

**Provide language-specific examples:**

**Node.js example:**
```javascript
// examples/nodejs/instrumentation.js

const { trace } = require('@opentelemetry/api');
const winston = require('winston');
const promClient = require('prom-client');

// Logging
const logger = winston.createLogger({
  format: winston.format.json(),
  defaultMeta: { service: 'checkout-service' },
  transports: [
    new winston.transports.Console()
  ]
});

// Metrics
const httpRequestsTotal = new promClient.Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['method', 'endpoint', 'status_code']
});

const httpRequestDuration = new promClient.Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration',
  labelNames: ['method', 'endpoint'],
  buckets: [0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1, 2.5, 5, 10]
});

// Tracing
const tracer = trace.getTracer('checkout-service');

// Middleware example
function instrumentedHandler(req, res) {
  const span = tracer.startSpan(`${req.method} ${req.path}`);
  const start = Date.now();
  
  try {
    // Business logic
    const result = processCheckout(req.body);
    
    // Log success
    logger.info('Checkout completed', {
      correlation_id: req.headers['x-correlation-id'],
      user_id: req.user.id,
      order_id: result.orderId,
      amount: result.amount,
      duration_ms: Date.now() - start
    });
    
    // Record metrics
    httpRequestsTotal.inc({
      method: req.method,
      endpoint: req.path,
      status_code: 200
    });
    
    httpRequestDuration.observe({
      method: req.method,
      endpoint: req.path
    }, (Date.now() - start) / 1000);
    
    // Set span attributes
    span.setAttributes({
      'http.method': req.method,
      'http.url': req.path,
      'http.status_code': 200,
      'user.id': req.user.id,
      'order.id': result.orderId
    });
    
    span.end();
    res.status(200).json(result);
    
  } catch (error) {
    // Log error
    logger.error('Checkout failed', {
      correlation_id: req.headers['x-correlation-id'],
      user_id: req.user.id,
      error_message: error.message,
      error_stack: error.stack,
      duration_ms: Date.now() - start
    });
    
    // Record error metrics
    httpRequestsTotal.inc({
      method: req.method,
      endpoint: req.path,
      status_code: 500
    });
    
    // Set span error
    span.recordException(error);
    span.setStatus({ code: SpanStatusCode.ERROR });
    span.end();
    
    res.status(500).json({ error: 'Checkout failed' });
  }
}
```

**Python example:**
```python
# examples/python/instrumentation.py

import logging
import time
from opentelemetry import trace
from prometheus_client import Counter, Histogram

# Logging
logging.basicConfig(
    level=logging.INFO,
    format='%(message)s'
)
logger = logging.getLogger(__name__)

# Metrics
http_requests_total = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status_code']
)

http_request_duration = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration',
    ['method', 'endpoint'],
    buckets=[0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1, 2.5, 5, 10]
)

# Tracing
tracer = trace.get_tracer(__name__)

def instrumented_handler(request):
    with tracer.start_as_current_span(f"{request.method} {request.path}") as span:
        start = time.time()
        
        try:
            # Business logic
            result = process_checkout(request.data)
            
            # Log success
            logger.info({
                'message': 'Checkout completed',
                'correlation_id': request.headers.get('x-correlation-id'),
                'user_id': request.user.id,
                'order_id': result['order_id'],
                'amount': result['amount'],
                'duration_ms': (time.time() - start) * 1000
            })
            
            # Record metrics
            http_requests_total.labels(
                method=request.method,
                endpoint=request.path,
                status_code=200
            ).inc()
            
            http_request_duration.labels(
                method=request.method,
                endpoint=request.path
            ).observe(time.time() - start)
            
            # Set span attributes
            span.set_attributes({
                'http.method': request.method,
                'http.url': request.path,
                'http.status_code': 200,
                'user.id': request.user.id,
                'order.id': result['order_id']
            })
            
            return {'status': 200, 'data': result}
            
        except Exception as error:
            # Log error
            logger.error({
                'message': 'Checkout failed',
                'correlation_id': request.headers.get('x-correlation-id'),
                'user_id': request.user.id,
                'error_message': str(error),
                'duration_ms': (time.time() - start) * 1000
            })
            
            # Record error metrics
            http_requests_total.labels(
                method=request.method,
                endpoint=request.path,
                status_code=500
            ).inc()
            
            # Set span error
            span.record_exception(error)
            span.set_status(trace.Status(trace.StatusCode.ERROR))
            
            return {'status': 500, 'error': 'Checkout failed'}
```

#### 8.5 Build Instrumentation Libraries

**Create shared libraries:**

```javascript
// libraries/nodejs/observability-lib/index.js

const { setupLogging } = require('./logging');
const { setupMetrics } = require('./metrics');
const { setupTracing } = require('./tracing');
const { instrumentExpress } = require('./middleware');

function initializeObservability(config) {
  const logger = setupLogging(config.service);
  const metrics = setupMetrics(config.service);
  const tracer = setupTracing(config.service, config.tracingEndpoint);
  
  return {
    logger,
    metrics,
    tracer,
    instrumentExpress: () => instrumentExpress(logger, metrics, tracer)
  };
}

module.exports = { initializeObservability };
```

**Usage:**
```javascript
const { initializeObservability } = require('@company/observability-lib');

const obs = initializeObservability({
  service: 'checkout-service',
  tracingEndpoint: 'http://jaeger:14268/api/traces'
});

const app = express();
app.use(obs.instrumentExpress());

// Now all requests are automatically instrumented
```

#### 8.6 Document Best Practices

**Best practices guide:**
```markdown
# Observability Best Practices

## Logging

### DO
- Use structured logging (JSON)
- Include correlation IDs in all logs
- Log business events (order created, user registered)
- Log errors with full context
- Use appropriate log levels

### DON'T
- Log sensitive data (passwords, credit cards)
- Log in tight loops (performance impact)
- Use string concatenation (use structured fields)
- Log entire objects (log specific fields)

## Metrics

### DO
- Expose RED metrics for all services
- Use histograms for latency (not averages)
- Keep label cardinality low
- Use consistent naming across services

### DON'T
- Use high-cardinality labels (user_id, request_id)
- Create too many metrics (increases cost)
- Use gauges for counters (or vice versa)

## Tracing

### DO
- Propagate trace context across all services
- Sample intelligently (always trace errors)
- Add meaningful span attributes
- Use semantic conventions (OpenTelemetry)

### DON'T
- Trace everything (expensive)
- Forget to propagate context (broken traces)
- Add high-cardinality attributes

## General

### DO
- Design observability from the start
- Use correlation IDs to connect logs, metrics, traces
- Monitor observability costs
- Regularly review and tune

### DON'T
- Add observability as an afterthought
- Ignore cost (can spiral quickly)
- Set and forget (requires ongoing tuning)
```

### Quality Checklist

- [ ] Logging standards documented (what, when, how)
- [ ] Metric standards documented (naming, labels, types)
- [ ] Tracing standards documented (spans, attributes, propagation)
- [ ] Code examples provided for all supported languages
- [ ] Instrumentation libraries created
- [ ] Best practices guide published
- [ ] Standards reviewed and approved by team
- [ ] Training materials created

### Common Pitfalls

- **No standards:** Each service instruments differently
- **Too complex:** Standards are ignored if too difficult
- **No examples:** Engineers don't know how to implement
- **No libraries:** Duplicate instrumentation code
- **No training:** Team doesn't follow standards

### Outputs

- Logging standards document
- Metric standards document
- Tracing standards document
- Code examples (per language)
- Instrumentation libraries
- Best practices guide

---

## Step 9: Plan Implementation (30-45 minutes)

### Objective

Create a phased rollout plan for implementing observability across all services.

### Actions

#### 9.1 Prioritize Services to Instrument

**Prioritization criteria:**
```
High priority:
- Critical user journeys (checkout, login)
- Services with frequent incidents
- Services with poor visibility
- New services (easier to instrument)

Medium priority:
- Supporting services
- Services with some observability
- Stable services with few incidents

Low priority:
- Internal tools
- Rarely used services
- Services being deprecated
```

**Example prioritization:**
```
Phase 1 (Week 1-2): Critical services
- Checkout Service
- Payment Service
- Order Service
- API Gateway

Phase 2 (Week 3-4): Supporting services
- Inventory Service
- Notification Service
- User Service

Phase 3 (Week 5-6): Remaining services
- Analytics Service
- Reporting Service
- Admin Service
```

#### 9.2 Define Implementation Phases

**Phase structure:**

**Phase 1: Foundation (Week 1-2)**
```
Goals:
- Set up centralized logging (ELK/Datadog/etc.)
- Set up metrics backend (Prometheus/Datadog/etc.)
- Set up tracing backend (Jaeger/Datadog/etc.)
- Create instrumentation libraries
- Document standards

Deliverables:
- Infrastructure ready
- Libraries published
- Standards documented
```

**Phase 2: Critical Services (Week 3-4)**
```
Goals:
- Instrument 4 critical services
- Create dashboards for each service
- Set up alerts for critical services
- Validate observability works end-to-end

Deliverables:
- 4 services fully instrumented
- 4 service health dashboards
- Critical alerts configured
- SLO dashboard created
```

**Phase 3: Expansion (Week 5-8)**
```
Goals:
- Instrument remaining services
- Create debugging dashboards
- Refine alerting based on feedback
- Conduct team training

Deliverables:
- All services instrumented
- Complete dashboard suite
- Tuned alerting
- Team trained
```

**Phase 4: Optimization (Week 9-12)**
```
Goals:
- Optimize sampling rates
- Reduce observability costs
- Improve alert signal-to-noise
- Create runbooks

Deliverables:
- Cost-optimized observability
- Low alert fatigue
- Comprehensive runbooks
- Observability review process
```

#### 9.3 Assign Ownership

**Ownership model:**

```
Observability Platform Team:
- Infrastructure (logging, metrics, tracing backends)
- Instrumentation libraries
- Standards and best practices
- Training and support

Service Teams:
- Instrument their services
- Create service-specific dashboards
- Define service-specific alerts
- Maintain runbooks

SRE Team:
- SLO definitions
- Cross-service dashboards
- Incident response
- On-call rotation
```

**RACI matrix:**

| Task | Platform Team | Service Teams | SRE Team |
|------|---------------|---------------|----------|
| Infrastructure setup | R, A | I | C |
| Instrumentation libraries | R, A | C | I |
| Service instrumentation | C | R, A | I |
| Dashboards (service-specific) | C | R, A | C |
| Dashboards (cross-service) | C | I | R, A |
| Alerting | C | R | A |
| Incident response | I | C | R, A |
| Standards | R, A | C | C |

R = Responsible, A = Accountable, C = Consulted, I = Informed

#### 9.4 Estimate Effort

**Per-service effort:**
```
Small service (< 5 endpoints):
- Instrumentation: 4-8 hours
- Dashboard creation: 2-4 hours
- Alert setup: 2-4 hours
- Testing: 2-4 hours
Total: 10-20 hours (1-2.5 days)

Medium service (5-15 endpoints):
- Instrumentation: 8-16 hours
- Dashboard creation: 4-8 hours
- Alert setup: 4-8 hours
- Testing: 4-8 hours
Total: 20-40 hours (2.5-5 days)

Large service (> 15 endpoints):
- Instrumentation: 16-32 hours
- Dashboard creation: 8-16 hours
- Alert setup: 8-16 hours
- Testing: 8-16 hours
Total: 40-80 hours (5-10 days)
```

**Total effort estimate:**
```
Infrastructure setup: 40-80 hours (1-2 weeks)
Library development: 40-80 hours (1-2 weeks)
Documentation: 20-40 hours (0.5-1 week)

10 services (mixed sizes): 200-400 hours (5-10 weeks)

Total: 300-600 hours (7.5-15 weeks)

With 2-3 engineers: 3-6 months
```

#### 9.5 Plan Training

**Training plan:**

**Week 1: Kickoff**
- Observability strategy presentation
- Standards overview
- Tool demonstrations
- Q&A session

**Week 2-3: Hands-on workshops**
- Workshop 1: Logging (2 hours)
- Workshop 2: Metrics (2 hours)
- Workshop 3: Tracing (2 hours)
- Workshop 4: Dashboards and alerts (2 hours)

**Ongoing: Office hours**
- Weekly office hours for questions
- Slack channel for support
- Pair programming sessions

**Materials:**
- Slide decks
- Code examples
- Video recordings
- Written guides

#### 9.6 Define Success Criteria

**Success metrics:**

```
Coverage:
- [ ] 100% of critical services instrumented
- [ ] 80%+ of all services instrumented
- [ ] All services have health dashboards
- [ ] All services have alerts

Quality:
- [ ] Mean time to detection (MTTD) < 5 min
- [ ] Mean time to resolution (MTTR) < 30 min
- [ ] Alert-to-incident ratio > 50%
- [ ] Zero incidents due to lack of observability

Adoption:
- [ ] 90%+ of engineers trained
- [ ] Instrumentation libraries used by all services
- [ ] Standards followed consistently

Cost:
- [ ] Observability costs < 5% of infrastructure costs
- [ ] No unexpected cost spikes
```

### Quality Checklist

- [ ] Services prioritized (critical first)
- [ ] Implementation phases defined with timelines
- [ ] Ownership assigned (RACI matrix)
- [ ] Effort estimated per service and total
- [ ] Training plan created
- [ ] Success criteria defined and measurable
- [ ] Stakeholder buy-in obtained
- [ ] Budget approved

### Common Pitfalls

- **Big bang approach:** Trying to instrument everything at once
- **No ownership:** Unclear who's responsible
- **Underestimating effort:** Instrumentation takes time
- **No training:** Team doesn't know how to use observability
- **No success criteria:** Can't measure progress

### Outputs

- Service prioritization list
- Implementation phases with timelines
- Ownership matrix (RACI)
- Effort estimates
- Training plan
- Success criteria

---

## Step 10: Document and Communicate (30-45 minutes)

### Objective

Share observability strategy, standards, and implementation plan with all stakeholders.

### Actions

#### 10.1 Write Observability Strategy Document

**Document structure:**

```markdown
# Observability Strategy

## Executive Summary

One-page overview of observability initiative, goals, and expected outcomes.

## Current State

- Current observability gaps
- Incident response challenges
- Debugging pain points

## Future State

- Comprehensive observability (logs, metrics, traces)
- Fast incident detection and resolution
- Proactive issue identification

## Strategy

### Logging
- Centralized logging with [ELK/Datadog/etc.]
- Structured JSON logs
- 30-day retention

### Metrics
- Prometheus for metrics
- RED metrics for all services
- SLO-based alerting

### Tracing
- OpenTelemetry standard
- Jaeger backend
- Intelligent sampling

### Alerting
- PagerDuty for critical alerts
- Slack for warnings
- SLO-based thresholds

## Implementation Plan

- Phase 1: Foundation (Week 1-2)
- Phase 2: Critical services (Week 3-4)
- Phase 3: Expansion (Week 5-8)
- Phase 4: Optimization (Week 9-12)

## Success Criteria

- MTTD < 5 min
- MTTR < 30 min
- 100% critical service coverage

## Budget

- Infrastructure: $X/month
- Tooling: $Y/month
- Engineering time: Z person-months

## Risks and Mitigations

- Risk: Cost overruns → Mitigation: Monitor costs weekly
- Risk: Low adoption → Mitigation: Training and support
```

#### 10.2 Create Instrumentation Guide

**Guide structure:**

```markdown
# Instrumentation Guide

## Quick Start

1. Install observability library
2. Initialize in application
3. Instrument HTTP endpoints
4. Add business metrics
5. Test locally
6. Deploy

## Detailed Instructions

### Logging

[Step-by-step logging setup]

### Metrics

[Step-by-step metrics setup]

### Tracing

[Step-by-step tracing setup]

## Code Examples

### Node.js
[Complete example]

### Python
[Complete example]

### Java
[Complete example]

## Testing

### Local testing
[How to test locally]

### Validation
[How to validate instrumentation]

## Troubleshooting

### Common issues
[Solutions to common problems]
```

#### 10.3 Build Runbooks for Common Scenarios

**Runbook template:**

```markdown
# Runbook: High Error Rate

## Symptoms

- Alert: "High error rate on [service]"
- Error rate > 5% for 5+ minutes

## Impact

- Users experiencing errors
- SLO at risk

## Investigation Steps

1. **Check service health dashboard**
   - URL: [dashboard link]
   - Look for: Error rate spike, latency increase

2. **Check recent deployments**
   - Command: `kubectl rollout history deployment/[service]`
   - Look for: Recent changes

3. **Check logs for errors**
   - Query: `service:[service] AND level:ERROR`
   - Look for: Error patterns, stack traces

4. **Check dependencies**
   - Database: Check connection pool, slow queries
   - External APIs: Check response times, error rates

5. **Check traces**
   - Filter: Errors only
   - Look for: Slow spans, failed operations

## Common Causes

1. **Bad deployment**
   - Solution: Rollback deployment
   - Command: `kubectl rollout undo deployment/[service]`

2. **Database issues**
   - Solution: Check database health, restart if needed

3. **External API down**
   - Solution: Enable circuit breaker, use fallback

4. **Resource exhaustion**
   - Solution: Scale up service
   - Command: `kubectl scale deployment/[service] --replicas=10`

## Escalation

If not resolved in 30 minutes:
- Escalate to: [Engineering Manager]
- Contact: [phone/email]
```

**Create runbooks for:**
- High error rate
- High latency
- Service down
- Resource exhaustion
- Database issues
- External API failures

#### 10.4 Conduct Team Training

**Training sessions:**

**Session 1: Observability Overview (1 hour)**
- Why observability matters
- Three pillars (logs, metrics, traces)
- Our observability stack
- Demo: Using dashboards and alerts

**Session 2: Logging Workshop (2 hours)**
- Logging standards
- Hands-on: Instrument a service with logging
- Hands-on: Query logs in [ELK/Datadog/etc.]
- Best practices

**Session 3: Metrics Workshop (2 hours)**
- Metrics standards (RED, USE)
- Hands-on: Add metrics to a service
- Hands-on: Create a dashboard
- Hands-on: Set up an alert

**Session 4: Tracing Workshop (2 hours)**
- Distributed tracing concepts
- Hands-on: Instrument a service with tracing
- Hands-on: Investigate a slow request
- Context propagation

**Session 5: Incident Response (2 hours)**
- Using observability for incident response
- Hands-on: Simulate an incident
- Hands-on: Use logs, metrics, traces to debug
- Runbook walkthrough

#### 10.5 Publish Documentation

**Documentation site structure:**

```
Observability Documentation/
├── Getting Started
│   ├── Overview
│   ├── Quick Start
│   └── FAQ
├── Strategy
│   ├── Observability Strategy
│   ├── Implementation Plan
│   └── Success Metrics
├── Standards
│   ├── Logging Standards
│   ├── Metric Standards
│   ├── Tracing Standards
│   └── Best Practices
├── Guides
│   ├── Instrumentation Guide
│   ├── Dashboard Guide
│   ├── Alerting Guide
│   └── Troubleshooting
├── Examples
│   ├── Node.js
│   ├── Python
│   ├── Java
│   └── Go
├── Runbooks
│   ├── High Error Rate
│   ├── High Latency
│   ├── Service Down
│   └── [More runbooks]
└── Tools
    ├── Logging (ELK/Datadog/etc.)
    ├── Metrics (Prometheus/Datadog/etc.)
    ├── Tracing (Jaeger/Datadog/etc.)
    └── Alerting (PagerDuty/etc.)
```

**Publishing:**
- Internal wiki (Confluence, Notion)
- GitHub repository (for code examples)
- Slack channel for announcements
- Email to all engineering

#### 10.6 Gather Feedback

**Feedback mechanisms:**

**Surveys:**
- Post-training survey
- Monthly observability survey
- Incident retrospective feedback

**Office hours:**
- Weekly office hours for questions
- Slack channel for async support

**Metrics:**
- Instrumentation adoption rate
- Dashboard usage
- Alert effectiveness (alert-to-incident ratio)
- MTTD and MTTR trends

**Iteration:**
- Review feedback monthly
- Update standards based on learnings
- Improve tooling and libraries
- Refine training materials

### Quality Checklist

- [ ] Observability strategy document written
- [ ] Instrumentation guide created
- [ ] Runbooks written for common scenarios
- [ ] Team training conducted (all sessions)
- [ ] Documentation published (accessible to all)
- [ ] Feedback mechanisms established
- [ ] Communication plan executed
- [ ] Stakeholders informed

### Common Pitfalls

- **Poor documentation:** Team can't self-serve
- **No training:** Low adoption
- **One-way communication:** No feedback loop
- **Documentation rot:** Docs become outdated
- **No examples:** Engineers don't know how to start

### Outputs

- Observability strategy document
- Instrumentation guide
- Runbooks (5+ scenarios)
- Training materials (slides, videos)
- Published documentation site
- Feedback survey

---

## Summary

Following these 10 steps will result in a comprehensive observability system that provides:

✅ **Visibility:** Logs, metrics, and traces for all services  
✅ **Fast debugging:** MTTD < 5 min, MTTR < 30 min  
✅ **Proactive monitoring:** Alerts before user impact  
✅ **SLO tracking:** Know when you're meeting targets  
✅ **Team enablement:** Standards, tools, and training  

**Total time investment:** 4-8 hours for design, 3-6 months for implementation.

---

## Next Steps

After completing this workflow:

1. **Implement observability** — Follow the implementation plan
2. **Use observability for incidents** — See `incident-analysis` skill
3. **Improve based on learnings** — Iterate on observability design
4. **Validate production readiness** — See `production-readiness` skill

---

## Related Skills

- **incident-analysis** — Use observability for incident response
- **root-cause-analysis** — Deep-dive analysis using observability data
- **production-readiness** — Validate observability before production
- **capacity-planning** — Use metrics for capacity planning
