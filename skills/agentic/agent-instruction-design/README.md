# Agent Instruction Design

**Quick Reference Guide**

---

## Purpose

Design clear, executable instructions that enable AI agents to perform complex tasks autonomously with high success rates and minimal ambiguity.

---

## When to Use

✓ Creating task specifications for AI agent execution  
✓ Agent tasks failing due to unclear instructions  
✓ Designing autonomous workflows  
✓ Building multi-agent systems  
✓ Optimizing agent performance  
✓ Documenting repeatable agent workflows  

---

## Quick Start

### 1. Define Clear Objectives (10 min)
- State primary goal in one sentence
- List sub-goals and success criteria
- Define what "done" looks like
- Clarify scope boundaries

### 2. Break Down into Atomic Steps (20 min)
- List every action in order
- Ensure each step is atomic (single action)
- Identify parallel vs. sequential steps
- Define inputs and outputs for each

### 3. Add Preconditions & Outcomes (15 min)
- Specify what must be true before each step
- Define expected outcome for each step
- Make outcomes observable and verifiable

### 4. Design Decision Points (15 min)
- Identify where agent must make decisions
- Define decision criteria (if-then-else)
- Cover all possible branches

### 5. Add Error Handling (15 min)
- Identify potential failures
- Define detection and recovery
- Specify retry vs. fail-fast logic

---

## Instruction Template

```markdown
# Task: [Task Name]

## Objective
[One clear sentence describing the goal]

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Prerequisites
- Required tool: X
- Required access: Y
- Required state: Z

## Instructions

### Step 1: [Action]
**Preconditions**: [What must be true]
**Action**: [Exactly what to do]
**Expected Outcome**: [What success looks like]
**Validation**: [How to verify]
**On Error**: [What to do if it fails]

### Step 2: [Action]
...

## Decision Points

### Decision 1: [Condition]
- If [condition]: Do [action A]
- Else: Do [action B]

## Error Handling

### Error Type 1
**Detection**: [How to detect]
**Recovery**: [What to do]
**Retry**: [Yes/No, how many times]

## Validation Checkpoints
- [ ] Checkpoint 1: [Criteria]
- [ ] Checkpoint 2: [Criteria]
```

---

## Key Principles

### Clarity
- Use simple, direct language
- Active voice ("Read the file" not "The file should be read")
- No jargon or ambiguous terms
- Consistent terminology

### Completeness
- All necessary steps included
- Preconditions specified
- Expected outcomes defined
- All decision points covered
- Error handling for all failure modes

### Executability
- Each step is atomic and actionable
- Steps in correct order
- Dependencies clear
- Success is measurable

### Robustness
- Error handling for each step
- Fallback strategies defined
- Validation checkpoints at critical points
- Edge cases addressed

---

## Common Mistakes to Avoid

❌ **Vague Language**: "Improve the code"  
→ Use specific criteria: "Reduce cyclomatic complexity to <10"

❌ **Missing Preconditions**: "Read the config file"  
→ Specify: "Read config.json from /etc/app/"

❌ **Unclear Success**: "Complete the task"  
→ Define: "Task complete when all tests pass and file saved"

❌ **No Error Handling**: Only happy path  
→ Add: "If file not found, create it with defaults"

❌ **Overly Complex**: One massive instruction  
→ Break into smaller, focused sub-tasks

❌ **Assuming Tool Knowledge**: "Use the API"  
→ Specify: "Call POST /api/users with {email, name}"

---

## Quality Checklist

### Clarity
- [ ] Objective stated clearly
- [ ] Simple, direct language
- [ ] No ambiguous terms
- [ ] Active voice throughout
- [ ] Consistent terminology

### Completeness
- [ ] All steps included
- [ ] Preconditions specified
- [ ] Expected outcomes defined
- [ ] Decision logic clear
- [ ] Error handling complete

### Executability
- [ ] Each step is atomic
- [ ] Correct order
- [ ] Dependencies clear
- [ ] Success measurable

### Robustness
- [ ] Error handling for each step
- [ ] Fallback strategies
- [ ] Validation checkpoints
- [ ] Edge cases covered

---

## Success Metrics

**Target Benchmarks**:
- Success rate: **>90%**
- First-time success: **>80%**
- Clarity score: **>95%**
- Completeness score: **>95%**
- Error recovery rate: **>70%**

---

## Related Skills

**Prerequisites**:
- requirements-analysis
- agent-task-decomposition
- agent-context-engineering

**Next Steps**:
- agent-tool-selection
- agent-guardrails
- agent-evaluation

**Works With**:
- agent-workflow-design
- agent-handoff-design
- agent-observability

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **API Endpoint Implementation**: Step-by-step instructions
2. **Code Refactoring**: Safe refactoring instructions
3. **Bug Investigation**: Systematic debugging instructions
4. **Test Suite Creation**: Comprehensive testing instructions

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
