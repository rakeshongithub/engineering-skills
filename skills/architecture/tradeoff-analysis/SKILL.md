# Tradeoff Analysis

## Purpose

Systematically compare architectural alternatives by evaluating tradeoffs across multiple dimensions to support informed decision-making.

## When to Use

- When choosing between 2-5 viable architectural options
- When decisions involve complex tradeoffs across multiple quality attributes
- Before making architecture-decision to provide detailed comparison
- When stakeholders need objective comparison of alternatives
- When the team is divided on which approach to take

## When NOT to Use

- When there's only one viable option
- For trivial decisions with obvious answers
- When the decision has already been made (use architecture-decision to document it)
- For implementation details that don't involve significant tradeoffs

## Inputs

- **alternatives**: 2-5 options being considered
- **evaluation-criteria**: Quality attributes and requirements to evaluate against
- **constraints**: Budget, timeline, team expertise, compliance requirements
- **context**: Business goals, scale requirements, existing architecture

## Expected Outputs

- **tradeoff-matrix**: Structured comparison of alternatives across criteria
- **scoring-analysis**: Quantitative scores for each option
- **tradeoff-summary**: Key tradeoffs and insights
- **recommendation**: Suggested option(s) with rationale

## Workflow

### 1. Define Alternatives

For each option:
- **Name**: Clear, descriptive name
- **Description**: Brief overview (2-3 sentences)
- **Key characteristics**: What makes this option unique
- **Rough effort estimate**: Small/Medium/Large

### 2. Identify Evaluation Criteria

**Quality Attributes:**
- **Performance**: Response time, throughput, latency
- **Scalability**: Ability to handle growth
- **Reliability**: Availability, fault tolerance
- **Security**: Authentication, authorization, data protection
- **Maintainability**: Code quality, modularity, documentation
- **Operability**: Ease of deployment, monitoring, debugging
- **Cost**: Infrastructure, licensing, development time

**Other Criteria:**
- **Time to implement**: How long will it take?
- **Team expertise**: Do we have the skills?
- **Vendor lock-in**: How portable is the solution?
- **Community support**: Is there good documentation and support?
- **Future-proofing**: Will this scale with our needs?

**Assign weights:**
- High: Critical to success
- Medium: Important but not critical
- Low: Nice to have

### 3. Score Each Alternative

**Scoring scale: 1-10**
- 1-3: Poor (does not meet needs)
- 4-6: Acceptable (meets minimum requirements)
- 7-8: Good (exceeds requirements)
- 9-10: Excellent (far exceeds requirements)

**For each criterion:**
- Score each alternative objectively
- Provide brief justification
- Note any assumptions

### 4. Create Tradeoff Matrix

| Criterion | Weight | Option 1 | Option 2 | Option 3 |
|-----------|--------|----------|----------|----------|
| Performance | High | 8/10 | 6/10 | 9/10 |
| Scalability | High | 7/10 | 9/10 | 8/10 |
| Cost | High | 5/10 | 9/10 | 6/10 |
| Maintainability | Medium | 7/10 | 8/10 | 6/10 |
| Time to implement | Medium | 6/10 | 9/10 | 4/10 |
| Team expertise | Medium | 9/10 | 7/10 | 5/10 |
| **Weighted Score** | | **7.2** | **8.0** | **6.8** |

### 5. Analyze Tradeoffs

**For each alternative, identify:**

**Strengths:**
- What does this option do well?
- Where does it excel?

**Weaknesses:**
- What are the limitations?
- Where does it fall short?

**Tradeoffs:**
- What are you gaining?
- What are you giving up?
- Are the tradeoffs acceptable?

### 6. Consider Context and Constraints

**Must-have constraints:**
- Does any option fail to meet critical requirements? (Eliminate it)

**Important constraints:**
- Budget limitations
- Timeline constraints
- Team expertise
- Compliance requirements

**Risk factors:**
- Technical risks
- Schedule risks
- Team risks
- Business risks

### 7. Develop Recommendation

**Recommended option:**
- Which option scores highest?
- Which option best fits the context?
- Which option has acceptable tradeoffs?

**Rationale:**
- Why is this the best choice?
- What are the key factors?
- What tradeoffs are we accepting?

**Alternative if constraints change:**
- If budget increases, would we choose differently?
- If timeline extends, would we choose differently?

### 8. Document and Present

**Executive summary:**
- Options considered
- Recommended option
- Key tradeoffs
- Next steps

**Detailed analysis:**
- Full tradeoff matrix
- Scoring justifications
- Risk assessment
- Implementation considerations

## Decision Framework

### When Scores Are Close

If multiple options score similarly:

**Consider:**
- Which option better aligns with long-term strategy?
- Which option has lower risk?
- Which option is more reversible?
- Which option has team buy-in?

**Approach:**
- Focus on highest-weighted criteria
- Consider qualitative factors
- Prototype or spike if needed

### When One Criterion Dominates

If one criterion is far more important than others:

**Approach:**
- Ensure all options meet minimum requirements for other criteria
- Choose the option that excels in the dominant criterion
- Validate that tradeoffs are acceptable

### When Team Is Divided

If the team disagrees on the best option:

**Approach:**
- Review scoring objectively
- Identify sources of disagreement
- Consider time-boxed prototype
- Use consent-based decision-making

## Quality Checklist

- [ ] All viable alternatives are included
- [ ] Evaluation criteria are comprehensive and relevant
- [ ] Criteria are weighted appropriately
- [ ] Scoring is objective and justified
- [ ] Tradeoffs are clearly articulated
- [ ] Constraints are considered
- [ ] Risks are assessed
- [ ] Recommendation is clear and well-supported
- [ ] Analysis is validated with team
- [ ] Documentation is clear and accessible

## Common Mistakes

- **Confirmation bias**: Scoring to favor a preferred option
- **Too many criteria**: Diluting the analysis with low-value criteria
- **Equal weighting**: Not differentiating between critical and nice-to-have criteria
- **Subjective scoring**: Not providing evidence or justification
- **Ignoring constraints**: Not eliminating infeasible options
- **Analysis paralysis**: Over-analyzing when a decision should be made
- **Missing alternatives**: Not considering all viable options
- **No recommendation**: Presenting analysis without a clear recommendation

## Examples

See [examples.md](examples.md) for detailed examples of tradeoff analysis for various scenarios.

## Related Skills

- **Requires**: 
  - requirements-analysis (to understand requirements)
- **Commonly followed by**: 
  - architecture-decision (to make and document the decision)
  - system-design (to design based on decision)
- **Alternative to**: None (this is the primary tradeoff analysis skill)
- **Works with**: 
  - architecture-review (to evaluate current state)
  - risk-analysis (to assess risks)
  - cost-analysis (to evaluate costs)

## Skill Composition

Typical workflow:

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

### Completeness
- Are all viable alternatives included?
- Are evaluation criteria comprehensive?
- Are all criteria scored?
- Are tradeoffs clearly identified?

### Objectivity
- Is scoring based on evidence?
- Are justifications provided?
- Is bias minimized?
- Are assumptions documented?

### Clarity
- Is the tradeoff matrix easy to understand?
- Are tradeoffs clearly articulated?
- Is the recommendation clear?
- Is the rationale well-explained?

### Actionability
- Can decision-makers act on the analysis?
- Is the recommendation specific?
- Are next steps identified?
- Are risks and mitigations clear?