# Testing Strategy - Step-by-Step Instructions

## Overview

This skill helps you design a comprehensive testing strategy that ensures system quality through appropriate test coverage at all levels.

## Step-by-Step Workflow

### Step 1: Understand the System (1-2 hours)

**Actions:**
1. Review all functional requirements
2. Review non-functional requirements (performance, security, scalability)
3. Study system architecture diagrams
4. Identify all components and their interactions
5. Map out critical user journeys
6. List all external dependencies and integrations
7. Note compliance and regulatory requirements

**Outputs:**
- System understanding document
- Component interaction map
- Critical user journey list

### Step 2: Identify Risk Areas (30-60 minutes)

**Actions:**
1. **Assess business criticality:**
   - What functionality is most critical to the business?
   - What failures would have the highest impact?
   - What areas handle sensitive data or money?

2. **Assess technical complexity:**
   - What code is most complex?
   - What areas have the most dependencies?
   - What integrations are fragile?

3. **Assess change frequency:**
   - What areas change most often?
   - What areas have the most bugs historically?
   - What areas are being actively developed?

4. **Create risk matrix:**
   ```
   Component          | Business Impact | Complexity | Change Freq | Risk Level
   ------------------ | --------------- | ---------- | ----------- | ----------
   Payment Processing | High            | High       | Medium      | High
   User Auth          | High            | Medium     | Low         | High
   Product Catalog    | Medium          | Low        | High        | Medium
   ```

**Outputs:**
- Risk assessment matrix
- Prioritized list of high-risk areas

### Step 3: Define Testing Levels (1-2 hours)

**Actions:**
1. **Unit Testing Strategy:**
   - Define scope: What constitutes a "unit"?
   - Set coverage goals by risk level
   - Identify what NOT to unit test
   - Choose mocking strategy
   - Select unit testing framework

2. **Integration Testing Strategy:**
   - Identify integration points
   - Define integration test scope
   - Choose integration testing approach (in-process, out-of-process)
   - Select integration testing tools
   - Plan test data management

3. **E2E Testing Strategy:**
   - Identify critical user journeys
   - Define E2E test scope (happy paths + key errors)
   - Choose E2E testing framework
   - Plan test environment setup
   - Define browser/device coverage

4. **Additional Testing Types:**
   - Performance testing: Load, stress, spike, endurance
   - Security testing: Penetration, vulnerability scanning
   - Accessibility testing: WCAG compliance
   - Compatibility testing: Browsers, devices, OS

**Outputs:**
- Testing level definitions
- Coverage goals for each level
- Tool selections

### Step 4: Apply the Test Pyramid (30 minutes)

**Actions:**
1. **Validate test distribution:**
   ```
   E2E:    10-20% of tests (critical journeys only)
   Integ:  20-30% of tests (all integration points)
   Unit:   50-70% of tests (all business logic)
   ```

2. **Adjust if needed:**
   - Too many E2E tests? Move some to integration level
   - Too few unit tests? Increase unit test coverage
   - Missing integration tests? Add API/database tests

3. **Estimate test counts:**
   - Unit tests: ~500-1000 for medium project
   - Integration tests: ~50-100
   - E2E tests: ~10-30

**Outputs:**
- Balanced test distribution
- Estimated test counts

### Step 5: Select Testing Tools (1 hour)

**Actions:**
1. **Evaluate tool options:**
   - Does it support our tech stack?
   - Is it actively maintained?
   - Does the team have experience with it?
   - Is it well-documented?
   - What's the learning curve?
   - What's the cost?

2. **Choose tools for each level:**
   - Unit testing framework
   - Integration testing tools
   - E2E testing framework
   - Performance testing tool
   - Code coverage tool
   - Test reporting tool

3. **Validate tool compatibility:**
   - Do tools integrate with CI/CD?
   - Do tools work together?
   - Are there licensing conflicts?

**Outputs:**
- Selected tools for each testing level
- Tool integration plan
- Licensing and cost summary

### Step 6: Define Test Automation Strategy (1-2 hours)

**Actions:**
1. **Prioritize automation:**
   - High priority: Critical paths, regression tests, unit tests
   - Medium priority: Common workflows, API tests
   - Low priority: Edge cases, exploratory scenarios

2. **Define automation timeline:**
   - Phase 1 (Weeks 1-2): Unit test framework setup
   - Phase 2 (Weeks 3-4): Integration test framework setup
   - Phase 3 (Weeks 5-6): E2E test framework setup
   - Phase 4 (Ongoing): Test implementation

3. **Define test execution schedule:**
   - On every commit: Unit tests (< 5 minutes)
   - On every push: Unit + Integration tests (< 15 minutes)
   - On every PR: Full test suite (< 30 minutes)
   - Nightly: Full suite + performance tests
   - Weekly: Full suite + security scans

4. **Plan for test maintenance:**
   - Who owns test maintenance?
   - How to handle flaky tests?
   - When to update tests?
   - How to deprecate obsolete tests?

**Outputs:**
- Automation priority list
- Automation timeline
- Test execution schedule
- Maintenance plan

### Step 7: Define Test Data Strategy (30-60 minutes)

**Actions:**
1. **Choose test data approach:**
   - Fixtures: For stable, predictable data
   - Factories: For flexible, varied data
   - Mocks/Stubs: For external dependencies
   - Test databases: For integration tests
   - Anonymized production data: For realistic scenarios

2. **Define data management:**
   - How to create test data?
   - How to clean up test data?
   - How to version test data?
   - How to share test data across tests?

3. **Plan for data isolation:**
   - Each test should be independent
   - No shared state between tests
   - Clean database before/after tests
   - Use transactions for rollback

**Outputs:**
- Test data strategy
- Data creation and cleanup plan
- Data isolation approach

### Step 8: Define Quality Gates (30 minutes)

**Actions:**
1. **Set code coverage thresholds:**
   - Overall coverage: 70-80%
   - Critical paths: 90-100%
   - New code: 80-90%
   - Branches: 70-80%

2. **Define test success criteria:**
   - All tests must pass
   - No flaky tests
   - Test execution time < 30 minutes
   - Coverage thresholds met

3. **Set performance benchmarks:**
   - API response time: < 200ms (p95)
   - Page load time: < 3 seconds
   - Database query time: < 100ms
   - Concurrent users: 1000+

4. **Define security gates:**
   - No high/critical vulnerabilities
   - All dependencies up-to-date
   - Security scan passes
   - No secrets in code

**Outputs:**
- Quality gate definitions
- Success criteria
- CI/CD integration requirements

### Step 9: Create Implementation Plan (1 hour)

**Actions:**
1. **Define phases:**
   - Phase 1: Setup test infrastructure
   - Phase 2: Implement unit tests for critical paths
   - Phase 3: Implement integration tests
   - Phase 4: Implement E2E tests for critical journeys
   - Phase 5: Expand coverage to medium-priority areas

2. **Assign responsibilities:**
   - Who sets up test frameworks?
   - Who writes tests?
   - Who maintains tests?
   - Who reviews tests?

3. **Create timeline:**
   - Week 1-2: Framework setup
   - Week 3-4: Critical path tests
   - Week 5-6: Integration tests
   - Week 7-8: E2E tests
   - Ongoing: Expand coverage

4. **Identify dependencies:**
   - What needs to be done first?
   - What can be done in parallel?
   - What are the blockers?

**Outputs:**
- Implementation plan with phases
- Responsibility assignments
- Timeline with milestones
- Dependency map

### Step 10: Document and Review (1-2 hours)

**Actions:**
1. **Create testing strategy document:**
   - Executive summary
   - Testing objectives
   - Risk assessment
   - Testing levels and coverage goals
   - Tool selections
   - Automation strategy
   - Test data strategy
   - Quality gates
   - Implementation plan
   - Roles and responsibilities

2. **Review with stakeholders:**
   - Present to engineering team
   - Get feedback from QA team
   - Review with product/business stakeholders
   - Adjust based on feedback

3. **Get approval:**
   - Sign-off from engineering leadership
   - Budget approval (if needed)
   - Timeline approval

**Outputs:**
- Complete testing strategy document
- Stakeholder approval
- Action items for next steps

## Tips for Success

- **Start small**: Begin with critical paths, expand coverage over time
- **Be pragmatic**: 100% coverage is not the goal; effective coverage is
- **Involve the team**: Get buy-in from developers and QA early
- **Automate incrementally**: Don't try to automate everything at once
- **Measure and adjust**: Track metrics and adjust strategy as needed
- **Keep tests fast**: Slow tests won't be run frequently
- **Fix flaky tests immediately**: Don't let unreliable tests persist
- **Treat tests as production code**: Review, refactor, and maintain them

## Common Pitfalls to Avoid

- Inverting the test pyramid (too many E2E tests)
- Setting unrealistic coverage goals (100% coverage)
- Choosing tools without team input
- Not planning for test maintenance
- Ignoring test execution time
- Allowing flaky tests to persist
- Not integrating tests into CI/CD
- Testing implementation details instead of behavior
- Not prioritizing based on risk
- Forgetting about performance and security testing

## Time Estimates

By project size:
- **Small project** (1-2 developers): 4-8 hours
- **Medium project** (3-10 developers): 8-16 hours
- **Large project** (10+ developers): 16-24 hours

By testing maturity:
- **Starting from scratch**: 16-24 hours
- **Improving existing strategy**: 8-12 hours
- **Minor adjustments**: 2-4 hours