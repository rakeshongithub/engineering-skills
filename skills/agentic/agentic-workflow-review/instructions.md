# agentic-workflow-review - Step-by-Step Instructions

This guide provides executable instructions for reviewing and improving agentic workflows.

---

## Prerequisites

- Completed agent workflow design and implementation
- At least 30 days of performance data from observability platform
- Access to codebase and configuration
- Understanding of business requirements and SLAs
- Incident reports and postmortems (if available)

---

## Step-by-Step Process

### Step 1: Gather Workflow Context (30 minutes)

**Objective:** Collect all necessary information about the workflow.

**Actions:**

1. **Review Workflow Design:**
   - Obtain workflow diagram showing agents, tasks, and handoffs
   - Review task definitions and agent responsibilities
   - Understand workflow patterns (sequential, parallel, pipeline)

2. **Collect Performance Data:**
   - Export metrics from observability platform (last 30-90 days)
   - Collect latency metrics (p50, p95, p99)
   - Collect throughput metrics (tasks/hour, workflows/day)
   - Collect error rate metrics (% failed tasks)
   - Collect resource usage metrics (CPU, memory, API calls, cost)

3. **Review Incident History:**
   - Collect incident reports and postmortems
   - Identify recurring issues
   - Understand impact and resolution time

4. **Interview Stakeholders:**
   - Developers: Pain points, technical debt
   - Operators: Operational challenges, common failures
   - Business owners: Business priorities, SLAs, growth plans

**Validation Checklist:**
- [ ] Workflow design documentation is available
- [ ] Performance data covers at least 30 days
- [ ] Incident reports are collected
- [ ] Stakeholders have been interviewed

---

### Step 2: Analyze Performance (60 minutes)

**Objective:** Assess current performance and identify bottlenecks.

**Actions:**

1. **Calculate Key Metrics:**

**Latency:**
```python
import pandas as pd
import numpy as np

# Load performance data
df = pd.read_csv('performance_data.csv')

# Calculate latency percentiles
p50 = df['task_duration_ms'].quantile(0.50)
p95 = df['task_duration_ms'].quantile(0.95)
p99 = df['task_duration_ms'].quantile(0.99)

print(f"Latency: p50={p50}ms, p95={p95}ms, p99={p99}ms")

# Compare to SLA
sla_p95 = 2000  # 2 seconds
if p95 > sla_p95:
    print(f"⚠️ p95 latency {p95}ms exceeds SLA {sla_p95}ms")
else:
    print(f"✓ p95 latency {p95}ms meets SLA {sla_p95}ms")
```

**Throughput:**
```python
# Calculate throughput
df['timestamp'] = pd.to_datetime(df['timestamp'])
df['hour'] = df['timestamp'].dt.floor('H')
throughput = df.groupby('hour').size()

avg_throughput = throughput.mean()
min_throughput = throughput.min()
max_throughput = throughput.max()

print(f"Throughput: avg={avg_throughput}/hr, min={min_throughput}/hr, max={max_throughput}/hr")

# Compare to SLA
sla_throughput = 100  # 100 tasks/hour
if avg_throughput < sla_throughput:
    print(f"⚠️ Avg throughput {avg_throughput}/hr below SLA {sla_throughput}/hr")
else:
    print(f"✓ Avg throughput {avg_throughput}/hr meets SLA {sla_throughput}/hr")
```

**Error Rate:**
```python
# Calculate error rate
total_tasks = len(df)
failed_tasks = len(df[df['success'] == False])
error_rate = (failed_tasks / total_tasks) * 100

print(f"Error Rate: {error_rate:.2f}% ({failed_tasks}/{total_tasks})")

# Compare to SLA
sla_error_rate = 1.0  # 1%
if error_rate > sla_error_rate:
    print(f"⚠️ Error rate {error_rate:.2f}% exceeds SLA {sla_error_rate}%")
else:
    print(f"✓ Error rate {error_rate:.2f}% meets SLA {sla_error_rate}%")
```

2. **Identify Bottlenecks:**

```python
# Find slowest agents/tasks
slowest_agents = df.groupby('agent_id')['task_duration_ms'].quantile(0.95).sort_values(ascending=False)
print("Slowest Agents (p95 latency):")
print(slowest_agents.head(5))

# Find agents with highest error rates
error_rates = df.groupby('agent_id')['success'].apply(lambda x: (1 - x.mean()) * 100).sort_values(ascending=False)
print("\nAgents with Highest Error Rates:")
print(error_rates.head(5))
```

3. **Analyze Trends:**

```python
import matplotlib.pyplot as plt

# Plot latency trend
df_daily = df.groupby(df['timestamp'].dt.date)['task_duration_ms'].quantile(0.95)
plt.figure(figsize=(12, 6))
plt.plot(df_daily.index, df_daily.values)
plt.axhline(y=sla_p95, color='r', linestyle='--', label='SLA')
plt.xlabel('Date')
plt.ylabel('p95 Latency (ms)')
plt.title('Latency Trend Over Time')
plt.legend()
plt.show()
```

**Validation Checklist:**
- [ ] Key metrics are calculated
- [ ] SLA compliance is measured
- [ ] Bottlenecks are identified
- [ ] Trends are analyzed

---

### Step 3: Assess Reliability (45 minutes)

**Objective:** Evaluate reliability and identify failure modes.

**Actions:**

1. **Calculate Reliability Metrics:**

```python
# Calculate success rate
success_rate = (df['success'].sum() / len(df)) * 100
print(f"Success Rate: {success_rate:.2f}%")

# Calculate MTBF (Mean Time Between Failures)
failures = df[df['success'] == False]
if len(failures) > 1:
    failure_times = pd.to_datetime(failures['timestamp'])
    time_between_failures = failure_times.diff().dropna()
    mtbf_hours = time_between_failures.mean().total_seconds() / 3600
    print(f"MTBF: {mtbf_hours:.2f} hours")
```

2. **Analyze Failure Modes:**

```python
# Categorize errors
error_types = df[df['success'] == False].groupby('error_type').size().sort_values(ascending=False)
print("Top Failure Modes:")
for error_type, count in error_types.head(5).items():
    percentage = (count / len(failures)) * 100
    print(f"  {error_type}: {count} ({percentage:.1f}%)")
```

3. **Review Error Handling:**

**Checklist:**
- [ ] All external API calls have timeout and retry logic
- [ ] All agent tasks have error handling
- [ ] All handoffs have error recovery
- [ ] All errors are logged and monitored
- [ ] Fallback strategies exist for critical paths

**Validation Checklist:**
- [ ] Reliability metrics are calculated
- [ ] Failure modes are identified
- [ ] Error handling is assessed

---

### Step 4: Evaluate Maintainability (45 minutes)

**Objective:** Assess code quality and ease of modification.

**Actions:**

1. **Review Code Quality:**

**Automated Analysis:**
```bash
# Run code quality tools
pylint agent_code/ --output-format=json > pylint_report.json
flake8 agent_code/ --format=json > flake8_report.json
radon cc agent_code/ -a > complexity_report.txt
```

**Manual Review:**
- [ ] Agents follow single responsibility principle
- [ ] Code is DRY (no duplication)
- [ ] Functions/methods are small and focused (< 50 lines)
- [ ] Variable/function names are descriptive
- [ ] Code has appropriate comments
- [ ] Code follows language/framework conventions

2. **Review Documentation:**

**Checklist:**
- [ ] Workflow design is documented
- [ ] Agent responsibilities are documented
- [ ] Handoff protocols are documented
- [ ] Configuration is documented
- [ ] Operational runbooks exist
- [ ] Troubleshooting guides exist

3. **Assess Test Coverage:**

```bash
# Run test coverage analysis
pytest --cov=agent_code --cov-report=html
```

**Target:** > 80% test coverage

**Validation Checklist:**
- [ ] Code quality is assessed
- [ ] Documentation completeness is evaluated
- [ ] Test coverage is measured

---

### Step 5: Identify Best Practices Gaps (30 minutes)

**Objective:** Compare workflow against industry best practices.

**Actions:**

1. **Evaluate Agentic Workflow Best Practices:**

**Checklist:**
- [ ] Tasks are appropriately sized (1-4 hours for agents)
- [ ] Workflow patterns are appropriate for use case
- [ ] Context is structured and complete
- [ ] Instructions are clear and executable
- [ ] Tools are safe and appropriate
- [ ] Handoffs are efficient (< 1s latency)
- [ ] Safety guardrails prevent harmful actions
- [ ] Quality guardrails ensure output quality
- [ ] Observability covers all workflow steps
- [ ] Agent performance is evaluated regularly

2. **Evaluate Software Engineering Best Practices:**

**Checklist:**
- [ ] All code is in version control (Git)
- [ ] CI/CD pipeline automates deployment
- [ ] Unit tests cover critical logic
- [ ] Integration tests validate agent interactions
- [ ] End-to-end tests validate complete workflows
- [ ] Code is reviewed before merging
- [ ] Security scanning is automated
- [ ] Dependencies are kept up-to-date

3. **Calculate Compliance Score:**

```python
# Calculate best practices compliance
total_practices = 18  # Total number of best practices
compliant_practices = 12  # Number of practices followed

compliance_score = (compliant_practices / total_practices) * 100
print(f"Best Practices Compliance: {compliance_score:.0f}% ({compliant_practices}/{total_practices})")
```

**Validation Checklist:**
- [ ] All best practices are evaluated
- [ ] Gaps are documented
- [ ] Compliance score is calculated

---

### Step 6: Generate Recommendations (60 minutes)

**Objective:** Create specific, actionable recommendations.

**Actions:**

1. **Identify Optimization Opportunities:**

**Performance Optimizations:**
- Parallelization: Can sequential tasks be parallelized?
- Caching: Can results be cached to avoid redundant work?
- Batching: Can multiple requests be batched?
- Algorithm optimization: Can algorithms be improved?

**Example Recommendation:**
```markdown
### Recommendation: Parallelize Code Analysis and Security Scan

**Current State:**
- Code analysis and security scan run sequentially
- Total time: 2000ms (code analysis) + 1500ms (security scan) = 3500ms

**Proposed Change:**
- Run code analysis and security scan in parallel
- Total time: max(2000ms, 1500ms) = 2000ms

**Expected Impact:**
- Latency reduction: 3500ms → 2000ms (43% improvement)
- Throughput increase: 17 tasks/min → 30 tasks/min (76% improvement)

**Implementation Effort:** Low (1 day)
- Modify workflow orchestrator to run tasks in parallel
- Ensure tasks are independent (no shared state)
- Test parallel execution

**Priority:** High (high impact, low effort)
```

2. **Prioritize Recommendations:**

**Prioritization Matrix:**

| Recommendation | Impact | Effort | Priority |
|----------------|--------|--------|----------|
| Parallelize code analysis and security scan | High (43% latency reduction) | Low (1 day) | **High** |
| Cache external API results | High (30% latency reduction) | Medium (3 days) | **High** |
| Add circuit breaker to external API | High (90% error reduction) | Medium (2 days) | **High** |
| Refactor large agents | Medium (improved maintainability) | High (10 days) | Medium |
| Optimize algorithm X | Low (15% latency reduction) | High (7 days) | Low |

**Validation Checklist:**
- [ ] Recommendations are specific and actionable
- [ ] Impact is estimated with data
- [ ] Effort is estimated
- [ ] Recommendations are prioritized

---

### Step 7: Create Improvement Plan (30 minutes)

**Objective:** Prioritize and schedule recommendations.

**Actions:**

1. **Create Phased Roadmap:**

```markdown
## Improvement Plan

### Phase 1: Quick Wins (Weeks 1-2)
**Goal:** Achieve immediate performance and reliability improvements

- **Parallelize code analysis and security scan** (Owner: Alice, 1 day)
  - Expected: 43% latency reduction
  - Dependencies: None
  
- **Add circuit breaker to external API** (Owner: Bob, 2 days)
  - Expected: 90% error reduction (5% → 0.5%)
  - Dependencies: None
  
- **Extract duplicated validation logic** (Owner: Charlie, 1 day)
  - Expected: Improved maintainability
  - Dependencies: None

**Success Metrics:**
- p95 latency: 5s → 3s (40% improvement)
- Error rate: 5% → 0.5% (90% improvement)

### Phase 2: Major Improvements (Weeks 3-6)
**Goal:** Implement caching and comprehensive error handling

- **Implement caching for external API results** (Owner: Alice, 3 days)
  - Expected: 30% latency reduction
  - Dependencies: Circuit breaker (Phase 1)
  
- **Add comprehensive error handling and retries** (Owner: Bob, 5 days)
  - Expected: Improved reliability
  - Dependencies: None
  
- **Add integration and end-to-end tests** (Owner: Charlie, 5 days)
  - Expected: Test coverage 40% → 80%
  - Dependencies: None

**Success Metrics:**
- p95 latency: 3s → 2s (33% improvement from Phase 1)
- Test coverage: 40% → 80%

### Phase 3: Long-Term (Weeks 7-12)
**Goal:** Improve maintainability and implement advanced features

- **Refactor large agents into smaller, focused agents** (Owner: Team, 10 days)
  - Expected: Improved maintainability and extensibility
  - Dependencies: Comprehensive tests (Phase 2)
  
- **Implement distributed tracing** (Owner: Alice, 5 days)
  - Expected: Improved debugging and observability
  - Dependencies: None

**Success Metrics:**
- Code quality score: 6/10 → 8/10
- MTTR: 30 min → 15 min (50% improvement)
```

2. **Define Success Metrics:**

**Overall Success Metrics:**
- p95 latency: 5s → 2s (60% improvement)
- Error rate: 5% → 0.5% (90% improvement)
- Test coverage: 40% → 80%
- Code quality score: 6/10 → 8/10
- MTTR: 30 min → 15 min

**Validation Checklist:**
- [ ] Recommendations are organized into phases
- [ ] Owners are assigned
- [ ] Dependencies are identified
- [ ] Success metrics are defined

---

### Step 8: Document and Present Review (30 minutes)

**Objective:** Create review report and present findings.

**Actions:**

1. **Create Review Report:**

**Template:**
```markdown
# Agentic Workflow Review Report

## Executive Summary

**Workflow:** [Workflow Name]  
**Review Date:** [Date]  
**Reviewer:** [Name]

**Key Findings:**
- Performance: [Summary of performance issues]
- Reliability: [Summary of reliability issues]
- Maintainability: [Summary of maintainability issues]

**Top Recommendations:**
1. [Recommendation 1] ([Impact], [Effort])
2. [Recommendation 2] ([Impact], [Effort])
3. [Recommendation 3] ([Impact], [Effort])

**Expected Impact:**
- [Metric 1]: [Current] → [Target] ([% improvement])
- [Metric 2]: [Current] → [Target] ([% improvement])

**Investment:** [Timeline], [Resources]

## Current State Assessment
[Detailed analysis]

## Recommendations
[Prioritized list]

## Improvement Plan
[Phased roadmap]
```

2. **Prepare Presentation:**

**Slides:**
1. Executive Summary (1 slide)
2. Current State Assessment (2-3 slides)
3. Key Findings (2-3 slides)
4. Recommendations (3-5 slides)
5. Improvement Plan (2-3 slides)
6. Expected Impact and ROI (1 slide)

3. **Conduct Review Meeting:**
- Present findings and recommendations
- Discuss priorities and trade-offs
- Get buy-in for improvement plan
- Assign action items

**Validation Checklist:**
- [ ] Review report is complete
- [ ] Presentation is prepared
- [ ] Stakeholders are aligned
- [ ] Action items are assigned

---

## Success Criteria

Workflow review is successful when:

- [ ] All workflow aspects are reviewed (performance, reliability, maintainability)
- [ ] Performance data covers at least 30 days
- [ ] Bottlenecks and failure modes are identified
- [ ] Recommendations are specific, actionable, and prioritized
- [ ] Improvement plan is phased and realistic
- [ ] Stakeholders are aligned and committed
- [ ] Success metrics are defined and measurable
- [ ] Follow-up tracking is established

---

## Troubleshooting

### Issue: Insufficient performance data

**Solution:**
- Wait to collect at least 30 days of data
- Use synthetic load testing to generate data
- Review staging environment data as proxy

---

### Issue: Stakeholders disagree on priorities

**Solution:**
- Use prioritization matrix (impact vs. effort)
- Align on business goals and constraints
- Focus on quick wins to build momentum

---

### Issue: Recommendations are not implemented

**Solution:**
- Assign clear owners and deadlines
- Track progress in regular meetings
- Escalate blockers to leadership
- Celebrate early wins to maintain momentum

---

## Next Steps

After completing workflow review:

1. **Implement recommendations** according to improvement plan
2. **Track progress** against success metrics
3. **Measure impact** of implemented changes
4. **Conduct follow-up review** in 3-6 months
5. **Establish continuous improvement process** for ongoing optimization
