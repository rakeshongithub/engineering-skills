# Agent Task Decomposition - Step-by-Step Instructions

## Overview

This guide provides detailed, step-by-step instructions for breaking down complex engineering problems into agent-executable tasks.

## Prerequisites

- Clear understanding of the engineering problem
- Requirements documentation
- Knowledge of available agent capabilities
- Understanding of the existing codebase (if applicable)

## Step-by-Step Workflow

### Step 1: Understand the Problem Scope (15-20 minutes)

**Objective**: Gain a complete understanding of what needs to be accomplished.

**Actions**:

1. Read the engineering problem statement thoroughly
2. Review all requirements (functional and non-functional)
3. Identify constraints:
   - Technical constraints (technology stack, performance, scalability)
   - Resource constraints (time, budget, team size)
   - Business constraints (deadlines, compliance, dependencies)
4. Clarify ambiguities:
   - Ask questions about unclear requirements
   - Identify missing information
   - Document assumptions
5. Define success criteria:
   - What does "done" look like?
   - How will success be measured?
   - What are the acceptance criteria?

**Output**:
- Problem statement (1-2 paragraphs)
- List of requirements
- List of constraints
- Success criteria
- List of assumptions

**Example**:
```
Problem: Implement a user authentication system with OAuth2 support

Requirements:
- Support email/password authentication
- Support OAuth2 (Google, GitHub)
- JWT-based session management
- Password reset functionality
- Rate limiting on login attempts

Constraints:
- Must use existing PostgreSQL database
- Must complete within 2 weeks
- Must support 10,000 concurrent users

Success Criteria:
- All authentication methods working
- Security review passed
- Load testing passed
- Documentation complete
```

### Step 2: Identify Major Phases (10-15 minutes)

**Objective**: Break the problem into high-level phases.

**Actions**:

1. Identify natural phases in the work:
   - Discovery/Analysis
   - Design/Planning
   - Implementation
   - Testing/Validation
   - Documentation
   - Deployment/Integration
2. For each phase, estimate relative effort (S/M/L/XL)
3. Identify dependencies between phases
4. Determine which phases can overlap

**Output**:
- List of phases with descriptions
- Effort estimates per phase
- Phase dependency diagram

**Example**:
```
Phase 1: Design (M)
- Design database schema
- Design API endpoints
- Design authentication flow

Phase 2: Core Implementation (L)
- Implement user model and database
- Implement email/password auth
- Implement JWT token management

Phase 3: OAuth Implementation (M)
- Implement Google OAuth
- Implement GitHub OAuth

Phase 4: Security Features (M)
- Implement rate limiting
- Implement password reset
- Security hardening

Phase 5: Testing (M)
- Unit tests
- Integration tests
- Load tests

Phase 6: Documentation (S)
- API documentation
- Setup guide
- Security documentation
```

### Step 3: Decompose Phases into Tasks (30-45 minutes)

**Objective**: Break each phase into concrete, agent-executable tasks.

**Actions**:

For each phase:

1. List all the work items needed
2. Group related work items
3. Create tasks with:
   - Clear objective
   - Defined inputs
   - Expected outputs
   - Size estimate (15min - 2 hours)
4. Ensure each task is:
   - **Atomic**: Does one thing well
   - **Independent**: Can be executed without waiting for other tasks (where possible)
   - **Testable**: Has clear validation criteria
   - **Sized appropriately**: Not too large, not too small

**Task Template**:
```
Task ID: TASK-001
Name: [Clear, action-oriented name]
Phase: [Which phase this belongs to]
Description: [What needs to be done]
Inputs: [What information/files are needed]
Outputs: [What will be produced]
Size: [S/M/L - 15min/1hr/2hr]
Dependencies: [Which tasks must complete first]
Validation: [How to verify completion]
```

**Output**:
- Detailed task list for all phases
- Task descriptions following the template

**Example**:
```
Task ID: AUTH-001
Name: Design user database schema
Phase: Design
Description: Create database schema for user table including fields for email, password hash, OAuth IDs, and metadata
Inputs: Requirements document, existing database schema
Outputs: SQL migration file, schema diagram
Size: M (1 hour)
Dependencies: None
Validation: Schema includes all required fields, follows naming conventions, has proper indexes

Task ID: AUTH-002
Name: Implement user model
Phase: Core Implementation
Description: Create User model class with methods for CRUD operations
Inputs: Database schema (AUTH-001)
Outputs: User model code, unit tests
Size: M (1 hour)
Dependencies: AUTH-001
Validation: All CRUD operations work, tests pass, follows code standards
```

### Step 4: Define Task Dependencies (15-20 minutes)

**Objective**: Identify which tasks depend on others and create an execution plan.

**Actions**:

1. For each task, identify:
   - **Hard dependencies**: Tasks that must complete before this one
   - **Soft dependencies**: Tasks that should ideally complete first but aren't blockers
   - **No dependencies**: Tasks that can start immediately
2. Create a dependency graph:
   - Use a tool (Mermaid, draw.io) or simple text representation
   - Show task relationships visually
3. Identify the critical path:
   - Longest sequence of dependent tasks
   - Determines minimum completion time
4. Find parallelization opportunities:
   - Tasks with no dependencies can run in parallel
   - Tasks with the same dependencies can run in parallel

**Output**:
- Dependency graph
- Critical path identified
- List of tasks that can run in parallel

**Example**:
```
Critical Path:
AUTH-001 → AUTH-002 → AUTH-003 → AUTH-007 → AUTH-010
(5 tasks × 1 hour = 5 hours minimum)

Parallel Execution:
Group 1 (can start immediately):
- AUTH-001 (Design schema)
- AUTH-004 (Design API endpoints)
- AUTH-005 (Design OAuth flow)

Group 2 (after Group 1):
- AUTH-002 (User model)
- AUTH-006 (JWT utilities)

Group 3 (after Group 2):
- AUTH-003 (Email/password auth)
- AUTH-008 (Google OAuth)
- AUTH-009 (GitHub OAuth)
```

### Step 5: Define Handoff Points (10-15 minutes)

**Objective**: Specify how information flows between tasks.

**Actions**:

1. For each task dependency, define:
   - What information is passed?
   - In what format?
   - Where is it stored?
   - How is completeness verified?
2. Create handoff specifications:
   - Input requirements for receiving task
   - Output requirements for producing task
   - Validation criteria
3. Plan for handoff failures:
   - What if the output is incomplete?
   - What if the format is wrong?
   - How to request corrections?

**Output**:
- Handoff specifications for each dependency
- Validation criteria
- Failure handling procedures

**Example**:
```
Handoff: AUTH-001 → AUTH-002
Producer: AUTH-001 (Design schema)
Consumer: AUTH-002 (Implement user model)

Artifact:
- SQL migration file (migrations/001_create_users.sql)
- Schema documentation (docs/schema.md)

Validation:
- Migration file runs without errors
- All required fields present
- Indexes defined
- Documentation complete

Failure Handling:
- If validation fails, AUTH-002 cannot start
- AUTH-001 must be corrected
- Re-validate before proceeding
```

### Step 6: Add Validation Criteria (15-20 minutes)

**Objective**: Define how to verify each task is complete and correct.

**Actions**:

For each task, define:

1. **Completeness criteria**:
   - All required outputs produced?
   - All files created/modified?
   - All documentation written?
2. **Correctness criteria**:
   - Code compiles/runs?
   - Tests pass?
   - Meets requirements?
3. **Quality criteria**:
   - Code quality standards met?
   - Security best practices followed?
   - Performance acceptable?
4. **Integration criteria**:
   - Works with other components?
   - APIs compatible?
   - No breaking changes?

**Output**:
- Validation checklist for each task
- Acceptance criteria
- Quality gates

**Example**:
```
Task: AUTH-002 (Implement user model)

Completeness:
- [ ] User model class created
- [ ] CRUD methods implemented
- [ ] Unit tests written
- [ ] Documentation added

Correctness:
- [ ] All tests pass
- [ ] No compilation errors
- [ ] Meets requirements from AUTH-001

Quality:
- [ ] Code follows style guide
- [ ] Test coverage > 80%
- [ ] No security vulnerabilities
- [ ] Performance acceptable (< 100ms per operation)

Integration:
- [ ] Works with database schema
- [ ] Compatible with existing code
- [ ] No breaking changes
```

### Step 7: Plan for Failure and Recovery (10-15 minutes)

**Objective**: Prepare for task failures and define recovery strategies.

**Actions**:

1. Identify high-risk tasks:
   - Complex tasks
   - Tasks with external dependencies
   - Tasks on the critical path
2. For each high-risk task, define:
   - What could go wrong?
   - How to detect failure?
   - What to do if it fails?
   - How to roll back?
3. Create retry policies:
   - Which tasks can be retried?
   - How many retries?
   - What changes between retries?
4. Plan for partial completion:
   - Can tasks be partially completed?
   - How to resume from partial state?

**Output**:
- Risk assessment for tasks
- Failure handling strategies
- Rollback procedures
- Retry policies

**Example**:
```
High-Risk Task: AUTH-008 (Google OAuth)

Potential Failures:
1. OAuth configuration errors
   - Detection: Integration tests fail
   - Recovery: Review Google OAuth docs, verify credentials
   - Rollback: Revert code changes

2. Token validation issues
   - Detection: Token verification fails
   - Recovery: Debug token flow, check JWT implementation
   - Rollback: Use previous working version

Retry Policy:
- Max retries: 3
- Between retries: Review error logs, adjust configuration
- If all retries fail: Escalate to human review

Partial Completion:
- OAuth flow implemented but token validation failing
- Can resume by fixing validation logic only
- No need to redo entire OAuth implementation
```

### Step 8: Optimize Task Structure (10-15 minutes)

**Objective**: Refine the task list for optimal execution.

**Actions**:

1. Review task list for:
   - Tasks that are too small (< 15 minutes) → Combine
   - Tasks that are too large (> 2 hours) → Split
   - Unnecessary dependencies → Remove
   - Opportunities to parallelize → Exploit
2. Reorder tasks to:
   - Minimize wait time
   - Maximize parallel execution
   - Reduce handoff overhead
3. Optimize critical path:
   - Can any critical path tasks be parallelized?
   - Can dependencies be removed?
   - Can tasks be made smaller?

**Output**:
- Optimized task list
- Improved execution plan
- Reduced total execution time

**Example**:
```
Before Optimization:
- 25 tasks
- 8 on critical path
- Estimated time: 20 hours
- Max parallelization: 3 tasks

After Optimization:
- 20 tasks (combined 5 small tasks)
- 6 on critical path (removed 2 dependencies)
- Estimated time: 15 hours
- Max parallelization: 5 tasks

Changes:
1. Combined AUTH-011, AUTH-012, AUTH-013 (all small documentation tasks)
2. Removed dependency between AUTH-008 and AUTH-009 (can implement OAuth providers in parallel)
3. Split AUTH-010 (too large) into AUTH-010a and AUTH-010b
```

### Step 9: Document the Decomposition (15-20 minutes)

**Objective**: Create clear, comprehensive documentation for the task plan.

**Actions**:

1. Create a task catalog:
   - List all tasks with full details
   - Include all metadata
   - Add context and rationale
2. Create execution plan:
   - Show task order
   - Show dependencies
   - Show parallelization
3. Create handoff documentation:
   - Specify all handoffs
   - Include validation criteria
4. Add examples where helpful:
   - Show sample inputs
   - Show expected outputs

**Output**:
- Complete task documentation
- Execution plan
- Handoff specifications
- Examples

**Template**:
```markdown
# Task Decomposition: [Project Name]

## Overview
[Brief description of the project]

## Phases
[List of phases with descriptions]

## Tasks

### Phase 1: [Phase Name]

#### Task [ID]: [Name]
- **Description**: [What needs to be done]
- **Inputs**: [Required inputs]
- **Outputs**: [Expected outputs]
- **Size**: [S/M/L]
- **Dependencies**: [Task IDs]
- **Validation**: [How to verify]
- **Notes**: [Additional context]

[Repeat for all tasks]

## Execution Plan

### Critical Path
[List tasks on critical path]

### Parallel Execution Groups
[List tasks that can run in parallel]

### Estimated Timeline
[Timeline with milestones]

## Handoffs

### [Task A] → [Task B]
- **Artifact**: [What is passed]
- **Format**: [How it's structured]
- **Validation**: [How to verify]

[Repeat for all handoffs]
```

### Step 10: Validate the Decomposition (10-15 minutes)

**Objective**: Ensure the task plan is complete, correct, and executable.

**Actions**:

1. Review against requirements:
   - Do tasks cover all requirements?
   - Is anything missing?
   - Is anything unnecessary?
2. Validate task sizing:
   - Are tasks appropriately sized?
   - Are any tasks too large or too small?
3. Check dependencies:
   - Are dependencies correct?
   - Are there circular dependencies?
   - Are dependencies necessary?
4. Verify executability:
   - Can agents execute these tasks?
   - Is enough context provided?
   - Are instructions clear?
5. Assess achievability:
   - Is the plan realistic?
   - Are estimates reasonable?
   - Are resources available?

**Output**:
- Validated task decomposition
- List of issues found (if any)
- Corrected task plan

**Validation Checklist**:
```
- [ ] All requirements covered
- [ ] No missing tasks
- [ ] No unnecessary tasks
- [ ] All tasks appropriately sized
- [ ] Dependencies correct and necessary
- [ ] No circular dependencies
- [ ] Critical path identified
- [ ] Parallelization optimized
- [ ] Handoffs well-defined
- [ ] Validation criteria clear
- [ ] Failure handling planned
- [ ] Documentation complete
- [ ] Plan is achievable
- [ ] Estimates are reasonable
```

## Tips for Success

### Task Sizing
- Start with larger tasks and break them down iteratively
- Use the "can this be done in one agent execution?" test
- Consider agent token limits and execution time
- Err on the side of smaller tasks for complex work

### Dependency Management
- Minimize dependencies where possible
- Make tasks as independent as possible
- Provide complete context to reduce dependencies
- Use clear interfaces between tasks

### Validation
- Define validation criteria upfront, not after the fact
- Make validation objective and measurable
- Include both functional and quality criteria
- Plan for automated validation where possible

### Documentation
- Write task descriptions for someone unfamiliar with the project
- Include examples and context
- Be specific about inputs and outputs
- Explain the "why" not just the "what"

## Common Pitfalls to Avoid

1. **Vague task descriptions**: "Implement authentication" is too vague; "Implement email/password authentication with bcrypt hashing and rate limiting" is specific
2. **Missing context**: Assuming agents know things they don't; always provide complete context
3. **Ignoring handoffs**: Not specifying how information flows between tasks leads to integration failures
4. **Over-optimistic sizing**: Tasks often take longer than expected; add buffer
5. **Forgetting validation**: Without clear validation criteria, you can't verify completion
6. **No failure planning**: Things will go wrong; plan for it
7. **Ignoring agent limitations**: Creating tasks that require capabilities agents don't have

## Next Steps

After completing task decomposition:

1. Review with stakeholders
2. Get approval on the plan
3. Proceed to agent-workflow-design to orchestrate execution
4. Use agent-context-engineering to prepare context for each task
5. Use agent-instruction-design to create clear instructions
6. Begin task execution
7. Monitor progress and adjust as needed

## Additional Resources

- See examples.md for complete examples
- See SKILL.md for conceptual overview
- See related skills for complementary techniques