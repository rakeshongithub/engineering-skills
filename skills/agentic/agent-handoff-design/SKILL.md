# Agent Handoff Design

**Purpose**: Design effective handoffs between agents in multi-agent workflows to ensure seamless information transfer, maintain context continuity, and maximize overall system success.

---

## When to Use

Use this skill when:
- Building multi-agent workflows where agents collaborate on tasks
- One agent's output becomes another agent's input
- Context must be preserved across agent transitions
- Coordinating specialized agents with different capabilities
- Optimizing multi-agent system performance
- Debugging handoff failures or information loss
- Designing fault-tolerant multi-agent systems
- Creating reusable agent collaboration patterns

## When NOT to Use

Do not use this skill when:
- Working with a single agent (no handoffs needed)
- Agents operate completely independently (no coordination)
- The workflow is simple enough for one agent to handle
- Handoff patterns are already well-defined and working

---

## Inputs

### Required
- **Workflow Design**: Multi-agent workflow structure
- **Agent Capabilities**: What each agent can and cannot do
- **Task Dependencies**: Which tasks depend on which outputs
- **Context Requirements**: What information each agent needs

### Optional
- **Performance Requirements**: Latency, throughput constraints
- **Error Scenarios**: Known failure modes in handoffs
- **Historical Data**: Past handoff success rates
- **Rollback Requirements**: How to undo failed handoffs

---

## Expected Outputs

### Primary Deliverables
1. **Handoff Specification**: Detailed handoff protocol for each transition
2. **Context Transfer Schema**: What information passes between agents
3. **Validation Criteria**: How to verify successful handoffs
4. **Error Handling Protocol**: What to do when handoffs fail

### Supporting Artifacts
- **Handoff Diagrams**: Visual representation of agent transitions
- **Context Mapping**: What context each agent needs from predecessors
- **Handoff Templates**: Reusable patterns for common transitions
- **Performance Metrics**: Handoff latency, success rates

---

## Workflow

### Step 1: Map Agent Workflow
**Objective**: Understand the complete multi-agent workflow.

**Actions**:
1. Identify all agents in the workflow
2. Map the sequence of agent executions
3. Identify handoff points (where one agent passes to another)
4. Document agent responsibilities and outputs
5. Identify parallel vs. sequential handoffs

**Output**: Workflow diagram with handoff points

**Quality Check**: Are all agent transitions clearly identified?

---

### Step 2: Analyze Context Dependencies
**Objective**: Determine what information each agent needs from predecessors.

**Actions**:
1. For each agent, list required inputs
2. Trace inputs back to producing agents
3. Identify context that must persist across handoffs
4. Document implicit dependencies (assumptions, state)
5. Identify context that can be discarded

**Output**: Context dependency map

**Quality Check**: Does each agent have all needed context?

---

### Step 3: Design Context Transfer Schema
**Objective**: Define exactly what information passes at each handoff.

**Actions**:
1. For each handoff, specify transferred data:
   - Task results (outputs from previous agent)
   - Metadata (timestamps, agent IDs, versions)
   - Context (relevant background information)
   - State (what's been done, what remains)
2. Define data format (JSON, structured objects, etc.)
3. Specify required vs. optional fields
4. Add validation rules for transferred data

**Output**: Context transfer schema for each handoff

**Quality Check**: Is the schema complete, unambiguous, and validatable?

---

### Step 4: Define Handoff Protocols
**Objective**: Specify how handoffs occur.

**Actions**:
1. Choose handoff mechanism:
   - Synchronous (agent waits for next agent)
   - Asynchronous (agent completes, next agent starts later)
   - Event-driven (handoff triggered by events)
2. Define handoff trigger conditions
3. Specify handoff validation (verify transfer succeeded)
4. Define timeout and retry logic
5. Document handoff sequence and timing

**Output**: Handoff protocol specification

**Quality Check**: Is the protocol clear, implementable, and robust?

---

### Step 5: Implement Context Continuity
**Objective**: Ensure no information loss across handoffs.

**Actions**:
1. Identify critical context that must not be lost
2. Design context accumulation strategy:
   - Append (add to growing context)
   - Replace (new context replaces old)
   - Merge (combine old and new)
3. Implement context versioning
4. Add context validation at each handoff
5. Design context compression for large workflows

**Output**: Context continuity strategy

**Quality Check**: Is all critical context preserved?

---

### Step 6: Design Error Handling
**Objective**: Handle handoff failures gracefully.

**Actions**:
1. Identify potential handoff failures:
   - Agent unavailable
   - Invalid context format
   - Missing required data
   - Timeout
2. Define detection mechanisms for each failure
3. Specify recovery strategies:
   - Retry handoff
   - Use fallback agent
   - Rollback to previous state
   - Escalate to human
4. Implement error logging and notification

**Output**: Error handling protocol

**Quality Check**: Are all failure modes covered?

---

### Step 7: Optimize Handoff Performance
**Objective**: Minimize handoff latency and overhead.

**Actions**:
1. Measure baseline handoff latency
2. Identify performance bottlenecks:
   - Large context transfers
   - Synchronous waits
   - Redundant data
3. Optimize context transfer:
   - Compress large data
   - Transfer only necessary information
   - Use references instead of full data
4. Optimize handoff mechanism:
   - Async where possible
   - Batch handoffs
   - Parallel handoffs

**Output**: Optimized handoff design

**Quality Check**: Is handoff latency acceptable?

---

### Step 8: Implement Handoff Validation
**Objective**: Verify handoffs succeed and context is correct.

**Actions**:
1. Define validation criteria for each handoff:
   - Required fields present
   - Data types correct
   - Values within expected ranges
   - Context consistent with previous state
2. Implement validation at handoff boundaries
3. Define what to do on validation failure
4. Add validation logging and metrics

**Output**: Handoff validation specification

**Quality Check**: Can validation detect all handoff issues?

---

### Step 9: Test Handoff Scenarios
**Objective**: Validate handoff design with real workflows.

**Actions**:
1. Test happy path (all handoffs succeed)
2. Test error scenarios:
   - Agent failure mid-workflow
   - Invalid context transfer
   - Timeout
3. Test edge cases:
   - Large context
   - Many parallel handoffs
   - Long workflow chains
4. Measure handoff performance
5. Identify and fix issues

**Output**: Handoff test results and improvements

**Quality Check**: Do handoffs work reliably in all scenarios?

---

### Step 10: Document and Monitor
**Objective**: Create reusable handoff patterns and track performance.

**Actions**:
1. Document handoff design and rationale
2. Create handoff templates for common patterns
3. Implement monitoring:
   - Handoff success rate
   - Handoff latency
   - Context transfer size
   - Error frequency
4. Set up alerts for handoff failures
5. Plan regular reviews and optimizations

**Output**: Handoff documentation and monitoring plan

**Quality Check**: Is handoff design documented and monitorable?

---

## Decision Framework

### Handoff Mechanism Selection

```
Are agents tightly coupled (one depends immediately on other's output)?
├─ Yes → Synchronous handoff
└─ No → Can handoff be delayed?
   ├─ Yes → Asynchronous handoff (queue-based)
   └─ No → Event-driven handoff

Is context large (>1MB)?
├─ Yes → Use references/pointers, not full transfer
└─ No → Direct transfer OK

Are there multiple receiving agents?
├─ Yes → Broadcast or fan-out pattern
└─ No → Point-to-point handoff

Is handoff critical (failure breaks workflow)?
├─ Yes → Add retry, validation, and rollback
└─ No → Best-effort handoff OK
```

### Context Transfer Strategy

| Scenario | Strategy | Rationale |
|----------|----------|------------|
| Short workflow (2-3 agents) | Full context transfer | Simple, no accumulation issues |
| Long workflow (5+ agents) | Incremental context | Prevents context bloat |
| Parallel agents | Shared context store | Avoids duplication |
| Independent agents | Minimal context | Only essential information |
| Stateful workflow | Context accumulation | Preserve full history |
| Stateless workflow | Context replacement | Only current state matters |

---

## Quality Checklist

### Completeness
- [ ] All handoff points identified
- [ ] Context requirements documented for each agent
- [ ] Transfer schema defined for each handoff
- [ ] Error handling specified
- [ ] Validation criteria defined

### Correctness
- [ ] No information loss across handoffs
- [ ] Context format matches agent expectations
- [ ] All dependencies satisfied
- [ ] Validation catches errors

### Performance
- [ ] Handoff latency acceptable
- [ ] Context transfer size optimized
- [ ] No unnecessary synchronous waits
- [ ] Parallel handoffs where possible

### Robustness
- [ ] Error handling for all failure modes
- [ ] Retry and rollback mechanisms
- [ ] Monitoring and alerting
- [ ] Graceful degradation

---

## Common Mistakes

### 1. Information Loss
**Mistake**: Not transferring all necessary context.

**Why It Happens**: Incomplete dependency analysis.

**How to Avoid**: Thorough context dependency mapping; validate at each handoff.

**Recovery**: Add missing context fields; implement validation.

---

### 2. Context Bloat
**Mistake**: Transferring too much unnecessary context.

**Why It Happens**: "Better safe than sorry" mentality.

**How to Avoid**: Transfer only what's needed; use references for large data.

**Recovery**: Audit context usage; remove unused fields.

---

### 3. Tight Coupling
**Mistake**: Agents too dependent on specific context format.

**Why It Happens**: Not designing for flexibility.

**How to Avoid**: Use schemas with versioning; validate but don't over-specify.

**Recovery**: Add compatibility layers; version context schemas.

---

### 4. No Error Handling
**Mistake**: Assuming handoffs always succeed.

**Why It Happens**: Optimism bias; not testing failure scenarios.

**How to Avoid**: Design error handling upfront; test failure modes.

**Recovery**: Add retry, fallback, and rollback mechanisms.

---

### 5. Synchronous Bottlenecks
**Mistake**: Using synchronous handoffs when async would work.

**Why It Happens**: Simpler to implement synchronously.

**How to Avoid**: Analyze dependencies; use async where possible.

**Recovery**: Refactor to async handoffs; use queues or events.

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **Code Review Workflow**: Handoffs between analysis, review, and fix agents
2. **CI/CD Pipeline**: Handoffs between build, test, and deploy agents
3. **Bug Triage System**: Handoffs between detection, classification, and assignment agents
4. **Documentation Generation**: Handoffs between extraction, generation, and validation agents

---

## Related Skills

### Prerequisites
- **agent-task-decomposition**: Breaking work into agent-sized tasks
- **agent-workflow-design**: Designing multi-agent workflows
- **agent-context-engineering**: Engineering context for agents

### Commonly Followed By
- **agent-guardrails**: Adding safety to handoffs
- **agent-observability**: Monitoring handoff performance
- **agent-evaluation**: Measuring handoff effectiveness

### Works Well With
- **agent-instruction-design**: Clear instructions for handoff handling
- **agent-tool-selection**: Tools for handoff implementation

---

## Skill Composition

### Pattern: Multi-Agent Workflow Design
```
agent-task-decomposition
    ↓
agent-workflow-design
    ↓
agent-context-engineering
    ↓
agent-handoff-design ← YOU ARE HERE
    ↓
agent-observability
```

---

## Evaluation Criteria

### Handoff Quality
- **Success Rate**: Percentage of handoffs that complete successfully (target: >99%)
- **Context Completeness**: Percentage of required context transferred (target: 100%)
- **Validation Effectiveness**: Percentage of errors caught by validation (target: >95%)

### Performance
- **Handoff Latency**: Time for handoff to complete (target: <1s for most handoffs)
- **Context Transfer Size**: Bytes transferred per handoff (minimize)
- **Throughput**: Handoffs per second (maximize)

### Reliability
- **Error Recovery Rate**: Percentage of handoff errors successfully recovered (target: >90%)
- **Mean Time to Recovery**: Average time to recover from handoff failure (minimize)

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Advanced  
**Estimated Time**: 2-3 hours