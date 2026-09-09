# Deployment Strategy Design - Step-by-Step Instructions

## Overview

This document provides detailed, actionable instructions for choosing and designing deployment strategies that minimize risk, enable rapid rollback, and ensure high availability during application releases. Follow these steps sequentially to select and implement the optimal deployment strategy for your application.

## Prerequisites

Before starting, ensure you have:

- Understanding of application architecture and dependencies
- Knowledge of infrastructure capabilities (load balancers, orchestration platforms)
- Access to monitoring and observability tools
- Understanding of availability requirements and SLAs
- Familiarity with current deployment process (if exists)
- Permissions to configure infrastructure and deployments

## Step 1: Requirements Analysis

### 1.1 Analyze Application Characteristics

**Objective**: Understand application architecture and constraints.

**Actions**:
1. Document application architecture:
   ```markdown
   ## Application Architecture
   - Type: Microservices / Monolith / Serverless
   - Components: [List main components]
   - Dependencies: [External services, databases, APIs]
   - State management: Stateful / Stateless
   - Session handling: [How sessions are managed]
   ```

2. Measure application startup and shutdown:
   ```bash
   # Measure startup time
   time docker run myapp
   
   # Measure graceful shutdown time
   time docker stop myapp
   ```

3. Document resource requirements:
   ```markdown
   ## Resource Requirements
   - CPU: [e.g., 500m request, 1000m limit]
   - Memory: [e.g., 512Mi request, 1Gi limit]
   - Storage: [e.g., 10Gi persistent volume]
   - Network: [e.g., 1000 req/s capacity]
   ```

### 1.2 Define Availability Requirements

**Objective**: Establish uptime and downtime constraints.

**Actions**:
1. Document SLA requirements:
   ```markdown
   ## Availability Requirements
   - Uptime SLA: 99.9% / 99.95% / 99.99%
   - Acceptable downtime per month: [Calculate from SLA]
   - Maintenance window: [e.g., Sunday 2-4 AM UTC]
   - Business hours: [e.g., Mon-Fri 9 AM - 6 PM EST]
   - Peak traffic periods: [e.g., Black Friday, end of month]
   ```

2. Calculate downtime allowance:
   ```python
   # Calculate monthly downtime allowance
   sla_percentage = 99.9  # 99.9%
   hours_per_month = 730  # Average
   
   downtime_hours = hours_per_month * (1 - sla_percentage / 100)
   downtime_minutes = downtime_hours * 60
   
   print(f"SLA {sla_percentage}% allows {downtime_minutes:.1f} minutes downtime per month")
   # Output: SLA 99.9% allows 43.8 minutes downtime per month
   ```

3. Define RTO and RPO:
   ```markdown
   ## Disaster Recovery
   - RTO (Recovery Time Objective): [e.g., 1 hour]
   - RPO (Recovery Point Objective): [e.g., 15 minutes]
   - Backup frequency: [e.g., Hourly]
   - Geographic redundancy: [Required / Not required]
   ```

### 1.3 Assess Traffic Patterns

**Objective**: Understand traffic characteristics and user behavior.

**Actions**:
1. Analyze traffic metrics:
   ```sql
   -- Query traffic patterns from monitoring database
   SELECT 
       DATE_TRUNC('hour', timestamp) as hour,
       AVG(requests_per_second) as avg_rps,
       MAX(requests_per_second) as peak_rps,
       PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY requests_per_second) as p95_rps
   FROM traffic_metrics
   WHERE timestamp > NOW() - INTERVAL '30 days'
   GROUP BY hour
   ORDER BY hour;
   ```

2. Document traffic characteristics:
   ```markdown
   ## Traffic Patterns
   - Average traffic: [e.g., 1000 req/s]
   - Peak traffic: [e.g., 5000 req/s]
   - Traffic variability: Steady / Spiky / Seasonal
   - User session duration: [e.g., 15 minutes average]
   - Geographic distribution: [e.g., 60% US, 30% EU, 10% APAC]
   - Critical user journeys: [e.g., Checkout, Login, Search]
   ```

### 1.4 Determine Risk Tolerance

**Objective**: Understand acceptable risk and impact of failures.

**Actions**:
1. Assess business impact:
   ```markdown
   ## Risk Assessment
   - Revenue impact of 1 hour outage: $[amount]
   - Reputation impact: High / Medium / Low
   - Regulatory penalties: [If applicable]
   - Customer churn risk: High / Medium / Low
   - Acceptable error rate during deployment: [e.g., 0.1%]
   - Acceptable blast radius: [e.g., max 10% of users]
   ```

2. Define rollback requirements:
   ```markdown
   ## Rollback Requirements
   - Maximum rollback time: [e.g., 5 minutes]
   - Rollback trigger criteria: [e.g., error rate > 1%]
   - Acceptable data loss during rollback: [e.g., None]
   - Rollback testing frequency: [e.g., Monthly]
   ```

### 1.5 Evaluate Infrastructure Capabilities

**Objective**: Understand available infrastructure and tools.

**Actions**:
1. Document infrastructure:
   ```markdown
   ## Infrastructure Inventory
   
   ### Deployment Platform
   - Platform: Kubernetes / VMs / Serverless / Containers
   - Version: [e.g., Kubernetes 1.28]
   - Orchestration: [e.g., kubectl, Helm, Terraform]
   
   ### Load Balancing
   - Load balancer: [e.g., AWS ALB, NGINX, Istio]
   - Traffic splitting: Supported / Not supported
   - Health checks: [Capabilities]
   - Sticky sessions: Supported / Not supported
   
   ### Service Mesh
   - Service mesh: Istio / Linkerd / None
   - Traffic management: [Capabilities]
   - Observability: [Built-in features]
   
   ### Monitoring
   - Monitoring tool: [e.g., Datadog, Prometheus]
   - Metrics collection: [Real-time / Delayed]
   - Alerting: [Capabilities]
   - Dashboards: [Available / Need to create]
   ```

2. Test infrastructure capabilities:
   ```bash
   # Test load balancer traffic splitting
   kubectl apply -f test-traffic-split.yaml
   
   # Verify health check configuration
   kubectl describe service myapp | grep -A 5 "Health"
   
   # Test monitoring integration
   curl -X POST https://monitoring-api/test-event
   ```

**Deliverable**: Requirements documentation with application characteristics, availability requirements, traffic patterns, risk tolerance, and infrastructure capabilities.

---

## Step 2: Strategy Evaluation

### 2.1 Evaluate Blue/Green Deployment

**Objective**: Assess suitability of blue/green deployment.

**Actions**:
1. Create evaluation matrix:
   ```markdown
   ## Blue/Green Deployment Evaluation
   
   ### Pros
   - ✅ Instant rollback (< 1 minute)
   - ✅ Full validation before cutover
   - ✅ Zero downtime
   - ✅ Simple to understand and implement
   - ✅ Clean separation of environments
   
   ### Cons
   - ❌ Requires 2x infrastructure (high cost)
   - ❌ Database migrations can be complex
   - ❌ Need to maintain two identical environments
   - ❌ Wasted resources when not deploying
   
   ### Fit Assessment
   - Availability requirement: [Meets / Doesn't meet]
   - Budget constraint: [Within / Exceeds budget]
   - Infrastructure capability: [Supported / Not supported]
   - Team capability: [Can implement / Cannot implement]
   - Database compatibility: [Compatible / Incompatible]
   
   ### Overall Score: [X/10]
   ```

2. Calculate infrastructure cost:
   ```python
   # Calculate blue/green infrastructure cost
   single_env_cost = 5000  # Monthly cost for one environment
   blue_green_cost = single_env_cost * 2
   
   print(f"Blue/Green monthly cost: ${blue_green_cost}")
   print(f"Additional cost: ${blue_green_cost - single_env_cost}")
   ```

### 2.2 Evaluate Canary Deployment

**Objective**: Assess suitability of canary deployment.

**Actions**:
1. Create evaluation matrix:
   ```markdown
   ## Canary Deployment Evaluation
   
   ### Pros
   - ✅ Gradual risk mitigation
   - ✅ Test with real production traffic
   - ✅ Early issue detection
   - ✅ Minimal infrastructure overhead
   - ✅ Automated rollback based on metrics
   
   ### Cons
   - ❌ Requires sophisticated traffic routing
   - ❌ Need comprehensive monitoring
   - ❌ More complex to implement
   - ❌ Longer deployment time
   - ❌ Mixed versions in production
   
   ### Fit Assessment
   - Traffic routing capability: [Supported / Not supported]
   - Monitoring maturity: [Adequate / Inadequate]
   - Team expertise: [Sufficient / Insufficient]
   - Version compatibility: [Compatible / Incompatible]
   - Rollback automation: [Can implement / Cannot implement]
   
   ### Overall Score: [X/10]
   ```

2. Assess monitoring readiness:
   ```markdown
   ## Monitoring Readiness for Canary
   
   Required Metrics:
   - [ ] Error rate by version
   - [ ] Latency (P50, P95, P99) by version
   - [ ] Request rate by version
   - [ ] Success rate by version
   - [ ] Custom business metrics by version
   
   Alert Capabilities:
   - [ ] Automated alerts on metric thresholds
   - [ ] Comparison between canary and stable
   - [ ] Anomaly detection
   - [ ] Integration with deployment system
   ```

### 2.3 Evaluate Rolling Deployment

**Objective**: Assess suitability of rolling deployment.

**Actions**:
1. Create evaluation matrix:
   ```markdown
   ## Rolling Deployment Evaluation
   
   ### Pros
   - ✅ Minimal infrastructure overhead
   - ✅ Built-in Kubernetes support
   - ✅ Automatic rollback on failure
   - ✅ Simple to implement
   - ✅ Gradual rollout
   
   ### Cons
   - ❌ Mixed versions during deployment
   - ❌ Slower rollback than blue/green
   - ❌ Requires version compatibility
   - ❌ Limited pre-production validation
   
   ### Fit Assessment
   - Version compatibility: [Compatible / Incompatible]
   - Infrastructure cost sensitivity: [High / Low]
   - Orchestration platform: [Kubernetes / Other]
   - Rollback time requirement: [Meets / Doesn't meet]
   - Complexity tolerance: [Acceptable / Too complex]
   
   ### Overall Score: [X/10]
   ```

2. Test version compatibility:
   ```bash
   # Deploy old and new versions side-by-side
   kubectl apply -f deployment-v1.yaml
   kubectl apply -f deployment-v2.yaml
   
   # Test API compatibility
   curl http://v1-service/api/test
   curl http://v2-service/api/test
   
   # Verify database compatibility
   psql -c "SELECT version FROM schema_migrations;"
   ```

### 2.4 Evaluate Recreate Deployment

**Objective**: Assess suitability of recreate deployment.

**Actions**:
1. Create evaluation matrix:
   ```markdown
   ## Recreate Deployment Evaluation
   
   ### Pros
   - ✅ Simplest to implement
   - ✅ No version compatibility issues
   - ✅ Lowest infrastructure cost
   - ✅ Clean state between versions
   
   ### Cons
   - ❌ Requires downtime
   - ❌ Not suitable for high availability
   - ❌ Slow rollback
   - ❌ User impact during deployment
   
   ### Fit Assessment
   - Downtime tolerance: [Acceptable / Not acceptable]
   - Maintenance window: [Available / Not available]
   - Deployment frequency: [Low / High]
   - User impact tolerance: [High / Low]
   - Simplicity preference: [High / Low]
   
   ### Overall Score: [X/10]
   ```

### 2.5 Evaluate Feature Flags

**Objective**: Assess suitability of feature flag strategy.

**Actions**:
1. Create evaluation matrix:
   ```markdown
   ## Feature Flags Evaluation
   
   ### Pros
   - ✅ Decouple deployment from release
   - ✅ Instant feature rollback (< 1 second)
   - ✅ Targeted rollouts (by user, customer, region)
   - ✅ A/B testing support
   - ✅ Emergency kill switch
   
   ### Cons
   - ❌ Code complexity from flags
   - ❌ Need feature flag infrastructure
   - ❌ Flag cleanup required
   - ❌ Testing complexity
   - ❌ Potential for flag debt
   
   ### Fit Assessment
   - Feature flag infrastructure: [Exists / Need to build]
   - Code complexity tolerance: [Acceptable / Too complex]
   - A/B testing requirement: [Needed / Not needed]
   - Gradual rollout requirement: [Needed / Not needed]
   - Team discipline: [High / Low]
   
   ### Overall Score: [X/10]
   ```

2. Evaluate feature flag tools:
   ```markdown
   ## Feature Flag Tool Comparison
   
   | Tool | Cost | Features | Integration | Score |
   |------|------|----------|-------------|-------|
   | LaunchDarkly | $$$ | Excellent | Easy | 9/10 |
   | Unleash | $ | Good | Moderate | 7/10 |
   | Custom | Free | Basic | Hard | 5/10 |
   | Split.io | $$$ | Excellent | Easy | 8/10 |
   ```

### 2.6 Create Comparison Matrix

**Objective**: Compare all strategies objectively.

**Actions**:
1. Create comprehensive comparison:
   ```markdown
   ## Deployment Strategy Comparison Matrix
   
   | Criteria | Weight | Blue/Green | Canary | Rolling | Recreate | Feature Flags |
   |----------|--------|------------|--------|---------|----------|---------------|
   | Zero Downtime | 25% | 10 | 10 | 9 | 0 | 10 |
   | Rollback Speed | 20% | 10 | 8 | 6 | 3 | 10 |
   | Infrastructure Cost | 15% | 3 | 8 | 10 | 10 | 9 |
   | Implementation Complexity | 15% | 7 | 5 | 9 | 10 | 6 |
   | Risk Mitigation | 15% | 9 | 10 | 7 | 4 | 10 |
   | Testing Capability | 10% | 10 | 9 | 6 | 5 | 8 |
   | **Weighted Score** | | **8.0** | **8.4** | **7.9** | **5.3** | **8.9** |
   ```

2. Document trade-offs:
   ```markdown
   ## Key Trade-offs
   
   ### Blue/Green vs Canary
   - Blue/Green: Faster rollback, higher cost
   - Canary: Better risk mitigation, more complex
   
   ### Rolling vs Recreate
   - Rolling: Zero downtime, requires compatibility
   - Recreate: Simpler, requires downtime
   
   ### Feature Flags vs Deployment Strategies
   - Feature Flags: Decouple release from deployment
   - Can be combined with any deployment strategy
   ```

**Deliverable**: Strategy comparison matrix with scores, trade-off analysis, and fit assessment for each strategy.

---

## Step 3: Strategy Selection

### 3.1 Review Evaluation Results

**Objective**: Select optimal deployment strategy.

**Actions**:
1. Analyze scores and requirements:
   ```markdown
   ## Strategy Selection Analysis
   
   ### Top Candidates (Score > 8.0)
   1. Feature Flags (8.9) - Best for decoupling release from deployment
   2. Canary (8.4) - Best for risk mitigation with real traffic
   3. Blue/Green (8.0) - Best for instant rollback
   
   ### Requirements Alignment
   - Zero downtime required: ✅ All top candidates support
   - Budget constraint: ⚠️ Blue/Green exceeds budget
   - Risk mitigation: ✅ Canary and Feature Flags excel
   - Team expertise: ⚠️ Canary requires training
   
   ### Recommendation: Canary Deployment + Feature Flags
   - Combines gradual rollout with instant feature control
   - Meets all requirements within budget
   - Provides best risk mitigation
   ```

2. Consider hybrid approaches:
   ```markdown
   ## Hybrid Strategy Options
   
   ### Option 1: Canary + Feature Flags
   - Deploy with canary strategy
   - Use feature flags to control feature release
   - Benefits: Separate deployment and feature risk
   
   ### Option 2: Blue/Green + Canary
   - Deploy to green environment
   - Route small traffic to green (canary)
   - Full cutover when validated
   - Benefits: Combine instant rollback with gradual validation
   
   ### Option 3: Rolling + Feature Flags
   - Rolling deployment of new code
   - Features disabled by default
   - Enable features gradually via flags
   - Benefits: Low cost with feature control
   ```

### 3.2 Document Selection Rationale

**Objective**: Justify strategy selection.

**Actions**:
1. Create selection document:
   ```markdown
   # Deployment Strategy Selection
   
   ## Selected Strategy: Canary Deployment with Istio + Feature Flags
   
   ## Rationale
   
   ### Why Canary?
   - Allows testing with real production traffic
   - Gradual rollout minimizes blast radius
   - Automated rollback based on metrics
   - Fits within infrastructure budget
   - Supported by Istio service mesh
   
   ### Why Feature Flags?
   - Decouples deployment from feature release
   - Instant feature rollback without redeployment
   - Enables A/B testing
   - Supports targeted rollouts
   - Provides emergency kill switch
   
   ### Why Not Other Strategies?
   - Blue/Green: Exceeds budget (2x infrastructure)
   - Rolling: Less sophisticated risk mitigation
   - Recreate: Doesn't meet zero-downtime requirement
   
   ## Prerequisites for Implementation
   - [ ] Istio service mesh deployed
   - [ ] Prometheus monitoring configured
   - [ ] LaunchDarkly account and SDK integration
   - [ ] Team training on canary deployments
   - [ ] Automated rollback scripts
   ```

### 3.3 Define Success Criteria

**Objective**: Establish measurable success metrics.

**Actions**:
1. Define deployment success criteria:
   ```markdown
   ## Deployment Success Criteria
   
   ### Deployment Metrics
   - Deployment success rate: > 95%
   - Deployment duration: < 30 minutes
   - Rollback time: < 5 minutes
   - Rollback frequency: < 10%
   
   ### Availability Metrics
   - Uptime during deployments: > 99.95%
   - User-facing errors: < 0.1%
   - Service degradation: None
   
   ### Business Metrics
   - Revenue impact: $0
   - Customer complaints: < 5 per deployment
   - Deployment confidence: > 8/10 team survey
   ```

### 3.4 Plan Migration Path

**Objective**: Define transition from current to target state.

**Actions**:
1. Create migration plan:
   ```markdown
   ## Migration Plan: Current → Canary + Feature Flags
   
   ### Phase 1: Infrastructure Setup (Week 1-2)
   - Deploy Istio service mesh
   - Configure Prometheus monitoring
   - Set up LaunchDarkly
   - Create deployment automation
   
   ### Phase 2: Non-Production Testing (Week 3)
   - Test canary deployment in staging
   - Validate traffic routing
   - Test rollback procedures
   - Train team on new process
   
   ### Phase 3: Production Pilot (Week 4)
   - Deploy one low-risk service with canary
   - Monitor closely
   - Gather feedback
   - Refine process
   
   ### Phase 4: Full Rollout (Week 5-8)
   - Gradually migrate all services
   - Document lessons learned
   - Establish best practices
   ```

**Deliverable**: Strategy selection document with rationale, success criteria, and migration plan.

---

## Step 4: Infrastructure Design

### 4.1 Design Load Balancer Configuration

**Objective**: Configure load balancer for deployment strategy.

**Actions**:
1. For Canary with Istio:
   ```yaml
   # istio-virtual-service.yaml
   apiVersion: networking.istio.io/v1beta1
   kind: VirtualService
   metadata:
     name: myapp
   spec:
     hosts:
       - myapp.example.com
     http:
       - match:
           - headers:
               x-canary:
                 exact: "true"
         route:
           - destination:
               host: myapp
               subset: canary
             weight: 100
       - route:
           - destination:
               host: myapp
               subset: stable
             weight: 90
           - destination:
               host: myapp
               subset: canary
             weight: 10
   ```

2. For Blue/Green with AWS ALB:
   ```hcl
   # terraform/alb.tf
   resource "aws_lb_target_group" "blue" {
     name     = "myapp-blue"
     port     = 8080
     protocol = "HTTP"
     vpc_id   = var.vpc_id
     
     health_check {
       enabled             = true
       healthy_threshold   = 3
       interval            = 30
       matcher             = "200"
       path                = "/health"
       port                = "traffic-port"
       protocol            = "HTTP"
       timeout             = 5
       unhealthy_threshold = 3
     }
   }
   
   resource "aws_lb_target_group" "green" {
     name     = "myapp-green"
     port     = 8080
     protocol = "HTTP"
     vpc_id   = var.vpc_id
     
     health_check {
       enabled             = true
       healthy_threshold   = 3
       interval            = 30
       matcher             = "200"
       path                = "/health"
       port                = "traffic-port"
       protocol            = "HTTP"
       timeout             = 5
       unhealthy_threshold = 3
     }
   }
   
   resource "aws_lb_listener_rule" "production" {
     listener_arn = aws_lb_listener.main.arn
     priority     = 100
     
     action {
       type             = "forward"
       target_group_arn = var.active_environment == "blue" ? aws_lb_target_group.blue.arn : aws_lb_target_group.green.arn
     }
     
     condition {
       path_pattern {
         values = ["/*"]
       }
     }
   }
   ```

### 4.2 Design Health Check and Readiness Probes

**Objective**: Ensure traffic only routes to healthy instances.

**Actions**:
1. Implement comprehensive health checks:
   ```javascript
   // health-checks.js
   const express = require('express');
   const router = express.Router();
   
   // Startup probe - checks if application has started
   router.get('/health/startup', async (req, res) => {
       if (!global.appInitialized) {
           return res.status(503).json({
               status: 'starting',
               message: 'Application is still initializing'
           });
       }
       res.status(200).json({ status: 'started' });
   });
   
   // Liveness probe - checks if application is alive
   router.get('/health/live', async (req, res) => {
       const memoryUsage = process.memoryUsage();
       const heapUsedPercent = (memoryUsage.heapUsed / memoryUsage.heapTotal) * 100;
       
       if (heapUsedPercent > 95) {
           return res.status(503).json({
               status: 'unhealthy',
               reason: 'memory_critical'
           });
       }
       
       res.status(200).json({ status: 'alive' });
   });
   
   // Readiness probe - checks if application can serve traffic
   router.get('/health/ready', async (req, res) => {
       const checks = [];
       let allHealthy = true;
       
       // Check database
       try {
           await db.query('SELECT 1');
           checks.push({ name: 'database', status: 'healthy' });
       } catch (error) {
           allHealthy = false;
           checks.push({ name: 'database', status: 'unhealthy' });
       }
       
       // Check Redis
       try {
           await redis.ping();
           checks.push({ name: 'redis', status: 'healthy' });
       } catch (error) {
           allHealthy = false;
           checks.push({ name: 'redis', status: 'unhealthy' });
       }
       
       const status = allHealthy ? 200 : 503;
       res.status(status).json({
           status: allHealthy ? 'ready' : 'not_ready',
           checks: checks
       });
   });
   
   module.exports = router;
   ```

2. Configure Kubernetes probes:
   ```yaml
   # deployment.yaml
   apiVersion: apps/v1
   kind: Deployment
   spec:
     template:
       spec:
         containers:
           - name: myapp
             # Startup probe - gives app time to start
             startupProbe:
               httpGet:
                 path: /health/startup
                 port: 8080
               failureThreshold: 30
               periodSeconds: 10
             
             # Liveness probe - restarts unhealthy containers
             livenessProbe:
               httpGet:
                 path: /health/live
                 port: 8080
               initialDelaySeconds: 30
               periodSeconds: 10
               timeoutSeconds: 5
               failureThreshold: 3
             
             # Readiness probe - controls traffic routing
             readinessProbe:
               httpGet:
                 path: /health/ready
                 port: 8080
               initialDelaySeconds: 10
               periodSeconds: 5
               timeoutSeconds: 3
               successThreshold: 1
               failureThreshold: 3
   ```

### 4.3 Plan Resource Provisioning

**Objective**: Ensure adequate resources for deployment strategy.

**Actions**:
1. Calculate resource requirements:
   ```markdown
   ## Resource Requirements
   
   ### For Canary Deployment
   - Stable version: 20 replicas
   - Canary version: 2 replicas (10% traffic)
   - Total during deployment: 22 replicas
   - Overhead: 10%
   
   ### For Blue/Green Deployment
   - Blue environment: 20 replicas
   - Green environment: 20 replicas
   - Total during deployment: 40 replicas
   - Overhead: 100%
   
   ### For Rolling Deployment
   - Desired replicas: 20
   - Max surge: 25% (5 replicas)
   - Max unavailable: 10% (2 replicas)
   - Total during deployment: 23 replicas
   - Overhead: 15%
   ```

2. Configure resource limits:
   ```yaml
   # deployment.yaml
   spec:
     template:
       spec:
         containers:
           - name: myapp
             resources:
               requests:
                 cpu: 500m
                 memory: 512Mi
               limits:
                 cpu: 1000m
                 memory: 1Gi
   ```

**Deliverable**: Infrastructure design with load balancer configuration, health checks, and resource requirements.

---

## Step 5: Traffic Management Configuration

### 5.1 Configure Traffic Routing Rules

**Objective**: Set up traffic routing for deployment strategy.

**Actions**:
1. For Canary with Istio:
   ```yaml
   # istio-destination-rule.yaml
   apiVersion: networking.istio.io/v1beta1
   kind: DestinationRule
   metadata:
     name: myapp
   spec:
     host: myapp
     trafficPolicy:
       connectionPool:
         tcp:
           maxConnections: 100
         http:
           http1MaxPendingRequests: 50
           http2MaxRequests: 100
       outlierDetection:
         consecutiveErrors: 5
         interval: 30s
         baseEjectionTime: 30s
         maxEjectionPercent: 50
     subsets:
       - name: stable
         labels:
           version: v1.0.0
       - name: canary
         labels:
           version: v1.1.0
   ```

2. Configure traffic split percentages:
   ```python
   # update_traffic_split.py
   def update_traffic_split(canary_percentage):
       """
       Update Istio VirtualService traffic split
       """
       stable_percentage = 100 - canary_percentage
       
       virtual_service = {
           "apiVersion": "networking.istio.io/v1beta1",
           "kind": "VirtualService",
           "metadata": {"name": "myapp"},
           "spec": {
               "hosts": ["myapp"],
               "http": [{
                   "route": [
                       {
                           "destination": {
                               "host": "myapp",
                               "subset": "stable"
                           },
                           "weight": stable_percentage
                       },
                       {
                           "destination": {
                               "host": "myapp",
                               "subset": "canary"
                           },
                           "weight": canary_percentage
                       }
                   ]
               }]
           }
       }
       
       # Apply using kubectl
       import subprocess
       import json
       
       with open('/tmp/virtual-service.json', 'w') as f:
           json.dump(virtual_service, f)
       
       subprocess.run(['kubectl', 'apply', '-f', '/tmp/virtual-service.json'])
   ```

### 5.2 Configure Session Affinity (if needed)

**Objective**: Maintain user sessions during deployment.

**Actions**:
1. Configure sticky sessions:
   ```yaml
   # For Istio
   apiVersion: networking.istio.io/v1beta1
   kind: DestinationRule
   spec:
     trafficPolicy:
       loadBalancer:
         consistentHash:
           httpCookie:
             name: session-cookie
             ttl: 3600s
   ```

2. For stateless applications:
   ```markdown
   ## Session Management Strategy
   
   ### Externalize Session State
   - Store sessions in Redis
   - Use JWT tokens (stateless)
   - Avoid server-side sessions
   
   ### Benefits
   - No sticky session needed
   - Easier deployment
   - Better scalability
   ```

### 5.3 Configure Circuit Breakers and Retries

**Objective**: Prevent cascading failures during deployment.

**Actions**:
1. Configure circuit breakers:
   ```yaml
   # istio-destination-rule.yaml
   spec:
     trafficPolicy:
       outlierDetection:
         consecutiveErrors: 5
         interval: 30s
         baseEjectionTime: 30s
         maxEjectionPercent: 50
         minHealthPercent: 50
   ```

2. Configure retries:
   ```yaml
   # istio-virtual-service.yaml
   spec:
     http:
       - retries:
           attempts: 3
           perTryTimeout: 2s
           retryOn: 5xx,reset,connect-failure,refused-stream
   ```

**Deliverable**: Traffic management configuration with routing rules, session affinity, circuit breakers, and retries.

---

## Step 6: Deployment Automation

### 6.1 Create Deployment Scripts

**Objective**: Automate deployment execution.

**Actions**:
1. For Canary deployment:
   ```python
   # deploy_canary.py
   import time
   from kubernetes import client, config
   from prometheus_api_client import PrometheusConnect
   
   class CanaryDeployment:
       def __init__(self, service_name, new_version):
           self.service_name = service_name
           self.new_version = new_version
           self.k8s_apps = client.AppsV1Api()
           self.prometheus = PrometheusConnect(url="http://prometheus:9090")
       
       def deploy_canary(self):
           print(f"Deploying canary {self.new_version}")
           # Create canary deployment
           deployment = self._create_canary_deployment()
           self.k8s_apps.create_namespaced_deployment(
               namespace='production',
               body=deployment
           )
           self._wait_for_ready(f"{self.service_name}-canary")
       
       def progressive_rollout(self):
           stages = [
               {"percentage": 10, "duration": 300},
               {"percentage": 25, "duration": 300},
               {"percentage": 50, "duration": 600},
               {"percentage": 100, "duration": 300},
           ]
           
           for stage in stages:
               percentage = stage["percentage"]
               duration = stage["duration"]
               
               print(f"Setting canary traffic to {percentage}%")
               self._update_traffic_split(percentage)
               
               if not self._monitor_canary(duration):
                   print("Canary validation failed, rolling back")
                   self.rollback()
                   return False
           
           self.promote_canary()
           return True
       
       def _monitor_canary(self, duration):
           start_time = time.time()
           
           while time.time() - start_time < duration:
               error_rate = self._get_error_rate()
               if error_rate > 0.01:
                   return False
               
               p95_latency = self._get_p95_latency()
               if p95_latency > 500:
                   return False
               
               time.sleep(30)
           
           return True
   
   # Usage
   deployment = CanaryDeployment("myapp", "v1.1.0")
   deployment.deploy_canary()
   success = deployment.progressive_rollout()
   ```

2. For Blue/Green deployment:
   ```bash
   #!/bin/bash
   # deploy_blue_green.sh
   
   NEW_VERSION=$1
   ACTIVE_ENV=$(get_active_environment)
   INACTIVE_ENV=$([ "$ACTIVE_ENV" == "blue" ] && echo "green" || echo "blue")
   
   echo "Active: $ACTIVE_ENV, Deploying to: $INACTIVE_ENV"
   
   # Deploy to inactive environment
   kubectl set image deployment/myapp-$INACTIVE_ENV \
       myapp=myapp:$NEW_VERSION \
       -n production
   
   # Wait for rollout
   kubectl rollout status deployment/myapp-$INACTIVE_ENV -n production
   
   # Validate deployment
   if ! validate_deployment $INACTIVE_ENV; then
       echo "Validation failed"
       exit 1
   fi
   
   # Cutover
   echo "Cutting over to $INACTIVE_ENV"
   update_load_balancer $INACTIVE_ENV
   
   # Monitor
   if ! monitor_deployment 900; then
       echo "Monitoring failed, rolling back"
       update_load_balancer $ACTIVE_ENV
       exit 1
   fi
   
   echo "Deployment successful"
   ```

### 6.2 Implement Rollback Automation

**Objective**: Automate rollback procedures.

**Actions**:
1. Create rollback script:
   ```python
   # rollback.py
   def rollback_canary():
       """Rollback canary deployment"""
       print("Rolling back canary deployment")
       
       # Set traffic to 0% canary
       update_traffic_split(0)
       
       # Delete canary deployment
       k8s_apps.delete_namespaced_deployment(
           name=f"{service_name}-canary",
           namespace='production'
       )
       
       print("Rollback complete")
   
   def rollback_blue_green():
       """Rollback blue/green deployment"""
       active, inactive = get_active_environment()
       
       print(f"Rolling back to {inactive}")
       
       # Switch load balancer back
       update_load_balancer(inactive)
       
       print("Rollback complete")
   ```

2. Configure automatic rollback triggers:
   ```yaml
   # rollback-triggers.yaml
   triggers:
     - name: high_error_rate
       condition: error_rate > 0.01
       action: rollback
     
     - name: high_latency
       condition: p95_latency > 1000
       action: rollback
     
     - name: low_success_rate
       condition: success_rate < 0.99
       action: rollback
   ```

**Deliverable**: Deployment automation scripts with rollback automation and triggers.

---

## Step 7: Monitoring and Alerting Setup

### 7.1 Define Deployment Metrics

**Objective**: Track deployment health and success.

**Actions**:
1. Define key metrics:
   ```markdown
   ## Deployment Metrics
   
   ### Error Metrics
   - Error rate by version
   - Error rate comparison (canary vs stable)
   - Error types and distribution
   
   ### Performance Metrics
   - P50, P95, P99 latency by version
   - Request rate by version
   - Response time comparison
   
   ### Success Metrics
   - Success rate by version
   - Health check success rate
   - Deployment success/failure
   
   ### Traffic Metrics
   - Traffic distribution by version
   - Request volume by version
   - User distribution by version
   ```

2. Configure Prometheus queries:
   ```yaml
   # prometheus-queries.yaml
   queries:
     error_rate_by_version:
       query: |
         sum(rate(http_requests_total{status=~"5.."}[5m])) by (version) /
         sum(rate(http_requests_total[5m])) by (version)
     
     p95_latency_by_version:
       query: |
         histogram_quantile(0.95,
           sum(rate(http_request_duration_seconds_bucket[5m])) by (le, version)
         )
     
     traffic_distribution:
       query: |
         sum(rate(http_requests_total[5m])) by (version)
   ```

### 7.2 Create Deployment Dashboards

**Objective**: Visualize deployment health.

**Actions**:
1. Create Grafana dashboard:
   ```json
   {
     "dashboard": {
       "title": "Canary Deployment Dashboard",
       "panels": [
         {
           "title": "Traffic Distribution",
           "targets": [{
             "expr": "sum(rate(http_requests_total[5m])) by (version)"
           }]
         },
         {
           "title": "Error Rate Comparison",
           "targets": [{
             "expr": "sum(rate(http_requests_total{status=~'5..'}[5m])) by (version) / sum(rate(http_requests_total[5m])) by (version)"
           }]
         },
         {
           "title": "Latency Comparison (P95)",
           "targets": [{
             "expr": "histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, version))"
           }]
         }
       ]
     }
   }
   ```

### 7.3 Configure Alerts

**Objective**: Alert on deployment issues.

**Actions**:
1. Define alert rules:
   ```yaml
   # prometheus-alerts.yaml
   groups:
     - name: deployment
       rules:
         - alert: CanaryHighErrorRate
           expr: |
             sum(rate(http_requests_total{version="canary",status=~"5.."}[5m])) /
             sum(rate(http_requests_total{version="canary"}[5m])) > 0.01
           for: 5m
           labels:
             severity: critical
           annotations:
             summary: "Canary error rate exceeds threshold"
         
         - alert: CanaryHighLatency
           expr: |
             histogram_quantile(0.95,
               sum(rate(http_request_duration_seconds_bucket{version="canary"}[5m])) by (le)
             ) > 0.5
           for: 5m
           labels:
             severity: warning
           annotations:
             summary: "Canary P95 latency exceeds 500ms"
   ```

**Deliverable**: Monitoring configuration with metrics definitions, dashboards, and alert rules.

---

## Step 8: Rollback Procedure Design

### 8.1 Define Rollback Triggers

**Objective**: Establish clear rollback criteria.

**Actions**:
1. Document rollback triggers:
   ```markdown
   ## Rollback Triggers
   
   ### Automatic Rollback Triggers
   - Error rate > 1% for 5 minutes
   - P95 latency > 1000ms for 5 minutes
   - Success rate < 99% for 5 minutes
   - Health check failures > 50%
   - Critical alert fired
   
   ### Manual Rollback Triggers
   - User-reported critical issues
   - Data corruption detected
   - Security vulnerability discovered
   - Business decision to rollback
   - Compliance violation
   ```

2. Create rollback decision matrix:
   ```markdown
   | Metric | Threshold | Duration | Action |
   |--------|-----------|----------|--------|
   | Error rate | > 1% | 5 min | Auto rollback |
   | P95 latency | > 1000ms | 5 min | Auto rollback |
   | Success rate | < 99% | 5 min | Auto rollback |
   | Health checks | > 50% failing | 2 min | Auto rollback |
   | User complaints | > 10 | Immediate | Manual rollback |
   ```

### 8.2 Create Rollback Runbooks

**Objective**: Document rollback procedures.

**Actions**:
1. Create rollback runbook:
   ```markdown
   # Rollback Runbook
   
   ## Automatic Rollback (Canary)
   
   ### Trigger
   - Automated monitoring detects issue
   - Rollback script executes automatically
   
   ### Procedure
   1. Alert fires and triggers rollback
   2. Traffic immediately set to 0% canary
   3. Canary deployment deleted
   4. Verification: All traffic on stable
   5. Notification sent to team
   
   ### Verification
   - Check traffic distribution: 100% stable
   - Verify error rate returns to normal
   - Confirm canary pods deleted
   
   ## Manual Rollback (Blue/Green)
   
   ### Trigger
   - Manual decision to rollback
   - Execute rollback command
   
   ### Procedure
   1. Identify current active environment
   2. Switch load balancer to previous environment
   3. Verify traffic routing
   4. Monitor metrics
   5. Document rollback reason
   
   ### Commands
   ```bash
   # Get active environment
   ./get_active_env.sh
   
   # Rollback
   ./rollback_blue_green.sh
   
   # Verify
   curl https://myapp.com/health
   ```
   
   ### Post-Rollback
   - [ ] Verify all metrics normal
   - [ ] Notify stakeholders
   - [ ] Create incident ticket
   - [ ] Schedule post-mortem
   - [ ] Document lessons learned
   ```

### 8.3 Test Rollback Procedures

**Objective**: Validate rollback works reliably.

**Actions**:
1. Create rollback test plan:
   ```markdown
   ## Rollback Test Plan
   
   ### Test 1: Automatic Rollback on High Error Rate
   - Deploy canary with intentional errors
   - Verify automatic rollback triggers
   - Confirm traffic returns to stable
   - Validate rollback time < 5 minutes
   
   ### Test 2: Manual Rollback
   - Deploy to inactive environment
   - Cutover to new environment
   - Execute manual rollback
   - Verify rollback completes successfully
   
   ### Test 3: Rollback During High Load
   - Generate high traffic load
   - Trigger rollback
   - Verify no request failures
   - Confirm graceful rollback
   ```

2. Execute rollback tests:
   ```bash
   # Test automatic rollback
   ./test_rollback_automatic.sh
   
   # Test manual rollback
   ./test_rollback_manual.sh
   
   # Test rollback under load
   ./test_rollback_load.sh
   ```

**Deliverable**: Rollback procedures with triggers, runbooks, and test results.

---

## Step 9: Testing and Validation

### 9.1 Test Deployment in Non-Production

**Objective**: Validate deployment strategy before production.

**Actions**:
1. Deploy to staging environment:
   ```bash
   # Deploy canary to staging
   ./deploy_canary.sh staging v1.1.0
   
   # Monitor deployment
   watch kubectl get pods -n staging
   
   # Verify traffic routing
   kubectl get virtualservice -n staging -o yaml
   ```

2. Validate all deployment stages:
   ```markdown
   ## Staging Validation Checklist
   
   - [ ] Canary deployment created successfully
   - [ ] Health checks passing
   - [ ] Traffic routing works (10%, 25%, 50%, 100%)
   - [ ] Monitoring metrics collected
   - [ ] Alerts configured and firing correctly
   - [ ] Rollback works automatically
   - [ ] Manual rollback works
   - [ ] Performance acceptable
   - [ ] No errors in logs
   ```

### 9.2 Conduct Load Testing

**Objective**: Validate deployment under load.

**Actions**:
1. Run load tests:
   ```bash
   # Load test with k6
   k6 run --vus 100 --duration 10m load-test.js
   ```

2. Monitor during load test:
   ```markdown
   ## Load Test Monitoring
   
   - [ ] Error rate remains < 0.1%
   - [ ] P95 latency < 500ms
   - [ ] CPU usage < 80%
   - [ ] Memory usage < 80%
   - [ ] No pod restarts
   - [ ] Traffic distribution correct
   ```

### 9.3 Conduct Dry Run

**Objective**: Execute full deployment procedure as practice.

**Actions**:
1. Schedule dry run:
   ```markdown
   ## Deployment Dry Run
   
   **Date**: [Schedule date/time]
   **Participants**: [Team members]
   **Environment**: Staging
   
   ### Procedure
   1. Pre-deployment checklist
   2. Deploy canary
   3. Monitor metrics
   4. Progressive rollout
   5. Validation
   6. Rollback test
   7. Post-deployment review
   ```

2. Document dry run results:
   ```markdown
   ## Dry Run Results
   
   - Deployment time: [X minutes]
   - Issues encountered: [List]
   - Rollback time: [X minutes]
   - Team feedback: [Summary]
   - Action items: [List improvements]
   ```

**Deliverable**: Test results with staging validation, load test results, and dry run report.

---

## Step 10: Documentation and Training

### 10.1 Create Deployment Runbooks

**Objective**: Document standard deployment procedures.

**Actions**:
1. Create deployment runbook:
   ```markdown
   # Deployment Runbook: Canary Deployment
   
   ## Pre-Deployment Checklist
   
   - [ ] All tests passing in CI/CD
   - [ ] Security scans completed
   - [ ] Database migrations tested
   - [ ] Rollback plan documented
   - [ ] Stakeholders notified
   - [ ] Monitoring dashboards ready
   - [ ] On-call engineer identified
   
   ## Deployment Procedure
   
   ### Step 1: Deploy Canary
   ```bash
   ./deploy_canary.sh production v1.1.0
   ```
   
   **Expected**: Canary pods created, health checks passing
   
   ### Step 2: Route 10% Traffic
   ```bash
   ./update_traffic.sh 10
   ```
   
   **Expected**: 10% traffic to canary, 90% to stable
   
   ### Step 3: Monitor for 5 Minutes
   - Watch Grafana dashboard
   - Check error rate < 0.1%
   - Verify P95 latency < 500ms
   - Confirm no alerts
   
   ### Step 4: Increase to 25%
   ```bash
   ./update_traffic.sh 25
   ```
   
   ### Step 5: Monitor for 5 Minutes
   [Same monitoring as Step 3]
   
   ### Step 6: Increase to 50%
   ```bash
   ./update_traffic.sh 50
   ```
   
   ### Step 7: Monitor for 10 Minutes
   [Same monitoring as Step 3]
   
   ### Step 8: Promote to 100%
   ```bash
   ./promote_canary.sh
   ```
   
   ### Step 9: Cleanup
   - Delete old stable deployment
   - Update documentation
   - Notify stakeholders
   
   ## Rollback Procedure
   
   If any issues detected:
   ```bash
   ./rollback_canary.sh
   ```
   
   ## Post-Deployment
   
   - [ ] Verify all metrics normal
   - [ ] Monitor for 1 hour
   - [ ] Update deployment log
   - [ ] Document lessons learned
   ```

### 10.2 Create Training Materials

**Objective**: Enable team to execute deployment strategy.

**Actions**:
1. Create training presentation:
   ```markdown
   # Deployment Strategy Training
   
   ## Agenda
   1. Deployment strategy overview
   2. Why canary deployment?
   3. Infrastructure architecture
   4. Deployment procedure walkthrough
   5. Monitoring and metrics
   6. Rollback procedures
   7. Hands-on practice
   8. Q&A
   
   ## Key Concepts
   - Canary deployment
   - Traffic splitting
   - Health checks
   - Automated rollback
   - Progressive delivery
   ```

2. Conduct training sessions:
   ```markdown
   ## Training Schedule
   
   - **Session 1**: Overview and concepts (1 hour)
   - **Session 2**: Hands-on deployment (2 hours)
   - **Session 3**: Troubleshooting and rollback (1 hour)
   - **Session 4**: Best practices and Q&A (1 hour)
   ```

### 10.3 Create FAQ Documentation

**Objective**: Answer common questions.

**Actions**:
1. Document FAQs:
   ```markdown
   # Deployment Strategy FAQ
   
   ## Q: How long does a canary deployment take?
   A: Typically 30-45 minutes for full rollout with monitoring.
   
   ## Q: What happens if canary fails?
   A: Automatic rollback triggers, traffic returns to stable version.
   
   ## Q: Can I skip canary stages?
   A: No, progressive rollout is required for safety.
   
   ## Q: How do I rollback manually?
   A: Run `./rollback_canary.sh` or follow rollback runbook.
   
   ## Q: What if rollback fails?
   A: Escalate to on-call engineer, follow incident response.
   
   ## Q: Can I deploy during business hours?
   A: Yes, canary deployment is designed for anytime deployment.
   ```

**Deliverable**: Complete documentation including runbooks, training materials, and FAQ.

---

## Conclusion

Following these detailed instructions will result in a well-designed deployment strategy that:

- Minimizes deployment risk through gradual rollouts
- Enables rapid rollback when issues are detected
- Ensures high availability during deployments
- Provides clear procedures for deployment and rollback
- Empowers teams with training and documentation

Remember that deployment strategy is not static - continuously gather feedback, measure results, and iterate on improvements to maintain an effective deployment process that evolves with your application and team needs.