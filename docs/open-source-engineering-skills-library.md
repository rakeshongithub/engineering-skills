# Open-Source Engineering Skills Library

## Vision

Build an **open-source, composable engineering skill library for architecture, software engineering, and agentic development**.

The goal is not to create a collection of generic AI prompts. Each skill should represent a practical, repeatable engineering capability with:

- A clear problem statement
- Well-defined inputs
- A repeatable workflow
- Explicit outputs
- Decision criteria
- Quality checks
- Examples
- Relationships to other skills

The long-term vision is to make the repository behave like an **engineering skill graph** that can be used manually or orchestrated by AI coding agents.

---

# 1. Core Design Principle

> **One skill = one repeatable engineering capability with a clear input, decision process, output, and quality bar.**

Skills should be:

- Practical
- Reusable
- Composable
- Tool/technology aware where necessary, but not unnecessarily tied to a vendor
- Easy for humans to understand
- Easy for AI agents to discover and invoke
- Evaluatable through examples and quality criteria

---

# 2. Recommended Skill Categories

## A. Architecture & Design

These form the core of the library.

| Skill                          | Purpose                                   | Practical Output                               |
| ------------------------------ | ----------------------------------------- | ---------------------------------------------- |
| `architecture-review`          | Review an existing architecture           | Findings, risks, recommendations               |
| `architecture-design`          | Design architecture for a new requirement | Architecture, diagrams, decisions              |
| `system-design`                | Convert requirements into system design   | Components, APIs, data flow                    |
| `architecture-decision`        | Make an architectural/technology decision | ADR                                            |
| `tradeoff-analysis`            | Compare architectural alternatives        | Decision matrix                                |
| `scalability-analysis`         | Identify scalability bottlenecks          | Capacity risks and solutions                   |
| `reliability-analysis`         | Review availability and resilience        | Failure modes and mitigations                  |
| `security-architecture-review` | Review security architecture              | Threats and controls                           |
| `api-design-review`            | Review REST/event/API design              | API issues and recommendations                 |
| `data-architecture-review`     | Review data/storage architecture          | Data risks and recommendations                 |
| `integration-design`           | Design system integrations                | Integration architecture                       |
| `event-driven-design`          | Design event-driven systems               | Events, producers, consumers, failure handling |

---

# 3. Software Engineering Skills

| Skill                       | Purpose                                                       |
| --------------------------- | ------------------------------------------------------------- |
| `requirements-analysis`     | Turn vague requirements into engineering requirements         |
| `requirement-clarification` | Identify ambiguities and missing requirements                 |
| `technical-specification`   | Convert requirements into implementation-ready specifications |
| `code-review`               | Perform structured code review                                |
| `refactoring`               | Identify and plan safe refactoring                            |
| `technical-debt-analysis`   | Identify and prioritize technical debt                        |
| `bug-analysis`              | Analyze bugs systematically                                   |
| `root-cause-analysis`       | Perform root-cause analysis rather than symptom fixing        |
| `performance-analysis`      | Analyze application performance                               |
| `dependency-analysis`       | Analyze library/package dependencies                          |
| `migration-planning`        | Plan technology/platform migrations                           |
| `legacy-modernization`      | Modernize legacy applications incrementally                   |
| `testing-strategy`          | Define testing strategy                                       |
| `test-case-design`          | Generate meaningful test scenarios                            |
| `release-readiness`         | Determine whether a feature/release is ready                  |

# 3A. Frontend Engineering Skills

Frontend skills cover the client-side concerns that are easy to miss in backend-oriented engineering workflows: rendering, component boundaries, responsive behavior, browser state, accessibility, performance, testing, and security.

| Skill                           | Purpose                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------- |
| `frontend-architecture`         | Define frontend rendering, routing, module, data, and ownership boundaries    |
| `component-design`              | Design reusable components with clear APIs, states, and interaction contracts |
| `responsive-design`             | Design usable layouts across viewport sizes and input methods                 |
| `frontend-state-management`     | Structure state by ownership, lifetime, and synchronization behavior          |
| `frontend-accessibility-review` | Review keyboard, semantic, screen-reader, visual, and WCAG quality            |
| `frontend-performance-analysis` | Analyze loading, rendering, interaction, and runtime performance              |
| `frontend-testing-strategy`     | Define component, integration, visual, accessibility, and end-to-end coverage |
| `frontend-security-review`      | Review browser-facing security and sensitive data handling                    |

---

# 4. Agentic Engineering Skills

This category can become one of the distinctive parts of the repository.

The focus should be on **engineering with agents**, rather than generic "AI code generation."

| Skill                       | Purpose                                                 |
| --------------------------- | ------------------------------------------------------- |
| `agent-task-decomposition`  | Break large engineering problems into agent-sized tasks |
| `agent-workflow-design`     | Design workflows involving multiple agents              |
| `agent-selection`           | Decide which agent should perform which task            |
| `agent-context-engineering` | Determine what context an agent needs                   |
| `agent-instruction-design`  | Create effective agent instructions                     |
| `agent-output-validation`   | Validate agent-generated output                         |
| `agent-handoff-design`      | Design handoffs between agents                          |
| `agent-human-handoff`       | Determine when humans must intervene                    |
| `agent-tool-selection`      | Decide which tools an agent needs                       |
| `agent-memory-design`       | Design short/long-term agent memory                     |
| `agent-evaluation`          | Evaluate agent quality                                  |
| `agent-observability`       | Design tracing/monitoring for agents                    |
| `agent-guardrails`          | Define boundaries and controls                          |
| `agentic-workflow-review`   | Review an existing agentic workflow                     |

---

# 5. DevOps / Platform Skills

| Skill                       | Purpose                                  |
| --------------------------- | ---------------------------------------- |
| `ci-cd-design`              | Design CI/CD pipelines                   |
| `deployment-strategy`       | Choose blue/green, canary, rolling, etc. |
| `containerization`          | Containerize applications                |
| `kubernetes-review`         | Review Kubernetes architecture           |
| `cloud-architecture-review` | Review cloud architecture                |
| `observability-design`      | Design logs, metrics and traces          |
| `incident-analysis`         | Analyze production incidents             |
| `disaster-recovery`         | Design disaster recovery strategy        |
| `backup-recovery`           | Review backup/recovery strategy          |
| `capacity-planning`         | Estimate infrastructure capacity         |
| `cost-optimization`         | Analyze cloud/infrastructure costs       |
| `production-readiness`      | Review production readiness              |

---

# 6. Security Skills

Keep these practical rather than turning them into security textbooks.

| Skill                        | Purpose                                      |
| ---------------------------- | -------------------------------------------- |
| `threat-modeling`            | Threat model a system                        |
| `security-review`            | Perform security review of architecture/code |
| `authentication-design`      | Design authentication                        |
| `authorization-design`       | Design authorization                         |
| `api-security-review`        | Review API security                          |
| `data-protection-review`     | Review sensitive data handling               |
| `secrets-management`         | Review secrets handling                      |
| `dependency-security-review` | Identify dependency risks                    |
| `secure-coding-review`       | Identify common security problems            |
| `zero-trust-review`          | Evaluate zero-trust principles               |

---

# 7. Engineering Decision Skills

Architects spend significant time making decisions under uncertainty.

| Skill                      | Purpose                                 |
| -------------------------- | --------------------------------------- |
| `technology-selection`     | Select technology/framework             |
| `build-vs-buy`             | Analyze build vs. buy                   |
| `vendor-evaluation`        | Evaluate vendors                        |
| `architecture-tradeoff`    | Analyze competing architectural choices |
| `poc-evaluation`           | Evaluate POCs                           |
| `technology-risk-analysis` | Identify technology risks               |
| `technical-feasibility`    | Determine technical feasibility         |
| `decision-matrix`          | Create weighted decision matrix         |
| `adr-generator`            | Generate Architecture Decision Records  |

Example composition:

```text
Requirement
    ↓
technology-selection
    ↓
tradeoff-analysis
    ↓
risk-analysis
    ↓
architecture-decision
    ↓
ADR
```

---

# 8. Documentation & Knowledge Skills

| Skill                        | Purpose                        |
| ---------------------------- | ------------------------------ |
| `architecture-documentation` | Document architecture          |
| `api-documentation`          | Create API documentation       |
| `adr-documentation`          | Create ADRs                    |
| `runbook-generation`         | Create operational runbooks    |
| `technical-design-doc`       | Create design documents        |
| `engineering-guidelines`     | Create engineering standards   |
| `migration-documentation`    | Document migration strategy    |
| `onboarding-guide`           | Create technical onboarding    |
| `architecture-diagram`       | Generate architecture diagrams |
| `sequence-diagram`           | Generate sequence diagrams     |
| `dependency-diagram`         | Generate dependency diagrams   |

---

# 9. Engineering Analysis Skills

These are particularly useful for senior engineers and architects working with existing systems.

| Skill                       | Purpose                           |
| --------------------------- | --------------------------------- |
| `codebase-analysis`         | Understand an unfamiliar codebase |
| `architecture-discovery`    | Reverse-engineer architecture     |
| `dependency-mapping`        | Map dependencies                  |
| `service-boundary-analysis` | Evaluate microservice boundaries  |
| `coupling-analysis`         | Identify coupling                 |
| `cohesion-analysis`         | Analyze component cohesion        |
| `complexity-analysis`       | Analyze system/code complexity    |
| `bottleneck-analysis`       | Find bottlenecks                  |
| `failure-mode-analysis`     | Identify failure scenarios        |
| `risk-analysis`             | Identify technical risks          |

---

# 10. The Most Important Meta-Skill: `skill-orchestrator`

Create a dedicated meta-skill:

```text
skills/meta/skill-orchestrator/
```

Its purpose is **not to solve the engineering problem directly**.

Its job is to answer:

> **Which skills should I use, in what order, and why?**

For example, for:

> "We need to migrate our monolith to microservices."

The orchestrator could determine:

```text
migration-planning
        ↓
architecture-discovery
        ↓
service-boundary-analysis
        ↓
data-architecture-review
        ↓
integration-design
        ↓
scalability-analysis
        ↓
reliability-analysis
        ↓
security-architecture-review
        ↓
architecture-decision
        ↓
migration-plan
```

For:

> "We need to introduce an AI agent that processes customer support tickets."

It could compose:

```text
requirements-analysis
        ↓
agent-task-decomposition
        ↓
agent-workflow-design
        ↓
agent-tool-selection
        ↓
agent-context-engineering
        ↓
agent-guardrails
        ↓
security-review
        ↓
agent-evaluation
        ↓
agent-observability
        ↓
production-readiness
```

---

# 11. Skill Taxonomy

The orchestrator should understand relationships between skills.

Example:

```text
                    ┌─────────────────────┐
                    │ Skill Orchestrator  │
                    └──────────┬──────────┘
                               │
        ┌──────────────────────┼──────────────────────┐
        ↓                      ↓                      ↓
 Architecture             Engineering             Agentic
        │                      │                      │
        ↓                      ↓                      ↓
 system-design          requirements-analysis   agent-workflow
 architecture-review    code-review             agent-context
 tradeoff-analysis      testing-strategy        agent-evaluation
 scalability            refactoring              guardrails
 security               migration               observability
```

Skills should also define dependencies.

Example:

```text
architecture-review
       │
       ├── requires → architecture-discovery
       │
       ├── uses → scalability-analysis
       │
       ├── uses → security-review
       │
       └── uses → reliability-analysis
```

This creates a **machine-readable skill graph**.

---

# 12. Skill Authoring Meta-Skill

Create another meta-skill:

```text
skills/meta/skill-authoring/
```

Its purpose is to teach contributors how to create high-quality skills.

Recommended workflow:

```text
Problem
   ↓
Define skill purpose
   ↓
Define inputs
   ↓
Define outputs
   ↓
Define workflow
   ↓
Define constraints
   ↓
Define quality criteria
   ↓
Add examples
   ↓
Add evaluation cases
   ↓
Submit skill
```

This makes the repository capable of growing through community contributions.

---

# 13. Standard Skill Contract

Every skill should follow the same structure.

Recommended `SKILL.md` structure:

```text
# Skill Name

## Purpose

## When to Use

## When NOT to Use

## Inputs

## Expected Outputs

## Workflow

## Decision Framework

## Quality Checklist

## Common Mistakes

## Examples

## Related Skills

## Skill Composition

## Evaluation Criteria
```

A skill should be understandable without reading the entire repository.

---

# 14. Machine-Readable Skill Metadata

Each skill can expose metadata such as:

```yaml
name: architecture-review

category: architecture

inputs:
  - architecture
  - requirements
  - constraints

outputs:
  - findings
  - risks
  - recommendations

requires:
  - architecture-discovery

commonly_followed_by:
  - scalability-analysis
  - security-architecture-review
  - reliability-analysis
  - architecture-decision
```

This enables automated discovery and orchestration.

A future `catalog/skills.yaml` can contain the metadata for the entire repository.

---

# 15. Skill Graph

The repository should evolve from a collection of files into a skill graph.

Instead of thinking:

```text
50 SKILL.md files
```

think:

```text
                    ┌───────────────┐
                    │ ORCHESTRATOR  │
                    └───────┬───────┘
                            │
                    ┌───────▼───────┐
                    │ REQUIREMENTS  │
                    └───────┬───────┘
                            │
             ┌──────────────┼──────────────┐
             ↓              ↓              ↓
       ARCHITECTURE      SECURITY       DATA
             │              │              │
             └──────────────┼──────────────┘
                            ↓
                       TRADEOFFS
                            ↓
                         DESIGN
                            ↓
                      IMPLEMENTATION
                            ↓
                         TESTING
                            ↓
                       PRODUCTION
                            ↓
                       OBSERVABILITY
                            ↓
                       OPTIMIZATION
```

---

# 16. Workflow Recipes

In addition to individual skills, create reusable workflow recipes.

## New Feature

```text
requirements-analysis
        ↓
system-design
        ↓
architecture-review
        ↓
security-review
        ↓
api-design-review
        ↓
testing-strategy
        ↓
production-readiness
```

## New AI Agent

```text
requirements-analysis
        ↓
agent-task-decomposition
        ↓
agent-workflow-design
        ↓
agent-context-engineering
        ↓
agent-tool-selection
        ↓
agent-guardrails
        ↓
agent-evaluation
        ↓
agent-observability
        ↓
production-readiness
```

## Frontend Feature

```text
requirements-analysis
        ↓
frontend-architecture
        ↓
component-design
        ↓
responsive-design
        ↓
frontend-state-management
        ↓
frontend-accessibility-review
        ↓
frontend-testing-strategy
        ↓
frontend-performance-analysis
        ↓
production-readiness
```

## Legacy Modernization

```text
architecture-discovery
        ↓
technical-debt-analysis
        ↓
dependency-analysis
        ↓
service-boundary-analysis
        ↓
migration-planning
        ↓
architecture-decision
        ↓
testing-strategy
        ↓
production-readiness
```

Workflow recipes give users two ways to work:

```text
"I know what skill I need"
              ↓
        Use individual skill


"I have a problem"
              ↓
      Skill Orchestrator
              ↓
       Skill composition
              ↓
        Workflow
```

---

# 17. Recommended Repository Structure

```text
engineering-skills/
│
├── README.md
├── CONTRIBUTING.md
├── LICENSE
│
├── skills/
│   │
│   ├── meta/
│   │   ├── skill-orchestrator/
│   │   │   ├── SKILL.md
│   │   │   ├── examples/
│   │   │   └── evals/
│   │   │
│   │   └── skill-authoring/
│   │
│   ├── requirements/
│   │   ├── requirements-analysis/
│   │   └── requirement-clarification/
│   │
│   ├── architecture/
│   │   ├── architecture-discovery/
│   │   ├── architecture-design/
│   │   ├── architecture-review/
│   │   ├── architecture-decision/
│   │   ├── tradeoff-analysis/
│   │   ├── scalability-analysis/
│   │   └── reliability-analysis/
│   │
│   ├── engineering/
│   │   ├── code-review/
│   │   ├── refactoring/
│   │   ├── testing-strategy/
│   │   └── technical-debt-analysis/
│   │
│   ├── security/
│   │   ├── threat-modeling/
│   │   └── security-review/
│   │
│   ├── agentic/
│   │   ├── agent-task-decomposition/
│   │   ├── agent-workflow-design/
│   │   ├── agent-context-engineering/
│   │   ├── agent-evaluation/
│   │   └── agent-guardrails/
│   │
│   ├── frontend/
│   │   ├── frontend-architecture/
│   │   ├── component-design/
│   │   ├── responsive-design/
│   │   ├── frontend-state-management/
│   │   ├── frontend-accessibility-review/
│   │   ├── frontend-performance-analysis/
│   │   ├── frontend-testing-strategy/
│   │   └── frontend-security-review/
│   │
│   └── operations/
│       ├── production-readiness/
│       ├── incident-analysis/
│       └── observability-design/
│
├── workflows/
│   ├── new-feature.md
│   ├── system-design.md
│   ├── architecture-review.md
│   ├── legacy-modernization.md
│   ├── production-readiness.md
│   └── agent-development.md
│
├── catalog/
│   └── skills.yaml
│
└── examples/
    ├── ecommerce/
    ├── banking/
    ├── saas/
        ├── ai-agent/
        └── frontend/
```

---

# 18. Recommended Initial Release

Do not start with 100 skills.

Start with approximately **20 highly polished skills**.

## Phase 1 — Foundation

```text
01 skill-orchestrator
02 skill-authoring

03 requirements-analysis
04 architecture-discovery
05 system-design
06 architecture-review
07 architecture-decision
08 tradeoff-analysis
09 scalability-analysis
10 reliability-analysis
11 security-architecture-review
12 api-design-review
13 data-architecture-review
14 code-review
15 refactoring
16 testing-strategy
17 migration-planning
18 technical-debt-analysis
19 production-readiness
20 technical-design-document
```

These 20 can already provide substantial practical value.

---

# 19. Phase 2 — Agentic Engineering

Add:

```text
21 agent-task-decomposition
22 agent-workflow-design
23 agent-context-engineering
24 agent-instruction-design
25 agent-tool-selection
26 agent-handoff-design
27 agent-guardrails
28 agent-evaluation
29 agent-observability
30 agentic-workflow-review
```

This gives the repository a distinctive focus on:

> **Practical AI-assisted and agentic software architecture.**

---

# 20. Phase 3 — Engineering Operations

Add:

```text
31 incident-analysis
32 root-cause-analysis
33 observability-design
34 capacity-planning
35 disaster-recovery
36 backup-recovery
37 cloud-architecture-review
38 ci-cd-design
39 deployment-strategy
40 cost-optimization
```

---

# 21. North-Star Architecture

The overall repository can eventually operate like this:

```text
                    Engineering Problem
                           │
                           ▼
                  ┌─────────────────┐
                  │ Skill           │
                  │ Orchestrator    │
                  └────────┬────────┘
                           │
                 Discover / Select
                           │
                           ▼
                 ┌─────────────────┐
                 │ Skill Graph     │
                 └────────┬────────┘
                           │
                  Compose Skills
                           │
                           ▼
                    ┌────────────┐
                    │ Workflow   │
                    └─────┬──────┘
                          │
                          ▼
                    Engineering
                      Outcome
```

---

# 22. Project Positioning

Avoid positioning the project as:

> "A collection of AI skills for software development."

A stronger positioning is:

> **"An open-source, composable engineering skill library for architecture, software engineering, and agentic development."**

The key differentiator is that the repository is not just a set of independent prompts. It is designed as:

```text
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

---

# 23. Long-Term Direction

The architecture should remain vendor-neutral so that the same skills can eventually be used with:

- GitHub Copilot
- Claude
- Cursor
- Codex
- Other AI coding agents
- MCP-based workflows
- Custom internal engineering agents

The repository can evolve through the following stages:

```text
Stage 1
Individual Engineering Skills
        ↓
Stage 2
Standardized Skill Contract
        ↓
Stage 3
Skill Metadata / Catalog
        ↓
Stage 4
Skill Relationships / Graph
        ↓
Stage 5
Skill Orchestrator
        ↓
Stage 6
Workflow Recipes
        ↓
Stage 7
Automated Skill Evaluation
        ↓
Stage 8
Agentic Engineering Workflows
```

The ultimate goal is to make engineering expertise **discoverable, reusable, composable, and executable by both humans and AI agents**.
