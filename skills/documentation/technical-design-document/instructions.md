# Technical Design Document - Step-by-Step Instructions

## Overview

This skill helps you create comprehensive technical design documentation for systems, features, or architectural changes.

## Step-by-Step Workflow

### Step 1: Understand Requirements and Context (1-2 hours)

**Actions:**
1. Gather functional requirements
   - What problem are we solving?
   - Who are the users?
   - What features are needed?
   - What are the success criteria?

2. Gather non-functional requirements
   - Performance (latency, throughput)
   - Scalability (expected growth)
   - Reliability (availability, SLA)
   - Security (authentication, authorization, data protection)
   - Observability (monitoring, logging, tracing)

3. Understand context
   - Current system architecture
   - Existing constraints (technical, business, timeline)
   - Business goals
   - Budget

4. Identify stakeholders
   - Who reviews and approves?
   - Who implements?
   - Who operates and maintains?
   - Who are the end users?

**Outputs:**
- Requirements document (functional and non-functional)
- Context summary (current state, constraints, goals)
- Stakeholder list with roles

### Step 2: Define Scope and Goals (30-60 minutes)

**Actions:**
1. Define what is IN SCOPE
   - What will be designed and implemented?
   - What components will be created or modified?
   - What integrations are required?

2. Define what is OUT OF SCOPE
   - What will NOT be included?
   - What is deferred to future phases?
   - What is explicitly excluded?

3. Define GOALS
   - Primary objectives
   - Success metrics
   - Key results (OKRs)

4. Define NON-GOALS
   - What are we NOT trying to achieve?
   - What tradeoffs are we accepting?

**Outputs:**
- Scope statement (in scope, out of scope)
- Goals and non-goals

### Step 3: Design High-Level Architecture (2-4 hours)

**Actions:**
1. Identify major components
   - What are the main building blocks?
   - What are their responsibilities?

2. Define interactions
   - How do components communicate?
   - What are the data flows?
   - What are the protocols (REST, gRPC, message queue)?

3. Identify external dependencies
   - Third-party services
   - External APIs
   - Databases
   - Infrastructure

4. Create architecture diagram
   - Use standard notation (C4, UML, or custom)
   - Show components, services, databases, external systems
   - Indicate data flows and communication patterns
   - Label protocols and technologies

5. Document component responsibilities
   - What does each component do?
   - What are the interfaces?
   - What are the dependencies?

**Outputs:**
- High-level architecture diagram
- Component responsibility matrix
- External dependencies list

### Step 4: Design Detailed Components (3-6 hours)

**Actions:**

For each major component:

1. **Component Design:**
   - Purpose and responsibilities
   - Inputs and outputs
   - Internal structure (classes, modules, services)
   - Key algorithms or logic

2. **API Design:**
   - Endpoints or interfaces
   - Request and response formats (JSON, protobuf)
   - Authentication and authorization
   - Error handling and status codes
   - Versioning strategy
   - Rate limiting

3. **Data Model:**
   - Entities and relationships
   - Schema (tables, collections, documents)
   - Indexes and constraints
   - Data types and validation rules
   - Data retention and archival

4. **Sequence Diagrams:**
   - Key user flows
   - Request/response sequences
   - Failure scenarios
   - Error handling flows

**Outputs:**
- Component design documents (one per major component)
- API specifications (OpenAPI, gRPC proto, GraphQL schema)
- Data model diagrams (ERD, schema diagrams)
- Sequence diagrams for key flows

### Step 5: Address Non-Functional Requirements (2-3 hours)

**Actions:**

1. **Performance:**
   - Define latency requirements (p50, p95, p99)
   - Define throughput requirements (requests/sec, transactions/sec)
   - Identify bottlenecks and optimizations
   - Plan for caching, indexing, query optimization

2. **Scalability:**
   - Define scaling strategy (horizontal, vertical)
   - Estimate capacity needs
   - Plan for auto-scaling
   - Identify scaling limits

3. **Reliability:**
   - Define availability target (SLA)
   - Plan for redundancy and failover
   - Define error handling and retry strategies
   - Plan for disaster recovery

4. **Security:**
   - Define authentication mechanism (OAuth, JWT, API keys)
   - Define authorization model (RBAC, ABAC)
   - Plan for data encryption (in transit, at rest)
   - Identify security risks and mitigations
   - Plan for compliance (GDPR, HIPAA, SOC 2)

5. **Observability:**
   - Define metrics to collect (golden signals, business metrics)
   - Plan for logging (structured logs, log levels)
   - Plan for distributed tracing (if microservices)
   - Design dashboards and alerts

**Outputs:**
- Performance requirements and optimization plan
- Scalability plan with capacity estimates
- Reliability plan with SLA targets
- Security plan with threat model
- Observability plan with metrics and alerts

### Step 6: Document Design Decisions (2-3 hours)

**Actions:**

For each major design decision:

1. **State the decision**
   - What decision was made?
   - What problem does it solve?

2. **List alternatives considered**
   - What other options were evaluated?
   - What are the pros and cons of each?

3. **Explain rationale**
   - Why was this option chosen?
   - What are the tradeoffs?
   - What assumptions were made?

4. **Document consequences**
   - What are the implications?
   - What are the risks?
   - What are the future constraints?

**Format:** Use Architecture Decision Records (ADRs)

**Example:**
```
## ADR-001: Use PostgreSQL for Primary Database

**Context**: Need a database for transactional data with ACID guarantees.

**Alternatives**:
1. PostgreSQL - ACID, JSON support, mature
2. MongoDB - Flexible schema, horizontal scaling
3. DynamoDB - Fully managed, auto-scaling

**Decision**: PostgreSQL

**Rationale**: ACID critical for transactions, team expertise, sufficient for scale

**Consequences**: Need read replicas for scaling, plan for future sharding
```

**Outputs:**
- Architecture Decision Records (ADRs) for each major decision
- Alternatives comparison matrix

### Step 7: Identify Risks and Mitigations (1-2 hours)

**Actions:**

1. **Identify technical risks**
   - What could go wrong technically?
   - What are the unknowns?
   - What dependencies on unproven technologies?

2. **Identify operational risks**
   - What are the operational challenges?
   - What are the monitoring gaps?
   - What are the disaster recovery concerns?

3. **Identify timeline risks**
   - What could cause delays?
   - What are the dependencies on other teams?
   - What are the unknowns?

4. **Define mitigation strategies**
   - How will each risk be mitigated?
   - What are the contingency plans?
   - What are the early warning signals?

**Outputs:**
- Risk register with likelihood, impact, and mitigation
- Contingency plans for high-impact risks

### Step 8: Create Implementation Plan (2-3 hours)

**Actions:**

1. **Define phases**
   - Break implementation into phases or milestones
   - Define deliverables for each phase
   - Identify dependencies between phases

2. **Estimate timeline**
   - Estimate effort for each phase (person-weeks)
   - Create timeline with milestones
   - Identify critical path

3. **Assign team and resources**
   - Who implements each component?
   - What skills are required?
   - What resources are needed (infrastructure, tools, licenses)?

4. **Define testing strategy**
   - Unit testing
   - Integration testing
   - Performance testing
   - Security testing
   - Acceptance criteria

5. **Define rollout strategy**
   - Deployment approach (phased, canary, blue-green)
   - Rollback plan
   - Monitoring during rollout

**Outputs:**
- Implementation plan with phases and milestones
- Timeline with effort estimates
- Team and resource allocation
- Testing plan
- Rollout plan

### Step 9: Write and Structure Document (3-5 hours)

**Actions:**

1. **Write Executive Summary** (1 page)
   - Problem statement
   - Proposed solution (high-level)
   - Key benefits
   - Timeline and resources

2. **Write Requirements Section** (1-2 pages)
   - Functional requirements
   - Non-functional requirements
   - Constraints

3. **Write Design Section** (5-10 pages)
   - High-level architecture with diagram
   - Component design
   - Data model with diagrams
   - API design with examples
   - Sequence diagrams for key flows

4. **Write Design Decisions Section** (2-5 pages)
   - Include all ADRs
   - Alternatives comparison
   - Tradeoffs

5. **Write Non-Functional Requirements Section** (2-3 pages)
   - Performance plan
   - Scalability plan
   - Reliability plan
   - Security plan
   - Observability plan

6. **Write Risks and Mitigations Section** (1-2 pages)
   - Risk register
   - Mitigation strategies

7. **Write Implementation Plan Section** (2-3 pages)
   - Phases and milestones
   - Timeline
   - Team and resources
   - Testing strategy
   - Rollout strategy

8. **Add Appendix**
   - Detailed diagrams
   - API specifications (OpenAPI, proto files)
   - Database schemas (DDL)
   - References and links

**Outputs:**
- Complete technical design document (15-30 pages)

### Step 10: Review and Iterate (1-2 hours per review cycle)

**Actions:**

1. **Self-review**
   - Check for completeness
   - Check for clarity
   - Check for consistency
   - Verify diagrams match text

2. **Peer review (technical team)**
   - Share with engineers who will implement
   - Get feedback on feasibility
   - Get feedback on technical details
   - Address questions and concerns

3. **Stakeholder review**
   - Share with product, operations, security
   - Get feedback on requirements coverage
   - Get feedback on risks and timeline
   - Address concerns

4. **Architecture review** (if applicable)
   - Present to architecture review board
   - Get feedback on design decisions
   - Get approval or requested changes

5. **Iterate based on feedback**
   - Update design based on feedback
   - Clarify ambiguities
   - Add missing details
   - Re-review if significant changes

6. **Get approval**
   - Obtain sign-off from decision-makers
   - Document approval and date

**Outputs:**
- Reviewed and approved technical design document
- Feedback log with resolutions

### Step 11: Maintain and Update (Ongoing)

**Actions:**

1. **During implementation**
   - Update design as implementation reveals new information
   - Document deviations from original design
   - Keep diagrams in sync with code

2. **After implementation**
   - Update design to reflect as-built architecture
   - Document lessons learned
   - Mark as "implemented" with date

3. **When design is superseded**
   - Archive or mark as historical
   - Link to new design document

**Outputs:**
- Up-to-date design document reflecting current state

## Tips for Success

- **Start with why**: Clearly state the problem before jumping to solutions
- **Use diagrams**: A picture is worth a thousand words
- **Be specific**: Vague designs lead to implementation confusion
- **Document decisions**: Explain why, not just what
- **Consider alternatives**: Show you've thought through options
- **Address non-functionals**: Don't focus only on features
- **Identify risks**: Be honest about what could go wrong
- **Get feedback early**: Don't wait until the design is "perfect"
- **Keep it updated**: A stale design doc is worse than no doc
- **Know your audience**: Adjust detail level for readers

## Common Pitfalls to Avoid

- Writing a design that's too vague to implement
- Including implementation details that belong in code
- Not explaining why decisions were made
- Only considering one option without comparing alternatives
- Ignoring non-functional requirements (performance, security, etc.)
- Creating text-only documentation without diagrams
- Letting diagrams become outdated
- Not identifying risks and assuming everything will work perfectly
- Creating a design without a plan to implement it
- Designing in isolation without getting feedback
- Not maintaining the design as implementation evolves
- Writing for the wrong audience (too technical or too high-level)

## Validation Checklist

- [ ] Problem statement is clear
- [ ] Requirements are complete
- [ ] Scope and goals are defined
- [ ] Architecture diagram is clear and accurate
- [ ] Component design is detailed
- [ ] Data model is documented
- [ ] API design is documented
- [ ] Non-functional requirements are addressed
- [ ] Design decisions are documented with rationale
- [ ] Alternatives are considered
- [ ] Risks are identified with mitigations
- [ ] Implementation plan is realistic
- [ ] Document is reviewed by stakeholders
- [ ] Feedback is incorporated
- [ ] Document is approved