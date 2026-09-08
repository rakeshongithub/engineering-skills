# agent-observability - Quick Reference

Implement comprehensive monitoring, logging, and debugging infrastructure for AI agent workflows.

---

## Quick Start

### 1. Design Observability Strategy

**Key Questions:**
- What are your SLAs/SLOs? (latency, error rate, throughput)
- What compliance requirements must you meet? (GDPR, SOC 2, HIPAA)
- What failure modes are most critical to detect?
- Who needs visibility into agent operations?

---

### 2. Select Observability Tools

**Unified Platforms:**
- Datadog, New Relic, Splunk (all-in-one logging, metrics, tracing)

**Cloud-Native:**
- AWS: CloudWatch Logs + Metrics + X-Ray
- GCP: Cloud Logging + Monitoring + Trace
- Azure: Azure Monitor + Application Insights

**Open-Source:**
- Logging: ELK Stack, Loki
- Metrics: Prometheus + Grafana
- Tracing: Jaeger, Zipkin, OpenTelemetry

---

### 3. Implement Logging

**Structured Log Format:**
```json
{
  "timestamp": "2026-09-08T10:45:23.123Z",
  "level": "INFO",
  "correlation_id": "req-12345",
  "agent_id": "agent-1",
  "task_id": "task-456",
  "message": "Task completed",
  "duration_ms": 1234,
  "metadata": {...}
}
```

**What to Log:**
- Task start/end with correlation IDs
- Agent handoffs with context transfer details
- Errors with stack traces
- Performance metrics (duration)
- Business events (key milestones)

---

### 4. Collect Metrics

**Key Metrics:**

**Performance:**
- `agent.task.duration` (histogram): Task latency
- `agent.task.count` (counter): Task count
- `agent.task.error_rate` (gauge): Error rate

**Resource:**
- `agent.cpu.usage` (gauge): CPU utilization
- `agent.memory.usage` (gauge): Memory usage
- `agent.api.calls` (counter): API calls

**Business:**
- `agent.workflow.completion_rate` (gauge): Success rate
- `agent.sla.compliance` (gauge): SLA compliance

---

### 5. Enable Distributed Tracing

**Trace Structure:**
```
Workflow Trace (trace_id: abc123)
├─ Agent 1: Task (span_id: span1, 500ms)
├─ Handoff: Agent 1 → Agent 2 (span_id: span2, 50ms)
├─ Agent 2: Task (span_id: span3, 2000ms)
└─ Handoff: Agent 2 → Agent 3 (span_id: span4, 50ms)
```

**Critical:** Propagate trace context across all agent handoffs.

---

### 6. Create Dashboards

**Health Dashboard:**
- Error rate (last 1 hour)
- Task completion rate
- Active agents count
- Recent alerts

**Performance Dashboard:**
- Task duration percentiles (p50, p95, p99)
- Handoff latency
- Resource usage (CPU, memory)

**Business Dashboard:**
- Workflows completed today
- SLA compliance %
- Cost per workflow

---

### 7. Configure Alerts

**Critical Alerts (Page Immediately):**
- Error rate > 5% for 5 minutes
- No tasks completed in last 15 minutes
- p95 latency > 10s for 10 minutes

**Warning Alerts (Notify, No Page):**
- Error rate > 2% for 10 minutes
- p95 latency > 5s for 15 minutes
- Memory usage > 80% for 20 minutes

---

## Observability Templates

### Structured Logger (Python)

```python
import logging
import json
from datetime import datetime

class StructuredLogger:
    def __init__(self, agent_id):
        self.agent_id = agent_id
        self.logger = logging.getLogger(agent_id)
    
    def log(self, level, message, correlation_id, task_id=None, **metadata):
        log_entry = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": level,
            "correlation_id": correlation_id,
            "agent_id": self.agent_id,
            "task_id": task_id,
            "message": message,
            "metadata": metadata
        }
        self.logger.log(getattr(logging, level), json.dumps(log_entry))
```

---

### Metrics Collector (Python + CloudWatch)

```python
import boto3
from datetime import datetime

class MetricsCollector:
    def __init__(self, namespace="AgentWorkflow"):
        self.cloudwatch = boto3.client('cloudwatch')
        self.namespace = namespace
    
    def put_metric(self, metric_name, value, unit, dimensions):
        self.cloudwatch.put_metric_data(
            Namespace=self.namespace,
            MetricData=[{
                'MetricName': metric_name,
                'Value': value,
                'Unit': unit,
                'Timestamp': datetime.utcnow(),
                'Dimensions': [{'Name': k, 'Value': v} for k, v in dimensions.items()]
            }]
        )
```

---

### Distributed Tracing (Python + AWS X-Ray)

```python
from aws_xray_sdk.core import xray_recorder

class TracedAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
    
    @xray_recorder.capture('execute_task')
    def execute_task(self, task_id, inputs, trace_context=None):
        if trace_context:
            xray_recorder.put_annotation('parent_trace_id', trace_context['trace_id'])
        
        xray_recorder.put_annotation('agent_id', self.agent_id)
        xray_recorder.put_annotation('task_id', task_id)
        xray_recorder.put_metadata('inputs', inputs)
        
        outputs = self._do_work(inputs)
        xray_recorder.put_metadata('outputs', outputs)
        return outputs
```

---

## Common Patterns

### Pattern 1: Correlation ID Propagation

**Problem:** Cannot trace requests across agents.

**Solution:**
1. Generate correlation ID at workflow start
2. Include correlation ID in all logs
3. Pass correlation ID through all handoffs
4. Use correlation ID to query logs for complete workflow

---

### Pattern 2: Adaptive Trace Sampling

**Problem:** High-volume production traffic makes 100% tracing expensive.

**Solution:**
- Sample 100% of errors
- Sample 100% of slow requests (> p95 latency)
- Sample 1-10% of normal requests

---

### Pattern 3: Dynamic Alert Thresholds

**Problem:** Static thresholds cause false positives due to traffic patterns.

**Solution:**
- Use historical baselines (e.g., alert if error rate > 2x normal)
- Use time-of-day/day-of-week patterns
- Use anomaly detection for complex patterns

---

## Key Metrics

### Observability Health

| Metric | Target | Measurement |
|--------|--------|-------------|
| Mean Time to Detect (MTTD) | < 5 minutes | Time from issue to alert |
| Mean Time to Resolve (MTTR) | < 30 minutes | Time from alert to resolution |
| Alert Accuracy | > 95% | % of actionable alerts |
| Observability Coverage | 100% | % of workflow steps instrumented |

---

## Decision Trees

### Logging Strategy

```
Is this production?
├─ Yes: Use structured logging with sampling
│  ├─ High volume? → Sample 10-50% of logs
│  └─ Normal volume? → Log 100%
└─ No (dev/staging): Use verbose logging (100%)
```

### Metrics Granularity

```
What is the workflow duration?
├─ < 1 minute: Use 1-second granularity
├─ 1-10 minutes: Use 1-minute granularity
└─ > 10 minutes: Use 5-minute granularity
```

### Tracing Sampling

```
What is the traffic volume?
├─ < 100 req/min: Sample 100%
├─ 100-1000 req/min: Adaptive sampling (100% errors, 10% success)
└─ > 1000 req/min: Adaptive sampling (100% errors, 1% success)
```

---

## Troubleshooting

### Logs Not Appearing

**Check:**
- Log destination configuration
- Network connectivity to logging platform
- IAM permissions for logging

---

### Metrics Not Collected

**Check:**
- Metrics instrumentation code is executed
- Metrics client configuration
- High-cardinality dimensions (reduce if needed)

---

### Traces Fragmented

**Check:**
- Trace context propagation across handoffs
- Tracing instrumentation in all agents

---

### Alert Fatigue

**Solutions:**
- Tune alert thresholds based on historical data
- Use dynamic thresholds or anomaly detection
- Implement alert deduplication

---

## Related Skills

- **agent-workflow-design**: Design workflow before implementing observability
- **agent-handoff-design**: Design handoffs to enable trace propagation
- **agent-evaluation**: Use observability data to evaluate performance
- **agentic-workflow-review**: Use observability insights to optimize workflows
- **agent-guardrails**: Observability detects guardrail violations

---

## Additional Resources

- [SKILL.md](./SKILL.md) - Comprehensive documentation
- [instructions.md](./instructions.md) - Step-by-step implementation guide
- [examples.md](./examples.md) - Detailed examples

---

**Complexity:** Advanced  
**Estimated Time:** 2-4 hours  
**Version:** 1.0.0
