# Observability Design - Comprehensive Examples

This document provides four detailed, real-world examples of observability design across different domains and scales. Each example includes context, requirements, complete design, implementation details, and outcomes.

## Table of Contents

1. [Example 1: E-Commerce Platform Observability Design](#example-1-e-commerce-platform-observability-design)
2. [Example 2: Banking Platform Observability Design](#example-2-banking-platform-observability-design)
3. [Example 3: SaaS Platform Observability Design](#example-3-saas-platform-observability-design)
4. [Example 4: AI/ML Platform Observability Design](#example-4-aiml-platform-observability-design)

---

## Example 1: E-Commerce Platform Observability Design

### Context

**Company**: Global e-commerce platform serving 10,000+ merchants

**System Architecture**:
- **Architecture**: Microservices (30+ services)
- **Technologies**: 
  - Backend: Node.js (Express), Python (Flask), Go
  - Frontend: React, Next.js
  - Databases: PostgreSQL, MongoDB, Redis
  - Message Queue: Kafka
  - Infrastructure: Kubernetes on AWS (EKS)
- **Scale**: 
  - 50,000 requests/second at peak
  - 5 million daily active users
  - 100,000+ orders per day
- **Regions**: US-East, US-West, EU-West (multi-region deployment)

**Business Context**:
- Critical user journeys: Product search, add to cart, checkout, payment
- Revenue impact: Every minute of downtime = $50,000 lost revenue
- Seasonal peaks: Black Friday, Cyber Monday (10x normal traffic)
- Multi-tenant: Each merchant has isolated data and workflows

### Requirements

**Operational Requirements**:
- **SLO**: 99.95% availability (21.6 minutes downtime/month)
- **Latency**: p95 <200ms for API requests, p99 <500ms
- **MTTD**: <5 minutes for critical issues
- **MTTR**: <30 minutes for P0 incidents

**Compliance Requirements**:
- **PCI-DSS**: Payment card data security
- **GDPR**: EU customer data privacy and protection
- **Data Retention**: 
  - Operational logs: 30 days
  - Audit logs: 7 years (PCI-DSS requirement)

**Budget**: $50,000/month for observability

### Observability Design

#### 1. Logging Strategy

**Structured Logging Format (JSON)**:

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

**Log Levels and Sampling**:
- **ERROR/FATAL**: 100% (all errors logged)
- **WARN**: 100% (all warnings logged)
- **INFO**: 
  - High-volume services (search, catalog): 10% sampling
  - Medium-volume services (checkout, cart): 50% sampling
  - Low-volume services (admin, analytics): 100%
- **DEBUG**: Disabled in production (enable dynamically for troubleshooting)

**PII Masking**:
- Credit card numbers: Tokenized, only last 4 digits logged
- Email addresses: Hashed for correlation (`hash_abc123`)
- IP addresses: Partial masking (`192.168.xxx.xxx`)
- User IDs: Hashed consistently for correlation

**Log Aggregation Architecture**:
- **Platform**: Datadog Logs (chosen for unified observability with metrics and APM)
- **Collection**: Datadog agent on each Kubernetes node
- **Retention**:
  - Hot (searchable): 15 days
  - Archive (S3): 1 year for operational logs, 7 years for PCI-DSS audit logs
- **Cost Optimization**:
  - Index only ERROR/WARN and sampled INFO
  - Archive all logs to S3 for compliance
  - Use log patterns to reduce duplicate indexing

**Implementation Example (Node.js with Winston)**:

```javascript
const winston = require('winston');
const { trace } = require('@opentelemetry/api');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: 'checkout-service',
    version: process.env.SERVICE_VERSION,
    environment: process.env.NODE_ENV,
    region: process.env.AWS_REGION
  },
  transports: [
    new winston.transports.Console()
  ]
});

// Add trace context to logs
logger.on('data', (info) => {
  const span = trace.getActiveSpan();
  if (span) {
    const spanContext = span.spanContext();
    info.trace_id = spanContext.traceId;
    info.span_id = spanContext.spanId;
  }
});

// Usage
logger.info('Order created successfully', {
  order_id: 'order_789',
  merchant_id: 'merchant_12345',
  user_id: hashUserId(userId), // Hashed for privacy
  order_total: 129.99,
  payment_method: 'credit_card',
  duration_ms: 245
});
```

#### 2. Metrics Strategy

**SLI Definitions**:

1. **Availability SLI**: Percentage of successful requests (HTTP 2xx/3xx)
   - Target: 99.95% (21.6 minutes downtime/month)
   - Measurement: `(successful_requests / total_requests) × 100`

2. **Latency SLI**: p95 response time <200ms
   - Measurement: 95th percentile of `http_request_duration_seconds`

3. **Error Rate SLI**: <0.1% of requests result in errors
   - Measurement: `(error_requests / total_requests) × 100`

**RED Metrics (per service)**:

```prometheus
# Rate: Requests per second
http_requests_total{service="checkout-service", method="POST", endpoint="/orders", status="200"}

# Errors: Error count and rate
http_requests_errors_total{service="checkout-service", error_type="validation_error"}

# Duration: Latency histogram
http_request_duration_seconds{service="checkout-service", method="POST", endpoint="/orders"}
```

**Business Metrics**:

```prometheus
# Checkout funnel
checkout_steps_completed_total{step="cart", merchant_id="merchant_12345"}
checkout_steps_completed_total{step="shipping", merchant_id="merchant_12345"}
checkout_steps_completed_total{step="payment", merchant_id="merchant_12345"}
checkout_steps_completed_total{step="confirmation", merchant_id="merchant_12345"}

# Conversion metrics
checkout_conversions_total{merchant_id="merchant_12345"}
cart_abandonment_rate{merchant_id="merchant_12345"}

# Revenue
revenue_total{merchant_id="merchant_12345", currency="USD"}

# Active users
active_users{region="us-east-1"}
```

**Infrastructure Metrics (USE)**:

```prometheus
# Utilization
node_cpu_usage_percent{node="ip-10-0-1-23"}
node_memory_usage_percent{node="ip-10-0-1-23"}

# Saturation
kubernetes_pod_pending_count{namespace="production"}
http_request_queue_depth{service="checkout-service"}

# Errors
kubernetes_pod_restart_total{namespace="production", pod="checkout-service-abc123"}
node_disk_errors_total{node="ip-10-0-1-23"}
```

**Metrics Platform**:
- **Platform**: Datadog Metrics (unified with logs and APM)
- **Collection**: Datadog agent with Kubernetes integration
- **Cardinality Management**:
  - Limit labels to: `service`, `environment`, `region`, `status_code`, `method`
  - Avoid: `user_id`, `merchant_id`, `order_id` in labels (use tags in traces instead)
- **Retention**:
  - Full resolution (10s): 15 days
  - Rollup (1 hour): 15 months

**Implementation Example (Node.js with Prometheus client)**:

```javascript
const client = require('prom-client');

// RED Metrics
const httpRequestsTotal = new client.Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['service', 'method', 'endpoint', 'status']
});

const httpRequestDuration = new client.Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration in seconds',
  labelNames: ['service', 'method', 'endpoint'],
  buckets: [0.01, 0.05, 0.1, 0.5, 1, 5, 10]
});

// Business Metrics
const checkoutConversions = new client.Counter({
  name: 'checkout_conversions_total',
  help: 'Total checkout conversions',
  labelNames: ['merchant_id']
});

// Middleware to track metrics
app.use((req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    
    httpRequestsTotal.inc({
      service: 'checkout-service',
      method: req.method,
      endpoint: req.route?.path || req.path,
      status: res.statusCode
    });
    
    httpRequestDuration.observe({
      service: 'checkout-service',
      method: req.method,
      endpoint: req.route?.path || req.path
    }, duration);
  });
  
  next();
});

// Track business metric
app.post('/orders', async (req, res) => {
  // ... create order ...
  
  checkoutConversions.inc({ merchant_id: req.body.merchant_id });
  
  res.json({ order_id: order.id });
});
```

#### 3. Distributed Tracing Strategy

**Tracing Platform**: Datadog APM

**Instrumentation**:
- **Auto-instrumentation**: Express.js, Flask, gRPC, AWS SDK, PostgreSQL, MongoDB, Redis, Kafka
- **Manual instrumentation**: Custom business logic, external API calls
- **Libraries**: OpenTelemetry with Datadog exporter (vendor portability)

**Trace Context Propagation**:
- **Standard**: W3C Trace Context (`traceparent` header)
- **Propagation across**: HTTP, gRPC, Kafka, SQS
- **Format**: `traceparent: 00-{trace_id}-{span_id}-{flags}`

**Sampling Strategy (Adaptive)**:
- **Errors and slow requests (>1s)**: 100%
- **Normal requests**: 
  - 10% during normal traffic
  - 1% during peak traffic (Black Friday)
- **Per-merchant sampling**: 100% for new merchants (first 30 days)
- **Expected trace volume**: ~5M traces/day (~10% of requests)

**Span Naming and Tagging**:

```javascript
// Span naming: {service}.{operation}
"checkout-service.create_order"
"payment-service.process_payment"
"inventory-service.reserve_items"

// Standard tags
{
  "http.method": "POST",
  "http.status_code": 200,
  "http.url": "/api/orders",
  "db.type": "postgresql",
  "db.statement": "INSERT INTO orders ...", // Sanitized
  "error": false
}

// Business tags
{
  "merchant.id": "merchant_12345",
  "order.id": "order_789",
  "order.total": 129.99,
  "payment.method": "credit_card"
}
```

**Implementation Example (Node.js with OpenTelemetry)**:

```javascript
const { trace } = require('@opentelemetry/api');
const tracer = trace.getTracer('checkout-service');

app.post('/orders', async (req, res) => {
  const span = tracer.startSpan('checkout-service.create_order');
  
  try {
    // Add business context
    span.setAttributes({
      'merchant.id': req.body.merchant_id,
      'order.total': req.body.total,
      'payment.method': req.body.payment_method
    });
    
    // Create order
    const order = await createOrder(req.body);
    
    // Process payment (creates child span automatically)
    await processPayment(order.id, req.body.payment_method);
    
    // Reserve inventory (creates child span automatically)
    await reserveInventory(order.items);
    
    span.setAttributes({
      'order.id': order.id,
      'http.status_code': 201
    });
    
    res.status(201).json({ order_id: order.id });
  } catch (error) {
    span.recordException(error);
    span.setStatus({ code: SpanStatusCode.ERROR, message: error.message });
    res.status(500).json({ error: 'Order creation failed' });
  } finally {
    span.end();
  }
});
```

#### 4. Alerting Strategy

**SLO-Based Alerts (P0 - Critical)**:

```yaml
# Availability SLO Alert
name: "[P0] Availability SLO Violation - Checkout Service"
condition: "error_rate > 0.05% for 5 minutes"
threshold: 0.05  # 50% of error budget (0.1%)
window: 5m
action: "Page on-call engineer immediately"
runbook: "https://wiki.company.com/runbooks/checkout-availability"

# Latency SLO Alert
name: "[P0] Latency SLO Violation - Checkout Service"
condition: "p95_latency > 200ms for 5 minutes"
threshold: 200ms
window: 5m
action: "Page on-call engineer immediately"
runbook: "https://wiki.company.com/runbooks/checkout-latency"

# Error Budget Alert
name: "[P1] Error Budget Low - Checkout Service"
condition: "error_budget_remaining < 10%"
threshold: 10%
action: "Notify engineering team, freeze deployments"
```

**Service Health Alerts (P1 - High)**:

```yaml
# High Error Rate
name: "[P1] High Error Rate - Payment Service"
condition: "error_rate > 1% for 5 minutes"
threshold: 1%
window: 5m
action: "Notify on-call engineer via Slack, escalate if not acknowledged in 15 min"

# Dependency Failure
name: "[P1] Payment Gateway Failure"
condition: "payment_gateway_error_rate > 5% for 5 minutes"
threshold: 5%
window: 5m
action: "Page on-call engineer, notify payment team"
```

**Business Alerts (P1 - High)**:

```yaml
# Conversion Rate Drop
name: "[P1] Conversion Rate Drop"
condition: "conversion_rate < baseline * 0.8 for 10 minutes"
threshold: -20%  # 20% drop from baseline
window: 10m
action: "Notify on-call engineer and business stakeholders"

# Payment Processing Failures
name: "[P1] Payment Processing Failures"
condition: "payment_failure_rate > 5% for 10 minutes"
threshold: 5%
window: 10m
action: "Page on-call engineer, notify finance team"
```

**Alert Routing**:
- **P0**: PagerDuty → On-call engineer phone/SMS
- **P1**: PagerDuty → On-call engineer push notification + Slack #incidents
- **P2**: Slack #alerts-infrastructure
- **Escalation**: P0/P1 not acknowledged in 15 minutes → Escalate to senior engineer

#### 5. Dashboard Design

**Executive Dashboard**:
- SLO compliance and error budget (current month)
- Availability and latency trends (30 days)
- Business KPIs: Revenue, conversion rate, active users
- Incident count and MTTR

**Checkout Flow Dashboard**:
- Funnel visualization: Cart → Checkout → Payment → Confirmation
- Conversion rate and abandonment rate by step
- Payment method breakdown
- Error rate by checkout step
- Recent failed checkouts with trace links

**Service Dashboard (per service)**:
- RED metrics: Request rate, error rate, latency (p50/p95/p99)
- Service dependencies and health
- Recent errors with log and trace links
- Resource utilization (CPU, memory)
- Deployment markers

**Infrastructure Dashboard**:
- Kubernetes cluster health (nodes, pods)
- Resource utilization (CPU, memory, disk, network)
- Pod restarts and failures
- Capacity and saturation metrics

### Implementation Roadmap

**Phase 1: Foundation (Weeks 1-2)**
- Deploy Datadog agents to Kubernetes clusters (all regions)
- Implement structured logging in top 5 critical services:
  - checkout-service
  - payment-service
  - inventory-service
  - product-catalog-service
  - search-service
- Enable OpenTelemetry auto-instrumentation for these services
- Create basic RED metrics dashboards
- Set up initial SLO-based alerts
- Validate end-to-end tracing for checkout flow

**Phase 2: Expansion (Weeks 3-4)**
- Roll out structured logging to all 30+ services
- Implement custom instrumentation for business logic
- Create business metrics (conversion, revenue, abandonment)
- Implement PII masking and compliance controls
- Set up log archival to S3 (7-year retention for PCI-DSS)
- Create comprehensive dashboards for all services
- Implement alert routing and escalation policies

**Phase 3: Optimization (Weeks 5-6)**
- Implement adaptive trace sampling (10% normal, 1% peak)
- Optimize log sampling and filtering (10% for high-volume services)
- Tune alerts to reduce noise (target >70% alert-to-incident ratio)
- Create comprehensive runbooks for all alerts
- Train engineering teams on observability tools
- Establish weekly observability review process

**Phase 4: Advanced Features (Weeks 7-8)**
- Implement tail-based sampling for traces (100% errors, sampled normal)
- Create advanced correlation dashboards (logs + metrics + traces)
- Set up anomaly detection for business metrics
- Implement cost monitoring and optimization
- Document best practices and standards
- Conduct observability effectiveness review

### Cost Estimation

**Datadog Costs**:
- **Logs**: 500GB/day × $0.10/GB = $15,000/month
- **APM**: 100 hosts × $31/host + 5M spans/day × $1.27/M = $9,435/month
- **Infrastructure**: 100 hosts × $15/host = $1,500/month
- **Custom Metrics**: 500 custom metrics × $5/metric = $2,500/month

**AWS Costs**:
- **S3 Archive Storage**: 15TB × $0.023/GB = $345/month

**Total**: ~$28,780/month (within $50,000 budget, room for growth)

### Success Metrics

**Operational Metrics**:
- **MTTD**: <5 minutes for critical issues ✓ (Achieved: 3 minutes average)
- **MTTR**: <30 minutes for P0 incidents ✓ (Achieved: 22 minutes average)
- **SLO Compliance**: >99.95% availability ✓ (Achieved: 99.97%)

**Quality Metrics**:
- **Alert Quality**: >70% of alerts result in action ✓ (Achieved: 75%)
- **False Positive Rate**: <30% ✓ (Achieved: 25%)

**Cost Metrics**:
- **Observability Cost**: <5% of infrastructure cost ✓ (Achieved: 3.2%)

**Business Impact**:
- **Incident Detection**: 60% faster detection (from 8 min to 3 min)
- **Incident Resolution**: 40% faster resolution (from 45 min to 22 min)
- **Revenue Protection**: Prevented $2M in lost revenue (estimated) through faster incident response
- **Customer Satisfaction**: 15% reduction in checkout-related support tickets

### Key Learnings

1. **Start with Critical Paths**: Focusing on checkout flow first provided immediate value
2. **Adaptive Sampling**: Saved 50% on tracing costs while maintaining coverage for errors
3. **Business Metrics**: Correlating technical metrics with business KPIs enabled faster root cause analysis
4. **PII Masking**: Automated PII detection prevented compliance violations
5. **Runbooks**: Comprehensive runbooks reduced MTTR by 40%
6. **Cost Monitoring**: Regular cost reviews identified optimization opportunities (saved $8,000/month)

---

## Example 2: Banking Platform Observability Design

*[See SKILL.md for complete Example 2]*

---

## Example 3: SaaS Platform Observability Design

*[See SKILL.md for complete Example 3]*

---

## Example 4: AI/ML Platform Observability Design

*[See SKILL.md for complete Example 4]*
