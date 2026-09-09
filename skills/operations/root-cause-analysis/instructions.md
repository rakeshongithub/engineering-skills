# Root Cause Analysis — Step-by-Step Instructions

This guide provides detailed instructions for conducting systematic root cause analysis to identify fundamental causes of problems.

---

## Overview

Root Cause Analysis (RCA) is performed after complex or recurring incidents to understand not just what happened, but why it happened at a fundamental level. RCA goes deeper than incident analysis to identify systemic issues and prevent future occurrences.

**Time Required:** 3-8 hours depending on problem complexity

**Participants:**
- Incident responders
- Service owners
- Engineering leadership
- Product managers (for systemic issues)
- Process owners (for organizational issues)

---

## Step 1: Define the Problem (30-45 minutes)

### Create Clear Problem Statement

**Template:**
```
Problem: [Specific, measurable description]
Scope: [What's included and excluded]
Occurrence: [When and how often it happens]
Impact: [Quantified business and user impact]
```

**Example:**
```
Problem: Database outages occurring monthly, lasting 10-30 minutes each
Scope: Production database only, not staging or development
Occurrence: Monthly since June 2024 (6 incidents)
Impact: 100% service unavailability, ~$50K revenue loss per incident
```

### Distinguish Problem from Symptoms

**Symptoms (NOT the problem):**
- Service is slow ❌
- Users are complaining ❌
- Error rate is high ❌

**Problem (root issue):**
- Database connection pool exhausts under peak load ✓
- Memory leak causes OOM after 6 hours ✓
- Unbounded cache growth degrades performance ✓

### Get Stakeholder Agreement

**Actions:**
- Share problem statement with all stakeholders
- Ensure everyone agrees on scope
- Clarify what's in scope vs. out of scope
- Document assumptions and constraints
- Get explicit sign-off from leadership

### Quality Checks

- [ ] Problem is stated in specific, measurable terms
- [ ] Scope is clearly defined (what's in, what's out)
- [ ] Impact is quantified (users, revenue, time)
- [ ] Problem is distinguished from symptoms
- [ ] All stakeholders agree on problem definition
- [ ] Assumptions are documented

---

## Step 2: Gather Comprehensive Data (45-90 minutes)

### Collect Incident Data

**Logs:**
```bash
# Collect logs from all affected services
kubectl logs <pod-name> --since=24h > service-logs.txt

# Collect logs from specific time window
kubectl logs <pod-name> --since-time=2024-12-15T14:00:00Z > incident-logs.txt

# From logging system (Datadog, Splunk, etc.)
# Export logs for incident time window + 2 hours before/after
```

**Metrics:**
- CPU, memory, disk usage
- Request latency and throughput
- Error rates and types
- Database performance
- Network metrics
- Custom application metrics

**Traces:**
- Distributed traces (Jaeger, Zipkin)
- Request flows across services
- Latency breakdown by service

### Review System Architecture

**Understand:**
- Service dependencies
- Data flows
- Infrastructure topology
- Deployment architecture
- Scaling mechanisms

**Create Diagrams:**
```
[User] → [Load Balancer] → [Web Service]
                              ↓
                         [API Service]
                              ↓
                         [Database]
```

### Examine Code and Configuration

**Code Review:**
```bash
# Find recent changes to affected services
git log --since="2 weeks ago" --oneline -- path/to/service/

# Review specific commits
git show <commit-hash>

# Find who changed specific code
git blame path/to/file.js
```

**Configuration Review:**
- Environment variables
- Configuration files
- Feature flags
- Infrastructure as Code (Terraform, CloudFormation)

### Analyze Deployment History

**Check:**
```bash
# Kubernetes deployment history
kubectl rollout history deployment/<name>

# Describe specific revision
kubectl rollout history deployment/<name> --revision=5

# CI/CD deployment logs
# Review Jenkins, GitLab CI, GitHub Actions logs
```

### Review Previous Related Incidents

**Look for:**
- Similar symptoms
- Same services affected
- Common root causes
- Patterns across incidents
- Previous action items (were they completed?)

### Interview Stakeholders

**Questions to Ask:**
- What did you observe during the incident?
- What was different this time?
- What hypotheses did you test?
- What worked? What didn't?
- What information was missing?
- What would have helped?

### Quality Checks

- [ ] Logs from all relevant services collected
- [ ] Metrics covering incident window + buffer
- [ ] System architecture is understood
- [ ] Recent changes are identified
- [ ] Previous incidents are reviewed
- [ ] Stakeholders are interviewed
- [ ] Data quality is sufficient for analysis

---

## Step 3: Build Timeline and Sequence (30-60 minutes)

### Create Detailed Timeline

**Template:**
```
[TIME] | [SYSTEM/SERVICE] | [EVENT] | [METRIC] | [ACTION]
```

**Example:**
```
14:00 | Deployment    | v2.3.1 deployed          | -              | -
14:23 | API Service   | Error rate spike         | 0.1% → 95%     | Alert fired
14:24 | PagerDuty     | On-call paged            | -              | Alice ack
14:28 | Database      | Connection errors        | 50/50 conns    | -
14:30 | Investigation | Pool exhaustion found    | 100% util      | -
14:35 | Mitigation    | Pool size increased      | 50 → 200       | Deployed
14:38 | Resolution    | Error rate normalized    | 95% → 0%       | Resolved
```

### Identify Trigger Events

**Look for:**
- Code deployments
- Configuration changes
- Traffic spikes
- External dependency changes
- Infrastructure changes
- Time-based events (cron jobs, scheduled tasks)

### Map Cause-Effect Relationships

**Example:**
```
Deployment v2.3.1 (14:00)
    ↓
Connection leak introduced
    ↓
Connections not returned to pool
    ↓
Pool utilization increases over time
    ↓
Pool exhausted at 14:23 (under peak load)
    ↓
All new requests fail
    ↓
Error rate spikes to 95%
```

### Identify Decision Points

**Document:**
- What was known at each point?
- What decisions were made?
- What alternatives were considered?
- Why was each decision made?
- What information was missing?

### Identify Missed Detection Opportunities

**Questions:**
- When could this have been detected earlier?
- What monitoring would have helped?
- What alerts should have fired?
- Why didn't existing safeguards work?

### Quality Checks

- [ ] Timeline is complete and accurate
- [ ] All times are in same timezone (UTC)
- [ ] Cause-effect relationships are clear
- [ ] Trigger events are identified
- [ ] Decision points are documented
- [ ] Missed opportunities are noted

---

## Step 4: Apply "5 Whys" Analysis (30-60 minutes)

### Start with Problem Statement

**Problem:** Service experienced complete outage for 15 minutes

### Ask "Why" Five Times (or more)

**Example 1: Database Outage**

1. **Why did the service fail?**
   → Because the database connection pool was exhausted

2. **Why was the connection pool exhausted?**
   → Because connections were not being returned to the pool

3. **Why were connections not being returned?**
   → Because the finally block was missing in the database access code

4. **Why was the finally block missing?**
   → Because the code review didn't catch it

5. **Why didn't code review catch it?**
   → Because the code review checklist was incomplete

**Root Causes:**
- Missing finally block in code (technical)
- Incomplete code review checklist (process)
- No automated static analysis (tooling)

### Explore Multiple Causal Chains

**Alternative Chain:**

1. **Why did the service fail?**
   → Because the database connection pool was exhausted

2. **Why was the pool exhausted?**
   → Because the pool size (50) was too small for peak load

3. **Why was the pool size too small?**
   → Because it was never adjusted for increased traffic

4. **Why wasn't it adjusted?**
   → Because there was no monitoring on pool utilization

5. **Why was there no monitoring?**
   → Because connection pool monitoring wasn't in the observability standards

**Root Causes:**
- Small connection pool size (configuration)
- No connection pool monitoring (observability)
- Incomplete observability standards (process)

### Verify Each "Why" with Evidence

**For each "why", provide:**
- Log evidence
- Metric data
- Code references
- Configuration snapshots
- Stakeholder confirmation

**Example:**
```
Why: Connections not returned to pool
Evidence:
- Code review shows missing finally block (PR #1234, line 45)
- Logs show connection count increasing over time
- Metrics show pool utilization: 10% → 100% over 4 hours
- Database team confirms connections not being closed
```

### Identify Where Chains Converge

**Multiple chains may converge at:**
- Common organizational issues
- Shared process gaps
- Systemic technical debt
- Cultural factors

**Example:**
```
Chain 1: Code defect → Incomplete code review → Incomplete checklist
                                                        ↓
Chain 2: Small pool → No monitoring → Incomplete standards
                                                        ↓
                        Common Root: Incomplete engineering standards
```

### Distinguish Root Causes from Contributing Factors

**Root Cause Criteria:**
- If fixed, would prevent the problem ✓
- Fundamental, not a symptom ✓
- Actionable and within control ✓
- Systemic, not one-time ✓

**Contributing Factor Criteria:**
- Made problem more likely or severe
- Fixing reduces risk but doesn't eliminate it
- May be outside direct control
- May be situational

### Quality Checks

- [ ] Each "why" is supported by evidence
- [ ] Analysis reaches fundamental causes
- [ ] Multiple causal chains explored
- [ ] Root causes are actionable
- [ ] Contributing factors are identified
- [ ] Convergence points are noted

---

## Step 5: Create Fishbone Diagram (30-45 minutes)

### Identify Major Categories

**Standard Categories:**
1. **People** — Human factors, knowledge, skills
2. **Process** — Procedures, workflows, policies
3. **Technology** — Code, infrastructure, tools
4. **Environment** — External factors, dependencies

**Alternative Categories (6 Ms):**
1. **Machine** — Equipment, infrastructure
2. **Method** — Processes, procedures
3. **Material** — Inputs, dependencies
4. **Measurement** — Monitoring, metrics
5. **Mother Nature** — External events
6. **Manpower** — People, skills

### Brainstorm Factors in Each Category

**Example: Database Outage**

**People:**
- Developer didn't know about finally block pattern
- Code reviewer didn't catch the issue
- No database expert on the team
- Knowledge not shared across team

**Process:**
- Incomplete code review checklist
- No static analysis in CI/CD
- Load testing didn't run long enough
- No deployment freeze before peak periods

**Technology:**
- Missing finally block in code
- Small connection pool size
- No connection pool monitoring
- No auto-scaling for connection pool

**Environment:**
- Third-party database (limited control)
- Peak shopping period (high load)
- Recent traffic increase (50% growth)
- Deployment timing (4 hours before peak)

### Organize Factors Hierarchically

**Example:**
```
Technology
  |
  +-- Code Issues
  |     |
  |     +-- Missing finally block
  |     +-- No error handling
  |
  +-- Configuration Issues
  |     |
  |     +-- Small pool size
  |     +-- No auto-scaling
  |
  +-- Monitoring Issues
        |
        +-- No pool utilization monitoring
        +-- No connection leak detection
```

### Create Visual Fishbone Diagram

**Template:**
```
                    Problem: Database Outage
                              |
        People                |                Process
          |                   |                   |
          +-------------------+-------------------+
          |                                       |
          +-------------------+-------------------+
          |                   |                   |
      Technology              |              Environment
```

**Detailed Example:**
```
  No DB expert        Incomplete checklist
       |                      |
       +--------+    +--------+
                |    |
  People        |    |        Process
       \        |    |        /
        \       |    |       /
         +------+----+------+
                |            
                |  Database Outage
                |            
         +------+----+------+
        /       |    |       \
       /        |    |        \
  Technology   |    |    Environment
       |        |    |        |
       +--------+    +--------+
       |                      |
  Missing finally      Peak traffic
  Small pool size      Deployment timing
  No monitoring
```

### Identify Relationships Between Factors

**Example:**
```
No DB expert (People)
    ↓
Incomplete checklist (Process)
    ↓
Missing finally block not caught (Technology)
    ↓
Connection leak (Technology)
```

### Validate with Stakeholders

**Actions:**
- Share diagram with all participants
- Ask: "Are we missing any factors?"
- Ask: "Are relationships correct?"
- Incorporate feedback
- Get consensus

### Identify Most Significant Factors

**Criteria:**
- Impact on the problem
- Frequency of occurrence
- Ease of addressing
- Systemic vs. one-time

**Prioritization:**
- **High Impact + High Frequency** → Address immediately
- **High Impact + Low Frequency** → Address soon
- **Low Impact + High Frequency** → Consider addressing
- **Low Impact + Low Frequency** → Lower priority

### Quality Checks

- [ ] All major categories covered
- [ ] Factors are comprehensive
- [ ] Factors are specific and actionable
- [ ] Relationships are identified
- [ ] Diagram is clear and understandable
- [ ] Stakeholders validate completeness
- [ ] Most significant factors are identified

---

## Step 6: Perform Fault Tree Analysis (45-90 minutes)

### Identify the Top Event

**Top Event:** The problem you're analyzing

**Example:** Service Outage

### Identify Immediate Causes (AND/OR Logic)

**AND Gate:** All conditions must be true
**OR Gate:** Any condition can cause the event

**Example:**
```
Service Outage
    |
   AND  (All must be true for outage)
    |
    +-- Database unavailable
    +-- No fallback mechanism
```

### Break Down Each Cause into Sub-Causes

**Example:**
```
Database Unavailable
    |
   OR  (Any can cause unavailability)
    |
    +-- Connection pool exhausted
    +-- Database server down
    +-- Network partition
```

**Continue Breaking Down:**
```
Connection Pool Exhausted
    |
   AND  (All must be true)
    |
    +-- Connection leak
    +-- High traffic
    +-- Small pool size
```

### Continue Until Reaching Basic Events

**Basic Events:** Cannot be broken down further

**Example:**
```
Connection Leak
    |
   AND
    |
    +-- Missing finally block (BASIC EVENT)
    +-- Code review didn't catch it (BASIC EVENT)
```

### Create Complete Fault Tree

**Example:**
```
                    Service Outage
                          |
                         AND
                          |
          +---------------+---------------+
          |                               |
    Database                         No Fallback
    Unavailable                      (BASIC EVENT)
          |
         OR
          |
    +-----+-----+
    |           |
  Pool        Server
  Exhausted   Down
    |         (BASIC)
   AND
    |
  +-+-+-+
  | | | |
  L H S N
  e i m o
  a g a M
  k h l o
    T l n
    r P i
    a o t
    f o o
    f l r
      | |
    (B)(B)
```

Where:
- L = Connection Leak (BASIC)
- H = High Traffic (BASIC)
- S = Small Pool (BASIC)
- N = No Monitoring (BASIC)

### Identify Critical Paths

**Critical Path:** Shortest path from top event to basic events

**Example:**
```
Service Outage → Database Unavailable → Pool Exhausted → Connection Leak
```

**Why Critical:**
- Single point of failure
- Most likely path
- Easiest to address

### Identify Single Points of Failure

**SPOF:** Single event that can cause the top event

**Example:**
```
No Fallback Mechanism
    |
    +-- If database fails, service fails
    +-- No cache
    +-- No read replicas
    +-- No circuit breaker
```

### Identify Common Cause Failures

**Common Cause:** Single event that causes multiple failures

**Example:**
```
Deployment v2.3.1
    |
    +-- Introduced connection leak
    +-- Increased memory usage
    +-- Changed timeout behavior
```

### Calculate Probabilities (Optional)

**If data available:**
```
P(Service Outage) = P(Database Unavailable) × P(No Fallback)

P(Database Unavailable) = P(Pool Exhausted) + P(Server Down) + P(Network Partition)

P(Pool Exhausted) = P(Leak) × P(High Traffic) × P(Small Pool)
```

### Quality Checks

- [ ] Fault tree is logically complete
- [ ] AND/OR logic is correct
- [ ] All paths lead to basic events
- [ ] Critical paths are identified
- [ ] Single points of failure are noted
- [ ] Common cause failures are identified
- [ ] Analysis is thorough and rigorous

---

## Step 7: Identify Systemic Issues (30-60 minutes)

### Look for Patterns Across Multiple Incidents

**Questions:**
- Have we seen this before?
- Are there common themes?
- Do incidents share root causes?
- Are the same services affected?
- Are the same processes failing?

**Example:**
```
Incident 1 (June): Connection pool exhaustion
Incident 2 (July): Thread pool exhaustion
Incident 3 (Aug): Memory exhaustion

Pattern: Resource exhaustion due to lack of monitoring and limits
```

### Identify Process Gaps or Weaknesses

**Common Process Issues:**
- Incomplete code review checklists
- Inadequate testing (unit, integration, load)
- Poor deployment processes
- Insufficient documentation
- Lack of runbooks
- No post-deployment verification
- Inadequate monitoring and alerting

**Example:**
```
Process Gap: Code Review
- Checklist doesn't cover resource leaks
- No automated static analysis
- Reviewers not trained on common issues
- No enforcement of checklist completion
```

### Identify Organizational Factors

**Common Organizational Issues:**
- Knowledge silos (only one person knows the system)
- Unclear ownership (who's responsible?)
- Communication failures (teams don't talk)
- Resource constraints (not enough people/time)
- Competing priorities (reliability vs. features)
- Lack of training
- No dedicated SRE/reliability team

**Example:**
```
Organizational Issue: Knowledge Silos
- Only one person understands database configuration
- No documentation of connection pool tuning
- Knowledge not shared during onboarding
- Single point of failure for expertise
```

### Identify Technical Debt or Design Issues

**Common Technical Issues:**
- Monolithic architecture (hard to scale, deploy)
- Missing resilience patterns (circuit breakers, timeouts)
- Inadequate observability (logging, metrics, tracing)
- Poor error handling
- Lack of automated testing
- Infrastructure as code not adopted
- Manual deployment processes

**Example:**
```
Technical Debt: Resilience Patterns
- No circuit breakers for external dependencies
- No timeouts on external calls
- No bulkhead pattern (resource isolation)
- No retry logic with exponential backoff
- No fallback mechanisms
```

### Identify Cultural Factors

**Common Cultural Issues:**
- Blame culture (focus on who, not why)
- Move fast and break things (at expense of reliability)
- Not invented here syndrome (rejecting best practices)
- Hero culture (rewarding firefighting over prevention)
- Lack of psychological safety (people afraid to speak up)
- Reliability not valued (features prioritized over stability)

**Example:**
```
Cultural Issue: Hero Culture
- Engineers rewarded for fixing incidents quickly
- Prevention work not recognized or rewarded
- Firefighting valued over fire prevention
- Leads to reactive rather than proactive approach
```

### Identify Resource Constraints

**Common Resource Issues:**
- Insufficient engineering capacity
- Lack of specialized expertise (SRE, security)
- Limited budget for tools and infrastructure
- Competing priorities (too many initiatives)
- Technical debt backlog too large

**Example:**
```
Resource Constraint: Engineering Capacity
- Team of 5 supporting 20 services
- No time for reliability improvements
- Always in reactive mode
- Technical debt growing faster than it's addressed
```

### Identify Knowledge Gaps

**Common Knowledge Issues:**
- Lack of training on best practices
- New technologies adopted without expertise
- Insufficient documentation
- No knowledge sharing culture
- Onboarding inadequate

**Example:**
```
Knowledge Gap: Database Best Practices
- Team doesn't know connection pool tuning
- No training on database performance
- No database expert on the team
- Documentation is outdated
```

### Identify Communication Issues

**Common Communication Issues:**
- Silos between teams (dev, ops, product)
- Inadequate incident communication
- No regular sync meetings
- Poor documentation
- Knowledge not shared

**Example:**
```
Communication Issue: Dev/Ops Silos
- Dev team doesn't understand operational concerns
- Ops team not involved in design decisions
- No shared on-call rotation
- Different tools and processes
```

### Quality Checks

- [ ] Patterns across incidents are identified
- [ ] Process gaps are documented
- [ ] Organizational factors are considered
- [ ] Technical debt is identified
- [ ] Cultural factors are analyzed
- [ ] Resource constraints are noted
- [ ] Knowledge gaps are identified
- [ ] Communication issues are documented
- [ ] Issues are specific and actionable

---

## Step 8: Determine Root Causes (30-45 minutes)

### Review All Analysis

**Synthesize findings from:**
- 5 Whys analysis
- Fishbone diagram
- Fault tree analysis
- Systemic issues identification

**Look for:**
- Causes that appear in multiple analyses
- Convergence points
- Common themes
- Fundamental issues

### Identify Causes in Multiple Analyses

**Example:**
```
5 Whys: Incomplete code review checklist
Fishbone: Incomplete code review checklist (Process)
Fault Tree: Code review didn't catch it (Basic Event)
Systemic: Process gap in code review

→ Root Cause: Incomplete code review process
```

### Distinguish Root Causes from Contributing Factors

**Apply Root Cause Criteria:**

**Root Cause:**
- ✓ If fixed, would prevent the problem
- ✓ Fundamental, not a symptom
- ✓ Actionable and within control
- ✓ Systemic, not one-time

**Example:**
```
Missing finally block → Contributing Factor (one-time code defect)
Incomplete code review checklist → Root Cause (systemic process gap)
```

### Prioritize Root Causes by Impact

**Criteria:**
1. **Impact** — How much would fixing this reduce risk?
2. **Likelihood** — How likely is this to cause problems again?
3. **Scope** — Does this affect one service or many?
4. **Effort** — How hard is it to fix?

**Prioritization Matrix:**
```
High Impact + High Likelihood → P0 (Critical)
High Impact + Low Likelihood → P1 (High)
Low Impact + High Likelihood → P1 (High)
Low Impact + Low Likelihood → P2 (Medium)
```

**Example:**
```
Root Cause 1: Incomplete code review checklist
- Impact: High (could prevent many defects)
- Likelihood: High (code review happens daily)
- Scope: All services
- Effort: Low (update checklist, train team)
- Priority: P0

Root Cause 2: No connection pool monitoring
- Impact: High (early detection of issues)
- Likelihood: Medium (connection issues are common)
- Scope: Services using database
- Effort: Medium (add monitoring, alerts)
- Priority: P0

Root Cause 3: Knowledge silos
- Impact: Medium (reduces bus factor)
- Likelihood: Medium (people leave, get sick)
- Scope: All teams
- Effort: High (cultural change, documentation)
- Priority: P1
```

### Verify Root Causes with Evidence

**For each root cause, provide:**
- Evidence from logs, metrics, code
- References to analysis (5 Whys, Fishbone, Fault Tree)
- Stakeholder confirmation
- Historical data (has this caused problems before?)

**Example:**
```
Root Cause: Incomplete code review checklist

Evidence:
- Code review checklist doesn't mention resource leaks
- 3 previous incidents caused by resource leaks
- Code reviewers confirm they didn't know to check for this
- Static analysis tools not in CI/CD pipeline
- No training on common code review issues

Conclusion: Systemic gap in code review process
```

### Test Root Causes

**Ask: "If we fix this, would it prevent the problem?"**

**Example:**
```
Root Cause: Incomplete code review checklist

Test: If we update the checklist to include resource leak detection,
would it have prevented this incident?

Answer: Yes, if reviewer had checked for finally blocks, they would
have caught the missing finally block.

Conclusion: This is a valid root cause.
```

**Counter-Example:**
```
Proposed Root Cause: Developer made a mistake

Test: If we tell developers not to make mistakes, would it prevent
the problem?

Answer: No, humans make mistakes. We need systems to catch mistakes.

Conclusion: This is NOT a root cause, it's a symptom.
```

### Get Stakeholder Validation

**Actions:**
- Share root causes with all stakeholders
- Explain the rationale for each
- Ask for feedback and challenges
- Incorporate feedback
- Get consensus
- Document any disagreements

**Questions to Ask:**
- Do these root causes make sense?
- Are we missing any root causes?
- Do you agree with the prioritization?
- Are these actionable?
- Do you have additional evidence?

### Document Rationale

**For each root cause, document:**

**Template:**
```
Root Cause: [NAME]

Description: [DETAILED EXPLANATION]

Evidence:
- [EVIDENCE 1]
- [EVIDENCE 2]
- [EVIDENCE 3]

Analysis:
- Appears in: [5 Whys / Fishbone / Fault Tree / Systemic]
- Impact: [HIGH / MEDIUM / LOW]
- Likelihood: [HIGH / MEDIUM / LOW]
- Scope: [DESCRIPTION]

Rationale:
[WHY THIS IS A ROOT CAUSE, NOT A SYMPTOM]

Test:
If we fix this, would it prevent the problem? [YES/NO]
Explanation: [WHY]

Priority: [P0 / P1 / P2]
```

### Quality Checks

- [ ] All analyses reviewed and synthesized
- [ ] Root causes appear in multiple analyses
- [ ] Root causes are fundamental, not symptoms
- [ ] Root causes are supported by evidence
- [ ] Root causes are actionable
- [ ] Root causes are prioritized
- [ ] Root causes pass the "if we fix this" test
- [ ] Stakeholders validate root causes
- [ ] Rationale is clearly documented

---

## Step 9: Develop Recommendations (45-90 minutes)

### Develop Recommendations for Each Root Cause

**Template:**
```
[P0/P1/P2] [SHORT TITLE]
Owner: [NAME]
Deadline: [DATE]
Root Cause: [WHICH ROOT CAUSE THIS ADDRESSES]
Description: [DETAILED DESCRIPTION]
Success Criteria: [HOW TO VERIFY]
Effort: [SMALL / MEDIUM / LARGE]
Impact: [HIGH / MEDIUM / LOW]
```

**Example:**
```
P0 Update Code Review Checklist
Owner: Alice
Deadline: 2024-12-30
Root Cause: Incomplete code review process
Description: Update code review checklist to include:
- Resource leak detection (connections, files, threads)
- Error handling verification
- Finally block verification
- Timeout verification
Success Criteria:
- Checklist updated and published
- Team trained on new checklist items
- Checklist enforced in PR template
Effort: Small (4 hours)
Impact: High (prevents many defects)
```

### Categorize by Timeframe

**Immediate (Already Done):**
- Actions taken during the incident
- Hotfixes deployed
- Emergency mitigations

**Short-term (1-2 weeks):**
- Quick wins
- High-impact, low-effort improvements
- Prevent immediate recurrence

**Medium-term (1-3 months):**
- Moderate effort improvements
- Process changes
- Tooling improvements

**Long-term (3-6 months):**
- Large efforts
- Organizational changes
- Cultural shifts
- Architectural improvements

### Categorize by Type

**Technical:**
- Code changes
- Infrastructure improvements
- Tooling additions
- Architecture changes

**Process:**
- Checklist updates
- Runbook creation
- Policy changes
- Workflow improvements

**Organizational:**
- Team structure changes
- Ownership clarification
- Training programs
- Cultural initiatives

**Monitoring/Observability:**
- New metrics
- New alerts
- Dashboard improvements
- Tracing additions

### Estimate Effort and Impact

**Effort Estimation:**
- **Small:** < 1 week (1-40 hours)
- **Medium:** 1-4 weeks (40-160 hours)
- **Large:** > 1 month (160+ hours)

**Impact Estimation:**
- **High:** Prevents major incidents, affects all services
- **Medium:** Reduces risk significantly, affects some services
- **Low:** Incremental improvement, limited scope

**Prioritization Matrix:**
```
High Impact + Small Effort → P0 (Do immediately)
High Impact + Medium Effort → P1 (Do soon)
High Impact + Large Effort → P1-P2 (Plan carefully)
Medium Impact + Small Effort → P1 (Quick wins)
Medium Impact + Medium Effort → P2 (Schedule)
Medium Impact + Large Effort → P2-P3 (Consider)
Low Impact + Any Effort → P3 (Nice to have)
```

### Identify Dependencies

**Example:**
```
Recommendation 1: Add connection pool monitoring
Recommendation 2: Add alert for pool utilization > 80%

Dependency: Recommendation 2 depends on Recommendation 1
(Can't alert on metric that doesn't exist)

Order: 1 → 2
```

**Dependency Types:**
- **Sequential:** A must be done before B
- **Parallel:** A and B can be done simultaneously
- **Blocking:** A blocks B, but B doesn't need A to be complete

### Prioritize Recommendations

**Criteria:**
1. **Addresses root cause** (not just symptoms)
2. **Impact** (high impact = higher priority)
3. **Effort** (low effort = higher priority for same impact)
4. **Dependencies** (unblock others = higher priority)
5. **Risk** (high risk of recurrence = higher priority)

**Priority Levels:**
- **P0 (Critical):** Must do immediately (1-2 weeks)
- **P1 (High):** Should do soon (1-3 months)
- **P2 (Medium):** Should do eventually (3-6 months)
- **P3 (Low):** Nice to have (6+ months or backlog)

### Assign Owners

**Owner Criteria:**
- **Expertise:** Has the skills to implement
- **Availability:** Has capacity to take this on
- **Ownership:** Owns the affected service/process
- **Accountability:** Will be held accountable for completion

**Best Practices:**
- Assign one owner per recommendation (not a team)
- Get owner's commitment before assigning
- Ensure owner has authority to implement
- Provide support and resources

### Define Success Criteria

**Good Success Criteria:**
- Specific and measurable
- Verifiable (can test/demonstrate)
- Achievable
- Time-bound

**Examples:**

❌ **Bad:** "Improve monitoring"
✓ **Good:** "Connection pool utilization metric added to dashboard, alert fires when > 80%, tested in staging"

❌ **Bad:** "Better code review"
✓ **Good:** "Code review checklist updated with 5 new items, team trained, checklist completion rate > 95%"

❌ **Bad:** "Fix the issue"
✓ **Good:** "Connection leak fixed, load test runs for 8 hours without pool exhaustion, deployed to production"

### Create Implementation Plan

**For each recommendation:**

**Template:**
```
Recommendation: [TITLE]

Implementation Steps:
1. [STEP 1] - [OWNER] - [DEADLINE]
2. [STEP 2] - [OWNER] - [DEADLINE]
3. [STEP 3] - [OWNER] - [DEADLINE]

Milestones:
- [DATE]: [MILESTONE 1]
- [DATE]: [MILESTONE 2]
- [DATE]: [MILESTONE 3]

Risks:
- [RISK 1]: [MITIGATION]
- [RISK 2]: [MITIGATION]

Dependencies:
- [DEPENDENCY 1]
- [DEPENDENCY 2]

Success Criteria:
- [CRITERIA 1]
- [CRITERIA 2]
```

### Quality Checks

- [ ] Recommendations address root causes
- [ ] Recommendations are specific and actionable
- [ ] Effort and impact are estimated
- [ ] Dependencies are identified
- [ ] Recommendations are prioritized
- [ ] Owners are assigned and committed
- [ ] Success criteria are defined
- [ ] Implementation plans are created
- [ ] Stakeholders agree on recommendations

---

## Step 10: Create RCA Report and Present (60-120 minutes)

### Write Comprehensive RCA Report

**Report Structure:**

```markdown
# Root Cause Analysis Report: [TITLE]

## Executive Summary
[2-3 paragraphs for leadership]

## Problem Statement
[Clear, specific problem definition]

## Analysis Methodology
[Which techniques were used and why]

## Timeline of Events
[Detailed timeline]

## Analysis

### 5 Whys Analysis
[Causal chains]

### Fishbone Diagram
[Categorized causes]

### Fault Tree Analysis
[Logical failure paths]

### Systemic Issues
[Organizational, process, technical, cultural]

## Root Causes
[Detailed root cause analysis]

## Contributing Factors
[Factors that made problem worse]

## Recommendations
[Prioritized, actionable recommendations]

## Implementation Plan
[Timeline and ownership]

## Appendix
[Supporting data, diagrams, references]
```

### Write Executive Summary

**Template:**
```
This root cause analysis examines [PROBLEM] which occurred [WHEN] 
and resulted in [IMPACT].

Our analysis using [TECHNIQUES] identified [NUMBER] root causes:
1. [ROOT CAUSE 1]
2. [ROOT CAUSE 2]
3. [ROOT CAUSE 3]

We have developed [NUMBER] recommendations prioritized as [P0/P1/P2] 
to address these root causes and prevent recurrence. Implementation 
will begin [DATE] with completion expected by [DATE].

Key recommendations include:
- [RECOMMENDATION 1]
- [RECOMMENDATION 2]
- [RECOMMENDATION 3]
```

**Example:**
```
This root cause analysis examines recurring database outages which 
occurred monthly from June-December 2024 and resulted in cumulative 
service downtime of 3 hours and ~$300K in lost revenue.

Our analysis using 5 Whys, Fishbone diagrams, and Fault Tree Analysis 
identified 3 primary root causes:
1. Incomplete code review process (missing resource leak detection)
2. Inadequate observability (no connection pool monitoring)
3. Insufficient load testing (tests don't match production)

We have developed 12 recommendations prioritized as P0 (4), P1 (5), 
and P2 (3) to address these root causes and prevent recurrence. 
Implementation will begin December 22, 2024 with completion expected 
by March 31, 2025.

Key recommendations include:
- Update code review checklist with resource leak detection (P0)
- Add connection pool monitoring and alerting (P0)
- Extend load test duration to 8+ hours with production config (P0)
- Implement automated static analysis in CI/CD (P0)
```

### Document Methodology

**Explain:**
- Which analysis techniques were used
- Why each technique was chosen
- How techniques complemented each other
- What data sources were used
- Who participated in the analysis

**Example:**
```
Methodology:

We used three complementary analysis techniques:

1. 5 Whys Analysis: To drill down from symptoms to root causes through 
   iterative questioning. This helped us identify causal chains.

2. Fishbone Diagram: To categorize contributing factors across People, 
   Process, Technology, and Environment. This ensured comprehensive 
   factor identification.

3. Fault Tree Analysis: To analyze logical combinations of failures 
   and identify critical paths and single points of failure.

Data sources included:
- Production logs from all affected services (7 days)
- Metrics from Datadog (30 days)
- Code repository history (6 months)
- Previous incident reports (6 months)
- Interviews with 8 stakeholders

Participants:
- Engineering team (5 engineers)
- SRE team (2 SREs)
- Database team (1 DBA)
- Engineering leadership (2 managers)
```

### Present Analysis

**Include:**
- Visual diagrams (Fishbone, Fault Tree)
- Causal chains from 5 Whys
- Timeline of events
- Supporting data (graphs, logs)
- Clear explanation of logic

**Best Practices:**
- Use visuals to explain complex relationships
- Show convergence across different analyses
- Highlight key insights
- Make it easy to follow the logic

### Clearly State Root Causes

**Template:**
```
## Root Causes

We identified [NUMBER] root causes:

### Root Cause 1: [NAME]

**Description:** [DETAILED EXPLANATION]

**Evidence:**
- [EVIDENCE 1]
- [EVIDENCE 2]
- [EVIDENCE 3]

**Analysis:**
- Identified in: [5 Whys / Fishbone / Fault Tree]
- Impact: [HIGH / MEDIUM / LOW]
- Scope: [DESCRIPTION]

**Why This is a Root Cause:**
[EXPLANATION OF WHY THIS IS FUNDAMENTAL]

**If We Fix This:**
[WHAT WOULD BE PREVENTED]

---

[REPEAT FOR EACH ROOT CAUSE]
```

### Present Recommendations with Priorities

**Group by Priority:**

```markdown
## Recommendations

### P0 (Critical) — Implement within 1-2 weeks

#### 1. Update Code Review Checklist
- **Owner:** Alice
- **Deadline:** 2024-12-30
- **Root Cause:** Incomplete code review process
- **Description:** [DETAILED DESCRIPTION]
- **Success Criteria:** [CRITERIA]
- **Effort:** Small (4 hours)
- **Impact:** High

#### 2. Add Connection Pool Monitoring
[...]

### P1 (High) — Implement within 1-3 months

#### 3. Implement Automated Static Analysis
[...]

### P2 (Medium) — Implement within 3-6 months

#### 8. Create Database Runbook
[...]
```

### Create Presentation for Stakeholders

**Slide Outline:**

1. **Title Slide**
   - RCA Title
   - Date
   - Presenter

2. **Executive Summary** (1 slide)
   - Problem
   - Impact
   - Root causes
   - Key recommendations

3. **Problem Statement** (1 slide)
   - What happened
   - When it happened
   - Impact (users, revenue)

4. **Timeline** (1 slide)
   - Key events
   - Visual timeline

5. **Analysis** (3-4 slides)
   - 5 Whys (1 slide)
   - Fishbone Diagram (1 slide)
   - Fault Tree (1 slide)
   - Systemic Issues (1 slide)

6. **Root Causes** (1-2 slides)
   - List of root causes
   - Evidence for each

7. **Recommendations** (2-3 slides)
   - P0 recommendations
   - P1 recommendations
   - P2 recommendations

8. **Implementation Plan** (1 slide)
   - Timeline
   - Ownership
   - Milestones

9. **Next Steps** (1 slide)
   - Immediate actions
   - Follow-up plan
   - How to track progress

10. **Q&A**

### Conduct Review Meeting

**Agenda:**

```
1. Introduction (5 min)
   - Purpose of RCA
   - Scope of analysis
   - Participants

2. Problem Statement (5 min)
   - What happened
   - Impact

3. Analysis (20 min)
   - Methodology
   - 5 Whys
   - Fishbone
   - Fault Tree
   - Systemic Issues

4. Root Causes (10 min)
   - Identified root causes
   - Evidence
   - Rationale

5. Recommendations (15 min)
   - P0 recommendations
   - P1 recommendations
   - P2 recommendations
   - Implementation plan

6. Discussion (20 min)
   - Questions
   - Feedback
   - Additional insights
   - Challenges to analysis

7. Next Steps (5 min)
   - Action item tracking
   - Follow-up meetings
   - How to measure success

8. Wrap-up (5 min)
```

### Get Feedback and Buy-in

**Questions to Ask:**
- Do the root causes make sense?
- Are we missing anything?
- Do you agree with the recommendations?
- Do you agree with the prioritization?
- Are the timelines realistic?
- Do you have concerns about any recommendations?
- What support do you need?

**Address Concerns:**
- Listen actively
- Acknowledge concerns
- Explain rationale
- Be willing to adjust
- Document disagreements
- Seek consensus

### Publish and Share Widely

**Where to Publish:**
- Internal wiki/documentation system
- Team Slack/Teams channel
- Engineering all-hands
- Leadership updates
- Company-wide newsletter (if appropriate)

**Who to Share With:**
- All participants
- Engineering team
- SRE/Operations team
- Product team
- Engineering leadership
- Executive team (executive summary)
- Relevant stakeholders

**Best Practices:**
- Make it easy to find
- Use consistent naming (RCA-YYYY-MM-DD-Title)
- Link from related documentation
- Include in incident report
- Reference in future RCAs

### Quality Checks

- [ ] Report is comprehensive and clear
- [ ] Executive summary is concise (2-3 paragraphs)
- [ ] Methodology is explained
- [ ] Analysis is well-documented with visuals
- [ ] Root causes are clearly stated with evidence
- [ ] Recommendations are actionable and prioritized
- [ ] Implementation plan is realistic
- [ ] Presentation is prepared
- [ ] Review meeting is conducted
- [ ] Feedback is incorporated
- [ ] Stakeholders support findings and recommendations
- [ ] Report is published and widely shared

---

## Tools and Templates

### Analysis Tools

**Diagramming:**
- Mermaid (text-based diagrams)
- Lucidchart
- Draw.io
- Excalidraw
- Whimsical

**Collaboration:**
- Google Docs (collaborative editing)
- Miro (virtual whiteboard)
- Confluence (documentation)
- Notion (documentation)

**Data Analysis:**
- Datadog, New Relic (metrics)
- Splunk, ELK (logs)
- Jaeger, Zipkin (traces)
- Excel, Google Sheets (data analysis)

### Templates

**5 Whys Template:**
```
1. Why did [PROBLEM] happen?
   → [ANSWER]

2. Why did [ANSWER FROM 1] happen?
   → [ANSWER]

3. Why did [ANSWER FROM 2] happen?
   → [ANSWER]

4. Why did [ANSWER FROM 3] happen?
   → [ANSWER]

5. Why did [ANSWER FROM 4] happen?
   → [ANSWER]

Root Cause: [ANSWER FROM 5]
```

**Fishbone Template:**
```
                    [PROBLEM]
                        |
    People              |              Process
      |                 |                 |
      +-----------------+-----------------+
      |                                   |
      +-----------------+-----------------+
      |                 |                 |
  Technology            |           Environment
```

**Fault Tree Template:**
```
[TOP EVENT]
    |
  [GATE]
    |
  +-+-+
  | | |
 [SUB-EVENTS]
```

**Root Cause Template:**
```
Root Cause: [NAME]

Description: [EXPLANATION]

Evidence:
- [EVIDENCE 1]
- [EVIDENCE 2]

Analysis:
- Appears in: [ANALYSES]
- Impact: [HIGH/MEDIUM/LOW]
- Priority: [P0/P1/P2]

Recommendations:
- [RECOMMENDATION 1]
- [RECOMMENDATION 2]
```

---

## Common Pitfalls to Avoid

### 1. Stopping Too Early

❌ **Don't stop at:** "Service crashed"
✓ **Continue to:** "No memory limits + memory leak + inadequate testing"

**Solution:** Keep asking "why" until you reach actionable, fundamental causes.

### 2. Blame Culture

❌ **Don't say:** "Alice made a mistake"
✓ **Say instead:** "Code review process didn't catch the issue"

**Solution:** Focus on systems, processes, and organizational factors, not individuals.

### 3. Single Root Cause Bias

❌ **Don't assume:** There's only one root cause
✓ **Recognize:** Complex problems have multiple root causes

**Solution:** Use multiple analysis techniques to identify all root causes.

### 4. Confirmation Bias

❌ **Don't:** Look only for evidence supporting your hypothesis
✓ **Do:** Actively seek disconfirming evidence

**Solution:** Challenge your assumptions. Ask "what would disprove this?"

### 5. Insufficient Data

❌ **Don't:** Make conclusions without evidence
✓ **Do:** Gather comprehensive data from all sources

**Solution:** If data is missing, state assumptions clearly and note limitations.

### 6. Analysis Paralysis

❌ **Don't:** Over-analyze without reaching conclusions
✓ **Do:** Set time limits and focus on actionable insights

**Solution:** Use timeboxing. Good enough analysis with action is better than perfect analysis with no action.

### 7. Vague Recommendations

❌ **Vague:** "Improve testing"
✓ **Specific:** "Add integration tests for payment flow with 80% coverage by Jan 31"

**Solution:** Use SMART criteria (Specific, Measurable, Achievable, Relevant, Time-bound).

### 8. No Follow-Through

❌ **Don't:** Write great report but don't implement
✓ **Do:** Track action items and measure effectiveness

**Solution:** Assign owners, set deadlines, track progress, measure outcomes.

### 9. Ignoring Organizational Factors

❌ **Don't:** Only analyze technical factors
✓ **Do:** Consider processes, culture, communication, resources

**Solution:** Use Fishbone diagram to ensure all categories are considered.

### 10. Not Learning from Success

❌ **Don't:** Only analyze what went wrong
✓ **Do:** Also analyze what prevented worse outcomes

**Solution:** Ask "what went well?" and "what could have been worse?"

---

## Success Criteria

You've successfully completed root cause analysis when:

- [ ] Problem is clearly defined and scoped
- [ ] Comprehensive data is gathered from all sources
- [ ] Timeline is complete and accurate
- [ ] Multiple analysis techniques are used (5 Whys, Fishbone, Fault Tree)
- [ ] Systemic issues are identified (technical, process, organizational)
- [ ] Root causes are clearly identified with supporting evidence
- [ ] Root causes are fundamental, not symptoms
- [ ] Root causes are actionable and within control
- [ ] Contributing factors are distinguished from root causes
- [ ] Recommendations address root causes
- [ ] Recommendations are specific, measurable, and prioritized
- [ ] Owners are assigned with realistic deadlines
- [ ] Success criteria are defined for each recommendation
- [ ] Comprehensive RCA report is written
- [ ] Executive summary is clear and concise
- [ ] Stakeholders validate findings and recommendations
- [ ] Report is published and widely shared
- [ ] Action items are tracked
- [ ] Follow-up plan is established

---

## Next Steps

After completing root cause analysis:

### Immediate

1. **Publish RCA Report**
   - Share with all stakeholders
   - Make easily accessible
   - Link from incident report

2. **Create Action Item Tickets**
   - Create Jira/Linear/GitHub issues
   - Assign owners
   - Set deadlines
   - Link to RCA report

3. **Schedule Follow-up Reviews**
   - 1-week review (short-term actions)
   - 1-month review (medium-term actions)
   - 3-month review (long-term actions)

### Short-term

1. **Implement P0 Recommendations**
   - Focus on critical items
   - Track progress
   - Communicate status

2. **Update Documentation**
   - Update runbooks
   - Update architecture docs
   - Update processes

3. **Share Learnings**
   - Engineering all-hands
   - Team retrospectives
   - Documentation

### Long-term

1. **Implement All Recommendations**
   - Complete P1 and P2 items
   - Track effectiveness
   - Measure outcomes

2. **Measure Effectiveness**
   - Track incident recurrence
   - Measure time to detect/resolve
   - Monitor related metrics

3. **Consider Related Skills**
   - **architecture-review** — Address architectural root causes
   - **production-readiness** — Improve production readiness
   - **observability-design** — Improve detection capabilities
   - **testing-strategy** — Improve testing based on findings
   - **failure-mode-analysis** — Proactively identify failure modes

---

## Additional Resources

### Books
- "The Field Guide to Understanding Human Error" by Sidney Dekker
- "Site Reliability Engineering" by Google
- "The Phoenix Project" by Gene Kim

### Articles
- Google SRE Book: Postmortem Culture
- Etsy's Debriefing Facilitation Guide
- "How Complex Systems Fail" by Richard Cook

### Tools
- TapRooT (RCA software)
- Cause Mapping (RCA methodology)
- Sologic (RCA software)

### Training
- Root Cause Analysis courses (Coursera, Udemy)
- SRE training programs
- Incident management certifications
