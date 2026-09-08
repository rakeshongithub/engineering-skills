# Architecture Discovery

## Purpose

Reverse-engineer and document the architecture of an existing system by analyzing code, infrastructure, and documentation.

## When to Use

- When joining a new project with undocumented or outdated architecture
- Before planning a major refactoring or migration
- When conducting an architecture review of an unfamiliar system
- When onboarding new team members who need to understand the system
- Before making significant architectural changes
- When preparing for a technical due diligence

## When NOT to Use

- When architecture is already well-documented and current
- For trivial or single-component systems
- When you only need to understand a small part of the system (use targeted code analysis instead)
- For greenfield projects (use architecture-design instead)

## Inputs

- **codebase**: Access to source code repositories
- **infrastructure-config**: Infrastructure as code, deployment configs, cloud resources
- **existing-documentation**: Any available docs (even if outdated)
- **access-to-running-system**: Ability to observe the system in operation (logs, metrics, traces)

## Expected Outputs

- **architecture-diagram**: Visual representation of system components and their relationships
- **component-inventory**: List of all major components with their responsibilities
- **integration-map**: How components communicate (APIs, events, databases)
- **technology-stack**: Technologies, frameworks, and libraries used
- **architecture-documentation**: Written description of the architecture

## Workflow

### 1. Gather Available Information

- Review existing documentation (even if outdated)
- Identify key stakeholders and interview them
- Access source code repositories
- Review infrastructure configuration
- Access monitoring and logging systems
- Review deployment pipelines

### 2. Identify System Boundaries

- What is in scope for this system?
- What are the external dependencies?
- What are the entry points (APIs, UIs, jobs)?
- What are the data stores?
- What are the external integrations?

### 3. Map Components

- Identify major components/services
- Determine component responsibilities
- Identify component types (web app, API, worker, database, cache, etc.)
- Map component ownership (which team owns what)
- Document component technologies

### 4. Analyze Communication Patterns

- How do components communicate?
  - Synchronous (REST, gRPC, GraphQL)
  - Asynchronous (message queues, events)
  - Database sharing (if any)
- What are the data flows?
- What are the integration points?
- What are the authentication/authorization mechanisms?

### 5. Understand Data Architecture

- Identify all data stores (databases, caches, file storage)
- Map data ownership (which component owns which data)
- Identify data flows between components
- Document data models (schemas, relationships)
- Identify data consistency mechanisms

### 6. Analyze Infrastructure

- Where is the system deployed? (cloud, on-prem, hybrid)
- What is the deployment architecture?
- How is scaling handled?
- What are the networking configurations?
- What are the security controls?

### 7. Document Technology Stack

- Programming languages
- Frameworks and libraries
- Databases and data stores
- Message queues and event systems
- Cloud services
- Third-party integrations
- Monitoring and observability tools

### 8. Create Architecture Diagrams

**System Context Diagram:**
- System and its external dependencies
- Users and external systems

**Container Diagram:**
- Major components/services
- Databases and data stores
- Communication patterns

**Component Diagram (for key services):**
- Internal structure of critical components

### 9. Document Findings

- Write architecture overview
- Document each major component
- Describe integration patterns
- Explain data flows
- Note architectural decisions (if known)
- Identify areas of uncertainty

### 10. Validate Understanding

- Review findings with team members
- Verify against running system behavior
- Test hypotheses by examining code
- Confirm with original architects (if available)

## Decision Framework

### Level of Detail

**High-level (system context):**
- Use for executive communication
- Use for initial understanding
- Focus on major components and external dependencies

**Medium-level (container/service):**
- Use for architecture reviews
- Use for planning migrations
- Focus on services, databases, communication

**Detailed (component/code):**
- Use for refactoring planning
- Use for deep technical analysis
- Focus on internal component structure

### What to Prioritize

**High priority:**
- Core business logic components
- Critical data flows
- External integrations
- Authentication/authorization
- Data persistence

**Medium priority:**
- Supporting services
- Internal tools
- Monitoring and logging

**Low priority:**
- Deprecated components
- Rarely used features
- Development-only tools

## Quality Checklist

- [ ] All major components identified
- [ ] Communication patterns documented
- [ ] Data stores and data flows mapped
- [ ] Technology stack documented
- [ ] External dependencies identified
- [ ] Architecture diagrams created
- [ ] Findings validated with team
- [ ] Areas of uncertainty noted
- [ ] Documentation is understandable to newcomers
- [ ] Diagrams are accurate and up-to-date

## Common Mistakes

- **Relying only on documentation**: Docs are often outdated; verify against code and running system
- **Ignoring infrastructure**: Architecture isn't just code; include deployment, networking, security
- **Too much detail too soon**: Start high-level, drill down where needed
- **Not validating findings**: Assumptions can be wrong; verify with team and system behavior
- **Forgetting external dependencies**: Third-party services and integrations are part of architecture
- **Skipping data architecture**: Data flows and ownership are critical to understanding the system

## Examples

See [examples.md](examples.md) for detailed examples.

## Related Skills

- **Requires**: None
- **Commonly followed by**:
  - architecture-review (to assess the discovered architecture)
  - technical-debt-analysis (to identify issues)
  - migration-planning (to plan improvements)
- **Works with**:
  - codebase-analysis (for deeper code understanding)
  - dependency-analysis (for dependency mapping)

## Skill Composition

```
architecture-discovery
        ↓
architecture-review
        ↓
technical-debt-analysis
        ↓
migration-planning
```

## Evaluation Criteria

### Completeness
- Are all major components identified?
- Are all external dependencies documented?
- Are data flows mapped?

### Accuracy
- Does the documentation match the actual system?
- Have findings been validated?

### Clarity
- Can someone unfamiliar with the system understand it?
- Are diagrams clear and well-organized?

### Usefulness
- Can this documentation support architecture decisions?
- Does it help new team members onboard?
