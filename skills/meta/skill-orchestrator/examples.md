# Skill Orchestrator - Examples

This document provides practical examples of using the skill-orchestrator meta-skill for various engineering scenarios.

---

## Example 1: Microservices Migration

### Problem Statement

"We need to migrate our monolithic e-commerce application to microservices. The monolith is 5 years old, written in Java Spring Boot, and handles orders, inventory, payments, and customer management. We're experiencing scalability issues during peak shopping seasons."

### Context

- **Current system**: Java Spring Boot monolith
- **Database**: Single PostgreSQL database
- **Team**: 8 developers, 2 architects
- **Timeline**: 6-month migration
- **Constraints**: Zero downtime, maintain data consistency
- **Goals**: Improve scalability, enable independent deployment, reduce coupling

### Orchestrator Analysis

**Problem Category**: Legacy modernization + Architecture transformation

**Required Capabilities**:
- Understand current architecture
- Identify service boundaries
- Analyze data dependencies
- Plan migration strategy
- Ensure reliability and security
- Document decisions

### Skill Sequence

```
1. architecture-discovery
        ↓
2. technical-debt-analysis
        ↓
3. dependency-analysis
        ↓
4. service-boundary-analysis
        ↓
5. data-architecture-review
        ↓
6. integration-design
        ↓
7. scalability-analysis
        ↓
8. reliability-analysis
        ↓
9. security-architecture-review
        ↓
10. migration-planning
        ↓
11. architecture-decision
        ↓
12. testing-strategy
        ↓
13. observability-design
        ↓
14. production-readiness
        ↓
15. architecture-documentation
```

### Rationale

1. **architecture-discovery**: Understand the current monolith structure, components, and dependencies
2. **technical-debt-analysis**: Identify existing issues that should be addressed during migration
3. **dependency-analysis**: Map all internal and external dependencies
4. **service-boundary-analysis**: Determine optimal microservice boundaries based on business capabilities
5. **data-architecture-review**: Plan data separation strategy (shared DB, separate DBs, event sourcing)
6. **integration-design**: Design communication patterns between services (REST, events, gRPC)
7. **scalability-analysis**: Ensure new architecture addresses scalability issues
8. **reliability-analysis**: Design for fault tolerance and resilience
9. **security-architecture-review**: Ensure security in distributed system (auth, authorization, secrets)
10. **migration-planning**: Create phased migration plan (strangler pattern, parallel run)
11. **architecture-decision**: Document key decisions (ADRs) for technology choices
12. **testing-strategy**: Plan testing approach (contract testing, integration testing, E2E)
13. **observability-design**: Design logging, metrics, and tracing for distributed system
14. **production-readiness**: Validate readiness for production deployment
15. **architecture-documentation**: Document the new architecture for the team

### Expected Outcomes

- Complete understanding of current architecture
- Identified service boundaries (e.g., OrderService, InventoryService, PaymentService, CustomerService)
- Data migration strategy
- API contracts between services
- Phased migration plan
- Testing strategy
- Observability setup
- Production deployment plan
- Comprehensive documentation

### Estimated Timeline

- Discovery & Analysis (skills 1-3): 2 weeks
- Design (skills 4-6): 3 weeks
- Validation (skills 7-9): 2 weeks
- Planning (skills 10-14): 2 weeks
- Documentation (skill 15): 1 week
- **Total Planning Phase**: 10 weeks

---

## Example 2: AI Agent for Customer Support

### Problem Statement

"We want to build an AI agent that can handle tier-1 customer support tickets automatically. The agent should categorize tickets, provide automated responses for common issues, and escalate complex issues to human agents."

### Context

- **Current system**: Manual ticket handling via Zendesk
- **Volume**: 500 tickets/day, 60% are common issues
- **Team**: 2 AI engineers, 1 backend engineer
- **Timeline**: 3 months to MVP
- **Constraints**: Must integrate with existing Zendesk, maintain response quality
- **Goals**: Reduce tier-1 ticket load by 50%, maintain customer satisfaction

### Orchestrator Analysis

**Problem Category**: AI agent development + System integration

**Required Capabilities**:
- Analyze requirements
- Decompose agent tasks
- Design agent workflow
- Select appropriate tools
- Ensure quality and safety
- Plan evaluation

### Skill Sequence

```
1. requirements-analysis
        ↓
2. agent-task-decomposition
        ↓
3. agent-workflow-design
        ↓
4. agent-context-engineering
        ↓
5. agent-tool-selection
        ↓
6. agent-instruction-design
        ↓
7. agent-guardrails
        ↓
8. integration-design
        ↓
9. security-review
        ↓
10. agent-evaluation
        ↓
11. agent-observability
        ↓
12. testing-strategy
        ↓
13. production-readiness
        ↓
14. technical-design-document
```

### Rationale

1. **requirements-analysis**: Clarify what the agent should and shouldn't do
2. **agent-task-decomposition**: Break down into subtasks (categorize, respond, escalate)
3. **agent-workflow-design**: Design the agent's decision-making process
4. **agent-context-engineering**: Determine what context the agent needs (ticket history, KB articles, customer data)
5. **agent-tool-selection**: Choose tools (Zendesk API, knowledge base search, LLM, classification model)
6. **agent-instruction-design**: Create effective prompts and instructions
7. **agent-guardrails**: Define boundaries (what agent can't do, when to escalate, safety checks)
8. **integration-design**: Design Zendesk integration and data flow
9. **security-review**: Ensure customer data protection and secure API access
10. **agent-evaluation**: Define metrics (accuracy, response quality, escalation rate)
11. **agent-observability**: Design monitoring (agent decisions, performance, errors)
12. **testing-strategy**: Plan testing (unit tests, integration tests, human evaluation)
13. **production-readiness**: Validate before launch (rollout plan, rollback strategy)
14. **technical-design-document**: Document the design for the team

### Expected Outcomes

- Clear agent requirements and boundaries
- Agent workflow diagram
- Context requirements
- Tool integration plan
- Guardrails and safety measures
- Evaluation metrics and benchmarks
- Monitoring and observability setup
- Testing plan
- Production deployment strategy
- Technical documentation

### Estimated Timeline

- Requirements & Decomposition (skills 1-2): 1 week
- Agent Design (skills 3-7): 3 weeks
- Integration & Security (skills 8-9): 2 weeks
- Evaluation & Observability (skills 10-11): 2 weeks
- Testing & Readiness (skills 12-13): 1 week
- Documentation (skill 14): 1 week
- **Total Planning Phase**: 10 weeks

---

## Example 3: Production Incident - Database Performance

### Problem Statement

"Our main database is experiencing severe performance degradation. Query response times have increased from 50ms to 5 seconds. This is affecting all customer-facing features. We need to identify the root cause and fix it urgently."

### Context

- **Current system**: PostgreSQL database, 500GB data
- **Impact**: All users experiencing slowness
- **Team**: 2 DBAs, 3 backend engineers, 1 SRE
- **Timeline**: Critical - need resolution within 24 hours
- **Constraints**: Cannot take database offline
- **Goals**: Restore performance, prevent recurrence

### Orchestrator Analysis

**Problem Category**: Production incident + Performance issue

**Required Capabilities**:
- Analyze the incident
- Find root cause
- Design fix
- Prevent recurrence
- Document learnings

### Skill Sequence

```
1. incident-analysis
        ↓
2. performance-analysis
        ↓
3. root-cause-analysis
        ↓
4. bottleneck-analysis
        ↓
5. architecture-decision (for fix)
        ↓
6. reliability-analysis (to prevent recurrence)
        ↓
7. observability-design (to detect earlier next time)
        ↓
8. runbook-generation (for future incidents)
```

### Rationale

1. **incident-analysis**: Gather facts (when did it start, what changed, impact scope)
2. **performance-analysis**: Analyze database metrics (slow queries, locks, connections, I/O)
3. **root-cause-analysis**: Determine the underlying cause (not just symptoms)
4. **bottleneck-analysis**: Identify specific bottleneck (query, index, connection pool, disk I/O)
5. **architecture-decision**: Decide on fix (add index, optimize query, scale up, partition table)
6. **reliability-analysis**: Analyze how to prevent similar issues (query review process, load testing)
7. **observability-design**: Improve monitoring to detect issues earlier (query performance alerts, resource alerts)
8. **runbook-generation**: Create runbook for similar incidents in the future

### Expected Outcomes

- Root cause identified (e.g., missing index on frequently queried column)
- Immediate fix applied (e.g., add index)
- Performance restored
- Prevention measures implemented (e.g., query review process)
- Improved monitoring
- Incident runbook
- Post-mortem document

### Estimated Timeline

- Incident & Performance Analysis (skills 1-2): 2 hours
- Root Cause & Bottleneck (skills 3-4): 2 hours
- Fix Design & Implementation (skill 5): 2 hours
- Prevention & Observability (skills 6-7): 4 hours
- Documentation (skill 8): 2 hours
- **Total**: 12 hours

---

## Example 4: New Feature - Payment Gateway Integration

### Problem Statement

"We need to integrate a new payment gateway (Stripe) into our e-commerce platform. Currently, we only support PayPal. The new integration should support credit cards, Apple Pay, and Google Pay."

### Context

- **Current system**: Node.js/Express backend, React frontend
- **Existing payment**: PayPal integration
- **Team**: 2 backend engineers, 1 frontend engineer
- **Timeline**: 6 weeks to production
- **Constraints**: PCI compliance required, must support existing PayPal
- **Goals**: Add Stripe support, maintain security, ensure reliability

### Orchestrator Analysis

**Problem Category**: New feature development + Third-party integration

**Required Capabilities**:
- Analyze requirements
- Design system integration
- Ensure security
- Plan testing
- Ensure production readiness

### Skill Sequence

```
1. requirements-analysis
        ↓
2. requirement-clarification
        ↓
3. system-design
        ↓
4. api-design-review
        ↓
5. integration-design
        ↓
6. security-review
        ↓
7. data-protection-review
        ↓
8. reliability-analysis
        ↓
9. testing-strategy
        ↓
10. technical-specification
        ↓
11. production-readiness
        ↓
12. technical-design-document
```

### Rationale

1. **requirements-analysis**: Understand all payment scenarios (checkout, refunds, webhooks)
2. **requirement-clarification**: Clarify edge cases (failed payments, partial refunds, currency support)
3. **system-design**: Design payment service architecture (abstraction layer for multiple gateways)
4. **api-design-review**: Review API design for payment endpoints
5. **integration-design**: Design Stripe integration (SDK usage, webhook handling, idempotency)
6. **security-review**: Ensure secure payment handling (no card data storage, HTTPS, API keys)
7. **data-protection-review**: Review PCI compliance (tokenization, encryption)
8. **reliability-analysis**: Design for payment reliability (retries, idempotency, reconciliation)
9. **testing-strategy**: Plan testing (unit tests, integration tests, Stripe test mode)
10. **technical-specification**: Create detailed implementation spec for engineers
11. **production-readiness**: Validate before launch (Stripe production keys, monitoring, rollback)
12. **technical-design-document**: Document the integration for the team

### Expected Outcomes

- Clear requirements for all payment scenarios
- Payment service architecture
- API design
- Stripe integration design
- Security and compliance validation
- Testing plan
- Implementation specification
- Production deployment plan
- Technical documentation

### Estimated Timeline

- Requirements (skills 1-2): 1 week
- Design (skills 3-5): 2 weeks
- Security & Reliability (skills 6-8): 1 week
- Testing & Specification (skills 9-10): 1 week
- Readiness & Documentation (skills 11-12): 1 week
- **Total Planning Phase**: 6 weeks

---

## Example 5: Architecture Review - Existing System

### Problem Statement

"We've inherited a 3-year-old microservices architecture from an acquisition. We need to understand it, identify issues, and create a plan for improvements."

### Context

- **Current system**: 15 microservices, Kubernetes, AWS
- **Team**: New to this codebase
- **Timeline**: 4 weeks for review
- **Constraints**: System is in production, limited documentation
- **Goals**: Understand architecture, identify risks, plan improvements

### Orchestrator Analysis

**Problem Category**: Architecture review + Knowledge transfer

**Required Capabilities**:
- Discover architecture
- Analyze quality attributes
- Identify issues
- Document findings
- Recommend improvements

### Skill Sequence

```
1. architecture-discovery
        ↓
2. dependency-mapping
        ↓
3. architecture-review
        ↓
4. scalability-analysis
        ↓
5. reliability-analysis
        ↓
6. security-architecture-review
        ↓
7. data-architecture-review
        ↓
8. technical-debt-analysis
        ↓
9. complexity-analysis
        ↓
10. observability-design (review existing)
        ↓
11. architecture-decision (for improvements)
        ↓
12. architecture-documentation
```

### Rationale

1. **architecture-discovery**: Understand the system (services, databases, integrations)
2. **dependency-mapping**: Map service dependencies and data flow
3. **architecture-review**: Evaluate overall architecture quality
4. **scalability-analysis**: Assess scalability (bottlenecks, limits)
5. **reliability-analysis**: Assess reliability (failure modes, resilience)
6. **security-architecture-review**: Identify security issues
7. **data-architecture-review**: Review data management (consistency, backups)
8. **technical-debt-analysis**: Identify technical debt
9. **complexity-analysis**: Identify unnecessary complexity
10. **observability-design**: Review monitoring and logging
11. **architecture-decision**: Prioritize improvements and create roadmap
12. **architecture-documentation**: Create comprehensive documentation

### Expected Outcomes

- Architecture diagram
- Dependency map
- Architecture review report
- Scalability assessment
- Reliability assessment
- Security findings
- Technical debt inventory
- Improvement roadmap
- Comprehensive documentation

### Estimated Timeline

- Discovery (skills 1-2): 1 week
- Reviews (skills 3-7): 2 weeks
- Analysis (skills 8-9): 1 week
- Observability & Planning (skills 10-11): 1 week
- Documentation (skill 12): 1 week
- **Total**: 6 weeks

---

## Example 6: Technology Selection - Frontend Framework

### Problem Statement

"We're starting a new web application and need to choose a frontend framework. We're considering React, Vue, and Svelte."

### Context

- **Project**: New SaaS application
- **Team**: 4 frontend engineers (experienced with React)
- **Timeline**: Need decision in 2 weeks
- **Constraints**: Must support SSR, good performance, active ecosystem
- **Goals**: Choose framework that balances productivity, performance, and maintainability

### Orchestrator Analysis

**Problem Category**: Technology selection

**Required Capabilities**:
- Analyze requirements
- Evaluate options
- Compare tradeoffs
- Make decision
- Document decision

### Skill Sequence

```
1. requirements-analysis
        ↓
2. technology-selection
        ↓
3. tradeoff-analysis
        ↓
4. poc-evaluation (optional)
        ↓
5. technology-risk-analysis
        ↓
6. architecture-decision
        ↓
7. adr-documentation
```

### Rationale

1. **requirements-analysis**: Define what we need from the framework (SSR, performance, DX, ecosystem)
2. **technology-selection**: Evaluate React, Vue, Svelte against requirements
3. **tradeoff-analysis**: Compare tradeoffs (learning curve, performance, ecosystem, hiring)
4. **poc-evaluation**: Build small POCs to validate assumptions (optional)
5. **technology-risk-analysis**: Identify risks (community support, breaking changes, vendor lock-in)
6. **architecture-decision**: Make the decision with clear rationale
7. **adr-documentation**: Document the decision for future reference

### Expected Outcomes

- Requirements for frontend framework
- Evaluation of each option
- Tradeoff comparison matrix
- Risk assessment
- Decision with rationale
- ADR document

### Estimated Timeline

- Requirements (skill 1): 2 days
- Evaluation (skills 2-3): 1 week
- POC (skill 4): 3 days (if needed)
- Risk & Decision (skills 5-6): 2 days
- Documentation (skill 7): 1 day
- **Total**: 2 weeks

---

## Key Patterns Observed

### Pattern 1: Discovery → Design → Validate → Document

Most workflows follow this pattern:
1. Understand the current state or requirements
2. Design the solution
3. Validate the design (security, performance, reliability)
4. Document the decisions and design

### Pattern 2: Analysis Before Decision

Never jump to decisions:
1. Analyze the problem
2. Identify options
3. Compare tradeoffs
4. Then decide

### Pattern 3: Security and Reliability are Non-Negotiable

For production systems, always include:
- Security review
- Reliability analysis
- Production readiness

### Pattern 4: Documentation is Essential

Always end with documentation:
- For decisions: ADRs
- For architecture: Architecture docs
- For operations: Runbooks
- For implementation: Technical specs

### Pattern 5: Context Matters

The same problem (e.g., "build a new feature") requires different workflows based on:
- Complexity
- Risk
- Team experience
- Timeline
- Existing system

## Conclusion

These examples demonstrate how the skill-orchestrator analyzes problems and composes appropriate workflows. The key is to:

1. **Understand the problem deeply**
2. **Identify required capabilities**
3. **Map capabilities to skills**
4. **Sequence skills logically**
5. **Validate the workflow**
6. **Document the rationale**

With practice, you'll recognize common patterns and be able to quickly compose effective workflows for various engineering challenges.