# Skill Authoring - Examples

This document provides practical examples of creating different types of engineering skills.

---

## Example 1: Creating an Architecture Skill

### Skill: API Design Review

#### Step 1: Define the Problem

**Problem Statement**:
```
Problem: Teams often design APIs without systematic review, leading to 
poor developer experience, security vulnerabilities, and performance issues 
that are expensive to fix after release.

Why it matters: APIs are contracts that are hard to change once published. 
Poor API design leads to frustrated developers, security risks, and 
technical debt.

Current approach: Ad-hoc reviews, inconsistent criteria, often skipped 
due to time pressure.

What's missing: A systematic, repeatable process for reviewing APIs 
before implementation.
```

#### Step 2: Validate the Idea

**Validation**:
- ✅ Recurring problem: Yes, every team designs APIs
- ✅ Clear and specific: Yes, focused on API design review
- ✅ Doesn't exist: Checked catalog, no existing skill
- ✅ Repeatable workflow: Yes, can create step-by-step process
- ✅ Valuable outputs: Yes, list of issues and recommendations

**Decision**: Proceed with skill creation

#### Step 3: Choose Category and Name

**Category**: `architecture` (API design is an architectural concern)

**Name**: `api-design-review` (clear, specific, action-oriented)

#### Step 4: Create Directory Structure

```bash
mkdir -p skills/architecture/api-design-review
cd skills/architecture/api-design-review
touch SKILL.md skill.json instructions.md examples.md
mkdir examples evals
```

#### Step 5: Write SKILL.md

**Purpose**:
```markdown
## Purpose

Systematically review API design to identify usability, security, and 
performance issues before implementation.
```

**When to Use**:
```markdown
## When to Use

- Before implementing a new REST API
- When refactoring an existing API
- When receiving complaints about API usability
- Before making breaking changes to an API
- When designing public-facing APIs
```

**When NOT to Use**:
```markdown
## When NOT to Use

- For internal, trivial CRUD APIs with standard patterns
- When the API is already implemented and in production (use api-refactoring)
- For non-HTTP APIs (use protocol-specific review skills)
```

**Inputs**:
```markdown
## Inputs

- **api-specification**: OpenAPI/Swagger spec or equivalent documentation
- **use-cases**: Primary use cases the API should support
- **constraints**: Technical or business constraints (rate limits, auth)
- **target-audience**: Who will use this API (internal, external, mobile, web)
```

**Outputs**:
```markdown
## Expected Outputs

- **findings**: List of issues (usability, security, performance)
- **recommendations**: Specific improvements with rationale
- **risk-assessment**: Severity and impact of each issue
- **action-items**: Prioritized list of changes to make
```

**Workflow** (abbreviated):
```markdown
## Workflow

### 1. Review API Structure
- Validate resource naming conventions
- Check HTTP method usage
- Review URL structure

### 2. Evaluate Usability
- Assess developer experience
- Check consistency
- Review documentation

### 3. Security Review
- Validate authentication/authorization
- Check for security vulnerabilities
- Review data exposure

### 4. Performance Analysis
- Identify potential bottlenecks
- Review pagination strategy
- Check caching headers

### 5. Document Findings
- Categorize issues
- Prioritize recommendations
- Create action items
```

#### Step 6: Create skill.json

```json
{
  "name": "api-design-review",
  "category": "architecture",
  "version": "1.0.0",
  "description": "Systematically review API design to identify usability, security, and performance issues before implementation",
  "inputs": [
    "api-specification",
    "use-cases",
    "constraints",
    "target-audience"
  ],
  "outputs": [
    "findings",
    "recommendations",
    "risk-assessment",
    "action-items"
  ],
  "requires": [],
  "commonly_followed_by": [
    "security-review",
    "technical-specification",
    "api-documentation"
  ],
  "tags": [
    "api",
    "design",
    "review",
    "rest",
    "architecture"
  ],
  "complexity": "intermediate",
  "estimated_time": "2 hours"
}
```

#### Step 7: Create Examples

**Example in examples.md**:
```markdown
## Example 1: E-commerce Order API Review

### Problem Statement
Review the design of a new Order Management API before implementation.

### Context
- REST API for order processing
- Will be used by web and mobile clients
- Needs to support order creation, updates, cancellation, and tracking

### Inputs
- OpenAPI specification (provided)
- Use cases: Create order, update order, cancel order, track order
- Constraints: Must support 1000 requests/second, PCI compliance required
- Target audience: External developers (partners)

### Execution
[Detailed walkthrough of the review process]

### Outputs
**Findings**:
1. Security: API keys exposed in URL parameters (HIGH)
2. Usability: Inconsistent error response format (MEDIUM)
3. Performance: Missing pagination on order list endpoint (HIGH)

**Recommendations**:
1. Move API keys to Authorization header
2. Standardize error responses using RFC 7807
3. Add cursor-based pagination

**Action Items**:
1. [Priority 1] Fix security issues
2. [Priority 2] Add pagination
3. [Priority 3] Standardize errors
```

#### Step 8: Test the Skill

**Testing**:
- Executed the skill on a real API design
- Followed the workflow step-by-step
- Verified outputs were valuable
- Refined workflow based on learnings

#### Result

A complete, tested skill ready for submission.

---

## Example 2: Creating an Agentic Engineering Skill

### Skill: Agent Task Decomposition

#### Step 1: Define the Problem

**Problem Statement**:
```
Problem: Complex engineering tasks are often too large for AI agents to 
handle in a single execution, leading to incomplete solutions, errors, 
and poor quality outputs.

Why it matters: AI agents work best on focused, well-scoped tasks. 
Large, complex problems need to be broken down systematically.

Current approach: Manual decomposition, inconsistent granularity, 
often results in tasks that are still too large.

What's missing: A systematic method for breaking engineering problems 
into agent-appropriate tasks.
```

#### Step 2: Validate the Idea

**Validation**:
- ✅ Recurring problem: Yes, every complex agent workflow needs this
- ✅ Clear and specific: Yes, focused on task decomposition for agents
- ✅ Doesn't exist: No existing skill for agent task decomposition
- ✅ Repeatable workflow: Yes, can create systematic process
- ✅ Valuable outputs: Yes, list of executable agent tasks

**Decision**: Proceed

#### Step 3: Choose Category and Name

**Category**: `agentic` (agent-specific skill)

**Name**: `agent-task-decomposition`

#### Step 4: Write SKILL.md (Key Sections)

**Purpose**:
```markdown
## Purpose

Break down complex engineering problems into agent-sized tasks that can 
be executed independently by AI coding agents.
```

**When to Use**:
```markdown
## When to Use

- When designing a multi-agent workflow
- When a task is too complex for a single agent execution
- When planning agent-driven development
- When an agent execution failed due to task complexity
```

**Inputs**:
```markdown
## Inputs

- **engineering-problem**: The complex problem to decompose
- **context**: Relevant system context
- **agent-capabilities**: What the target agents can do
- **constraints**: Time, resource, or technical constraints
```

**Outputs**:
```markdown
## Expected Outputs

- **task-list**: Ordered list of agent-executable tasks
- **task-dependencies**: Dependencies between tasks
- **task-context**: What context each task needs
- **success-criteria**: How to validate each task
```

**Workflow** (abbreviated):
```markdown
## Workflow

### 1. Analyze the Problem
- Understand the overall goal
- Identify major components
- Assess complexity

### 2. Identify Natural Boundaries
- Find logical separation points
- Identify independent subtasks
- Group related work

### 3. Size the Tasks
- Ensure each task is agent-appropriate
- Validate task independence
- Check task clarity

### 4. Define Dependencies
- Map task relationships
- Determine execution order
- Identify parallel opportunities

### 5. Specify Context Requirements
- What does each task need to know?
- What outputs feed into next tasks?
- What validation is needed?
```

**Decision Framework**:
```markdown
## Decision Framework

### Task Size Guidelines

**Too small**:
- Can be done in < 5 minutes
- Trivial, no decision-making
- Creates excessive overhead

**Right size**:
- Clear, focused objective
- Can be completed in one agent execution
- Has measurable output
- Can be validated independently

**Too large**:
- Requires multiple decisions
- Spans multiple domains
- Takes > 30 minutes
- Has unclear success criteria
```

#### Step 5: Create Examples

**Example**:
```markdown
## Example 1: Decomposing "Add User Authentication"

### Problem Statement
"Add user authentication to the application"

### Analysis
This is too large for a single agent task. Needs decomposition.

### Decomposition

**Task 1**: Design authentication architecture
- Input: Application architecture, security requirements
- Output: Authentication design document
- Estimated time: 20 minutes

**Task 2**: Implement user registration endpoint
- Input: Authentication design, API standards
- Output: Registration endpoint code + tests
- Estimated time: 25 minutes
- Depends on: Task 1

**Task 3**: Implement login endpoint
- Input: Authentication design, API standards
- Output: Login endpoint code + tests
- Estimated time: 25 minutes
- Depends on: Task 1

**Task 4**: Implement JWT token generation
- Input: Authentication design, security requirements
- Output: Token service code + tests
- Estimated time: 20 minutes
- Depends on: Task 1

**Task 5**: Add authentication middleware
- Input: Authentication design, existing middleware
- Output: Auth middleware code + tests
- Estimated time: 20 minutes
- Depends on: Task 4

**Task 6**: Update existing endpoints with auth
- Input: List of endpoints, auth middleware
- Output: Updated endpoint code
- Estimated time: 15 minutes
- Depends on: Task 5

### Result
One complex task → 6 focused, executable agent tasks
```

#### Result

A complete agentic engineering skill that teaches systematic task decomposition.

---

## Example 3: Creating a Simple Engineering Skill

### Skill: Dependency Analysis

#### Step 1: Define the Problem

**Problem Statement**:
```
Problem: Teams often don't have a clear understanding of their 
dependencies (libraries, packages, services), leading to security 
vulnerabilities, version conflicts, and unexpected breaking changes.

Why it matters: Dependencies are a major source of security issues 
and technical debt. Understanding them is critical for maintenance.

Current approach: Manual inspection, often incomplete, not documented.

What's missing: A systematic process for analyzing and documenting 
dependencies.
```

#### Step 2: Create Simple Workflow

**Workflow**:
```markdown
## Workflow

### 1. Inventory Dependencies
- List all direct dependencies
- Identify transitive dependencies
- Document versions

### 2. Analyze Each Dependency
- Check for known vulnerabilities
- Review maintenance status
- Assess license compatibility
- Identify version constraints

### 3. Identify Risks
- Outdated dependencies
- Unmaintained packages
- Security vulnerabilities
- License issues
- Version conflicts

### 4. Create Action Plan
- Prioritize updates
- Plan replacements for unmaintained packages
- Document acceptable risk
```

#### Step 3: Create Minimal skill.json

```json
{
  "name": "dependency-analysis",
  "category": "engineering",
  "version": "1.0.0",
  "description": "Analyze project dependencies to identify risks and create an update plan",
  "inputs": [
    "dependency-manifest",
    "project-context"
  ],
  "outputs": [
    "dependency-inventory",
    "risk-assessment",
    "action-plan"
  ],
  "requires": [],
  "commonly_followed_by": [
    "security-review",
    "technical-debt-analysis"
  ],
  "tags": [
    "dependencies",
    "security",
    "maintenance"
  ],
  "complexity": "basic",
  "estimated_time": "1 hour"
}
```

#### Result

A simple, focused skill that can be created quickly.

---

## Example 4: Creating a Decision-Making Skill

### Skill: Technology Selection

#### Step 1: Define the Problem

**Problem Statement**:
```
Problem: Technology decisions are often made based on hype, familiarity, 
or incomplete analysis, leading to poor choices that are expensive to 
reverse.

Why it matters: Technology choices have long-term impact on productivity, 
maintainability, and team satisfaction.

Current approach: Ad-hoc evaluation, bias toward familiar technologies, 
inconsistent criteria.

What's missing: A structured decision-making process with clear criteria.
```

#### Step 2: Create Decision Framework

**Decision Framework**:
```markdown
## Decision Framework

### Evaluation Criteria

**Technical Fit**:
- Does it solve the problem?
- Does it meet performance requirements?
- Does it integrate with existing systems?
- Is it mature and stable?

**Team Fit**:
- Does the team have expertise?
- Is it learnable in available time?
- Does it match team preferences?

**Ecosystem**:
- Is the community active?
- Are there good libraries/tools?
- Is documentation comprehensive?
- Is hiring feasible?

**Business Fit**:
- What's the licensing cost?
- What's the vendor lock-in risk?
- What's the long-term support outlook?
- Does it align with company strategy?

### Weighting

Assign weights based on context:
- Startup: Favor speed, team expertise
- Enterprise: Favor stability, support
- Open source project: Favor community, licensing
```

#### Step 3: Create Comparison Template

**Workflow includes**:
```markdown
### 3. Create Comparison Matrix

| Criterion | Weight | Option A | Option B | Option C |
|-----------|--------|----------|----------|----------|
| Technical fit | 30% | 8/10 | 7/10 | 6/10 |
| Team fit | 25% | 6/10 | 9/10 | 5/10 |
| Ecosystem | 25% | 9/10 | 7/10 | 6/10 |
| Business fit | 20% | 7/10 | 8/10 | 9/10 |
| **Weighted Score** | | **7.5** | **7.8** | **6.4** |

**Recommendation**: Option B (highest score, strong team fit)
```

#### Result

A skill that provides structure for technology decisions.

---

## Key Patterns Observed

### Pattern 1: Problem → Workflow → Validation

All skills follow this pattern:
1. Clear problem statement
2. Step-by-step workflow
3. Quality validation criteria

### Pattern 2: Inputs → Process → Outputs

Every skill has:
- Specific inputs (what you need)
- Clear process (what you do)
- Valuable outputs (what you get)

### Pattern 3: Examples Make It Real

Examples transform abstract workflows into concrete, executable processes.

### Pattern 4: Decision Frameworks Add Value

Providing decision criteria helps users make better choices during execution.

### Pattern 5: Relationships Enable Composition

Defining skill relationships enables workflow orchestration.

## Common Mistakes in Skill Creation

### Mistake 1: Too Generic

**Bad**: "Review the code"
**Good**: "Review API design for security, usability, and performance"

### Mistake 2: Missing Examples

**Bad**: Only abstract workflow
**Good**: Workflow + 2-3 realistic examples

### Mistake 3: Vague Outputs

**Bad**: "Recommendations"
**Good**: "Prioritized list of API improvements with rationale and estimated effort"

### Mistake 4: Incomplete Workflow

**Bad**: "1. Analyze, 2. Recommend, 3. Done"
**Good**: Detailed steps with decision points, quality checks, and validation

### Mistake 5: No Quality Criteria

**Bad**: No way to validate execution
**Good**: Clear checklist of quality criteria

## Tips for Different Skill Types

### Architecture Skills
- Focus on design decisions
- Include tradeoff analysis
- Provide decision frameworks
- Show composition with other skills

### Engineering Skills
- Be practical and actionable
- Include code examples where relevant
- Focus on quality and best practices
- Provide validation criteria

### Agentic Skills
- Be explicit and unambiguous
- Define clear inputs and outputs
- Include validation steps
- Show how agents compose

### Security Skills
- Be comprehensive
- Include threat models
- Provide risk assessment frameworks
- Reference standards (OWASP, etc.)

### Decision Skills
- Provide clear criteria
- Include comparison frameworks
- Show tradeoff analysis
- Document the decision (ADR)

## Conclusion

Creating skills becomes easier with practice. Start with simple skills, use the templates, and iterate based on feedback. The examples above show that skills can range from simple (dependency-analysis) to complex (agent-task-decomposition), but all follow the same basic structure.

Key takeaways:
1. Start with a clear problem
2. Create a repeatable workflow
3. Provide realistic examples
4. Define quality criteria
5. Test before submitting

Happy skill authoring!