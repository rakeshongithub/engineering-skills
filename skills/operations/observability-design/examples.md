# Observability Design: Examples

This document provides comprehensive examples of observability design for different types of systems and scenarios.

---

## Example 1: E-commerce Platform Observability

### Context

**System:**
- Microservices architecture (15 services)
- 10,000 requests/second peak traffic
- Critical user journey: Browse → Add to Cart → Checkout → Payment → Order Confirmation

**Requirements:**
- 99.9% availability SLO (43 min downtime/month)
- p95 latency < 500ms
- Error rate < 0.1%
- PCI DSS compliance (payment data)

**Challenges:**
- Distributed transactions across multiple services
- Third-party payment gateway (variable latency)
- High traffic spikes during sales events
- Need to track business metrics (revenue, conversion rate)

### Observability Design

#### Logging Strategy

**Centralized Logging:** Elasticsearch + Logstash + Kibana (ELK)

**Log Format:**
```json
{
  "timestamp": "2026-09-09T10:15:30.123Z",
  "level": "INFO",
  "service": "checkout-service",
  "environment": "production",
  "correlation_id": "abc123-def456-ghi789",
  "user_id": "user_12345",
  "session_id": "session_67890",
  "message": "Order created successfully",
  "order_id": "order_11111",
  "amount": 99.99,
  "currency": "USD",
  "payment_method": "credit_card",
  "items_count": 3,
  "duration_ms": 245,
  "version": "1.2.3",
  "host": "checkout-pod-7f8d9c"
}
```

**Retention:**
- Hot storage (fast queries): 7 days
- Warm storage: 30 days
- Cold storage (compliance): 1 year

**PII Redaction:**
```json
{
  "credit_card": "4532-****-****-9010",
  "email": "u***@example.com",
  "full_name": "[REDACTED]"
}
```

**Critical Logs:**
```javascript
// Order creation
logger.info('Order created', {
  correlation_id: req.correlationId,
  user_id: user.id,
  order_id: order.id,
  amount: order.total,
  currency: 'USD',
  items_count: order.items.length,
  payment_method: order.paymentMethod,
  duration_ms: Date.now() - startTime
});

// Payment processing
logger.info('Payment processed', {
  correlation_id: req.correlationId,
  order_id: order.id,
  payment_gateway: 'stripe',
  transaction_id: transaction.id,
  amount: order.total,
  duration_ms: Date.now() - startTime
});

// Inventory update
logger.info('Inventory updated', {
  correlation_id: req.correlationId,
  order_id: order.id,
  items_updated: items.length,
  duration_ms: Date.now() - startTime
});

// Errors
logger.error('Payment failed', {
  correlation_id: req.correlationId,
  order_id: order.id,
  payment_gateway: 'stripe',
  error_code: error.code,
  error_message: error.message,
  amount: order.total,
  retry_count: retryCount
});
```

#### Metrics Strategy

**Metrics Backend:** Prometheus + Thanos (long-term storage)

**RED Metrics (all services):**
```
# Rate
http_requests_total{service="checkout", endpoint="/api/checkout", method="POST", status_code="200"}

# Errors
http_requests_total{service="checkout", endpoint="/api/checkout", method="POST", status_code="500"}

# Duration
http_request_duration_seconds{service="checkout", endpoint="/api/checkout", method="POST"}
```

**Business Metrics:**
```
# Orders
orders_created_total{status="completed"}
orders_created_total{status="failed"}

# Revenue
revenue_dollars_total{currency="USD"}
average_order_value_dollars{currency="USD"}

# Conversion
checkout_started_total
checkout_completed_total
checkout_abandonment_rate

# Cart
cart_items_added_total
cart_items_removed_total
average_cart_size

# Inventory
inventory_out_of_stock_total{product_id="..."}
```

**Infrastructure Metrics (USE):**
```
# Utilization
container_cpu_usage_percent{service="checkout"}
container_memory_usage_percent{service="checkout"}

# Saturation
container_cpu_throttling_seconds_total{service="checkout"}
container_memory_oom_kills_total{service="checkout"}

# Errors
container_restarts_total{service="checkout"}
```

**Dependency Metrics:**
```
# Database
database_connections_active{service="checkout", database="orders_db"}
database_query_duration_seconds{service="checkout", operation="SELECT"}

# Payment Gateway
payment_gateway_request_duration_seconds{gateway="stripe"}
payment_gateway_errors_total{gateway="stripe", error_type="timeout"}

# Message Queue
queue_messages_pending{queue="order_processing"}
queue_processing_duration_seconds{queue="order_processing"}
```

**Retention:**
- Raw data (15s): 7 days
- 1m aggregation: 30 days
- 5m aggregation: 90 days
- 1h aggregation: 1 year

#### Tracing Strategy

**Tracing Backend:** Jaeger
**Standard:** OpenTelemetry

**Sampling Strategy:**
```yaml
sampling:
  # Always sample errors
  - type: always_on
    condition: status_code >= 500
  
  # Always sample slow requests
  - type: always_on
    condition: duration > 1s
  
  # Always sample checkout flow
  - type: always_on
    condition: endpoint == "/api/checkout"
  
  # Sample 10% of everything else
  - type: probabilistic
    rate: 0.1
```

**Critical Trace: Checkout Flow**
```
Trace ID: abc123-def456-ghi789
Total Duration: 487ms

Spans:
1. POST /api/checkout (API Gateway) - 487ms
   ├── 2. Validate cart (Checkout Service) - 12ms
   ├── 3. Calculate total (Checkout Service) - 8ms
   ├── 4. Process payment (Payment Service) - 312ms
   │   ├── 5. Call Stripe API (External) - 298ms
   │   └── 6. Save transaction (Database) - 14ms
   ├── 7. Create order (Order Service) - 89ms
   │   ├── 8. Insert order (Database) - 45ms
   │   └── 9. Update inventory (Inventory Service) - 44ms
   │       └── 10. Update stock (Database) - 38ms
   └── 11. Send confirmation (Notification Service) - 66ms
       └── 12. Queue email (Message Queue) - 5ms
```

**Span Attributes:**
```javascript
// Checkout span
span.setAttributes({
  'http.method': 'POST',
  'http.url': '/api/checkout',
  'http.status_code': 200,
  'user.id': 'user_12345',
  'order.id': 'order_11111',
  'order.amount': 99.99,
  'order.currency': 'USD',
  'order.items_count': 3
});

// Payment span
span.setAttributes({
  'payment.gateway': 'stripe',
  'payment.method': 'credit_card',
  'payment.amount': 99.99,
  'payment.transaction_id': 'txn_abc123'
});

// Database span
span.setAttributes({
  'db.system': 'postgresql',
  'db.name': 'orders_db',
  'db.operation': 'INSERT',
  'db.statement': 'INSERT INTO orders (user_id, total, status) VALUES ($1, $2, $3)',
  'db.rows_affected': 1
});
```

**Retention:** 30 days

#### Alerting Strategy

**Critical Alerts (PagerDuty):**
```yaml
# Service down
- alert: ServiceDown
  expr: up{service="checkout"} == 0
  for: 2m
  severity: critical
  
# High error rate
- alert: HighErrorRate
  expr: |
    sum(rate(http_requests_total{status_code=~"5.."}[5m])) by (service)
    /
    sum(rate(http_requests_total[5m])) by (service)
    > 0.05
  for: 5m
  severity: critical
  
# SLO breach
- alert: SLOBreach
  expr: |
    sum(rate(http_requests_total{status_code=~"5.."}[30m]))
    /
    sum(rate(http_requests_total[30m]))
    > 0.001
  for: 10m
  severity: critical
  
# Payment gateway down
- alert: PaymentGatewayDown
  expr: payment_gateway_errors_total{gateway="stripe"} > 10
  for: 5m
  severity: critical
```

**Warning Alerts (Slack):**
```yaml
# Elevated error rate
- alert: ElevatedErrorRate
  expr: |
    sum(rate(http_requests_total{status_code=~"5.."}[5m])) by (service)
    /
    sum(rate(http_requests_total[5m])) by (service)
    > 0.01
  for: 10m
  severity: warning
  
# High latency
- alert: HighLatency
  expr: |
    histogram_quantile(0.95,
      rate(http_request_duration_seconds_bucket[5m])
    ) > 0.5
  for: 10m
  severity: warning
  
# High CPU
- alert: HighCPU
  expr: container_cpu_usage_percent{service="checkout"} > 80
  for: 15m
  severity: warning
```

#### Dashboards

**1. Platform Health Dashboard**
```
+--------------------------------------------------+
| E-commerce Platform Health                       |
+--------------------------------------------------+
| Request Rate    | Error Rate     | p95 Latency   |
| 8,542 req/s     | 0.03%          | 287ms         |
| [graph]         | [graph]        | [graph]       |
+--------------------------------------------------+
| Service Health                                   |
| ✅ API Gateway   ✅ Checkout     ✅ Payment       |
| ✅ Order         ✅ Inventory    ✅ Notification  |
+--------------------------------------------------+
| Business Metrics                                 |
| Orders/min: 142 | Revenue/hr: $8,542             |
| Conversion: 3.2%| AOV: $87.50                    |
+--------------------------------------------------+
```

**2. Checkout Service Dashboard**
```
+--------------------------------------------------+
| Checkout Service                                 |
+--------------------------------------------------+
| RED Metrics                                      |
| Rate: 1,245/s   | Errors: 0.02%  | p95: 312ms    |
| [graph]         | [graph]        | [graph]       |
+--------------------------------------------------+
| Dependencies                                     |
| Payment Gateway | Database       | Inventory     |
| p95: 298ms      | p95: 45ms      | p95: 44ms     |
| [graph]         | [graph]        | [graph]       |
+--------------------------------------------------+
| Resources                                        |
| CPU: 45%        | Memory: 62%    | Connections   |
| [gauge]         | [gauge]        | 234/500       |
+--------------------------------------------------+
```

**3. SLO Dashboard**
```
+--------------------------------------------------+
| SLO: 99.9% Availability                          |
+--------------------------------------------------+
| Current: 99.97% ✅                               |
| Error Budget: 92% remaining                      |
| Burn Rate: 0.3x (healthy)                        |
+--------------------------------------------------+
| [30-day availability graph]                      |
+--------------------------------------------------+
| Recent SLO Breaches:                             |
| - 2026-09-01: 99.85% (payment gateway outage)    |
+--------------------------------------------------+
```

**4. Business Metrics Dashboard**
```
+--------------------------------------------------+
| E-commerce Business Metrics                      |
+--------------------------------------------------+
| Revenue                                          |
| Today: $204,500 | This week: $1.2M               |
| [graph]                                          |
+--------------------------------------------------+
| Orders                                           |
| Today: 2,340    | Avg: 142/hr                    |
| [graph]                                          |
+--------------------------------------------------+
| Conversion Funnel                                |
| Visits: 100,000 → Cart: 15,000 → Checkout: 3,200 |
| → Completed: 2,340 (2.34% conversion)           |
+--------------------------------------------------+
```

### Implementation

**Phase 1 (Week 1-2): Infrastructure**
- Set up ELK stack
- Set up Prometheus + Thanos
- Set up Jaeger
- Create instrumentation library

**Phase 2 (Week 3-4): Critical Services**
- Instrument: API Gateway, Checkout, Payment, Order
- Create dashboards
- Set up critical alerts

**Phase 3 (Week 5-6): Remaining Services**
- Instrument: Inventory, Notification, User, Product
- Complete dashboard suite
- Tune alerting

**Phase 4 (Week 7-8): Optimization**
- Optimize sampling rates
- Reduce costs (retention, sampling)
- Create runbooks
- Team training

### Outcomes

**Before Observability:**
- MTTD: 15-30 minutes (manual discovery)
- MTTR: 2-4 hours (difficult debugging)
- Incident frequency: 8-10/month
- Customer complaints: High

**After Observability:**
- MTTD: < 3 minutes (automatic alerts)
- MTTR: < 20 minutes (fast debugging with traces)
- Incident frequency: 2-3/month (proactive detection)
- Customer complaints: Low

**Cost:**
- Infrastructure: $2,000/month (ELK, Prometheus, Jaeger on AWS)
- Engineering time: 3 person-months (implementation)
- Ongoing: 0.5 person-month (maintenance)

---

## Example 2: SaaS Application Observability

### Context

**System:**
- Multi-tenant SaaS platform
- 1,000 customers, 50,000 users
- Monolith + microservices hybrid
- Critical: Data processing pipeline (ETL)

**Requirements:**
- 99.95% availability SLO (21.6 min downtime/month)
- Per-tenant metrics and isolation
- SOC 2 and GDPR compliance
- p95 latency < 1s

**Challenges:**
- Multi-tenancy (need per-tenant metrics)
- Compliance (long retention, PII handling)
- Mixed architecture (monolith + microservices)
- Background jobs (ETL pipeline)

### Observability Design

#### Logging Strategy

**Centralized Logging:** Splunk (compliance requirements)

**Log Format:**
```json
{
  "timestamp": "2026-09-09T10:15:30.123Z",
  "level": "INFO",
  "service": "data-pipeline",
  "environment": "production",
  "correlation_id": "abc123-def456-ghi789",
  "tenant_id": "tenant_acme",
  "user_id": "user_12345",
  "message": "ETL job completed",
  "job_id": "job_67890",
  "records_processed": 10000,
  "duration_ms": 45000,
  "version": "2.1.0"
}
```

**Retention:**
- Hot storage: 30 days
- Warm storage: 90 days
- Cold storage (compliance): 7 years

**PII Redaction:**
```json
{
  "email": "[REDACTED]",
  "phone": "[REDACTED]",
  "ip_address": "[REDACTED]",
  "user_name": "[REDACTED]"
}
```

**Tenant Isolation:**
```javascript
// All logs include tenant_id for filtering
logger.info('Data export started', {
  tenant_id: tenant.id,
  export_id: export.id,
  records_count: recordsCount
});

// Splunk query for tenant-specific logs
// index=saas tenant_id="tenant_acme" level=ERROR
```

#### Metrics Strategy

**Metrics Backend:** Datadog (unified platform)

**Per-Tenant Metrics:**
```
# API usage
api_requests_total{tenant_id="tenant_acme", endpoint="/api/data"}

# Resource usage
tenant_storage_bytes{tenant_id="tenant_acme"}
tenant_compute_seconds{tenant_id="tenant_acme"}

# Business metrics
tenant_active_users{tenant_id="tenant_acme"}
tenant_api_calls_total{tenant_id="tenant_acme"}
```

**ETL Pipeline Metrics:**
```
# Job metrics
etl_jobs_started_total{job_type="daily_sync"}
etl_jobs_completed_total{job_type="daily_sync", status="success"}
etl_jobs_completed_total{job_type="daily_sync", status="failed"}
etl_job_duration_seconds{job_type="daily_sync"}

# Data metrics
etl_records_processed_total{job_type="daily_sync"}
etl_records_failed_total{job_type="daily_sync"}
etl_data_quality_score{job_type="daily_sync"}
```

**Multi-Tenancy Metrics:**
```
# Noisy neighbor detection
tenant_cpu_usage_percent{tenant_id="..."}
tenant_memory_usage_bytes{tenant_id="..."}
tenant_request_rate{tenant_id="..."}

# Fair usage
tenant_rate_limit_exceeded_total{tenant_id="..."}
```

#### Tracing Strategy

**Tracing Backend:** Datadog APM

**Sampling:**
```yaml
sampling:
  # Always sample errors
  - type: always_on
    condition: status_code >= 500
  
  # Always sample slow requests
  - type: always_on
    condition: duration > 2s
  
  # Sample 5% of normal requests (cost optimization)
  - type: probabilistic
    rate: 0.05
  
  # Always sample ETL jobs
  - type: always_on
    condition: job_type == "etl"
```

**Critical Trace: ETL Pipeline**
```
Trace ID: etl-job-67890
Total Duration: 45,234ms

Spans:
1. ETL Job: daily_sync - 45,234ms
   ├── 2. Extract data from source (External API) - 12,456ms
   │   ├── 3. Fetch page 1 (1000 records) - 2,345ms
   │   ├── 4. Fetch page 2 (1000 records) - 2,234ms
   │   └── ... (10 pages total)
   ├── 5. Transform data - 8,123ms
   │   ├── 6. Validate schema - 1,234ms
   │   ├── 7. Enrich data - 4,567ms
   │   └── 8. Clean data - 2,322ms
   └── 9. Load data to warehouse - 24,655ms
       ├── 10. Batch insert (batch 1) - 5,234ms
       ├── 11. Batch insert (batch 2) - 5,123ms
       └── ... (5 batches total)
```

#### Alerting Strategy

**Critical Alerts:**
```yaml
# ETL job failures
- alert: ETLJobFailed
  expr: etl_jobs_completed_total{status="failed"} > 0
  for: 5m
  severity: critical
  
# Tenant service degradation
- alert: TenantServiceDegraded
  expr: |
    sum(rate(api_requests_total{status_code=~"5.."}[5m])) by (tenant_id)
    /
    sum(rate(api_requests_total[5m])) by (tenant_id)
    > 0.05
  for: 5m
  severity: critical
  
# Data quality issues
- alert: DataQualityLow
  expr: etl_data_quality_score < 0.95
  for: 10m
  severity: critical
```

**Per-Tenant Alerts:**
```yaml
# Tenant-specific error rate
- alert: TenantHighErrorRate
  expr: |
    sum(rate(api_requests_total{tenant_id="tenant_acme", status_code=~"5.."}[5m]))
    /
    sum(rate(api_requests_total{tenant_id="tenant_acme"}[5m]))
    > 0.05
  for: 5m
  severity: warning
  annotations:
    summary: "High error rate for tenant {{ $labels.tenant_id }}"
```

#### Dashboards

**1. Platform Health Dashboard**
```
+--------------------------------------------------+
| SaaS Platform Health                             |
+--------------------------------------------------+
| Overall Availability: 99.97% ✅                  |
| Active Tenants: 987 | Active Users: 48,234       |
+--------------------------------------------------+
| API Health                                       |
| Rate: 2,345/s   | Errors: 0.02%  | p95: 487ms    |
| [graph]         | [graph]        | [graph]       |
+--------------------------------------------------+
| ETL Pipeline                                     |
| Jobs today: 1,000 | Success: 998 | Failed: 2     |
| Avg duration: 42s | p95: 89s                      |
+--------------------------------------------------+
```

**2. Tenant Dashboard (per-tenant view)**
```
+--------------------------------------------------+
| Tenant: Acme Corp                                |
+--------------------------------------------------+
| API Usage                                        |
| Requests today: 125,000 | Quota: 1M (12.5% used)  |
| [graph]                                          |
+--------------------------------------------------+
| Performance                                      |
| Availability: 99.98% | p95 latency: 423ms        |
| Error rate: 0.01%                                |
+--------------------------------------------------+
| Resource Usage                                   |
| Storage: 45GB / 100GB | Compute: 234h / 1000h    |
+--------------------------------------------------+
| Active Users: 487 | API Calls/user: 256          |
+--------------------------------------------------+
```

**3. ETL Pipeline Dashboard**
```
+--------------------------------------------------+
| ETL Pipeline Health                              |
+--------------------------------------------------+
| Jobs (24h)                                       |
| Started: 1,000 | Completed: 998 | Failed: 2      |
| Success rate: 99.8%                              |
| [graph]                                          |
+--------------------------------------------------+
| Performance                                      |
| Avg duration: 42s | p95: 89s | p99: 145s        |
| [graph]                                          |
+--------------------------------------------------+
| Data Quality                                     |
| Records processed: 10M | Failed: 1,234 (0.01%)   |
| Quality score: 99.2%                             |
+--------------------------------------------------+
```

### Outcomes

**Compliance:**
- ✅ SOC 2 audit passed (comprehensive logging)
- ✅ GDPR compliant (PII redaction, 7-year retention)
- ✅ Audit trail for all tenant actions

**Multi-Tenancy:**
- ✅ Per-tenant metrics and dashboards
- ✅ Noisy neighbor detection
- ✅ Fair usage monitoring

**ETL Pipeline:**
- ✅ Job failure detection < 5 min
- ✅ Data quality monitoring
- ✅ Performance optimization (reduced p95 from 145s to 89s)

**Cost:**
- Datadog: $8,000/month (unified platform)
- Splunk: $12,000/month (compliance logging)
- Total: $20,000/month

---

## Example 3: Microservices Platform (High Volume)

### Context

**System:**
- 50+ microservices
- Kubernetes deployment (200+ pods)
- 100,000 requests/second peak
- Global deployment (3 regions)

**Requirements:**
- 99.99% availability SLO (4.3 min downtime/month)
- p99 latency < 100ms
- Cost-effective observability (high volume)

**Challenges:**
- Very high request volume (cost)
- Complex service mesh
- Multi-region deployment
- Need for distributed tracing

### Observability Design

#### Logging Strategy

**Centralized Logging:** Grafana Loki (cost-effective)

**Log Sampling:**
```
Sample 100%:
- Errors (level=ERROR)
- Warnings (level=WARN)
- Critical business events

Sample 10%:
- Info logs (level=INFO)

Sample 1%:
- Debug logs (level=DEBUG, if enabled)
```

**Retention:**
- Hot storage: 7 days (cost optimization)
- Warm storage: 30 days (sampled)
- No long-term retention (use metrics for trends)

#### Metrics Strategy

**Metrics Backend:** Prometheus + Thanos

**Cardinality Control:**
```
# ✅ GOOD: Low cardinality
http_requests_total{
  service="api-gateway",
  region="us-east-1",
  endpoint_group="/api/v1/*",  # Grouped endpoints
  method="POST",
  status_code="200"
}

# ❌ BAD: High cardinality (avoided)
http_requests_total{
  endpoint="/api/v1/users/12345/orders/67890",  # Unique per request
  user_id="12345"  # Millions of users
}
```

**Aggregation:**
```
# Pre-aggregate high-volume metrics
recording_rules:
  - record: service:http_requests:rate5m
    expr: sum(rate(http_requests_total[5m])) by (service)
  
  - record: service:http_errors:rate5m
    expr: sum(rate(http_requests_total{status_code=~"5.."}[5m])) by (service)
  
  - record: service:http_latency:p95
    expr: histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (service, le))
```

#### Tracing Strategy

**Tracing Backend:** Grafana Tempo (cost-effective)

**Aggressive Sampling (cost optimization):**
```yaml
sampling:
  # Always sample errors
  - type: always_on
    condition: status_code >= 500
  
  # Always sample very slow requests
  - type: always_on
    condition: duration > 5s
  
  # Sample 1% of normal requests (high volume)
  - type: probabilistic
    rate: 0.01
  
  # Tail-based sampling (if trace contains error)
  - type: tail_based
    condition: contains_error == true
    rate: 1.0
```

**Retention:** 7 days (cost optimization)

#### Alerting Strategy

**SLO-Based Alerting:**
```yaml
# Multi-window, multi-burn-rate alerts
- alert: ErrorBudgetBurnRateFast
  expr: |
    (
      sum(rate(http_requests_total{status_code=~"5.."}[1h]))
      /
      sum(rate(http_requests_total[1h]))
    ) > (14.4 * 0.0001)  # 14.4x burn rate for 99.99% SLO
  for: 2m
  severity: critical
  annotations:
    summary: "Fast burn rate: will exhaust error budget in 2 days"

- alert: ErrorBudgetBurnRateSlow
  expr: |
    (
      sum(rate(http_requests_total{status_code=~"5.."}[6h]))
      /
      sum(rate(http_requests_total[6h]))
    ) > (6 * 0.0001)  # 6x burn rate
  for: 15m
  severity: warning
  annotations:
    summary: "Slow burn rate: will exhaust error budget in 5 days"
```

#### Cost Optimization

**Strategies:**
```
Logging:
- Sample info logs (10%)
- Short retention (7 days)
- Use Loki (cheaper than ELK)
- Savings: 70% vs. full logging

Metrics:
- Control cardinality
- Use recording rules
- Downsample old data
- Use Thanos (object storage)
- Savings: 60% vs. full retention

Tracing:
- Aggressive sampling (1%)
- Short retention (7 days)
- Use Tempo (object storage)
- Savings: 90% vs. full tracing

Total cost: $3,000/month (vs. $30,000 without optimization)
```

### Outcomes

**Performance:**
- ✅ 99.99% availability achieved
- ✅ p99 latency < 80ms (better than target)
- ✅ MTTD < 2 minutes
- ✅ MTTR < 15 minutes

**Cost:**
- ✅ $3,000/month (10x cheaper than alternatives)
- ✅ 90% cost reduction through sampling and optimization

**Scale:**
- ✅ Handles 100,000 req/s
- ✅ 50+ services instrumented
- ✅ 200+ pods monitored

---

## Example 4: Monolith to Microservices Migration

### Context

**System:**
- Legacy monolith (10 years old)
- Gradual migration to microservices
- Hybrid architecture during migration
- 3-year migration timeline

**Requirements:**
- Maintain observability during migration
- Track migration progress
- Compare monolith vs. microservices performance
- No disruption to existing monitoring

**Challenges:**
- Different instrumentation (monolith vs. microservices)
- Distributed tracing across monolith and microservices
- Gradual rollout (feature flags)
- Need to compare old vs. new

### Observability Design

#### Phase 1: Instrument Monolith

**Add basic observability to monolith:**

```ruby
# Ruby monolith instrumentation
class ApplicationController < ActionController::Base
  around_action :instrument_request
  
  def instrument_request
    start_time = Time.now
    correlation_id = request.headers['X-Correlation-ID'] || SecureRandom.uuid
    
    # Add correlation ID to logs
    Thread.current[:correlation_id] = correlation_id
    
    # Start trace span
    tracer = OpenTelemetry.tracer_provider.tracer('monolith')
    span = tracer.start_span("#{request.method} #{request.path}")
    
    begin
      yield
      
      # Log request
      Rails.logger.info({
        message: 'Request completed',
        correlation_id: correlation_id,
        method: request.method,
        path: request.path,
        status: response.status,
        duration_ms: ((Time.now - start_time) * 1000).to_i
      }.to_json)
      
      # Record metrics
      METRICS[:requests].increment(
        labels: {
          method: request.method,
          path: request.path,
          status: response.status
        }
      )
      
    rescue => error
      # Log error
      Rails.logger.error({
        message: 'Request failed',
        correlation_id: correlation_id,
        error: error.message,
        backtrace: error.backtrace.first(5)
      }.to_json)
      
      # Record error span
      span.record_exception(error)
      
      raise
    ensure
      span.finish
    end
  end
end
```

#### Phase 2: Add Distributed Tracing at API Gateway

**API Gateway routes to monolith or microservices:**

```javascript
// API Gateway (Node.js)
const { trace, context, propagation } = require('@opentelemetry/api');

app.use((req, res, next) => {
  const tracer = trace.getTracer('api-gateway');
  const span = tracer.startSpan(`${req.method} ${req.path}`);
  
  // Generate or extract correlation ID
  const correlationId = req.headers['x-correlation-id'] || uuidv4();
  req.correlationId = correlationId;
  
  // Determine routing (monolith vs. microservice)
  const useNewService = featureFlags.isEnabled('new-checkout-service', req.user);
  
  // Add routing metadata to span
  span.setAttributes({
    'http.method': req.method,
    'http.url': req.path,
    'routing.target': useNewService ? 'microservice' : 'monolith',
    'feature.flag': 'new-checkout-service',
    'feature.enabled': useNewService
  });
  
  // Propagate trace context
  const headers = {};
  propagation.inject(context.active(), headers);
  req.headers = { ...req.headers, ...headers, 'x-correlation-id': correlationId };
  
  // Route request
  if (useNewService) {
    proxyToMicroservice(req, res, span);
  } else {
    proxyToMonolith(req, res, span);
  }
});
```

#### Phase 3: Instrument New Microservices

**Full observability for new microservices:**

```python
# New checkout microservice (Python)
from opentelemetry import trace
from prometheus_client import Counter, Histogram
import logging

tracer = trace.get_tracer(__name__)
logger = logging.getLogger(__name__)

requests_total = Counter(
    'http_requests_total',
    'Total requests',
    ['service', 'endpoint', 'status_code', 'source']
)

request_duration = Histogram(
    'http_request_duration_seconds',
    'Request duration',
    ['service', 'endpoint', 'source']
)

@app.route('/api/checkout', methods=['POST'])
def checkout():
    with tracer.start_as_current_span('POST /api/checkout') as span:
        correlation_id = request.headers.get('x-correlation-id')
        
        # Mark as new microservice
        span.set_attribute('service.type', 'microservice')
        span.set_attribute('migration.phase', 'new')
        
        # Business logic
        result = process_checkout(request.json)
        
        # Log
        logger.info({
            'message': 'Checkout completed',
            'correlation_id': correlation_id,
            'service': 'checkout-microservice',
            'migration_phase': 'new'
        })
        
        # Metrics (with source label)
        requests_total.labels(
            service='checkout',
            endpoint='/api/checkout',
            status_code=200,
            source='microservice'
        ).inc()
        
        return result
```

#### Migration Tracking Metrics

**Track migration progress:**

```
# Traffic split (monolith vs. microservices)
http_requests_total{source="monolith", endpoint="/api/checkout"}
http_requests_total{source="microservice", endpoint="/api/checkout"}

# Migration percentage
migration_traffic_percent{endpoint="/api/checkout", target="microservice"}

# Performance comparison
http_request_duration_seconds{source="monolith", endpoint="/api/checkout"}
http_request_duration_seconds{source="microservice", endpoint="/api/checkout"}

# Error rate comparison
http_errors_total{source="monolith", endpoint="/api/checkout"}
http_errors_total{source="microservice", endpoint="/api/checkout"}
```

#### Migration Dashboard

```
+--------------------------------------------------+
| Migration Progress: Checkout Service             |
+--------------------------------------------------+
| Traffic Split                                    |
| Monolith: 60% (6,000 req/s)                      |
| Microservice: 40% (4,000 req/s)                  |
| [graph showing gradual shift]                    |
+--------------------------------------------------+
| Performance Comparison                           |
|                | Monolith  | Microservice        |
| p50 latency    | 245ms     | 187ms (-24%)        |
| p95 latency    | 567ms     | 423ms (-25%)        |
| p99 latency    | 1.2s      | 789ms (-34%)        |
+--------------------------------------------------+
| Error Rate Comparison                            |
| Monolith: 0.05% | Microservice: 0.03%            |
+--------------------------------------------------+
| Migration Readiness                              |
| ✅ Performance better                             |
| ✅ Error rate lower                               |
| ✅ No critical issues                             |
| ⚠️  Increase traffic to 60%                      |
+--------------------------------------------------+
```

### Migration Strategy

**Gradual rollout:**

```
Week 1-2: 10% traffic to microservice
  - Monitor closely
  - Compare metrics
  - Fix issues

Week 3-4: 25% traffic
  - Validate performance
  - Ensure stability

Week 5-6: 50% traffic
  - Equal split
  - Final validation

Week 7-8: 75% traffic
  - Prepare for full cutover

Week 9: 100% traffic
  - Decommission monolith endpoint
  - Archive monolith code
```

### Outcomes

**Observability:**
- ✅ End-to-end tracing (monolith + microservices)
- ✅ Correlation IDs across all systems
- ✅ Unified dashboards showing both

**Migration:**
- ✅ Data-driven migration decisions
- ✅ Performance improvements visible (25% latency reduction)
- ✅ Safe rollout with rollback capability
- ✅ Zero downtime migration

**Learnings:**
- Distributed tracing essential for hybrid architecture
- Feature flags + observability enable safe migration
- Comparison metrics critical for validation
- Gradual rollout reduces risk

---

## Summary

These examples demonstrate observability design for:

1. **E-commerce Platform:** High-traffic, distributed transactions, business metrics
2. **SaaS Application:** Multi-tenancy, compliance, ETL pipelines
3. **Microservices Platform:** High volume, cost optimization, scale
4. **Migration:** Hybrid architecture, gradual rollout, comparison metrics

**Common Patterns:**
- Structured logging with correlation IDs
- RED metrics for services, USE metrics for resources
- Intelligent sampling for cost optimization
- SLO-based alerting
- Business metrics alongside technical metrics
- Dashboards for different audiences (ops, business, leadership)

**Key Takeaways:**
- Observability design depends on system characteristics and requirements
- Cost optimization is critical at scale (sampling, retention, cardinality)
- Compliance requirements drive retention and PII handling
- Multi-tenancy requires per-tenant metrics and isolation
- Migration scenarios need comparison metrics and gradual rollout
