# Deployment Strategy Design

## Quick Reference

**Category**: Operations > Deployment  
**Complexity**: Intermediate  
**Estimated Time**: 2-4 hours  
**Priority**: MEDIUM

## Purpose

Choose and design deployment strategies (blue/green, canary, rolling, recreate, feature flags) that minimize risk, enable rapid rollback, and ensure high availability during application releases.

## When to Use This Skill

- Planning deployment approach for new applications
- Improving deployment reliability and reducing outages
- Implementing zero-downtime deployments
- Reducing deployment risk and blast radius
- Supporting progressive rollouts and A/B testing
- Enabling fast rollback capabilities
- Migrating between deployment strategies

## Quick Start

### 1. Analyze Requirements (15-30 min)
- Document application characteristics (architecture, dependencies, state)
- Define availability requirements (SLA, downtime tolerance)
- Assess traffic patterns and user behavior
- Determine risk tolerance and business impact
- Evaluate infrastructure capabilities

### 2. Evaluate Strategies (20-30 min)
- Assess blue/green deployment suitability
- Evaluate canary deployment feasibility
- Consider rolling deployment applicability
- Analyze recreate deployment appropriateness
- Evaluate feature flag integration benefits
- Create comparison matrix

### 3. Select Strategy (10-15 min)
- Review evaluation results
- Consider hybrid approaches
- Document selection rationale
- Define success criteria
- Plan migration path

### 4. Implement (60-120 min)
- Design infrastructure
- Configure traffic management
- Automate deployment
- Set up monitoring
- Design rollback procedures
- Test and validate
- Document and train

## Deployment Strategy Decision Tree

```
Can you afford duplicate infrastructure?
├─ Yes → Can you afford 2x cost?
│  ├─ Yes → Need instant rollback?
│  │  ├─ Yes → Use Blue/Green
│  │  └─ No → Use Canary
│  └─ No → Use Canary or Rolling
└─ No → Is downtime acceptable?
   ├─ Yes → Use Recreate
   └─ No → Use Rolling or Canary

Need to decouple deployment from release?
└─ Yes → Add Feature Flags to any strategy
```

## Strategy Quick Comparison

| Strategy | Downtime | Rollback Speed | Cost | Complexity | Best For |
|----------|----------|----------------|------|------------|----------|
| **Blue/Green** | Zero | Instant (< 1 min) | High (2x) | Medium | Mission-critical, instant rollback |
| **Canary** | Zero | Fast (2-5 min) | Medium | High | Risk mitigation, real traffic testing |
| **Rolling** | Zero | Moderate (5-10 min) | Low | Low | Cost-sensitive, frequent deploys |
| **Recreate** | Yes | Slow (10+ min) | Low | Low | Acceptable downtime, simplicity |
| **Feature Flags** | Zero | Instant (< 1 sec) | Low | Medium | Decouple deploy from release |

## Key Deliverables

### Primary
1. **Deployment Strategy Recommendation** - Selected strategy with justification and risk assessment
2. **Implementation Plan** - Infrastructure requirements, traffic management, automation
3. **Deployment Workflow Documentation** - Step-by-step procedures and checklists
4. **Risk Mitigation Plan** - Identified risks and mitigation strategies
5. **Configuration Specifications** - Load balancer, service mesh, feature flag configs

### Supporting
6. **Architecture Diagrams** - Current state, target state, traffic flow, rollback flow
7. **Monitoring and Metrics Plan** - Key metrics, alerts, dashboards, success criteria
8. **Testing Strategy** - Pre-deployment, canary validation, smoke tests, rollback tests
9. **Runbooks and Procedures** - Deployment runbook, rollback runbook, troubleshooting
10. **Training Materials** - Strategy overview, operator training, developer guidelines, FAQ

## Common Deployment Strategies

### Blue/Green Deployment

**How it works**: Maintain two identical environments (blue and green). Deploy to inactive environment, validate, then switch traffic.

**Pros**:
- Instant rollback (< 1 minute)
- Full validation before cutover
- Zero downtime
- Simple to understand

**Cons**:
- Requires 2x infrastructure (high cost)
- Database migrations can be complex
- Wasted resources when not deploying

**Use when**: Zero downtime critical, can afford 2x cost, need instant rollback

### Canary Deployment

**How it works**: Deploy new version to small subset of instances, gradually increase traffic while monitoring metrics.

**Pros**:
- Gradual risk mitigation
- Test with real production traffic
- Early issue detection
- Minimal infrastructure overhead

**Cons**:
- Requires sophisticated traffic routing
- Need comprehensive monitoring
- More complex to implement
- Mixed versions in production

**Use when**: Want to test with real traffic, have good monitoring, can route traffic dynamically

### Rolling Deployment

**How it works**: Gradually replace instances with new version, updating a few at a time.

**Pros**:
- Minimal infrastructure overhead
- Built-in Kubernetes support
- Automatic rollback on failure
- Simple to implement

**Cons**:
- Mixed versions during deployment
- Slower rollback than blue/green
- Requires version compatibility

**Use when**: Cost-sensitive, have orchestration platform, versions are compatible

### Recreate Deployment

**How it works**: Stop all old instances, then start all new instances.

**Pros**:
- Simplest to implement
- No version compatibility issues
- Lowest infrastructure cost

**Cons**:
- Requires downtime
- Slow rollback
- User impact during deployment

**Use when**: Downtime acceptable, want simplest approach, have maintenance window

### Feature Flags

**How it works**: Deploy code with features disabled, enable features gradually via configuration.

**Pros**:
- Decouple deployment from release
- Instant feature rollback (< 1 second)
- Targeted rollouts (by user, customer, region)
- A/B testing support

**Cons**:
- Code complexity from flags
- Need feature flag infrastructure
- Flag cleanup required

**Use when**: Want to decouple deploy from release, need A/B testing, want instant feature control

## Essential Components

### 1. Health Checks
```yaml
# Kubernetes health checks
startupProbe:   # Gives app time to start
  httpGet:
    path: /health/startup
  failureThreshold: 30
  periodSeconds: 10

livenessProbe:  # Restarts unhealthy containers
  httpGet:
    path: /health/live
  periodSeconds: 10
  failureThreshold: 3

readinessProbe: # Controls traffic routing
  httpGet:
    path: /health/ready
  periodSeconds: 5
  failureThreshold: 3
```

### 2. Traffic Management
```yaml
# Istio canary traffic split
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
spec:
  http:
    - route:
        - destination:
            subset: stable
          weight: 90
        - destination:
            subset: canary
          weight: 10
```

### 3. Monitoring Metrics
- Error rate by version
- Latency (P50, P95, P99) by version
- Request rate by version
- Success rate by version
- Traffic distribution by version

### 4. Rollback Triggers
- Error rate > 1% for 5 minutes → Auto rollback
- P95 latency > 1000ms for 5 minutes → Auto rollback
- Success rate < 99% for 5 minutes → Auto rollback
- Health check failures > 50% → Auto rollback

## Success Metrics

### Deployment Metrics
- **Success Rate**: > 95%
- **Deployment Duration**: < 30 minutes
- **Rollback Time**: < 5 minutes
- **Rollback Frequency**: < 10%

### Availability Metrics
- **Uptime During Deployments**: Meets SLA
- **User-Facing Errors**: < 0.1%
- **Service Degradation**: Minimal or none

### Business Metrics
- **Time to Market**: Reduced by deployment strategy
- **Deployment Confidence**: High team confidence
- **Customer Impact**: Minimal complaints
- **Revenue Impact**: Zero revenue loss

## Common Mistakes to Avoid

1. **Choosing strategy based on hype** - Evaluate objectively against requirements
2. **Ignoring infrastructure constraints** - Assess capabilities before selection
3. **Inadequate health checks** - Implement comprehensive health validation
4. **Missing rollback automation** - Automate rollback procedures
5. **Not defining rollback triggers** - Define clear rollback criteria
6. **Insufficient monitoring** - Implement comprehensive deployment monitoring
7. **Skipping non-production testing** - Test thoroughly before production
8. **Poor communication** - Establish clear communication plan
9. **Canary too small** - Size canary for statistical significance
10. **Not testing rollback** - Test rollback procedures regularly

## Related Skills

- **CI/CD Design** - Provides pipeline for automated deployments
- **Infrastructure as Code** - Provisions infrastructure for deployment strategies
- **Monitoring and Observability** - Validates deployment success
- **Container Orchestration** - Enables sophisticated deployment strategies
- **Load Balancing** - Routes traffic for deployments
- **Database Migration** - Handles schema changes
- **Incident Response** - Handles deployment failures
- **Feature Flag Management** - Implements feature flag strategies

## Resources

### Documentation
- [SKILL.md](./SKILL.md) - Comprehensive skill documentation
- [instructions.md](./instructions.md) - Step-by-step implementation guide
- [examples.md](./examples.md) - Real-world examples and case studies

### External Resources
- [Kubernetes Deployment Strategies](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
- [Istio Traffic Management](https://istio.io/latest/docs/concepts/traffic-management/)
- [LaunchDarkly Feature Flags](https://launchdarkly.com/)
- [Argo Rollouts](https://argoproj.github.io/argo-rollouts/)

## Quick Tips

💡 **Start Simple**: Begin with rolling deployment, graduate to canary as monitoring matures  
💡 **Test Rollback**: Test rollback procedures as rigorously as deployment  
💡 **Monitor Everything**: Comprehensive monitoring is critical for automated rollback  
💡 **Document Clearly**: Clear runbooks prevent mistakes during incidents  
💡 **Train Teams**: Ensure team understands deployment strategy before production use  
💡 **Iterate**: Continuously improve based on feedback and metrics  

## Version History

- **v1.0.0** (2024-01-15) - Initial release

---

**Need Help?** Refer to [SKILL.md](./SKILL.md) for comprehensive documentation or [instructions.md](./instructions.md) for detailed step-by-step guidance.