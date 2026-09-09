# Deployment Strategy - Examples

This document provides comprehensive real-world examples of different deployment strategies (blue/green, canary, rolling, feature flags) with complete implementation details.

---

## Example 1: Blue/Green Deployment for E-commerce Platform

### Context

**Company:** TechMart E-commerce  
**System:** Product catalog and checkout service  
**Traffic:** 10,000 requests/minute peak  
**Requirements:**
- Zero downtime (24/7 operations)
- Instant rollback capability (< 1 minute)
- Revenue loss: $10,000/minute during outage
- Deployment frequency: Weekly releases

**Current Pain Points:**
- Rolling deployments cause brief service disruptions
- Rollback takes 10-15 minutes (too slow)
- Customer complaints during deployments

### Strategy Selection

**Chosen Strategy:** Blue/Green Deployment

**Rationale:**
- **Zero Downtime:** Instant traffic switch from blue to green
- **Fast Rollback:** < 1 minute (switch back to blue)
- **Low Risk:** Validate green environment before switching traffic
- **Resources:** Can afford 2x infrastructure ($15,000/month vs. $10,000/minute revenue loss)

**Alternative Considered:** Canary deployment (rejected due to slower rollback time)

### Architecture

**Infrastructure:**
- **Platform:** AWS (ECS Fargate)
- **Load Balancer:** Application Load Balancer (ALB)
- **Database:** PostgreSQL RDS with read replicas
- **Cache:** Redis ElastiCache
- **CDN:** CloudFront

**Blue Environment:**
- 20 ECS tasks (current production)
- ALB target group: `techmart-blue`
- Database: Primary RDS instance
- Cache: Redis cluster (shared with green)

**Green Environment:**
- 20 ECS tasks (new deployment)
- ALB target group: `techmart-green`
- Database: Same RDS instance (shared)
- Cache: Same Redis cluster (shared)

### Traffic Management

**Load Balancer Configuration:**

```yaml
# ALB Listener Rule (Initial State)
ListenerArn: arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/techmart-alb/...
DefaultActions:
  - Type: forward
    ForwardConfig:
      TargetGroups:
        - TargetGroupArn: arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-blue/...
          Weight: 100
        - TargetGroupArn: arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-green/...
          Weight: 0
```

**Traffic Shifting Plan:**

```
T+0:  Blue 100%, Green 0%   (Deploy to green, run smoke tests)
T+5:  Blue 0%,   Green 100% (Instant switch after validation)
```

### Deployment Procedure

#### Step 1: Deploy to Green Environment (T+0)

```bash
#!/bin/bash
# deploy-green.sh

set -e

echo "[T+0] Deploying to green environment..."

# Update ECS service with new task definition
aws ecs update-service \
  --cluster techmart-prod \
  --service techmart-green \
  --task-definition techmart-app:v2.5.0 \
  --force-new-deployment

# Wait for green deployment to stabilize
aws ecs wait services-stable \
  --cluster techmart-prod \
  --services techmart-green

echo "[T+2] Green deployment complete"
```

#### Step 2: Run Smoke Tests on Green (T+2)

```bash
#!/bin/bash
# smoke-test-green.sh

set -e

GREEN_URL="http://internal-green.techmart.com"

echo "[T+2] Running smoke tests on green environment..."

# Test 1: Health check
HEALTH=$(curl -s "$GREEN_URL/health" | jq -r '.status')
if [ "$HEALTH" != "healthy" ]; then
  echo "❌ Health check failed: $HEALTH"
  exit 1
fi
echo "✅ Health check passed"

# Test 2: Product catalog
PRODUCTS=$(curl -s "$GREEN_URL/api/products?limit=10" | jq '.products | length')
if [ "$PRODUCTS" -lt 10 ]; then
  echo "❌ Product catalog test failed: only $PRODUCTS products returned"
  exit 1
fi
echo "✅ Product catalog test passed"

# Test 3: Checkout flow
CHECKOUT=$(curl -s -X POST "$GREEN_URL/api/checkout" \
  -H "Content-Type: application/json" \
  -d '{"cart_id": "test-cart-123"}' | jq -r '.status')
if [ "$CHECKOUT" != "success" ]; then
  echo "❌ Checkout test failed: $CHECKOUT"
  exit 1
fi
echo "✅ Checkout test passed"

# Test 4: Database connectivity
DB_CHECK=$(curl -s "$GREEN_URL/api/db-check" | jq -r '.database')
if [ "$DB_CHECK" != "connected" ]; then
  echo "❌ Database check failed: $DB_CHECK"
  exit 1
fi
echo "✅ Database check passed"

echo "[T+4] All smoke tests passed ✅"
```

#### Step 3: Switch Traffic to Green (T+5)

```bash
#!/bin/bash
# switch-to-green.sh

set -e

echo "[T+5] Switching traffic from blue to green..."

# Update ALB listener to route 100% traffic to green
aws elbv2 modify-listener \
  --listener-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/techmart-alb/... \
  --default-actions Type=forward,ForwardConfig='{"TargetGroups":[{"TargetGroupArn":"arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-green/...","Weight":100},{"TargetGroupArn":"arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-blue/...","Weight":0}]}'

echo "[T+5] Traffic switched to green ✅"
echo "Monitoring for 15 minutes before declaring success..."
```

#### Step 4: Monitor Green Environment (T+5 to T+20)

```bash
#!/bin/bash
# monitor-green.sh

set -e

echo "[T+5] Monitoring green environment..."

for i in {1..15}; do
  echo "Minute $i of 15..."
  
  # Check error rate
  ERROR_RATE=$(aws cloudwatch get-metric-statistics \
    --namespace "TechMart/App" \
    --metric-name ErrorRate \
    --dimensions Name=Environment,Value=green \
    --start-time $(date -u -d '1 minute ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 60 \
    --statistics Average \
    --query 'Datapoints[0].Average' \
    --output text)
  
  if (( $(echo "$ERROR_RATE > 1.0" | bc -l) )); then
    echo "❌ Error rate too high: $ERROR_RATE%"
    echo "Triggering automatic rollback..."
    ./rollback-to-blue.sh
    exit 1
  fi
  
  # Check latency (p95)
  LATENCY_P95=$(aws cloudwatch get-metric-statistics \
    --namespace "TechMart/App" \
    --metric-name Latency \
    --dimensions Name=Environment,Value=green \
    --start-time $(date -u -d '1 minute ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 60 \
    --statistics Average \
    --extended-statistics p95 \
    --query 'Datapoints[0].ExtendedStatistics.p95' \
    --output text)
  
  if (( $(echo "$LATENCY_P95 > 500" | bc -l) )); then
    echo "⚠️  Latency elevated: ${LATENCY_P95}ms (threshold: 500ms)"
  fi
  
  echo "✅ Minute $i: Error rate ${ERROR_RATE}%, Latency p95 ${LATENCY_P95}ms"
  sleep 60
done

echo "[T+20] Monitoring complete. Deployment successful! ✅"
```

#### Step 5: Rollback Procedure (If Needed)

```bash
#!/bin/bash
# rollback-to-blue.sh

set -e

echo "[ROLLBACK] Rolling back to blue environment..."

# Update ALB listener to route 100% traffic back to blue
aws elbv2 modify-listener \
  --listener-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/techmart-alb/... \
  --default-actions Type=forward,ForwardConfig='{"TargetGroups":[{"TargetGroupArn":"arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-blue/...","Weight":100},{"TargetGroupArn":"arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/techmart-green/...","Weight":0}]}'

echo "[ROLLBACK] Traffic switched back to blue ✅"
echo "Rollback completed in < 1 minute"

# Send alert to team
curl -X POST https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK \
  -H 'Content-Type: application/json' \
  -d '{"text":"🚨 TechMart deployment rolled back to blue environment due to high error rate"}'
```

### Validation Criteria

**Success Metrics:**
- ✅ Error rate < 1% (green vs. blue baseline)
- ✅ Latency p95 < 300ms (green vs. 250ms blue baseline)
- ✅ Checkout success rate > 99.5%
- ✅ No critical errors in logs
- ✅ Database connection pool healthy
- ✅ Cache hit rate > 80%

**Rollback Triggers:**
- ❌ Error rate > 1% for 2 consecutive minutes
- ❌ Critical errors detected (payment failures, database errors)
- ❌ Latency p95 > 500ms for 5 consecutive minutes
- ❌ Manual decision by engineering manager

### Results

**Deployment Timeline:**
- T+0: Deploy to green (2 minutes)
- T+2: Smoke tests (2 minutes)
- T+5: Traffic switch (< 10 seconds)
- T+5 to T+20: Monitoring (15 minutes)
- **Total: 20 minutes**

**Metrics:**
- **Downtime:** 0 seconds ✅
- **Rollback Time:** < 1 minute ✅
- **Error Rate:** 0.3% (below 1% threshold) ✅
- **Latency p95:** 280ms (below 300ms threshold) ✅
- **Customer Impact:** Zero complaints ✅

**Cost:**
- **Additional Infrastructure:** $15,000/month (2x ECS tasks during deployment)
- **Deployment Duration:** 20 minutes
- **Cost per Deployment:** ~$7 (20 minutes of 2x infrastructure)
- **ROI:** Prevents $10,000/minute revenue loss

---

## Example 2: Canary Deployment for Payment Service

### Context

**Company:** FinPay Payment Processor  
**System:** Payment authorization service  
**Traffic:** 50,000 transactions/hour  
**Requirements:**
- Zero downtime
- Extremely low risk (payment processing)
- Gradual rollout with monitoring at each stage
- Fast rollback (< 2 minutes)
- Compliance: PCI DSS

**Current Pain Points:**
- Blue/green too risky (instant 100% traffic shift)
- Need gradual rollout to detect issues early
- Payment failures have severe business impact

### Strategy Selection

**Chosen Strategy:** Canary Deployment

**Rationale:**
- **Very Low Risk:** Gradual rollout (5% → 25% → 50% → 100%)
- **Early Detection:** Monitor at each stage before increasing traffic
- **Fast Rollback:** < 2 minutes (route traffic back to stable)
- **Cost-Effective:** Only 1.1x infrastructure (5% canary instances)

**Alternative Considered:** Blue/green (rejected due to instant 100% traffic shift risk)

### Architecture

**Infrastructure:**
- **Platform:** Kubernetes (GKE)
- **Service Mesh:** Istio (for traffic splitting)
- **Database:** Cloud SQL (PostgreSQL)
- **Monitoring:** Datadog

**Stable Version:**
- 20 pods (v2.4.0)
- Istio virtual service weight: 95%

**Canary Version:**
- 1 pod initially (v2.5.0)
- Istio virtual service weight: 5%

### Traffic Management

**Istio Virtual Service Configuration:**

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: payment-service
  namespace: production
spec:
  hosts:
    - payment-service.production.svc.cluster.local
  http:
    - match:
        - headers:
            x-canary:
              exact: "true"
      route:
        - destination:
            host: payment-service
            subset: canary
          weight: 100
    - route:
        - destination:
            host: payment-service
            subset: stable
          weight: 95
        - destination:
            host: payment-service
            subset: canary
          weight: 5
```

**Traffic Shifting Plan:**

```
T+0:  Stable 100%, Canary 0%   (Deploy canary)
T+5:  Stable 95%,  Canary 5%   (Monitor for 10 min)
T+15: Stable 75%,  Canary 25%  (Monitor for 10 min)
T+25: Stable 50%,  Canary 50%  (Monitor for 10 min)
T+35: Stable 0%,   Canary 100% (Complete)
```

### Deployment Procedure

#### Step 1: Deploy Canary (T+0)

```bash
#!/bin/bash
# deploy-canary.sh

set -e

echo "[T+0] Deploying canary version v2.5.0..."

# Update canary deployment
kubectl set image deployment/payment-service-canary \
  payment-service=gcr.io/finpay/payment-service:v2.5.0 \
  -n production

# Wait for canary pod to be ready
kubectl rollout status deployment/payment-service-canary -n production

echo "[T+2] Canary deployment ready"
```

#### Step 2: Shift 5% Traffic to Canary (T+5)

```bash
#!/bin/bash
# shift-traffic-5.sh

set -e

echo "[T+5] Shifting 5% traffic to canary..."

# Update Istio virtual service
kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: payment-service
  namespace: production
spec:
  hosts:
    - payment-service.production.svc.cluster.local
  http:
    - route:
        - destination:
            host: payment-service
            subset: stable
          weight: 95
        - destination:
            host: payment-service
            subset: canary
          weight: 5
EOF

echo "[T+5] 5% traffic shifted to canary"
echo "Monitoring for 10 minutes..."
```

#### Step 3: Monitor Canary (5% Traffic)

```bash
#!/bin/bash
# monitor-canary.sh

set -e

CANARY_WEIGHT=$1
MONITOR_DURATION=$2

echo "Monitoring canary at $CANARY_WEIGHT% traffic for $MONITOR_DURATION minutes..."

for i in $(seq 1 $MONITOR_DURATION); do
  echo "Minute $i of $MONITOR_DURATION..."
  
  # Get metrics from Datadog API
  STABLE_ERROR_RATE=$(curl -s -X GET "https://api.datadoghq.com/api/v1/query?query=avg:payment.error_rate{version:stable}" \
    -H "DD-API-KEY: $DD_API_KEY" \
    -H "DD-APPLICATION-KEY: $DD_APP_KEY" | jq '.series[0].pointlist[-1][1]')
  
  CANARY_ERROR_RATE=$(curl -s -X GET "https://api.datadoghq.com/api/v1/query?query=avg:payment.error_rate{version:canary}" \
    -H "DD-API-KEY: $DD_API_KEY" \
    -H "DD-APPLICATION-KEY: $DD_APP_KEY" | jq '.series[0].pointlist[-1][1]')
  
  # Compare canary vs. stable
  if (( $(echo "$CANARY_ERROR_RATE > $STABLE_ERROR_RATE * 1.5" | bc -l) )); then
    echo "❌ Canary error rate too high: $CANARY_ERROR_RATE% vs stable $STABLE_ERROR_RATE%"
    echo "Triggering rollback..."
    ./rollback-canary.sh
    exit 1
  fi
  
  # Check for critical errors
  CRITICAL_ERRORS=$(kubectl logs -l version=canary -n production --since=1m | grep -c "CRITICAL" || true)
  if [ "$CRITICAL_ERRORS" -gt 0 ]; then
    echo "❌ Critical errors detected in canary: $CRITICAL_ERRORS"
    ./rollback-canary.sh
    exit 1
  fi
  
  echo "✅ Minute $i: Stable error rate ${STABLE_ERROR_RATE}%, Canary error rate ${CANARY_ERROR_RATE}%"
  sleep 60
done

echo "✅ Monitoring complete. Canary healthy at $CANARY_WEIGHT% traffic."
```

#### Step 4: Gradual Traffic Increase

```bash
#!/bin/bash
# gradual-rollout.sh

set -e

echo "Starting gradual canary rollout..."

# Stage 1: 5% traffic
echo "[T+5] Stage 1: 5% traffic"
./shift-traffic.sh 5
./monitor-canary.sh 5 10

# Stage 2: 25% traffic
echo "[T+15] Stage 2: 25% traffic"
./shift-traffic.sh 25
./monitor-canary.sh 25 10

# Stage 3: 50% traffic
echo "[T+25] Stage 3: 50% traffic"
./shift-traffic.sh 50
./monitor-canary.sh 50 10

# Stage 4: 100% traffic
echo "[T+35] Stage 4: 100% traffic"
./shift-traffic.sh 100
./monitor-canary.sh 100 10

echo "[T+45] Canary rollout complete! ✅"
```

#### Step 5: Rollback Procedure

```bash
#!/bin/bash
# rollback-canary.sh

set -e

echo "[ROLLBACK] Rolling back canary deployment..."

# Route 100% traffic back to stable
kubectl apply -f - <<EOF
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: payment-service
  namespace: production
spec:
  hosts:
    - payment-service.production.svc.cluster.local
  http:
    - route:
        - destination:
            host: payment-service
            subset: stable
          weight: 100
        - destination:
            host: payment-service
            subset: canary
          weight: 0
EOF

echo "[ROLLBACK] 100% traffic routed back to stable"

# Scale down canary
kubectl scale deployment/payment-service-canary --replicas=0 -n production

echo "[ROLLBACK] Rollback complete in < 2 minutes ✅"

# Alert team
curl -X POST https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK \
  -H 'Content-Type: application/json' \
  -d '{"text":"🚨 Payment service canary rolled back due to elevated error rate"}'
```

### Validation Criteria

**Success Metrics (Canary vs. Stable):**
- ✅ Error rate within 10% of stable (e.g., stable 0.2%, canary < 0.22%)
- ✅ Latency p95 within 20% of stable
- ✅ Payment authorization success rate > 99.5%
- ✅ No critical errors in logs
- ✅ Database query latency similar to stable

**Rollback Triggers:**
- ❌ Canary error rate > 1.5x stable error rate
- ❌ Critical errors detected (payment authorization failures)
- ❌ Latency p95 > 2x stable latency
- ❌ Payment success rate < 99.5%
- ❌ Manual decision by on-call engineer

### Results

**Deployment Timeline:**
- T+0: Deploy canary (2 minutes)
- T+5: 5% traffic, monitor (10 minutes)
- T+15: 25% traffic, monitor (10 minutes)
- T+25: 50% traffic, monitor (10 minutes)
- T+35: 100% traffic, monitor (10 minutes)
- **Total: 45 minutes**

**Metrics:**
- **Downtime:** 0 seconds ✅
- **Rollback Time:** < 2 minutes ✅
- **Error Rate (Canary):** 0.21% vs. 0.20% stable ✅
- **Latency p95 (Canary):** 95ms vs. 90ms stable ✅
- **Payment Success Rate:** 99.8% ✅
- **Issues Detected:** 0 ✅

**Cost:**
- **Additional Infrastructure:** 1 canary pod (5% of 20 pods = 1 pod)
- **Cost Increase:** ~5% during deployment
- **Deployment Duration:** 45 minutes

---

## Example 3: Feature Flags for SaaS Application

### Context

**Company:** CloudDocs SaaS  
**System:** Document collaboration platform  
**Users:** 500,000 active users  
**Requirements:**
- A/B testing for new features
- Gradual rollout to user segments
- Instant rollback (toggle off)
- Decouple deployment from feature release

**Current Pain Points:**
- Can't test features with real users before full release
- No way to gradually roll out features
- Rollback requires code deployment (slow)

### Strategy Selection

**Chosen Strategy:** Feature Flags

**Rationale:**
- **Instant Rollback:** Toggle off feature flag (< 1 second)
- **Gradual Rollout:** Enable for 1% → 10% → 50% → 100% of users
- **A/B Testing:** Compare feature enabled vs. disabled
- **Decouple Deployment from Release:** Deploy code with flag disabled
- **Cost-Effective:** No additional infrastructure (1x)

**Alternative Considered:** Canary deployment (rejected due to infrastructure overhead)

### Architecture

**Infrastructure:**
- **Platform:** AWS (ECS Fargate)
- **Feature Flag Service:** LaunchDarkly
- **Database:** DynamoDB
- **Analytics:** Mixpanel

**Feature Flag Configuration:**

```json
{
  "key": "real-time-collaboration",
  "name": "Real-time Collaboration",
  "description": "Enable real-time collaborative editing",
  "kind": "boolean",
  "variations": [
    {
      "value": false,
      "name": "Disabled",
      "description": "Feature disabled"
    },
    {
      "value": true,
      "name": "Enabled",
      "description": "Feature enabled"
    }
  ],
  "targeting": {
    "rules": [
      {
        "clauses": [
          {
            "attribute": "email",
            "op": "endsWith",
            "values": ["@clouddocs.com"],
            "negate": false
          }
        ],
        "variation": 1,
        "description": "Internal users"
      },
      {
        "clauses": [
          {
            "attribute": "beta_user",
            "op": "in",
            "values": [true],
            "negate": false
          }
        ],
        "variation": 1,
        "description": "Beta users"
      }
    ],
    "rollout": {
      "variations": [
        {
          "variation": 0,
          "weight": 99000
        },
        {
          "variation": 1,
          "weight": 1000
        }
      ]
    }
  }
}
```

### Code Implementation

**Feature Flag in Application Code:**

```javascript
// services/documentService.js

const LaunchDarkly = require('launchdarkly-node-server-sdk');
const ldClient = LaunchDarkly.init(process.env.LAUNCHDARKLY_SDK_KEY);

class DocumentService {
  async openDocument(userId, documentId) {
    const user = await this.getUser(userId);
    
    // Check feature flag
    const isRealTimeCollabEnabled = await ldClient.variation(
      'real-time-collaboration',
      {
        key: userId,
        email: user.email,
        custom: {
          beta_user: user.betaUser,
          plan: user.plan
        }
      },
      false // default value if flag unavailable
    );
    
    if (isRealTimeCollabEnabled) {
      // New feature: Real-time collaboration
      return this.openDocumentWithRealTimeCollab(documentId, userId);
    } else {
      // Existing feature: Traditional editing
      return this.openDocumentTraditional(documentId, userId);
    }
  }
  
  async openDocumentWithRealTimeCollab(documentId, userId) {
    // Initialize WebSocket connection for real-time updates
    const wsConnection = await this.websocketService.connect(userId);
    
    // Subscribe to document changes
    await this.realtimeService.subscribe(documentId, wsConnection);
    
    // Load document with operational transforms
    const document = await this.documentRepo.findById(documentId);
    return {
      document,
      realtimeEnabled: true,
      wsConnection
    };
  }
  
  async openDocumentTraditional(documentId, userId) {
    // Traditional document loading (no real-time)
    const document = await this.documentRepo.findById(documentId);
    return {
      document,
      realtimeEnabled: false
    };
  }
}
```

### Rollout Plan

**Phase 1: Internal Users (Day 1)**
- Enable for all @clouddocs.com email addresses
- Monitor for bugs and performance issues
- Gather internal feedback

**Phase 2: Beta Users (Day 3)**
- Enable for users with `beta_user: true` flag
- ~10,000 users (2% of total)
- Monitor engagement metrics
- Gather user feedback via in-app surveys

**Phase 3: Gradual Rollout (Day 7-14)**
- Day 7: 1% of all users
- Day 9: 10% of all users
- Day 11: 25% of all users
- Day 13: 50% of all users
- Day 14: 100% of all users

**Phase 4: A/B Testing (Day 14-30)**
- 50% feature enabled, 50% feature disabled
- Measure engagement metrics:
  - Time spent in documents
  - Number of collaborators per document
  - User satisfaction (NPS)
  - Feature usage frequency

### Deployment Procedure

#### Step 1: Deploy Code with Flag Disabled (Day 0)

```bash
#!/bin/bash
# deploy-with-feature-flag.sh

set -e

echo "[Day 0] Deploying code with real-time-collaboration flag disabled..."

# Deploy new version with feature flag code
aws ecs update-service \
  --cluster clouddocs-prod \
  --service clouddocs-app \
  --task-definition clouddocs-app:v3.0.0 \
  --force-new-deployment

aws ecs wait services-stable \
  --cluster clouddocs-prod \
  --services clouddocs-app

echo "[Day 0] Deployment complete. Feature flag 'real-time-collaboration' is DISABLED for all users."
```

#### Step 2: Enable for Internal Users (Day 1)

```bash
#!/bin/bash
# enable-for-internal.sh

set -e

echo "[Day 1] Enabling real-time-collaboration for internal users..."

# Update LaunchDarkly flag via API
curl -X PATCH "https://app.launchdarkly.com/api/v2/flags/default/real-time-collaboration" \
  -H "Authorization: $LAUNCHDARKLY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "patch": [
      {
        "op": "add",
        "path": "/environments/production/targets/0",
        "value": {
          "values": ["internal"],
          "variation": 1
        }
      }
    ]
  }'

echo "[Day 1] Feature enabled for internal users (@clouddocs.com)"
echo "Monitoring for 48 hours..."
```

#### Step 3: Gradual Rollout (Day 7-14)

```bash
#!/bin/bash
# gradual-rollout.sh

set -e

echo "Starting gradual rollout of real-time-collaboration..."

# Day 7: 1% rollout
echo "[Day 7] Enabling for 1% of users..."
curl -X PATCH "https://app.launchdarkly.com/api/v2/flags/default/real-time-collaboration" \
  -H "Authorization: $LAUNCHDARKLY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "patch": [
      {
        "op": "replace",
        "path": "/environments/production/rollout/variations/1/weight",
        "value": 1000
      }
    ]
  }'
sleep $((48 * 3600))  # Wait 48 hours

# Day 9: 10% rollout
echo "[Day 9] Enabling for 10% of users..."
curl -X PATCH "https://app.launchdarkly.com/api/v2/flags/default/real-time-collaboration" \
  -H "Authorization: $LAUNCHDARKLY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "patch": [
      {
        "op": "replace",
        "path": "/environments/production/rollout/variations/1/weight",
        "value": 10000
      }
    ]
  }'
sleep $((48 * 3600))  # Wait 48 hours

# Continue for 25%, 50%, 100%...
```

#### Step 4: Monitor Feature Performance

```javascript
// monitoring/featureFlagMetrics.js

const mixpanel = require('mixpanel').init(process.env.MIXPANEL_TOKEN);

class FeatureFlagMetrics {
  async trackFeatureUsage(userId, featureKey, enabled) {
    mixpanel.track('Feature Flag Evaluated', {
      distinct_id: userId,
      feature_key: featureKey,
      enabled: enabled,
      timestamp: new Date().toISOString()
    });
  }
  
  async trackFeatureEngagement(userId, featureKey, action) {
    mixpanel.track('Feature Engagement', {
      distinct_id: userId,
      feature_key: featureKey,
      action: action,
      timestamp: new Date().toISOString()
    });
  }
  
  async getFeatureMetrics(featureKey) {
    // Get metrics from Mixpanel
    const enabledUsers = await this.getEnabledUserCount(featureKey);
    const engagementRate = await this.getEngagementRate(featureKey);
    const errorRate = await this.getErrorRate(featureKey);
    
    return {
      enabledUsers,
      engagementRate,
      errorRate
    };
  }
}
```

#### Step 5: Instant Rollback (If Needed)

```bash
#!/bin/bash
# rollback-feature-flag.sh

set -e

echo "[ROLLBACK] Disabling real-time-collaboration feature flag..."

# Disable feature flag via LaunchDarkly API
curl -X PATCH "https://app.launchdarkly.com/api/v2/flags/default/real-time-collaboration" \
  -H "Authorization: $LAUNCHDARKLY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "patch": [
      {
        "op": "replace",
        "path": "/environments/production/on",
        "value": false
      }
    ]
  }'

echo "[ROLLBACK] Feature flag disabled for all users in < 1 second ✅"

# Alert team
curl -X POST https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK \
  -H 'Content-Type: application/json' \
  -d '{"text":"🚨 Real-time collaboration feature flag disabled due to elevated error rate"}'
```

### Validation Criteria

**Success Metrics:**
- ✅ Feature engagement rate > 30% (users who try the feature)
- ✅ Error rate < 1% (feature-related errors)
- ✅ User satisfaction (NPS) > 40
- ✅ No critical bugs reported
- ✅ Performance impact < 10% (latency increase)

**Rollback Triggers:**
- ❌ Error rate > 2% (feature-related errors)
- ❌ Critical bugs (data loss, corruption)
- ❌ User satisfaction (NPS) < 20
- ❌ Performance degradation > 20%
- ❌ Manual decision by product manager

### Results

**Rollout Timeline:**
- Day 0: Deploy code with flag disabled
- Day 1: Enable for internal users (100 users)
- Day 3: Enable for beta users (10,000 users)
- Day 7: 1% rollout (5,000 users)
- Day 9: 10% rollout (50,000 users)
- Day 11: 25% rollout (125,000 users)
- Day 13: 50% rollout (250,000 users)
- Day 14: 100% rollout (500,000 users)
- **Total: 14 days**

**Metrics:**
- **Downtime:** 0 seconds ✅
- **Rollback Time:** < 1 second (instant toggle) ✅
- **Feature Engagement:** 45% (users tried feature) ✅
- **Error Rate:** 0.8% ✅
- **User Satisfaction (NPS):** 52 ✅
- **Performance Impact:** 5% latency increase ✅

**A/B Test Results (Day 14-30):**
- **Time Spent in Documents:** +25% (feature enabled vs. disabled)
- **Collaborators per Document:** +40%
- **User Retention:** +15%
- **Feature Usage Frequency:** 3.2x per week average

**Cost:**
- **LaunchDarkly:** $500/month (feature flag service)
- **Additional Infrastructure:** $0 (no extra infrastructure needed)
- **Total Cost:** $500/month

---

## Example 4: Rolling Deployment for Microservices Platform

### Context

**Company:** DevOps Tools Inc.  
**System:** Internal CI/CD platform (microservices)  
**Users:** 200 internal developers  
**Requirements:**
- Brief downtime acceptable (< 1 minute)
- Resource-constrained (limited budget)
- Simple deployment process
- Frequent deployments (multiple times per day)

**Current Pain Points:**
- Manual deployment process (slow, error-prone)
- No automated health checks
- Inconsistent deployment across services

### Strategy Selection

**Chosen Strategy:** Rolling Deployment

**Rationale:**
- **Low Complexity:** Built-in to Kubernetes (no extra tools)
- **Cost-Effective:** No additional infrastructure (1x)
- **Acceptable Downtime:** < 1 minute (brief instance restarts)
- **Fast Deployment:** 5-10 minutes total
- **Good Enough:** Internal tool, not customer-facing

**Alternative Considered:** Blue/green (rejected due to 2x infrastructure cost)

### Architecture

**Infrastructure:**
- **Platform:** Kubernetes (self-hosted)
- **Services:** 15 microservices
- **Database:** PostgreSQL (shared)
- **Monitoring:** Prometheus + Grafana

**Deployment Configuration:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ci-orchestrator
  namespace: devops-platform
spec:
  replicas: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Allow 1 extra pod during rollout
      maxUnavailable: 1  # Allow 1 pod to be unavailable
  selector:
    matchLabels:
      app: ci-orchestrator
  template:
    metadata:
      labels:
        app: ci-orchestrator
        version: v2.3.0
    spec:
      containers:
        - name: ci-orchestrator
          image: devops-tools/ci-orchestrator:v2.3.0
          ports:
            - containerPort: 8080
          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 30
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 10
            periodSeconds: 5
          resources:
            requests:
              cpu: 500m
              memory: 512Mi
            limits:
              cpu: 1000m
              memory: 1Gi
```

### Traffic Management

**Rolling Update Process:**

```
T+0:  5 pods (v2.2.0) running
T+1:  1 pod (v2.2.0) terminated, 1 pod (v2.3.0) starting
T+2:  4 pods (v2.2.0), 1 pod (v2.3.0) ready
T+3:  1 pod (v2.2.0) terminated, 1 pod (v2.3.0) starting
T+4:  3 pods (v2.2.0), 2 pods (v2.3.0) ready
T+5:  1 pod (v2.2.0) terminated, 1 pod (v2.3.0) starting
T+6:  2 pods (v2.2.0), 3 pods (v2.3.0) ready
T+7:  1 pod (v2.2.0) terminated, 1 pod (v2.3.0) starting
T+8:  1 pod (v2.2.0), 4 pods (v2.3.0) ready
T+9:  1 pod (v2.2.0) terminated, 1 pod (v2.3.0) starting
T+10: 0 pods (v2.2.0), 5 pods (v2.3.0) ready ✅
```

### Deployment Procedure

#### Step 1: Update Deployment Manifest

```bash
#!/bin/bash
# deploy-rolling.sh

set -e

SERVICE_NAME=$1
NEW_VERSION=$2

echo "Deploying $SERVICE_NAME to version $NEW_VERSION..."

# Update image version in deployment
kubectl set image deployment/$SERVICE_NAME \
  $SERVICE_NAME=devops-tools/$SERVICE_NAME:$NEW_VERSION \
  -n devops-platform

echo "Rolling update initiated"
```

#### Step 2: Monitor Rollout

```bash
#!/bin/bash
# monitor-rollout.sh

set -e

SERVICE_NAME=$1

echo "Monitoring rollout of $SERVICE_NAME..."

# Watch rollout status
kubectl rollout status deployment/$SERVICE_NAME -n devops-platform

if [ $? -eq 0 ]; then
  echo "✅ Rollout successful"
else
  echo "❌ Rollout failed"
  exit 1
fi
```

#### Step 3: Validate Deployment

```bash
#!/bin/bash
# validate-deployment.sh

set -e

SERVICE_NAME=$1
NEW_VERSION=$2

echo "Validating deployment of $SERVICE_NAME version $NEW_VERSION..."

# Check all pods are running new version
POD_COUNT=$(kubectl get pods -n devops-platform -l app=$SERVICE_NAME -o json | jq '.items | length')
NEW_VERSION_COUNT=$(kubectl get pods -n devops-platform -l app=$SERVICE_NAME,version=$NEW_VERSION -o json | jq '.items | length')

if [ "$POD_COUNT" -ne "$NEW_VERSION_COUNT" ]; then
  echo "❌ Not all pods running new version: $NEW_VERSION_COUNT/$POD_COUNT"
  exit 1
fi

echo "✅ All $POD_COUNT pods running version $NEW_VERSION"

# Run smoke tests
SERVICE_URL="http://$SERVICE_NAME.devops-platform.svc.cluster.local:8080"

HEALTH=$(curl -s "$SERVICE_URL/health" | jq -r '.status')
if [ "$HEALTH" != "healthy" ]; then
  echo "❌ Health check failed: $HEALTH"
  exit 1
fi

echo "✅ Health check passed"
echo "✅ Deployment validation complete"
```

#### Step 4: Rollback Procedure (If Needed)

```bash
#!/bin/bash
# rollback-deployment.sh

set -e

SERVICE_NAME=$1

echo "[ROLLBACK] Rolling back $SERVICE_NAME deployment..."

# Rollback to previous revision
kubectl rollout undo deployment/$SERVICE_NAME -n devops-platform

# Wait for rollback to complete
kubectl rollout status deployment/$SERVICE_NAME -n devops-platform

echo "[ROLLBACK] Rollback complete ✅"

# Alert team
curl -X POST https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK \
  -H 'Content-Type: application/json' \
  -d '{"text":"🚨 '$SERVICE_NAME' deployment rolled back to previous version"}'
```

### Validation Criteria

**Success Metrics:**
- ✅ All pods running new version
- ✅ Health checks passing
- ✅ Readiness probes passing
- ✅ No errors in logs
- ✅ Service responding to requests

**Rollback Triggers:**
- ❌ Pods crash looping
- ❌ Health checks failing
- ❌ Errors in logs (critical)
- ❌ Service not responding
- ❌ Manual decision by engineer

### Results

**Deployment Timeline:**
- T+0: Initiate rolling update
- T+1 to T+10: Pods updated one by one (10 minutes for 5 pods)
- T+10: All pods running new version
- T+11: Validation complete
- **Total: 11 minutes**

**Metrics:**
- **Downtime:** ~30 seconds (brief, during pod restarts) ✅
- **Rollback Time:** ~10 minutes (rolling update to previous version) ✅
- **Deployment Success Rate:** 95% ✅
- **Resource Usage:** 1x infrastructure (no extra cost) ✅
- **Deployment Frequency:** 5-10 times per day ✅

**Cost:**
- **Additional Infrastructure:** $0 (no extra infrastructure)
- **Deployment Time:** 11 minutes
- **Total Cost:** $0 per deployment

---

## Summary

These four examples demonstrate how to select and implement deployment strategies based on specific requirements:

1. **Blue/Green (E-commerce):** Zero downtime, instant rollback, 2x infrastructure
2. **Canary (Payment Service):** Very low risk, gradual rollout, fast rollback
3. **Feature Flags (SaaS):** Instant rollback, A/B testing, no extra infrastructure
4. **Rolling (Microservices):** Low complexity, cost-effective, brief downtime acceptable

**Key Takeaways:**
- Match strategy to requirements (downtime tolerance, risk, rollback speed)
- Automate deployment and rollback procedures
- Monitor closely during deployment
- Test rollback procedures regularly
- Document procedures for team

**Next Steps:**
- Adapt these examples to your specific context
- Implement automated deployment pipelines
- Establish monitoring and alerting
- Train team on deployment procedures
- Conduct regular DR drills
