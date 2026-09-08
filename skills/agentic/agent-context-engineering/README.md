# Agent Context Engineering

**Quick Reference Guide**

---

## Purpose

Engineer effective context packages that enable AI agents to execute tasks with complete understanding and minimal ambiguity.

---

## When to Use

✓ Preparing tasks for AI agent execution  
✓ Agent needs to understand existing code/architecture  
✓ Handoffs between agents require shared understanding  
✓ Agent output quality suffering from insufficient context  
✓ Designing prompts for autonomous agents  
✓ Building multi-agent systems  
✓ Debugging agent failures caused by context gaps  

---

## Quick Start

### 1. Analyze Task Requirements (15-20 min)
- List all operations the agent will perform
- Identify decision points needing context
- Map to specific code artifacts
- Consider edge cases and errors

### 2. Identify Context Sources (20-30 min)
- Locate relevant files, modules, documentation
- Find examples and test cases
- Identify implicit context (conventions, patterns)

### 3. Assess Context Scope (15-20 min)
- Calculate token/size requirements
- Prioritize: must-have, should-have, nice-to-have
- Balance completeness with agent constraints

### 4. Structure Hierarchically (20-30 min)
- **Layer 1**: Overview (architecture, purpose, concepts)
- **Layer 2**: Interfaces (APIs, contracts)
- **Layer 3**: Implementation (code, algorithms)
- **Layer 4**: Examples (tests, usage patterns)
- **Layer 5**: Metadata (dependencies, constraints)

### 5. Optimize for Agent Consumption (15-20 min)
- Use clear, consistent formatting
- Add headers and navigation
- Highlight critical information
- Remove noise and redundancy

---

## Context Package Template

```markdown
# Context Package: [Task Name]

## Overview
- System architecture
- Key concepts
- File organization
- Conventions

## Interfaces
- API contracts
- Expected inputs/outputs
- Data structures

## Implementation Patterns
- Code examples
- Patterns to follow
- Error handling

## Examples
- Test cases
- Similar implementations
- Usage patterns

## Metadata & Constraints
- Dependencies
- ⚠️ Critical constraints
- Related files
- Version information
```

---

## Key Principles

### Completeness
- Cover all task requirements
- Include dependencies and related code
- Document domain knowledge and business rules
- Provide examples for edge cases
- Make implicit assumptions explicit

### Quality
- Current and accurate information
- No contradictions or ambiguities
- Appropriate level of detail
- Clear relationships between elements
- Metadata for provenance and confidence

### Usability
- Well-structured and navigable
- Consistent formatting
- Highlighted critical information
- Fits within agent constraints
- Sequential or random access friendly

### Maintainability
- Versioned and timestamped
- Update triggers defined
- Traceable sources
- Can be regenerated automatically

---

## Common Mistakes to Avoid

❌ **Context Overload**: Too much context overwhelms the agent  
→ Prioritize ruthlessly, use on-demand retrieval

❌ **Implicit Assumptions**: Assuming agent knows conventions  
→ Document all conventions explicitly

❌ **Stale Context**: Outdated information  
→ Implement versioning and change detection

❌ **Missing Relationships**: Isolated context without connections  
→ Add dependency maps and relationship diagrams

❌ **Poor Structure**: Unorganized context dumps  
→ Use hierarchical structure with clear headers

❌ **Ambiguous Context**: Multiple interpretations possible  
→ Use precise language, provide examples

❌ **No Validation**: Not testing context sufficiency  
→ Walk through task with context before execution

---

## Quality Checklist

### Completeness
- [ ] All task requirements have corresponding context
- [ ] Dependencies and related code included
- [ ] Domain knowledge documented
- [ ] Examples cover edge cases
- [ ] Implicit assumptions made explicit

### Quality
- [ ] Information is current and accurate
- [ ] No contradictions or ambiguities
- [ ] Appropriate level of detail
- [ ] Clear relationships between elements

### Usability
- [ ] Well-structured and easy to navigate
- [ ] Consistent formatting throughout
- [ ] Critical information highlighted
- [ ] Fits within agent constraints

### Maintainability
- [ ] Versioned and timestamped
- [ ] Update triggers defined
- [ ] Sources are traceable

---

## Context Scope Decision Guide

**Is the task well-defined and isolated?**  
└─ Yes → Minimal context (interfaces, immediate dependencies)  
└─ No → Comprehensive context (architecture, patterns, examples)

**Does the agent have domain knowledge?**  
└─ Yes → Focus on technical context  
└─ No → Include business context

**Is the codebase familiar to the agent?**  
└─ Yes → Incremental context (only what's new)  
└─ No → Comprehensive context (architecture, patterns, conventions)

**Are there critical constraints?**  
└─ Yes → Explicit constraint documentation  
└─ No → Standard best practices

**Is this a one-time task or recurring?**  
└─ One-time → Inline context in prompt  
└─ Recurring → Build reusable context templates

---

## Success Metrics

**Target Benchmarks**:
- Agent success rate: **>90%** with engineered context
- Context sufficiency: **<5%** of errors due to missing context
- Context utilization: **>70%** of provided context used
- Staleness: **<24 hours** for active codebases
- Completeness score: **>95%**

---

## Related Skills

**Prerequisites**:
- architecture-discovery
- requirements-analysis
- agent-task-decomposition

**Next Steps**:
- agent-instruction-design
- agent-tool-selection
- agent-handoff-design

**Works With**:
- agent-workflow-design
- agent-guardrails
- agent-observability

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **REST API Implementation**: Context for building new API endpoint
2. **Legacy Code Refactoring**: Context for safe refactoring
3. **Multi-Agent Bug Fix**: Context coordination across agents
4. **Database Migration**: Context for complex schema changes

---

## Full Documentation

- **[SKILL.md](./SKILL.md)**: Complete skill documentation
- **[instructions.md](./instructions.md)**: Step-by-step execution guide
- **[examples.md](./examples.md)**: Detailed examples
- **[skill.json](./skill.json)**: Machine-readable metadata

---

**Version**: 1.0.0  
**Complexity**: Intermediate  
**Estimated Time**: 1-3 hours  
**Category**: Agentic Engineering
