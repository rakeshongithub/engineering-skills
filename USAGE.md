# Engineering Skills Library - Usage Guide

## Overview

The **Engineering Skills Library** is an open-source, composable collection of engineering capabilities designed for architecture, software engineering, and agentic development. Each skill represents a practical, repeatable engineering capability with clear workflows, quality standards, and real-world examples.

### What Makes This Different

This is **not** a collection of generic AI prompts. It's a **composable engineering skill system** that combines:

```
Individual Skills + Skill Metadata + Skill Relationships +
Skill Orchestration + Workflow Recipes + Evaluation =
Composable Engineering Skill System
```

### Available Skills

The library includes **32 production-ready skills** across multiple categories:

**Phase 1: Foundation (20 Skills)** - Architecture, Engineering, Security, Operations, Documentation

**Phase 2: Agentic Engineering (10 Skills)** - Agent Planning, Context, Execution, Safety, Operations

**Phase 3: Engineering Operations (2 Skills)** - Incident analysis and root-cause analysis

For a complete list with metadata, see [`catalog/skills.yaml`](catalog/skills.yaml).

## Workflow Recipes

Use the reusable recipes in [`workflows/`](workflows/README.md) when a problem matches a common engineering scenario. Available recipes cover new feature delivery, system design, architecture review, legacy modernization, production readiness, agent development, and incident response.

Each recipe defines its inputs, skill sequence, handoff artifacts, quality gates, and expected outputs. Use `skill-orchestrator` when the problem needs a different composition.

---

## Quick Start

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

4. **Integrate with your tools** - See integration guides below

---

## Documentation

Detailed documentation is organized into focused guides in the [`docs/`](docs/) folder:

### Getting Started

- **[Overview](docs/01-overview.md)** - Introduction, available skills, and getting started

### Tool Integration Guides

- **[GitHub Copilot Integration](docs/02-github-copilot-integration.md)** - Use skills with Copilot in VS Code
- **[Claude Integration](docs/03-claude-integration.md)** - Desktop app and API integration
- **[Cursor Integration](docs/04-cursor-integration.md)** - IDE integration with .cursorrules
- **[Custom AI Agents](docs/05-custom-agents.md)** - Build custom agents with skill APIs
- **[MCP Workflows](docs/06-mcp-workflows.md)** - Model Context Protocol integration

### Guides and Best Practices

- **[Best Practices](docs/07-best-practices.md)** - Skill selection, composition, and optimization
- **[Agentic Engineering](docs/08-agentic-engineering.md)** - Build multi-agent systems
- **[Examples and Use Cases](docs/09-examples.md)** - Real-world scenarios and patterns
- **[Troubleshooting](docs/10-troubleshooting.md)** - Common issues and solutions

---

## Quick Integration Examples

### GitHub Copilot

```
@workspace I need to perform an architecture review.
Use the architecture-review skill from the engineering-skills library.

Context:
- System: E-commerce platform
- Technology: Node.js, React, PostgreSQL
- Scale: 100K daily users
```

[See full guide →](docs/02-github-copilot-integration.md)

### Claude (Anthropic)

```python
import anthropic

client = anthropic.Anthropic()
skill = load_skill("architecture/architecture-review")

message = client.messages.create(
    model="claude-3-5-sonnet-20241022",
    max_tokens=4096,
    system=f"""You are an expert architect.
    {skill}
    Follow the workflow exactly.""",
    messages=[{"role": "user", "content": "Review this architecture: ..."}]
)
```

[See full guide →](docs/03-claude-integration.md)

### Cursor IDE

Create `.cursorrules` file:

```
# Engineering Skills Integration

When asked to perform engineering tasks, use skills from the engineering-skills library.

For complex problems, use skill-orchestrator first to determine the workflow.
```

Then use in Composer:

```
@Composer I need to refactor our authentication module.

Use the refactoring skill from engineering-skills/skills/engineering/refactoring/
```

[See full guide →](docs/04-cursor-integration.md)

---

## Advanced Integration

### Custom AI Coding Agents

Build custom agents using the SkillLoader and SkillExecutor patterns:

```python
from engineering_skills import SkillLoader, SkillExecutor

loader = SkillLoader()
executor = SkillExecutor(client, loader)

result = executor.execute_skill(
    'architecture-review',
    inputs={...}
)
```

[See full guide →](docs/05-custom-agents.md)

### MCP (Model Context Protocol)

Create an MCP server to expose skills as tools:

```json
{
  "mcpServers": {
    "engineering-skills": {
      "command": "python",
      "args": ["skills_mcp_server.py"]
    }
  }
}
```

Claude Desktop will automatically discover and use the skills.

[See full guide →](docs/06-mcp-workflows.md)

---

## Best Practices and Guides

**Best Practices:**

- [When to Use Which Skills](docs/07-best-practices.md#when-to-use-which-skills)
- [Combining Multiple Skills](docs/07-best-practices.md#combining-multiple-skills-effectively)
- [Performance Optimization](docs/07-best-practices.md#performance-optimization-tips)
- [Error Handling](docs/07-best-practices.md#error-handling)

**Agentic Engineering:**

- [Building Multi-Agent Systems](docs/08-agentic-engineering.md)
- [Workflow Patterns](docs/08-agentic-engineering.md#multi-agent-workflow-patterns)
- [Code Review Agent Example](docs/08-agentic-engineering.md#example-building-a-code-review-agent-system)

**Examples:**

- [Real-World Scenarios](docs/09-examples.md)
- [Common Patterns](docs/09-examples.md#common-patterns)
- [Tips for Success](docs/09-examples.md#tips-for-success)

**Troubleshooting:**

- [Common Issues](docs/10-troubleshooting.md#common-issues-and-solutions)
- [Debugging Tips](docs/10-troubleshooting.md#debugging-tips)
- [Getting Help](docs/10-troubleshooting.md#getting-help)

---

## Contributing and Support

### Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:

- Creating new skills
- Improving existing skills
- Adding examples and evaluations
- Submitting contributions

### Support

For questions, issues, or discussions:

- Open an issue on GitHub
- Join our community discussions
- Contribute improvements and new skills

---

## Resources

- **Repository**: [github.com/your-org/engineering-skills](https://github.com/your-org/engineering-skills)
- **Catalog**: [`catalog/skills.yaml`](catalog/skills.yaml)
- **Documentation**: [`docs/`](docs/)
- **Examples**: [`examples/`](examples/)

---

**License**: See [LICENSE](LICENSE)

**Version**: 1.0.0

**Last Updated**: 2024
