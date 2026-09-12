# Agentic Engineering Skills - Comprehensive Guide

## Overview

Phase 2 introduces 10 specialized skills for designing, implementing, and managing AI agent-based engineering workflows. These skills enable you to build production-ready multi-agent systems with proper planning, safety, and observability.

For an end-to-end sequence, follow the [agent development workflow](../workflows/agent-development.md). It composes requirements, task decomposition, workflow design, context and instruction design, tool selection, guardrails, evaluation, observability, and production readiness.

## When to Use Agentic Skills

**Use agentic skills when:**

- Building multi-agent systems or workflows
- Automating complex engineering tasks with AI agents
- Designing agent orchestration and coordination
- Implementing safety and quality controls for agents
- Evaluating and optimizing agent performance
- Debugging and monitoring agent workflows

**Don't use agentic skills for:**

- Simple single-agent tasks (use foundational skills instead)
- Non-AI automation (use engineering/operations skills)
- Manual engineering work without AI assistance

## Available Agentic Skills

### Agent Planning (2 Skills)

1. **agent-task-decomposition** - Break complex problems into agent tasks
2. **agent-workflow-design** - Design multi-agent workflows and coordination

### Agent Context (2 Skills)

3. **agent-context-engineering** - Structure and optimize context for agents
4. **agent-instruction-design** - Create clear, effective agent instructions

### Agent Execution (2 Skills)

5. **agent-tool-selection** - Select and configure tools for agents
6. **agent-handoff-design** - Design context transfer between agents

### Agent Safety & Quality (2 Skills)

7. **agent-guardrails** - Implement safety and compliance controls
8. **agent-evaluation** - Measure and improve agent performance

### Agent Operations (2 Skills)

9. **agent-observability** - Monitor and debug agent workflows
10. **agentic-workflow-review** - Review and optimize agent systems

## Multi-Agent Workflow Patterns

### Pattern 1: Sequential Pipeline

```
Agent A → Agent B → Agent C → Agent D
```

**Use when:**

- Each agent depends on previous agent's output
- Linear workflow with clear dependencies
- Order matters

**Example:** Code review pipeline

```
Static Analysis → Architecture Review → Test Coverage → Documentation Review
```

### Pattern 2: Parallel Execution

```
        ┌─ Agent A ─┐
Input ─┤          ├─ Aggregator → Output
        └─ Agent B ─┘
```

**Use when:**

- Agents work independently
- No dependencies between agents
- Speed is important

**Example:** Multi-aspect analysis

```
              ┌─ Security Review ─┐
Architecture ─┤ Scalability     ├─ Combined Report
              └─ Reliability     ─┘
```

### Pattern 3: Conditional Branching

```
         ┌─ Path A ─┐
Decision ─┤          ├─ Merge
         └─ Path B ─┘
```

**Use when:**

- Different paths based on conditions
- Dynamic workflow selection
- Context-dependent execution

**Example:** Migration strategy

```
                ┌─ Microservices Path ─┐
Architecture ──┤ Modular Monolith   ├─ Implementation
                └─ Serverless Path    ─┘
```

### Pattern 4: Iterative Refinement

```
Agent → Validator ────────────────┐
  ↑                              │
  └────── If not valid ───────┘
```

**Use when:**

- Quality refinement needed
- Iterative improvement
- Validation loops

**Example:** Documentation generation

```
Generate Docs → Quality Check ────────────────┐
     ↑                                      │
     └────── If quality < 90% ───────┘
```

## Example: Building a Code Review Agent System

Complete walkthrough of building a production-ready multi-agent code review system.

### Step 1: Task Decomposition

```yaml
Tasks:
  - Static Analysis Agent:
      Purpose: Detect linting errors and code smells
      Input: Source code files
      Output: List of issues with severity
      Tools: ESLint, SonarQube, Semgrep

  - Architecture Review Agent:
      Purpose: Validate architectural patterns
      Input: Code structure, dependencies
      Output: Architecture violations
      Tools: Dependency analysis, pattern matching

  - Test Coverage Agent:
      Purpose: Identify untested code
      Input: Source code, test files
      Output: Coverage gaps, missing tests
      Tools: Jest coverage, test analysis

  - Documentation Agent:
      Purpose: Check documentation completeness
      Input: Code files, existing docs
      Output: Missing or unclear documentation
      Tools: JSDoc parser, comment analysis
```

### Step 2: Workflow Design

```yaml
Workflow:
  Pattern: Parallel with aggregation

  Execution:
    - Trigger: Pull request created
    - Parallel execution:
        - Static Analysis Agent
        - Architecture Review Agent
        - Test Coverage Agent
        - Documentation Agent
    - Aggregate results
    - Generate consolidated report

  Error Handling:
    - Continue on agent failure
    - Mark failed agents in report
    - Retry transient failures (max 3)

  Timeout: 5 minutes total
```

### Step 3: Context Engineering

```yaml
Context Structure:
  Overview:
    - Repository structure
    - Coding standards
    - Architecture principles

  Interfaces:
    - PR diff
    - Changed files list
    - Related files

  Implementation:
    - Full file contents for changed files
    - Relevant test files
    - Configuration files

  Examples:
    - Good code examples from repo
    - Common patterns to follow
    - Anti-patterns to avoid

  Metadata:
    - File paths
    - Dependencies
    - Test coverage baseline
    - Previous review comments
```

### Step 4: Instruction Design

```yaml
Static Analysis Agent Instructions:
  1. Parse provided source code files
  2. Run configured linters (ESLint, Prettier)
  3. Categorize violations by severity:
     - Critical: Security issues, breaking changes
     - High: Code quality, performance
     - Medium: Style violations
     - Low: Suggestions
  4. For each violation:
     - Provide exact location (file:line:column)
     - Explain the issue
     - Suggest specific fix with code example
     - Reference coding standard
  5. Filter false positives using context
  6. Return structured JSON report
```

### Step 5: Tool Selection

```yaml
Selected Tools:
  Linting:
    - ESLint: JavaScript/TypeScript linting
    - Prettier: Code formatting
    - SonarQube: Code quality and security

  Analysis:
    - Semgrep: Custom pattern matching
    - Dependency-cruiser: Dependency validation

  Testing:
    - Jest: Test coverage analysis
    - Test parser: Test structure analysis

  Configuration:
    - Read-only repository access
    - API rate limits: 100 calls/hour
    - Timeout: 2 minutes per agent
```

### Step 6: Handoff Design

```yaml
Handoff: Agents to Aggregator
  Type: Asynchronous (wait for all)

  Context Transfer:
    - Agent name and status
    - Findings list
    - Execution time
    - Error messages (if any)

  Schema:
    {
      "agent": "static-analysis",
      "status": "completed",
      "findings": [
        {
          "severity": "high",
          "file": "src/utils.ts",
          "line": 42,
          "message": "...",
          "suggestion": "..."
        }
      ],
      "execution_time_ms": 1234,
      "error": null
    }
```

### Step 7: Guardrails

```yaml
Safety Guardrails:
  - Read-only repository access
  - No code modification without approval
  - Rate limiting: 100 API calls/hour
  - Timeout: 5 minutes per agent
  - Sandbox execution environment

Quality Guardrails:
  - Minimum 80% confidence for flagged issues
  - Cross-validation between agents
  - Human review for critical findings
  - False positive tracking

Compliance Guardrails:
  - No exposure of sensitive data
  - Audit logging of all actions
  - GDPR compliance for code analysis
  - Data retention: 90 days
```

For workflow-level human approvals, use the [HITL Checkpoint Contract](../workflows/hitl-checkpoint-contract.md). It defines the evidence, decision owner, prohibited actions, expiry, escalation, and audit record required before an agent can resume a high-impact action.

### Step 8: Evaluation

```yaml
Metrics:
  Quantitative:
    - Accuracy: % of flagged issues that are valid
    - Completeness: % of actual issues found
    - Latency: Time to complete review
    - Cost: API calls and compute

  Qualitative:
    - Usefulness: Developer feedback (1-5)
    - Clarity: How clear are suggestions
    - Actionability: Can developers act on findings

Targets:
  - Accuracy: >90%
  - Completeness: >85%
  - Latency: <3 minutes
  - Cost: <$0.50 per review
  - Usefulness: >4.0/5.0

Evaluation Process:
  - Continuous: Track all metrics
  - Weekly: Review trends
  - Monthly: Developer survey
  - Quarterly: Comprehensive analysis
```

### Step 9: Observability

```yaml
Logging:
  - Format: Structured JSON
  - Correlation ID: Per PR review
  - Levels: DEBUG, INFO, WARN, ERROR
  - Retention: 90 days

Metrics:
  - Agent execution time
  - Handoff latency
  - API call count
  - Error rate by type
  - Success rate

Tracing:
  - Distributed trace across agents
  - Spans per agent
  - Handoff spans
  - Error traces

Dashboards:
  1. Workflow Overview:
    - Current status
    - Queue depth
    - Completion rate
    - Error count

  2. Performance:
    - Latency (p50, p95, p99)
    - Throughput (reviews/hour)
    - Agent utilization

  3. Quality:
    - Accuracy trends
    - False positive rate
    - Developer feedback

Alerts:
  - Error rate >5%: Page on-call
  - Latency >5 min: Warning
  - Queue depth >50: Warning
```

### Step 10: Workflow Review

```yaml
Review Schedule:
  - Weekly: Quick metrics check
  - Monthly: Performance optimization
  - Quarterly: Comprehensive review

Review Checklist:
  Performance:
    - Meeting latency targets?
    - Parallelization opportunities?
    - Bottlenecks identified?

  Reliability:
    - Error rate acceptable?
    - Retries working?
    - Fallbacks sufficient?

  Quality:
    - Developer satisfaction?
    - Accuracy improving?
    - False positives decreasing?

  Cost:
    - Within budget?
    - API usage optimized?
    - Resource utilization efficient?
```

## Skill Composition Patterns

### Pattern 1: Production-Ready Agent System

**Skill Sequence:**

```
agent-task-decomposition
    ↓
agent-workflow-design
    ↓
agent-context-engineering
    ↓
agent-instruction-design
    ↓
agent-tool-selection
    ↓
agent-handoff-design
    ↓
agent-guardrails
    ↓
agent-evaluation
    ↓
agent-observability
    ↓
agentic-workflow-review
```

**Use when:** Building a new multi-agent system from scratch

### Pattern 2: Continuous Improvement

**Skill Sequence:**

```
agent-evaluation
    ↓
agent-observability
    ↓
agentic-workflow-review
    ↓
Optimization (iterate)
```

**Use when:** Optimizing an existing agent system

### Pattern 3: Incident Response

**Skill Sequence:**

```
agent-observability (investigate)
    ↓
agent-evaluation (assess impact)
    ↓
agent-guardrails (adjust controls)
    ↓
agentic-workflow-review (prevent recurrence)
```

**Use when:** Troubleshooting agent workflow failures

## Best Practices for Agentic Engineering

### 1. Start Simple

- Begin with single-agent workflows
- Add complexity gradually
- Validate each addition

### 2. Design for Failure

- Assume agents will fail
- Implement retries and fallbacks
- Graceful degradation

### 3. Measure Everything

- Track all metrics from day one
- Use data to drive improvements
- A/B test changes

### 4. Prioritize Safety

- Implement guardrails early
- Regular security reviews
- Audit all agent actions

### 5. Iterate Based on Feedback

- Collect user feedback
- Monitor quality metrics
- Continuously improve

## Related Documentation

- [Back to Overview](01-overview.md)
- [Examples and Use Cases](09-examples.md)
- [Best Practices](07-best-practices.md)
- [Troubleshooting](10-troubleshooting.md)
