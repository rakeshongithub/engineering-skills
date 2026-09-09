# Observability Design

**Quick reference guide for designing comprehensive observability systems**

---

## Overview

Observability design is the practice of creating systems that provide visibility into application behavior through logs, metrics, and traces. This skill helps you design observability strategies that enable fast debugging, proactive monitoring, and operational excellence.

**Category:** Operations  
**Complexity:** Advanced  
**Time:** 4-8 hours  
**Prerequisites:** System architecture understanding, SLO definitions

---

## When to Use

✅ Designing observability for new systems  
✅ Improving existing monitoring capabilities  
✅ After incidents reveal observability gaps  
✅ Before production deployment (production readiness)  
✅ Implementing microservices or distributed systems  
✅ When troubleshooting time is too high  

---

## Quick Start

### 1. Define Requirements (30-60 min)
- Identify critical user journeys
- Define SLOs (availability, latency, error rate)
- List known failure modes
- Establish observability goals (MTTD, MTTR targets)

### 2. Design Three Pillars (2-3 hours)

**Logging:**
- Structured JSON logs
- Centralized aggregation (ELK, Splunk, Datadog)
- Correlation IDs for request tracing
- PII redaction
- Retention policy (7-30 days hot, longer for compliance)

**Metrics:**
- RED metrics (Rate, Errors, Duration) for services
- USE metrics (Utilization, Saturation, Errors) for resources
- Business metrics (orders, revenue, conversions)
- Low-cardinality labels
- Prometheus, Datadog, or CloudWatch

**Tracing:**
- OpenTelemetry standard
- Distributed tracing across services
- Intelligent sampling (always trace errors, sample normal requests)
- Jaeger, Zipkin, or Datadog APM

### 3. Design Alerting (45-75 min)
- SLO-based alerts (error budget burn rate)
- Critical alerts (page on-call)
- Warning alerts (Slack, email)
- Alert fatigue prevention (group, suppress, tune)
- Runbooks for common alerts

### 4. Create Dashboards (45-60 min)
- Service health dashboards (RED metrics)
- SLO dashboards (compliance, error budget)
- Business metrics dashboards
- Debugging dashboards (detailed metrics, logs, traces)

### 5. Implement (3-6 months)
- Set up infrastructure (logging, metrics, tracing backends)
- Create instrumentation libraries
- Instrument services (critical first)
- Create dashboards and alerts
- Train team

---

## Three Pillars Cheat Sheet

### Logs: What Happened

**Use for:**
- Detailed event information
- Debugging specific requests
- Audit trails
- Error details

**Best practices:**
```json
{
  "timestamp": "2026-09-09T10:15:30.123Z",
  "level": "INFO",
  "service": "checkout-service",
  "correlation_id": "abc123",
  "message": "Order created",
  "order_id": "12345",
  "duration_ms": 245
}
```

- Use structured logging (JSON)
- Include correlation IDs
- Redact PII
- Use appropriate log levels

### Metrics: How Much, How Fast

**Use for:**
- Aggregated data
- Trends over time
- Alerting
- SLO tracking

**RED metrics (services):**
```
Rate: http_requests_total
Errors: http_requests_total{status_code=~"5.."}
Duration: http_request_duration_seconds
```

**USE metrics (resources):**
```
Utilization: cpu_usage_percent
Saturation: load_average
Errors: disk_errors_total
```

**Best practices:**
- Use histograms for latency (not averages)
- Keep label cardinality low (< 100 unique values)
- Use consistent naming (snake_case, include unit)

### Traces: Where Time Was Spent

**Use for:**
- Request flow across services
- Latency breakdown
- Dependency mapping
- Performance optimization

**Best practices:**
- Use OpenTelemetry (vendor-neutral)
- Propagate trace context (W3C Trace Context)
- Sample intelligently:
  - 100% errors
  - 100% slow requests (> p95)
  - 1-10% normal requests
- Add meaningful span attributes

---

## Alerting Quick Reference

### Alert Categories

**Critical (P0):** Page on-call immediately
- Service completely down
- Error rate > 5%
- SLO breach
- Security incident

**Warning (P1):** Slack, email, ticket
- Error rate > 1%
- Latency degraded (> 1.5x SLO)
- Resource saturation (CPU > 80%)
- Error budget burning fast

**Info (P2):** Awareness only
- Deployment completed
- Configuration change

### SLO-Based Alerting

```yaml
# Fast burn rate (exhaust budget in 2 days)
- alert: ErrorBudgetBurnRateFast
  expr: |
    (error_rate_1h / slo_target) > 14.4
  for: 2m
  severity: critical

# Slow burn rate (exhaust budget in 5 days)
- alert: ErrorBudgetBurnRateSlow
  expr: |
    (error_rate_6h / slo_target) > 6
  for: 15m
  severity: warning
```

### Alert Fatigue Prevention

✅ Alert on symptoms (high latency), not causes (high CPU)  
✅ Group related alerts (by service, time window)  
✅ Suppress during maintenance  
✅ Link to runbooks  
✅ Review alerts weekly (> 50% should lead to action)  

---

## Tool Selection Guide

### Logging

| Tool | Best For | Cost |
|------|----------|------|
| **ELK Stack** | Self-hosted, high volume | Low (infra) |
| **Splunk** | Enterprise, compliance | High |
| **CloudWatch** | AWS-native | Medium |
| **Datadog** | Unified platform | High |
| **Grafana Loki** | Kubernetes, cost-conscious | Low |

### Metrics

| Tool | Best For | Cost |
|------|----------|------|
| **Prometheus** | Kubernetes, self-hosted | Low |
| **Datadog** | Unified platform | High |
| **New Relic** | APM-focused | High |
| **CloudWatch** | AWS-native | Medium |

### Tracing

| Tool | Best For | Cost |
|------|----------|------|
| **Jaeger** | Kubernetes, OpenTelemetry | Low |
| **Zipkin** | Simple setups | Low |
| **Datadog APM** | Unified platform | High |
| **Grafana Tempo** | Cost-conscious | Low |
| **AWS X-Ray** | AWS-native | Medium |

### All-in-One Platforms

**Datadog:** Logs + Metrics + Traces + APM (expensive, comprehensive)  
**New Relic:** APM-focused (expensive, good UI)  
**Grafana Stack:** Loki + Prometheus + Tempo (open source, cost-effective)  

---

## Common Mistakes

❌ **Logging everything** → Use appropriate log levels, sample if needed  
❌ **High cardinality metrics** → Limit unique label values (< 100)  
❌ **No sampling strategy** → Sample intelligently (always trace errors)  
❌ **Alert fatigue** → Alert only on actionable issues  
❌ **No correlation IDs** → Can't trace requests across services  
❌ **Ignoring cost** → Monitor and optimize observability costs  
❌ **No standardization** → Create instrumentation standards  
❌ **Observability as afterthought** → Design from the start  
❌ **No training** → Team won't use observability effectively  
❌ **No SLOs** → Don't know what to monitor or alert on  

---

## Cost Optimization

### Logging
- Sample info logs (10-50%)
- Short retention (7-30 days)
- Use cost-effective tools (Loki vs. Splunk)
- **Savings:** 50-70%

### Metrics
- Control cardinality (low unique label values)
- Use recording rules (pre-aggregate)
- Downsample old data (1m → 5m → 1h)
- Use object storage (Thanos, Mimir)
- **Savings:** 40-60%

### Tracing
- Aggressive sampling (1-10% normal requests)
- Short retention (7-30 days)
- Use object storage (Tempo)
- **Savings:** 80-90%

**Example:**
- Full observability: $30,000/month
- Optimized: $3,000/month (10x reduction)

---

## Success Metrics

### Coverage
✅ 100% of critical services instrumented  
✅ 80%+ of all services instrumented  
✅ All services have health dashboards  
✅ All services have alerts  

### Quality
✅ MTTD (Mean Time to Detection) < 5 min  
✅ MTTR (Mean Time to Resolution) < 30 min  
✅ Alert-to-incident ratio > 50%  
✅ Zero incidents due to lack of observability  

### Adoption
✅ 90%+ of engineers trained  
✅ Instrumentation libraries used by all services  
✅ Standards followed consistently  

### Cost
✅ Observability costs < 5% of infrastructure costs  
✅ No unexpected cost spikes  

---

## Example: E-commerce Platform

**System:** 15 microservices, 10K req/s, 99.9% SLO

**Logging:**
- ELK stack
- Structured JSON with correlation IDs
- 30-day retention
- PII redaction

**Metrics:**
- Prometheus + Thanos
- RED metrics for all services
- Business metrics (orders/min, revenue/hr)
- 90-day retention

**Tracing:**
- OpenTelemetry + Jaeger
- 10% sampling (100% for errors)
- 30-day retention

**Alerting:**
- Critical: Error rate > 1%, Latency > 1s (PagerDuty)
- Warning: Error rate > 0.5%, Latency > 500ms (Slack)

**Results:**
- MTTD: 15-30 min → < 3 min
- MTTR: 2-4 hours → < 20 min
- Incidents: 8-10/month → 2-3/month

---

## Related Skills

**Prerequisites:**
- `architecture-discovery` — Understand system structure
- `system-design` — Design observability into system

**Commonly Followed By:**
- `incident-analysis` — Use observability for incident response
- `production-readiness` — Validate observability before production
- `capacity-planning` — Use metrics for capacity planning

**Works With:**
- `agent-observability` — Observability for AI agents
- `reliability-analysis` — Use observability data for reliability
- `performance-optimization` — Find bottlenecks with observability

---

## Resources

**Documentation:**
- [SKILL.md](./SKILL.md) — Comprehensive guide
- [instructions.md](./instructions.md) — Step-by-step workflow
- [examples.md](./examples.md) — Real-world examples

**External Resources:**
- [OpenTelemetry](https://opentelemetry.io/) — Observability standard
- [Prometheus](https://prometheus.io/) — Metrics and alerting
- [Grafana](https://grafana.com/) — Dashboards and visualization
- [Google SRE Book](https://sre.google/books/) — SLO-based monitoring

---

## Quick Commands

### Prometheus Queries

```promql
# Request rate (requests per second)
rate(http_requests_total[5m])

# Error rate (percentage)
sum(rate(http_requests_total{status_code=~"5.."}[5m]))
/
sum(rate(http_requests_total[5m]))

# p95 latency
histogram_quantile(0.95,
  rate(http_request_duration_seconds_bucket[5m])
)

# Availability (percentage)
1 - (
  sum(rate(http_requests_total{status_code=~"5.."}[30d]))
  /
  sum(rate(http_requests_total[30d]))
)
```

### Log Queries (Elasticsearch)

```
# Find errors in last hour
service:checkout AND level:ERROR AND @timestamp:[now-1h TO now]

# Find slow requests
service:checkout AND duration_ms:>1000

# Trace specific request
correlation_id:"abc123-def456-ghi789"

# Find payment failures
service:payment AND message:"Payment failed"
```

---

## Version

**1.0.0** — Initial release

---

**Next Steps:**
1. Read [SKILL.md](./SKILL.md) for comprehensive guide
2. Follow [instructions.md](./instructions.md) for step-by-step workflow
3. Review [examples.md](./examples.md) for real-world scenarios
4. Implement observability for your system
5. Use observability for incident response (see `incident-analysis` skill)
