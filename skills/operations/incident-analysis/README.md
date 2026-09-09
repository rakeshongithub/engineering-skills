# Incident Analysis

**Systematically analyze production incidents to understand what happened, why it happened, and how to prevent recurrence.**

---

## Quick Start

```bash
# After a production incident:

1. Gather data (logs, metrics, timeline)
2. Construct detailed timeline
3. Assess user and business impact
4. Identify root cause using "5 Whys"
5. Analyze detection and response
6. Define specific action items
7. Document lessons learned
8. Create incident report
9. Conduct post-mortem meeting
10. Track and follow up on action items
```

---

## What This Skill Does

Incident analysis is a structured approach to understanding production incidents and preventing their recurrence. This skill helps you:

- **Understand what happened** — Build a complete, accurate timeline
- **Identify root causes** — Go beyond symptoms to fundamental causes
- **Quantify impact** — Measure user and business impact
- **Prevent recurrence** — Define specific, actionable improvements
- **Build organizational learning** — Capture and share knowledge
- **Improve processes** — Enhance incident response and prevention

---

## When to Use

✅ **Use this skill when:**
- After a production incident or outage
- Investigating service degradation
- Analyzing near-miss events
- Conducting post-mortem reviews
- Identifying patterns across multiple incidents
- Building organizational learning from failures

❌ **Don't use this skill when:**
- During active incident response (resolve first, analyze later)
- For trivial issues (reserve for significant incidents)
- Without sufficient data (ensure logs and metrics are available)
- As blame assignment (focus on system improvement)
- Without stakeholder involvement (include all relevant teams)

---

## Key Concepts

### Blameless Post-Mortems

Focus on **system and process failures**, not individual mistakes. Create psychological safety to encourage honest discussion and learning.

### Root Cause vs. Symptoms

**Symptoms:**
- "Database was slow" ❌
- "High error rate" ❌
- "Service unavailable" ❌

**Root Causes:**
- "Connection leak in checkout service due to missing finally block" ✅
- "Circuit breaker disabled in production configuration" ✅
- "No timeout on third-party API calls" ✅

### The "5 Whys" Technique

Ask "why" repeatedly to drill down from symptoms to root causes:

1. Why did the service fail? → Database connection pool exhausted
2. Why was the pool exhausted? → Connections not being returned
3. Why weren't connections returned? → Missing finally block
4. Why was the finally block missing? → Code review didn't catch it
5. Why didn't code review catch it? → Checklist incomplete

**Root Cause:** Incomplete code review checklist + missing finally block

### Time Metrics

- **TTD (Time to Detect):** Incident start → Detection
- **TTA (Time to Acknowledge):** Detection → Acknowledgment
- **TTI (Time to Investigate):** Acknowledgment → Root cause identified
- **TTM (Time to Mitigate):** Root cause → Mitigation deployed
- **TTR (Time to Resolve):** Incident start → Full resolution

---

## Core Workflow

### 1. Gather Data (30-60 min)

Collect:
- Timeline of events
- System logs from all affected services
- Metrics (CPU, memory, latency, errors)
- Distributed traces (if available)
- User impact data
- Recent changes (deployments, config)
- Alerts and notifications

### 2. Construct Timeline (30-45 min)

Build chronological sequence:
- When was it detected?
- What actions were taken?
- What was the impact at each point?
- When was it resolved?

### 3. Assess Impact (20-30 min)

Quantify:
- Affected users
- Failed transactions
- Revenue impact
- Duration
- SLA/SLO violations

### 4. Identify Root Cause (45-90 min)

Use "5 Whys" to find:
- Primary root cause
- Contributing factors
- Why it wasn't prevented
- Why it wasn't detected earlier

### 5. Analyze Detection and Response (20-30 min)

Evaluate:
- How quickly was it detected?
- Were alerts effective?
- How effective was the response?
- What worked well?
- What could be improved?

### 6. Define Action Items (30-45 min)

Create:
- Immediate fixes (already done)
- Short-term improvements (1-2 weeks)
- Long-term improvements (1-3 months)
- Owners and deadlines for each

### 7. Document Lessons Learned (15-20 min)

Capture:
- What went well
- What didn't go well
- What we learned
- How to improve

### 8. Create Incident Report (45-60 min)

Write:
- Executive summary
- Detailed timeline
- Impact assessment
- Root cause analysis
- Action items
- Lessons learned

### 9. Conduct Post-Mortem Meeting (60-90 min)

Review:
- Present findings
- Discuss action items
- Get feedback
- Ensure alignment

### 10. Track and Follow Up (Ongoing)

Ensure:
- Action items are tracked
- Progress is monitored
- Improvements are measured
- Documentation is updated

---

## Example: Database Connection Pool Exhaustion

**Incident:** E-commerce checkout outage, 15 minutes, $50K lost sales

**Root Cause:** Connection leak in checkout service (missing finally block)

**Contributing Factors:**
- No connection pool monitoring
- Code review didn't catch leak
- Load testing insufficient

**Action Items:**
- ✅ Immediate: Increased pool size, fixed leak
- 🔄 Short-term: Add monitoring, update code review checklist
- 📅 Long-term: Auto-scaling pool, automated leak detection

**Key Learning:** Connection pool monitoring is critical; resource leaks can cause complete outages

---

## Common Mistakes to Avoid

### 1. Stopping at Symptoms

❌ "Database was slow" (symptom)  
✅ "Connection leak caused pool exhaustion" (root cause)

### 2. Blame Culture

❌ "Bob made a mistake"  
✅ "Code review process didn't catch the issue"

### 3. Vague Action Items

❌ "Improve monitoring"  
✅ "Add alert for connection pool > 80% utilized"

### 4. No Follow-Up

❌ Creating action items that are never completed  
✅ Track items, assign owners, set deadlines, follow up

### 5. Incomplete Timelines

❌ Missing key events or having gaps  
✅ Correlate logs, metrics, and alerts for complete picture

---

## Files in This Skill

- **SKILL.md** — Complete skill documentation
- **skill.json** — Machine-readable metadata
- **instructions.md** — Step-by-step workflow
- **examples.md** — Real-world incident examples
- **README.md** — This file (quick reference)

---

## Related Skills

### Prerequisites
- **observability-design** — Need good observability to analyze incidents

### Commonly Followed By
- **root-cause-analysis** — Deeper dive into fundamental causes
- **production-readiness** — Improve readiness based on learnings
- **observability-design** — Improve monitoring based on gaps found
- **architecture-review** — Address architectural issues discovered

### Related
- **disaster-recovery** — Planning for major incidents
- **capacity-planning** — Addressing capacity-related incidents
- **security-architecture-review** — For security incidents

---

## Quality Checklist

**Data Collection:**
- [ ] Complete timeline from detection to resolution
- [ ] Logs from all affected services
- [ ] Metrics covering incident window
- [ ] User impact quantified
- [ ] Recent changes identified

**Analysis Quality:**
- [ ] Root cause is specific and actionable
- [ ] Contributing factors are identified
- [ ] Analysis is blameless
- [ ] Detection and response are evaluated

**Action Items:**
- [ ] Actions address root cause, not just symptoms
- [ ] Each action has a clear owner
- [ ] Deadlines are realistic
- [ ] Actions are prioritized

**Report Quality:**
- [ ] Executive summary is clear and concise
- [ ] Timeline is accurate and complete
- [ ] Impact is quantified
- [ ] Report is blameless and constructive

---

## Getting Started

1. **Read SKILL.md** for comprehensive documentation
2. **Review examples.md** for real-world incident examples
3. **Follow instructions.md** for step-by-step guidance
4. **Use the templates** in examples.md for your incidents
5. **Practice** with past incidents to build the skill

---

## Additional Resources

- Google SRE Book: Postmortem Culture
- Etsy's Debriefing Facilitation Guide
- PagerDuty Incident Response Documentation
- Atlassian Incident Postmortem Template

---

## Tags

`operations` `incident-response` `post-mortem` `reliability` `observability` `production` `debugging` `analysis` `root-cause` `lessons-learned` `blameless` `sre`

---

## Version

**1.0.0** — Initial release (Phase 3)