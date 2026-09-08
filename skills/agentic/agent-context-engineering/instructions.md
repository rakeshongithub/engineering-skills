# Agent Context Engineering - Step-by-Step Instructions

## Overview
This guide provides executable instructions for engineering effective context packages that enable AI agents to execute tasks successfully.

---

## Prerequisites
- Completed architecture-discovery to understand the system
- Completed requirements-analysis to know what needs to be accomplished
- Completed agent-task-decomposition to break down the work
- Access to codebase, documentation, and relevant artifacts
- Understanding of the agent's capabilities and limitations

---

## Step-by-Step Workflow

### Step 1: Analyze Task Requirements (15-20 minutes)

**Goal**: Understand exactly what the agent needs to know.

**Instructions**:
1. Read the task definition and success criteria
2. List every operation the agent will perform (read, write, analyze, decide)
3. Identify all decision points where context influences the outcome
4. Map operations to specific code artifacts (files, modules, functions)
5. List domain knowledge requirements (business rules, constraints)
6. Consider edge cases and error scenarios

**Deliverable**: Task requirements document

**Example**:
```markdown
## Task: Implement user authentication endpoint

### Operations
- Read existing auth patterns
- Write new endpoint handler
- Integrate with user service
- Add validation logic
- Write tests

### Decision Points
- Which auth pattern to use (JWT vs session)
- Where to place the endpoint (which router)
- What validation rules to apply
- How to handle errors

### Code Artifacts
- src/routes/auth.js (existing patterns)
- src/services/userService.js (user operations)
- src/middleware/validation.js (validation patterns)
- tests/auth.test.js (test patterns)

### Domain Knowledge
- Password requirements (min length, complexity)
- Session duration policy
- Rate limiting rules
```

---

### Step 2: Identify Context Sources (20-30 minutes)

**Goal**: Locate where all needed context exists.

**Instructions**:
1. For each code artifact, note the file path and relevant sections
2. Search for architectural documentation (ADRs, design docs, diagrams)
3. Find examples of similar implementations
4. Locate tests that demonstrate expected behavior
5. Identify implicit context (coding standards, naming conventions)
6. Document where each piece of context lives

**Deliverable**: Context source inventory

**Example**:
```markdown
## Context Sources

### Code
- src/routes/auth.js:15-45 (existing login endpoint)
- src/services/userService.js:30-60 (user lookup)
- src/middleware/validation.js:10-25 (validation pattern)

### Documentation
- docs/architecture/auth-strategy.md (auth approach)
- docs/adr/003-jwt-authentication.md (decision rationale)

### Examples
- src/routes/products.js:20-40 (similar endpoint pattern)
- tests/auth.test.js:15-50 (auth test pattern)

### Implicit Context
- .eslintrc.json (code style)
- src/routes/README.md (routing conventions)
- package.json (available libraries)
```

---

### Step 3: Assess Context Scope (15-20 minutes)

**Goal**: Determine how much context to provide.

**Instructions**:
1. Calculate approximate token/character count for each context source
2. Prioritize context:
   - Must-have: Critical for task success
   - Should-have: Improves quality but not essential
   - Nice-to-have: Helpful but can be retrieved on-demand
3. Check agent's context window limit
4. Decide what to include upfront vs. make retrievable
5. Balance completeness with cognitive load

**Deliverable**: Prioritized context inventory with size estimates

**Example**:
```markdown
## Context Scope

### Must-Have (Upfront) - ~3,000 tokens
- Existing auth endpoint pattern (500 tokens)
- User service interface (300 tokens)
- Validation middleware pattern (400 tokens)
- Auth strategy overview (800 tokens)
- Error handling pattern (500 tokens)
- Test pattern (500 tokens)

### Should-Have (Upfront) - ~1,500 tokens
- Similar endpoint example (600 tokens)
- Routing conventions (400 tokens)
- ADR on JWT (500 tokens)

### Nice-to-Have (On-Demand) - ~2,000 tokens
- Full user service implementation
- All existing tests
- Complete API documentation

Total Upfront: ~4,500 tokens (within 8K limit)
```

---

### Step 4: Structure Context Hierarchically (20-30 minutes)

**Goal**: Organize context from high-level to detailed.

**Instructions**:
1. Create overview layer: system architecture, purpose, key concepts
2. Create interface layer: APIs, contracts, public interfaces
3. Create implementation layer: specific code, algorithms, logic
4. Create example layer: test cases, usage examples, patterns
5. Create metadata layer: dependencies, versions, constraints
6. Organize in a logical reading order

**Deliverable**: Hierarchical context structure

**Example**:
```markdown
# Context Package: User Authentication Endpoint

## Layer 1: Overview
- System uses JWT-based authentication
- Auth endpoints live in src/routes/auth.js
- User data managed by UserService
- Validation via middleware

## Layer 2: Interfaces
- POST /auth/login (existing)
- POST /auth/register (to be created)
- UserService.createUser(data) → Promise<User>
- UserService.findByEmail(email) → Promise<User|null>

## Layer 3: Implementation
[Code snippets from existing auth endpoint]
[UserService implementation details]
[Validation middleware code]

## Layer 4: Examples
[Existing login endpoint]
[Similar product endpoint]
[Test examples]

## Layer 5: Metadata
- Dependencies: jsonwebtoken, bcrypt, express-validator
- Constraints: Password min 8 chars, email must be unique
- Related files: auth.js, userService.js, validation.js
```

---

### Step 5: Design Context Retrieval Strategy (15-20 minutes)

**Goal**: Define how the agent accesses context during execution.

**Instructions**:
1. Identify context for initial prompt (must-have)
2. Design retrieval mechanisms for on-demand context:
   - File reading tools
   - Search/grep tools
   - Documentation lookup
   - RAG systems
3. Define when to refresh context (code changes, new requirements)
4. Plan for context caching and reuse
5. Design fallback strategies (what if context unavailable)

**Deliverable**: Context retrieval plan

**Example**:
```markdown
## Retrieval Strategy

### Initial Prompt (Upfront)
- Auth strategy overview
- Existing endpoint pattern
- User service interface
- Validation pattern
- Test pattern

### On-Demand Retrieval
- Full user service implementation: read_file("src/services/userService.js")
- All existing tests: read_file("tests/auth.test.js")
- API documentation: search_docs("authentication API")
- Similar endpoints: grep_codebase("router.post.*auth")

### Refresh Triggers
- Before execution: verify context sources haven't changed
- On error: fetch additional context based on error type
- On request: agent can request specific context

### Caching
- Cache static context (architecture, ADRs) for session
- Re-fetch code on each execution (may have changed)
- Cache validation results to avoid re-reading

### Fallbacks
- If file not found: search for similar patterns
- If doc missing: use code comments and tests
- If example unavailable: provide general pattern
```

---

### Step 6: Enrich Context with Metadata (15-20 minutes)

**Goal**: Add information that helps the agent understand context quality and usage.

**Instructions**:
1. Add provenance: source file, creation date, author
2. Add confidence levels: how current and reliable the context is
3. Add relationships: dependencies, impacts, related code
4. Add usage guidance: how to interpret and apply the context
5. Add constraints: limitations, assumptions, requirements

**Deliverable**: Metadata-enriched context package

**Example**:
```markdown
## Context: Existing Login Endpoint

**Source**: src/routes/auth.js:15-45  
**Last Modified**: 2026-09-01  
**Confidence**: High (actively used, well-tested)  
**Dependencies**: UserService, JWT library, validation middleware  
**Related**: src/routes/auth.js:50-80 (logout endpoint)  

**Usage Guidance**:
- Follow this pattern for new auth endpoints
- Reuse validation middleware approach
- Use same error handling structure

**Constraints**:
- Must validate input before processing
- Must hash passwords before storage
- Must return JWT token on success
- Must rate-limit to prevent brute force

```javascript
// Existing login endpoint (pattern to follow)
router.post('/login', 
  validateLoginInput,
  async (req, res) => {
    // Pattern implementation
  }
);
```
```

---

### Step 7: Validate Context Completeness (20-30 minutes)

**Goal**: Ensure no critical gaps exist.

**Instructions**:
1. Walk through the task step-by-step with only the context package
2. At each decision point, verify context provides the answer
3. Check for missing dependencies, imports, or related code
4. Verify examples cover the task's complexity and edge cases
5. Have someone unfamiliar with the code review the context
6. Simulate agent execution or do a dry run

**Deliverable**: Context validation report

**Example**:
```markdown
## Context Validation Report

### Task Walkthrough
1. Create new endpoint ✓ (pattern provided)
2. Validate input ✓ (validation middleware shown)
3. Check if user exists ✓ (UserService interface documented)
4. Hash password ✓ (example shows bcrypt usage)
5. Save user ✓ (UserService.createUser documented)
6. Generate JWT ✓ (JWT generation pattern shown)
7. Return response ✓ (response format in example)
8. Handle errors ✓ (error handling pattern provided)
9. Write tests ✓ (test pattern included)

### Gaps Identified
- Missing: Email uniqueness check logic → ADD
- Missing: Password complexity requirements → ADD
- Ambiguous: Where to place new endpoint in file → CLARIFY

### Gap Resolution
- Added UserService.findByEmail example
- Added password validation rules from docs
- Clarified endpoint placement convention

### Validation Result
✓ Context is complete for task execution
```

---

### Step 8: Optimize Context for Agent Consumption (15-20 minutes)

**Goal**: Format context for maximum comprehension and efficiency.

**Instructions**:
1. Use consistent formatting (markdown, code blocks, headers)
2. Add clear section headers and navigation
3. Highlight critical information (warnings, constraints, requirements)
4. Remove redundancy and irrelevant details
5. Structure for sequential reading or random access
6. Add table of contents for long context

**Deliverable**: Optimized, agent-ready context package

**Example**:
```markdown
# Context Package: User Registration Endpoint

## Table of Contents
1. [Overview](#overview)
2. [Architecture](#architecture)
3. [Interfaces](#interfaces)
4. [Implementation Patterns](#implementation-patterns)
5. [Examples](#examples)
6. [Constraints](#constraints)

---

## Overview
🎯 **Goal**: Implement POST /auth/register endpoint following existing patterns.

**Key Points**:
- Use JWT authentication (see ADR-003)
- Follow validation middleware pattern
- Reuse UserService for data operations
- Match existing endpoint structure

---

## Architecture

```
Client Request
    ↓
Express Router (src/routes/auth.js)
    ↓
Validation Middleware (src/middleware/validation.js)
    ↓
Route Handler
    ↓
UserService (src/services/userService.js)
    ↓
Database
```

---

## Interfaces

### UserService.createUser()
```typescript
creatUser(data: {
  email: string;
  password: string;
  name: string;
}): Promise<User>
```

**⚠️ Critical**: Password must be hashed before calling this method.

---

## Implementation Patterns

### Pattern: Auth Endpoint
```javascript
router.post('/endpoint', 
  validationMiddleware,
  async (req, res) => {
    try {
      // 1. Extract data
      // 2. Business logic
      // 3. Return response
    } catch (error) {
      // Error handling
    }
  }
);
```

---

## Examples

### Example 1: Existing Login Endpoint
[Clean, annotated code example]

### Example 2: Validation Middleware
[Clean, annotated code example]

---

## Constraints

⚠️ **Must Follow**:
- Email must be unique (check before creating)
- Password min 8 characters, must include number and special char
- Hash password with bcrypt (salt rounds: 10)
- Rate limit: 5 requests per 15 minutes per IP
- Return 201 on success, 400 on validation error, 409 on duplicate
```

---

### Step 9: Implement Context Versioning (10-15 minutes)

**Goal**: Ensure context stays current as code evolves.

**Instructions**:
1. Tag context with version number or timestamp
2. Define update triggers (code changes, new requirements)
3. Implement change detection for context sources
4. Create context diff mechanisms
5. Plan for context deprecation and archival

**Deliverable**: Context versioning strategy

**Example**:
```markdown
## Context Versioning Strategy

### Version Tagging
```json
{
  "context_package": "user-registration-endpoint",
  "version": "1.2.0",
  "created": "2026-09-08T10:00:00Z",
  "sources": [
    {
      "file": "src/routes/auth.js",
      "last_modified": "2026-09-01T14:30:00Z",
      "git_hash": "a3f5c2d"
    },
    {
      "file": "src/services/userService.js",
      "last_modified": "2026-08-28T09:15:00Z",
      "git_hash": "b7e9f1a"
    }
  ]
}
```

### Update Triggers
- Source file modified → regenerate context
- New requirement added → update context
- Bug fix in example code → update context
- Architecture change → update context

### Change Detection
- Monitor git commits to source files
- Compare file modification timestamps
- Track git hashes of included code
- Alert when context is stale (>7 days)

### Context Diff
```markdown
## Changes in v1.2.0 (from v1.1.0)
- Updated: UserService interface (added email validation)
- Added: Password complexity requirements
- Removed: Deprecated session-based auth example
```

### Deprecation
- Mark context as deprecated when source code is refactored
- Provide migration path to new context
- Archive old context for historical reference
```

---

### Step 10: Monitor and Refine Context Quality (Ongoing)

**Goal**: Continuously improve context based on agent performance.

**Instructions**:
1. Track agent success rates with different context configurations
2. Analyze agent errors to identify context gaps or ambiguities
3. Collect feedback from agent outputs and human reviews
4. A/B test different context structures or content
5. Iterate on context design based on empirical results

**Deliverable**: Context quality metrics and improvement plan

**Example**:
```markdown
## Context Quality Metrics (Week of 2026-09-01)

### Success Metrics
- Agent success rate: 92% (target: >90%) ✓
- Context sufficiency: 3% errors due to missing context (target: <5%) ✓
- Context utilization: 78% of provided context used (target: >70%) ✓
- Average task completion time: 8 minutes (baseline: 12 minutes) ✓

### Error Analysis
- 3 failures due to missing email uniqueness check → Added to context
- 2 failures due to ambiguous error handling → Clarified pattern
- 1 failure due to outdated validation rules → Updated context

### A/B Test Results
- Hierarchical structure vs. flat: +15% comprehension (hierarchical wins)
- Inline examples vs. separate section: +8% success rate (inline wins)
- Minimal vs. comprehensive: +12% success rate (comprehensive wins)

### Improvement Plan
1. Add email uniqueness check to all user creation contexts
2. Create reusable error handling pattern library
3. Implement automated context freshness checks
4. Standardize on hierarchical structure with inline examples
```

---

## Validation Checklist

Before finalizing context, verify:

- [ ] All task requirements have corresponding context
- [ ] Dependencies and related code are included
- [ ] Domain knowledge and business rules are documented
- [ ] Examples cover common and edge cases
- [ ] Error handling and constraints are explicit
- [ ] Information is current and accurate
- [ ] No contradictions or ambiguities
- [ ] Appropriate level of detail
- [ ] Clear relationships between context elements
- [ ] Well-structured and easy to navigate
- [ ] Consistent formatting throughout
- [ ] Critical information is highlighted
- [ ] Fits within agent's context window
- [ ] Versioned and timestamped
- [ ] Update triggers defined
- [ ] Sources are traceable

---

## Common Pitfalls

1. **Too much context**: Agent overwhelmed, performance degrades
   - Solution: Prioritize ruthlessly, use on-demand retrieval

2. **Stale context**: Agent uses outdated patterns
   - Solution: Implement versioning and change detection

3. **Missing relationships**: Agent misses side effects
   - Solution: Add dependency maps and impact analysis

4. **Ambiguous context**: Agent interprets incorrectly
   - Solution: Use precise language, add examples, clarify constraints

5. **Poor structure**: Agent can't find needed information
   - Solution: Hierarchical organization, clear headers, table of contents

---

## Next Steps

After completing context engineering:

1. Proceed to **agent-instruction-design** to write clear instructions using this context
2. Use **agent-tool-selection** to choose tools that can access and use the context
3. Apply **agent-handoff-design** if context needs to be passed between agents
4. Implement **agent-guardrails** to ensure context includes safety constraints
5. Use **agent-observability** to monitor how agents use the context

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08
