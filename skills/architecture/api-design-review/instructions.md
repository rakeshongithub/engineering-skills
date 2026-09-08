# API Design Review - Step-by-Step Instructions

## Overview

This skill helps you systematically review API design for usability, security, performance, and consistency before implementation.

## Step-by-Step Workflow

### Step 1: Understand API Context (30-45 minutes)

**Actions:**
1. Review API purpose and scope:
   - What problem does this API solve?
   - Who are the consumers (internal, external, partners)?
   - What are the key use cases?
   - What is the expected scale?
2. Identify API style:
   - REST (resource-oriented)
   - GraphQL (query-oriented)
   - gRPC (RPC-oriented)
   - WebSocket (real-time)
   - Webhooks (event-driven)
3. Understand constraints:
   - Performance requirements (latency, throughput)
   - Security requirements (authentication, authorization)
   - Compliance requirements (GDPR, HIPAA, etc.)
   - Backward compatibility needs
4. Review existing APIs for consistency:
   - Organization's API standards
   - Related APIs
   - Industry best practices

**Outputs:**
- API context document
- List of applicable standards and constraints

### Step 2: Review API Specification (1-2 hours)

**Actions:**
1. Review API specification:
   - OpenAPI/Swagger spec (REST)
   - GraphQL schema (GraphQL)
   - Proto files (gRPC)
2. Check for completeness:
   - All endpoints documented?
   - All request/response formats defined?
   - All error responses documented?
   - Examples provided?
3. Validate specification syntax:
   - Valid OpenAPI/GraphQL/Proto syntax
   - No syntax errors or warnings
   - Proper references and reusable components

**Outputs:**
- List of specification issues
- Completeness assessment

### Step 3: Review Consistency (1-2 hours)

**Actions:**
1. **Naming conventions:**
   - Resource names (plural vs. singular)
   - Field names (camelCase, snake_case, kebab-case)
   - Consistent across all endpoints
   - Intuitive and self-documenting

2. **URL structure (REST):**
   - Consistent patterns (`/resources/{id}/sub-resources`)
   - No verbs in URLs (`/users` not `/getUsers`)
   - Proper nesting (max 2-3 levels)
   - Query parameters for filtering/sorting

3. **Response format:**
   - Consistent structure across endpoints
   - Consistent field naming
   - Consistent date/time formats (ISO 8601)
   - Consistent pagination format

4. **Error handling:**
   - Consistent error response structure
   - Meaningful error codes
   - Helpful error messages
   - Include request ID for debugging

**Outputs:**
- List of consistency issues
- Recommendations for standardization

### Step 4: Review Usability (1-2 hours)

**Actions:**
1. **Intuitiveness:**
   - Are resource names clear?
   - Are relationships obvious?
   - Are field names self-explanatory?
   - Is the API easy to understand?

2. **Discoverability:**
   - Can developers explore the API easily?
   - Are related resources linked (HATEOAS for REST)?
   - Is introspection available (GraphQL)?
   - Are examples provided?

3. **Flexibility:**
   - Do endpoints support common use cases?
   - Can clients filter and sort results?
   - Can clients select specific fields?
   - Are batch operations available?

4. **Simplicity:**
   - Is the API minimal (only what's needed)?
   - Are there unnecessary endpoints?
   - Is the request/response format simple?
   - Are there simpler alternatives?

**Common Issues to Check:**
- Over-fetching (returning too much data)
- Under-fetching (requiring multiple requests)
- Complex request formats
- Missing common operations
- Inconsistent patterns

**Outputs:**
- Usability assessment
- Recommendations for improvement

### Step 5: Review Performance (1-2 hours)

**Actions:**
1. **Response size:**
   - Is pagination implemented for large collections?
   - Can clients request specific fields?
   - Are payloads minimal?
   - Is compression supported?

2. **Caching:**
   - Are proper cache headers used (Cache-Control, ETag)?
   - Are conditional requests supported (If-None-Match)?
   - Are immutable resources marked as cacheable?
   - Is there a cache invalidation strategy?

3. **Efficiency:**
   - Are batch endpoints available?
   - Are N+1 queries avoided (GraphQL DataLoader)?
   - Are long-running operations async?
   - Are webhooks used instead of polling?

4. **Rate limiting:**
   - Is rate limiting implemented?
   - Are rate limit headers included?
   - Are limits appropriate for use cases?

**Performance Issues to Check:**
- No pagination (unbounded lists)
- No field selection (always returns full objects)
- No caching (every request hits database)
- Synchronous long-running operations
- No batch operations (requires many requests)

**Outputs:**
- Performance assessment
- Recommendations for optimization

### Step 6: Review Security (1-2 hours)

**Actions:**
1. **Authentication:**
   - What authentication method? (OAuth 2.0, JWT, API keys)
   - Are tokens short-lived?
   - Is there a refresh mechanism?
   - Is the authentication flow secure?

2. **Authorization:**
   - Is authorization enforced per resource?
   - Is the principle of least privilege followed?
   - Are permissions granular enough?
   - Are sensitive fields hidden from unauthorized users?

3. **Input validation:**
   - Are all inputs validated?
   - Are data types enforced?
   - Are ranges checked (min/max)?
   - Are formats validated (email, URL, date)?
   - Is there protection against injection attacks?

4. **Data protection:**
   - Is HTTPS enforced?
   - Are sensitive fields encrypted?
   - Is PII handled correctly?
   - Are secrets never exposed in responses?

5. **Rate limiting and abuse prevention:**
   - Is rate limiting per-user?
   - Are there different limits for different endpoints?
   - Is there protection against DDoS?

**Security Issues to Check:**
- Weak authentication (API keys in URLs)
- No authorization checks
- Missing input validation
- Exposing sensitive data
- No rate limiting
- HTTP instead of HTTPS

**Outputs:**
- Security assessment
- List of security issues
- Recommendations for hardening

### Step 7: Review API Style-Specific Design (1-2 hours)

**For REST APIs:**
1. **HTTP methods:**
   - GET for retrieval (idempotent, cacheable)
   - POST for creation (not idempotent)
   - PUT for full replacement (idempotent)
   - PATCH for partial update (idempotent)
   - DELETE for removal (idempotent)

2. **Status codes:**
   - 2xx for success (200, 201, 204)
   - 4xx for client errors (400, 401, 403, 404)
   - 5xx for server errors (500, 503)
   - Correct code for each scenario

3. **Resource design:**
   - Nouns, not verbs
   - Plural resource names
   - Proper nesting for relationships
   - Consistent URL structure

**For GraphQL APIs:**
1. **Schema design:**
   - Clear, single-responsibility types
   - Explicit nullability
   - Connection pattern for lists
   - Separate input types for mutations
   - Enums for fixed values

2. **Query design:**
   - Depth limiting
   - Complexity analysis
   - Field-level permissions
   - Cursor-based pagination

3. **Mutation design:**
   - Single responsibility per mutation
   - Input objects for related fields
   - Return updated object and metadata
   - Idempotent where possible

**For gRPC APIs:**
1. **Service design:**
   - Clear service boundaries
   - Logical grouping of methods
   - Proper use of streaming

2. **Message design:**
   - Well-defined message types
   - Proper use of oneof
   - Versioning strategy

**Outputs:**
- Style-specific assessment
- Recommendations for compliance with best practices

### Step 8: Review Documentation (30-60 minutes)

**Actions:**
1. **Specification completeness:**
   - All endpoints documented?
   - Request/response examples provided?
   - Error responses documented?
   - Authentication documented?

2. **Developer experience:**
   - Is there a getting started guide?
   - Are common use cases documented?
   - Are there tutorials?
   - Are SDKs available?
   - Is there a changelog?

3. **Documentation quality:**
   - Is it accurate?
   - Is it up-to-date?
   - Is it easy to understand?
   - Are there code examples?

**Outputs:**
- Documentation assessment
- Recommendations for improvement

### Step 9: Review Versioning Strategy (30-45 minutes)

**Actions:**
1. **Versioning approach:**
   - URL versioning (`/v1/users`)
   - Header versioning (`Accept: application/vnd.api+json; version=1`)
   - Is it consistent?

2. **Version management:**
   - How are breaking changes handled?
   - What is the deprecation policy?
   - How long are old versions supported?
   - Is there a migration guide?

3. **Backward compatibility:**
   - Are changes backward compatible?
   - Are new fields optional?
   - Are old fields deprecated gracefully?

**Outputs:**
- Versioning assessment
- Recommendations for version management

### Step 10: Document Findings and Recommendations (1-2 hours)

**Actions:**
1. Categorize issues by severity:
   - **Critical**: Security vulnerabilities, breaking changes
   - **High**: Poor performance, inconsistent design
   - **Medium**: Suboptimal patterns, missing documentation
   - **Low**: Minor inconsistencies, nice-to-haves

2. For each issue, document:
   - **Problem**: What is the issue?
   - **Impact**: Why is it a problem?
   - **Recommendation**: What should be done?
   - **Example**: Show before/after
   - **Priority**: Critical/High/Medium/Low

3. Create action items:
   - Prioritized list of changes
   - Effort estimates
   - Dependencies

4. Update API specification:
   - Incorporate feedback
   - Fix identified issues
   - Add missing documentation

5. Prepare presentation:
   - Executive summary
   - Key findings
   - Recommendations
   - Next steps

**Outputs:**
- Complete API design review report
- Prioritized action items
- Updated API specification
- Presentation deck

## Tips for Success

- **Use examples**: Show concrete before/after examples for each recommendation
- **Be specific**: "Use snake_case for field names" not "improve naming"
- **Prioritize**: Focus on critical and high-priority issues first
- **Consider context**: Recommendations should fit the use case and constraints
- **Test usability**: Try using the API from a client's perspective
- **Check consistency**: Compare with existing APIs in the organization
- **Validate security**: Don't skip security review, even for internal APIs
- **Think long-term**: Consider how the API will evolve
- **Involve stakeholders**: Get input from API consumers and implementers
- **Provide rationale**: Explain why each recommendation matters

## Common Pitfalls to Avoid

- Reviewing only the happy path, ignoring error cases
- Focusing only on REST principles, ignoring usability
- Not considering performance implications
- Skipping security review for "internal" APIs
- Recommending changes without examples
- Not prioritizing issues
- Being too prescriptive without understanding constraints
- Not validating recommendations with actual use cases
- Ignoring existing API standards in the organization
- Reviewing after implementation (too late to change)

## Validation Checklist

Before finalizing your review:

- [ ] All endpoints have been reviewed
- [ ] Consistency across the API is checked
- [ ] Usability from client perspective is considered
- [ ] Performance implications are assessed
- [ ] Security is thoroughly reviewed
- [ ] Documentation is evaluated
- [ ] Versioning strategy is reviewed
- [ ] Issues are categorized by severity
- [ ] Recommendations are specific with examples
- [ ] Action items are prioritized
- [ ] Review is validated with stakeholders
