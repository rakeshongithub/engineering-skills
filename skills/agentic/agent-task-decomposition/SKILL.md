# Agent Task Decomposition

## Purpose

Break down large, complex engineering problems into agent-sized tasks that can be executed independently by AI coding agents while maintaining coherence and achieving the overall objective.

## When to Use

- When you have a complex engineering problem that's too large for a single agent execution
- When you need to parallelize work across multiple agents
- When you want to create a clear execution plan for agentic workflows
- When you need to estimate effort and dependencies for agent-driven development
- When designing multi-agent systems that need to collaborate on a shared goal
- When you want to optimize agent execution by breaking work into optimal chunks

## When NOT to Use

- For simple, single-step tasks that can be completed by one agent in one execution
- When the problem is too vague or requirements are unclear (use requirements-analysis first)
- For tasks that require continuous human judgment and cannot be automated
- When the overhead of task decomposition exceeds the benefit
- For exploratory work where the path forward is unknown (use architecture-discovery first)

## Inputs

- **engineering-problem**: The high-level problem or feature to implement
- **requirements**: Functional and non-functional requirements
- **constraints**: Technical, resource, or timeline constraints
- **context**: Existing codebase, architecture, and dependencies
- **agent-capabilities**: What the available agents can do (tools, models, limitations)
- **success-criteria**: How to measure successful completion

## Expected Outputs

- **task-list**: Ordered list of agent-executable tasks
- **task-dependencies**: Dependencies and execution order
- **task-sizing**: Estimated complexity and time for each task
- **handoff-points**: Where one agent's output becomes another's input
- **validation-criteria**: How to verify each task is complete
- **rollback-strategy**: How to handle task failures

## Workflow

### 1. Understand the Problem Scope

**Actions:**
- Read and analyze the engineering problem
- Identify the core objective and success criteria
- Understand constraints and limitations
- Clarify any ambiguities

**Questions to answer:**
- What is the ultimate goal?
- What are the must-have vs. nice-to-have requirements?
- What are the hard constraints?
- What context is available?

**Output:**
- Clear problem statement
- Defined success criteria
- Identified constraints

### 2. Identify Major Phases

**Actions:**
- Break the problem into high-level phases
- Identify natural boundaries (discovery, design, implementation, testing, deployment)
- Consider dependencies between phases
- Estimate relative effort for each phase

**Common phases:**
- Discovery/Analysis
- Design/Planning
- Implementation
- Testing/Validation
- Documentation
- Deployment/Integration

**Output:**
- List of major phases
- Phase dependencies
- High-level effort estimates

### 3. Decompose Phases into Tasks

**For each phase:**

**Actions:**
- Break the phase into concrete, executable tasks
- Ensure each task has a clear input and output
- Make tasks independent where possible
- Size tasks appropriately for agent execution (15min - 2 hours)
- Define task boundaries clearly

**Task sizing guidelines:**
- **Small task**: 15-30 minutes, 1-3 files, clear scope
- **Medium task**: 30min-1 hour, 3-10 files, moderate complexity
- **Large task**: 1-2 hours, 10+ files, complex logic
- **Too large**: >2 hours, needs further decomposition

**Output:**
- Detailed task list for each phase
- Task size estimates
- Clear task descriptions

### 4. Define Task Dependencies

**Actions:**
- Identify which tasks depend on others
- Create a dependency graph
- Identify tasks that can run in parallel
- Find the critical path
- Optimize for parallel execution where possible

**Dependency types:**
- **Sequential**: Task B requires Task A's output
- **Parallel**: Tasks can run simultaneously
- **Conditional**: Task execution depends on previous results
- **Optional**: Task may be skipped based on conditions

**Output:**
- Dependency graph
- Execution order
- Parallelization opportunities
- Critical path

### 5. Define Handoff Points

**Actions:**
- Identify where one agent's output becomes another's input
- Define the format and structure of handoff artifacts
- Specify validation criteria for handoffs
- Plan for handoff failures

**Handoff considerations:**
- What information must be passed?
- In what format?
- How is completeness verified?
- What happens if the handoff is incomplete?

**Output:**
- Handoff specifications
- Data formats
- Validation criteria

### 6. Add Validation Criteria

**For each task:**

**Actions:**
- Define how to verify task completion
- Specify acceptance criteria
- Add quality checks
- Define failure conditions

**Validation types:**
- **Completeness**: All required outputs produced?
- **Correctness**: Outputs meet requirements?
- **Quality**: Code quality, test coverage, documentation?
- **Integration**: Works with other components?

**Output:**
- Validation criteria per task
- Quality gates
- Acceptance criteria

### 7. Plan for Failure and Recovery

**Actions:**
- Identify tasks with high failure risk
- Define rollback strategies
- Plan for partial completion
- Add retry logic where appropriate

**Failure scenarios:**
- Task fails completely
- Task produces incorrect output
- Task times out
- Dependencies are not met

**Output:**
- Failure handling strategy
- Rollback procedures
- Retry policies

### 8. Optimize Task Structure

**Actions:**
- Review task list for optimization opportunities
- Combine overly granular tasks
- Split tasks that are too large
- Reorder tasks to minimize dependencies
- Maximize parallelization

**Optimization criteria:**
- Minimize total execution time
- Maximize agent utilization
- Reduce handoff overhead
- Simplify dependency graph

**Output:**
- Optimized task list
- Improved execution plan

### 9. Document the Decomposition

**Actions:**
- Create clear task descriptions
- Document dependencies
- Specify inputs and outputs
- Add context and rationale
- Include examples where helpful

**Documentation should include:**
- Task ID and name
- Description and objective
- Inputs required
- Expected outputs
- Dependencies
- Validation criteria
- Estimated effort

**Output:**
- Complete task documentation
- Execution plan

### 10. Validate the Decomposition

**Actions:**
- Review task list for completeness
- Verify all requirements are covered
- Check for missing dependencies
- Ensure tasks are agent-executable
- Validate sizing estimates

**Validation questions:**
- Do the tasks cover all requirements?
- Are tasks sized appropriately?
- Are dependencies correct?
- Can agents execute these tasks?
- Is the plan achievable?

**Output:**
- Validated task decomposition
- Execution-ready plan

## Decision Framework

### Task Sizing

**How small should tasks be?**

- **Too small**: Excessive overhead, too many handoffs, coordination complexity
- **Too large**: Agent timeouts, difficult to debug, hard to parallelize
- **Just right**: 15min - 2 hours, clear scope, manageable complexity

**Factors to consider:**
- Agent execution limits (time, token, memory)
- Task complexity and uncertainty
- Handoff overhead
- Debugging and recovery needs

### Dependency Management

**Sequential vs. Parallel:**

- **Sequential**: Use when tasks have hard dependencies, outputs feed into next task
- **Parallel**: Use when tasks are independent, can maximize throughput
- **Hybrid**: Most real workflows have both sequential and parallel sections

**Minimize dependencies by:**
- Making tasks more self-contained
- Providing complete context upfront
- Using shared state carefully
- Designing clear interfaces

### Granularity Tradeoffs

**Fine-grained decomposition:**
- **Pros**: Better parallelization, easier debugging, clearer progress tracking
- **Cons**: More handoffs, higher overhead, more coordination

**Coarse-grained decomposition:**
- **Pros**: Fewer handoffs, less overhead, simpler coordination
- **Cons**: Harder to parallelize, longer execution times, harder to debug

**Choose based on:**
- Problem complexity
- Agent capabilities
- Timeline constraints
- Team size and coordination ability

## Quality Checklist

- [ ] All requirements are covered by tasks
- [ ] Each task has a clear, single objective
- [ ] Tasks are sized appropriately (15min - 2 hours)
- [ ] Task inputs and outputs are well-defined
- [ ] Dependencies are identified and documented
- [ ] Parallelization opportunities are exploited
- [ ] Handoff points are clearly specified
- [ ] Validation criteria are defined for each task
- [ ] Failure and recovery strategies are planned
- [ ] Tasks are executable by available agents
- [ ] Task descriptions are clear and unambiguous
- [ ] Execution order is logical and efficient
- [ ] Critical path is identified
- [ ] Resource requirements are estimated
- [ ] Success criteria can be measured

## Common Mistakes

- **Tasks too large**: Creating tasks that exceed agent execution limits or are too complex to complete in one run
- **Unclear boundaries**: Not defining clear inputs, outputs, and scope for each task
- **Missing dependencies**: Failing to identify that Task B requires Task A's output
- **Over-decomposition**: Breaking tasks so small that handoff overhead dominates
- **Ignoring agent limitations**: Creating tasks that require capabilities agents don't have
- **No validation criteria**: Not defining how to verify task completion
- **Sequential when parallel is possible**: Missing opportunities to parallelize independent tasks
- **Vague task descriptions**: Writing tasks that are ambiguous or open to interpretation
- **No failure planning**: Not considering what happens when tasks fail
- **Ignoring context**: Not providing enough context for agents to execute tasks independently

## Examples

See [examples.md](examples.md) for detailed examples including:
- Decomposing a REST API implementation
- Breaking down a database migration
- Structuring a multi-agent refactoring project

## Related Skills

- **Requires**: 
  - requirements-analysis (to understand what needs to be built)
  - architecture-discovery (for existing system context)
- **Commonly followed by**: 
  - agent-workflow-design (to orchestrate the tasks)
  - agent-context-engineering (to prepare context for each task)
  - agent-instruction-design (to create clear instructions)
- **Works with**: 
  - agent-handoff-design (for defining handoffs)
  - agent-evaluation (for validating task execution)
  - testing-strategy (for planning validation)

## Skill Composition

Typical workflow for agent-driven development:

```
requirements-analysis
        ↓
architecture-discovery (if existing system)
        ↓
agent-task-decomposition (this skill)
        ↓
agent-workflow-design
        ↓
agent-context-engineering
        ↓
agent-instruction-design
        ↓
Task execution
        ↓
agent-evaluation
```

## Evaluation Criteria

### Completeness
- Do the tasks cover all requirements?
- Are all phases represented?
- Are dependencies complete?
- Is validation defined for all tasks?

### Executability
- Can agents actually execute these tasks?
- Are tasks within agent capability limits?
- Is enough context provided?
- Are instructions clear?

### Efficiency
- Is the critical path optimized?
- Are parallelization opportunities used?
- Is task sizing optimal?
- Are handoffs minimized?

### Quality
- Are validation criteria clear?
- Is failure handling planned?
- Are tasks well-documented?
- Can progress be tracked?

### Achievability
- Is the plan realistic?
- Are estimates reasonable?
- Are resources available?
- Are risks identified?