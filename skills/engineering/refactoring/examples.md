# Refactoring - Examples

## Example 1: Extract Method Refactoring

### Before Refactoring

```javascript
function processOrder(order) {
  // Validate order
  if (!order.items || order.items.length === 0) {
    throw new Error('Order must have items');
  }
  if (!order.customerId) {
    throw new Error('Order must have customer');
  }
  
  // Calculate total
  let subtotal = 0;
  for (let item of order.items) {
    subtotal += item.price * item.quantity;
  }
  const tax = subtotal * 0.08;
  const shipping = subtotal > 100 ? 0 : 10;
  const total = subtotal + tax + shipping;
  
  // Apply discount
  let discount = 0;
  if (order.couponCode) {
    if (order.couponCode === 'SAVE10') {
      discount = total * 0.1;
    } else if (order.couponCode === 'SAVE20') {
      discount = total * 0.2;
    }
  }
  
  const finalTotal = total - discount;
  
  // Create order record
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
  
  return orderRecord;
}
```

**Problems:**
- Method is too long (40+ lines)
- Does multiple things (validate, calculate, create)
- Hard to test individual pieces
- Hard to understand at a glance

### Refactoring Steps

**Step 1: Extract validation**
```javascript
function validateOrder(order) {
  if (!order.items || order.items.length === 0) {
    throw new Error('Order must have items');
  }
  if (!order.customerId) {
    throw new Error('Order must have customer');
  }
}

function processOrder(order) {
  validateOrder(order);
  
  // Calculate total
  let subtotal = 0;
  for (let item of order.items) {
    subtotal += item.price * item.quantity;
  }
  const tax = subtotal * 0.08;
  const shipping = subtotal > 100 ? 0 : 10;
  const total = subtotal + tax + shipping;
  
  // Apply discount
  let discount = 0;
  if (order.couponCode) {
    if (order.couponCode === 'SAVE10') {
      discount = total * 0.1;
    } else if (order.couponCode === 'SAVE20') {
      discount = total * 0.2;
    }
  }
  
  const finalTotal = total - discount;
  
  // Create order record
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
  
  return orderRecord;
}
```

**Step 2: Extract subtotal calculation**
```javascript
function calculateSubtotal(items) {
  let subtotal = 0;
  for (let item of items) {
    subtotal += item.price * item.quantity;
  }
  return subtotal;
}

function processOrder(order) {
  validateOrder(order);
  
  const subtotal = calculateSubtotal(order.items);
  const tax = subtotal * 0.08;
  const shipping = subtotal > 100 ? 0 : 10;
  const total = subtotal + tax + shipping;
  
  // Apply discount
  let discount = 0;
  if (order.couponCode) {
    if (order.couponCode === 'SAVE10') {
      discount = total * 0.1;
    } else if (order.couponCode === 'SAVE20') {
      discount = total * 0.2;
    }
  }
  
  const finalTotal = total - discount;
  
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
  
  return orderRecord;
}
```

**Step 3: Extract discount calculation**
```javascript
function calculateDiscount(total, couponCode) {
  if (!couponCode) return 0;
  
  if (couponCode === 'SAVE10') return total * 0.1;
  if (couponCode === 'SAVE20') return total * 0.2;
  
  return 0;
}

function processOrder(order) {
  validateOrder(order);
  
  const subtotal = calculateSubtotal(order.items);
  const tax = subtotal * 0.08;
  const shipping = subtotal > 100 ? 0 : 10;
  const total = subtotal + tax + shipping;
  const discount = calculateDiscount(total, order.couponCode);
  const finalTotal = total - discount;
  
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
  
  return orderRecord;
}
```

**Step 4: Extract magic numbers**
```javascript
const TAX_RATE = 0.08;
const FREE_SHIPPING_THRESHOLD = 100;
const STANDARD_SHIPPING = 10;

function processOrder(order) {
  validateOrder(order);
  
  const subtotal = calculateSubtotal(order.items);
  const tax = subtotal * TAX_RATE;
  const shipping = subtotal > FREE_SHIPPING_THRESHOLD ? 0 : STANDARD_SHIPPING;
  const total = subtotal + tax + shipping;
  const discount = calculateDiscount(total, order.couponCode);
  const finalTotal = total - discount;
  
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
  
  return orderRecord;
}
```

### After Refactoring

```javascript
const TAX_RATE = 0.08;
const FREE_SHIPPING_THRESHOLD = 100;
const STANDARD_SHIPPING = 10;

function validateOrder(order) {
  if (!order.items || order.items.length === 0) {
    throw new Error('Order must have items');
  }
  if (!order.customerId) {
    throw new Error('Order must have customer');
  }
}

function calculateSubtotal(items) {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}

function calculateDiscount(total, couponCode) {
  if (!couponCode) return 0;
  if (couponCode === 'SAVE10') return total * 0.1;
  if (couponCode === 'SAVE20') return total * 0.2;
  return 0;
}

function processOrder(order) {
  validateOrder(order);
  
  const subtotal = calculateSubtotal(order.items);
  const tax = subtotal * TAX_RATE;
  const shipping = subtotal > FREE_SHIPPING_THRESHOLD ? 0 : STANDARD_SHIPPING;
  const total = subtotal + tax + shipping;
  const discount = calculateDiscount(total, order.couponCode);
  const finalTotal = total - discount;
  
  return {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    subtotal,
    tax,
    shipping,
    discount,
    total: finalTotal,
    status: 'pending',
    createdAt: new Date(),
  };
}
```

**Benefits:**
- Each function has a single responsibility
- Functions are small and focused
- Easy to test each piece independently
- Easy to understand what each function does
- Magic numbers are named constants

---

## Example 2: Replace Conditional with Polymorphism

### Before Refactoring

```python
class PaymentProcessor:
    def process_payment(self, payment_type, amount):
        if payment_type == 'credit_card':
            # Process credit card
            fee = amount * 0.029 + 0.30
            total = amount + fee
            # Charge credit card
            return {'amount': total, 'fee': fee, 'method': 'credit_card'}
        elif payment_type == 'paypal':
            # Process PayPal
            fee = amount * 0.034 + 0.30
            total = amount + fee
            # Charge PayPal
            return {'amount': total, 'fee': fee, 'method': 'paypal'}
        elif payment_type == 'bank_transfer':
            # Process bank transfer
            fee = 0  # No fee
            total = amount
            # Initiate transfer
            return {'amount': total, 'fee': fee, 'method': 'bank_transfer'}
        else:
            raise ValueError(f'Unknown payment type: {payment_type}')
```

**Problems:**
- Long if-elif chain
- Adding new payment type requires modifying this method
- Violates Open/Closed Principle
- Hard to test individual payment types

### After Refactoring

```python
from abc import ABC, abstractmethod

class PaymentMethod(ABC):
    @abstractmethod
    def calculate_fee(self, amount):
        pass
    
    @abstractmethod
    def charge(self, amount):
        pass
    
    def process(self, amount):
        fee = self.calculate_fee(amount)
        total = amount + fee
        self.charge(total)
        return {
            'amount': total,
            'fee': fee,
            'method': self.get_name()
        }
    
    @abstractmethod
    def get_name(self):
        pass

class CreditCardPayment(PaymentMethod):
    def calculate_fee(self, amount):
        return amount * 0.029 + 0.30
    
    def charge(self, amount):
        # Charge credit card
        pass
    
    def get_name(self):
        return 'credit_card'

class PayPalPayment(PaymentMethod):
    def calculate_fee(self, amount):
        return amount * 0.034 + 0.30
    
    def charge(self, amount):
        # Charge PayPal
        pass
    
    def get_name(self):
        return 'paypal'

class BankTransferPayment(PaymentMethod):
    def calculate_fee(self, amount):
        return 0  # No fee
    
    def charge(self, amount):
        # Initiate transfer
        pass
    
    def get_name(self):
        return 'bank_transfer'

class PaymentProcessor:
    def __init__(self):
        self.payment_methods = {
            'credit_card': CreditCardPayment(),
            'paypal': PayPalPayment(),
            'bank_transfer': BankTransferPayment(),
        }
    
    def process_payment(self, payment_type, amount):
        payment_method = self.payment_methods.get(payment_type)
        if not payment_method:
            raise ValueError(f'Unknown payment type: {payment_type}')
        return payment_method.process(amount)
```

**Benefits:**
- Each payment type is a separate class
- Easy to add new payment types (just add a new class)
- Each class can be tested independently
- Follows Open/Closed Principle
- More maintainable and extensible

---

## Example 3: Reduce Duplication

### Before Refactoring

```javascript
class UserService {
  async getUserById(id) {
    try {
      const user = await db.users.findById(id);
      if (!user) {
        logger.warn(`User not found: ${id}`);
        return null;
      }
      logger.info(`User retrieved: ${id}`);
      return user;
    } catch (error) {
      logger.error(`Error retrieving user: ${id}`, error);
      throw error;
    }
  }
  
  async getUserByEmail(email) {
    try {
      const user = await db.users.findByEmail(email);
      if (!user) {
        logger.warn(`User not found: ${email}`);
        return null;
      }
      logger.info(`User retrieved: ${email}`);
      return user;
    } catch (error) {
      logger.error(`Error retrieving user: ${email}`, error);
      throw error;
    }
  }
  
  async getUserByUsername(username) {
    try {
      const user = await db.users.findByUsername(username);
      if (!user) {
        logger.warn(`User not found: ${username}`);
        return null;
      }
      logger.info(`User retrieved: ${username}`);
      return user;
    } catch (error) {
      logger.error(`Error retrieving user: ${username}`, error);
      throw error;
    }
  }
}
```

**Problems:**
- Massive code duplication
- Same error handling in all methods
- Same logging in all methods
- Hard to maintain (change in one place requires changing all)

### After Refactoring

```javascript
class UserService {
  async findUser(finder, identifier) {
    try {
      const user = await finder(identifier);
      if (!user) {
        logger.warn(`User not found: ${identifier}`);
        return null;
      }
      logger.info(`User retrieved: ${identifier}`);
      return user;
    } catch (error) {
      logger.error(`Error retrieving user: ${identifier}`, error);
      throw error;
    }
  }
  
  async getUserById(id) {
    return this.findUser(
      (id) => db.users.findById(id),
      id
    );
  }
  
  async getUserByEmail(email) {
    return this.findUser(
      (email) => db.users.findByEmail(email),
      email
    );
  }
  
  async getUserByUsername(username) {
    return this.findUser(
      (username) => db.users.findByUsername(username),
      username
    );
  }
}
```

**Benefits:**
- No duplication
- Error handling and logging in one place
- Easy to modify behavior (change once, affects all)
- More maintainable
- Shorter, clearer code

---

## Example 4: Simplify Complex Conditional

### Before Refactoring

```javascript
function calculateShipping(order) {
  if (order.total > 100 && order.user.isPremium && order.destination.country === 'US') {
    return 0;
  } else if (order.total > 50 && order.destination.country === 'US') {
    return 5;
  } else if (order.destination.country === 'US') {
    return 10;
  } else if (order.destination.country === 'CA' || order.destination.country === 'MX') {
    return 15;
  } else {
    return 25;
  }
}
```

**Problems:**
- Complex nested conditions
- Hard to understand logic
- Hard to test all paths
- Hard to modify

### After Refactoring

```javascript
function calculateShipping(order) {
  if (isEligibleForFreeShipping(order)) {
    return 0;
  }
  
  if (isUSOrder(order)) {
    return getUSShippingCost(order);
  }
  
  if (isNorthAmericaOrder(order)) {
    return 15;
  }
  
  return 25; // International
}

function isEligibleForFreeShipping(order) {
  return order.total > 100 && 
         order.user.isPremium && 
         isUSOrder(order);
}

function isUSOrder(order) {
  return order.destination.country === 'US';
}

function isNorthAmericaOrder(order) {
  return ['CA', 'MX'].includes(order.destination.country);
}

function getUSShippingCost(order) {
  if (order.total > 50) return 5;
  return 10;
}
```

**Benefits:**
- Clear, readable logic
- Each condition has a descriptive name
- Easy to test each piece
- Easy to modify rules
- Self-documenting code