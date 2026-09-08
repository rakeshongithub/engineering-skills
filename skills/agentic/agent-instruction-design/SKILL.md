# Agent Instruction Design

**Purpose**: Design clear, executable instructions that enable AI agents to perform complex tasks autonomously with high success rates and minimal ambiguity.

---

## When to Use

Use this skill when:
- Creating task specifications for AI agent execution
- Agent tasks are failing due to unclear or ambiguous instructions
- Designing autonomous workflows that require minimal human intervention
- Building multi-agent systems where agents need precise coordination
- Optimizing agent performance through better instruction clarity
- Documenting repeatable agent workflows
- Creating templates for common agent tasks
- Debugging agent failures caused by instruction misinterpretation

## When NOT to Use

Do not use this skill when:
- Writing documentation for human developers (use standard documentation practices)
- Instructions are already clear and agents are succeeding consistently
- The task is too simple to warrant formal instruction design
- You're working with conversational AI that doesn't follow structured instructions
- The task requires human judgment that can't be codified

---

## Inputs

### Required
- **Task Definition**: What needs to be accomplished
- **Context Package**: All information the agent needs (from agent-context-engineering)
- **Success Criteria**: How to measure successful completion
- **Agent Capabilities**: What tools and knowledge the agent has

### Optional
- **Constraints**: Limitations, requirements, or rules to follow
- **Error Scenarios**: Known failure modes and how to handle them
- **Examples**: Reference implementations or expected outputs
- **Fallback Strategies**: What to do when primary approach fails

---

## Expected Outputs

### Primary Deliverables
1. **Instruction Document**: Step-by-step, executable instructions
2. **Decision Trees**: Logic for handling conditional paths
3. **Validation Criteria**: How agent verifies each step succeeded
4. **Error Handling Guide**: What to do when things go wrong

### Supporting Artifacts
- **Instruction Templates**: Reusable patterns for common tasks
- **Instruction Quality Metrics**: Clarity, completeness, executability scores
- **Test Cases**: Scenarios to validate instruction effectiveness
- **Instruction Versioning**: Tracking improvements over time

---

## Workflow

### Step 1: Define Clear Objectives
**Objective**: Establish exactly what the agent needs to accomplish.

**Actions**:
1. State the primary goal in one clear sentence
2. List all sub-goals or intermediate outcomes
3. Define success criteria (observable, measurable outcomes)
4. Identify what "done" looks like
5. Clarify what is NOT part of the task (scope boundaries)

**Output**: Clear objective statement with success criteria

**Quality Check**: Can someone unfamiliar with the task understand what success looks like?

---

### Step 2: Break Down into Atomic Steps
**Objective**: Decompose the task into smallest executable units.

**Actions**:
1. List every action the agent must take, in order
2. Ensure each step is atomic (single, clear action)
3. Make steps sequential where order matters
4. Identify steps that can be parallelized
5. Ensure each step has a clear input and output

**Output**: Numbered list of atomic steps

**Quality Check**: Can each step be executed independently without ambiguity?

---

### Step 3: Add Explicit Preconditions
**Objective**: Specify what must be true before each step.

**Actions**:
1. For each step, identify required preconditions
2. Specify required inputs (data, files, state)
3. Define environmental requirements (tools, permissions, resources)
4. Document dependencies on previous steps
5. Add validation checks for preconditions

**Output**: Precondition checklist for each step

**Quality Check**: Can the agent verify preconditions before attempting the step?

---

### Step 4: Specify Expected Outcomes
**Objective**: Define what success looks like for each step.

**Actions**:
1. For each step, describe the expected outcome
2. Make outcomes observable and verifiable
3. Provide examples of successful outcomes
4. Define how to validate the outcome
5. Specify what to do if outcome doesn't match expectations

**Output**: Expected outcome specification for each step

**Quality Check**: Can the agent programmatically verify the outcome?

---

### Step 5: Design Decision Points
**Objective**: Handle conditional logic and branching paths.

**Actions**:
1. Identify points where agent must make decisions
2. Define decision criteria (if X then Y, else Z)
3. Provide clear logic for each decision
4. Specify all possible branches
5. Ensure each branch eventually converges or terminates

**Output**: Decision tree or flowchart

**Quality Check**: Are all decision paths covered? No ambiguous conditions?

---

### Step 6: Add Error Handling Instructions
**Objective**: Define what to do when things go wrong.

**Actions**:
1. Identify potential failure modes for each step
2. Define how to detect each failure
3. Specify recovery actions for each failure type
4. Define when to retry vs. when to fail fast
5. Specify what information to log on failure

**Output**: Error handling guide with recovery strategies

**Quality Check**: Does every failure mode have a defined response?

---

### Step 7: Specify Tool Usage
**Objective**: Clearly define how and when to use available tools.

**Actions**:
1. List all tools the agent will use
2. For each tool, specify when to use it
3. Define exact parameters and inputs
4. Specify how to interpret tool outputs
5. Provide examples of correct tool usage

**Output**: Tool usage guide with examples

**Quality Check**: Can the agent use each tool correctly based on instructions?

---

### Step 8: Add Validation Checkpoints
**Objective**: Ensure agent can verify progress at key points.

**Actions**:
1. Identify critical points where validation is needed
2. Define validation criteria for each checkpoint
3. Specify validation methods (tests, checks, assertions)
4. Define what to do if validation fails
5. Add intermediate success criteria

**Output**: Validation checkpoint plan

**Quality Check**: Can the agent detect and recover from errors early?

---

### Step 9: Optimize for Clarity and Conciseness
**Objective**: Make instructions easy to understand and follow.

**Actions**:
1. Use simple, direct language (avoid jargon)
2. Use active voice ("Read the file" not "The file should be read")
3. Use consistent terminology throughout
4. Remove redundancy and unnecessary details
5. Add examples for complex steps

**Output**: Polished, clear instruction document

**Quality Check**: Can the instructions be understood on first reading?

---

### Step 10: Test and Iterate
**Objective**: Validate instructions with real agent execution.

**Actions**:
1. Execute instructions with an agent
2. Observe where agent struggles or fails
3. Identify ambiguities or missing information
4. Refine instructions based on observations
5. Repeat until success rate is acceptable

**Output**: Validated, tested instructions with success metrics

**Quality Check**: Does the agent succeed consistently (>90%)?

---

## Decision Framework

### Instruction Granularity Decision Tree

```
Is the agent experienced with this type of task?
├─ Yes → Higher-level instructions ("Implement authentication")
└─ No → Detailed step-by-step ("1. Create auth route, 2. Add validation...")

Is the task complex or multi-step?
├─ Complex → Break into smaller sub-tasks with checkpoints
└─ Simple → Single instruction with clear outcome

Are there multiple valid approaches?
├─ Yes → Provide decision criteria or recommend one approach
└─ No → Specify the single correct approach

Are there critical constraints?
├─ Yes → Highlight constraints explicitly (warnings, must/must-not)
└─ No → Standard best practices apply

Is error handling critical?
├─ Yes → Detailed error handling for each step
└─ No → General error handling guidance
```

### Instruction Format Selection

| Task Type | Best Format | Example |
|-----------|-------------|----------|
| Sequential workflow | Numbered steps | "1. Read file, 2. Parse data, 3. Write output" |
| Conditional logic | Decision tree or flowchart | "If X exists, do A, else do B" |
| Parallel tasks | Bulleted list with dependencies | "• Task A (no deps), • Task B (requires A)" |
| Complex algorithm | Pseudocode or code example | "for each item: validate, transform, save" |
| Configuration | Declarative specification | "Set timeout=30s, retries=3" |
| Validation | Checklist | "[ ] File exists, [ ] Format valid, [ ] Size < 10MB" |

---

## Quality Checklist

### Clarity
- [ ] Objective is stated clearly in one sentence
- [ ] Each step uses simple, direct language
- [ ] No ambiguous terms or jargon
- [ ] Active voice used throughout
- [ ] Consistent terminology

### Completeness
- [ ] All necessary steps are included
- [ ] Preconditions specified for each step
- [ ] Expected outcomes defined for each step
- [ ] All decision points have clear logic
- [ ] Error handling covers all failure modes
- [ ] Tool usage is fully specified

### Executability
- [ ] Each step is atomic and actionable
- [ ] Steps are in correct order
- [ ] Dependencies between steps are clear
- [ ] Agent has all needed information
- [ ] Validation criteria are verifiable
- [ ] Success can be measured objectively

### Robustness
- [ ] Error handling for each step
- [ ] Fallback strategies defined
- [ ] Validation checkpoints at critical points
- [ ] Recovery procedures specified
- [ ] Edge cases addressed

---

## Common Mistakes

### 1. Vague or Ambiguous Language
**Mistake**: "Improve the code" or "Make it better"

**Why It Happens**: Assuming agent understands implicit goals.

**How to Avoid**:
- Use specific, measurable criteria ("Reduce function complexity from 15 to <10")
- Define "better" explicitly ("Improve performance by 20%")
- Provide examples of desired outcome

**Recovery**: When agent produces unexpected results, identify vague instructions and make them specific.

---

### 2. Missing Preconditions
**Mistake**: "Read the config file" (without specifying which file or where it is)

**Why It Happens**: Assuming agent has context that wasn't provided.

**How to Avoid**:
- Specify exact file paths, names, locations
- Define required state before each step
- Validate preconditions explicitly

**Recovery**: When agent fails, check if preconditions were specified; add them.

---

### 3. Unclear Success Criteria
**Mistake**: "Complete the task" (without defining what complete means)

**Why It Happens**: Success seems obvious to humans but not to agents.

**How to Avoid**:
- Define observable, measurable outcomes
- Provide examples of successful completion
- Specify validation methods

**Recovery**: Add explicit success criteria and validation steps.

---

### 4. No Error Handling
**Mistake**: Only specifying happy path, ignoring failures.

**Why It Happens**: Optimism bias; not considering what can go wrong.

**How to Avoid**:
- Identify failure modes for each step
- Define detection and recovery for each
- Test error paths explicitly

**Recovery**: When agent encounters errors, add error handling instructions.

---

### 5. Overly Complex Instructions
**Mistake**: Trying to cover everything in one massive instruction set.

**Why It Happens**: Fear of missing something; lack of decomposition.

**How to Avoid**:
- Break complex tasks into smaller sub-tasks
- Use hierarchical instructions (high-level + detailed)
- Provide instructions just-in-time, not all upfront

**Recovery**: Simplify by breaking into smaller, focused instruction sets.

---

### 6. Assuming Tool Knowledge
**Mistake**: "Use the API" (without specifying which API, how to call it, what parameters)

**Why It Happens**: Forgetting agent may not know tool details.

**How to Avoid**:
- Specify exact tool names and parameters
- Provide examples of correct tool usage
- Define how to interpret tool outputs

**Recovery**: Add detailed tool usage instructions with examples.

---

### 7. Missing Decision Logic
**Mistake**: "Handle edge cases appropriately" (without defining what's appropriate)

**Why It Happens**: Assuming agent can infer correct decisions.

**How to Avoid**:
- Define explicit decision criteria
- Cover all branches (if-then-else)
- Provide examples of each case

**Recovery**: Add decision trees or flowcharts for conditional logic.

---

### 8. No Validation Checkpoints
**Mistake**: Only checking success at the end, not during execution.

**Why It Happens**: Not considering intermediate validation.

**How to Avoid**:
- Add checkpoints after critical steps
- Define validation criteria for each checkpoint
- Specify what to do if validation fails

**Recovery**: Add intermediate validation steps to catch errors early.

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **API Endpoint Implementation**: Step-by-step instructions for building a REST endpoint
2. **Code Refactoring**: Instructions for safe, behavior-preserving refactoring
3. **Bug Investigation**: Systematic instructions for debugging and fixing issues
4. **Test Suite Creation**: Instructions for comprehensive test coverage

---

## Related Skills

### Prerequisites
- **requirements-analysis**: Understanding what needs to be done
- **agent-task-decomposition**: Breaking down work into agent-sized tasks
- **agent-context-engineering**: Providing necessary context

### Commonly Followed By
- **agent-tool-selection**: Choosing tools to execute instructions
- **agent-guardrails**: Adding safety constraints to instructions
- **agent-evaluation**: Measuring instruction effectiveness

### Works Well With
- **agent-workflow-design**: Coordinating instructions across agents
- **agent-handoff-design**: Passing instructions between agents
- **agent-observability**: Monitoring instruction execution

### Alternatives
- **Conversational Prompting**: For interactive, exploratory tasks (less structured)
- **Example-Based Learning**: For tasks where examples are clearer than instructions

---

## Skill Composition

### Pattern: Complete Agent Task Specification
```
requirements-analysis
    ↓
agent-task-decomposition
    ↓
agent-context-engineering
    ↓
agent-instruction-design ← YOU ARE HERE
    ↓
agent-tool-selection
    ↓
Execution
```

### Pattern: Instruction-Driven Workflow
```
agent-instruction-design (task 1)
    ↓
agent-instruction-design (task 2)
    ↓
agent-handoff-design (pass results)
    ↓
agent-instruction-design (task 3)
    ↓
agent-evaluation (measure success)
```

---

## Evaluation Criteria

### Instruction Quality (Primary Metric)
- **Clarity Score**: Percentage of instructions understood correctly on first reading
- **Completeness Score**: Percentage of task requirements covered by instructions
- **Executability Score**: Percentage of steps that can be executed without clarification

### Agent Performance Metrics
- **Success Rate**: Percentage of tasks completed successfully following instructions
- **First-Time Success**: Percentage of tasks completed correctly on first attempt
- **Error Rate**: Percentage of executions resulting in errors

### Efficiency Metrics
- **Instruction Length**: Number of steps (fewer is better for simple tasks)
- **Execution Time**: Time to complete task following instructions
- **Clarification Requests**: Number of times agent needs additional information

### Robustness Metrics
- **Error Recovery Rate**: Percentage of errors successfully recovered
- **Edge Case Coverage**: Percentage of edge cases handled correctly
- **Validation Effectiveness**: Percentage of errors caught by validation checkpoints

### Target Benchmarks
- Success rate: >90% for well-designed instructions
- First-time success: >80%
- Clarity score: >95% (minimal ambiguity)
- Completeness score: >95%
- Error recovery rate: >70%

---

## Advanced Topics

### Adaptive Instructions
- Adjusting instruction detail based on agent performance
- Learning optimal instruction patterns from execution history
- Personalizing instructions for different agent types

### Instruction Composition
- Building complex instructions from reusable components
- Creating instruction libraries for common patterns
- Parameterized instructions for similar tasks

### Natural Language vs. Structured Instructions
- When to use narrative instructions vs. formal specifications
- Hybrid approaches combining both
- Translating between formats

### Instruction Versioning and Evolution
- Tracking instruction improvements over time
- A/B testing different instruction approaches
- Deprecating outdated instruction patterns

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Intermediate  
**Estimated Time**: 1-3 hours