# MCP (Model Context Protocol) Workflows

## What is MCP?

Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context to LLMs. It enables:

- Standardized context sharing between tools and LLMs
- Reusable context servers
- Tool discovery and invocation
- Structured data exchange

## Using Skills in MCP-Based Systems

### MCP Server for Engineering Skills

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

### MCP Server Configuration

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

### MCP Client Implementation

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

## Integration with Claude Desktop via MCP

### 1. Configure Claude Desktop

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

### 2. Use in Claude Desktop

Claude will automatically discover the tools:

```
List all available engineering skills.

[Claude uses list_skills tool]

Now use the architecture-review skill to review this architecture:
[architecture description]

[Claude uses skill_architecture_review tool]
```

## Best Practices

### 1. Tool Registration

- Register each skill as a separate tool
- Use descriptive tool names and descriptions
- Define clear parameter schemas

### 2. Context Management

- Keep skill content in tool responses
- Use structured data formats (YAML, JSON)
- Provide complete context for LLM execution

### 3. Error Handling

- Return clear error messages
- Validate skill names and parameters
- Handle missing files gracefully

### 4. Performance

- Cache skill content
- Lazy-load skill files
- Use async operations

## Example Workflows

### Architecture Review via MCP

```python
async def architecture_review_workflow():
    async with Client("engineering-skills") as client:
        # Get architecture review skill
        skill = await client.call_tool(
            "get_skill",
            {"skill_name": "architecture-review"}
        )
        
        # Execute review
        result = await client.call_tool(
            "skill_architecture_review",
            {
                "inputs": {
                    "architecture": "...",
                    "requirements": "...",
                    "constraints": "..."
                }
            }
        )
        
        return result.content[0].text
```

### Multi-Skill Orchestration via MCP

```python
async def orchestrated_workflow():
    async with Client("engineering-skills") as client:
        # Get workflow plan
        workflow = await client.call_tool(
            "orchestrate_skills",
            {
                "problem": "Design new payment system",
                "context": {...}
            }
        )
        
        # Parse and execute skills
        # (Implementation depends on workflow format)
        
        return results
```

## Related Documentation

- [Back to Overview](01-overview.md)
- [Custom AI Agents](05-custom-agents.md)
- [Best Practices](07-best-practices.md)
- [Examples and Use Cases](09-examples.md)