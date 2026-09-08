# Testing Strategy - Examples

## Example 1: E-commerce Platform Testing Strategy

### Context
- **System**: E-commerce platform with product catalog, shopping cart, checkout, and order management
- **Tech Stack**: React frontend, Node.js backend, PostgreSQL database, Stripe payments
- **Team**: 5 developers, 2 QA engineers
- **Timeline**: 8 weeks to implement testing strategy

### Risk Assessment

| Component | Business Impact | Complexity | Change Freq | Risk Level |
|-----------|----------------|------------|-------------|------------|
| Payment Processing | Critical | High | Low | **High** |
| Checkout Flow | Critical | Medium | Medium | **High** |
| User Authentication | Critical | Medium | Low | **High** |
| Shopping Cart | High | Low | High | **Medium** |
| Product Catalog | Medium | Low | High | **Medium** |
| Order Management | Medium | Medium | Medium | **Medium** |
| Product Search | Low | Medium | Low | **Low** |

### Testing Strategy

#### Unit Testing (60% of tests)
**Framework:** Jest + React Testing Library

**Coverage Goals:**
- Payment processing logic: 95%
- Checkout calculations: 90%
- Cart operations: 80%
- Product catalog: 70%

**What to test:**
- Price calculations (discounts, taxes, shipping)
- Cart operations (add, remove, update quantity)
- Form validation logic
- Data transformations
- Business rules

**Example unit test:**
```javascript
describe('calculateOrderTotal', () => {
  it('should calculate total with tax and shipping', () => {
    const items = [{ price: 100, quantity: 2 }];
    const tax = 0.08;
    const shipping = 10;
    
    const total = calculateOrderTotal(items, tax, shipping);
    
    expect(total).toBe(226); // (200 * 1.08) + 10
  });
  
  it('should apply discount before tax', () => {
    const items = [{ price: 100, quantity: 1 }];
    const discount = 20;
    const tax = 0.1;
    
    const total = calculateOrderTotal(items, tax, 0, discount);
    
    expect(total).toBe(88); // (100 - 20) * 1.1
  });
});
```

#### Integration Testing (30% of tests)
**Framework:** Supertest + Testcontainers

**What to test:**
- All API endpoints
- Database operations
- Stripe payment integration
- Email service integration
- Authentication flows

**Example integration test:**
```javascript
describe('POST /api/orders', () => {
  it('should create order and charge payment', async () => {
    const order = {
      items: [{ productId: '123', quantity: 2 }],
      paymentMethod: 'card',
      cardToken: 'tok_visa',
    };
    
    const response = await request(app)
      .post('/api/orders')
      .send(order)
      .expect(201);
    
    expect(response.body).toHaveProperty('orderId');
    expect(response.body.status).toBe('paid');
    
    // Verify order in database
    const dbOrder = await db.orders.findById(response.body.orderId);
    expect(dbOrder.status).toBe('paid');
  });
});
```

#### E2E Testing (10% of tests)
**Framework:** Playwright

**Critical Journeys:**
1. Browse products → Add to cart → Checkout → Payment → Confirmation
2. User registration → Login → Browse → Logout
3. Add to cart → Apply coupon → Checkout
4. Guest checkout flow

**Example E2E test:**
```javascript
test('complete checkout flow', async ({ page }) => {
  // Browse products
  await page.goto('/products');
  await page.click('text=Add to Cart');
  
  // View cart
  await page.click('text=Cart (1)');
  await expect(page.locator('.cart-item')).toHaveCount(1);
  
  // Checkout
  await page.click('text=Checkout');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="cardNumber"]', '4242424242424242');
  await page.fill('[name="expiry"]', '12/25');
  await page.fill('[name="cvc"]', '123');
  
  // Complete order
  await page.click('text=Place Order');
  
  // Verify confirmation
  await expect(page.locator('text=Order Confirmed')).toBeVisible();
  await expect(page.locator('.order-number')).toBeVisible();
});
```

### Test Automation Plan

**Phase 1 (Weeks 1-2): Infrastructure**
- Set up Jest for unit tests
- Configure Supertest for API tests
- Set up Playwright for E2E tests
- Configure code coverage reporting
- Integrate with CI/CD

**Phase 2 (Weeks 3-4): Critical Path Tests**
- Unit tests for payment calculations
- Integration tests for payment API
- E2E test for complete checkout flow
- Unit tests for cart operations

**Phase 3 (Weeks 5-6): Expand Coverage**
- Unit tests for product catalog
- Integration tests for all API endpoints
- E2E tests for user authentication
- Performance tests for product search

**Phase 4 (Weeks 7-8): Polish and Optimize**
- Achieve 80% overall coverage
- Fix flaky tests
- Optimize test execution time
- Document testing practices

### Quality Gates

**Code Coverage:**
- Overall: 80%
- Payment processing: 95%
- Checkout flow: 90%
- New code: 85%

**Test Execution:**
- Unit tests: < 2 minutes
- Integration tests: < 5 minutes
- E2E tests: < 10 minutes
- All tests: < 15 minutes

**Performance:**
- API response time: < 200ms (p95)
- Checkout completion: < 5 seconds
- Product search: < 1 second

---

## Example 2: SaaS Application Testing Strategy

### Context
- **System**: Multi-tenant project management SaaS
- **Tech Stack**: Vue.js frontend, Python/Django backend, PostgreSQL, Redis
- **Team**: 8 developers, 1 QA engineer
- **Focus**: Tenant isolation, data security, performance

### Risk Assessment

**High Risk:**
- Tenant data isolation
- User permissions and access control
- Data export/import
- Billing and subscription management

**Medium Risk:**
- Project collaboration features
- Real-time updates (WebSocket)
- File uploads
- Email notifications

**Low Risk:**
- UI styling
- Dashboard widgets
- User preferences

### Testing Strategy

#### Unit Testing (55% of tests)
**Framework:** pytest + pytest-django

**Focus Areas:**
- Tenant isolation logic
- Permission checking
- Business rules
- Data validation

**Example:**
```python
def test_tenant_isolation():
    """Users should only see data from their tenant"""
    tenant1 = Tenant.objects.create(name="Tenant 1")
    tenant2 = Tenant.objects.create(name="Tenant 2")
    
    user1 = User.objects.create(email="user1@t1.com", tenant=tenant1)
    user2 = User.objects.create(email="user2@t2.com", tenant=tenant2)
    
    project1 = Project.objects.create(name="Project 1", tenant=tenant1)
    project2 = Project.objects.create(name="Project 2", tenant=tenant2)
    
    # User 1 should only see Project 1
    projects = Project.objects.for_user(user1)
    assert projects.count() == 1
    assert projects.first() == project1
    
    # User 2 should only see Project 2
    projects = Project.objects.for_user(user2)
    assert projects.count() == 1
    assert projects.first() == project2
```

#### Integration Testing (35% of tests)
**Framework:** pytest + Django REST framework test client

**Focus Areas:**
- API endpoint security
- Tenant isolation in APIs
- Database queries
- Cache invalidation
- WebSocket connections

**Example:**
```python
def test_api_tenant_isolation(api_client, tenant1, tenant2, user1, user2):
    """API should enforce tenant isolation"""
    project1 = Project.objects.create(name="Project 1", tenant=tenant1)
    project2 = Project.objects.create(name="Project 2", tenant=tenant2)
    
    # User 1 should access Project 1
    api_client.force_authenticate(user=user1)
    response = api_client.get(f'/api/projects/{project1.id}/')
    assert response.status_code == 200
    
    # User 1 should NOT access Project 2
    response = api_client.get(f'/api/projects/{project2.id}/')
    assert response.status_code == 404  # Not found (not 403 to avoid info leak)
```

#### E2E Testing (10% of tests)
**Framework:** Cypress

**Critical Journeys:**
1. Sign up → Create project → Invite team member → Collaborate
2. Upload file → Share → Download
3. Create task → Assign → Complete → Archive
4. Export data → Verify contents

### Additional Testing

**Security Testing:**
- Penetration testing (quarterly)
- Dependency vulnerability scanning (weekly)
- OWASP Top 10 testing
- Tenant isolation verification

**Performance Testing:**
- Load testing: 1000 concurrent users
- Database query optimization
- Cache hit rate monitoring
- WebSocket connection limits

**Accessibility Testing:**
- WCAG 2.1 AA compliance
- Screen reader testing
- Keyboard navigation

### Quality Gates

**Security:**
- No high/critical vulnerabilities
- All tenant isolation tests pass
- Permission tests at 100% coverage

**Performance:**
- API response: < 100ms (p95)
- WebSocket latency: < 50ms
- Database queries: < 50ms
- Page load: < 2 seconds

---

## Example 3: Mobile App Testing Strategy

### Context
- **System**: Social media mobile app (iOS + Android)
- **Tech Stack**: React Native, Node.js backend, MongoDB
- **Team**: 4 mobile developers, 2 backend developers, 1 QA

### Testing Strategy

#### Unit Testing (50% of tests)
**Framework:** Jest + React Native Testing Library

**Focus:**
- Business logic
- State management (Redux)
- Data transformations
- Utility functions

#### Integration Testing (30% of tests)
**Framework:** Detox (E2E framework that also works for integration)

**Focus:**
- Navigation flows
- API integration
- Local storage
- Push notifications
- Camera/photo library integration

#### E2E Testing (20% of tests)
**Framework:** Detox

**Critical Journeys:**
1. Sign up → Create profile → Post photo → Share
2. Browse feed → Like → Comment → Share
3. Search users → Follow → View profile
4. Receive notification → Open → Interact

**Example:**
```javascript
describe('Post creation flow', () => {
  it('should create and publish a post', async () => {
    // Login
    await element(by.id('email-input')).typeText('test@example.com');
    await element(by.id('password-input')).typeText('password123');
    await element(by.id('login-button')).tap();
    
    // Create post
    await element(by.id('create-post-button')).tap();
    await element(by.id('photo-picker')).tap();
    await element(by.id('camera-roll-photo-1')).tap();
    await element(by.id('caption-input')).typeText('My first post!');
    await element(by.id('publish-button')).tap();
    
    // Verify post appears in feed
    await expect(element(by.text('My first post!'))).toBeVisible();
  });
});
```

### Device Testing

**iOS:**
- iPhone 12 (iOS 15)
- iPhone 13 Pro (iOS 16)
- iPad Air (iOS 16)

**Android:**
- Samsung Galaxy S21 (Android 12)
- Google Pixel 6 (Android 13)
- Samsung Galaxy Tab (Android 12)

### Performance Testing

**Metrics:**
- App launch time: < 2 seconds
- Screen transition: < 300ms
- Image loading: < 1 second
- API response: < 500ms
- Memory usage: < 200MB
- Battery drain: < 5% per hour

### Quality Gates

**Functionality:**
- All critical journeys pass
- No crashes in test runs
- All permissions handled correctly

**Performance:**
- App size: < 50MB
- Launch time: < 2 seconds
- Memory: < 200MB
- Battery: < 5%/hour

**Compatibility:**
- Works on iOS 14+
- Works on Android 10+
- Supports all screen sizes