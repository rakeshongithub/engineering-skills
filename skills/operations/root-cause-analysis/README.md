# Root Cause Analysis

**Perform deep, systematic analysis to identify fundamental causes of problems beyond immediate symptoms.**

---

## Quick Start

```bash
# For complex or recurring problems:

1. Define the problem clearly
2. Gather comprehensive data
3. Build timeline and sequence
4. Apply "5 Whys" analysis
5. Create Fishbone diagram
6. Perform Fault Tree analysis
7. Identify systemic issues
8. Determine root causes
9. Develop recommendations
10. Create RCA report and present
```

---

## What This Skill Does

Root Cause Analysis (RCA) goes deeper than incident analysis to identify fundamental, systemic causes of problems. This skill helps you:

- **Find root causes** — Not just symptoms or proximate causes
- **Understand systems** — Technical, process, and organizational factors
- **Prevent recurrence** — Address fundamental issues
- **Drive systemic improvement** — Fix underlying problems
- **Build organizational learning** — Understand patterns and trends

---

## When to Use

✅ **Use this skill when:**
- Complex or recurring incidents
- Incident analysis doesn't reveal clear root cause
- Investigating systemic problems
- Multiple related incidents occur
- Simple fixes don't prevent recurrence

❌ **Don't use this skill when:**
- Simple, one-time incidents (use incident-analysis)
- During active incident response
- Without sufficient data
- For blame assignment
- Without time for deep analysis

---

## Key Techniques

### 1. "5 Whys" Analysis

Ask "why" repeatedly to drill down:

```
1. Why did the service fail?
   → Database connection pool exhausted

2. Why was the pool exhausted?
   → Connections not being returned

3. Why weren't connections returned?
   → Missing finally block in code

4. Why was the finally block missing?
   → Code review didn't catch it

5. Why didn't code review catch it?
   → Incomplete code review checklist

Root Cause: Incomplete code review process
```

### 2. Fishbone (Ishikawa) Diagram

Categorize causes:

```
                    Problem
                       |
        People         |         Process
          |            |            |
          +------------+------------+
          |                         |
          +------------+------------+
          |            |            |
      Technology       |       Environment
```

### 3. Fault Tree Analysis

Logical analysis of failure paths:

```
System Failure
    |
    AND
    |
    +-- Component A fails
    |   |
    |   OR
    |   |
    |   +-- Cause 1
    |   +-- Cause 2
    |
    +-- No redundancy
```

---

## Root Cause vs. Contributing Factor

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

---

## Common Mistakes

### 1. Stopping Too Early

❌ "Service crashed" (proximate cause)  
✅ "No memory limits + memory leak + inadequate testing" (root causes)

### 2. Single Root Cause Bias

❌ Assuming there's only one root cause  
✅ Complex problems usually have multiple root causes

### 3. Blame Culture

❌ "Alice made a mistake"  
✅ "Code review process didn't catch the issue"

### 4. Vague Recommendations

❌ "Improve testing"  
✅ "Add integration tests for payment flow with 80% coverage"

---

## Example: Recurring Database Outages

**Problem:** Database outages occurring monthly despite fixes

**5 Whys:**
1. Why outages? → Database runs out of memory
2. Why out of memory? → Query result sets too large
3. Why too large? → No pagination on API endpoints
4. Why no pagination? → Not in original requirements
5. Why not added later? → No process for API design review

**Root Causes:**
1. Missing API design review process
2. No pagination requirements in API standards
3. No memory limits on database queries

**Recommendations:**
- P0: Implement API design review process
- P0: Add pagination to all list endpoints
- P1: Create API design standards
- P1: Implement query memory limits

---

## Files in This Skill

- **SKILL.md** — Complete skill documentation
- **skill.json** — Machine-readable metadata
- **instructions.md** — Step-by-step workflow
- **examples.md** — Real-world RCA examples
- **README.md** — This file (quick reference)

---

## Related Skills

### Prerequisites
- **incident-analysis** — Provides initial incident data
- **architecture-discovery** — Understanding system structure

### Commonly Followed By
- **architecture-review** — Address architectural root causes
- **production-readiness** — Improve production readiness
- **observability-design** — Improve detection
- **testing-strategy** — Improve testing

---

## Quality Checklist

**Problem Definition:**
- [ ] Problem is clearly stated
- [ ] Scope is well-defined
- [ ] Impact is quantified

**Analysis:**
- [ ] Multiple techniques used
- [ ] Root causes are fundamental
- [ ] Evidence-based conclusions
- [ ] Systemic issues identified

**Recommendations:**
- [ ] Address root causes
- [ ] Specific and actionable
- [ ] Prioritized by impact
- [ ] Owners assigned

---

## Getting Started

1. **Read SKILL.md** for comprehensive documentation
2. **Review examples.md** for real-world RCA examples
3. **Follow instructions.md** for step-by-step guidance
4. **Practice** with past incidents

---

## Tags

`operations` `analysis` `root-cause` `incident-response` `problem-solving` `systemic-improvement` `5-whys` `fishbone` `fault-tree` `reliability` `sre`

---

## Version

**1.0.0** — Initial release (Phase 3)