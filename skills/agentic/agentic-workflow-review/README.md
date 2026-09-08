# agentic-workflow-review - Quick Reference

Review and improve agentic workflows to optimize performance, enhance reliability, and improve maintainability.

---

## Quick Start

### 1. Gather Context

**Required:**
- Workflow design documentation
- Performance data (30+ days)
- Incident reports
- Business requirements and SLAs
- Codebase and configuration

---

### 2. Analyze Performance

**Key Metrics:**
- **Latency:** p50, p95, p99 task duration
- **Throughput:** Tasks completed per hour/day
- **Error Rate:** % of failed tasks
- **Resource Usage:** CPU, memory, API calls, cost

**Compare to SLAs:**
- Latency SLA compliance %
- Throughput SLA compliance %
- Error rate SLA compliance %

---

### 3. Assess Reliability

**Key Metrics:**
- **Availability:** % uptime
- **Success Rate:** % of workflows completed successfully
- **MTBF:** Mean time between failures
- **MTTR:** Mean time to recover

**Analyze:**
- Common failure modes
- Error handling coverage
- Resilience to external failures

---

### 4. Evaluate Maintainability

**Assess:**
- **Code Quality:** Modularity, readability, testability
- **Documentation:** Workflow design, runbooks, API docs
- **Test Coverage:** Unit, integration, end-to-end tests
- **Extensibility:** Ease of adding/modifying agents

---

### 5. Identify Gaps

**Best Practices Checklist:**
- [ ] Tasks appropriately sized
- [ ] Workflow patterns appropriate
- [ ] Context structured and complete
- [ ] Instructions clear and executable
- [ ] Tools safe and appropriate
- [ ] Handoffs efficient (< 1s)
- [ ] Safety guardrails in place
- [ ] Quality guardrails in place
- [ ] Observability comprehensive
- [ ] Performance evaluated regularly

---

### 6. Generate Recommendations

**Prioritization Matrix:**

| Impact \ Effort | Low | Medium | High |
|-----------------|-----|--------|------|
| **High** | **DO FIRST** | **DO NEXT** | **PLAN** |
| **Medium** | **DO SOON** | **EVALUATE** | **DEFER** |
| **Low** | **MAYBE** | **DEFER** | **SKIP** |

---

### 7. Create Improvement Plan

**Phased Roadmap:**
- **Phase 1 (Weeks 1-2):** Quick wins (high impact, low effort)
- **Phase 2 (Weeks 3-6):** Major improvements (high impact, medium effort)
- **Phase 3 (Weeks 7-12):** Long-term (medium impact, high effort)

---

## Common Optimizations

### Performance

**Parallelization:**
- Run independent tasks in parallel
- Expected impact: 30-50% latency reduction

**Caching:**
- Cache external API results
- Expected impact: 20-40% latency reduction

**Batching:**
- Batch multiple requests into one
- Expected impact: 10-30% latency reduction

---

### Reliability

**Timeout and Retry:**
```python
response = requests.post(url, json=data, timeout=5)
# Retry 3 times with exponential backoff
```

**Circuit Breaker:**
```python
@circuit(failure_threshold=5, recovery_timeout=60)
def call_external_api(data):
    return requests.post(url, json=data)
```

**Fallback:**
```python
try:
    return call_external_api(data)
except CircuitBreakerError:
    return fallback_cached_result(data)
```

---

### Maintainability

**Extract Duplicated Code:**
```python
# Before: Duplicated in 3 agents
if not data or not isinstance(data, dict):
    raise ValueError("Invalid data")

# After: Shared utility
from validation import validate_data
validate_data(data)
```

**Break Complex Functions:**
```python
# Before: 150 lines, complexity 18
def process_data(data):
    # Complex logic
    ...

# After: 4 functions, complexity 4 each
def process_data(data):
    validated = _validate(data)
    transformed = _transform(validated)
    enriched = _enrich(transformed)
    return enriched
```

---

## Review Report Template

```markdown
# Workflow Review Report

## Executive Summary

**Workflow:** [Name]  
**Review Date:** [Date]  
**Reviewer:** [Name]

**Key Findings:**
- Performance: [Summary]
- Reliability: [Summary]
- Maintainability: [Summary]

**Top Recommendations:**
1. [Recommendation 1] ([Impact], [Effort])
2. [Recommendation 2] ([Impact], [Effort])
3. [Recommendation 3] ([Impact], [Effort])

**Expected Impact:**
- [Metric 1]: [Current] → [Target] ([% improvement])
- [Metric 2]: [Current] → [Target] ([% improvement])

**Investment:** [Timeline], [Resources]

## Current State
[Analysis]

## Recommendations
[Prioritized list]

## Improvement Plan
[Phased roadmap]
```

---

## Key Metrics

### Review Quality

| Metric | Target |
|--------|--------|
| Review Completeness | 100% (all aspects reviewed) |
| Recommendation Quality | > 80% implemented |
| Performance Improvement | > 30% |
| Reliability Improvement | > 50% |
| Maintainability Improvement | > 20% |

---

## Common Mistakes

### 1. Reviewing Without Data

**Problem:** Recommendations not based on actual bottlenecks.

**Solution:** Require 30+ days of performance data.

---

### 2. Focusing Only on Performance

**Problem:** Ignoring reliability and maintainability.

**Solution:** Balance optimization across all three dimensions.

---

### 3. No Prioritization

**Problem:** Team doesn't know where to start.

**Solution:** Use impact vs. effort matrix to prioritize.

---

### 4. No Follow-Up

**Problem:** Recommendations not implemented.

**Solution:** Assign owners, track progress, measure success.

---

## Decision Trees

### Performance vs. Reliability

```
What is the primary issue?
├─ Performance (high latency, low throughput)
│  └─ Prioritize: Parallelization, caching, batching
├─ Reliability (high error rate, frequent failures)
│  └─ Prioritize: Timeout, retry, circuit breaker, fallback
└─ Both
   └─ Address critical reliability issues first, then optimize performance
```

---

## Related Skills

- **agent-workflow-design**: Design workflow before reviewing
- **agent-evaluation**: Evaluate performance to inform review
- **agent-observability**: Use observability data for analysis
- **agent-guardrails**: Add guardrails based on reliability findings
- **agent-task-decomposition**: Re-decompose tasks based on findings

---

## Additional Resources

- [SKILL.md](./SKILL.md) - Comprehensive documentation
- [instructions.md](./instructions.md) - Step-by-step guide
- [examples.md](./examples.md) - Detailed examples

---

**Complexity:** Advanced  
**Estimated Time:** 2-3 hours  
**Version:** 1.0.0
