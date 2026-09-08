# Agent Tool Selection

**Quick Reference Guide**

---

## Purpose

Select and configure the optimal set of tools for AI agents to execute tasks efficiently, safely, and successfully.

---

## When to Use

✓ Designing agent workflows requiring specific capabilities  
✓ Agent tasks failing due to missing/inappropriate tools  
✓ Optimizing agent performance through better tool choices  
✓ Building multi-agent systems with specialized tool sets  
✓ Configuring tool access and permissions  

---

## Quick Start

### 1. Analyze Task Requirements (15 min)
- Break down task into operations
- Identify required capabilities
- Note performance and security requirements

### 2. Inventory Available Tools (20 min)
- List all available tools
- Document capabilities, inputs, outputs
- Note limitations and dependencies

### 3. Map Capabilities to Tools (20 min)
- Match requirements to tools
- Identify gaps and overlaps
- Note tool combinations needed

### 4. Evaluate Trade-offs (30 min)
- Score tools on functionality, reliability, performance, safety
- Calculate weighted scores
- Document selection rationale

### 5. Select and Configure (35 min)
- Choose primary and fallback tools
- Configure parameters and permissions
- Implement safety guardrails
- Validate with test cases

---

## Tool Selection Template

```markdown
# Tool Selection: [Task Name]

## Requirements
1. [Capability 1]
2. [Capability 2]
...

## Tool Selection Matrix

| Requirement | Candidate Tools | Selected | Rationale |
|-------------|-----------------|----------|------------|
| [Req 1] | [Tool A, Tool B] | [Tool A] | [Why] |

## Tool Configuration

### [Tool Name]
- **Parameters**: [Key settings]
- **Permissions**: [Access level]
- **Safety**: [Guardrails]
- **Fallback**: [Alternative tool]

## Usage Patterns

1. Use [Tool X] when [condition]
2. Use [Tool Y] for [purpose]
...
```

---

## Tool Evaluation Criteria

| Criterion | Weight | Questions |
|-----------|--------|------------|
| Functionality | High | Does it satisfy the requirement? |
| Reliability | High | What's the success rate? |
| Performance | Medium | Is it fast enough? |
| Safety | High | What are the risks? |
| Ease of Use | Medium | How complex is it? |
| Compatibility | Medium | Works with other tools? |

---

## Common Mistakes to Avoid

❌ **Tool Overload**: Too many tools  
→ Select minimal necessary set

❌ **Missing Tools**: Can't complete task  
→ Thorough requirements analysis

❌ **Unsafe Access**: Risk of harm  
→ Implement safety guardrails

❌ **Poor Configuration**: Suboptimal performance  
→ Test and optimize parameters

❌ **Ignored Dependencies**: Tools conflict  
→ Map and validate dependencies

---

## Quality Checklist

- [ ] All requirements have corresponding tools
- [ ] Tool catalog is complete and current
- [ ] Fallback tools for critical capabilities
- [ ] Tool dependencies documented
- [ ] Configuration parameters optimized
- [ ] Usage patterns clearly defined
- [ ] Safety guardrails implemented
- [ ] All tools validated with tests

---

## Success Metrics

**Target Benchmarks**:
- Coverage: **100%** of requirements
- Efficiency: **>80%** of tools used
- Safety: **0** tool-related incidents
- Success rate: **>90%** with selected tools

---

## Related Skills

**Prerequisites**:
- agent-task-decomposition
- agent-context-engineering
- agent-instruction-design

**Next Steps**:
- agent-guardrails
- agent-evaluation
- agent-observability

---

## Examples

See [examples.md](./examples.md):

1. **Code Analysis Agent**: Tools for code review
2. **DevOps Agent**: Tools for deployment
3. **Testing Agent**: Tools for test generation
4. **Documentation Agent**: Tools for docs

---

## Full Documentation

- **[SKILL.md](./SKILL.md)**: Complete documentation
- **[instructions.md](./instructions.md)**: Step-by-step guide
- **[examples.md](./examples.md)**: Detailed examples
- **[skill.json](./skill.json)**: Metadata

---

**Version**: 1.0.0  
**Complexity**: Intermediate  
**Estimated Time**: 1-2 hours  
**Category**: Agentic Engineering
