# Skill Orchestrator

## Purpose

Determine which engineering skills to use, in what order, and why, based on the engineering problem at hand.

This meta-skill does **not** solve the engineering problem directly. Instead, it analyzes the problem and composes a workflow of other skills that, when executed in sequence, will solve the problem effectively.

## When to Use

- When facing a complex engineering problem that requires multiple skills
- When you need to plan a structured approach to an engineering challenge
- When you want to ensure all necessary aspects (architecture, security, testing, etc.) are addressed
- When you need to explain your engineering approach to others
- When working with AI agents that need a clear execution plan

## When NOT to Use

- For simple, single-skill problems (e.g., "review this API design" → use `api-design-review` directly)
- When you already know the exact skill sequence needed
- For problems outside the engineering domain
- When immediate action is needed without planning

## Inputs

- **engineering-problem**: Clear description of the engineering challenge
- **context**: Relevant background (existing system, constraints, team, timeline)
- **constraints**: Technical, business, or resource limitations
- **goals**: Desired outcomes and success criteria

## Expected Outputs

- **skill-sequence**: Ordered list of skills to execute
- **workflow-plan**: Detailed execution plan with dependencies
- **rationale**: Explanation of why each skill is needed and why in this order
- **dependencies**: What each skill needs from previous steps

## Workflow

### 1. Analyze the Problem

- Identify the core engineering challenge
- Determine problem category (architecture, migration, new feature, incident, etc.)
- Assess complexity and scope
- Identify stakeholders and their needs

### 2. Identify Required Capabilities

- What needs to be understood? (discovery, analysis)
- What needs to be designed? (architecture, system design)
- What needs to be decided? (technology selection, tradeoffs)
- What needs to be validated? (security, performance, reliability)
- What needs to be documented? (ADRs, design docs, runbooks)
- What needs to be implemented? (code, infrastructure, tests)
- What needs to be operated? (deployment, monitoring, incident response)

### 3. Map Capabilities to Skills

- For each required capability, identify the appropriate skill(s)
- Consider skill relationships and dependencies
- Check for skills that commonly work together
- Identify optional vs. mandatory skills

### 4. Sequence the Skills

- Start with discovery/analysis skills
- Follow with design/decision skills
- Include validation skills (security, performance, reliability)
- Add documentation skills
- End with implementation/operational skills
- Ensure each skill has the inputs it needs from previous steps

### 5. Validate the Workflow

- Does the sequence make logical sense?
- Are there any gaps in the workflow?
- Are there any unnecessary steps?
- Will the final output meet the stated goals?
- Can this workflow be executed by the available team/agents?

### 6. Document the Plan

- List skills in execution order
- Explain why each skill is needed
- Show dependencies between skills
- Identify decision points
- Specify expected outputs at each stage

## Decision Framework

### Problem Category Patterns

**New Feature Development:**
```
requirements-analysis
    ↓
system-design
    ↓
architecture-review
    ↓
security-review
    ↓
api-design-review
    ↓
testing-strategy
    ↓
production-readiness
```

**Architecture Review:**
```
architecture-discovery
    ↓
architecture-review
    ↓
scalability-analysis
    ↓
reliability-analysis
    ↓
security-architecture-review
    ↓
data-architecture-review
    ↓
architecture-decision
```

**Legacy Modernization:**
```
architecture-discovery
    ↓
technical-debt-analysis
    ↓
dependency-analysis
    ↓
service-boundary-analysis
    ↓
migration-planning
    ↓
architecture-decision
    ↓
testing-strategy
    ↓
production-readiness
```

**AI Agent Development:**
```
requirements-analysis
    ↓
agent-task-decomposition
    ↓
agent-workflow-design
    ↓
agent-context-engineering
    ↓
agent-tool-selection
    ↓
agent-guardrails
    ↓
security-review
    ↓
agent-evaluation
    ↓
agent-observability
    ↓
production-readiness
```

**Production Incident:**
```
incident-analysis
    ↓
root-cause-analysis
    ↓
failure-mode-analysis
    ↓
reliability-analysis
    ↓
observability-design
    ↓
architecture-decision
```

### Skill Selection Criteria

**Always include:**
- Skills that address the core problem
- Skills that validate critical aspects (security, reliability)
- Skills that document key decisions

**Consider including:**
- Skills that provide deeper analysis
- Skills that explore alternatives
- Skills that improve quality

**Usually skip:**
- Skills not relevant to the problem domain
- Skills whose outputs aren't needed
- Skills that duplicate other skills

## Quality Checklist

- [ ] The skill sequence addresses the stated problem
- [ ] Each skill's inputs are available from previous steps or initial context
- [ ] The sequence follows a logical order (discover → design → decide → validate → implement)
- [ ] Critical aspects are validated (security, reliability, performance)
- [ ] Key decisions are documented
- [ ] The final output meets the stated goals
- [ ] There are no unnecessary steps
- [ ] There are no gaps in the workflow
- [ ] The workflow is executable by the available team/agents
- [ ] The rationale for each skill is clear

## Common Mistakes

- **Skipping discovery**: Jumping to design without understanding the current state
- **Missing validation**: Not including security, reliability, or performance reviews
- **Wrong order**: Trying to make decisions before gathering necessary information
- **Over-engineering**: Including skills that don't add value for the specific problem
- **Under-engineering**: Skipping critical skills to save time
- **Ignoring dependencies**: Sequencing skills without considering what inputs they need
- **One-size-fits-all**: Using the same workflow for different problem types

## Examples

See [examples.md](examples.md) for detailed examples of skill orchestration for various engineering scenarios.

## Related Skills

- **Requires**: None (this is a foundational meta-skill)
- **Commonly followed by**: The first skill in the composed sequence
- **Works with**: All skills in the library
- **Alternative to**: Manual skill selection and sequencing

## Skill Composition

This skill is unique in that it doesn't compose with other skills in the traditional sense. Instead, it **creates compositions** of other skills.

The orchestrator can be used recursively:
```
skill-orchestrator (high-level problem)
    ↓
skill-orchestrator (sub-problem 1)
    ↓
skill-sequence-1
    ↓
skill-orchestrator (sub-problem 2)
    ↓
skill-sequence-2
```

## Evaluation Criteria

### Workflow Quality
- Does the workflow solve the stated problem?
- Is the sequence logical and efficient?
- Are all critical aspects addressed?

### Completeness
- Are there any gaps in the workflow?
- Are all necessary skills included?
- Are dependencies properly handled?

### Practicality
- Can the workflow be executed with available resources?
- Is the time estimate realistic?
- Are the outputs actionable?

### Clarity
- Is the rationale clear and convincing?
- Are dependencies well-documented?
- Can someone else execute this workflow?

## Advanced Usage

### Conditional Workflows

Some workflows may have decision points:

```
architecture-discovery
    ↓
IF (monolith) → service-boundary-analysis
IF (microservices) → integration-design
    ↓
architecture-review
```

### Parallel Execution

Some skills can be executed in parallel:

```
architecture-discovery
    ↓
┌───────────────┬────────────────┬─────────────────┐
│ scalability   │ security       │ reliability     │
│ analysis      │ review         │ analysis        │
└───────────────┴────────────────┴─────────────────┘
    ↓
architecture-decision
```

### Iterative Workflows

Some workflows may require iteration:

```
architecture-design
    ↓
architecture-review
    ↓
IF (issues found) → REPEAT architecture-design
IF (approved) → CONTINUE
    ↓
implementation
```

## Integration with AI Agents

This skill is particularly valuable for AI coding agents:

1. **Agent receives problem** → Calls skill-orchestrator
2. **Orchestrator returns workflow** → Agent executes skills in sequence
3. **Each skill produces output** → Becomes input for next skill
4. **Final skill completes** → Problem solved

The orchestrator enables agents to handle complex, multi-step engineering problems systematically rather than attempting to solve everything in a single step.