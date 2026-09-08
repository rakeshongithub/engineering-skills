# Production Readiness - Examples

## Example 1: SaaS Platform - Pre-Launch Assessment

### Context
- **System**: Project management SaaS platform
- **Stage**: Moving from beta to general availability (GA)
- **Current Users**: 500 beta users
- **Expected Growth**: 10,000 users in first 3 months
- **SLA Target**: 99.9% availability (43 minutes downtime/month)

### Assessment Results

#### Operational Readiness: YELLOW

**Monitoring and Observability:**
- ✅ Golden signals monitored (latency, traffic, errors, saturation)
- ✅ Business metrics tracked (signups, active users, MRR)
- ✅ Infrastructure metrics collected
- ✅ Structured logging implemented
- ✅ Logs centralized in Datadog
- ❌ **GAP**: No distributed tracing (microservices architecture)
- ✅ System health dashboard created
- ❌ **GAP**: Service-level dashboards incomplete (only 3 of 8 services)

**Alerting:**
- ✅ Alerts for error rate spikes
- ✅ Alerts for resource exhaustion
- ❌ **GAP**: No alerts for SLA violations (p95 latency > 500ms)
- ❌ **GAP**: Alerts missing runbook links
- ✅ Alerts routed to PagerDuty
- ✅ Escalation policy defined

**Runbooks and Documentation:**
- ❌ **GAP**: Only 2 runbooks created (database failover, API server restart)
- ❌ **GAP**: Missing runbooks for common scenarios (high latency, queue backup, cache failures)
- ✅ Architecture diagrams up-to-date
- ✅ Deployment procedures documented
- ❌ **GAP**: Rollback procedures not tested

**On-Call and Incident Response:**
- ✅ On-call rotation established (3 engineers)
- ❌ **GAP**: On-call engineers not trained on all services
- ✅ Incident response process defined
- ✅ Post-mortem template created
- ❌ **GAP**: No practice incident drills conducted

**Gaps:**
1. **HIGH**: Missing distributed tracing - Hard to debug issues across services
2. **HIGH**: Incomplete service dashboards - Can't monitor all services effectively
3. **HIGH**: Missing SLA violation alerts - Won't know when SLA is breached
4. **MEDIUM**: Runbooks incomplete - Slower incident response
5. **MEDIUM**: Rollback not tested - Risk during deployment
6. **MEDIUM**: On-call training incomplete - Inconsistent incident response

#### Reliability and Resilience: YELLOW

**High Availability:**
- ✅ No single points of failure (multi-AZ deployment)
- ✅ Critical components have redundancy (2+ instances)
- ✅ Load balancing configured (ALB)
- ✅ Auto-scaling configured (CPU > 70%)
- ✅ Circuit breakers implemented (Hystrix)
- ✅ Retries with exponential backoff
- ❌ **GAP**: Timeouts not configured consistently (some services have 60s timeout)
- ✅ Graceful degradation for non-critical features

**Data Durability:**
- ✅ Database backups automated (daily, retained 30 days)
- ❌ **GAP**: Backup restoration never tested
- ✅ Point-in-time recovery enabled (PostgreSQL)
- ✅ Data replication (primary + 1 read replica)

**Disaster Recovery:**
- ✅ RTO defined: 4 hours
- ✅ RPO defined: 1 hour
- ❌ **GAP**: DR plan documented but never tested
- ✅ Backups stored in separate AWS region
- ✅ Backup encryption enabled

**Gaps:**
1. **HIGH**: Backup restoration never tested - Don't know if backups actually work
2. **HIGH**: DR plan never tested - Don't know if we can meet RTO/RPO
3. **MEDIUM**: Inconsistent timeouts - Could cause cascading failures

#### Performance and Scalability: GREEN

**Performance:**
- ✅ Performance benchmarks established
- ✅ Latency targets: p50 < 200ms, p95 < 500ms, p99 < 1s
- ✅ Throughput target: 1000 req/s
- ✅ Performance tested under 2x expected load
- ✅ Database queries optimized
- ✅ Indexes created for common queries
- ✅ Redis caching implemented
- ✅ CloudFront CDN for static assets
- ✅ API response times acceptable (p95 = 350ms)

**Scalability:**
- ✅ Stateless application servers (can scale horizontally)
- ✅ Auto-scaling policies configured
- ✅ Load testing performed (up to 2000 req/s)
- ✅ Scaling limits documented (max 50 instances)
- ✅ Current capacity: 500 req/s
- ✅ Expected peak: 200 req/s
- ✅ Capacity headroom: 2.5x (sufficient)

**Gaps:** None

#### Security: YELLOW

**Authentication and Authorization:**
- ✅ Authentication implemented (OAuth 2.0)
- ✅ MFA available (optional)
- ❌ **GAP**: MFA not enforced for admin users
- ✅ Authorization checks at API layer
- ✅ RBAC implemented
- ✅ Secrets in AWS Secrets Manager

**Data Protection:**
- ✅ TLS 1.3 for data in transit
- ✅ Database encryption at rest
- ✅ S3 encryption at rest
- ✅ PII handling compliant with GDPR
- ✅ Data retention policy defined (2 years)

**Network Security:**
- ✅ Security groups configured (least privilege)
- ✅ WAF enabled (AWS WAF)
- ❌ **GAP**: DDoS protection not enabled (AWS Shield Standard only)
- ✅ VPC with private subnets

**Vulnerability Management:**
- ✅ Dependency scanning in CI/CD (Snyk)
- ✅ Security patches applied monthly
- ❌ **GAP**: No penetration testing performed

**Gaps:**
1. **HIGH**: MFA not enforced for admins - Account takeover risk
2. **MEDIUM**: No DDoS protection - Vulnerable to large attacks
3. **MEDIUM**: No penetration testing - Unknown vulnerabilities may exist

#### Deployment and Rollback: YELLOW

**Deployment:**
- ✅ Deployment strategy: Blue-green
- ✅ CI/CD automated (GitHub Actions)
- ✅ Deployment tested in staging
- ✅ Database migrations tested
- ✅ Feature flags configured (LaunchDarkly)
- ✅ Smoke tests automated
- ✅ Health checks configured

**Rollback:**
- ✅ Rollback procedures documented
- ❌ **GAP**: Rollback never tested
- ❌ **GAP**: Rollback time unknown (target: < 15 minutes)
- ✅ Database rollback strategy defined (migrations are reversible)

**Gaps:**
1. **HIGH**: Rollback never tested - Don't know if it works or how long it takes

#### Compliance and Governance: GREEN

- ✅ GDPR compliance verified
- ✅ Audit logging enabled
- ✅ Data residency requirements met (EU data in EU region)
- ✅ Change approval process followed
- ✅ Stakeholders notified
- ✅ Cost monitoring configured
- ✅ Cost alerts set up

**Gaps:** None

### Gap Summary

**Blockers (Must fix before launch): 0**

**High Priority (Should fix before launch): 6**
1. Missing distributed tracing
2. Incomplete service dashboards
3. Missing SLA violation alerts
4. Backup restoration never tested
5. DR plan never tested
6. Rollback never tested

**Medium Priority (Can launch with plan to fix): 5**
1. Runbooks incomplete
2. On-call training incomplete
3. Inconsistent timeouts
4. MFA not enforced for admins
5. No DDoS protection

**Low Priority: 2**
1. No penetration testing
2. No practice incident drills

### Go/No-Go Decision: CONDITIONAL GO

**Rationale:**
- No blockers present
- High-priority gaps are manageable with mitigation plans
- System has been stable in beta for 3 months
- Team is confident in core functionality

**Launch Strategy: Phased Launch**
1. **Week 1**: Launch to 10% of new signups (with beta users = ~1000 total users)
2. **Week 2**: If stable, increase to 50% (~ 2500 total users)
3. **Week 3**: If stable, increase to 100% (full GA)

**Mitigation Plans:**
1. **Distributed tracing**: Implement in Week 1 (already in progress)
2. **Service dashboards**: Complete in Week 1
3. **SLA alerts**: Add in Week 1
4. **Backup restoration**: Test in Week 1 (schedule maintenance window)
5. **DR plan**: Test in Week 2 (after backup test)
6. **Rollback**: Test in staging in Week 1, validate in production during Week 1 launch

**Increased Monitoring:**
- 24/7 on-call coverage for first 2 weeks
- Daily team sync to review metrics and incidents
- Weekly stakeholder update

**Rollback Criteria:**
- Error rate > 1% for > 5 minutes
- p95 latency > 1s for > 10 minutes
- Availability < 99% in any 1-hour window
- Any data loss or corruption

### Action Plan

**Before Launch (Week 0):**
- [ ] Implement distributed tracing (Owner: Backend team, 3 days)
- [ ] Complete service dashboards (Owner: DevOps, 2 days)
- [ ] Add SLA violation alerts (Owner: DevOps, 1 day)
- [ ] Test backup restoration (Owner: DBA, 1 day)
- [ ] Test rollback in staging (Owner: DevOps, 1 day)
- [ ] Train on-call engineers on all services (Owner: Tech Lead, 2 days)

**Week 1 (10% Launch):**
- [ ] Launch to 10% of signups
- [ ] Monitor closely (24/7 on-call)
- [ ] Test DR plan (Owner: DevOps, scheduled maintenance)
- [ ] Complete remaining runbooks (Owner: Team, 3 days)

**Week 2 (50% Launch):**
- [ ] Increase to 50% if stable
- [ ] Continue 24/7 on-call
- [ ] Fix inconsistent timeouts (Owner: Backend team, 2 days)
- [ ] Enforce MFA for admins (Owner: Security, 1 day)

**Week 3 (100% Launch):**
- [ ] Increase to 100% (full GA) if stable
- [ ] Return to normal on-call rotation

**Post-Launch (1 month):**
- [ ] Enable AWS Shield Advanced (DDoS protection) (Owner: DevOps, 1 day)
- [ ] Schedule penetration testing (Owner: Security, 2 weeks)
- [ ] Conduct practice incident drill (Owner: Tech Lead, 1 day)

---

## Example 2: E-commerce Platform - Black Friday Readiness

### Context
- **System**: E-commerce platform
- **Event**: Black Friday (expected 10x normal traffic)
- **Normal Load**: 1,000 req/s, 100,000 daily orders
- **Expected Load**: 10,000 req/s peak, 1,000,000 orders on Black Friday
- **SLA Target**: 99.95% availability, p95 latency < 1s

### Assessment Results

#### Operational Readiness: GREEN

- ✅ Comprehensive monitoring and alerting
- ✅ Runbooks for all critical scenarios
- ✅ 24/7 on-call coverage during Black Friday week
- ✅ War room set up for real-time monitoring
- ✅ Incident response team assigned

#### Reliability and Resilience: GREEN

- ✅ Multi-region deployment (active-active)
- ✅ Auto-scaling tested up to 15,000 req/s
- ✅ Circuit breakers and graceful degradation
- ✅ Database read replicas (5 replicas)
- ✅ Database write scaling (sharding by customer_id)
- ✅ Redis cluster for session and cache
- ✅ Message queue (RabbitMQ) for async processing
- ✅ CDN (CloudFront) for static assets and API caching

#### Performance and Scalability: GREEN

- ✅ Load testing performed at 15,000 req/s (1.5x expected peak)
- ✅ p95 latency at 10,000 req/s: 800ms (within SLA)
- ✅ Database query optimization completed
- ✅ Caching strategy optimized (95% cache hit rate)
- ✅ Capacity headroom: 1.5x expected peak

#### Security: GREEN

- ✅ DDoS protection enabled (Cloudflare)
- ✅ WAF rules tuned for high traffic
- ✅ Rate limiting configured (per IP and per user)
- ✅ Fraud detection enabled
- ✅ PCI-DSS compliant (using Stripe)

#### Deployment and Rollback: GREEN

- ✅ Code freeze 1 week before Black Friday
- ✅ Deployment tested in staging at 10x load
- ✅ Rollback tested (< 5 minutes)
- ✅ Feature flags for all new features (can disable instantly)

### Gap Summary

**Blockers: 0**
**High Priority: 0**
**Medium Priority: 2**
1. Database connection pool could be exhausted at extreme load (> 15,000 req/s)
2. Email service (SendGrid) rate limits could be hit (100,000 emails/hour)

**Low Priority: 1**
1. Monitoring dashboard could be slow at high metric volume

### Go/No-Go Decision: GO

**Rationale:**
- No blockers or high-priority gaps
- System tested at 1.5x expected peak load
- Team has experience from previous Black Fridays
- Comprehensive monitoring and rollback capabilities

**Launch Strategy: Full Launch with Enhanced Monitoring**

**Mitigation Plans:**
1. **Database connections**: Increase connection pool size, add 2 more read replicas (already in progress)
2. **Email rate limits**: Pre-warm SendGrid account, implement email queuing with retry

**Enhanced Monitoring:**
- War room with real-time dashboards (Thursday-Sunday)
- 24/7 on-call coverage (Wednesday-Monday)
- Hourly status updates to stakeholders
- Automated alerts to war room Slack channel

**Graceful Degradation Plan:**
- If load > 12,000 req/s: Disable non-critical features (recommendations, reviews)
- If load > 15,000 req/s: Enable queue for checkout ("You're in line" page)
- If database writes failing: Enable read-only mode for browsing, queue orders

**Rollback Criteria:**
- Availability < 99% for > 5 minutes
- p95 latency > 2s for > 10 minutes
- Payment processing error rate > 0.5%
- Any data corruption

### Action Plan

**1 Week Before:**
- [x] Load testing at 15,000 req/s completed
- [x] Code freeze in effect
- [x] All deployments to production frozen
- [ ] Increase database connection pool (Owner: DBA, 1 day)
- [ ] Add 2 more read replicas (Owner: DBA, 1 day)
- [ ] Pre-warm SendGrid account (Owner: Backend, 1 day)
- [ ] Implement email queuing (Owner: Backend, 2 days)

**3 Days Before:**
- [ ] Final system check (all teams)
- [ ] War room setup and tested
- [ ] On-call schedule confirmed
- [ ] Stakeholder communication plan confirmed

**Black Friday:**
- [ ] War room active (6am-midnight)
- [ ] Hourly status updates
- [ ] Monitor graceful degradation triggers
- [ ] Be ready to rollback or enable degraded mode

**Post-Black Friday:**
- [ ] Post-mortem within 3 days
- [ ] Document lessons learned
- [ ] Plan improvements for next year

**Result:** Successful Black Friday, 99.97% availability, p95 latency 750ms, 1.2M orders processed

---

## Example 3: Mobile App Backend - First Production Deployment

### Context
- **System**: Backend API for new mobile app (iOS, Android)
- **Stage**: First production deployment (no beta)
- **Expected Users**: 10,000 in first month
- **SLA Target**: 99.5% availability (3.6 hours downtime/month)

### Assessment Results

#### Operational Readiness: RED

**Monitoring and Observability:**
- ✅ Application metrics (custom metrics in CloudWatch)
- ❌ **BLOCKER**: No centralized logging (logs only on instances)
- ❌ **BLOCKER**: No error tracking (no Sentry or similar)
- ❌ **GAP**: No dashboards created
- ❌ **GAP**: No distributed tracing

**Alerting:**
- ❌ **BLOCKER**: No alerts configured
- ❌ **BLOCKER**: No on-call rotation

**Runbooks and Documentation:**
- ❌ **BLOCKER**: No runbooks
- ❌ **GAP**: Deployment procedures not documented
- ❌ **GAP**: No architecture diagrams

**Gaps:**
1. **BLOCKER**: No centralized logging - Can't debug issues
2. **BLOCKER**: No error tracking - Won't know when errors occur
3. **BLOCKER**: No alerts - Won't know when system is down
4. **BLOCKER**: No on-call - No one to respond to incidents
5. **BLOCKER**: No runbooks - Can't respond to incidents effectively

#### Reliability and Resilience: RED

- ❌ **BLOCKER**: Single EC2 instance (single point of failure)
- ❌ **BLOCKER**: No auto-scaling
- ❌ **BLOCKER**: No load balancer
- ❌ **BLOCKER**: No database backups
- ❌ **GAP**: No circuit breakers or retries
- ❌ **GAP**: No graceful degradation

**Gaps:**
1. **BLOCKER**: Single instance - Any failure causes complete outage
2. **BLOCKER**: No database backups - Risk of data loss
3. **BLOCKER**: No auto-scaling - Can't handle traffic spikes

#### Performance and Scalability: YELLOW

- ✅ Performance tested at expected load (100 req/s)
- ❌ **GAP**: No load testing at higher load
- ✅ Database queries optimized
- ❌ **GAP**: No caching

#### Security: YELLOW

- ✅ Authentication (JWT)
- ✅ HTTPS (TLS 1.2)
- ❌ **GAP**: Secrets in environment variables (not in secrets manager)
- ❌ **GAP**: No rate limiting
- ❌ **GAP**: No DDoS protection

#### Deployment and Rollback: RED

- ❌ **BLOCKER**: Manual deployment (SSH and run commands)
- ❌ **BLOCKER**: No rollback procedure
- ❌ **GAP**: No staging environment
- ❌ **GAP**: No CI/CD

**Gaps:**
1. **BLOCKER**: Manual deployment - Error-prone, slow
2. **BLOCKER**: No rollback - Can't recover from bad deployment

### Gap Summary

**Blockers (Must fix before launch): 11**
1. No centralized logging
2. No error tracking
3. No alerts
4. No on-call rotation
5. No runbooks
6. Single instance (no redundancy)
7. No database backups
8. No auto-scaling
9. Manual deployment
10. No rollback procedure

**High Priority: 5**
**Medium Priority: 3**
**Low Priority: 2**

### Go/No-Go Decision: NO-GO

**Rationale:**
- **11 blockers** present
- System is not production-ready
- High risk of outages and data loss
- No way to detect or respond to incidents
- Cannot meet 99.5% SLA target

**Recommendation: Delay launch by 3-4 weeks**

### Action Plan to Achieve Production Readiness

**Week 1: Operational Readiness**
- [ ] Set up centralized logging (ELK or CloudWatch Logs) - 2 days
- [ ] Set up error tracking (Sentry) - 1 day
- [ ] Create system health dashboard - 1 day
- [ ] Configure alerts (error rate, latency, availability) - 1 day
- [ ] Establish on-call rotation - 1 day
- [ ] Create runbooks for common scenarios - 2 days

**Week 2: Reliability and Resilience**
- [ ] Add load balancer (ALB) - 1 day
- [ ] Add auto-scaling (min 2 instances) - 1 day
- [ ] Set up database backups (automated daily) - 1 day
- [ ] Test backup restoration - 1 day
- [ ] Implement circuit breakers (Hystrix or Resilience4j) - 2 days
- [ ] Implement retries with exponential backoff - 1 day

**Week 3: Deployment and Rollback**
- [ ] Set up staging environment - 2 days
- [ ] Set up CI/CD (GitHub Actions or GitLab CI) - 2 days
- [ ] Automate deployment (blue-green) - 2 days
- [ ] Document and test rollback procedure - 1 day

**Week 4: Security and Final Validation**
- [ ] Move secrets to AWS Secrets Manager - 1 day
- [ ] Implement rate limiting - 1 day
- [ ] Enable AWS Shield (DDoS protection) - 1 day
- [ ] Load testing at 2x expected load - 1 day
- [ ] Final production readiness review - 1 day
- [ ] Go/No-Go decision - 1 day

**Total Effort: 3-4 weeks**

**Re-Assessment After 4 Weeks:**
- All blockers addressed
- High-priority gaps addressed
- System meets production readiness criteria
- **Decision: GO** (with phased launch to 10%, 50%, 100%)

---

## Example 4: Microservices Platform - Major Architecture Change

### Context
- **System**: Microservices platform (20 services)
- **Change**: Migrating from monolithic database to database-per-service
- **Current State**: Production system with 50,000 users
- **SLA Target**: 99.9% availability (maintain current SLA)

### Assessment Results

#### Operational Readiness: YELLOW

- ✅ Monitoring and alerting in place
- ❌ **GAP**: Dashboards need updates for new database topology
- ❌ **GAP**: Alerts need updates for new databases
- ✅ Runbooks exist for current system
- ❌ **GAP**: Runbooks need updates for new architecture
- ✅ On-call rotation established

#### Reliability and Resilience: YELLOW

- ✅ Each service has redundancy
- ✅ Auto-scaling configured
- ❌ **GAP**: New databases not yet configured for backups
- ❌ **GAP**: Data replication not yet set up
- ❌ **GAP**: Failover procedures not tested
- ✅ Circuit breakers and retries in place

#### Data Migration: YELLOW

- ✅ Migration scripts written
- ✅ Migration tested in staging
- ❌ **GAP**: Migration not tested at production scale
- ❌ **GAP**: Data validation scripts incomplete
- ❌ **BLOCKER**: Rollback plan for data migration not defined

#### Deployment and Rollback: YELLOW

- ✅ Deployment automated
- ✅ Phased rollout plan (service by service)
- ❌ **BLOCKER**: Rollback plan incomplete (how to rollback data migration?)
- ❌ **GAP**: Rollback not tested

### Gap Summary

**Blockers: 2**
1. Rollback plan for data migration not defined
2. Rollback not tested

**High Priority: 6**
1. Dashboards need updates
2. Alerts need updates
3. Runbooks need updates
4. Database backups not configured
5. Data replication not set up
6. Migration not tested at production scale

**Medium Priority: 2**
**Low Priority: 1**

### Go/No-Go Decision: NO-GO (for now)

**Rationale:**
- **2 blockers** related to rollback
- High-risk change (data migration)
- Rollback is critical for this type of change

**Recommendation: Address blockers, then proceed with phased rollout**

### Action Plan

**Week 1: Address Blockers**
- [ ] Define rollback plan:
  - Keep old monolithic database for 2 weeks
  - Dual-write to both old and new databases during migration
  - Rollback = switch reads back to old database
- [ ] Test rollback in staging - 2 days
- [ ] Document rollback procedures - 1 day

**Week 2: Address High-Priority Gaps**
- [ ] Update dashboards for new databases - 1 day
- [ ] Update alerts for new databases - 1 day
- [ ] Update runbooks - 2 days
- [ ] Configure database backups - 1 day
- [ ] Set up data replication - 1 day
- [ ] Test migration at production scale (using production snapshot) - 2 days

**Week 3: Phased Rollout (Service 1-5)**
- [ ] Migrate Service 1 (lowest risk) - Day 1
- [ ] Monitor for 24 hours
- [ ] Migrate Service 2-5 (one per day) - Day 2-5
- [ ] Monitor each for 24 hours before next migration

**Week 4: Phased Rollout (Service 6-10)**
- [ ] Continue migrating services (one per day)
- [ ] Monitor closely

**Week 5-6: Phased Rollout (Service 11-20)**
- [ ] Complete migration of all services
- [ ] Verify data consistency
- [ ] Monitor for 1 week

**Week 7: Cleanup**
- [ ] Verify all services migrated successfully
- [ ] Verify data consistency across all databases
- [ ] Stop dual-writes
- [ ] Decommission old monolithic database

**Rollback Criteria (per service):**
- Data inconsistency detected
- Error rate > 0.5% for > 5 minutes
- p95 latency increase > 50%
- Any data loss

**Result:** Successful migration over 6 weeks, zero downtime, 99.95% availability maintained