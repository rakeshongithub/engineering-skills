# Incident Analysis — Step-by-Step Instructions

This guide provides detailed instructions for conducting systematic incident analysis.

---

## Overview

Incident analysis is performed after a production incident to understand what happened, why it happened, and how to prevent it from happening again. The process should be blameless and focus on system and process improvements.

**Time Required:** 2-6 hours depending on incident complexity

**Participants:**
- Incident responders
- Service owners
- On-call engineers
- Engineering leadership (for severe incidents)

---

## Step 1: Gather Data (30-60 minutes)

### What to Collect

1. **Timeline Data**
   - When was the incident detected?
   - When did it actually start?
   - When was it resolved?
   - What actions were taken and when?

2. **System Logs**
   ```bash
   # Example: Collect logs from incident window
   kubectl logs <pod-name> --since=2h > incident-logs.txt
   
   # Or from logging system
   # Datadog, Splunk, CloudWatch, etc.
   ```

3. **Metrics Data**
   - CPU usage
   - Memory usage
   - Request latency
   - Error rates
   - Database performance
   - Network metrics

4. **Distributed Traces** (if available)
   - Request flows
   - Service dependencies
   - Latency breakdown

5. **User Impact Data**
   - Number of affected users
   - Failed transactions
   - Revenue impact
   - Geographic distribution

6. **Recent Changes**
   ```bash
   # Check recent deployments
   kubectl rollout history deployment/<name>
   
   # Check git commits
   git log --since="2 days ago" --oneline
   ```

7. **Alerts and Notifications**
   - What alerts fired?
   - When did they fire?
   - Who was notified?

### Quality Checks

- [ ] Complete timeline from detection to resolution
- [ ] Logs from all affected services
- [ ] Metrics covering the incident window
- [ ] User impact quantified
- [ ] Recent changes identified

---

## Step 2: Construct Timeline (30-45 minutes)

### Timeline Template

```
[TIME] - [EVENT] - [METRIC/IMPACT] - [ACTION TAKEN]

Example:
14:23 UTC - Error rate spike to 95% - 1000 req/s failing - Alert fired
14:24 UTC - Alert: "High error rate" - - On-call paged
14:25 UTC - On-call engineer acknowledged - - Investigation started
14:30 UTC - Database connection errors identified - 50/50 connections used - 
14:32 UTC - Connection pool exhausted - - 
14:35 UTC - Connection pool size increased to 200 - - Mitigation deployed
14:38 UTC - Error rate dropped to 5% - 50 req/s failing - 
14:45 UTC - Error rate at 0% - - Full recovery confirmed
```

### Key Elements to Include

1. **Detection**
   - When was the incident first detected?
   - How was it detected? (alert, user report, monitoring)

2. **Escalation**
   - When was on-call paged?
   - When did investigation begin?
   - When were additional teams engaged?

3. **Investigation**
   - Key findings during investigation
   - Hypotheses tested
   - Decision points

4. **Mitigation**
   - Actions taken to resolve
   - When each action was taken
   - Effect of each action

5. **Resolution**
   - When was service restored?
   - When was full recovery confirmed?

### Quality Checks

- [ ] All times are in the same timezone (prefer UTC)
- [ ] Events are in chronological order
- [ ] Key decision points are documented
- [ ] Response actions are clearly noted
- [ ] User impact is tracked throughout

---

## Step 3: Assess Impact (20-30 minutes)

### Quantify User Impact

```
Affected Users: [NUMBER]
Failed Transactions: [NUMBER]
Duration: [TIME]
Geographic Impact: [REGIONS]
User-Facing Errors: [DESCRIPTION]
```

### Quantify Business Impact

```
Revenue Impact: $[AMOUNT]
SLA Violations: [YES/NO]
SLO Violations: [WHICH ONES]
Reputation Impact: [ASSESSMENT]
Customer Support Tickets: [NUMBER]
```

### Measure Response Metrics

```
Time to Detect (TTD): [TIME from incident start to detection]
Time to Acknowledge (TTA): [TIME from detection to acknowledgment]
Time to Investigate (TTI): [TIME from acknowledgment to root cause identified]
Time to Mitigate (TTM): [TIME from root cause to mitigation deployed]
Time to Resolve (TTR): [TIME from incident start to full resolution]
```

### Quality Checks

- [ ] User impact is quantified
- [ ] Business impact is estimated
- [ ] Duration is clearly stated
- [ ] SLA/SLO impact is documented
- [ ] Detection and resolution times are calculated

---

## Step 4: Identify Root Cause (45-90 minutes)

### Use the "5 Whys" Technique

**Example:**

1. **Why did the service fail?**
   - Because the database connection pool was exhausted

2. **Why was the connection pool exhausted?**
   - Because connections were not being returned to the pool

3. **Why were connections not being returned?**
   - Because of a connection leak in the checkout service

4. **Why was there a connection leak?**
   - Because the finally block was missing in the database access code

5. **Why was the missing finally block not caught?**
   - Because code review checklist didn't include connection leak checks

**Root Cause:** Missing finally block in database access code + inadequate code review process

### Distinguish Symptoms from Root Causes

**Symptoms:**
- High error rate ❌ (symptom)
- Slow response time ❌ (symptom)
- Database connection errors ❌ (symptom)

**Root Causes:**
- Connection leak in checkout service ✓
- Missing finally block in database code ✓
- Inadequate code review checklist ✓

### Identify Contributing Factors

1. **Technical Factors**
   - Code defects
   - Configuration errors
   - Infrastructure issues
   - Dependency failures

2. **Process Factors**
   - Inadequate testing
   - Poor deployment process
   - Insufficient monitoring
   - Inadequate documentation

3. **Organizational Factors**
   - Knowledge gaps
   - Communication failures
   - Unclear ownership
   - Insufficient resources

### Quality Checks

- [ ] Root cause is specific and actionable
- [ ] Contributing factors are identified
- [ ] Analysis goes beyond symptoms
- [ ] Safeguard failures are explained
- [ ] Analysis is blameless

---

## Step 5: Analyze Detection and Response (20-30 minutes)

### Detection Analysis

**Questions to Answer:**
- How was the incident detected?
- How long did it take to detect?
- Why wasn't it detected earlier?
- Were the right alerts in place?
- Did alerts fire correctly?
- Were alerts actionable?

**Example Analysis:**
```
Detection Method: Automated alert
Time to Detect: 1 minute (excellent)
Alert Quality: Clear and actionable
Gaps: No alert on connection pool utilization (would have detected earlier)
```

### Response Analysis

**Questions to Answer:**
- How quickly was the incident acknowledged?
- How effective was the escalation process?
- Were runbooks available and helpful?
- What worked well during response?
- What could be improved?
- Was communication effective?

**Example Analysis:**
```
Acknowledgment Time: 2 minutes (good)
Escalation: Effective, right people engaged
Runbooks: No runbook for this scenario
Communication: Good internal, poor external
What Worked: Fast detection, clear ownership
What Didn't: No runbook, connection leak not in code review checklist
```

### Quality Checks

- [ ] Detection time is measured
- [ ] Monitoring gaps are identified
- [ ] Response effectiveness is evaluated
- [ ] Communication is assessed
- [ ] Process improvements are identified

---

## Step 6: Define Action Items (30-45 minutes)

### Action Item Template

```
[P0/P1/P2/P3] [SHORT DESCRIPTION]
Owner: [NAME]
Deadline: [DATE]
Description: [DETAILED DESCRIPTION]
Success Criteria: [HOW TO VERIFY]
```

### Categorize Actions

**1. Immediate (Already Done)**
```
✓ Increased connection pool size to 200
✓ Restarted affected services
✓ Fixed connection leak in checkout service
```

**2. Short-term (1-2 weeks)**
```
P0 Add monitoring for connection pool utilization
   Owner: Alice
   Deadline: 2024-12-30
   Description: Add metric for connection pool usage, alert at 80%
   Success: Alert fires in staging when pool reaches 80%

P0 Add connection leak check to code review checklist
   Owner: Bob
   Deadline: 2024-12-30
   Description: Update checklist, train team
   Success: Checklist updated, team trained
```

**3. Long-term (1-3 months)**
```
P1 Implement connection pool auto-scaling
   Owner: Charlie
   Deadline: 2025-01-31
   Description: Automatically scale pool based on load
   Success: Pool scales automatically in production

P1 Add automated connection leak detection to CI/CD
   Owner: Diana
   Deadline: 2025-01-31
   Description: Static analysis tool to detect leaks
   Success: Tool catches known leak patterns in CI
```

### Ensure Actions Address Root Cause

❌ **Bad:** "Improve monitoring"
✓ **Good:** "Add alert for database connection pool utilization > 80%"

❌ **Bad:** "Better code review"
✓ **Good:** "Add connection leak detection to code review checklist"

### Quality Checks

- [ ] Actions are specific and measurable
- [ ] Each action has an owner
- [ ] Deadlines are realistic
- [ ] Actions address root cause
- [ ] Monitoring improvements are included
- [ ] Actions are prioritized

---

## Step 7: Document Lessons Learned (15-20 minutes)

### Template

```markdown
## Lessons Learned

### What Went Well
- Fast detection (1 minute from incident start)
- Clear escalation process
- Quick mitigation once root cause identified
- Good communication within engineering team

### What Didn't Go Well
- No monitoring on connection pool utilization
- Connection leak not caught in code review
- No runbook for database connection issues
- Delayed external communication to users

### What We Learned
- Connection pool monitoring is critical
- Code review checklists need to be comprehensive
- Runbooks should cover common failure modes
- External communication needs improvement

### How to Improve
- Add connection pool monitoring and alerts
- Update code review checklist
- Create runbook for database issues
- Improve external communication process
```

### Quality Checks

- [ ] Positive aspects are recognized
- [ ] Improvement areas are identified
- [ ] Knowledge is captured
- [ ] Documentation is updated
- [ ] Learning is shared

---

## Step 8: Create Incident Report (45-60 minutes)

### Report Structure

```markdown
# Incident Report: [TITLE]

## Executive Summary
[2-3 paragraphs for leadership]

## Incident Details
- **Date/Time:** [START] to [END]
- **Duration:** [DURATION]
- **Severity:** [SEV-1/2/3/4]
- **Services Affected:** [LIST]
- **User Impact:** [DESCRIPTION]

## Timeline
[DETAILED TIMELINE]

## Impact Assessment
[USER AND BUSINESS IMPACT]

## Root Cause Analysis
[ROOT CAUSE AND CONTRIBUTING FACTORS]

## Resolution
[HOW IT WAS RESOLVED]

## Action Items
[PRIORITIZED LIST WITH OWNERS AND DEADLINES]

## Lessons Learned
[WHAT WENT WELL, WHAT DIDN'T, WHAT WE LEARNED]

## Appendix
[SUPPORTING DATA: GRAPHS, LOGS, METRICS]
```

### Executive Summary Example

```
On December 15, 2024 at 14:23 UTC, our e-commerce platform experienced 
a 15-minute outage affecting 100% of users attempting to complete checkout. 
The incident was caused by database connection pool exhaustion due to a 
connection leak in the checkout service. The issue was detected within 
1 minute via automated alerts, and service was restored within 15 minutes 
by increasing the connection pool size and fixing the connection leak.

Approximately 10,000 users were impacted, resulting in an estimated 
$50,000 in lost sales. We have implemented immediate fixes and identified 
5 action items to prevent recurrence, including improved monitoring, 
code review enhancements, and automated leak detection.
```

### Quality Checks

- [ ] Report is clear and well-organized
- [ ] Executive summary is concise
- [ ] Timeline is accurate and complete
- [ ] Root cause is clearly explained
- [ ] Action items are specific
- [ ] Report is blameless

---

## Step 9: Conduct Post-Mortem Meeting (60-90 minutes)

### Meeting Agenda

```
1. Introduction (5 min)
   - Purpose of meeting
   - Blameless culture reminder

2. Incident Overview (10 min)
   - What happened
   - Impact
   - Timeline

3. Root Cause Analysis (15 min)
   - Root cause
   - Contributing factors
   - Why it wasn't prevented
   - Why it wasn't detected earlier

4. Response Analysis (10 min)
   - What went well
   - What didn't go well
   - Lessons learned

5. Action Items (15 min)
   - Review proposed actions
   - Discuss priorities
   - Confirm owners and deadlines

6. Discussion (20 min)
   - Questions
   - Additional insights
   - Feedback on analysis

7. Next Steps (5 min)
   - Action item tracking
   - Follow-up meetings
   - Documentation updates
```

### Facilitation Tips

1. **Set the Tone**
   - Emphasize blameless culture
   - Focus on system and process improvement
   - Encourage open discussion

2. **Keep It Focused**
   - Stick to the agenda
   - Table unrelated discussions
   - Manage time carefully

3. **Encourage Participation**
   - Ask for input from all attendees
   - Create psychological safety
   - Value all perspectives

4. **Document Decisions**
   - Capture additional insights
   - Update action items as needed
   - Note any changes to analysis

### Quality Checks

- [ ] All stakeholders invited
- [ ] Blameless culture emphasized
- [ ] Action items reviewed and confirmed
- [ ] Additional insights captured
- [ ] Follow-up plan established

---

## Step 10: Track and Follow Up (Ongoing)

### Action Item Tracking

**Use a tracking system:**
- Jira, Linear, GitHub Issues, etc.
- Create tickets for each action item
- Assign owners
- Set deadlines
- Track progress

**Example Jira Ticket:**
```
Title: Add connection pool monitoring and alerting
Type: Incident Follow-up
Priority: P0
Owner: Alice
Due Date: 2024-12-30

Description:
Add monitoring for database connection pool utilization and create 
alert when utilization exceeds 80%.

Acceptance Criteria:
- [ ] Metric added for connection pool utilization
- [ ] Alert configured for > 80% utilization
- [ ] Alert tested in staging
- [ ] Alert deployed to production
- [ ] Runbook updated with alert response

Related Incident: INC-2024-12-15-001
```

### Follow-Up Reviews

**1-Week Follow-Up:**
- Review short-term action items
- Confirm immediate fixes are working
- Address any new issues

**1-Month Follow-Up:**
- Review long-term action items
- Measure effectiveness of improvements
- Update documentation

**3-Month Follow-Up:**
- Confirm all action items completed
- Measure incident recurrence
- Assess overall improvement

### Measure Effectiveness

**Metrics to Track:**
- Time to detect similar incidents
- Time to resolve similar incidents
- Frequency of similar incidents
- Effectiveness of new monitoring
- Effectiveness of new processes

### Quality Checks

- [ ] All action items are tracked
- [ ] Owners are accountable
- [ ] Progress is monitored
- [ ] Follow-up reviews are scheduled
- [ ] Effectiveness is measured
- [ ] Documentation is updated

---

## Tools and Templates

### Incident Report Template

See `examples.md` for complete incident report templates.

### Timeline Visualization Tools

- Mermaid diagrams
- Lucidchart
- Draw.io
- Google Sheets

### Collaboration Tools

- Google Docs (for collaborative editing)
- Confluence (for documentation)
- Slack/Teams (for communication)
- Zoom/Meet (for post-mortem meetings)

### Monitoring and Logging

- Datadog, New Relic, Dynatrace (APM)
- Splunk, ELK, CloudWatch (Logging)
- Prometheus, Grafana (Metrics)
- Jaeger, Zipkin (Tracing)

---

## Common Pitfalls to Avoid

1. **Stopping at Symptoms**
   - Don't stop at "database was slow"
   - Ask "why" until you reach root cause

2. **Blame Culture**
   - Focus on system failures, not people
   - Create psychological safety

3. **Incomplete Data**
   - Gather all relevant logs and metrics
   - Don't rely on memory

4. **Vague Action Items**
   - Be specific and measurable
   - Assign owners and deadlines

5. **No Follow-Up**
   - Track action items
   - Measure effectiveness

6. **Analysis Paralysis**
   - Set time limits
   - Focus on actionable insights

7. **Poor Communication**
   - Write for your audience
   - Use clear language

8. **Ignoring Near-Misses**
   - Analyze incidents that almost happened
   - Prevent future incidents

---

## Success Criteria

You've successfully completed incident analysis when:

- [ ] Root cause is clearly identified with supporting evidence
- [ ] Contributing factors are documented
- [ ] Timeline is complete and accurate
- [ ] Impact is quantified
- [ ] Action items are specific with owners and deadlines
- [ ] Lessons learned are captured
- [ ] Incident report is published
- [ ] Post-mortem meeting is conducted
- [ ] Action items are being tracked
- [ ] Follow-up reviews are scheduled
- [ ] Documentation is updated
- [ ] Knowledge is shared across the organization

---

## Next Steps

After completing incident analysis:

1. **Immediate:**
   - Publish incident report
   - Create action item tickets
   - Update documentation

2. **Short-term:**
   - Complete short-term action items
   - Conduct 1-week follow-up
   - Share learnings with team

3. **Long-term:**
   - Complete long-term action items
   - Measure effectiveness
   - Update processes and runbooks
   - Consider related skills:
     - **root-cause-analysis** for deeper analysis
     - **observability-design** to improve monitoring
     - **production-readiness** to prevent similar incidents

---

## Additional Resources

- Google SRE Book: Postmortem Culture
- Etsy's Debriefing Facilitation Guide
- PagerDuty Incident Response Documentation
- Atlassian Incident Postmortem Template
- NIST Guide to Incident Handling