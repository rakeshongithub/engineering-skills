# Code Review

## Purpose

Systematically review code for quality, correctness, security, and maintainability.

## When to Use

- Before merging code into main/production branches
- When reviewing pull requests or merge requests
- During pair programming sessions
- When onboarding new team members (review their code)
- When evaluating code quality in legacy systems
- Before major releases to ensure quality standards
- When establishing or enforcing coding standards

## When NOT to Use

- For architecture-level reviews (use architecture-review instead)
- For security-focused reviews of entire systems (use security-architecture-review instead)
- For automated code quality checks (use linters and static analysis tools)
- For trivial changes (typo fixes, formatting)
- When immediate hotfixes are needed (review after deployment)

## Inputs

- **code-changes**: The code to be reviewed (diff, pull request, commit)
- **requirements**: What the code is supposed to do
- **coding-standards**: Team's coding conventions and style guides
- **context**: Related code, architecture decisions, previous discussions

## Expected Outputs

- **review-comments**: Specific feedback on the code
- **issues-found**: List of problems categorized by severity
- **approval-status**: Approve, Request Changes, or Comment
- **learning-points**: Educational feedback for the author

## Workflow

### 1. Understand the Context (5-10 minutes)

- Read the description/ticket associated with the code
- Understand what problem the code is solving
- Review any related requirements or design documents
- Check for related pull requests or previous discussions
- Understand the scope of changes

### 2. Review for Correctness (10-20 minutes)

**Functionality:**
- Does the code do what it's supposed to do?
- Are all requirements implemented?
- Are edge cases handled?
- Are error conditions handled properly?

**Logic:**
- Is the logic correct and sound?
- Are there any logical errors or bugs?
- Are conditions and loops correct?
- Are there any off-by-one errors?

**Testing:**
- Are there tests for the new code?
- Do tests cover edge cases and error conditions?
- Are tests meaningful and not just for coverage?
- Do all tests pass?

### 3. Review for Code Quality (10-20 minutes)

**Readability:**
- Is the code easy to read and understand?
- Are variable and function names descriptive?
- Is the code self-documenting?
- Are comments used appropriately (why, not what)?

**Maintainability:**
- Is the code modular and well-organized?
- Are functions/methods single-purpose?
- Is there code duplication (DRY principle)?
- Is the code easy to modify and extend?

**Complexity:**
- Is the code unnecessarily complex?
- Can complex logic be simplified?
- Are there overly long functions or classes?
- Is nesting depth reasonable?

**Standards:**
- Does the code follow team coding standards?
- Is formatting consistent?
- Are naming conventions followed?
- Are language idioms used correctly?

### 4. Review for Security (5-10 minutes)

- Are inputs validated and sanitized?
- Is authentication and authorization handled correctly?
- Are secrets or sensitive data exposed?
- Are there SQL injection or XSS vulnerabilities?
- Is data encrypted when necessary?
- Are dependencies secure and up-to-date?

### 5. Review for Performance (5-10 minutes)

- Are there obvious performance issues?
- Are database queries optimized?
- Is caching used appropriately?
- Are there memory leaks or resource leaks?
- Are expensive operations necessary?
- Is the code scalable?

### 6. Review for Design (5-10 minutes)

- Does the code fit well with existing architecture?
- Are design patterns used appropriately?
- Is the code loosely coupled?
- Are abstractions appropriate?
- Is the code testable?
- Are dependencies managed well?

### 7. Provide Constructive Feedback (10-15 minutes)

**Structure your comments:**
- Be specific: Point to exact lines and explain the issue
- Be constructive: Suggest improvements, don't just criticize
- Be respectful: Focus on the code, not the person
- Categorize: Mark critical issues vs. suggestions
- Educate: Explain why something is an issue

**Comment types:**
- **Critical**: Must be fixed before merging (bugs, security issues)
- **Important**: Should be fixed (quality issues, best practices)
- **Suggestion**: Nice to have (optimizations, style preferences)
- **Question**: Seeking clarification or understanding
- **Praise**: Acknowledge good code and practices

### 8. Make a Decision (2-5 minutes)

**Approve:**
- Code meets all quality standards
- No critical or important issues
- Ready to merge

**Request Changes:**
- Critical issues must be fixed
- Important issues should be addressed
- Re-review needed after changes

**Comment:**
- Minor suggestions only
- No blocking issues
- Author can merge after considering feedback

## Decision Framework

### Issue Severity

**Critical (Must Fix):**
- Bugs that cause incorrect behavior
- Security vulnerabilities
- Data loss or corruption risks
- Breaking changes without migration path
- Violations of critical requirements

**Important (Should Fix):**
- Code quality issues affecting maintainability
- Performance problems affecting user experience
- Missing or inadequate tests
- Violations of coding standards
- Design issues that will cause future problems

**Suggestion (Nice to Fix):**
- Minor style inconsistencies
- Potential optimizations
- Alternative approaches
- Refactoring opportunities
- Documentation improvements

### Approval Decision

**Approve if:**
- No critical issues
- No more than 1-2 important issues
- Code meets quality standards
- Tests are adequate
- Author is trusted to address minor feedback

**Request Changes if:**
- Any critical issues exist
- Multiple important issues exist
- Code doesn't meet minimum quality standards
- Tests are missing or inadequate
- Requires significant rework

**Comment if:**
- Only suggestions or questions
- Minor improvements suggested
- Want to provide feedback without blocking
- Seeking clarification

## Quality Checklist

- [ ] Code implements all requirements correctly
- [ ] Edge cases and error conditions are handled
- [ ] Code is readable and well-organized
- [ ] Variable and function names are descriptive
- [ ] Code follows team coding standards
- [ ] No code duplication (DRY principle)
- [ ] Functions/methods are single-purpose and reasonably sized
- [ ] Tests are present and meaningful
- [ ] Tests cover edge cases and error conditions
- [ ] No security vulnerabilities (input validation, auth, secrets)
- [ ] No obvious performance issues
- [ ] Code fits well with existing architecture
- [ ] Dependencies are appropriate and secure
- [ ] Comments explain why, not what
- [ ] Documentation is updated if needed
- [ ] Feedback is specific, constructive, and respectful

## Common Mistakes

- **Nitpicking**: Focusing on trivial style issues instead of substance
- **Not testing the code**: Reviewing without running or testing the code
- **Vague feedback**: Saying "this is bad" without explaining why or how to fix
- **Being too lenient**: Approving code with known issues to avoid conflict
- **Being too strict**: Blocking code for personal preferences
- **Ignoring context**: Not understanding the problem the code is solving
- **Rewriting in comments**: Suggesting complete rewrites instead of incremental improvements
- **Not acknowledging good code**: Only pointing out problems, never praising
- **Inconsistent standards**: Applying different standards to different people
- **Reviewing too fast**: Rushing through without careful consideration

## Examples

See [examples.md](examples.md) for detailed examples of code reviews for various scenarios.

## Related Skills

- **Requires**: 
  - None (foundational skill)
- **Commonly followed by**: 
  - refactoring (to improve code quality)
  - testing-strategy (to improve test coverage)
- **Alternative to**: 
  - None (this is the primary code review skill)
- **Works with**: 
  - architecture-review (for design-level review)
  - security-architecture-review (for security-focused review)
  - technical-debt-analysis (to identify debt)

## Skill Composition

Typical workflow:

```
code-implementation
        ↓
code-review (this skill)
        ↓
refactoring (if issues found)
        ↓
code-review (re-review)
        ↓
merge
```

## Evaluation Criteria

### Completeness
- Are all aspects of the code reviewed (correctness, quality, security, performance)?
- Is feedback provided for all significant issues?
- Are tests reviewed as thoroughly as production code?

### Accuracy
- Are identified issues actually problems?
- Are suggestions technically sound?
- Is severity assessment appropriate?

### Constructiveness
- Is feedback specific and actionable?
- Are suggestions provided, not just criticism?
- Is the tone respectful and educational?
- Is good code acknowledged?

### Efficiency
- Is the review completed in a reasonable time?
- Is feedback focused on important issues?
- Are trivial issues deprioritized?

### Consistency
- Are standards applied consistently?
- Is feedback aligned with team conventions?
- Are similar issues identified across the codebase?