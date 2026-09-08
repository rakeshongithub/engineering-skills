# Data Architecture Review

## Purpose

Review data architecture including data models, storage choices, and data flows for correctness, efficiency, and scalability.

## When to Use

- Before implementing a new data model or database schema
- When planning data storage strategy for a new system
- During architecture reviews focused on data layer
- When experiencing data-related performance issues
- Before major database migrations or technology changes
- When scaling data storage to handle growth
- When establishing data governance and compliance requirements
- When integrating multiple data sources or systems

## When NOT to Use

- For API design (use api-design-review instead)
- For application logic review (use code-review instead)
- For data analytics or BI strategy (use different skill)
- For trivial CRUD applications with simple data models
- After database is already in production with significant data (migration becomes costly)

## Inputs

- **data-model**: Entity-relationship diagrams, database schemas, data dictionaries
- **data-flows**: How data moves through the system, ETL processes
- **storage-choices**: Database technologies, storage solutions
- **data-requirements**: Volume, velocity, variety, retention, compliance needs
- **access-patterns**: How data is queried, read/write ratios, query patterns

## Expected Outputs

- **data-issues**: Problems with data model, schema design, storage choices
- **optimization-opportunities**: Performance improvements, cost reductions
- **recommendations**: Specific changes to improve data architecture
- **migration-plan**: Steps to implement recommended changes (if needed)
- **data-governance**: Recommendations for data quality, security, compliance

## Workflow

### 1. Understand Data Requirements

**Data characteristics (4 Vs):**
- **Volume**: How much data? (GB, TB, PB)
- **Velocity**: How fast does data grow? (MB/day, GB/day)
- **Variety**: What types of data? (structured, semi-structured, unstructured)
- **Veracity**: What quality requirements? (accuracy, consistency, completeness)

**Access patterns:**
- **Read/write ratio**: 90/10, 50/50, 10/90?
- **Query patterns**: Simple lookups, complex joins, aggregations, full-text search?
- **Latency requirements**: Real-time (<100ms), near real-time (<1s), batch (minutes/hours)?
- **Consistency requirements**: Strong consistency, eventual consistency, read-your-writes?

**Compliance and governance:**
- **Data retention**: How long to keep data?
- **Data privacy**: GDPR, CCPA, HIPAA requirements?
- **Data residency**: Geographic restrictions?
- **Audit requirements**: Who accessed what data when?

**Scale requirements:**
- **Current**: Users, transactions/sec, data size
- **Projected (1 year)**: Expected growth
- **Peak load**: Maximum expected load

### 2. Review Data Model Design

#### Entity Design
- **Single Responsibility**: Each entity represents one concept
- **Clear boundaries**: Well-defined entity boundaries
- **Appropriate granularity**: Not too fine-grained or coarse-grained
- **Normalized vs. denormalized**: Appropriate for use case

#### Relationships
- **Cardinality**: One-to-one, one-to-many, many-to-many correctly modeled
- **Referential integrity**: Foreign keys, cascading deletes
- **Relationship ownership**: Clear parent-child relationships
- **Bidirectional relationships**: Necessary or can be unidirectional?

#### Attributes
- **Data types**: Appropriate types (INT vs. BIGINT, VARCHAR vs. TEXT)
- **Nullability**: Explicit about what can be null
- **Defaults**: Sensible default values
- **Constraints**: Check constraints, unique constraints
- **Enums vs. lookup tables**: When to use each

#### Common Issues
- **Over-normalization**: Too many joins for common queries
- **Under-normalization**: Data duplication, update anomalies
- **Missing indexes**: Slow queries
- **Wrong data types**: Using VARCHAR for numbers, TEXT for short strings
- **No audit fields**: Missing created_at, updated_at, deleted_at
- **Unclear naming**: Ambiguous table or column names

### 3. Review Database Schema Design

#### Relational Database (SQL)

**Normalization:**
- **1NF**: Atomic values, no repeating groups
- **2NF**: No partial dependencies
- **3NF**: No transitive dependencies
- **When to denormalize**: For read-heavy workloads, reporting

**Indexes:**
- **Primary keys**: Every table has a primary key
- **Foreign keys**: Relationships are indexed
- **Query optimization**: Indexes for common WHERE, JOIN, ORDER BY clauses
- **Composite indexes**: For multi-column queries
- **Index overhead**: Balance query performance vs. write performance

**Partitioning:**
- **Horizontal partitioning (sharding)**: Split data across multiple databases
- **Vertical partitioning**: Split columns into separate tables
- **Range partitioning**: By date, ID range
- **Hash partitioning**: By hash of key

**Common Issues:**
- Missing indexes on foreign keys
- Too many indexes (slows down writes)
- No partitioning strategy for large tables
- Using GUID/UUID as primary key (index fragmentation)
- No archival strategy for old data

#### NoSQL Database

**Document Database (MongoDB, DynamoDB):**
- **Document structure**: Embedded vs. referenced documents
- **Denormalization**: Duplicate data for read performance
- **Indexes**: Compound indexes, text indexes
- **Sharding key**: Evenly distributed, supports common queries

**Key-Value Store (Redis, DynamoDB):**
- **Key design**: Hierarchical keys, namespacing
- **TTL**: Automatic expiration for cache
- **Data structures**: Strings, hashes, lists, sets, sorted sets

**Column Family (Cassandra, HBase):**
- **Partition key**: Even distribution, no hot spots
- **Clustering key**: Sort order within partition
- **Denormalization**: Query-driven data modeling
- **Compaction strategy**: Size-tiered, leveled, time-window

**Graph Database (Neo4j, Neptune):**
- **Node labels**: Clear node types
- **Relationship types**: Explicit relationship semantics
- **Properties**: Appropriate properties on nodes and edges
- **Indexes**: Indexes on frequently queried properties

### 4. Review Storage Technology Choices

#### Relational vs. NoSQL

**Use Relational (PostgreSQL, MySQL) when:**
- Strong consistency required
- Complex joins and transactions needed
- Data is highly structured and relational
- ACID guarantees are critical
- Schema is stable and well-defined

**Use Document Store (MongoDB, DynamoDB) when:**
- Schema is flexible or evolving
- Data is hierarchical or nested
- Read-heavy workload
- Horizontal scaling is needed
- Eventual consistency is acceptable

**Use Key-Value Store (Redis, Memcached) when:**
- Simple key-based lookups
- Caching layer
- Session storage
- Real-time analytics (counters, leaderboards)
- Pub/sub messaging

**Use Column Family (Cassandra, HBase) when:**
- Write-heavy workload
- Time-series data
- Massive scale (petabytes)
- High availability required
- Eventual consistency is acceptable

**Use Graph Database (Neo4j, Neptune) when:**
- Highly connected data (social networks, recommendations)
- Complex relationship queries
- Pattern matching (fraud detection)
- Path finding (routing, network analysis)

**Use Search Engine (Elasticsearch, Solr) when:**
- Full-text search required
- Faceted search, filtering
- Log aggregation and analysis
- Real-time analytics

#### Polyglot Persistence

**Use multiple databases when:**
- Different data types have different requirements
- Example: PostgreSQL for transactional data + Redis for caching + Elasticsearch for search

**Challenges:**
- Data synchronization
- Consistency across stores
- Operational complexity
- Cost

### 5. Review Data Flows

#### Data Ingestion
- **Batch vs. streaming**: Appropriate for use case
- **Data validation**: Validate at ingestion
- **Error handling**: Dead letter queues, retry logic
- **Idempotency**: Handle duplicate messages
- **Backpressure**: Handle producer faster than consumer

#### Data Transformation (ETL/ELT)
- **Extract**: Efficient data extraction
- **Transform**: Business logic, data quality
- **Load**: Bulk loading, upserts
- **Orchestration**: Airflow, Dagster, Step Functions
- **Monitoring**: Data quality checks, SLA monitoring

#### Data Synchronization
- **CDC (Change Data Capture)**: Debezium, AWS DMS
- **Event sourcing**: Append-only event log
- **Dual writes**: Avoid (consistency issues)
- **Saga pattern**: Distributed transactions

#### Data Access
- **Direct database access**: For simple CRUD
- **API layer**: For complex logic, access control
- **Caching**: Redis, Memcached for hot data
- **Read replicas**: For read-heavy workloads
- **CQRS**: Separate read and write models

### 6. Review Data Quality and Governance

#### Data Quality
- **Accuracy**: Data is correct
- **Completeness**: No missing required fields
- **Consistency**: Data is consistent across systems
- **Timeliness**: Data is up-to-date
- **Validity**: Data conforms to business rules

**Mechanisms:**
- Input validation at API layer
- Database constraints (NOT NULL, CHECK, UNIQUE)
- Data quality monitoring and alerting
- Regular data audits

#### Data Security
- **Encryption at rest**: Database encryption, field-level encryption
- **Encryption in transit**: TLS for all connections
- **Access control**: Role-based access control (RBAC)
- **Audit logging**: Who accessed what data when
- **Data masking**: Mask sensitive data in non-production
- **Backup encryption**: Encrypted backups

#### Data Privacy
- **PII identification**: Identify and classify PII
- **Data minimization**: Collect only necessary data
- **Right to deletion**: Support GDPR deletion requests
- **Data retention**: Automatic deletion after retention period
- **Consent management**: Track user consent
- **Data residency**: Store data in required regions

### 7. Review Performance and Scalability

#### Query Performance
- **Slow query analysis**: Identify slow queries (>100ms)
- **Explain plans**: Analyze query execution plans
- **Missing indexes**: Add indexes for common queries
- **N+1 queries**: Use eager loading, batching
- **Query optimization**: Rewrite inefficient queries

#### Write Performance
- **Batch inserts**: Bulk loading instead of row-by-row
- **Async writes**: Use message queues for non-critical writes
- **Write-through vs. write-back caching**: Appropriate strategy
- **Connection pooling**: Reuse database connections

#### Scaling Strategy
- **Vertical scaling**: Larger database instance (short-term)
- **Read replicas**: For read-heavy workloads
- **Sharding**: For write-heavy workloads
- **Caching**: Redis, CDN for frequently accessed data
- **Archival**: Move old data to cheaper storage

#### Cost Optimization
- **Right-sizing**: Appropriate instance sizes
- **Reserved instances**: For predictable workloads
- **Storage tiering**: Hot, warm, cold storage
- **Compression**: Reduce storage costs
- **Query optimization**: Reduce compute costs

### 8. Review Backup and Disaster Recovery

#### Backup Strategy
- **Frequency**: Continuous, hourly, daily?
- **Retention**: How long to keep backups?
- **Backup type**: Full, incremental, differential
- **Backup location**: Same region, cross-region, offline
- **Encryption**: Encrypted backups
- **Testing**: Regular restore tests

#### Disaster Recovery
- **RTO (Recovery Time Objective)**: How long to restore?
- **RPO (Recovery Point Objective)**: How much data loss acceptable?
- **Failover strategy**: Automatic or manual?
- **Multi-region replication**: For high availability
- **Runbooks**: Documented recovery procedures

## Decision Framework

### Normalization vs. Denormalization

**Normalize when:**
- Write-heavy workload
- Data consistency is critical
- Storage cost is a concern
- Data is highly relational

**Denormalize when:**
- Read-heavy workload
- Query performance is critical
- Data is mostly read-only
- Joins are expensive

### Indexing Strategy

**Add index when:**
- Column is frequently in WHERE clause
- Column is frequently in JOIN condition
- Column is frequently in ORDER BY
- Query is slow without index

**Don't add index when:**
- Column has low cardinality (few distinct values)
- Table has high write volume
- Column is rarely queried
- Index would be larger than table

### Sharding Strategy

**Shard when:**
- Single database can't handle write load
- Data size exceeds single database capacity
- Need to scale horizontally

**Sharding key selection:**
- **Even distribution**: No hot shards
- **Query support**: Supports common queries
- **Stable**: Doesn't change over time
- **Examples**: User ID, tenant ID, geographic region

## Quality Checklist

- [ ] Data requirements are clearly defined (volume, velocity, variety)
- [ ] Access patterns are documented (read/write ratio, query patterns)
- [ ] Data model is reviewed for correctness and efficiency
- [ ] Database schema follows best practices (normalization, indexes)
- [ ] Storage technology choices are appropriate for use case
- [ ] Data flows are efficient and reliable
- [ ] Data quality mechanisms are in place
- [ ] Data security and privacy requirements are met
- [ ] Performance and scalability are addressed
- [ ] Backup and disaster recovery strategy is defined
- [ ] Cost implications are considered
- [ ] Migration plan is provided (if changes needed)

## Common Mistakes

- **Premature optimization**: Optimizing before understanding access patterns
- **Wrong database choice**: Using SQL for document data or NoSQL for relational data
- **Missing indexes**: Slow queries due to missing indexes
- **Too many indexes**: Slow writes due to index overhead
- **No sharding strategy**: Can't scale when needed
- **Ignoring data growth**: No plan for data archival or partitioning
- **Weak data types**: Using VARCHAR for everything
- **No audit fields**: Can't track when data was created/modified
- **Exposing internal IDs**: Using auto-increment IDs as public identifiers
- **No backup testing**: Backups exist but restores never tested
- **Ignoring compliance**: Not considering GDPR, HIPAA, etc.
- **Dual writes**: Writing to multiple databases without transaction coordination

## Examples

See [examples.md](examples.md) for detailed examples of data architecture reviews for various scenarios.

## Related Skills

- **Requires**: 
  - requirements-analysis (to understand data requirements)
- **Commonly followed by**: 
  - architecture-decision (to decide on data architecture approach)
  - migration-planning (if changes are needed)
  - data-modeling (to create detailed data models)
- **Alternative to**: None (this is the primary data architecture review skill)
- **Works with**: 
  - architecture-review (for overall architecture assessment)
  - api-design-review (APIs often reflect data model)
  - scalability-analysis (data layer is often the bottleneck)
  - security-architecture-review (data security is critical)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
data-architecture-review
        ↓
architecture-decision
        ↓
data-modeling
        ↓
implementation
```

Alternative workflow for existing systems:

```
architecture-review
        ↓
data-architecture-review
        ↓
performance-analysis
        ↓
migration-planning
```

## Evaluation Criteria

### Completeness
- Are all data entities reviewed?
- Are data flows documented?
- Are storage choices justified?
- Is a migration plan provided (if needed)?

### Correctness
- Is the data model correct?
- Are relationships properly defined?
- Are data types appropriate?
- Are constraints enforced?

### Efficiency
- Are queries optimized?
- Are indexes appropriate?
- Is denormalization used where beneficial?
- Are storage choices cost-effective?

### Scalability
- Can the data architecture handle growth?
- Is there a sharding strategy?
- Are there performance bottlenecks?
- Is there a data archival strategy?
