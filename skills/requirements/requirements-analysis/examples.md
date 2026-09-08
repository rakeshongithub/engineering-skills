# Requirements Analysis - Practical Examples

## Example 1: User Authentication Feature

### Input
Stakeholder request: "We need users to be able to log in to the application."

### Analysis Process

#### Step 1: Initial Questions
- What authentication methods? (username/password, social login, SSO, MFA?)
- Who are the users? (customers, employees, partners?)
- What happens after login? (redirect where?)
- Password requirements?
- Session duration?
- Forgot password flow?
- Account lockout policy?

#### Step 2: Extracted Requirements

**Functional Requirements:**
- FR-1: Users can register with email and password
- FR-2: Users can log in with email and password
- FR-3: Users can reset forgotten passwords via email
- FR-4: Users can log out
- FR-5: System locks account after 5 failed login attempts
- FR-6: Users can unlock account via email verification

**Non-Functional Requirements:**
- NFR-1: Passwords must meet complexity requirements (min 8 chars, 1 uppercase, 1 number, 1 special char)
- NFR-2: Passwords must be hashed using bcrypt with cost factor 12
- NFR-3: Login response time < 500ms (P95)
- NFR-4: Session timeout after 30 minutes of inactivity
- NFR-5: Support 100 concurrent login requests
- NFR-6: Comply with OWASP authentication guidelines

**Security Requirements:**
- SEC-1: Implement rate limiting (max 5 login attempts per minute per IP)
- SEC-2: Use HTTPS for all authentication endpoints
- SEC-3: Set secure, httpOnly cookies for session tokens
- SEC-4: Log all authentication events (success and failure)
- SEC-5: Implement CSRF protection

#### Step 3: Acceptance Criteria

**AC-1: User Registration**
```
Given a new user on the registration page
When they enter valid email and password
And click "Register"
Then an account is created
And a verification email is sent
And they are redirected to "check your email" page
```

**AC-2: User Login**
```
Given a registered user with verified email
When they enter correct email and password
And click "Login"
Then they are authenticated
And redirected to dashboard
And session cookie is set
```

**AC-3: Failed Login**
```
Given a user entering incorrect password
When they submit login form
Then login fails
And error message displays "Invalid email or password"
And failed attempt is logged
And attempt counter increments
```

**AC-4: Account Lockout**
```
Given a user with 4 failed login attempts
When they fail login a 5th time
Then account is locked
And error message displays "Account locked. Check email for unlock instructions."
And unlock email is sent
```

### Output Document Structure

```markdown
# User Authentication Requirements

## 1. Overview
Implement secure user authentication system supporting registration, login, logout, and password reset.

## 2. Functional Requirements
[FR-1 through FR-6 detailed above]

## 3. Non-Functional Requirements
[NFR-1 through NFR-6 detailed above]

## 4. Security Requirements
[SEC-1 through SEC-5 detailed above]

## 5. User Flows
[Diagrams for registration, login, password reset]

## 6. Acceptance Criteria
[AC-1 through AC-4 detailed above]

## 7. Out of Scope
- Social login (OAuth)
- Multi-factor authentication
- Single sign-on (SSO)
- Biometric authentication

## 8. Assumptions
- Email service is available and configured
- HTTPS is enabled on all environments
- Database supports encrypted storage

## 9. Dependencies
- Email service integration
- Session management infrastructure
- User database schema
```

---

## Example 2: E-commerce Search Feature

### Input
Product owner: "Customers should be able to search for products easily and find what they're looking for quickly."

### Analysis Process

#### Clarification Questions Asked
1. What can users search by? (product name, description, SKU, category, brand?)
2. Should search support filters? (price range, category, brand, ratings?)
3. Should search support sorting? (relevance, price, popularity, newest?)
4. Should there be autocomplete/suggestions?
5. How should search handle typos or misspellings?
6. What's the expected search volume? (requests per second)
7. What's acceptable search response time?
8. Should search results be personalized?

#### Extracted Requirements

**Functional Requirements:**
- FR-1: Users can search products by keyword
- FR-2: Search matches against product name, description, brand, and category
- FR-3: Search results display product image, name, price, rating, and availability
- FR-4: Users can filter results by category, price range, brand, and rating
- FR-5: Users can sort results by relevance, price (low-high, high-low), rating, and newest
- FR-6: Search provides autocomplete suggestions as user types
- FR-7: Search handles common misspellings and typos
- FR-8: Empty search returns all products (with filters available)
- FR-9: Search results paginate (20 products per page)
- FR-10: Users can save search queries

**Non-Functional Requirements:**
- NFR-1: Search response time < 200ms (P95)
- NFR-2: Autocomplete response time < 100ms (P95)
- NFR-3: Support 500 concurrent search requests
- NFR-4: Search index updates within 5 minutes of product changes
- NFR-5: Search available 99.9% of time
- NFR-6: Relevance scoring produces useful results (measured by click-through rate > 60%)

**User Experience Requirements:**
- UX-1: Search box prominently displayed on all pages
- UX-2: Search results highlight matching keywords
- UX-3: "No results" page suggests alternative searches or popular products
- UX-4: Applied filters are clearly visible and removable
- UX-5: Search preserves state when navigating back from product page

#### Acceptance Criteria

**AC-1: Basic Search**
```
Given a user on any page
When they enter "laptop" in search box
And press Enter or click search icon
Then they see results page with laptop products
And results are sorted by relevance
And each result shows image, name, price, rating, availability
```

**AC-2: Search with Filters**
```
Given a user viewing search results for "laptop"
When they select "Dell" brand filter
And select price range "$500-$1000"
Then results update to show only Dell laptops in that price range
And filter selections are visually indicated
And result count updates
```

**AC-3: Autocomplete**
```
Given a user typing in search box
When they type "lap"
Then autocomplete dropdown appears
And shows suggestions like "laptop", "laptop bag", "laptop stand"
And suggestions update as they continue typing
```

**AC-4: Typo Handling**
```
Given a user searching for "laptp" (typo)
When search executes
Then results for "laptop" are shown
And message displays "Showing results for 'laptop'"
And option to search for "laptp" exactly is provided
```

**AC-5: No Results**
```
Given a user searching for "xyzabc123" (no matches)
When search executes
Then "No results found" message displays
And suggestions for popular products or categories are shown
And search tips are provided ("Try different keywords", etc.)
```

### Output Document

```markdown
# Product Search Requirements

## 1. Executive Summary
Implement full-text product search with filtering, sorting, autocomplete, and typo tolerance to improve product discovery and conversion.

## 2. Business Goals
- Increase product discovery rate by 30%
- Reduce "no results" searches by 50%
- Improve search-to-purchase conversion by 20%

## 3. Functional Requirements
[FR-1 through FR-10]

## 4. Non-Functional Requirements
[NFR-1 through NFR-6]

## 5. User Experience Requirements
[UX-1 through UX-5]

## 6. Search Scope
**In Scope:**
- Product name, description, brand, category
- Active products only
- English language

**Out of Scope:**
- Reviews/comments search
- Image-based search
- Voice search
- Multi-language support

## 7. Acceptance Criteria
[AC-1 through AC-5]

## 8. Success Metrics
- Search usage: % of sessions using search
- Click-through rate: % of searches resulting in product click
- Zero-results rate: % of searches with no results
- Search-to-purchase: % of searches leading to purchase
- Average response time

## 9. Technical Constraints
- Must integrate with existing product catalog API
- Must work on mobile and desktop
- Must support 100,000+ products

## 10. Dependencies
- Search engine selection (Elasticsearch, Algolia, etc.)
- Product catalog API
- Analytics integration
```

---

## Example 3: API Rate Limiting

### Input
Security team: "We need to prevent API abuse."

### Analysis Process

#### Clarification Questions
1. What constitutes "abuse"? (request volume, patterns, endpoints?)
2. What should happen when limit is exceeded? (reject, queue, throttle?)
3. Should limits differ by user type? (free vs. paid, internal vs. external?)
4. Should limits be per endpoint or global?
5. What time window? (per second, minute, hour, day?)
6. Should there be burst allowances?
7. How should users know they're rate limited?
8. Should there be a way to request limit increases?

#### Extracted Requirements

**Functional Requirements:**
- FR-1: Implement rate limiting on all public API endpoints
- FR-2: Return HTTP 429 (Too Many Requests) when limit exceeded
- FR-3: Include rate limit headers in all API responses
- FR-4: Provide different rate limits for different user tiers
- FR-5: Allow administrators to configure rate limits per endpoint
- FR-6: Log rate limit violations

**Rate Limit Tiers:**
- Free tier: 100 requests/hour, 1,000 requests/day
- Basic tier: 1,000 requests/hour, 10,000 requests/day
- Pro tier: 10,000 requests/hour, 100,000 requests/day
- Enterprise: Custom limits

**Non-Functional Requirements:**
- NFR-1: Rate limiting check adds < 5ms latency
- NFR-2: Rate limit counters are accurate within 1%
- NFR-3: Rate limiting system is highly available (99.99%)
- NFR-4: Support distributed rate limiting across multiple servers

**Response Headers:**
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640000000
Retry-After: 3600
```

#### Acceptance Criteria

**AC-1: Rate Limit Enforcement**
```
Given a free tier user with 100 requests/hour limit
When they make their 101st request within an hour
Then response status is 429
And response body explains rate limit exceeded
And Retry-After header indicates when they can retry
```

**AC-2: Rate Limit Headers**
```
Given any API request
When response is returned
Then X-RateLimit-Limit header shows user's limit
And X-RateLimit-Remaining shows remaining requests
And X-RateLimit-Reset shows when limit resets (Unix timestamp)
```

**AC-3: Different Limits by Tier**
```
Given a Pro tier user
When they make requests
Then their limit is 10,000 requests/hour
And limit headers reflect Pro tier limits
```

### Output Document

```markdown
# API Rate Limiting Requirements

## 1. Purpose
Prevent API abuse, ensure fair usage, and protect system resources.

## 2. Functional Requirements
[FR-1 through FR-6]

## 3. Rate Limit Tiers
[Tier definitions]

## 4. Non-Functional Requirements
[NFR-1 through NFR-4]

## 5. Error Response Format
```json
{
  "error": "rate_limit_exceeded",
  "message": "You have exceeded your rate limit of 100 requests per hour.",
  "retry_after": 3600,
  "limit": 100,
  "remaining": 0,
  "reset": 1640000000
}
```

## 6. Acceptance Criteria
[AC-1 through AC-3]

## 7. Implementation Notes
- Use sliding window algorithm for accurate counting
- Store counters in Redis for distributed access
- Implement graceful degradation if rate limit service is unavailable

## 8. Monitoring
- Track rate limit hit rate by endpoint and tier
- Alert if rate limit service latency > 10ms
- Dashboard showing top rate-limited users
```

---

## Key Takeaways from Examples

1. **Always ask clarifying questions** - Initial requests are rarely complete
2. **Be specific with NFRs** - "Fast" and "secure" need quantifiable definitions
3. **Define acceptance criteria** - Make requirements testable
4. **Document out-of-scope** - Explicitly state what's NOT included
5. **Consider all requirement types** - Functional, non-functional, security, UX, etc.
6. **Think about edge cases** - What happens when things go wrong?
7. **Define success metrics** - How will you know if it's working?