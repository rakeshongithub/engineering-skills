# Reliability Analysis - Step-by-Step Instructions

## Overview

This skill helps you systematically analyze system reliability to identify failure modes and design resilience mechanisms to meet availability requirements.

## Step-by-Step Workflow

### Step 1: Define Reliability Requirements (30-60 minutes)

**Actions:**
1. Establish SLA targets:
   - Availability percentage (99%, 99.9%, 99.99%, etc.)
   - Maximum acceptable error rate
   - Recovery Time Objective (RTO)
   - Recovery Point Objective (RPO)
2. Calculate error budget:
   - 99% = 7.2 hours/month downtime
   - 99.9% = 43 minutes/month downtime
   - 99.99% = 4.3 minutes/month downtime
   - 99.999% = 26 seconds/month downtime
3. Understand business impact:
   - Revenue loss per minute of downtime
   - Customer impact
   - Regulatory implications
4. Define critical user journeys:
   - Must-work features
   - Can-degrade features
   - Acceptable degraded performance

**Outputs:**
- Reliability requirements document
- Error budget calculation
- Critical user journey definitions

### Step 2: Map System Dependencies (1-2 hours)

**Actions:**
1. Identify all components:
   - Frontend (web, mobile, CDN)
   - API layer (load balancers, gateways, servers)
   - Application layer (services, workers)
   - Data layer (databases, caches, queues)
   - Infrastructure (compute, network, DNS)
   - External dependencies (third-party APIs)
2. Document dependency relationships:
   - Create dependency graph
   - Identify critical paths
   - Identify single points of failure
3. Classify dependencies:
   - Hard dependencies (cannot function without)
   - Soft dependencies (can degrade without)
   - Optional dependencies (nice-to-have)
4. Document current availability:
   - Review metrics for past 3-6 months
   - Calculate actual uptime per component
   - Review incident history

**Outputs:**
- Component inventory
- Dependency graph diagram
- Current availability metrics

### Step 3: Identify Failure Modes (2-3 hours)

**Actions:**
1. For each component, brainstorm failure scenarios:

   **Infrastructure failures:**
   - Server/instance failure
   - Network partition or latency
   - Disk failure or storage exhaustion
   - DNS resolution failure
   - Certificate expiration
   - Cloud provider regional outage

   **Application failures:**
   - Application crash or memory leak
   - Deadlock or infinite loop
   - Resource exhaustion
   - Unhandled exceptions
   - Configuration errors
   - Deployment failures

   **Data failures:**
   - Database failure or corruption
   - Cache failure
   - Message queue backlog or loss
   - Data inconsistency
   - Backup failure

   **Dependency failures:**
   - Third-party API downtime
   - Third-party API rate limiting
   - Third-party API latency spike
   - Authentication service failure

   **Human errors:**
   - Bad configuration deployment
   - Accidental data deletion
   - Incorrect scaling decisions

   **External events:**
   - DDoS attacks
   - Traffic spikes
   - Natural disasters

2. Review past incidents:
   - What failures have occurred?
   - What was the impact?
   - How often do they occur?
   - What was the root cause?

3. Document each failure mode:
   - Description
   - Affected components
   - Impact on user experience
   - Current mitigation (if any)

**Outputs:**
- Comprehensive list of failure modes
- Incident history analysis

### Step 4: Assess Risk for Each Failure Mode (1-2 hours)

**Actions:**
1. For each failure mode, assess likelihood:
   - **High**: Monthly or more frequent
   - **Medium**: Quarterly or annually
   - **Low**: Multi-year
   - **Very Low**: Theoretical but unlikely

   Base on:
   - Historical incident data
   - Industry benchmarks
   - Component reliability specs
   - Team experience

2. For each failure mode, assess impact:
   - **Critical**: Complete outage, data loss
   - **High**: Major feature unavailable
   - **Medium**: Minor feature unavailable
   - **Low**: Minimal impact

   Consider:
   - Number of users affected
   - Revenue impact
   - Data loss potential
   - Reputation damage

3. Calculate risk level:
   - Use risk matrix (likelihood × impact)
   - Categorize as Critical/High/Medium/Low risk

4. Prioritize failure modes by risk level

**Outputs:**
- Risk assessment for each failure mode
- Risk matrix visualization
- Prioritized list of risks

### Step 5: Design Mitigation Strategies (3-4 hours)

**Actions:**
1. For each high/critical risk, design mitigation:

   **Redundancy strategies:**
   - Active-active: Multiple instances serving traffic
   - Active-passive: Standby instances ready to take over
   - Multi-region: Deploy across geographic regions
   - Multi-cloud: Use multiple cloud providers

   **Fault tolerance strategies:**
   - Retries: Automatic retry with exponential backoff
   - Circuit breakers: Stop calling failing dependencies
   - Timeouts: Prevent indefinite waiting
   - Bulkheads: Isolate resources
   - Rate limiting: Protect against overload

   **Graceful degradation strategies:**
   - Feature flags: Disable non-critical features
   - Fallbacks: Serve cached or default data
   - Partial responses: Return available data
   - Read-only mode: Allow reads when writes fail

   **Data resilience strategies:**
   - Replication: Sync or async data replication
   - Backups: Regular automated backups
   - Point-in-time recovery: Restore to any point
   - Multi-region replication: Protect against regional failures

2. For each mitigation strategy, document:
   - How it addresses the failure mode
   - Implementation approach
   - Dependencies or prerequisites
   - Effort estimate (Small/Medium/Large)
   - Cost implications
   - Expected impact on availability

3. Consider monitoring and alerting:
   - What metrics to track?
   - What alerts to configure?
   - What runbooks to create?

**Outputs:**
- Mitigation strategy for each high/critical risk
- Implementation approach for each strategy
- Cost and effort estimates

### Step 6: Validate SLA Feasibility (1-2 hours)

**Actions:**
1. Calculate composite availability:

   **For components in series (all must work):**
   ```
   Composite = A1 × A2 × A3 × ... × An
   Example: 99.9% × 99.9% × 99.9% = 99.7%
   ```

   **For components in parallel (any can work):**
   ```
   Composite = 1 - [(1-A1) × (1-A2) × ... × (1-An)]
   Example: 1 - [(1-0.999) × (1-0.999)] = 99.9999%
   ```

2. Calculate current system availability:
   - Map dependency graph
   - Calculate composite availability
   - Compare to target SLA

3. Calculate projected availability with mitigations:
   - Apply mitigation strategies
   - Recalculate composite availability
   - Validate against target SLA

4. Identify gaps:
   - What is the current availability?
   - What is the target availability?
   - What improvements are needed?
   - Which components are weakest links?

5. Validate assumptions:
   - Are component availability numbers realistic?
   - Are calculations correct?
   - Are all dependencies considered?

**Outputs:**
- Current vs. target availability analysis
- Projected availability with mitigations
- Gap analysis
- Weakest link identification

### Step 7: Develop Action Plan (1-2 hours)

**Actions:**
1. For each mitigation strategy, document:
   - **Problem statement**: What failure mode?
   - **Recommendation**: What specific action?
   - **Rationale**: Why this approach?
   - **Alternatives**: What other options?
   - **Effort estimate**: Days/weeks/months
   - **Priority**: Critical/High/Medium/Low
   - **Expected impact**: Availability improvement
   - **Cost implications**: Infrastructure and development costs

2. Group recommendations by timeline:
   - **Immediate (0-3 months)**: Critical risks
   - **Short-term (3-6 months)**: High risks
   - **Long-term (6-12 months)**: Medium risks

3. Identify dependencies:
   - What must be done first?
   - What can be done in parallel?
   - What are the blockers?

4. Create realistic roadmap:
   - Timeline with milestones
   - Resource requirements
   - Decision points

5. Define monitoring and validation:
   - Key metrics to track
   - Alerts to configure
   - Chaos engineering experiments
   - Review cadence

**Outputs:**
- Prioritized action plan
- Timeline and roadmap
- Monitoring and validation strategy

### Step 8: Document and Present Findings (2-3 hours)

**Actions:**
1. Write executive summary (1 page):
   - Current availability vs. target
   - Top 3-5 critical risks
   - Recommended approach
   - Timeline and cost estimate

2. Document detailed analysis:
   - Reliability requirements
   - Dependency mapping
   - Failure mode analysis
   - Risk assessment
   - Mitigation strategies
   - SLA feasibility analysis
   - Action plan

3. Create visual artifacts:
   - Architecture diagram with failure points
   - Dependency graph
   - Risk matrix
   - Availability improvement timeline

4. Prepare presentation for stakeholders:
   - Technical deep-dive for engineering
   - Business-focused summary for leadership
   - Cost-benefit analysis for finance

5. Review with teams:
   - Engineering team: Technical validation
   - Operations team: Operational feasibility
   - Leadership: Business alignment

**Outputs:**
- Complete reliability analysis report
- Architecture diagrams
- Presentation deck

## Tips for Success

- **Use data, not assumptions**: Base analysis on actual incident history and metrics
- **Think holistically**: Consider all types of failures (infrastructure, application, data, human)
- **Be realistic**: Don't over-engineer for five nines if 99.9% is sufficient
- **Test resilience**: Validate that mitigation strategies actually work (chaos engineering)
- **Consider costs**: Balance reliability with cost efficiency
- **Plan for operations**: Include monitoring, alerting, and incident response
- **Involve the team**: Get input from engineering, operations, and business stakeholders
- **Document decisions**: Record why certain approaches were chosen
- **Iterate**: Reliability analysis is not one-time, revisit as system evolves
- **Learn from incidents**: Use post-mortems to improve analysis

## Common Pitfalls to Avoid

- Focusing only on infrastructure failures, ignoring application-level issues
- Making assumptions without reviewing actual incident history
- Over-engineering for unrealistic availability targets
- Ignoring the reliability of external dependencies
- Not testing that resilience mechanisms actually work
- Forgetting to consider human errors and operational issues
- Implementing resilience without proper monitoring and alerting
- Treating reliability analysis as one-time instead of ongoing
- Ignoring cost implications of reliability improvements
- Not having an incident response plan for when failures occur

## Validation Checklist

Before finalizing your analysis:

- [ ] Reliability requirements are based on business needs
- [ ] All components and dependencies are mapped
- [ ] Failure modes are based on incident history and brainstorming
- [ ] Risk assessment uses data, not just intuition
- [ ] Mitigation strategies are specific and actionable
- [ ] SLA feasibility calculations are validated
- [ ] Cost implications are calculated
- [ ] Action plan is realistic and time-bound
- [ ] Monitoring and alerting strategy is defined
- [ ] Analysis is validated with engineering and operations teams
- [ ] Incident response procedures are considered
