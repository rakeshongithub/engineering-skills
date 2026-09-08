# API Design Review - Examples

## Example 1: REST API Design Review for E-commerce Platform

### Context
- **API**: Product catalog and order management REST API
- **Consumers**: Web frontend, mobile apps, partner integrations
- **Scale**: 10,000 requests/sec peak
- **Specification**: OpenAPI 3.0

### Key Findings

#### Critical Issues

1. **Inconsistent HTTP Methods**
   - **Problem**: Using GET for mutations
   ```json
   // ❌ BEFORE: Wrong HTTP method
   GET /api/cart/add?productId=123&quantity=2
   ```
   - **Impact**: Not idempotent, can't be cached, violates REST principles
   - **Recommendation**: Use POST for mutations
   ```json
   // ✅ AFTER: Correct HTTP method
   POST /api/v1/cart/items
   Content-Type: application/json
   
   {
     "productId": "123",
     "quantity": 2
   }
   ```
   - **Priority**: Critical

2. **Security: API Keys in URL**
   - **Problem**: API key passed as query parameter
   ```
   GET /api/orders?apiKey=sk_live_abc123
   ```
   - **Impact**: Keys logged in server logs, browser history, proxy logs
   - **Recommendation**: Use Authorization header
   ```
   GET /api/v1/orders
   Authorization: Bearer sk_live_abc123
   ```
   - **Priority**: Critical

#### High Priority Issues

3. **No Pagination**
   - **Problem**: `/api/products` returns all products (10,000+)
   ```json
   // ❌ BEFORE: No pagination
   GET /api/products
   
   Response: [
     { "id": 1, ... },
     { "id": 2, ... },
     // ... 10,000 items
   ]
   ```
   - **Impact**: Slow response, high memory usage, poor UX
   - **Recommendation**: Implement cursor-based pagination
   ```json
   // ✅ AFTER: With pagination
   GET /api/v1/products?limit=20&cursor=eyJpZCI6MTAwfQ
   
   Response: {
     "data": [
       { "id": 1, ... },
       { "id": 2, ... },
       // ... 20 items
     ],
     "pagination": {
       "nextCursor": "eyJpZCI6MTIwfQ",
       "hasMore": true
     }
   }
   ```
   - **Priority**: High

4. **Inconsistent Naming**
   - **Problem**: Mixing camelCase and snake_case
   ```json
   // ❌ BEFORE: Inconsistent
   {
     "productId": 123,
     "product_name": "Widget",
     "Price": 29.99,
     "created-at": "2024-01-01"
   }
   ```
   - **Recommendation**: Use consistent camelCase
   ```json
   // ✅ AFTER: Consistent camelCase
   {
     "productId": 123,
     "productName": "Widget",
     "price": 29.99,
     "createdAt": "2024-01-01T00:00:00Z"
   }
   ```
   - **Priority**: High

5. **Wrong Status Codes**
   - **Problem**: Returning 200 for errors
   ```json
   // ❌ BEFORE: Wrong status code
   HTTP/1.1 200 OK
   {
     "status": "error",
     "message": "Product not found"
   }
   ```
   - **Recommendation**: Use proper HTTP status codes
   ```json
   // ✅ AFTER: Correct status code
   HTTP/1.1 404 Not Found
   {
     "error": {
       "code": "PRODUCT_NOT_FOUND",
       "message": "Product with ID 123 not found",
       "requestId": "req_abc123"
     }
   }
   ```
   - **Priority**: High

#### Medium Priority Issues

6. **No Filtering or Sorting**
   - **Problem**: Can't filter products by category or price
   - **Recommendation**: Add query parameters
   ```
   GET /api/v1/products?category=electronics&minPrice=100&maxPrice=500&sort=price:asc
   ```
   - **Priority**: Medium

7. **Over-fetching**
   - **Problem**: Always returns all fields, even when not needed
   - **Recommendation**: Support field selection
   ```
   GET /api/v1/products?fields=id,name,price
   ```
   - **Priority**: Medium

8. **No Rate Limiting**
   - **Problem**: No protection against abuse
   - **Recommendation**: Implement rate limiting with headers
   ```
   HTTP/1.1 200 OK
   X-RateLimit-Limit: 1000
   X-RateLimit-Remaining: 999
   X-RateLimit-Reset: 1640995200
   ```
   - **Priority**: Medium

### Recommendations Summary

**Immediate (Before Launch):**
1. Fix HTTP methods (GET for mutations)
2. Move API keys to Authorization header
3. Implement pagination
4. Fix status codes
5. Standardize naming to camelCase

**Short-term (Next Sprint):**
6. Add filtering and sorting
7. Implement field selection
8. Add rate limiting
9. Improve error messages
10. Add request IDs for debugging

**Long-term (Next Quarter):**
11. Add caching headers
12. Implement batch endpoints
13. Add webhooks for order updates
14. Create comprehensive API documentation
15. Build client SDKs

---

## Example 2: GraphQL API Design Review for Social Media Platform

### Context
- **API**: Social media feed and messaging GraphQL API
- **Consumers**: Web, iOS, Android apps
- **Scale**: 100,000 concurrent users
- **Specification**: GraphQL schema

### Key Findings

#### Critical Issues

1. **N+1 Query Problem**
   - **Problem**: No DataLoader, causes N+1 queries
   ```graphql
   # ❌ BEFORE: N+1 queries
   type Post {
     id: ID!
     content: String!
     author: User!  # Separate query for each post's author
   }
   
   # Query for 20 posts = 1 query + 20 author queries = 21 queries
   ```
   - **Impact**: Severe performance degradation
   - **Recommendation**: Implement DataLoader
   ```javascript
   // ✅ AFTER: With DataLoader
   const userLoader = new DataLoader(async (userIds) => {
     const users = await User.findAll({ where: { id: userIds } });
     return userIds.map(id => users.find(u => u.id === id));
   });
   
   // Now: 1 query for posts + 1 batched query for all authors = 2 queries
   ```
   - **Priority**: Critical

2. **No Query Complexity Limits**
   - **Problem**: Allows deeply nested queries (DoS risk)
   ```graphql
   # ❌ BEFORE: No limits
   query DeeplyNested {
     user(id: "1") {
       posts {
         comments {
           author {
             posts {
               comments {
                 author {
                   posts {
                     # ... infinitely deep
                   }
                 }
               }
             }
           }
         }
       }
     }
   }
   ```
   - **Impact**: Server overload, DoS vulnerability
   - **Recommendation**: Implement query complexity analysis
   ```javascript
   // ✅ AFTER: With complexity limits
   const depthLimit = require('graphql-depth-limit');
   const { createComplexityLimitRule } = require('graphql-validation-complexity');
   
   const server = new ApolloServer({
     schema,
     validationRules: [
       depthLimit(5),  // Max depth: 5
       createComplexityLimitRule(1000)  // Max complexity: 1000
     ]
   });
   ```
   - **Priority**: Critical

#### High Priority Issues

3. **Poor Pagination Design**
   - **Problem**: Using offset-based pagination
   ```graphql
   # ❌ BEFORE: Offset pagination
   type Query {
     posts(offset: Int, limit: Int): [Post!]!
   }
   ```
   - **Impact**: Inconsistent results when data changes, poor performance
   - **Recommendation**: Use cursor-based pagination (Relay spec)
   ```graphql
   # ✅ AFTER: Cursor-based pagination
   type Query {
     posts(
       first: Int
       after: String
       last: Int
       before: String
     ): PostConnection!
   }
   
   type PostConnection {
     edges: [PostEdge!]!
     pageInfo: PageInfo!
     totalCount: Int!
   }
   
   type PostEdge {
     node: Post!
     cursor: String!
   }
   
   type PageInfo {
     hasNextPage: Boolean!
     hasPreviousPage: Boolean!
     startCursor: String
     endCursor: String
   }
   ```
   - **Priority**: High

4. **No Error Handling in Schema**
   - **Problem**: Errors only in top-level errors array
   ```graphql
   # ❌ BEFORE: No error handling
   type Mutation {
     createPost(input: CreatePostInput!): Post!
   }
   ```
   - **Recommendation**: Use union types for errors
   ```graphql
   # ✅ AFTER: Explicit error handling
   type Mutation {
     createPost(input: CreatePostInput!): CreatePostResult!
   }
   
   union CreatePostResult = CreatePostSuccess | ValidationError | AuthorizationError
   
   type CreatePostSuccess {
     post: Post!
   }
   
   type ValidationError {
     message: String!
     field: String!
   }
   
   type AuthorizationError {
     message: String!
   }
   ```
   - **Priority**: High

5. **Overly Generic Types**
   - **Problem**: Everything is a String
   ```graphql
   # ❌ BEFORE: Generic types
   type Post {
     id: String!
     status: String!  # Should be enum
     createdAt: String!  # Should be DateTime
     tags: String!  # Should be [String!]!
   }
   ```
   - **Recommendation**: Use specific types
   ```graphql
   # ✅ AFTER: Specific types
   scalar DateTime
   
   enum PostStatus {
     DRAFT
     PUBLISHED
     ARCHIVED
   }
   
   type Post {
     id: ID!
     status: PostStatus!
     createdAt: DateTime!
     tags: [String!]!
   }
   ```
   - **Priority**: High

#### Medium Priority Issues

6. **No Field-Level Authorization**
   - **Problem**: All fields visible to all users
   - **Recommendation**: Implement field-level permissions
   ```javascript
   // ✅ AFTER: Field-level auth
   const resolvers = {
     User: {
       email: (user, args, context) => {
         if (context.user.id !== user.id && !context.user.isAdmin) {
           return null;  // Hide email from other users
         }
         return user.email;
       }
     }
   };
   ```
   - **Priority**: Medium

7. **Missing Input Validation**
   - **Problem**: No validation on mutation inputs
   - **Recommendation**: Add input validation
   ```graphql
   # ✅ AFTER: With validation
   input CreatePostInput {
     content: String! @constraint(minLength: 1, maxLength: 5000)
     tags: [String!]! @constraint(maxItems: 10)
   }
   ```
   - **Priority**: Medium

### Recommendations Summary

**Critical (Before Launch):**
1. Implement DataLoader to fix N+1 queries
2. Add query complexity and depth limits
3. Implement proper error handling

**High Priority (Next Sprint):**
4. Migrate to cursor-based pagination
5. Use specific types (enums, scalars) instead of String
6. Add field-level authorization
7. Implement input validation

**Medium Priority (Next Month):**
8. Add subscriptions for real-time updates
9. Implement query cost analysis
10. Add persisted queries for performance
11. Create comprehensive schema documentation

---

## Example 3: API Versioning Strategy Review

### Context
- **API**: Payment processing API
- **Consumers**: 500+ merchant integrations
- **Problem**: Need to make breaking changes but can't break existing clients
- **Current**: No versioning strategy

### Current State Issues

1. **No Versioning**
   - **Problem**: All clients use same unversioned API
   ```
   POST /api/payments
   ```
   - **Impact**: Can't make breaking changes without breaking all clients

2. **Breaking Changes Needed**
   - Change payment status from string to enum
   - Rename `amount` to `amountCents` for clarity
   - Split `address` into `billingAddress` and `shippingAddress`
   - Add required `idempotencyKey` field

### Recommended Versioning Strategy

#### URL Versioning (Recommended)

```
# Version 1 (existing clients)
POST /api/v1/payments

# Version 2 (new clients)
POST /api/v2/payments
```

**Advantages:**
- Clear and explicit
- Easy to route to different implementations
- Visible in logs and monitoring
- Industry standard

**Implementation:**
```javascript
// v1 endpoint (backward compatible)
app.post('/api/v1/payments', async (req, res) => {
  const payment = {
    amount: req.body.amount,  // Old field name
    status: req.body.status,  // String
    address: req.body.address  // Single address
  };
  // ... process payment
});

// v2 endpoint (new design)
app.post('/api/v2/payments', async (req, res) => {
  const payment = {
    amountCents: req.body.amountCents,  // New field name
    status: req.body.status,  // Enum
    billingAddress: req.body.billingAddress,  // Split addresses
    shippingAddress: req.body.shippingAddress,
    idempotencyKey: req.body.idempotencyKey  // Required
  };
  // ... process payment
});
```

#### Deprecation Policy

**Timeline:**
1. **Month 0**: Launch v2, announce v1 deprecation
2. **Month 1-6**: Support both v1 and v2
3. **Month 6**: Send deprecation warnings to v1 users
4. **Month 9**: Final warning, v1 sunset date announced
5. **Month 12**: Sunset v1 (stop accepting new requests)
6. **Month 12+**: v1 returns 410 Gone

**Communication:**
```
HTTP/1.1 200 OK
Deprecation: true
Sunset: Sat, 31 Dec 2024 23:59:59 GMT
Link: </api/v2/payments>; rel="successor-version"

{
  "data": { ... },
  "warnings": [
    {
      "code": "DEPRECATED_VERSION",
      "message": "API v1 is deprecated and will be sunset on Dec 31, 2024. Please migrate to v2.",
      "migrationGuide": "https://docs.example.com/api/v1-to-v2-migration"
    }
  ]
}
```

#### Migration Guide

**v1 to v2 Changes:**

| v1 Field | v2 Field | Change |
|----------|----------|--------|
| `amount` | `amountCents` | Renamed for clarity |
| `status` (string) | `status` (enum) | Type changed |
| `address` | `billingAddress`, `shippingAddress` | Split into two fields |
| - | `idempotencyKey` | New required field |

**Example Migration:**

```javascript
// ❌ v1 Request
POST /api/v1/payments
{
  "amount": 2999,
  "status": "pending",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  }
}

// ✅ v2 Request
POST /api/v2/payments
{
  "amountCents": 2999,
  "status": "PENDING",  // Enum value
  "billingAddress": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  },
  "shippingAddress": {
    "street": "123 Main St",
    "city": "New York",
    "zip": "10001"
  },
  "idempotencyKey": "pay_abc123"  // Required
}
```

### Recommendations

1. **Implement URL versioning** (`/api/v1/`, `/api/v2/`)
2. **Create v2 with breaking changes**
3. **Support v1 for 12 months**
4. **Add deprecation headers to v1 responses**
5. **Create comprehensive migration guide**
6. **Notify all clients via email**
7. **Monitor v1 usage and reach out to slow adopters**
8. **Sunset v1 after 12 months**

---

## Example 4: API Security Review

### Context
- **API**: Healthcare patient data API (HIPAA compliance required)
- **Consumers**: Mobile apps, partner systems
- **Sensitivity**: PHI (Protected Health Information)

### Security Findings

#### Critical Issues

1. **Weak Authentication**
   - **Problem**: Using API keys for authentication
   ```
   GET /api/patients/123
   X-API-Key: abc123
   ```
   - **Impact**: Keys can't be revoked per-user, no expiration
   - **Recommendation**: Use OAuth 2.0 with JWT
   ```
   GET /api/v1/patients/123
   Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
   ```
   - **Priority**: Critical

2. **No Field-Level Encryption**
   - **Problem**: PHI stored and transmitted in plaintext
   - **Impact**: HIPAA violation
   - **Recommendation**: Encrypt sensitive fields
   ```json
   {
     "id": "123",
     "name": "encrypted:AES256:abc123...",
     "ssn": "encrypted:AES256:def456...",
     "diagnosis": "encrypted:AES256:ghi789..."
   }
   ```
   - **Priority**: Critical

3. **No Audit Logging**
   - **Problem**: No record of who accessed what data
   - **Impact**: HIPAA violation, can't detect breaches
   - **Recommendation**: Log all data access
   ```javascript
   auditLog.record({
     userId: req.user.id,
     action: 'READ',
     resource: 'patient',
     resourceId: '123',
     timestamp: new Date(),
     ipAddress: req.ip,
     userAgent: req.headers['user-agent']
   });
   ```
   - **Priority**: Critical

4. **Missing Authorization Checks**
   - **Problem**: Any authenticated user can access any patient
   ```javascript
   // ❌ BEFORE: No authorization
   app.get('/api/patients/:id', authenticate, async (req, res) => {
     const patient = await Patient.findById(req.params.id);
     res.json(patient);
   });
   ```
   - **Recommendation**: Check user permissions
   ```javascript
   // ✅ AFTER: With authorization
   app.get('/api/v1/patients/:id', authenticate, async (req, res) => {
     const patient = await Patient.findById(req.params.id);
     
     // Check if user has permission to access this patient
     if (!canAccessPatient(req.user, patient)) {
       return res.status(403).json({
         error: {
           code: 'FORBIDDEN',
           message: 'You do not have permission to access this patient'
         }
       });
     }
     
     res.json(patient);
   });
   ```
   - **Priority**: Critical

#### High Priority Issues

5. **No Input Validation**
   - **Problem**: Accepting any input without validation
   - **Impact**: SQL injection, XSS, data corruption
   - **Recommendation**: Validate all inputs
   ```javascript
   const { body, validationResult } = require('express-validator');
   
   app.post('/api/v1/patients',
     authenticate,
     [
       body('name').isLength({ min: 1, max: 100 }).trim().escape(),
       body('dateOfBirth').isISO8601(),
       body('email').isEmail().normalizeEmail(),
       body('phone').matches(/^\+?[1-9]\d{1,14}$/)
     ],
     async (req, res) => {
       const errors = validationResult(req);
       if (!errors.isEmpty()) {
         return res.status(400).json({ errors: errors.array() });
       }
       // ... create patient
     }
   );
   ```
   - **Priority**: High

6. **No Rate Limiting**
   - **Problem**: No protection against brute force or data scraping
   - **Recommendation**: Implement rate limiting
   ```javascript
   const rateLimit = require('express-rate-limit');
   
   const limiter = rateLimit({
     windowMs: 15 * 60 * 1000,  // 15 minutes
     max: 100,  // Limit each IP to 100 requests per windowMs
     message: 'Too many requests, please try again later'
   });
   
   app.use('/api/', limiter);
   ```
   - **Priority**: High

### Recommendations Summary

**Critical (Must Fix Before Launch):**
1. Implement OAuth 2.0 with JWT
2. Encrypt PHI fields
3. Implement comprehensive audit logging
4. Add authorization checks to all endpoints

**High Priority (Next Sprint):**
5. Add input validation
6. Implement rate limiting
7. Add request signing for partner integrations
8. Implement IP whitelisting for sensitive operations

**Compliance:**
9. Document all security controls for HIPAA audit
10. Implement data retention policies
11. Add breach notification mechanism
12. Conduct penetration testing
