# Architecture Decision

## Purpose

Make and document architectural and technology decisions using a structured, evidence-based approach.

## When to Use

- When choosing between multiple architectural approaches or technologies
- When making decisions that will be difficult or expensive to reverse
- When decisions will significantly impact the system's quality attributes
- When team alignment is needed on a technical direction
- When documenting the rationale behind important architectural choices

## When NOT to Use

- For trivial or easily reversible decisions
- When the decision has already been made and documented
- For implementation details that don't affect architecture
- When comparing alternatives (use tradeoff-analysis for detailed comparison)

## Inputs

- **decision-context**: What decision needs to be made and why
- **requirements**: Functional and non-functional requirements
- **constraints**: Technical, business, timeline, budget constraints
- **alternatives**: Options being considered
- **stakeholders**: Who is affected by this decision

## Expected Outputs

- **architecture-decision-record**: Documented decision with context, options, and rationale
- **decision-outcome**: The chosen option
- **consequences**: Expected positive and negative consequences
- **action-items**: Next steps to implement the decision

## Workflow

### 1. Define the Decision Context

**What decision needs to be made?**
- Be specific about what you're deciding
- Frame as a question: "Should we use X or Y for Z?"

**Why does this decision matter?**
- What is the business or technical driver?
- What happens if we don't decide?
- What is the urgency?

**Who is affected?**
- Which teams or stakeholders?
- Who should be consulted?
- Who has decision-making authority?

### 2. Identify Decision Criteria

**Must-have criteria:**
- Requirements that any solution must meet
- Deal-breakers or non-negotiables

**Important criteria:**
- Performance requirements
- Scalability needs
- Security requirements
- Maintainability considerations
- Cost constraints
- Team expertise
- Time to implement

**Nice-to-have criteria:**
- Preferences that would be beneficial but not essential

### 3. Identify Alternatives

**List all viable options:**
- Option 1: [Description]
- Option 2: [Description]
- Option 3: [Description]
- Option N: Do nothing / status quo

**For each option, document:**
- Brief description
- How it meets requirements
- Key advantages
- Key disadvantages
- Rough effort estimate

### 4. Evaluate Alternatives

**Score each option against criteria:**

| Criterion | Weight | Option 1 | Option 2 | Option 3 |
|-----------|--------|----------|----------|----------|
| Performance | High | 8/10 | 6/10 | 9/10 |
| Cost | High | 5/10 | 9/10 | 6/10 |
| Maintainability | Medium | 7/10 | 8/10 | 6/10 |
| Time to implement | Medium | 6/10 | 9/10 | 4/10 |

**Consider:**
- Does any option fail must-have criteria? (Eliminate it)
- Which option best balances the important criteria?
- What are the tradeoffs?

### 5. Assess Risks and Consequences

**For the leading option(s):**

**Positive consequences:**
- What benefits do we gain?
- What problems does this solve?
- What opportunities does this create?

**Negative consequences:**
- What challenges will we face?
- What new problems might this create?
- What are we giving up?

**Risks:**
- What could go wrong?
- What assumptions are we making?
- What is our mitigation strategy?

### 6. Make the Decision

**Choose the option that:**
- Meets all must-have criteria
- Best balances important criteria
- Has acceptable risks and consequences
- Has team buy-in

**Document the decision:**
- What was decided
- Why this option was chosen
- What alternatives were considered
- What are the expected consequences

### 7. Create Architecture Decision Record (ADR)

**ADR Template:**

```markdown
# ADR-XXX: [Decision Title]

## Status
[Proposed | Accepted | Deprecated | Superseded]

## Context
What is the issue we're trying to solve? What are the constraints?

## Decision
What decision did we make?

## Alternatives Considered
- Option 1: [Brief description and why not chosen]
- Option 2: [Brief description and why not chosen]

## Consequences
### Positive
- [Benefit 1]
- [Benefit 2]

### Negative
- [Tradeoff 1]
- [Tradeoff 2]

## Risks
- [Risk 1 and mitigation]
- [Risk 2 and mitigation]

## Related Decisions
- ADR-XXX: [Related decision]
```

### 8. Communicate and Implement

**Communication:**
- Share ADR with stakeholders
- Present decision and rationale to the team
- Address questions and concerns
- Get sign-off from decision-makers

**Implementation:**
- Create action items for implementation
- Assign ownership
- Set timeline
- Plan for monitoring and validation

## Decision Framework

### Decision Reversibility

**Irreversible (Type 1) Decisions:**
- Difficult or impossible to reverse
- Require extensive analysis and consensus
- Examples: Database choice, programming language, cloud provider
- **Approach**: Thorough evaluation, formal ADR, stakeholder approval

**Reversible (Type 2) Decisions:**
- Can be changed relatively easily
- Can be made quickly with less analysis
- Examples: Library choice, caching strategy, API framework
- **Approach**: Lightweight evaluation, document decision, move forward

### Decision Urgency

**Urgent:**
- Blocking critical work
- Time-sensitive opportunity
- **Approach**: Time-box analysis, make best decision with available information

**Important but not urgent:**
- Affects long-term architecture
- **Approach**: Thorough analysis, stakeholder input, formal ADR

**Low urgency:**
- Can be deferred
- **Approach**: Gather more information, revisit later

### Consensus Level

**Full consensus:**
- Everyone agrees
- Ideal but not always achievable

**Consent:**
- No one has strong objections
- "Good enough for now, safe enough to try"
- Practical for most decisions

**Consultative:**
- Input gathered, but decision-maker decides
- Appropriate when expertise is concentrated

## Quality Checklist

- [ ] Decision context is clearly defined
- [ ] Decision criteria are identified and weighted
- [ ] All viable alternatives are considered
- [ ] Alternatives are evaluated against criteria
- [ ] Risks and consequences are assessed
- [ ] Decision is documented in an ADR
- [ ] Rationale is clear and evidence-based
- [ ] Stakeholders are consulted
- [ ] Decision-maker has approved
- [ ] ADR is stored in version control
- [ ] Team is informed of the decision
- [ ] Action items are created for implementation

## Common Mistakes

- **Analysis paralysis**: Over-analyzing reversible decisions
- **Gut decisions**: Making important decisions without analysis
- **Ignoring constraints**: Not considering budget, timeline, or team expertise
- **Confirmation bias**: Only considering evidence that supports a preferred option
- **Missing alternatives**: Not exploring enough options
- **No documentation**: Making decisions without recording rationale
- **Skipping stakeholders**: Not involving affected parties
- **Ignoring consequences**: Not thinking through second-order effects

## Examples

See [examples.md](examples.md) for detailed examples of architecture decisions for various scenarios.

## Related Skills

- **Requires**: 
  - requirements-analysis (to understand requirements)
- **Commonly followed by**: 
  - system-design (to design based on decision)
  - technical-design-document (to document the design)
  - migration-planning (if decision involves migration)
- **Alternative to**: None (this is the primary decision-making skill)
- **Works with**: 
  - tradeoff-analysis (for detailed comparison)
  - architecture-review (to validate decisions)
  - risk-analysis (to assess risks)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
architecture-decision
        ↓
system-design
        ↓
technical-design-document
```

With tradeoff analysis:

```
requirements-analysis
        ↓
tradeoff-analysis
        ↓
architecture-decision
        ↓
system-design
```

## Evaluation Criteria

### Decision Quality
- Is the decision based on evidence and analysis?
- Are all viable alternatives considered?
- Are criteria clearly defined and weighted?
- Are risks and consequences assessed?

### Documentation Quality
- Is the ADR clear and complete?
- Is the rationale well-explained?
- Are alternatives documented?
- Are consequences and risks captured?

### Process Quality
- Were stakeholders consulted?
- Was the right level of analysis applied?
- Was the decision made in a timely manner?
- Is there a plan for implementation?

### Outcome Quality
- Does the decision meet requirements?
- Are consequences acceptable?
- Does the team support the decision?
- Is the decision implementable within constraints?