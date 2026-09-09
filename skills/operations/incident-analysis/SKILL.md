# Incident Analysis

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 2-6 hours

---

## Purpose

Systematically analyze production incidents to understand what happened, why it happened, and how to prevent recurrence.

---

## When to Use

- After a production incident or outage
- When investigating service degradation
- When analyzing near-miss events
- When conducting post-mortem reviews
- When identifying patterns across multiple incidents
- When building organizational learning from failures
- When improving incident response processes
- When validating monitoring and alerting effectiveness

---

## When NOT to Use

- **During active incident response** — focus on resolution first, analysis later
- **For trivial issues** — reserve formal analysis for significant incidents
- **Without sufficient data** — ensure logs, metrics, and traces are available
- **As blame assignment** — focus on system improvement, not individual fault
- **Without stakeholder involvement** — include all relevant teams
- **For hypothetical scenarios** — use threat modeling or failure mode analysis instead
- **Without follow-up action** — analysis without remediation wastes effort

---

## Inputs

### Required

- **Incident description** — what happened and when
- **Timeline of events** — sequence of actions and observations
- **System logs** — application and infrastructure logs
- **Metrics and monitoring data** — system performance during incident
- **Alerts and notifications** — what fired and when
- **Response actions** — what was done to resolve the incident

### Optional

- **Distributed traces** — request flows across services
- **User impact data** — affected users, transactions, revenue
- **Communication logs** — incident response team communications
- **Previous incident reports** — historical context
- **System architecture diagrams** — understanding of system structure
- **Runbooks and procedures** — documented response processes
- **Change logs** — recent deployments or configuration changes

---

## Expected Outputs

### Primary Deliverables

1. **Incident Report**
   - Executive summary
   - Detailed timeline
   - Impact assessment
   - Root cause analysis
   - Contributing factors
   - Resolution steps

2. **Root Cause Analysis**
   - Primary root cause
   - Contributing factors
   - Why it wasn't detected earlier
   - Why it wasn't prevented

3. **Action Items**
   - Immediate fixes (already implemented)
   - Short-term improvements (1-2 weeks)
   - Long-term improvements (1-3 months)
   - Owners and deadlines

4. **Lessons Learned**
   - What went well
   - What didn't go well
   - What we learned
   - How to improve

### Supporting Artifacts

- **Timeline visualization** — graphical representation of events
- **Impact metrics** — quantified user and business impact
- **Detection analysis** — how quickly the incident was detected
- **Response analysis** — how quickly the incident was resolved
- **Prevention recommendations** — specific technical improvements
- **Process improvements** — incident response process updates

---

## Workflow

### Step 1: Gather Data

**Objective:** Collect all relevant information about the incident.

**Actions:**
- Collect incident timeline from monitoring and logs
- Gather metrics data (CPU, memory, latency, error rates)
- Extract relevant log entries from all affected services
- Collect distributed traces if available
- Document user impact (affected users, failed transactions)
- Gather communication logs from incident response
- Identify recent changes (deployments, config changes)
- Collect alerts and notifications that fired

**Quality Check:**
- [ ] Complete timeline from detection to resolution
- [ ] Logs from all relevant services
- [ ] Metrics covering the incident window
- [ ] User impact quantified
- [ ] Recent changes identified

### Step 2: Construct Timeline

**Objective:** Build a detailed, accurate timeline of events.

**Actions:**
- Create chronological sequence of events
- Include detection time, escalation, and resolution
- Add key metrics at each point in time
- Note all response actions taken
- Identify decision points and why decisions were made
- Mark when alerts fired
- Note when different teams were engaged
- Include user-visible impact at each stage

**Quality Check:**
- [ ] All times are accurate and in the same timezone
- [ ] Events are in chronological order
- [ ] Key decision points are documented
- [ ] Response actions are clearly noted
- [ ] User impact is tracked throughout

### Step 3: Assess Impact

**Objective:** Quantify the business and technical impact.

**Actions:**
- Calculate affected users or customers
- Measure failed or degraded transactions
- Estimate revenue impact if applicable
- Measure duration of impact
- Assess data integrity issues
- Evaluate reputation or trust impact
- Measure SLA/SLO violations
- Calculate time to detect (TTD) and time to resolve (TTR)

**Quality Check:**
- [ ] User impact is quantified
- [ ] Business impact is estimated
- [ ] Duration is clearly stated
- [ ] SLA/SLO impact is documented
- [ ] Detection and resolution times are calculated

### Step 4: Identify Root Cause

**Objective:** Determine the fundamental cause of the incident.

**Actions:**
- Use "5 Whys" technique to drill down
- Distinguish between symptoms and root causes
- Identify the primary root cause
- Identify contributing factors
- Determine why existing safeguards failed
- Analyze why the issue wasn't detected earlier
- Evaluate why the issue wasn't prevented
- Avoid blame — focus on system and process failures

**Quality Check:**
- [ ] Root cause is specific and actionable
- [ ] Contributing factors are identified
- [ ] Analysis goes beyond symptoms
- [ ] Safeguard failures are explained
- [ ] Analysis is blameless

### Step 5: Analyze Detection and Response

**Objective:** Evaluate how well the incident was detected and handled.

**Actions:**
- Measure time to detection (TTD)
- Evaluate monitoring and alerting effectiveness
- Assess escalation process
- Review response actions for effectiveness
- Identify what worked well
- Identify what could be improved
- Evaluate communication effectiveness
- Assess runbook and documentation quality

**Quality Check:**
- [ ] Detection time is measured
- [ ] Monitoring gaps are identified
- [ ] Response effectiveness is evaluated
- [ ] Communication is assessed
- [ ] Process improvements are identified

### Step 6: Identify Contributing Factors

**Objective:** Understand all factors that contributed to the incident.

**Actions:**
- Identify technical contributing factors
- Identify process contributing factors
- Identify organizational contributing factors
- Analyze recent changes that may have contributed
- Evaluate system design weaknesses
- Assess operational practices
- Consider external factors (load, dependencies)
- Identify knowledge gaps

**Quality Check:**
- [ ] Technical factors are documented
- [ ] Process factors are documented
- [ ] Organizational factors are considered
- [ ] Recent changes are analyzed
- [ ] System design issues are identified

### Step 7: Define Action Items

**Objective:** Create specific, actionable improvements.

**Actions:**
- Define immediate fixes (already implemented during incident)
- Identify short-term improvements (1-2 weeks)
- Identify long-term improvements (1-3 months)
- Assign owners to each action item
- Set realistic deadlines
- Prioritize based on risk reduction
- Ensure actions address root cause, not just symptoms
- Include monitoring and detection improvements

**Quality Check:**
- [ ] Actions are specific and measurable
- [ ] Each action has an owner
- [ ] Deadlines are realistic
- [ ] Actions address root cause
- [ ] Monitoring improvements are included
- [ ] Actions are prioritized

### Step 8: Document Lessons Learned

**Objective:** Capture organizational learning.

**Actions:**
- Document what went well during response
- Document what didn't go well
- Capture new knowledge gained
- Identify process improvements
- Document technical insights
- Share knowledge across teams
- Update runbooks and documentation
- Consider training needs

**Quality Check:**
- [ ] Positive aspects are recognized
- [ ] Improvement areas are identified
- [ ] Knowledge is captured
- [ ] Documentation is updated
- [ ] Learning is shared

### Step 9: Create Incident Report

**Objective:** Produce a comprehensive, clear incident report.

**Actions:**
- Write executive summary (2-3 paragraphs)
- Include detailed timeline
- Document impact assessment
- Present root cause analysis
- List contributing factors
- Document resolution steps
- Present action items with owners and deadlines
- Include lessons learned
- Add supporting data (graphs, logs, metrics)

**Quality Check:**
- [ ] Report is clear and well-organized
- [ ] Executive summary is concise
- [ ] Timeline is accurate and complete
- [ ] Root cause is clearly explained
- [ ] Action items are specific
- [ ] Report is blameless

### Step 10: Review and Follow-Up

**Objective:** Ensure learning and improvement happen.

**Actions:**
- Conduct post-mortem meeting with all stakeholders
- Present findings and action items
- Get feedback on analysis
- Ensure action items are tracked
- Schedule follow-up reviews
- Track action item completion
- Measure effectiveness of improvements
- Update incident response processes

**Quality Check:**
- [ ] Post-mortem meeting is scheduled
- [ ] All stakeholders are included
- [ ] Action items are tracked
- [ ] Follow-up is scheduled
- [ ] Improvements are measured

---

## Decision Framework

### Severity Classification

**Critical (SEV-1)**
- Complete service outage
- Data loss or corruption
- Security breach
- Revenue impact > $X
- Requires immediate analysis

**High (SEV-2)**
- Significant service degradation
- Major feature unavailable
- Large user impact
- Requires analysis within 24 hours

**Medium (SEV-3)**
- Partial service degradation
- Moderate user impact
- Requires analysis within 1 week

**Low (SEV-4)**
- Minor issues
- Minimal user impact
- Analysis optional

### Root Cause Determination

**Technical Root Causes**
- Code defects
- Configuration errors
- Infrastructure failures
- Dependency failures
- Capacity issues
- Data issues

**Process Root Causes**
- Inadequate testing
- Poor deployment process
- Insufficient monitoring
- Inadequate documentation
- Lack of runbooks
- Poor change management

**Organizational Root Causes**
- Knowledge gaps
- Communication failures
- Unclear ownership
- Insufficient resources
- Inadequate training

### Action Item Prioritization

**P0 (Immediate)**
- Prevents recurrence of critical incident
- Addresses security vulnerability
- Fixes data integrity issue
- Implement within 1 week

**P1 (High)**
- Significantly reduces incident risk
- Improves detection or response
- Addresses systemic issue
- Implement within 1 month

**P2 (Medium)**
- Incremental improvement
- Reduces likelihood or impact
- Process improvement
- Implement within 3 months

**P3 (Low)**
- Nice to have
- Minor improvement
- Implement when capacity allows

---

## Quality Checklist

### Data Collection
- [ ] Complete timeline from detection to resolution
- [ ] Logs from all affected services
- [ ] Metrics covering incident window
- [ ] User impact quantified
- [ ] Recent changes identified
- [ ] Alerts and notifications documented

### Analysis Quality
- [ ] Root cause is specific and actionable
- [ ] Contributing factors are identified
- [ ] Analysis is blameless
- [ ] Detection and response are evaluated
- [ ] System design issues are identified
- [ ] Process gaps are identified

### Action Items
- [ ] Actions address root cause, not just symptoms
- [ ] Each action has a clear owner
- [ ] Deadlines are realistic
- [ ] Actions are prioritized by risk reduction
- [ ] Monitoring improvements are included
- [ ] Actions are tracked

### Report Quality
- [ ] Executive summary is clear and concise
- [ ] Timeline is accurate and complete
- [ ] Impact is quantified
- [ ] Root cause is clearly explained
- [ ] Action items are specific and measurable
- [ ] Report is blameless and constructive
- [ ] Lessons learned are documented

### Follow-Up
- [ ] Post-mortem meeting conducted
- [ ] All stakeholders included
- [ ] Action items are tracked
- [ ] Follow-up reviews scheduled
- [ ] Documentation updated
- [ ] Knowledge shared across teams

---

## Common Mistakes

### 1. Stopping at Symptoms

**Problem:** Identifying "database was slow" as the root cause.

**Solution:** Ask "why was the database slow?" Continue until you reach a fundamental cause.

### 2. Blame Culture

**Problem:** Focusing on who made a mistake rather than why the system allowed it.

**Solution:** Use blameless post-mortems. Focus on system and process improvements.

### 3. Incomplete Timelines

**Problem:** Missing key events or having gaps in the timeline.

**Solution:** Correlate logs, metrics, and alerts to build a complete picture.

### 4. Vague Action Items

**Problem:** "Improve monitoring" without specifics.

**Solution:** "Add alert for database connection pool exhaustion with threshold of 80%."

### 5. No Follow-Up

**Problem:** Creating action items that are never completed.

**Solution:** Track action items, assign owners, set deadlines, and follow up.

### 6. Analysis Without Data

**Problem:** Making assumptions without supporting evidence.

**Solution:** Base analysis on logs, metrics, and traces. State assumptions clearly.

### 7. Ignoring Near-Misses

**Problem:** Only analyzing incidents that caused user impact.

**Solution:** Analyze near-misses to prevent future incidents.

### 8. Single Root Cause Bias

**Problem:** Assuming there's only one root cause.

**Solution:** Recognize that incidents often have multiple contributing factors.

### 9. Fixing Symptoms Only

**Problem:** Implementing quick fixes without addressing underlying issues.

**Solution:** Ensure action items address root causes and contributing factors.

### 10. Poor Communication

**Problem:** Incident reports that are too technical or too vague.

**Solution:** Write for your audience. Executive summary for leadership, technical details for engineers.

---

## Examples

### Example 1: Database Connection Pool Exhaustion

**Scenario:** E-commerce site experienced 15-minute outage during peak traffic.

**Incident Summary:**
- **When:** 2024-12-15 14:23 UTC
- **Duration:** 15 minutes
- **Impact:** 100% of users unable to complete checkout
- **Revenue Impact:** ~$50,000 in lost sales

**Timeline:**
```
14:23 - Error rate spike to 95%
14:24 - Alert fired: "High error rate"
14:25 - On-call engineer paged
14:28 - Engineer begins investigation
14:30 - Identified database connection errors
14:32 - Discovered connection pool exhausted
14:35 - Increased connection pool size
14:38 - Service recovered
14:45 - Confirmed full recovery
```

**Root Cause:**
Database connection pool size (50 connections) was insufficient for peak traffic load. A recent code change introduced a connection leak where connections were not being properly returned to the pool.

**Contributing Factors:**
1. No monitoring on connection pool utilization
2. Load testing didn't simulate peak traffic patterns
3. Code review didn't catch connection leak
4. No alerts on database connection errors

**Action Items:**
1. **Immediate (Done):** Increased connection pool size to 200
2. **Short-term (1 week):**
   - Add monitoring for connection pool utilization
   - Add alert for connection pool > 80% utilized
   - Fix connection leak in checkout service
   - Add connection leak detection to code review checklist
3. **Long-term (1 month):**
   - Implement connection pool auto-scaling
   - Update load testing to include peak traffic scenarios
   - Add automated connection leak detection to CI/CD

**Lessons Learned:**
- **What went well:** Fast detection (1 minute), clear escalation, quick mitigation
- **What didn't go well:** No visibility into connection pool, connection leak not caught in review
- **What we learned:** Need better connection pool monitoring and automated leak detection

### Example 2: Cascading Failure from Dependency

**Scenario:** Payment service outage caused by third-party payment gateway failure.

**Incident Summary:**
- **When:** 2024-12-20 09:15 UTC
- **Duration:** 45 minutes
- **Impact:** 80% of payment transactions failed
- **Revenue Impact:** ~$120,000 in delayed transactions

**Timeline:**
```
09:15 - Payment gateway latency increases to 5s
09:17 - Payment service thread pool exhausted
09:18 - Payment service becomes unresponsive
09:19 - Checkout service starts timing out
09:20 - Alert fired: "Payment service unhealthy"
09:22 - On-call engineer paged
09:25 - Engineer begins investigation
09:30 - Identified payment gateway as root cause
09:35 - Enabled circuit breaker to fail fast
09:40 - Increased timeout and thread pool size
09:45 - Switched to backup payment gateway
09:50 - Service fully recovered
10:00 - Confirmed all systems healthy
```

**Root Cause:**
Third-party payment gateway experienced performance degradation. Payment service had no circuit breaker, causing thread pool exhaustion and cascading failure to checkout service.

**Contributing Factors:**
1. No circuit breaker on payment gateway calls
2. Thread pool size too small for degraded dependency
3. No timeout on payment gateway calls
4. No automatic failover to backup gateway
5. Insufficient monitoring on dependency health

**Action Items:**
1. **Immediate (Done):**
   - Enabled circuit breaker
   - Increased thread pool size
   - Added timeout to payment gateway calls
2. **Short-term (2 weeks):**
   - Implement automatic failover to backup gateway
   - Add monitoring for payment gateway latency
   - Add alert for circuit breaker open state
   - Test circuit breaker and failover in staging
3. **Long-term (1 month):**
   - Implement bulkhead pattern for all external dependencies
   - Add chaos engineering tests for dependency failures
   - Create runbook for payment gateway failures

**Lessons Learned:**
- **What went well:** Quick identification of root cause, effective mitigation
- **What didn't go well:** No resilience patterns, cascading failure not prevented
- **What we learned:** All external dependencies need circuit breakers and timeouts

### Example 3: Configuration Change Causing Memory Leak

**Scenario:** Gradual service degradation over 6 hours leading to out-of-memory errors.

**Incident Summary:**
- **When:** 2024-12-22 08:00 UTC (detected at 14:00)
- **Duration:** 6 hours degradation + 30 minutes recovery
- **Impact:** 40% of requests failing, 100% experiencing high latency
- **User Impact:** ~10,000 users affected

**Timeline:**
```
08:00 - Configuration change deployed (cache size increased)
08:30 - Memory usage begins climbing
10:00 - Memory at 60% (normal: 40%)
12:00 - Memory at 80%
13:00 - Memory at 95%
14:00 - Out-of-memory errors begin
14:02 - Alert fired: "High error rate"
14:05 - On-call engineer paged
14:10 - Engineer begins investigation
14:15 - Identified memory leak
14:20 - Rolled back configuration change
14:25 - Restarted affected instances
14:30 - Service recovered
14:45 - Confirmed full recovery
```

**Root Cause:**
Configuration change increased cache size without considering memory constraints. Cache eviction policy was not working correctly, causing unbounded memory growth.

**Contributing Factors:**
1. Configuration change not tested under load
2. No memory limit on cache
3. Cache eviction policy bug not caught
4. No alert on memory usage trends
5. 6-hour delay in detection

**Action Items:**
1. **Immediate (Done):**
   - Rolled back configuration change
   - Restarted services
2. **Short-term (1 week):**
   - Fix cache eviction policy bug
   - Add memory limits to cache configuration
   - Add alert for memory usage > 70%
   - Add alert for memory growth rate
   - Require load testing for configuration changes
3. **Long-term (1 month):**
   - Implement canary deployments for config changes
   - Add automated memory leak detection
   - Create configuration change checklist
   - Improve observability for memory usage

**Lessons Learned:**
- **What went well:** Quick rollback once issue identified
- **What didn't go well:** 6-hour detection delay, configuration change not tested
- **What we learned:** Configuration changes need same rigor as code changes

### Example 4: DNS Resolution Failure

**Scenario:** Intermittent service failures due to DNS resolution issues.

**Incident Summary:**
- **When:** 2024-12-25 16:00 UTC
- **Duration:** 2 hours (intermittent)
- **Impact:** 15% of requests failing intermittently
- **User Impact:** ~5,000 users experienced errors

**Timeline:**
```
16:00 - Intermittent errors begin
16:05 - Alert fired: "Elevated error rate"
16:10 - On-call engineer paged
16:15 - Engineer begins investigation
16:30 - Identified DNS resolution failures
16:35 - Discovered DNS server overloaded
16:40 - Increased DNS cache TTL
16:45 - Added secondary DNS servers
16:50 - Error rate decreased
17:00 - Scaled DNS infrastructure
17:30 - Errors stopped
18:00 - Confirmed full recovery
```

**Root Cause:**
Primary DNS server became overloaded during traffic spike. Services had short DNS cache TTL (5 seconds) and no fallback DNS servers configured.

**Contributing Factors:**
1. Single DNS server (no redundancy)
2. Very short DNS cache TTL
3. No monitoring on DNS server load
4. No fallback DNS configuration
5. DNS resolution not included in load testing

**Action Items:**
1. **Immediate (Done):**
   - Increased DNS cache TTL to 60 seconds
   - Added secondary DNS servers
   - Scaled DNS infrastructure
2. **Short-term (1 week):**
   - Configure multiple DNS servers in all services
   - Add monitoring for DNS resolution latency
   - Add alert for DNS resolution failures
   - Test DNS failover
3. **Long-term (1 month):**
   - Implement local DNS caching in services
   - Add DNS resolution to load testing
   - Create runbook for DNS issues
   - Consider managed DNS service

**Lessons Learned:**
- **What went well:** Identified DNS as root cause, multiple mitigations applied
- **What didn't go well:** No DNS redundancy, short cache TTL amplified problem
- **What we learned:** Infrastructure dependencies need same attention as application dependencies

---

## Related Skills

### Prerequisites
- **observability-design** — need good observability to analyze incidents
- **architecture-discovery** — understanding system architecture helps analysis

### Commonly Followed By
- **root-cause-analysis** — deeper dive into fundamental causes
- **production-readiness** — improve production readiness based on learnings
- **observability-design** — improve monitoring based on gaps found
- **architecture-review** — address architectural issues discovered

### Related Skills
- **disaster-recovery** — planning for major incidents
- **capacity-planning** — addressing capacity-related incidents
- **security-architecture-review** — for security incidents
- **agent-observability** — for agentic system incidents

### Alternative Approaches
- **root-cause-analysis** — for deeper analysis of complex incidents
- **failure-mode-analysis** — for proactive incident prevention

---

## Skill Composition

### Production Incident Response Workflow

```
Incident Occurs
      ↓
Incident Response (resolve)
      ↓
incident-analysis (this skill)
      ↓
root-cause-analysis (if needed)
      ↓
Action Items
      ↓
observability-design (improve monitoring)
      ↓
architecture-review (if architectural issues)
      ↓
production-readiness (validate improvements)
```

### Incident Prevention Workflow

```
incident-analysis (historical incidents)
      ↓
Pattern Identification
      ↓
failure-mode-analysis
      ↓
architecture-review
      ↓
observability-design
      ↓
production-readiness
```

---

## Evaluation Criteria

### Analysis Quality

**Excellent:**
- Root cause clearly identified with supporting evidence
- Contributing factors comprehensively documented
- Timeline is complete and accurate
- Impact is quantified
- Analysis is blameless
- Action items address root causes
- Lessons learned are actionable

**Good:**
- Root cause identified
- Major contributing factors documented
- Timeline is mostly complete
- Impact is estimated
- Action items are defined
- Some lessons learned captured

**Needs Improvement:**
- Root cause is vague or symptom-focused
- Contributing factors missing
- Timeline has gaps
- Impact not quantified
- Action items are vague
- No lessons learned

### Report Quality

**Excellent:**
- Clear, well-organized structure
- Executive summary is concise and informative
- Technical details are accurate
- Supporting data included (graphs, logs)
- Action items are specific with owners and deadlines
- Report is accessible to both technical and non-technical audiences

**Good:**
- Organized structure
- Summary provided
- Technical details included
- Action items defined
- Mostly clear communication

**Needs Improvement:**
- Disorganized or hard to follow
- No summary
- Missing technical details
- Vague action items
- Poor communication

### Follow-Up Effectiveness

**Excellent:**
- All action items completed on time
- Improvements measurably reduce incident risk
- Documentation updated
- Knowledge shared across organization
- Similar incidents prevented

**Good:**
- Most action items completed
- Some improvements implemented
- Some documentation updated
- Knowledge shared with immediate team

**Needs Improvement:**
- Few action items completed
- Minimal improvements
- No documentation updates
- Knowledge not shared
- Similar incidents recur

---

## Tags

`operations`, `incident-response`, `post-mortem`, `reliability`, `observability`, `production`, `debugging`, `analysis`, `root-cause`, `lessons-learned`, `blameless`, `sre`

---

## Version

**1.0.0** — Initial release