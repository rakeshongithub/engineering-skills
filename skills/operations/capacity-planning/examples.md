# Capacity Planning - Comprehensive Examples

This document provides four detailed, real-world examples of capacity planning covering diverse scenarios and industries.

## Example 1: E-Commerce Platform Capacity Planning for Holiday Season

[See SKILL.md Example 1 for complete details]

**Summary**: Mid-sized e-commerce company planning capacity for holiday season with 95% traffic growth, Black Friday peak of 5x normal traffic, and mobile app launch. Implemented phased capacity scaling with auto-scaling, achieving 99.97% uptime and handling 238,000 req/s peak.

**Key Highlights**:
- Traffic growth: 95% (user growth + mobile app)
- Black Friday peak: 243,750 req/s (5x normal peak)
- Capacity headroom: 30% for uncertainty
- Cost: $168,000 for 2-month holiday season (5% under budget)
- Results: 99.97% uptime, zero incidents, successful auto-scaling

---

## Example 2: SaaS Platform Database Capacity Planning

[See SKILL.md Example 2 for complete details]

**Summary**: B2B SaaS platform with degrading database performance planning for 3x user growth (250K to 750K users). Implemented optimization-first approach, then phased scaling with eventual sharding architecture for long-term scalability.

**Key Highlights**:
- User growth: 3x (250K to 750K users)
- Optimization first: Reduced query time from 300ms to 80ms
- Short-term: Vertical scaling + read replicas
- Long-term: Sharding architecture for 2M+ users
- Cost per user: Decreased 35% after sharding

---

## Example 3: Video Streaming Platform CDN Capacity Planning

[See SKILL.md Example 3 for complete details]

**Summary**: Video streaming platform expanding internationally to 5 countries while launching live streaming and 4K support. Implemented multi-region CDN architecture with cache optimization, reducing costs 37% while supporting 2.7x traffic growth.

**Key Highlights**:
- Traffic growth: 2.7x (50 TB to 135 TB/month)
- International expansion: 5 new countries
- Cache hit ratio: Improved from 65% to 87%
- Cost reduction: 37% vs. unoptimized projection
- Latency: < 80ms globally (beat 100ms target)

---

## Example 4: Machine Learning Training Infrastructure Capacity Planning

[See SKILL.md Example 4 for complete details]

**Summary**: AI/ML company planning GPU infrastructure for 2x team growth (50 to 100 engineers) and larger models (up to 1T parameters). Implemented multi-tier architecture with spot instances, reducing queue time from weeks to hours while maintaining cost per job.

**Key Highlights**:
- Team growth: 2x (50 to 100 ML engineers)
- Queue time: Reduced from 2-3 weeks to < 12 hours
- GPU capacity: 4x increase (64 to 256 GPUs)
- Spot instance adoption: 75% of jobs
- Cost per job: Maintained at ~$600 despite 2x growth

---

## Additional Example Scenarios

### Example 5: Global Gaming Platform Capacity Planning

**Context**: Multiplayer online game with 5 million daily active users planning for new game mode launch and geographic expansion.

**Current State**:
- Infrastructure: 200 game servers across 3 regions (US, EU, Asia)
- Concurrent players (peak): 500,000
- Average session duration: 45 minutes
- Current cost: $80,000/month
- Performance: 50ms average latency, 99.5% uptime

**Requirements**:
- Launch battle royale mode (expect 2x concurrent players)
- Expand to 2 new regions (South America, Australia)
- Support seasonal events (3x traffic spikes)
- Maintain < 50ms latency globally
- Target 99.9% uptime

**Capacity Planning Approach**:

**Step 1: Workload Analysis**
```markdown
Game modes:
- Standard mode: 10 players/server, 30 min average
- Battle royale: 100 players/server, 20 min average
- Seasonal events: 50 players/server, 15 min average

Resource consumption:
- CPU: 4 cores per game server
- Memory: 16 GB per game server
- Network: 10 Mbps per server
- Storage: 100 GB per server (game assets)
```

**Step 2: Demand Forecast**
```markdown
Current: 500,000 concurrent players

Battle royale launch:
- Expected adoption: 60% of players
- Concurrent players: +300,000
- Total: 800,000 concurrent

Geographic expansion:
- South America: +100,000 concurrent
- Australia: +50,000 concurrent
- Total: 950,000 concurrent

Seasonal events (3x spike):
- Peak concurrent: 2,850,000 players
```

**Step 3: Capacity Sizing**
```markdown
Servers needed:

Standard mode (40% of players):
- Players: 380,000
- Servers: 380,000 / 10 = 38,000 servers

Battle royale (60% of players):
- Players: 570,000
- Servers: 570,000 / 100 = 5,700 servers

Total baseline: 43,700 servers

Seasonal events (3x):
- Total servers: 131,100 servers

With 30% headroom:
- Baseline: 56,810 servers
- Seasonal peak: 170,430 servers
```

**Step 4: Cost Optimization**
```markdown
Strategy: Hybrid on-demand + spot instances

Baseline (always-on): 56,810 servers
- Reserved instances (1-year): 40,000 servers
- On-demand: 16,810 servers

Seasonal burst: 113,620 servers
- Spot instances: 113,620 servers
- Interruption handling: 5-minute grace period, auto-reconnect

Cost calculation:
- Reserved: 40,000 × $0.10/hr × 730 hrs = $2,920,000/mo
- On-demand: 16,810 × $0.15/hr × 730 hrs = $1,840,000/mo
- Spot (seasonal, 20% of time): 113,620 × $0.03/hr × 146 hrs = $498,000/mo
- Total: $5,258,000/mo

Cost per player:
- Baseline: $5,258,000 / 950,000 = $5.54/player/mo
- Seasonal: $5,258,000 / 2,850,000 = $1.84/player/mo
```

**Results**:
- Successfully launched battle royale mode
- Expanded to 5 regions globally
- Handled seasonal event with 2.9M concurrent players
- Maintained < 45ms average latency globally
- Achieved 99.92% uptime
- Cost per player decreased 15% vs. projection

---

### Example 6: Financial Services Real-Time Trading Platform

**Context**: High-frequency trading platform needs to plan capacity for regulatory reporting requirements and increased trading volume.

**Current State**:
- Trading volume: 10 million trades/day
- Peak throughput: 50,000 trades/second
- Latency requirement: < 1ms p99
- Current infrastructure: On-premises data center
- Cost: $500,000/month

**Requirements**:
- Support 5x trading volume growth
- Implement real-time regulatory reporting
- Maintain < 1ms latency
- 99.99% uptime (< 4.3 min downtime/month)
- Full disaster recovery capability

**Capacity Planning Approach**:

**Step 1: Performance Requirements**
```markdown
Latency budget breakdown:
- Network: 0.2ms
- Application processing: 0.3ms
- Database write: 0.3ms
- Monitoring/logging: 0.2ms
- Total: 1.0ms (p99)

Throughput requirements:
- Current peak: 50,000 trades/sec
- 5x growth: 250,000 trades/sec
- With 50% headroom: 375,000 trades/sec
```

**Step 2: Infrastructure Design**
```markdown
Compute tier:
- High-frequency servers with NVMe storage
- 100 servers × (32 cores, 256 GB RAM)
- Dedicated 100 Gbps network per server
- NUMA-optimized for low latency

Database tier:
- In-memory database (Redis Enterprise)
- 20 nodes × (64 cores, 512 GB RAM)
- Persistent storage: NVMe SSD array
- Active-active replication

Network tier:
- 100 Gbps backbone
- Sub-millisecond switching
- Dedicated VLAN per trading desk
- Direct market connectivity

Disaster recovery:
- Active-active data centers (2 locations)
- Real-time replication
- Automatic failover < 1 second
```

**Step 3: Cost Analysis**
```markdown
On-premises infrastructure:
- Servers: $3,000,000 (capital)
- Storage: $1,500,000 (capital)
- Network: $2,000,000 (capital)
- Total capex: $6,500,000
- Annual opex: $2,000,000
- 3-year TCO: $12,500,000

Cloud alternative (AWS):
- Compute: $800,000/month
- Storage: $200,000/month
- Network: $300,000/month
- Total: $1,300,000/month
- 3-year TCO: $46,800,000

Decision: On-premises (3.7x cheaper over 3 years)
```

**Results**:
- Successfully handling 280,000 trades/second
- Maintained < 0.8ms p99 latency
- Achieved 99.995% uptime (2.6 min downtime in 6 months)
- Disaster recovery tested quarterly with < 1s failover
- Regulatory reporting implemented with zero impact on latency

---

### Example 7: Healthcare SaaS Electronic Health Records

**Context**: Healthcare SaaS provider with 500 hospital customers needs to plan capacity for HIPAA-compliant infrastructure and rapid customer growth.

**Current State**:
- Customers: 500 hospitals
- Active users: 100,000 healthcare providers
- Records: 50 million patient records
- Storage: 200 TB
- Current cost: $150,000/month

**Requirements**:
- Grow to 2,000 hospitals (4x growth)
- Support 500,000 concurrent users
- HIPAA compliance (encryption, audit logging, access controls)
- 99.95% uptime
- Data residency requirements (US only)
- 7-year data retention

**Capacity Planning Approach**:

**Step 1: Data Growth Modeling**
```markdown
Current state:
- 50M records / 500 hospitals = 100K records/hospital
- 200 TB / 50M records = 4 KB/record average

Projected growth:
- Hospitals: 500 → 2,000 (4x)
- Records: 50M → 200M (4x)
- Storage: 200 TB → 800 TB (4x)

Data retention:
- Active data (< 1 year): 200 TB
- Warm data (1-3 years): 300 TB
- Cold data (3-7 years): 300 TB
- Total: 800 TB
```

**Step 2: Compliance Requirements**
```markdown
HIPAA requirements:
- Encryption at rest: AES-256
- Encryption in transit: TLS 1.3
- Access logging: All data access logged
- Audit retention: 7 years
- Access controls: Role-based, MFA
- Data backup: Daily, 7-year retention

Infrastructure implications:
- Dedicated encryption keys per customer
- Separate audit log storage: 50 TB
- Backup storage: 800 TB × 7 years = 5.6 PB
- Compliance overhead: ~20% performance impact
```

**Step 3: Capacity Sizing**
```markdown
Application tier:
- 200 instances (m5.2xlarge)
- Auto-scaling: 100-300 instances
- Load balancers: 3 (multi-AZ)

Database tier:
- PostgreSQL (RDS)
- Master: db.r5.12xlarge
- Read replicas: 10 × db.r5.8xlarge
- Encrypted storage: 1 PB

Storage tier:
- Active: S3 Standard (200 TB)
- Warm: S3 Intelligent-Tiering (300 TB)
- Cold: S3 Glacier (300 TB)
- Backups: S3 Glacier Deep Archive (5.6 PB)

Audit logging:
- CloudWatch Logs: 50 TB/year
- S3 archive: 350 TB (7 years)
```

**Step 4: Cost Analysis**
```markdown
Compute: $45,000/month
Database: $60,000/month
Storage:
- Active (S3 Standard): $4,600/month
- Warm (S3 IT): $3,000/month
- Cold (S3 Glacier): $1,200/month
- Backups (S3 Glacier DA): $5,600/month
Audit logs: $3,000/month
Encryption (KMS): $2,000/month
Network: $15,000/month
Compliance tools: $10,000/month

Total: $149,400/month
Cost per hospital: $74.70/month
```

**Results**:
- Successfully onboarded 2,100 hospitals (exceeded target)
- Supporting 520,000 concurrent users
- Maintained 99.97% uptime
- Passed HIPAA audit with zero findings
- Cost per hospital decreased to $71/month
- Zero security incidents or data breaches

---

### Example 8: IoT Platform for Smart City Infrastructure

**Context**: IoT platform managing smart city infrastructure (traffic lights, sensors, cameras) needs to scale from 1 city to 50 cities.

**Current State**:
- Devices: 10,000 IoT devices (1 city)
- Data ingestion: 100,000 messages/second
- Storage: 10 TB/month
- Current cost: $30,000/month

**Requirements**:
- Scale to 50 cities (500,000 devices)
- Support real-time analytics
- 99.9% uptime
- Edge processing for latency-sensitive applications
- 5-year data retention for analytics

**Capacity Planning Approach**:

**Step 1: Data Ingestion Modeling**
```markdown
Current (1 city):
- Devices: 10,000
- Messages/device/sec: 10
- Total: 100,000 msg/sec
- Message size: 1 KB
- Data rate: 100 MB/sec = 259 TB/month

Projected (50 cities):
- Devices: 500,000
- Messages/sec: 5,000,000
- Data rate: 5 GB/sec = 12,960 TB/month
```

**Step 2: Architecture Design**
```markdown
Edge tier (per city):
- Edge gateway: 10 per city
- Processing: Real-time filtering, aggregation
- Storage: 1 TB local cache
- Reduces data sent to cloud by 90%

Cloud ingestion tier:
- Kafka cluster: 50 brokers
- Throughput: 500 MB/sec
- Retention: 7 days

Processing tier:
- Stream processing: Apache Flink
- Batch processing: Apache Spark
- Real-time analytics: ClickHouse

Storage tier:
- Hot data (< 1 month): 1.3 PB
- Warm data (1-12 months): 15 PB
- Cold data (1-5 years): 60 PB
- Total: 76.3 PB
```

**Step 3: Cost Optimization**
```markdown
Edge processing savings:
- Without edge: 12,960 TB/month to cloud
- With edge: 1,296 TB/month to cloud (90% reduction)
- Data transfer savings: $200,000/month

Storage tiering:
- Hot (S3 Standard): $30,000/month
- Warm (S3 IA): $90,000/month
- Cold (S3 Glacier): $60,000/month
- Total: $180,000/month

Vs. all hot storage: $1,760,000/month
Savings: $1,580,000/month (90%)
```

**Results**:
- Successfully deployed to 52 cities
- Managing 520,000 IoT devices
- Processing 5.2M messages/second
- Edge processing reduced cloud costs by 85%
- Achieved 99.94% uptime
- Real-time analytics with < 1 second latency

---

## Comparison Matrix

| Example | Industry | Scale | Key Challenge | Solution | Cost Impact |
|---------|----------|-------|---------------|----------|-------------|
| 1. E-Commerce | Retail | 2x seasonal | Holiday traffic spike | Auto-scaling + headroom | +97% seasonal |
| 2. SaaS DB | B2B SaaS | 3x users | Database bottleneck | Optimization + sharding | -35% per user |
| 3. Video CDN | Media | 2.7x traffic | Global expansion | Multi-region + caching | -37% optimized |
| 4. ML Training | AI/ML | 2x team | GPU queue time | Multi-tier + spot | Maintained |
| 5. Gaming | Gaming | 3x seasonal | Global latency | Multi-region + spot | -15% per player |
| 6. Trading | Finance | 5x volume | Sub-ms latency | On-prem + active-active | -74% vs cloud |
| 7. Healthcare | Healthcare | 4x customers | HIPAA compliance | Tiered storage + encryption | -5% per hospital |
| 8. IoT | Smart City | 50x devices | Data ingestion | Edge processing + tiering | -85% vs no edge |

---

## Lessons Learned Across Examples

### Common Success Patterns

1. **Optimization Before Scaling**: Example 2 (SaaS DB) shows optimization reduced needs by 60%
2. **Tiered Architecture**: Examples 4, 7, 8 used tiering for cost optimization
3. **Multi-Region**: Examples 3, 5, 6 used geographic distribution for performance
4. **Headroom Strategy**: All examples maintained 20-50% headroom for growth
5. **Phased Implementation**: All examples used incremental rollout
6. **Cost Optimization**: All examples achieved 15-90% cost savings through optimization
7. **Monitoring**: All examples implemented comprehensive capacity monitoring
8. **Scenario Planning**: All examples planned for multiple growth scenarios

### Common Challenges and Solutions

1. **Challenge**: Unpredictable growth
   - **Solution**: Maintain 30-50% headroom, auto-scaling, scenario planning

2. **Challenge**: Cost overruns
   - **Solution**: Reserved instances, spot instances, storage tiering, optimization

3. **Challenge**: Performance bottlenecks
   - **Solution**: Identify and optimize before scaling, use caching, sharding

4. **Challenge**: Seasonal spikes
   - **Solution**: Auto-scaling, spot instances, pre-warming

5. **Challenge**: Geographic expansion
   - **Solution**: Multi-region architecture, CDN, edge processing

6. **Challenge**: Compliance requirements
   - **Solution**: Purpose-built compliance infrastructure, encryption, audit logging

7. **Challenge**: Database scaling
   - **Solution**: Read replicas, caching, sharding, optimization

8. **Challenge**: Real-time requirements
   - **Solution**: In-memory databases, edge processing, optimized networking

### Key Metrics Across Examples

**Capacity Metrics**:
- Headroom: 20-50% maintained across all examples
- Utilization: 50-70% target for optimal cost/performance
- Growth support: All examples supported 2-50x growth

**Performance Metrics**:
- Uptime: 99.5-99.995% achieved
- Latency: Met or exceeded targets in all cases
- Throughput: Supported projected load with headroom

**Cost Metrics**:
- Optimization savings: 15-90% through various strategies
- Cost per unit: Decreased or maintained in all examples
- ROI: Positive in all cases, payback < 12 months

**Business Metrics**:
- Growth support: Infrastructure enabled business growth
- Zero revenue loss: No capacity-related business impact
- Customer satisfaction: No capacity-related complaints
