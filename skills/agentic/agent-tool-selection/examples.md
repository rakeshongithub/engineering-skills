# Agent Tool Selection - Examples

## Example 1: Code Analysis Agent

### Scenario
Select tools for an agent that performs code review, identifies bugs, and suggests refactorings.

### Task Requirements
1. Read source code files
2. Parse and analyze code structure
3. Detect code smells and anti-patterns
4. Run static analysis
5. Generate refactoring suggestions
6. Create review reports

### Available Tools
- **read_file**: Read file contents
- **grep**: Search for patterns in files
- **ast_parser**: Parse code into AST
- **eslint**: JavaScript linting
- **pylint**: Python linting
- **sonarqube**: Multi-language static analysis
- **write_file**: Write reports

### Tool Selection Process

**Step 1: Map Requirements to Tools**

| Requirement | Candidate Tools | Selected | Rationale |
|-------------|-----------------|----------|------------|
| Read files | read_file | read_file | Only option, works well |
| Search code | grep, ast_parser | grep | Faster for pattern search |
| Parse structure | ast_parser | ast_parser | Required for structural analysis |
| Lint JavaScript | eslint, sonarqube | eslint | Specialized, faster |
| Lint Python | pylint, sonarqube | pylint | Specialized, better rules |
| Multi-language analysis | sonarqube | sonarqube | Comprehensive, cross-language |
| Generate reports | write_file | write_file | Only option |

**Step 2: Configure Tools**

```json
{
  "read_file": {
    "encoding": "utf-8",
    "max_size": "10MB"
  },
  "grep": {
    "case_sensitive": false,
    "context_lines": 3
  },
  "eslint": {
    "config": ".eslintrc.json",
    "fix": false,
    "max_warnings": 100
  },
  "pylint": {
    "rcfile": ".pylintrc",
    "score": true
  },
  "sonarqube": {
    "quality_gate": "default",
    "timeout": "5m"
  }
}
```

**Step 3: Define Usage Patterns**

```markdown
1. Use read_file to load source files
2. Use grep to find specific patterns (TODO, FIXME, etc.)
3. Use ast_parser to analyze code structure
4. Use eslint for JavaScript files
5. Use pylint for Python files
6. Use sonarqube for comprehensive multi-language analysis
7. Use write_file to generate review report
```

**Step 4: Safety Guardrails**

- read_file: Limit to project directory, max 10MB per file
- write_file: Only write to reports/ directory
- All linters: Read-only mode (no auto-fix)
- sonarqube: Rate limit to prevent API abuse

**Result**: Agent successfully reviews code with 95% accuracy, generating comprehensive reports.

---

## Example 2: DevOps Agent

### Scenario
Select tools for an agent that deploys applications, monitors health, and handles incidents.

### Task Requirements
1. Build application
2. Run tests
3. Deploy to staging
4. Validate deployment
5. Monitor application health
6. Rollback on failure

### Available Tools
- **run_command**: Execute shell commands
- **docker**: Container operations
- **kubectl**: Kubernetes operations
- **aws_cli**: AWS operations
- **http_request**: Make HTTP requests
- **slack_notify**: Send notifications

### Tool Selection Process

**Step 1: Map Requirements to Tools**

| Requirement | Selected Tools | Configuration |
|-------------|----------------|---------------|
| Build app | run_command, docker | docker build with caching |
| Run tests | run_command | timeout: 10m, fail-fast |
| Deploy | kubectl, aws_cli | kubectl apply, aws ecs update |
| Validate | http_request, kubectl | Health checks, pod status |
| Monitor | http_request, aws_cli | CloudWatch metrics, endpoint checks |
| Rollback | kubectl, aws_cli | kubectl rollout undo |
| Notify | slack_notify | On success/failure |

**Step 2: Tool Configuration**

```yaml
tools:
  run_command:
    timeout: 600  # 10 minutes
    shell: /bin/bash
    env:
      CI: true
  
  docker:
    registry: ecr.us-east-1.amazonaws.com
    cache: true
    build_args:
      NODE_ENV: production
  
  kubectl:
    context: staging-cluster
    namespace: default
    timeout: 300
  
  http_request:
    timeout: 30
    retries: 3
    retry_delay: 5
  
  slack_notify:
    channel: "#deployments"
    mention_on_failure: "@oncall"
```

**Step 3: Safety Guardrails**

- run_command: Whitelist allowed commands, no sudo
- kubectl: Staging namespace only, require confirmation for prod
- aws_cli: Read-only except for specific deployment operations
- Rollback: Automatic on health check failure
- Notifications: Always notify on deployment start/end

**Result**: Agent successfully deploys with 98% success rate, automatic rollback on failures.

---

## Example 3: Testing Agent

### Scenario
Select tools for an agent that generates tests, runs them, and reports coverage.

### Task Requirements
1. Analyze code to identify test gaps
2. Generate unit tests
3. Generate integration tests
4. Run test suites
5. Measure code coverage
6. Generate test reports

### Available Tools
- **read_file**: Read source and test files
- **write_file**: Write new test files
- **ast_parser**: Analyze code structure
- **jest**: JavaScript testing
- **pytest**: Python testing
- **coverage**: Code coverage measurement
- **test_generator_ai**: AI-powered test generation

### Tool Selection Process

**Step 1: Tool Selection Matrix**

```markdown
| Capability | Primary Tool | Fallback | Rationale |
|------------|--------------|----------|------------|
| Read code | read_file | - | Standard file reading |
| Analyze structure | ast_parser | grep | AST provides better analysis |
| Generate tests | test_generator_ai | Manual templates | AI generates better tests |
| Write tests | write_file | - | Standard file writing |
| Run JS tests | jest | - | Project uses Jest |
| Run Python tests | pytest | - | Project uses pytest |
| Measure coverage | coverage | - | Standard coverage tool |
```

**Step 2: Tool Configuration**

```json
{
  "test_generator_ai": {
    "model": "gpt-4",
    "temperature": 0.3,
    "max_tests_per_function": 5,
    "include_edge_cases": true
  },
  "jest": {
    "config": "jest.config.js",
    "coverage": true,
    "verbose": true,
    "bail": false
  },
  "pytest": {
    "config": "pytest.ini",
    "coverage": true,
    "verbose": true,
    "maxfail": 0
  },
  "coverage": {
    "threshold": 80,
    "exclude": ["tests/", "node_modules/"]
  }
}
```

**Step 3: Usage Workflow**

```markdown
1. read_file: Load source files
2. ast_parser: Analyze code structure, identify functions
3. read_file: Load existing tests
4. ast_parser: Identify test gaps
5. test_generator_ai: Generate tests for uncovered code
6. write_file: Save generated tests
7. jest/pytest: Run all tests
8. coverage: Measure coverage
9. write_file: Generate coverage report
```

**Step 4: Safety Guardrails**

- test_generator_ai: Review generated tests before saving
- write_file: Only write to tests/ directory
- jest/pytest: Run in isolated environment
- Coverage threshold: Fail if <80%

**Result**: Agent generates high-quality tests, achieving 85% coverage on average.

---

## Example 4: Documentation Agent

### Scenario
Select tools for an agent that generates and updates documentation from code.

### Task Requirements
1. Extract code documentation (docstrings, comments)
2. Analyze code structure and APIs
3. Generate API documentation
4. Update README files
5. Create usage examples
6. Validate documentation accuracy

### Available Tools
- **read_file**: Read source files
- **write_file**: Write documentation
- **ast_parser**: Parse code structure
- **jsdoc**: JavaScript documentation generator
- **sphinx**: Python documentation generator
- **markdown_validator**: Validate markdown syntax
- **link_checker**: Check documentation links

### Tool Selection

```markdown
| Requirement | Tool | Configuration |
|-------------|------|---------------|
| Read code | read_file | All source files |
| Parse structure | ast_parser | Extract functions, classes, APIs |
| Generate JS docs | jsdoc | Template: default, output: docs/ |
| Generate Python docs | sphinx | Theme: sphinx_rtd_theme |
| Write docs | write_file | Output: docs/, README.md |
| Validate markdown | markdown_validator | Strict mode |
| Check links | link_checker | Timeout: 10s per link |
```

**Tool Configuration**

```yaml
jsdoc:
  template: default
  destination: docs/api/javascript
  recurse: true
  
sphinx:
  theme: sphinx_rtd_theme
  extensions:
    - sphinx.ext.autodoc
    - sphinx.ext.napoleon
  output: docs/api/python

markdown_validator:
  strict: true
  rules:
    - no-trailing-spaces
    - no-duplicate-headings
    - no-empty-links

link_checker:
  timeout: 10
  follow_redirects: true
  ignore_patterns:
    - localhost
    - 127.0.0.1
```

**Safety Guardrails**

- read_file: Only read source and doc files
- write_file: Only write to docs/ and README.md
- link_checker: Rate limit external requests
- All tools: Dry-run mode for validation before writing

**Result**: Agent generates comprehensive, accurate documentation with 90% link validity.

---

## Summary

These examples demonstrate:

1. **Systematic tool selection** based on requirements
2. **Tool configuration** optimized for specific tasks
3. **Safety guardrails** to prevent misuse
4. **Fallback strategies** for critical operations
5. **Validation** of tool effectiveness

**Key Patterns**:
- Map requirements to tools systematically
- Configure tools for optimal performance
- Implement safety constraints
- Validate tool selection with real tasks
- Iterate based on performance data

**Version**: 1.0.0  
**Last Updated**: 2026-09-08
