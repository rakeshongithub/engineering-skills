# API Design Review

## Purpose

Review API design for usability, security, performance, and consistency before implementation to ensure high-quality, maintainable APIs.

## When to Use

- Before implementing a new API or major API version
- When designing public APIs that external developers will use
- During architecture reviews for API-heavy systems
- When refactoring or versioning existing APIs
- Before committing to an API contract that will be difficult to change
- When establishing API design standards for a team or organization
- When integrating with third-party systems and designing the integration layer

## When NOT to Use

- For internal function signatures or method calls (use code-review instead)
- For database schema design (use data-architecture-review instead)
- For UI/UX design (use different skill)
- After API is already implemented and in production (too late for design review)
- For trivial CRUD APIs with no special requirements

## Inputs

- **api-specification**: OpenAPI/Swagger spec, GraphQL schema, or API documentation
- **use-cases**: User stories, integration scenarios, client requirements
- **constraints**: Performance requirements, security requirements, compliance needs
- **api-standards**: Organization's API guidelines, industry standards (REST, GraphQL, gRPC)
- **existing-apis**: Related APIs for consistency checking

## Expected Outputs

- **design-issues**: List of problems, inconsistencies, and violations
- **recommendations**: Specific improvements with examples
- **best-practices**: Applicable design patterns and standards
- **action-items**: Prioritized list of changes to make before implementation
- **updated-specification**: Revised API spec incorporating feedback

## Workflow

### 1. Understand API Context

**Purpose and scope:**
- What problem does this API solve?
- Who are the consumers (internal, external, partners)?
- What are the key use cases?
- What is the expected scale (requests/sec, data volume)?

**API style:**
- REST (resource-oriented)
- GraphQL (query-oriented)
- gRPC (RPC-oriented)
- WebSocket (real-time)
- Webhooks (event-driven)

**Constraints:**
- Performance requirements (latency, throughput)
- Security requirements (authentication, authorization)
- Compliance requirements (GDPR, HIPAA, PCI-DSS)
- Backward compatibility needs

### 2. Review API Design Principles

#### Consistency
- **Naming conventions**: Consistent resource names, field names, casing
- **URL structure**: Consistent patterns for endpoints
- **Response format**: Consistent structure across endpoints
- **Error handling**: Consistent error response format
- **Versioning**: Consistent versioning strategy

#### Usability
- **Intuitive**: Easy to understand and use
- **Self-documenting**: Clear naming, obvious relationships
- **Discoverable**: Easy to explore and learn
- **Minimal**: Only expose what's necessary
- **Flexible**: Support common use cases without workarounds

#### Performance
- **Efficient**: Minimize round trips and payload size
- **Cacheable**: Support caching where appropriate
- **Paginated**: Large result sets are paginated
- **Filtered**: Support filtering and field selection
- **Batched**: Support batch operations where appropriate

#### Security
- **Authenticated**: Require authentication for protected resources
- **Authorized**: Enforce fine-grained permissions
- **Encrypted**: Use HTTPS for all endpoints
- **Rate limited**: Protect against abuse
- **Input validated**: Validate and sanitize all inputs

#### Evolvability
- **Versioned**: Clear versioning strategy
- **Backward compatible**: Don't break existing clients
- **Extensible**: Easy to add new features
- **Deprecation**: Clear deprecation policy

### 3. Review REST API Design (if applicable)

#### Resource Design
- **Nouns, not verbs**: `/users` not `/getUsers`
- **Plural names**: `/users` not `/user`
- **Hierarchical**: `/users/{id}/orders` for relationships
- **Consistent casing**: kebab-case or snake_case for URLs

#### HTTP Methods
- **GET**: Retrieve resources (idempotent, cacheable)
- **POST**: Create resources (not idempotent)
- **PUT**: Replace entire resource (idempotent)
- **PATCH**: Partial update (idempotent)
- **DELETE**: Remove resource (idempotent)

#### Status Codes
- **2xx**: Success (200 OK, 201 Created, 204 No Content)
- **3xx**: Redirection (301 Moved Permanently, 304 Not Modified)
- **4xx**: Client errors (400 Bad Request, 401 Unauthorized, 404 Not Found)
- **5xx**: Server errors (500 Internal Server Error, 503 Service Unavailable)

#### Common Issues
- Using verbs in URLs (`/createUser` instead of `POST /users`)
- Wrong HTTP methods (`GET` for mutations)
- Wrong status codes (`200` for errors)
- Inconsistent naming (mixing camelCase and snake_case)
- Missing pagination for large collections
- No filtering or sorting options
- Exposing internal IDs or implementation details

### 4. Review GraphQL API Design (if applicable)

#### Schema Design
- **Clear types**: Well-defined, single-responsibility types
- **Nullable fields**: Explicit about what can be null
- **Connections**: Use connection pattern for lists (edges, nodes, pageInfo)
- **Input types**: Separate input types for mutations
- **Enums**: Use enums for fixed sets of values

#### Query Design
- **Depth limiting**: Prevent deeply nested queries
- **Complexity analysis**: Estimate query cost
- **Field-level permissions**: Control access to sensitive fields
- **Pagination**: Cursor-based pagination for large lists
- **Filtering**: Support common filter operations

#### Mutation Design
- **Single responsibility**: One mutation per action
- **Input objects**: Group related inputs
- **Return types**: Return updated object and metadata
- **Idempotency**: Design mutations to be idempotent where possible

#### Common Issues
- N+1 query problems (missing DataLoader)
- No query complexity limits (DoS risk)
- Overly generic types (everything is String)
- Missing pagination
- No error handling in schema
- Exposing internal implementation details

### 5. Review API Security

#### Authentication
- **Method**: OAuth 2.0, JWT, API keys, mutual TLS
- **Token expiration**: Short-lived access tokens
- **Refresh tokens**: Secure refresh mechanism
- **Scope**: Principle of least privilege

#### Authorization
- **Resource-level**: Check permissions per resource
- **Field-level**: Hide sensitive fields from unauthorized users
- **Rate limiting**: Per-user, per-endpoint limits
- **CORS**: Properly configured CORS headers

#### Input Validation
- **Type validation**: Enforce correct data types
- **Range validation**: Check min/max values
- **Format validation**: Validate emails, URLs, dates
- **Injection prevention**: Prevent SQL injection, XSS
- **Size limits**: Limit request body size

#### Data Protection
- **HTTPS only**: No plain HTTP
- **Sensitive data**: Don't log or expose in errors
- **PII handling**: Comply with privacy regulations
- **Encryption**: Encrypt sensitive fields at rest

### 6. Review API Performance

#### Response Size
- **Pagination**: Required for large collections
- **Field selection**: Allow clients to request specific fields (GraphQL, JSON:API)
- **Compression**: Enable gzip/brotli compression
- **Minimal payloads**: Don't include unnecessary data

#### Caching
- **Cache headers**: Proper Cache-Control, ETag headers
- **Conditional requests**: Support If-None-Match, If-Modified-Since
- **Immutable resources**: Mark immutable resources as cacheable
- **Cache invalidation**: Strategy for cache invalidation

#### Efficiency
- **Batch endpoints**: Support batching related requests
- **Eager loading**: Avoid N+1 queries
- **Async operations**: Long-running operations return immediately with job ID
- **Webhooks**: Push updates instead of polling

### 7. Review API Documentation

#### Specification
- **OpenAPI/Swagger**: Machine-readable spec for REST
- **GraphQL schema**: Introspectable schema with descriptions
- **Examples**: Request and response examples for each endpoint
- **Error codes**: Document all possible error responses

#### Developer Experience
- **Getting started**: Quick start guide
- **Authentication**: How to authenticate
- **Common use cases**: Tutorials for common scenarios
- **SDKs**: Client libraries for popular languages
- **Changelog**: Document changes between versions

### 8. Review API Versioning

#### Versioning Strategy
- **URL versioning**: `/v1/users`, `/v2/users`
- **Header versioning**: `Accept: application/vnd.api+json; version=1`
- **Query parameter**: `/users?version=1` (not recommended)

#### Version Management
- **Backward compatibility**: Don't break existing clients
- **Deprecation policy**: How long old versions are supported
- **Migration guide**: Help clients upgrade
- **Sunset headers**: Indicate when version will be retired

## Decision Framework

### API Style Selection

**Use REST when:**
- Building CRUD-heavy APIs
- Clients need caching (HTTP caching works well)
- Stateless operations
- Wide client compatibility needed
- Simple, resource-oriented domain

**Use GraphQL when:**
- Clients need flexible data fetching
- Mobile apps need to minimize requests
- Complex, graph-like data relationships
- Multiple client types with different needs
- Real-time updates needed (subscriptions)

**Use gRPC when:**
- High performance, low latency required
- Microservice-to-microservice communication
- Streaming data (bidirectional)
- Strong typing and code generation desired
- Internal APIs (not public-facing)

### Issue Severity

**Critical:**
- Security vulnerabilities
- Breaking changes to existing API
- Data loss or corruption risks
- Compliance violations

**High:**
- Poor performance (will not scale)
- Inconsistent with API standards
- Difficult to use correctly
- Missing critical functionality

**Medium:**
- Suboptimal design patterns
- Inconsistent naming
- Missing documentation
- Poor error messages

**Low:**
- Minor inconsistencies
- Missing nice-to-have features
- Verbose payloads
- Suboptimal but functional design

## Quality Checklist

- [ ] API follows organization's design standards
- [ ] Naming is consistent and intuitive
- [ ] HTTP methods and status codes are used correctly (REST)
- [ ] Schema is well-designed with clear types (GraphQL)
- [ ] Authentication and authorization are properly designed
- [ ] Input validation is comprehensive
- [ ] Error responses are consistent and helpful
- [ ] Pagination is implemented for large collections
- [ ] Caching strategy is defined
- [ ] Performance considerations are addressed
- [ ] API is versioned appropriately
- [ ] Documentation is complete and accurate
- [ ] Backward compatibility is maintained
- [ ] Security best practices are followed
- [ ] Common use cases are well-supported

## Common Mistakes

- **Inconsistent naming**: Mixing camelCase, snake_case, kebab-case
- **Wrong HTTP methods**: Using GET for mutations, POST for retrieval
- **Poor error handling**: Returning 200 with error in body
- **No versioning**: No plan for evolving the API
- **Exposing internals**: Leaking database IDs, internal field names
- **No pagination**: Returning unbounded lists
- **Over-fetching**: Returning more data than needed
- **Under-fetching**: Requiring multiple requests for related data
- **No rate limiting**: Vulnerable to abuse
- **Weak authentication**: Using insecure auth methods
- **No documentation**: Expecting developers to guess
- **Breaking changes**: Changing existing endpoints without versioning

## Examples

See [examples.md](examples.md) for detailed examples of API design reviews for various scenarios.

## Related Skills

- **Requires**: 
  - requirements-analysis (to understand API requirements)
- **Commonly followed by**: 
  - technical-specification (to document final API design)
  - implementation (to build the API)
  - api-testing (to validate implementation)
- **Alternative to**: None (this is the primary API design review skill)
- **Works with**: 
  - architecture-review (for overall architecture assessment)
  - security-architecture-review (for security-focused review)
  - data-architecture-review (for data model review)
  - code-review (for implementation review)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
api-design-review
        ↓
technical-specification
        ↓
implementation
        ↓
api-testing
```

Alternative workflow for existing APIs:

```
architecture-review
        ↓
api-design-review
        ↓
api-refactoring
        ↓
api-versioning
```

## Evaluation Criteria

### Completeness
- Are all endpoints reviewed?
- Are all design principles considered?
- Are security, performance, and usability addressed?
- Is documentation reviewed?

### Accuracy
- Are issues correctly identified?
- Are recommendations technically sound?
- Are best practices accurately applied?
- Are examples correct?

### Actionability
- Are recommendations specific and clear?
- Are priorities well-defined?
- Can developers act on the feedback?
- Are examples provided for improvements?

### Impact
- Will recommendations improve API quality?
- Are critical issues identified?
- Will API be easier to use and maintain?
- Are long-term implications considered?
