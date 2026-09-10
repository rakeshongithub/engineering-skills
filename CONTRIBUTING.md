# Contributing to Engineering Skills Library

Thank you for your interest in contributing! This guide will help you create high-quality engineering skills.

## Before You Start

1. **Review existing skills** to understand the pattern
2. **Use the skill-authoring meta-skill** (`skills/meta/skill-authoring/`) as your guide
3. **Check the catalog** to avoid duplicates
4. **Start with a clear problem** that the skill solves

## Skill Creation Process

### 1. Define the Skill Purpose

- What engineering problem does it solve?
- When should someone use it?
- When should they NOT use it?
- What makes it different from similar skills?

### 2. Define Inputs and Outputs

**Inputs** should be:

- Specific and well-defined
- Practical and obtainable
- Necessary (not nice-to-have)

**Outputs** should be:

- Actionable
- Measurable
- Valuable

### 3. Create the Workflow

- Break down the skill into clear steps
- Include decision points
- Add quality checks
- Define success criteria

### 4. Add Examples

- Provide at least 2-3 realistic examples
- Show both simple and complex cases
- Include expected outputs

### 5. Define Relationships

- What skills does this depend on?
- What skills commonly follow this one?
- How does it compose with other skills?

## Adding Domain Examples

Domain examples should show a realistic engineering problem without depending on proprietary code or credentials. Prefer one Markdown file per scenario under `examples/<domain>/` and include:

- Context, constraints, and available evidence
- The workflow recipe and skills being composed
- Important decisions or handoff artifacts
- Expected outputs and quality gates

Add the scenario to `examples/README.md` so it can be discovered from the repository entry points.

## Standard Skill Structure

Every skill must include:

```
skills/<category>/<skill-name>/
├── SKILL.md          # Main skill documentation
├── skill.json        # Machine-readable metadata
├── instructions.md   # Detailed execution instructions
├── examples.md       # Practical examples
└── evals/           # Evaluation cases (optional)
```

### SKILL.md Template

```markdown
# Skill Name

## Purpose

[One-sentence description]

## When to Use

[Specific scenarios]

## When NOT to Use

[Anti-patterns and limitations]

## Inputs

- Input 1: Description
- Input 2: Description

## Expected Outputs

- Output 1: Description
- Output 2: Description

## Workflow

1. Step 1
2. Step 2
3. Step 3

## Decision Framework

[How to make key decisions]

## Quality Checklist

- [ ] Criterion 1
- [ ] Criterion 2

## Common Mistakes

- Mistake 1: Why it's wrong
- Mistake 2: Why it's wrong

## Examples

[See examples.md]

## Related Skills

- **Requires**: skill-name
- **Commonly followed by**: skill-name
- **Alternative to**: skill-name

## Skill Composition

[How this skill combines with others]

## Evaluation Criteria

[How to measure success]
```

### skill.json Template

```json
{
  "name": "skill-name",
  "category": "architecture|engineering|agentic|security|operations|meta",
  "version": "1.0.0",
  "description": "Brief description",
  "inputs": ["input-1", "input-2"],
  "outputs": ["output-1", "output-2"],
  "requires": ["dependency-skill-1"],
  "commonly_followed_by": ["next-skill-1", "next-skill-2"],
  "tags": ["tag1", "tag2"],
  "complexity": "basic|intermediate|advanced",
  "estimated_time": "15min|1hour|4hours|1day"
}
```

## Quality Standards

### Every skill must:

- [ ] Solve a clear, practical engineering problem
- [ ] Have well-defined inputs and outputs
- [ ] Include a repeatable workflow
- [ ] Provide decision criteria
- [ ] Include quality checks
- [ ] Have at least 2 realistic examples
- [ ] Define relationships to other skills
- [ ] Be understandable without reading the entire repository
- [ ] Be vendor-neutral (unless inherently technology-specific)
- [ ] Be executable by both humans and AI agents

### Documentation must:

- [ ] Be clear and concise
- [ ] Use consistent terminology
- [ ] Include practical examples
- [ ] Avoid unnecessary jargon
- [ ] Be actionable

### Metadata must:

- [ ] Be accurate and complete
- [ ] Follow the standard schema
- [ ] Include all relationships
- [ ] Use consistent naming

## Submission Process

1. **Create a branch** for your skill
2. **Follow the standard structure**
3. **Write comprehensive documentation**
4. **Add metadata** (skill.json)
5. **Include examples**
6. **Test with AI agents** (if possible)
7. **Submit a pull request**
8. **Respond to feedback**

## Review Criteria

Your contribution will be reviewed for:

- **Clarity**: Is the skill easy to understand?
- **Practicality**: Does it solve a real problem?
- **Completeness**: Are all required sections present?
- **Quality**: Does it meet the quality standards?
- **Uniqueness**: Does it add value beyond existing skills?
- **Composability**: Does it work well with other skills?
- **Executability**: Can it be used by both humans and AI?

## Getting Help

- Review the **skill-authoring meta-skill** for detailed guidance
- Look at **existing skills** for examples
- Check **workflow recipes** to understand skill composition
- Open an issue for questions

## Code of Conduct

- Be respectful and constructive
- Focus on engineering value
- Collaborate openly
- Help others learn

Thank you for contributing to making engineering expertise more accessible and reusable!
