# Technical Debt Analysis - Step-by-Step Instructions

## Overview

This skill helps you systematically identify, quantify, and prioritize technical debt to create an actionable remediation plan.

## Step-by-Step Workflow

### Step 1: Gather Data (2-4 hours)

**Actions:**
1. **Collect code quality metrics:**
   - Run code coverage tool (e.g., Istanbul, JaCoCo, Coverage.py)
   - Run static analysis (e.g., SonarQube, ESLint, Pylint)
   - Measure code complexity (cyclomatic complexity)
   - Detect code duplication (copy-paste detector)
   - Analyze dependency health (outdated, vulnerable)

2. **Collect performance metrics:**
   - Build time
   - Test execution time
   - Deployment time
   - Application response times
   - Resource utilization

3. **Collect development metrics:**
   - Average time to implement features
   - Bug rate (bugs per sprint/month)
   - Time to fix bugs
   - Deployment frequency
   - Failed deployment rate
   - Mean time to recovery (MTTR)

4. **Gather team feedback:**
   - Conduct developer survey
   - Review retrospective notes
   - Collect pain points from code reviews
   - Interview team members

**Outputs:**
- Code quality report
- Performance metrics dashboard
- Development metrics summary
- Team feedback document

### Step 2: Identify Technical Debt (2-4 hours)

**Actions:**
1. **Analyze code debt:**
   - Review static analysis warnings
   - Identify code duplication (>5% is concerning)
   - Find complex methods (cyclomatic complexity >10)
   - Locate large files (>500 lines)
   - Find low test coverage areas (<70%)
   - Search for TODO/FIXME comments
   - Identify commented-out code

2. **Analyze architecture debt:**
   - Review architecture diagrams (or lack thereof)
   - Identify tight coupling between modules
   - Find circular dependencies
   - Locate monolithic components that should be split
   - Identify inconsistent patterns

3. **Analyze technology debt:**
   - Check for outdated dependencies (>2 years old)
   - Identify security vulnerabilities (use npm audit, Snyk)
   - Find unsupported frameworks or libraries
   - Locate incompatible technology versions

4. **Analyze testing debt:**
   - Measure test coverage by module
   - Identify flaky tests
   - Measure test execution time
   - Find missing test types (unit, integration, E2E)

5. **Analyze documentation debt:**
   - Check for missing README files
   - Identify undocumented APIs
   - Find outdated documentation
   - Locate missing architecture diagrams

6. **Analyze infrastructure debt:**
   - Review deployment process (manual vs. automated)
   - Check CI/CD pipeline maturity
   - Assess monitoring and alerting coverage
   - Review disaster recovery plans

**Outputs:**
- Categorized list of technical debt items
- Evidence for each debt item (metrics, examples)

### Step 3: Quantify Impact (2-3 hours)

**Actions:**
1. **For each debt item, assess business impact:**
   - Does it slow feature development? (How much?)
   - Does it increase bug rate? (By how much?)
   - Does it affect reliability? (Downtime, errors)
   - Does it impact customers? (Performance, UX)
   - Does it increase costs? (Infrastructure, support)

2. **Assess technical impact:**
   - How many developers are affected?
   - How frequently is the code changed?
   - Does it block other improvements?
   - Does it increase complexity?
   - Does it create security risks?

3. **Score impact:**
   - **Critical (10)**: Blocking development, causing outages, security risks
   - **High (7-9)**: Significantly slowing development, frequent bugs
   - **Medium (4-6)**: Moderate impact on velocity or quality
   - **Low (1-3)**: Minor inconvenience, rarely encountered

4. **Document impact:**
   ```
   Debt Item: Low test coverage in payment module
   Business Impact: High (8/10)
   - Bugs in payment code affect revenue
   - Fear of breaking changes slows development
   - Recent production bug cost $50K in lost sales
   
   Technical Impact: High (8/10)
   - 3 developers work on payment code weekly
   - Code changes require extensive manual testing
   - Blocks refactoring efforts
   ```

**Outputs:**
- Impact score for each debt item
- Documented rationale for each score

### Step 4: Estimate Remediation Effort (1-2 hours)

**Actions:**
1. **For each debt item, estimate effort:**
   - **Small (1-5 days)**: Dependency updates, simple refactoring
   - **Medium (1-4 weeks)**: Module refactoring, test coverage
   - **Large (1-3 months)**: Architecture changes, migrations

2. **Assess risk:**
   - **Low**: Well-understood, low risk of breaking changes
   - **Medium**: Some unknowns, moderate risk
   - **High**: Complex, high risk of breaking changes

3. **Identify dependencies:**
   - What needs to be done first?
   - What can be done in parallel?
   - What blocks other work?

4. **Document effort:**
   ```
   Debt Item: Low test coverage in payment module
   Effort: Medium (2-3 weeks)
   Risk: Medium
   - Need to understand existing behavior
   - Some edge cases are unclear
   - Requires coordination with QA team
   
   Dependencies:
   - None (can start immediately)
   ```

**Outputs:**
- Effort estimate for each debt item
- Risk assessment for each debt item
- Dependency map

### Step 5: Prioritize Technical Debt (1-2 hours)

**Actions:**
1. **Create prioritization matrix:**
   ```
   Impact ↑
   
   High   | P1: Strategic  | P0: Quick Wins
          | Investments    |
   -------+----------------+---------------
   Low    | P3: Avoid      | P2: Fill-ins
          |                |
          +--------------------------------
            High <-- Effort --> Low
   ```

2. **Assign priorities:**
   - **P0 (Critical)**: High impact, low effort - Do first
   - **P1 (High)**: High impact, high effort - Plan carefully
   - **P2 (Medium)**: Low impact, low effort - Do when available
   - **P3 (Low)**: Low impact, high effort - Avoid

3. **Consider additional factors:**
   - Security vulnerabilities: Always P0
   - Compliance deadlines: Adjust priority
   - Team morale: Consider impact on developers
   - Enables new features: May increase priority

4. **Create prioritized list:**
   ```
   P0 (Quick Wins):
   1. Update vulnerable dependencies (Impact: 9, Effort: 2 days)
   2. Fix flaky tests (Impact: 7, Effort: 3 days)
   
   P1 (Strategic):
   1. Add test coverage to payment module (Impact: 8, Effort: 3 weeks)
   2. Refactor authentication service (Impact: 8, Effort: 4 weeks)
   
   P2 (Fill-ins):
   1. Update documentation (Impact: 4, Effort: 2 days)
   2. Remove dead code (Impact: 3, Effort: 1 day)
   
   P3 (Avoid):
   1. Rewrite legacy admin panel (Impact: 3, Effort: 3 months)
   ```

**Outputs:**
- Prioritized list of technical debt
- Rationale for prioritization decisions

### Step 6: Create Remediation Plan (2-3 hours)

**Actions:**
1. **Group by timeline:**
   - **Immediate (0-3 months)**: P0 items + critical P1 items
   - **Short-term (3-6 months)**: Remaining P1 items
   - **Long-term (6-12 months)**: P2 items

2. **For each item, define:**
   - **What**: Description of the debt
   - **Why**: Impact and rationale
   - **How**: Approach and steps
   - **When**: Timeline and milestones
   - **Who**: Owner or team
   - **Success criteria**: How to measure completion

3. **Example plan item:**
   ```
   Item: Add test coverage to payment module
   Priority: P1
   Timeline: Q2 2024 (Weeks 1-3)
   
   What:
   - Increase test coverage from 45% to 80%
   - Focus on critical payment flows
   
   Why:
   - Recent production bug cost $50K
   - Developers afraid to change payment code
   - Slowing feature development
   
   How:
   1. Week 1: Write characterization tests for existing behavior
   2. Week 2: Add unit tests for payment calculations
   3. Week 3: Add integration tests for payment gateway
   
   Who: Sarah (lead), John (support)
   
   Success Criteria:
   - Test coverage >= 80%
   - All critical payment flows covered
   - No production bugs in payment code for 3 months
   ```

4. **Allocate resources:**
   - Assign owners to each item
   - Allocate time (e.g., 20% of sprint capacity)
   - Schedule work in sprints

**Outputs:**
- Detailed remediation plan
- Timeline with milestones
- Resource allocation

### Step 7: Establish Debt Prevention (1 hour)

**Actions:**
1. **Define quality standards:**
   - Minimum test coverage (e.g., 70%)
   - Maximum complexity (e.g., cyclomatic complexity <10)
   - Maximum duplication (e.g., <5%)
   - Code review requirements

2. **Set up automated gates:**
   - CI/CD quality checks
   - Pre-commit hooks
   - Pull request checks
   - Dependency vulnerability scanning

3. **Establish processes:**
   - Include "reduce debt" in Definition of Done
   - Allocate % of capacity to debt (e.g., 20%)
   - Regular debt review (quarterly)
   - Architecture Decision Records (ADRs)

4. **Monitor debt:**
   - Track quality metrics over time
   - Dashboard for debt trends
   - Alert on degradation

**Outputs:**
- Quality standards document
- Automated quality gates
- Debt prevention processes
- Monitoring dashboard

### Step 8: Communicate and Get Buy-in (1-2 hours)

**Actions:**
1. **Prepare presentation:**
   - Executive summary (1 slide)
   - Current state of debt (metrics, examples)
   - Impact on business (revenue, velocity, quality)
   - Prioritized remediation plan
   - Resource requirements (time, budget)
   - Expected benefits (faster development, fewer bugs)

2. **Present to stakeholders:**
   - Engineering leadership
   - Product management
   - Executive team
   - Development team

3. **Get approval:**
   - Budget for debt paydown
   - Time allocation (% of sprints)
   - Team commitment

4. **Communicate plan:**
   - Share with entire team
   - Make plan accessible (wiki, docs)
   - Regular updates on progress

**Outputs:**
- Stakeholder presentation
- Approved budget and timeline
- Team commitment
- Communication plan

## Tips for Success

- **Be data-driven**: Use metrics, not opinions
- **Involve the team**: Get developer input on pain points
- **Prioritize ruthlessly**: You can't fix everything
- **Focus on impact**: Fix high-impact debt first
- **Prevent new debt**: Don't just fix old debt
- **Communicate clearly**: Help stakeholders understand the value
- **Track progress**: Measure improvement over time
- **Celebrate wins**: Recognize debt paydown achievements

## Common Pitfalls to Avoid

- Treating all old code as debt
- Not quantifying impact
- No prioritization (trying to fix everything)
- Ignoring team feedback
- Not getting stakeholder buy-in
- No action plan (just analysis)
- Perfectionism (trying to eliminate all debt)
- Not preventing new debt
- Vague debt descriptions
- No metrics or tracking

## Time Estimates

By codebase size:
- **Small** (<10K LOC): 4-8 hours
- **Medium** (10K-100K LOC): 8-16 hours
- **Large** (>100K LOC): 16-24 hours