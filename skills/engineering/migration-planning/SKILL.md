# Migration Planning

## Purpose

Plan technology or platform migrations with minimal risk and downtime.

## When to Use

- When migrating to a new technology stack or framework
- When moving from monolith to microservices
- When migrating to cloud from on-premise
- When upgrading major framework versions with breaking changes
- When migrating databases or data stores
- When changing deployment platforms or infrastructure
- When consolidating or splitting systems
- When replacing legacy systems

## When NOT to Use

- For minor version updates (use standard upgrade process)
- For simple refactoring (use refactoring skill instead)
- For new features (use system-design instead)
- When migration is not yet approved (get approval first)
- For emergency fixes (fix first, plan migration later)

## Inputs

- **current-state**: Existing system architecture, technology, data
- **target-state**: Desired architecture, technology, data model
- **constraints**: Timeline, budget, downtime tolerance, team skills
- **dependencies**: Systems, teams, or processes that depend on the current system

## Expected Outputs

- **migration-plan**: Detailed step-by-step migration plan
- **risk-mitigation**: Identified risks and mitigation strategies
- **rollback-strategy**: Plan to revert if migration fails
- **timeline**: Realistic timeline with milestones

## Workflow

### 1. Understand Current State (2-4 hours)

**Document current system:**
- Architecture and components
- Technology stack and versions
- Data model and volumes
- Integration points
- Performance characteristics
- Known issues and limitations

**Assess dependencies:**
- What systems depend on this?
- What teams use this?
- What processes rely on this?
- What data flows in/out?

**Gather metrics:**
- Traffic patterns and volumes
- Data size and growth rate
- Performance baselines
- Availability and reliability
- Cost (infrastructure, maintenance)

### 2. Define Target State (2-3 hours)

**Define goals:**
- Why are we migrating?
- What problems are we solving?
- What improvements do we expect?
- What are the success criteria?

**Design target architecture:**
- New technology stack
- New architecture patterns
- New data model (if applicable)
- New infrastructure
- New deployment model

**Validate target state:**
- Does it solve the problems?
- Is it technically feasible?
- Does the team have the skills?
- Is it within budget?
- Does it meet requirements?

### 3. Identify Migration Approach (1-2 hours)

**Migration Strategies:**

**Big Bang Migration:**
- Migrate everything at once
- **Pros**: Fast, clean cutover
- **Cons**: High risk, potential for extended downtime
- **When to use**: Small systems, can tolerate downtime

**Phased Migration:**
- Migrate in stages (by feature, module, or user segment)
- **Pros**: Lower risk, can validate each phase
- **Cons**: Longer timeline, need to maintain both systems
- **When to use**: Large systems, can't tolerate downtime

**Strangler Fig Pattern:**
- Gradually replace old system with new
- Route traffic to new system incrementally
- **Pros**: Very low risk, can roll back easily
- **Cons**: Longest timeline, complex routing logic
- **When to use**: Critical systems, zero downtime required

**Parallel Run:**
- Run old and new systems in parallel
- Compare results, switch when confident
- **Pros**: High confidence, easy rollback
- **Cons**: Expensive (running two systems), complex
- **When to use**: Critical systems, need high confidence

**Choose approach based on:**
- System criticality
- Downtime tolerance
- Team capacity
- Timeline constraints
- Budget

### 4. Identify Risks (1-2 hours)

**Common migration risks:**

**Technical Risks:**
- Data loss or corruption
- Performance degradation
- Integration breakage
- Unexpected incompatibilities
- Missing features in new system

**Operational Risks:**
- Extended downtime
- Rollback complexity
- Team lacks skills
- Insufficient testing
- Poor communication

**Business Risks:**
- Customer impact
- Revenue loss
- Compliance violations
- Timeline delays
- Budget overruns

**For each risk:**
- Assess likelihood (Low/Medium/High)
- Assess impact (Low/Medium/High)
- Define mitigation strategy
- Define contingency plan

### 5. Design Migration Steps (3-4 hours)

**Typical migration phases:**

**Phase 1: Preparation**
- Set up new infrastructure
- Install and configure new technology
- Migrate configuration and secrets
- Set up monitoring and logging
- Create migration scripts/tools

**Phase 2: Data Migration**
- Design data migration strategy
- Create data migration scripts
- Test data migration on subset
- Validate data integrity
- Plan for data sync during migration

**Phase 3: Code Migration**
- Migrate or rewrite code
- Update integrations
- Update tests
- Update documentation
- Code review and testing

**Phase 4: Testing**
- Unit testing
- Integration testing
- Performance testing
- Security testing
- User acceptance testing (UAT)

**Phase 5: Deployment**
- Deploy to staging
- Final validation
- Deploy to production
- Monitor closely
- Validate success

**Phase 6: Cleanup**
- Decommission old system
- Clean up temporary migration code
- Update documentation
- Retrospective

### 6. Create Rollback Strategy (1-2 hours)

**Rollback plan must include:**
- Clear rollback triggers (when to roll back)
- Step-by-step rollback procedure
- Data rollback strategy
- Communication plan
- Time limit for rollback decision

**Rollback triggers:**
- Critical bugs in new system
- Performance degradation >20%
- Data integrity issues
- Integration failures
- Availability <99%

**Rollback considerations:**
- Can we roll back data changes?
- How long does rollback take?
- What data might be lost?
- How to communicate rollback?

### 7. Plan Testing Strategy (2-3 hours)

**Testing levels:**

**Unit Testing:**
- Test migrated code
- Test migration scripts
- Test data transformations

**Integration Testing:**
- Test all integrations
- Test data flows
- Test APIs

**Performance Testing:**
- Load testing
- Stress testing
- Compare to baseline

**Data Validation:**
- Compare data before/after
- Validate data integrity
- Check for data loss

**User Acceptance Testing:**
- Test critical workflows
- Involve actual users
- Validate business requirements

**Dry Runs:**
- Practice migration in staging
- Time each step
- Identify issues
- Refine process

### 8. Create Timeline and Milestones (1-2 hours)

**Estimate timeline:**
- Break down into tasks
- Estimate effort for each task
- Identify dependencies
- Add buffer (20-30%)
- Define milestones

**Example timeline:**
```
Week 1-2: Preparation
  - Set up infrastructure
  - Create migration scripts
  - Milestone: Infrastructure ready

Week 3-4: Data Migration Testing
  - Test data migration
  - Validate data integrity
  - Milestone: Data migration validated

Week 5-6: Code Migration
  - Migrate code
  - Update integrations
  - Milestone: Code migration complete

Week 7: Testing
  - Full testing cycle
  - UAT
  - Milestone: Testing complete

Week 8: Deployment
  - Deploy to production
  - Monitor and validate
  - Milestone: Migration complete
```

### 9. Document and Communicate (2-3 hours)

**Migration plan document:**
- Executive summary
- Current state and target state
- Migration approach and rationale
- Detailed migration steps
- Risk assessment and mitigation
- Rollback strategy
- Testing strategy
- Timeline and milestones
- Roles and responsibilities
- Communication plan

**Stakeholder communication:**
- Engineering team
- Product management
- Customer support
- Customers (if applicable)
- Executive team

**Communication plan:**
- Pre-migration: What's happening, when, why
- During migration: Status updates
- Post-migration: Results, next steps

## Decision Framework

### Migration Approach Selection

**Big Bang:**
- System is small (<10K users)
- Downtime is acceptable (e.g., overnight)
- Migration is low risk
- Team is confident

**Phased:**
- System is medium-large
- Some downtime is acceptable
- Can migrate by module or feature
- Want to validate each phase

**Strangler Fig:**
- System is critical
- Zero downtime required
- Can route traffic incrementally
- Have time for gradual migration

**Parallel Run:**
- System is critical
- Need high confidence
- Can afford to run both systems
- Have time for comparison

### Risk Tolerance

**Low risk tolerance:**
- Use Strangler Fig or Parallel Run
- Extensive testing
- Multiple dry runs
- Gradual rollout

**Medium risk tolerance:**
- Use Phased migration
- Thorough testing
- One dry run
- Staged rollout

**High risk tolerance:**
- Use Big Bang
- Standard testing
- Deploy and monitor

## Quality Checklist

- [ ] Current state is fully documented
- [ ] Target state is clearly defined
- [ ] Migration approach is chosen and justified
- [ ] All risks are identified and have mitigation plans
- [ ] Migration steps are detailed and specific
- [ ] Rollback strategy is defined and tested
- [ ] Testing strategy covers all aspects
- [ ] Timeline is realistic with buffer
- [ ] Roles and responsibilities are assigned
- [ ] Communication plan is defined
- [ ] Dry run is planned
- [ ] Stakeholders are informed and bought in
- [ ] Success criteria are defined
- [ ] Monitoring and validation plan is ready

## Common Mistakes

- **Underestimating complexity**: Migrations always take longer than expected
- **Insufficient testing**: Skipping dry runs or UAT
- **No rollback plan**: Assuming migration will succeed
- **Poor communication**: Not keeping stakeholders informed
- **Ignoring data**: Focusing on code, forgetting data migration
- **No monitoring**: Not having visibility during migration
- **Big bang when phased is better**: Taking unnecessary risk
- **Not involving users**: Migrating without user validation
- **Inadequate skills**: Team doesn't know new technology
- **No buffer**: Planning with zero slack

## Examples

See [examples.md](examples.md) for detailed examples of migration planning for various scenarios.

## Related Skills

- **Requires**: 
  - architecture-discovery (to understand current state)
- **Commonly followed by**: 
  - architecture-decision (to make migration decisions)
  - testing-strategy (to plan migration testing)
- **Alternative to**: 
  - None (this is the primary migration planning skill)
- **Works with**: 
  - architecture-review (to validate target state)
  - technical-debt-analysis (migrations often address debt)
  - refactoring (for code migration)

## Skill Composition

Typical workflow:

```
architecture-discovery
        ↓
technical-debt-analysis
        ↓
migration-planning (this skill)
        ↓
architecture-decision
        ↓
testing-strategy
        ↓
implementation
```

## Evaluation Criteria

### Completeness
- Is current state fully documented?
- Is target state clearly defined?
- Are all migration steps detailed?
- Is rollback strategy complete?
- Is testing strategy comprehensive?

### Risk Management
- Are all risks identified?
- Are mitigation strategies defined?
- Is rollback plan tested?
- Are contingencies planned?

### Feasibility
- Is timeline realistic?
- Does team have required skills?
- Is budget sufficient?
- Are dependencies managed?

### Communication
- Are stakeholders informed?
- Is plan clearly documented?
- Is communication plan defined?
- Are success criteria clear?