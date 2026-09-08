# Data Architecture Review - Step-by-Step Instructions

## Overview

This skill helps you systematically review data architecture including data models, storage choices, and data flows for correctness, efficiency, and scalability.

## Step-by-Step Workflow

### Step 1: Understand Data Requirements (30-60 minutes)

**Actions:**
1. Define data characteristics (4 Vs):
   - **Volume**: Current and projected data size
   - **Velocity**: Data growth rate
   - **Variety**: Types of data (structured, semi-structured, unstructured)
   - **Veracity**: Data quality requirements
2. Document access patterns:
   - Read/write ratio
   - Query patterns (lookups, joins, aggregations, search)
   - Latency requirements
   - Consistency requirements
3. Identify compliance requirements:
   - Data retention policies
   - Privacy regulations (GDPR, CCPA, HIPAA)
   - Data residency requirements
   - Audit requirements
4. Define scale requirements:
   - Current: users, transactions/sec, data size
   - Projected (1 year): expected growth
   - Peak load: maximum expected load

**Outputs:**
- Data requirements document
- Access patterns documentation
- Compliance checklist

### Step 2: Review Data Model Design (1-2 hours)

**Actions:**
1. Review entity design:
   - Does each entity represent a single concept?
   - Are entity boundaries clear?
   - Is granularity appropriate?
   - Is normalization level appropriate for use case?

2. Review relationships:
   - Are cardinalities correct (1:1, 1:N, N:M)?
   - Is referential integrity enforced?
   - Are cascading deletes appropriate?
   - Are bidirectional relationships necessary?

3. Review attributes:
   - Are data types appropriate?
   - Is nullability explicit?
   - Are defaults sensible?
   - Are constraints defined?
   - Enums vs. lookup tables - appropriate choice?

4. Check for common issues:
   - Over-normalization (too many joins)
   - Under-normalization (data duplication)
   - Missing audit fields (created_at, updated_at)
   - Unclear naming
   - Wrong data types

**Outputs:**
- Data model assessment
- List of data model issues
- Recommendations for improvements

### Step 3: Review Database Schema Design (1-2 hours)

**Actions:**
1. **For Relational Databases:**
   - Check normalization (1NF, 2NF, 3NF)
   - Review primary keys (every table has one)
   - Review foreign keys (relationships indexed)
   - Review indexes:
     * Are common WHERE clauses indexed?
     * Are JOIN columns indexed?
     * Are ORDER BY columns indexed?
     * Are there too many indexes (write overhead)?
   - Review partitioning strategy:
     * Is partitioning needed for large tables?
     * Is partition key appropriate?

2. **For NoSQL Databases:**
   - **Document stores**: Review document structure, embedded vs. referenced
   - **Key-value stores**: Review key design, TTL usage
   - **Column family**: Review partition key, clustering key
   - **Graph databases**: Review node labels, relationship types

3. Check for common issues:
   - Missing indexes on foreign keys
   - Too many indexes
   - No partitioning for large tables
   - Using GUID/UUID as primary key (fragmentation)
   - No archival strategy

**Outputs:**
- Schema assessment
- Index recommendations
- Partitioning recommendations

### Step 4: Review Storage Technology Choices (1-2 hours)

**Actions:**
1. For each data store, validate choice:
   - **Relational**: Appropriate for structured, relational data?
   - **Document**: Appropriate for flexible schema, hierarchical data?
   - **Key-value**: Appropriate for simple lookups, caching?
   - **Column family**: Appropriate for write-heavy, time-series data?
   - **Graph**: Appropriate for highly connected data?
   - **Search engine**: Appropriate for full-text search?

2. Evaluate polyglot persistence:
   - Are multiple databases necessary?
   - Is data synchronization handled?
   - Is operational complexity justified?
   - Are costs reasonable?

3. Consider alternatives:
   - Would a different database be better?
   - Can we consolidate databases?
   - Are we using the right tool for each job?

**Outputs:**
- Storage technology assessment
- Recommendations for changes (if any)
- Justification for choices

### Step 5: Review Data Flows (1-2 hours)

**Actions:**
1. **Data ingestion:**
   - Batch vs. streaming - appropriate?
   - Is data validated at ingestion?
   - Is error handling robust?
   - Are duplicate messages handled (idempotency)?
   - Is backpressure handled?

2. **Data transformation (ETL/ELT):**
   - Is extraction efficient?
   - Is transformation logic correct?
   - Is loading optimized (bulk loading)?
   - Is orchestration appropriate?
   - Is data quality monitored?

3. **Data synchronization:**
   - Is CDC (Change Data Capture) used?
   - Are dual writes avoided?
   - Is eventual consistency acceptable?
   - Are conflicts handled?

4. **Data access:**
   - Direct database access or API layer?
   - Is caching used for hot data?
   - Are read replicas used for read-heavy workloads?
   - Is CQRS appropriate?

**Outputs:**
- Data flow diagrams
- Data flow assessment
- Recommendations for improvements

### Step 6: Review Data Quality and Governance (1-2 hours)

**Actions:**
1. **Data quality:**
   - Are inputs validated?
   - Are database constraints enforced?
   - Is data quality monitored?
   - Are there data quality SLAs?

2. **Data security:**
   - Is data encrypted at rest?
   - Is data encrypted in transit?
   - Is access control enforced (RBAC)?
   - Is audit logging implemented?
   - Is data masked in non-production?
   - Are backups encrypted?

3. **Data privacy:**
   - Is PII identified and classified?
   - Is data minimization practiced?
   - Can users request data deletion (GDPR)?
   - Is data retention automated?
   - Is consent tracked?
   - Is data residency enforced?

4. Check for compliance gaps:
   - GDPR: Right to deletion, data portability
   - HIPAA: PHI encryption, audit logging
   - PCI-DSS: Cardholder data protection
   - SOC 2: Access controls, encryption

**Outputs:**
- Data quality assessment
- Security assessment
- Privacy assessment
- Compliance gap analysis

### Step 7: Review Performance and Scalability (1-2 hours)

**Actions:**
1. **Query performance:**
   - Identify slow queries (>100ms)
   - Analyze explain plans
   - Identify missing indexes
   - Identify N+1 queries
   - Recommend query optimizations

2. **Write performance:**
   - Are batch inserts used?
   - Are async writes used for non-critical data?
   - Is connection pooling configured?
   - Are write-heavy tables optimized?

3. **Scaling strategy:**
   - Can we scale vertically (larger instance)?
   - Should we use read replicas?
   - Should we implement sharding?
   - Should we add caching?
   - Should we archive old data?

4. **Cost optimization:**
   - Are instances right-sized?
   - Should we use reserved instances?
   - Should we use storage tiering?
   - Can we reduce storage with compression?
   - Can we optimize queries to reduce compute?

**Outputs:**
- Performance assessment
- Scaling recommendations
- Cost optimization recommendations

### Step 8: Review Backup and Disaster Recovery (30-60 minutes)

**Actions:**
1. **Backup strategy:**
   - What is backup frequency?
   - What is retention period?
   - What backup type (full, incremental)?
   - Where are backups stored?
   - Are backups encrypted?
   - Are backups tested regularly?

2. **Disaster recovery:**
   - What is RTO (Recovery Time Objective)?
   - What is RPO (Recovery Point Objective)?
   - Is failover automatic or manual?
   - Is multi-region replication needed?
   - Are recovery procedures documented?

3. Validate backup and DR:
   - Test restore from backup
   - Test failover procedure
   - Measure actual RTO and RPO
   - Update runbooks

**Outputs:**
- Backup and DR assessment
- Recommendations for improvements
- Updated runbooks

### Step 9: Document Findings and Recommendations (1-2 hours)

**Actions:**
1. Categorize issues by severity:
   - **Critical**: Data loss risk, security vulnerabilities, compliance violations
   - **High**: Performance issues, scalability bottlenecks
   - **Medium**: Suboptimal design, missing best practices
   - **Low**: Minor optimizations, nice-to-haves

2. For each issue, document:
   - **Problem**: What is the issue?
   - **Impact**: Why is it a problem?
   - **Recommendation**: What should be done?
   - **Example**: Show before/after (if applicable)
   - **Effort**: Small/Medium/Large
   - **Priority**: Critical/High/Medium/Low

3. Create migration plan (if needed):
   - What changes are needed?
   - What is the sequence?
   - What are the risks?
   - What is the rollback plan?
   - What is the timeline?

4. Prepare presentation:
   - Executive summary
   - Key findings
   - Recommendations
   - Migration plan
   - Cost implications

**Outputs:**
- Complete data architecture review report
- Prioritized recommendations
- Migration plan (if needed)
- Presentation deck

## Tips for Success

- **Understand access patterns first**: Don't optimize without knowing how data is accessed
- **Use data, not assumptions**: Base recommendations on actual query patterns and metrics
- **Consider the full lifecycle**: Ingestion, storage, access, archival, deletion
- **Think about scale**: Will this work at 10x, 100x current scale?
- **Balance consistency and performance**: Strong consistency has performance cost
- **Don't over-engineer**: Choose simplest solution that meets requirements
- **Consider operational complexity**: Can the team operate this?
- **Plan for failure**: Backups, replication, disaster recovery
- **Compliance is not optional**: GDPR, HIPAA, PCI-DSS must be addressed
- **Test your assumptions**: Load test, backup restore test, failover test

## Common Pitfalls to Avoid

- Optimizing prematurely before understanding access patterns
- Choosing wrong database type for the use case
- Missing indexes causing slow queries
- Too many indexes causing slow writes
- No sharding strategy for future growth
- Ignoring data archival (data grows forever)
- Using weak data types (VARCHAR for everything)
- No audit fields (can't track changes)
- Exposing internal IDs publicly
- Backups exist but never tested
- Ignoring compliance requirements
- Dual writes without transaction coordination
- Not considering operational complexity

## Validation Checklist

Before finalizing your review:

- [ ] Data requirements are documented and validated
- [ ] Access patterns are understood
- [ ] Data model is reviewed for correctness
- [ ] Database schema follows best practices
- [ ] Storage technology choices are justified
- [ ] Data flows are documented and efficient
- [ ] Data quality mechanisms are in place
- [ ] Security and privacy requirements are met
- [ ] Performance and scalability are addressed
- [ ] Backup and disaster recovery are tested
- [ ] Cost implications are calculated
- [ ] Migration plan is realistic (if needed)
- [ ] Review is validated with engineering team
