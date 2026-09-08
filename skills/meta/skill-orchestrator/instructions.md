# Skill Orchestrator - Execution Instructions

This document provides detailed, step-by-step instructions for executing the skill-orchestrator meta-skill.

## Overview

The skill-orchestrator analyzes an engineering problem and produces a workflow of skills that should be executed to solve it. This is a planning and composition skill, not an execution skill.

## Prerequisites

- Access to the skills catalog (`catalog/skills.yaml`)
- Understanding of available skills and their relationships
- Clear problem statement from the user
- Relevant context about the engineering challenge

## Step-by-Step Execution

### Step 1: Gather Problem Information

**Input Required:**
- Engineering problem description
- Context (existing system, team, constraints)
- Goals and success criteria
- Timeline and resource constraints

**Actions:**
1. Read the problem statement carefully
2. Identify any ambiguities or missing information
3. Clarify with stakeholders if needed
4. Document all constraints

**Output:**
- Complete problem specification

### Step 2: Categorize the Problem

**Actions:**
1. Determine the primary problem category:
   - New feature development
   - Architecture review/design
   - System migration
   - Performance optimization
   - Security improvement
   - AI/Agent development
   - Production incident
   - Technical debt reduction
   - Technology evaluation
   - Documentation creation

2. Identify secondary aspects:
   - Does it involve security?
   - Does it require scalability analysis?
   - Does it need documentation?
   - Does it involve data architecture?
   - Does it require testing strategy?

**Output:**
- Problem category
- List of aspects to address

### Step 3: Identify Required Capabilities

**For each aspect, determine what's needed:**

**Discovery/Understanding:**
- Do we need to understand existing architecture?
- Do we need to analyze current code?
- Do we need to map dependencies?
- Do we need to identify technical debt?

**Design/Planning:**
- Do we need to design new architecture?
- Do we need to design APIs?
- Do we need to plan data architecture?
- Do we need to design integrations?

**Decision-Making:**
- Do we need to select technologies?
- Do we need to analyze tradeoffs?
- Do we need to evaluate vendors?
- Do we need to create ADRs?

**Validation:**
- Do we need security review?
- Do we need performance analysis?
- Do we need reliability analysis?
- Do we need scalability analysis?

**Documentation:**
- Do we need architecture documentation?
- Do we need API documentation?
- Do we need runbooks?
- Do we need migration guides?

**Implementation:**
- Do we need testing strategy?
- Do we need deployment strategy?
- Do we need observability design?

**Output:**
- List of required capabilities

### Step 4: Map Capabilities to Skills

**Actions:**
1. For each required capability, consult the skills catalog
2. Identify the most appropriate skill(s)
3. Note any skill dependencies
4. Identify skills that commonly work together
5. Mark skills as mandatory or optional

**Example Mapping:**

| Capability | Skill(s) | Priority |
|------------|----------|----------|
| Understand existing system | architecture-discovery | Mandatory |
| Identify service boundaries | service-boundary-analysis | Mandatory |
| Review security | security-architecture-review | Mandatory |
| Document decisions | adr-documentation | Optional |

**Output:**
- Capability-to-skill mapping
- Priority for each skill

### Step 5: Sequence the Skills

**Sequencing Rules:**

1. **Start with Discovery**
   - Architecture discovery
   - Codebase analysis
   - Dependency mapping
   - Requirements analysis

2. **Follow with Analysis**
   - Technical debt analysis
   - Complexity analysis
   - Bottleneck analysis
   - Risk analysis

3. **Then Design**
   - System design
   - Architecture design
   - API design
   - Data architecture design

4. **Make Decisions**
   - Technology selection
   - Tradeoff analysis
   - Architecture decision

5. **Validate**
   - Security review
   - Scalability analysis
   - Reliability analysis
   - Performance analysis

6. **Document**
   - ADR documentation
   - Architecture documentation
   - API documentation
   - Runbooks

7. **Plan Implementation**
   - Testing strategy
   - Migration planning
   - Deployment strategy
   - Observability design

8. **Ensure Production Readiness**
   - Production readiness review

**Dependency Checking:**
- Ensure each skill's required inputs are available from:
  - Initial problem context
  - Outputs of previous skills in the sequence

**Output:**
- Ordered skill sequence

### Step 6: Document Dependencies

**For each skill in the sequence, document:**

1. **Inputs needed:**
   - What information does this skill need?
   - Where does it come from? (initial context or previous skill)

2. **Outputs produced:**
   - What will this skill produce?
   - Which subsequent skills need these outputs?

3. **Decision points:**
   - Are there conditional branches?
   - What determines which path to take?

**Example:**

```
Skill: architecture-discovery
Inputs:
  - System description (from initial context)
  - Access to codebase (from initial context)
Outputs:
  - Architecture diagram
  - Component inventory
  - Dependency map
Used by:
  - service-boundary-analysis (needs dependency map)
  - technical-debt-analysis (needs component inventory)
```

**Output:**
- Dependency documentation for each skill

### Step 7: Add Rationale

**For each skill, explain:**

1. **Why is this skill needed?**
   - What problem does it address?
   - What risk does it mitigate?
   - What value does it add?

2. **Why in this position?**
   - Why not earlier?
   - Why not later?
   - What dependencies dictate this position?

3. **What happens if we skip it?**
   - What risks do we accept?
   - What quality do we sacrifice?

**Example:**

```
Skill: security-architecture-review
Why needed:
  - Identifies security vulnerabilities before implementation
  - Ensures compliance with security standards
  - Reduces risk of security incidents
Why here:
  - After architecture design (need architecture to review)
  - Before implementation (cheaper to fix in design)
  - Parallel with other validation skills (scalability, reliability)
If skipped:
  - Security vulnerabilities may reach production
  - Expensive security fixes post-launch
  - Potential compliance violations
```

**Output:**
- Rationale for each skill

### Step 8: Validate the Workflow

**Validation Checklist:**

- [ ] Does the workflow address the original problem?
- [ ] Are all mandatory capabilities covered?
- [ ] Is the sequence logically ordered?
- [ ] Are all dependencies satisfied?
- [ ] Are there any circular dependencies?
- [ ] Are there any unnecessary skills?
- [ ] Are there any gaps?
- [ ] Can this be executed with available resources?
- [ ] Is the timeline realistic?
- [ ] Are decision points clearly marked?
- [ ] Is the rationale convincing?

**If validation fails:**
- Identify the issue
- Adjust the workflow
- Re-validate

**Output:**
- Validated workflow

### Step 9: Format the Output

**Create the final workflow document with:**

1. **Executive Summary**
   - Problem statement
   - Proposed approach
   - Expected outcomes
   - Estimated timeline

2. **Skill Sequence**
   - Ordered list of skills
   - Brief description of each
   - Dependencies
   - Estimated time per skill

3. **Workflow Diagram**
   - Visual representation of the sequence
   - Show parallel execution if applicable
   - Show decision points if applicable

4. **Detailed Rationale**
   - Why each skill is needed
   - Why in this order
   - What each skill produces

5. **Execution Plan**
   - Who executes each skill (human, agent, team)
   - What tools are needed
   - What access is required
   - What the success criteria are

6. **Risk Assessment**
   - What could go wrong
   - What skills mitigate which risks
   - What risks remain

**Output:**
- Complete workflow document

## Output Format

### Minimal Format (for simple problems)

```markdown
# Workflow: [Problem Name]

## Problem
[Brief description]

## Skill Sequence
1. skill-name-1 - [why needed]
2. skill-name-2 - [why needed]
3. skill-name-3 - [why needed]

## Expected Outcome
[What this workflow will produce]
```

### Standard Format (for typical problems)

```markdown
# Workflow: [Problem Name]

## Problem Statement
[Detailed description]

## Context
[Relevant background]

## Goals
[What we want to achieve]

## Skill Sequence

### 1. skill-name-1
- **Purpose**: [why needed]
- **Inputs**: [what it needs]
- **Outputs**: [what it produces]
- **Estimated time**: [duration]

### 2. skill-name-2
- **Purpose**: [why needed]
- **Inputs**: [what it needs]
- **Outputs**: [what it produces]
- **Estimated time**: [duration]

[Continue for all skills]

## Workflow Diagram
```
skill-1
   ↓
skill-2
   ↓
skill-3
```

## Total Estimated Time
[Sum of all skills]

## Expected Outcomes
[What this workflow will deliver]
```

### Comprehensive Format (for complex problems)

```markdown
# Workflow: [Problem Name]

## Executive Summary
- **Problem**: [one-sentence description]
- **Approach**: [high-level strategy]
- **Timeline**: [estimated duration]
- **Resources**: [what's needed]
- **Outcomes**: [what will be delivered]

## Problem Statement
[Detailed description of the engineering challenge]

## Context
- **Current state**: [existing system/situation]
- **Constraints**: [technical, business, resource]
- **Stakeholders**: [who's involved]
- **Timeline**: [deadlines]

## Goals and Success Criteria
- Goal 1: [success criteria]
- Goal 2: [success criteria]

## Skill Sequence

### Phase 1: Discovery

#### 1. skill-name-1
- **Purpose**: [why needed]
- **Inputs**: 
  - Input 1 (from: initial context)
  - Input 2 (from: initial context)
- **Outputs**:
  - Output 1 (used by: skill-3, skill-5)
  - Output 2 (used by: skill-4)
- **Estimated time**: [duration]
- **Rationale**: [detailed explanation]

[Continue for all Phase 1 skills]

### Phase 2: Design
[Similar structure]

### Phase 3: Validation
[Similar structure]

### Phase 4: Documentation
[Similar structure]

## Workflow Diagram
```
        Discovery Phase
              ↓
     ┌────────┴────────┐
     ↓                 ↓
  skill-1          skill-2
     └────────┬────────┘
              ↓
        Design Phase
              ↓
          skill-3
              ↓
     ┌────────┼────────┐
     ↓        ↓        ↓
  skill-4  skill-5  skill-6
     └────────┼────────┘
              ↓
    Documentation Phase
              ↓
          skill-7
```

## Dependencies

| Skill | Depends On | Provides For |
|-------|------------|-------------|
| skill-1 | - | skill-3, skill-5 |
| skill-2 | - | skill-4 |
| skill-3 | skill-1 | skill-7 |

## Decision Points

### After skill-3
- **If** [condition] → execute skill-4
- **Else** → skip to skill-5

## Execution Plan

| Skill | Executor | Tools Needed | Access Required |
|-------|----------|--------------|----------------|
| skill-1 | AI Agent | Code analysis | Codebase read |
| skill-2 | Human Architect | Diagramming | Architecture docs |

## Risk Assessment

| Risk | Mitigated By | Residual Risk |
|------|--------------|---------------|
| Security vulnerabilities | security-review | Low |
| Scalability issues | scalability-analysis | Medium |

## Timeline

- **Phase 1 (Discovery)**: 2 hours
- **Phase 2 (Design)**: 4 hours
- **Phase 3 (Validation)**: 3 hours
- **Phase 4 (Documentation)**: 1 hour
- **Total**: 10 hours

## Expected Outcomes

1. [Specific deliverable 1]
2. [Specific deliverable 2]
3. [Specific deliverable 3]

## Success Criteria

- [ ] All goals achieved
- [ ] All risks mitigated
- [ ] All deliverables produced
- [ ] Quality standards met
```

## Tips for Effective Orchestration

### 1. Start Simple
- Don't over-engineer the workflow
- Include only necessary skills
- Can always add more skills later

### 2. Think in Phases
- Group related skills together
- Makes the workflow easier to understand
- Enables parallel execution

### 3. Be Explicit About Dependencies
- Don't assume dependencies are obvious
- Document what each skill needs
- Prevents execution errors

### 4. Consider the Executor
- Can a human execute this?
- Can an AI agent execute this?
- What tools/access are needed?

### 5. Validate Early
- Don't wait until the end to validate
- Check the workflow as you build it
- Get feedback from stakeholders

### 6. Be Flexible
- Workflows may need adjustment during execution
- Mark optional skills clearly
- Document decision points

### 7. Learn from Execution
- Track which workflows work well
- Identify common patterns
- Create reusable workflow recipes

## Common Patterns

### Pattern: New Feature
```
requirements → design → review → validate → document → plan
```

### Pattern: Architecture Review
```
discover → analyze → review → decide → document
```

### Pattern: Migration
```
discover → analyze → plan → design → validate → document → execute
```

### Pattern: Incident Response
```
analyze → root-cause → fix-design → validate → document → prevent
```

### Pattern: Agent Development
```
requirements → decompose → design → validate → evaluate → deploy
```

## Integration with AI Agents

### Agent Workflow

1. **Agent receives problem**
   ```
   Input: "We need to migrate our monolith to microservices"
   ```

2. **Agent calls skill-orchestrator**
   ```
   skill-orchestrator(problem, context, goals)
   ```

3. **Orchestrator returns workflow**
   ```
   Output: [skill-1, skill-2, skill-3, ...]
   ```

4. **Agent executes skills in sequence**
   ```
   result-1 = execute(skill-1, initial-context)
   result-2 = execute(skill-2, result-1)
   result-3 = execute(skill-3, result-2)
   ...
   ```

5. **Final result delivered**
   ```
   Output: Complete solution to the problem
   ```

### Agent-Specific Considerations

- **Context passing**: Ensure each skill receives necessary context
- **Error handling**: What happens if a skill fails?
- **Checkpointing**: Can the workflow resume if interrupted?
- **Observability**: How to track progress?
- **Human-in-the-loop**: When to ask for human input?

## Troubleshooting

### Problem: Workflow is too long
**Solution**: 
- Remove optional skills
- Combine related skills
- Parallelize independent skills

### Problem: Dependencies are circular
**Solution**:
- Re-sequence skills
- Break circular dependency
- Combine dependent skills

### Problem: Missing inputs
**Solution**:
- Add discovery skill earlier
- Request additional context
- Add prerequisite skill

### Problem: Unclear rationale
**Solution**:
- Revisit the problem statement
- Clarify goals
- Explain each skill's value

### Problem: Workflow doesn't solve the problem
**Solution**:
- Re-analyze the problem
- Identify missing capabilities
- Add necessary skills

## Conclusion

The skill-orchestrator is a powerful meta-skill that enables systematic, comprehensive approaches to complex engineering problems. By following these instructions, you can create effective workflows that leverage the full power of the engineering skills library.