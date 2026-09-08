# Architecture Decision - Step-by-Step Instructions

## Overview

This skill guides you through making and documenting architectural decisions using Architecture Decision Records (ADRs).

## Step-by-Step Workflow

### Step 1: Define Decision Context (30 minutes)

**Actions:**
1. Clearly state what decision needs to be made
2. Explain why this decision is important
3. Identify who is affected by this decision
4. Determine urgency and reversibility
5. Identify decision-maker and stakeholders

**Outputs:**
- Decision statement (question format)
- Context document
- Stakeholder list

### Step 2: Identify Decision Criteria (30 minutes)

**Actions:**
1. List must-have criteria (deal-breakers)
2. List important criteria (performance, cost, maintainability, etc.)
3. List nice-to-have criteria
4. Assign weights to criteria (High/Medium/Low)

**Outputs:**
- Weighted criteria list

### Step 3: Identify Alternatives (30-60 minutes)

**Actions:**
1. Brainstorm all viable options
2. Include "do nothing" as an option
3. For each option, document:
   - Brief description
   - Key advantages
   - Key disadvantages
   - Rough effort estimate
4. Eliminate options that fail must-have criteria

**Outputs:**
- List of 2-5 viable alternatives

### Step 4: Evaluate Alternatives (1-2 hours)

**Actions:**
1. Create evaluation matrix
2. Score each option against each criterion (1-10)
3. Calculate weighted scores
4. Identify top 1-2 options
5. Validate scoring with team members

**Outputs:**
- Completed evaluation matrix
- Top options identified

### Step 5: Assess Risks and Consequences (1 hour)

**Actions:**
1. For top option(s), identify:
   - Positive consequences
   - Negative consequences
   - Risks and mitigation strategies
2. Consider second-order effects
3. Identify assumptions

**Outputs:**
- Risk and consequence assessment

### Step 6: Make Decision (30 minutes)

**Actions:**
1. Review evaluation and assessment
2. Consult with stakeholders
3. Make decision
4. Get approval from decision-maker

**Outputs:**
- Chosen option
- Decision rationale

### Step 7: Create ADR (1 hour)

**Actions:**
1. Use ADR template
2. Document:
   - Context
   - Decision
   - Alternatives considered
   - Consequences (positive and negative)
   - Risks
3. Assign ADR number
4. Set status (Proposed/Accepted)
5. Store in version control (docs/adr/ or similar)

**Outputs:**
- Complete ADR document

### Step 8: Communicate and Implement (30-60 minutes)

**Actions:**
1. Share ADR with team and stakeholders
2. Present decision in team meeting
3. Address questions and concerns
4. Create implementation action items
5. Assign ownership and timeline

**Outputs:**
- Communication sent
- Action items created

## ADR Template

```markdown
# ADR-XXX: [Short Decision Title]

**Date:** YYYY-MM-DD
**Status:** [Proposed | Accepted | Deprecated | Superseded by ADR-YYY]
**Deciders:** [List of people involved in decision]

## Context

[Describe the issue or problem that requires a decision. Include:
- What is the problem?
- Why does it need to be solved?
- What are the constraints?
- What are the requirements?]

## Decision

[State the decision clearly and concisely. What are we going to do?]

## Alternatives Considered

### Option 1: [Name]
- **Description:** [Brief description]
- **Pros:** [Advantages]
- **Cons:** [Disadvantages]
- **Why not chosen:** [Reason]

### Option 2: [Name]
- **Description:** [Brief description]
- **Pros:** [Advantages]
- **Cons:** [Disadvantages]
- **Why not chosen:** [Reason]

## Consequences

### Positive
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

### Negative
- [Tradeoff 1]
- [Tradeoff 2]
- [Tradeoff 3]

## Risks

- **Risk 1:** [Description]
  - **Mitigation:** [How we'll address it]
- **Risk 2:** [Description]
  - **Mitigation:** [How we'll address it]

## Implementation Notes

[Any important notes about implementing this decision]

## Related Decisions

- [ADR-XXX: Related decision]
- [ADR-YYY: Another related decision]
```

## Tips for Success

- **Be decisive**: Don't let perfect be the enemy of good
- **Document quickly**: Write ADR while decision is fresh
- **Be honest about tradeoffs**: Every decision has downsides
- **Involve the right people**: Consult those affected, but don't seek universal approval
- **Time-box analysis**: Don't over-analyze reversible decisions
- **Update ADRs**: Mark as deprecated when superseded

## Common Pitfalls to Avoid

- Spending weeks analyzing a decision that can be reversed in days
- Making irreversible decisions without proper analysis
- Not documenting the decision rationale
- Ignoring stakeholder input
- Choosing based on personal preference rather than criteria
- Not considering the "do nothing" option