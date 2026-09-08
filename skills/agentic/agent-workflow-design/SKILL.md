# Agent Workflow Design

## Purpose

Design and orchestrate multi-agent workflows that coordinate multiple AI agents to accomplish complex engineering tasks efficiently, reliably, and with clear handoffs and error handling.

## When to Use

- When a complex task requires multiple agents working in sequence or parallel
- When you need to coordinate different specialized agents for different subtasks
- When designing systems where agents need to hand off work to each other
- When you want to optimize agent execution through parallelization
- When you need to ensure reliability and error handling in multi-agent systems
- When creating reusable workflow patterns for common engineering tasks

## When NOT to Use

- For simple, single-agent tasks that don't require coordination
- When the task decomposition hasn't been done yet (use agent-task-decomposition first)
- For ad-hoc, one-time workflows that won't be reused
- When human-in-the-loop is required at every step (not suitable for automation)
- For exploratory work where the workflow cannot be predetermined

## Inputs

- **task-decomposition**: List of tasks with dependencies from agent-task-decomposition
- **agent-capabilities**: Available agents and their capabilities
- **workflow-requirements**: Performance, reliability, and quality requirements
- **constraints**: Resource limits, time constraints, budget
- **handoff-specifications**: How information flows between agents
- **error-handling-requirements**: How to handle failures and retries

## Expected Outputs

- **workflow-diagram**: Visual representation of the agent workflow
- **execution-plan**: Detailed plan for agent orchestration
- **agent-assignments**: Which agents execute which tasks
- **coordination-logic**: How agents coordinate and synchronize
- **error-handling-strategy**: How failures are detected and handled
- **monitoring-plan**: How to track workflow progress and health

## Workflow

### 1. Analyze Task Dependencies

**Actions:**
- Review task decomposition from agent-task-decomposition skill
- Identify sequential dependencies (A must complete before B)
- Identify parallel opportunities (A and B can run simultaneously)
- Find critical path (longest sequence of dependent tasks)
- Identify synchronization points where agents must coordinate

**Questions to answer:**
- Which tasks must run in sequence?
- Which tasks can run in parallel?
- Where do agents need to synchronize?
- What is the critical path?

**Output:**
- Dependency graph
- Critical path analysis
- Parallelization opportunities
- Synchronization points

### 2. Select Workflow Pattern

**Actions:**
- Choose appropriate workflow pattern based on task structure
- Consider common patterns: sequential, parallel, pipeline, fan-out/fan-in, conditional
- Adapt pattern to specific requirements
- Plan for hybrid patterns if needed

**Common patterns:**
- **Sequential**: Tasks run one after another (A → B → C)
- **Parallel**: Tasks run simultaneously (A, B, C all at once)
- **Pipeline**: Continuous flow through stages (A → B → C, with overlap)
- **Fan-out/Fan-in**: One task spawns multiple parallel tasks, then merge results
- **Conditional**: Different paths based on conditions (if X then A else B)
- **Iterative**: Repeat until condition met (loop)

**Output:**
- Selected workflow pattern
- Rationale for pattern choice
- Pattern adaptations needed

### 3. Assign Agents to Tasks

**Actions:**
- Match agent capabilities to task requirements
- Consider agent specialization (code generation, testing, review, etc.)
- Balance load across available agents
- Plan for agent reuse vs. dedicated agents
- Consider agent context limits and execution time

**Assignment criteria:**
- Agent has required capabilities
- Agent is available when task is ready
- Agent has appropriate context size for task
- Load is balanced across agents

**Output:**
- Agent-to-task mapping
- Agent utilization plan
- Load distribution

### 4. Design Coordination Mechanisms

**Actions:**
- Define how agents communicate and coordinate
- Specify handoff protocols (how output from Agent A reaches Agent B)
- Design synchronization points (where agents wait for each other)
- Plan for shared state management
- Define coordination events (start, complete, fail, retry)

**Coordination mechanisms:**
- **Message passing**: Agents send messages to each other
- **Shared state**: Agents read/write to common storage
- **Event-driven**: Agents react to events
- **Orchestrator**: Central coordinator manages all agents
- **Choreography**: Agents self-coordinate based on rules

**Output:**
- Coordination mechanism design
- Handoff protocols
- Synchronization strategy
- State management approach

### 5. Design Error Handling

**Actions:**
- Identify potential failure points
- Define error detection mechanisms
- Design retry strategies
- Plan for graceful degradation
- Define rollback procedures
- Specify escalation paths (when to involve humans)

**Error scenarios:**
- Agent execution failure
- Timeout
- Invalid output
- Dependency failure
- Resource exhaustion

**Error handling strategies:**
- **Retry**: Retry failed task (with backoff)
- **Fallback**: Use alternative agent or approach
- **Compensate**: Undo previous work
- **Escalate**: Alert human for intervention
- **Fail-fast**: Stop entire workflow

**Output:**
- Error handling strategy
- Retry policies
- Rollback procedures
- Escalation rules

### 6. Design Monitoring and Observability

**Actions:**
- Define what to monitor (progress, performance, errors)
- Specify monitoring metrics and events
- Design logging strategy
- Plan for real-time visibility
- Define alerts and notifications

**Monitoring aspects:**
- **Progress**: Which tasks completed, which are in progress
- **Performance**: Execution time, throughput, resource usage
- **Quality**: Output quality, validation results
- **Errors**: Failures, retries, escalations
- **Dependencies**: Waiting on dependencies, blocked tasks

**Output:**
- Monitoring plan
- Metrics and events to track
- Logging strategy
- Alert definitions

### 7. Optimize Workflow Execution

**Actions:**
- Analyze workflow for bottlenecks
- Optimize critical path
- Maximize parallelization
- Minimize handoff overhead
- Balance resource utilization
- Reduce wait times

**Optimization techniques:**
- Parallelize independent tasks
- Pipeline dependent tasks
- Cache intermediate results
- Batch similar tasks
- Prefetch dependencies
- Optimize agent assignments

**Output:**
- Optimized workflow design
- Performance improvements identified
- Resource utilization plan

### 8. Create Workflow Diagram

**Actions:**
- Create visual representation of the workflow
- Show agents, tasks, dependencies, and data flow
- Include decision points and error handling
- Make diagram clear and understandable
- Use standard notation (BPMN, flowchart, sequence diagram)

**Diagram elements:**
- Agents (swimlanes or actors)
- Tasks (boxes or activities)
- Dependencies (arrows)
- Decision points (diamonds)
- Data flow (labeled arrows)
- Error handling (dashed lines or special notation)

**Output:**
- Workflow diagram
- Legend explaining notation
- Annotations for complex parts

### 9. Document Execution Plan

**Actions:**
- Write detailed execution plan
- Specify agent configurations
- Document handoff formats
- Include error handling procedures
- Add examples and edge cases

**Execution plan contents:**
- Workflow overview
- Agent assignments and configurations
- Task execution order
- Handoff specifications
- Error handling procedures
- Monitoring and logging
- Success criteria

**Output:**
- Complete execution plan document
- Configuration specifications
- Operational procedures

### 10. Validate Workflow Design

**Actions:**
- Review workflow against requirements
- Verify all tasks are covered
- Check dependencies are correct
- Validate error handling is comprehensive
- Ensure monitoring is adequate
- Simulate workflow execution mentally or with tools

**Validation checks:**
- All tasks from decomposition included
- Dependencies correctly represented
- No circular dependencies
- Error handling covers all failure modes
- Monitoring provides adequate visibility
- Workflow is achievable with available agents

**Output:**
- Validated workflow design
- List of issues found and resolved
- Approval to proceed with implementation

## Decision Framework

### Workflow Pattern Selection

**Sequential Pattern:**
- **Use when**: Tasks have strict dependencies, must run in order
- **Pros**: Simple, predictable, easy to debug
- **Cons**: Slow, no parallelization, inefficient

**Parallel Pattern:**
- **Use when**: Tasks are independent, can run simultaneously
- **Pros**: Fast, efficient resource use, high throughput
- **Cons**: Complex coordination, harder to debug, resource intensive

**Pipeline Pattern:**
- **Use when**: Tasks have sequential dependencies but can overlap
- **Pros**: Good throughput, efficient, balanced resource use
- **Cons**: Complex to implement, requires careful synchronization

**Fan-out/Fan-in Pattern:**
- **Use when**: One task produces work for many parallel tasks, then merge results
- **Pros**: Scales well, good for data-parallel work
- **Cons**: Synchronization overhead, merge complexity

### Coordination Mechanism Selection

**Orchestrator (Central Coordinator):**
- **Use when**: Complex workflows, need central control, clear ownership
- **Pros**: Simple agent logic, clear control flow, easy monitoring
- **Cons**: Single point of failure, bottleneck, less scalable

**Choreography (Distributed Coordination):**
- **Use when**: Simple workflows, agents can self-coordinate, need scalability
- **Pros**: No single point of failure, scalable, resilient
- **Cons**: Complex agent logic, harder to monitor, emergent behavior

**Hybrid:**
- **Use when**: Need both central control and distributed coordination
- **Pros**: Flexible, can optimize for different parts of workflow
- **Cons**: More complex, harder to understand

### Error Handling Strategy

**Retry:**
- **Use when**: Transient failures, idempotent operations
- **Limit**: Max 3 retries with exponential backoff
- **Risk**: Infinite loops, wasted resources

**Fallback:**
- **Use when**: Alternative approaches available
- **Benefit**: Resilience, graceful degradation
- **Risk**: Complexity, maintaining alternatives

**Compensate:**
- **Use when**: Need to undo previous work
- **Benefit**: Consistency, clean failure
- **Risk**: Complex compensation logic

**Escalate:**
- **Use when**: Cannot recover automatically
- **Benefit**: Human judgment, prevents bad outcomes
- **Risk**: Delays, requires human availability

## Quality Checklist

- [ ] All tasks from decomposition are included in workflow
- [ ] Dependencies are correctly represented
- [ ] No circular dependencies exist
- [ ] Parallelization opportunities are exploited
- [ ] Critical path is identified and optimized
- [ ] Agent assignments match capabilities to requirements
- [ ] Handoff protocols are clearly specified
- [ ] Error handling covers all failure scenarios
- [ ] Retry policies are defined with limits
- [ ] Rollback procedures are documented
- [ ] Monitoring provides adequate visibility
- [ ] Workflow diagram is clear and accurate
- [ ] Execution plan is complete and detailed
- [ ] Workflow is achievable with available resources
- [ ] Performance requirements can be met

## Common Mistakes

- **Over-serialization**: Not exploiting parallelization opportunities, running tasks sequentially when they could run in parallel
- **Under-coordination**: Not specifying how agents coordinate, leading to race conditions and inconsistencies
- **Inadequate error handling**: Not planning for failures, assuming everything will work perfectly
- **Unclear handoffs**: Not specifying exactly what information passes between agents and in what format
- **No monitoring**: Not planning how to track workflow progress and diagnose issues
- **Ignoring resource limits**: Designing workflows that exceed available agent capacity or execution time limits
- **Circular dependencies**: Creating dependency cycles that prevent workflow from progressing
- **Missing synchronization**: Not coordinating agents when needed, leading to race conditions
- **Overly complex workflows**: Creating unnecessarily complex workflows when simpler patterns would work
- **No rollback plan**: Not planning how to recover from failures or undo partial work

## Examples

See [examples.md](examples.md) for detailed examples including:
- Sequential workflow for code review
- Parallel workflow for multi-module testing
- Pipeline workflow for continuous refactoring
- Fan-out/fan-in workflow for distributed analysis

## Related Skills

- **Requires**: 
  - agent-task-decomposition (to get task list and dependencies)
- **Commonly followed by**: 
  - agent-context-engineering (to prepare context for each agent)
  - agent-instruction-design (to create agent instructions)
  - agent-handoff-design (to implement handoff mechanisms)
- **Works with**: 
  - agent-evaluation (to validate workflow execution)
  - agent-observability (to monitor workflow)
  - agent-guardrails (to ensure safe execution)

## Skill Composition

Typical workflow for multi-agent system design:

```
requirements-analysis
        ↓
agent-task-decomposition
        ↓
agent-workflow-design (this skill)
        ↓
agent-context-engineering
        ↓
agent-instruction-design
        ↓
agent-handoff-design
        ↓
Workflow implementation
        ↓
agent-evaluation
        ↓
agent-observability
```

## Evaluation Criteria

### Completeness
- All tasks covered?
- All dependencies represented?
- All failure modes handled?
- All handoffs specified?

### Correctness
- Dependencies accurate?
- No circular dependencies?
- Agent assignments appropriate?
- Coordination mechanisms sound?

### Efficiency
- Parallelization maximized?
- Critical path optimized?
- Resource utilization balanced?
- Handoff overhead minimized?

### Reliability
- Error handling comprehensive?
- Retry policies appropriate?
- Rollback procedures defined?
- Escalation paths clear?

### Observability
- Progress trackable?
- Errors detectable?
- Performance measurable?
- Debugging feasible?