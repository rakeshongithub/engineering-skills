# System Design

## Purpose

Convert requirements into a comprehensive system design that defines components, APIs, data flows, and technical architecture.

## When to Use

- Starting a new feature or system
- Requirements have been analyzed and clarified
- Need to design technical solution before implementation
- Planning major system changes or additions
- Before architecture review
- When multiple implementation approaches are possible
- Communicating design to engineering team

## When NOT to Use

- Requirements are unclear (use requirements-analysis first)
- Making minor code changes
- Bug fixes with known solutions
- System design already exists and is current
- Prototyping or experimentation phase

## Inputs

- Analyzed requirements (functional and non-functional)
- Business constraints (budget, timeline, compliance)
- Existing system architecture
- Technology stack and standards
- Team capabilities and preferences
- Scalability and performance requirements
- Security and compliance requirements

## Expected Outputs

- **System Design Document** containing:
  - System overview and context
  - Component architecture
  - API specifications
  - Data model and flows
  - Technology choices
  - Deployment architecture
  - Security design
  - Scalability approach
- **Architecture Diagrams** (C4 model or similar)
- **API Contracts** (OpenAPI/Swagger specs)
- **Data Models** (ERD, schema definitions)
- **Sequence Diagrams** for key flows
- **Technology Stack** documentation
- **Trade-off Analysis** for key decisions

## Workflow

### 1. Understand Requirements (1-2 hours)
- Review functional requirements
- Review non-functional requirements (NFRs)
- Identify constraints
- Clarify ambiguities
- Prioritize requirements

### 2. Define System Context (1 hour)
- Identify users and actors
- Identify external systems
- Define system boundaries
- Create context diagram

### 3. Design High-Level Architecture (2-4 hours)
- Identify major components
- Define component responsibilities
- Determine architectural style (monolith, microservices, serverless, etc.)
- Design component interactions
- Create container diagram

### 4. Design Data Architecture (2-3 hours)
- Design data model
- Choose data stores
- Define data flows
- Plan data consistency strategy
- Design caching strategy
- Address data privacy and compliance

### 5. Design APIs (2-4 hours)
- Define API endpoints
- Specify request/response formats
- Design authentication and authorization
- Plan API versioning
- Define error handling
- Create API documentation

### 6. Design Key Workflows (2-3 hours)
- Identify critical user journeys
- Create sequence diagrams
- Design error handling
- Plan retry and fallback strategies

### 7. Address Non-Functional Requirements (2-4 hours)
- **Performance:** Design for target latency and throughput
- **Scalability:** Plan horizontal/vertical scaling
- **Reliability:** Design for target availability
- **Security:** Design authentication, authorization, encryption
- **Observability:** Plan logging, monitoring, tracing

### 8. Make Technology Choices (1-2 hours)
- Select programming languages
- Choose frameworks and libraries
- Select databases and caches
- Choose messaging systems
- Document rationale for each choice

### 9. Design Deployment Architecture (1-2 hours)
- Define deployment model
- Plan infrastructure
- Design CI/CD pipeline
- Plan environments (dev, staging, prod)

### 10. Document and Review (2-3 hours)
- Write system design document
- Create all diagrams
- Document trade-offs and decisions
- Review with team
- Iterate based on feedback

## Decision Framework

### Architectural Style Selection
- **Monolith:** Simple requirements, small team, rapid iteration
- **Microservices:** Complex domain, multiple teams, independent scaling
- **Serverless:** Event-driven, variable load, minimal ops
- **Hybrid:** Mix based on component needs

### Data Store Selection
- **Relational (SQL):** Structured data, ACID transactions, complex queries
- **Document (NoSQL):** Flexible schema, hierarchical data
- **Key-Value:** Simple lookups, caching, sessions
- **Graph:** Relationship-heavy data
- **Time-Series:** Metrics, logs, events

### Communication Pattern Selection
- **Synchronous (REST/gRPC):** Request-response, immediate feedback
- **Asynchronous (Message Queue):** Decoupling, reliability, buffering
- **Event-Driven (Pub/Sub):** Fan-out, loose coupling
- **Hybrid:** Mix based on use case

### Scalability Strategy
- **Vertical:** Increase resources (CPU, RAM)
- **Horizontal:** Add more instances
- **Caching:** Reduce database load
- **CDN:** Distribute static content
- **Read Replicas:** Scale reads
- **Sharding:** Distribute data

## Quality Checklist

- [ ] All functional requirements are addressed
- [ ] All NFRs have design solutions
- [ ] System context is clearly defined
- [ ] Component responsibilities are clear and single-purpose
- [ ] APIs are well-defined with contracts
- [ ] Data model supports all use cases
- [ ] Data flows are documented
- [ ] Security is designed in (not bolted on)
- [ ] Scalability approach is defined
- [ ] Failure scenarios are considered
- [ ] Monitoring and observability are planned
- [ ] Technology choices are justified
- [ ] Deployment architecture is defined
- [ ] Diagrams are clear and use standard notation
- [ ] Trade-offs are documented
- [ ] Team has reviewed and approved design

## Common Mistakes

1. **Designing before understanding requirements**
   - Always start with clear, analyzed requirements
   - Don't assume you know what's needed

2. **Over-engineering**
   - Design for current needs, not hypothetical future
   - Add complexity only when justified

3. **Under-engineering**
   - Don't ignore NFRs (performance, security, scalability)
   - Plan for production from the start

4. **Ignoring existing architecture**
   - Understand current system before designing changes
   - Ensure consistency with existing patterns

5. **Not documenting trade-offs**
   - Every design decision involves trade-offs
   - Document why you chose one approach over another

6. **Skipping API design**
   - APIs are contracts - design them carefully
   - Poor API design is hard to fix later

7. **Forgetting about operations**
   - Design for deployment, monitoring, debugging
   - Consider operational complexity

8. **Not validating with team**
   - Get feedback early and often
   - Team buy-in is critical for success

## Examples

### Example 1: E-commerce Checkout System

**Requirements:**
- Users can add items to cart and checkout
- Support credit card and PayPal payments
- Send order confirmation emails
- Update inventory in real-time
- Handle 1000 orders/hour
- 99.9% availability

**System Design:**

**Architecture Style:** Microservices (separate scaling for checkout vs. inventory)

**Components:**
1. **Cart Service** (Node.js)
   - Manage shopping cart
   - Redis for cart storage
   - REST API

2. **Checkout Service** (Java/Spring Boot)
   - Process checkout
   - Orchestrate payment and order creation
   - PostgreSQL for orders

3. **Payment Service** (Node.js)
   - Integrate with Stripe and PayPal
   - Handle payment processing
   - Store payment records

4. **Inventory Service** (Go)
   - Manage stock levels
   - Reserve inventory
   - PostgreSQL for inventory

5. **Notification Service** (Python)
   - Send emails
   - Integrate with SendGrid

**Data Flow:**
```
1. User adds items to cart → Cart Service → Redis
2. User clicks checkout → Checkout Service
3. Checkout Service → Inventory Service (reserve items)
4. Checkout Service → Payment Service (process payment)
5. Payment Service → Stripe/PayPal API
6. On success: Checkout Service → Create Order (PostgreSQL)
7. Checkout Service → Publish OrderCreated event
8. Notification Service → Send confirmation email
9. Inventory Service → Update stock levels
```

**API Design:**

```yaml
# Cart Service
POST /api/cart/items
DELETE /api/cart/items/{itemId}
GET /api/cart

# Checkout Service
POST /api/checkout
  Request:
    cartId: string
    paymentMethod: "credit_card" | "paypal"
    paymentDetails: object
    shippingAddress: object
  Response:
    orderId: string
    status: "success" | "failed"
    confirmationNumber: string
```

**Technology Stack:**
- **Languages:** Node.js, Java, Go, Python
- **Frameworks:** Express, Spring Boot, Gin, Flask
- **Databases:** PostgreSQL (orders, inventory), Redis (cart, cache)
- **Messaging:** RabbitMQ (async events)
- **Payments:** Stripe, PayPal SDKs
- **Email:** SendGrid
- **Infrastructure:** Kubernetes, AWS

**Scalability:**
- Horizontal scaling for all services
- Redis for cart (fast, ephemeral)
- Database read replicas
- CDN for static assets
- Rate limiting on APIs

**Security:**
- HTTPS for all communication
- JWT for authentication
- PCI DSS compliance for payment data
- Secrets in AWS Secrets Manager
- Input validation on all endpoints

### Example 2: Real-Time Analytics Dashboard

**Requirements:**
- Display real-time metrics from IoT devices
- Support 10,000 devices sending data every 10 seconds
- Dashboard updates in near real-time (< 5 seconds)
- Historical data queries
- Alerting on threshold breaches

**System Design:**

**Architecture Style:** Event-driven, serverless

**Components:**
1. **Ingestion API** (AWS API Gateway + Lambda)
   - Receive device data
   - Validate and enrich
   - Publish to Kinesis

2. **Stream Processor** (AWS Lambda + Kinesis)
   - Process incoming data
   - Calculate aggregations
   - Detect anomalies

3. **Time-Series Database** (AWS Timestream)
   - Store device metrics
   - Optimized for time-series queries

4. **Real-Time Service** (WebSocket API + Lambda)
   - Push updates to connected clients
   - Manage WebSocket connections

5. **Query Service** (Lambda + API Gateway)
   - Historical data queries
   - Aggregations and analytics

6. **Alert Service** (Lambda + SNS)
   - Evaluate alert rules
   - Send notifications

**Data Flow:**
```
1. IoT Device → POST /api/metrics → API Gateway → Lambda
2. Lambda → Publish to Kinesis Stream
3. Stream Processor Lambda (triggered by Kinesis)
   - Calculate aggregations
   - Write to Timestream
   - Publish to SNS (for real-time updates)
4. WebSocket Lambda (subscribed to SNS)
   - Push to connected dashboard clients
5. Alert Lambda (triggered by Kinesis)
   - Evaluate rules
   - Send alerts via SNS
```

**Technology Stack:**
- **Ingestion:** API Gateway, Lambda (Node.js)
- **Streaming:** Kinesis Data Streams
- **Processing:** Lambda (Python)
- **Storage:** Timestream (time-series), DynamoDB (metadata)
- **Real-time:** WebSocket API, Lambda
- **Alerting:** SNS, SES
- **Frontend:** React, WebSocket client

**Scalability:**
- API Gateway auto-scales
- Lambda auto-scales
- Kinesis shards scale based on throughput
- Timestream auto-scales

**Performance:**
- Kinesis provides < 1 second latency
- WebSocket for real-time push (no polling)
- Timestream optimized for time-series queries
- DynamoDB for fast metadata lookups

## Related Skills

### Prerequisites
- `requirements-analysis` - Clear requirements before design

### Commonly Followed By
- `architecture-review` - Review the design
- `api-design-review` - Detailed API review
- `data-architecture-review` - Detailed data review
- `security-architecture-review` - Security review
- `technical-specification` - Detailed implementation specs

### Works With
- `architecture-decision` - Document key decisions
- `tradeoff-analysis` - Analyze alternatives
- `scalability-analysis` - Deep dive on scaling
- `technical-design-document` - Formal documentation

### Alternative To
- None (this is a core skill)

## Skill Composition

Typical workflow:

```
requirements-analysis
       ↓
system-design (this skill)
       ↓
architecture-review
       ↓
api-design-review
       ↓
data-architecture-review
       ↓
security-architecture-review
       ↓
architecture-decision
       ↓
technical-specification
       ↓
Implementation
```

## Evaluation Criteria

### Quality Indicators
- All requirements are addressed in design
- Components have clear, single responsibilities
- APIs are well-defined and consistent
- Data model supports all use cases
- NFRs have concrete solutions
- Trade-offs are documented
- Team understands and approves design

### Red Flags
- Requirements not fully understood
- Components with unclear responsibilities
- Missing API specifications
- Data model gaps
- NFRs ignored or hand-waved
- No consideration of failure scenarios
- Technology choices not justified
- Team doesn't understand design

### Success Metrics
- Engineering team can implement from design
- Design passes architecture review
- Stakeholders approve design
- Design addresses all requirements
- NFRs are achievable with design
- Design is consistent with existing architecture