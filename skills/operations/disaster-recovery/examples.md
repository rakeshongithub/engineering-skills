# Disaster Recovery - Examples

This document provides comprehensive real-world examples of disaster recovery (DR) implementations with different strategies, RTO/RPO requirements, and complete failover/failback procedures.

---

## Example 1: E-commerce Platform with Warm Standby DR

### Context

**Company:** TechMart E-commerce  
**System:** Product catalog, checkout, and order management  
**Traffic:** 50,000 requests/minute peak (Black Friday)  
**Revenue:** $50,000/hour average, $200,000/hour peak  
**Requirements:**
- **RTO:** 15 minutes (acceptable downtime)
- **RPO:** 5 minutes (acceptable data loss)
- **Compliance:** PCI DSS (payment data protection)
- **Budget:** $60,000/year DR infrastructure

**Business Impact:**
- Revenue loss: $50,000/hour during normal hours
- Revenue loss: $200,000/hour during peak hours
- Customer trust: High (negative reviews, lost customers)
- Regulatory: PCI DSS compliance required

### DR Strategy Selection

**Chosen Strategy:** Warm Standby

**Rationale:**
- **RTO 15 minutes:** Achievable with scaled-down DR site + auto-scaling
- **RPO 5 minutes:** Achievable with asynchronous database replication
- **Cost-effective:** 50% capacity DR site (~60% of primary infrastructure cost)
- **Compliance:** Meets PCI DSS DR requirements

**Alternative Considered:**
- Hot Standby: Rejected due to 2x infrastructure cost ($120,000/year)
- Pilot Light: Rejected due to RTO > 1 hour (too slow)

### Architecture

**Primary Site (us-east-1):**
- **Compute:** 40 EC2 instances (c5.2xlarge) behind Application Load Balancer
- **Database:** RDS PostgreSQL Multi-AZ (db.r5.4xlarge)
- **Cache:** ElastiCache Redis cluster (3 nodes)
- **Storage:** S3 for product images, order documents
- **CDN:** CloudFront for static assets

**DR Site (us-west-2):**
- **Compute:** 20 EC2 instances (c5.2xlarge) - 50% capacity, auto-scaling enabled
- **Database:** RDS PostgreSQL read replica (db.r5.4xlarge)
- **Cache:** ElastiCache Redis cluster (3 nodes) - shared cache layer
- **Storage:** S3 with cross-region replication
- **CDN:** CloudFront (same distribution, multi-region origin)

**Data Replication:**
- **Database:** Asynchronous replication (30-60 second lag)
- **Storage:** S3 cross-region replication (near real-time)
- **Cache:** Shared Redis cluster (no replication needed)

**DNS Failover:**
- **Route 53 Health Checks:** Monitor primary ALB health every 30 seconds
- **Failover Policy:** Automatic failover to DR site if 3 consecutive health check failures
- **TTL:** 60 seconds (fast DNS propagation)

### Failover Procedure

#### Pre-Failover Checklist

```markdown
## Pre-Failover Validation

- [ ] Primary site confirmed unavailable (health checks failing)
- [ ] DR site health confirmed (all instances healthy)
- [ ] Database replication lag < 5 minutes
- [ ] S3 replication confirmed current
- [ ] On-call team notified
- [ ] Stakeholders notified (CTO, CEO, Customer Support)
```

#### Automatic Failover (Triggered by Route 53)

**Timeline:**

```
T+0:00  Primary site failure detected (region outage)
T+0:30  Route 53 health check fails (1st failure)
T+1:00  Route 53 health check fails (2nd failure)
T+1:30  Route 53 health check fails (3rd failure)
T+1:30  Route 53 initiates DNS failover to DR site
T+2:00  DNS propagation begins (TTL 60 seconds)
T+3:00  Traffic starts routing to DR site
T+3:00  CloudWatch alarm triggers auto-scaling in DR site
T+5:00  DR site scales from 20 to 40 instances (100% capacity)
T+7:00  Database read replica promoted to primary
T+10:00 All traffic routed to DR site
T+15:00 Failover complete ✅
```

#### Manual Failover Steps (If Needed)

**Step 1: Validate DR Site Health (T+0)**

```bash
#!/bin/bash
# validate-dr-site.sh

set -e

echo "[T+0] Validating DR site health..."

# Check DR instances
DR_HEALTHY_INSTANCES=$(aws ec2 describe-instance-status \
  --region us-west-2 \
  --filters "Name=instance-state-name,Values=running" \
  --query 'InstanceStatuses[?InstanceStatus.Status==`ok`] | length(@)')

if [ "$DR_HEALTHY_INSTANCES" -lt 20 ]; then
  echo "❌ DR site unhealthy: only $DR_HEALTHY_INSTANCES/20 instances healthy"
  exit 1
fi

echo "✅ DR site healthy: $DR_HEALTHY_INSTANCES instances running"

# Check database replication lag
REPLICATION_LAG=$(aws rds describe-db-instances \
  --db-instance-identifier techmart-dr-replica \
  --region us-west-2 \
  --query 'DBInstances[0].StatusInfos[?StatusType==`read replication`].Status' \
  --output text)

echo "✅ Database replication status: $REPLICATION_LAG"

# Check S3 replication
S3_REPLICATION=$(aws s3api get-bucket-replication \
  --bucket techmart-products-us-east-1 \
  --query 'ReplicationConfiguration.Rules[0].Status' \
  --output text)

echo "✅ S3 replication status: $S3_REPLICATION"

echo "[T+1] DR site validation complete ✅"
```

**Step 2: Promote Database Read Replica (T+2)**

```bash
#!/bin/bash
# promote-database.sh

set -e

echo "[T+2] Promoting database read replica to primary..."

# Promote read replica
aws rds promote-read-replica \
  --db-instance-identifier techmart-dr-replica \
  --region us-west-2

echo "[T+3] Waiting for database promotion to complete..."

# Wait for promotion (typically 3-5 minutes)
aws rds wait db-instance-available \
  --db-instance-identifier techmart-dr-replica \
  --region us-west-2

echo "[T+7] Database promotion complete ✅"

# Update application configuration to use new primary
aws ssm put-parameter \
  --name "/techmart/database/endpoint" \
  --value "techmart-dr-replica.abc123.us-west-2.rds.amazonaws.com" \
  --type "String" \
  --overwrite \
  --region us-west-2

echo "[T+7] Application configuration updated ✅"
```

**Step 3: Scale DR Site to 100% Capacity (T+3)**

```bash
#!/bin/bash
# scale-dr-site.sh

set -e

echo "[T+3] Scaling DR site to 100% capacity..."

# Update Auto Scaling Group desired capacity
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name techmart-dr-asg \
  --desired-capacity 40 \
  --region us-west-2

echo "[T+4] Waiting for instances to launch..."

# Wait for instances to be healthy (typically 2-3 minutes)
sleep 180

# Verify capacity
CURRENT_CAPACITY=$(aws autoscaling describe-auto-scaling-groups \
  --auto-scaling-group-names techmart-dr-asg \
  --region us-west-2 \
  --query 'AutoScalingGroups[0].DesiredCapacity' \
  --output text)

echo "✅ DR site scaled to $CURRENT_CAPACITY instances"

echo "[T+7] Scaling complete ✅"
```

**Step 4: Update DNS to Route Traffic to DR Site (T+8)**

```bash
#!/bin/bash
# update-dns.sh

set -e

echo "[T+8] Updating DNS to route traffic to DR site..."

# Update Route 53 record to point to DR ALB
aws route53 change-resource-record-sets \
  --hosted-zone-id Z1234567890ABC \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "www.techmart.com",
        "Type": "A",
        "SetIdentifier": "DR-Site",
        "Failover": "PRIMARY",
        "AliasTarget": {
          "HostedZoneId": "Z0987654321XYZ",
          "DNSName": "techmart-dr-alb-789012.us-west-2.elb.amazonaws.com",
          "EvaluateTargetHealth": true
        },
        "TTL": 60
      }
    }]
  }'

echo "[T+9] DNS update initiated. Waiting for propagation..."

sleep 120  # Wait for DNS TTL

echo "[T+11] DNS propagation complete ✅"
```

**Step 5: Validate DR Site Functionality (T+12)**

```bash
#!/bin/bash
# validate-dr-functionality.sh

set -e

echo "[T+12] Validating DR site functionality..."

DR_URL="https://www.techmart.com"

# Test 1: Homepage
HOMEPAGE_STATUS=$(curl -s -o /dev/null -w "%{http_code}" "$DR_URL")
if [ "$HOMEPAGE_STATUS" != "200" ]; then
  echo "❌ Homepage test failed: HTTP $HOMEPAGE_STATUS"
  exit 1
fi
echo "✅ Homepage test passed"

# Test 2: Product catalog
PRODUCTS=$(curl -s "$DR_URL/api/products?limit=10" | jq '.products | length')
if [ "$PRODUCTS" -lt 10 ]; then
  echo "❌ Product catalog test failed: only $PRODUCTS products"
  exit 1
fi
echo "✅ Product catalog test passed"

# Test 3: Checkout flow
CHECKOUT=$(curl -s -X POST "$DR_URL/api/checkout" \
  -H "Content-Type: application/json" \
  -d '{"cart_id": "test-cart-123"}' | jq -r '.status')
if [ "$CHECKOUT" != "success" ]; then
  echo "❌ Checkout test failed: $CHECKOUT"
  exit 1
fi
echo "✅ Checkout test passed"

# Test 4: Database connectivity
DB_CHECK=$(curl -s "$DR_URL/api/health/database" | jq -r '.status')
if [ "$DB_CHECK" != "healthy" ]; then
  echo "❌ Database check failed: $DB_CHECK"
  exit 1
fi
echo "✅ Database check passed"

echo "[T+15] DR site validation complete ✅"
echo "Failover successful! All systems operational in DR site."
```

### Monitoring During Failover

**CloudWatch Alarms:**

```yaml
# DR Site Monitoring
Alarms:
  - Name: DR-Site-Error-Rate
    Metric: ErrorRate
    Threshold: 1%
    EvaluationPeriods: 2
    Action: Alert on-call team
    
  - Name: DR-Site-Latency-P95
    Metric: Latency
    Statistic: p95
    Threshold: 500ms
    EvaluationPeriods: 3
    Action: Alert on-call team
    
  - Name: DR-Database-Replication-Lag
    Metric: ReplicaLag
    Threshold: 300 seconds
    EvaluationPeriods: 1
    Action: Alert on-call team
    
  - Name: DR-Site-CPU-Utilization
    Metric: CPUUtilization
    Threshold: 80%
    EvaluationPeriods: 2
    Action: Trigger auto-scaling
```

**Metrics to Monitor:**

| Metric | Baseline (Primary) | Acceptable (DR) | Action if Exceeded |
|--------|-------------------|-----------------|--------------------|
| Error Rate | 0.2% | < 1% | Investigate errors |
| Latency p95 | 250ms | < 500ms | Check instance health |
| Throughput | 50,000 req/min | > 40,000 req/min | Scale up instances |
| Database Connections | 200 | < 400 | Check connection pool |
| Cache Hit Rate | 85% | > 70% | Warm up cache |

### Failback Procedure

**When to Failback:**
- Primary site fully restored and tested
- DR site stable for at least 24 hours
- Planned maintenance window scheduled
- Stakeholder approval obtained

**Failback Timeline:**

```
T+0:00  Primary site restored and validated
T+0:00  Create new read replica in primary site from DR database
T+2:00  Read replica synchronized (replication lag < 1 minute)
T+2:00  Schedule maintenance window (low-traffic period)
T+2:00  Notify stakeholders of planned failback

[Maintenance Window Begins]
T+0:00  Stop writes to DR database (enable read-only mode)
T+0:01  Wait for replication to catch up (< 1 minute)
T+0:02  Promote primary site read replica to primary
T+0:05  Update application configuration to use primary database
T+0:06  Scale primary site to 100% capacity (40 instances)
T+0:10  Update DNS to route traffic to primary site
T+0:12  Validate primary site functionality
T+0:15  Failback complete ✅
T+0:15  Scale down DR site to 50% capacity (20 instances)
T+0:20  Create new read replica in DR site for future DR
[Maintenance Window Ends]
```

**Failback Script:**

```bash
#!/bin/bash
# failback-to-primary.sh

set -e

echo "[FAILBACK] Starting failback to primary site..."

# Step 1: Enable read-only mode on DR database
echo "[T+0] Enabling read-only mode on DR database..."
aws rds modify-db-instance \
  --db-instance-identifier techmart-dr-replica \
  --db-parameter-group-name techmart-readonly \
  --region us-west-2 \
  --apply-immediately

sleep 60  # Wait for parameter group change

# Step 2: Promote primary site read replica
echo "[T+2] Promoting primary site read replica..."
aws rds promote-read-replica \
  --db-instance-identifier techmart-primary-replica \
  --region us-east-1

aws rds wait db-instance-available \
  --db-instance-identifier techmart-primary-replica \
  --region us-east-1

echo "[T+5] Primary database promoted ✅"

# Step 3: Update application configuration
echo "[T+5] Updating application configuration..."
aws ssm put-parameter \
  --name "/techmart/database/endpoint" \
  --value "techmart-primary-replica.abc123.us-east-1.rds.amazonaws.com" \
  --type "String" \
  --overwrite \
  --region us-east-1

# Step 4: Scale primary site to 100% capacity
echo "[T+6] Scaling primary site to 100% capacity..."
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name techmart-primary-asg \
  --desired-capacity 40 \
  --region us-east-1

sleep 240  # Wait for instances to launch

# Step 5: Update DNS to route traffic to primary site
echo "[T+10] Updating DNS to route traffic to primary site..."
aws route53 change-resource-record-sets \
  --hosted-zone-id Z1234567890ABC \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "www.techmart.com",
        "Type": "A",
        "SetIdentifier": "Primary-Site",
        "Failover": "PRIMARY",
        "AliasTarget": {
          "HostedZoneId": "Z1234567890ABC",
          "DNSName": "techmart-primary-alb-123456.us-east-1.elb.amazonaws.com",
          "EvaluateTargetHealth": true
        },
        "TTL": 60
      }
    }]
  }'

sleep 120  # Wait for DNS propagation

# Step 6: Validate primary site
echo "[T+12] Validating primary site functionality..."
./validate-primary-functionality.sh

echo "[T+15] Failback complete ✅"

# Step 7: Scale down DR site
echo "[T+15] Scaling down DR site to 50% capacity..."
aws autoscaling set-desired-capacity \
  --auto-scaling-group-name techmart-dr-asg \
  --desired-capacity 20 \
  --region us-west-2

echo "[T+20] Failback complete. Primary site operational."
```

### DR Testing Plan

**Test Frequency:** Quarterly (every 3 months)

**Test Scenarios:**

**Test 1: Full Failover Test**
- **Objective:** Validate complete failover procedure
- **Duration:** 2 hours
- **Steps:**
  1. Schedule maintenance window (low-traffic period)
  2. Simulate primary site failure (disable Route 53 health checks)
  3. Execute failover procedure
  4. Validate DR site functionality
  5. Measure RTO and RPO achieved
  6. Execute failback procedure
  7. Document results and lessons learned

**Test 2: Database Failover Test**
- **Objective:** Validate database promotion procedure
- **Duration:** 1 hour
- **Steps:**
  1. Promote read replica to primary (in test environment)
  2. Validate data integrity
  3. Measure promotion time
  4. Test application connectivity

**Test 3: Tabletop Exercise**
- **Objective:** Train team on DR procedures
- **Duration:** 1 hour
- **Steps:**
  1. Present simulated disaster scenario
  2. Walk through DR procedures
  3. Identify gaps in documentation
  4. Update runbooks based on findings

**Success Criteria:**
- ✅ RTO < 15 minutes (measured from failure to full functionality)
- ✅ RPO < 5 minutes (measured data loss)
- ✅ Zero critical errors during failover
- ✅ All validation tests pass
- ✅ Team able to execute procedures without assistance

### Results

**Failover Test Results (2026-06-15):**

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| RTO | < 15 min | 13 min 42 sec | ✅ Pass |
| RPO | < 5 min | 2 min 18 sec | ✅ Pass |
| Error Rate | < 1% | 0.3% | ✅ Pass |
| Latency p95 | < 500ms | 320ms | ✅ Pass |
| Data Loss | 0 transactions | 0 transactions | ✅ Pass |
| Failback Time | < 20 min | 17 min 30 sec | ✅ Pass |

**Lessons Learned:**
1. DNS propagation took longer than expected (3 minutes vs. 2 minutes)
2. Auto-scaling took 4 minutes to reach 100% capacity (acceptable)
3. Database promotion was faster than expected (5 minutes vs. 7 minutes)
4. No data loss due to replication lag < 3 minutes
5. Team executed procedures successfully with minimal guidance

**Cost Analysis:**

**DR Infrastructure Cost:**
- DR site (50% capacity): $30,000/year
- Database read replica: $18,000/year
- S3 cross-region replication: $2,400/year
- Route 53 health checks: $600/year
- **Total: $51,000/year**

**ROI Calculation:**
- Prevented downtime: 8 hours/year (estimated without DR)
- Revenue loss prevented: $400,000/year (8 hours × $50,000/hour)
- DR cost: $51,000/year
- **Net benefit: $349,000/year**
- **ROI: 684%**

---

## Example 2: Payment Processing System with Hot Standby DR

### Context

**Company:** FinPay Payment Processor  
**System:** Payment authorization and settlement  
**Traffic:** 100,000 transactions/hour  
**Revenue:** $0.30 per transaction ($30,000/hour)  
**Requirements:**
- **RTO:** 30 seconds (mission-critical)
- **RPO:** Near-zero (cannot lose payment data)
- **Compliance:** PCI DSS Level 1, SOC 2 Type II
- **Budget:** $150,000/year DR infrastructure

**Business Impact:**
- Revenue loss: $30,000/hour
- Regulatory fines: Up to $100,000 for PCI DSS violations
- Customer trust: Critical (payment processor reputation)
- Legal liability: Payment data loss unacceptable

### DR Strategy Selection

**Chosen Strategy:** Hot Standby

**Rationale:**
- **RTO 30 seconds:** Requires full DR site with instant failover
- **RPO near-zero:** Requires synchronous database replication
- **Compliance:** PCI DSS requires robust DR capabilities
- **Cost justified:** $150,000/year vs. $30,000/hour revenue loss + regulatory fines

**Alternative Considered:**
- Warm Standby: Rejected due to RTO > 5 minutes (too slow)
- Multi-Site Active/Active: Considered but rejected due to complexity

### Architecture

**Primary Site (us-east-1):**
- **Compute:** 50 EC2 instances (c5.4xlarge) behind Network Load Balancer
- **Database:** RDS PostgreSQL Multi-AZ with synchronous replication (db.r5.8xlarge)
- **Cache:** ElastiCache Redis cluster (6 nodes)
- **Message Queue:** Amazon MQ (ActiveMQ) for transaction processing
- **Storage:** S3 for transaction logs, audit trails

**DR Site (us-west-2):**
- **Compute:** 50 EC2 instances (c5.4xlarge) - 100% capacity, active
- **Database:** RDS PostgreSQL with synchronous replication from primary
- **Cache:** ElastiCache Redis cluster (6 nodes) - active
- **Message Queue:** Amazon MQ (ActiveMQ) - active
- **Storage:** S3 with cross-region replication

**Data Replication:**
- **Database:** Synchronous replication (< 1 second lag, typically < 100ms)
- **Storage:** S3 cross-region replication (near real-time)
- **Cache:** Active-active Redis cluster (shared across regions)
- **Message Queue:** Active-active Amazon MQ cluster

**Load Balancing:**
- **Global Accelerator:** AWS Global Accelerator for instant failover
- **Health Checks:** Monitor both sites every 10 seconds
- **Failover:** Automatic failover in < 30 seconds

### Failover Procedure

#### Automatic Failover (Triggered by Global Accelerator)

**Timeline:**

```
T+0:00  Primary site failure detected (region outage)
T+0:10  Global Accelerator health check fails (1st failure)
T+0:20  Global Accelerator health check fails (2nd failure)
T+0:20  Global Accelerator initiates failover to DR site
T+0:25  Traffic routed to DR site (instant)
T+0:30  Failover complete ✅
```

**Automatic Failover Configuration:**

```json
{
  "GlobalAccelerator": {
    "Name": "FinPay-Payment-Processor",
    "IpAddressType": "IPV4",
    "Enabled": true,
    "Listeners": [
      {
        "Protocol": "TCP",
        "PortRanges": [{"FromPort": 443, "ToPort": 443}],
        "ClientAffinity": "SOURCE_IP"
      }
    ],
    "EndpointGroups": [
      {
        "EndpointGroupRegion": "us-east-1",
        "TrafficDialPercentage": 100,
        "HealthCheckIntervalSeconds": 10,
        "HealthCheckPath": "/health",
        "HealthCheckProtocol": "HTTPS",
        "ThresholdCount": 2,
        "EndpointConfigurations": [
          {
            "EndpointId": "arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/net/finpay-primary-nlb/...",
            "Weight": 100,
            "ClientIPPreservationEnabled": true
          }
        ]
      },
      {
        "EndpointGroupRegion": "us-west-2",
        "TrafficDialPercentage": 100,
        "HealthCheckIntervalSeconds": 10,
        "HealthCheckPath": "/health",
        "HealthCheckProtocol": "HTTPS",
        "ThresholdCount": 2,
        "EndpointConfigurations": [
          {
            "EndpointId": "arn:aws:elasticloadbalancing:us-west-2:123456789012:loadbalancer/net/finpay-dr-nlb/...",
            "Weight": 100,
            "ClientIPPreservationEnabled": true
          }
        ]
      }
    ]
  }
}
```

#### Database Synchronous Replication

**PostgreSQL Configuration:**

```sql
-- Primary database configuration
ALTER SYSTEM SET synchronous_commit = 'remote_apply';
ALTER SYSTEM SET synchronous_standby_names = 'dr_replica';
ALTER SYSTEM SET wal_level = 'replica';
ALTER SYSTEM SET max_wal_senders = 10;
ALTER SYSTEM SET wal_keep_segments = 64;

-- Verify replication status
SELECT 
  client_addr,
  state,
  sync_state,
  replay_lag
FROM pg_stat_replication;

-- Expected output:
--  client_addr  |   state   | sync_state | replay_lag 
-- --------------+-----------+------------+------------
--  10.0.2.100   | streaming | sync       | 00:00:00.05
```

**Replication Monitoring:**

```bash
#!/bin/bash
# monitor-replication.sh

set -e

while true; do
  # Check replication lag
  REPLICATION_LAG=$(aws rds describe-db-instances \
    --db-instance-identifier finpay-dr-replica \
    --region us-west-2 \
    --query 'DBInstances[0].StatusInfos[?StatusType==`read replication`].Message' \
    --output text | grep -oP 'lag: \K[0-9]+')
  
  if [ "$REPLICATION_LAG" -gt 1000 ]; then
    echo "⚠️  Replication lag: ${REPLICATION_LAG}ms (threshold: 1000ms)"
    # Alert on-call team
    aws sns publish \
      --topic-arn arn:aws:sns:us-east-1:123456789012:finpay-alerts \
      --message "Database replication lag: ${REPLICATION_LAG}ms"
  else
    echo "✅ Replication lag: ${REPLICATION_LAG}ms"
  fi
  
  sleep 10
done
```

### Monitoring During Failover

**Critical Metrics:**

| Metric | Baseline | Alert Threshold | Critical Threshold |
|--------|----------|-----------------|--------------------|
| Transaction Success Rate | 99.99% | < 99.9% | < 99.5% |
| Authorization Latency p95 | 50ms | > 100ms | > 200ms |
| Database Replication Lag | < 50ms | > 500ms | > 1000ms |
| Payment Failures | < 0.01% | > 0.1% | > 0.5% |
| Settlement Errors | 0 | > 0 | > 5 |

**PagerDuty Alert Configuration:**

```yaml
Alerts:
  - Name: Primary-Site-Down
    Severity: P0
    Condition: Health check failures > 2
    Action: Page on-call engineer immediately
    Escalation: CTO after 5 minutes
    
  - Name: DR-Site-Failover
    Severity: P0
    Condition: Traffic routed to DR site
    Action: Page on-call engineer + CTO immediately
    Notification: All stakeholders (CEO, CFO, COO)
    
  - Name: Payment-Authorization-Failures
    Severity: P0
    Condition: Authorization failures > 0.5%
    Action: Page on-call engineer immediately
    Escalation: CTO after 2 minutes
    
  - Name: Database-Replication-Lag
    Severity: P1
    Condition: Replication lag > 1000ms
    Action: Alert on-call engineer
    Escalation: Page after 5 minutes
```

### Failback Procedure

**Failback Timeline:**

```
T+0:00  Primary site restored and validated
T+0:00  Verify database synchronization
T+0:05  Schedule maintenance window (coordinated with payment networks)
T+0:05  Notify stakeholders (payment networks, customers, regulators)

[Maintenance Window - 2 AM EST]
T+0:00  Verify primary site health (all systems operational)
T+0:02  Update Global Accelerator to route traffic to primary site
T+0:05  Validate payment processing on primary site
T+0:10  Failback complete ✅
T+0:10  Monitor for 1 hour before declaring success
[Maintenance Window Ends]
```

**Failback Validation:**

```bash
#!/bin/bash
# validate-failback.sh

set -e

echo "Validating failback to primary site..."

# Test payment authorization
for i in {1..100}; do
  RESPONSE=$(curl -s -X POST "https://api.finpay.com/v1/authorize" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
      "amount": 1000,
      "currency": "USD",
      "card_number": "4111111111111111",
      "cvv": "123",
      "exp_month": "12",
      "exp_year": "2025"
    }')
  
  STATUS=$(echo $RESPONSE | jq -r '.status')
  if [ "$STATUS" != "approved" ]; then
    echo "❌ Payment authorization failed: $STATUS"
    exit 1
  fi
done

echo "✅ 100 payment authorizations successful"

# Verify database connectivity
DB_STATUS=$(curl -s "https://api.finpay.com/health/database" | jq -r '.status')
if [ "$DB_STATUS" != "healthy" ]; then
  echo "❌ Database health check failed: $DB_STATUS"
  exit 1
fi

echo "✅ Database health check passed"

# Verify settlement processing
SETTLEMENT_STATUS=$(curl -s "https://api.finpay.com/health/settlement" | jq -r '.status')
if [ "$SETTLEMENT_STATUS" != "healthy" ]; then
  echo "❌ Settlement health check failed: $SETTLEMENT_STATUS"
  exit 1
fi

echo "✅ Settlement health check passed"

echo "Failback validation complete ✅"
```

### DR Testing Plan

**Test Frequency:** Monthly (required by PCI DSS)

**Test Scenarios:**

**Test 1: Full Failover Test (Monthly)**
- **Objective:** Validate complete failover and failback
- **Duration:** 30 minutes
- **Steps:**
  1. Schedule maintenance window (2 AM EST, low-traffic period)
  2. Simulate primary site failure (disable Global Accelerator endpoint)
  3. Measure failover time (target: < 30 seconds)
  4. Validate payment processing on DR site
  5. Process 1,000 test transactions
  6. Measure RPO (data loss)
  7. Execute failback procedure
  8. Validate payment processing on primary site
  9. Document results for PCI DSS compliance

**Test 2: Database Replication Test (Weekly)**
- **Objective:** Validate synchronous replication
- **Duration:** 15 minutes
- **Steps:**
  1. Monitor replication lag during peak traffic
  2. Verify replication lag < 100ms
  3. Test database failover in test environment
  4. Validate data integrity

**Test 3: Disaster Simulation (Quarterly)**
- **Objective:** Test team response to real disaster
- **Duration:** 2 hours
- **Steps:**
  1. Unannounced disaster simulation (on-call team only)
  2. Measure team response time
  3. Validate DR procedures execution
  4. Document lessons learned
  5. Update runbooks

**Success Criteria:**
- ✅ RTO < 30 seconds
- ✅ RPO < 1 second (near-zero data loss)
- ✅ Payment authorization success rate > 99.99%
- ✅ Zero settlement errors
- ✅ PCI DSS compliance maintained

### Results

**Failover Test Results (2026-08-01):**

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| RTO | < 30 sec | 24 sec | ✅ Pass |
| RPO | < 1 sec | 0 sec (zero data loss) | ✅ Pass |
| Authorization Success Rate | > 99.99% | 99.998% | ✅ Pass |
| Settlement Errors | 0 | 0 | ✅ Pass |
| Replication Lag | < 100ms | 45ms | ✅ Pass |
| Failback Time | < 10 min | 8 min 15 sec | ✅ Pass |

**PCI DSS Compliance:**
- ✅ DR testing performed monthly
- ✅ RTO/RPO requirements met
- ✅ Zero payment data loss
- ✅ Audit trail maintained
- ✅ Compliance documentation complete

**Cost Analysis:**

**DR Infrastructure Cost:**
- DR site (100% capacity): $90,000/year
- Database synchronous replication: $36,000/year
- Global Accelerator: $6,000/year
- S3 cross-region replication: $3,600/year
- Message queue replication: $7,200/year
- **Total: $142,800/year**

**ROI Calculation:**
- Prevented downtime: 4 hours/year (estimated without DR)
- Revenue loss prevented: $120,000/year (4 hours × $30,000/hour)
- Regulatory fines prevented: $100,000/year (estimated)
- DR cost: $142,800/year
- **Net benefit: $77,200/year**
- **ROI: 54%** (plus intangible benefits: customer trust, regulatory compliance)

---

## Example 3: SaaS Application with Pilot Light DR

### Context

**Company:** CloudDocs SaaS  
**System:** Document collaboration platform  
**Users:** 50,000 active users  
**Revenue:** $100/user/year ($5M annual revenue)  
**Requirements:**
- **RTO:** 2 hours (acceptable downtime for SaaS)
- **RPO:** 30 minutes (acceptable data loss)
- **Compliance:** SOC 2 Type II
- **Budget:** $30,000/year DR infrastructure (cost-optimized)

**Business Impact:**
- Revenue loss: $570/hour (estimated)
- Customer churn: 5% if downtime > 4 hours
- Regulatory: SOC 2 requires DR capabilities
- Brand reputation: Moderate impact

### DR Strategy Selection

**Chosen Strategy:** Pilot Light

**Rationale:**
- **RTO 2 hours:** Achievable with minimal DR infrastructure + provisioning time
- **RPO 30 minutes:** Achievable with hourly snapshots
- **Cost-optimized:** Minimal DR infrastructure ($30,000/year budget)
- **Compliance:** Meets SOC 2 DR requirements

**Alternative Considered:**
- Warm Standby: Rejected due to budget constraints ($60,000/year)
- Backup/Restore: Rejected due to RTO > 8 hours (too slow)

### Architecture

**Primary Site (us-east-1):**
- **Compute:** 20 EC2 instances (c5.xlarge) behind Application Load Balancer
- **Database:** RDS PostgreSQL Multi-AZ (db.r5.2xlarge)
- **Storage:** S3 for documents, user files
- **Search:** Elasticsearch cluster (3 nodes)
- **Cache:** ElastiCache Redis (2 nodes)

**DR Site (us-west-2) - Pilot Light:**
- **Compute:** 0 instances (provisioned on failover)
- **Database:** RDS PostgreSQL read replica (db.r5.2xlarge) - ONLY running component
- **Storage:** S3 with cross-region replication
- **Search:** 0 nodes (provisioned on failover)
- **Cache:** 0 nodes (provisioned on failover)

**Data Replication:**
- **Database:** Asynchronous replication via read replica (5-10 minute lag)
- **Storage:** S3 cross-region replication (near real-time)
- **Search:** Snapshot-based (hourly snapshots)
- **Cache:** Not replicated (rebuilt on failover)

**Cost Optimization:**
- Only database read replica running in DR site
- All other infrastructure provisioned on-demand during failover
- Use Terraform/CloudFormation for rapid provisioning

### Failover Procedure

**Failover Timeline:**

```
T+0:00  Primary site failure detected
T+0:05  DR team assembled, failover decision made
T+0:10  Provision DR infrastructure (EC2, Elasticsearch, Redis)
T+0:40  Infrastructure provisioned and healthy
T+0:45  Promote database read replica to primary
T+0:50  Deploy application code to DR instances
T+0:55  Update DNS to point to DR site
T+1:00  Warm up cache, rebuild search index
T+1:30  Validate application functionality
T+2:00  Failover complete ✅
```

**Step 1: Provision DR Infrastructure (T+10)**

```bash
#!/bin/bash
# provision-dr-infrastructure.sh

set -e

echo "[T+10] Provisioning DR infrastructure..."

# Provision infrastructure using Terraform
cd /opt/clouddocs/terraform/dr-site

terraform init
terraform plan -out=dr-plan
terraform apply dr-plan

echo "[T+40] DR infrastructure provisioned ✅"

# Wait for instances to be healthy
aws ec2 wait instance-status-ok \
  --instance-ids $(terraform output -json instance_ids | jq -r '.[]') \
  --region us-west-2

echo "[T+40] All instances healthy ✅"
```

**Terraform Configuration:**

```hcl
# dr-site/main.tf

provider "aws" {
  region = "us-west-2"
}

# Auto Scaling Group for application servers
resource "aws_autoscaling_group" "clouddocs_dr" {
  name                 = "clouddocs-dr-asg"
  vpc_zone_identifier  = [aws_subnet.dr_subnet_a.id, aws_subnet.dr_subnet_b.id]
  min_size             = 20
  max_size             = 40
  desired_capacity     = 20
  health_check_type    = "ELB"
  health_check_grace_period = 300
  
  launch_template {
    id      = aws_launch_template.clouddocs_dr.id
    version = "$Latest"
  }
  
  tag {
    key                 = "Name"
    value               = "clouddocs-dr-instance"
    propagate_at_launch = true
  }
}

# Elasticsearch cluster
resource "aws_elasticsearch_domain" "clouddocs_dr" {
  domain_name           = "clouddocs-dr-search"
  elasticsearch_version = "7.10"
  
  cluster_config {
    instance_type  = "r5.large.elasticsearch"
    instance_count = 3
    zone_awareness_enabled = true
  }
  
  ebs_options {
    ebs_enabled = true
    volume_size = 100
  }
}

# ElastiCache Redis cluster
resource "aws_elasticache_cluster" "clouddocs_dr" {
  cluster_id           = "clouddocs-dr-cache"
  engine               = "redis"
  node_type            = "cache.r5.large"
  num_cache_nodes      = 2
  parameter_group_name = "default.redis6.x"
  port                 = 6379
}
```

**Step 2: Promote Database Read Replica (T+45)**

```bash
#!/bin/bash
# promote-database.sh

set -e

echo "[T+45] Promoting database read replica..."

aws rds promote-read-replica \
  --db-instance-identifier clouddocs-dr-replica \
  --region us-west-2

echo "[T+46] Waiting for database promotion..."

aws rds wait db-instance-available \
  --db-instance-identifier clouddocs-dr-replica \
  --region us-west-2

echo "[T+50] Database promotion complete ✅"
```

**Step 3: Deploy Application Code (T+50)**

```bash
#!/bin/bash
# deploy-application.sh

set -e

echo "[T+50] Deploying application code to DR instances..."

# Update application configuration
aws ssm put-parameter \
  --name "/clouddocs/database/endpoint" \
  --value "clouddocs-dr-replica.abc123.us-west-2.rds.amazonaws.com" \
  --type "String" \
  --overwrite \
  --region us-west-2

aws ssm put-parameter \
  --name "/clouddocs/elasticsearch/endpoint" \
  --value "clouddocs-dr-search-abc123.us-west-2.es.amazonaws.com" \
  --type "String" \
  --overwrite \
  --region us-west-2

aws ssm put-parameter \
  --name "/clouddocs/redis/endpoint" \
  --value "clouddocs-dr-cache.abc123.0001.usw2.cache.amazonaws.com" \
  --type "String" \
  --overwrite \
  --region us-west-2

# Trigger application deployment via CodeDeploy
aws deploy create-deployment \
  --application-name clouddocs-app \
  --deployment-group-name clouddocs-dr-deployment-group \
  --s3-location bucket=clouddocs-deployments,key=clouddocs-v2.5.0.zip,bundleType=zip \
  --region us-west-2

echo "[T+55] Application deployment complete ✅"
```

**Step 4: Update DNS (T+55)**

```bash
#!/bin/bash
# update-dns.sh

set -e

echo "[T+55] Updating DNS to point to DR site..."

aws route53 change-resource-record-sets \
  --hosted-zone-id Z1234567890ABC \
  --change-batch '{
    "Changes": [{
      "Action": "UPSERT",
      "ResourceRecordSet": {
        "Name": "app.clouddocs.com",
        "Type": "A",
        "AliasTarget": {
          "HostedZoneId": "Z0987654321XYZ",
          "DNSName": "clouddocs-dr-alb-789012.us-west-2.elb.amazonaws.com",
          "EvaluateTargetHealth": true
        },
        "TTL": 60
      }
    }]
  }'

echo "[T+57] DNS update complete. Waiting for propagation..."
sleep 180

echo "[T+60] DNS propagation complete ✅"
```

**Step 5: Rebuild Search Index and Warm Cache (T+60)**

```bash
#!/bin/bash
# rebuild-search-cache.sh

set -e

echo "[T+60] Rebuilding search index and warming cache..."

# Restore Elasticsearch snapshot
aws es restore-elasticsearch-snapshot \
  --domain-name clouddocs-dr-search \
  --snapshot-name clouddocs-snapshot-2026-09-09 \
  --region us-west-2

echo "[T+70] Search index restored ✅"

# Warm cache by pre-loading frequently accessed data
curl -X POST "https://app.clouddocs.com/admin/cache/warm" \
  -H "Authorization: Bearer $ADMIN_TOKEN"

echo "[T+80] Cache warmed ✅"
```

**Step 6: Validate DR Site (T+90)**

```bash
#!/bin/bash
# validate-dr-site.sh

set -e

echo "[T+90] Validating DR site functionality..."

DR_URL="https://app.clouddocs.com"

# Test 1: User login
LOGIN=$(curl -s -X POST "$DR_URL/api/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "test@clouddocs.com", "password": "test123"}' | jq -r '.token')

if [ -z "$LOGIN" ]; then
  echo "❌ Login test failed"
  exit 1
fi
echo "✅ Login test passed"

# Test 2: Document retrieval
DOCUMENTS=$(curl -s "$DR_URL/api/documents" \
  -H "Authorization: Bearer $LOGIN" | jq '.documents | length')

if [ "$DOCUMENTS" -lt 1 ]; then
  echo "❌ Document retrieval test failed"
  exit 1
fi
echo "✅ Document retrieval test passed"

# Test 3: Document search
SEARCH=$(curl -s "$DR_URL/api/search?q=test" \
  -H "Authorization: Bearer $LOGIN" | jq '.results | length')

if [ "$SEARCH" -lt 1 ]; then
  echo "❌ Search test failed"
  exit 1
fi
echo "✅ Search test passed"

# Test 4: Document collaboration
COLLAB=$(curl -s -X POST "$DR_URL/api/documents/123/collaborate" \
  -H "Authorization: Bearer $LOGIN" \
  -H "Content-Type: application/json" \
  -d '{"user_id": "456"}' | jq -r '.status')

if [ "$COLLAB" != "success" ]; then
  echo "❌ Collaboration test failed"
  exit 1
fi
echo "✅ Collaboration test passed"

echo "[T+120] DR site validation complete ✅"
echo "Failover successful! All systems operational in DR site."
```

### Failback Procedure

**Failback Timeline:**

```
T+0:00  Primary site restored and validated
T+0:00  Create new read replica in primary site from DR database
T+4:00  Read replica synchronized
T+4:00  Schedule maintenance window
T+4:00  Notify users of planned maintenance (30-minute window)

[Maintenance Window]
T+0:00  Enable read-only mode on DR database
T+0:05  Promote primary site read replica to primary
T+0:10  Deploy application code to primary site
T+0:15  Update DNS to route traffic to primary site
T+0:20  Validate primary site functionality
T+0:30  Failback complete ✅
T+0:30  Deprovision DR infrastructure (cost savings)
[Maintenance Window Ends]
```

### DR Testing Plan

**Test Frequency:** Semi-annually (every 6 months)

**Test Scenarios:**

**Test 1: Full Failover Test**
- **Objective:** Validate complete failover procedure
- **Duration:** 3 hours
- **Steps:**
  1. Schedule maintenance window (weekend, low-traffic period)
  2. Simulate primary site failure
  3. Execute failover procedure
  4. Measure RTO and RPO
  5. Validate application functionality
  6. Execute failback procedure
  7. Document results for SOC 2 compliance

**Test 2: Infrastructure Provisioning Test**
- **Objective:** Validate Terraform provisioning speed
- **Duration:** 1 hour
- **Steps:**
  1. Provision DR infrastructure in test environment
  2. Measure provisioning time
  3. Validate infrastructure health
  4. Deprovision infrastructure

**Success Criteria:**
- ✅ RTO < 2 hours
- ✅ RPO < 30 minutes
- ✅ All application features functional
- ✅ SOC 2 compliance maintained

### Results

**Failover Test Results (2026-07-15):**

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| RTO | < 2 hours | 1 hour 52 min | ✅ Pass |
| RPO | < 30 min | 18 min | ✅ Pass |
| Infrastructure Provisioning | < 40 min | 35 min | ✅ Pass |
| Database Promotion | < 10 min | 6 min | ✅ Pass |
| Application Deployment | < 10 min | 8 min | ✅ Pass |
| Failback Time | < 30 min | 28 min | ✅ Pass |

**Cost Analysis:**

**DR Infrastructure Cost:**
- Database read replica (running 24/7): $18,000/year
- S3 cross-region replication: $2,400/year
- Elasticsearch snapshots: $1,200/year
- DR infrastructure (provisioned on failover): $0/year (on-demand)
- **Total: $21,600/year**

**ROI Calculation:**
- Prevented downtime: 12 hours/year (estimated without DR)
- Revenue loss prevented: $6,840/year (12 hours × $570/hour)
- Customer churn prevented: $250,000/year (5% of $5M annual revenue)
- DR cost: $21,600/year
- **Net benefit: $235,240/year**
- **ROI: 1,089%**

---

## Example 4: Internal Tools with Backup/Restore DR

### Context

**Company:** TechCorp Engineering  
**System:** Internal developer tools (CI/CD, code review, project management)  
**Users:** 500 internal developers  
**Requirements:**
- **RTO:** 24 hours (acceptable downtime for internal tools)
- **RPO:** 24 hours (acceptable data loss)
- **Compliance:** None (internal tools)
- **Budget:** $10,000/year DR infrastructure (budget-conscious)

**Business Impact:**
- Productivity loss: 500 developers × $100/hour × 8 hours = $400,000/day
- Revenue impact: Indirect (delayed feature releases)
- Customer impact: None (internal tools)
- Regulatory: None

### DR Strategy Selection

**Chosen Strategy:** Backup/Restore

**Rationale:**
- **RTO 24 hours:** Achievable with manual infrastructure provisioning + restore
- **RPO 24 hours:** Achievable with daily backups
- **Budget-conscious:** Minimal DR cost (backup storage only)
- **Internal tools:** Downtime acceptable for non-customer-facing systems

**Alternative Considered:**
- Pilot Light: Rejected due to budget constraints
- Warm Standby: Rejected due to unnecessary for internal tools

### Architecture

**Primary Site (us-east-1):**
- **Compute:** 10 EC2 instances (t3.large)
- **Database:** RDS PostgreSQL (db.t3.large)
- **Storage:** S3 for artifacts, build logs
- **Git:** GitLab self-hosted (3 nodes)

**DR Site (us-west-2):**
- **Compute:** 0 instances (provisioned on restore)
- **Database:** 0 instances (restored from backup)
- **Storage:** S3 with cross-region replication
- **Git:** 0 nodes (restored from backup)

**Backup Strategy:**
- **Database:** Daily automated snapshots (retained for 30 days)
- **Storage:** S3 cross-region replication (continuous)
- **Git:** Daily GitLab backup to S3 (retained for 30 days)
- **Configurations:** Daily backup of infrastructure configurations to S3

**Cost Optimization:**
- No running DR infrastructure
- Backup storage only ($500/month)
- Use S3 Glacier for long-term retention (90 days+)

### Backup Procedure

**Daily Backup Script:**

```bash
#!/bin/bash
# daily-backup.sh
# Runs daily at 2 AM EST via cron

set -e

BACKUP_DATE=$(date +%Y-%m-%d)

echo "[BACKUP] Starting daily backup for $BACKUP_DATE..."

# Backup 1: RDS Database Snapshot
echo "[BACKUP] Creating RDS snapshot..."
aws rds create-db-snapshot \
  --db-instance-identifier techcorp-tools-db \
  --db-snapshot-identifier techcorp-tools-db-$BACKUP_DATE \
  --region us-east-1

echo "✅ RDS snapshot created: techcorp-tools-db-$BACKUP_DATE"

# Backup 2: GitLab Backup
echo "[BACKUP] Creating GitLab backup..."
ssh gitlab-server "sudo gitlab-backup create BACKUP=$BACKUP_DATE"

# Upload GitLab backup to S3
scp gitlab-server:/var/opt/gitlab/backups/${BACKUP_DATE}_gitlab_backup.tar \
  /tmp/${BACKUP_DATE}_gitlab_backup.tar

aws s3 cp /tmp/${BACKUP_DATE}_gitlab_backup.tar \
  s3://techcorp-backups/gitlab/${BACKUP_DATE}_gitlab_backup.tar \
  --region us-east-1

echo "✅ GitLab backup uploaded to S3"

# Backup 3: Infrastructure Configurations
echo "[BACKUP] Backing up infrastructure configurations..."
tar -czf /tmp/${BACKUP_DATE}_configs.tar.gz \
  /opt/techcorp/terraform \
  /opt/techcorp/ansible \
  /opt/techcorp/configs

aws s3 cp /tmp/${BACKUP_DATE}_configs.tar.gz \
  s3://techcorp-backups/configs/${BACKUP_DATE}_configs.tar.gz \
  --region us-east-1

echo "✅ Infrastructure configurations backed up"

# Backup 4: Copy to DR region (us-west-2)
echo "[BACKUP] Copying backups to DR region..."
aws rds copy-db-snapshot \
  --source-db-snapshot-identifier techcorp-tools-db-$BACKUP_DATE \
  --target-db-snapshot-identifier techcorp-tools-db-$BACKUP_DATE-dr \
  --source-region us-east-1 \
  --region us-west-2

echo "✅ RDS snapshot copied to DR region"

# S3 cross-region replication handles GitLab and config backups automatically

echo "[BACKUP] Daily backup complete for $BACKUP_DATE ✅"

# Cleanup old backups (retain 30 days)
echo "[BACKUP] Cleaning up old backups..."
OLD_DATE=$(date -d "30 days ago" +%Y-%m-%d)

aws rds delete-db-snapshot \
  --db-snapshot-identifier techcorp-tools-db-$OLD_DATE \
  --region us-east-1 || true

aws rds delete-db-snapshot \
  --db-snapshot-identifier techcorp-tools-db-$OLD_DATE-dr \
  --region us-west-2 || true

aws s3 rm s3://techcorp-backups/gitlab/${OLD_DATE}_gitlab_backup.tar || true
aws s3 rm s3://techcorp-backups/configs/${OLD_DATE}_configs.tar.gz || true

echo "[BACKUP] Old backups cleaned up ✅"
```

### Recovery Procedure

**Recovery Timeline:**

```
T+0:00  Primary site failure detected
T+0:30  DR team assembled, recovery decision made
T+1:00  Provision DR infrastructure (EC2, RDS)
T+4:00  Infrastructure provisioned and healthy
T+5:00  Restore RDS database from latest snapshot
T+8:00  Database restore complete
T+9:00  Restore GitLab from backup
T+12:00 GitLab restore complete
T+13:00 Deploy application code
T+14:00 Update DNS to point to DR site
T+15:00 Validate functionality
T+16:00 Recovery complete ✅
```

**Step 1: Provision DR Infrastructure (T+1:00)**

```bash
#!/bin/bash
# provision-dr-infrastructure.sh

set -e

echo "[T+1:00] Provisioning DR infrastructure..."

# Provision infrastructure using Terraform
cd /opt/techcorp/terraform/dr-site

terraform init
terraform plan -out=dr-plan
terraform apply dr-plan

echo "[T+4:00] DR infrastructure provisioned ✅"
```

**Step 2: Restore RDS Database (T+5:00)**

```bash
#!/bin/bash
# restore-database.sh

set -e

echo "[T+5:00] Restoring RDS database from latest snapshot..."

# Find latest snapshot
LATEST_SNAPSHOT=$(aws rds describe-db-snapshots \
  --db-instance-identifier techcorp-tools-db \
  --region us-west-2 \
  --query 'DBSnapshots | sort_by(@, &SnapshotCreateTime) | [-1].DBSnapshotIdentifier' \
  --output text)

echo "Latest snapshot: $LATEST_SNAPSHOT"

# Restore database from snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier techcorp-tools-db-dr \
  --db-snapshot-identifier $LATEST_SNAPSHOT \
  --db-instance-class db.t3.large \
  --region us-west-2

echo "[T+6:00] Waiting for database restore..."

aws rds wait db-instance-available \
  --db-instance-identifier techcorp-tools-db-dr \
  --region us-west-2

echo "[T+8:00] Database restore complete ✅"
```

**Step 3: Restore GitLab (T+9:00)**

```bash
#!/bin/bash
# restore-gitlab.sh

set -e

echo "[T+9:00] Restoring GitLab from backup..."

# Find latest GitLab backup
LATEST_GITLAB_BACKUP=$(aws s3 ls s3://techcorp-backups-dr/gitlab/ \
  --region us-west-2 | sort | tail -n 1 | awk '{print $4}')

echo "Latest GitLab backup: $LATEST_GITLAB_BACKUP"

# Download GitLab backup
aws s3 cp s3://techcorp-backups-dr/gitlab/$LATEST_GITLAB_BACKUP \
  /tmp/$LATEST_GITLAB_BACKUP \
  --region us-west-2

# Restore GitLab
scp /tmp/$LATEST_GITLAB_BACKUP gitlab-dr-server:/var/opt/gitlab/backups/
ssh gitlab-dr-server "sudo gitlab-backup restore BACKUP=${LATEST_GITLAB_BACKUP%.tar}"

echo "[T+12:00] GitLab restore complete ✅"
```

**Step 4: Update DNS (T+14:00)**

```bash
#!/bin/bash
# update-dns.sh

set -e

echo "[T+14:00] Updating DNS to point to DR site..."

aws route53 change-resource-record-sets \
  --hosted-zone-id Z1234567890ABC \
  --change-batch '{
    "Changes": [
      {
        "Action": "UPSERT",
        "ResourceRecordSet": {
          "Name": "tools.techcorp.com",
          "Type": "A",
          "TTL": 300,
          "ResourceRecords": [{"Value": "'$(terraform output -raw dr_alb_ip)'"}]
        }
      },
      {
        "Action": "UPSERT",
        "ResourceRecordSet": {
          "Name": "gitlab.techcorp.com",
          "Type": "A",
          "TTL": 300,
          "ResourceRecords": [{"Value": "'$(terraform output -raw gitlab_dr_ip)'"}]
        }
      }
    ]
  }'

echo "[T+14:30] DNS update complete ✅"
```

**Step 5: Validate DR Site (T+15:00)**

```bash
#!/bin/bash
# validate-dr-site.sh

set -e

echo "[T+15:00] Validating DR site functionality..."

# Test 1: CI/CD pipeline
CI_STATUS=$(curl -s "https://tools.techcorp.com/health" | jq -r '.status')
if [ "$CI_STATUS" != "healthy" ]; then
  echo "❌ CI/CD health check failed"
  exit 1
fi
echo "✅ CI/CD health check passed"

# Test 2: GitLab access
GITLAB_STATUS=$(curl -s "https://gitlab.techcorp.com/-/health" | jq -r '.status')
if [ "$GITLAB_STATUS" != "ok" ]; then
  echo "❌ GitLab health check failed"
  exit 1
fi
echo "✅ GitLab health check passed"

# Test 3: Database connectivity
DB_STATUS=$(curl -s "https://tools.techcorp.com/health/database" | jq -r '.status')
if [ "$DB_STATUS" != "healthy" ]; then
  echo "❌ Database health check failed"
  exit 1
fi
echo "✅ Database health check passed"

echo "[T+16:00] DR site validation complete ✅"
echo "Recovery successful! All systems operational in DR site."
```

### DR Testing Plan

**Test Frequency:** Annually

**Test Scenarios:**

**Test 1: Backup Validation Test (Monthly)**
- **Objective:** Validate backups are restorable
- **Duration:** 2 hours
- **Steps:**
  1. Restore latest database snapshot in test environment
  2. Validate data integrity
  3. Restore latest GitLab backup in test environment
  4. Validate Git repositories accessible
  5. Document results

**Test 2: Full Recovery Test (Annually)**
- **Objective:** Validate complete recovery procedure
- **Duration:** 8 hours
- **Steps:**
  1. Schedule maintenance window (weekend)
  2. Simulate primary site failure
  3. Execute recovery procedure
  4. Measure RTO and RPO
  5. Validate all tools functional
  6. Document results

**Success Criteria:**
- ✅ RTO < 24 hours
- ✅ RPO < 24 hours
- ✅ All backups restorable
- ✅ All tools functional after recovery

### Results

**Recovery Test Results (2026-05-20):**

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| RTO | < 24 hours | 16 hours | ✅ Pass |
| RPO | < 24 hours | 18 hours | ✅ Pass |
| Infrastructure Provisioning | < 4 hours | 3 hours 15 min | ✅ Pass |
| Database Restore | < 4 hours | 3 hours 30 min | ✅ Pass |
| GitLab Restore | < 4 hours | 3 hours 45 min | ✅ Pass |
| Data Loss | < 24 hours | 18 hours | ✅ Pass |

**Cost Analysis:**

**DR Infrastructure Cost:**
- RDS snapshots (30 days retention): $1,200/year
- S3 cross-region replication: $1,800/year
- S3 Glacier (long-term retention): $600/year
- DR infrastructure (provisioned on recovery): $0/year
- **Total: $3,600/year**

**ROI Calculation:**
- Prevented downtime: 24 hours/year (estimated without DR)
- Productivity loss prevented: $400,000/year (500 developers × $100/hour × 8 hours)
- DR cost: $3,600/year
- **Net benefit: $396,400/year**
- **ROI: 11,011%**

---

## Summary

These four examples demonstrate how to implement disaster recovery strategies based on different RTO/RPO requirements and budget constraints:

1. **E-commerce Platform (Warm Standby):** RTO 15 min, RPO 5 min, $51,000/year, 684% ROI
2. **Payment Processing (Hot Standby):** RTO 30 sec, RPO near-zero, $142,800/year, 54% ROI
3. **SaaS Application (Pilot Light):** RTO 2 hours, RPO 30 min, $21,600/year, 1,089% ROI
4. **Internal Tools (Backup/Restore):** RTO 24 hours, RPO 24 hours, $3,600/year, 11,011% ROI

**Key Takeaways:**
- Match DR strategy to business criticality and budget
- Test DR procedures regularly (monthly to annually)
- Automate failover and recovery procedures
- Document procedures for team execution
- Measure RTO/RPO in tests to validate requirements
- Calculate ROI to justify DR investment

**Next Steps:**
- Adapt these examples to your specific context
- Implement automated backup and recovery procedures
- Establish DR testing schedule
- Train team on DR procedures
- Conduct regular DR drills