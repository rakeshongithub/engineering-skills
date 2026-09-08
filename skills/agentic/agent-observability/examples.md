# agent-observability - Examples

This document provides detailed, realistic examples of implementing observability for AI agent workflows.

---

## Example 1: Logging Implementation with Structured Logging and Log Aggregation

### Scenario

You're building a multi-agent code review workflow with 3 agents:
1. **Code Analysis Agent**: Analyzes code for issues
2. **Security Scan Agent**: Scans for security vulnerabilities
3. **Review Aggregation Agent**: Combines results and generates final report

You need comprehensive logging to debug issues and track workflow execution.

### Implementation

#### Step 1: Design Log Structure

```json
{
  "timestamp": "2026-09-08T10:45:23.123Z",
  "level": "INFO",
  "correlation_id": "review-abc123",
  "agent_id": "code-analysis-agent-1",
  "task_id": "task-456",
  "task_type": "code_analysis",
  "message": "Code analysis completed",
  "duration_ms": 1234,
  "metadata": {
    "files_analyzed": 5,
    "issues_found": 3,
    "severity_breakdown": {"high": 1, "medium": 2, "low": 0}
  }
}
```

#### Step 2: Implement Structured Logger

```python
import logging
import json
from datetime import datetime
import traceback

class StructuredLogger:
    def __init__(self, agent_id):
        self.agent_id = agent_id
        self.logger = logging.getLogger(agent_id)
        
        # Configure JSON formatter
        handler = logging.StreamHandler()
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.INFO)
    
    def _log(self, level, message, correlation_id, task_id=None, task_type=None, **metadata):
        log_entry = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": level,
            "correlation_id": correlation_id,
            "agent_id": self.agent_id,
            "task_id": task_id,
            "task_type": task_type,
            "message": message,
            "metadata": metadata
        }
        # Remove None values
        log_entry = {k: v for k, v in log_entry.items() if v is not None}
        
        self.logger.log(getattr(logging, level), json.dumps(log_entry))
    
    def task_start(self, correlation_id, task_id, task_type, inputs):
        self._log("INFO", "Task started", correlation_id, task_id, task_type, inputs=inputs)
    
    def task_end(self, correlation_id, task_id, task_type, outputs, duration_ms):
        self._log("INFO", "Task completed", correlation_id, task_id, task_type, 
                  outputs=outputs, duration_ms=duration_ms)
    
    def handoff(self, correlation_id, from_agent, to_agent, context_size):
        self._log("INFO", "Handoff initiated", correlation_id, 
                  from_agent=from_agent, to_agent=to_agent, context_size_bytes=context_size)
    
    def error(self, correlation_id, task_id, task_type, error):
        self._log("ERROR", "Task failed", correlation_id, task_id, task_type,
                  error=str(error), stack_trace=traceback.format_exc())
```

#### Step 3: Instrument Agent Code

```python
import time

class CodeAnalysisAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
        self.logger = StructuredLogger(agent_id)
    
    def execute(self, correlation_id, task_id, code_files):
        start_time = time.time()
        
        # Log task start
        self.logger.task_start(
            correlation_id=correlation_id,
            task_id=task_id,
            task_type="code_analysis",
            inputs={"file_count": len(code_files)}
        )
        
        try:
            # Perform code analysis
            issues = self._analyze_code(code_files)
            
            # Log task completion
            duration_ms = int((time.time() - start_time) * 1000)
            self.logger.task_end(
                correlation_id=correlation_id,
                task_id=task_id,
                task_type="code_analysis",
                outputs={
                    "files_analyzed": len(code_files),
                    "issues_found": len(issues),
                    "severity_breakdown": self._count_by_severity(issues)
                },
                duration_ms=duration_ms
            )
            
            return issues
        
        except Exception as e:
            # Log error
            self.logger.error(
                correlation_id=correlation_id,
                task_id=task_id,
                task_type="code_analysis",
                error=e
            )
            raise
    
    def handoff_to_security_agent(self, correlation_id, security_agent, analysis_results):
        # Log handoff
        context = {"issues": analysis_results}
        context_json = json.dumps(context)
        
        self.logger.handoff(
            correlation_id=correlation_id,
            from_agent=self.agent_id,
            to_agent=security_agent.agent_id,
            context_size=len(context_json)
        )
        
        # Execute handoff
        return security_agent.execute(correlation_id, f"task-{security_agent.agent_id}", context)
```

#### Step 4: Configure Log Aggregation (CloudWatch Logs)

```python
import boto3
import watchtower

# Configure CloudWatch Logs handler
cloudwatch_handler = watchtower.CloudWatchLogHandler(
    log_group='/agent-workflow/code-review',
    stream_name=f'{agent_id}-{datetime.utcnow().strftime("%Y-%m-%d")}',
    use_queues=False
)

logger.addHandler(cloudwatch_handler)
```

#### Step 5: Query Logs for Debugging

**Find all errors for a specific review:**
```
fields @timestamp, level, message, metadata.error, metadata.stack_trace
| filter correlation_id = "review-abc123" and level = "ERROR"
| sort @timestamp desc
```

**Find slow tasks:**
```
fields @timestamp, agent_id, task_id, metadata.duration_ms
| filter metadata.duration_ms > 5000
| sort metadata.duration_ms desc
```

**Trace complete workflow execution:**
```
fields @timestamp, agent_id, task_type, message, metadata
| filter correlation_id = "review-abc123"
| sort @timestamp asc
```

### Results

**Before Logging:**
- Debugging required manual code inspection
- No visibility into workflow execution
- Failures were difficult to diagnose

**After Logging:**
- All workflow steps are logged with correlation IDs
- Can trace complete workflow execution in logs
- Errors include full context and stack traces
- Debugging time reduced from hours to minutes

---

## Example 2: Metrics Collection with Latency, Throughput, and Error Rates

### Scenario

You need to monitor the performance of your code review workflow to ensure it meets SLAs:
- **Latency SLA:** p95 latency < 2 seconds per task
- **Throughput SLA:** Process at least 100 reviews per hour
- **Error Rate SLA:** < 1% error rate

### Implementation

#### Step 1: Define Metrics

**Performance Metrics:**
- `agent.task.duration` (histogram): Time to complete tasks
- `agent.task.count` (counter): Number of tasks executed
- `agent.task.error_rate` (gauge): Percentage of failed tasks

**Resource Metrics:**
- `agent.api.calls` (counter): External API calls made
- `agent.cost` (counter): Estimated cost per task

**Business Metrics:**
- `agent.review.completion_rate` (gauge): % of reviews completed successfully
- `agent.sla.compliance` (gauge): % of tasks meeting SLA

#### Step 2: Implement Metrics Collector

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
    
    def record_error_rate(self, agent_id, task_type, error_rate):
        self.put_metric(
            'ErrorRate',
            error_rate,
            'Percent',
            {'AgentId': agent_id, 'TaskType': task_type}
        )
    
    def record_api_call(self, agent_id, api_name):
        self.put_metric(
            'ApiCalls',
            1,
            'Count',
            {'AgentId': agent_id, 'ApiName': api_name}
        )
```

#### Step 3: Instrument Agent Code

```python
import time

class CodeAnalysisAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
        self.metrics = MetricsCollector()
    
    def execute(self, correlation_id, task_id, code_files):
        start_time = time.time()
        success = False
        
        try:
            # Perform code analysis
            issues = self._analyze_code(code_files)
            success = True
            return issues
        
        except Exception as e:
            raise
        
        finally:
            # Record metrics
            duration_ms = int((time.time() - start_time) * 1000)
            
            self.metrics.record_task_duration(
                agent_id=self.agent_id,
                task_type="code_analysis",
                duration_ms=duration_ms
            )
            
            self.metrics.record_task_completion(
                agent_id=self.agent_id,
                task_type="code_analysis",
                success=success
            )
    
    def _analyze_code(self, code_files):
        # Record API call to external code analysis service
        self.metrics.record_api_call(
            agent_id=self.agent_id,
            api_name="CodeAnalysisAPI"
        )
        
        # Call external API
        response = requests.post('https://api.codeanalysis.com/analyze', json=code_files)
        return response.json()['issues']
```

#### Step 4: Create Metrics Dashboard

**CloudWatch Dashboard JSON:**
```json
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AgentWorkflow", "TaskDuration", {"stat": "p50", "label": "p50 Latency"}],
          ["...", {"stat": "p95", "label": "p95 Latency"}],
          ["...", {"stat": "p99", "label": "p99 Latency"}]
        ],
        "period": 300,
        "region": "us-east-1",
        "title": "Task Latency Percentiles",
        "yAxis": {"left": {"min": 0, "max": 5000}},
        "annotations": {
          "horizontal": [{"value": 2000, "label": "SLA Threshold (2s)", "color": "#d62728"}]
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AgentWorkflow", "TaskCount", {"stat": "Sum", "label": "Tasks Completed"}]
        ],
        "period": 3600,
        "stat": "Sum",
        "region": "us-east-1",
        "title": "Throughput (Tasks per Hour)",
        "annotations": {
          "horizontal": [{"value": 100, "label": "SLA Threshold (100/hr)", "color": "#d62728"}]
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AgentWorkflow", "ErrorRate", {"stat": "Average", "label": "Error Rate"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1",
        "title": "Error Rate (%)",
        "yAxis": {"left": {"min": 0, "max": 10}},
        "annotations": {
          "horizontal": [{"value": 1.0, "label": "SLA Threshold (1%)", "color": "#d62728"}]
        }
      }
    }
  ]
}
```

#### Step 5: Configure Alerts

```python
cloudwatch = boto3.client('cloudwatch')

# Alert: High Latency
cloudwatch.put_metric_alarm(
    AlarmName='CodeReview-HighLatency',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=2,
    MetricName='TaskDuration',
    Namespace='AgentWorkflow',
    Period=300,
    Statistic='p95',
    Threshold=2000.0,  # 2 seconds
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123456789012:agent-alerts'],
    AlarmDescription='Alert when p95 latency exceeds 2 seconds'
)

# Alert: High Error Rate
cloudwatch.put_metric_alarm(
    AlarmName='CodeReview-HighErrorRate',
    ComparisonOperator='GreaterThanThreshold',
    EvaluationPeriods=1,
    MetricName='ErrorRate',
    Namespace='AgentWorkflow',
    Period=300,
    Statistic='Average',
    Threshold=1.0,  # 1%
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123456789012:pagerduty-critical'],
    AlarmDescription='Alert when error rate exceeds 1%'
)

# Alert: Low Throughput
cloudwatch.put_metric_alarm(
    AlarmName='CodeReview-LowThroughput',
    ComparisonOperator='LessThanThreshold',
    EvaluationPeriods=1,
    MetricName='TaskCount',
    Namespace='AgentWorkflow',
    Period=3600,
    Statistic='Sum',
    Threshold=100.0,  # 100 tasks per hour
    ActionsEnabled=True,
    AlarmActions=['arn:aws:sns:us-east-1:123456789012:agent-alerts'],
    AlarmDescription='Alert when throughput falls below 100 tasks/hour'
)
```

### Results

**Metrics Collected:**
- p95 latency: 1.2 seconds (within SLA)
- Throughput: 150 tasks/hour (exceeds SLA)
- Error rate: 0.5% (within SLA)

**Alerts Configured:**
- High latency alert (> 2s)
- High error rate alert (> 1%)
- Low throughput alert (< 100/hr)

**Benefits:**
- Real-time visibility into performance
- Proactive alerting before SLA violations
- Data-driven optimization decisions

---

## Example 3: Distributed Tracing Across Multi-Agent Workflow

### Scenario

You have a complex code review workflow with 5 agents:
1. **Task Decomposition Agent**: Breaks review into tasks
2. **Code Analysis Agent**: Analyzes code quality
3. **Security Scan Agent**: Scans for vulnerabilities
4. **Test Coverage Agent**: Checks test coverage
5. **Review Aggregation Agent**: Combines results

You need distributed tracing to understand end-to-end execution and identify bottlenecks.

### Implementation

#### Step 1: Set Up AWS X-Ray

```python
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.core import patch_all

# Auto-instrument AWS SDK calls
patch_all()

# Configure X-Ray recorder
xray_recorder.configure(
    service='CodeReviewWorkflow',
    sampling=True,
    context_missing='LOG_ERROR'
)
```

#### Step 2: Instrument Agent Code with Tracing

```python
from aws_xray_sdk.core import xray_recorder

class TaskDecompositionAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
    
    @xray_recorder.capture('task_decomposition')
    def execute(self, correlation_id, review_request):
        # Add metadata to trace
        xray_recorder.put_annotation('correlation_id', correlation_id)
        xray_recorder.put_annotation('agent_id', self.agent_id)
        xray_recorder.put_metadata('review_request', review_request)
        
        # Decompose review into tasks
        tasks = self._decompose(review_request)
        
        xray_recorder.put_metadata('tasks', tasks)
        return tasks
    
    @xray_recorder.capture('decompose')
    def _decompose(self, review_request):
        # Detailed decomposition logic
        files = review_request['files']
        tasks = [
            {'type': 'code_analysis', 'files': files},
            {'type': 'security_scan', 'files': files},
            {'type': 'test_coverage', 'files': files}
        ]
        return tasks

class CodeAnalysisAgent:
    def __init__(self, agent_id):
        self.agent_id = agent_id
    
    @xray_recorder.capture('code_analysis')
    def execute(self, correlation_id, task, trace_context=None):
        # Propagate trace context from previous agent
        if trace_context:
            xray_recorder.put_annotation('parent_trace_id', trace_context['trace_id'])
        
        # Add metadata
        xray_recorder.put_annotation('correlation_id', correlation_id)
        xray_recorder.put_annotation('agent_id', self.agent_id)
        xray_recorder.put_metadata('task', task)
        
        # Analyze code
        issues = self._analyze(task['files'])
        
        xray_recorder.put_metadata('issues', issues)
        return issues
    
    @xray_recorder.capture('analyze_files')
    def _analyze(self, files):
        issues = []
        for file in files:
            file_issues = self._analyze_file(file)
            issues.extend(file_issues)
        return issues
    
    @xray_recorder.capture('analyze_file')
    def _analyze_file(self, file):
        # Call external API
        import requests
        response = requests.post('https://api.codeanalysis.com/analyze', json={'file': file})
        return response.json()['issues']
```

#### Step 3: Propagate Trace Context Across Handoffs

```python
class WorkflowOrchestrator:
    def __init__(self):
        self.task_decomp_agent = TaskDecompositionAgent('task-decomp-1')
        self.code_analysis_agent = CodeAnalysisAgent('code-analysis-1')
        self.security_scan_agent = SecurityScanAgent('security-scan-1')
        self.test_coverage_agent = TestCoverageAgent('test-coverage-1')
        self.review_agg_agent = ReviewAggregationAgent('review-agg-1')
    
    @xray_recorder.capture('code_review_workflow')
    def execute_review(self, correlation_id, review_request):
        # Step 1: Task Decomposition
        tasks = self.task_decomp_agent.execute(correlation_id, review_request)
        
        # Get current trace context
        trace_context = self._get_trace_context()
        
        # Step 2: Execute tasks in parallel
        results = []
        for task in tasks:
            if task['type'] == 'code_analysis':
                result = self.code_analysis_agent.execute(correlation_id, task, trace_context)
            elif task['type'] == 'security_scan':
                result = self.security_scan_agent.execute(correlation_id, task, trace_context)
            elif task['type'] == 'test_coverage':
                result = self.test_coverage_agent.execute(correlation_id, task, trace_context)
            results.append(result)
        
        # Step 3: Aggregate results
        trace_context = self._get_trace_context()
        final_report = self.review_agg_agent.execute(correlation_id, results, trace_context)
        
        return final_report
    
    def _get_trace_context(self):
        segment = xray_recorder.current_segment()
        return {
            'trace_id': segment.trace_id,
            'parent_id': segment.id
        }
```

#### Step 4: View Traces in AWS X-Ray Console

**Trace Map:**
```
CodeReviewWorkflow
├─ task_decomposition (500ms)
│  └─ decompose (400ms)
├─ code_analysis (2000ms)
│  ├─ analyze_files (1800ms)
│  │  ├─ analyze_file (file1.js) (600ms)
│  │  ├─ analyze_file (file2.js) (700ms)
│  │  └─ analyze_file (file3.js) (500ms)
│  └─ External API Call (1500ms)
├─ security_scan (1500ms)
│  └─ External API Call (1200ms)
├─ test_coverage (1000ms)
│  └─ External API Call (800ms)
└─ review_aggregation (300ms)
   └─ aggregate_results (200ms)
```

**Total Duration:** 5.3 seconds  
**Bottleneck:** Code analysis (2 seconds) due to external API latency

### Results

**Before Tracing:**
- No visibility into which agent was slow
- Difficult to identify bottlenecks
- Debugging required manual instrumentation

**After Tracing:**
- Complete visibility into end-to-end execution
- Identified bottleneck: External API call in code analysis (1.5s)
- Optimization: Implemented caching for API calls
- Reduced total duration from 5.3s to 3.2s (40% improvement)

---

## Example 4: Debugging Production Issue Using Logs, Metrics, and Traces

### Scenario

**Incident:** Code review workflow is failing with 15% error rate (normal: 0.5%).

**Alert:** PagerDuty alert fired at 10:45 AM: "HighErrorRate: Error rate 15% exceeds threshold 5%"

**Objective:** Diagnose and fix the issue using observability tools.

### Debugging Process

#### Step 1: Check Dashboards

**Health Dashboard:**
- Error rate: 15% (RED - exceeds threshold)
- Task completion rate: 85% (YELLOW - below normal 99%)
- Active agents: 3 (GREEN - normal)

**Performance Dashboard:**
- p95 latency: 8 seconds (RED - exceeds SLA of 2s)
- p50 latency: 1.5 seconds (GREEN - within SLA)
- Handoff latency: 200ms (GREEN - normal)

**Observation:** High error rate and high p95 latency suggest a performance issue affecting some requests.

---

#### Step 2: Query Logs for Errors

**CloudWatch Logs Insights Query:**
```
fields @timestamp, correlation_id, agent_id, task_type, message, metadata.error
| filter level = "ERROR" and @timestamp > ago(1h)
| sort @timestamp desc
| limit 20
```

**Results:**
```json
[
  {
    "timestamp": "2026-09-08T10:44:32Z",
    "correlation_id": "review-xyz789",
    "agent_id": "code-analysis-agent-1",
    "task_type": "code_analysis",
    "message": "Task failed",
    "error": "HTTPError: 503 Service Unavailable",
    "stack_trace": "..."
  },
  {
    "timestamp": "2026-09-08T10:43:15Z",
    "correlation_id": "review-abc456",
    "agent_id": "code-analysis-agent-1",
    "task_type": "code_analysis",
    "message": "Task failed",
    "error": "HTTPError: 503 Service Unavailable",
    "stack_trace": "..."
  }
]
```

**Observation:** All errors are from `code-analysis-agent-1` with `503 Service Unavailable` from external API.

---

#### Step 3: Analyze Metrics

**Query API Call Metrics:**
```
SELECT COUNT(*) FROM ApiCalls 
WHERE ApiName = 'CodeAnalysisAPI' 
AND timestamp > now() - 1h
GROUP BY http_status_code
```

**Results:**
- `200 OK`: 850 requests (85%)
- `503 Service Unavailable`: 150 requests (15%)

**Observation:** External API is returning 503 errors for 15% of requests.

---

#### Step 4: View Distributed Traces

**X-Ray Trace for Failed Request (review-xyz789):**
```
CodeReviewWorkflow (FAILED)
├─ task_decomposition (500ms) ✓
├─ code_analysis (30000ms) ✗ FAILED
│  ├─ analyze_files (29500ms)
│  │  └─ External API Call (29000ms) ✗ 503 Service Unavailable
│  └─ Retry Logic (500ms) ✗ FAILED
└─ Workflow Aborted
```

**Observation:** External API call is timing out after 29 seconds (retry logic exhausted).

---

#### Step 5: Root Cause Analysis

**Root Cause:** External Code Analysis API is experiencing service degradation:
- 15% of requests return `503 Service Unavailable`
- Requests that succeed take normal time (~600ms)
- Requests that fail take 29 seconds (timeout)

**Impact:**
- 15% of code reviews are failing
- p95 latency increased from 2s to 8s
- User experience degraded

---

#### Step 6: Implement Fix

**Immediate Mitigation (Circuit Breaker):**
```python
from circuitbreaker import circuit

class CodeAnalysisAgent:
    @circuit(failure_threshold=5, recovery_timeout=60)
    def _call_external_api(self, file):
        response = requests.post(
            'https://api.codeanalysis.com/analyze',
            json={'file': file},
            timeout=5  # Reduce timeout from 29s to 5s
        )
        if response.status_code == 503:
            raise ServiceUnavailableError("API is unavailable")
        return response.json()
```

**Long-Term Fix (Fallback to Cached Results):**
```python
class CodeAnalysisAgent:
    def _analyze_file(self, file):
        try:
            # Try external API
            return self._call_external_api(file)
        except ServiceUnavailableError:
            # Fallback to cached results
            cached_result = self.cache.get(file)
            if cached_result:
                return cached_result
            else:
                # Fallback to basic static analysis
                return self._basic_static_analysis(file)
```

---

#### Step 7: Verify Fix

**After Deployment:**
- Error rate: 0.5% (GREEN - back to normal)
- p95 latency: 1.8 seconds (GREEN - within SLA)
- Circuit breaker trips when API is unavailable, preventing long timeouts
- Fallback to cached results provides degraded but functional service

**Incident Resolved:** 10:58 AM (13 minutes from alert to resolution)

### Results

**Debugging Efficiency:**
- **MTTD (Mean Time to Detect):** 2 minutes (alert fired 2 minutes after issue started)
- **MTTD (Mean Time to Diagnose):** 8 minutes (used logs, metrics, traces to identify root cause)
- **MTTR (Mean Time to Resolve):** 13 minutes (deployed circuit breaker and fallback logic)

**Key Learnings:**
- Observability enabled rapid diagnosis (logs showed errors, metrics showed API failures, traces showed timeout)
- Circuit breaker pattern prevents cascading failures
- Fallback logic provides degraded but functional service during external API outages

**Improvements Made:**
- Added circuit breaker to prevent long timeouts
- Implemented caching and fallback logic
- Added monitoring for external API health
- Created runbook for this failure scenario

---

## Summary

These examples demonstrate:

1. **Logging Implementation:** Structured logging with correlation IDs enables request tracing and debugging
2. **Metrics Collection:** Performance metrics (latency, throughput, error rate) enable SLA monitoring and alerting
3. **Distributed Tracing:** End-to-end tracing identifies bottlenecks and optimizes workflow performance
4. **Debugging Workflow:** Combining logs, metrics, and traces enables rapid diagnosis and resolution of production issues

**Key Takeaways:**
- Observability is essential for production agent systems
- Structured logging, metrics, and tracing provide complementary views
- Correlation IDs enable end-to-end request tracing
- Dashboards and alerts enable proactive monitoring
- Debugging tools reduce MTTR from hours to minutes
