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

**Current Status:** 30 production-ready skills (Phase 1: 20 foundational + Phase 2: 10 agentic)

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

---

## Phase 2: Agentic Engineering Skills

Phase 2 introduces 10 comprehensive skills for designing, implementing, and managing AI agent-based engineering workflows. These skills enable you to build production-ready multi-agent systems with proper planning, safety, and observability.

### Agent Planning & Design

**1. agent-task-decomposition** (`skills/agentic/agent-task-decomposition/`)

- Break down complex engineering problems into agent-sized tasks
- Define clear inputs, outputs, and dependencies
- Optimize for parallel execution and handoff efficiency
- **Use when:** Starting any multi-agent project or complex automation

**2. agent-workflow-design** (`skills/agentic/agent-workflow-design/`)

- Design multi-agent workflows with proper orchestration
- Implement patterns: sequential, parallel, pipeline, fan-out/fan-in, conditional
- Handle coordination, error recovery, and monitoring
- **Use when:** Orchestrating multiple agents or complex workflows

### Agent Context & Instructions

**3. agent-context-engineering** (`skills/agentic/agent-context-engineering/`)

- Engineer effective context packages for agent execution
- Structure context in 5 layers: Overview, Interfaces, Implementation, Examples, Metadata
- Optimize context retrieval and versioning strategies
- **Use when:** Preparing context for agent tasks or improving agent success rates

**4. agent-instruction-design** (`skills/agentic/agent-instruction-design/`)

- Design clear, executable instructions for autonomous agents
- Create atomic steps with preconditions and expected outcomes
- Implement validation checkpoints and error handling
- **Use when:** Writing agent prompts or improving instruction clarity

### Agent Execution & Integration

**5. agent-tool-selection** (`skills/agentic/agent-tool-selection/`)

- Select optimal tools for agent task execution
- Evaluate tools across functionality, reliability, performance, safety
- Configure tools with proper guardrails and fallbacks
- **Use when:** Configuring agent capabilities or optimizing tool usage

**6. agent-handoff-design** (`skills/agentic/agent-handoff-design/`)

- Design seamless handoffs between agents
- Implement synchronous, asynchronous, and event-driven handoffs
- Ensure context continuity and error recovery
- **Use when:** Building multi-agent workflows with agent-to-agent transitions

### Agent Safety & Quality

**7. agent-guardrails** (`skills/agentic/agent-guardrails/`)

- Implement safety, quality, and compliance guardrails
- Prevent data loss, unauthorized access, and resource abuse
- Add pre-execution, runtime, and post-execution validation
- **Use when:** Deploying agents to production or handling sensitive operations

**8. agent-evaluation** (`skills/agentic/agent-evaluation/`)

- Evaluate agent performance with quantitative and qualitative metrics
- Measure accuracy, latency, cost, correctness, and relevance
- Implement A/B testing and continuous evaluation pipelines
- **Use when:** Measuring agent effectiveness or optimizing performance

### Agent Operations & Improvement

**9. agent-observability** (`skills/agentic/agent-observability/`)

- Implement logging, metrics, and distributed tracing
- Build dashboards and alerts for agent monitoring
- Debug agent workflows with comprehensive visibility
- **Use when:** Operating agents in production or troubleshooting issues

**10. agentic-workflow-review** (`skills/agentic/agentic-workflow-review/`)

- Review and optimize agentic workflows
- Improve performance, reliability, and maintainability
- Apply best practices and avoid anti-patterns
- **Use when:** Improving existing agent systems or conducting post-mortems

### Agentic Skill Composition Patterns

**Production-Ready Agent System:**

```
agent-task-decomposition → agent-workflow-design →
agent-context-engineering → agent-instruction-design →
agent-tool-selection → agent-handoff-design →
agent-guardrails → agent-evaluation →
agent-observability → agentic-workflow-review
```

**Continuous Improvement Cycle:**

```
agent-evaluation → agent-observability →
agentic-workflow-review → optimization
```

**Incident Response & Debugging:**

```
agent-observability → agent-evaluation →
agent-guardrails (adjustment) → agentic-workflow-review
```

## Skill Categories

### Phase 1: Foundation (20 Skills) ✅

- **Requirements** (1 skill) - Requirements analysis and clarification
- **Architecture & Design** (9 skills) - System design, architecture review, tradeoff analysis, scalability, reliability, API design, data architecture
- **Security** (1 skill) - Security architecture review, threat modeling
- **Software Engineering** (5 skills) - Code review, refactoring, testing strategy, migration planning, technical debt
- **Operations** (1 skill) - Production readiness assessment
- **Documentation** (1 skill) - Technical design documents

### Phase 2: Agentic Engineering (10 Skills) ✅

- **Agent Planning** (2 skills) - Task decomposition, workflow design
- **Agent Context** (2 skills) - Context engineering, instruction design
- **Agent Execution** (2 skills) - Tool selection, handoff design
- **Agent Safety & Quality** (2 skills) - Guardrails, evaluation
- **Agent Operations** (2 skills) - Observability, workflow review

### Coming Soon: Phase 3

- **DevOps & Platform** - CI/CD, deployment, observability design
- **Incident Response** - Incident analysis, root cause analysis
- **Cloud & Infrastructure** - Cloud architecture, capacity planning, cost optimization

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
Stage 1: Individual Engineering Skills ✅
   ↓
Stage 2: Standardized Skill Contract ✅
   ↓
Stage 3: Skill Metadata / Catalog ✅
   ↓
Stage 4: Skill Relationships / Graph ✅
   ↓
Stage 5: Skill Orchestrator ✅
   ↓
Stage 6: Workflow Recipes 🚧 (In Progress)
   ↓
Stage 7: Automated Skill Evaluation ⏸️ (Planned)
   ↓
Stage 8: Agentic Engineering Workflows ✅ (Phase 2 Complete)
   ↓
Stage 9: Phase 3 - Engineering Operations 🎯 (Next)
```

**Current Status:**

- ✅ **30 production-ready skills** (Phase 1: 20 + Phase 2: 10)
- ✅ **Complete skill graph** with relationships and dependencies
- ✅ **Agentic engineering foundation** for multi-agent workflows
- 🚧 **Workflow recipes** combining skills for common scenarios
- 🎯 **Phase 3 planning** for engineering operations skills

The goal: Make engineering expertise **discoverable, reusable, composable, and executable by both humans and AI agents**.
