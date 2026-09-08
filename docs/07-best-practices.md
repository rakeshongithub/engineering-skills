# Best Practices

## When to Use Which Skills

### Use Single Skills When:

- Problem is well-defined and narrow
- You know exactly which skill you need
- Time is limited
- Output requirements are clear

**Example:**

```
I need to review this API design.
→ Use: api-design-review
```

### Use Skill Orchestrator When:

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

### Use Workflow Recipes When:

- Problem matches a common pattern
- You want a proven approach
- You need to explain the process to others
- You're training team members

**Example:**

```
We need to migrate our database.
→ Use: migration workflow recipe
```

## Combining Multiple Skills Effectively

### Sequential Composition

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

### Parallel Composition

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

### Conditional Composition

Skill selection based on context:

```
architecture-discovery
    ↓
IF (monolith) → service-boundary-analysis
IF (microservices) → integration-design
    ↓
architecture-review
```

## Performance Optimization Tips

### 1. Cache Skill Content

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

### 2. Load Only What You Need

```python
# Instead of loading entire skill
skill = loader.load_skill('architecture-review')

# Load only metadata for selection
metadata = loader.get_skill_metadata('architecture-review')

# Load full content only when executing
if should_execute:
    skill = loader.load_skill('architecture-review')
```

### 3. Parallelize Independent Skills

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

### 4. Use Streaming for Long Outputs

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

## Context Management

### 1. Minimize Context Size

- Load only essential skill sections
- Summarize previous outputs
- Use references instead of full content

### 2. Structure Context Clearly

```python
context = {
    'overview': 'Brief system description',
    'architecture': 'Current architecture',
    'requirements': 'Key requirements',
    'constraints': 'Known constraints',
    'previous_outputs': {
        'requirements-analysis': 'Summary...',
        'system-design': 'Summary...'
    }
}
```

### 3. Use Skill Metadata

- Check required inputs before execution
- Validate outputs match expected format
- Track skill dependencies

## Quality Assurance

### 1. Validate Skill Outputs

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

### 2. Use Quality Checklists

- Reference skill's quality checklist
- Validate against checklist items
- Document any deviations

### 3. Implement Feedback Loops

- Collect user feedback on skill outputs
- Track success metrics
- Iterate on skill improvements

## Error Handling

### 1. Graceful Degradation

```python
def execute_skill_with_fallback(skill_name, inputs):
    try:
        return executor.execute_skill(skill_name, inputs)
    except SkillNotFoundError:
        # Try alternative skill
        alternative = get_alternative_skill(skill_name)
        return executor.execute_skill(alternative, inputs)
    except Exception as e:
        # Log error and return partial result
        logger.error(f"Skill execution failed: {e}")
        return {'error': str(e), 'partial_result': None}
```

### 2. Retry Logic

```python
def execute_skill_with_retry(skill_name, inputs, max_retries=3):
    for attempt in range(max_retries):
        try:
            return executor.execute_skill(skill_name, inputs)
        except TemporaryError as e:
            if attempt < max_retries - 1:
                time.sleep(2 ** attempt)  # Exponential backoff
                continue
            raise
```

### 3. Comprehensive Logging

```python
import logging

logger = logging.getLogger(__name__)

class LoggingSkillExecutor(SkillExecutor):
    def execute_skill(self, skill_name, inputs):
        logger.info(f"Executing skill: {skill_name}")
        logger.debug(f"Inputs: {inputs}")

        try:
            result = super().execute_skill(skill_name, inputs)
            logger.info(f"Skill completed: {skill_name}")
            logger.debug(f"Output length: {len(result['output'])}")
            return result
        except Exception as e:
            logger.error(f"Skill failed: {skill_name}", exc_info=True)
            raise
```

## Team Collaboration

### 1. Document Skill Usage

- Record which skills were used
- Document why specific skills were chosen
- Share learnings with team

### 2. Create Team Workflows

- Define standard skill sequences for common tasks
- Document in team wiki or README
- Update based on team feedback

### 3. Share Skill Customizations

- If you customize skills, share with team
- Contribute improvements back to library
- Maintain team-specific skill extensions

## Security Considerations

### 1. Validate Inputs

- Sanitize user inputs before passing to skills
- Validate file paths and references
- Check for injection attacks

### 2. Control Access

- Restrict skill execution permissions
- Audit skill usage
- Implement rate limiting

### 3. Protect Sensitive Data

- Don't include secrets in skill inputs
- Redact sensitive information from outputs
- Use secure context storage

## Continuous Improvement

### 1. Collect Metrics

- Track skill execution time
- Measure output quality
- Monitor error rates

### 2. Analyze Usage Patterns

- Identify frequently used skills
- Find skill composition patterns
- Optimize common workflows

### 3. Iterate on Skills

- Update skills based on feedback
- Add new examples
- Improve instructions

## Related Documentation

- [Back to Overview](01-overview.md)
- [Troubleshooting](10-troubleshooting.md)
- [Examples and Use Cases](09-examples.md)
- [Agentic Engineering Guide](08-agentic-engineering.md)