# Agent Tool Selection

**Purpose**: Select and configure the optimal set of tools for AI agents to execute tasks efficiently, safely, and successfully.

---

## When to Use

Use this skill when:
- Designing agent workflows that require specific capabilities
- Agent tasks are failing due to missing or inappropriate tools
- Optimizing agent performance through better tool choices
- Building multi-agent systems with specialized tool sets
- Evaluating trade-offs between different tool options
- Configuring tool access and permissions for agents
- Debugging agent failures related to tool usage
- Creating reusable agent templates with standard tool sets

## When NOT to Use

Do not use this skill when:
- Tool requirements are already well-defined and working
- The task can be completed without tools (pure reasoning)
- You're working with a fixed, non-configurable agent
- Tool selection is predetermined by platform constraints

---

## Inputs

### Required
- **Task Requirements**: What the agent needs to accomplish
- **Available Tools**: Complete catalog of tools the agent can access
- **Agent Capabilities**: Agent's ability to use different tool types
- **Constraints**: Performance, security, cost limitations

### Optional
- **Historical Performance Data**: Past success rates with different tools
- **Tool Dependencies**: Which tools require which other tools
- **Context Package**: Information about the codebase/environment
- **Fallback Options**: Alternative tools for redundancy

---

## Expected Outputs

### Primary Deliverables
1. **Tool Selection Matrix**: Recommended tools for each task requirement
2. **Tool Configuration**: Parameters, permissions, and settings
3. **Tool Usage Guide**: How and when to use each selected tool
4. **Tool Validation Plan**: How to verify tool effectiveness

### Supporting Artifacts
- **Tool Dependency Graph**: Relationships between tools
- **Tool Performance Benchmarks**: Expected performance characteristics
- **Tool Risk Assessment**: Security and reliability considerations
- **Tool Alternatives**: Backup options if primary tools fail

---

## Workflow

### Step 1: Analyze Task Requirements
**Objective**: Understand what capabilities the agent needs.

**Actions**:
1. Break down task into specific operations
2. Identify required capabilities (read files, execute code, search, etc.)
3. Determine performance requirements (speed, accuracy, reliability)
4. Identify security and safety requirements
5. Consider scalability and resource constraints

**Output**: Capability requirements matrix

**Quality Check**: Are all task operations mapped to required capabilities?

---

### Step 2: Inventory Available Tools
**Objective**: Catalog all tools the agent can potentially use.

**Actions**:
1. List all available tools with their capabilities
2. Document tool inputs, outputs, and side effects
3. Identify tool limitations and constraints
4. Note tool dependencies and prerequisites
5. Assess tool reliability and maturity

**Output**: Comprehensive tool catalog

**Quality Check**: Is the tool catalog complete and up-to-date?

---

### Step 3: Map Capabilities to Tools
**Objective**: Match task requirements to available tools.

**Actions**:
1. For each required capability, identify candidate tools
2. Evaluate how well each tool satisfies the requirement
3. Identify gaps where no tool exists
4. Note overlaps where multiple tools provide same capability
5. Consider tool combinations for complex requirements

**Output**: Capability-to-tool mapping

**Quality Check**: Does every requirement have at least one tool option?

---

### Step 4: Evaluate Tool Trade-offs
**Objective**: Choose the best tool for each capability.

**Actions**:
1. Compare tools on performance (speed, accuracy, reliability)
2. Assess ease of use and learning curve
3. Evaluate security and safety implications
4. Consider cost and resource usage
5. Check compatibility with other selected tools

**Output**: Tool evaluation matrix with scores

**Quality Check**: Are trade-offs clearly documented and justified?

---

### Step 5: Select Primary and Fallback Tools
**Objective**: Choose optimal tools with backup options.

**Actions**:
1. Select primary tool for each capability
2. Identify fallback tools for critical capabilities
3. Document selection rationale
4. Verify no conflicts between selected tools
5. Ensure tool set is minimal but complete

**Output**: Final tool selection with fallbacks

**Quality Check**: Is the tool set necessary and sufficient?

---

### Step 6: Configure Tool Parameters
**Objective**: Set up tools for optimal performance.

**Actions**:
1. Define tool-specific parameters and settings
2. Configure access permissions and security
3. Set timeouts, retries, and error handling
4. Optimize for performance and resource usage
5. Document configuration rationale

**Output**: Tool configuration specifications

**Quality Check**: Are configurations optimized for the task?

---

### Step 7: Design Tool Usage Patterns
**Objective**: Define how and when to use each tool.

**Actions**:
1. Specify conditions for using each tool
2. Define tool invocation patterns and sequences
3. Document expected inputs and outputs
4. Specify error handling for tool failures
5. Create usage examples for each tool

**Output**: Tool usage guide

**Quality Check**: Can the agent use tools correctly based on the guide?

---

### Step 8: Implement Tool Safety Guardrails
**Objective**: Prevent misuse and ensure safe tool usage.

**Actions**:
1. Identify potential risks for each tool
2. Define usage constraints and limits
3. Implement validation for tool inputs
4. Set up monitoring and logging
5. Create rollback mechanisms for destructive operations

**Output**: Tool safety specifications

**Quality Check**: Are all tool risks mitigated?

---

### Step 9: Validate Tool Selection
**Objective**: Verify tools work as expected for the task.

**Actions**:
1. Test each tool with representative inputs
2. Verify tool outputs match expectations
3. Test tool combinations and interactions
4. Validate error handling and fallbacks
5. Measure performance against requirements

**Output**: Tool validation report

**Quality Check**: Do all tools pass validation tests?

---

### Step 10: Document and Iterate
**Objective**: Create reusable tool selection documentation.

**Actions**:
1. Document final tool selection and rationale
2. Create tool usage examples and patterns
3. Track tool performance in production
4. Identify opportunities for optimization
5. Update tool selection based on learnings

**Output**: Tool selection documentation and improvement plan

**Quality Check**: Is documentation complete and actionable?

---

## Decision Framework

### Tool Selection Decision Tree

```
Does a tool exist for this capability?
├─ No → Can the task be decomposed to use existing tools?
│  ├─ Yes → Decompose and select tools for sub-tasks
│  └─ No → Flag as gap, consider workarounds or new tool development
└─ Yes → How many tools provide this capability?
   ├─ One → Validate it meets requirements, select it
   └─ Multiple → Evaluate trade-offs
      ├─ Performance critical? → Choose fastest/most accurate
      ├─ Safety critical? → Choose most reliable/safest
      ├─ Cost critical? → Choose most efficient
      └─ General purpose? → Choose most versatile/well-supported
```

### Tool Evaluation Criteria

| Criterion | Weight | Evaluation Questions |
|-----------|--------|----------------------|
| **Functionality** | High | Does it fully satisfy the requirement? |
| **Reliability** | High | What's the success rate? How often does it fail? |
| **Performance** | Medium | Is it fast enough? Resource efficient? |
| **Safety** | High | What are the risks? Can it cause harm? |
| **Ease of Use** | Medium | How complex is it to use correctly? |
| **Compatibility** | Medium | Does it work with other selected tools? |
| **Cost** | Low-Medium | What are the resource/API costs? |
| **Maturity** | Medium | Is it well-tested and maintained? |

---

## Quality Checklist

### Completeness
- [ ] All task requirements have corresponding tools
- [ ] Tool catalog is comprehensive and current
- [ ] Fallback tools identified for critical capabilities
- [ ] Tool dependencies documented
- [ ] Configuration parameters specified

### Appropriateness
- [ ] Tools match task requirements
- [ ] No over-engineering (unnecessary tools)
- [ ] No under-engineering (missing capabilities)
- [ ] Trade-offs clearly evaluated
- [ ] Selection rationale documented

### Safety
- [ ] Tool risks identified and mitigated
- [ ] Access permissions properly configured
- [ ] Input validation implemented
- [ ] Destructive operations have safeguards
- [ ] Monitoring and logging enabled

### Usability
- [ ] Tool usage patterns clearly defined
- [ ] Examples provided for each tool
- [ ] Error handling specified
- [ ] Agent can use tools based on documentation

---

## Common Mistakes

### 1. Tool Overload
**Mistake**: Giving agent access to too many tools.

**Why It Happens**: "More is better" mentality; not considering cognitive load.

**How to Avoid**: Select minimal necessary tool set; remove redundant tools.

**Recovery**: Audit tool usage; remove unused or rarely-used tools.

---

### 2. Missing Critical Tools
**Mistake**: Not providing tools needed for task completion.

**Why It Happens**: Incomplete requirements analysis; overlooking edge cases.

**How to Avoid**: Thorough task decomposition; validate tool set against all requirements.

**Recovery**: When agent fails, identify missing capability and add appropriate tool.

---

### 3. Unsafe Tool Access
**Mistake**: Giving agent access to destructive tools without guardrails.

**Why It Happens**: Not considering safety implications; trusting agent too much.

**How to Avoid**: Risk assessment for each tool; implement safety constraints.

**Recovery**: Add validation, confirmation steps, or rollback mechanisms.

---

### 4. Poor Tool Configuration
**Mistake**: Using default settings that don't match task requirements.

**Why It Happens**: Not understanding tool parameters; lack of optimization.

**How to Avoid**: Study tool documentation; test different configurations.

**Recovery**: Profile tool performance; adjust parameters based on results.

---

### 5. Ignoring Tool Dependencies
**Mistake**: Selecting tools that conflict or have unmet dependencies.

**Why It Happens**: Not checking tool compatibility; selecting tools in isolation.

**How to Avoid**: Map tool dependencies; validate tool combinations.

**Recovery**: Resolve conflicts by replacing tools or adding missing dependencies.

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **Code Analysis Agent**: Selecting tools for code review and refactoring
2. **DevOps Agent**: Tools for deployment and monitoring
3. **Testing Agent**: Tools for test generation and execution
4. **Documentation Agent**: Tools for documentation generation and updates

---

## Related Skills

### Prerequisites
- **agent-task-decomposition**: Understanding task requirements
- **agent-context-engineering**: Understanding the environment
- **agent-instruction-design**: Knowing how tools will be used

### Commonly Followed By
- **agent-guardrails**: Implementing safety constraints for tools
- **agent-evaluation**: Measuring tool effectiveness
- **agent-observability**: Monitoring tool usage

### Works Well With
- **agent-workflow-design**: Coordinating tools across workflows
- **agent-handoff-design**: Passing tool access between agents

---

## Skill Composition

### Pattern: Complete Agent Configuration
```
agent-task-decomposition
    ↓
agent-context-engineering
    ↓
agent-instruction-design
    ↓
agent-tool-selection ← YOU ARE HERE
    ↓
agent-guardrails
    ↓
Execution
```

---

## Evaluation Criteria

### Tool Selection Quality
- **Coverage**: Percentage of requirements with appropriate tools (target: 100%)
- **Efficiency**: Percentage of selected tools actually used (target: >80%)
- **Safety**: Number of tool-related incidents (target: 0)

### Agent Performance
- **Success Rate**: Task completion rate with selected tools (target: >90%)
- **Tool Usage Accuracy**: Correct tool usage rate (target: >95%)
- **Fallback Effectiveness**: Success rate when using fallback tools (target: >70%)

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Intermediate  
**Estimated Time**: 1-2 hours