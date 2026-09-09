# Deployment Strategy Design Skill

## Purpose

Choose and design deployment strategies (blue/green, canary, rolling, recreate, feature flags) that minimize risk, enable rapid rollback, and ensure high availability during application releases.

## When to Use

Use this skill when:

- **Planning New Application Deployments**: Designing deployment approach for new applications or services
- **Improving Deployment Reliability**: Current deployments cause frequent outages or require extensive manual intervention
- **Implementing Zero-Downtime Deployments**: Business requires continuous availability during releases
- **Reducing Deployment Risk**: Need to minimize impact of potential deployment failures
- **Supporting Progressive Rollouts**: Want to gradually expose new versions to production traffic
- **Enabling Fast Rollback**: Need ability to quickly revert to previous version if issues arise
- **Testing in Production**: Want to validate new versions with real production traffic before full rollout
- **Supporting A/B Testing**: Need to run multiple versions simultaneously for experimentation
- **Migrating Between Deployment Strategies**: Transitioning from one deployment approach to another
- **Implementing Feature Flags**: Want to decouple deployment from feature release
- **Supporting Multi-Region Deployments**: Deploying across multiple geographic regions
- **Handling Database Schema Changes**: Need deployment strategy that accommodates database migrations
- **Managing Stateful Applications**: Deploying applications with persistent state or sessions
- **Optimizing Resource Usage**: Want to minimize infrastructure costs during deployments
- **Meeting Compliance Requirements**: Regulatory requirements dictate specific deployment controls

## When NOT to Use

Avoid this skill when:

- **Debugging Deployment Issues**: Use troubleshooting skills for active deployment problems
- **Implementing CI/CD Pipeline**: Use CI/CD design skill for overall pipeline architecture
- **Infrastructure Provisioning**: Use infrastructure-as-code skills for resource provisioning
- **Application Architecture Design**: Focus on application design before deployment strategy
- **Performance Optimization**: Use performance tuning skills for runtime optimization
- **Security Hardening**: Use security skills for security-specific concerns
- **Monitoring Setup**: Use observability skills for monitoring and alerting
- **Cost Optimization**: Use cloud cost optimization for financial analysis
- **Disaster Recovery Planning**: Use DR skills for backup and recovery strategies
- **Capacity Planning**: Use capacity planning skills for resource sizing
- **One-Time Deployments**: Simple one-off deployments don't require formal strategy
- **Static Content Deployment**: CDN deployments have different considerations
- **Configuration Changes Only**: Configuration updates may not need full deployment strategy
- **Development Environment**: Development deployments typically don't need production-grade strategies
- **Prototype or POC**: Proof-of-concept deployments can use simpler approaches

## Inputs

### Required Inputs

1. **Application Characteristics**
   - Application architecture (monolith, microservices, serverless, etc.)
   - Stateful vs stateless nature
   - Session management requirements
   - Database dependencies and schema change frequency
   - External service dependencies
   - Startup and shutdown time
   - Resource requirements (CPU, memory, storage)

2. **Availability Requirements**
   - Uptime SLA (99.9%, 99.95%, 99.99%, etc.)
   - Acceptable downtime window (if any)
   - Maintenance window availability
   - Business hours and peak traffic periods
   - Geographic distribution requirements
   - Disaster recovery requirements (RTO, RPO)

3. **Traffic Patterns**
   - Expected traffic volume (requests per second)
   - Traffic variability (steady, spiky, seasonal)
   - User session duration
   - Geographic distribution of users
   - Critical user journeys
   - Peak load characteristics

4. **Risk Tolerance**
   - Impact of deployment failures (revenue, reputation, compliance)
   - Acceptable error rate during deployment
   - Rollback time requirements
   - Blast radius tolerance (percentage of users affected)
   - Testing confidence level
   - Regulatory constraints

5. **Infrastructure Capabilities**
   - Deployment platform (Kubernetes, VMs, serverless, containers)
   - Load balancer capabilities (traffic splitting, health checks)
   - Service mesh availability (Istio, Linkerd, etc.)
   - Monitoring and observability tools
   - Infrastructure automation (Terraform, CloudFormation, etc.)
   - Network topology and constraints

### Optional Inputs

6. **Current Deployment Process**
   - Existing deployment strategy
   - Current deployment frequency
   - Historical deployment success rate
   - Common deployment failure modes
   - Rollback procedures
   - Deployment duration

7. **Team Capabilities**
   - Team size and expertise
   - On-call coverage
   - Deployment automation maturity
   - Monitoring and alerting maturity
   - Incident response capabilities

8. **Business Context**
   - Deployment frequency goals (daily, weekly, on-demand)
   - Release coordination requirements
   - Stakeholder notification needs
   - Marketing or launch event coordination
   - Competitive pressure for rapid releases

9. **Technical Constraints**
   - Legacy system integration requirements
   - Network latency constraints
   - Data residency requirements
   - Compliance and audit requirements
   - Budget constraints

10. **Feature Flag Infrastructure**
    - Existing feature flag system (LaunchDarkly, Unleash, custom)
    - Feature flag maturity
    - Dynamic configuration capabilities
    - User targeting capabilities

## Expected Outputs

### Primary Deliverables

1. **Deployment Strategy Recommendation**
   - Selected deployment strategy with justification
   - Strategy comparison matrix
   - Risk assessment for chosen strategy
   - Rollback approach and procedures
   - Success criteria and validation approach

2. **Implementation Plan**
   - Infrastructure requirements
   - Traffic management configuration
   - Health check and readiness probe definitions
   - Deployment automation scripts
   - Monitoring and alerting setup

3. **Deployment Workflow Documentation**
   - Step-by-step deployment procedure
   - Pre-deployment checklist
   - Deployment validation steps
   - Rollback triggers and procedures
   - Post-deployment verification

4. **Risk Mitigation Plan**
   - Identified deployment risks
   - Mitigation strategies for each risk
   - Monitoring and detection mechanisms
   - Incident response procedures
   - Communication plan

5. **Configuration Specifications**
   - Load balancer configuration
   - Service mesh configuration (if applicable)
   - Feature flag configuration (if applicable)
   - Environment-specific settings
   - Traffic routing rules

### Supporting Deliverables

6. **Deployment Architecture Diagrams**
   - Current state architecture
   - Target state architecture
   - Traffic flow diagrams
   - Rollback flow diagrams
   - Multi-region deployment topology (if applicable)

7. **Monitoring and Metrics Plan**
   - Key deployment metrics to track
   - Alert definitions and thresholds
   - Dashboard specifications
   - Success/failure criteria
   - Performance baseline expectations

8. **Testing Strategy**
   - Pre-deployment testing requirements
   - Canary validation tests (if applicable)
   - Smoke test definitions
   - Performance test approach
   - Rollback testing procedures

9. **Runbooks and Procedures**
   - Standard deployment runbook
   - Emergency rollback runbook
   - Troubleshooting guide
   - Escalation procedures
   - Communication templates

10. **Training Materials**
    - Deployment strategy overview
    - Operator training guide
    - Developer guidelines
    - FAQ and common scenarios
    - Lessons learned documentation

## Workflow

### Step 1: Requirements Analysis

**Objective**: Understand application characteristics and constraints.

**Actions**:
- Analyze application architecture and dependencies
- Document availability requirements and SLAs
- Assess current deployment process and pain points
- Identify regulatory and compliance constraints
- Evaluate team capabilities and maturity
- Review infrastructure capabilities and limitations
- Document risk tolerance and business impact
- Gather traffic patterns and usage data

**Outputs**:
- Requirements documentation
- Constraint analysis
- Current state assessment
- Stakeholder requirements matrix

**Quality Checks**:
- All stakeholders consulted
- Requirements are specific and measurable
- Constraints are clearly documented
- Current pain points identified

### Step 2: Strategy Evaluation

**Objective**: Evaluate deployment strategy options against requirements.

**Actions**:
- Assess blue/green deployment suitability
- Evaluate canary deployment feasibility
- Consider rolling deployment applicability
- Analyze recreate deployment appropriateness
- Evaluate feature flag integration benefits
- Consider hybrid approaches
- Create comparison matrix with pros/cons
- Score each strategy against requirements

**Outputs**:
- Strategy comparison matrix
- Scoring against requirements
- Risk assessment per strategy
- Cost analysis per strategy

**Quality Checks**:
- All viable strategies evaluated
- Evaluation criteria are objective
- Trade-offs clearly documented
- Costs estimated for each option

### Step 3: Strategy Selection

**Objective**: Choose optimal deployment strategy.

**Actions**:
- Review evaluation results with stakeholders
- Consider hybrid approaches if needed
- Document selection rationale
- Identify implementation prerequisites
- Plan migration path from current state
- Define success criteria
- Establish rollback strategy
- Document decision and approvals

**Outputs**:
- Selected deployment strategy
- Selection rationale document
- Implementation prerequisites
- Migration plan

**Quality Checks**:
- Selection aligns with requirements
- Stakeholders agree with choice
- Prerequisites are achievable
- Migration path is clear

### Step 4: Infrastructure Design

**Objective**: Design infrastructure to support chosen strategy.

**Actions**:
- Design load balancer configuration
- Plan traffic routing mechanisms
- Design health check and readiness probes
- Configure service mesh (if applicable)
- Plan resource provisioning
- Design network topology
- Plan monitoring and observability
- Design feature flag infrastructure (if needed)

**Outputs**:
- Infrastructure architecture diagrams
- Load balancer configuration
- Health check specifications
- Resource requirements

**Quality Checks**:
- Infrastructure supports chosen strategy
- Health checks are comprehensive
- Monitoring is adequate
- Resources are appropriately sized

### Step 5: Traffic Management Configuration

**Objective**: Configure traffic routing for deployment strategy.

**Actions**:
- Configure load balancer traffic splitting
- Set up weighted routing rules
- Configure DNS settings (if needed)
- Implement service mesh traffic management
- Configure sticky sessions (if needed)
- Set up traffic mirroring (if applicable)
- Configure circuit breakers and retries
- Test traffic routing behavior

**Outputs**:
- Traffic routing configuration
- Load balancer rules
- Service mesh policies
- Traffic split percentages

**Quality Checks**:
- Traffic routing works as expected
- Failover behavior is correct
- Session affinity works (if needed)
- Circuit breakers function properly

### Step 6: Deployment Automation

**Objective**: Automate deployment execution.

**Actions**:
- Create deployment automation scripts
- Implement version management
- Configure deployment orchestration
- Automate health check validation
- Implement automatic rollback triggers
- Create deployment status reporting
- Integrate with CI/CD pipeline
- Test automation end-to-end

**Outputs**:
- Deployment automation scripts
- Orchestration configuration
- Rollback automation
- Status reporting integration

**Quality Checks**:
- Automation is reliable and repeatable
- Error handling is comprehensive
- Rollback automation works
- Status reporting is accurate

### Step 7: Monitoring and Alerting Setup

**Objective**: Implement monitoring for deployment validation.

**Actions**:
- Define deployment success metrics
- Configure deployment-specific alerts
- Set up deployment dashboards
- Implement error rate monitoring
- Configure latency monitoring
- Set up traffic distribution monitoring
- Implement version tracking
- Configure anomaly detection

**Outputs**:
- Monitoring configuration
- Alert definitions
- Dashboard specifications
- Metric collection setup

**Quality Checks**:
- All critical metrics are monitored
- Alerts are actionable
- Dashboards provide clear visibility
- Anomaly detection is tuned

### Step 8: Rollback Procedure Design

**Objective**: Define and automate rollback procedures.

**Actions**:
- Define rollback triggers (automatic and manual)
- Create rollback automation
- Document rollback procedures
- Test rollback execution
- Define rollback decision criteria
- Establish rollback communication plan
- Create rollback runbooks
- Validate rollback time meets requirements

**Outputs**:
- Rollback automation scripts
- Rollback runbooks
- Rollback decision criteria
- Communication templates

**Quality Checks**:
- Rollback is fast and reliable
- Rollback procedures are documented
- Rollback triggers are appropriate
- Communication plan is clear

### Step 9: Testing and Validation

**Objective**: Validate deployment strategy works as designed.

**Actions**:
- Test deployment in non-production environment
- Validate traffic routing behavior
- Test rollback procedures
- Conduct failure scenario testing
- Validate monitoring and alerting
- Test with production-like load
- Validate health checks and readiness probes
- Conduct end-to-end deployment dry run

**Outputs**:
- Test results documentation
- Identified issues and resolutions
- Performance validation results
- Dry run report

**Quality Checks**:
- All scenarios tested successfully
- Rollback works reliably
- Monitoring detects issues
- Performance meets requirements

### Step 10: Documentation and Training

**Objective**: Enable teams to execute deployment strategy.

**Actions**:
- Create deployment runbooks
- Document rollback procedures
- Write troubleshooting guides
- Create training materials
- Conduct team training sessions
- Document lessons learned
- Create FAQ documentation
- Establish feedback mechanisms

**Outputs**:
- Deployment runbooks
- Training materials
- Troubleshooting guides
- FAQ documentation

**Quality Checks**:
- Documentation is clear and complete
- Team is trained and confident
- Runbooks are actionable
- Feedback process established

## Decision Framework

### Blue/Green Deployment

**Choose Blue/Green when**:
- Zero downtime is absolutely critical
- Can afford duplicate infrastructure costs
- Need instant rollback capability (< 1 minute)
- Database changes are backward compatible
- Want to validate entire deployment before cutover
- Have simple traffic switching mechanism
- Application is stateless or session state is externalized
- Deployment validation can be done on idle environment

**Avoid Blue/Green when**:
- Infrastructure costs are prohibitive (2x resources)
- Database schema changes require migration
- Application has complex state management
- Cannot maintain two complete environments
- Deployment validation requires production traffic
- Infrastructure is highly customized and hard to duplicate

### Canary Deployment

**Choose Canary when**:
- Want to test with real production traffic
- Need gradual risk mitigation
- Have sophisticated monitoring and metrics
- Can route traffic based on criteria (percentage, user attributes)
- Want to detect issues before full rollout
- Have automated rollback capabilities
- Can tolerate mixed versions in production
- Need to validate performance under real load

**Avoid Canary when**:
- Cannot run multiple versions simultaneously
- Lack sophisticated traffic routing capabilities
- Monitoring and metrics are insufficient
- Cannot tolerate any production errors
- Database schema changes are incompatible across versions
- Application has strong version dependencies

### Rolling Deployment

**Choose Rolling when**:
- Have multiple instances to update incrementally
- Want to minimize infrastructure costs
- Can tolerate mixed versions temporarily
- Need automatic rollback on failure
- Don't require instant full rollback
- Application supports backward compatibility
- Have orchestration platform (Kubernetes, etc.)
- Want simple, automated deployment process

**Avoid Rolling when**:
- Cannot tolerate mixed versions
- Need instant rollback capability
- Database changes require all instances on same version
- Application has version-specific dependencies
- Have only single instance
- Require extensive pre-production validation

### Recreate Deployment

**Choose Recreate when**:
- Downtime is acceptable
- Cannot run multiple versions simultaneously
- Have database schema changes requiring downtime
- Want simplest deployment approach
- Have maintenance window availability
- Application has complex state that's hard to migrate
- Infrastructure resources are limited
- Deployment frequency is low

**Avoid Recreate when**:
- Zero downtime is required
- Deployment frequency is high
- Downtime impacts revenue significantly
- SLA doesn't permit downtime
- Users are global (no good maintenance window)
- Competitive pressure requires continuous availability

### Feature Flags

**Choose Feature Flags when**:
- Want to decouple deployment from release
- Need to enable features for specific users
- Want to conduct A/B testing
- Need emergency feature kill switch
- Want to gradually roll out features
- Have complex feature dependencies
- Need to coordinate releases across teams
- Want to reduce deployment risk

**Avoid Feature Flags when**:
- Features are simple and low-risk
- Don't have feature flag infrastructure
- Code complexity from flags is too high
- Team lacks feature flag management discipline
- Features are tightly coupled to deployment
- Cannot maintain flag cleanup processes

### Hybrid Approaches

**Blue/Green + Canary**:
- Deploy to green environment
- Route small percentage to green (canary)
- Gradually increase traffic
- Full cutover when validated
- Instant rollback by switching back to blue

**Rolling + Feature Flags**:
- Rolling deployment of new code
- Features disabled by default
- Gradually enable features via flags
- Independent feature and deployment rollback

**Canary + Feature Flags**:
- Canary deployment with feature flags off
- Validate deployment health
- Enable features gradually via flags
- Separate deployment and feature risk

## Quality Checklist

### Strategy Selection Quality

- [ ] Strategy aligns with availability requirements
- [ ] Strategy supports required deployment frequency
- [ ] Strategy is compatible with application architecture
- [ ] Strategy fits within infrastructure capabilities
- [ ] Strategy meets rollback time requirements
- [ ] Strategy is within budget constraints
- [ ] Strategy is appropriate for team maturity
- [ ] Strategy addresses identified risks
- [ ] Strategy supports business objectives
- [ ] Stakeholders approve selected strategy

### Infrastructure Quality

- [ ] Load balancer supports required traffic routing
- [ ] Health checks accurately detect application health
- [ ] Readiness probes prevent premature traffic routing
- [ ] Infrastructure can handle peak load
- [ ] Network topology supports deployment strategy
- [ ] Monitoring provides adequate visibility
- [ ] Infrastructure is properly sized
- [ ] Redundancy and failover are configured
- [ ] Security controls are implemented
- [ ] Infrastructure is documented

### Traffic Management Quality

- [ ] Traffic routing works as designed
- [ ] Traffic split percentages are configurable
- [ ] Session affinity works correctly (if needed)
- [ ] Circuit breakers prevent cascading failures
- [ ] Retries are configured appropriately
- [ ] Timeouts are set correctly
- [ ] Traffic mirroring works (if applicable)
- [ ] DNS configuration is correct
- [ ] Load balancing algorithm is appropriate
- [ ] Failover behavior is correct

### Deployment Automation Quality

- [ ] Deployment is fully automated
- [ ] Deployment is idempotent
- [ ] Deployment handles failures gracefully
- [ ] Deployment provides clear status
- [ ] Deployment integrates with CI/CD
- [ ] Deployment is repeatable
- [ ] Deployment time meets requirements
- [ ] Deployment supports rollback
- [ ] Deployment validates success
- [ ] Deployment logs are comprehensive

### Monitoring Quality

- [ ] All critical metrics are monitored
- [ ] Alerts are actionable and not noisy
- [ ] Dashboards provide clear visibility
- [ ] Metrics are collected in real-time
- [ ] Anomaly detection is configured
- [ ] Version tracking is implemented
- [ ] Error rates are monitored
- [ ] Latency is monitored
- [ ] Traffic distribution is visible
- [ ] Deployment events are tracked

### Rollback Quality

- [ ] Rollback procedures are documented
- [ ] Rollback can be executed quickly
- [ ] Rollback is tested and validated
- [ ] Rollback triggers are defined
- [ ] Rollback automation works reliably
- [ ] Rollback communication plan exists
- [ ] Rollback decision criteria are clear
- [ ] Rollback time meets requirements
- [ ] Rollback preserves data integrity
- [ ] Rollback is well understood by team

### Testing Quality

- [ ] Deployment tested in non-production
- [ ] Traffic routing validated
- [ ] Rollback tested successfully
- [ ] Failure scenarios tested
- [ ] Load testing completed
- [ ] Health checks validated
- [ ] Monitoring validated
- [ ] End-to-end dry run successful
- [ ] Performance meets requirements
- [ ] All issues resolved

### Documentation Quality

- [ ] Deployment runbooks are complete
- [ ] Rollback procedures documented
- [ ] Troubleshooting guides created
- [ ] Architecture diagrams current
- [ ] Configuration documented
- [ ] Training materials created
- [ ] FAQ documentation exists
- [ ] Lessons learned captured
- [ ] Documentation is accessible
- [ ] Documentation is maintained

## Common Mistakes

### Strategy Selection Mistakes

1. **Choosing Strategy Based on Hype**
   - **Mistake**: Selecting trendy deployment strategy without evaluating fit
   - **Impact**: Strategy doesn't match requirements, causes deployment issues
   - **Solution**: Evaluate strategies objectively against specific requirements
   - **Prevention**: Use decision framework, create comparison matrix

2. **Ignoring Infrastructure Constraints**
   - **Mistake**: Choosing strategy that infrastructure cannot support
   - **Impact**: Cannot implement chosen strategy, delays and rework
   - **Solution**: Assess infrastructure capabilities before strategy selection
   - **Prevention**: Include infrastructure team in strategy evaluation

3. **Underestimating Complexity**
   - **Mistake**: Choosing complex strategy without adequate team expertise
   - **Impact**: Implementation failures, operational difficulties
   - **Solution**: Match strategy complexity to team capabilities
   - **Prevention**: Assess team skills, provide training, start simple

4. **Not Considering Database Changes**
   - **Mistake**: Ignoring database schema migration requirements
   - **Impact**: Deployment failures, data corruption, extended downtime
   - **Solution**: Design strategy that accommodates database changes
   - **Prevention**: Include database team in strategy design

5. **Overlooking Cost Implications**
   - **Mistake**: Choosing strategy without considering infrastructure costs
   - **Impact**: Budget overruns, unsustainable costs
   - **Solution**: Calculate total cost of ownership for each strategy
   - **Prevention**: Include cost analysis in strategy evaluation

### Implementation Mistakes

6. **Inadequate Health Checks**
   - **Mistake**: Using simple health checks that don't validate application readiness
   - **Impact**: Traffic routed to unhealthy instances, user-facing errors
   - **Solution**: Implement comprehensive health and readiness checks
   - **Prevention**: Test health checks thoroughly, validate dependencies

7. **Poor Traffic Routing Configuration**
   - **Mistake**: Incorrect load balancer or service mesh configuration
   - **Impact**: Traffic not distributed as intended, deployment failures
   - **Solution**: Carefully configure and test traffic routing
   - **Prevention**: Validate configuration in non-production first

8. **Missing Rollback Automation**
   - **Mistake**: Manual rollback procedures that are slow and error-prone
   - **Impact**: Extended outages, increased MTTR
   - **Solution**: Automate rollback procedures
   - **Prevention**: Test rollback automation regularly

9. **Insufficient Monitoring**
   - **Mistake**: Deploying without adequate monitoring to detect issues
   - **Impact**: Issues not detected until users complain
   - **Solution**: Implement comprehensive deployment monitoring
   - **Prevention**: Define monitoring requirements upfront

10. **Skipping Non-Production Testing**
    - **Mistake**: Testing deployment strategy only in production
    - **Impact**: Production incidents, user impact
    - **Solution**: Test thoroughly in non-production environments
    - **Prevention**: Require successful non-production validation

### Operational Mistakes

11. **Not Defining Rollback Triggers**
    - **Mistake**: Unclear criteria for when to rollback
    - **Impact**: Delayed rollback decisions, extended incidents
    - **Solution**: Define clear rollback triggers and thresholds
    - **Prevention**: Document rollback decision criteria

12. **Ignoring Session State**
    - **Mistake**: Not considering user sessions during deployment
    - **Impact**: Users logged out, sessions lost, poor user experience
    - **Solution**: Plan for session handling in deployment strategy
    - **Prevention**: Include session management in strategy design

13. **Poor Communication**
    - **Mistake**: Not communicating deployment status to stakeholders
    - **Impact**: Confusion, duplicate work, poor incident response
    - **Solution**: Establish clear communication plan
    - **Prevention**: Define communication requirements upfront

14. **Not Testing Rollback**
    - **Mistake**: Assuming rollback will work without testing
    - **Impact**: Rollback fails when needed, extended outages
    - **Solution**: Test rollback procedures regularly
    - **Prevention**: Include rollback testing in validation

15. **Inadequate Documentation**
    - **Mistake**: Poor or missing deployment and rollback documentation
    - **Impact**: Slow deployments, errors, knowledge silos
    - **Solution**: Create comprehensive runbooks and documentation
    - **Prevention**: Document as you design and implement

### Canary-Specific Mistakes

16. **Canary Too Small**
    - **Mistake**: Canary receives too little traffic to detect issues
    - **Impact**: Issues not detected until full rollout
    - **Solution**: Size canary to receive statistically significant traffic
    - **Prevention**: Calculate minimum canary size based on traffic

17. **Canary Too Large**
    - **Mistake**: Canary receives too much traffic, increasing blast radius
    - **Impact**: Too many users affected if canary has issues
    - **Solution**: Start with small canary, increase gradually
    - **Prevention**: Define canary progression plan

18. **Insufficient Canary Duration**
    - **Mistake**: Promoting canary too quickly
    - **Impact**: Issues not detected, appear after full rollout
    - **Solution**: Monitor canary for sufficient duration
    - **Prevention**: Define minimum canary duration based on metrics

19. **Poor Canary Metrics**
    - **Mistake**: Not monitoring right metrics for canary validation
    - **Impact**: Issues not detected or false positives
    - **Solution**: Define comprehensive canary success metrics
    - **Prevention**: Establish metrics and thresholds upfront

20. **Manual Canary Promotion**
    - **Mistake**: Requiring manual intervention to promote canary
    - **Impact**: Slow deployments, human error, delays
    - **Solution**: Automate canary promotion based on metrics
    - **Prevention**: Design automated promotion from the start

## Examples

### Example 1: E-Commerce Platform - Canary Deployment with Istio

**Context**:
A high-traffic e-commerce platform with 1 million daily active users needs to deploy new features multiple times per week. The platform consists of microservices running on Kubernetes with Istio service mesh. Current deployment process uses rolling updates but has caused several production incidents due to undetected issues. The business requires 99.95% uptime and cannot tolerate revenue-impacting outages.

**Requirements**:
- Zero revenue-impacting outages
- Deploy 3-5 times per week
- Detect issues before affecting majority of users
- Rollback within 5 minutes if issues detected
- Support A/B testing for new features
- Maintain user sessions during deployments

**Solution Design**:

**Selected Strategy**: Canary Deployment with Istio + Feature Flags

**Rationale**:
- Canary allows testing with real production traffic
- Gradual rollout minimizes blast radius
- Istio provides sophisticated traffic management
- Feature flags decouple deployment from feature release
- Automated rollback based on metrics

**Implementation**:

1. **Istio VirtualService Configuration**:
```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: product-service
spec:
  hosts:
    - product-service
  http:
    - match:
        - headers:
            x-canary:
              exact: "true"
      route:
        - destination:
            host: product-service
            subset: canary
          weight: 100
    - route:
        - destination:
            host: product-service
            subset: stable
          weight: 90
        - destination:
            host: product-service
            subset: canary
          weight: 10
---
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: product-service
spec:
  host: product-service
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
        version: v1.2.3
    - name: canary
      labels:
        version: v1.2.4
```

2. **Canary Deployment Automation**:
```python
# deploy_canary.py
import time
import requests
from kubernetes import client, config
from prometheus_api_client import PrometheusConnect

class CanaryDeployment:
    def __init__(self, service_name, new_version, namespace='production'):
        self.service_name = service_name
        self.new_version = new_version
        self.namespace = namespace
        self.k8s_apps = client.AppsV1Api()
        self.prometheus = PrometheusConnect(url="http://prometheus:9090")
        
    def deploy_canary(self):
        """Deploy canary version"""
        print(f"Deploying canary {self.new_version} for {self.service_name}")
        
        # Create canary deployment
        deployment = self._create_canary_deployment()
        self.k8s_apps.create_namespaced_deployment(
            namespace=self.namespace,
            body=deployment
        )
        
        # Wait for canary to be ready
        self._wait_for_ready(f"{self.service_name}-canary")
        
    def progressive_rollout(self):
        """Gradually increase canary traffic"""
        stages = [
            {"percentage": 10, "duration": 300},   # 10% for 5 minutes
            {"percentage": 25, "duration": 300},   # 25% for 5 minutes
            {"percentage": 50, "duration": 600},   # 50% for 10 minutes
            {"percentage": 75, "duration": 300},   # 75% for 5 minutes
            {"percentage": 100, "duration": 300},  # 100% for 5 minutes
        ]
        
        for stage in stages:
            percentage = stage["percentage"]
            duration = stage["duration"]
            
            print(f"Setting canary traffic to {percentage}%")
            self._update_traffic_split(percentage)
            
            print(f"Monitoring for {duration} seconds...")
            if not self._monitor_canary(duration):
                print("Canary validation failed, rolling back")
                self.rollback()
                return False
        
        print("Canary validation successful, promoting to stable")
        self.promote_canary()
        return True
    
    def _monitor_canary(self, duration):
        """Monitor canary metrics"""
        start_time = time.time()
        
        while time.time() - start_time < duration:
            # Check error rate
            error_rate = self._get_error_rate()
            if error_rate > 0.01:  # 1% threshold
                print(f"Error rate {error_rate*100:.2f}% exceeds threshold")
                return False
            
            # Check latency
            p95_latency = self._get_p95_latency()
            if p95_latency > 500:  # 500ms threshold
                print(f"P95 latency {p95_latency}ms exceeds threshold")
                return False
            
            # Compare canary vs stable
            canary_error_rate = self._get_error_rate(version="canary")
            stable_error_rate = self._get_error_rate(version="stable")
            
            if canary_error_rate > stable_error_rate * 1.5:
                print(f"Canary error rate {canary_error_rate*100:.2f}% is 50% worse than stable")
                return False
            
            time.sleep(30)  # Check every 30 seconds
        
        return True
    
    def _get_error_rate(self, version=None):
        """Get error rate from Prometheus"""
        version_label = f',version="{version}"' if version else ''
        query = f'''
            sum(rate(http_requests_total{{
                service="{self.service_name}",
                status=~"5.."{version_label}
            }}[5m])) /
            sum(rate(http_requests_total{{
                service="{self.service_name}"{version_label}
            }}[5m]))
        '''
        result = self.prometheus.custom_query(query)
        return float(result[0]['value'][1]) if result else 0.0
    
    def _get_p95_latency(self, version=None):
        """Get P95 latency from Prometheus"""
        version_label = f',version="{version}"' if version else ''
        query = f'''
            histogram_quantile(0.95,
                sum(rate(http_request_duration_seconds_bucket{{
                    service="{self.service_name}"{version_label}
                }}[5m])) by (le)
            ) * 1000
        '''
        result = self.prometheus.custom_query(query)
        return float(result[0]['value'][1]) if result else 0.0
    
    def _update_traffic_split(self, canary_percentage):
        """Update Istio VirtualService traffic split"""
        # This would use Istio API or kubectl to update VirtualService
        stable_percentage = 100 - canary_percentage
        
        virtual_service = {
            "apiVersion": "networking.istio.io/v1beta1",
            "kind": "VirtualService",
            "metadata": {"name": self.service_name},
            "spec": {
                "hosts": [self.service_name],
                "http": [{
                    "route": [
                        {
                            "destination": {
                                "host": self.service_name,
                                "subset": "stable"
                            },
                            "weight": stable_percentage
                        },
                        {
                            "destination": {
                                "host": self.service_name,
                                "subset": "canary"
                            },
                            "weight": canary_percentage
                        }
                    ]
                }]
            }
        }
        # Apply using kubectl or Istio API
        
    def promote_canary(self):
        """Promote canary to stable"""
        print("Promoting canary to stable")
        
        # Update stable deployment to canary version
        deployment = self.k8s_apps.read_namespaced_deployment(
            name=self.service_name,
            namespace=self.namespace
        )
        deployment.spec.template.metadata.labels['version'] = self.new_version
        deployment.spec.template.spec.containers[0].image = f"{self.service_name}:{self.new_version}"
        
        self.k8s_apps.patch_namespaced_deployment(
            name=self.service_name,
            namespace=self.namespace,
            body=deployment
        )
        
        # Wait for rollout
        self._wait_for_ready(self.service_name)
        
        # Delete canary deployment
        self.k8s_apps.delete_namespaced_deployment(
            name=f"{self.service_name}-canary",
            namespace=self.namespace
        )
        
        # Reset traffic to 100% stable
        self._update_traffic_split(0)
        
    def rollback(self):
        """Rollback canary deployment"""
        print("Rolling back canary deployment")
        
        # Set traffic to 0% canary
        self._update_traffic_split(0)
        
        # Delete canary deployment
        self.k8s_apps.delete_namespaced_deployment(
            name=f"{self.service_name}-canary",
            namespace=self.namespace
        )
        
        print("Rollback complete")

# Usage
if __name__ == "__main__":
    deployment = CanaryDeployment(
        service_name="product-service",
        new_version="v1.2.4"
    )
    
    deployment.deploy_canary()
    success = deployment.progressive_rollout()
    
    if success:
        print("Deployment successful")
    else:
        print("Deployment failed and rolled back")
```

3. **Monitoring Dashboard**:
```yaml
# Grafana dashboard configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: canary-dashboard
data:
  dashboard.json: |
    {
      "dashboard": {
        "title": "Canary Deployment - Product Service",
        "panels": [
          {
            "title": "Traffic Distribution",
            "targets": [
              {
                "expr": "sum(rate(http_requests_total{service='product-service'}[5m])) by (version)"
              }
            ]
          },
          {
            "title": "Error Rate Comparison",
            "targets": [
              {
                "expr": "sum(rate(http_requests_total{service='product-service',status=~'5..'}[5m])) by (version) / sum(rate(http_requests_total{service='product-service'}[5m])) by (version)"
              }
            ]
          },
          {
            "title": "Latency Comparison (P95)",
            "targets": [
              {
                "expr": "histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket{service='product-service'}[5m])) by (le, version))"
              }
            ]
          },
          {
            "title": "Success Rate",
            "targets": [
              {
                "expr": "sum(rate(http_requests_total{service='product-service',status=~'2..'}[5m])) by (version) / sum(rate(http_requests_total{service='product-service'}[5m])) by (version)"
              }
            ]
          }
        ]
      }
    }
```

**Results**:
- Deployment frequency increased from weekly to 3-5 times per week
- Zero revenue-impacting outages since implementation
- Issues detected and rolled back automatically before affecting >10% of users
- Average rollback time: 2 minutes
- Deployment confidence increased significantly
- A/B testing enabled for new features

**Key Success Factors**:
- Automated canary progression based on metrics
- Comprehensive monitoring and alerting
- Fast automated rollback
- Istio provided sophisticated traffic management
- Clear rollback criteria and thresholds

---

### Example 2: Banking Application - Blue/Green Deployment

**Context**:
A core banking application serving 2 million customers requires quarterly releases with strict regulatory compliance. The application is a monolithic Java application running on VMs with Oracle database. Current deployment process requires 4-hour maintenance window with complete system downtime. Regulators require ability to instantly rollback to previous version if issues arise. The bank has budget for duplicate infrastructure.

**Requirements**:
- Reduce deployment downtime from 4 hours to < 5 minutes
- Instant rollback capability (< 1 minute)
- Complete audit trail of all deployments
- Ability to validate deployment before user traffic
- Database backward compatibility
- Maintain compliance with banking regulations

**Solution Design**:

**Selected Strategy**: Blue/Green Deployment with Database Versioning

**Rationale**:
- Blue/green provides instant rollback via load balancer switch
- Can validate green environment before cutover
- Meets regulatory requirements for instant rollback
- Budget allows for duplicate infrastructure
- Reduces downtime to seconds

**Implementation**:

1. **Infrastructure Setup**:
```hcl
# Terraform configuration for blue/green infrastructure
resource "aws_instance" "blue" {
  count         = 3
  ami           = var.app_ami_blue
  instance_type = "m5.2xlarge"
  
  tags = {
    Name        = "banking-app-blue-${count.index}"
    Environment = "blue"
    Version     = var.blue_version
  }
}

resource "aws_instance" "green" {
  count         = 3
  ami           = var.app_ami_green
  instance_type = "m5.2xlarge"
  
  tags = {
    Name        = "banking-app-green-${count.index}"
    Environment = "green"
    Version     = var.green_version
  }
}

resource "aws_lb_target_group" "blue" {
  name     = "banking-app-blue"
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
  name     = "banking-app-green"
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

2. **Database Migration Strategy**:
```sql
-- Backward-compatible database migration approach

-- Phase 1: Add new columns (compatible with both versions)
ALTER TABLE accounts ADD COLUMN account_type_v2 VARCHAR(50);
ALTER TABLE accounts ADD COLUMN metadata_json CLOB;

-- Create view for backward compatibility
CREATE OR REPLACE VIEW accounts_v1 AS
SELECT 
    account_id,
    customer_id,
    account_number,
    balance,
    COALESCE(account_type_v2, account_type) as account_type,
    created_date,
    modified_date
FROM accounts;

-- Phase 2: Migrate data (can run while both versions active)
UPDATE accounts 
SET account_type_v2 = account_type,
    metadata_json = JSON_OBJECT(
        'legacy_type', account_type,
        'migration_date', SYSDATE
    )
WHERE account_type_v2 IS NULL;

-- Phase 3: After green is stable, drop old columns (next release)
-- ALTER TABLE accounts DROP COLUMN account_type;
```

3. **Deployment Automation**:
```python
# blue_green_deployment.py
import boto3
import time
import logging
from datetime import datetime

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class BlueGreenDeployment:
    def __init__(self, region='us-east-1'):
        self.ec2 = boto3.client('ec2', region_name=region)
        self.elb = boto3.client('elbv2', region_name=region)
        self.rds = boto3.client('rds', region_name=region)
        self.cloudwatch = boto3.client('cloudwatch', region_name=region)
        
    def get_active_environment(self):
        """Determine which environment is currently active"""
        # Check load balancer target group
        response = self.elb.describe_listeners(
            ListenerArns=['arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/banking-app/50dc6c495c0c9188/f2f7dc8efc522ab2']
        )
        
        target_group_arn = response['Listeners'][0]['DefaultActions'][0]['TargetGroupArn']
        
        if 'blue' in target_group_arn:
            return 'blue', 'green'
        else:
            return 'green', 'blue'
    
    def deploy_to_inactive(self, new_version):
        """Deploy new version to inactive environment"""
        active, inactive = self.get_active_environment()
        logger.info(f"Active: {active}, Deploying to: {inactive}")
        
        # Get inactive environment instances
        instances = self._get_environment_instances(inactive)
        
        # Deploy to each instance
        for instance in instances:
            logger.info(f"Deploying {new_version} to {instance['InstanceId']}")
            self._deploy_to_instance(instance['InstanceId'], new_version)
        
        # Wait for all instances to be healthy
        logger.info("Waiting for instances to be healthy...")
        self._wait_for_healthy_instances(inactive)
        
        logger.info(f"Deployment to {inactive} complete")
        return inactive
    
    def validate_deployment(self, environment):
        """Validate deployment in inactive environment"""
        logger.info(f"Validating {environment} environment")
        
        # Run smoke tests
        if not self._run_smoke_tests(environment):
            logger.error("Smoke tests failed")
            return False
        
        # Validate database connectivity
        if not self._validate_database(environment):
            logger.error("Database validation failed")
            return False
        
        # Check application health
        if not self._check_application_health(environment):
            logger.error("Application health check failed")
            return False
        
        # Run integration tests
        if not self._run_integration_tests(environment):
            logger.error("Integration tests failed")
            return False
        
        logger.info(f"{environment} validation successful")
        return True
    
    def cutover(self, new_active):
        """Switch load balancer to new environment"""
        logger.info(f"Cutting over to {new_active}")
        
        # Record cutover event for audit
        self._record_audit_event('CUTOVER_STARTED', new_active)
        
        # Get target group ARN for new active
        target_groups = self.elb.describe_target_groups(
            Names=[f'banking-app-{new_active}']
        )
        new_target_group_arn = target_groups['TargetGroups'][0]['TargetGroupArn']
        
        # Update listener to point to new target group
        self.elb.modify_listener(
            ListenerArn='arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/banking-app/50dc6c495c0c9188/f2f7dc8efc522ab2',
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': new_target_group_arn
                }
            ]
        )
        
        logger.info("Load balancer updated")
        
        # Record cutover completion
        self._record_audit_event('CUTOVER_COMPLETED', new_active)
        
        # Monitor for 15 minutes post-cutover
        logger.info("Monitoring post-cutover...")
        if not self._monitor_post_cutover(new_active, duration=900):
            logger.error("Post-cutover monitoring detected issues")
            return False
        
        logger.info("Cutover successful")
        return True
    
    def rollback(self):
        """Instant rollback to previous environment"""
        logger.error("ROLLBACK INITIATED")
        
        active, inactive = self.get_active_environment()
        
        # Record rollback event
        self._record_audit_event('ROLLBACK_STARTED', inactive)
        
        # Switch back to previous environment
        target_groups = self.elb.describe_target_groups(
            Names=[f'banking-app-{inactive}']
        )
        rollback_target_group_arn = target_groups['TargetGroups'][0]['TargetGroupArn']
        
        self.elb.modify_listener(
            ListenerArn='arn:aws:elasticloadbalancing:us-east-1:123456789012:listener/app/banking-app/50dc6c495c0c9188/f2f7dc8efc522ab2',
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': rollback_target_group_arn
                }
            ]
        )
        
        logger.info(f"Rolled back to {inactive}")
        
        # Record rollback completion
        self._record_audit_event('ROLLBACK_COMPLETED', inactive)
        
        return True
    
    def _monitor_post_cutover(self, environment, duration):
        """Monitor metrics after cutover"""
        start_time = time.time()
        
        while time.time() - start_time < duration:
            # Check error rate
            error_rate = self._get_error_rate(environment)
            if error_rate > 0.001:  # 0.1% threshold
                logger.error(f"Error rate {error_rate*100:.3f}% exceeds threshold")
                return False
            
            # Check response time
            response_time = self._get_avg_response_time(environment)
            if response_time > 1000:  # 1 second threshold
                logger.error(f"Response time {response_time}ms exceeds threshold")
                return False
            
            # Check database connections
            db_connections = self._get_db_connections(environment)
            if db_connections > 80:  # 80% of pool
                logger.error(f"Database connections {db_connections}% of pool")
                return False
            
            logger.info(f"Monitoring: Error rate {error_rate*100:.3f}%, Response time {response_time}ms")
            time.sleep(60)
        
        return True
    
    def _record_audit_event(self, event_type, environment):
        """Record deployment event for compliance audit"""
        # This would write to audit log database
        audit_record = {
            'timestamp': datetime.utcnow().isoformat(),
            'event_type': event_type,
            'environment': environment,
            'user': 'deployment-system',
            'source_ip': 'ci-cd-pipeline'
        }
        logger.info(f"Audit: {audit_record}")
        # Write to audit database

# Deployment execution
if __name__ == "__main__":
    deployment = BlueGreenDeployment()
    
    # Deploy to inactive environment
    inactive_env = deployment.deploy_to_inactive(new_version="v2.5.0")
    
    # Validate deployment
    if not deployment.validate_deployment(inactive_env):
        logger.error("Validation failed, aborting deployment")
        exit(1)
    
    # Manual approval gate
    approval = input("Validation successful. Approve cutover? (yes/no): ")
    if approval.lower() != 'yes':
        logger.info("Deployment cancelled by operator")
        exit(0)
    
    # Cutover to new environment
    if not deployment.cutover(inactive_env):
        logger.error("Cutover monitoring failed, rolling back")
        deployment.rollback()
        exit(1)
    
    logger.info("Deployment completed successfully")
```

**Results**:
- Deployment downtime reduced from 4 hours to 30 seconds
- Rollback time: < 30 seconds (load balancer switch)
- Zero failed deployments since implementation
- Complete audit trail maintained for compliance
- Deployment confidence increased significantly
- Ability to validate before user impact

**Key Success Factors**:
- Duplicate infrastructure enabled instant cutover
- Backward-compatible database migrations
- Comprehensive validation before cutover
- Automated audit logging for compliance
- Clear rollback procedures

---

### Example 3: Mobile API - Rolling Deployment with Health Checks

**Context**:
A mobile application backend API serving 500,000 mobile users needs frequent deployments (daily) with minimal infrastructure costs. The API runs on Kubernetes with 20 replicas across 3 availability zones. Current deployment uses basic rolling update but has caused API errors during deployments due to insufficient health checking. Budget constraints prevent duplicate infrastructure for blue/green.

**Requirements**:
- Deploy daily without user-facing errors
- Minimize infrastructure costs (no duplicate environments)
- Automatic rollback on health check failures
- Support for database schema changes
- Maintain 99.9% availability
- Handle traffic spikes during deployment

**Solution Design**:

**Selected Strategy**: Rolling Deployment with Sophisticated Health Checks + PodDisruptionBudget

**Rationale**:
- Rolling deployment minimizes infrastructure costs
- Kubernetes provides built-in rolling update support
- Sophisticated health checks prevent traffic to unhealthy pods
- PodDisruptionBudget ensures minimum availability
- Automatic rollback on failure

**Implementation**:

1. **Kubernetes Deployment with Health Checks**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mobile-api
  labels:
    app: mobile-api
spec:
  replicas: 20
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%        # Allow 25% extra pods during rollout
      maxUnavailable: 10%  # Maximum 10% pods unavailable
  selector:
    matchLabels:
      app: mobile-api
  template:
    metadata:
      labels:
        app: mobile-api
        version: v1.5.0
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9090"
    spec:
      containers:
        - name: api
          image: mobile-api:v1.5.0
          ports:
            - containerPort: 8080
              name: http
            - containerPort: 9090
              name: metrics
          env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: api-secrets
                  key: database-url
          resources:
            requests:
              cpu: 500m
              memory: 512Mi
            limits:
              cpu: 1000m
              memory: 1Gi
          
          # Startup probe - gives app time to start
          startupProbe:
            httpGet:
              path: /health/startup
              port: 8080
            failureThreshold: 30
            periodSeconds: 10
            # Allows up to 5 minutes for startup
          
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
          
          # Graceful shutdown
          lifecycle:
            preStop:
              exec:
                command: ["/bin/sh", "-c", "sleep 15"]
      
      terminationGracePeriodSeconds: 30
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: mobile-api-pdb
spec:
  minAvailable: 18  # Ensure at least 18 of 20 pods available
  selector:
    matchLabels:
      app: mobile-api
```

2. **Comprehensive Health Check Implementation**:
```javascript
// health-checks.js
const express = require('express');
const router = express.Router();
const db = require('./database');
const redis = require('./redis');

// Startup probe - checks if application has started
router.get('/health/startup', async (req, res) => {
    try {
        // Check if application has completed initialization
        if (!global.appInitialized) {
            return res.status(503).json({
                status: 'starting',
                message: 'Application is still initializing'
            });
        }
        
        res.status(200).json({
            status: 'started',
            timestamp: new Date().toISOString()
        });
    } catch (error) {
        res.status(503).json({
            status: 'error',
            error: error.message
        });
    }
});

// Liveness probe - checks if application is alive
router.get('/health/live', async (req, res) => {
    try {
        // Basic liveness check - is the process responsive?
        const memoryUsage = process.memoryUsage();
        const heapUsedPercent = (memoryUsage.heapUsed / memoryUsage.heapTotal) * 100;
        
        // Fail if memory usage is critically high (might indicate memory leak)
        if (heapUsedPercent > 95) {
            return res.status(503).json({
                status: 'unhealthy',
                reason: 'memory_critical',
                heapUsedPercent: heapUsedPercent.toFixed(2)
            });
        }
        
        res.status(200).json({
            status: 'alive',
            uptime: process.uptime(),
            memory: {
                heapUsedPercent: heapUsedPercent.toFixed(2),
                rss: (memoryUsage.rss / 1024 / 1024).toFixed(2) + ' MB'
            }
        });
    } catch (error) {
        res.status(503).json({
            status: 'error',
            error: error.message
        });
    }
});

// Readiness probe - checks if application can serve traffic
router.get('/health/ready', async (req, res) => {
    const checks = [];
    let allHealthy = true;
    
    try {
        // Check database connectivity
        const dbStart = Date.now();
        try {
            await db.query('SELECT 1');
            checks.push({
                name: 'database',
                status: 'healthy',
                responseTime: Date.now() - dbStart
            });
        } catch (error) {
            allHealthy = false;
            checks.push({
                name: 'database',
                status: 'unhealthy',
                error: error.message
            });
        }
        
        // Check Redis connectivity
        const redisStart = Date.now();
        try {
            await redis.ping();
            checks.push({
                name: 'redis',
                status: 'healthy',
                responseTime: Date.now() - redisStart
            });
        } catch (error) {
            allHealthy = false;
            checks.push({
                name: 'redis',
                status: 'unhealthy',
                error: error.message
            });
        }
        
        // Check if accepting new connections
        const activeConnections = global.activeConnections || 0;
        const maxConnections = process.env.MAX_CONNECTIONS || 1000;
        
        if (activeConnections >= maxConnections) {
            allHealthy = false;
            checks.push({
                name: 'connections',
                status: 'unhealthy',
                reason: 'max_connections_reached',
                active: activeConnections,
                max: maxConnections
            });
        } else {
            checks.push({
                name: 'connections',
                status: 'healthy',
                active: activeConnections,
                max: maxConnections
            });
        }
        
        // Check if graceful shutdown in progress
        if (global.shuttingDown) {
            allHealthy = false;
            checks.push({
                name: 'shutdown',
                status: 'shutting_down'
            });
        }
        
        const status = allHealthy ? 200 : 503;
        res.status(status).json({
            status: allHealthy ? 'ready' : 'not_ready',
            checks: checks,
            timestamp: new Date().toISOString()
        });
        
    } catch (error) {
        res.status(503).json({
            status: 'error',
            error: error.message
        });
    }
});

// Graceful shutdown handler
process.on('SIGTERM', () => {
    console.log('SIGTERM received, starting graceful shutdown');
    global.shuttingDown = true;
    
    // Stop accepting new connections
    server.close(() => {
        console.log('Server closed, existing connections completed');
        
        // Close database connections
        db.close();
        redis.quit();
        
        process.exit(0);
    });
    
    // Force shutdown after 25 seconds (before terminationGracePeriodSeconds)
    setTimeout(() => {
        console.error('Forced shutdown after timeout');
        process.exit(1);
    }, 25000);
});

module.exports = router;
```

3. **Deployment Automation with Validation**:
```bash
#!/bin/bash
# deploy.sh - Rolling deployment with validation

set -e

NEW_VERSION=$1
NAMESPACE="production"
DEPLOYMENT="mobile-api"

if [ -z "$NEW_VERSION" ]; then
    echo "Usage: $0 <version>"
    exit 1
fi

echo "Deploying ${DEPLOYMENT} version ${NEW_VERSION}"

# Get current version for rollback
CURRENT_VERSION=$(kubectl get deployment ${DEPLOYMENT} -n ${NAMESPACE} -o jsonpath='{.spec.template.spec.containers[0].image}' | cut -d':' -f2)
echo "Current version: ${CURRENT_VERSION}"

# Update deployment image
kubectl set image deployment/${DEPLOYMENT} \
    api=mobile-api:${NEW_VERSION} \
    -n ${NAMESPACE} \
    --record

echo "Deployment updated, monitoring rollout..."

# Monitor rollout with timeout
if timeout 600 kubectl rollout status deployment/${DEPLOYMENT} -n ${NAMESPACE}; then
    echo "Rollout completed successfully"
else
    echo "ERROR: Rollout timed out or failed"
    echo "Rolling back to ${CURRENT_VERSION}"
    kubectl rollout undo deployment/${DEPLOYMENT} -n ${NAMESPACE}
    kubectl rollout status deployment/${DEPLOYMENT} -n ${NAMESPACE}
    exit 1
fi

# Post-deployment validation
echo "Running post-deployment validation..."

# Get service endpoint
SERVICE_URL=$(kubectl get service ${DEPLOYMENT} -n ${NAMESPACE} -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

# Wait for service to be ready
sleep 30

# Run smoke tests
echo "Running smoke tests..."
if ! curl -f -s "http://${SERVICE_URL}/health/ready" > /dev/null; then
    echo "ERROR: Health check failed"
    echo "Rolling back to ${CURRENT_VERSION}"
    kubectl rollout undo deployment/${DEPLOYMENT} -n ${NAMESPACE}
    kubectl rollout status deployment/${DEPLOYMENT} -n ${NAMESPACE}
    exit 1
fi

# Check error rate for 5 minutes
echo "Monitoring error rate for 5 minutes..."
for i in {1..10}; do
    ERROR_RATE=$(curl -s "http://prometheus:9090/api/v1/query?query=sum(rate(http_requests_total{app='mobile-api',status=~'5..'}[1m]))/sum(rate(http_requests_total{app='mobile-api'}[1m]))" | jq -r '.data.result[0].value[1]')
    
    if (( $(echo "$ERROR_RATE > 0.01" | bc -l) )); then
        echo "ERROR: Error rate ${ERROR_RATE} exceeds threshold 0.01"
        echo "Rolling back to ${CURRENT_VERSION}"
        kubectl rollout undo deployment/${DEPLOYMENT} -n ${NAMESPACE}
        kubectl rollout status deployment/${DEPLOYMENT} -n ${NAMESPACE}
        exit 1
    fi
    
    echo "Check $i/10: Error rate ${ERROR_RATE}"
    sleep 30
done

echo "Deployment validation successful"
echo "Deployment of ${NEW_VERSION} completed successfully"
```

**Results**:
- Daily deployments with zero user-facing errors
- Infrastructure costs minimized (no duplicate environments)
- Automatic rollback on health check failures
- Deployment time: 8-10 minutes
- 99.95% availability maintained (exceeding 99.9% SLA)
- Zero manual rollbacks needed

**Key Success Factors**:
- Comprehensive health checks (startup, liveness, readiness)
- PodDisruptionBudget ensured minimum availability
- Graceful shutdown prevented connection errors
- Automated validation and rollback
- Appropriate resource limits and requests

---

### Example 4: SaaS Platform - Feature Flag Deployment

**Context**:
A B2B SaaS platform with 10,000 enterprise customers needs to deploy new features frequently while minimizing risk. Different customers have different feature requirements and risk tolerances. Some customers want early access to new features, while others prefer stability. The platform needs to support A/B testing, gradual rollouts, and instant feature rollback without redeployment.

**Requirements**:
- Decouple deployment from feature release
- Enable features for specific customers or user segments
- Support A/B testing for new features
- Instant feature rollback without redeployment
- Gradual feature rollout (1% → 10% → 50% → 100%)
- Different feature sets for different customer tiers
- Audit trail of feature flag changes

**Solution Design**:

**Selected Strategy**: Continuous Deployment + Feature Flags (LaunchDarkly)

**Rationale**:
- Feature flags decouple deployment from release
- Can enable features for specific customers
- Supports A/B testing and experimentation
- Instant feature disable without redeployment
- Gradual rollout reduces risk
- Different configurations per customer tier

**Implementation**:

1. **Feature Flag Configuration**:
```javascript
// feature-flags.js
const LaunchDarkly = require('launchdarkly-node-server-sdk');

class FeatureFlagService {
    constructor() {
        this.client = LaunchDarkly.init(process.env.LAUNCHDARKLY_SDK_KEY);
    }
    
    async initialize() {
        await this.client.waitForInitialization();
        console.log('LaunchDarkly initialized');
    }
    
    async isFeatureEnabled(featureKey, user) {
        try {
            const context = {
                kind: 'user',
                key: user.id,
                email: user.email,
                custom: {
                    customerId: user.customerId,
                    customerTier: user.customerTier,
                    signupDate: user.signupDate,
                    region: user.region
                }
            };
            
            return await this.client.variation(featureKey, context, false);
        } catch (error) {
            console.error(`Error checking feature flag ${featureKey}:`, error);
            return false; // Fail closed
        }
    }
    
    async getFeatureVariation(featureKey, user, defaultValue) {
        try {
            const context = {
                kind: 'user',
                key: user.id,
                email: user.email,
                custom: {
                    customerId: user.customerId,
                    customerTier: user.customerTier
                }
            };
            
            return await this.client.variation(featureKey, context, defaultValue);
        } catch (error) {
            console.error(`Error getting feature variation ${featureKey}:`, error);
            return defaultValue;
        }
    }
    
    async trackFeatureUsage(featureKey, user, metricValue = 1) {
        try {
            const context = {
                kind: 'user',
                key: user.id
            };
            
            this.client.track(featureKey, context, null, metricValue);
        } catch (error) {
            console.error(`Error tracking feature usage ${featureKey}:`, error);
        }
    }
    
    close() {
        this.client.close();
    }
}

module.exports = new FeatureFlagService();
```

2. **Feature Implementation with Flags**:
```javascript
// Example: New analytics dashboard feature
const express = require('express');
const router = express.Router();
const featureFlags = require('./feature-flags');

router.get('/dashboard', async (req, res) => {
    const user = req.user;
    
    // Check if new analytics dashboard is enabled for this user
    const useNewDashboard = await featureFlags.isFeatureEnabled(
        'new-analytics-dashboard',
        user
    );
    
    if (useNewDashboard) {
        // Track feature usage for analytics
        await featureFlags.trackFeatureUsage('new-analytics-dashboard', user);
        
        // Render new dashboard
        return res.render('dashboard-v2', {
            user: user,
            features: await getV2Features(user)
        });
    } else {
        // Render old dashboard
        return res.render('dashboard-v1', {
            user: user,
            features: await getV1Features(user)
        });
    }
});

// Example: A/B test for pricing page
router.get('/pricing', async (req, res) => {
    const user = req.user || { id: req.sessionID };
    
    // Get pricing page variation (A or B)
    const pricingVariation = await featureFlags.getFeatureVariation(
        'pricing-page-test',
        user,
        'control'
    );
    
    // Track which variation user sees
    await featureFlags.trackFeatureUsage(`pricing-${pricingVariation}`, user);
    
    if (pricingVariation === 'treatment') {
        return res.render('pricing-new', {
            variation: 'treatment',
            prices: getNewPricing()
        });
    } else {
        return res.render('pricing-current', {
            variation: 'control',
            prices: getCurrentPricing()
        });
    }
});

module.exports = router;
```

3. **LaunchDarkly Flag Configuration**:
```json
{
  "name": "new-analytics-dashboard",
  "key": "new-analytics-dashboard",
  "description": "New analytics dashboard with real-time metrics",
  "kind": "boolean",
  "variations": [
    {
      "value": false,
      "name": "Old Dashboard",
      "description": "Current analytics dashboard"
    },
    {
      "value": true,
      "name": "New Dashboard",
      "description": "New real-time analytics dashboard"
    }
  ],
  "temporary": true,
  "tags": ["analytics", "dashboard", "gradual-rollout"],
  "environments": {
    "production": {
      "on": true,
      "rules": [
        {
          "variation": 1,
          "description": "Enterprise tier early access",
          "clauses": [
            {
              "attribute": "customerTier",
              "op": "in",
              "values": ["enterprise"]
            }
          ]
        },
        {
          "variation": 1,
          "description": "Beta testers",
          "clauses": [
            {
              "attribute": "email",
              "op": "endsWith",
              "values": ["@company.com"]
            }
          ]
        }
      ],
      "fallthrough": {
        "rollout": {
          "variations": [
            {
              "variation": 0,
              "weight": 90000
            },
            {
              "variation": 1,
              "weight": 10000
            }
          ]
        }
      },
      "offVariation": 0
    }
  }
}
```

4. **Gradual Rollout Automation**:
```python
# rollout_automation.py
import ldclient
from ldclient.config import Config
import time
import requests

class FeatureRollout:
    def __init__(self, api_key, project_key, environment):
        self.api_key = api_key
        self.project_key = project_key
        self.environment = environment
        self.base_url = "https://app.launchdarkly.com/api/v2"
        self.headers = {
            "Authorization": api_key,
            "Content-Type": "application/json"
        }
    
    def gradual_rollout(self, flag_key, stages):
        """
        Gradually roll out feature flag
        
        stages: List of dicts with 'percentage' and 'duration' keys
        Example: [{"percentage": 10, "duration": 3600}, ...]
        """
        print(f"Starting gradual rollout for {flag_key}")
        
        for stage in stages:
            percentage = stage["percentage"]
            duration = stage["duration"]
            
            print(f"Setting rollout to {percentage}%")
            self._update_rollout_percentage(flag_key, percentage)
            
            print(f"Monitoring for {duration} seconds...")
            if not self._monitor_metrics(flag_key, duration):
                print("Metrics indicate issues, rolling back")
                self._update_rollout_percentage(flag_key, 0)
                return False
        
        print(f"Gradual rollout of {flag_key} completed successfully")
        return True
    
    def _update_rollout_percentage(self, flag_key, percentage):
        """Update feature flag rollout percentage"""
        url = f"{self.base_url}/flags/{self.project_key}/{flag_key}"
        
        # Get current flag configuration
        response = requests.get(url, headers=self.headers)
        flag_config = response.json()
        
        # Update rollout percentage
        patch = [
            {
                "op": "replace",
                "path": f"/environments/{self.environment}/fallthrough/rollout/variations/1/weight",
                "value": percentage * 1000  # LaunchDarkly uses basis points
            },
            {
                "op": "replace",
                "path": f"/environments/{self.environment}/fallthrough/rollout/variations/0/weight",
                "value": (100 - percentage) * 1000
            }
        ]
        
        response = requests.patch(
            url,
            headers=self.headers,
            json=patch
        )
        
        if response.status_code == 200:
            print(f"Updated {flag_key} rollout to {percentage}%")
        else:
            print(f"Failed to update rollout: {response.text}")
    
    def _monitor_metrics(self, flag_key, duration):
        """Monitor metrics during rollout stage"""
        start_time = time.time()
        
        while time.time() - start_time < duration:
            # Get metrics from monitoring system
            metrics = self._get_feature_metrics(flag_key)
            
            # Check error rate
            if metrics.get('error_rate', 0) > 0.02:  # 2% threshold
                print(f"Error rate {metrics['error_rate']*100:.2f}% exceeds threshold")
                return False
            
            # Check performance
            if metrics.get('p95_latency', 0) > 1000:  # 1 second threshold
                print(f"P95 latency {metrics['p95_latency']}ms exceeds threshold")
                return False
            
            # Check user satisfaction (if available)
            if metrics.get('satisfaction_score', 100) < 80:
                print(f"Satisfaction score {metrics['satisfaction_score']} below threshold")
                return False
            
            time.sleep(60)  # Check every minute
        
        return True
    
    def _get_feature_metrics(self, flag_key):
        """Get metrics for feature from monitoring system"""
        # This would query your monitoring system (Datadog, New Relic, etc.)
        # For example:
        response = requests.get(
            "https://api.datadoghq.com/api/v1/query",
            params={
                "query": f"avg:feature.error_rate{{flag:{flag_key}}}",
                "from": int(time.time() - 300),
                "to": int(time.time())
            },
            headers={"DD-API-KEY": "your-datadog-api-key"}
        )
        
        # Parse and return metrics
        return {
            "error_rate": 0.005,  # Example
            "p95_latency": 250,   # Example
            "satisfaction_score": 92  # Example
        }

# Usage
if __name__ == "__main__":
    rollout = FeatureRollout(
        api_key="your-launchdarkly-api-key",
        project_key="your-project",
        environment="production"
    )
    
    # Define rollout stages
    stages = [
        {"percentage": 1, "duration": 1800},    # 1% for 30 minutes
        {"percentage": 10, "duration": 3600},   # 10% for 1 hour
        {"percentage": 25, "duration": 3600},   # 25% for 1 hour
        {"percentage": 50, "duration": 7200},   # 50% for 2 hours
        {"percentage": 100, "duration": 3600},  # 100% for 1 hour
    ]
    
    success = rollout.gradual_rollout(
        flag_key="new-analytics-dashboard",
        stages=stages
    )
    
    if success:
        print("Feature rollout completed successfully")
    else:
        print("Feature rollout failed and was rolled back")
```

**Results**:
- Deployment frequency: Multiple times per day
- Feature release frequency: Independent of deployments
- Zero production incidents from new features
- Instant feature rollback (< 1 second)
- Successful A/B testing program
- Different feature sets per customer tier
- Complete audit trail of feature changes

**Key Success Factors**:
- Feature flags decoupled deployment from release
- Gradual rollout minimized risk
- Automated monitoring and rollback
- Customer segmentation enabled targeted rollouts
- A/B testing provided data-driven decisions

---

## Comparison Matrix

| Example | Strategy | Infrastructure Cost | Rollback Time | Complexity | Best For |
|---------|----------|---------------------|---------------|------------|----------|
| 1. E-Commerce | Canary + Istio | Medium | 2 minutes | High | High-traffic microservices |
| 2. Banking | Blue/Green | High (2x) | 30 seconds | Medium | Mission-critical, compliance |
| 3. Mobile API | Rolling | Low | 8-10 minutes | Low | Cost-sensitive, frequent deploys |
| 4. SaaS | Feature Flags | Low | < 1 second | Medium | B2B, experimentation |

## Related Skills

- **CI/CD Design**: Provides pipeline for automated deployments
- **Infrastructure as Code**: Provisions infrastructure for deployment strategies
- **Monitoring and Observability**: Validates deployment success and detects issues
- **Container Orchestration**: Enables sophisticated deployment strategies (Kubernetes)
- **Load Balancing**: Routes traffic for blue/green and canary deployments
- **Database Migration**: Handles schema changes during deployments
- **Incident Response**: Handles deployment failures and rollbacks
- **Capacity Planning**: Ensures sufficient resources for deployment strategies
- **Performance Testing**: Validates deployment performance
- **Feature Flag Management**: Implements feature flag strategies

## Skill Composition

### Prerequisites

- **Application Architecture Understanding**: Knowledge of application structure and dependencies
- **Infrastructure Basics**: Understanding of load balancers, networking, compute resources
- **Deployment Fundamentals**: Basic deployment concepts and procedures
- **Monitoring Basics**: Understanding of metrics, logs, and alerts

### Complementary Skills

- **Kubernetes**: Container orchestration for advanced deployment strategies
- **Service Mesh**: Traffic management for canary and blue/green deployments
- **Scripting**: Automation of deployment procedures
- **Database Management**: Schema migration and backward compatibility
- **Cloud Platforms**: AWS, Azure, GCP deployment services

### Advanced Combinations

- **Canary + Feature Flags**: Separate deployment and feature risk
- **Blue/Green + Database Versioning**: Zero-downtime with schema changes
- **Rolling + Progressive Delivery**: Gradual rollout with automated validation
- **Multi-Region Deployment**: Geographic distribution with local failover

## Evaluation Criteria

### Strategy Selection (25%)

- **Alignment with Requirements**: Strategy matches availability, risk, and business needs
- **Infrastructure Compatibility**: Strategy works with available infrastructure
- **Cost Effectiveness**: Strategy is within budget constraints
- **Team Capability**: Team can implement and operate strategy
- **Scalability**: Strategy scales with growth

### Implementation Quality (30%)

- **Automation**: Deployment is fully automated
- **Health Checks**: Comprehensive health and readiness validation
- **Traffic Management**: Traffic routing works correctly
- **Monitoring**: Adequate visibility into deployment health
- **Rollback**: Fast and reliable rollback capability

### Operational Excellence (25%)

- **Deployment Success Rate**: Percentage of successful deployments
- **Rollback Frequency**: How often rollbacks are needed
- **Mean Time to Deploy**: Average deployment duration
- **Mean Time to Rollback**: Average rollback duration
- **Deployment Confidence**: Team confidence in deployment process

### Business Impact (20%)

- **Availability**: Uptime during and after deployments
- **User Impact**: User-facing errors during deployments
- **Deployment Frequency**: Ability to deploy as frequently as needed
- **Time to Market**: Speed of getting features to users
- **Risk Mitigation**: Effectiveness at minimizing deployment risk

### Success Metrics

**Deployment Metrics**:
- Deployment success rate: > 95%
- Deployment frequency: Meets business requirements
- Deployment duration: < 30 minutes
- Rollback time: < 5 minutes
- Rollback frequency: < 10%

**Availability Metrics**:
- Uptime during deployments: Meets SLA
- User-facing errors: < 0.1% during deployments
- Service degradation: Minimal or none

**Business Metrics**:
- Time to market: Reduced by deployment strategy
- Deployment confidence: High team confidence
- Customer impact: Minimal customer complaints
- Revenue impact: Zero revenue loss from deployments