# Integration with Claude (Anthropic)

## Overview

Claude can leverage engineering skills through multiple methods: file uploads, Projects feature, and API integration.

## Using Skills with Claude Desktop App

### Method 1: Upload Skill Files as Context

1. Open Claude Desktop
2. Upload the specific skill file(s) you need:
   - `SKILL.md` for overview
   - `instructions.md` for detailed workflow
   - `examples.md` for reference examples

3. Reference them in your prompt:

```
I've uploaded the architecture-review skill documentation.

Please review this architecture following the workflow in the skill:

[Architecture description or diagram]
```

### Method 2: Copy Skill Content into Prompt

For shorter skills, copy the relevant sections:

```
I need you to act as an expert architect using this skill:

[Paste SKILL.md content]

Now apply this skill to:
[Your specific problem]
```

### Method 3: Use Projects Feature

Claude Projects allow persistent context:

1. Create a project: "Engineering Skills"
2. Add frequently used skills to project knowledge
3. Reference skills naturally in conversation:

```
Use the scalability-analysis skill to evaluate this architecture.
```

## Using Skills with Claude API

### Programmatic Skill Invocation

```python
import anthropic
import os

# Read skill content
def load_skill(skill_name):
    skill_path = f"engineering-skills/skills/{skill_name}/SKILL.md"
    with open(skill_path, 'r') as f:
        return f.read()

# Create client
client = anthropic.Anthropic(api_key=os.environ.get("ANTHROPIC_API_KEY"))

# Load skill
architecture_review_skill = load_skill("architecture/architecture-review")

# Create message with skill context
message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=4096,
    system=f"""You are an expert software architect.
    Use the following skill to guide your analysis:

    {architecture_review_skill}

    Follow the workflow exactly and produce outputs matching the expected format.""",
    messages=[
        {
            "role": "user",
            "content": """Review this architecture:

            System: Microservices-based e-commerce platform
            Components: API Gateway, User Service, Product Service, Order Service
            Database: PostgreSQL per service
            Cache: Redis
            Message Queue: RabbitMQ
            """
        }
    ]
)

print(message.content[0].text)
```

### Skill Orchestration with API

```python
def orchestrate_skills(problem, context):
    """Use skill-orchestrator to determine workflow"""

    orchestrator_skill = load_skill("meta/skill-orchestrator")

    # Get workflow plan
    workflow = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=2048,
        system=f"""You are a skill orchestrator.
        {orchestrator_skill}

        Determine which skills to use and in what order.""",
        messages=[{
            "role": "user",
            "content": f"Problem: {problem}\n\nContext: {context}"
        }]
    )

    return workflow.content[0].text

# Example usage
workflow = orchestrate_skills(
    problem="Migrate monolith to microservices",
    context="Legacy Java application, 500K LOC, PostgreSQL database"
)
print(workflow)
```

## Context Management Tips for Claude

### 1. Use System Prompts for Skills

- Place skill instructions in system prompt
- Keeps skill context persistent across conversation

### 2. Chain Skills Explicitly

```
First, apply requirements-analysis skill to these requirements.
Then, use the output as input to system-design skill.
```

### 3. Reference Skill Sections

```
Follow the "Decision Framework" section of the tradeoff-analysis skill.
```

### 4. Request Skill-Compliant Outputs

```
Produce outputs exactly matching the "Expected Outputs" section of the skill.
```

## Best Practices

1. **Load skills in system prompt** - Maintains context across conversation
2. **Be explicit about workflow** - State which skill sections to follow
3. **Provide all required inputs** - Check skill's input requirements
4. **Request structured outputs** - Reference expected output format
5. **Use Projects for frequent skills** - Saves time and tokens

## Example Workflows

### Architecture Review

```python
# Complete architecture review workflow
client = anthropic.Anthropic()

# Load skill
skill = load_skill("architecture/architecture-review")

# Execute review
review = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=4096,
    system=f"""You are an expert architect.
    
    {skill}
    
    Follow the workflow exactly.""",
    messages=[{
        "role": "user",
        "content": """Review this architecture:
        
        [Architecture details]
        """
    }]
)

print(review.content[0].text)
```

### Multi-Skill Workflow

```python
# Sequential skill execution
skills = [
    "requirements/requirements-analysis",
    "architecture/system-design",
    "security/security-architecture-review"
]

context = "Initial requirements..."

for skill_name in skills:
    skill = load_skill(skill_name)
    
    result = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=4096,
        system=f"Execute this skill: {skill}",
        messages=[{
            "role": "user",
            "content": context
        }]
    )
    
    # Use output as input for next skill
    context = result.content[0].text
    print(f"\n=== {skill_name} ===")
    print(context)
```

## Related Documentation

- [Back to Overview](01-overview.md)
- [GitHub Copilot Integration](02-github-copilot-integration.md)
- [Cursor Integration](04-cursor-integration.md)
- [Custom AI Agents](05-custom-agents.md)