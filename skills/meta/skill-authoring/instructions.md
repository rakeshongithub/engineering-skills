# Skill Authoring - Execution Instructions

This document provides step-by-step instructions for creating a new engineering skill.

## Overview

Skill authoring is the process of capturing a repeatable engineering capability as a structured, documented skill that can be used by both humans and AI agents.

## Prerequisites

- Understanding of the engineering problem you want to solve
- Familiarity with the skill structure (read SKILL.md template)
- Access to the repository structure
- Text editor for creating markdown and JSON files

## Quick Start Checklist

Before you begin, ensure you have:

- [ ] Identified a specific engineering problem
- [ ] Confirmed no existing skill covers this problem
- [ ] Determined the skill category
- [ ] Identified your target audience
- [ ] Allocated 2-4 hours for skill creation

## Step-by-Step Process

### Phase 1: Planning (30 minutes)

#### Step 1.1: Define the Problem

**Action**: Write down the engineering problem this skill will solve.

**Template**:
```
Problem: [Describe the problem in 2-3 sentences]

Why it matters: [Why is this problem worth solving?]

Current approach: [How do people solve this today?]

What's missing: [Why existing approaches are insufficient]
```

**Example**:
```
Problem: Teams often design APIs without systematic review, leading 
to poor developer experience, security vulnerabilities, and performance 
issues that are expensive to fix after release.

Why it matters: APIs are contracts that are hard to change once 
published. Poor API design leads to frustrated developers, security 
risks, and technical debt.

Current approach: Ad-hoc reviews, inconsistent criteria, often 
skipped due to time pressure.

What's missing: A systematic, repeatable process for reviewing APIs 
before implementation.
```

#### Step 1.2: Validate the Skill Idea

**Questions to answer**:

1. **Is this problem recurring?**
   - Will this skill be used more than once?
   - Do multiple teams face this problem?

2. **Is this problem clear and specific?**
   - Can you describe it in one sentence?
   - Is the scope well-defined?

3. **Does this skill already exist?**
   - Check the skills catalog
   - Review similar skills

4. **Can this be a repeatable workflow?**
   - Can you break it into steps?
   - Can different people execute it?

5. **Will the outputs be valuable?**
   - Will the outputs be actionable?
   - Will they solve the problem?

**Decision**: If you answered "yes" to all questions, proceed. Otherwise, refine your idea or choose a different problem.

#### Step 1.3: Choose the Category

**Categories**:
- `meta/` - Meta-skills (orchestration, authoring)
- `requirements/` - Requirements analysis and clarification
- `architecture/` - Architecture and design
- `engineering/` - Software engineering practices
- `security/` - Security reviews and design
- `agentic/` - AI agent development and workflows
- `operations/` - DevOps and platform engineering

**Action**: Choose the most appropriate category for your skill.

#### Step 1.4: Name the Skill

**Naming conventions**:
- Use lowercase with hyphens: `api-design-review`
- Be specific and descriptive
- Use action verbs where appropriate
- Keep it concise (2-4 words)

**Examples**:
- Good: `architecture-review`, `agent-workflow-design`, `security-review`
- Bad: `review`, `design-stuff`, `api-review-and-design-and-documentation`

#### Step 1.5: Create the Directory Structure

**Action**: Create the skill directory and files.

```bash
mkdir -p skills/<category>/<skill-name>
cd skills/<category>/<skill-name>
touch SKILL.md
touch skill.json
touch instructions.md
touch examples.md
mkdir examples
mkdir evals
```

**Result**: You should have:
```
skills/<category>/<skill-name>/
├── SKILL.md
├── skill.json
├── instructions.md
├── examples.md
├── examples/
└── evals/
```

---

### Phase 2: Core Documentation (60-90 minutes)

#### Step 2.1: Write the Purpose Statement

**Action**: Write a one-sentence purpose statement.

**Template**:
```
[Action verb] [object] to [outcome]
```

**Examples**:
- "Review API design to identify usability, security, and performance issues before implementation"
- "Decompose complex engineering problems into agent-sized tasks for AI agent execution"
- "Analyze system architecture to identify scalability bottlenecks and recommend solutions"

**Tips**:
- Start with an action verb (Review, Analyze, Design, Identify, etc.)
- Be specific about what and why
- Focus on the outcome

#### Step 2.2: Define When to Use

**Action**: List 3-5 specific scenarios where this skill is valuable.

**Template**:
```
## When to Use

- [Specific scenario 1]
- [Specific scenario 2]
- [Specific scenario 3]
- [Specific scenario 4]
- [Specific scenario 5]
```

**Tips**:
- Be concrete and specific
- Use real-world scenarios
- Think about different contexts

#### Step 2.3: Define When NOT to Use

**Action**: List anti-patterns and limitations.

**Template**:
```
## When NOT to Use

- [Anti-pattern or limitation 1]
- [Anti-pattern or limitation 2]
- [Anti-pattern or limitation 3]
```

**Tips**:
- Clarify boundaries
- Prevent misuse
- Suggest alternatives where applicable

#### Step 2.4: Define Inputs

**Action**: List all required inputs.

**Template**:
```
## Inputs

- **input-name**: Description of what it is and why it's needed
- **input-name**: Description of what it is and why it's needed
```

**Validation**:
- [ ] Each input is specific and well-defined
- [ ] Each input is obtainable (not hypothetical)
- [ ] Each input is necessary (not nice-to-have)
- [ ] Each input has a clear purpose

#### Step 2.5: Define Expected Outputs

**Action**: List all expected outputs.

**Template**:
```
## Expected Outputs

- **output-name**: Description of what it is and how it will be used
- **output-name**: Description of what it is and how it will be used
```

**Validation**:
- [ ] Each output is actionable
- [ ] Each output is measurable or verifiable
- [ ] Each output provides value
- [ ] Each output has a clear purpose

#### Step 2.6: Create the Workflow

**Action**: Break the skill into clear, sequential steps.

**Template**:
```
## Workflow

### 1. [Step Name]

[What to do in this step]

**Actions**:
- Action 1
- Action 2

**Look for**:
- Thing 1
- Thing 2

**Output**: [What this step produces]

### 2. [Step Name]

[Continue for all steps]
```

**Tips**:
- Use action verbs for step names
- Be specific about what to do
- Include decision points
- Add quality checks
- Make it executable

**Typical workflow structure**:
1. Gather inputs / Understand context
2. Analyze / Investigate
3. Identify issues / Opportunities
4. Design / Plan solutions
5. Validate / Review
6. Document / Deliver outputs

#### Step 2.7: Add Decision Framework

**Action**: Provide guidance for key decisions.

**Template**:
```
## Decision Framework

### [Decision Point Name]

**When to choose Option A**:
- Criterion 1
- Criterion 2

**When to choose Option B**:
- Criterion 1
- Criterion 2

**Tradeoffs**:
- Option A: [Pros and cons]
- Option B: [Pros and cons]
```

**Include**:
- Common decision points
- Selection criteria
- Tradeoffs
- Examples

#### Step 2.8: Create Quality Checklist

**Action**: Define validation criteria.

**Template**:
```
## Quality Checklist

- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]
- [ ] [Criterion 4]
- [ ] [Criterion 5]
```

**Categories to cover**:
- Completeness (Did we cover everything?)
- Correctness (Is it accurate?)
- Quality (Does it meet standards?)
- Actionability (Can we act on this?)

#### Step 2.9: Document Common Mistakes

**Action**: List common pitfalls and how to avoid them.

**Template**:
```
## Common Mistakes

- **[Mistake name]**: [Why it's wrong] → [How to avoid it]
- **[Mistake name]**: [Why it's wrong] → [How to avoid it]
```

**Tips**:
- Think about what could go wrong
- Include lessons learned
- Provide corrective guidance

---

### Phase 3: Examples and Relationships (45-60 minutes)

#### Step 3.1: Create Examples

**Action**: Write 2-3 realistic examples in examples.md.

**Structure for each example**:
```markdown
## Example [N]: [Title]

### Problem Statement
[Describe the specific problem]

### Context
[Provide relevant background]

### Inputs
[List the actual inputs for this example]

### Execution
[Show how the skill is executed step-by-step]

### Outputs
[Show the actual outputs produced]

### Lessons Learned
[What insights came from this example]
```

**Example types to include**:
1. **Simple example**: Straightforward, common case
2. **Complex example**: Edge cases, complications
3. **Real-world example**: Actual scenario (anonymized if needed)

#### Step 3.2: Define Related Skills

**Action**: Identify skill relationships.

**Template**:
```
## Related Skills

- **Requires**: [Skills that must be executed before this one]
- **Commonly followed by**: [Skills typically executed after this one]
- **Alternative to**: [Skills that solve similar problems differently]
- **Works with**: [Skills that complement this one]
```

**Tips**:
- Review the skills catalog
- Think about the workflow
- Consider dependencies

#### Step 3.3: Define Skill Composition

**Action**: Show how this skill combines with others.

**Template**:
```
## Skill Composition

### Typical workflow:
```
skill-1
    ↓
this-skill
    ↓
skill-2
```

### Alternative workflow:
```
skill-a
    ↓
this-skill
    ↓
skill-b
```
```

**Include**:
- Common workflows
- Alternative compositions
- Parallel execution (if applicable)

#### Step 3.4: Add Evaluation Criteria

**Action**: Define success metrics.

**Template**:
```
## Evaluation Criteria

### Completeness
- [Criterion 1]
- [Criterion 2]

### Quality
- [Criterion 1]
- [Criterion 2]

### Outcome
- [Criterion 1]
- [Criterion 2]
```

**Questions to answer**:
- How do we know the skill was executed well?
- What makes a good vs. poor execution?
- What outcomes indicate success?

---

### Phase 4: Metadata and Validation (30 minutes)

#### Step 4.1: Create skill.json

**Action**: Create machine-readable metadata.

**Template**:
```json
{
  "name": "skill-name",
  "category": "architecture",
  "version": "1.0.0",
  "description": "Brief description (one sentence)",
  "inputs": [
    "input-1",
    "input-2"
  ],
  "outputs": [
    "output-1",
    "output-2"
  ],
  "requires": [
    "dependency-skill"
  ],
  "commonly_followed_by": [
    "next-skill-1",
    "next-skill-2"
  ],
  "tags": [
    "tag1",
    "tag2"
  ],
  "complexity": "intermediate",
  "estimated_time": "2 hours"
}
```

**Field guidelines**:
- `name`: Use the skill directory name
- `category`: Match the directory structure
- `version`: Start with "1.0.0"
- `description`: One sentence from the purpose statement
- `inputs`: Match SKILL.md exactly
- `outputs`: Match SKILL.md exactly
- `requires`: List dependency skills (can be empty)
- `commonly_followed_by`: List typical next skills
- `tags`: Add relevant keywords
- `complexity`: basic | intermediate | advanced
- `estimated_time`: Realistic time estimate

#### Step 4.2: Validate the Skill

**Validation checklist**:

**Content validation**:
- [ ] Purpose is clear and one sentence
- [ ] When to use has 3-5 scenarios
- [ ] When NOT to use is documented
- [ ] Inputs are specific and obtainable
- [ ] Outputs are actionable and valuable
- [ ] Workflow has clear steps
- [ ] Decision framework is provided
- [ ] Quality checklist is included
- [ ] Common mistakes are documented
- [ ] Examples are realistic (2-3 examples)
- [ ] Related skills are identified
- [ ] Skill composition is shown
- [ ] Evaluation criteria are defined

**Metadata validation**:
- [ ] skill.json is valid JSON
- [ ] All required fields are present
- [ ] Inputs/outputs match SKILL.md
- [ ] Category is correct
- [ ] Tags are relevant
- [ ] Estimated time is realistic

**Quality validation**:
- [ ] Writing is clear and concise
- [ ] No typos or grammar errors
- [ ] Formatting is consistent
- [ ] Links work (if any)
- [ ] Examples are complete

#### Step 4.3: Test the Skill

**Action**: Execute the skill yourself on a real or realistic problem.

**Testing process**:
1. Choose a real problem the skill should solve
2. Gather the required inputs
3. Follow the workflow step-by-step
4. Document any issues or ambiguities
5. Verify the outputs are valuable
6. Refine the skill based on learnings

**Questions to answer**:
- Can the skill be executed as documented?
- Are there any missing steps?
- Are there any ambiguities?
- Do the outputs solve the problem?
- Would someone else be able to execute this?

#### Step 4.4: Peer Review (Optional but Recommended)

**Action**: Have another engineer review the skill.

**Review focus areas**:
- Clarity: Is it easy to understand?
- Completeness: Is anything missing?
- Practicality: Can it be executed?
- Value: Does it solve a real problem?
- Uniqueness: Is it different from existing skills?

---

### Phase 5: Finalization (15 minutes)

#### Step 5.1: Final Quality Check

**Action**: Review the complete skill package.

**Files to check**:
- [ ] SKILL.md is complete
- [ ] skill.json is accurate
- [ ] instructions.md is detailed (if created)
- [ ] examples.md has 2-3 examples
- [ ] All sections follow the template
- [ ] Writing is clear and concise
- [ ] No typos or formatting issues

#### Step 5.2: Create a README (Optional)

**Action**: If the skill is complex, create a README.md in the skill directory.

**Include**:
- Quick overview
- Links to main files
- Quick start guide

#### Step 5.3: Update the Catalog

**Action**: Add the skill to `catalog/skills.yaml` (if it exists).

**Format**:
```yaml
- name: skill-name
  category: category-name
  path: skills/category/skill-name
  description: Brief description
  version: 1.0.0
```

#### Step 5.4: Submit

**Action**: Create a pull request or commit the skill.

**PR description should include**:
- What problem this skill solves
- Why it's needed
- How it's different from existing skills
- Any testing performed

---

## Templates

### Minimal SKILL.md Template

```markdown
# [Skill Name]

## Purpose
[One sentence]

## When to Use
- [Scenario 1]
- [Scenario 2]
- [Scenario 3]

## When NOT to Use
- [Anti-pattern 1]
- [Anti-pattern 2]

## Inputs
- **input-1**: [Description]
- **input-2**: [Description]

## Expected Outputs
- **output-1**: [Description]
- **output-2**: [Description]

## Workflow

### 1. [Step Name]
[Description]

### 2. [Step Name]
[Description]

## Quality Checklist
- [ ] [Criterion 1]
- [ ] [Criterion 2]

## Common Mistakes
- **[Mistake]**: [Why wrong] → [How to avoid]

## Examples
See [examples.md](examples.md)

## Related Skills
- **Requires**: [skill-name]
- **Commonly followed by**: [skill-name]

## Evaluation Criteria
[How to measure success]
```

### Minimal skill.json Template

```json
{
  "name": "skill-name",
  "category": "category",
  "version": "1.0.0",
  "description": "Brief description",
  "inputs": ["input-1", "input-2"],
  "outputs": ["output-1", "output-2"],
  "requires": [],
  "commonly_followed_by": [],
  "tags": ["tag1", "tag2"],
  "complexity": "intermediate",
  "estimated_time": "1 hour"
}
```

## Tips for Success

### Writing Tips

1. **Be specific**: Avoid vague language
2. **Be concise**: Every word should add value
3. **Be practical**: Focus on execution, not theory
4. **Be clear**: Assume no prior context
5. **Be consistent**: Use the same terminology throughout

### Common Pitfalls to Avoid

1. **Too generic**: "Review the code" vs. "Review API design for security, usability, and performance"
2. **Too specific**: "Review the login API for our mobile app" vs. "Review API design"
3. **Missing examples**: Examples make skills concrete and understandable
4. **Vague outputs**: "Recommendations" vs. "Prioritized list of API improvements with rationale"
5. **Incomplete workflow**: Skipping important steps or decision points

### Quality Indicators

**Good skill**:
- Can be understood in 5 minutes
- Can be executed by someone new to it
- Produces valuable, actionable outputs
- Has clear examples
- Fits well in the skill graph

**Poor skill**:
- Requires extensive context to understand
- Has ambiguous or missing steps
- Produces vague outputs
- Lacks examples
- Duplicates existing skills

## Troubleshooting

### Problem: The skill feels too generic
**Solution**: Add more specific criteria, examples, and decision points. Focus on a narrower problem.

### Problem: The skill feels too specific
**Solution**: Generalize the problem. Remove project-specific details. Focus on the repeatable pattern.

### Problem: The workflow is too long
**Solution**: Consider splitting into multiple skills or grouping steps into phases.

### Problem: Hard to define clear outputs
**Solution**: Think about what would make the skill execution successful. What deliverable would be valuable?

### Problem: Can't find related skills
**Solution**: Think about what comes before (what inputs do you need?) and what comes after (who uses your outputs?).

## Conclusion

Creating a skill takes 2-4 hours but provides lasting value. Follow this process, use the templates, and don't hesitate to iterate based on feedback.

Welcome to the community of skill authors!