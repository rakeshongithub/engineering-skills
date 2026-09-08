# System Design

## Purpose

Convert requirements into a comprehensive system design with components, APIs, data flows, and technical decisions.

## When to Use

- When designing a new system or major feature
- After completing requirements analysis
- Before beginning implementation
- When planning a significant refactoring
- During technical interviews or architecture discussions

## When NOT to Use

- For trivial features that don't need formal design
- When requirements are unclear (use requirements-analysis first)
- For minor bug fixes or small changes
- When the design already exists and is current

## Inputs

- **requirements**: Functional and non-functional requirements
- **constraints**: Technical, business, timeline, budget constraints
- **non-functional-requirements**: Performance, scalability, security, reliability requirements
- **existing-architecture**: Current system architecture (if applicable)

## Expected Outputs

- **system-architecture**: High-level architecture diagram and description
- **component-design**: Detailed component specifications
- **api-specifications**: API contracts and interfaces
- **data-model**: Database schemas and data structures
- **technology-choices**: Selected technologies with rationale

## Workflow

### 1. Understand Requirements and Constraints

- Review functional requirements
- Understand non-functional requirements (performance, scalability, security)
- Identify constraints (technical, budget, timeline)
- Clarify success criteria

### 2. Define System Scope and Boundaries

- What is in scope?
- What is out of scope?
- What are the external dependencies?
- What are the integration points?

### 3. Identify Major Components

- Break down the system into logical components
- Define component responsibilities
- Ensure single responsibility principle
- Identify component types (services, databases, caches, queues)

### 4. Design Data Model

- Identify entities and relationships
- Design database schemas
- Choose data stores (SQL, NoSQL, cache, object storage)
- Define data ownership (which component owns which data)
- Plan for data consistency and integrity

### 5. Design APIs and Interfaces

- Define API contracts between components
- Choose API styles (REST, GraphQL, gRPC, events)
- Design request/response formats
- Define error handling
- Plan for versioning

### 6. Design Data Flows

- Map how data flows through the system
- Identify synchronous vs. asynchronous flows
- Design event flows (if applicable)
- Plan for data transformations

### 7. Address Non-Functional Requirements

**Performance:**
- Identify performance bottlenecks
- Design for required response times
- Plan caching strategy
- Optimize data access patterns

**Scalability:**
- Design for horizontal scaling
- Identify stateless vs. stateful components
- Plan for load balancing
- Design for expected load and growth

**Security:**
- Design authentication mechanism
- Design authorization model
- Plan for data encryption (in transit and at rest)
- Identify security boundaries

**Reliability:**
- Design for fault tolerance
- Plan for failure scenarios
- Design retry and fallback mechanisms
- Plan for data backup and recovery

### 8. Make Technology Choices

- Choose programming languages
- Select frameworks and libraries
- Choose databases and data stores
- Select cloud services (if applicable)
- Choose communication protocols

### 9. Create Architecture Diagrams

- System context diagram
- Component diagram
- Sequence diagrams (for key flows)
- Data flow diagrams
- Deployment diagram

### 10. Document Design Decisions

- Why this architecture?
- Why these technologies?
- What tradeoffs were made?
- What alternatives were considered?

## Decision Framework

### Architecture Patterns

**Monolith:**
- Use for: Small teams, simple domains, rapid development
- Pros: Simple deployment, easy transactions, straightforward
- Cons: Scaling challenges, tight coupling, long deployment cycles

**Microservices:**
- Use for: Large teams, complex domains, independent scaling
- Pros: Independent deployment, technology diversity, fault isolation
- Cons: Complexity, distributed transactions, operational overhead

**Serverless:**
- Use for: Event-driven, variable load, minimal ops
- Pros: Auto-scaling, pay-per-use, no server management
- Cons: Cold starts, vendor lock-in, debugging challenges

### Data Storage

**Relational (PostgreSQL, MySQL):**
- Use for: Structured data, ACID transactions, complex queries

**NoSQL (MongoDB, DynamoDB):**
- Use for: Flexible schema, high write throughput, horizontal scaling

**Cache (Redis, Memcached):**
- Use for: Fast reads, session storage, temporary data

**Object Storage (S3, GCS):**
- Use for: Files, images, backups, large objects

### Communication Patterns

**Synchronous (REST, gRPC):**
- Use for: Request-response, immediate feedback, simple flows

**Asynchronous (Message queues, Events):**
- Use for: Decoupling, eventual consistency, high throughput

## Quality Checklist

- [ ] All requirements are addressed
- [ ] Non-functional requirements are met
- [ ] Components have clear responsibilities
- [ ] APIs are well-defined
- [ ] Data model is normalized and efficient
- [ ] Security is designed in (not bolted on)
- [ ] System can scale to meet expected load
- [ ] Failure scenarios are considered
- [ ] Technology choices are justified
- [ ] Design is documented with diagrams
- [ ] Tradeoffs are explicitly stated

## Common Mistakes

- **Over-engineering**: Designing for scale you don't need
- **Under-engineering**: Ignoring non-functional requirements
- **Premature optimization**: Optimizing before understanding bottlenecks
- **Ignoring constraints**: Designing without considering budget, timeline, team skills
- **Technology-first**: Choosing technologies before understanding requirements
- **Skipping documentation**: Not documenting design decisions and rationale

## Examples

See [examples.md](examples.md) for detailed examples.

## Related Skills

- **Requires**: requirements-analysis
- **Commonly followed by**:
  - architecture-review
  - api-design-review
  - data-architecture-review
  - security-architecture-review
- **Works with**:
  - architecture-decision
  - tradeoff-analysis

## Skill Composition

```
requirements-analysis
        ↓
system-design
        ↓
architecture-review
        ↓
api-design-review
        ↓
technical-specification
```

## Evaluation Criteria

### Completeness
- Are all requirements addressed?
- Are all components defined?
- Are all APIs specified?

### Quality
- Is the design scalable?
- Is the design secure?
- Is the design maintainable?

### Clarity
- Is the design well-documented?
- Are diagrams clear and accurate?
- Are decisions justified?
