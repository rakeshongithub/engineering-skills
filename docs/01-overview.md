# Engineering Skills Library - Overview

## What is the Engineering Skills Library?

The **Engineering Skills Library** is an open-source, composable collection of engineering capabilities designed for architecture, software engineering, and agentic development. Each skill represents a practical, repeatable engineering capability with:

- Clear problem statements and purpose
- Well-defined inputs and outputs
- Repeatable workflows and decision frameworks
- Quality checklists and evaluation criteria
- Real-world examples and use cases
- Relationships to other skills for composition

## What Makes This Different

This is **not** a collection of generic AI prompts. It's a **composable engineering skill system** that combines:

```
Individual Skills + Skill Metadata + Skill Relationships +
Skill Orchestration + Workflow Recipes + Evaluation =
Composable Engineering Skill System
```

## Available Skills

The library includes **32 production-ready skills** across multiple categories:

**Phase 1: Foundation (20 Skills)**

- **Meta Skills** (2): Skill orchestration and authoring
- **Requirements** (1): Requirements analysis and clarification
- **Architecture** (9): System design, architecture review, tradeoff analysis, scalability, reliability, API design, data architecture
- **Engineering** (5): Code review, refactoring, testing strategy, migration planning, technical debt
- **Security** (1): Security architecture review, threat modeling
- **Operations** (1): Production readiness, deployment strategies
- **Documentation** (1): Technical design documents, ADRs

**Phase 2: Agentic Engineering (10 Skills)**

- **Agent Planning** (2): Task decomposition, workflow design
- **Agent Context** (2): Context engineering, instruction design
- **Agent Execution** (2): Tool selection, handoff design
- **Agent Safety & Quality** (2): Guardrails, evaluation
- **Agent Operations** (2): Observability, workflow review

**Phase 3: Engineering Operations (2 Skills)**

- **Incident Response** (2): Incident analysis, root-cause analysis

## Workflow Recipes

The [`workflows/`](../workflows/README.md) directory contains reusable compositions for new feature delivery, system design, architecture review, legacy modernization, production readiness, agent development, and incident response. Each recipe identifies the input evidence, ordered skill sequence, quality gates, and final artifacts.

The [`examples/`](../examples/README.md) directory demonstrates those compositions in four domains: e-commerce, banking, SaaS, and AI-agent support operations.

For a complete list with metadata, see [`catalog/skills.yaml`](../catalog/skills.yaml).

## Getting Started

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
   - See integration guides in the [docs](.) folder
   - Start with simple single-skill usage
   - Progress to orchestrated workflows

## Next Steps

- [Integration with GitHub Copilot](02-github-copilot-integration.md)
- [Integration with Claude](03-claude-integration.md)
- [Integration with Cursor](04-cursor-integration.md)
- [Custom AI Coding Agents](05-custom-agents.md)
- [MCP Workflows](06-mcp-workflows.md)
- [Best Practices](07-best-practices.md)
- [Agentic Engineering Guide](08-agentic-engineering.md)
- [Examples and Use Cases](09-examples.md)
- [Troubleshooting](10-troubleshooting.md)

## Resources

- **Repository**: [github.com/your-org/engineering-skills](https://github.com/your-org/engineering-skills)
- **Catalog**: [`catalog/skills.yaml`](../catalog/skills.yaml)
- **Documentation**: [`docs/`](../docs/)
- **Examples**: [`examples/`](../examples/)

## Support

For questions, issues, or discussions:

- Open an issue on GitHub
- Join our community discussions
- Contribute improvements and new skills

---

**License**: See [LICENSE](../LICENSE)

**Version**: 1.0.0

**Last Updated**: 2024
