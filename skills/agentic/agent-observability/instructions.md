# agent-observability - Step-by-Step Instructions

This guide provides executable instructions for implementing observability infrastructure for AI agent workflows.

---

## Prerequisites

- Completed agent workflow design (from agent-workflow-design skill)
- Completed agent handoff design (from agent-handoff-design skill)
- Access to logging/monitoring infrastructure (CloudWatch, Datadog, Prometheus, etc.)
- Understanding of SLA/SLO requirements
- Understanding of compliance requirements (if applicable)

---

## Step-by-Step Process

### Step 1: Identify Observability Needs (30 minutes)

**Objective:** Understand what needs to be monitored and why.

**Actions:**

1. **Review Agent Workflow:**
   - Identify all agent tasks and handoffs
   - Identify critical paths (tasks that must succeed for workflow success)
   - Identify failure points (tasks most likely to fail)

2. **Define SLAs/SLOs:**
   - Response time SLA (e.g., p95 latency < 2 seconds)
   - Availability SLA (e.g., 99.9% uptime)
   - Error rate SLO (e.g., < 1% error rate)

3. **Identify Compliance Requirements:**
   - Audit trail requirements (who did what when)
   - Data retention requirements (how long to keep logs)
   - Privacy requirements (PII redaction, encryption)

4. **Define Stakeholder Needs:**
   - Operations: Real-time health dashboards, alerts
   - Business: Completion rates, SLA compliance
   - Developers: Debugging tools, performance profiling

**Validation Checklist:**
- [ ] All SLAs/SLOs are documented
- [ ] All critical failure modes are identified
- [ ] Compliance requirements are documented
- [ ] Stakeholder visibility needs are defined

---

### Step 2: Select Observability Tools (30 minutes)

**Objective:** Choose the right tools for logging, metrics, tracing, and alerting.

**Actions:**

1. **Evaluate Logging Platforms:**
   - **Cloud-Native:** CloudWatch Logs (AWS), Cloud Logging (GCP), Azure Monitor
   - **Unified Platforms:** Datadog, New Relic, Splunk
   - **Open-Source:** ELK Stack (Elasticsearch, Logstash, Kibana), Loki

2. **Evaluate Metrics Platforms:**
   - **Cloud-Native:** CloudWatch Metrics, Cloud Monitoring, Azure Monitor
   - **Unified Platforms:** Datadog, New Relic, Prometheus + Grafana

3. **Evaluate Tracing Platforms:**
   - **Cloud-Native:** AWS X-Ray, Cloud Trace, Azure Monitor
   - **Unified Platforms:** Datadog APM, New Relic APM
   - **Open-Source:** Jaeger, Zipkin, OpenTelemetry

4. **Evaluate Alerting Platforms:**
   - **Cloud-Native:** CloudWatch Alarms, Cloud Monitoring Alerts
   - **Incident Management:** PagerDuty, Opsgenie, VictorOps
   - **Collaboration:** Slack, Microsoft Teams

5. **Make Selection:**
   - Consider cost, integration complexity, feature fit
   - Prefer unified platforms for simplicity (if budget allows)
   - Prefer cloud-native tools for cloud deployments
   - Prefer open-source for cost optimization and flexibility

**Example Selection:**
- **Logging:** CloudWatch Logs (AWS deployment)
- **Metrics:** CloudWatch Metrics + Grafana
- **Tracing:** AWS X-Ray
- **Alerting:** CloudWatch Alarms + PagerDuty + Slack

**Validation Checklist:**
- [ ] Selected tools meet all functional requirements
- [ ] Tools integrate with existing infrastructure
- [ ] Cost is within budget constraints
- [ ] Tools support required retention and compliance policies

---

### Step 3: Design Logging Strategy (45 minutes)

**Objective:** Define what to log, how to structure logs, and where to send them.

**Actions:**

1. **Define Log Levels:**
   - **DEBUG:** Detailed diagnostic information (development only)
   - **INFO:** Normal operational events (task start/end, handoffs)
   - **WARN:** Unexpected but recoverable events (retries, degraded performance)
   - **ERROR:** Failures that prevent task completion
   - **CRITICAL:** System-wide failures requiring immediate attention

2. **Design Structured Log Format:**
   ```json
   {
     "timestamp": "2026-09-08T10:45:23.123Z",
     "level": "INFO",
     "correlation_id": "req-12345",
     "agent_id": "code-review-agent-1",
     "task_id": "task-67890",
     "task_type": "code_review",
     "message": "Code review completed",
     "duration_ms": 1234,
     "metadata": {
       "files_reviewed": 5,
       "issues_found": 3
     }
   }
   ```

3. **Identify What to Log:**
   - **Task Start:** `{level: "INFO", message: "Task started", task_id, agent_id, inputs}`
   - **Task End:** `{level: "INFO", message: "Task completed", task_id, agent_id, outputs, duration_ms}`
   - **Handoff:** `{level: "INFO", message: "Handoff initiated", from_agent, to_agent, context_size}`
   - **Error:** `{level: "ERROR", message: "Task failed", task_id, error, stack_trace}`

4. **Implement PII Redaction:**
   - Identify sensitive fields (email, phone, SSN, credit card)
   - Implement automatic redaction (replace with `[REDACTED]`)
   - Test redaction with sample data

5. **Configure Log Destinations:**
   - **Development:** Console output
   - **Staging:** CloudWatch Logs with 7-day retention
   - **Production:** CloudWatch Logs with 30-day retention + S3 archive

**Example Code (Python):**
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
    
    def task_start(self, correlation_id, task_id, inputs):
        self.log("INFO", "Task started", correlation_id, task_id, inputs=inputs)
    
    def task_end(self, correlation_id, task_id, outputs, duration_ms):
        self.log("INFO", "Task completed", correlation_id, task_id, 
                 outputs=outputs, duration_ms=duration_ms)
    
    def error(self, correlation_id, task_id, error):
        self.log("ERROR", "Task failed", correlation_id, task_id, 
                 error=str(error), stack_trace=traceback.format_exc())
```

**Validation Checklist:**
- [ ] Log structure is defined and documented
- [ ] All workflow steps have corresponding log events
- [ ] Correlation IDs are included in all logs
- [ ] PII redaction is implemented and tested
- [ ] Log destinations are configured

---

### Step 4: Design Metrics Collection (45 minutes)

**Objective:** Define metrics to track agent performance and health.

**Actions:**

1. **Define Performance Metrics:**
   - `agent.task.duration` (histogram): Time to complete tasks
   - `agent.task.count` (counter): Number of tasks executed
   - `agent.task.error_rate` (gauge): Percentage of failed tasks
   - `agent.handoff.latency` (histogram): Time for agent-to-agent handoffs

2. **Define Resource Metrics:**
   - `agent.cpu.usage` (gauge): CPU utilization
   - `agent.memory.usage` (gauge): Memory consumption
   - `agent.api.calls` (counter): External API calls made
   - `agent.cost` (counter): Estimated cost per task

3. **Define Business Metrics:**
   - `agent.workflow.completion_rate` (gauge): % of workflows completed successfully
   - `agent.sla.compliance` (gauge): % of tasks meeting SLA

4. **Define Metric Dimensions:**
   - `agent_id`: Specific agent instance
   - `task_type`: Type of task (e.g., "code_review", "testing")
   - `environment`: prod, staging, dev
   - `workflow_id`: Specific workflow instance

5. **Implement Metrics Collection:**

**Example Code (Python with CloudWatch):**
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
            MetricData=[
                {
                    'MetricName': metric_name,
                    'Value': value,
                    'Unit': unit,
                    'Timestamp': datetime.utcnow(),
                    'Dimensions': [
                        {'Name': k, 'Value': v} for k, v in dimensions.items()
                    ]
                }
            ]
        )
    
    def record_task_duration(self, agent_id, task_type, duration_ms):
        self.put_metric(
            'TaskDuration',
            duration_ms,
            'Milliseconds',
            {'AgentId': agent_id, 'TaskType': task_type}
        )
    
    def record_task_completion(self, agent_id, task_type, success):
        self.put_metric(
            'TaskCount',
            1,
            'Count',
            {'AgentId': agent_id, 'TaskType': task_type, 'Success': str(success)}
        )
```

**Validation Checklist:**
- [ ] All SLA metrics are defined
- [ ] Resource metrics are defined
- [ ] Business metrics are defined
- [ ] Metrics have appropriate dimensions
- [ ] Metrics collection code is implemented

---

### Step 5: Implement Distributed Tracing (60 minutes)

**Objective:** Enable end-to-end request tracing across multi-agent workflows.

**Actions:**

1. **Select Tracing Standard:**
   - **OpenTelemetry:** Cross-platform, vendor-neutral
   - **AWS X-Ray:** AWS-native, integrated with AWS services
   - **Datadog APM:** Unified platform with logging and metrics

2. **Instrument Agent Code:**
   - Create span for each agent task
   - Include span attributes (agent_id, task_id, inputs, outputs)
   - Propagate trace context across handoffs

**Example Code (Python with AWS X-Ray):**
```python
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.core import patch_all

patch_all()  # Auto-instrument AWS SDK calls

class TracedAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
    
    @xray_recorder.capture('execute_task')
    def execute_task(self, task_id, inputs, trace_context=None):
        # Propagate trace context from previous agent
        if trace_context:
            xray_recorder.put_annotation('parent_trace_id', trace_context['trace_id'])
        
        # Add task metadata to trace
        xray_recorder.put_annotation('agent_id', self.agent_id)
        xray_recorder.put_annotation('task_id', task_id)
        xray_recorder.put_metadata('inputs', inputs)
        
        # Execute task
        try:
            outputs = self._do_work(inputs)
            xray_recorder.put_metadata('outputs', outputs)
            return outputs
        except Exception as e:
            xray_recorder.put_annotation('error', str(e))
            raise
    
    def handoff_to_next_agent(self, next_agent, outputs):
        # Get current trace context
        trace_context = {
            'trace_id': xray_recorder.current_segment().trace_id,
            'parent_id': xray_recorder.current_segment().id
        }
        
        # Pass trace context to next agent
        return next_agent.execute_task(
            task_id=f"task-{next_agent.agent_id}",
            inputs=outputs,
            trace_context=trace_context
        )
```

3. **Configure Trace Sampling:**
   - **Development:** 100% sampling
   - **Production:** Adaptive sampling (100% errors, 10% successes)

**Validation Checklist:**
- [ ] Tracing instrumentation is implemented
- [ ] Trace context propagates across handoffs
- [ ] Spans include relevant attributes
- [ ] Trace sampling is configured

---

### Step 6: Create Operational Dashboards (45 minutes)

**Objective:** Build dashboards for monitoring agent health and performance.

**Actions:**

1. **Create Health Dashboard:**
   - Error rate (last 1 hour)
   - Task completion rate (last 1 hour)
   - Active agents count
   - Recent alerts

2. **Create Performance Dashboard:**
   - Task duration percentiles (p50, p95, p99)
   - Handoff latency
   - Resource usage (CPU, memory)
   - API call rates

3. **Create Business Dashboard:**
   - Workflows completed today
   - SLA compliance %
   - Cost per workflow
   - Top failure reasons

**Example (CloudWatch Dashboard JSON):**
```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AgentWorkflow", "TaskCount", {"stat": "Sum", "label": "Tasks Completed"}]
        ],
        "period": 300,
        "stat": "Sum",
        "region": "us-east-1",
        "title": "Task Completion Rate"
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AgentWorkflow", "TaskDuration", {"stat": "p95", "label": "p95 Latency"}]
        ],
        "period": 300,
        "stat": "p95",
        "region": "us-east-1",
        "title": "Task Latency (p95)",
        "yAxis": {"left": {"min": 0}},
        "annotations": {
          "horizontal": [{"value": 2000, "label": "SLA Threshold"}]
        }
      }
    }
  ]
}
```

**Validation Checklist:**
- [ ] Health dashboard shows real-time error rates and throughput
- [ ] Performance dashboard shows latency percentiles
- [ ] Business dashboard shows completion rates and SLA compliance
- [ ] Dashboards are accessible to intended audiences

---

### Step 7: Configure Alerts (30 minutes)

**Objective:** Set up proactive alerts for failures and performance degradation.

**Actions:**

1. **Define Alert Rules:**

**Critical Alerts:**
```yaml
alert: HighErrorRate
condition: error_rate > 5% for 5 minutes
notification: PagerDuty

alert: SystemDown
condition: task_count == 0 for 15 minutes
notification: PagerDuty

alert: HighLatency
condition: p95_latency > 10s for 10 minutes
notification: PagerDuty
```

**Warning Alerts:**
```yaml
alert: ElevatedErrorRate
condition: error_rate > 2% for 10 minutes
notification: Slack

alert: ElevatedLatency
condition: p95_latency > 5s for 15 minutes
notification: Slack
```

2. **Configure Notification Channels:**
   - **PagerDuty:** Critical alerts, on-call rotation
   - **Slack:** Warning alerts, #agent-alerts channel
   - **Email:** Info alerts, daily digest

**Example (CloudWatch Alarm):**
```python
cloudwatch = boto3.client('cloudwatch')

cloudwatch.put_metric_alarm(
    AlarmName='HighErrorRate',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=1,
    MetricName='ErrorRate',
    Namespace='AgentWorkflow',
    Period=300,
    Statistic='Average',
    Threshold=5.0,
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123456789012:pagerduty-critical'],
    AlarmDescription='Alert when error rate exceeds 5%'
)
```

**Validation Checklist:**
- [ ] All critical failure modes have alerts
- [ ] Alert thresholds are appropriate
- [ ] Notification channels are configured and tested
- [ ] On-call rotation is defined

---

### Step 8: Test Observability Infrastructure (30 minutes)

**Objective:** Validate that observability infrastructure works as expected.

**Actions:**

1. **Generate Test Traffic:**
   - Execute successful workflows
   - Trigger intentional failures
   - Execute slow workflows

2. **Verify Logs:**
   - Query logs for test workflows
   - Verify correlation IDs are present
   - Verify structured format

3. **Verify Metrics:**
   - Check that metrics are collected
   - Verify metric values are accurate
   - Check metric dimensions

4. **Verify Traces:**
   - View traces in tracing platform
   - Verify end-to-end workflow is captured
   - Verify trace context propagation

5. **Verify Dashboards:**
   - Check that dashboards display data
   - Verify charts are accurate

6. **Verify Alerts:**
   - Trigger test alert
   - Verify notification is delivered

**Validation Checklist:**
- [ ] Logs are queryable and structured correctly
- [ ] Metrics are collected and accurate
- [ ] Traces capture complete workflow execution
- [ ] Dashboards display correct data
- [ ] Alerts fire and deliver notifications

---

### Step 9: Document and Train (30 minutes)

**Objective:** Ensure team can effectively use observability infrastructure.

**Actions:**

1. **Document Observability Architecture:**
   - Logging setup and configuration
   - Metrics definitions and collection
   - Tracing setup and propagation
   - Dashboard descriptions
   - Alert definitions and response procedures

2. **Create Operational Runbooks:**
   - How to query logs for debugging
   - How to interpret metrics and dashboards
   - How to use distributed tracing
   - How to respond to alerts
   - Common troubleshooting procedures

3. **Train Team:**
   - Walkthrough of observability infrastructure
   - Hands-on practice with debugging tools
   - Alert response drills

**Validation Checklist:**
- [ ] Documentation is complete and accessible
- [ ] Runbooks are tested and accurate
- [ ] Team is trained on observability tools

---

## Success Criteria

Observability implementation is successful when:

- [ ] All agent tasks emit structured logs with correlation IDs
- [ ] All SLA metrics are collected and visualized
- [ ] Distributed tracing captures end-to-end workflow execution
- [ ] Dashboards provide actionable insights into agent health and performance
- [ ] Alerts fire proactively for critical failures and performance issues
- [ ] Team can diagnose issues using logs, metrics, and traces
- [ ] Mean Time to Detect (MTTD) < 5 minutes
- [ ] Mean Time to Resolve (MTTR) < 30 minutes
- [ ] Alert accuracy > 95% (low false positive rate)

---

## Troubleshooting

### Issue: Logs are not appearing in logging platform

**Possible Causes:**
- Log destination not configured correctly
- Network connectivity issues
- Insufficient permissions

**Solutions:**
- Verify log destination configuration
- Test network connectivity to logging platform
- Verify IAM permissions for logging

---

### Issue: Metrics are not being collected

**Possible Causes:**
- Metrics instrumentation not implemented
- Metrics client not configured
- High-cardinality dimensions causing issues

**Solutions:**
- Verify metrics instrumentation code is executed
- Verify metrics client configuration
- Reduce metric dimensions to avoid high cardinality

---

### Issue: Traces are fragmented (not end-to-end)

**Possible Causes:**
- Trace context not propagated across handoffs
- Tracing instrumentation missing for some agents

**Solutions:**
- Verify trace context is included in handoff payloads
- Verify all agents have tracing instrumentation
- Test trace context propagation with sample workflows

---

### Issue: Alerts are firing too frequently (false positives)

**Possible Causes:**
- Alert thresholds too sensitive
- Metrics have high variance

**Solutions:**
- Tune alert thresholds based on historical data
- Use dynamic thresholds or anomaly detection
- Implement alert deduplication and suppression

---

## Next Steps

After implementing observability:

1. **Use agent-evaluation skill** to evaluate agent performance using observability data
2. **Use agentic-workflow-review skill** to optimize workflows based on observability insights
3. **Continuously improve observability** by adding new metrics, dashboards, and alerts as needed
