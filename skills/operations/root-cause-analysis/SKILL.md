# Root Cause Analysis

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 3-8 hours

---

## Purpose

Perform deep, systematic analysis to identify the fundamental causes of problems, going beyond immediate symptoms to understand underlying systemic issues.

---

## When to Use

- After complex or recurring incidents
- When incident-analysis doesn't reveal clear root cause
- When investigating systemic problems
- When multiple related incidents occur
- When simple fixes don't prevent recurrence
- When building organizational learning from failures
- When analyzing chronic performance issues
- When investigating cascading failures

---

## When NOT to Use

- **For simple, one-time incidents** — use incident-analysis instead
- **During active incident response** — focus on resolution first
- **Without sufficient data** — need comprehensive logs and metrics
- **For blame assignment** — focus on system improvement
- **Without time for deep analysis** — this is a thorough, time-intensive process
- **For hypothetical problems** — use failure-mode-analysis instead
- **Without stakeholder buy-in** — requires organizational commitment

---

## Inputs

### Required

- **Problem statement** — clear description of the issue
- **Incident data** — logs, metrics, traces from incidents
- **Timeline of events** — detailed sequence of what happened
- **System architecture** — understanding of system structure
- **Historical context** — previous related incidents
- **Stakeholder input** — perspectives from different teams

### Optional

- **Code repository** — access to source code
- **Configuration history** — changes over time
- **Deployment history** — what was deployed when
- **Organizational context** — team structure, processes
- **External factors** — dependencies, market conditions
- **Performance baselines** — normal vs. abnormal behavior
- **User feedback** — how users experienced the problem

---

## Expected Outputs

### Primary Deliverables

1. **Root Cause Analysis Report**
   - Problem statement
   - Analysis methodology
   - Causal chain analysis
   - Root cause identification
   - Contributing factors
   - Systemic issues
   - Recommendations

2. **Causal Chain Diagram**
   - Visual representation of cause-effect relationships
   - Multiple levels of causation
   - Interconnections between factors

3. **Systemic Recommendations**
   - Process improvements
   - Technical improvements
   - Organizational improvements
   - Long-term strategic changes

4. **Prevention Strategy**
   - How to prevent this specific problem
   - How to prevent similar problems
   - How to detect problems earlier
   - How to respond more effectively

### Supporting Artifacts

- **Fishbone (Ishikawa) diagram** — categorized causes
- **5 Whys analysis** — drilling down to root causes
- **Fault tree analysis** — logical analysis of failure paths
- **Timeline analysis** — events leading to problem
- **Pattern analysis** — similarities with other incidents
- **Risk assessment** — likelihood and impact of recurrence

---

## Workflow

### Step 1: Define the Problem

**Objective:** Create a clear, specific problem statement.

**Actions:**
- State the problem in specific, measurable terms
- Define the scope (what's included, what's not)
- Identify when the problem occurs
- Quantify the impact
- Distinguish problem from symptoms
- Get stakeholder agreement on problem definition
- Document assumptions and constraints

**Quality Check:**
- [ ] Problem is stated clearly and specifically
- [ ] Scope is well-defined
- [ ] Impact is quantified
- [ ] Problem is distinguished from symptoms
- [ ] Stakeholders agree on problem definition

### Step 2: Gather Comprehensive Data

**Objective:** Collect all relevant information about the problem.

**Actions:**
- Collect incident data (logs, metrics, traces)
- Gather timeline of events
- Interview stakeholders and incident responders
- Review system architecture and design
- Examine code and configuration
- Analyze deployment and change history
- Review previous related incidents
- Collect user feedback and impact data

**Quality Check:**
- [ ] Data from all relevant sources collected
- [ ] Multiple perspectives gathered
- [ ] Historical context understood
- [ ] Technical and organizational factors considered
- [ ] Data quality is sufficient for analysis

### Step 3: Build Timeline and Sequence

**Objective:** Understand the sequence of events leading to the problem.

**Actions:**
- Create detailed timeline of events
- Identify trigger events
- Map cause-effect relationships
- Identify decision points
- Note what was known at each point
- Identify missed opportunities for detection
- Correlate events across systems
- Identify patterns or anomalies

**Quality Check:**
- [ ] Timeline is complete and accurate
- [ ] Cause-effect relationships are identified
- [ ] Decision points are documented
- [ ] Patterns are recognized
- [ ] Gaps in knowledge are noted

### Step 4: Apply "5 Whys" Analysis

**Objective:** Drill down from symptoms to root causes.

**Actions:**
- Start with the problem statement
- Ask "why did this happen?" for each answer
- Continue asking "why" until reaching fundamental cause
- Explore multiple causal chains
- Verify each "why" with evidence
- Identify where chains converge
- Distinguish root causes from contributing factors
- Document all causal chains

**Quality Check:**
- [ ] Each "why" is supported by evidence
- [ ] Analysis reaches fundamental causes
- [ ] Multiple causal chains explored
- [ ] Root causes are actionable
- [ ] Contributing factors are identified

### Step 5: Create Fishbone Diagram

**Objective:** Categorize and visualize all contributing factors.

**Actions:**
- Identify major categories (People, Process, Technology, Environment)
- Brainstorm factors in each category
- Organize factors hierarchically
- Identify relationships between factors
- Visualize in fishbone diagram
- Validate with stakeholders
- Identify most significant factors
- Document insights from categorization

**Quality Check:**
- [ ] All major categories covered
- [ ] Factors are comprehensive
- [ ] Relationships are identified
- [ ] Diagram is clear and understandable
- [ ] Stakeholders validate completeness

### Step 6: Perform Fault Tree Analysis

**Objective:** Analyze logical combinations of failures.

**Actions:**
- Identify the top event (the problem)
- Identify immediate causes (AND/OR logic)
- Break down each cause into sub-causes
- Continue until reaching basic events
- Identify critical paths
- Calculate probabilities if data available
- Identify single points of failure
- Identify common cause failures

**Quality Check:**
- [ ] Fault tree is logically complete
- [ ] AND/OR logic is correct
- [ ] Critical paths are identified
- [ ] Single points of failure are noted
- [ ] Analysis is thorough

### Step 7: Identify Systemic Issues

**Objective:** Understand broader organizational and technical issues.

**Actions:**
- Look for patterns across multiple incidents
- Identify process gaps or weaknesses
- Identify organizational factors
- Identify technical debt or design issues
- Identify cultural factors
- Identify resource constraints
- Identify knowledge gaps
- Identify communication issues

**Quality Check:**
- [ ] Systemic issues are identified
- [ ] Patterns are recognized
- [ ] Organizational factors are considered
- [ ] Technical and cultural issues are balanced
- [ ] Issues are specific and actionable

### Step 8: Determine Root Causes

**Objective:** Identify the fundamental causes that, if addressed, would prevent the problem.

**Actions:**
- Review all analysis (5 Whys, Fishbone, Fault Tree)
- Identify causes that appear in multiple analyses
- Distinguish root causes from contributing factors
- Prioritize root causes by impact
- Verify root causes with evidence
- Test root causes (would fixing them prevent the problem?)
- Get stakeholder validation
- Document rationale for each root cause

**Quality Check:**
- [ ] Root causes are fundamental, not symptoms
- [ ] Root causes are supported by evidence
- [ ] Root causes are actionable
- [ ] Stakeholders agree on root causes
- [ ] Rationale is clearly documented

### Step 9: Develop Recommendations

**Objective:** Create specific, actionable recommendations.

**Actions:**
- Develop recommendations for each root cause
- Categorize by timeframe (immediate, short-term, long-term)
- Categorize by type (technical, process, organizational)
- Estimate effort and impact for each
- Identify dependencies between recommendations
- Prioritize recommendations
- Assign owners
- Define success criteria

**Quality Check:**
- [ ] Recommendations address root causes
- [ ] Recommendations are specific and actionable
- [ ] Effort and impact are estimated
- [ ] Recommendations are prioritized
- [ ] Owners are assigned
- [ ] Success criteria are defined

### Step 10: Create RCA Report and Present

**Objective:** Document and communicate findings.

**Actions:**
- Write comprehensive RCA report
- Include executive summary
- Document methodology
- Present analysis (5 Whys, Fishbone, Fault Tree)
- Clearly state root causes
- Present recommendations with priorities
- Create presentation for stakeholders
- Conduct review meeting
- Get feedback and buy-in
- Publish and share widely

**Quality Check:**
- [ ] Report is comprehensive and clear
- [ ] Executive summary is concise
- [ ] Analysis is well-documented
- [ ] Recommendations are actionable
- [ ] Stakeholders understand and support findings
- [ ] Report is published and accessible

---

## Decision Framework

### When to Use Different RCA Techniques

**5 Whys:**
- Simple, linear causal chains
- Quick analysis needed
- Problem has clear sequence of events
- Good for initial exploration

**Fishbone Diagram:**
- Multiple contributing factors
- Need to categorize causes
- Brainstorming with team
- Comprehensive factor identification

**Fault Tree Analysis:**
- Complex technical systems
- Need logical analysis
- Multiple failure paths
- Quantitative analysis needed

**Timeline Analysis:**
- Sequence of events is important
- Multiple systems involved
- Decision points are critical
- Understanding what was known when

**Causal Chain Analysis:**
- Multiple interconnected causes
- Need to show relationships
- Complex systemic issues
- Visual communication needed

### Root Cause vs. Contributing Factor

**Root Cause:**
- Fundamental cause
- If fixed, would prevent the problem
- Actionable and within control
- Systemic, not one-time

**Contributing Factor:**
- Made problem more likely or severe
- Fixing it reduces risk but doesn't eliminate it
- May be outside direct control
- May be situational

### Prioritizing Recommendations

**P0 (Critical):**
- Addresses primary root cause
- Prevents recurrence of severe incidents
- High impact, achievable effort
- Implement within 1-2 weeks

**P1 (High):**
- Addresses major contributing factors
- Significantly reduces risk
- Moderate effort, high impact
- Implement within 1-3 months

**P2 (Medium):**
- Addresses systemic issues
- Incremental improvement
- Moderate effort and impact
- Implement within 3-6 months

**P3 (Low):**
- Nice to have improvements
- Low impact or high effort
- Implement when capacity allows

---

## Quality Checklist

### Problem Definition
- [ ] Problem is clearly and specifically stated
- [ ] Scope is well-defined
- [ ] Impact is quantified
- [ ] Problem is distinguished from symptoms
- [ ] Stakeholders agree on problem definition

### Data Collection
- [ ] Comprehensive data from all relevant sources
- [ ] Multiple perspectives gathered
- [ ] Historical context understood
- [ ] Technical and organizational factors considered
- [ ] Data quality is sufficient

### Analysis Quality
- [ ] Multiple analysis techniques used
- [ ] Causal chains are complete and logical
- [ ] Root causes are fundamental, not symptoms
- [ ] Root causes are supported by evidence
- [ ] Systemic issues are identified
- [ ] Analysis is thorough and rigorous

### Root Cause Identification
- [ ] Root causes are clearly identified
- [ ] Root causes are actionable
- [ ] Contributing factors are distinguished
- [ ] Stakeholders validate root causes
- [ ] Rationale is well-documented

### Recommendations
- [ ] Recommendations address root causes
- [ ] Recommendations are specific and actionable
- [ ] Effort and impact are estimated
- [ ] Recommendations are prioritized
- [ ] Owners are assigned
- [ ] Success criteria are defined

### Report Quality
- [ ] Report is comprehensive and clear
- [ ] Executive summary is concise
- [ ] Analysis is well-documented
- [ ] Visual aids are effective
- [ ] Recommendations are actionable
- [ ] Report is accessible and shared

---

## Common Mistakes

### 1. Stopping Too Early

**Problem:** Stopping at proximate causes instead of root causes.

**Example:**
- Proximate: "Service crashed"
- Root: "No memory limits + memory leak + inadequate testing"

**Solution:** Keep asking "why" until you reach actionable, fundamental causes.

### 2. Blame Culture

**Problem:** Focusing on who made a mistake instead of why the system allowed it.

**Solution:** Focus on system design, processes, and organizational factors.

### 3. Single Root Cause Bias

**Problem:** Assuming there's only one root cause.

**Solution:** Recognize that complex problems usually have multiple root causes.

### 4. Confirmation Bias

**Problem:** Looking for evidence that supports initial hypothesis.

**Solution:** Actively seek disconfirming evidence. Challenge assumptions.

### 5. Insufficient Data

**Problem:** Making conclusions without sufficient evidence.

**Solution:** Gather comprehensive data. State assumptions clearly.

### 6. Analysis Paralysis

**Problem:** Over-analyzing without reaching conclusions.

**Solution:** Set time limits. Focus on actionable insights.

### 7. Vague Recommendations

**Problem:** "Improve testing" without specifics.

**Solution:** "Add integration tests for payment flow with 80% coverage."

### 8. No Follow-Through

**Problem:** Great analysis but no implementation.

**Solution:** Assign owners, set deadlines, track progress.

### 9. Ignoring Organizational Factors

**Problem:** Only analyzing technical factors.

**Solution:** Consider processes, culture, communication, resources.

### 10. Not Learning from Success

**Problem:** Only analyzing failures.

**Solution:** Also analyze what prevented worse outcomes. What worked well?

---

## Examples

### Example 1: Recurring Database Outages

**Problem:** Database outages occurring monthly despite fixes.

**5 Whys Analysis:**
1. Why do outages occur? → Database runs out of memory
2. Why does it run out of memory? → Query result sets are too large
3. Why are result sets too large? → No pagination on API endpoints
4. Why is there no pagination? → Not in original requirements
5. Why wasn't it added later? → No process for reviewing API design

**Root Causes:**
1. Missing API design review process
2. No pagination requirements in API standards
3. No memory limits on database queries

**Recommendations:**
- P0: Implement API design review process
- P0: Add pagination to all list endpoints
- P1: Create API design standards document
- P1: Implement query memory limits
- P2: Add API design training for engineers

### Example 2: Cascading Microservice Failures

**Problem:** Single service failure causes widespread outages.

**Fault Tree Analysis:**
```
System Outage
    |
    AND
    |
    +-- Service A fails
    |   |
    |   OR
    |   |
    |   +-- High latency
    |   +-- Out of memory
    |   +-- Dependency failure
    |
    +-- No circuit breaker
    |
    +-- No timeout
    |
    +-- No bulkhead
```

**Root Causes:**
1. No resilience patterns (circuit breaker, timeout, bulkhead)
2. No architecture review for resilience
3. No chaos engineering testing

**Recommendations:**
- P0: Implement circuit breakers for all external calls
- P0: Add timeouts to all external calls
- P1: Implement bulkhead pattern
- P1: Establish architecture review for resilience
- P1: Implement chaos engineering tests

### Example 3: Deployment Failures

**Problem:** 30% of deployments fail or require rollback.

**Fishbone Diagram Categories:**

**Process:**
- No deployment checklist
- No rollback testing
- Manual deployment steps

**Technology:**
- No canary deployments
- Insufficient monitoring
- No automated rollback

**People:**
- Knowledge not shared
- No deployment training
- Unclear ownership

**Environment:**
- Staging != production
- No deployment windows
- Peak traffic deployments

**Root Causes:**
1. Manual deployment process
2. Staging environment doesn't match production
3. No deployment best practices documentation

**Recommendations:**
- P0: Automate deployment process
- P0: Implement canary deployments
- P1: Make staging match production
- P1: Create deployment best practices guide
- P2: Implement deployment windows policy

### Example 4: Security Incident

**Problem:** Unauthorized access to customer data.

**Causal Chain:**
```
Unauthorized Access
    ↑
Weak Authentication
    ↑
No MFA Required
    ↑
No Security Review
    ↑
No Security Requirements
    ↑
No Security Champion
```

**Root Causes:**
1. No security requirements in development process
2. No security review before production
3. No security champion or ownership

**Recommendations:**
- P0: Implement MFA for all accounts
- P0: Conduct security audit of all systems
- P1: Establish security review process
- P1: Assign security champion
- P1: Create security requirements checklist
- P2: Security training for all engineers

---

## Related Skills

### Prerequisites
- **incident-analysis** — provides initial incident data
- **architecture-discovery** — understanding system structure

### Commonly Followed By
- **architecture-review** — address architectural root causes
- **production-readiness** — improve production readiness
- **observability-design** — improve detection capabilities
- **testing-strategy** — improve testing based on findings

### Related Skills
- **failure-mode-analysis** — proactive failure analysis
- **technical-debt-analysis** — addressing systemic technical issues
- **security-architecture-review** — for security-related root causes

### Alternative Approaches
- **incident-analysis** — for simpler, one-time incidents
- **failure-mode-analysis** — for proactive analysis

---

## Skill Composition

### Incident Investigation Workflow

```
Incident Occurs
      ↓
Incident Response (resolve)
      ↓
incident-analysis
      ↓
Is root cause clear?
      |
      +-- Yes → Action Items
      |
      +-- No → root-cause-analysis (this skill)
                    ↓
              Root Causes Identified
                    ↓
              architecture-review (if architectural)
                    ↓
              production-readiness
                    ↓
              observability-design
```

### Systemic Improvement Workflow

```
Multiple Related Incidents
      ↓
root-cause-analysis
      ↓
Systemic Issues Identified
      ↓
      +-- Technical → architecture-review
      +-- Process → Process improvement
      +-- Organizational → Organizational change
      ↓
Implement Changes
      ↓
Measure Effectiveness
```

---

## Evaluation Criteria

### Analysis Quality

**Excellent:**
- Multiple analysis techniques used effectively
- Root causes are fundamental and actionable
- Systemic issues clearly identified
- Analysis is thorough and rigorous
- Evidence-based conclusions
- Stakeholder validation obtained

**Good:**
- Primary analysis technique used well
- Root causes identified
- Some systemic issues noted
- Analysis is logical
- Conclusions supported by data

**Needs Improvement:**
- Analysis is superficial
- Stops at proximate causes
- Systemic issues not identified
- Conclusions not well-supported
- Stakeholder input not sought

### Recommendation Quality

**Excellent:**
- Recommendations directly address root causes
- Specific, actionable, and measurable
- Prioritized by impact and effort
- Owners assigned with deadlines
- Success criteria defined
- Implementation plan included

**Good:**
- Recommendations address root causes
- Mostly specific and actionable
- Some prioritization
- Owners identified

**Needs Improvement:**
- Vague recommendations
- Don't address root causes
- No prioritization
- No owners or deadlines

### Impact

**Excellent:**
- Recommendations implemented
- Problem recurrence prevented
- Systemic improvements made
- Organizational learning achieved
- Similar problems prevented

**Good:**
- Some recommendations implemented
- Problem recurrence reduced
- Some improvements made
- Learning captured

**Needs Improvement:**
- Few recommendations implemented
- Problem recurs
- Minimal improvement
- Limited learning

---

## Tags

`operations`, `analysis`, `root-cause`, `incident-response`, `problem-solving`, `systemic-improvement`, `5-whys`, `fishbone`, `fault-tree`, `reliability`, `sre`, `post-mortem`

---

## Version

**1.0.0** — Initial release