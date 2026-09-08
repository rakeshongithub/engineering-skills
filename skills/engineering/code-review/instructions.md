# Code Review - Step-by-Step Instructions

## Overview

This skill helps you systematically review code to ensure quality, correctness, security, and maintainability before it's merged into the codebase.

## Step-by-Step Workflow

### Step 1: Understand the Context (5-10 minutes)

**Actions:**
1. Read the pull request description or commit message
2. Review the associated ticket, issue, or requirement
3. Understand the problem being solved
4. Check for related pull requests or discussions
5. Note any architectural decisions or constraints
6. Review the scope of changes (files changed, lines added/removed)

**Outputs:**
- Clear understanding of what the code is supposed to do
- Context for evaluating the implementation

### Step 2: Review for Correctness (10-20 minutes)

**Actions:**
1. **Functionality Check:**
   - Does the code implement all requirements?
   - Does it solve the stated problem?
   - Are all acceptance criteria met?

2. **Logic Verification:**
   - Are conditions correct (&&, ||, !)?
   - Are loops correct (start, end, increment)?
   - Are there off-by-one errors?
   - Is null/undefined handling correct?

3. **Edge Cases:**
   - Empty inputs (null, undefined, empty string, empty array)
   - Boundary values (0, -1, max values)
   - Invalid inputs
   - Concurrent access scenarios

4. **Error Handling:**
   - Are errors caught and handled appropriately?
   - Are error messages helpful?
   - Are resources cleaned up in error cases?
   - Are errors logged?

5. **Testing:**
   - Are there tests for the new code?
   - Do tests cover happy path and edge cases?
   - Do tests cover error conditions?
   - Are tests meaningful (not just for coverage)?
   - Do all tests pass?

**Outputs:**
- List of correctness issues (bugs, logic errors, missing edge cases)

### Step 3: Review for Code Quality (10-20 minutes)

**Actions:**
1. **Readability:**
   - Can you understand what the code does without extensive comments?
   - Are variable names descriptive (avoid `x`, `temp`, `data`)?
   - Are function names clear and verb-based?
   - Is the code structure logical and easy to follow?

2. **Maintainability:**
   - Are functions/methods single-purpose?
   - Is code modular and reusable?
   - Is there code duplication? (DRY principle)
   - Can the code be easily modified or extended?
   - Are magic numbers replaced with named constants?

3. **Complexity:**
   - Are functions too long (>50 lines)?
   - Is nesting depth reasonable (<4 levels)?
   - Can complex logic be simplified?
   - Are there overly clever solutions that sacrifice clarity?

4. **Comments and Documentation:**
   - Are comments used to explain "why", not "what"?
   - Is complex logic explained?
   - Are assumptions documented?
   - Is public API documented?
   - Are TODOs tracked and linked to tickets?

5. **Coding Standards:**
   - Does code follow team style guide?
   - Is formatting consistent?
   - Are naming conventions followed?
   - Are language idioms used correctly?

**Outputs:**
- List of code quality issues and improvement suggestions

### Step 4: Review for Security (5-10 minutes)

**Actions:**
1. **Input Validation:**
   - Are all inputs validated?
   - Is input sanitized to prevent injection attacks?
   - Are file uploads restricted and validated?
   - Are URL parameters validated?

2. **Authentication and Authorization:**
   - Is authentication required where needed?
   - Are authorization checks in place?
   - Are permissions checked before operations?
   - Is the principle of least privilege followed?

3. **Data Protection:**
   - Are secrets (API keys, passwords) hardcoded?
   - Is sensitive data encrypted in transit (HTTPS)?
   - Is sensitive data encrypted at rest?
   - Are secrets stored in secure vaults?
   - Is sensitive data logged?

4. **Common Vulnerabilities:**
   - SQL injection (use parameterized queries)
   - XSS (escape user input in HTML)
   - CSRF (use CSRF tokens)
   - Path traversal (validate file paths)
   - Command injection (avoid shell execution with user input)

5. **Dependencies:**
   - Are dependencies up-to-date?
   - Are there known vulnerabilities in dependencies?
   - Are dependencies from trusted sources?

**Outputs:**
- List of security issues and vulnerabilities

### Step 5: Review for Performance (5-10 minutes)

**Actions:**
1. **Database Queries:**
   - Are queries optimized (use indexes)?
   - Are N+1 queries avoided?
   - Is pagination used for large result sets?
   - Are queries necessary or can data be cached?

2. **Algorithms and Data Structures:**
   - Is the algorithm efficient for the use case?
   - Are data structures appropriate?
   - Are there unnecessary iterations?
   - Can operations be batched?

3. **Resource Management:**
   - Are resources (connections, files) properly closed?
   - Are there memory leaks?
   - Is memory usage reasonable?
   - Are large objects cached or reused?

4. **Caching:**
   - Is caching used where appropriate?
   - Are cache keys well-designed?
   - Is cache invalidation handled correctly?

5. **Scalability:**
   - Will the code scale with increased load?
   - Are there bottlenecks?
   - Can operations be parallelized?

**Outputs:**
- List of performance issues and optimization opportunities

### Step 6: Review for Design (5-10 minutes)

**Actions:**
1. **Architecture Fit:**
   - Does the code fit with existing architecture?
   - Are architectural patterns followed?
   - Is the code in the right place (layer, module)?

2. **Design Patterns:**
   - Are design patterns used appropriately?
   - Are patterns applied correctly?
   - Is the code over-engineered or under-engineered?

3. **Coupling and Cohesion:**
   - Is the code loosely coupled?
   - Are dependencies minimized?
   - Is cohesion high (related code together)?

4. **Abstractions:**
   - Are abstractions appropriate?
   - Are interfaces well-defined?
   - Is the code testable?

5. **Dependencies:**
   - Are dependencies necessary?
   - Are dependencies injected (not hardcoded)?
   - Are circular dependencies avoided?

**Outputs:**
- List of design issues and suggestions

### Step 7: Provide Constructive Feedback (10-15 minutes)

**Actions:**
1. **Categorize Issues:**
   - Critical: Must fix (bugs, security)
   - Important: Should fix (quality, best practices)
   - Suggestion: Nice to have (optimizations, style)
   - Question: Seeking clarification
   - Praise: Acknowledge good code

2. **Write Clear Comments:**
   - Be specific: Point to exact lines
   - Be constructive: Suggest solutions
   - Be respectful: Focus on code, not person
   - Explain why: Educate, don't just criticize
   - Provide examples: Show better alternatives

3. **Structure Feedback:**
   ```
   [Severity] Issue description
   
   Why this is a problem:
   [Explanation]
   
   Suggested fix:
   [Code example or approach]
   ```

4. **Balance Criticism and Praise:**
   - Point out good practices
   - Acknowledge clever solutions
   - Recognize improvements from previous reviews

**Outputs:**
- Well-structured, constructive review comments

### Step 8: Make a Decision (2-5 minutes)

**Actions:**
1. **Evaluate Overall Quality:**
   - Count critical, important, and suggestion-level issues
   - Consider the author's experience level
   - Consider the urgency of the change

2. **Choose Approval Status:**
   - **Approve**: No critical issues, minor suggestions only
   - **Request Changes**: Critical or multiple important issues
   - **Comment**: Feedback without blocking merge

3. **Communicate Decision:**
   - Summarize key issues
   - Explain approval decision
   - Provide clear next steps

**Outputs:**
- Approval decision with clear rationale

## Tips for Success

- **Run the code**: Don't just read it, test it locally if possible
- **Use tools**: Leverage linters, static analysis, and automated tests
- **Be timely**: Review within 24 hours to avoid blocking the author
- **Be thorough but efficient**: Focus on important issues, not nitpicks
- **Ask questions**: If something is unclear, ask rather than assume
- **Learn from the code**: Every review is a learning opportunity
- **Be consistent**: Apply the same standards to everyone
- **Follow up**: Check that your feedback was addressed

## Common Pitfalls to Avoid

- Reviewing too quickly without understanding the context
- Focusing on style over substance
- Being vague ("this is bad" without explaining why)
- Rewriting the entire solution in comments
- Approving code with known issues to avoid conflict
- Blocking code for personal preferences
- Not running or testing the code
- Ignoring tests or reviewing them superficially
- Being overly critical without acknowledging good code
- Inconsistent application of standards

## Review Time Estimates

By change size:
- **Small** (<100 lines): 15-30 minutes
- **Medium** (100-500 lines): 30-60 minutes
- **Large** (500-1000 lines): 1-2 hours
- **Very Large** (>1000 lines): Request to split into smaller PRs

## Example Review Comment Templates

### Critical Issue
```
[Critical] Potential SQL injection vulnerability

This query concatenates user input directly into SQL, which allows SQL injection attacks.

Suggested fix:
Use parameterized queries instead:

Before:
query = `SELECT * FROM users WHERE id = ${userId}`

After:
query = 'SELECT * FROM users WHERE id = ?'
db.execute(query, [userId])
```

### Important Issue
```
[Important] Missing error handling

This async function doesn't handle errors, which could cause unhandled promise rejections.

Suggested fix:
Wrap in try-catch or add .catch():

try {
  const result = await fetchData();
  return result;
} catch (error) {
  logger.error('Failed to fetch data', error);
  throw error;
}
```

### Suggestion
```
[Suggestion] Consider extracting this logic

This function is doing two things: validation and processing. Consider extracting validation into a separate function for better testability and reusability.

Example:
function validateInput(data) { ... }
function processData(data) { ... }
```

### Praise
```
[Praise] Excellent error handling!

I really like how you've handled all edge cases and provided helpful error messages. This will make debugging much easier.
```