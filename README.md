# Open-Source Engineering Skills Library

## Vision

An **open-source, composable engineering skill library for architecture, software engineering, and agentic development**.

Each skill represents a practical, repeatable engineering capability with:

- Clear problem statement
- Well-defined inputs
- Repeatable workflow
- Explicit outputs
- Decision criteria
- Quality checks
- Examples
- Relationships to other skills

## What Makes This Different

This is not a collection of generic AI prompts. It's designed as:

```
Individual Skills
       +
Skill Metadata
       +
Skill Relationships
       +
Skill Orchestration
       +
Workflow Recipes
       +
Evaluation
       =
Composable Engineering Skill System
```

## Repository Structure

```
engineering-skills/
├── skills/           # Individual engineering skills
│   ├── meta/        # Meta-skills (orchestrator, authoring)
│   ├── requirements/
│   ├── architecture/
│   ├── engineering/
│   ├── security/
│   ├── agentic/
│   └── operations/
├── workflows/        # Reusable workflow recipes
├── catalog/         # Machine-readable skill catalog
└── examples/        # Domain-specific examples
```

## Getting Started

### For Humans

1. **Browse skills** in the `skills/` directory
2. **Use individual skills** for specific engineering tasks
3. **Follow workflow recipes** in `workflows/` for common scenarios
4. **Start with the orchestrator** (`skills/meta/skill-orchestrator/`) to compose skills
5. **Read the comprehensive usage guide** in [USAGE.md](USAGE.md) for detailed integration instructions

### For AI Agents

1. **Read skill metadata** from `catalog/skills.yaml`
2. **Discover skills** using the skill graph
3. **Compose workflows** using skill relationships
4. **Execute skills** following the standard contract

### Detailed Usage Instructions

For comprehensive guidance on integrating this library with various AI development tools, see **[USAGE.md](USAGE.md)**, which includes:

- **GitHub Copilot** integration patterns and workflows
- **Claude** (Anthropic) usage with Desktop app and API
- **Cursor** IDE integration and Composer workflows
- **Custom AI coding agents** with API/interface patterns
- **MCP (Model Context Protocol)** server implementations
- **Other AI development tools** integration strategies
- Real-world examples and troubleshooting guides

## Core Meta-Skills

### Skill Orchestrator

Answers: **Which skills should I use, in what order, and why?**

Location: `skills/meta/skill-orchestrator/`

### Skill Authoring

Teaches: **How to create high-quality skills**

Location: `skills/meta/skill-authoring/`

## Skill Categories

- **Architecture & Design** - System design, architecture review, tradeoff analysis
- **Software Engineering** - Code review, refactoring, testing strategy
- **Agentic Engineering** - Agent workflows, context engineering, guardrails
- **DevOps & Platform** - CI/CD, deployment, observability
- **Security** - Threat modeling, security review, authentication design
- **Engineering Decisions** - Technology selection, build vs buy, ADRs
- **Documentation** - Architecture docs, API docs, runbooks
- **Analysis** - Codebase analysis, dependency mapping, complexity analysis

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on:

- Creating new skills
- Following the standard skill contract
- Adding examples and evaluations
- Submitting contributions

## License

See [LICENSE](LICENSE) for details.

## Project Positioning

> **An open-source, composable engineering skill library for architecture, software engineering, and agentic development.**

Vendor-neutral and designed to work with:

- GitHub Copilot
- Claude
- Cursor
- Custom AI coding agents
- MCP-based workflows
- Any AI development tool

## Evolution Roadmap

```
Stage 1: Individual Engineering Skills
   ↓
Stage 2: Standardized Skill Contract
   ↓
Stage 3: Skill Metadata / Catalog
   ↓
Stage 4: Skill Relationships / Graph
   ↓
Stage 5: Skill Orchestrator
   ↓
Stage 6: Workflow Recipes
   ↓
Stage 7: Automated Skill Evaluation
   ↓
Stage 8: Agentic Engineering Workflows
```

The goal: Make engineering expertise **discoverable, reusable, composable, and executable by both humans and AI agents**.
