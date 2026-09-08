# Refactoring

## Purpose

Improve code structure and quality without changing external behavior through systematic refactoring.

## When to Use

- When code quality issues are identified in code review
- When technical debt is impacting development velocity
- Before adding new features to complex code
- When code is difficult to understand or maintain
- When code duplication is excessive
- When preparing code for extension or modification
- After identifying code smells or anti-patterns
- When test coverage is sufficient to ensure safety

## When NOT to Use

- When there are no tests (write tests first)
- When fixing bugs (fix bugs separately, then refactor)
- When adding new features (refactor first, then add features)
- When under tight deadlines (refactor when you have time to do it right)
- For code that will be deleted soon
- When you don't understand the code (understand it first)
- For large-scale architectural changes (use migration-planning instead)

## Inputs

- **code-to-refactor**: The code that needs improvement
- **quality-issues**: Identified code smells, duplication, complexity issues
- **test-coverage**: Existing tests to ensure behavior is preserved
- **constraints**: Timeline, scope, risk tolerance

## Expected Outputs

- **refactoring-plan**: Step-by-step plan for refactoring
- **refactored-code**: Improved code with better structure
- **test-results**: Confirmation that behavior is unchanged
- **documentation**: Updated documentation reflecting changes

## Workflow

### 1. Ensure Test Coverage (30-60 minutes)

**Before refactoring, ensure adequate test coverage:**
- Unit tests for the code being refactored
- Integration tests for affected components
- E2E tests for critical workflows

**If tests are missing:**
- Write characterization tests (document current behavior)
- Add tests for critical paths
- Achieve minimum 70% coverage before refactoring

**Why this matters:**
- Tests are your safety net
- They ensure behavior doesn't change
- They give you confidence to make changes

### 2. Identify Code Smells (30-60 minutes)

**Common code smells:**

**Bloaters:**
- Long methods (>50 lines)
- Large classes (>300 lines)
- Long parameter lists (>3 parameters)
- Primitive obsession (using primitives instead of objects)

**Object-Orientation Abusers:**
- Switch statements (should use polymorphism)
- Refused bequest (subclass doesn't use parent methods)
- Alternative classes with different interfaces

**Change Preventers:**
- Divergent change (one class changes for multiple reasons)
- Shotgun surgery (one change requires changes in many classes)
- Parallel inheritance hierarchies

**Dispensables:**
- Comments (code should be self-documenting)
- Duplicate code
- Dead code
- Speculative generality (code for future that may never come)

**Couplers:**
- Feature envy (method uses another class more than its own)
- Inappropriate intimacy (classes too dependent on each other)
- Message chains (a.b().c().d())
- Middle man (class just delegates to another)

### 3. Prioritize Refactoring (15-30 minutes)

**High priority:**
- Code that changes frequently
- Code that's hard to understand
- Code that's blocking new features
- Code with high complexity (cyclomatic complexity > 10)
- Code with duplication

**Medium priority:**
- Code with minor quality issues
- Code that's moderately complex
- Code with some duplication

**Low priority:**
- Code that rarely changes
- Code that's already clear
- Code that will be deleted soon

### 4. Create Refactoring Plan (30-60 minutes)

**Plan should include:**
1. What to refactor (specific files, functions, classes)
2. What refactoring techniques to apply
3. Order of refactoring steps
4. How to verify each step (run tests)
5. Rollback plan if something goes wrong

**Refactoring techniques:**

**Composing Methods:**
- Extract Method: Break long methods into smaller ones
- Inline Method: Remove unnecessary methods
- Extract Variable: Name complex expressions
- Inline Variable: Remove unnecessary variables
- Replace Temp with Query: Replace temporary variables with methods
- Split Temporary Variable: Use separate variables for separate purposes

**Moving Features:**
- Move Method: Move method to the class that uses it most
- Move Field: Move field to the class that uses it most
- Extract Class: Split large class into smaller classes
- Inline Class: Merge small class into another

**Organizing Data:**
- Encapsulate Field: Make fields private, provide getters/setters
- Replace Magic Number with Constant: Use named constants
- Replace Type Code with Class: Use objects instead of primitives
- Replace Array with Object: Use objects for structured data

**Simplifying Conditionals:**
- Decompose Conditional: Extract condition and branches into methods
- Consolidate Conditional: Combine similar conditions
- Replace Nested Conditional with Guard Clauses: Handle special cases first
- Replace Conditional with Polymorphism: Use inheritance instead of switch

**Simplifying Method Calls:**
- Rename Method: Use clear, descriptive names
- Add Parameter: Add parameter for needed data
- Remove Parameter: Remove unused parameters
- Introduce Parameter Object: Group parameters into object
- Replace Parameter with Method: Calculate value instead of passing it

### 5. Refactor in Small Steps (2-6 hours)

**For each refactoring step:**
1. Make one small change
2. Run tests to ensure behavior is unchanged
3. Commit the change
4. Repeat

**Example workflow:**
```
1. Extract method "calculateTotal" from "processOrder"
   - Run tests ✓
   - Commit "Extract calculateTotal method"

2. Move "calculateTotal" to "Order" class
   - Run tests ✓
   - Commit "Move calculateTotal to Order class"

3. Rename "processOrder" to "submitOrder"
   - Run tests ✓
   - Commit "Rename processOrder to submitOrder"
```

**Key principles:**
- Make small, incremental changes
- Run tests after each change
- Commit frequently
- If tests fail, revert and try again
- Don't change behavior and refactor at the same time

### 6. Review and Validate (30-60 minutes)

**After refactoring:**
- Run full test suite
- Check code coverage (should not decrease)
- Review code quality metrics (complexity, duplication)
- Get code review from team
- Verify performance is not degraded
- Update documentation

**Quality checks:**
- Is the code easier to understand?
- Is duplication reduced?
- Is complexity reduced?
- Are methods smaller and more focused?
- Are classes more cohesive?
- Is coupling reduced?

### 7. Document Changes (15-30 minutes)

**Update documentation:**
- Code comments (if necessary)
- API documentation
- Architecture diagrams (if structure changed)
- README or developer guides

**Communicate changes:**
- Share with team
- Explain what changed and why
- Highlight any API changes
- Update any affected documentation

## Decision Framework

### When to Refactor

**Refactor now:**
- Code is blocking new development
- Code has high complexity and changes frequently
- Duplication is causing bugs
- Team agrees it's a priority
- You have good test coverage

**Refactor later:**
- Code rarely changes
- Code will be deleted soon
- No test coverage (write tests first)
- Under tight deadline
- Team doesn't see value

**Don't refactor:**
- Code works and is clear
- No tests and can't write them
- Code will be replaced soon
- Risk outweighs benefit

### How Much to Refactor

**Minimal refactoring:**
- Rename variables/methods for clarity
- Extract magic numbers to constants
- Remove dead code
- Fix obvious issues

**Moderate refactoring:**
- Extract methods
- Reduce duplication
- Simplify conditionals
- Improve naming

**Extensive refactoring:**
- Restructure classes
- Apply design patterns
- Reduce coupling
- Improve architecture

## Quality Checklist

- [ ] Adequate test coverage exists before refactoring
- [ ] All tests pass before starting
- [ ] Refactoring plan is documented
- [ ] Changes are made in small, incremental steps
- [ ] Tests are run after each change
- [ ] Each change is committed separately
- [ ] Code complexity is reduced
- [ ] Code duplication is reduced
- [ ] Code is more readable and maintainable
- [ ] All tests still pass after refactoring
- [ ] Code coverage is maintained or improved
- [ ] Performance is not degraded
- [ ] Documentation is updated
- [ ] Team has reviewed the changes

## Common Mistakes

- **Refactoring without tests**: Changing code without safety net
- **Changing behavior**: Refactoring should preserve behavior
- **Big bang refactoring**: Making too many changes at once
- **Not running tests frequently**: Only running tests at the end
- **Refactoring and adding features**: Doing both at the same time
- **Over-engineering**: Making code more complex in the name of refactoring
- **Premature optimization**: Optimizing before it's needed
- **Not committing frequently**: Making it hard to revert if needed
- **Ignoring team feedback**: Refactoring without team buy-in
- **Refactoring for the sake of it**: Changing code that's already good

## Examples

See [examples.md](examples.md) for detailed examples of refactoring scenarios.

## Related Skills

- **Requires**: 
  - None (foundational skill)
- **Commonly followed by**: 
  - code-review (to review refactored code)
  - testing-strategy (to improve test coverage)
- **Alternative to**: 
  - None (this is the primary refactoring skill)
- **Works with**: 
  - technical-debt-analysis (to identify what to refactor)
  - code-review (to identify refactoring opportunities)
  - architecture-review (for larger refactoring efforts)

## Skill Composition

Typical workflow:

```
code-review or technical-debt-analysis
        ↓
refactoring (this skill)
        ↓
code-review
        ↓
merge
```

## Evaluation Criteria

### Safety
- Are there adequate tests before refactoring?
- Are tests run after each change?
- Is behavior preserved?
- Are changes reversible?

### Quality Improvement
- Is code complexity reduced?
- Is duplication reduced?
- Is code more readable?
- Is code more maintainable?
- Are code smells addressed?

### Process
- Are changes made in small steps?
- Are changes committed frequently?
- Is the refactoring plan followed?
- Is documentation updated?

### Team Impact
- Does the team understand the changes?
- Does the team agree with the refactoring?
- Are API changes communicated?
- Is knowledge shared?