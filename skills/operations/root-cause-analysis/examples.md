# Root Cause Analysis — Examples

This document provides detailed examples of root cause analysis across different scenarios, demonstrating the application of various RCA techniques.

---

## Example 1: Recurring Database Outages

### Scenario

E-commerce platform experiencing monthly database outages despite multiple attempted fixes. Each outage lasts 10-30 minutes and causes complete service unavailability.

### Problem Statement

```
Problem: Database outages occurring monthly since June 2024
Scope: Production database only (PostgreSQL primary)
Occurrence: 6 incidents over 6 months, always during peak traffic
Impact: 100% service unavailability, ~$50K revenue loss per incident
Pattern: Outages always occur during peak shopping hours (2-4 PM EST)
```

### Data Gathered

**Incident Timeline:**
- June 15: 15-minute outage, connection pool exhaustion
- July 20: 20-minute outage, connection pool exhaustion
- August 10: 30-minute outage, connection pool exhaustion
- September 5: 25-minute outage, connection pool exhaustion
- October 12: 20-minute outage, connection pool exhaustion
- November 18: 15-minute outage, connection pool exhaustion

**Common Factors:**
- All during peak traffic (2-4 PM EST)
- All caused by connection pool exhaustion
- Pool size: 50 connections
- No connection pool monitoring
- Quick fixes: restart service, increase pool temporarily

### 5 Whys Analysis

**Chain 1: Technical Root Cause**

1. **Why do database outages occur monthly?**
   → Because the database connection pool gets exhausted

2. **Why does the connection pool get exhausted?**
   → Because database query result sets are too large and consume all connections

3. **Why are result sets too large?**
   → Because API endpoints don't implement pagination

4. **Why don't API endpoints implement pagination?**
   → Because pagination wasn't in the original requirements

5. **Why wasn't pagination added later when the issue was discovered?**
   → Because there's no process for reviewing and updating API design standards

**Root Cause:** Missing API design review process and lack of pagination standards

---

**Chain 2: Observability Root Cause**

1. **Why do outages occur monthly?**
   → Because connection pool exhaustion isn't detected until it's too late

2. **Why isn't it detected earlier?**
   → Because there's no monitoring on connection pool utilization

3. **Why is there no monitoring on connection pool utilization?**
   → Because connection pool metrics aren't in the standard observability setup

4. **Why aren't they in the standard setup?**
   → Because observability standards don't cover database connection pooling

5. **Why don't standards cover connection pooling?**
   → Because there's no process for updating observability standards based on incidents

**Root Cause:** Incomplete observability standards and no process for continuous improvement

---

**Chain 3: Testing Root Cause**

1. **Why do outages occur?**
   → Because the system can't handle peak traffic load

2. **Why can't it handle peak load?**
   → Because load testing didn't reveal the connection pool issue

3. **Why didn't load testing reveal the issue?**
   → Because load tests don't run long enough (only 30 minutes)

4. **Why don't they run longer?**
   → Because load test duration isn't based on production traffic patterns

5. **Why isn't it based on production patterns?**
   → Because there's no process for aligning load tests with production reality

**Root Cause:** Inadequate load testing process that doesn't match production conditions

### Fishbone Diagram

```
                    Database Outages
                          |
    People                |                Process
      |                   |                   |
      |                   |                   |
  No DB expert           |         No API design review
  Knowledge silos        |         Incomplete code review
  No training            |         Inadequate load testing
      |                   |         No deployment standards
      |                   |                   |
      +-------------------+-------------------+
      |                                       |
      +-------------------+-------------------+
      |                   |                   |
      |                   |                   |
  Technology              |             Environment
      |                   |                   |
  No pagination          |           Peak traffic spikes
  Small pool size        |           Third-party DB (limited control)
  No pool monitoring     |           Growing user base
  No auto-scaling        |           Seasonal patterns
  Missing finally blocks |           
```

### Fault Tree Analysis

```
                    Database Outage
                          |
                         AND
                          |
          +---------------+---------------+
          |                               |
    Connection Pool                  No Resilience
      Exhausted                        Patterns
          |                               |
         OR                          (BASIC EVENT)
          |
    +-----+-----+-----+
    |     |     |     |
  Large  High  Small  No
  Result Traffic Pool Monitor
  Sets   Load  Size  (BASIC)
    |     |     |     
   AND  (BASIC)(BASIC)
    |     
  +-+-+
  | | |
  N N N
  o o o
    | |
  P C M
  a o e
  g d m
  i e o
  n   r
  a L y
  t e
  i a
  o k
  n
  |
(BASIC)
```

**Critical Path:**
Database Outage → Pool Exhausted → Large Result Sets → No Pagination

**Single Points of Failure:**
1. No resilience patterns (circuit breaker, timeout, bulkhead)
2. No connection pool monitoring
3. Single database (no read replicas for queries)

### Systemic Issues Identified

**Technical Issues:**
1. **Missing Resilience Patterns**
   - No circuit breakers
   - No timeouts on database queries
   - No bulkhead pattern (resource isolation)
   - No graceful degradation

2. **Inadequate Observability**
   - No connection pool metrics
   - No query performance monitoring
   - No alerts for approaching capacity
   - No dashboard for database health

3. **API Design Gaps**
   - No pagination on list endpoints
   - No query result size limits
   - No API design standards
   - No performance requirements

**Process Issues:**
1. **No API Design Review**
   - APIs designed without performance review
   - No standards for pagination, filtering, sorting
   - No process for updating existing APIs

2. **Inadequate Load Testing**
   - Tests run for 30 minutes (production patterns are 4+ hours)
   - Tests use different configuration than production
   - No continuous load testing
   - No chaos engineering

3. **Incomplete Code Review**
   - Checklist doesn't cover resource leaks
   - No automated static analysis
   - Reviewers not trained on database best practices

**Organizational Issues:**
1. **Knowledge Silos**
   - Only one person understands database configuration
   - No documentation of connection pool tuning
   - Knowledge not shared during onboarding

2. **Reactive Culture**
   - Quick fixes instead of root cause fixes
   - Firefighting rewarded over prevention
   - No time allocated for reliability improvements

### Root Causes Determined

**Root Cause 1: Missing API Design Review Process**

**Description:** No systematic process for reviewing API design for performance, scalability, and best practices. APIs are designed and implemented without considering pagination, result set size limits, or query performance.

**Evidence:**
- 15 list endpoints without pagination
- No API design standards document
- No performance requirements in API specs
- Developers confirm no review process exists
- Previous incidents also caused by API design issues

**Impact:** High (affects all API development)
**Likelihood:** High (APIs designed daily)
**Scope:** All services with APIs

**Test:** If we implement API design review process with pagination requirements, would it prevent this issue?
**Answer:** Yes, reviewers would require pagination on all list endpoints.

**Priority:** P0

---

**Root Cause 2: No Connection Pool Monitoring**

**Description:** No monitoring or alerting on database connection pool utilization. Issues aren't detected until pool is completely exhausted and service fails.

**Evidence:**
- No connection pool metrics in Datadog
- No alerts for pool utilization
- SRE team confirms monitoring gap
- Would have detected issue 2-3 hours before failure
- Monitoring exists for other resources (CPU, memory) but not connection pool

**Impact:** High (early detection prevents outages)
**Likelihood:** Medium (connection issues are common)
**Scope:** All services using database connection pools

**Test:** If we add connection pool monitoring and alerts, would it prevent outages?
**Answer:** No, but it would detect issues 2-3 hours earlier, allowing proactive mitigation.

**Priority:** P0

---

**Root Cause 3: Inadequate Load Testing Process**

**Description:** Load tests don't match production conditions (duration, configuration, traffic patterns). Issues that manifest over hours aren't caught in 30-minute tests.

**Evidence:**
- Load tests run for 30 minutes (production peak is 4+ hours)
- Load tests use pool size of 20 (production uses 50)
- Connection leak only manifests after 2+ hours
- Load test configuration hasn't been updated in 2 years
- No process for aligning tests with production

**Impact:** High (prevents detection of time-based issues)
**Likelihood:** Medium (load testing happens weekly)
**Scope:** All services

**Test:** If we extend load tests to 4+ hours with production config, would it catch the issue?
**Answer:** Yes, connection pool exhaustion would be detected in load tests.

**Priority:** P0

### Recommendations

**P0 (Critical) — Implement within 1-2 weeks**

**1. Implement API Design Review Process**
- **Owner:** Engineering Manager
- **Deadline:** 2024-12-30
- **Root Cause:** Missing API design review process
- **Description:**
  - Create API design review checklist
  - Include pagination, filtering, sorting, result size limits
  - Require review before implementation
  - Train team on API best practices
- **Success Criteria:**
  - API design review checklist published
  - All new APIs go through review
  - Team trained (100% attendance)
  - Review process documented
- **Effort:** Medium (40 hours)
- **Impact:** High

**2. Add Pagination to All List Endpoints**
- **Owner:** API Team Lead
- **Deadline:** 2025-01-15
- **Root Cause:** Missing API design review process
- **Description:**
  - Audit all list endpoints (15 identified)
  - Implement cursor-based pagination
  - Default page size: 50, max: 100
  - Update API documentation
- **Success Criteria:**
  - All 15 list endpoints have pagination
  - API docs updated
  - Tested with large datasets
  - Deployed to production
- **Effort:** Large (120 hours)
- **Impact:** High

**3. Add Connection Pool Monitoring and Alerting**
- **Owner:** SRE Team Lead
- **Deadline:** 2024-12-22
- **Root Cause:** No connection pool monitoring
- **Description:**
  - Add connection pool utilization metric to Datadog
  - Create dashboard for database health
  - Alert when pool > 80% utilized
  - Alert when pool > 90% utilized (critical)
- **Success Criteria:**
  - Metric visible in Datadog
  - Dashboard created and shared
  - Alerts tested in staging
  - Alerts deployed to production
- **Effort:** Small (16 hours)
- **Impact:** High

**4. Extend Load Test Duration and Configuration**
- **Owner:** QA Lead
- **Deadline:** 2024-12-29
- **Root Cause:** Inadequate load testing process
- **Description:**
  - Extend load tests to 8 hours minimum
  - Use production configuration (pool size, timeouts, etc.)
  - Run tests weekly
  - Monitor connection pool during tests
- **Success Criteria:**
  - Load tests run for 8+ hours
  - Configuration matches production
  - Connection pool monitored during tests
  - Tests detect known issues
- **Effort:** Medium (32 hours)
- **Impact:** High

---

**P1 (High) — Implement within 1-3 months**

**5. Implement Query Result Size Limits**
- **Owner:** Database Team
- **Deadline:** 2025-01-31
- **Description:** Add database-level limits on query result sizes
- **Effort:** Medium (40 hours)
- **Impact:** Medium

**6. Implement Connection Pool Auto-Scaling**
- **Owner:** SRE Team
- **Deadline:** 2025-02-28
- **Description:** Automatically scale pool based on utilization and latency
- **Effort:** Large (80 hours)
- **Impact:** Medium

**7. Add Automated Static Analysis for Resource Leaks**
- **Owner:** DevOps Lead
- **Deadline:** 2025-02-15
- **Description:** Integrate SpotBugs or similar into CI/CD
- **Effort:** Medium (32 hours)
- **Impact:** Medium

**8. Create Database Best Practices Documentation**
- **Owner:** Database Team Lead
- **Deadline:** 2025-02-28
- **Description:** Document connection pool tuning, query optimization, etc.
- **Effort:** Medium (40 hours)
- **Impact:** Medium

---

**P2 (Medium) — Implement within 3-6 months**

**9. Implement Read Replicas for Queries**
- **Owner:** Database Team
- **Deadline:** 2025-03-31
- **Description:** Route read queries to replicas, reduce load on primary
- **Effort:** Large (120 hours)
- **Impact:** Medium

**10. Implement Circuit Breaker for Database Calls**
- **Owner:** Platform Team
- **Deadline:** 2025-04-30
- **Description:** Fail fast when database is unhealthy
- **Effort:** Large (80 hours)
- **Impact:** Low

### RCA Report Executive Summary

```
This root cause analysis examines recurring database outages which occurred 
monthly from June-November 2024 (6 incidents) and resulted in cumulative 
service downtime of 2 hours and ~$300K in lost revenue.

Our analysis using 5 Whys, Fishbone diagrams, and Fault Tree Analysis 
identified 3 primary root causes:
1. Missing API design review process (no pagination requirements)
2. No connection pool monitoring (late detection)
3. Inadequate load testing (doesn't match production conditions)

We have developed 10 recommendations prioritized as P0 (4), P1 (4), and P2 (2) 
to address these root causes and prevent recurrence. Implementation will begin 
December 22, 2024 with completion expected by April 30, 2025.

Key recommendations include:
- Implement API design review process with pagination requirements (P0)
- Add pagination to all 15 list endpoints (P0)
- Add connection pool monitoring and alerting (P0)
- Extend load test duration to 8+ hours with production config (P0)
```

---

## Example 2: Cascading Microservice Failures

### Scenario

Payment processing system experiencing cascading failures where a single slow service causes widespread outages across multiple services.

### Problem Statement

```
Problem: Single service degradation causes system-wide outages
Scope: Payment processing system (8 microservices)
Occurrence: 4 incidents in 3 months, triggered by different services
Impact: 50-100% service unavailability, $200K+ revenue loss per incident
Pattern: Always starts with one service, cascades to others within minutes
```

### 5 Whys Analysis

1. **Why does one slow service cause system-wide outages?**
   → Because calling services wait indefinitely for responses

2. **Why do they wait indefinitely?**
   → Because there are no timeouts configured on service-to-service calls

3. **Why are there no timeouts?**
   → Because timeout configuration isn't in the service template

4. **Why isn't it in the template?**
   → Because resilience patterns aren't part of architecture standards

5. **Why aren't resilience patterns in architecture standards?**
   → Because there's no architecture review process for new services

**Root Causes:**
1. No architecture review process
2. Missing resilience patterns (timeouts, circuit breakers, bulkheads)
3. No chaos engineering to test failure scenarios

### Fault Tree Analysis

```
                    System-Wide Outage
                            |
                           AND
                            |
            +---------------+---------------+
            |                               |
      Service A Slow                   No Resilience
            |                             Patterns
           OR                                |
            |                               AND
    +-------+-------+                        |
    |       |       |               +--------+--------+
  High    Dep   Memory              |        |        |
  Load   Fail   Leak              No      No      No
    |      |       |            Timeout  Circuit Bulkhead
  (BASIC)(BASIC)(BASIC)         (BASIC) Breaker  (BASIC)
                                        (BASIC)
```

**Critical Path:**
System Outage → No Resilience Patterns → No Timeout/Circuit Breaker/Bulkhead

**Single Points of Failure:**
1. No timeouts (threads block indefinitely)
2. No circuit breakers (keep calling failing service)
3. No bulkheads (one slow call exhausts all threads)

### Root Causes Determined

**Root Cause 1: No Architecture Review for Resilience**

**Description:** Services are designed and deployed without review for resilience patterns. No requirement for timeouts, circuit breakers, or bulkheads.

**Evidence:**
- 8 services, none have circuit breakers
- 6 services missing timeouts
- No bulkhead pattern anywhere
- No architecture review checklist
- Developers confirm no review process

**Priority:** P0

---

**Root Cause 2: No Chaos Engineering Testing**

**Description:** Failure scenarios are never tested. Don't know how system behaves when services are slow or unavailable.

**Evidence:**
- No chaos engineering tests
- Never tested service degradation scenarios
- Never tested timeout/circuit breaker behavior
- Would have discovered cascading failure issue

**Priority:** P1

### Recommendations

**P0 Recommendations:**

1. **Implement Timeouts on All Service Calls**
   - Owner: Platform Team
   - Deadline: 2024-12-30
   - Add 5-second timeout to all HTTP calls
   - Add 10-second timeout to all database calls

2. **Implement Circuit Breakers for All External Calls**
   - Owner: Platform Team
   - Deadline: 2025-01-15
   - Use Resilience4j or similar
   - Configure: 50% error rate, 10 calls minimum

3. **Establish Architecture Review Process**
   - Owner: Principal Engineer
   - Deadline: 2024-12-22
   - Create architecture review checklist
   - Include resilience patterns
   - Require review before production

**P1 Recommendations:**

4. **Implement Bulkhead Pattern**
   - Owner: Platform Team
   - Deadline: 2025-02-28
   - Separate thread pools for different operations
   - Prevent resource exhaustion

5. **Implement Chaos Engineering Tests**
   - Owner: SRE Team
   - Deadline: 2025-02-28
   - Monthly chaos tests
   - Test service degradation, failures
   - Validate resilience patterns

---

## Example 3: Memory Leak from Configuration Change

### Scenario

Service experiencing gradual memory growth over 6 hours, leading to out-of-memory errors and crashes. Issue started after a configuration change.

### Problem Statement

```
Problem: Service crashes with OOM errors after 6 hours
Scope: User service (single service)
Occurrence: Started December 1, recurring daily since then
Impact: Service restart required daily, 5-minute downtime each
Pattern: Memory grows linearly, crashes at ~8GB, always after 6 hours
```

### 5 Whys Analysis

1. **Why does the service crash?**
   → Because it runs out of memory (OOM)

2. **Why does it run out of memory?**
   → Because memory usage grows unbounded over time

3. **Why does memory grow unbounded?**
   → Because cache eviction policy has a bug

4. **Why does the cache eviction policy have a bug?**
   → Because configuration change on Dec 1 increased cache size beyond eviction threshold

5. **Why did configuration change cause this?**
   → Because configuration changes aren't tested with same rigor as code changes

**Root Causes:**
1. Configuration changes not tested adequately
2. No memory growth rate monitoring
3. Cache eviction policy bug not caught in code review

### Fishbone Diagram

```
                    Memory Leak / OOM
                          |
    People                |                Process
      |                   |                   |
  Config changed         |         No config testing
  without testing        |         No canary deployment
      |                   |         for config changes
      |                   |                   |
      +-------------------+-------------------+
      |                                       |
      +-------------------+-------------------+
      |                   |                   |
  Technology              |             Environment
      |                   |                   |
  Cache eviction bug     |           Production only
  No memory limit        |           (not in staging)
  No growth monitoring   |           
```

### Root Causes Determined

**Root Cause 1: Configuration Changes Not Tested**

**Description:** Configuration changes are deployed directly to production without testing. No canary deployment, no staging validation.

**Evidence:**
- Configuration deployed directly to production
- No staging environment testing
- No canary deployment for config
- Config changes treated as "low risk"

**Priority:** P0

---

**Root Cause 2: No Memory Growth Rate Monitoring**

**Description:** Memory usage is monitored, but not the rate of growth. Gradual leaks aren't detected until OOM.

**Evidence:**
- Memory usage metric exists
- No alert for growth rate
- Would have detected 6-hour leak pattern
- SRE confirms monitoring gap

**Priority:** P0

### Recommendations

**P0 Recommendations:**

1. **Require Testing for Configuration Changes**
   - Owner: DevOps Lead
   - Deadline: 2024-12-22
   - Test config changes in staging
   - Canary deployment for config
   - Same rigor as code changes

2. **Add Memory Growth Rate Monitoring**
   - Owner: SRE Team
   - Deadline: 2024-12-30
   - Alert when memory grows > 100MB/hour
   - Dashboard showing growth rate

3. **Fix Cache Eviction Bug**
   - Owner: Backend Team
   - Deadline: 2024-12-20
   - Fix eviction threshold logic
   - Add unit tests for eviction
   - Deploy fix

---

## Example 4: Third-Party API Rate Limiting

### Scenario

E-commerce platform experiencing 50% checkout failures due to payment gateway rate limiting.

### Problem Statement

```
Problem: 50% of checkout attempts fail with payment errors
Scope: Checkout service, payment gateway integration
Occurrence: Started during Black Friday sale, continues during high traffic
Impact: $500K+ in lost sales over 3 days
Pattern: Failures correlate with traffic spikes, error: "Rate limit exceeded"
```

### 5 Whys Analysis

1. **Why do checkout attempts fail?**
   → Because payment gateway returns "Rate limit exceeded" errors

2. **Why does the gateway rate limit us?**
   → Because we exceed our API quota during traffic spikes

3. **Why do we exceed the quota?**
   → Because we retry failed requests immediately without backoff

4. **Why do we retry immediately?**
   → Because retry logic doesn't implement exponential backoff

5. **Why doesn't it implement exponential backoff?**
   → Because third-party API integration best practices aren't documented or enforced

**Root Causes:**
1. No third-party API integration standards
2. No rate limit monitoring
3. No circuit breaker for third-party APIs
4. No retry with exponential backoff

### Fault Tree Analysis

```
                    Checkout Failures
                            |
                           AND
                            |
            +---------------+---------------+
            |                               |
      Rate Limit                      No Resilience
       Exceeded                         Patterns
            |                               |
           AND                             AND
            |                               |
    +-------+-------+             +---------+---------+
    |               |             |         |         |
  High          Immediate       No      No      No
  Traffic        Retry       Circuit  Backoff Queue
    |               |        Breaker     |       |
  (BASIC)       (BASIC)     (BASIC) (BASIC) (BASIC)
```

### Root Causes Determined

**Root Cause 1: No Third-Party API Integration Standards**

**Description:** No documented standards for integrating with third-party APIs. Each integration implemented differently.

**Evidence:**
- 5 third-party integrations, all different patterns
- No circuit breakers
- No exponential backoff
- No rate limit handling
- Developers confirm no standards exist

**Priority:** P0

---

**Root Cause 2: No Rate Limit Monitoring**

**Description:** No monitoring of API quota usage or rate limit errors. Don't know we're approaching limits until we hit them.

**Evidence:**
- No metric for rate limit errors
- No dashboard for API quota usage
- No alerts for approaching limits
- Would have detected issue before Black Friday

**Priority:** P0

### Recommendations

**P0 Recommendations:**

1. **Create Third-Party API Integration Standards**
   - Owner: Principal Engineer
   - Deadline: 2024-12-30
   - Document required patterns:
     - Circuit breaker
     - Exponential backoff
     - Rate limit handling
     - Timeout configuration
   - Require for all integrations

2. **Implement Exponential Backoff for Payment Gateway**
   - Owner: Payment Team
   - Deadline: 2024-12-22
   - Retry with exponential backoff: 1s, 2s, 4s, 8s
   - Max 4 retries
   - Jitter to prevent thundering herd

3. **Add Rate Limit Monitoring and Alerting**
   - Owner: SRE Team
   - Deadline: 2024-12-27
   - Monitor rate limit errors
   - Alert when error rate > 5%
   - Dashboard for API quota usage

4. **Implement Circuit Breaker for Payment Gateway**
   - Owner: Payment Team
   - Deadline: 2024-12-29
   - Open circuit after 50% error rate
   - Half-open after 30 seconds
   - Fail fast when circuit open

**P1 Recommendations:**

5. **Implement Request Queue for Rate-Limited APIs**
   - Owner: Platform Team
   - Deadline: 2025-01-31
   - Queue requests when approaching rate limit
   - Process at sustainable rate
   - Prevent quota exhaustion

6. **Negotiate Higher Rate Limits with Payment Gateway**
   - Owner: Product Manager
   - Deadline: 2025-01-15
   - Contact Stripe for enterprise tier
   - Higher rate limits for peak traffic

---

## Template: Blank RCA Report

```markdown
# Root Cause Analysis Report: [TITLE]

## Executive Summary

[2-3 paragraphs summarizing problem, root causes, and key recommendations]

## Problem Statement

```
Problem: [Specific, measurable description]
Scope: [What's included and excluded]
Occurrence: [When and how often]
Impact: [Quantified impact]
Pattern: [Any patterns observed]
```

## Data Gathered

**Incident Timeline:**
- [DATE]: [DESCRIPTION]
- [DATE]: [DESCRIPTION]

**Common Factors:**
- [FACTOR 1]
- [FACTOR 2]

## Analysis

### 5 Whys Analysis

**Chain 1:**

1. **Why [PROBLEM]?**
   → [ANSWER]

2. **Why [ANSWER FROM 1]?**
   → [ANSWER]

3. **Why [ANSWER FROM 2]?**
   → [ANSWER]

4. **Why [ANSWER FROM 3]?**
   → [ANSWER]

5. **Why [ANSWER FROM 4]?**
   → [ANSWER]

**Root Cause:** [ANSWER FROM 5]

### Fishbone Diagram

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

### Fault Tree Analysis

```
[TOP EVENT]
    |
  [GATE]
    |
  [SUB-EVENTS]
```

### Systemic Issues

**Technical Issues:**
- [ISSUE 1]
- [ISSUE 2]

**Process Issues:**
- [ISSUE 1]
- [ISSUE 2]

**Organizational Issues:**
- [ISSUE 1]
- [ISSUE 2]

## Root Causes

### Root Cause 1: [NAME]

**Description:** [DETAILED EXPLANATION]

**Evidence:**
- [EVIDENCE 1]
- [EVIDENCE 2]

**Impact:** [HIGH/MEDIUM/LOW]
**Likelihood:** [HIGH/MEDIUM/LOW]
**Scope:** [DESCRIPTION]
**Priority:** [P0/P1/P2]

---

[REPEAT FOR EACH ROOT CAUSE]

## Recommendations

### P0 (Critical) — Implement within 1-2 weeks

**1. [RECOMMENDATION TITLE]**
- **Owner:** [NAME]
- **Deadline:** [DATE]
- **Root Cause:** [WHICH ROOT CAUSE]
- **Description:** [DETAILED DESCRIPTION]
- **Success Criteria:**
  - [CRITERIA 1]
  - [CRITERIA 2]
- **Effort:** [SMALL/MEDIUM/LARGE]
- **Impact:** [HIGH/MEDIUM/LOW]

---

[REPEAT FOR EACH RECOMMENDATION]

## Implementation Plan

[TIMELINE AND MILESTONES]

## Appendix

[SUPPORTING DATA, DIAGRAMS, REFERENCES]
```

---

## Additional Example Scenarios

### Scenario 5: Deployment Pipeline Failures

**Summary:** 40% of deployments fail or require rollback due to inadequate testing and manual deployment steps.

**Root Causes:**
1. Manual deployment process (error-prone)
2. Staging environment doesn't match production
3. No deployment best practices documentation

**Key Recommendations:**
- Automate deployment process (P0)
- Make staging match production (P0)
- Implement canary deployments (P1)

---

### Scenario 6: Data Inconsistency Issues

**Summary:** Customer data inconsistencies between services due to eventual consistency without proper conflict resolution.

**Root Causes:**
1. No distributed transaction strategy
2. No conflict resolution logic
3. No data validation across services

**Key Recommendations:**
- Implement saga pattern for distributed transactions (P0)
- Add conflict resolution logic (P0)
- Add cross-service data validation (P1)

---

### Scenario 7: Security Incident - Unauthorized Access

**Summary:** Unauthorized access to customer data due to weak authentication and missing security review.

**Root Causes:**
1. No MFA requirement
2. No security review before production
3. No security champion or ownership

**Key Recommendations:**
- Implement MFA for all accounts (P0)
- Establish security review process (P0)
- Assign security champion (P1)

---

### Scenario 8: Performance Degradation

**Summary:** Gradual performance degradation over 3 months due to N+1 query problem and missing database indexes.

**Root Causes:**
1. No query performance monitoring
2. No database query review in code review
3. No automated performance testing

**Key Recommendations:**
- Add query performance monitoring (P0)
- Add database query review to checklist (P0)
- Implement automated performance tests (P1)

---

## Using These Examples

### For Learning

1. **Study the Analysis Techniques**
   - See how 5 Whys, Fishbone, and Fault Tree complement each other
   - Understand how to identify root causes vs. symptoms
   - Learn how to prioritize recommendations

2. **Understand Patterns**
   - Notice common root causes across examples
   - Recognize systemic issues
   - Identify prevention strategies

3. **Practice**
   - Apply techniques to past incidents
   - Create your own RCA reports
   - Share and get feedback

### For Templates

1. **Use the Blank Template**
   - Copy structure for your RCAs
   - Adapt to your context
   - Maintain consistency

2. **Adapt Examples**
   - Use similar scenarios as starting point
   - Modify for your specific situation
   - Keep what works, change what doesn't

### For Training

1. **Team Workshops**
   - Walk through examples together
   - Practice analysis techniques
   - Discuss root causes and recommendations

2. **Case Studies**
   - Use examples as case studies
   - Have team identify root causes
   - Compare with provided analysis

3. **Onboarding**
   - Share examples with new team members
   - Explain RCA process
   - Set expectations for quality

---

## References

- Google SRE Book: Postmortem Culture
- "The Field Guide to Understanding Human Error" by Sidney Dekker
- "Site Reliability Engineering" by Google
- NASA Root Cause Analysis Guide
- FAA System Safety Handbook
