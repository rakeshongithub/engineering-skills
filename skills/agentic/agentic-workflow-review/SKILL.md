# agentic-workflow-review

**Purpose:** Review and improve agentic workflows to optimize performance, enhance reliability, improve maintainability, and ensure alignment with best practices.

---

## When to Use

Use this skill when:
- Agent workflows are underperforming (high latency, low throughput, high error rates)
- Reliability issues occur frequently (failures, timeouts, data loss)
- Workflows are difficult to maintain or extend (complex, poorly documented, tightly coupled)
- You need to conduct post-incident reviews to prevent recurrence
- Preparing for production deployment and want to validate workflow quality
- Implementing continuous improvement processes for agent systems
- Onboarding new team members and want to ensure workflow quality standards
- Scaling workflows to handle increased load or complexity
- Migrating workflows to new infrastructure or platforms
- Compliance or audit requirements mandate workflow reviews

---

## When NOT to Use

Do NOT use this skill when:
- Workflows are still in initial design phase (use agent-workflow-design instead)
- You need to implement observability first (use agent-observability instead)
- You need to evaluate agent output quality (use agent-evaluation instead)
- Workflows are functioning well with no performance, reliability, or maintainability issues
- You lack performance data or observability to inform the review
- The workflow is a proof-of-concept with no production deployment plans
- You need to design new workflows from scratch (not review existing ones)

---

## Inputs

| Input | Description | Required | Format |
|-------|-------------|----------|--------|
| workflow_design | Complete workflow design including tasks, agents, handoffs, and dependencies | Yes | Workflow diagram, task definitions, agent specifications |
| performance_data | Historical performance metrics (latency, throughput, error rates, resource usage) | Yes | Time-series data from observability platform |
| incident_reports | Documentation of past failures, outages, and issues | No | Incident reports, postmortems, bug reports |
| business_requirements | Current and future business needs (SLAs, scale, features) | Yes | Requirements document with SLAs, growth projections |
| codebase | Agent implementation code and configuration | Yes | Source code, configuration files, infrastructure-as-code |
| observability_data | Logs, metrics, traces from production or staging | Yes | Log files, metric dashboards, trace data |

---

## Expected Outputs

| Output | Description | Format |
|--------|-------------|--------|
| review_report | Comprehensive assessment of workflow quality with findings and recommendations | Document with executive summary, detailed findings, prioritized recommendations |
| optimization_recommendations | Specific, actionable recommendations for performance improvements | List of recommendations with expected impact and implementation effort |
| reliability_improvements | Recommendations for enhancing workflow reliability | List of improvements with failure modes addressed |
| maintainability_enhancements | Recommendations for improving code quality, documentation, and modularity | List of enhancements with maintainability benefits |
| improvement_plan | Prioritized roadmap for implementing recommendations | Project plan with tasks, timelines, owners, dependencies |
| best_practices_checklist | Checklist of best practices with current compliance status | Checklist with pass/fail status for each best practice |

---

## Workflow

### Step 1: Gather Workflow Context

**Objective:** Collect all necessary information about the workflow.

**Actions:**
1. Review workflow design documentation (tasks, agents, handoffs, dependencies)
2. Review agent implementation code and configuration
3. Collect performance data from observability platform (last 30 days)
4. Review incident reports and postmortems
5. Interview stakeholders (developers, operators, business owners)
6. Understand business requirements and SLAs

**Decision Points:**
- Is the workflow documentation complete and up-to-date?
- Is performance data available and representative?
- Are there known issues or pain points?
- What are the business priorities (performance, reliability, cost)?

**Outputs:**
- Workflow design documentation
- Performance metrics (latency, throughput, error rates)
- Incident history
- Business requirements and SLAs
- Stakeholder feedback

**Quality Checks:**
- [ ] Workflow documentation is complete
- [ ] Performance data covers at least 30 days
- [ ] All major incidents are documented
- [ ] Business requirements are clear
- [ ] Stakeholders have been consulted

---

### Step 2: Analyze Workflow Performance

**Objective:** Assess current performance against SLAs and identify bottlenecks.

**Actions:**
1. Calculate key performance metrics:
   - **Latency:** p50, p95, p99 task duration
   - **Throughput:** Tasks completed per hour/day
   - **Error Rate:** % of failed tasks
   - **Resource Usage:** CPU, memory, API calls, cost per task

2. Compare against SLAs:
   - Latency SLA compliance %
   - Throughput SLA compliance %
   - Error rate SLA compliance %

3. Identify performance bottlenecks:
   - Which agents/tasks are slowest?
   - Which handoffs have highest latency?
   - Which external dependencies are slow?

4. Analyze performance trends:
   - Is performance degrading over time?
   - Are there performance spikes or anomalies?
   - How does performance vary by time of day or load?

**Performance Analysis Framework:**

**Latency Analysis:**
- Identify tasks with p95 latency > SLA
- Use distributed tracing to identify slow spans
- Analyze external API call latencies
- Check for sequential operations that could be parallelized

**Throughput Analysis:**
- Calculate current throughput vs. target
- Identify throughput bottlenecks (agent capacity, external API rate limits)
- Analyze parallelization opportunities

**Error Rate Analysis:**
- Categorize errors by type (transient, permanent, configuration)
- Identify error patterns (time-based, load-based)
- Analyze error recovery mechanisms

**Outputs:**
- Performance metrics summary
- SLA compliance report
- Bottleneck analysis
- Performance trends

**Quality Checks:**
- [ ] All key metrics are calculated
- [ ] SLA compliance is measured
- [ ] Bottlenecks are identified with data
- [ ] Trends are analyzed

---

### Step 3: Assess Workflow Reliability

**Objective:** Evaluate reliability and identify failure modes.

**Actions:**
1. Calculate reliability metrics:
   - **Availability:** % uptime
   - **Success Rate:** % of workflows completed successfully
   - **MTBF (Mean Time Between Failures):** Average time between failures
   - **MTTR (Mean Time to Recover):** Average time to recover from failures

2. Analyze failure modes:
   - What are the most common failure types?
   - What are the most impactful failures (user-facing, data loss)?
   - Are failures transient or permanent?
   - Are failures handled gracefully?

3. Review error handling:
   - Are all failure modes handled?
   - Are retry mechanisms in place?
   - Are fallback strategies implemented?
   - Are errors logged and monitored?

4. Assess resilience:
   - How does the workflow handle external service failures?
   - Are there single points of failure?
   - Is the workflow fault-tolerant?
   - Are there circuit breakers or rate limiters?

**Reliability Assessment Framework:**

**Failure Mode Analysis:**
```
For each failure mode:
1. Frequency: How often does it occur?
2. Impact: What is the user/business impact?
3. Detection: How quickly is it detected?
4. Recovery: How quickly is it recovered?
5. Prevention: Can it be prevented?
```

**Error Handling Assessment:**
- [ ] All external API calls have timeout and retry logic
- [ ] All agent tasks have error handling
- [ ] All handoffs have error recovery
- [ ] All errors are logged and monitored
- [ ] Fallback strategies exist for critical paths

**Outputs:**
- Reliability metrics summary
- Failure mode analysis
- Error handling assessment
- Resilience assessment

**Quality Checks:**
- [ ] All reliability metrics are calculated
- [ ] All failure modes are documented
- [ ] Error handling is assessed
- [ ] Resilience gaps are identified

---

### Step 4: Evaluate Workflow Maintainability

**Objective:** Assess code quality, documentation, and ease of modification.

**Actions:**
1. Review code quality:
   - **Modularity:** Are agents and tasks well-separated?
   - **Readability:** Is code easy to understand?
   - **Testability:** Are agents and tasks testable?
   - **Reusability:** Are common patterns extracted into reusable components?

2. Review documentation:
   - Is workflow design documented?
   - Are agent responsibilities documented?
   - Are handoff protocols documented?
   - Are operational runbooks available?

3. Assess configuration management:
   - Is configuration separated from code?
   - Is configuration versioned?
   - Is configuration validated?

4. Evaluate extensibility:
   - How easy is it to add new agents or tasks?
   - How easy is it to modify existing agents?
   - Are there tight couplings that hinder changes?

**Maintainability Assessment Framework:**

**Code Quality Checklist:**
- [ ] Agents follow single responsibility principle
- [ ] Code is DRY (no duplication)
- [ ] Functions/methods are small and focused
- [ ] Variable/function names are descriptive
- [ ] Code has appropriate comments
- [ ] Code follows language/framework conventions

**Documentation Checklist:**
- [ ] Workflow design is documented
- [ ] Agent responsibilities are documented
- [ ] Handoff protocols are documented
- [ ] Configuration is documented
- [ ] Operational runbooks exist
- [ ] Troubleshooting guides exist

**Outputs:**
- Code quality assessment
- Documentation assessment
- Configuration management assessment
- Extensibility assessment

**Quality Checks:**
- [ ] Code quality is assessed
- [ ] Documentation completeness is evaluated
- [ ] Configuration management is reviewed
- [ ] Extensibility is evaluated

---

### Step 5: Identify Best Practices Gaps

**Objective:** Compare workflow against industry best practices.

**Actions:**
1. Review against agentic workflow best practices:
   - **Task Decomposition:** Are tasks appropriately sized?
   - **Workflow Patterns:** Are appropriate patterns used (sequential, parallel, pipeline)?
   - **Context Engineering:** Is context well-structured and sufficient?
   - **Instruction Design:** Are instructions clear and executable?
   - **Tool Selection:** Are tools appropriate and safe?
   - **Handoff Design:** Are handoffs efficient and reliable?
   - **Guardrails:** Are safety and quality guardrails in place?
   - **Observability:** Is observability comprehensive?
   - **Evaluation:** Is agent performance evaluated?

2. Review against general software engineering best practices:
   - **Version Control:** Is all code in version control?
   - **CI/CD:** Is deployment automated?
   - **Testing:** Are there unit, integration, and end-to-end tests?
   - **Code Review:** Is code reviewed before deployment?
   - **Security:** Are security best practices followed?

**Best Practices Checklist:**

**Agentic Workflow Best Practices:**
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

**Software Engineering Best Practices:**
- [ ] All code is in version control (Git)
- [ ] CI/CD pipeline automates deployment
- [ ] Unit tests cover critical logic
- [ ] Integration tests validate agent interactions
- [ ] End-to-end tests validate complete workflows
- [ ] Code is reviewed before merging
- [ ] Security scanning is automated
- [ ] Dependencies are kept up-to-date

**Outputs:**
- Best practices compliance report
- List of gaps and non-compliance items

**Quality Checks:**
- [ ] All best practices are evaluated
- [ ] Gaps are documented
- [ ] Compliance % is calculated

---

### Step 6: Generate Optimization Recommendations

**Objective:** Create specific, actionable recommendations for performance improvements.

**Actions:**
1. Identify optimization opportunities:
   - **Parallelization:** Can sequential tasks be parallelized?
   - **Caching:** Can results be cached to avoid redundant work?
   - **Batching:** Can multiple requests be batched?
   - **Lazy Loading:** Can data loading be deferred?
   - **Algorithm Optimization:** Can algorithms be improved?

2. Prioritize recommendations by impact:
   - **High Impact:** Expected improvement > 50% (latency, throughput, cost)
   - **Medium Impact:** Expected improvement 20-50%
   - **Low Impact:** Expected improvement < 20%

3. Estimate implementation effort:
   - **Low Effort:** < 1 day
   - **Medium Effort:** 1-5 days
   - **High Effort:** > 5 days

4. Create recommendation matrix:

| Recommendation | Expected Impact | Effort | Priority |
|----------------|-----------------|--------|----------|
| Parallelize code analysis and security scan | 40% latency reduction | Low | High |
| Cache external API results | 30% latency reduction | Medium | High |
| Batch handoffs | 20% latency reduction | Low | Medium |
| Optimize algorithm X | 15% latency reduction | High | Low |

**Outputs:**
- List of optimization recommendations
- Impact and effort estimates
- Prioritized recommendation matrix

**Quality Checks:**
- [ ] Recommendations are specific and actionable
- [ ] Impact is estimated with data
- [ ] Effort is estimated
- [ ] Recommendations are prioritized

---

### Step 7: Generate Reliability Recommendations

**Objective:** Create recommendations for enhancing workflow reliability.

**Actions:**
1. Identify reliability improvements:
   - **Error Handling:** Add retry logic, fallbacks, circuit breakers
   - **Timeout Management:** Add timeouts to prevent hanging
   - **Idempotency:** Make operations idempotent to support retries
   - **Data Validation:** Add input/output validation
   - **Graceful Degradation:** Implement fallback strategies

2. Address failure modes:
   - For each failure mode, recommend prevention or mitigation
   - Prioritize by frequency and impact

3. Enhance monitoring and alerting:
   - Add missing metrics or logs
   - Improve alert thresholds
   - Add runbooks for common failures

**Reliability Improvement Template:**

```
Failure Mode: External API timeout
Frequency: 5% of requests
Impact: Workflow fails, user sees error
Current Handling: None (request hangs for 30s then fails)

Recommendations:
1. Add 5-second timeout to API calls
2. Implement retry logic (3 retries with exponential backoff)
3. Add circuit breaker (open after 5 consecutive failures)
4. Implement fallback to cached results
5. Add alert for circuit breaker open

Expected Impact: Reduce failure rate from 5% to 0.5%
Effort: Medium (2 days)
Priority: High
```

**Outputs:**
- List of reliability recommendations
- Failure mode mitigation strategies
- Monitoring and alerting improvements

**Quality Checks:**
- [ ] All major failure modes have recommendations
- [ ] Recommendations address root causes
- [ ] Impact and effort are estimated
- [ ] Recommendations are prioritized

---

### Step 8: Generate Maintainability Recommendations

**Objective:** Create recommendations for improving code quality and maintainability.

**Actions:**
1. Identify code quality improvements:
   - **Refactoring:** Extract duplicated code, simplify complex logic
   - **Modularization:** Break large agents into smaller, focused agents
   - **Naming:** Improve variable/function names for clarity
   - **Documentation:** Add missing documentation

2. Improve testability:
   - Add unit tests for critical logic
   - Add integration tests for agent interactions
   - Add end-to-end tests for workflows

3. Enhance configuration management:
   - Extract hardcoded values to configuration
   - Implement configuration validation
   - Version configuration files

4. Improve extensibility:
   - Reduce tight coupling between agents
   - Use dependency injection
   - Define clear interfaces

**Maintainability Improvement Template:**

```
Issue: Code duplication in 3 agents (same validation logic)
Impact: Changes require updating 3 places, risk of inconsistency

Recommendation: Extract validation logic into shared utility
Benefits:
- Single source of truth
- Easier to maintain and test
- Reduced risk of inconsistency

Effort: Low (1 day)
Priority: Medium
```

**Outputs:**
- List of maintainability recommendations
- Code quality improvements
- Testing improvements
- Configuration management improvements

**Quality Checks:**
- [ ] Code quality issues are identified
- [ ] Testing gaps are identified
- [ ] Configuration issues are identified
- [ ] Recommendations are actionable

---

### Step 9: Create Improvement Plan

**Objective:** Prioritize and schedule recommendations for implementation.

**Actions:**
1. Consolidate all recommendations (performance, reliability, maintainability)
2. Prioritize by:
   - **Business Impact:** Alignment with business priorities
   - **Technical Impact:** Expected improvement in metrics
   - **Effort:** Implementation complexity and time
   - **Risk:** Risk of implementation (low risk = higher priority)

3. Create implementation roadmap:
   - **Phase 1 (Quick Wins):** High impact, low effort (weeks 1-2)
   - **Phase 2 (Major Improvements):** High impact, medium effort (weeks 3-6)
   - **Phase 3 (Long-Term):** Medium impact, high effort (weeks 7-12)

4. Assign owners and dependencies:
   - Who will implement each recommendation?
   - What are the dependencies between recommendations?

5. Define success metrics:
   - How will you measure success?
   - What are the target metrics after implementation?

**Improvement Plan Template:**

```
Phase 1: Quick Wins (Weeks 1-2)
- Parallelize code analysis and security scan (Owner: Alice, Impact: 40% latency reduction)
- Add circuit breaker to external API (Owner: Bob, Impact: 5% → 0.5% error rate)
- Extract duplicated validation logic (Owner: Charlie, Impact: Improved maintainability)

Phase 2: Major Improvements (Weeks 3-6)
- Implement caching for external API results (Owner: Alice, Impact: 30% latency reduction)
- Add comprehensive error handling and retries (Owner: Bob, Impact: Improved reliability)
- Add integration and end-to-end tests (Owner: Charlie, Impact: Improved quality)

Phase 3: Long-Term (Weeks 7-12)
- Refactor large agents into smaller, focused agents (Owner: Team, Impact: Improved maintainability)
- Implement advanced observability (distributed tracing) (Owner: Alice, Impact: Improved debugging)
- Optimize algorithm X (Owner: Bob, Impact: 15% latency reduction)

Success Metrics:
- p95 latency: Reduce from 5s to 2s (60% improvement)
- Error rate: Reduce from 5% to 0.5% (90% improvement)
- Test coverage: Increase from 40% to 80%
- Code quality score: Increase from 6/10 to 8/10
```

**Outputs:**
- Prioritized improvement plan
- Implementation roadmap with phases
- Owner assignments
- Success metrics

**Quality Checks:**
- [ ] All recommendations are included
- [ ] Recommendations are prioritized
- [ ] Roadmap is realistic
- [ ] Owners are assigned
- [ ] Success metrics are defined

---

### Step 10: Document and Present Review

**Objective:** Create comprehensive review report and present findings.

**Actions:**
1. Create review report with:
   - **Executive Summary:** Key findings and recommendations (1 page)
   - **Current State Assessment:** Performance, reliability, maintainability (2-3 pages)
   - **Detailed Findings:** Bottlenecks, failure modes, code quality issues (5-10 pages)
   - **Recommendations:** Prioritized list with impact and effort (3-5 pages)
   - **Improvement Plan:** Roadmap with phases, owners, success metrics (2-3 pages)
   - **Appendices:** Detailed data, charts, code samples

2. Prepare presentation for stakeholders:
   - Tailor to audience (executives, developers, operators)
   - Focus on business impact and ROI
   - Include visualizations (charts, diagrams)

3. Conduct review meeting:
   - Present findings and recommendations
   - Discuss priorities and trade-offs
   - Get buy-in for improvement plan
   - Assign action items

4. Follow up:
   - Track implementation progress
   - Measure success metrics
   - Conduct follow-up reviews

**Review Report Template:**

```markdown
# Agentic Workflow Review Report

## Executive Summary

**Workflow:** Code Review Workflow  
**Review Date:** 2026-09-08  
**Reviewer:** [Name]

**Key Findings:**
- Performance: p95 latency 5s (SLA: 2s) - **Not Meeting SLA**
- Reliability: 5% error rate (SLA: 1%) - **Not Meeting SLA**
- Maintainability: Code quality 6/10, test coverage 40% - **Needs Improvement**

**Top Recommendations:**
1. Parallelize code analysis and security scan (40% latency reduction, Low effort)
2. Add circuit breaker to external API (90% error reduction, Medium effort)
3. Implement caching for API results (30% latency reduction, Medium effort)

**Expected Impact:**
- p95 latency: 5s → 2s (60% improvement)
- Error rate: 5% → 0.5% (90% improvement)
- Test coverage: 40% → 80%

**Investment:** 6 weeks, 3 engineers

## Current State Assessment
[Detailed performance, reliability, maintainability analysis]

## Detailed Findings
[Bottlenecks, failure modes, code quality issues]

## Recommendations
[Prioritized list with impact and effort]

## Improvement Plan
[Roadmap with phases, owners, success metrics]
```

**Outputs:**
- Comprehensive review report
- Stakeholder presentation
- Meeting notes and action items

**Quality Checks:**
- [ ] Report is comprehensive and clear
- [ ] Presentation is tailored to audience
- [ ] Stakeholders are aligned on priorities
- [ ] Action items are assigned

---

## Decision Framework

### Prioritization Matrix

**Use this matrix to prioritize recommendations:**

| Impact \ Effort | Low Effort | Medium Effort | High Effort |
|-----------------|------------|---------------|-------------|
| **High Impact** | **DO FIRST** (Quick wins) | **DO NEXT** (Major improvements) | **PLAN CAREFULLY** (Strategic initiatives) |
| **Medium Impact** | **DO SOON** (Easy wins) | **EVALUATE** (Consider ROI) | **DEFER** (Low priority) |
| **Low Impact** | **MAYBE** (If time allows) | **DEFER** (Low priority) | **SKIP** (Not worth it) |

---

### Performance vs. Reliability Trade-off

**When to prioritize performance:**
- Current performance is significantly below SLA
- Performance directly impacts user experience
- Performance issues are causing business impact (lost revenue, user churn)

**When to prioritize reliability:**
- Error rates are high or increasing
- Failures are causing data loss or corruption
- Failures are causing user-facing outages
- Reliability issues are more frequent than performance issues

**Balanced Approach:**
- Address critical reliability issues first (prevent data loss, outages)
- Then optimize performance (improve user experience)
- Continuously improve both (reliability and performance are not mutually exclusive)

---

## Quality Checklist

Before considering workflow review complete, verify:

### Data Collection
- [ ] Performance data covers at least 30 days
- [ ] All major incidents are documented
- [ ] Business requirements are clear
- [ ] Stakeholders have been consulted

### Analysis
- [ ] Performance metrics are calculated and compared to SLAs
- [ ] Bottlenecks are identified with data
- [ ] Failure modes are documented and analyzed
- [ ] Code quality is assessed
- [ ] Best practices compliance is evaluated

### Recommendations
- [ ] Recommendations are specific and actionable
- [ ] Impact and effort are estimated
- [ ] Recommendations are prioritized
- [ ] Recommendations address root causes

### Improvement Plan
- [ ] All recommendations are included in plan
- [ ] Plan is phased and realistic
- [ ] Owners are assigned
- [ ] Success metrics are defined
- [ ] Dependencies are identified

### Documentation
- [ ] Review report is comprehensive and clear
- [ ] Presentation is prepared
- [ ] Stakeholders are aligned
- [ ] Action items are assigned

---

## Common Mistakes

### 1. Reviewing Without Data

**Mistake:** Conducting review based on intuition or anecdotes without performance data.

**Why It's a Problem:**
- Recommendations may not address actual bottlenecks
- Cannot measure success objectively
- Wastes time on non-issues

**How to Avoid:**
- Require at least 30 days of performance data
- Use observability tools to collect metrics
- Base recommendations on data, not opinions

---

### 2. Focusing Only on Performance

**Mistake:** Optimizing for performance while ignoring reliability and maintainability.

**Why It's a Problem:**
- Fast but unreliable workflows frustrate users
- Performance optimizations may reduce code quality
- Technical debt accumulates

**How to Avoid:**
- Assess performance, reliability, AND maintainability
- Balance optimization across all three dimensions
- Consider long-term sustainability, not just short-term gains

---

### 3. Recommending Without Prioritization

**Mistake:** Providing a long list of recommendations without prioritization.

**Why It's a Problem:**
- Team doesn't know where to start
- High-impact items may be delayed
- Low-impact items may waste time

**How to Avoid:**
- Prioritize by impact and effort
- Create phased implementation plan
- Focus on quick wins first

---

### 4. Ignoring Stakeholder Input

**Mistake:** Conducting review in isolation without consulting stakeholders.

**Why It's a Problem:**
- May miss important context or constraints
- Recommendations may not align with business priorities
- Lack of buy-in for implementation

**How to Avoid:**
- Interview stakeholders early in review
- Align recommendations with business priorities
- Present findings and get feedback

---

### 5. No Follow-Up

**Mistake:** Delivering review report and moving on without tracking implementation.

**Why It's a Problem:**
- Recommendations may not be implemented
- Cannot measure success
- Review becomes a "shelf-ware" document

**How to Avoid:**
- Assign owners and deadlines
- Track implementation progress
- Measure success metrics
- Conduct follow-up reviews

---

## Examples

See [examples.md](./examples.md) for detailed examples:
1. **Performance Optimization** - Reducing latency and increasing throughput
2. **Reliability Improvements** - Adding error handling and retry logic
3. **Maintainability Enhancements** - Improving code quality and documentation
4. **Best Practices Application** - Applying design patterns and avoiding anti-patterns

---

## Related Skills

### Required Before This Skill
- **agent-workflow-design**: Need workflow design to review
- **agent-evaluation**: Need performance data to inform review
- **agent-observability**: Need observability data to analyze performance

### Commonly Followed By
- **agent-workflow-design**: Redesign workflow based on review findings
- **agent-guardrails**: Add guardrails based on reliability findings
- **agent-observability**: Enhance observability based on review findings

### Works Well With
- **agent-task-decomposition**: Re-decompose tasks based on performance findings
- **agent-handoff-design**: Optimize handoffs based on latency findings
- **agent-context-engineering**: Improve context based on maintainability findings

---

## Skill Composition

### Pattern: Continuous Improvement Cycle

```
agent-workflow-design
    ↓
Implementation
    ↓
agent-observability
    ↓
agent-evaluation
    ↓
agentic-workflow-review (this skill)
    ↓
Optimization
    ↓
[Repeat]
```

**Use this pattern when:** Implementing continuous improvement for agent workflows.

---

### Pattern: Pre-Production Review

```
agent-workflow-design
    ↓
Implementation
    ↓
agentic-workflow-review (this skill)
    ↓
Optimization
    ↓
Production Deployment
```

**Use this pattern when:** Validating workflow quality before production deployment.

---

## Evaluation Criteria

### Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Review Completeness | 100% | % of workflow aspects reviewed (performance, reliability, maintainability) |
| Recommendation Quality | > 80% | % of recommendations implemented |
| Performance Improvement | > 30% | % improvement in key metrics (latency, throughput, error rate) |
| Reliability Improvement | > 50% | % reduction in error rate or MTTR |
| Maintainability Improvement | > 20% | % improvement in code quality score or test coverage |

### Quality Indicators

**High-Quality Review:**
- Comprehensive data collection (30+ days of metrics)
- Detailed analysis of performance, reliability, maintainability
- Specific, actionable recommendations with impact estimates
- Prioritized implementation plan with owners and timelines
- Stakeholder alignment and buy-in
- Follow-up tracking and measurement

**Low-Quality Review:**
- Limited or no performance data
- Superficial analysis
- Vague or generic recommendations
- No prioritization or implementation plan
- No stakeholder involvement
- No follow-up

---

## Tags

workflow-review, optimization, performance, reliability, maintainability, code-quality, best-practices, continuous-improvement, post-incident-review, technical-debt, refactoring, monitoring, evaluation

---

## Metadata

- **Complexity:** Advanced
- **Estimated Time:** 2-3 hours
- **Last Updated:** 2026-09-08
- **Version:** 1.0.0
