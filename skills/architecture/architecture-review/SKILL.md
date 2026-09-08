# Architecture Review

## Purpose

Systematically evaluate an existing architecture for quality, risks, gaps, and improvement opportunities.

## When to Use

- When inheriting a legacy system or codebase
- Before making major architectural changes or migrations
- During quarterly or annual architecture health checks
- When system performance, reliability, or security issues emerge
- Before scaling a system to handle significantly more load
- When preparing for a technical audit or compliance review

## When NOT to Use

- For new systems that don't have an architecture yet (use system-design instead)
- For code-level reviews (use code-review instead)
- For security-specific reviews (use security-architecture-review instead)
- When you need to make a decision between alternatives (use architecture-decision instead)

## Inputs

- **architecture-documentation**: Existing architecture diagrams, ADRs, design docs
- **system-context**: Business requirements, user needs, scale requirements
- **codebase**: Source code, infrastructure-as-code, configuration
- **metrics**: Performance metrics, error rates, availability data
- **constraints**: Budget, timeline, compliance requirements

## Expected Outputs

- **architecture-assessment**: Overall health score and summary
- **findings**: List of issues, risks, and gaps categorized by severity
- **recommendations**: Prioritized improvements with effort estimates
- **architecture-diagram**: Updated or corrected architecture diagram
- **action-plan**: Roadmap for addressing critical issues

## Workflow

### 1. Understand the Context

- Review business requirements and goals
- Understand user needs and usage patterns
- Identify scale requirements (users, data, transactions)
- Note compliance and regulatory requirements
- Understand team structure and capabilities

### 2. Gather Architecture Artifacts

**Documentation:**
- Architecture diagrams (system, component, deployment)
- Architecture Decision Records (ADRs)
- Technical design documents
- API specifications
- Data models and schemas

**Code and Infrastructure:**
- Source code repository structure
- Infrastructure-as-code (Terraform, CloudFormation)
- Configuration files
- Deployment scripts
- Monitoring and alerting setup

**Metrics and Data:**
- Performance metrics (latency, throughput)
- Availability and uptime data
- Error rates and types
- Resource utilization (CPU, memory, storage)
- Cost data

### 3. Review Architectural Qualities

#### Scalability
- Can the system handle 10x current load?
- Are there obvious bottlenecks?
- Is horizontal scaling possible?
- Are there single points of failure?

#### Reliability
- What is the actual uptime?
- How are failures handled?
- Is there redundancy for critical components?
- Are there circuit breakers and retries?
- Is there a disaster recovery plan?

#### Performance
- Are response times acceptable?
- Are there performance bottlenecks?
- Is caching used effectively?
- Are database queries optimized?
- Is the system over-engineered or under-optimized?

#### Security
- Is authentication and authorization properly implemented?
- Are secrets managed securely?
- Is data encrypted in transit and at rest?
- Are there known vulnerabilities?
- Is the principle of least privilege followed?

#### Maintainability
- Is the architecture well-documented?
- Is the codebase modular and loosely coupled?
- Are there clear boundaries between components?
- Is technical debt manageable?
- Can new engineers understand the system?

#### Cost Efficiency
- Are resources right-sized?
- Are there unused or over-provisioned resources?
- Are there opportunities for cost optimization?
- Is the architecture cost-effective for the scale?

### 4. Identify Issues and Risks

**Categorize findings by severity:**

**Critical (P0):**
- Security vulnerabilities
- Single points of failure affecting availability
- Data loss risks
- Compliance violations

**High (P1):**
- Scalability bottlenecks
- Performance issues affecting users
- Reliability issues causing frequent outages
- Technical debt blocking new development

**Medium (P2):**
- Maintainability issues
- Cost inefficiencies
- Missing documentation
- Suboptimal design patterns

**Low (P3):**
- Minor optimizations
- Nice-to-have improvements
- Future-proofing enhancements

### 5. Develop Recommendations

For each finding:

**Problem Statement:**
- What is the issue?
- Why is it a problem?
- What is the impact?

**Recommendation:**
- What should be done?
- Why is this the right solution?
- What are the alternatives?

**Effort Estimate:**
- Small (days)
- Medium (weeks)
- Large (months)

**Priority:**
- Must-fix (critical)
- Should-fix (high value)
- Nice-to-fix (low priority)

### 6. Create Action Plan

**Immediate (0-3 months):**
- Critical security fixes
- Availability improvements
- Blocking technical debt

**Short-term (3-6 months):**
- Scalability improvements
- Performance optimizations
- Documentation updates

**Long-term (6-12 months):**
- Major refactoring
- Technology migrations
- Architecture evolution

### 7. Document and Present Findings

**Executive Summary:**
- Overall architecture health (Red/Yellow/Green)
- Top 3-5 critical issues
- Recommended next steps

**Detailed Findings:**
- All issues categorized by severity
- Supporting evidence (metrics, diagrams)
- Recommendations with effort estimates

**Visual Artifacts:**
- Current architecture diagram
- Proposed architecture diagram (if applicable)
- Data flow diagrams
- Deployment diagrams

## Decision Framework

### Issue Severity

**Critical:**
- Causes data loss or corruption
- Violates security or compliance requirements
- Causes frequent or prolonged outages
- Blocks critical business functionality

**High:**
- Significantly impacts performance or user experience
- Prevents scaling to meet business needs
- Creates significant technical debt
- Increases operational burden substantially

**Medium:**
- Impacts maintainability or developer productivity
- Increases costs unnecessarily
- Missing best practices
- Suboptimal but functional design

**Low:**
- Minor inefficiencies
- Nice-to-have improvements
- Future-proofing opportunities

### Recommendation Priority

**Must-fix:**
- Addresses critical or high-severity issues
- High impact, reasonable effort
- Unblocks other important work
- Required for compliance or security

**Should-fix:**
- Addresses medium-severity issues
- Good ROI (impact vs. effort)
- Improves developer productivity
- Reduces operational burden

**Nice-to-fix:**
- Addresses low-severity issues
- Low effort, low-to-medium impact
- Can be deferred without significant risk

## Quality Checklist

- [ ] All major architectural components are reviewed
- [ ] Scalability, reliability, performance, security, and maintainability are assessed
- [ ] Issues are categorized by severity with supporting evidence
- [ ] Recommendations are specific and actionable
- [ ] Effort estimates are provided for each recommendation
- [ ] Recommendations are prioritized
- [ ] Architecture diagrams are created or updated
- [ ] Metrics and data support the findings
- [ ] Action plan is realistic and time-bound
- [ ] Findings are validated with the team
- [ ] Executive summary is clear and concise

## Common Mistakes

- **Surface-level review**: Only reviewing documentation without examining code or metrics
- **Focusing only on technology**: Ignoring business context and requirements
- **No prioritization**: Listing issues without severity or priority
- **Vague recommendations**: Saying "improve performance" without specific actions
- **Ignoring constraints**: Recommending solutions that are not feasible given budget or timeline
- **No metrics**: Making claims without supporting data
- **Perfectionism**: Recommending a complete rewrite instead of incremental improvements
- **Missing follow-up**: Not creating an action plan or tracking remediation

## Examples

See [examples.md](examples.md) for detailed examples of architecture reviews for various scenarios.

## Related Skills

- **Requires**: 
  - architecture-discovery (to understand the current architecture)
- **Commonly followed by**: 
  - architecture-decision (to make decisions on improvements)
  - tradeoff-analysis (to evaluate alternatives)
  - migration-planning (if major changes are needed)
  - technical-debt-analysis (to prioritize debt)
- **Alternative to**: 
  - security-architecture-review (for security-focused reviews)
  - scalability-analysis (for scalability-focused reviews)
- **Works with**: 
  - code-review (for implementation-level review)
  - production-readiness (for deployment readiness)
  - reliability-analysis (for availability assessment)

## Skill Composition

Typical workflow:

```
architecture-discovery
        ↓
architecture-review
        ↓
architecture-decision
        ↓
migration-planning
```

## Evaluation Criteria

### Completeness
- Are all major architectural components reviewed?
- Are all quality attributes (scalability, reliability, security, etc.) assessed?
- Are findings supported by evidence?
- Is an action plan provided?

### Accuracy
- Are the findings valid and accurate?
- Are severity levels appropriate?
- Are effort estimates realistic?
- Are recommendations technically sound?

### Actionability
- Are recommendations specific and clear?
- Are priorities well-defined?
- Can the team act on the recommendations?
- Is there a clear roadmap?

### Communication
- Is the executive summary clear and concise?
- Are findings presented in a logical structure?
- Are diagrams helpful and accurate?
- Is the report accessible to both technical and non-technical stakeholders?