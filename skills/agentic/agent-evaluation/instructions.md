# Agent Evaluation - Step-by-Step Instructions

## Overview

This document provides detailed, executable instructions for evaluating AI agent performance and output quality.

---

## Prerequisites

- Completed agent implementation
- Access to agent execution environment
- Understanding of business requirements
- Ability to create test datasets
- Tools for metrics collection and analysis

---

## Step-by-Step Workflow

### Step 1: Define Evaluation Criteria

1. **Identify Key Metrics:**
   - Accuracy: Correctness of outputs
   - Success Rate: Percentage of successful completions
   - Latency: Response time (mean, p95, p99)
   - Cost: Resource consumption per task
   - Quality: Output quality scores

2. **Set Thresholds:**
   ```yaml
   metrics:
     accuracy:
       minimum: 0.90
       target: 0.95
       excellent: 0.99
     success_rate:
       minimum: 0.85
       target: 0.95
       excellent: 0.99
     latency_p95:
       minimum: 10s
       target: 5s
       excellent: 2s
   ```

3. **Prioritize Metrics:**
   - Critical (must meet minimum)
   - Important (should meet target)
   - Nice-to-have (optimize if possible)

### Step 2: Prepare Test Dataset

1. **Create Test Cases:**
   - 80% typical use cases
   - 10% edge cases
   - 10% failure scenarios

2. **Define Ground Truth:**
   ```yaml
   test_case:
     id: TC001
     input: "Refactor UserProfile component"
     expected_output:
       - "Functional component with hooks"
       - "All functionality preserved"
     acceptance_criteria:
       - "Component renders correctly"
       - "No console errors"
   ```

3. **Aim for 100-1000 test cases** for statistical significance

### Step 3: Run Evaluation

1. **Execute Agent on Test Dataset:**
   ```python
   results = []
   for test_case in test_dataset:
       start = time.time()
       output = agent.execute(test_case.input)
       latency = time.time() - start
       
       results.append({
           "test_case_id": test_case.id,
           "output": output,
           "latency": latency,
           "success": validate(output, test_case.expected)
       })
   ```

2. **Collect Metrics:**
   - Execution time
   - API calls and cost
   - Success/failure status
   - Output quality scores

### Step 4: Analyze Results

1. **Calculate Aggregate Metrics:**
   ```python
   success_rate = sum(r["success"] for r in results) / len(results)
   mean_latency = np.mean([r["latency"] for r in results])
   p95_latency = np.percentile([r["latency"] for r in results], 95)
   ```

2. **Identify Patterns:**
   - Which categories perform best/worst?
   - Common failure modes?
   - Outliers?

### Step 5: Assess Quality

1. **Score Outputs on Quality Dimensions:**
   - Correctness (1-5)
   - Completeness (1-5)
   - Coherence (1-5)
   - Usefulness (1-5)

2. **Calculate Weighted Quality Score:**
   ```python
   quality_score = (
       correctness * 0.4 +
       completeness * 0.3 +
       coherence * 0.2 +
       usefulness * 0.1
   )
   ```

### Step 6: Generate Recommendations

1. **Identify Top Issues:**
   - Performance bottlenecks
   - Common failure modes
   - Quality gaps

2. **Prioritize Improvements:**
   - High impact, low effort (quick wins)
   - High impact, high effort (strategic)

3. **Create Action Plan:**
   ```yaml
   recommendations:
     - id: R1
       priority: high
       issue: "Latency exceeds target"
       recommendation: "Implement caching"
       expected_impact: "40% latency reduction"
       effort: "2-3 days"
   ```

### Step 7: Create Evaluation Report

1. **Executive Summary:**
   - Overall assessment (pass/fail)
   - Key metrics
   - Top recommendations

2. **Detailed Results:**
   - Performance metrics with charts
   - Quality assessment
   - Failure analysis

3. **Share with Stakeholders**

### Step 8: Implement Continuous Evaluation

1. **Automate Evaluation:**
   - Schedule daily/weekly runs
   - Automated test execution
   - Automated reporting

2. **Set Up Monitoring:**
   - Real-time metrics collection
   - Alerting for regressions
   - Performance dashboards

3. **Track Trends Over Time**

---

## Success Criteria

- [ ] Evaluation criteria defined and approved
- [ ] Test dataset created (>100 test cases)
- [ ] All test cases executed
- [ ] Metrics collected and analyzed
- [ ] Quality assessment completed
- [ ] Recommendations generated and prioritized
- [ ] Evaluation report created and shared
- [ ] Continuous evaluation implemented (if applicable)

---

## Next Steps

- Implement recommendations
- Set up agent-observability for detailed monitoring
- Conduct agentic-workflow-review for comprehensive optimization
