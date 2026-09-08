# Migration Planning - Examples

## Example 1: Monolith to Microservices Migration

### Context
- **Current**: Java monolith (500K LOC, 10 years old)
- **Target**: Microservices architecture
- **Team**: 12 developers
- **Timeline**: 18 months
- **Downtime tolerance**: Zero

### Migration Plan

**Approach: Strangler Fig Pattern**

**Phase 1: Preparation (Months 1-2)**
1. Set up microservices infrastructure (Kubernetes)
2. Set up service mesh (Istio)
3. Set up monitoring (Prometheus, Grafana)
4. Set up CI/CD for microservices
5. Train team on microservices patterns

**Phase 2: Extract First Service - Authentication (Months 3-4)**
1. Identify authentication boundaries
2. Create new authentication microservice
3. Migrate authentication logic
4. Set up routing: 10% traffic to new service
5. Monitor and validate
6. Gradually increase to 100%
7. Remove auth code from monolith

**Phase 3: Extract Second Service - Notifications (Months 5-6)**
1. Create notifications microservice
2. Migrate notification logic
3. Gradual traffic migration (10% → 100%)
4. Remove from monolith

**Phase 4-6: Extract Remaining Services (Months 7-16)**
- User service
- Product catalog service
- Order service
- Payment service
- Inventory service
- Reporting service

**Phase 7: Cleanup (Months 17-18)**
1. Decommission monolith
2. Clean up routing logic
3. Update documentation
4. Retrospective

### Risk Mitigation

**Risk: Service extraction breaks functionality**
- Mitigation: Gradual traffic migration (10%, 25%, 50%, 100%)
- Rollback: Route traffic back to monolith

**Risk: Data consistency issues**
- Mitigation: Use saga pattern for distributed transactions
- Monitoring: Track data inconsistencies

**Risk: Performance degradation**
- Mitigation: Performance testing before each rollout
- Monitoring: Track latency and throughput

### Success Metrics

**After 6 months:**
- 2 services extracted
- Zero production incidents
- Deployment frequency: 1/month → 1/week

**After 12 months:**
- 6 services extracted
- Independent scaling working
- Deployment frequency: 1/week → 2/day

**After 18 months:**
- All services extracted
- Monolith decommissioned
- 99.9% availability
- Feature velocity +50%

---

## Example 2: Database Migration (MySQL to PostgreSQL)

### Context
- **Current**: MySQL 5.7
- **Target**: PostgreSQL 14
- **Data**: 500GB, 100M rows
- **Downtime tolerance**: <4 hours
- **Timeline**: 8 weeks

### Migration Plan

**Approach: Parallel Run with Cutover**

**Week 1-2: Preparation**
1. Set up PostgreSQL infrastructure
2. Create schema migration scripts
3. Create data migration scripts
4. Set up replication (MySQL → PostgreSQL)

**Week 3-4: Data Migration Testing**
1. Migrate 10% of data
2. Validate data integrity
3. Test application with PostgreSQL
4. Measure performance
5. Fix issues

**Week 5: Full Data Migration (Staging)**
1. Migrate all data to staging PostgreSQL
2. Set up continuous replication
3. Run application against PostgreSQL in staging
4. Full testing cycle

**Week 6: Parallel Run**
1. Set up dual writes (MySQL + PostgreSQL)
2. Read from MySQL (primary)
3. Compare data between databases
4. Monitor for discrepancies

**Week 7: Cutover Preparation**
1. Final data validation
2. Dry run of cutover
3. Prepare rollback plan
4. Communication to stakeholders

**Week 8: Cutover**
1. Friday 6 PM: Stop writes to MySQL
2. Final data sync (MySQL → PostgreSQL)
3. Validate data integrity
4. Switch application to PostgreSQL
5. Monitor closely
6. Sunday: Validate success
7. Monday: Full team monitoring

### Rollback Plan

**Rollback triggers:**
- Data integrity issues
- Performance degradation >20%
- Critical bugs
- Availability <99%

**Rollback procedure:**
1. Stop application
2. Sync data (PostgreSQL → MySQL)
3. Switch application back to MySQL
4. Validate
5. Resume operations

**Rollback time: <2 hours**

### Data Validation

**Validation checks:**
- Row counts match
- Key data fields match
- Checksums match
- Foreign key integrity
- No data loss

**Validation script:**
```sql
-- Compare row counts
SELECT 'users' as table_name, COUNT(*) FROM mysql.users
UNION
SELECT 'users', COUNT(*) FROM postgres.users;

-- Compare checksums
SELECT MD5(GROUP_CONCAT(id, email ORDER BY id)) FROM mysql.users;
SELECT MD5(STRING_AGG(id || email, '' ORDER BY id)) FROM postgres.users;
```

---

## Example 3: Cloud Migration (On-Premise to AWS)

### Context
- **Current**: On-premise data center
- **Target**: AWS cloud
- **Applications**: 20 applications
- **Timeline**: 12 months
- **Downtime tolerance**: Varies by application

### Migration Plan

**Approach: Phased Migration (by application)**

**Month 1-2: Preparation**
1. AWS account setup
2. Network setup (VPN, Direct Connect)
3. IAM and security setup
4. Migration tools setup (AWS DMS, CloudEndure)
5. Training team on AWS

**Month 3-4: Pilot Migration (Low-risk app)**
1. Choose simple, non-critical app
2. Migrate to AWS
3. Validate
4. Learn lessons
5. Refine process

**Month 5-10: Migrate Remaining Apps**

**Priority 1 (Months 5-6): Quick Wins**
- Stateless applications
- Low complexity
- Low risk

**Priority 2 (Months 7-8): Medium Complexity**
- Applications with databases
- Medium traffic
- Some integrations

**Priority 3 (Months 9-10): High Complexity**
- Critical applications
- High traffic
- Many integrations
- Requires zero downtime

**Month 11-12: Cleanup**
1. Decommission on-premise infrastructure
2. Optimize AWS costs
3. Update documentation
4. Retrospective

### Migration Strategy by Application Type

**Stateless Web Apps:**
- Approach: Lift and shift
- Downtime: <1 hour
- Steps:
  1. Create AMI from on-premise VM
  2. Launch EC2 instances
  3. Update DNS
  4. Validate

**Databases:**
- Approach: AWS DMS with replication
- Downtime: <4 hours
- Steps:
  1. Set up RDS instance
  2. Start DMS replication
  3. Validate data sync
  4. Cutover during maintenance window

**Microservices:**
- Approach: Containerize and deploy to ECS/EKS
- Downtime: Zero (blue-green deployment)
- Steps:
  1. Containerize application
  2. Deploy to ECS/EKS
  3. Gradual traffic shift
  4. Decommission on-premise

### Cost Analysis

**On-Premise (Annual):**
- Hardware: $500K
- Data center: $200K
- Maintenance: $100K
- **Total: $800K**

**AWS (Annual):**
- Compute: $300K
- Storage: $100K
- Network: $50K
- **Total: $450K**

**Savings: $350K/year (44%)**

---

## Example 4: Framework Upgrade (React 16 to React 18)

### Context
- **Current**: React 16.14
- **Target**: React 18.2
- **Codebase**: 50K LOC
- **Timeline**: 4 weeks
- **Downtime tolerance**: Zero

### Migration Plan

**Approach: Phased Upgrade**

**Week 1: Preparation**
1. Audit dependencies
2. Identify breaking changes
3. Update testing strategy
4. Create feature flags for new features
5. Set up monitoring

**Week 2: Upgrade Dependencies**
1. Update React to 18
2. Update React DOM to 18
3. Update related libraries (React Router, Redux, etc.)
4. Fix TypeScript errors
5. Fix ESLint errors

**Week 3: Fix Breaking Changes**
1. Replace deprecated APIs
2. Update lifecycle methods
3. Fix automatic batching issues
4. Update tests
5. Fix any runtime errors

**Week 4: Testing and Deployment**
1. Full testing cycle
2. Performance testing
3. Deploy to staging
4. UAT
5. Deploy to production
6. Monitor closely

### Breaking Changes

**1. Automatic Batching**
```javascript
// Before (React 16): Multiple renders
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // Renders twice
}, 1000);

// After (React 18): Single render
// No code change needed, but verify behavior
```

**2. Stricter Hydration**
```javascript
// Fix: Ensure server and client render the same
const [isClient, setIsClient] = useState(false);

useEffect(() => {
  setIsClient(true);
}, []);

return isClient ? <ClientOnlyComponent /> : null;
```

**3. Concurrent Features (Optional)**
```javascript
// Enable concurrent features
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

### Testing Strategy

**Unit Tests:**
- All existing tests must pass
- Add tests for new concurrent features

**Integration Tests:**
- Test all user workflows
- Test with concurrent features enabled

**Performance Tests:**
- Measure render performance
- Compare to React 16 baseline
- Verify improvements

### Rollback Plan

**If issues found:**
1. Revert to React 16 (via Git)
2. Redeploy previous version
3. Investigate issues
4. Fix and retry

**Rollback time: <30 minutes**