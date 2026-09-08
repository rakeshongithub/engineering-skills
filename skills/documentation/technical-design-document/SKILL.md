# Technical Design Document

## Purpose

Create comprehensive technical design documentation for systems, features, or architectural changes.

## When to Use

- Before implementing a new system or major feature
- When proposing significant architectural changes
- When designing complex integrations with external systems
- Before starting a large refactoring or migration project
- When multiple teams need to coordinate on a shared design
- When documenting decisions for future reference
- When onboarding new team members to a complex system
- When preparing for technical reviews or architecture approvals

## When NOT to Use

- For simple features that don't require design documentation
- For bug fixes or minor changes
- When the design is still highly uncertain (use spike or prototype instead)
- For API documentation (use API specification instead)
- For user-facing documentation (use user guide instead)
- For code-level documentation (use inline comments and README instead)

## Inputs

- **requirements**: Functional and non-functional requirements
- **architecture**: Current system architecture (if applicable)
- **design-decisions**: Key architectural decisions and tradeoffs
- **stakeholders**: List of stakeholders and their concerns
- **constraints**: Technical, business, and timeline constraints

## Expected Outputs

- **design-document**: Comprehensive technical design document
- **diagrams**: Architecture diagrams, sequence diagrams, data models
- **decision-rationale**: Explanation of key design decisions and alternatives considered
- **implementation-guide**: High-level implementation plan and milestones
- **risks**: Identified risks and mitigation strategies

## Workflow

### 1. Understand Requirements and Context

**Gather requirements:**
- What problem are we solving?
- Who are the users?
- What are the functional requirements?
- What are the non-functional requirements (performance, scalability, security)?
- What are the success criteria?

**Understand context:**
- What is the current system architecture?
- What are the existing constraints?
- What are the business goals?
- What is the timeline?
- What is the budget?

**Identify stakeholders:**
- Who needs to review and approve the design?
- Who will implement the design?
- Who will operate and maintain the system?
- Who are the end users?

### 2. Define Scope and Goals

**In Scope:**
- What will be designed and implemented?
- What components will be created or modified?
- What integrations are required?

**Out of Scope:**
- What will NOT be included in this design?
- What is deferred to future phases?
- What is explicitly excluded?

**Goals:**
- What are the primary objectives?
- What are the success metrics?
- What are the key results (OKRs)?

**Non-Goals:**
- What are we explicitly NOT trying to achieve?
- What tradeoffs are we accepting?

### 3. Design High-Level Architecture

**System Overview:**
- What are the major components?
- How do they interact?
- What are the key data flows?
- What are the external dependencies?

**Architecture Diagram:**
- Create a high-level architecture diagram
- Show components, services, databases, external systems
- Indicate data flows and communication patterns
- Use standard notation (C4, UML, or custom)

**Component Responsibilities:**
- What does each component do?
- What are the interfaces between components?
- What are the dependencies?

### 4. Design Detailed Components

For each major component:

**Component Design:**
- What is the purpose of this component?
- What are the inputs and outputs?
- What is the internal structure?
- What are the key algorithms or logic?

**API Design:**
- What are the API endpoints or interfaces?
- What are the request and response formats?
- What are the error handling strategies?
- What is the versioning strategy?

**Data Model:**
- What data does this component store or process?
- What is the schema?
- What are the relationships?
- What are the indexes and constraints?

**Sequence Diagrams:**
- How do components interact for key scenarios?
- What is the flow of requests and responses?
- What are the failure modes?

### 5. Address Non-Functional Requirements

**Performance:**
- What are the latency requirements?
- What are the throughput requirements?
- How will performance be achieved?
- What are the bottlenecks and optimizations?

**Scalability:**
- How will the system scale?
- What are the scaling limits?
- What is the scaling strategy (horizontal, vertical)?
- What are the capacity estimates?

**Reliability:**
- What is the availability target (SLA)?
- How will failures be handled?
- What are the redundancy and failover strategies?
- What is the disaster recovery plan?

**Security:**
- What are the security requirements?
- How is authentication and authorization handled?
- How is data protected (encryption, access control)?
- What are the security risks and mitigations?

**Observability:**
- What metrics will be collected?
- What logs will be generated?
- What traces will be captured?
- What dashboards and alerts are needed?

### 6. Document Design Decisions

For each major design decision:

**Decision:**
- What decision was made?
- What problem does it solve?

**Alternatives Considered:**
- What other options were considered?
- What are the pros and cons of each?

**Rationale:**
- Why was this option chosen?
- What are the tradeoffs?
- What assumptions were made?

**Consequences:**
- What are the implications of this decision?
- What are the risks?
- What are the future constraints?

**Example Format (ADR - Architecture Decision Record):**
```
## Decision: Use PostgreSQL for Primary Database

**Context**: Need a database for transactional data with ACID guarantees.

**Alternatives**:
1. PostgreSQL - Pros: ACID, JSON support, mature. Cons: Scaling writes.
2. MongoDB - Pros: Flexible schema, horizontal scaling. Cons: Weaker consistency.
3. DynamoDB - Pros: Fully managed, auto-scaling. Cons: Limited query flexibility.

**Decision**: Use PostgreSQL.

**Rationale**: 
- ACID guarantees are critical for financial transactions
- JSON support allows schema flexibility where needed
- Team has PostgreSQL expertise
- Write scaling is not a concern at current scale (< 1000 writes/sec)

**Consequences**:
- Will need to implement read replicas for read scaling
- Will need to plan for write scaling in future (sharding or migration)
- Operational overhead of managing PostgreSQL
```

### 7. Identify Risks and Mitigations

**Technical Risks:**
- What could go wrong technically?
- What are the unknowns?
- What are the dependencies on unproven technologies?

**Operational Risks:**
- What are the operational challenges?
- What are the monitoring and alerting gaps?
- What are the disaster recovery concerns?

**Timeline Risks:**
- What could cause delays?
- What are the dependencies on other teams?
- What are the unknowns that could extend the timeline?

**Mitigation Strategies:**
- How will each risk be mitigated?
- What are the contingency plans?
- What are the early warning signals?

### 8. Create Implementation Plan

**Phases:**
- Break the implementation into phases or milestones
- Define what will be delivered in each phase
- Identify dependencies between phases

**Timeline:**
- Estimate effort for each phase
- Create a timeline with milestones
- Identify critical path

**Team and Resources:**
- Who will implement each component?
- What skills are required?
- What resources are needed (infrastructure, tools, licenses)?

**Testing Strategy:**
- How will the design be validated?
- What types of testing are needed (unit, integration, performance, security)?
- What are the acceptance criteria?

**Rollout Strategy:**
- How will the system be deployed?
- What is the rollout plan (phased, canary, blue-green)?
- What is the rollback plan?

### 9. Document and Review

**Document Structure:**
1. **Executive Summary** (1 page)
   - Problem statement
   - Proposed solution
   - Key benefits
   - Timeline and resources

2. **Requirements** (1-2 pages)
   - Functional requirements
   - Non-functional requirements
   - Constraints

3. **Design** (5-10 pages)
   - High-level architecture
   - Component design
   - Data model
   - API design
   - Sequence diagrams

4. **Design Decisions** (2-5 pages)
   - Key decisions with rationale
   - Alternatives considered
   - Tradeoffs

5. **Non-Functional Requirements** (2-3 pages)
   - Performance
   - Scalability
   - Reliability
   - Security
   - Observability

6. **Risks and Mitigations** (1-2 pages)
   - Technical risks
   - Operational risks
   - Timeline risks
   - Mitigation strategies

7. **Implementation Plan** (2-3 pages)
   - Phases and milestones
   - Timeline
   - Team and resources
   - Testing strategy
   - Rollout strategy

8. **Appendix**
   - Detailed diagrams
   - API specifications
   - Database schemas
   - References

**Review Process:**
1. Self-review for completeness and clarity
2. Peer review by technical team
3. Review by stakeholders (product, operations, security)
4. Architecture review (if applicable)
5. Approval by decision-makers

**Iterate Based on Feedback:**
- Address questions and concerns
- Clarify ambiguities
- Update design based on feedback
- Re-review if significant changes

### 10. Maintain and Update

**During Implementation:**
- Update design as implementation reveals new information
- Document deviations from the original design
- Keep diagrams and documentation in sync with code

**After Implementation:**
- Update design to reflect as-built architecture
- Document lessons learned
- Archive or mark as historical if design is superseded

## Decision Framework

### Level of Detail

**High-Level Design:**
- For early-stage exploration
- Focus on architecture and key decisions
- Light on implementation details
- 5-10 pages

**Detailed Design:**
- For implementation-ready design
- Includes component design, APIs, data models
- Sufficient for team to implement without ambiguity
- 15-30 pages

**Comprehensive Design:**
- For complex systems or regulatory requirements
- Includes detailed specifications, edge cases, error handling
- Serves as reference documentation
- 30+ pages

### Audience

**Technical Audience (Engineers):**
- Focus on architecture, APIs, data models
- Include technical details and code examples
- Use technical terminology

**Non-Technical Audience (Product, Executives):**
- Focus on problem, solution, benefits
- Use diagrams and high-level explanations
- Avoid technical jargon

**Mixed Audience:**
- Start with executive summary
- Provide high-level overview
- Include technical details in appendix

### Documentation Format

**Markdown/Text:**
- Easy to version control
- Easy to review and comment
- Good for collaborative editing

**Google Docs/Word:**
- Good for stakeholder review
- Easy to comment and suggest edits
- Good for presentations

**Confluence/Wiki:**
- Good for living documentation
- Easy to link to related pages
- Good for discoverability

**Specialized Tools (Notion, Coda):**
- Good for structured documentation
- Good for embedding diagrams and code
- Good for templates

## Quality Checklist

- [ ] Problem statement is clear and well-defined
- [ ] Requirements are complete and unambiguous
- [ ] Scope and goals are clearly defined
- [ ] High-level architecture is documented with diagrams
- [ ] Component design is detailed and clear
- [ ] Data model is documented with schemas
- [ ] API design is documented with examples
- [ ] Non-functional requirements are addressed
- [ ] Design decisions are documented with rationale
- [ ] Alternatives are considered and compared
- [ ] Risks are identified with mitigation strategies
- [ ] Implementation plan is realistic and detailed
- [ ] Document is reviewed by stakeholders
- [ ] Feedback is incorporated
- [ ] Document is approved

## Common Mistakes

- **Too vague**: Design lacks sufficient detail for implementation
- **Too detailed**: Design includes implementation details that should be in code
- **No rationale**: Decisions are stated without explaining why
- **No alternatives**: Only one option considered, no comparison
- **Ignoring non-functional requirements**: Focus only on functionality
- **No diagrams**: Text-only documentation is hard to understand
- **Outdated diagrams**: Diagrams don't match the text
- **No risks**: Assumes everything will go perfectly
- **No implementation plan**: Design without a plan to execute
- **No review**: Design created in isolation without feedback
- **Not maintained**: Design becomes outdated as implementation evolves
- **Wrong audience**: Too technical for stakeholders or too high-level for engineers

## Examples

See [examples.md](examples.md) for detailed examples of technical design documents for various scenarios.

## Related Skills

- **Requires**: 
  - system-design (to create the design)
- **Commonly followed by**: 
  - technical-specification (for detailed specs)
  - implementation (to build the system)
- **Alternative to**: None (this is the primary documentation skill)
- **Works with**: 
  - architecture-decision (for documenting decisions)
  - requirements-analysis (for gathering requirements)
  - architecture-review (for validating the design)
  - api-design (for API specifications)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
system-design
        ↓
technical-design-document
        ↓
architecture-review
        ↓
implementation
```

Complex project workflow:

```
requirements-analysis
        ↓
system-design
        ↓
architecture-decision (for key decisions)
        ↓
technical-design-document
        ↓
architecture-review
        ↓
security-architecture-review
        ↓
implementation
```

## Evaluation Criteria

### Completeness
- Are all requirements addressed?
- Are all components designed?
- Are non-functional requirements covered?
- Is an implementation plan provided?

### Clarity
- Is the problem clearly stated?
- Are diagrams clear and accurate?
- Is the design easy to understand?
- Are technical terms defined?

### Depth
- Is the design detailed enough for implementation?
- Are edge cases and error handling addressed?
- Are performance and scalability considerations included?
- Are security and reliability addressed?

### Rationale
- Are design decisions explained?
- Are alternatives considered?
- Are tradeoffs clearly stated?
- Are assumptions documented?

### Actionability
- Can the team implement based on this design?
- Is the implementation plan realistic?
- Are risks and mitigations identified?
- Are success criteria defined?