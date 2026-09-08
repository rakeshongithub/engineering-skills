# Engineering Skills Library - Usage Guide

## Overview

The **Engineering Skills Library** is an open-source, composable collection of engineering capabilities designed for architecture, software engineering, and agentic development. Each skill represents a practical, repeatable engineering capability with:

- Clear problem statements and purpose
- Well-defined inputs and outputs
- Repeatable workflows and decision frameworks
- Quality checklists and evaluation criteria
- Real-world examples and use cases
- Relationships to other skills for composition

### What Makes This Different

This is **not** a collection of generic AI prompts. It's a **composable engineering skill system** that combines:

```
Individual Skills + Skill Metadata + Skill Relationships + 
Skill Orchestration + Workflow Recipes + Evaluation = 
Composable Engineering Skill System
```

### Available Skills

The library includes skills across multiple categories:

- **Meta Skills**: Skill orchestration and authoring
- **Requirements**: Requirements analysis and clarification
- **Architecture**: System design, architecture review, tradeoff analysis, scalability, reliability
- **Engineering**: Code review, refactoring, testing strategy, migration planning, technical debt
- **Security**: Security architecture review, threat modeling
- **Operations**: Production readiness, deployment strategies
- **Documentation**: Technical design documents, ADRs

For a complete list, see [`catalog/skills.yaml`](catalog/skills.yaml).

---

## Integration with GitHub Copilot

### How to Use Skills with Copilot

GitHub Copilot can leverage these skills through context-aware prompts in VS Code, GitHub.com, or the CLI.

#### Method 1: Direct Skill Reference in Chat

```
@workspace I need to perform an architecture review. 
Use the architecture-review skill from the engineering-skills library.

Context:
- System: E-commerce platform
- Technology: Node.js, React, PostgreSQL
- Scale: 100K daily users
```

#### Method 2: Include Skill Files in Workspace

1. Clone or add the engineering-skills repository to your workspace
2. Reference specific skills in your prompts:

```
@workspace Review my API design using the guidelines from 
/engineering-skills/skills/architecture/api-design-review/SKILL.md

API to review: /src/api/users.ts
```

#### Method 3: Use Skill Instructions Directly

```
Follow the workflow from 
/engineering-skills/skills/engineering/code-review/instructions.md 
to review this pull request.

Files changed:
- src/services/payment.ts
- src/models/transaction.ts
```

### Best Practices for Copilot

1. **Be Explicit About Skill Usage**
   ```
   Use the system-design skill to convert these requirements into a system design:
   [requirements]
   ```

2. **Provide Required Context**
   - Include the skill's required inputs
   - Reference relevant code files
   - Specify constraints and goals

3. **Leverage Skill Composition**
   ```
   First use requirements-analysis to clarify these requirements,
   then use system-design to create the architecture.
   ```

4. **Use the Orchestrator for Complex Problems**
   ```
   Use the skill-orchestrator to determine which skills I need for:
   "Migrating our monolith to microservices"
   ```

### Example Copilot Workflows

#### New Feature Development

```
@workspace I need to design a new authentication system.

1. Use requirements-analysis skill to clarify requirements
2. Use system-design skill to create the architecture
3. Use security-architecture-review to validate security
4. Use api-design-review for the API endpoints
5. Use testing-strategy to plan testing

Requirements:
- OAuth2 and JWT support
- Multi-factor authentication
- Session management
```

#### Code Review

```
@workspace Perform a code review using the code-review skill.

Follow the quality checklist from:
/engineering-skills/skills/engineering/code-review/SKILL.md

Files: #file:src/components/UserProfile.tsx
```

---

## Integration with Claude (Anthropic)

### Using Skills with Claude Desktop App

#### Method 1: Upload Skill Files as Context

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

#### Method 2: Copy Skill Content into Prompt

For shorter skills, copy the relevant sections:

```
I need you to act as an expert architect using this skill:

[Paste SKILL.md content]

Now apply this skill to:
[Your specific problem]
```

#### Method 3: Use Projects Feature

Claude Projects allow persistent context:

1. Create a project: "Engineering Skills"
2. Add frequently used skills to project knowledge
3. Reference skills naturally in conversation:

```
Use the scalability-analysis skill to evaluate this architecture.
```

### Using Skills with Claude API

#### Programmatic Skill Invocation

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

#### Skill Orchestration with API

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

### Context Management Tips for Claude

1. **Use System Prompts for Skills**
   - Place skill instructions in system prompt
   - Keeps skill context persistent across conversation

2. **Chain Skills Explicitly**
   ```
   First, apply requirements-analysis skill to these requirements.
   Then, use the output as input to system-design skill.
   ```

3. **Reference Skill Sections**
   ```
   Follow the "Decision Framework" section of the tradeoff-analysis skill.
   ```

4. **Request Skill-Compliant Outputs**
   ```
   Produce outputs exactly matching the "Expected Outputs" section of the skill.
   ```

---

## Integration with Cursor

### Setting Up Skills in Cursor IDE

#### Method 1: Add to Cursor Rules

1. Create `.cursorrules` file in your project root:

```
# Engineering Skills Integration

When asked to perform engineering tasks, use skills from the engineering-skills library.

Skill locations:
- Architecture: engineering-skills/skills/architecture/
- Engineering: engineering-skills/skills/engineering/
- Security: engineering-skills/skills/security/

Always:
1. Identify the appropriate skill for the task
2. Follow the skill's workflow exactly
3. Produce outputs matching the skill's expected format
4. Reference the skill in your response

For complex problems, use skill-orchestrator first to determine the workflow.
```

2. Cursor will automatically apply these rules to all AI interactions

#### Method 2: Workspace Context

1. Add engineering-skills as a submodule or folder in your workspace
2. Cursor indexes all workspace files
3. Reference skills naturally:

```
@Codebase Use the code-review skill to review this file
```

#### Method 3: Docs Integration

1. Open Cursor Settings → Features → Docs
2. Add engineering-skills repository as documentation source
3. Skills become searchable and referenceable

### Using Skills in Cursor Chat

#### Single Skill Execution

```
@Docs Find the architecture-review skill and apply it to our current architecture.

Our architecture:
- Frontend: React + TypeScript
- Backend: Node.js + Express
- Database: MongoDB
- Deployment: AWS ECS
```

#### Multi-Skill Workflow

```
I need to add a new payment processing feature.

1. Use requirements-analysis to clarify requirements
2. Use system-design to design the solution
3. Use security-architecture-review to validate security
4. Use testing-strategy to plan tests

Initial requirements:
- Support Stripe and PayPal
- Handle webhooks
- Store transaction history
```

### Using Skills in Cursor Composer

Composer is ideal for multi-file, multi-step tasks:

```
@Composer I need to refactor our authentication module.

Use the refactoring skill from engineering-skills/skills/engineering/refactoring/

Scope:
- Files: src/auth/*.ts
- Goals: Improve testability, reduce coupling, add error handling
- Constraints: No breaking changes to public API

Follow the skill's workflow and apply changes across all relevant files.
```

### Cursor Workflow Examples

#### Architecture Review

```
@Codebase Perform an architecture review using the architecture-review skill.

Analyze:
- Overall architecture
- Component relationships  
- Data flows
- Deployment architecture

Produce findings, risks, and recommendations as specified in the skill.
```

#### Migration Planning

```
@Composer We need to migrate from REST to GraphQL.

Use migration-planning skill to create a migration plan.

Current state: REST API with 50+ endpoints
Target state: GraphQL API with type-safe schema
Constraints: Zero downtime, backward compatibility during transition
```

---

## Integration with Custom AI Coding Agents

### API/Interface Patterns for Skill Invocation

#### Pattern 1: Skill Loader

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

#### Pattern 2: Skill Executor

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

#### Pattern 3: Skill Orchestrator

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

### Configuration Examples

#### Agent Configuration (YAML)

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

#### Agent Configuration (JSON)

```json
{
  "agent": {
    "name": "engineering-assistant",
    "skills": {
      "repository": "engineering-skills",
      "catalog": "catalog/skills.yaml",
      "categories": [
        "architecture",
        "engineering",
        "security",
        "operations"
      ]
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

---

## MCP (Model Context Protocol) Workflows

### What is MCP?

Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context to LLMs. It enables:

- Standardized context sharing between tools and LLMs
- Reusable context servers
- Tool discovery and invocation
- Structured data exchange

### Using Skills in MCP-Based Systems

#### MCP Server for Engineering Skills

```python
# skills_mcp_server.py
from mcp.server import Server, Tool
from mcp.types import TextContent, ToolResponse
import yaml
from pathlib import Path

class EngineeringSkillsServer(Server):
    def __init__(self):
        super().__init__("engineering-skills")
        self.skills_path = Path("engineering-skills")
        self.catalog = self._load_catalog()
        self._register_tools()
    
    def _load_catalog(self):
        with open(self.skills_path / "catalog" / "skills.yaml") as f:
            return yaml.safe_load(f)
    
    def _register_tools(self):
        """Register each skill as an MCP tool"""
        
        # Register skill discovery
        self.add_tool(
            Tool(
                name="list_skills",
                description="List all available engineering skills",
                parameters={}
            ),
            self._list_skills
        )
        
        self.add_tool(
            Tool(
                name="get_skill",
                description="Get detailed information about a specific skill",
                parameters={
                    "skill_name": {
                        "type": "string",
                        "description": "Name of the skill to retrieve"
                    }
                }
            ),
            self._get_skill
        )
        
        self.add_tool(
            Tool(
                name="orchestrate_skills",
                description="Determine which skills to use for a problem",
                parameters={
                    "problem": {
                        "type": "string",
                        "description": "Engineering problem description"
                    },
                    "context": {
                        "type": "object",
                        "description": "Additional context"
                    }
                }
            ),
            self._orchestrate_skills
        )
        
        # Register individual skills as tools
        for skill in self.catalog['skills']:
            self._register_skill_tool(skill)
    
    def _register_skill_tool(self, skill):
        """Register an individual skill as an MCP tool"""
        
        self.add_tool(
            Tool(
                name=f"skill_{skill['name'].replace('-', '_')}",
                description=skill['description'],
                parameters={
                    "inputs": {
                        "type": "object",
                        "description": "Skill inputs as key-value pairs"
                    }
                }
            ),
            lambda params: self._execute_skill(skill['name'], params['inputs'])
        )
    
    async def _list_skills(self, params):
        """List all skills"""
        skills_list = []
        for skill in self.catalog['skills']:
            skills_list.append({
                'name': skill['name'],
                'category': skill['category'],
                'description': skill['description'],
                'complexity': skill['complexity']
            })
        
        return ToolResponse(
            content=[TextContent(text=yaml.dump(skills_list))]
        )
    
    async def _get_skill(self, params):
        """Get skill details"""
        skill_name = params['skill_name']
        skill_info = self._find_skill(skill_name)
        
        if not skill_info:
            return ToolResponse(
                content=[TextContent(text=f"Skill {skill_name} not found")],
                is_error=True
            )
        
        skill_path = self.skills_path / skill_info['path']
        skill_content = (skill_path / 'SKILL.md').read_text()
        
        return ToolResponse(
            content=[TextContent(text=skill_content)]
        )
    
    async def _orchestrate_skills(self, params):
        """Use skill-orchestrator to determine workflow"""
        # Load orchestrator skill
        orchestrator_path = self.skills_path / "skills" / "meta" / "skill-orchestrator"
        orchestrator_content = (orchestrator_path / "SKILL.md").read_text()
        
        # Return orchestrator skill for LLM to use
        return ToolResponse(
            content=[TextContent(
                text=f"""Use this skill to determine the workflow:
                
                {orchestrator_content}
                
                Problem: {params['problem']}
                Context: {params.get('context', {})}
                """
            )]
        )
    
    async def _execute_skill(self, skill_name, inputs):
        """Execute a skill"""
        skill_info = self._find_skill(skill_name)
        skill_path = self.skills_path / skill_info['path']
        
        skill_content = (skill_path / 'SKILL.md').read_text()
        instructions = (skill_path / 'instructions.md').read_text()
        
        return ToolResponse(
            content=[TextContent(
                text=f"""{skill_content}
                
                ---
                
                {instructions}
                
                ---
                
                Inputs:
                {yaml.dump(inputs)}
                """
            )]
        )
    
    def _find_skill(self, name):
        for skill in self.catalog['skills']:
            if skill['name'] == name:
                return skill
        return None

if __name__ == "__main__":
    server = EngineeringSkillsServer()
    server.run()
```

#### MCP Server Configuration

```json
{
  "mcpServers": {
    "engineering-skills": {
      "command": "python",
      "args": ["skills_mcp_server.py"],
      "env": {
        "SKILLS_PATH": "./engineering-skills"
      }
    }
  }
}
```

#### MCP Client Implementation

```python
from mcp.client import Client
import asyncio

async def use_engineering_skills():
    # Connect to skills server
    async with Client("engineering-skills") as client:
        
        # List available skills
        skills = await client.call_tool("list_skills", {})
        print("Available skills:")
        print(skills.content[0].text)
        
        # Get specific skill
        skill = await client.call_tool(
            "get_skill",
            {"skill_name": "architecture-review"}
        )
        print("\nArchitecture Review Skill:")
        print(skill.content[0].text)
        
        # Orchestrate workflow
        workflow = await client.call_tool(
            "orchestrate_skills",
            {
                "problem": "Migrate monolith to microservices",
                "context": {
                    "current_system": "Java monolith",
                    "database": "PostgreSQL",
                    "scale": "500K users"
                }
            }
        )
        print("\nWorkflow:")
        print(workflow.content[0].text)
        
        # Execute specific skill
        result = await client.call_tool(
            "skill_architecture_review",
            {
                "inputs": {
                    "architecture": "Microservices with API Gateway...",
                    "requirements": "High availability, low latency",
                    "constraints": "AWS only"
                }
            }
        )
        print("\nArchitecture Review Result:")
        print(result.content[0].text)

# Run
asyncio.run(use_engineering_skills())
```

#### Integration with Claude Desktop via MCP

1. **Configure Claude Desktop**

Add to `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS):

```json
{
  "mcpServers": {
    "engineering-skills": {
      "command": "python",
      "args": ["/path/to/skills_mcp_server.py"],
      "env": {
        "SKILLS_PATH": "/path/to/engineering-skills"
      }
    }
  }
}
```

2. **Use in Claude Desktop**

Claude will automatically discover the tools:

```
List all available engineering skills.

[Claude uses list_skills tool]

Now use the architecture-review skill to review this architecture:
[architecture description]

[Claude uses skill_architecture_review tool]
```

---

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

Create tool-specific plugins:

**VS Code Extension Example:**

```typescript
// extension.ts
import * as vscode from 'vscode';
import * as yaml from 'js-yaml';
import * as fs from 'fs';

export function activate(context: vscode.ExtensionContext) {
    
    // Register command to list skills
    let listSkills = vscode.commands.registerCommand(
        'engineering-skills.list',
        () => {
            const catalog = loadCatalog();
            const skills = catalog.skills.map(s => 
                `${s.name} (${s.category}): ${s.description}`
            );
            
            vscode.window.showQuickPick(skills).then(selected => {
                if (selected) {
                    const skillName = selected.split(' ')[0];
                    showSkill(skillName);
                }
            });
        }
    );
    
    // Register command to execute skill
    let executeSkill = vscode.commands.registerCommand(
        'engineering-skills.execute',
        async () => {
            const skillName = await vscode.window.showInputBox({
                prompt: 'Enter skill name'
            });
            
            if (skillName) {
                const skill = loadSkill(skillName);
                const panel = vscode.window.createWebviewPanel(
                    'skill',
                    `Skill: ${skillName}`,
                    vscode.ViewColumn.One,
                    {}
                );
                
                panel.webview.html = getSkillHtml(skill);
            }
        }
    );
    
    context.subscriptions.push(listSkills, executeSkill);
}

function loadCatalog() {
    const catalogPath = 'engineering-skills/catalog/skills.yaml';
    const content = fs.readFileSync(catalogPath, 'utf8');
    return yaml.load(content);
}

function loadSkill(skillName: string) {
    const catalog = loadCatalog();
    const skillInfo = catalog.skills.find(s => s.name === skillName);
    const skillPath = `engineering-skills/${skillInfo.path}`;
    
    return {
        metadata: skillInfo,
        skill: fs.readFileSync(`${skillPath}/SKILL.md`, 'utf8'),
        instructions: fs.readFileSync(`${skillPath}/instructions.md`, 'utf8')
    };
}
```

### Compatibility Considerations

#### Context Window Limitations

**Problem:** Some tools have limited context windows

**Solution:**
- Use skill summaries instead of full content
- Load only relevant sections (e.g., workflow, not examples)
- Use skill-orchestrator to select minimal skill set

#### Token Optimization

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

#### Format Compatibility

Some tools prefer specific formats:

```python
def convert_skill_to_json_schema(skill_name):
    """Convert skill to JSON Schema for tools that need it"""
    skill = loader.load_skill(skill_name)
    
    return {
        "name": skill['metadata']['name'],
        "description": skill['metadata']['description'],
        "parameters": {
            "type": "object",
            "properties": extract_inputs_schema(skill['skill']),
            "required": extract_required_inputs(skill['skill'])
        },
        "returns": extract_outputs_schema(skill['skill'])
    }
```

### Adapting Skills for Different Platforms

#### For Prompt-Based Tools (ChatGPT, etc.)

```
I want you to act as an expert software architect.

Use this skill to guide your work:

[Paste SKILL.md content]

Now apply this skill to:
[Your problem]

Follow the workflow exactly and produce outputs in the format specified.
```

#### For Code-Generation Tools (GitHub Copilot, Tabnine)

```
// Use architecture-review skill to review this architecture
// Skill location: engineering-skills/skills/architecture/architecture-review/
// Follow the quality checklist in the skill

class ArchitectureReview {
    // Generate review based on skill workflow
}
```

#### For Agent Frameworks (LangChain, AutoGPT)

```python
from langchain.tools import Tool
from langchain.agents import initialize_agent

# Create tools from skills
skill_tools = []
for skill in loader.catalog['skills']:
    skill_tools.append(
        Tool(
            name=skill['name'],
            func=lambda inputs: execute_skill(skill['name'], inputs),
            description=skill['description']
        )
    )

# Initialize agent with skill tools
agent = initialize_agent(
    skill_tools,
    llm,
    agent="zero-shot-react-description",
    verbose=True
)

# Use agent
result = agent.run(
    "Review the architecture of our e-commerce platform"
)
```

---

## Best Practices

### When to Use Which Skills

#### Use Single Skills When:
- Problem is well-defined and narrow
- You know exactly which skill you need
- Time is limited
- Output requirements are clear

**Example:**
```
I need to review this API design.
→ Use: api-design-review
```

#### Use Skill Orchestrator When:
- Problem is complex or multi-faceted
- You're unsure which skills are needed
- Multiple aspects need to be addressed
- You want a comprehensive approach

**Example:**
```
We're building a new payment processing system.
→ Use: skill-orchestrator first
→ Then: Execute recommended skill sequence
```

#### Use Workflow Recipes When:
- Problem matches a common pattern
- You want a proven approach
- You need to explain the process to others
- You're training team members

**Example:**
```
We need to migrate our database.
→ Use: migration workflow recipe
```

### Combining Multiple Skills Effectively

#### Sequential Composition

Each skill builds on the previous:

```
requirements-analysis
    ↓ (outputs: clear requirements)
system-design  
    ↓ (outputs: architecture)
architecture-review
    ↓ (outputs: findings, recommendations)
architecture-decision
    ↓ (outputs: ADR)
```

#### Parallel Composition

Independent skills executed simultaneously:

```
architecture-discovery
    ↓
┌───────────────┬─────────────────┬──────────────────┐
│ scalability   │ security        │ reliability      │
│ analysis      │ review          │ analysis         │
└───────────────┴─────────────────┴──────────────────┘
    ↓
architecture-decision
```

#### Conditional Composition

Skill selection based on context:

```
architecture-discovery
    ↓
IF (monolith) → service-boundary-analysis
IF (microservices) → integration-design
    ↓
architecture-review
```

### Performance Optimization Tips

#### 1. Cache Skill Content

```python
class CachedSkillLoader(SkillLoader):
    def __init__(self):
        super().__init__()
        self._cache = {}
    
    def load_skill(self, skill_name):
        if skill_name not in self._cache:
            self._cache[skill_name] = super().load_skill(skill_name)
        return self._cache[skill_name]
```

#### 2. Load Only What You Need

```python
# Instead of loading entire skill
skill = loader.load_skill('architecture-review')

# Load only metadata for selection
metadata = loader.get_skill_metadata('architecture-review')

# Load full content only when executing
if should_execute:
    skill = loader.load_skill('architecture-review')
```

#### 3. Parallelize Independent Skills

```python
import asyncio

async def execute_parallel_skills(skill_names, inputs):
    tasks = [
        executor.execute_skill(name, inputs)
        for name in skill_names
    ]
    return await asyncio.gather(*tasks)

# Execute security, scalability, reliability in parallel
results = await execute_parallel_skills(
    ['security-architecture-review', 'scalability-analysis', 'reliability-analysis'],
    shared_inputs
)
```

#### 4. Use Streaming for Long Outputs

```python
async def execute_skill_streaming(skill_name, inputs):
    """Stream skill execution results"""
    skill = loader.load_skill(skill_name)
    
    async with client.messages.stream(
        model="claude-3-5-sonnet-20241022",
        max_tokens=4096,
        system=f"Execute {skill_name} skill: {skill['skill']}",
        messages=[{"role": "user", "content": str(inputs)}]
    ) as stream:
        async for text in stream.text_stream:
            yield text
```

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: Skill Not Found

**Symptoms:**
```
Error: Skill 'architecture-reviw' not found
```

**Solutions:**
1. Check skill name spelling (it's `architecture-review`, not `architecture-reviw`)
2. Verify skill exists in catalog: `catalog/skills.yaml`
3. Check skill path is correct
4. Ensure repository is up to date

```python
# List all available skills
for skill in loader.catalog['skills']:
    print(skill['name'])
```

#### Issue: Missing Required Inputs

**Symptoms:**
```
Skill execution failed: Missing required input 'architecture'
```

**Solutions:**
1. Read skill's "Inputs" section to see what's required
2. Check previous skill outputs for needed data
3. Provide inputs explicitly

```python
# Check required inputs
skill = loader.load_skill('architecture-review')
inputs_section = extract_section(skill['skill'], 'Inputs')
print(inputs_section)
```

#### Issue: Skill Sequence Doesn't Make Sense

**Symptoms:**
- Trying to review architecture before discovering it
- Making decisions without analyzing tradeoffs
- Skipping validation steps

**Solutions:**
1. Use skill-orchestrator to determine correct sequence
2. Follow standard patterns (discover → design → decide → validate)
3. Check skill dependencies in metadata

```python
# Check skill relationships
skill = loader.catalog['skills'].find(s => s['name'] == 'architecture-review')
print("Requires:", skill.get('requires', []))
print("Commonly followed by:", skill.get('commonly_followed_by', []))
```

#### Issue: Output Doesn't Match Expected Format

**Symptoms:**
- Skill produces narrative instead of structured output
- Missing required sections
- Format doesn't match examples

**Solutions:**
1. Explicitly reference "Expected Outputs" section in prompt
2. Provide output format template
3. Use examples as reference

```python
system_prompt = f"""
Execute {skill_name} skill.

{skill['skill']}

IMPORTANT: Produce outputs EXACTLY matching the "Expected Outputs" section.
Use the examples in examples.md as reference for format.
"""
```

#### Issue: Context Window Exceeded

**Symptoms:**
```
Error: Context length exceeded (max: 200000 tokens)
```

**Solutions:**
1. Load minimal skill content (purpose + workflow only)
2. Summarize previous skill outputs before passing to next skill
3. Use skill-orchestrator to reduce number of skills
4. Split complex problems into smaller sub-problems

```python
def summarize_output(output, max_length=1000):
    """Summarize skill output to reduce token usage"""
    if len(output) <= max_length:
        return output
    
    # Use LLM to summarize
    summary = client.messages.create(
        model="claude-3-5-sonnet-20241022",
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": f"Summarize this in {max_length} chars: {output}"
        }]
    )
    return summary.content[0].text
```

### Debugging Tips

#### 1. Enable Verbose Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

class DebugSkillExecutor(SkillExecutor):
    def execute_skill(self, skill_name, inputs):
        logger.debug(f"Executing skill: {skill_name}")
        logger.debug(f"Inputs: {inputs}")
        
        result = super().execute_skill(skill_name, inputs)
        
        logger.debug(f"Output length: {len(result['output'])}")
        logger.debug(f"Output preview: {result['output'][:200]}...")
        
        return result
```

#### 2. Validate Skill Outputs

```python
def validate_skill_output(skill_name, output):
    """Validate that output matches expected format"""
    skill = loader.load_skill(skill_name)
    expected_outputs = extract_section(skill['skill'], 'Expected Outputs')
    
    # Check if output contains expected sections
    for expected in parse_expected_outputs(expected_outputs):
        if expected not in output:
            logger.warning(f"Missing expected output: {expected}")
            return False
    
    return True
```

#### 3. Trace Skill Execution

```python
class TracedSkillExecutor(SkillExecutor):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.trace = []
    
    def execute_skill(self, skill_name, inputs):
        start_time = time.time()
        result = super().execute_skill(skill_name, inputs)
        end_time = time.time()
        
        self.trace.append({
            'skill': skill_name,
            'duration': end_time - start_time,
            'input_size': len(str(inputs)),
            'output_size': len(result['output']),
            'timestamp': start_time
        })
        
        return result
    
    def print_trace(self):
        print("\nExecution Trace:")
        for entry in self.trace:
            print(f"{entry['skill']}: {entry['duration']:.2f}s "
                  f"(in: {entry['input_size']}, out: {entry['output_size']})")
```

---

## Examples and Use Cases

### Real-World Scenario 1: New Microservice

**Problem:** Design and implement a new user authentication microservice

**Approach:**

```python
# Step 1: Orchestrate
workflow = orchestrator.orchestrate(
    problem="Design new authentication microservice",
    context={
        "requirements": "OAuth2, JWT, MFA support",
        "existing_system": "Monolithic auth in main app",
        "constraints": "Must integrate with existing user database"
    }
)

# Workflow determined:
# 1. requirements-analysis
# 2. system-design
# 3. api-design-review
# 4. security-architecture-review
# 5. data-architecture-review
# 6. testing-strategy
# 7. production-readiness

# Step 2: Execute workflow
for step in workflow:
    result = executor.execute_skill(step['skill'], step['inputs'])
    print(f"Completed: {step['skill']}")
    print(result['output'])
```

**Expected Outputs:**
- Clear requirements document
- System architecture diagram
- API specification
- Security review findings
- Data model design
- Testing strategy
- Production readiness checklist

### Real-World Scenario 2: Legacy System Migration

**Problem:** Migrate legacy monolith to cloud-native microservices

**Cursor Workflow:**

```
@Composer I need to migrate our legacy system to microservices.

Use skill-orchestrator to determine the workflow, then execute:

Current state:
- Java monolith (500K LOC)
- Oracle database
- On-premise deployment
- 200K daily users

Target state:
- Cloud-native microservices
- AWS infrastructure
- Containerized deployment
- Zero downtime migration

Constraints:
- 6-month timeline
- Limited team (5 engineers)
- Cannot rewrite everything
```

**Cursor executes:**

1. `architecture-discovery` - Map current architecture
2. `technical-debt-analysis` - Identify problem areas
3. `service-boundary-analysis` - Define microservice boundaries
4. `data-architecture-review` - Plan data migration
5. `migration-planning` - Create migration roadmap
6. `architecture-decision` - Document key decisions
7. `testing-strategy` - Plan testing approach
8. `production-readiness` - Ensure operational readiness

### Real-World Scenario 3: Production Incident

**Problem:** Database performance degradation in production

**Claude API Workflow:**

```python
# Incident response workflow
incident_context = {
    "incident": "Database queries taking 10x longer than normal",
    "symptoms": "API response times increased from 200ms to 2000ms",
    "affected_services": ["user-service", "order-service"],
    "started": "2 hours ago",
    "metrics": "CPU 90%, Memory 75%, Disk I/O 95%"
}

# Execute incident analysis skills
results = []

# 1. Incident analysis
results.append(
    executor.execute_skill('incident-analysis', incident_context)
)

# 2. Root cause analysis (using incident analysis output)
results.append(
    executor.execute_skill(
        'root-cause-analysis',
        {**incident_context, 'incident_analysis': results[0]['output']}
    )
)

# 3. Reliability analysis (prevent recurrence)
results.append(
    executor.execute_skill(
        'reliability-analysis',
        {**incident_context, 'root_cause': results[1]['output']}
    )
)

# Generate incident report
for result in results:
    print(f"\n=== {result['skill']} ===")
    print(result['output'])
```

### Real-World Scenario 4: API Design Review

**Problem:** Review new REST API before implementation

**GitHub Copilot Chat:**

```
@workspace Use the api-design-review skill to review this API design.

Skill location: engineering-skills/skills/architecture/api-design-review/

API Specification:

POST /api/v1/orders
Request:
{
  "userId": "string",
  "items": [{"productId": "string", "quantity": number}],
  "paymentMethod": "string"
}

Response:
{
  "orderId": "string",
  "status": "string",
  "total": number
}

Follow the skill's quality checklist and provide findings.
```

**Copilot Response:**

```markdown
# API Design Review: Orders API

## Findings

### Critical Issues
1. **Missing Authentication**: No auth mechanism specified
2. **No Idempotency**: POST without idempotency key
3. **Insufficient Error Handling**: No error response format defined

### Recommendations
1. Add authentication header requirement
2. Add `Idempotency-Key` header
3. Define error response schema
4. Add pagination for list endpoints
5. Version API properly

### Improved Design

```http
POST /api/v1/orders
Authorization: Bearer {token}
Idempotency-Key: {uuid}
Content-Type: application/json

Request:
{
  "items": [
    {"productId": "prod_123", "quantity": 2}
  ],
  "paymentMethodId": "pm_456",
  "shippingAddressId": "addr_789"
}

Response 201:
{
  "orderId": "ord_abc",
  "status": "pending",
  "total": {"amount": 9999, "currency": "USD"},
  "createdAt": "2024-01-15T10:30:00Z"
}

Response 400:
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Invalid product ID",
    "details": [{"field": "items[0].productId", "issue": "not_found"}]
  }
}
```
```

### Real-World Scenario 5: Technical Debt Prioritization

**Problem:** Prioritize technical debt across large codebase

**Custom Agent:**

```python
# Technical debt analysis workflow
codebase_context = {
    "repository": "https://github.com/company/product",
    "languages": ["TypeScript", "Python"],
    "size": "250K LOC",
    "team_size": 15,
    "known_issues": [
        "Outdated dependencies",
        "Missing tests",
        "Complex authentication logic",
        "Inconsistent error handling"
    ]
}

# Execute technical debt analysis
debt_analysis = executor.execute_skill(
    'technical-debt-analysis',
    codebase_context
)

print(debt_analysis['output'])

# Output includes:
# - Categorized technical debt
# - Priority ranking
# - Estimated effort
# - Risk assessment
# - Remediation roadmap
```

---

## Conclusion

The Engineering Skills Library provides a powerful, flexible foundation for integrating engineering expertise into AI-assisted development workflows. Whether you're using GitHub Copilot, Claude, Cursor, custom agents, or MCP-based systems, these skills enable:

- **Consistent engineering practices** across tools and teams
- **Composable workflows** for complex problems
- **Reusable expertise** that improves over time
- **Structured outputs** that meet quality standards
- **Vendor-neutral integration** with any AI development tool

### Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/your-org/engineering-skills.git
   ```

2. **Explore the skills**
   ```bash
   cd engineering-skills/skills
   ls -R
   ```

3. **Try a skill**
   - Read `SKILL.md` for overview
   - Follow `instructions.md` for detailed workflow
   - Reference `examples.md` for real-world usage

4. **Integrate with your tools**
   - Use patterns from this guide
   - Start with simple single-skill usage
   - Progress to orchestrated workflows

### Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:
- Creating new skills
- Improving existing skills
- Adding examples and evaluations
- Submitting contributions

### Resources

- **Repository**: [github.com/your-org/engineering-skills](https://github.com/your-org/engineering-skills)
- **Catalog**: [`catalog/skills.yaml`](catalog/skills.yaml)
- **Documentation**: [`docs/`](docs/)
- **Examples**: [`examples/`](examples/)

### Support

For questions, issues, or discussions:
- Open an issue on GitHub
- Join our community discussions
- Contribute improvements and new skills

---

**License**: See [LICENSE](LICENSE)

**Version**: 1.0.0

**Last Updated**: 2024
