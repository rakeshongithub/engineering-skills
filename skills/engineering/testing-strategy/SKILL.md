# Testing Strategy

## Purpose

Design a comprehensive testing strategy covering unit, integration, and end-to-end testing to ensure system quality and reliability.

## When to Use

- When starting a new project or feature
- When establishing quality standards for a team
- When improving test coverage in an existing system
- Before major releases or migrations
- When defining CI/CD pipeline requirements
- When quality issues are frequently escaping to production
- When onboarding new team members to testing practices

## When NOT to Use

- For implementing individual tests (use test-implementation instead)
- For debugging specific test failures (use debugging instead)
- For code-level reviews (use code-review instead)
- For trivial changes that don't warrant a full strategy
- When a testing strategy already exists and is working well

## Inputs

- **requirements**: Functional and non-functional requirements to be tested
- **architecture**: System architecture and component structure
- **risk-areas**: High-risk areas requiring more thorough testing
- **constraints**: Budget, timeline, team skills, tooling limitations

## Expected Outputs

- **testing-plan**: Comprehensive plan covering all testing levels
- **test-coverage-goals**: Target coverage percentages and critical paths
- **testing-tools**: Recommended tools for each testing level
- **test-automation-strategy**: What to automate and when

## Workflow

### 1. Understand the System (1-2 hours)

- Review functional requirements
- Review non-functional requirements (performance, security, reliability)
- Understand system architecture and components
- Identify critical user journeys
- Identify integration points and dependencies
- Note compliance and regulatory requirements

### 2. Identify Risk Areas (30-60 minutes)

**High-risk areas typically include:**
- Payment processing and financial transactions
- Authentication and authorization
- Data privacy and security
- Critical business logic
- Third-party integrations
- Complex algorithms
- Areas with frequent bugs
- New or recently changed code

**Risk assessment:**
- High risk: Critical functionality, high complexity, frequent changes
- Medium risk: Important functionality, moderate complexity
- Low risk: Simple functionality, rarely changes

### 3. Define Testing Levels (1-2 hours)

#### Unit Testing
**What:** Test individual functions, methods, or classes in isolation

**Coverage goals:**
- High-risk areas: 90-100%
- Medium-risk areas: 70-80%
- Low-risk areas: 50-60%

**What to test:**
- Business logic
- Utility functions
- Data transformations
- Edge cases and error conditions
- Validation logic

**What NOT to test:**
- Third-party libraries
- Framework code
- Simple getters/setters
- Configuration files

#### Integration Testing
**What:** Test interactions between components, modules, or services

**Coverage goals:**
- All critical integration points
- All API endpoints
- All database interactions
- All external service integrations

**What to test:**
- API contracts (request/response formats)
- Database operations (CRUD)
- Message queue interactions
- Third-party service integrations
- Authentication and authorization flows
- Error handling across boundaries

#### End-to-End (E2E) Testing
**What:** Test complete user workflows from start to finish

**Coverage goals:**
- All critical user journeys (happy paths)
- Key error scenarios
- Cross-browser/cross-device (if applicable)

**What to test:**
- Complete user workflows (login, checkout, etc.)
- Multi-step processes
- UI interactions
- Data persistence across workflows
- Critical business scenarios

#### Other Testing Types
**Performance Testing:**
- Load testing (expected load)
- Stress testing (beyond expected load)
- Spike testing (sudden load increases)
- Endurance testing (sustained load)

**Security Testing:**
- Penetration testing
- Vulnerability scanning
- Authentication/authorization testing
- Input validation testing

**Accessibility Testing:**
- Screen reader compatibility
- Keyboard navigation
- WCAG compliance

### 4. Select Testing Tools (1 hour)

**Unit Testing:**
- JavaScript/TypeScript: Jest, Vitest, Mocha
- Python: pytest, unittest
- Java: JUnit, TestNG
- C#: xUnit, NUnit

**Integration Testing:**
- API Testing: Postman, REST Assured, Supertest
- Database Testing: Testcontainers, in-memory databases
- Contract Testing: Pact, Spring Cloud Contract

**E2E Testing:**
- Web: Playwright, Cypress, Selenium
- Mobile: Appium, Detox
- API: Postman, REST Assured

**Performance Testing:**
- k6, JMeter, Gatling, Locust

**Test Data Management:**
- Faker.js, Factory Bot, Test Data Builder pattern

**Code Coverage:**
- JavaScript: Istanbul/nyc
- Python: Coverage.py
- Java: JaCoCo
- C#: Coverlet

### 5. Define Test Automation Strategy (1-2 hours)

**Test Pyramid:**
```
       /\
      /E2E\      <- Few (slow, expensive, brittle)
     /------\
    /  Integ \
   /----------\  <- Some (moderate speed/cost)
  /   Unit     \
 /---------------\ <- Many (fast, cheap, stable)
```

**Automation priorities:**
1. **Automate first:**
   - Unit tests (fast, stable)
   - API integration tests
   - Critical user journeys (E2E)
   - Regression tests

2. **Automate later:**
   - Edge case E2E scenarios
   - Visual regression tests
   - Exploratory testing scenarios

3. **Keep manual:**
   - Exploratory testing
   - Usability testing
   - Ad-hoc testing
   - New feature exploration

**When to run tests:**
- Unit tests: On every commit (pre-commit hook)
- Integration tests: On every push (CI pipeline)
- E2E tests: On every merge to main (CI pipeline)
- Performance tests: Nightly or weekly
- Security tests: Weekly or on-demand

### 6. Define Test Data Strategy (30-60 minutes)

**Approaches:**
- **Test fixtures**: Predefined test data
- **Factories**: Generate test data programmatically
- **Mocks/Stubs**: Simulate external dependencies
- **Test databases**: Isolated database for testing
- **Data anonymization**: Use production data (anonymized)

**Best practices:**
- Keep test data minimal and focused
- Use factories for flexible test data generation
- Isolate tests (no shared state)
- Clean up test data after tests
- Version control test fixtures

### 7. Define Quality Gates (30 minutes)

**Code coverage:**
- Minimum overall coverage: 70-80%
- Critical paths coverage: 90-100%
- New code coverage: 80-90%

**Test success rate:**
- All tests must pass before merge
- No flaky tests allowed
- Fix or quarantine failing tests immediately

**Performance benchmarks:**
- API response time: < 200ms (p95)
- Page load time: < 3 seconds
- Database query time: < 100ms

**Security checks:**
- No high/critical vulnerabilities
- All dependencies up-to-date
- Security scan passes

### 8. Document the Strategy (1-2 hours)

**Testing Strategy Document should include:**
- Testing objectives and goals
- Testing levels and coverage goals
- Testing tools and frameworks
- Test automation strategy
- Test data strategy
- Quality gates and success criteria
- Roles and responsibilities
- Timeline and milestones

## Decision Framework

### What to Test

**High priority (must test):**
- Critical business logic
- Payment and financial operations
- Authentication and authorization
- Data integrity and validation
- Security-sensitive operations
- Regulatory compliance requirements

**Medium priority (should test):**
- Important user workflows
- Integration points
- Error handling
- Data transformations
- API contracts

**Low priority (nice to test):**
- Simple CRUD operations
- UI styling and layout
- Logging and monitoring
- Configuration management

### What to Automate

**Automate:**
- Repetitive tests
- Regression tests
- Tests that run frequently
- Tests with clear pass/fail criteria
- Tests that are stable and not flaky

**Keep manual:**
- Exploratory testing
- Usability testing
- Tests requiring human judgment
- Tests that change frequently
- Tests that are expensive to automate

### Test Coverage Goals

**High coverage (90-100%):**
- Critical business logic
- Payment processing
- Security-sensitive code
- Complex algorithms

**Medium coverage (70-80%):**
- Standard business logic
- API endpoints
- Data access layer

**Low coverage (50-60%):**
- Simple utilities
- Configuration code
- UI components (if tested via E2E)

## Quality Checklist

- [ ] All testing levels are defined (unit, integration, E2E)
- [ ] Coverage goals are specified for each level
- [ ] High-risk areas are identified and prioritized
- [ ] Testing tools are selected for each level
- [ ] Test automation strategy is defined
- [ ] Test data strategy is defined
- [ ] Quality gates are established
- [ ] CI/CD integration is planned
- [ ] Roles and responsibilities are assigned
- [ ] Timeline and milestones are defined
- [ ] Strategy is documented and reviewed
- [ ] Team is trained on tools and practices

## Common Mistakes

- **Testing everything equally**: Not prioritizing based on risk
- **Over-relying on E2E tests**: Inverting the test pyramid (slow, brittle tests)
- **Under-testing critical paths**: Missing high-risk areas
- **No test automation**: Relying entirely on manual testing
- **Flaky tests**: Allowing unreliable tests to persist
- **Testing implementation details**: Tests break with refactoring
- **No test data strategy**: Tests fail due to data issues
- **Ignoring performance testing**: Only testing functionality
- **No quality gates**: Allowing poor quality code to merge
- **Not maintaining tests**: Tests become outdated and irrelevant

## Examples

See [examples.md](examples.md) for detailed examples of testing strategies for various scenarios.

## Related Skills

- **Requires**: 
  - None (foundational skill)
- **Commonly followed by**: 
  - test-implementation (to implement the tests)
  - ci-cd-design (to integrate tests into pipeline)
- **Alternative to**: 
  - None (this is the primary testing strategy skill)
- **Works with**: 
  - code-review (to ensure tests are reviewed)
  - architecture-review (to understand system for testing)
  - requirements-analysis (to understand what to test)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
architecture-design
        ↓
testing-strategy (this skill)
        ↓
test-implementation
        ↓
ci-cd-design
```

## Evaluation Criteria

### Completeness
- Are all testing levels addressed?
- Are all risk areas identified?
- Are coverage goals defined?
- Is test automation addressed?
- Is test data strategy defined?

### Appropriateness
- Are testing levels appropriate for the system?
- Are coverage goals realistic?
- Are tools appropriate for the tech stack?
- Is the test pyramid balanced?

### Feasibility
- Can the strategy be implemented within constraints?
- Are tools and skills available?
- Is the timeline realistic?
- Is the budget sufficient?

### Effectiveness
- Will the strategy catch critical bugs?
- Are high-risk areas adequately covered?
- Is the strategy sustainable long-term?
- Does it support CI/CD goals?