# Technical Debt Analysis

## Purpose

Identify, quantify, and prioritize technical debt for systematic remediation.

## When to Use

- When planning a major refactoring effort
- During quarterly or annual technical planning
- When development velocity is decreasing
- Before starting a new project phase or feature
- When onboarding new team members (to understand debt)
- When preparing for a technical audit
- When deciding between new features and debt paydown
- When quality issues are increasing

## When NOT to Use

- For immediate bug fixes (fix bugs directly)
- For minor code quality issues (use code-review instead)
- For architectural changes (use architecture-review instead)
- When you don't have time to act on findings
- For new projects with no existing debt

## Inputs

- **codebase**: Source code, tests, documentation
- **architecture**: System architecture and design documents
- **quality-metrics**: Code coverage, complexity, duplication, bug rates
- **team-feedback**: Developer pain points and frustrations

## Expected Outputs

- **debt-inventory**: Comprehensive list of technical debt items
- **impact-assessment**: Business and technical impact of each debt item
- **prioritization**: Ranked list of debt to address
- **remediation-plan**: Roadmap for paying down debt

## Workflow

### 1. Gather Data (2-4 hours)

**Code Quality Metrics:**
- Code coverage percentage
- Cyclomatic complexity
- Code duplication percentage
- Lines of code per file/class
- Dependency analysis
- Static analysis warnings

**Performance Metrics:**
- Build time
- Test execution time
- Deployment time
- Application performance

**Development Metrics:**
- Time to implement features
- Bug rate and severity
- Time to fix bugs
- Deployment frequency
- Mean time to recovery (MTTR)

**Team Feedback:**
- Developer surveys
- Pain point discussions
- Retrospective notes
- Code review comments

### 2. Identify Technical Debt (2-4 hours)

**Code Debt:**
- Code duplication
- Complex, hard-to-understand code
- Lack of tests or low test coverage
- Outdated or missing documentation
- Code smells and anti-patterns
- Commented-out code
- TODO comments

**Architecture Debt:**
- Tight coupling between components
- Monolithic architecture preventing scaling
- Missing or outdated architecture documentation
- Inconsistent architectural patterns
- Over-engineered or under-engineered solutions

**Technology Debt:**
- Outdated dependencies and frameworks
- Unsupported libraries or tools
- Security vulnerabilities in dependencies
- Incompatible technology stack
- Missing or outdated development tools

**Testing Debt:**
- Low test coverage
- Flaky or unreliable tests
- Slow test execution
- Missing test automation
- No performance or security tests

**Documentation Debt:**
- Missing or outdated documentation
- Undocumented APIs
- No architecture diagrams
- Missing runbooks or playbooks
- Outdated README files

**Infrastructure Debt:**
- Manual deployment processes
- No CI/CD pipeline
- Inconsistent environments
- Missing monitoring or alerting
- No disaster recovery plan

### 3. Quantify Impact (2-3 hours)

**For each debt item, assess:**

**Business Impact:**
- Does it slow down feature development?
- Does it increase bug rate?
- Does it affect system reliability?
- Does it impact customer experience?
- Does it increase operational costs?

**Technical Impact:**
- How many developers does it affect?
- How often is the affected code changed?
- Does it block other improvements?
- Does it increase complexity?
- Does it create security risks?

**Impact Scoring:**
- **Critical**: Blocking development, causing outages, security risks
- **High**: Significantly slowing development, frequent bugs
- **Medium**: Moderate impact on velocity or quality
- **Low**: Minor inconvenience, rarely encountered

### 4. Estimate Remediation Effort (1-2 hours)

**For each debt item, estimate:**

**Effort Level:**
- **Small** (days): Simple refactoring, dependency updates
- **Medium** (weeks): Module refactoring, test coverage improvements
- **Large** (months): Architecture changes, major migrations

**Risk Level:**
- **Low**: Well-understood, low risk of breaking changes
- **Medium**: Some unknowns, moderate risk
- **High**: Complex, high risk of breaking changes

**Dependencies:**
- What needs to be done first?
- What can be done in parallel?
- What blocks other work?

### 5. Prioritize Technical Debt (1-2 hours)

**Prioritization Matrix:**

```
High Impact + Low Effort = Quick Wins (Do First)
High Impact + High Effort = Strategic Investments (Plan Carefully)
Low Impact + Low Effort = Fill-ins (Do When Available)
Low Impact + High Effort = Avoid (Don't Do)
```

**Priority Factors:**
1. **Impact on business**: Revenue, customer satisfaction, compliance
2. **Impact on team**: Developer productivity, morale
3. **Effort required**: Time, resources, risk
4. **Dependencies**: Blocks other work, enables future work
5. **Urgency**: Security vulnerabilities, compliance deadlines

**Priority Levels:**
- **P0 (Critical)**: Must fix immediately
- **P1 (High)**: Should fix in next quarter
- **P2 (Medium)**: Should fix in next 6 months
- **P3 (Low)**: Nice to fix, no timeline

### 6. Create Remediation Plan (2-3 hours)

**Immediate (0-3 months):**
- Critical security vulnerabilities
- Blocking technical debt
- High-impact, low-effort items (quick wins)

**Short-term (3-6 months):**
- High-impact items
- Items enabling new features
- Test coverage improvements

**Long-term (6-12 months):**
- Strategic refactoring
- Architecture improvements
- Technology migrations

**For each item in the plan:**
- What: Description of the debt
- Why: Impact and rationale for fixing
- How: Approach and steps
- When: Timeline and milestones
- Who: Team or person responsible
- Success criteria: How to measure completion

### 7. Establish Debt Prevention (1 hour)

**Prevent new debt:**
- Code review standards
- Definition of Done includes quality
- Automated quality gates in CI/CD
- Regular refactoring time
- Architecture decision records (ADRs)
- Documentation requirements

**Monitor debt:**
- Track quality metrics over time
- Regular debt analysis (quarterly)
- Include debt in sprint planning
- Allocate % of capacity to debt paydown (e.g., 20%)

### 8. Communicate and Get Buy-in (1-2 hours)

**Prepare presentation:**
- Executive summary
- Current state of technical debt
- Impact on business and team
- Prioritized remediation plan
- Resource requirements
- Expected benefits

**Stakeholders:**
- Engineering leadership
- Product management
- Executive team
- Development team

**Get approval:**
- Budget for debt paydown
- Time allocation (% of sprints)
- Team buy-in and commitment

## Decision Framework

### What Counts as Technical Debt?

**Technical Debt:**
- Intentional shortcuts taken for speed
- Outdated technology or approaches
- Missing tests or documentation
- Code that's hard to maintain
- Architecture that doesn't scale

**NOT Technical Debt:**
- Bugs (fix them)
- Missing features (build them)
- Different coding style preferences
- Code that works fine and is maintainable

### When to Pay Down Debt

**Pay down now:**
- Blocking new development
- Causing frequent production issues
- Security vulnerabilities
- High impact, low effort

**Pay down soon:**
- Slowing development velocity
- Increasing bug rate
- Affecting team morale
- High impact, high effort (plan carefully)

**Pay down later:**
- Low impact on business and team
- High effort required
- Other priorities are more important

**Don't pay down:**
- Code that will be deleted soon
- Low impact, high effort
- "Debt" that's actually just different style

## Quality Checklist

- [ ] All major debt categories are assessed (code, architecture, technology, testing, documentation, infrastructure)
- [ ] Quality metrics are collected and analyzed
- [ ] Team feedback is gathered
- [ ] Each debt item has impact assessment
- [ ] Each debt item has effort estimate
- [ ] Debt is prioritized using consistent criteria
- [ ] Remediation plan has clear timeline
- [ ] Remediation plan has assigned owners
- [ ] Prevention strategies are defined
- [ ] Stakeholders are informed and bought in
- [ ] Success criteria are defined
- [ ] Plan is documented and accessible

## Common Mistakes

- **Treating everything as debt**: Not all old code is debt
- **No prioritization**: Trying to fix everything at once
- **Ignoring team feedback**: Only looking at metrics
- **No action plan**: Identifying debt but not fixing it
- **Perfectionism**: Trying to eliminate all debt
- **Not preventing new debt**: Only fixing old debt
- **No stakeholder buy-in**: Not getting approval for time/budget
- **Vague descriptions**: Not clearly defining debt items
- **No metrics**: Not measuring impact or progress
- **Ignoring quick wins**: Only focusing on big items

## Examples

See [examples.md](examples.md) for detailed examples of technical debt analysis for various scenarios.

## Related Skills

- **Requires**: 
  - None (foundational skill)
- **Commonly followed by**: 
  - refactoring (to fix code debt)
  - migration-planning (for technology debt)
- **Alternative to**: 
  - None (this is the primary debt analysis skill)
- **Works with**: 
  - architecture-review (for architecture debt)
  - code-review (for code debt)
  - testing-strategy (for testing debt)

## Skill Composition

Typical workflow:

```
technical-debt-analysis (this skill)
        ↓
prioritization
        ↓
refactoring or migration-planning
        ↓
code-review
        ↓
deployment
```

## Evaluation Criteria

### Completeness
- Are all debt categories assessed?
- Is team feedback included?
- Are metrics collected?
- Is impact quantified?
- Is effort estimated?

### Accuracy
- Are debt items actually debt?
- Are impact assessments realistic?
- Are effort estimates reasonable?
- Are priorities appropriate?

### Actionability
- Is there a clear remediation plan?
- Are items specific and measurable?
- Are owners assigned?
- Are timelines realistic?
- Can the team act on the plan?

### Effectiveness
- Does the plan address high-impact debt?
- Are quick wins identified?
- Is debt prevention addressed?
- Will the plan improve velocity and quality?