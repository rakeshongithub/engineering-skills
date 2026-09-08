# Agent Handoff Design - Quick Reference

Design effective handoffs between agents in multi-agent workflows.

---

## Quick Start

**Purpose**: Ensure seamless information transfer and context continuity when one agent passes work to another.

**When to Use**: Multi-agent workflows where agents collaborate on tasks.

**Time Required**: 2-3 hours

---

## Handoff Design Template

### 1. Identify Handoff Points

```
Workflow: [Agent 1] → [Agent 2] → [Agent 3]

Handoff Points:
- Agent 1 → Agent 2
- Agent 2 → Agent 3
```

### 2. Define Context Transfer Schema

```json
{
  "handoff_id": "<unique_id>",
  "timestamp": "<ISO8601>",
  "source_agent": {
    "name": "<agent_name>",
    "version": "<version>"
  },
  "task_results": {
    "<output_key>": "<output_value>"
  },
  "metadata": {
    "workflow_id": "<workflow_id>",
    "project_name": "<project>"
  },
  "context": {
    "<context_key>": "<context_value>"
  },
  "state": {
    "completed_tasks": ["<task1>", "<task2>"],
    "remaining_tasks": ["<task3>", "<task4>"]
  }
}
```

### 3. Choose Handoff Mechanism

**Synchronous** (agent waits):
```python
result = agent1.execute()
agent2.execute(result)
```

**Asynchronous** (queue-based):
```python
agent1.execute()  # Returns immediately
queue.push(result)
# Later...
result = queue.pop()
agent2.execute(result)
```

**Event-Driven** (triggered by events):
```python
@on_event("agent1_complete")
def start_agent2(event):
    agent2.execute(event.data)
```

### 4. Implement Validation

```python
def validate_handoff(context):
    # Required fields present
    assert "task_results" in context
    
    # Data types correct
    assert isinstance(context["task_results"], dict)
    
    # Values valid
    assert len(context["task_results"]) > 0
    
    return True
```

### 5. Add Error Handling

```python
try:
    validate_handoff(context)
    agent2.execute(context)
except ValidationError as e:
    logger.error(f"Handoff validation failed: {e}")
    retry_handoff()
except TimeoutError:
    logger.error("Handoff timed out")
    use_fallback_agent()
except Exception as e:
    logger.error(f"Handoff failed: {e}")
    notify_human()
```

---

## Handoff Mechanism Decision Tree

```
Are agents tightly coupled?
├─ Yes → Synchronous handoff
└─ No → Can handoff be delayed?
   ├─ Yes → Asynchronous handoff
   └─ No → Event-driven handoff

Is context large (>1MB)?
├─ Yes → Use references/pointers
└─ No → Direct transfer OK

Are there multiple receiving agents?
├─ Yes → Broadcast/fan-out
└─ No → Point-to-point
```

---

## Context Transfer Strategies

| Scenario | Strategy | Rationale |
|----------|----------|----------|
| Short workflow (2-3 agents) | Full context transfer | Simple, no accumulation issues |
| Long workflow (5+ agents) | Incremental context | Prevents context bloat |
| Parallel agents | Shared context store | Avoids duplication |
| Independent agents | Minimal context | Only essential information |
| Stateful workflow | Context accumulation | Preserve full history |
| Stateless workflow | Context replacement | Only current state matters |

---

## Quality Checklist

### Completeness
- [ ] All handoff points identified
- [ ] Context requirements documented
- [ ] Transfer schema defined
- [ ] Error handling specified
- [ ] Validation criteria defined

### Correctness
- [ ] No information loss
- [ ] Context format matches expectations
- [ ] All dependencies satisfied
- [ ] Validation catches errors

### Performance
- [ ] Handoff latency acceptable (<1s)
- [ ] Context transfer size optimized
- [ ] No unnecessary synchronous waits
- [ ] Parallel handoffs where possible

### Robustness
- [ ] Error handling for all failure modes
- [ ] Retry and rollback mechanisms
- [ ] Monitoring and alerting
- [ ] Graceful degradation

---

## Common Handoff Patterns

### Pattern 1: Sequential Pipeline

```
[Agent A] → [Agent B] → [Agent C]
```

**Use When**: Linear workflow, each agent depends on previous

**Handoff Type**: Synchronous or asynchronous

**Example**: Code analysis → Code review → Code fix

---

### Pattern 2: Parallel Fan-Out

```
              ┌─ [Agent B1]
[Agent A] ────├─ [Agent B2]
              └─ [Agent B3]
```

**Use When**: Multiple agents process same input independently

**Handoff Type**: Broadcast, asynchronous

**Example**: Build → (Unit tests, Integration tests, E2E tests)

---

### Pattern 3: Parallel Fan-In

```
[Agent A1] ──┐
[Agent A2] ───├─ [Agent B]
[Agent A3] ──┘
```

**Use When**: Multiple agents produce inputs for single agent

**Handoff Type**: Synchronous (wait for all) or asynchronous (process as available)

**Example**: (Frontend tests, Backend tests, API tests) → Report generator

---

### Pattern 4: Conditional Branching

```
              ┌─ [Agent B] (if condition)
[Agent A] ────┤
              └─ [Agent C] (else)
```

**Use When**: Next agent depends on previous agent's output

**Handoff Type**: Event-driven

**Example**: Security scan → (Deploy if passed, Notify team if failed)

---

## Performance Optimization Tips

### 1. Reduce Context Size

**Before**:
```json
{
  "full_file_content": "<500KB of code>",
  "analysis": "..."
}
```

**After**:
```json
{
  "file_path": "s3://bucket/file.js",
  "analysis": "..."
}
```

**Improvement**: 90% reduction in transfer size

---

### 2. Use Async Handoffs

**Before** (synchronous):
```python
result = agent1.execute()  # Blocks
agent2.execute(result)     # Waits
```

**After** (asynchronous):
```python
agent1.execute_async()  # Returns immediately
# Agent 2 starts when ready
```

**Improvement**: 40% reduction in total latency

---

### 3. Batch Handoffs

**Before** (individual):
```python
for item in items:
    handoff(item)  # 100 separate handoffs
```

**After** (batched):
```python
batch_handoff(items)  # 1 batched handoff
```

**Improvement**: 10x throughput increase

---

### 4. Compress Large Data

```python
import gzip
compressed = gzip.compress(json.dumps(context).encode())
```

**Improvement**: 60% reduction in transfer time

---

## Error Handling Patterns

### Retry with Exponential Backoff

```python
for attempt in range(MAX_RETRIES):
    try:
        handoff(context)
        break
    except TransientError:
        time.sleep(BACKOFF ** attempt)
```

---

### Fallback Agent

```python
try:
    primary_agent.execute(context)
except AgentUnavailable:
    fallback_agent.execute(context)
```

---

### Rollback on Failure

```python
checkpoint = save_state()
try:
    agent.execute(context)
except Error:
    restore_state(checkpoint)
```

---

### Escalate to Human

```python
if not recoverable(error):
    notify_human(error)
    pause_workflow()
```

---

## Monitoring Metrics

### Success Metrics

```python
# Handoff success rate
metrics.gauge("handoff.success_rate", 
              successful_handoffs / total_handoffs)

# Target: >99%
```

### Performance Metrics

```python
# Handoff latency
metrics.histogram("handoff.latency", latency_ms)

# Target: <1s (P95)
```

### Context Metrics

```python
# Context transfer size
metrics.histogram("handoff.context_size", len(context))

# Target: <100KB average
```

### Error Metrics

```python
# Error frequency
metrics.counter("handoff.errors", tags={"error_type": error_type})

# Target: <1% error rate
```

---

## Troubleshooting Guide

### Issue: High Failure Rate

**Symptoms**: Handoffs failing frequently

**Diagnosis**:
1. Check validation logs
2. Inspect failed context
3. Verify schema compatibility

**Solutions**:
- Fix schema mismatches
- Add missing required fields
- Improve validation rules

---

### Issue: High Latency

**Symptoms**: Slow handoffs, timeouts

**Diagnosis**:
1. Measure handoff latency
2. Identify bottlenecks
3. Profile context transfer

**Solutions**:
- Compress large context
- Use async handoffs
- Transfer only necessary data

---

### Issue: Context Loss

**Symptoms**: Missing data, incomplete results

**Diagnosis**:
1. Compare context before/after handoff
2. Check accumulation strategy
3. Verify context continuity

**Solutions**:
- Fix accumulation logic
- Add context validation
- Implement versioning

---

## Related Skills

- **agent-task-decomposition**: Breaking work into agent-sized tasks
- **agent-workflow-design**: Designing multi-agent workflows
- **agent-context-engineering**: Engineering context for agents
- **agent-guardrails**: Adding safety to handoffs
- **agent-observability**: Monitoring handoff performance

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **Code Review Workflow**: Synchronous handoffs between analysis, review, and fix agents
2. **CI/CD Pipeline**: Asynchronous and event-driven handoffs in build/test/deploy pipeline
3. **Bug Triage System**: Queue-based handoffs for high-volume bug processing
4. **Documentation Generation**: Mixed handoff patterns for doc extraction, generation, and publishing

---

## Additional Resources

- [SKILL.md](./SKILL.md): Comprehensive documentation
- [instructions.md](./instructions.md): Step-by-step process
- [skill.json](./skill.json): Machine-readable metadata

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Advanced  
**Estimated Time**: 2-3 hours