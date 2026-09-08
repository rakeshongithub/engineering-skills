# agentic-workflow-review - Examples

This document provides detailed, realistic examples of reviewing and improving agentic workflows.

---

## Example 1: Performance Optimization - Reducing Latency and Increasing Throughput

### Scenario

You're reviewing a code review workflow with 3 agents:
1. **Code Analysis Agent**: Analyzes code for issues (2000ms)
2. **Security Scan Agent**: Scans for vulnerabilities (1500ms)
3. **Review Aggregation Agent**: Combines results (300ms)

**Current Performance:**
- p95 latency: 5 seconds (SLA: 2 seconds) ❌
- Throughput: 17 tasks/minute (SLA: 30 tasks/minute) ❌
- Error rate: 0.5% (SLA: 1%) ✓

**Business Impact:**
- Developers wait 5 seconds for code review feedback
- Can only process 17 reviews/minute, limiting team productivity

### Review Process

#### Step 1: Analyze Performance Data

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load performance data
df = pd.read_csv('code_review_performance.csv')

# Calculate latency by agent
agent_latency = df.groupby('agent_id')['duration_ms'].quantile(0.95)
print("Agent Latency (p95):")
print(agent_latency)

# Output:
# code-analysis-agent: 2000ms
# security-scan-agent: 1500ms
# review-aggregation-agent: 300ms
# Total (sequential): 3800ms
```

#### Step 2: Identify Bottleneck

**Finding:** Code analysis and security scan run sequentially, but they're independent (no shared state).

**Opportunity:** Parallelize these tasks to reduce latency.

#### Step 3: Estimate Impact

**Current (Sequential):**
```
Code Analysis (2000ms)
    ↓
Security Scan (1500ms)
    ↓
Review Aggregation (300ms)

Total: 2000 + 1500 + 300 = 3800ms
```

**Proposed (Parallel):**
```
Code Analysis (2000ms) ┐
                       ├→ Review Aggregation (300ms)
Security Scan (1500ms) ┘

Total: max(2000, 1500) + 300 = 2300ms
```

**Expected Impact:**
- Latency: 3800ms → 2300ms (39% reduction)
- Throughput: 15.8 tasks/min → 26.1 tasks/min (65% increase)

#### Step 4: Implement Optimization

**Before (Sequential):**
```python
class CodeReviewWorkflow:
    def execute(self, code_files):
        # Sequential execution
        code_analysis_results = self.code_analysis_agent.execute(code_files)
        security_scan_results = self.security_scan_agent.execute(code_files)
        final_report = self.review_agg_agent.execute(
            code_analysis_results, 
            security_scan_results
        )
        return final_report
```

**After (Parallel):**
```python
import concurrent.futures

class CodeReviewWorkflow:
    def execute(self, code_files):
        # Parallel execution
        with concurrent.futures.ThreadPoolExecutor(max_workers=2) as executor:
            code_analysis_future = executor.submit(
                self.code_analysis_agent.execute, code_files
            )
            security_scan_future = executor.submit(
                self.security_scan_agent.execute, code_files
            )
            
            # Wait for both to complete
            code_analysis_results = code_analysis_future.result()
            security_scan_results = security_scan_future.result()
        
        # Aggregate results
        final_report = self.review_agg_agent.execute(
            code_analysis_results, 
            security_scan_results
        )
        return final_report
```

#### Step 5: Measure Impact

**After Deployment:**
- p95 latency: 2.3 seconds (SLA: 2 seconds) ❌ (close, but still over)
- Throughput: 26 tasks/minute (SLA: 30 tasks/minute) ❌ (improved, but still under)

**Additional Optimization:** Cache external API results

```python
from functools import lru_cache
import hashlib

class CodeAnalysisAgent:
    def __init__(self):
        self.cache = {}
    
    def execute(self, code_files):
        # Generate cache key from file contents
        cache_key = hashlib.md5(str(code_files).encode()).hexdigest()
        
        # Check cache
        if cache_key in self.cache:
            return self.cache[cache_key]
        
        # Call external API
        results = self._call_external_api(code_files)
        
        # Store in cache
        self.cache[cache_key] = results
        return results
```

**Final Results:**
- p95 latency: 1.8 seconds (SLA: 2 seconds) ✓ (10% under SLA)
- Throughput: 33 tasks/minute (SLA: 30 tasks/minute) ✓ (10% over SLA)
- Error rate: 0.5% (SLA: 1%) ✓

### Summary

**Optimizations Implemented:**
1. Parallelized code analysis and security scan (39% latency reduction)
2. Implemented caching for external API results (additional 22% latency reduction)

**Total Impact:**
- Latency: 3800ms → 1800ms (53% reduction)
- Throughput: 15.8 tasks/min → 33 tasks/min (109% increase)
- Now meeting all SLAs

**Implementation Effort:** 2 days (1 day parallelization, 1 day caching)

---

## Example 2: Reliability Improvements - Adding Error Handling and Retry Logic

### Scenario

You're reviewing a data processing workflow with frequent failures:

**Current Reliability:**
- Error rate: 15% (SLA: 1%) ❌
- MTBF: 6.7 hours
- MTTR: 45 minutes

**Top Failure Mode:**
- External API timeouts (60% of failures)
- Impact: Workflow fails, data processing incomplete

### Review Process

#### Step 1: Analyze Failure Modes

```python
import pandas as pd

# Load failure data
df = pd.read_csv('workflow_failures.csv')

# Categorize failures
failure_types = df.groupby('error_type').size().sort_values(ascending=False)
print("Failure Modes:")
for error_type, count in failure_types.items():
    percentage = (count / len(df)) * 100
    print(f"  {error_type}: {count} ({percentage:.1f}%)")

# Output:
# API Timeout: 90 (60%)
# Network Error: 30 (20%)
# Invalid Input: 20 (13%)
# Other: 10 (7%)
```

#### Step 2: Review Current Error Handling

**Current Code (No Error Handling):**
```python
class DataProcessingAgent:
    def execute(self, data):
        # No timeout, no retry, no fallback
        response = requests.post('https://api.dataprocessing.com/process', json=data)
        return response.json()
```

**Issues:**
- No timeout (requests hang for 30+ seconds)
- No retry logic (transient failures cause workflow failure)
- No fallback (no degraded service)
- No circuit breaker (cascading failures)

#### Step 3: Design Reliability Improvements

**Recommendation 1: Add Timeout and Retry Logic**

```python
import time
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

class DataProcessingAgent:
    def __init__(self):
        # Configure retry strategy
        retry_strategy = Retry(
            total=3,  # 3 retries
            backoff_factor=1,  # Exponential backoff: 1s, 2s, 4s
            status_forcelist=[429, 500, 502, 503, 504],  # Retry on these status codes
            method_whitelist=["POST"]  # Retry POST requests
        )
        
        adapter = HTTPAdapter(max_retries=retry_strategy)
        self.session = requests.Session()
        self.session.mount("https://", adapter)
    
    def execute(self, data):
        try:
            response = self.session.post(
                'https://api.dataprocessing.com/process',
                json=data,
                timeout=5  # 5-second timeout
            )
            response.raise_for_status()
            return response.json()
        except requests.exceptions.Timeout:
            # Log timeout and raise
            logger.error(f"API timeout after 5 seconds")
            raise
        except requests.exceptions.RequestException as e:
            # Log error and raise
            logger.error(f"API request failed: {e}")
            raise
```

**Expected Impact:**
- Reduce timeout failures from 60% to 10% (retry logic handles transient failures)
- Reduce overall error rate from 15% to 5%

**Recommendation 2: Add Circuit Breaker**

```python
from circuitbreaker import circuit

class DataProcessingAgent:
    @circuit(failure_threshold=5, recovery_timeout=60)
    def _call_api(self, data):
        response = self.session.post(
            'https://api.dataprocessing.com/process',
            json=data,
            timeout=5
        )
        if response.status_code == 503:
            raise ServiceUnavailableError("API is unavailable")
        return response.json()
    
    def execute(self, data):
        try:
            return self._call_api(data)
        except CircuitBreakerError:
            # Circuit is open, use fallback
            logger.warning("Circuit breaker open, using fallback")
            return self._fallback(data)
```

**Expected Impact:**
- Prevent cascading failures when API is down
- Reduce error rate from 5% to 2%

**Recommendation 3: Implement Fallback Strategy**

```python
class DataProcessingAgent:
    def _fallback(self, data):
        # Try cached results
        cached_result = self.cache.get(data)
        if cached_result:
            logger.info("Using cached result")
            return cached_result
        
        # Try basic local processing
        logger.info("Using basic local processing")
        return self._basic_processing(data)
    
    def _basic_processing(self, data):
        # Simplified processing that doesn't require external API
        return {"status": "processed_locally", "data": data}
```

**Expected Impact:**
- Reduce error rate from 2% to 0.5% (fallback provides degraded service)

#### Step 4: Implement and Measure

**After Deployment:**
- Error rate: 0.5% (SLA: 1%) ✓ (97% reduction)
- MTBF: 200 hours (30x improvement)
- MTTR: 10 minutes (78% reduction)

### Summary

**Reliability Improvements Implemented:**
1. Added 5-second timeout and 3-retry logic with exponential backoff
2. Implemented circuit breaker (open after 5 consecutive failures)
3. Added fallback to cached results and basic local processing

**Total Impact:**
- Error rate: 15% → 0.5% (97% reduction)
- MTBF: 6.7 hours → 200 hours (30x improvement)
- MTTR: 45 minutes → 10 minutes (78% reduction)
- Now meeting SLA

**Implementation Effort:** 3 days

---

## Example 3: Maintainability Enhancements - Improving Code Quality and Documentation

### Scenario

You're reviewing a workflow with maintainability issues:

**Current State:**
- Code quality score: 5/10 (poor)
- Test coverage: 30% (target: 80%)
- Documentation: Minimal (no runbooks, no architecture docs)
- Technical debt: High (duplicated code, complex functions, tight coupling)

**Business Impact:**
- New features take 2-3x longer to implement
- Bugs are difficult to diagnose and fix
- Onboarding new developers takes 2-3 weeks

### Review Process

#### Step 1: Assess Code Quality

**Automated Analysis:**
```bash
# Run code quality tools
pylint agent_code/ --output-format=json > pylint_report.json
radon cc agent_code/ -a > complexity_report.txt

# Results:
# Pylint score: 5.2/10
# Average cyclomatic complexity: 12 (target: < 10)
# Duplicated code: 25% (target: < 5%)
```

**Manual Review Findings:**
1. **Code Duplication:** Same validation logic in 3 agents
2. **Complex Functions:** Several functions > 100 lines
3. **Poor Naming:** Variables like `data`, `result`, `temp`
4. **Missing Comments:** No docstrings or comments
5. **Tight Coupling:** Agents directly call each other (no interfaces)

#### Step 2: Identify Maintainability Issues

**Issue 1: Code Duplication**

**Current (Duplicated in 3 agents):**
```python
class Agent1:
    def execute(self, data):
        # Validation logic (duplicated)
        if not data:
            raise ValueError("Data is required")
        if not isinstance(data, dict):
            raise ValueError("Data must be a dict")
        if 'id' not in data:
            raise ValueError("Data must have 'id' field")
        
        # Process data
        ...

class Agent2:
    def execute(self, data):
        # Same validation logic (duplicated)
        if not data:
            raise ValueError("Data is required")
        if not isinstance(data, dict):
            raise ValueError("Data must be a dict")
        if 'id' not in data:
            raise ValueError("Data must have 'id' field")
        
        # Process data
        ...
```

**Recommendation: Extract to Shared Utility**

```python
# validation.py
def validate_data(data):
    """Validate data structure.
    
    Args:
        data: Input data to validate
        
    Raises:
        ValueError: If data is invalid
    """
    if not data:
        raise ValueError("Data is required")
    if not isinstance(data, dict):
        raise ValueError("Data must be a dict")
    if 'id' not in data:
        raise ValueError("Data must have 'id' field")

# agent1.py
from validation import validate_data

class Agent1:
    def execute(self, data):
        validate_data(data)
        # Process data
        ...
```

**Benefits:**
- Single source of truth
- Easier to maintain and test
- Reduced risk of inconsistency

**Issue 2: Complex Functions**

**Current (150 lines, cyclomatic complexity: 18):**
```python
def process_data(data):
    # 150 lines of complex logic
    if condition1:
        if condition2:
            if condition3:
                # Nested logic
                ...
    # More complex logic
    ...
```

**Recommendation: Break into Smaller Functions**

```python
def process_data(data):
    """Process data through multiple stages.
    
    Args:
        data: Input data
        
    Returns:
        Processed data
    """
    validated_data = _validate(data)
    transformed_data = _transform(validated_data)
    enriched_data = _enrich(transformed_data)
    return enriched_data

def _validate(data):
    """Validate data structure."""
    # 20 lines of validation logic
    ...

def _transform(data):
    """Transform data format."""
    # 30 lines of transformation logic
    ...

def _enrich(data):
    """Enrich data with additional information."""
    # 40 lines of enrichment logic
    ...
```

**Benefits:**
- Each function has single responsibility
- Easier to understand and test
- Reduced cyclomatic complexity (18 → 4 per function)

#### Step 3: Improve Documentation

**Recommendation: Add Comprehensive Documentation**

**Architecture Documentation:**
```markdown
# Workflow Architecture

## Overview
This workflow processes customer data through 3 agents:
1. Validation Agent: Validates data structure
2. Enrichment Agent: Enriches data with external sources
3. Storage Agent: Stores data in database

## Agent Responsibilities

### Validation Agent
- Validates data structure and required fields
- Rejects invalid data with clear error messages
- Logs validation failures

### Enrichment Agent
- Calls external APIs to enrich data
- Implements caching to reduce API calls
- Handles API failures gracefully

### Storage Agent
- Stores data in PostgreSQL database
- Implements retry logic for transient failures
- Ensures data consistency

## Handoff Protocols

Validation Agent → Enrichment Agent:
- Input: Validated data (dict)
- Output: Enriched data (dict)
- Error handling: Retry 3 times, then fail

Enrichment Agent → Storage Agent:
- Input: Enriched data (dict)
- Output: Storage confirmation (dict)
- Error handling: Retry 5 times, then fail
```

**Operational Runbook:**
```markdown
# Operational Runbook

## Common Issues

### Issue: High Error Rate

**Symptoms:**
- Error rate > 5%
- Alert: "HighErrorRate" fired

**Diagnosis:**
1. Check dashboard for error breakdown
2. Query logs for recent errors:
   ```
   fields @timestamp, error_type, message
   | filter level = "ERROR" and @timestamp > ago(1h)
   | stats count() by error_type
   ```
3. Identify most common error type

**Resolution:**
- If "API Timeout": Check external API health, increase timeout if needed
- If "Invalid Input": Check data source for schema changes
- If "Database Error": Check database health and connections

**Escalation:**
If issue persists > 30 minutes, escalate to on-call engineer.
```

#### Step 4: Increase Test Coverage

**Recommendation: Add Unit and Integration Tests**

**Unit Tests:**
```python
import pytest
from validation import validate_data

def test_validate_data_valid():
    data = {'id': '123', 'name': 'Test'}
    validate_data(data)  # Should not raise

def test_validate_data_missing_id():
    data = {'name': 'Test'}
    with pytest.raises(ValueError, match="must have 'id' field"):
        validate_data(data)

def test_validate_data_not_dict():
    data = "not a dict"
    with pytest.raises(ValueError, match="must be a dict"):
        validate_data(data)
```

**Integration Tests:**
```python
def test_workflow_end_to_end():
    # Test complete workflow
    workflow = DataProcessingWorkflow()
    input_data = {'id': '123', 'name': 'Test'}
    
    result = workflow.execute(input_data)
    
    assert result['status'] == 'success'
    assert result['data']['id'] == '123'
    assert 'enriched_field' in result['data']
```

**After Implementation:**
- Test coverage: 30% → 85%
- All critical logic has unit tests
- All workflows have integration tests

#### Step 5: Measure Impact

**After Deployment:**
- Code quality score: 5/10 → 8/10 (60% improvement)
- Test coverage: 30% → 85%
- Documentation: Complete (architecture docs, runbooks, API docs)
- Time to implement new features: 2-3 weeks → 1 week (50% reduction)
- Time to diagnose bugs: 2 hours → 30 minutes (75% reduction)
- Onboarding time: 2-3 weeks → 1 week (67% reduction)

### Summary

**Maintainability Enhancements Implemented:**
1. Extracted duplicated code into shared utilities
2. Broke complex functions into smaller, focused functions
3. Added comprehensive documentation (architecture, runbooks, API docs)
4. Increased test coverage from 30% to 85%

**Total Impact:**
- Code quality: 5/10 → 8/10
- Test coverage: 30% → 85%
- Feature development time: -50%
- Bug diagnosis time: -75%
- Onboarding time: -67%

**Implementation Effort:** 2 weeks

---

## Example 4: Best Practices Application - Design Patterns and Anti-Patterns

### Scenario

You're reviewing a workflow and identifying best practices gaps.

**Current State:**
- Best practices compliance: 55% (11/20 practices)
- Missing: Proper error handling, observability, testing, documentation

### Review Process

#### Step 1: Evaluate Against Best Practices

**Agentic Workflow Best Practices:**

| Practice | Status | Gap |
|----------|--------|-----|
| Tasks appropriately sized | ✓ | - |
| Workflow patterns appropriate | ✓ | - |
| Context structured and complete | ✓ | - |
| Instructions clear and executable | ✓ | - |
| Tools safe and appropriate | ✓ | - |
| Handoffs efficient (< 1s) | ❌ | Handoffs take 2-3s |
| Safety guardrails in place | ❌ | No input validation |
| Quality guardrails in place | ❌ | No output validation |
| Observability comprehensive | ❌ | No distributed tracing |
| Performance evaluated regularly | ❌ | No evaluation process |

**Software Engineering Best Practices:**

| Practice | Status | Gap |
|----------|--------|-----|
| Code in version control | ✓ | - |
| CI/CD pipeline | ✓ | - |
| Unit tests | ❌ | 30% coverage (target: 80%) |
| Integration tests | ❌ | No integration tests |
| End-to-end tests | ❌ | No E2E tests |
| Code review | ✓ | - |
| Security scanning | ❌ | No automated scanning |
| Dependencies up-to-date | ✓ | - |

**Compliance Score:** 55% (11/20 practices)

#### Step 2: Identify Anti-Patterns

**Anti-Pattern 1: God Agent**

**Current:**
```python
class DataProcessingAgent:
    def execute(self, data):
        # Agent does everything (validation, enrichment, storage)
        validated_data = self._validate(data)
        enriched_data = self._enrich(validated_data)
        self._store(enriched_data)
        return {"status": "success"}
```

**Problem:** Single agent with too many responsibilities, violates single responsibility principle.

**Recommendation: Break into Multiple Focused Agents**

```python
class ValidationAgent:
    def execute(self, data):
        # Only validates
        return self._validate(data)

class EnrichmentAgent:
    def execute(self, data):
        # Only enriches
        return self._enrich(data)

class StorageAgent:
    def execute(self, data):
        # Only stores
        self._store(data)
        return {"status": "success"}
```

**Anti-Pattern 2: Chatty Handoffs**

**Current:**
```python
# Agent 1 calls Agent 2 multiple times
for item in items:
    result = agent2.execute(item)  # 100 handoffs for 100 items
```

**Problem:** Excessive handoffs increase latency.

**Recommendation: Batch Handoffs**

```python
# Agent 1 calls Agent 2 once with batch
results = agent2.execute_batch(items)  # 1 handoff for 100 items
```

#### Step 3: Apply Design Patterns

**Pattern 1: Circuit Breaker (Reliability)**

**Implementation:**
```python
from circuitbreaker import circuit

class ExternalAPIAgent:
    @circuit(failure_threshold=5, recovery_timeout=60)
    def execute(self, data):
        return self._call_external_api(data)
```

**Pattern 2: Retry with Exponential Backoff (Reliability)**

**Implementation:**
```python
import time

def execute_with_retry(func, max_retries=3):
    for attempt in range(max_retries):
        try:
            return func()
        except TransientError:
            if attempt < max_retries - 1:
                sleep_time = 2 ** attempt  # Exponential backoff
                time.sleep(sleep_time)
            else:
                raise
```

**Pattern 3: Observer Pattern (Observability)**

**Implementation:**
```python
class Agent:
    def __init__(self):
        self.observers = []
    
    def attach(self, observer):
        self.observers.append(observer)
    
    def notify(self, event):
        for observer in self.observers:
            observer.update(event)
    
    def execute(self, data):
        self.notify({"event": "task_start", "data": data})
        result = self._do_work(data)
        self.notify({"event": "task_end", "result": result})
        return result

# Attach logging and metrics observers
agent.attach(LoggingObserver())
agent.attach(MetricsObserver())
```

#### Step 4: Measure Impact

**After Implementation:**
- Best practices compliance: 55% → 90% (18/20 practices)
- Handoff latency: 2-3s → 0.5s (75% reduction)
- Error rate: 5% → 0.5% (90% reduction)
- Test coverage: 30% → 85%

### Summary

**Best Practices Applied:**
1. Broke "God Agent" into focused agents (single responsibility)
2. Batched handoffs to reduce latency
3. Applied circuit breaker pattern for reliability
4. Applied retry with exponential backoff for transient failures
5. Applied observer pattern for observability

**Anti-Patterns Removed:**
1. God Agent
2. Chatty Handoffs

**Total Impact:**
- Best practices compliance: 55% → 90%
- Handoff latency: -75%
- Error rate: -90%
- Test coverage: +183%

**Implementation Effort:** 1 week

---

## Summary

These examples demonstrate:

1. **Performance Optimization:** Parallelization and caching reduced latency by 53% and increased throughput by 109%
2. **Reliability Improvements:** Timeout, retry, circuit breaker, and fallback reduced error rate by 97%
3. **Maintainability Enhancements:** Code refactoring, documentation, and testing improved code quality by 60% and reduced development time by 50%
4. **Best Practices Application:** Design patterns and anti-pattern removal improved compliance from 55% to 90%

**Key Takeaways:**
- Use data to identify bottlenecks and prioritize improvements
- Balance performance, reliability, and maintainability
- Apply design patterns to solve common problems
- Measure impact to validate improvements
- Continuous improvement is essential for production workflows
