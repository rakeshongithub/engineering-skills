# Incident Analysis — Examples

This document provides detailed examples of incident analysis across different scenarios.

---

## Example 1: Database Connection Pool Exhaustion

### Scenario

E-commerce platform experienced complete checkout outage during peak holiday shopping period.

### Incident Report

```markdown
# Incident Report: Checkout Service Outage

## Executive Summary

On December 15, 2024 at 14:23 UTC, our e-commerce platform experienced a 
15-minute complete outage of the checkout service, affecting 100% of users 
attempting to complete purchases. The incident was caused by database 
connection pool exhaustion due to a connection leak in the checkout service 
introduced in a recent deployment.

The issue was detected within 1 minute via automated error rate alerts. 
Service was restored within 15 minutes through emergency connection pool 
size increase and hotfix deployment to fix the connection leak.

Approximately 10,000 users were directly impacted during peak holiday 
shopping, resulting in an estimated $50,000 in lost sales and 247 customer 
support tickets. We have implemented immediate fixes and identified 6 action 
items to prevent recurrence.

## Incident Details

- **Incident ID:** INC-2024-12-15-001
- **Date/Time:** December 15, 2024, 14:23 - 14:38 UTC
- **Duration:** 15 minutes
- **Severity:** SEV-1 (Critical)
- **Services Affected:** Checkout Service, Payment Service
- **User Impact:** 100% of checkout attempts failed
- **Business Impact:** ~$50,000 in lost sales

## Timeline

14:23:00 UTC - Error rate spike from 0.1% to 95%
14:23:15 UTC - Alert fired: "Checkout Service - High Error Rate"
14:23:30 UTC - Alert fired: "Checkout Service - Unhealthy"
14:24:00 UTC - On-call engineer (Alice) paged
14:25:00 UTC - Alice acknowledges and begins investigation
14:26:00 UTC - Customer support reports checkout failures
14:28:00 UTC - Alice identifies database connection errors in logs
14:29:00 UTC - Database team engaged
14:30:00 UTC - Connection pool utilization at 100% (50/50 connections)
14:32:00 UTC - Connection leak suspected
14:33:00 UTC - Recent deployment identified as potential cause
14:35:00 UTC - Emergency mitigation: Connection pool size increased to 200
14:36:00 UTC - Error rate drops to 20%
14:37:00 UTC - Hotfix deployed: Fixed connection leak
14:38:00 UTC - Error rate drops to 0%
14:40:00 UTC - Monitoring confirms full recovery
14:45:00 UTC - Incident declared resolved
15:00:00 UTC - Post-incident monitoring continues

## Impact Assessment

### User Impact
- **Affected Users:** ~10,000 users
- **Failed Transactions:** ~2,500 checkout attempts
- **Geographic Distribution:** Global (all regions)
- **User Experience:** Complete inability to complete checkout
- **Customer Support:** 247 tickets filed

### Business Impact
- **Revenue Impact:** ~$50,000 in lost sales (estimated)
- **Average Order Value:** $20
- **Failed Orders:** ~2,500
- **SLA Violation:** Yes (99.9% uptime SLA)
- **Reputation Impact:** Moderate (during peak shopping period)

### Technical Impact
- **Services Affected:** Checkout Service (primary), Payment Service (secondary)
- **Database Impact:** Connection pool exhausted
- **Monitoring:** Alerts functioned correctly
- **Time to Detect:** 1 minute (excellent)
- **Time to Resolve:** 15 minutes (good)

## Root Cause Analysis

### Primary Root Cause

A connection leak was introduced in the checkout service in deployment v2.3.1 
on December 15, 2024 at 10:00 UTC. The leak was caused by a missing `finally` 
block in the database access code for the `processPayment()` method.

**Code Issue:**
```java
// BEFORE (with leak)
public void processPayment(Payment payment) {
    Connection conn = dataSource.getConnection();
    try {
        // Process payment
        paymentDAO.save(conn, payment);
    } catch (SQLException e) {
        logger.error("Payment processing failed", e);
        throw new PaymentException(e);
    }
    // Missing: conn.close() in finally block
}

// AFTER (fixed)
public void processPayment(Payment payment) {
    Connection conn = null;
    try {
        conn = dataSource.getConnection();
        paymentDAO.save(conn, payment);
    } catch (SQLException e) {
        logger.error("Payment processing failed", e);
        throw new PaymentException(e);
    } finally {
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                logger.error("Failed to close connection", e);
            }
        }
    }
}
```

### Contributing Factors

1. **Inadequate Code Review**
   - Code review checklist did not include connection leak detection
   - Reviewer did not catch missing finally block
   - No automated static analysis for resource leaks

2. **Insufficient Monitoring**
   - No monitoring on database connection pool utilization
   - No alert for connection pool approaching capacity
   - Would have detected issue before complete exhaustion

3. **Inadequate Load Testing**
   - Load tests did not run long enough to detect slow leak
   - Load tests used smaller connection pool than production
   - Connection leak only manifests under sustained load

4. **Small Connection Pool**
   - Connection pool size (50) was too small for peak traffic
   - Even without leak, pool was operating near capacity
   - No auto-scaling of connection pool

5. **Deployment During Peak Period**
   - Deployment occurred 4 hours before peak shopping period
   - Leak had time to accumulate before peak traffic
   - Should have frozen deployments before peak period

### Why It Wasn't Prevented

- Code review process did not catch the connection leak
- No automated static analysis for resource leaks
- Load testing was insufficient (duration and pool size)
- No deployment freeze before peak shopping period

### Why It Wasn't Detected Earlier

- No monitoring on connection pool utilization
- Leak was slow (accumulated over 4 hours)
- Only became critical during peak traffic
- No alerts on database connection errors until critical threshold

## Resolution

### Immediate Actions (During Incident)

1. **14:35 UTC - Emergency Mitigation**
   - Increased connection pool size from 50 to 200
   - Provided immediate relief (error rate dropped to 20%)
   - Bought time to deploy proper fix

2. **14:37 UTC - Hotfix Deployment**
   - Deployed fix for connection leak
   - Added finally block to properly close connections
   - Error rate dropped to 0%

3. **14:40 UTC - Verification**
   - Monitored error rates and latency
   - Verified connection pool utilization stabilized
   - Confirmed user transactions succeeding

### Permanent Fixes

See Action Items section.

## Action Items

### Immediate (Completed)

- [x] **Increased connection pool size to 200**
  - Owner: Alice
  - Completed: 2024-12-15 14:35 UTC
  - Status: Deployed to production

- [x] **Fixed connection leak in checkout service**
  - Owner: Bob
  - Completed: 2024-12-15 14:37 UTC
  - Status: Hotfix deployed

- [x] **Added monitoring for connection pool utilization**
  - Owner: Alice
  - Completed: 2024-12-15 16:00 UTC
  - Status: Dashboard created, alerts pending

### Short-term (1-2 weeks)

- [ ] **P0: Add alert for connection pool > 80% utilized**
  - Owner: Alice
  - Deadline: 2024-12-22
  - Description: Create PagerDuty alert when connection pool exceeds 80%
  - Success Criteria: Alert fires in staging when threshold reached

- [ ] **P0: Add connection leak detection to code review checklist**
  - Owner: Charlie
  - Deadline: 2024-12-22
  - Description: Update checklist, train team on resource leak detection
  - Success Criteria: Checklist updated, team training completed

- [ ] **P0: Implement automated static analysis for resource leaks**
  - Owner: Diana
  - Deadline: 2024-12-29
  - Description: Integrate SpotBugs or similar tool into CI/CD pipeline
  - Success Criteria: Tool catches known leak patterns, fails build on detection

- [ ] **P1: Extend load test duration and connection pool configuration**
  - Owner: Eve
  - Deadline: 2024-12-29
  - Description: Run load tests for 4+ hours with production pool size
  - Success Criteria: Load tests run for 4 hours, use production config

### Long-term (1-3 months)

- [ ] **P1: Implement connection pool auto-scaling**
  - Owner: Frank
  - Deadline: 2025-01-31
  - Description: Automatically scale pool based on utilization and latency
  - Success Criteria: Pool scales automatically in production, tested in staging

- [ ] **P1: Implement deployment freeze policy for peak periods**
  - Owner: Grace
  - Deadline: 2025-01-31
  - Description: Define peak periods, freeze deployments 24h before
  - Success Criteria: Policy documented, automated enforcement in CI/CD

- [ ] **P2: Implement connection pool health checks**
  - Owner: Henry
  - Deadline: 2025-02-28
  - Description: Add health check endpoint that includes pool utilization
  - Success Criteria: Health check fails when pool > 90% utilized

- [ ] **P2: Create runbook for database connection issues**
  - Owner: Iris
  - Deadline: 2025-02-28
  - Description: Document troubleshooting steps, common causes, mitigations
  - Success Criteria: Runbook published, team trained

## Lessons Learned

### What Went Well

1. **Fast Detection**
   - Incident detected within 1 minute via automated alerts
   - Alerts were clear and actionable
   - On-call engineer paged immediately

2. **Effective Escalation**
   - Database team engaged quickly
   - Clear ownership and communication
   - Right people involved at right time

3. **Quick Mitigation**
   - Emergency connection pool increase bought time
   - Hotfix deployed within 15 minutes
   - Good collaboration between teams

4. **Good Monitoring**
   - Error rate monitoring detected issue immediately
   - Logs provided clear evidence of root cause
   - Metrics helped quantify impact

### What Didn't Go Well

1. **No Connection Pool Monitoring**
   - Could have detected issue before complete exhaustion
   - Would have provided earlier warning
   - Now added to monitoring

2. **Connection Leak Not Caught in Code Review**
   - Code review checklist was incomplete
   - No automated static analysis
   - Reviewer missed the issue

3. **Insufficient Load Testing**
   - Tests didn't run long enough to detect leak
   - Tests used different configuration than production
   - Leak only manifested under sustained load

4. **Deployment During Peak Period**
   - Deployment 4 hours before peak shopping
   - No deployment freeze policy
   - Increased risk during critical period

5. **Small Connection Pool**
   - Pool size (50) was too small for peak traffic
   - Even without leak, operating near capacity
   - Should have been larger or auto-scaling

### What We Learned

1. **Connection Pool Monitoring is Critical**
   - Must monitor pool utilization
   - Alert before exhaustion
   - Include in standard observability

2. **Resource Leaks are Serious**
   - Can cause complete outages
   - Must be caught in code review
   - Need automated detection

3. **Load Testing Must Match Production**
   - Same configuration
   - Same duration
   - Same traffic patterns

4. **Deployment Timing Matters**
   - Avoid deployments before peak periods
   - Implement deployment freeze policy
   - Consider deployment windows

5. **Defense in Depth**
   - Multiple layers of detection and prevention
   - Code review + static analysis + monitoring
   - Don't rely on single safeguard

### How to Improve

1. **Improve Monitoring**
   - Add connection pool utilization monitoring
   - Add alerts for approaching capacity
   - Include in standard observability

2. **Enhance Code Review**
   - Update checklist to include resource leak detection
   - Train team on common leak patterns
   - Implement automated static analysis

3. **Improve Load Testing**
   - Run tests for longer duration (4+ hours)
   - Use production configuration
   - Include resource leak detection

4. **Implement Deployment Policies**
   - Define peak periods
   - Freeze deployments before peak periods
   - Automate enforcement

5. **Improve Resilience**
   - Implement connection pool auto-scaling
   - Add health checks that include pool utilization
   - Create runbooks for common issues

## Appendix

### Supporting Data

**Error Rate Graph:**
```
100% |                    ███
     |                  ██   █
 80% |                ██       █
     |              ██           
 60% |            ██             
     |          ██               
 40% |        ██                 
     |      ██                   
 20% |    ██                     ██
     |  ██                         ███
  0% |██_____________________________███
     14:20  14:25  14:30  14:35  14:40
            ↑      ↑      ↑      ↑
         Detect  Leak   Pool   Fixed
                 Found  +Size
```

**Connection Pool Utilization:**
```
100% |              ██████████
     |            ██          
 80% |          ██            
     |        ██              
 60% |      ██                
     |    ██                  
 40% |  ██                    
     |██                      
 20% |                        ████
     |                            ███
  0% |________________________________
     10:00  11:00  12:00  13:00  14:00  14:30
       ↑                        ↑      ↑
    Deploy                   Peak   Fixed
```

### Related Incidents

None. This is the first connection pool exhaustion incident.

### References

- Deployment: v2.3.1 (December 15, 2024, 10:00 UTC)
- Code Change: PR #1234 (checkout-service)
- Monitoring Dashboard: https://datadog.com/dashboard/checkout-service
- PagerDuty Incident: https://pagerduty.com/incidents/INC-2024-12-15-001
```

---

## Example 2: Cascading Failure from Third-Party Dependency

### Scenario

Payment service outage caused by third-party payment gateway performance degradation, leading to cascading failure.

### Incident Report Summary

```markdown
# Incident Report: Payment Service Cascading Failure

## Executive Summary

On December 20, 2024 at 09:15 UTC, our payment service experienced a 45-minute 
partial outage affecting 80% of payment transactions. The incident was triggered 
by performance degradation in our third-party payment gateway (Stripe), which 
caused thread pool exhaustion in our payment service, leading to a cascading 
failure affecting the checkout service.

The root cause was inadequate resilience patterns (no circuit breaker, no timeout, 
no bulkhead) when calling the payment gateway. When the gateway became slow, our 
payment service threads became blocked waiting for responses, eventually exhausting 
the thread pool and making the service unresponsive.

Approximately 8,000 users were affected, with ~2,000 payment transactions failing 
completely and ~6,000 experiencing significant delays. Estimated revenue impact 
was $120,000 in delayed transactions. We implemented immediate fixes during the 
incident and have identified 8 action items to prevent similar cascading failures.

## Incident Details

- **Incident ID:** INC-2024-12-20-001
- **Date/Time:** December 20, 2024, 09:15 - 10:00 UTC
- **Duration:** 45 minutes
- **Severity:** SEV-1 (Critical)
- **Services Affected:** Payment Service (primary), Checkout Service (secondary)
- **Root Cause:** Cascading failure due to third-party dependency degradation
- **User Impact:** 80% of payment transactions failed or delayed
- **Business Impact:** ~$120,000 in delayed transactions

## Timeline

09:15:00 UTC - Stripe payment gateway latency increases from 200ms to 5s
09:17:00 UTC - Payment service thread pool begins filling up
09:18:00 UTC - Payment service thread pool exhausted (200/200 threads busy)
09:18:30 UTC - Payment service becomes unresponsive
09:19:00 UTC - Checkout service starts timing out calling payment service
09:19:30 UTC - Checkout service latency increases to 30s
09:20:00 UTC - Alert fired: "Payment Service - Unhealthy"
09:20:30 UTC - Alert fired: "Checkout Service - High Latency"
09:22:00 UTC - On-call engineer (Bob) paged
09:23:00 UTC - Bob acknowledges and begins investigation
09:25:00 UTC - Bob identifies payment service is unresponsive
09:27:00 UTC - Thread pool exhaustion identified in payment service
09:30:00 UTC - Stripe status page shows "Degraded Performance"
09:32:00 UTC - Identified Stripe as root cause
09:35:00 UTC - Emergency mitigation: Enabled circuit breaker config (was disabled)
09:37:00 UTC - Circuit breaker opens, payment service starts failing fast
09:38:00 UTC - Thread pool begins recovering
09:40:00 UTC - Increased thread pool size from 200 to 500
09:42:00 UTC - Increased timeout from 30s to 10s (fail faster)
09:45:00 UTC - Switched to backup payment gateway (PayPal)
09:47:00 UTC - Payment success rate increases to 95%
09:50:00 UTC - Stripe performance returns to normal
09:52:00 UTC - Switched back to Stripe (primary gateway)
09:55:00 UTC - Payment success rate at 100%
10:00:00 UTC - All services healthy, incident resolved
10:30:00 UTC - Post-incident monitoring confirms stability

## Root Cause Analysis

### Primary Root Cause

The payment service lacked proper resilience patterns (circuit breaker, timeout, 
bulkhead) when calling the third-party payment gateway. When Stripe experienced 
performance degradation, our payment service threads became blocked waiting for 
responses, leading to thread pool exhaustion and service unavailability.

**Specific Issues:**

1. **No Circuit Breaker**
   - Circuit breaker code existed but was disabled in production
   - Continued sending requests to degraded gateway
   - No fail-fast behavior

2. **No Timeout**
   - Payment gateway calls had no timeout configured
   - Threads waited indefinitely for responses
   - Amplified thread pool exhaustion

3. **No Bulkhead**
   - Single thread pool for all operations
   - Payment gateway calls could exhaust all threads
   - No isolation between operations

4. **No Automatic Failover**
   - Manual failover to backup gateway required
   - 30 minutes to switch to PayPal
   - Should be automatic based on circuit breaker state

### Contributing Factors

1. **Circuit Breaker Disabled in Production**
   - Code existed but was disabled via configuration
   - Disabled during previous debugging session
   - No process to re-enable after debugging

2. **Insufficient Monitoring**
   - No monitoring on payment gateway latency
   - No alert for circuit breaker state
   - No visibility into thread pool utilization

3. **No Chaos Engineering**
   - Never tested behavior when payment gateway is slow
   - Never tested circuit breaker in production
   - No regular resilience testing

4. **Single Point of Failure**
   - Heavy reliance on single payment gateway (Stripe)
   - Backup gateway (PayPal) rarely used
   - No automatic failover mechanism

## Action Items

### Immediate (Completed)

- [x] Enabled circuit breaker in payment service
- [x] Increased thread pool size to 500
- [x] Added 10-second timeout to payment gateway calls
- [x] Switched to backup gateway during incident
- [x] Switched back to primary gateway after recovery

### Short-term (1-2 weeks)

- [ ] **P0: Implement automatic failover to backup payment gateway**
  - Owner: Bob
  - Deadline: 2024-12-27
  - Description: Auto-switch to PayPal when Stripe circuit breaker opens
  - Success: Automatic failover tested in staging

- [ ] **P0: Add monitoring for payment gateway latency**
  - Owner: Carol
  - Deadline: 2024-12-27
  - Description: Monitor latency for all payment gateways
  - Success: Dashboard shows gateway latency, alerts configured

- [ ] **P0: Add alert for circuit breaker open state**
  - Owner: Carol
  - Deadline: 2024-12-27
  - Description: Alert when circuit breaker opens
  - Success: Alert fires when circuit breaker opens in staging

- [ ] **P0: Test circuit breaker and failover in staging**
  - Owner: Dave
  - Deadline: 2024-12-29
  - Description: Chaos engineering test: simulate gateway degradation
  - Success: Circuit breaker opens, auto-failover works

### Long-term (1-3 months)

- [ ] **P1: Implement bulkhead pattern for all external dependencies**
  - Owner: Eve
  - Deadline: 2025-01-31
  - Description: Separate thread pools for different operations
  - Success: Payment gateway has dedicated thread pool

- [ ] **P1: Add chaos engineering tests for dependency failures**
  - Owner: Frank
  - Deadline: 2025-01-31
  - Description: Regular chaos tests for all external dependencies
  - Success: Monthly chaos tests running, results tracked

- [ ] **P1: Create runbook for payment gateway failures**
  - Owner: Grace
  - Deadline: 2025-02-28
  - Description: Document troubleshooting, failover procedures
  - Success: Runbook published, team trained

- [ ] **P2: Implement health checks that include dependency health**
  - Owner: Henry
  - Deadline: 2025-02-28
  - Description: Health check fails when circuit breaker is open
  - Success: Health check reflects dependency health

## Lessons Learned

### What Went Well

- Quick identification of root cause (Stripe degradation)
- Effective mitigation (circuit breaker, timeout, failover)
- Good collaboration between teams
- Clear communication during incident

### What Didn't Go Well

- Circuit breaker was disabled in production
- No automatic failover to backup gateway
- 30-minute delay to switch gateways
- No monitoring on payment gateway health
- Never tested circuit breaker in production

### What We Learned

- All external dependencies need circuit breakers and timeouts
- Circuit breakers must be tested regularly
- Automatic failover is critical for third-party dependencies
- Chaos engineering is essential for validating resilience
- Configuration changes (like disabling circuit breaker) need better governance

### How to Improve

- Implement and test circuit breakers for all external dependencies
- Add automatic failover for critical dependencies
- Regular chaos engineering tests
- Better monitoring and alerting for dependency health
- Stricter governance for production configuration changes
```

---

## Example 3: Configuration Change Memory Leak

### Scenario

Gradual service degradation over 6 hours due to configuration change causing unbounded cache growth.

### Key Points

**Timeline:**
- 08:00: Configuration deployed (cache size increased)
- 08:30: Memory usage begins climbing
- 14:00: Out-of-memory errors begin (6-hour delay in detection)
- 14:30: Service recovered after rollback

**Root Cause:**
- Configuration change increased cache size
- Cache eviction policy had a bug
- Unbounded memory growth

**Key Learning:**
- Configuration changes need same rigor as code changes
- Memory growth rate alerts are critical
- Canary deployments should apply to config changes

---

## Example 4: DNS Resolution Failure

### Scenario

Intermittent service failures due to DNS server overload during traffic spike.

### Key Points

**Timeline:**
- 16:00: Intermittent errors begin
- 16:30: DNS resolution failures identified
- 17:00: DNS infrastructure scaled
- 18:00: Full recovery

**Root Cause:**
- Single DNS server (no redundancy)
- Very short DNS cache TTL (5 seconds)
- DNS server overloaded during traffic spike

**Key Learning:**
- Infrastructure dependencies need same attention as application dependencies
- DNS redundancy is critical
- Appropriate cache TTL reduces load

---

## Template: Blank Incident Report

```markdown
# Incident Report: [TITLE]

## Executive Summary

[2-3 paragraphs summarizing the incident for leadership]

## Incident Details

- **Incident ID:** INC-YYYY-MM-DD-NNN
- **Date/Time:** [START] to [END]
- **Duration:** [DURATION]
- **Severity:** SEV-[1/2/3/4]
- **Services Affected:** [LIST]
- **Root Cause:** [ONE SENTENCE]
- **User Impact:** [DESCRIPTION]
- **Business Impact:** [DESCRIPTION]

## Timeline

[HH:MM UTC] - [EVENT]
[HH:MM UTC] - [EVENT]
...

## Impact Assessment

### User Impact
- **Affected Users:** [NUMBER]
- **Failed Transactions:** [NUMBER]
- **Geographic Distribution:** [REGIONS]
- **User Experience:** [DESCRIPTION]

### Business Impact
- **Revenue Impact:** $[AMOUNT]
- **SLA Violations:** [YES/NO]
- **SLO Violations:** [WHICH ONES]
- **Reputation Impact:** [ASSESSMENT]

### Technical Impact
- **Services Affected:** [LIST]
- **Time to Detect:** [TIME]
- **Time to Resolve:** [TIME]

## Root Cause Analysis

### Primary Root Cause

[DETAILED EXPLANATION]

### Contributing Factors

1. [FACTOR 1]
2. [FACTOR 2]
...

### Why It Wasn't Prevented

[EXPLANATION]

### Why It Wasn't Detected Earlier

[EXPLANATION]

## Resolution

### Immediate Actions

[WHAT WAS DONE DURING THE INCIDENT]

### Permanent Fixes

[SEE ACTION ITEMS]

## Action Items

### Immediate (Completed)

- [x] [ACTION]
  - Owner: [NAME]
  - Completed: [DATE]
  - Status: [STATUS]

### Short-term (1-2 weeks)

- [ ] **P0: [ACTION]**
  - Owner: [NAME]
  - Deadline: [DATE]
  - Description: [DESCRIPTION]
  - Success Criteria: [CRITERIA]

### Long-term (1-3 months)

- [ ] **P1: [ACTION]**
  - Owner: [NAME]
  - Deadline: [DATE]
  - Description: [DESCRIPTION]
  - Success Criteria: [CRITERIA]

## Lessons Learned

### What Went Well

- [ITEM 1]
- [ITEM 2]
...

### What Didn't Go Well

- [ITEM 1]
- [ITEM 2]
...

### What We Learned

- [LEARNING 1]
- [LEARNING 2]
...

### How to Improve

- [IMPROVEMENT 1]
- [IMPROVEMENT 2]
...

## Appendix

### Supporting Data

[GRAPHS, LOGS, METRICS]

### Related Incidents

[LIST OF RELATED INCIDENTS]

### References

[LINKS TO RELEVANT RESOURCES]
```

---

## Additional Example Scenarios

### Scenario 5: API Rate Limiting Incident

**Summary:** Third-party API started rate limiting requests, causing 50% of user requests to fail.

**Key Learning:** Need circuit breaker, exponential backoff, and rate limit monitoring for all third-party APIs.

### Scenario 6: Database Replica Lag

**Summary:** Read replica lag increased to 5 minutes, causing stale data to be served to users.

**Key Learning:** Monitor replica lag, implement lag-aware routing, have fallback to primary database.

### Scenario 7: Certificate Expiration

**Summary:** SSL certificate expired, causing all HTTPS traffic to fail.

**Key Learning:** Automated certificate renewal, monitoring for certificate expiration, alerts 30 days before expiry.

### Scenario 8: Kubernetes Node Failure

**Summary:** Kubernetes node failure caused pod evictions and service disruption.

**Key Learning:** Proper pod disruption budgets, multiple availability zones, node auto-scaling.

---

## Using These Examples

### For Learning

1. Read through each example
2. Identify the root cause
3. Understand the contributing factors
4. Review the action items
5. Consider how to prevent similar incidents

### For Templates

1. Use the blank template for your incidents
2. Adapt the structure to your needs
3. Include all required sections
4. Focus on clarity and actionability

### For Training

1. Use examples in incident response training
2. Practice writing incident reports
3. Conduct mock post-mortems
4. Learn from real-world scenarios

---

## References

- Google SRE Book: Postmortem Culture
- Etsy's Debriefing Facilitation Guide
- PagerDuty Incident Response Documentation
- Atlassian Incident Postmortem Template