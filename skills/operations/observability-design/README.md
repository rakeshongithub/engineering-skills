# Observability Design Skill

## Quick Reference

**Purpose**: Design comprehensive logging, metrics, and distributed tracing systems for production observability.

**Category**: Operations  
**Complexity**: Advanced  
**Estimated Time**: 4-8 weeks

## Overview

This skill guides you through designing comprehensive observability systems that provide complete visibility into distributed systems, enable rapid incident detection and resolution, and support data-driven operational decisions.

## When to Use This Skill

✅ **Use this skill when**:
- Designing observability for new systems or architectures
- Preparing systems for production deployment
- Implementing SRE practices and SLO monitoring
- Debugging complex distributed system failures
- Meeting compliance and audit requirements
- Optimizing system performance and costs

❌ **Don't use this skill when**:
- Building simple, single-server applications
- Working on early-stage prototypes or MVPs
- Operating in resource-constrained environments (IoT, edge)
- Dealing with legacy systems without modification access

## Key Deliverables

1. **Observability Architecture Document**
   - Three-pillar design (logs, metrics, traces)
   - Tool selection and justification
   - Data flow and storage architecture

2. **Logging Strategy**
   - Structured logging format and standards
   - Log aggregation and retention policies
   - PII masking and compliance controls

3. **Metrics Framework**
   - SLI/SLO definitions and error budgets
   - RED metrics (Rate, Errors, Duration)
   - USE metrics (Utilization, Saturation, Errors)

4. **Distributed Tracing Design**
   - Trace context propagation standards
   - Sampling strategy (head-based, tail-based, adaptive)
   - Instrumentation guidelines

5. **Alerting Strategy**
   - Alert severity levels and escalation policies
   - SLO-based and symptom-based alerts
   - Runbooks and notification routing

6. **Dashboards**
   - Executive/business dashboards
   - Service operational dashboards
   - Infrastructure monitoring dashboards
   - Incident response dashboards

7. **Implementation Roadmap**
   - Phased rollout plan
   - Service prioritization
   - Training and enablement

## Quick Start Guide

### Phase 1: Discovery (Week 1)
1. Understand system architecture and dependencies
2. Define SLAs, SLOs, and business KPIs
3. Assess current observability state and identify gaps

### Phase 2: Design Logging (Week 2)
4. Design structured logging framework
5. Plan log aggregation and storage
6. Implement log correlation and context propagation

### Phase 3: Design Metrics (Week 3)
7. Define metrics taxonomy and SLI/SLO framework
8. Design metrics collection and storage
9. Create alerting and notification strategy

### Phase 4: Design Tracing (Week 4)
10. Design distributed tracing architecture
11. Define instrumentation standards

### Phase 5: Implementation Planning (Week 5)
12. Create dashboards and visualization strategy
13. Develop implementation roadmap
14. Establish governance and continuous improvement

## Three Pillars of Observability

### 1. Logs
**What**: Discrete events with context  
**When**: Debugging specific issues, audit trails  
**Example**: "Order 12345 failed payment processing: Invalid card"

### 2. Metrics
**What**: Numerical measurements over time  
**When**: Monitoring trends, alerting, capacity planning  
**Example**: "Request rate: 5,000 req/s, Error rate: 0.5%, p95 latency: 150ms"

### 3. Traces
**What**: End-to-end request flow across services  
**When**: Understanding distributed system behavior, performance optimization  
**Example**: "Request took 450ms: API Gateway (50ms) → Service A (200ms) → Database (150ms) → Service B (50ms)"

## Common Patterns

### SLI/SLO Framework
```
SLI (Service Level Indicator): Metric measuring service quality
  Example: "Availability = successful_requests / total_requests"

SLO (Service Level Objective): Target for SLI
  Example: "Availability ≥ 99.9% over 30 days"

Error Budget: Allowed failure
  Example: "0.1% error budget = 43.2 minutes downtime/month"
```

### RED Metrics (for request-driven services)
- **Rate**: Requests per second
- **Errors**: Error count and rate
- **Duration**: Latency distribution (p50, p95, p99)

### USE Metrics (for resources)
- **Utilization**: % of resource capacity used
- **Saturation**: Degree of resource overload
- **Errors**: Resource errors

## Tool Selection Guide

### Logging Platforms
- **ELK Stack**: Self-hosted, flexible, powerful search
- **Splunk**: Enterprise, mature, expensive
- **Datadog Logs**: Cloud-native, integrated, easy setup
- **Grafana Loki**: Cost-effective, Kubernetes-native

### Metrics Platforms
- **Prometheus**: Open-source, pull-based, Kubernetes-native
- **Datadog**: Cloud-native, comprehensive, expensive
- **CloudWatch**: AWS-native, simple, limited features
- **Grafana Cloud**: Managed Prometheus, scalable

### Tracing Platforms
- **Jaeger**: Open-source, mature, self-hosted
- **Datadog APM**: Integrated, auto-instrumentation
- **AWS X-Ray**: AWS-native, easy integration
- **Honeycomb**: High-cardinality, powerful querying

## Cost Optimization Tips

1. **Implement Sampling**
   - Logs: Sample INFO (10%), keep all WARN/ERROR (100%)
   - Traces: Tail-based sampling (100% errors, 1-10% normal)

2. **Manage Cardinality**
   - Limit metric labels (avoid user_id, request_id)
   - Aggregate small entities (tenant=other)

3. **Tiered Retention**
   - Hot (7-30 days): Fast search
   - Warm (30-90 days): Slower search
   - Cold (90+ days): Archive

4. **Filter and Drop**
   - Drop noisy or low-value logs
   - Drop unused metrics
   - Use recording rules for expensive queries

**Target**: Observability cost <5% of infrastructure cost

## Common Mistakes to Avoid

❌ **Logging everything without strategy** → High costs, noise  
✅ **Log with purpose, use levels and sampling**

❌ **High-cardinality metrics** (user_id in labels) → Metric explosion  
✅ **Limit labels, use tags in traces for high-cardinality data**

❌ **Alert on everything** → Alert fatigue  
✅ **Alert on symptoms (user impact), ensure alerts are actionable**

❌ **No correlation between logs/metrics/traces** → Slow debugging  
✅ **Implement correlation IDs, link pillars together**

❌ **Ignoring cost from the start** → Runaway costs  
✅ **Design with cost in mind, implement sampling, monitor and optimize**

## Success Metrics

- **MTTD** (Mean Time to Detect): <5 minutes for critical issues
- **MTTR** (Mean Time to Resolve): <30 minutes for P0 incidents
- **SLO Compliance**: >99.9% (or your target)
- **Alert Quality**: >70% of alerts result in action
- **Observability Cost**: <5% of infrastructure cost

## Related Skills

**Prerequisites**:
- system-architecture-design
- requirements-analysis
- technology-selection

**Commonly Followed By**:
- incident-response
- sre-practices
- performance-optimization
- chaos-engineering

**Works Well With**:
- security-design
- cost-optimization
- capacity-planning

## Resources

### Documentation
- [SKILL.md](./SKILL.md) - Comprehensive skill documentation
- [instructions.md](./instructions.md) - Step-by-step implementation guide
- [examples.md](./examples.md) - Real-world examples and case studies

### Standards
- [W3C Trace Context](https://www.w3.org/TR/trace-context/)
- [OpenTelemetry](https://opentelemetry.io/)
- [Prometheus Naming Conventions](https://prometheus.io/docs/practices/naming/)

### Books
- "Observability Engineering" by Charity Majors, Liz Fong-Jones, George Miranda
- "Site Reliability Engineering" by Google
- "The Art of Monitoring" by James Turnbull

## Getting Help

For questions or issues with this skill:
1. Review the [SKILL.md](./SKILL.md) for comprehensive guidance
2. Check [examples.md](./examples.md) for similar use cases
3. Consult [instructions.md](./instructions.md) for step-by-step procedures

## Version

**Version**: 1.0.0  
**Last Updated**: 2026-09-09  
**Skill Type**: Design  
**Complexity**: Advanced
