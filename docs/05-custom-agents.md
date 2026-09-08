# Integration with Custom AI Coding Agents

## Overview

This guide shows how to integrate engineering skills into custom AI coding agents using API/interface patterns.

## API/Interface Patterns for Skill Invocation

### Pattern 1: Skill Loader

```python
import yaml
import os
from pathlib import Path

class SkillLoader:
    def __init__(self, skills_path="engineering-skills/skills"):
        self.skills_path = Path(skills_path)
        self.catalog = self._load_catalog()

    def _load_catalog(self):
        catalog_path = Path("engineering-skills/catalog/skills.yaml")
        with open(catalog_path) as f:
            return yaml.safe_load(f)

    def load_skill(self, skill_name):
        """Load a skill by name"""
        skill_info = self._find_skill(skill_name)
        if not skill_info:
            raise ValueError(f"Skill {skill_name} not found")

        skill_path = Path(skill_info['path'])

        return {
            'metadata': skill_info,
            'skill': self._read_file(skill_path / 'SKILL.md'),
            'instructions': self._read_file(skill_path / 'instructions.md'),
            'examples': self._read_file(skill_path / 'examples.md')
        }

    def _find_skill(self, name):
        for skill in self.catalog['skills']:
            if skill['name'] == name:
                return skill
        return None

    def _read_file(self, path):
        if path.exists():
            with open(path) as f:
                return f.read()
        return None

    def get_skills_by_category(self, category):
        """Get all skills in a category"""
        return [s for s in self.catalog['skills'] if s['category'] == category]

    def search_skills(self, query):
        """Search skills by description or tags"""
        results = []
        query_lower = query.lower()

        for skill in self.catalog['skills']:
            if (query_lower in skill['description'].lower() or
                any(query_lower in tag for tag in skill.get('tags', []))):
                results.append(skill)

        return results

# Usage
loader = SkillLoader()
skill = loader.load_skill('architecture-review')
print(skill['metadata'])
print(skill['skill'])
```

### Pattern 2: Skill Executor

```python
from typing import Dict, Any, List
import anthropic

class SkillExecutor:
    def __init__(self, llm_client, skill_loader):
        self.llm = llm_client
        self.loader = skill_loader

    def execute_skill(self, skill_name: str, inputs: Dict[str, Any]) -> Dict[str, Any]:
        """Execute a single skill"""

        # Load skill
        skill = self.loader.load_skill(skill_name)

        # Prepare prompt
        system_prompt = f"""
        You are an expert engineer executing the {skill_name} skill.

        Skill Definition:
        {skill['skill']}

        Execution Instructions:
        {skill['instructions']}

        Follow the workflow exactly and produce outputs matching the expected format.
        """

        user_prompt = self._format_inputs(inputs)

        # Execute with LLM
        response = self.llm.messages.create(
            model="claude-3-5-sonnet-20241022",
            max_tokens=4096,
            system=system_prompt,
            messages=[{"role": "user", "content": user_prompt}]
        )

        return {
            'skill': skill_name,
            'inputs': inputs,
            'output': response.content[0].text,
            'metadata': skill['metadata']
        }

    def _format_inputs(self, inputs: Dict[str, Any]) -> str:
        """Format inputs for LLM"""
        formatted = []
        for key, value in inputs.items():
            formatted.append(f"{key}:\n{value}\n")
        return "\n".join(formatted)

# Usage
client = anthropic.Anthropic()
loader = SkillLoader()
executor = SkillExecutor(client, loader)

result = executor.execute_skill(
    'architecture-review',
    inputs={
        'architecture': 'Microservices with API Gateway...',
        'requirements': 'Must handle 1M requests/day...',
        'constraints': 'AWS only, budget $10K/month'
    }
)

print(result['output'])
```

### Pattern 3: Skill Orchestrator

```python
class SkillOrchestrator:
    def __init__(self, executor, loader):
        self.executor = executor
        self.loader = loader

    def orchestrate(self, problem: str, context: Dict[str, Any]) -> List[Dict[str, Any]]:
        """Use skill-orchestrator to determine and execute workflow"""

        # Step 1: Get workflow plan
        workflow_result = self.executor.execute_skill(
            'skill-orchestrator',
            inputs={
                'engineering-problem': problem,
                'context': str(context)
            }
        )

        # Step 2: Parse skill sequence from output
        skill_sequence = self._parse_workflow(workflow_result['output'])

        # Step 3: Execute skills in sequence
        results = []
        accumulated_context = context.copy()

        for skill_name in skill_sequence:
            print(f"Executing skill: {skill_name}")

            result = self.executor.execute_skill(
                skill_name,
                inputs=accumulated_context
            )

            results.append(result)

            # Add outputs to context for next skill
            accumulated_context[f'{skill_name}_output'] = result['output']

        return results

    def _parse_workflow(self, workflow_output: str) -> List[str]:
        """Extract skill names from workflow plan"""
        # Simple parser - look for skill names in catalog
        skill_names = [s['name'] for s in self.loader.catalog['skills']]
        found_skills = []

        for skill in skill_names:
            if skill in workflow_output:
                found_skills.append(skill)

        return found_skills

# Usage
orchestrator = SkillOrchestrator(executor, loader)

results = orchestrator.orchestrate(
    problem="Design a new real-time notification system",
    context={
        'current_system': 'Polling-based notifications',
        'scale': '100K concurrent users',
        'constraints': 'Must use existing AWS infrastructure'
    }
)

for result in results:
    print(f"\n=== {result['skill']} ===")
    print(result['output'])
```

## Configuration Examples

### Agent Configuration (YAML)

```yaml
agent:
  name: engineering-assistant

  skills:
    repository: engineering-skills
    auto_load: true

  capabilities:
    - skill-orchestration
    - skill-execution
    - skill-composition

  llm:
    provider: anthropic
    model: claude-3-5-sonnet-20241022
    max_tokens: 4096

  workflows:
    new-feature:
      - requirements-analysis
      - system-design
      - architecture-review
      - security-architecture-review
      - testing-strategy
      - production-readiness

    architecture-review:
      - architecture-discovery
      - architecture-review
      - scalability-analysis
      - reliability-analysis
      - security-architecture-review

    migration:
      - architecture-discovery
      - technical-debt-analysis
      - migration-planning
      - architecture-decision
      - testing-strategy
```

### Agent Configuration (JSON)

```json
{
  "agent": {
    "name": "engineering-assistant",
    "skills": {
      "repository": "engineering-skills",
      "catalog": "catalog/skills.yaml",
      "categories": ["architecture", "engineering", "security", "operations"]
    },
    "execution": {
      "mode": "sequential",
      "allow_parallel": true,
      "checkpoint": true,
      "retry_on_failure": 3
    },
    "output": {
      "format": "markdown",
      "include_metadata": true,
      "save_artifacts": true
    }
  }
}
```

## Integration with Other AI Development Tools

### General Integration Patterns

Most AI development tools can integrate with engineering skills using these patterns:

#### Pattern 1: File-Based Context

1. Add engineering-skills to your project/workspace
2. Reference skill files in prompts
3. Tool reads and uses skill content

**Compatible with:**
- Cody (Sourcegraph)
- Tabnine
- Amazon CodeWhisperer
- Any IDE-based AI assistant

#### Pattern 2: API-Based Integration

1. Create API wrapper around skills
2. Expose skills as API endpoints
3. AI tool calls API to access skills

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()
loader = SkillLoader()

class SkillRequest(BaseModel):
    skill_name: str
    inputs: dict

@app.get("/skills")
def list_skills():
    return loader.catalog['skills']

@app.get("/skills/{skill_name}")
def get_skill(skill_name: str):
    return loader.load_skill(skill_name)

@app.post("/skills/{skill_name}/execute")
def execute_skill(skill_name: str, request: SkillRequest):
    skill = loader.load_skill(skill_name)
    # Return skill content for AI to process
    return {
        'skill': skill['skill'],
        'instructions': skill['instructions'],
        'inputs': request.inputs
    }
```

#### Pattern 3: Plugin/Extension

Create tool-specific plugins - see examples in the full documentation.

## Compatibility Considerations

### Context Window Limitations

**Problem:** Some tools have limited context windows

**Solution:**
- Use skill summaries instead of full content
- Load only relevant sections (e.g., workflow, not examples)
- Use skill-orchestrator to select minimal skill set

### Token Optimization

```python
def load_skill_minimal(skill_name):
    """Load only essential parts of a skill"""
    skill = loader.load_skill(skill_name)

    # Extract only key sections
    return {
        'name': skill['metadata']['name'],
        'purpose': extract_section(skill['skill'], 'Purpose'),
        'workflow': extract_section(skill['skill'], 'Workflow'),
        'outputs': extract_section(skill['skill'], 'Expected Outputs')
    }

def extract_section(content, section_name):
    """Extract a specific markdown section"""
    lines = content.split('\n')
    in_section = False
    section_lines = []

    for line in lines:
        if line.startswith(f'## {section_name}'):
            in_section = True
            continue
        elif line.startswith('## ') and in_section:
            break
        elif in_section:
            section_lines.append(line)

    return '\n'.join(section_lines).strip()
```

## Related Documentation

- [Back to Overview](01-overview.md)
- [MCP Workflows](06-mcp-workflows.md)
- [Best Practices](07-best-practices.md)
- [Examples and Use Cases](09-examples.md)