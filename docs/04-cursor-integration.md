# Integration with Cursor

## Overview

Cursor IDE provides multiple ways to integrate engineering skills: `.cursorrules` files, workspace context, and Docs integration.

## Setting Up Skills in Cursor IDE

### Method 1: Add to Cursor Rules

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

### Method 2: Workspace Context

1. Add engineering-skills as a submodule or folder in your workspace
2. Cursor indexes all workspace files
3. Reference skills naturally:

```
@Codebase Use the code-review skill to review this file
```

### Method 3: Docs Integration

1. Open Cursor Settings → Features → Docs
2. Add engineering-skills repository as documentation source
3. Skills become searchable and referenceable

## Using Skills in Cursor Chat

### Single Skill Execution

```
@Docs Find the architecture-review skill and apply it to our current architecture.

Our architecture:
- Frontend: React + TypeScript
- Backend: Node.js + Express
- Database: MongoDB
- Deployment: AWS ECS
```

### Multi-Skill Workflow

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

## Using Skills in Cursor Composer

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

## Cursor Workflow Examples

### Architecture Review

```
@Codebase Perform an architecture review using the architecture-review skill.

Analyze:
- Overall architecture
- Component relationships
- Data flows
- Deployment architecture

Produce findings, risks, and recommendations as specified in the skill.
```

### Migration Planning

```
@Composer We need to migrate from REST to GraphQL.

Use migration-planning skill to create a migration plan.

Current state: REST API with 50+ endpoints
Target state: GraphQL API with type-safe schema
Constraints: Zero downtime, backward compatibility during transition
```

### Code Review

```
@Codebase Review this pull request using the code-review skill.

Files changed:
- src/services/payment.ts
- src/models/transaction.ts
- tests/payment.test.ts

Follow the quality checklist and provide structured feedback.
```

## Best Practices

### 1. Use `.cursorrules` for Consistency

- Define standard skill usage patterns
- Ensure all team members follow same approach
- Update rules as skills evolve

### 2. Leverage Workspace Indexing

- Add skills to workspace for automatic indexing
- Use `@Codebase` to search across skills and code
- Reference skills by path for precision

### 3. Composer for Complex Tasks

- Use Composer for multi-file refactoring
- Leverage skill workflows for consistency
- Apply changes atomically

### 4. Combine with Cursor Features

- Use `@Docs` for skill discovery
- Use `@Codebase` for context-aware skill application
- Use Composer for orchestrated workflows

## Tips for Success

1. **Add skills to workspace** - Enables indexing and search
2. **Use `.cursorrules`** - Ensures consistent skill application
3. **Reference skills explicitly** - Improves accuracy
4. **Provide context** - Include all required skill inputs
5. **Use Composer for workflows** - Better for multi-step tasks

## Related Documentation

- [Back to Overview](01-overview.md)
- [GitHub Copilot Integration](02-github-copilot-integration.md)
- [Claude Integration](03-claude-integration.md)
- [Best Practices](07-best-practices.md)