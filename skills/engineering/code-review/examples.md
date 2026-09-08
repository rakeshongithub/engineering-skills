# Code Review - Examples

## Example 1: API Endpoint Review

### Code Being Reviewed

```javascript
// POST /api/users
app.post('/api/users', async (req, res) => {
  const { name, email, password } = req.body;
  
  const user = await db.query(
    `INSERT INTO users (name, email, password) VALUES ('${name}', '${email}', '${password}')`
  );
  
  res.json({ id: user.id, name, email });
});
```

### Review Comments

#### Critical Issues

**1. SQL Injection Vulnerability**
```
[Critical] SQL injection vulnerability on line 5-7

The query concatenates user input directly into SQL, allowing SQL injection attacks.
An attacker could send: email = "'; DROP TABLE users; --"

Suggested fix:
Use parameterized queries:

const user = await db.query(
  'INSERT INTO users (name, email, password) VALUES (?, ?, ?)',
  [name, email, password]
);
```

**2. Storing Plaintext Passwords**
```
[Critical] Password stored in plaintext on line 6

Passwords must be hashed before storage. Storing plaintext passwords is a severe security vulnerability.

Suggested fix:
const bcrypt = require('bcrypt');
const hashedPassword = await bcrypt.hash(password, 10);

const user = await db.query(
  'INSERT INTO users (name, email, password) VALUES (?, ?, ?)',
  [name, email, hashedPassword]
);
```

#### Important Issues

**3. Missing Input Validation**
```
[Important] No input validation on line 3

The code doesn't validate inputs. Users could submit invalid emails, empty names, or weak passwords.

Suggested fix:
const { body } = req;
const { error, value } = userSchema.validate(body);
if (error) {
  return res.status(400).json({ error: error.details[0].message });
}
const { name, email, password } = value;
```

**4. Missing Error Handling**
```
[Important] No error handling for database operation

If the database query fails (e.g., duplicate email), the error will crash the server or leak error details to the client.

Suggested fix:
try {
  const user = await db.query(...);
  res.json({ id: user.id, name, email });
} catch (error) {
  if (error.code === 'ER_DUP_ENTRY') {
    return res.status(409).json({ error: 'Email already exists' });
  }
  logger.error('Failed to create user', error);
  res.status(500).json({ error: 'Internal server error' });
}
```

**5. Missing Authentication**
```
[Important] No authentication check

This endpoint allows anyone to create users. Should this require admin authentication?

Question: Is this a public registration endpoint or an admin-only endpoint?
```

#### Suggestions

**6. Missing Tests**
```
[Suggestion] Add tests for this endpoint

Consider adding tests for:
- Successful user creation
- Duplicate email handling
- Invalid input validation
- SQL injection attempts
```

### Improved Code

```javascript
const bcrypt = require('bcrypt');
const { userSchema } = require('./validation');

app.post('/api/users', async (req, res) => {
  // Validate input
  const { error, value } = userSchema.validate(req.body);
  if (error) {
    return res.status(400).json({ error: error.details[0].message });
  }
  
  const { name, email, password } = value;
  
  try {
    // Hash password
    const hashedPassword = await bcrypt.hash(password, 10);
    
    // Insert user with parameterized query
    const user = await db.query(
      'INSERT INTO users (name, email, password) VALUES (?, ?, ?)',
      [name, email, hashedPassword]
    );
    
    // Return user without password
    res.status(201).json({ 
      id: user.id, 
      name: user.name, 
      email: user.email 
    });
  } catch (error) {
    if (error.code === 'ER_DUP_ENTRY') {
      return res.status(409).json({ error: 'Email already exists' });
    }
    logger.error('Failed to create user', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});
```

**Decision: Request Changes** (Critical security issues must be fixed)

---

## Example 2: React Component Review

### Code Being Reviewed

```javascript
function UserList() {
  const [users, setUsers] = useState([]);
  
  fetch('/api/users')
    .then(res => res.json())
    .then(data => setUsers(data));
  
  return (
    <div>
      {users.map(user => (
        <div key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}
```

### Review Comments

#### Critical Issues

**1. Infinite Loop**
```
[Critical] Infinite render loop on lines 4-6

The fetch is called on every render, which triggers a state update, which triggers another render, creating an infinite loop.

Suggested fix:
Use useEffect with empty dependency array:

useEffect(() => {
  fetch('/api/users')
    .then(res => res.json())
    .then(data => setUsers(data));
}, []);
```

#### Important Issues

**2. Missing Error Handling**
```
[Important] No error handling for fetch

If the API call fails, the user sees nothing and errors are silent.

Suggested fix:
const [error, setError] = useState(null);

useEffect(() => {
  fetch('/api/users')
    .then(res => {
      if (!res.ok) throw new Error('Failed to fetch users');
      return res.json();
    })
    .then(data => setUsers(data))
    .catch(err => setError(err.message));
}, []);

if (error) return <div>Error: {error}</div>;
```

**3. Missing Loading State**
```
[Important] No loading indicator

Users see an empty list while data is loading, which is confusing.

Suggested fix:
const [loading, setLoading] = useState(true);

useEffect(() => {
  fetch('/api/users')
    .then(res => res.json())
    .then(data => {
      setUsers(data);
      setLoading(false);
    });
}, []);

if (loading) return <div>Loading...</div>;
```

#### Suggestions

**4. Consider Custom Hook**
```
[Suggestion] Extract data fetching to custom hook

This pattern is reusable. Consider creating a useFetch hook:

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, [url]);
  
  return { data, loading, error };
}

// Usage:
const { data: users, loading, error } = useFetch('/api/users');
```

**5. Accessibility**
```
[Suggestion] Add semantic HTML

Consider using semantic HTML for better accessibility:

<ul>
  {users.map(user => (
    <li key={user.id}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </li>
  ))}
</ul>
```

### Improved Code

```javascript
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch('/api/users')
      .then(res => {
        if (!res.ok) throw new Error('Failed to fetch users');
        return res.json();
      })
      .then(data => {
        setUsers(data);
        setLoading(false);
      })
      .catch(err => {
        setError(err.message);
        setLoading(false);
      });
  }, []);
  
  if (loading) return <div>Loading users...</div>;
  if (error) return <div>Error: {error}</div>;
  
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </li>
      ))}
    </ul>
  );
}
```

**Decision: Request Changes** (Critical infinite loop issue)

---

## Example 3: Utility Function Review

### Code Being Reviewed

```python
def calculate_discount(price, discount_percent):
    discount = price * discount_percent / 100
    return price - discount
```

### Review Comments

#### Important Issues

**1. Missing Input Validation**
```
[Important] No input validation

The function doesn't validate inputs. Negative prices or discounts > 100% would produce incorrect results.

Suggested fix:
def calculate_discount(price, discount_percent):
    if price < 0:
        raise ValueError("Price cannot be negative")
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")
    
    discount = price * discount_percent / 100
    return price - discount
```

**2. Missing Type Hints**
```
[Important] Missing type hints

Python type hints improve code clarity and enable static type checking.

Suggested fix:
def calculate_discount(price: float, discount_percent: float) -> float:
    ...
```

**3. Missing Tests**
```
[Important] No tests found

Utility functions should have comprehensive tests.

Suggested tests:
- Normal case: calculate_discount(100, 10) == 90
- Zero discount: calculate_discount(100, 0) == 100
- Full discount: calculate_discount(100, 100) == 0
- Negative price: raises ValueError
- Invalid discount: raises ValueError
```

#### Suggestions

**4. Add Docstring**
```
[Suggestion] Add docstring

Docstrings help other developers understand the function.

Suggested docstring:
"""
Calculate the final price after applying a percentage discount.

Args:
    price: The original price (must be non-negative)
    discount_percent: The discount percentage (0-100)

Returns:
    The final price after discount

Raises:
    ValueError: If price is negative or discount is not in range 0-100

Example:
    >>> calculate_discount(100, 10)
    90.0
"""
```

**5. Consider Rounding**
```
[Suggestion] Consider rounding for currency

Prices should typically be rounded to 2 decimal places.

Suggested addition:
return round(price - discount, 2)
```

### Improved Code

```python
def calculate_discount(price: float, discount_percent: float) -> float:
    """
    Calculate the final price after applying a percentage discount.
    
    Args:
        price: The original price (must be non-negative)
        discount_percent: The discount percentage (0-100)
    
    Returns:
        The final price after discount, rounded to 2 decimal places
    
    Raises:
        ValueError: If price is negative or discount is not in range 0-100
    
    Example:
        >>> calculate_discount(100, 10)
        90.0
    """
    if price < 0:
        raise ValueError("Price cannot be negative")
    if discount_percent < 0 or discount_percent > 100:
        raise ValueError("Discount must be between 0 and 100")
    
    discount = price * discount_percent / 100
    return round(price - discount, 2)
```

**Decision: Request Changes** (Missing validation and tests)

---

## Example 4: Good Code Review (Approval)

### Code Being Reviewed

```typescript
import { z } from 'zod';
import { logger } from './logger';
import { sendEmail } from './email-service';

const orderSchema = z.object({
  userId: z.string().uuid(),
  items: z.array(z.object({
    productId: z.string(),
    quantity: z.number().int().positive(),
  })).min(1),
  totalAmount: z.number().positive(),
});

export async function createOrder(data: unknown) {
  // Validate input
  const result = orderSchema.safeParse(data);
  if (!result.success) {
    logger.warn('Invalid order data', { errors: result.error });
    throw new Error('Invalid order data');
  }
  
  const order = result.data;
  
  try {
    // Create order in database
    const createdOrder = await db.orders.create({
      data: {
        userId: order.userId,
        items: order.items,
        totalAmount: order.totalAmount,
        status: 'pending',
      },
    });
    
    // Send confirmation email (async, don't wait)
    sendEmail({
      to: order.userId,
      template: 'order-confirmation',
      data: { orderId: createdOrder.id },
    }).catch(err => {
      logger.error('Failed to send order confirmation email', err);
    });
    
    logger.info('Order created', { orderId: createdOrder.id });
    return createdOrder;
  } catch (error) {
    logger.error('Failed to create order', { error, order });
    throw new Error('Failed to create order');
  }
}
```

### Review Comments

#### Praise

**1. Excellent Input Validation**
```
[Praise] Great use of Zod for validation!

I really like how you're using Zod for runtime type validation. This ensures type safety at runtime, not just compile time.
```

**2. Good Error Handling**
```
[Praise] Comprehensive error handling

You've handled errors at multiple levels:
- Input validation errors
- Database errors
- Email sending errors (without blocking the main flow)

This is exactly the right approach.
```

**3. Good Logging**
```
[Praise] Excellent logging practices

You're logging at appropriate levels (warn, info, error) with relevant context. This will make debugging much easier.
```

#### Suggestions

**4. Consider Transaction**
```
[Suggestion] Consider using a database transaction

If order creation involves multiple database operations in the future, consider wrapping in a transaction:

const createdOrder = await db.$transaction(async (tx) => {
  const order = await tx.orders.create(...);
  // Other operations
  return order;
});
```

**5. Add Tests**
```
[Suggestion] Add tests

This function would benefit from tests covering:
- Successful order creation
- Invalid input validation
- Database error handling
- Email sending failure (should not fail the order)
```

### Summary

```
This is excellent code! The validation, error handling, and logging are all well-implemented.

Only minor suggestions:
- Consider adding tests
- Consider transactions for future-proofing

Approving - great work! ✅
```

**Decision: Approve** (High quality code with only minor suggestions)