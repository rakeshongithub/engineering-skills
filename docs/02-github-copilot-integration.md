# Integration with GitHub Copilot

## Overview

GitHub Copilot can leverage engineering skills through context-aware prompts in VS Code, GitHub.com, or the CLI.

## How to Use Skills with Copilot

### Method 1: Direct Skill Reference in Chat

```
@workspace I need to perform an architecture review.
Use the architecture-review skill from the engineering-skills library.

Context:
- System: E-commerce platform
- Technology: Node.js, React, PostgreSQL
- Scale: 100K daily users
```

### Method 2: Include Skill Files in Workspace

1. Clone or add the engineering-skills repository to your workspace
2. Reference specific skills in your prompts:

```
@workspace Review my API design using the guidelines from
/engineering-skills/skills/architecture/api-design-review/SKILL.md

API to review: /src/api/users.ts
```

### Method 3: Use Skill Instructions Directly

```
Follow the workflow from
/engineering-skills/skills/engineering/code-review/instructions.md
to review this pull request.

Files changed:
- src/services/payment.ts
- src/models/transaction.ts
```

## Best Practices for Copilot

### 1. Be Explicit About Skill Usage

```
Use the system-design skill to convert these requirements into a system design:
[requirements]
```

### 2. Provide Required Context

- Include the skill's required inputs
- Reference relevant code files
- Specify constraints and goals

### 3. Leverage Skill Composition

```
First use requirements-analysis to clarify these requirements,
then use system-design to create the architecture.
```

### 4. Use the Orchestrator for Complex Problems

```
Use the skill-orchestrator to determine which skills I need for:
"Migrating our monolith to microservices"
```

## Example Copilot Workflows

### New Feature Development

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

### Code Review

```
@workspace Perform a code review using the code-review skill.

Follow the quality checklist from:
/engineering-skills/skills/engineering/code-review/SKILL.md

Files: #file:src/components/UserProfile.tsx
```

## Tips for Success

1. **Reference skill files explicitly** - Copilot works best with direct file references
2. **Provide context** - Include all required inputs for the skill
3. **Use workspace context** - Leverage `@workspace` to include relevant files
4. **Chain skills** - Explicitly state when one skill's output feeds into another
5. **Be specific** - Clear instructions lead to better results

## Related Documentation

- [Back to Overview](01-overview.md)
- [Claude Integration](03-claude-integration.md)
- [Cursor Integration](04-cursor-integration.md)
- [Best Practices](07-best-practices.md)