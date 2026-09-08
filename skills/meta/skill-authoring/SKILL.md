# Skill Authoring

## Purpose

Teach contributors how to create high-quality, reusable engineering skills that follow the standard skill contract and can be effectively used by both humans and AI agents.

This meta-skill guides the entire process of skill creation, from identifying a problem to publishing a complete, well-documented skill.

## When to Use

- When you want to contribute a new skill to the library
- When you've identified a repeatable engineering capability that should be captured
- When you need to standardize an existing engineering practice
- When you want to teach others a specific engineering technique
- When you're creating skills for internal use or custom skill libraries

## When NOT to Use

- For one-off, non-repeatable tasks
- For problems that are too specific to a single project
- For generic advice that doesn't constitute a clear workflow
- When an existing skill already covers the capability
- For problems outside the engineering domain

## Inputs

- **engineering-problem**: The problem this skill will solve
- **skill-idea**: Initial concept for the skill
- **target-audience**: Who will use this skill (junior engineers, architects, AI agents, etc.)
- **skill-category**: Which category the skill belongs to (architecture, engineering, agentic, etc.)

## Expected Outputs

- **skill-definition**: Clear definition of the skill's purpose and scope
- **skill-documentation**: Complete SKILL.md file
- **skill-metadata**: skill.json file with machine-readable metadata
- **skill-examples**: examples.md file with practical examples
- **quality-validation**: Confirmation that the skill meets quality standards

## Workflow

### 1. Define the Skill Purpose

**Questions to answer:**
- What specific engineering problem does this skill solve?
- What makes this problem worth capturing as a skill?
- How is this different from existing skills?
- What value does this skill provide?

**Output:**
- One-sentence purpose statement
- Clear problem statement

**Example:**
```
Purpose: Systematically review API design to identify usability, 
security, and performance issues before implementation.

Problem: APIs are often designed without systematic review, leading 
to poor developer experience, security vulnerabilities, and 
performance issues that are expensive to fix after release.
```

### 2. Define When to Use (and When NOT to Use)

**When to Use:**
- List specific scenarios where this skill is valuable
- Be concrete and practical
- Include 3-5 scenarios

**When NOT to Use:**
- List anti-patterns and limitations
- Clarify boundaries
- Prevent misuse

**Example:**
```
When to Use:
- Before implementing a new REST API
- When refactoring an existing API
- When receiving complaints about API usability
- Before making breaking changes to an API

When NOT to Use:
- For internal, non-public APIs (unless critical)
- For trivial CRUD APIs with standard patterns
- When the API is already implemented and in production (use api-refactoring instead)
```

### 3. Define Inputs

**Requirements:**
- Inputs should be specific and well-defined
- Inputs should be obtainable (not hypothetical)
- Inputs should be necessary (not nice-to-have)
- Each input should have a clear purpose

**Format:**
```
- **input-name**: Description of what it is and why it's needed
```

**Example:**
```
Inputs:
- **api-specification**: OpenAPI/Swagger spec or equivalent documentation
- **use-cases**: Primary use cases the API should support
- **constraints**: Technical or business constraints (rate limits, auth requirements)
- **target-audience**: Who will use this API (internal, external, mobile, web)
```

### 4. Define Expected Outputs

**Requirements:**
- Outputs should be actionable
- Outputs should be measurable or verifiable
- Outputs should provide value
- Each output should have a clear purpose

**Format:**
```
- **output-name**: Description of what it is and how it will be used
```

**Example:**
```
Expected Outputs:
- **findings**: List of issues found (usability, security, performance)
- **recommendations**: Specific improvements with rationale
- **risk-assessment**: Severity and impact of each issue
- **action-items**: Prioritized list of changes to make
```

### 5. Create the Workflow

**Requirements:**
- Break the skill into clear, sequential steps
- Each step should have a clear purpose
- Include decision points where applicable
- Add quality checks
- Make it executable by both humans and AI

**Structure:**
```
1. Step name
   - What to do
   - How to do it
   - What to look for
   - Output of this step

2. Step name
   ...
```

**Tips:**
- Use action verbs (Analyze, Identify, Design, Validate)
- Be specific about what to do
- Include examples where helpful
- Add decision criteria

### 6. Add Decision Framework

**Purpose:**
- Help users make key decisions during skill execution
- Provide criteria for choices
- Reduce ambiguity

**Include:**
- Decision points in the workflow
- Criteria for making decisions
- Common patterns
- Tradeoffs to consider

**Example:**
```
Decision Framework:

API Style Selection:
- REST: For resource-oriented operations, public APIs
- GraphQL: For complex data requirements, flexible queries
- gRPC: For internal services, high performance needs
- WebSocket: For real-time, bidirectional communication

Versioning Strategy:
- URL versioning (/v1/): Simple, explicit, cache-friendly
- Header versioning: Cleaner URLs, more flexible
- Content negotiation: Most flexible, more complex
```

### 7. Create Quality Checklist

**Purpose:**
- Ensure the skill execution meets quality standards
- Provide validation criteria
- Enable self-assessment

**Format:**
```
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3
```

**Example:**
```
Quality Checklist:
- [ ] All API endpoints reviewed
- [ ] Security issues identified and assessed
- [ ] Performance implications considered
- [ ] Developer experience evaluated
- [ ] Error handling reviewed
- [ ] Documentation completeness checked
- [ ] Versioning strategy validated
- [ ] Breaking changes identified
```

### 8. Document Common Mistakes

**Purpose:**
- Help users avoid common pitfalls
- Share lessons learned
- Improve skill execution quality

**Format:**
```
- **Mistake**: Why it's wrong and how to avoid it
```

**Example:**
```
Common Mistakes:
- **Skipping authentication review**: Assuming auth is handled elsewhere; 
  always verify API-level auth
- **Ignoring rate limiting**: Not considering abuse scenarios; 
  always design rate limiting upfront
- **Over-designing**: Creating overly complex APIs for simple use cases; 
  start simple, add complexity only when needed
```

### 9. Add Examples

**Requirements:**
- Provide at least 2-3 realistic examples
- Show both simple and complex cases
- Include expected inputs and outputs
- Make examples practical and relatable

**Create examples.md with:**
- Example 1: Simple, straightforward case
- Example 2: Complex case with edge cases
- Example 3: Real-world scenario (if applicable)

### 10. Define Related Skills

**Purpose:**
- Show how this skill fits in the skill graph
- Enable skill composition
- Help users discover related skills

**Categories:**
- **Requires**: Skills that must be executed before this one
- **Commonly followed by**: Skills typically executed after this one
- **Alternative to**: Skills that solve similar problems differently
- **Works with**: Skills that complement this one

**Example:**
```
Related Skills:
- **Requires**: requirements-analysis (to understand API requirements)
- **Commonly followed by**: 
  - security-review (to validate security aspects)
  - technical-specification (to create implementation spec)
- **Alternative to**: None (this is the primary API review skill)
- **Works with**: 
  - architecture-review (for broader system context)
  - data-architecture-review (for data model validation)
```

### 11. Define Skill Composition

**Purpose:**
- Show how this skill combines with others
- Provide workflow examples
- Enable orchestration

**Example:**
```
Skill Composition:

Typical workflow for new API:
requirements-analysis
        ↓
api-design
        ↓
api-design-review (this skill)
        ↓
security-review
        ↓
technical-specification
        ↓
implementation
```

### 12. Add Evaluation Criteria

**Purpose:**
- Define how to measure skill execution success
- Enable quality assessment
- Support continuous improvement

**Include:**
- Completeness criteria
- Quality criteria
- Outcome criteria

**Example:**
```
Evaluation Criteria:

Completeness:
- All API endpoints reviewed?
- All use cases covered?
- All security aspects considered?

Quality:
- Issues clearly described?
- Recommendations actionable?
- Priorities justified?

Outcome:
- API design improved?
- Risks mitigated?
- Team aligned on changes?
```

### 13. Create Machine-Readable Metadata

**Create skill.json with:**
```json
{
  "name": "skill-name",
  "category": "architecture|engineering|agentic|security|operations|meta",
  "version": "1.0.0",
  "description": "Brief description",
  "inputs": ["input-1", "input-2"],
  "outputs": ["output-1", "output-2"],
  "requires": ["dependency-skill"],
  "commonly_followed_by": ["next-skill"],
  "tags": ["tag1", "tag2"],
  "complexity": "basic|intermediate|advanced",
  "estimated_time": "15min|1hour|4hours|1day"
}
```

### 14. Validate the Skill

**Validation Checklist:**

- [ ] **Purpose is clear**: Can someone understand the skill in 30 seconds?
- [ ] **Problem is real**: Does this solve an actual engineering problem?
- [ ] **Inputs are obtainable**: Can users realistically provide these inputs?
- [ ] **Outputs are valuable**: Will the outputs be useful?
- [ ] **Workflow is executable**: Can someone follow these steps?
- [ ] **Examples are realistic**: Do examples reflect real scenarios?
- [ ] **Quality criteria are clear**: Can someone validate their work?
- [ ] **Relationships are documented**: Is the skill graph connection clear?
- [ ] **Metadata is complete**: Is skill.json accurate?
- [ ] **No duplication**: Is this different from existing skills?

### 15. Test the Skill

**Testing methods:**

1. **Self-execution**: Execute the skill yourself on a real problem
2. **Peer review**: Have another engineer review and execute it
3. **AI agent test**: If possible, have an AI agent execute it
4. **Documentation review**: Ensure all sections are clear and complete

**Questions to answer:**
- Can the skill be executed as documented?
- Are there any ambiguities?
- Are there any missing steps?
- Are the examples helpful?
- Is the output valuable?

### 16. Finalize and Submit

**Final checklist:**

- [ ] SKILL.md is complete
- [ ] skill.json is accurate
- [ ] instructions.md is detailed (if needed)
- [ ] examples.md has 2-3 examples
- [ ] All sections follow the template
- [ ] Writing is clear and concise
- [ ] No typos or formatting issues
- [ ] Skill has been tested
- [ ] Skill adds unique value

**Submission:**
- Create a pull request
- Include rationale for the skill
- Reference any related issues
- Be responsive to feedback

## Decision Framework

### Is This Worth Creating as a Skill?

**Yes, if:**
- It solves a recurring engineering problem
- It has a clear, repeatable workflow
- It provides unique value
- It can be executed by different people/agents
- It has measurable outputs

**No, if:**
- It's a one-off task
- It's too specific to one project
- It's already covered by existing skills
- It's just generic advice without a workflow
- It can't be practically executed

### What Level of Detail?

**Basic skill:**
- Simple workflow (3-5 steps)
- Minimal decision points
- Straightforward examples
- Estimated time: 15min - 1 hour

**Intermediate skill:**
- Moderate workflow (5-10 steps)
- Some decision points
- Multiple examples
- Estimated time: 1-4 hours

**Advanced skill:**
- Complex workflow (10+ steps)
- Multiple decision points
- Comprehensive examples
- Estimated time: 4+ hours

### Should This Be Multiple Skills?

**Split into multiple skills if:**
- The workflow has distinct, independent phases
- Different parts require different expertise
- Some parts are optional or conditional
- The skill is becoming too complex

**Keep as one skill if:**
- Steps are tightly coupled
- Splitting would create artificial boundaries
- The workflow is naturally sequential
- Splitting would reduce clarity

## Quality Checklist

- [ ] Skill solves a clear, practical problem
- [ ] Purpose is stated in one sentence
- [ ] Inputs are specific and obtainable
- [ ] Outputs are actionable and valuable
- [ ] Workflow is clear and executable
- [ ] Decision framework is provided
- [ ] Quality checklist is included
- [ ] Common mistakes are documented
- [ ] At least 2 realistic examples are provided
- [ ] Related skills are identified
- [ ] Skill composition is shown
- [ ] Evaluation criteria are defined
- [ ] Metadata (skill.json) is complete and accurate
- [ ] Skill is understandable without reading the entire repository
- [ ] Skill is vendor-neutral (unless inherently technology-specific)
- [ ] Skill can be executed by both humans and AI agents
- [ ] Writing is clear, concise, and free of jargon
- [ ] Skill has been tested
- [ ] Skill adds unique value to the library

## Common Mistakes

- **Too generic**: Creating skills that are just general advice without clear workflows
- **Too specific**: Creating skills for one-off problems that won't be reused
- **Missing examples**: Not providing realistic examples that demonstrate the skill
- **Vague outputs**: Defining outputs that are too abstract or unmeasurable
- **Incomplete workflow**: Skipping important steps or decision points
- **No quality criteria**: Not defining how to validate skill execution
- **Ignoring relationships**: Not connecting the skill to others in the library
- **Poor metadata**: Creating inaccurate or incomplete skill.json
- **Not testing**: Submitting without executing the skill yourself
- **Duplicating existing skills**: Not checking if a similar skill already exists

## Examples

See [examples.md](examples.md) for detailed examples of creating different types of skills.

## Related Skills

- **Requires**: None (this is a foundational meta-skill)
- **Commonly followed by**: The newly created skill
- **Works with**: skill-orchestrator (for understanding how skills compose)
- **Alternative to**: None (this is the primary skill authoring guide)

## Skill Composition

This skill is typically used standalone when creating new skills. However, it can be part of a larger workflow:

```
Identify engineering problem
        ↓
skill-authoring (create the skill)
        ↓
Peer review
        ↓
Test the skill
        ↓
Submit to repository
        ↓
skill-orchestrator (use the skill in workflows)
```

## Evaluation Criteria

### Skill Quality
- Is the skill clear and understandable?
- Does it solve a real problem?
- Can it be executed as documented?
- Does it produce valuable outputs?

### Documentation Quality
- Is the documentation complete?
- Are examples realistic and helpful?
- Is the writing clear and concise?
- Is the metadata accurate?

### Uniqueness
- Does this skill add unique value?
- Is it different from existing skills?
- Does it fill a gap in the library?

### Reusability
- Can this skill be used by different people?
- Can it be used in different contexts?
- Can it be composed with other skills?
- Can AI agents execute it?

## Advanced Topics

### Creating Skill Families

Some skills naturally group together:

```
architecture-review (parent)
├── scalability-analysis (child)
├── reliability-analysis (child)
├── security-architecture-review (child)
└── data-architecture-review (child)
```

When creating skill families:
- Define the parent skill broadly
- Create child skills for specific aspects
- Show clear relationships
- Enable both standalone and composed usage

### Creating Conditional Skills

Some skills have conditional workflows:

```
Step 1: Analyze the system
        ↓
IF (monolith) → Step 2a: Monolith-specific analysis
IF (microservices) → Step 2b: Microservices-specific analysis
        ↓
Step 3: Continue with common steps
```

Document conditions clearly and provide guidance for each path.

### Creating Iterative Skills

Some skills require iteration:

```
Step 1: Design
        ↓
Step 2: Review
        ↓
IF (issues found) → REPEAT Step 1
IF (approved) → Step 3: Document
```

Define iteration criteria and exit conditions.

## Integration with AI Agents

When creating skills for AI agents:

1. **Be explicit**: Don't assume context; state everything clearly
2. **Use structured outputs**: Define exact output formats
3. **Provide examples**: Show expected inputs and outputs
4. **Include validation**: Add quality checks agents can execute
5. **Define constraints**: Specify boundaries and limitations
6. **Enable composition**: Show how the skill fits in workflows

## Conclusion

Creating high-quality skills requires:
- Clear problem understanding
- Structured thinking
- Practical examples
- Quality validation
- Continuous improvement

By following this skill-authoring process, you'll create skills that are:
- Practical and reusable
- Clear and executable
- Valuable to the community
- Composable with other skills
- Usable by both humans and AI agents

Welcome to the community of skill authors!