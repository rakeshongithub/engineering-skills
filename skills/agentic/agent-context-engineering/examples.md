# Agent Context Engineering - Examples

This document provides detailed examples of engineering context for different types of agent tasks.

---

## Example 1: REST API Implementation

### Scenario
An agent needs to implement a new REST API endpoint for user registration. The codebase already has authentication patterns, but the agent has never seen this specific codebase before.

### Task Definition
```markdown
**Task**: Implement POST /api/auth/register endpoint

**Requirements**:
- Accept email, password, and name
- Validate input (email format, password strength)
- Check email uniqueness
- Hash password before storage
- Create user record
- Generate JWT token
- Return user data and token
- Handle errors appropriately
```

### Context Engineering Process

#### Step 1: Analyze Task Requirements

**Operations**:
- Read existing auth endpoint patterns
- Write new endpoint handler
- Integrate with UserService
- Add input validation
- Implement error handling
- Write unit tests

**Decision Points**:
- Where to place the endpoint (which file, which position)
- Which validation library to use
- How to structure the response
- What HTTP status codes to return
- How to handle duplicate emails

**Code Artifacts Needed**:
- Existing auth endpoints (patterns)
- UserService interface and implementation
- Validation middleware
- Error handling utilities
- Test examples

#### Step 2: Identify Context Sources

```markdown
**Code Sources**:
- src/routes/auth.js:10-50 (existing login endpoint)
- src/routes/auth.js:55-75 (logout endpoint)
- src/services/userService.js (full file)
- src/middleware/validation.js:15-40 (validation patterns)
- src/utils/errors.js (error handling)

**Documentation**:
- docs/adr/003-jwt-authentication.md (auth strategy)
- docs/api/authentication.md (API documentation)
- README.md (project setup and conventions)

**Examples**:
- tests/routes/auth.test.js:20-60 (login endpoint tests)
- src/routes/products.js:30-55 (similar CRUD endpoint)

**Implicit Context**:
- .eslintrc.json (code style)
- package.json (available dependencies)
- src/routes/README.md (routing conventions)
```

#### Step 3: Assess Context Scope

```markdown
**Must-Have (Upfront) - ~4,200 tokens**:
- Existing login endpoint (600 tokens) - shows pattern
- UserService interface (400 tokens) - shows available methods
- Validation middleware pattern (500 tokens) - shows how to validate
- Error handling utilities (400 tokens) - shows error patterns
- JWT generation pattern (300 tokens) - shows token creation
- Test pattern (800 tokens) - shows testing approach
- Auth strategy overview (600 tokens) - explains architecture
- API response format (200 tokens) - shows response structure
- Routing conventions (400 tokens) - explains file organization

**Should-Have (Upfront) - ~1,500 tokens**:
- Similar CRUD endpoint (600 tokens) - additional pattern
- Password hashing example (400 tokens) - security pattern
- ADR on JWT (500 tokens) - decision rationale

**Nice-to-Have (On-Demand) - ~3,000 tokens**:
- Full UserService implementation details
- All existing auth tests
- Complete API documentation
- Database schema

**Total Upfront**: ~5,700 tokens (well within 8K context window)
```

#### Step 4: Structure Context Hierarchically

```markdown
# Context Package: User Registration Endpoint

## Layer 1: Overview (Architecture)

### System Architecture
- Express.js REST API
- JWT-based authentication (see ADR-003)
- PostgreSQL database via UserService
- Validation via express-validator middleware
- Error handling via custom error classes

### File Organization
- Routes: src/routes/*.js (Express routers)
- Services: src/services/*.js (business logic)
- Middleware: src/middleware/*.js (request processing)
- Utils: src/utils/*.js (helpers)
- Tests: tests/routes/*.test.js (Jest tests)

### Conventions
- One router per resource (auth, users, products, etc.)
- Async/await for all async operations
- Custom error classes for error handling
- Validation middleware before route handlers

---

## Layer 2: Interfaces (Contracts)

### UserService Interface
```typescript
class UserService {
  async createUser(data: {
    email: string;
    password: string; // Must be hashed before calling
    name: string;
  }): Promise<User>;
  
  async findByEmail(email: string): Promise<User | null>;
  
  async findById(id: string): Promise<User | null>;
}
```

### Expected Request/Response
```javascript
// Request
POST /api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "name": "John Doe"
}

// Success Response (201)
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid-here",
      "email": "user@example.com",
      "name": "John Doe",
      "createdAt": "2026-09-08T10:00:00Z"
    },
    "token": "jwt-token-here"
  }
}

// Error Response (400)
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  }
}
```

---

## Layer 3: Implementation Patterns

### Pattern 1: Auth Endpoint Structure
```javascript
// src/routes/auth.js (existing login endpoint)
const express = require('express');
const router = express.Router();
const { validateLogin } = require('../middleware/validation');
const userService = require('../services/userService');
const { generateToken } = require('../utils/jwt');
const { AuthenticationError } = require('../utils/errors');

router.post('/login', 
  validateLogin, // Validation middleware runs first
  async (req, res, next) => {
    try {
      // 1. Extract validated data
      const { email, password } = req.body;
      
      // 2. Business logic
      const user = await userService.findByEmail(email);
      if (!user || !await userService.verifyPassword(password, user.password)) {
        throw new AuthenticationError('Invalid credentials');
      }
      
      // 3. Generate token
      const token = generateToken({ userId: user.id });
      
      // 4. Return response
      res.status(200).json({
        success: true,
        data: {
          user: {
            id: user.id,
            email: user.email,
            name: user.name
          },
          token
        }
      });
    } catch (error) {
      next(error); // Pass to error handling middleware
    }
  }
);
```

**Key Patterns to Follow**:
1. Validation middleware before handler
2. Try-catch for error handling
3. Custom error classes for specific errors
4. Consistent response format
5. Don't return password in response
6. Use next(error) to pass errors to error handler

### Pattern 2: Validation Middleware
```javascript
// src/middleware/validation.js
const { body, validationResult } = require('express-validator');
const { ValidationError } = require('../utils/errors');

const validateLogin = [
  body('email')
    .isEmail()
    .withMessage('Invalid email format')
    .normalizeEmail(),
  body('password')
    .notEmpty()
    .withMessage('Password is required'),
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      throw new ValidationError('Invalid input', errors.array());
    }
    next();
  }
];

// For registration, you'll need similar validation with additional rules:
const validateRegistration = [
  body('email')
    .isEmail()
    .withMessage('Invalid email format')
    .normalizeEmail(),
  body('password')
    .isLength({ min: 8 })
    .withMessage('Password must be at least 8 characters')
    .matches(/\d/)
    .withMessage('Password must contain a number')
    .matches(/[!@#$%^&*]/)
    .withMessage('Password must contain a special character'),
  body('name')
    .trim()
    .notEmpty()
    .withMessage('Name is required')
    .isLength({ min: 2, max: 50 })
    .withMessage('Name must be 2-50 characters'),
  (req, res, next) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      throw new ValidationError('Invalid input', errors.array());
    }
    next();
  }
];
```

### Pattern 3: Password Hashing
```javascript
// src/services/userService.js (excerpt)
const bcrypt = require('bcrypt');
const SALT_ROUNDS = 10;

class UserService {
  async createUser({ email, password, name }) {
    // Hash password before storing
    const hashedPassword = await bcrypt.hash(password, SALT_ROUNDS);
    
    // Create user with hashed password
    const user = await db.users.create({
      email,
      password: hashedPassword,
      name
    });
    
    return user;
  }
}
```

---

## Layer 4: Examples

### Example Test
```javascript
// tests/routes/auth.test.js (excerpt)
describe('POST /api/auth/login', () => {
  it('should login user with valid credentials', async () => {
    const response = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'test@example.com',
        password: 'ValidPass123!'
      });
    
    expect(response.status).toBe(200);
    expect(response.body.success).toBe(true);
    expect(response.body.data).toHaveProperty('user');
    expect(response.body.data).toHaveProperty('token');
    expect(response.body.data.user).not.toHaveProperty('password');
  });
  
  it('should return 400 for invalid email', async () => {
    const response = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'invalid-email',
        password: 'ValidPass123!'
      });
    
    expect(response.status).toBe(400);
    expect(response.body.success).toBe(false);
    expect(response.body.error.code).toBe('VALIDATION_ERROR');
  });
});
```

---

## Layer 5: Metadata & Constraints

### Dependencies
- express: ^4.18.0
- express-validator: ^7.0.0
- bcrypt: ^5.1.0
- jsonwebtoken: ^9.0.0

### Constraints
- ⚠️ Email must be unique (check before creating user)
- ⚠️ Password must be hashed with bcrypt (10 salt rounds)
- ⚠️ Password requirements: min 8 chars, 1 number, 1 special char
- ⚠️ Return 201 on successful creation
- ⚠️ Return 400 on validation error
- ⚠️ Return 409 on duplicate email
- ⚠️ Never return password in response
- ⚠️ Rate limit: 5 requests per 15 minutes per IP

### Related Files
- src/routes/auth.js (add endpoint here)
- src/middleware/validation.js (add validateRegistration)
- src/services/userService.js (use createUser, findByEmail)
- tests/routes/auth.test.js (add tests here)
```

#### Step 5-10: Additional Context Engineering

**Retrieval Strategy**: Provide all Layer 1-5 upfront. Agent can retrieve full UserService implementation or additional tests on-demand using file reading tools.

**Metadata**: All code snippets tagged with source file and line numbers. Confidence level: High (code is from production, well-tested).

**Validation**: Walked through task step-by-step. Agent can:
1. Find where to add endpoint ✓
2. Know how to validate input ✓
3. Check email uniqueness ✓
4. Hash password ✓
5. Create user ✓
6. Generate token ✓
7. Format response ✓
8. Handle errors ✓
9. Write tests ✓

**Optimization**: Used hierarchical structure, clear headers, highlighted constraints, removed redundant code.

**Versioning**: Tagged with git hash of source files, timestamp of context creation.

### Result

With this context package, the agent successfully:
- Created the registration endpoint in the correct file and location
- Implemented proper validation with all password requirements
- Checked email uniqueness before creation
- Hashed password correctly
- Generated JWT token
- Returned proper response format
- Handled all error cases
- Wrote comprehensive tests

**Success Rate**: 95% (19 out of 20 test executions successful)

---

## Example 2: Legacy Code Refactoring

### Scenario
An agent needs to refactor a complex legacy function that has grown to 200 lines with multiple responsibilities. The code has no tests and unclear dependencies.

### Task Definition
```markdown
**Task**: Refactor processOrder() function in src/orders/orderProcessor.js

**Requirements**:
- Break into smaller, single-responsibility functions
- Add error handling
- Improve readability
- Maintain exact same behavior (no functional changes)
- Add unit tests
- Update all callers if signature changes
```

### Context Engineering Challenges

1. **Understanding Current Behavior**: 200-line function with complex logic
2. **Identifying Dependencies**: What does this function call? What calls it?
3. **Preserving Behavior**: Must not break existing functionality
4. **Finding All Callers**: Need to update if signature changes

### Context Package Structure

```markdown
# Context Package: Legacy Order Processor Refactoring

## Layer 1: Overview

### Current State
- Function: processOrder() in src/orders/orderProcessor.js:45-245
- Size: 200 lines
- Complexity: High (cyclomatic complexity: 28)
- Responsibilities: 7 distinct responsibilities (violation of SRP)
- Test coverage: 0%
- Known issues: 3 bug reports related to this function

### Refactoring Goal
- Break into 5-7 smaller functions
- Each function has single responsibility
- Add error handling
- Maintain 100% behavioral compatibility
- Achieve 80%+ test coverage

### Risk Assessment
- High: Function is critical (used in checkout flow)
- Medium: No existing tests to verify behavior preservation
- Low: Function is well-isolated (few dependencies)

---

## Layer 2: Current Implementation

### Full Function Code
```javascript
// src/orders/orderProcessor.js:45-245
async function processOrder(orderData) {
  // [Full 200-line function provided here]
  // Including all logic, edge cases, error handling
}
```

### Identified Responsibilities
1. **Validation** (lines 50-70): Validate order data
2. **Inventory Check** (lines 72-95): Check product availability
3. **Price Calculation** (lines 97-125): Calculate totals, taxes, discounts
4. **Payment Processing** (lines 127-165): Process payment
5. **Inventory Update** (lines 167-185): Deduct inventory
6. **Order Creation** (lines 187-215): Create order record
7. **Notification** (lines 217-240): Send confirmation email

---

## Layer 3: Dependencies

### What This Function Calls
```javascript
// Internal dependencies
const inventoryService = require('../inventory/inventoryService');
const paymentService = require('../payment/paymentService');
const emailService = require('../notifications/emailService');
const db = require('../database');

// External dependencies
const { v4: uuidv4 } = require('uuid');
const { calculateTax } = require('../utils/tax');
const { applyDiscount } = require('../utils/discounts');
```

### What Calls This Function
```javascript
// Callers identified via grep:
// 1. src/routes/checkout.js:78
router.post('/checkout', async (req, res) => {
  const order = await processOrder(req.body.orderData);
  res.json({ orderId: order.id });
});

// 2. src/services/subscriptionService.js:120
async function renewSubscription(subscriptionId) {
  const orderData = buildOrderDataFromSubscription(subscriptionId);
  return await processOrder(orderData);
}

// 3. tests/integration/checkout.test.js:45 (integration test)
```

---

## Layer 4: Behavioral Specifications

### Input/Output Contract
```typescript
// Input
interface OrderData {
  userId: string;
  items: Array<{
    productId: string;
    quantity: number;
  }>;
  shippingAddress: Address;
  paymentMethod: PaymentMethod;
  discountCode?: string;
}

// Output (Success)
interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
  subtotal: number;
  tax: number;
  discount: number;
  total: number;
  status: 'pending' | 'confirmed';
  createdAt: Date;
}

// Output (Error)
// Throws: ValidationError, InventoryError, PaymentError
```

### Edge Cases & Behavior
```markdown
1. **Empty order**: Throws ValidationError("Order must contain at least one item")
2. **Out of stock**: Throws InventoryError("Product X out of stock")
3. **Invalid discount code**: Ignores code, proceeds without discount (logs warning)
4. **Payment failure**: Throws PaymentError, does NOT create order or update inventory
5. **Email failure**: Logs error but still returns successful order (email is non-critical)
6. **Partial inventory**: If any item out of stock, entire order fails (atomic operation)
```

### Known Bugs (Must Preserve or Fix)
```markdown
1. Bug #234: Discount applied before tax (incorrect, but changing breaks compatibility)
   - Context: Must preserve this behavior unless explicitly asked to fix
   
2. Bug #456: Race condition in inventory check vs. update
   - Context: Known issue, not addressed in this refactoring
   
3. Bug #567: Email sent even if order creation fails
   - Context: Should be fixed during refactoring (email only on success)
```

---

## Layer 5: Refactoring Guidance

### Recommended Structure
```javascript
// Proposed refactored structure
async function processOrder(orderData) {
  // Orchestrator function
  const validatedData = await validateOrderData(orderData);
  await checkInventoryAvailability(validatedData.items);
  const pricing = await calculateOrderPricing(validatedData);
  const payment = await processPayment(pricing.total, validatedData.paymentMethod);
  await updateInventory(validatedData.items);
  const order = await createOrderRecord(validatedData, pricing, payment);
  await sendOrderConfirmation(order); // Non-blocking
  return order;
}

// Each extracted function
async function validateOrderData(orderData) { /* ... */ }
async function checkInventoryAvailability(items) { /* ... */ }
async function calculateOrderPricing(orderData) { /* ... */ }
async function processPayment(amount, paymentMethod) { /* ... */ }
async function updateInventory(items) { /* ... */ }
async function createOrderRecord(orderData, pricing, payment) { /* ... */ }
async function sendOrderConfirmation(order) { /* ... */ }
```

### Testing Strategy
```javascript
// 1. Characterization tests (before refactoring)
// Capture current behavior with various inputs
describe('processOrder - Current Behavior', () => {
  it('should process valid order', async () => {
    const result = await processOrder(validOrderData);
    expect(result).toMatchSnapshot(); // Capture current output
  });
  
  it('should handle out of stock', async () => {
    await expect(processOrder(outOfStockOrder))
      .rejects.toThrow('Product X out of stock');
  });
  
  // ... more characterization tests
});

// 2. Unit tests (after refactoring)
// Test each extracted function independently
describe('validateOrderData', () => {
  it('should validate valid data', () => {
    expect(() => validateOrderData(validData)).not.toThrow();
  });
  
  it('should reject empty items', () => {
    expect(() => validateOrderData({ items: [] }))
      .toThrow('Order must contain at least one item');
  });
});
```

### Refactoring Steps
```markdown
1. Write characterization tests to capture current behavior
2. Run tests to establish baseline (should pass)
3. Extract validateOrderData function
4. Run tests (should still pass)
5. Extract checkInventoryAvailability function
6. Run tests (should still pass)
7. Repeat for each responsibility
8. Add unit tests for each extracted function
9. Update callers if signature changed (none expected)
10. Final integration test run
```

### Constraints
- ⚠️ Must maintain exact same input/output contract
- ⚠️ Must preserve all edge case behaviors (even bugs, unless fixing bug #567)
- ⚠️ Must not change order of operations (tax before discount, etc.)
- ⚠️ Must maintain atomicity (all or nothing for inventory updates)
- ⚠️ Characterization tests must pass before and after refactoring
```

### Result

With this context package, the agent:
- Understood the complete current behavior
- Identified all 7 responsibilities correctly
- Extracted functions with appropriate boundaries
- Preserved all edge case behaviors
- Fixed bug #567 (email only on success)
- Preserved bugs #234 and #456 (as instructed)
- Wrote comprehensive characterization tests
- Achieved 85% test coverage
- All existing callers continued working without changes

**Success Rate**: 100% (behavioral compatibility maintained)

---

## Example 3: Multi-Agent Bug Fix

### Scenario
A production bug requires coordination between three agents: one to reproduce the bug, one to identify the root cause, and one to implement the fix. Context must be engineered for each agent and passed between them.

### Task Definition
```markdown
**Bug Report**: Users report intermittent 500 errors during checkout

**Symptoms**:
- Occurs ~5% of the time
- Only during high traffic
- Error message: "Database connection timeout"
- Started after deployment on 2026-09-05

**Agents**:
1. **Reproduction Agent**: Reproduce the bug reliably
2. **Analysis Agent**: Identify root cause
3. **Fix Agent**: Implement and test fix
```

### Context Engineering for Multi-Agent Workflow

#### Agent 1: Reproduction Agent Context

```markdown
# Context Package: Bug Reproduction

## Bug Report
- **ID**: BUG-789
- **Severity**: High (affects checkout)
- **Frequency**: ~5% of checkout attempts
- **Environment**: Production
- **Started**: 2026-09-05 (after deployment #456)

## Symptoms
- 500 Internal Server Error
- Error message: "Database connection timeout"
- Only during high traffic periods (>100 concurrent users)
- Checkout flow hangs for 30 seconds then fails

## Reproduction Steps (from user reports)
1. Add items to cart
2. Proceed to checkout
3. Fill in shipping information
4. Click "Place Order"
5. Wait 30 seconds
6. Receive 500 error

## Environment Details
- **Production DB**: PostgreSQL 14, connection pool size: 20
- **Traffic**: Peak 150 concurrent users
- **Recent Changes**: Deployment #456 on 2026-09-05
  - Added inventory reservation feature
  - Modified order processing flow

## Logs (Sample)
```
2026-09-08 10:15:23 ERROR: Database connection timeout
2026-09-08 10:15:23 ERROR: Connection pool exhausted (20/20 connections in use)
2026-09-08 10:15:23 ERROR: processOrder failed for user 12345
```

## Relevant Code
- src/orders/orderProcessor.js (modified in deployment #456)
- src/database/connectionPool.js (connection pool config)
- src/inventory/reservationService.js (new in deployment #456)

## Reproduction Goal
- Reliably reproduce the bug in test environment
- Identify exact conditions that trigger the bug
- Capture detailed logs and stack traces
- Pass findings to Analysis Agent
```

**Agent 1 Output** (passed to Agent 2):
```markdown
## Reproduction Results

**Success**: Bug reliably reproduced in staging environment

**Conditions**:
- 100+ concurrent checkout requests
- Database connection pool size: 20
- Bug occurs when connection pool is exhausted

**Root Cause Hypothesis**:
- New inventory reservation feature holds DB connections longer
- Connections not released properly in error cases
- Pool exhaustion leads to timeout

**Evidence**:
- Connection pool monitoring shows 20/20 connections held
- Connections held for 30+ seconds (normal: <1 second)
- Stack trace shows connections held in reservationService.reserveInventory()

**Detailed Logs**: [attached]
**Stack Traces**: [attached]
```

#### Agent 2: Analysis Agent Context

```markdown
# Context Package: Root Cause Analysis

## Input from Reproduction Agent
[Full reproduction results from Agent 1]

## Code Under Investigation

### New Feature (Deployment #456)
```javascript
// src/inventory/reservationService.js (NEW)
class ReservationService {
  async reserveInventory(items) {
    const client = await db.pool.connect(); // Get connection
    try {
      await client.query('BEGIN');
      
      for (const item of items) {
        // Reserve inventory
        await client.query(
          'UPDATE inventory SET reserved = reserved + $1 WHERE product_id = $2',
          [item.quantity, item.productId]
        );
      }
      
      await client.query('COMMIT');
      return true;
    } catch (error) {
      await client.query('ROLLBACK');
      throw error; // ⚠️ Connection not released on error!
    } finally {
      client.release(); // This should release, but...
    }
  }
}
```

### How It's Called
```javascript
// src/orders/orderProcessor.js (MODIFIED)
async function processOrder(orderData) {
  // ... validation ...
  
  // NEW: Reserve inventory before payment
  await reservationService.reserveInventory(orderData.items); // ⚠️ If this throws, what happens?
  
  // Process payment
  const payment = await processPayment(orderData);
  
  // ... rest of order processing ...
}
```

## Analysis Goal
- Identify exact code path causing connection leak
- Determine why connections aren't released
- Assess impact and scope
- Recommend fix approach
- Pass findings to Fix Agent
```

**Agent 2 Output** (passed to Agent 3):
```markdown
## Root Cause Analysis Results

**Root Cause Identified**: Connection leak in error path

**Detailed Explanation**:
1. reservationService.reserveInventory() acquires DB connection
2. If inventory check fails (out of stock), it throws error
3. Error is caught by processOrder(), which throws PaymentError
4. Connection is released in finally block of reserveInventory()
5. **BUT**: If error occurs between reserveInventory() and payment processing,
   the connection is released, but a NEW connection is acquired for rollback
6. Under high load, this creates connection pool exhaustion

**Specific Issue**:
```javascript
// Problem: Each operation gets its own connection
await reservationService.reserveInventory(items); // Connection 1
await processPayment(data); // Connection 2
await createOrder(data); // Connection 3

// If any step fails, we need to rollback ALL previous steps
// But each used a different connection, so rollback is incomplete
```

**Impact**:
- Affects ~5% of orders (when inventory is low or payment fails)
- Connection pool exhaustion under load
- Cascading failures (one failure blocks others)

**Recommended Fix**:
1. Use single transaction across entire order processing
2. Pass db client through all functions
3. Rollback entire transaction on any error
4. Ensure connection released in all code paths

**Fix Scope**:
- Modify: orderProcessor.js, reservationService.js
- Add: Transaction management utility
- Test: Connection pool under load, error scenarios
```

#### Agent 3: Fix Agent Context

```markdown
# Context Package: Bug Fix Implementation

## Input from Analysis Agent
[Full root cause analysis from Agent 2]

## Fix Requirements
1. Use single transaction for entire order processing
2. Ensure connection released in all code paths
3. Maintain atomicity (all or nothing)
4. No behavior changes (except bug fix)
5. Add tests for connection management

## Implementation Guidance

### Pattern: Transaction Management
```javascript
// Recommended pattern
async function withTransaction(callback) {
  const client = await db.pool.connect();
  try {
    await client.query('BEGIN');
    const result = await callback(client);
    await client.query('COMMIT');
    return result;
  } catch (error) {
    await client.query('ROLLBACK');
    throw error;
  } finally {
    client.release(); // Always released
  }
}

// Usage
async function processOrder(orderData) {
  return await withTransaction(async (client) => {
    await reserveInventory(client, orderData.items);
    const payment = await processPayment(client, orderData);
    const order = await createOrder(client, orderData, payment);
    return order;
  });
}
```

### Files to Modify
1. **src/database/transaction.js** (NEW): Add withTransaction utility
2. **src/orders/orderProcessor.js**: Use withTransaction
3. **src/inventory/reservationService.js**: Accept client parameter
4. **tests/orders/orderProcessor.test.js**: Add connection leak tests

### Testing Requirements
```javascript
// Test: Connection released on success
it('should release connection on successful order', async () => {
  const initialPoolSize = db.pool.totalCount;
  await processOrder(validOrderData);
  expect(db.pool.totalCount).toBe(initialPoolSize);
});

// Test: Connection released on error
it('should release connection on order failure', async () => {
  const initialPoolSize = db.pool.totalCount;
  await expect(processOrder(invalidOrderData)).rejects.toThrow();
  expect(db.pool.totalCount).toBe(initialPoolSize);
});

// Test: No connection leak under load
it('should not leak connections under concurrent load', async () => {
  const promises = Array(100).fill().map(() => processOrder(validOrderData));
  await Promise.allSettled(promises);
  expect(db.pool.waitingCount).toBe(0); // No waiting connections
});
```

### Constraints
- ⚠️ Must maintain atomicity (all or nothing)
- ⚠️ Must release connection in ALL code paths (success, error, exception)
- ⚠️ Must not change order processing behavior (except fix)
- ⚠️ Must add tests to prevent regression
```

**Agent 3 Output** (final result):
```markdown
## Fix Implementation Results

**Status**: Fix implemented and tested successfully

**Changes Made**:
1. Created src/database/transaction.js with withTransaction utility
2. Modified src/orders/orderProcessor.js to use single transaction
3. Modified src/inventory/reservationService.js to accept client parameter
4. Added comprehensive connection management tests

**Testing Results**:
- All existing tests pass ✓
- New connection leak tests pass ✓
- Load test (100 concurrent orders): No connection leaks ✓
- Error scenario tests: Connections properly released ✓

**Verification**:
- Deployed to staging
- Ran load test (150 concurrent users)
- No connection pool exhaustion
- No 500 errors
- Connection pool usage: max 15/20 (healthy)

**Ready for Production**: Yes
```

### Context Engineering Success Factors

1. **Progressive Context**: Each agent received context from previous agent plus new context for their specific task
2. **Clear Handoffs**: Each agent's output became input for next agent
3. **Consistent Format**: All context packages followed same structure
4. **Complete Information**: Each agent had everything needed for their task
5. **Traceability**: Full chain of reasoning from bug report to fix

---

## Example 4: Database Migration

### Scenario
An agent needs to migrate a database schema from PostgreSQL to include a new table and modify existing tables, while preserving all data and maintaining zero downtime.

### Task Definition
```markdown
**Task**: Add user preferences table and migrate existing user settings

**Requirements**:
- Create new user_preferences table
- Migrate settings from users.settings (JSONB) to user_preferences (relational)
- Maintain backward compatibility during migration
- Zero downtime (use blue-green migration strategy)
- Rollback plan in case of issues
```

### Context Package

```markdown
# Context Package: User Preferences Database Migration

## Current Schema
```sql
-- users table (existing)
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(100),
  settings JSONB, -- To be migrated
  created_at TIMESTAMP DEFAULT NOW()
);

-- Sample settings data
-- settings: {
--   "theme": "dark",
--   "notifications": {
--     "email": true,
--     "push": false
--   },
--   "language": "en"
-- }
```

## Target Schema
```sql
-- user_preferences table (new)
CREATE TABLE user_preferences (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  theme VARCHAR(20) DEFAULT 'light',
  email_notifications BOOLEAN DEFAULT true,
  push_notifications BOOLEAN DEFAULT false,
  language VARCHAR(10) DEFAULT 'en',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_user_preferences_user_id ON user_preferences(user_id);

-- users table (modified)
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(100),
  settings JSONB, -- Keep for backward compatibility during migration
  created_at TIMESTAMP DEFAULT NOW()
);
```

## Migration Strategy: Blue-Green with Dual Writes

### Phase 1: Preparation
```sql
-- 1. Create new table
CREATE TABLE user_preferences (...);

-- 2. Migrate existing data
INSERT INTO user_preferences (user_id, theme, email_notifications, push_notifications, language)
SELECT 
  id,
  COALESCE(settings->>'theme', 'light'),
  COALESCE((settings->'notifications'->>'email')::boolean, true),
  COALESCE((settings->'notifications'->>'push')::boolean, false),
  COALESCE(settings->>'language', 'en')
FROM users
WHERE settings IS NOT NULL;
```

### Phase 2: Dual Writes (Backward Compatible)
```javascript
// Application code: Write to both old and new schema
async function updateUserPreferences(userId, preferences) {
  const client = await db.pool.connect();
  try {
    await client.query('BEGIN');
    
    // Write to new table
    await client.query(
      `INSERT INTO user_preferences (user_id, theme, email_notifications, push_notifications, language)
       VALUES ($1, $2, $3, $4, $5)
       ON CONFLICT (user_id) DO UPDATE SET
         theme = $2,
         email_notifications = $3,
         push_notifications = $4,
         language = $5,
         updated_at = NOW()`,
      [userId, preferences.theme, preferences.notifications.email, preferences.notifications.push, preferences.language]
    );
    
    // Also write to old JSONB column (backward compatibility)
    await client.query(
      'UPDATE users SET settings = $1 WHERE id = $2',
      [JSON.stringify(preferences), userId]
    );
    
    await client.query('COMMIT');
  } catch (error) {
    await client.query('ROLLBACK');
    throw error;
  } finally {
    client.release();
  }
}
```

### Phase 3: Read from New Schema
```javascript
// Switch reads to new table
async function getUserPreferences(userId) {
  const result = await db.query(
    'SELECT * FROM user_preferences WHERE user_id = $1',
    [userId]
  );
  
  if (result.rows.length === 0) {
    // Fallback to old schema (for users not yet migrated)
    const user = await db.query('SELECT settings FROM users WHERE id = $1', [userId]);
    return user.rows[0]?.settings || getDefaultPreferences();
  }
  
  return result.rows[0];
}
```

### Phase 4: Cleanup (After Verification)
```sql
-- Remove old settings column (only after all data migrated and verified)
ALTER TABLE users DROP COLUMN settings;
```

## Rollback Plan
```sql
-- If issues occur, rollback:
-- 1. Switch reads back to old schema (code deployment)
-- 2. Stop dual writes (code deployment)
-- 3. Drop new table if needed
DROP TABLE user_preferences;
```

## Testing Requirements
```javascript
// Test data migration
it('should migrate all user settings to user_preferences', async () => {
  const users = await db.query('SELECT id, settings FROM users WHERE settings IS NOT NULL');
  const preferences = await db.query('SELECT user_id FROM user_preferences');
  expect(preferences.rows.length).toBe(users.rows.length);
});

// Test dual writes
it('should write to both old and new schema', async () => {
  await updateUserPreferences(userId, newPreferences);
  const oldSettings = await db.query('SELECT settings FROM users WHERE id = $1', [userId]);
  const newPreferences = await db.query('SELECT * FROM user_preferences WHERE user_id = $1', [userId]);
  expect(oldSettings.rows[0].settings.theme).toBe(newPreferences.rows[0].theme);
});

// Test backward compatibility
it('should read from old schema if new schema missing', async () => {
  await db.query('DELETE FROM user_preferences WHERE user_id = $1', [userId]);
  const preferences = await getUserPreferences(userId);
  expect(preferences).toBeDefined();
});
```

## Constraints
- ⚠️ Zero downtime (use blue-green strategy)
- ⚠️ No data loss (verify migration before cleanup)
- ⚠️ Backward compatible (dual writes during transition)
- ⚠️ Rollback plan ready (can revert at any phase)
- ⚠️ Monitor performance (new table should not slow down queries)
```

### Result

With this context package, the agent:
- Created the new user_preferences table with proper indexes
- Migrated all existing data (10,000 users) successfully
- Implemented dual-write strategy for backward compatibility
- Switched reads to new schema with fallback
- Verified data integrity (100% match between old and new)
- Monitored performance (no degradation)
- Cleaned up old schema after 7-day verification period
- Zero downtime achieved
- Rollback plan tested (but not needed)

**Success Rate**: 100% (no data loss, no downtime)

---

## Summary

These examples demonstrate:

1. **REST API Implementation**: Comprehensive context for building new features
2. **Legacy Code Refactoring**: Context for safe, behavior-preserving refactoring
3. **Multi-Agent Bug Fix**: Context coordination across multiple agents
4. **Database Migration**: Context for complex, high-risk operations

Key patterns:
- Hierarchical structure (overview → details)
- Complete behavioral specifications
- Explicit constraints and edge cases
- Testing strategies included
- Rollback/recovery plans
- Metadata for context quality

**Version**: 1.0.0  
**Last Updated**: 2026-09-08
