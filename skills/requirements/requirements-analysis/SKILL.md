# Requirements Analysis

## Purpose

Transform vague or incomplete requirements into clear, actionable engineering requirements with well-defined acceptance criteria.

## When to Use

- When starting a new feature or project with unclear requirements
- When stakeholders provide high-level goals without technical details
- When requirements are ambiguous or contradictory
- When you need to bridge the gap between business needs and technical implementation
- Before beginning system design or architecture work

## When NOT to Use

- When requirements are already clear and well-documented
- For trivial changes that don't need formal analysis
- When working on internal refactoring without external requirements
- For emergency bug fixes requiring immediate action

## Inputs

- **initial-requirements**: Raw requirements from stakeholders (user stories, feature requests, business goals)
- **stakeholders**: Who is requesting this and who will be affected
- **context**: Existing system, business context, user needs, competitive landscape
- **constraints**: Technical limitations, budget, timeline, compliance requirements

## Expected Outputs

- **functional-requirements**: What the system must do (features, behaviors, capabilities)
- **non-functional-requirements**: How the system must perform (performance, security, scalability, usability)
- **acceptance-criteria**: Measurable conditions that define when requirements are met
- **assumptions**: What we're assuming to be true
- **open-questions**: What still needs clarification

## Workflow

### 1. Gather Initial Information

- Collect all available requirements documentation
- Identify all stakeholders and their roles
- Understand the business context and goals
- Review existing system (if applicable)
- Note any constraints or dependencies

### 2. Clarify the Problem

- What problem are we solving?
- Who has this problem?
- Why is this problem worth solving?
- What happens if we don't solve it?
- What does success look like?

### 3. Extract Functional Requirements

- What must the system do?
- What are the core capabilities?
- What are the user workflows?
- What are the edge cases?
- What are the integration points?

**Format:**
```
FR-1: The system shall [action] when [condition]
FR-2: Users must be able to [capability]
```

### 4. Identify Non-Functional Requirements

**Performance:**
- Response time requirements
- Throughput requirements
- Capacity requirements

**Security:**
- Authentication requirements
- Authorization requirements
- Data protection requirements
- Compliance requirements

**Scalability:**
- Expected load
- Growth projections
- Scaling requirements

**Reliability:**
- Availability requirements (uptime %)
- Disaster recovery requirements
- Data durability requirements

**Usability:**
- User experience requirements
- Accessibility requirements
- Browser/device support

**Maintainability:**
- Code quality standards
- Documentation requirements
- Testing requirements

### 5. Define Acceptance Criteria

For each requirement, define:

```
Given [context]
When [action]
Then [expected outcome]
```

**Make criteria:**
- Specific (not vague)
- Measurable (can be tested)
- Achievable (realistic)
- Relevant (tied to requirements)
- Testable (can be verified)

### 6. Identify Assumptions

- What are we assuming about users?
- What are we assuming about the system?
- What are we assuming about integrations?
- What are we assuming about data?
- What are we assuming about the environment?

**Document as:**
```
A-1: We assume [assumption]
A-2: We assume [assumption]
```

### 7. Document Open Questions

- What is still unclear?
- What needs stakeholder input?
- What needs technical investigation?
- What are the risks or unknowns?

**Format:**
```
Q-1: [Question] - [Who can answer] - [Priority: High/Medium/Low]
```

### 8. Validate and Prioritize

- Review requirements with stakeholders
- Confirm understanding is correct
- Prioritize requirements (Must-have, Should-have, Nice-to-have)
- Get sign-off on scope

## Decision Framework

### Requirement Priority

**Must-have (P0):**
- Core functionality without which the feature is useless
- Critical security or compliance requirements
- Blocking dependencies for other work

**Should-have (P1):**
- Important functionality that significantly improves the solution
- Performance requirements that affect user experience
- Integration requirements for key workflows

**Nice-to-have (P2):**
- Enhancements that add value but aren't essential
- Optimizations that improve but don't block
- Future-proofing capabilities

### Requirement Clarity

**Clear requirement:**
- Specific and unambiguous
- Measurable or testable
- Has defined acceptance criteria
- Stakeholders agree on meaning

**Unclear requirement:**
- Vague or open to interpretation
- No way to measure success
- Missing acceptance criteria
- Stakeholders have different interpretations

**Action:** Clarify before proceeding to design.

### Scope Management

**In scope:**
- Directly supports stated goals
- Feasible within constraints
- Agreed upon by stakeholders

**Out of scope:**
- Doesn't support core goals
- Not feasible within constraints
- Not agreed upon by stakeholders
- Can be deferred to future phases

## Quality Checklist

- [ ] All functional requirements are clearly stated
- [ ] Non-functional requirements are defined and measurable
- [ ] Each requirement has acceptance criteria
- [ ] Requirements are prioritized
- [ ] Assumptions are documented
- [ ] Open questions are identified
- [ ] Requirements are validated with stakeholders
- [ ] Scope is clearly defined (in-scope and out-of-scope)
- [ ] Requirements are testable
- [ ] Requirements are feasible within constraints
- [ ] Dependencies are identified
- [ ] Requirements are traceable to business goals

## Common Mistakes

- **Accepting vague requirements**: Not pushing back on unclear or ambiguous requirements
- **Skipping non-functional requirements**: Focusing only on features and ignoring performance, security, scalability
- **No acceptance criteria**: Not defining how to verify requirements are met
- **Assuming instead of asking**: Making assumptions instead of asking clarifying questions
- **Over-specifying solutions**: Defining implementation details instead of requirements
- **Ignoring constraints**: Not considering technical, budget, or timeline limitations
- **Missing stakeholder validation**: Not confirming understanding with stakeholders
- **Scope creep**: Allowing requirements to expand without proper evaluation

## Examples

See [examples.md](examples.md) for detailed examples of requirements analysis for various scenarios.

## Related Skills

- **Requires**: None (this is a foundational skill)
- **Commonly followed by**: 
  - system-design (to design the system)
  - architecture-design (to design the architecture)
  - technical-specification (to create implementation specs)
- **Alternative to**: None (this is the primary requirements analysis skill)
- **Works with**: 
  - requirement-clarification (for deeper clarification)
  - architecture-review (for validating requirements against architecture)

## Skill Composition

Typical workflow:

```
requirements-analysis
        ↓
system-design
        ↓
architecture-review
        ↓
security-review
        ↓
technical-specification
```

## Evaluation Criteria

### Completeness
- Are all functional requirements captured?
- Are non-functional requirements defined?
- Are acceptance criteria provided for each requirement?
- Are assumptions and open questions documented?

### Clarity
- Are requirements unambiguous?
- Can requirements be understood by both technical and non-technical stakeholders?
- Are acceptance criteria measurable?

### Quality
- Are requirements testable?
- Are requirements feasible?
- Are requirements prioritized?
- Are requirements validated with stakeholders?

### Actionability
- Can engineers design a system from these requirements?
- Are requirements specific enough to estimate effort?
- Are dependencies and constraints clear?
