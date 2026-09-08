# Troubleshooting

## Common Issues and Solutions

This guide helps you resolve common problems when using engineering skills.

## Issue: Skill Not Found

### Symptoms

```
Error: Skill 'architecture-reviw' not found
```

### Solutions

1. **Check skill name spelling**
   - Correct: `architecture-review`
   - Incorrect: `architecture-reviw`

2. **Verify skill exists in catalog**

   ```python
   # List all available skills
   for skill in loader.catalog['skills']:
       print(skill['name'])
   ```

3. **Check skill path is correct**

   ```python
   skill_path = Path("engineering-skills/skills/architecture/architecture-review")
   if not skill_path.exists():
       print(f"Path does not exist: {skill_path}")
   ```

4. **Ensure repository is up to date**

   ```bash
   cd engineering-skills
   git pull origin main
   ```

## Issue: Missing Required Inputs

### Symptoms

```
Skill execution failed: Missing required input 'architecture'
```

### Solutions

1. **Read skill's "Inputs" section**

   ```python
   skill = loader.load_skill('architecture-review')
   inputs_section = extract_section(skill['skill'], 'Inputs')
   print(inputs_section)
   ```

2. **Check previous skill outputs**

   - Ensure previous skills produced required data
   - Verify output format matches expected input

3. **Provide inputs explicitly**

   ```python
   result = executor.execute_skill(
       'architecture-review',
       inputs={
           'architecture': 'System description...',
           'requirements': 'Requirements...',
           'constraints': 'Constraints...'
       }
   )
   ```

## Issue: Skill Sequence Doesn't Make Sense

### Symptoms

- Trying to review architecture before discovering it
- Making decisions without analyzing tradeoffs
- Skipping validation steps

### Solutions

1. **Use skill-orchestrator**

   ```python
   workflow = orchestrator.orchestrate(
       problem="Your problem description",
       context={...}
   )
   ```

2. **Follow standard patterns**

   - Discover → Design → Decide → Validate
   - Analysis → Planning → Execution → Verification

3. **Check skill dependencies**

   ```python
   skill = loader.catalog['skills'].find(s => s['name'] == 'architecture-review')
   print("Requires:", skill.get('requires', []))
   print("Commonly followed by:", skill.get('commonly_followed_by', []))
   ```

## Issue: Output Doesn't Match Expected Format

### Symptoms

- Skill produces narrative instead of structured output
- Missing required sections
- Format doesn't match examples

### Solutions

1. **Explicitly reference "Expected Outputs" section**

   ```python
   system_prompt = f"""
   Execute {skill_name} skill.

   {skill['skill']}

   IMPORTANT: Produce outputs EXACTLY matching the "Expected Outputs" section.
   Use the examples in examples.md as reference for format.
   """
   ```

2. **Provide output format template**

   ```python
   user_prompt = f"""
   {inputs}

   Output format:
   ## Findings
   [List findings here]

   ## Recommendations
   [List recommendations here]
   """
   ```

3. **Use examples as reference**

   ```python
   examples = skill['examples']
   system_prompt = f"""
   {skill['skill']}

   Examples of correct output format:
   {examples}
   """
   ```

## Issue: Context Window Exceeded

### Symptoms

```
Error: Context length exceeded (max: 200000 tokens)
```

### Solutions

1. **Load minimal skill content**

   ```python
   def load_skill_minimal(skill_name):
       """Load only essential parts"""
       skill = loader.load_skill(skill_name)
       return {
           'name': skill['metadata']['name'],
           'purpose': extract_section(skill['skill'], 'Purpose'),
           'workflow': extract_section(skill['skill'], 'Workflow'),
           'outputs': extract_section(skill['skill'], 'Expected Outputs')
       }
   ```

2. **Summarize previous outputs**

   ```python
   def summarize_output(output, max_length=1000):
       """Summarize skill output"""
       if len(output) <= max_length:
           return output

       # Use LLM to summarize
       summary = client.messages.create(
           model="claude-3-5-sonnet-20241022",
           max_tokens=500,
           messages=[{
               "role": "user",
               "content": f"Summarize in {max_length} chars: {output}"
           }]
       )
       return summary.content[0].text
   ```

3. **Use skill-orchestrator to reduce skills**

   - Let orchestrator select minimal skill set
   - Avoid unnecessary skills
   - Focus on essential analysis

4. **Split complex problems**

   - Break into smaller sub-problems
   - Execute separately
   - Combine results

## Issue: Slow Skill Execution

### Symptoms

- Skills taking longer than expected
- Timeout errors
- Poor performance

### Solutions

1. **Parallelize independent skills**

   ```python
   import asyncio

   async def execute_parallel(skill_names, inputs):
       tasks = [
           executor.execute_skill(name, inputs)
           for name in skill_names
       ]
       return await asyncio.gather(*tasks)
   ```

2. **Cache skill content**

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

3. **Optimize context size**

   - Load only necessary sections
   - Remove redundant information
   - Compress large inputs

4. **Use streaming for long outputs**

   ```python
   async def execute_streaming(skill_name, inputs):
       async with client.messages.stream(...) as stream:
           async for text in stream.text_stream:
               yield text
   ```

## Issue: Inconsistent Results

### Symptoms

- Different outputs for same inputs
- Quality varies between runs
- Unpredictable behavior

### Solutions

1. **Set temperature to 0 for consistency**

   ```python
   response = client.messages.create(
       model="claude-3-5-sonnet-20241022",
       temperature=0,  # Deterministic
       ...
   )
   ```

2. **Provide more specific instructions**

   - Be explicit about requirements
   - Include examples
   - Specify output format

3. **Validate outputs**

   ```python
   def validate_output(skill_name, output):
       skill = loader.load_skill(skill_name)
       expected = extract_section(skill['skill'], 'Expected Outputs')
       # Check if output matches expected format
       return validate_format(output, expected)
   ```

## Issue: Integration Problems

### Symptoms

- Skills not working with your tool
- Context not being used
- Outputs not formatted correctly

### Solutions

1. **Check tool compatibility**

   - Verify tool supports file context or API integration
   - Review integration patterns in documentation
   - Test with simple skill first

2. **Adapt skill format**

   ```python
   def adapt_for_tool(skill, tool_name):
       if tool_name == "copilot":
           return format_for_copilot(skill)
       elif tool_name == "cursor":
           return format_for_cursor(skill)
       # ...
   ```

3. **Use appropriate integration method**

   - File-based: Add skills to workspace
   - API-based: Create API wrapper
   - Plugin: Develop tool-specific extension

## Debugging Tips

### 1. Enable Verbose Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

class DebugSkillExecutor(SkillExecutor):
    def execute_skill(self, skill_name, inputs):
        logger.debug(f"Executing: {skill_name}")
        logger.debug(f"Inputs: {inputs}")

        result = super().execute_skill(skill_name, inputs)

        logger.debug(f"Output length: {len(result['output'])}")
        logger.debug(f"Preview: {result['output'][:200]}...")

        return result
```

### 2. Validate Skill Outputs

```python
def validate_skill_output(skill_name, output):
    """Validate output matches expected format"""
    skill = loader.load_skill(skill_name)
    expected_outputs = extract_section(skill['skill'], 'Expected Outputs')

    for expected in parse_expected_outputs(expected_outputs):
        if expected not in output:
            logger.warning(f"Missing: {expected}")
            return False

    return True
```

### 3. Trace Skill Execution

```python
import time

class TracedSkillExecutor(SkillExecutor):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.trace = []

    def execute_skill(self, skill_name, inputs):
        start = time.time()
        result = super().execute_skill(skill_name, inputs)
        end = time.time()

        self.trace.append({
            'skill': skill_name,
            'duration': end - start,
            'input_size': len(str(inputs)),
            'output_size': len(result['output']),
            'timestamp': start
        })

        return result

    def print_trace(self):
        print("\nExecution Trace:")
        for entry in self.trace:
            print(f"{entry['skill']}: {entry['duration']:.2f}s "
                  f"(in: {entry['input_size']}, out: {entry['output_size']})")
```

### 4. Test with Simple Examples

```python
# Start with minimal test
test_result = executor.execute_skill(
    'architecture-review',
    inputs={
        'architecture': 'Simple monolith with database',
        'requirements': 'Basic CRUD operations',
        'constraints': 'None'
    }
)

print(test_result['output'])
```

### 5. Verify Skill Files

```python
def verify_skill_files(skill_name):
    """Check if all skill files exist"""
    skill_info = loader._find_skill(skill_name)
    skill_path = Path(skill_info['path'])

    required_files = ['SKILL.md', 'instructions.md', 'examples.md']
    missing = []

    for file in required_files:
        if not (skill_path / file).exists():
            missing.append(file)

    if missing:
        print(f"Missing files for {skill_name}: {missing}")
        return False

    return True
```

## Getting Help

### 1. Check Documentation

- Review skill's SKILL.md file
- Read instructions.md for detailed workflow
- Check examples.md for reference

### 2. Search Issues

- Check GitHub issues for similar problems
- Look for known limitations
- Review closed issues for solutions

### 3. Ask the Community

- Open a GitHub discussion
- Provide minimal reproducible example
- Include error messages and logs

### 4. Report Bugs

- Open GitHub issue with:
  - Skill name and version
  - Steps to reproduce
  - Expected vs actual behavior
  - Error messages and logs
  - Environment details

## Related Documentation

- [Back to Overview](01-overview.md)
- [Best Practices](07-best-practices.md)
- [Examples and Use Cases](09-examples.md)
- [Agentic Engineering Guide](08-agentic-engineering.md)