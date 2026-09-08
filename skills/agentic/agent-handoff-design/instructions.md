# Agent Handoff Design - Step-by-Step Instructions

This guide provides executable instructions for designing effective handoffs between agents in multi-agent workflows.

---

## Overview

Agent handoff design ensures seamless information transfer and context continuity when one agent passes work to another. Follow these steps to create robust, efficient handoffs.

**Time Required**: 2-3 hours  
**Complexity**: Advanced  
**Prerequisites**: Completed agent-task-decomposition, agent-workflow-design, agent-context-engineering

---

## Step-by-Step Process

### Step 1: Map Agent Workflow (20-30 minutes)

**Objective**: Create a complete map of your multi-agent workflow.

**Instructions**:

1. **List all agents**:
   ```
   Agent 1: Code Analyzer
   Agent 2: Code Reviewer
   Agent 3: Code Fixer
   Agent 4: Test Runner
   ```

2. **Document agent responsibilities**:
   - What each agent does
   - What each agent produces
   - What each agent consumes

3. **Identify handoff points**:
   - Where Agent 1 → Agent 2
   - Where Agent 2 → Agent 3
   - Parallel vs. sequential handoffs

4. **Create workflow diagram**:
   ```
   [Code Analyzer] → [Code Reviewer] → [Code Fixer] → [Test Runner]
                           ↓
                    [Documentation Generator]
   ```

**Validation**:
- [ ] All agents identified
- [ ] All handoff points marked
- [ ] Parallel branches identified
- [ ] Agent responsibilities clear

**Common Pitfalls**:
- Missing implicit handoffs (e.g., shared state)
- Not identifying parallel handoffs
- Unclear agent boundaries

---

### Step 2: Analyze Context Dependencies (20-30 minutes)

**Objective**: Determine what information each agent needs from predecessors.

**Instructions**:

1. **For each agent, list required inputs**:
   ```
   Code Reviewer needs:
   - Source code files (from Code Analyzer)
   - Analysis results (from Code Analyzer)
   - Code quality metrics (from Code Analyzer)
   - Project context (from initial request)
   ```

2. **Trace inputs back to producing agents**:
   - Who produces each input?
   - Is it direct (previous agent) or indirect (earlier agent)?

3. **Identify context that must persist**:
   - Project name, version
   - User preferences
   - Workflow ID
   - Accumulated results

4. **Identify context that can be discarded**:
   - Temporary files
   - Intermediate calculations
   - Debug information

**Validation**:
- [ ] All inputs traced to sources
- [ ] Persistent context identified
- [ ] Discardable context identified
- [ ] No missing dependencies

**Common Pitfalls**:
- Missing implicit dependencies (assumptions)
- Not identifying transitive dependencies
- Over-transferring context

---

### Step 3: Design Context Transfer Schema (30-40 minutes)

**Objective**: Define exactly what information passes at each handoff.

**Instructions**:

1. **For each handoff, create a schema**:
   ```json
   {
     "handoff": "CodeAnalyzer → CodeReviewer",
     "schema": {
       "task_results": {
         "analyzed_files": ["string"],
         "issues_found": ["object"],
         "metrics": "object"
       },
       "metadata": {
         "agent_id": "string",
         "timestamp": "ISO8601",
         "version": "string"
       },
       "context": {
         "project_name": "string",
         "workflow_id": "string",
         "user_preferences": "object"
       },
       "state": {
         "completed_tasks": ["string"],
         "remaining_tasks": ["string"],
         "errors": ["object"]
       }
     }
   }
   ```

2. **Specify required vs. optional fields**:
   - Mark required fields with `"required": true`
   - Document defaults for optional fields

3. **Add validation rules**:
   ```json
   {
     "analyzed_files": {
       "type": "array",
       "minItems": 1,
       "items": {"type": "string", "pattern": "^.*\\.(js|ts|py)$"}
     }
   }
   ```

4. **Define data format**:
   - JSON for structured data
   - File paths for large files
   - URLs for external resources

**Validation**:
- [ ] Schema complete and unambiguous
- [ ] Required fields marked
- [ ] Validation rules defined
- [ ] Data format specified

**Common Pitfalls**:
- Ambiguous field names
- Missing validation rules
- No versioning strategy

---

### Step 4: Define Handoff Protocols (20-30 minutes)

**Objective**: Specify how handoffs occur.

**Instructions**:

1. **Choose handoff mechanism**:

   **Synchronous** (agent waits):
   ```python
   result = agent1.execute()
   agent2.execute(result)  # Waits for agent1
   ```

   **Asynchronous** (agent completes, next starts later):
   ```python
   agent1.execute()  # Returns immediately
   queue.push(result)
   # Later...
   result = queue.pop()
   agent2.execute(result)
   ```

   **Event-driven** (triggered by events):
   ```python
   @on_event("agent1_complete")
   def start_agent2(event):
       agent2.execute(event.data)
   ```

2. **Define trigger conditions**:
   ```
   Agent 2 starts when:
   - Agent 1 completes successfully
   - Required context is available
   - No blocking errors
   ```

3. **Specify validation**:
   ```python
   def validate_handoff(context):
       assert context["analyzed_files"], "No files analyzed"
       assert len(context["issues_found"]) > 0, "No issues found"
       return True
   ```

4. **Define timeout and retry**:
   ```
   Timeout: 30 seconds
   Retries: 3 attempts
   Backoff: Exponential (1s, 2s, 4s)
   ```

**Validation**:
- [ ] Mechanism chosen and justified
- [ ] Trigger conditions clear
- [ ] Validation implemented
- [ ] Timeout and retry defined

**Common Pitfalls**:
- Using synchronous when async would work
- No timeout (hangs forever)
- No retry (fails on transient errors)

---

### Step 5: Implement Context Continuity (20-30 minutes)

**Objective**: Ensure no information loss across handoffs.

**Instructions**:

1. **Identify critical context**:
   ```
   Critical (must not lose):
   - Workflow ID
   - User request
   - Project metadata
   - Accumulated results

   Non-critical (can regenerate):
   - Temporary files
   - Cache data
   ```

2. **Choose accumulation strategy**:

   **Append** (add to growing context):
   ```json
   {
     "results": [
       {"agent": "analyzer", "output": "..."},
       {"agent": "reviewer", "output": "..."},
       {"agent": "fixer", "output": "..."}
     ]
   }
   ```

   **Replace** (new context replaces old):
   ```json
   {
     "current_state": "reviewing",
     "current_files": ["file1.js", "file2.js"]
   }
   ```

   **Merge** (combine old and new):
   ```json
   {
     "files": ["file1.js", "file2.js", "file3.js"],  // Merged
     "metrics": {"complexity": 15, "coverage": 80}  // Merged
   }
   ```

3. **Implement versioning**:
   ```json
   {
     "context_version": "1.2.0",
     "schema_version": "2.0",
     "data": {...}
   }
   ```

4. **Add validation**:
   ```python
   def validate_context_continuity(old_context, new_context):
       assert new_context["workflow_id"] == old_context["workflow_id"]
       assert new_context["context_version"] >= old_context["context_version"]
   ```

**Validation**:
- [ ] Critical context identified
- [ ] Accumulation strategy chosen
- [ ] Versioning implemented
- [ ] Validation added

**Common Pitfalls**:
- Losing critical context
- Context bloat (accumulating too much)
- No versioning (compatibility issues)

---

### Step 6: Design Error Handling (20-30 minutes)

**Objective**: Handle handoff failures gracefully.

**Instructions**:

1. **Identify potential failures**:
   ```
   Failure Modes:
   - Agent unavailable (crashed, offline)
   - Invalid context format (schema mismatch)
   - Missing required data (incomplete handoff)
   - Timeout (agent too slow)
   - Validation failure (data doesn't meet criteria)
   ```

2. **Define detection mechanisms**:
   ```python
   try:
       validate_schema(context)
   except ValidationError as e:
       handle_invalid_context(e)

   if time.time() - start_time > TIMEOUT:
       handle_timeout()
   ```

3. **Specify recovery strategies**:

   **Retry**:
   ```python
   for attempt in range(MAX_RETRIES):
       try:
           handoff(context)
           break
       except TransientError:
           time.sleep(BACKOFF ** attempt)
   ```

   **Fallback**:
   ```python
   try:
       primary_agent.execute(context)
   except AgentUnavailable:
       fallback_agent.execute(context)
   ```

   **Rollback**:
   ```python
   checkpoint = save_state()
   try:
       agent.execute(context)
   except Error:
       restore_state(checkpoint)
   ```

   **Escalate**:
   ```python
   if not recoverable(error):
       notify_human(error)
       pause_workflow()
   ```

4. **Implement logging**:
   ```python
   logger.error(f"Handoff failed: {agent1} → {agent2}", 
                extra={"context": context, "error": str(e)})
   ```

**Validation**:
- [ ] All failure modes identified
- [ ] Detection mechanisms implemented
- [ ] Recovery strategies defined
- [ ] Logging and notification added

**Common Pitfalls**:
- Not handling all failure modes
- Retrying non-transient errors
- No logging (can't debug failures)

---

### Step 7: Optimize Handoff Performance (15-20 minutes)

**Objective**: Minimize handoff latency and overhead.

**Instructions**:

1. **Measure baseline**:
   ```python
   start = time.time()
   handoff(context)
   latency = time.time() - start
   print(f"Handoff latency: {latency}s")
   ```

2. **Identify bottlenecks**:
   ```
   Bottlenecks:
   - Large context transfer (500KB)
   - Synchronous wait (5s)
   - Redundant data (duplicated fields)
   ```

3. **Optimize context transfer**:

   **Compress large data**:
   ```python
   import gzip
   compressed = gzip.compress(json.dumps(context).encode())
   ```

   **Transfer only necessary information**:
   ```python
   # Before: 500KB
   context = {"full_file_content": "...", "analysis": "..."}

   # After: 50KB
   context = {"file_path": "/path/to/file", "analysis": "..."}
   ```

   **Use references**:
   ```python
   # Instead of full data
   context = {"data_url": "s3://bucket/data.json"}
   ```

4. **Optimize mechanism**:

   **Use async**:
   ```python
   # Before: Synchronous (blocks)
   result = agent1.execute()
   agent2.execute(result)

   # After: Asynchronous (doesn't block)
   agent1.execute_async()
   # Agent 2 starts when ready
   ```

   **Batch handoffs**:
   ```python
   # Instead of 10 separate handoffs
   batch_handoff([context1, context2, ..., context10])
   ```

   **Parallel handoffs**:
   ```python
   # Start multiple agents in parallel
   asyncio.gather(
       agent2.execute(context),
       agent3.execute(context),
       agent4.execute(context)
   )
   ```

**Validation**:
- [ ] Baseline measured
- [ ] Bottlenecks identified
- [ ] Optimizations implemented
- [ ] Performance improved

**Common Pitfalls**:
- Premature optimization
- Over-compressing (CPU overhead)
- Losing data in optimization

---

### Step 8: Implement Handoff Validation (15-20 minutes)

**Objective**: Verify handoffs succeed and context is correct.

**Instructions**:

1. **Define validation criteria**:
   ```python
   def validate_handoff(context):
       # Required fields present
       assert "workflow_id" in context
       assert "task_results" in context

       # Data types correct
       assert isinstance(context["analyzed_files"], list)

       # Values within expected ranges
       assert len(context["analyzed_files"]) > 0
       assert context["metrics"]["complexity"] >= 0

       # Context consistent
       assert context["workflow_id"] == expected_workflow_id
   ```

2. **Implement at handoff boundaries**:
   ```python
   def handoff(context):
       validate_handoff(context)  # Validate before transfer
       transfer(context)
       validate_received(context)  # Validate after transfer
   ```

3. **Define failure actions**:
   ```python
   try:
       validate_handoff(context)
   except ValidationError as e:
       log_validation_failure(e)
       if is_critical(e):
           raise  # Stop workflow
       else:
           fix_context(context)  # Attempt repair
   ```

4. **Add logging and metrics**:
   ```python
   metrics.increment("handoff.validation.success")
   logger.info("Handoff validated", extra={"context_size": len(context)})
   ```

**Validation**:
- [ ] Validation criteria defined
- [ ] Validation implemented at boundaries
- [ ] Failure actions specified
- [ ] Logging and metrics added

**Common Pitfalls**:
- Too strict validation (false positives)
- Too loose validation (misses errors)
- No logging (can't debug)

---

### Step 9: Test Handoff Scenarios (30-40 minutes)

**Objective**: Validate handoff design with real workflows.

**Instructions**:

1. **Test happy path**:
   ```python
   def test_happy_path():
       context = create_valid_context()
       result = agent1.execute(context)
       assert handoff_successful(result)
       result = agent2.execute(result)
       assert handoff_successful(result)
   ```

2. **Test error scenarios**:

   **Agent failure**:
   ```python
   def test_agent_failure():
       with mock.patch.object(agent2, 'execute', side_effect=AgentCrashed):
           result = workflow.execute()
           assert result.status == "failed"
           assert result.error == "Agent 2 crashed"
   ```

   **Invalid context**:
   ```python
   def test_invalid_context():
       context = {"invalid": "data"}
       with pytest.raises(ValidationError):
           handoff(context)
   ```

   **Timeout**:
   ```python
   def test_timeout():
       with mock.patch.object(agent2, 'execute', side_effect=lambda: time.sleep(100)):
           with pytest.raises(TimeoutError):
               handoff(context, timeout=1)
   ```

3. **Test edge cases**:

   **Large context**:
   ```python
   def test_large_context():
       context = create_context(size_mb=10)
       start = time.time()
       handoff(context)
       assert time.time() - start < 5  # Should complete in 5s
   ```

   **Many parallel handoffs**:
   ```python
   def test_parallel_handoffs():
       contexts = [create_context() for _ in range(100)]
       results = parallel_handoff(contexts)
       assert all(r.success for r in results)
   ```

   **Long workflow chains**:
   ```python
   def test_long_chain():
       result = chain_execute([agent1, agent2, ..., agent10], context)
       assert result.success
       assert no_context_loss(result)
   ```

4. **Measure performance**:
   ```python
   latencies = [measure_handoff_latency() for _ in range(100)]
   print(f"P50: {percentile(latencies, 50)}ms")
   print(f"P95: {percentile(latencies, 95)}ms")
   print(f"P99: {percentile(latencies, 99)}ms")
   ```

**Validation**:
- [ ] Happy path tested
- [ ] Error scenarios tested
- [ ] Edge cases tested
- [ ] Performance measured
- [ ] Issues identified and fixed

**Common Pitfalls**:
- Only testing happy path
- Not testing at scale
- Not measuring performance

---

### Step 10: Document and Monitor (15-20 minutes)

**Objective**: Create reusable handoff patterns and track performance.

**Instructions**:

1. **Document handoff design**:
   ```markdown
   # Agent Handoff Design

   ## Overview
   This workflow uses 4 agents with 3 handoff points.

   ## Handoff Points
   1. Code Analyzer → Code Reviewer (synchronous)
   2. Code Reviewer → Code Fixer (asynchronous)
   3. Code Fixer → Test Runner (event-driven)

   ## Context Schema
   [Include schema for each handoff]

   ## Error Handling
   [Document recovery strategies]
   ```

2. **Create handoff templates**:
   ```python
   # Template: Synchronous Handoff
   def synchronous_handoff(agent1, agent2, context):
       result = agent1.execute(context)
       validate_handoff(result)
       return agent2.execute(result)

   # Template: Asynchronous Handoff
   def async_handoff(agent1, agent2, context):
       agent1.execute_async(context, on_complete=lambda r: agent2.execute(r))
   ```

3. **Implement monitoring**:
   ```python
   # Handoff success rate
   metrics.gauge("handoff.success_rate", 
                 successful_handoffs / total_handoffs)

   # Handoff latency
   metrics.histogram("handoff.latency", latency_ms)

   # Context transfer size
   metrics.histogram("handoff.context_size", len(context))

   # Error frequency
   metrics.counter("handoff.errors", tags={"error_type": error_type})
   ```

4. **Set up alerts**:
   ```yaml
   alerts:
     - name: High Handoff Failure Rate
       condition: handoff.success_rate < 0.95
       action: notify_team

     - name: High Handoff Latency
       condition: handoff.latency.p95 > 5000  # 5s
       action: investigate
   ```

5. **Plan reviews**:
   ```
   Review Schedule:
   - Weekly: Review handoff metrics
   - Monthly: Optimize slow handoffs
   - Quarterly: Update handoff patterns
   ```

**Validation**:
- [ ] Handoff design documented
- [ ] Templates created
- [ ] Monitoring implemented
- [ ] Alerts configured
- [ ] Review schedule planned

**Common Pitfalls**:
- No documentation (can't reuse)
- No monitoring (can't detect issues)
- No alerts (issues go unnoticed)

---

## Quick Reference

### Handoff Checklist

- [ ] **Workflow Mapped**: All agents and handoff points identified
- [ ] **Dependencies Analyzed**: Context requirements documented
- [ ] **Schema Defined**: Transfer schema for each handoff
- [ ] **Protocol Specified**: Handoff mechanism chosen
- [ ] **Continuity Ensured**: No information loss
- [ ] **Errors Handled**: Recovery strategies defined
- [ ] **Performance Optimized**: Latency acceptable
- [ ] **Validation Implemented**: Handoffs verified
- [ ] **Testing Complete**: All scenarios tested
- [ ] **Documentation Done**: Design documented and monitored

---

## Troubleshooting

### Issue: Handoffs Failing

**Symptoms**: High failure rate, validation errors

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
2. Identify bottlenecks (context size, synchronous waits)
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

**Version**: 1.0.0  
**Last Updated**: 2026-09-08