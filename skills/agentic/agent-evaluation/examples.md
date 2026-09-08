# Agent Evaluation - Practical Examples

This document provides four comprehensive examples of evaluating AI agents.

---

## Example 1: Success Metrics Evaluation - Code Review Agent

### Scenario

Evaluate a code review agent that analyzes pull requests and provides feedback.

### Evaluation Criteria

```yaml
metrics:
  accuracy:
    description: "Percentage of issues correctly identified"
    target: 0.90
  
  false_positive_rate:
    description: "Percentage of flagged issues that are not real issues"
    target: 0.10
  
  completeness:
    description: "Percentage of actual issues identified"
    target: 0.85
  
  latency:
    description: "Time to review a PR"
    target: 30s
  
  cost_per_review:
    description: "Cost in API calls and compute"
    target: $0.50
```

### Test Dataset

- 500 pull requests with known issues
- Ground truth: Manual review by senior engineers
- Categories: Bug fixes (40%), Features (35%), Refactoring (15%), Documentation (10%)

### Results

```python
results = {
    "total_test_cases": 500,
    "successful_reviews": 475,
    "success_rate": 0.95,
    
    "accuracy": 0.88,  # Below target (0.90)
    "false_positive_rate": 0.12,  # Above target (0.10)
    "completeness": 0.82,  # Below target (0.85)
    
    "latency": {
        "mean": 25.3,
        "p95": 45.2,
        "p99": 68.1
    },
    
    "cost_per_review": 0.42,  # Meets target
    
    "by_category": {
        "bug_fixes": {"accuracy": 0.92, "completeness": 0.88},
        "features": {"accuracy": 0.85, "completeness": 0.79},
        "refactoring": {"accuracy": 0.86, "completeness": 0.75},
        "documentation": {"accuracy": 0.90, "completeness": 0.85}
    }
}
```

### Analysis

**Strengths:**
- High success rate (95%)
- Good performance on bug fixes
- Cost-effective
- Latency within target

**Weaknesses:**
- Accuracy below target (88% vs. 90%)
- High false positive rate (12% vs. 10%)
- Completeness below target (82% vs. 85%)
- Struggles with refactoring reviews

### Recommendations

```yaml
recommendations:
  - id: R1
    priority: high
    issue: "High false positive rate (12%)"
    recommendation: "Improve context engineering to reduce false positives"
    expected_impact: "Reduce false positives by 50% (from 12% to 6%)"
    effort: "3-5 days"
  
  - id: R2
    priority: high
    issue: "Low completeness on refactoring (75%)"
    recommendation: "Add specialized instructions for refactoring patterns"
    expected_impact: "Increase refactoring completeness to 85%"
    effort: "2-3 days"
  
  - id: R3
    priority: medium
    issue: "Overall accuracy below target"
    recommendation: "Fine-tune prompts based on failure analysis"
    expected_impact: "Increase accuracy from 88% to 92%"
    effort: "1-2 days"
```

---

## Example 2: Quality Assessment - Documentation Agent

### Scenario

Evaluate a documentation agent that generates API documentation from code.

### Quality Rubric

```yaml
quality_dimensions:
  correctness:
    weight: 0.35
    description: "Accuracy of documented API behavior"
  
  completeness:
    weight: 0.30
    description: "Coverage of all API endpoints and parameters"
  
  clarity:
    weight: 0.20
    description: "Readability and understandability"
  
  usefulness:
    weight: 0.15
    description: "Includes examples and edge cases"
```

### Test Dataset

- 200 API endpoints across 10 microservices
- Ground truth: Manually written documentation by technical writers

### Quality Scoring Process

```python
def score_quality(generated_doc, ground_truth):
    scores = {}
    
    # Correctness: Compare API signatures, parameters, return types
    scores["correctness"] = compare_api_details(generated_doc, ground_truth)
    
    # Completeness: Check coverage of endpoints, parameters, examples
    scores["completeness"] = calculate_coverage(generated_doc, ground_truth)
    
    # Clarity: Readability metrics (Flesch-Kincaid, sentence length)
    scores["clarity"] = assess_readability(generated_doc)
    
    # Usefulness: Presence of examples, edge cases, common errors
    scores["usefulness"] = count_useful_elements(generated_doc)
    
    # Weighted overall score
    overall = (
        scores["correctness"] * 0.35 +
        scores["completeness"] * 0.30 +
        scores["clarity"] * 0.20 +
        scores["usefulness"] * 0.15
    )
    
    return {**scores, "overall": overall}
```

### Results

```python
quality_results = {
    "mean_scores": {
        "correctness": 4.2,  # out of 5
        "completeness": 3.8,
        "clarity": 4.5,
        "usefulness": 3.5,
        "overall": 4.0
    },
    
    "distribution": {
        "excellent (4.5-5.0)": 35,
        "good (4.0-4.5)": 80,
        "acceptable (3.5-4.0)": 60,
        "needs_improvement (<3.5)": 25
    },
    
    "common_issues": [
        "Missing examples for complex endpoints (40% of cases)",
        "Incomplete parameter descriptions (25% of cases)",
        "No error handling documentation (30% of cases)"
    ]
}
```

### Recommendations

```yaml
recommendations:
  - id: R1
    priority: high
    issue: "40% of docs missing examples"
    recommendation: "Add instruction to always include code examples"
    expected_impact: "Increase usefulness score from 3.5 to 4.2"
  
  - id: R2
    priority: medium
    issue: "Incomplete parameter descriptions"
    recommendation: "Enhance context with parameter metadata from code"
    expected_impact: "Increase completeness score from 3.8 to 4.3"
  
  - id: R3
    priority: medium
    issue: "Missing error handling documentation"
    recommendation: "Add error handling as required section in template"
    expected_impact: "Increase completeness score by 0.3"
```

---

## Example 3: A/B Testing - Comparing Two Agent Configurations

### Scenario

Compare two configurations of a test generation agent:
- **Agent A:** Uses GPT-4 with detailed prompts
- **Agent B:** Uses GPT-4 with concise prompts + few-shot examples

### Test Dataset

- 300 functions to generate tests for
- Same test dataset for both agents

### Comparison Metrics

```yaml
metrics:
  - test_coverage
  - test_quality (correctness, edge cases)
  - latency
  - cost
```

### Results

```python
comparison = {
    "agent_a": {
        "test_coverage": 0.85,
        "test_quality": 4.2,
        "latency_mean": 12.3,
        "cost_per_test": 0.35
    },
    
    "agent_b": {
        "test_coverage": 0.88,
        "test_quality": 4.5,
        "latency_mean": 8.7,
        "cost_per_test": 0.28
    },
    
    "improvement": {
        "test_coverage": "+3.5%",
        "test_quality": "+7.1%",
        "latency": "-29.3%",
        "cost": "-20.0%"
    },
    
    "statistical_significance": {
        "test_coverage": "p < 0.05 (significant)",
        "test_quality": "p < 0.01 (highly significant)",
        "latency": "p < 0.001 (highly significant)",
        "cost": "p < 0.01 (highly significant)"
    }
}
```

### Analysis

**Agent B outperforms Agent A on all metrics:**
- Higher test coverage (+3.5%)
- Better test quality (+7.1%)
- Faster execution (-29.3%)
- Lower cost (-20.0%)
- All differences statistically significant

**Why Agent B Performs Better:**
- Few-shot examples provide concrete patterns
- Concise prompts reduce token usage (faster, cheaper)
- Examples guide agent to better edge case coverage

### Decision

**Deploy Agent B to production**

Expected benefits:
- 20% cost reduction
- 30% latency improvement
- 7% quality improvement

---

## Example 4: Continuous Improvement - Iterative Evaluation

### Scenario

Implement continuous evaluation for a bug triage agent over 3 months.

### Evaluation Schedule

- **Weekly:** Automated evaluation on 50 test cases
- **Monthly:** Full evaluation on 500 test cases
- **Continuous:** Real-time metrics from production

### Timeline and Results

#### Month 1: Baseline

```python
baseline = {
    "accuracy": 0.82,
    "latency_p95": 15.2,
    "cost_per_triage": 0.45,
    "user_satisfaction": 3.5  # out of 5
}
```

**Issues Identified:**
- Low accuracy on security bugs (65%)
- High latency on complex issues
- Missing priority classification

#### Month 1 Actions:

```yaml
improvements:
  - Add specialized context for security bugs
  - Implement caching for common patterns
  - Add priority classification to output schema
```

#### Month 2: After Improvements

```python
month_2 = {
    "accuracy": 0.88,  # +7.3%
    "latency_p95": 10.5,  # -30.9%
    "cost_per_triage": 0.38,  # -15.6%
    "user_satisfaction": 4.1  # +17.1%
}
```

**New Issues Identified:**
- Still struggling with edge cases (rare bug types)
- Inconsistent priority classification

#### Month 2 Actions:

```yaml
improvements:
  - Add few-shot examples for rare bug types
  - Refine priority classification logic
  - Add post-execution validation for priority
```

#### Month 3: After Further Improvements

```python
month_3 = {
    "accuracy": 0.93,  # +5.7% from month 2
    "latency_p95": 9.8,  # -6.7%
    "cost_per_triage": 0.35,  # -7.9%
    "user_satisfaction": 4.5  # +9.8%
}
```

### Overall Improvement (Baseline to Month 3)

```python
total_improvement = {
    "accuracy": "+13.4% (0.82 → 0.93)",
    "latency_p95": "-35.5% (15.2s → 9.8s)",
    "cost_per_triage": "-22.2% ($0.45 → $0.35)",
    "user_satisfaction": "+28.6% (3.5 → 4.5)"
}
```

### Key Learnings

1. **Continuous evaluation enables rapid iteration**
   - Weekly evaluations caught regressions early
   - Monthly deep dives identified systemic issues

2. **Incremental improvements compound**
   - Each improvement cycle added 5-7% gains
   - Total improvement over 3 months: 13-35%

3. **User feedback is critical**
   - User satisfaction tracked alongside technical metrics
   - User feedback guided prioritization

4. **Automation reduces overhead**
   - Automated weekly evaluations took <5 minutes
   - Enabled frequent iteration without manual effort

### Continuous Evaluation Pipeline

```python
class ContinuousEvaluator:
    def __init__(self, agent, test_dataset):
        self.agent = agent
        self.test_dataset = test_dataset
        self.history = []
    
    def weekly_evaluation(self):
        """Quick evaluation on subset of test cases."""
        sample = random.sample(self.test_dataset, 50)
        results = self.run_evaluation(sample)
        
        # Check for regressions
        if self.detect_regression(results):
            self.send_alert("Performance regression detected")
        
        self.history.append(results)
        return results
    
    def monthly_evaluation(self):
        """Full evaluation on complete test dataset."""
        results = self.run_evaluation(self.test_dataset)
        
        # Generate detailed report
        report = self.generate_report(results)
        
        # Identify improvement opportunities
        recommendations = self.generate_recommendations(results)
        
        self.history.append(results)
        return report, recommendations
    
    def detect_regression(self, current_results):
        """Detect if performance degraded."""
        if len(self.history) < 1:
            return False
        
        previous = self.history[-1]
        
        # Check if accuracy dropped >5%
        if current_results["accuracy"] < previous["accuracy"] * 0.95:
            return True
        
        return False
```

---

## Summary

These examples demonstrate:

1. **Success Metrics Evaluation:** Measuring quantitative performance
2. **Quality Assessment:** Evaluating qualitative output quality
3. **A/B Testing:** Comparing agent configurations
4. **Continuous Improvement:** Iterative evaluation and optimization

### Common Success Factors

- Clear evaluation criteria aligned with business goals
- Representative test datasets
- Both quantitative and qualitative assessment
- Actionable recommendations
- Continuous evaluation for ongoing improvement
