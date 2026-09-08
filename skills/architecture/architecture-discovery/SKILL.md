# Architecture Discovery

## Purpose

Reverse-engineer and document the architecture of an existing system when documentation is missing, outdated, or incomplete.

## When to Use

- Joining a team with undocumented legacy systems
- Planning a migration or modernization effort
- Conducting an architecture review of an unfamiliar system
- Onboarding to a new codebase
- Before making significant architectural changes
- Preparing for system integration
- Creating architecture documentation from scratch
- Understanding dependencies before refactoring

## When NOT to Use

- Architecture documentation already exists and is current
- Building a new system from scratch
- Making minor code changes that don't require architectural understanding
- System is simple enough to understand from code inspection alone
- Time constraints don't allow for thorough discovery

## Inputs

- Source code repository
- Running application (if available)
- Deployment configurations
- Infrastructure definitions (IaC, Docker, K8s manifests)
- Database schemas
- API documentation (if any)
- Logs and monitoring data
- Team interviews and tribal knowledge
- Existing (even if outdated) documentation

## Expected Outputs

- **Architecture Overview Document** containing:
  - System context and purpose
  - High-level architecture diagram
  - Component inventory
  - Technology stack
  - Integration points
  - Data flows
- **Component Catalog** with details on each component
- **Dependency Map** showing relationships between components
- **Technology Inventory** listing all frameworks, libraries, and tools
- **Architecture Diagrams** (C4 model or similar)
- **Data Architecture** documentation
- **Deployment Architecture** documentation
- **Identified Risks and Technical Debt**

## Workflow

### 1. Define Scope and Objectives (30-60 minutes)
- Clarify why you're doing discovery
- Define what level of detail is needed
- Identify time constraints
- Determine what questions need answers

### 2. Gather Existing Information (1-2 hours)
- Collect any existing documentation
- Review README files
- Check wikis and knowledge bases
- Interview team members
- Review recent architecture decisions

### 3. Analyze Code Structure (2-4 hours)
- Examine repository structure
- Identify major modules/packages
- Map folder organization to components
- Identify entry points (main, server startup, etc.)
- Review build and dependency files

### 4. Identify Components and Boundaries (2-4 hours)
- List all major components/services
- Identify component responsibilities
- Map component interactions
- Identify shared libraries
- Document component ownership

### 5. Map Technology Stack (1-2 hours)
- List programming languages
- Identify frameworks and libraries
- Document databases and data stores
- Identify messaging systems
- List external services and APIs
- Document infrastructure components

### 6. Trace Data Flows (2-4 hours)
- Identify data sources
- Map data transformations
- Document data storage
- Trace data movement between components
- Identify data formats and protocols

### 7. Map Integration Points (2-3 hours)
- Identify external system dependencies
- Document API endpoints
- Map authentication mechanisms
- Identify message queues and events
- Document third-party integrations

### 8. Analyze Deployment Architecture (1-3 hours)
- Review deployment configurations
- Identify environments (dev, staging, prod)
- Document infrastructure components
- Map networking and security
- Identify scaling mechanisms

### 9. Create Architecture Diagrams (2-4 hours)
- System context diagram
- Container diagram (services, databases, etc.)
- Component diagram (internal structure)
- Deployment diagram
- Data flow diagrams

### 10. Document Findings (2-4 hours)
- Write architecture overview
- Document each component
- Create technology inventory
- Note architectural patterns used
- Identify technical debt and risks
- Highlight areas of uncertainty

## Decision Framework

### Level of Detail Decision
- **High-level**: System context, major components, key integrations (1-2 days)
- **Medium**: Above + component internals, data flows (3-5 days)
- **Detailed**: Above + code-level patterns, all dependencies (1-2 weeks)

### Discovery Approach
- **Top-down**: Start with system context, drill into details
- **Bottom-up**: Start with code, build up to architecture
- **Hybrid**: Combine both approaches (recommended)

### Information Source Priority
1. Running system (most accurate)
2. Recent code (current state)
3. Configuration files (deployment reality)
4. Team knowledge (context and rationale)
5. Existing docs (may be outdated)

## Quality Checklist

- [ ] All major components are identified and documented
- [ ] Component responsibilities are clear
- [ ] Integration points are mapped
- [ ] Technology stack is fully inventoried
- [ ] Data flows are documented
- [ ] Architecture diagrams are created
- [ ] Deployment architecture is understood
- [ ] Security mechanisms are identified
- [ ] Scaling approach is documented
- [ ] Technical debt is noted
- [ ] Areas of uncertainty are highlighted
- [ ] Documentation is validated with team
- [ ] Diagrams use consistent notation (e.g., C4, UML)
- [ ] Key architectural patterns are identified
- [ ] External dependencies are cataloged

## Common Mistakes

1. **Relying solely on existing documentation**
   - Documentation is often outdated
   - Always verify against actual code and running system

2. **Not talking to the team**
   - Tribal knowledge is invaluable
   - Team can explain "why" behind decisions

3. **Getting lost in implementation details**
   - Focus on architecture, not every line of code
   - Stay at the appropriate level of abstraction

4. **Ignoring deployment and infrastructure**
   - Architecture isn't just code structure
   - Deployment patterns are architectural decisions

5. **Not documenting assumptions and uncertainties**
   - Be explicit about what you don't know
   - Mark assumptions for later validation

6. **Creating diagrams without validation**
   - Review diagrams with team members
   - Validate understanding before finalizing

7. **Trying to document everything at once**
   - Start with high-level, add detail iteratively
   - Focus on what's needed for your objectives

8. **Not using standard notation**
   - Use C4, UML, or other standard diagram types
   - Inconsistent notation confuses readers

## Examples

### Example 1: Microservices E-commerce Platform

**Discovery Findings:**

**System Context:**
- E-commerce platform with 12 microservices
- Supports web and mobile clients
- Integrates with payment gateway, shipping providers, email service

**Components:**
1. **API Gateway** (Node.js, Express)
   - Routes requests to services
   - Handles authentication
   - Rate limiting

2. **User Service** (Java, Spring Boot)
   - User registration and authentication
   - User profile management
   - PostgreSQL database

3. **Product Catalog Service** (Python, FastAPI)
   - Product information
   - Search functionality (Elasticsearch)
   - MongoDB database

4. **Order Service** (Java, Spring Boot)
   - Order processing
   - Order history
   - PostgreSQL database

5. **Payment Service** (Node.js)
   - Payment processing
   - Integration with Stripe
   - Transaction logging

6. **Inventory Service** (Go)
   - Stock management
   - Reservation system
   - Redis cache

**Integration Patterns:**
- Synchronous: REST APIs for client-facing operations
- Asynchronous: RabbitMQ for inter-service events
- Event types: OrderCreated, PaymentProcessed, InventoryUpdated

**Data Architecture:**
- Polyglot persistence (PostgreSQL, MongoDB, Redis)
- Each service owns its data
- No direct database access between services
- Event-driven data synchronization

**Deployment:**
- Kubernetes cluster (AWS EKS)
- Each service in separate pod
- Horizontal pod autoscaling
- Nginx ingress controller

**Identified Issues:**
- No distributed tracing
- Inconsistent logging formats
- Some services share database (anti-pattern)
- Missing circuit breakers
- No API versioning strategy

### Example 2: Legacy Monolithic Application

**Discovery Findings:**

**System Overview:**
- 15-year-old Java monolith
- Layered architecture (presentation, business, data)
- Single Oracle database
- Deployed on traditional app servers (Tomcat)

**Architecture Layers:**

1. **Presentation Layer**
   - JSP pages (legacy)
   - REST API controllers (newer features)
   - jQuery frontend

2. **Business Logic Layer**
   - Service classes (business logic)
   - Transaction management
   - Business rule engine

3. **Data Access Layer**
   - Hibernate ORM
   - Custom SQL queries
   - Stored procedures

**Key Modules:**
- Customer Management
- Order Processing
- Inventory Management
- Reporting
- Billing

**Technology Stack:**
- Java 8 (outdated)
- Spring Framework 4.x
- Hibernate 4.x
- Oracle 11g
- Tomcat 8
- jQuery 1.x

**Integration Points:**
- SOAP web services (legacy integrations)
- REST APIs (newer integrations)
- FTP file transfers (batch processes)
- Direct database access from reporting tools

**Deployment:**
- Manual deployment process
- Single production server (no HA)
- Database backups via cron jobs
- No containerization

**Technical Debt:**
- Outdated dependencies with security vulnerabilities
- No automated testing
- Tightly coupled modules
- God classes with 5000+ lines
- No separation of concerns in some areas
- Hard-coded configuration
- No monitoring or observability

**Modernization Opportunities:**
- Extract microservices for key modules
- Upgrade to Java 17 and Spring Boot
- Implement API gateway
- Add automated testing
- Containerize application
- Implement CI/CD

### Example 3: Serverless Application

**Discovery Findings:**

**System Overview:**
- Serverless application on AWS
- Event-driven architecture
- Multiple Lambda functions

**Components:**

1. **API Functions** (Node.js Lambda)
   - User authentication
   - CRUD operations
   - API Gateway triggers

2. **Processing Functions** (Python Lambda)
   - Data transformation
   - S3 event triggers
   - SQS queue processing

3. **Scheduled Functions** (Python Lambda)
   - Daily reports
   - Data cleanup
   - CloudWatch Events triggers

**Data Storage:**
- DynamoDB (primary database)
- S3 (file storage)
- ElastiCache Redis (caching)
- RDS PostgreSQL (analytics)

**Event Flow:**
```
API Gateway → Lambda → DynamoDB
                ↓
              SNS Topic
                ↓
          Lambda (async processing)
                ↓
              S3 Bucket
                ↓
          Lambda (S3 trigger)
                ↓
              SQS Queue
```

**Infrastructure:**
- CloudFormation for IaC
- Separate stacks per environment
- VPC for RDS and ElastiCache
- Lambda functions in public subnet (API)
- Lambda functions in private subnet (processing)

**Security:**
- IAM roles per function (least privilege)
- API Gateway with Cognito auth
- Secrets in AWS Secrets Manager
- Encryption at rest (KMS)

**Identified Issues:**
- Cold start latency on some functions
- No X-Ray tracing enabled
- Inconsistent error handling
- Some functions exceed 15-minute timeout
- No cost monitoring/alerting

## Related Skills

### Prerequisites
- None (this is often a starting skill)

### Commonly Followed By
- `architecture-review` - Review discovered architecture
- `technical-debt-analysis` - Analyze identified technical debt
- `migration-planning` - Plan modernization
- `architecture-decision` - Make improvement decisions
- `system-design` - Design improvements or new features

### Works With
- `codebase-analysis` - Deeper code-level analysis
- `dependency-analysis` - Map dependencies
- `security-review` - Assess security posture

### Alternative To
- `architecture-documentation` - When architecture is known, just needs documentation

## Skill Composition

Typical workflow:

```
Unfamiliar System
       ↓
architecture-discovery (this skill)
       ↓
architecture-review
       ↓
technical-debt-analysis
       ↓
migration-planning
       ↓
architecture-decision
```

## Evaluation Criteria

### Quality Indicators
- All major components identified
- Integration points are clear
- Diagrams are understandable
- Team validates findings
- Sufficient detail for intended purpose
- Uncertainties are documented

### Red Flags
- Major components missing
- Integration points unclear
- Diagrams are confusing or inconsistent
- Team disagrees with findings
- Too much or too little detail
- Assumptions not documented

### Success Metrics
- Can answer key architectural questions
- Team agrees documentation is accurate
- Sufficient for next steps (review, migration, etc.)
- New team members can understand system from documentation
- Diagrams are used in team discussions