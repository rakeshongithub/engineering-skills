# Agent Context Engineering

**Purpose**: Engineer effective context packages that enable AI agents to execute tasks with complete understanding and minimal ambiguity.

---

## When to Use

Use this skill when:
- Preparing tasks for AI agent execution
- An agent needs to understand existing code, architecture, or business logic
- Handoffs between agents require shared understanding
- Agent output quality is suffering due to insufficient context
- Designing prompts or instructions for autonomous agents
- Building multi-agent systems that need coordinated understanding
- Debugging agent failures caused by context gaps
- Optimizing agent performance through better context engineering

## When NOT to Use

Do not use this skill when:
- Writing documentation for human developers (use standard documentation practices)
- The task is simple enough that minimal context suffices
- Context requirements are already well-defined and validated
- You're working with a single, isolated function that has no dependencies
- The agent has already demonstrated successful execution with current context

---

## Inputs

### Required
- **Task Definition**: Clear description of what the agent needs to accomplish
- **Agent Capabilities**: Understanding of the agent's tools, knowledge, and limitations
- **Existing Codebase**: Access to relevant code, documentation, and artifacts
- **Success Criteria**: How to measure if the agent completed the task correctly

### Optional
- **Previous Agent Outputs**: Context from earlier agents in the workflow
- **Domain Knowledge**: Business rules, technical constraints, compliance requirements
- **Examples**: Similar tasks or reference implementations
- **Error History**: Previous failures and their root causes

---

## Expected Outputs

### Primary Deliverables
1. **Context Package**: Structured collection of information for agent consumption
2. **Context Map**: Visual or structured representation of context relationships
3. **Retrieval Strategy**: How and when the agent should access different context elements
4. **Validation Checklist**: Criteria to verify context completeness and quality

### Supporting Artifacts
- **Context Templates**: Reusable patterns for common task types
- **Context Metrics**: Size, complexity, and coverage measurements
- **Context Dependencies**: What context requires what other context
- **Context Versioning**: How context evolves as the codebase changes

---

## Workflow

### Step 1: Analyze Task Requirements
**Objective**: Understand what the agent needs to know to succeed.

**Actions**:
1. Break down the task into specific operations the agent will perform
2. Identify decision points where the agent needs contextual understanding
3. List all code artifacts the agent will read, modify, or create
4. Determine what domain knowledge is essential vs. nice-to-have
5. Consider edge cases and error scenarios the agent must handle

**Output**: Task requirements document with context needs identified

**Quality Check**: Can you explain why each piece of context is necessary?

---

### Step 2: Identify Context Sources
**Objective**: Locate where the needed context exists.

**Actions**:
1. Map task requirements to specific files, modules, or documentation
2. Identify architectural diagrams, ADRs, or design documents
3. Locate relevant examples, tests, or reference implementations
4. Find domain knowledge in requirements, user stories, or business rules
5. Identify implicit context (coding standards, patterns, conventions)

**Output**: Context source inventory with locations and access methods

**Quality Check**: Have you identified both explicit and implicit context sources?

---

### Step 3: Assess Context Scope
**Objective**: Determine how much context is needed without overwhelming the agent.

**Actions**:
1. Calculate token/size requirements for each context element
2. Prioritize context by criticality (must-have, should-have, nice-to-have)
3. Identify context that can be retrieved on-demand vs. provided upfront
4. Consider the agent's context window limitations
5. Balance completeness with cognitive load

**Output**: Prioritized context inventory with size estimates

**Quality Check**: Does the context fit within agent constraints while covering critical needs?

---

### Step 4: Structure Context Hierarchically
**Objective**: Organize context in layers from high-level to detailed.

**Actions**:
1. Create overview layer: architecture, purpose, key concepts
2. Create interface layer: APIs, contracts, public interfaces
3. Create implementation layer: specific code, algorithms, logic
4. Create example layer: test cases, usage examples, patterns
5. Create metadata layer: dependencies, versions, constraints

**Output**: Hierarchical context structure with clear layers

**Quality Check**: Can the agent navigate from general understanding to specific details?

---

### Step 5: Design Context Retrieval Strategy
**Objective**: Define how the agent accesses context during execution.

**Actions**:
1. Identify context that should be provided in initial prompt
2. Design retrieval mechanisms for on-demand context (RAG, search, file access)
3. Define context refresh triggers (when to re-fetch updated information)
4. Plan for context caching and reuse across agent invocations
5. Design fallback strategies when context is unavailable

**Output**: Context retrieval plan with access patterns

**Quality Check**: Can the agent efficiently access context when needed without delays?

---

### Step 6: Enrich Context with Metadata
**Objective**: Add information that helps the agent understand and use context effectively.

**Actions**:
1. Add provenance: where context came from, when it was created
2. Add confidence levels: how reliable or current the context is
3. Add relationships: how context elements relate to each other
4. Add usage guidance: how to interpret and apply the context
5. Add constraints: what limitations or assumptions apply

**Output**: Metadata-enriched context package

**Quality Check**: Does the agent have enough information to judge context quality and applicability?

---

### Step 7: Validate Context Completeness
**Objective**: Ensure no critical context gaps exist.

**Actions**:
1. Walk through the task step-by-step with the context package
2. Identify decision points and verify context supports each decision
3. Check for missing dependencies, imports, or related code
4. Verify examples cover the task's complexity and edge cases
5. Test context with a simulated agent execution or human review

**Output**: Context validation report with gap analysis

**Quality Check**: Can the task be completed successfully with only the provided context?

---

### Step 8: Optimize Context for Agent Consumption
**Objective**: Format context for maximum agent comprehension and efficiency.

**Actions**:
1. Use clear, consistent formatting (markdown, JSON, structured text)
2. Add section headers, summaries, and navigation aids
3. Highlight critical information (warnings, constraints, requirements)
4. Remove noise, redundancy, and irrelevant details
5. Structure for sequential reading or random access as appropriate

**Output**: Optimized, agent-ready context package

**Quality Check**: Is the context easy to parse, navigate, and understand?

---

### Step 9: Implement Context Versioning
**Objective**: Ensure context stays current as the codebase evolves.

**Actions**:
1. Tag context with version numbers or timestamps
2. Define update triggers (code changes, new requirements, bug fixes)
3. Implement change detection for context sources
4. Create context diff mechanisms to show what changed
5. Plan for context deprecation and archival

**Output**: Context versioning strategy and implementation

**Quality Check**: Will the agent always receive current, accurate context?

---

### Step 10: Monitor and Refine Context Quality
**Objective**: Continuously improve context based on agent performance.

**Actions**:
1. Track agent success rates with different context configurations
2. Analyze agent errors to identify context gaps or ambiguities
3. Collect feedback from agent outputs and human reviews
4. A/B test different context structures or content
5. Iterate on context design based on empirical results

**Output**: Context quality metrics and improvement plan

**Quality Check**: Is context quality improving over time based on data?

---

## Decision Framework

### Context Scope Decision Tree

```
Is the task well-defined and isolated?
├─ Yes → Minimal context (interfaces, immediate dependencies)
└─ No → Is it a new feature or modification?
   ├─ New feature → Architecture + patterns + examples
   └─ Modification → Existing code + change rationale + impact analysis

Does the agent have domain knowledge?
├─ Yes → Focus on technical context (code, APIs, architecture)
└─ No → Include business context (requirements, rules, examples)

Is the codebase familiar to the agent?
├─ Yes → Incremental context (only what's new or changed)
└─ No → Comprehensive context (architecture, patterns, conventions)

Are there critical constraints?
├─ Yes → Explicit constraint documentation (security, performance, compliance)
└─ No → Standard best practices

Is this a one-time task or recurring?
├─ One-time → Inline context in prompt
└─ Recurring → Build reusable context templates
```

### Context Format Selection

| Context Type | Best Format | Rationale |
|--------------|-------------|------------|
| Code snippets | Markdown code blocks with language tags | Syntax highlighting, clear boundaries |
| Architecture | Diagrams (ASCII/Mermaid) + descriptions | Visual + textual understanding |
| APIs/Interfaces | Structured lists or tables | Easy scanning, clear contracts |
| Examples | Annotated code with explanations | Shows usage in context |
| Requirements | Numbered lists with acceptance criteria | Clear, testable, traceable |
| Constraints | Highlighted callouts or warnings | Draws attention to critical info |
| Relationships | Dependency graphs or maps | Shows connections and impacts |

---

## Quality Checklist

### Context Completeness
- [ ] All task requirements have corresponding context
- [ ] Dependencies and related code are included
- [ ] Domain knowledge and business rules are documented
- [ ] Examples cover common and edge cases
- [ ] Error handling and constraints are explicit
- [ ] Implicit assumptions are made explicit

### Context Quality
- [ ] Information is current and accurate
- [ ] No contradictions or ambiguities
- [ ] Appropriate level of detail (not too shallow or deep)
- [ ] Clear relationships between context elements
- [ ] Metadata provides provenance and confidence levels

### Context Usability
- [ ] Well-structured and easy to navigate
- [ ] Consistent formatting throughout
- [ ] Critical information is highlighted
- [ ] Fits within agent's context window constraints
- [ ] Can be consumed sequentially or randomly accessed
- [ ] Includes usage guidance and examples

### Context Maintainability
- [ ] Versioned and timestamped
- [ ] Update triggers defined
- [ ] Sources are traceable
- [ ] Can be regenerated or refreshed automatically
- [ ] Deprecation strategy exists

---

## Common Mistakes

### 1. Context Overload
**Mistake**: Providing too much context, overwhelming the agent.

**Why It Happens**: Fear of missing something important; lack of prioritization.

**How to Avoid**:
- Prioritize context by criticality
- Use on-demand retrieval for nice-to-have context
- Test with minimal context first, then add as needed
- Monitor context window usage

**Recovery**: If agent performance degrades, reduce context to essentials and use retrieval for details.

---

### 2. Implicit Assumptions
**Mistake**: Assuming the agent knows conventions, patterns, or domain knowledge.

**Why It Happens**: Human experts forget what's not obvious to agents.

**How to Avoid**:
- Document all conventions and patterns explicitly
- Include examples that demonstrate implicit knowledge
- Have someone unfamiliar with the codebase review context
- Test context with a fresh agent instance

**Recovery**: When agent makes unexpected decisions, identify and document the missing implicit knowledge.

---

### 3. Stale Context
**Mistake**: Providing outdated context that doesn't reflect current codebase state.

**Why It Happens**: Context isn't updated when code changes; no versioning.

**How to Avoid**:
- Implement context versioning
- Automate context regeneration
- Add timestamps and change detection
- Validate context before agent execution

**Recovery**: When agent produces incorrect output, check if context is current; regenerate if needed.

---

### 4. Missing Relationships
**Mistake**: Providing isolated context without showing how pieces relate.

**Why It Happens**: Focus on individual components without system-level view.

**How to Avoid**:
- Create dependency maps and relationship diagrams
- Document how components interact
- Include call graphs and data flow
- Show impact analysis for changes

**Recovery**: When agent misses side effects or dependencies, add relationship documentation.

---

### 5. Poor Context Structure
**Mistake**: Unorganized, hard-to-navigate context dumps.

**Why It Happens**: Lack of planning; treating context as afterthought.

**How to Avoid**:
- Design hierarchical structure (overview → details)
- Use consistent formatting and headers
- Add navigation aids (table of contents, cross-references)
- Organize by agent's consumption pattern

**Recovery**: Restructure context with clear sections, headers, and logical flow.

---

### 6. Ambiguous Context
**Mistake**: Context that can be interpreted multiple ways.

**Why It Happens**: Vague language; lack of examples; missing constraints.

**How to Avoid**:
- Use precise, technical language
- Provide examples for clarification
- Make constraints and requirements explicit
- Have context reviewed for clarity

**Recovery**: When agent produces unexpected output, identify ambiguities and clarify.

---

### 7. Ignoring Agent Limitations
**Mistake**: Providing context in formats the agent can't process effectively.

**Why It Happens**: Not understanding agent's capabilities and constraints.

**How to Avoid**:
- Know the agent's context window size
- Understand what formats the agent handles well
- Test context with the specific agent
- Design retrieval for large context needs

**Recovery**: Reformat context to match agent's optimal consumption patterns.

---

### 8. No Context Validation
**Mistake**: Not testing if context is sufficient before agent execution.

**Why It Happens**: Time pressure; overconfidence; lack of process.

**How to Avoid**:
- Walk through task with context before agent execution
- Simulate agent decision points
- Have peer review context completeness
- Run pilot tests with small tasks

**Recovery**: When agent fails, perform gap analysis and add missing context.

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **REST API Implementation**: Engineering context for an agent to build a new API endpoint
2. **Legacy Code Refactoring**: Providing context for safe refactoring of complex legacy code
3. **Multi-Agent Bug Fix**: Coordinating context across agents for collaborative debugging
4. **Database Migration**: Context engineering for schema changes with data preservation

---

## Related Skills

### Prerequisites
- **architecture-discovery**: Understanding the system before engineering context
- **requirements-analysis**: Knowing what the agent needs to accomplish
- **agent-task-decomposition**: Breaking down tasks to identify context needs

### Commonly Followed By
- **agent-instruction-design**: Using engineered context to write clear instructions
- **agent-tool-selection**: Choosing tools that can access and use the context
- **agent-handoff-design**: Passing context between agents in workflows

### Works Well With
- **agent-workflow-design**: Coordinating context across multi-agent workflows
- **agent-guardrails**: Ensuring context includes safety and quality constraints
- **agent-observability**: Monitoring how agents use context

### Alternatives
- **Manual Documentation**: For human developers (less structured, more narrative)
- **RAG Systems**: For dynamic, retrieval-based context (complements this skill)

---

## Skill Composition

### Pattern: Agent-Ready Development
```
requirements-analysis
    ↓
architecture-discovery
    ↓
agent-task-decomposition
    ↓
agent-context-engineering ← YOU ARE HERE
    ↓
agent-instruction-design
    ↓
Execution
```

### Pattern: Context-Driven Multi-Agent System
```
agent-context-engineering (initial)
    ↓
agent-workflow-design
    ↓
agent-context-engineering (per agent)
    ↓
agent-handoff-design (context transfer)
    ↓
agent-evaluation (context quality assessment)
```

---

## Evaluation Criteria

### Context Effectiveness (Primary Metric)
- **Agent Success Rate**: Percentage of tasks completed correctly with provided context
- **Context Sufficiency**: Percentage of agent questions/errors due to missing context
- **Context Efficiency**: Ratio of used context to provided context

### Context Quality Metrics
- **Completeness Score**: Percentage of task requirements covered by context
- **Accuracy Score**: Percentage of context that is current and correct
- **Clarity Score**: Percentage of context that is unambiguous (measured by agent interpretation consistency)

### Context Efficiency Metrics
- **Context Size**: Total tokens/characters in context package
- **Context Utilization**: Percentage of context actually used by agent
- **Retrieval Latency**: Time to access on-demand context

### Maintenance Metrics
- **Staleness**: Age of context relative to source code
- **Update Frequency**: How often context is refreshed
- **Regeneration Cost**: Time/resources to update context

### Target Benchmarks
- Agent success rate: >90% with engineered context
- Context sufficiency: <5% of errors due to missing context
- Context utilization: >70% of provided context used
- Staleness: <24 hours for active codebases
- Completeness score: >95%

---

## Advanced Topics

### Dynamic Context Engineering
- Adapting context based on agent performance in real-time
- Learning optimal context configurations from historical data
- Personalizing context for different agent types or models

### Context Compression
- Summarization techniques for large codebases
- Semantic compression to preserve meaning while reducing size
- Hierarchical context with progressive detail

### Context Synthesis
- Combining multiple context sources intelligently
- Resolving conflicts between context sources
- Generating synthetic examples from existing code

### Context Security
- Redacting sensitive information from context
- Access control for different context elements
- Audit trails for context usage

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Intermediate  
**Estimated Time**: 1-3 hours