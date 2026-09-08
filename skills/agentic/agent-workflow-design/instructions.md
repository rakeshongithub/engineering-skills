# Agent Workflow Design - Step-by-Step Instructions

## Overview

This guide provides detailed instructions for designing multi-agent workflows that coordinate AI agents effectively.

## Prerequisites

- Completed task decomposition (from agent-task-decomposition skill)
- Understanding of available agent capabilities
- Clear workflow requirements
- Knowledge of the problem domain

## Step-by-Step Workflow

### Step 1: Analyze Task Dependencies (15-20 minutes)

**Objective**: Understand how tasks relate to each other.

**Actions**:

1. Review task decomposition output
2. Create dependency graph:
   - List all tasks
   - Draw arrows showing dependencies
   - Identify sequential chains
   - Identify parallel branches
3. Find critical path:
   - Longest sequence of dependent tasks
   - Determines minimum completion time
4. Identify synchronization points:
   - Where parallel tasks must wait for each other
   - Where results must be merged

**Output**:
```
Dependency Graph:
TASK-001 (no dependencies)
  ↓
TASK-002 (depends on TASK-001)
  ↓
TASK-003, TASK-004, TASK-005 (parallel, depend on TASK-002)
  ↓
TASK-006 (depends on TASK-003, TASK-004, TASK-005)

Critical Path: TASK-001 → TASK-002 → TASK-003 → TASK-006
Parallel Opportunities: TASK-003, TASK-004, TASK-005
Sync Point: Before TASK-006 (wait for all parallel tasks)
```

### Step 2: Select Workflow Pattern (10-15 minutes)

**Objective**: Choose the right workflow pattern for your task structure.

**Actions**:

1. Review common patterns:
   - **Sequential**: A → B → C (simple, predictable)
   - **Parallel**: A, B, C (fast, efficient)
   - **Pipeline**: A → B → C with overlap (balanced)
   - **Fan-out/Fan-in**: A → (B1, B2, B3) → C (scalable)
   - **Conditional**: if X then A else B (flexible)
2. Match pattern to task structure
3. Consider hybrid patterns if needed
4. Document pattern choice and rationale

**Decision Matrix**:
```
If tasks are mostly sequential → Sequential or Pipeline
If tasks are mostly independent → Parallel
If one task spawns many → Fan-out/Fan-in
If execution depends on conditions → Conditional
If mixed → Hybrid
```

**Output**:
```
Selected Pattern: Fan-out/Fan-in
Rationale: TASK-002 produces output that feeds 3 parallel tasks (TASK-003, 004, 005), then results merge in TASK-006
Adaptations: Add error handling for parallel tasks, implement result aggregation logic
```

### Step 3: Assign Agents to Tasks (20-30 minutes)

**Objective**: Match the right agents to the right tasks.

**Actions**:

1. List available agents and their capabilities
2. For each task, identify required capabilities
3. Match agents to tasks based on:
   - Capability match
   - Availability
   - Context size limits
   - Execution time limits
4. Balance load across agents
5. Decide on agent reuse vs. dedicated agents

**Agent Capability Matrix**:
```
Agent Type | Capabilities | Context Limit | Time Limit
-----------|--------------|---------------|------------
CodeGen    | Code generation, refactoring | 100K tokens | 5 min
Reviewer   | Code review, quality analysis | 50K tokens | 3 min
Tester     | Test generation, execution | 75K tokens | 10 min
DocWriter  | Documentation, diagrams | 50K tokens | 3 min
```

**Assignment Example**:
```
TASK-001 (Design schema): CodeGen agent
TASK-002 (Implement models): CodeGen agent (reuse)
TASK-003 (Write tests): Tester agent
TASK-004 (Code review): Reviewer agent
TASK-005 (Documentation): DocWriter agent
TASK-006 (Integration): CodeGen agent (reuse)
```

**Output**:
- Agent-to-task mapping
- Agent utilization timeline
- Load distribution analysis

### Step 4: Design Coordination Mechanisms (20-30 minutes)

**Objective**: Define how agents communicate and coordinate.

**Actions**:

1. Choose coordination approach:
   - **Orchestrator**: Central coordinator manages all agents
   - **Choreography**: Agents self-coordinate
   - **Hybrid**: Mix of both
2. Define handoff protocols:
   - What information passes between agents?
   - In what format?
   - Where is it stored?
3. Design synchronization:
   - How do agents wait for dependencies?
   - How are parallel tasks synchronized?
4. Plan state management:
   - Shared state vs. message passing
   - State storage location
   - State access patterns

**Orchestrator Example**:
```
Orchestrator:
  1. Start TASK-001 with CodeGen agent
  2. Wait for completion
  3. Validate output
  4. Start TASK-002 with CodeGen agent, pass TASK-001 output
  5. Wait for completion
  6. Start TASK-003, 004, 005 in parallel
  7. Wait for all three to complete
  8. Aggregate results
  9. Start TASK-006 with aggregated results
```

**Handoff Protocol Example**:
```
TASK-001 → TASK-002:
  Artifact: schema.sql file
  Location: /output/task-001/schema.sql
  Format: PostgreSQL SQL
  Validation: File exists, SQL is valid, contains required tables
  On failure: Retry TASK-001 or escalate
```

**Output**:
- Coordination mechanism design
- Handoff protocols for each dependency
- Synchronization strategy
- State management approach

### Step 5: Design Error Handling (20-30 minutes)

**Objective**: Plan for failures and recovery.

**Actions**:

1. Identify failure points:
   - Agent execution failure
   - Timeout
   - Invalid output
   - Dependency failure
2. For each failure point, define:
   - Detection mechanism
   - Recovery strategy
   - Retry policy
   - Escalation path
3. Design rollback procedures
4. Define failure thresholds

**Error Handling Matrix**:
```
Failure Type | Detection | Recovery | Retry | Escalate
-------------|-----------|----------|-------|----------
Agent crash | No output after timeout | Restart agent | 3x with backoff | After 3 failures
Invalid output | Validation fails | Request correction | 2x | After 2 failures
Timeout | Execution exceeds limit | Extend time or split task | 1x | After 1 failure
Dependency fail | Upstream task fails | Wait for retry | N/A | If upstream escalates
```

**Retry Policy Example**:
```
Retry Configuration:
  Max retries: 3
  Backoff: Exponential (1s, 2s, 4s)
  Retry on: Transient failures, timeouts
  No retry on: Validation failures, permanent errors
  Between retries: Log error, adjust parameters if possible
```

**Rollback Example**:
```
If TASK-006 fails:
  1. Mark TASK-006 as failed
  2. Preserve outputs from TASK-003, 004, 005
  3. Do NOT rollback completed tasks
  4. Retry TASK-006 with same inputs
  5. If retry fails, escalate to human
```

**Output**:
- Error handling strategy document
- Retry policies per failure type
- Rollback procedures
- Escalation rules

### Step 6: Design Monitoring and Observability (15-20 minutes)

**Objective**: Plan how to track workflow execution and diagnose issues.

**Actions**:

1. Define monitoring metrics:
   - Progress (tasks completed, in progress, pending)
   - Performance (execution time, throughput)
   - Quality (validation results, error rates)
   - Resources (agent utilization, memory, tokens)
2. Specify logging:
   - What to log (events, errors, state changes)
   - Log format and structure
   - Log storage and retention
3. Design alerts:
   - What conditions trigger alerts
   - Who gets notified
   - Alert channels (email, Slack, etc.)
4. Plan dashboards:
   - Real-time workflow status
   - Historical trends
   - Error analysis

**Monitoring Plan Example**:
```
Metrics to Track:
  - workflow_status (pending|running|completed|failed)
  - task_status (per task)
  - execution_time (per task and total)
  - error_count (per task and total)
  - retry_count (per task)
  - agent_utilization (per agent type)

Logging:
  - Task start: {task_id, agent, timestamp, inputs}
  - Task complete: {task_id, duration, outputs, validation}
  - Task error: {task_id, error_type, message, stack_trace}
  - Handoff: {from_task, to_task, artifact, validation}

Alerts:
  - Task failure after max retries → Notify engineer
  - Workflow timeout → Notify engineer
  - Error rate > 20% → Notify engineer
  - Critical path delay > 50% → Notify PM
```

**Output**:
- Monitoring plan with metrics
- Logging strategy
- Alert definitions
- Dashboard requirements

### Step 7: Optimize Workflow Execution (15-20 minutes)

**Objective**: Improve workflow efficiency and performance.

**Actions**:

1. Analyze critical path:
   - Can any critical path tasks be parallelized?
   - Can any be made faster?
2. Maximize parallelization:
   - Find more parallel opportunities
   - Reduce unnecessary dependencies
3. Minimize handoff overhead:
   - Reduce data transfer size
   - Optimize handoff format
   - Cache intermediate results
4. Balance resource utilization:
   - Avoid agent overload
   - Minimize idle time
5. Reduce wait times:
   - Prefetch dependencies
   - Pipeline where possible

**Optimization Checklist**:
```
- [ ] Critical path minimized
- [ ] All parallelization opportunities exploited
- [ ] Handoff overhead minimized
- [ ] Agent utilization balanced
- [ ] No unnecessary waits
- [ ] Caching used where beneficial
- [ ] Pipeline pattern used where applicable
```

**Before/After Example**:
```
Before:
  Total time: 20 hours (sequential)
  Agent utilization: 33% (1 agent busy, 2 idle)
  Critical path: 20 hours

After:
  Total time: 8 hours (parallelized)
  Agent utilization: 83% (all agents busy)
  Critical path: 8 hours
  Improvement: 60% faster
```

**Output**:
- Optimized workflow design
- Performance improvement estimates
- Resource utilization plan

### Step 8: Create Workflow Diagram (20-30 minutes)

**Objective**: Visualize the workflow clearly.

**Actions**:

1. Choose diagram type:
   - Flowchart (simple, widely understood)
   - BPMN (standard, detailed)
   - Sequence diagram (shows interactions)
   - Swimlane diagram (shows agent responsibilities)
2. Create diagram showing:
   - Agents (swimlanes or actors)
   - Tasks (boxes)
   - Dependencies (arrows)
   - Decision points (diamonds)
   - Data flow (labeled arrows)
   - Error handling (dashed lines)
3. Add legend and annotations
4. Ensure clarity and readability

**Diagram Example (Text Representation)**:
```
[Orchestrator]
      |
      v
  [TASK-001: CodeGen]
      |
      v
  [TASK-002: CodeGen]
      |
      +--------------------+--------------------+
      |                    |                    |
      v                    v                    v
[TASK-003: Tester]  [TASK-004: Reviewer]  [TASK-005: DocWriter]
      |                    |                    |
      +--------------------+--------------------+
                           |
                           v
                    [TASK-006: CodeGen]
                           |
                           v
                       [Complete]

Legend:
  [Box] = Task
  Agent type shown after colon
  Arrows = Dependencies
  Parallel branches shown with split/merge
```

**Output**:
- Workflow diagram (visual)
- Legend explaining notation
- Annotations for complex parts

### Step 9: Document Execution Plan (30-45 minutes)

**Objective**: Create comprehensive documentation for workflow execution.

**Actions**:

1. Write workflow overview
2. Document each task:
   - Objective
   - Agent assignment
   - Inputs and outputs
   - Validation criteria
3. Specify agent configurations
4. Document handoff formats
5. Include error handling procedures
6. Add monitoring and logging details
7. Provide examples

**Execution Plan Template**:
```markdown
# Workflow Execution Plan: [Name]

## Overview
[Brief description]

## Workflow Pattern
[Pattern name and rationale]

## Agent Assignments
| Task | Agent | Configuration |
|------|-------|---------------|
| ... | ... | ... |

## Task Execution Details

### TASK-001: [Name]
- **Agent**: [Agent type]
- **Inputs**: [List]
- **Outputs**: [List]
- **Validation**: [Criteria]
- **Error Handling**: [Strategy]

[Repeat for all tasks]

## Handoff Specifications
[Details for each handoff]

## Error Handling
[Retry policies, rollback procedures]

## Monitoring
[Metrics, logging, alerts]

## Success Criteria
[How to determine workflow succeeded]
```

**Output**:
- Complete execution plan document
- Configuration specifications
- Operational procedures

### Step 10: Validate Workflow Design (15-20 minutes)

**Objective**: Ensure the workflow design is correct and complete.

**Actions**:

1. Review against requirements:
   - All requirements covered?
   - All constraints respected?
2. Verify completeness:
   - All tasks included?
   - All dependencies represented?
   - All handoffs specified?
3. Check correctness:
   - No circular dependencies?
   - Agent assignments appropriate?
   - Error handling comprehensive?
4. Simulate execution:
   - Walk through workflow mentally
   - Identify potential issues
5. Get review from stakeholders

**Validation Checklist**:
```
- [ ] All tasks from decomposition included
- [ ] All dependencies correct
- [ ] No circular dependencies
- [ ] Agent assignments match capabilities
- [ ] Handoffs clearly specified
- [ ] Error handling covers all scenarios
- [ ] Monitoring provides visibility
- [ ] Workflow achievable with resources
- [ ] Performance requirements can be met
- [ ] Documentation complete
```

**Output**:
- Validated workflow design
- List of issues found and resolved
- Stakeholder approval
- Ready for implementation

## Tips for Success

### Workflow Design
- Start simple, add complexity only when needed
- Prefer parallelization over serialization when possible
- Make handoffs explicit and well-defined
- Plan for failures from the start

### Agent Coordination
- Use orchestrator for complex workflows
- Use choreography for simple, scalable workflows
- Keep coordination logic simple
- Minimize shared state

### Error Handling
- Assume everything can fail
- Define retry limits to prevent infinite loops
- Have clear escalation paths
- Test error handling paths

### Monitoring
- Monitor progress, performance, and errors
- Log enough to debug issues
- Alert on actionable conditions only
- Make monitoring real-time

## Common Pitfalls to Avoid

1. **Over-serialization**: Running tasks sequentially when they could be parallel
2. **Unclear handoffs**: Not specifying exact format and validation
3. **No error handling**: Assuming everything works perfectly
4. **Missing monitoring**: Not tracking progress and issues
5. **Circular dependencies**: Creating dependency cycles
6. **Overloading agents**: Assigning too much work to one agent
7. **Ignoring constraints**: Exceeding agent limits or resource constraints
8. **Overly complex**: Creating unnecessarily complex workflows

## Next Steps

After completing workflow design:

1. Review with team and stakeholders
2. Get approval on design
3. Proceed to agent-context-engineering
4. Use agent-instruction-design for agent instructions
5. Implement handoffs with agent-handoff-design
6. Implement workflow
7. Test with agent-evaluation
8. Monitor with agent-observability

## Additional Resources

- See examples.md for complete workflow examples
- See SKILL.md for conceptual overview
- See related skills for complementary techniques