# Refactoring - Step-by-Step Instructions

## Overview

This skill helps you systematically improve code quality and structure while preserving external behavior through safe, incremental refactoring.

## Step-by-Step Workflow

### Step 1: Ensure Test Coverage (30-60 minutes)

**Actions:**
1. **Assess current test coverage:**
   - Run code coverage tool
   - Identify untested code
   - Check coverage percentage

2. **Write missing tests:**
   - Focus on code you plan to refactor
   - Write characterization tests (document current behavior)
   - Aim for 70%+ coverage before refactoring

3. **Verify all tests pass:**
   - Run full test suite
   - Fix any failing tests
   - Ensure tests are reliable (not flaky)

**Outputs:**
- Test coverage report showing 70%+ coverage
- All tests passing
- Confidence to proceed with refactoring

**Why this step is critical:**
Tests are your safety net. Without them, you can't verify that refactoring preserves behavior.

### Step 2: Identify Code Smells (30-60 minutes)

**Actions:**
1. **Use automated tools:**
   - Run linters (ESLint, Pylint, etc.)
   - Run static analysis tools (SonarQube, CodeClimate)
   - Check complexity metrics (cyclomatic complexity)
   - Identify duplication (copy-paste detector)

2. **Manual code review:**
   - Look for long methods (>50 lines)
   - Look for large classes (>300 lines)
   - Identify duplicate code
   - Find complex conditionals
   - Spot poor naming

3. **Categorize issues:**
   - **Bloaters**: Long methods, large classes, long parameter lists
   - **Duplication**: Copy-pasted code, similar logic
   - **Complexity**: Nested conditionals, complex logic
   - **Coupling**: Classes too dependent on each other
   - **Poor naming**: Unclear variable/method names

**Outputs:**
- List of code smells with locations
- Categorized by type and severity
- Prioritized for refactoring

### Step 3: Prioritize Refactoring (15-30 minutes)

**Actions:**
1. **Assess impact:**
   - How often does this code change?
   - How many developers work on it?
   - Is it blocking new features?
   - How complex is it?

2. **Assess effort:**
   - How much time will refactoring take?
   - How risky is the refactoring?
   - Do we have adequate tests?

3. **Create priority matrix:**
   ```
   High Impact + Low Effort = Do First
   High Impact + High Effort = Plan Carefully
   Low Impact + Low Effort = Do If Time
   Low Impact + High Effort = Don't Do
   ```

**Outputs:**
- Prioritized list of refactoring tasks
- Estimated effort for each task
- Clear decision on what to refactor now vs. later

### Step 4: Create Refactoring Plan (30-60 minutes)

**Actions:**
1. **Choose refactoring techniques:**
   - For long methods: Extract Method
   - For duplication: Extract Method, Extract Class
   - For complex conditionals: Decompose Conditional, Guard Clauses
   - For poor naming: Rename Method, Rename Variable
   - For large classes: Extract Class, Move Method

2. **Order refactoring steps:**
   - Start with safest refactorings (rename)
   - Then extract methods
   - Then move methods/fields
   - Finally restructure classes

3. **Define success criteria:**
   - Complexity reduced by X%
   - Duplication reduced by Y%
   - Method length < 50 lines
   - Class size < 300 lines
   - All tests still pass

**Outputs:**
- Step-by-step refactoring plan
- Chosen refactoring techniques
- Success criteria

### Step 5: Refactor in Small Steps (2-6 hours)

**Actions:**
1. **For each refactoring step:**
   a. Make ONE small change
   b. Run tests
   c. If tests pass, commit
   d. If tests fail, revert and try again

2. **Common refactoring patterns:**

   **Extract Method:**
   ```javascript
   // Before
   function processOrder(order) {
     let total = 0;
     for (let item of order.items) {
       total += item.price * item.quantity;
     }
     total = total * 1.08; // tax
     // ...
   }
   
   // After
   function processOrder(order) {
     const total = calculateTotal(order.items);
     // ...
   }
   
   function calculateTotal(items) {
     let subtotal = 0;
     for (let item of items) {
       subtotal += item.price * item.quantity;
     }
     return subtotal * 1.08; // tax
   }
   ```

   **Extract Variable:**
   ```javascript
   // Before
   if (order.items.length > 0 && order.total > 100 && order.user.isPremium) {
     applyDiscount();
   }
   
   // After
   const hasItems = order.items.length > 0;
   const isLargeOrder = order.total > 100;
   const isPremiumUser = order.user.isPremium;
   
   if (hasItems && isLargeOrder && isPremiumUser) {
     applyDiscount();
   }
   ```

   **Replace Conditional with Guard Clauses:**
   ```javascript
   // Before
   function getPaymentAmount(user) {
     if (user.isDead) {
       return 0;
     } else {
       if (user.isRetired) {
         return user.pension;
       } else {
         return user.salary;
       }
     }
   }
   
   // After
   function getPaymentAmount(user) {
     if (user.isDead) return 0;
     if (user.isRetired) return user.pension;
     return user.salary;
   }
   ```

3. **Commit frequently:**
   - Commit after each successful refactoring step
   - Use descriptive commit messages
   - Keep commits small and focused

**Outputs:**
- Refactored code
- Passing tests
- Git history showing incremental changes

### Step 6: Review and Validate (30-60 minutes)

**Actions:**
1. **Run full test suite:**
   - All unit tests
   - All integration tests
   - All E2E tests
   - Verify 100% pass rate

2. **Check quality metrics:**
   - Code coverage (should not decrease)
   - Cyclomatic complexity (should decrease)
   - Code duplication (should decrease)
   - Method/class size (should decrease)

3. **Verify performance:**
   - Run performance tests
   - Check for performance regressions
   - Profile if needed

4. **Get code review:**
   - Request review from team
   - Explain what changed and why
   - Address feedback

**Outputs:**
- All tests passing
- Improved quality metrics
- No performance regressions
- Team approval

### Step 7: Document Changes (15-30 minutes)

**Actions:**
1. **Update code documentation:**
   - Update comments if needed
   - Update API documentation
   - Update README if public API changed

2. **Communicate changes:**
   - Write summary of changes
   - Explain rationale
   - Highlight any API changes
   - Share with team

3. **Update tracking:**
   - Close related tickets
   - Update technical debt backlog
   - Document lessons learned

**Outputs:**
- Updated documentation
- Team communication
- Closed tickets

## Refactoring Techniques Reference

### Extract Method
**When:** Method is too long or does multiple things
**How:** Extract code into a new method with a descriptive name
**Example:** Extract calculation logic into `calculateTotal()`

### Inline Method
**When:** Method body is as clear as the method name
**How:** Replace method calls with method body
**Example:** Inline `getTotal() { return this.total; }` to just `this.total`

### Extract Variable
**When:** Complex expression is hard to understand
**How:** Extract expression into a well-named variable
**Example:** `const isEligible = age > 18 && hasLicense && !isSuspended`

### Rename Method/Variable
**When:** Name doesn't clearly express intent
**How:** Rename to a more descriptive name
**Example:** `calc()` → `calculateMonthlyPayment()`

### Move Method
**When:** Method uses another class more than its own
**How:** Move method to the class it uses most
**Example:** Move `calculateDiscount()` from `Order` to `Discount`

### Extract Class
**When:** Class has too many responsibilities
**How:** Create new class and move related methods/fields
**Example:** Extract `Address` class from `Customer` class

### Replace Magic Number with Constant
**When:** Numeric literals appear in code
**How:** Define named constant
**Example:** `const TAX_RATE = 0.08;` instead of `total * 0.08`

### Decompose Conditional
**When:** Complex conditional is hard to understand
**How:** Extract condition and branches into methods
**Example:** Extract `isEligibleForDiscount()` from complex if statement

## Tips for Success

- **Always have tests first**: Never refactor without tests
- **Make small changes**: One refactoring at a time
- **Run tests frequently**: After every change
- **Commit frequently**: Make it easy to revert
- **Don't change behavior**: Refactoring preserves behavior
- **Use IDE refactoring tools**: They're safer than manual changes
- **Get team buy-in**: Explain why refactoring is needed
- **Set time limits**: Don't let refactoring become endless

## Common Pitfalls to Avoid

- Refactoring without tests
- Making too many changes at once
- Changing behavior while refactoring
- Not running tests after each change
- Refactoring and adding features simultaneously
- Over-engineering simple code
- Refactoring code that will be deleted
- Not getting team feedback
- Spending too much time on low-impact refactoring

## Time Estimates

By refactoring scope:
- **Single method**: 15-30 minutes
- **Single class**: 1-2 hours
- **Multiple classes**: 2-4 hours
- **Module/package**: 4-8 hours
- **Large subsystem**: 1-2 weeks (consider migration-planning instead)