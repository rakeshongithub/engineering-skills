# Agent Evaluation - Quick Reference

## Overview

**Purpose:** Evaluate AI agent performance and output quality to measure effectiveness and identify improvements.

**Complexity:** Intermediate  
**Estimated Time:** 1-3 hours  
**Category:** Agentic Engineering

---

## When to Use

- After implementing an agent to measure performance
- When optimizing workflows to identify bottlenecks
- To compare different agent configurations (A/B testing)
- For continuous monitoring and improvement
- Before deploying to production

---

## Quick Start

### 1. Define Evaluation Criteria

```yaml
metrics:
  accuracy:
    target: 0.95
  success_rate:
    target: 0.95
  latency_p95:
    target: 5s
  cost_per_task:
    target: $0.50
```

### 2. Create Test Dataset

- 100-1000 test cases
- 80% typical, 10% edge cases, 10% failures
- Include ground truth and acceptance criteria

### 3. Run Evaluation

```python
results = []
for test_case in test_dataset:
    output = agent.execute(test_case.input)
    results.append({
        "success": validate(output, test_case.expected),
        "latency": measure_latency(),
        "quality": score_quality(output)
    })
```

### 4. Analyze and Report

- Calculate aggregate metrics
- Identify failure patterns
- Generate recommendations
- Create evaluation report

---

## Key Metrics

### Performance Metrics

- **Accuracy:** Correctness of outputs
- **Success Rate:** % of successful completions
- **Latency:** Response time (mean, p95, p99)
- **Cost:** Resource consumption per task

### Quality Metrics

- **Correctness:** Does it solve the problem?
- **Completeness:** All requirements addressed?
- **Coherence:** Well-structured and logical?
- **Usefulness:** Provides value?

---

## Evaluation Checklist

- [ ] Evaluation criteria defined
- [ ] Test dataset created (>100 cases)
- [ ] All test cases executed
- [ ] Metrics collected and analyzed
- [ ] Quality assessment completed
- [ ] Failure analysis performed
- [ ] Recommendations generated
- [ ] Report created and shared

---

## Common Patterns

### Pattern 1: Pre-Production Validation

```
agent-workflow-design
    ↓
agent-evaluation
    ↓
production-readiness
    ↓
Deploy
```

### Pattern 2: Continuous Improvement

```
agent-evaluation
    ↓
Identify Issues
    ↓
Implement Fixes
    ↓
agent-evaluation (re-evaluate)
    ↓
Repeat
```

### Pattern 3: A/B Testing

```
Design Agent A and Agent B
    ↓
agent-evaluation (compare)
    ↓
Select Best
    ↓
Deploy Winner
```

---

## Related Skills

**Requires:**
- agent-task-decomposition
- agent-workflow-design
- agent-guardrails

**Commonly Followed By:**
- agent-observability
- agentic-workflow-review

**Works With:**
- testing-strategy
- production-readiness

---

## Resources

- [Full Documentation](SKILL.md)
- [Step-by-Step Instructions](instructions.md)
- [Practical Examples](examples.md)

---

**Last Updated:** 2026-09-08  
**Version:** 1.0.0
