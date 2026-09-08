# Requirements Analysis - Examples

## Example 1: E-commerce Checkout Feature

### Initial Requirements

**Stakeholder request:**
> "We need to add a checkout feature to our e-commerce site. Users should be able to buy products easily."

### Requirements Analysis Output

#### Functional Requirements

**FR-1**: Users shall be able to add products to a shopping cart
**FR-2**: Users shall be able to view cart contents with item details and total price
**FR-3**: Users shall be able to update quantities or remove items from cart
**FR-4**: Users shall be able to apply discount codes at checkout
**FR-5**: Users shall be able to select shipping method (standard, express)
**FR-6**: Users shall be able to enter shipping address
**FR-7**: Users shall be able to enter payment information (credit card)
**FR-8**: System shall validate payment information before processing
**FR-9**: System shall process payment via payment gateway
**FR-10**: System shall send order confirmation email after successful payment
**FR-11**: Users shall be able to view order history

#### Non-Functional Requirements

**Performance:**
- NFR-P-1: Checkout page shall load in < 2 seconds
- NFR-P-2: Payment processing shall complete in < 5 seconds
- NFR-P-3: System shall handle 1,000 concurrent checkouts

**Security:**
- NFR-S-1: Payment information shall be transmitted over HTTPS
- NFR-S-2: Credit card numbers shall not be stored (use tokenization)
- NFR-S-3: System shall comply with PCI DSS requirements
- NFR-S-4: System shall implement CSRF protection

**Reliability:**
- NFR-R-1: Payment failures shall be logged and retryable
- NFR-R-2: Cart data shall persist for 30 days
- NFR-R-3: System shall have 99.9% uptime during business hours

**Usability:**
- NFR-U-1: Checkout flow shall be completable in < 3 minutes
- NFR-U-2: Checkout shall work on mobile devices (responsive)
- NFR-U-3: Error messages shall be clear and actionable

#### Acceptance Criteria

**AC-1 (Add to Cart):**
```
Given a user is viewing a product
When they click "Add to Cart"
Then the product is added to their cart
And the cart icon shows updated item count
```

**AC-2 (Apply Discount Code):**
```
Given a user has items in their cart
When they enter a valid discount code
Then the discount is applied to the total
And the discounted price is displayed

Given a user enters an invalid discount code
When they click "Apply"
Then an error message is displayed
And the total price remains unchanged
```

**AC-3 (Payment Processing):**
```
Given a user has entered valid payment information
When they click "Place Order"
Then payment is processed via payment gateway
And order confirmation is displayed
And confirmation email is sent within 2 minutes

Given payment processing fails
When the failure occurs
Then user sees error message with retry option
And cart contents are preserved
```

#### Assumptions

- A-1: Users have valid email addresses - If wrong, need alternative notification method
- A-2: Payment gateway API is available 99.9% of time - If wrong, need fallback payment method
- A-3: Users are familiar with online checkout - If wrong, need more guidance/tooltips
- A-4: Shipping addresses are within supported regions - If wrong, need region validation

#### Open Questions

- Q-1: Should we support guest checkout or require account creation? | Owner: Product | Priority: H | Status: Open
- Q-2: What payment methods should we support (credit card, PayPal, Apple Pay)? | Owner: Product | Priority: H | Status: Open
- Q-3: Should we support international shipping? | Owner: Business | Priority: M | Status: Open
- Q-4: What is the tax calculation logic? | Owner: Finance | Priority: H | Status: Open
- Q-5: Should we save payment methods for future use? | Owner: Product | Priority: M | Status: Open

#### Scope

**In Scope:**
- Shopping cart functionality
- Checkout flow
- Payment processing (credit card)
- Order confirmation
- Email notifications

**Out of Scope:**
- Inventory management (handled by existing system)
- Shipping fulfillment (handled by existing system)
- Customer support features
- Loyalty/rewards program
- Gift wrapping options

---

## Example 2: User Authentication System

### Initial Requirements

**Stakeholder request:**
> "We need a secure login system for our application. Users should be able to sign up and log in."

### Requirements Analysis Output

#### Functional Requirements

**FR-1**: Users shall be able to register with email and password
**FR-2**: System shall validate email format and password strength
**FR-3**: System shall send email verification after registration
**FR-4**: Users shall be able to log in with email and password
**FR-5**: Users shall be able to log out
**FR-6**: Users shall be able to reset forgotten password via email
**FR-7**: System shall support session management
**FR-8**: System shall support "Remember Me" functionality
**FR-9**: Users shall be able to change password when logged in
**FR-10**: System shall lock account after 5 failed login attempts

#### Non-Functional Requirements

**Security:**
- NFR-S-1: Passwords shall be hashed using bcrypt (cost factor 12)
- NFR-S-2: Password reset tokens shall expire after 1 hour
- NFR-S-3: Sessions shall expire after 24 hours of inactivity
- NFR-S-4: System shall implement rate limiting (5 login attempts per minute)
- NFR-S-5: All authentication endpoints shall use HTTPS
- NFR-S-6: System shall log all authentication events

**Performance:**
- NFR-P-1: Login shall complete in < 500ms
- NFR-P-2: Registration shall complete in < 1 second
- NFR-P-3: Password reset email shall be sent within 2 minutes

**Reliability:**
- NFR-R-1: Authentication service shall have 99.95% uptime
- NFR-R-2: Failed authentication attempts shall be logged
- NFR-R-3: System shall gracefully handle email service failures

**Usability:**
- NFR-U-1: Password requirements shall be clearly displayed
- NFR-U-2: Error messages shall not reveal whether email exists
- NFR-U-3: Login form shall be accessible (WCAG 2.1 AA)

#### Acceptance Criteria

**AC-1 (Registration):**
```
Given a new user visits the registration page
When they enter valid email and password
Then account is created
And verification email is sent
And user is redirected to "Check your email" page

Given a user enters an email that already exists
When they submit registration form
Then error message is displayed
And account is not created

Given a user enters a weak password
When they submit registration form
Then password strength error is displayed
And account is not created
```

**AC-2 (Login):**
```
Given a registered user with verified email
When they enter correct email and password
Then they are logged in
And redirected to dashboard
And session is created

Given a user enters incorrect password
When they submit login form
Then generic error message is displayed
And login attempt is logged
And they remain on login page
```

**AC-3 (Password Reset):**
```
Given a user has forgotten their password
When they enter their email on password reset page
Then password reset email is sent
And user sees "Check your email" message

Given a user clicks valid reset link
When they enter new password
Then password is updated
And they are redirected to login page
And all existing sessions are invalidated
```

**AC-4 (Account Lockout):**
```
Given a user has failed login 5 times
When they attempt 6th login
Then account is locked for 30 minutes
And user sees lockout message
And security team is notified
```

#### Assumptions

- A-1: Users have access to email - If wrong, need SMS verification option
- A-2: Email service is reliable - If wrong, need retry mechanism
- A-3: Users can remember passwords - If wrong, need social login option
- A-4: HTTPS is available - If wrong, cannot proceed (security requirement)

#### Open Questions

- Q-1: Should we support social login (Google, GitHub)? | Owner: Product | Priority: M | Status: Open
- Q-2: Should we support two-factor authentication (2FA)? | Owner: Security | Priority: H | Status: Open
- Q-3: What is the password policy (min length, complexity)? | Owner: Security | Priority: H | Status: Open
- Q-4: How long should sessions last? | Owner: Security | Priority: H | Status: Answered (24 hours)
- Q-5: Should we support SSO for enterprise customers? | Owner: Business | Priority: L | Status: Open

#### Scope

**In Scope:**
- Email/password registration
- Email verification
- Login/logout
- Password reset
- Session management
- Account lockout

**Out of Scope:**
- Social login (deferred to Phase 2)
- Two-factor authentication (deferred to Phase 2)
- SSO (deferred to enterprise version)
- User profile management
- Role-based access control (separate feature)

---

## Example 3: API Performance Monitoring

### Initial Requirements

**Stakeholder request:**
> "Our API is sometimes slow. We need to monitor performance and get alerts when there are issues."

### Requirements Analysis Output

#### Functional Requirements

**FR-1**: System shall track API response times for all endpoints
**FR-2**: System shall track API error rates
**FR-3**: System shall track API request volume
**FR-4**: System shall provide real-time dashboard showing key metrics
**FR-5**: System shall send alerts when response time exceeds threshold
**FR-6**: System shall send alerts when error rate exceeds threshold
**FR-7**: System shall provide historical performance data (30 days)
**FR-8**: System shall identify slow endpoints
**FR-9**: System shall track performance by endpoint, method, and status code
**FR-10**: System shall provide performance reports

#### Non-Functional Requirements

**Performance:**
- NFR-P-1: Monitoring shall add < 5ms overhead to API requests
- NFR-P-2: Dashboard shall load in < 2 seconds
- NFR-P-3: Alerts shall be sent within 1 minute of threshold breach

**Reliability:**
- NFR-R-1: Monitoring system shall have 99.9% uptime
- NFR-R-2: Monitoring data shall be retained for 30 days
- NFR-R-3: Monitoring failures shall not affect API functionality

**Scalability:**
- NFR-SC-1: System shall handle monitoring 10,000 requests/second
- NFR-SC-2: System shall store up to 100M data points

**Usability:**
- NFR-U-1: Dashboard shall be accessible to non-technical users
- NFR-U-2: Alerts shall include actionable information

#### Acceptance Criteria

**AC-1 (Response Time Tracking):**
```
Given an API request is made
When the request completes
Then response time is recorded
And stored with endpoint, method, status code, timestamp
```

**AC-2 (Performance Alert):**
```
Given API response time exceeds 500ms for 5 consecutive requests
When the threshold is breached
Then alert is sent to on-call engineer
And alert includes endpoint, average response time, time range
```

**AC-3 (Dashboard):**
```
Given a user opens the monitoring dashboard
When the page loads
Then they see:
  - Average response time (last hour, last 24 hours)
  - Error rate (last hour, last 24 hours)
  - Request volume (last hour, last 24 hours)
  - Top 10 slowest endpoints
  - Recent alerts
```

#### Assumptions

- A-1: API framework supports middleware/interceptors - If wrong, need alternative instrumentation
- A-2: Team has access to monitoring infrastructure - If wrong, need to provision
- A-3: On-call rotation exists - If wrong, need to set up

#### Open Questions

- Q-1: What are the acceptable response time thresholds? | Owner: Engineering | Priority: H | Status: Open
- Q-2: What alert channels should we use (email, Slack, PagerDuty)? | Owner: Operations | Priority: H | Status: Open
- Q-3: Should we track database query performance separately? | Owner: Engineering | Priority: M | Status: Open
- Q-4: Do we need distributed tracing? | Owner: Engineering | Priority: M | Status: Open

#### Scope

**In Scope:**
- Response time tracking
- Error rate tracking
- Request volume tracking
- Real-time dashboard
- Alerting
- Historical data (30 days)

**Out of Scope:**
- Distributed tracing (deferred)
- Database query monitoring (separate project)
- Log aggregation (existing tool)
- Custom metrics (Phase 2)
- Anomaly detection (Phase 2)
