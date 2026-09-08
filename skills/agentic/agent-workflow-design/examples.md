# Agent Workflow Design - Examples

## Example 1: Sequential Workflow - Code Review Process

### Problem Statement

Design a workflow for automated code review of a pull request.

### Requirements

- Review code quality
- Check for security issues
- Verify tests exist and pass
- Generate review report
- Must complete within 10 minutes

### Task Decomposition (Input)

```
TASK-001: Analyze code changes
TASK-002: Run static analysis
TASK-003: Check security vulnerabilities
TASK-004: Verify test coverage
TASK-005: Run tests
TASK-006: Generate review report
```

### Workflow Design

#### Pattern Selection

**Pattern**: Sequential with some parallelization
**Rationale**: Most tasks depend on code analysis, but some checks can run in parallel

#### Workflow Diagram

```
[Orchestrator]
      |
      v
[TASK-001: Analyze Code]
      |
      +------------------+------------------+
      |                  |                  |
      v                  v                  v
[TASK-002: Static] [TASK-003: Security] [TASK-004: Coverage]
      |                  |                  |
      +------------------+------------------+
                         |
                         v
                  [TASK-005: Run Tests]
                         |
                         v
                  [TASK-006: Report]
                         |
                         v
                     [Complete]
```

#### Agent Assignments

```
TASK-001: CodeAnalyzer agent
TASK-002: StaticAnalysis agent
TASK-003: SecurityScanner agent
TASK-004: CoverageChecker agent
TASK-005: TestRunner agent
TASK-006: ReportGenerator agent
```

#### Execution Plan

**Phase 1: Code Analysis (2 minutes)**
- TASK-001 analyzes code changes
- Output: List of changed files, functions, and dependencies

**Phase 2: Parallel Checks (4 minutes)**
- TASK-002, 003, 004 run in parallel
- Each receives code analysis output
- Each produces findings

**Phase 3: Testing (3 minutes)**
- TASK-005 runs after parallel checks
- Uses test coverage info from TASK-004
- Runs only affected tests

**Phase 4: Report Generation (1 minute)**
- TASK-006 aggregates all findings
- Generates comprehensive review report

#### Coordination

**Mechanism**: Orchestrator pattern

**Handoffs**:
```
TASK-001 → TASK-002, 003, 004:
  Artifact: code_analysis.json
  Format: {
    "changed_files": ["file1.js", "file2.js"],
    "functions": [{"name": "foo", "file": "file1.js", "lines": [10, 30]}],
    "dependencies": ["lodash", "express"]
  }
  Validation: JSON valid, files exist, functions parsed

TASK-002, 003, 004 → TASK-005:
  Artifact: findings.json (from each)
  Format: {
    "task": "TASK-002",
    "findings": [{"severity": "high", "message": "...", "file": "...", "line": 10}]
  }
  Validation: JSON valid, findings array present

TASK-005 → TASK-006:
  Artifact: test_results.json
  Format: {
    "total": 100,
    "passed": 98,
    "failed": 2,
    "failures": [{"test": "...", "error": "..."}]
  }
  Validation: JSON valid, counts match
```

#### Error Handling

```
TASK-001 failure:
  - Retry: 2x
  - If still fails: Cannot proceed, escalate

TASK-002, 003, 004 failure:
  - Retry: 1x
  - If one fails: Continue with others, note missing check in report
  - If all fail: Escalate

TASK-005 failure:
  - Retry: 1x (tests might be flaky)
  - If fails: Report test failure, continue to TASK-006

TASK-006 failure:
  - Retry: 2x
  - If fails: Escalate (report generation should not fail)
```

#### Monitoring

```
Metrics:
  - workflow_duration (target: < 10 min)
  - task_duration (per task)
  - findings_count (per severity)
  - test_pass_rate
  - error_count

Logs:
  - Task start/complete
  - Findings discovered
  - Tests failed
  - Errors and retries

Alerts:
  - Workflow timeout (> 10 min)
  - High severity findings (> 5)
  - Test failures (> 10%)
  - Task failures after retries
```

#### Success Criteria

- All tasks completed
- Report generated
- Completed within 10 minutes
- All findings documented

---

## Example 2: Parallel Workflow - Multi-Module Testing

### Problem Statement

Design a workflow to test a multi-module application in parallel.

### Requirements

- Test 5 independent modules simultaneously
- Each module has unit tests, integration tests, and E2E tests
- Aggregate test results
- Generate coverage report
- Must complete within 30 minutes

### Task Decomposition (Input)

```
TASK-001: Setup test environment

Module A:
TASK-002: Test module A (unit, integration, E2E)

Module B:
TASK-003: Test module B

Module C:
TASK-004: Test module C

Module D:
TASK-005: Test module D

Module E:
TASK-006: Test module E

TASK-007: Aggregate results
TASK-008: Generate coverage report
```

### Workflow Design

#### Pattern Selection

**Pattern**: Fan-out/Fan-in
**Rationale**: One setup task, then parallel testing, then merge results

#### Workflow Diagram

```
[Orchestrator]
      |
      v
[TASK-001: Setup]
      |
      +--------+--------+--------+--------+
      |        |        |        |        |
      v        v        v        v        v
  [TASK-002] [TASK-003] [TASK-004] [TASK-005] [TASK-006]
  [Module A] [Module B] [Module C] [Module D] [Module E]
      |        |        |        |        |
      +--------+--------+--------+--------+
                       |
                       v
              [TASK-007: Aggregate]
                       |
                       v
              [TASK-008: Coverage]
                       |
                       v
                   [Complete]
```

#### Agent Assignments

```
TASK-001: EnvironmentSetup agent
TASK-002: TestRunner agent (instance 1)
TASK-003: TestRunner agent (instance 2)
TASK-004: TestRunner agent (instance 3)
TASK-005: TestRunner agent (instance 4)
TASK-006: TestRunner agent (instance 5)
TASK-007: ResultAggregator agent
TASK-008: CoverageReporter agent

Note: 5 parallel TestRunner agents
```

#### Execution Plan

**Phase 1: Setup (5 minutes)**
- TASK-001 sets up test environment
- Installs dependencies
- Prepares test databases
- Output: Environment ready signal

**Phase 2: Parallel Testing (20 minutes)**
- TASK-002 through TASK-006 run simultaneously
- Each tests one module independently
- Each produces test results and coverage data

**Phase 3: Aggregation (3 minutes)**
- TASK-007 waits for all 5 test tasks
- Aggregates results from all modules
- Identifies overall pass/fail status

**Phase 4: Coverage Report (2 minutes)**
- TASK-008 generates coverage report
- Combines coverage from all modules
- Produces HTML and JSON reports

**Total Time**: ~30 minutes (vs. 105 minutes if sequential)

#### Coordination

**Mechanism**: Orchestrator with fan-out/fan-in

**Synchronization**:
```
After TASK-001:
  - Broadcast "environment ready" to TASK-002 through TASK-006
  - All 5 tasks start simultaneously

Before TASK-007:
  - Wait for all 5 test tasks to complete
  - Barrier synchronization
  - If any task fails, still proceed with available results
```

**Handoffs**:
```
TASK-001 → TASK-002..006:
  Artifact: environment_config.json
  Format: {
    "db_url": "...",
    "test_env": "staging",
    "ready": true
  }
  Validation: ready flag is true

TASK-002..006 → TASK-007:
  Artifact: test_results_{module}.json (one per module)
  Format: {
    "module": "A",
    "total_tests": 150,
    "passed": 148,
    "failed": 2,
    "duration": 1200,
    "failures": [{"test": "...", "error": "..."}],
    "coverage": {"lines": 85.5, "branches": 78.2}
  }
  Validation: JSON valid, counts consistent

TASK-007 → TASK-008:
  Artifact: aggregated_results.json
  Format: {
    "modules": ["A", "B", "C", "D", "E"],
    "total_tests": 750,
    "passed": 740,
    "failed": 10,
    "overall_status": "passed",
    "module_results": [...]
  }
  Validation: All modules present, counts sum correctly
```

#### Error Handling

```
TASK-001 failure:
  - Retry: 2x
  - If fails: Cannot proceed, escalate immediately

TASK-002..006 (individual module) failure:
  - Retry: 1x
  - If fails: Mark module as failed, continue with other modules
  - TASK-007 proceeds with partial results
  - Report notes which modules failed

TASK-007 failure:
  - Retry: 2x
  - If fails: Escalate (aggregation should not fail)

TASK-008 failure:
  - Retry: 1x
  - If fails: Provide aggregated results without coverage report
```

#### Monitoring

```
Metrics:
  - workflow_duration (target: < 30 min)
  - parallel_task_duration (per module)
  - total_tests_run
  - test_pass_rate (overall and per module)
  - coverage_percentage (overall and per module)
  - agent_utilization (5 parallel agents)

Logs:
  - Setup complete
  - Each module test start/complete
  - Test failures (per module)
  - Aggregation complete
  - Coverage report generated

Alerts:
  - Workflow timeout (> 30 min)
  - Any module timeout (> 20 min)
  - Test pass rate < 95%
  - Coverage < 80%
  - More than 2 modules failed
```

#### Success Criteria

- All modules tested (or failed with retry)
- Results aggregated
- Coverage report generated
- Completed within 30 minutes
- Overall test pass rate > 95%

---

## Example 3: Pipeline Workflow - Continuous Refactoring

### Problem Statement

Design a workflow for refactoring a large codebase in stages with continuous validation.

### Requirements

- Refactor 20 files
- After each file: review, test, commit
- Pipeline pattern for continuous flow
- Must maintain working codebase at all times
- Complete within 2 days

### Task Decomposition (Input)

```
For each file (20 files):
  TASK-{N}-1: Refactor file N
  TASK-{N}-2: Review refactored code
  TASK-{N}-3: Run tests
  TASK-{N}-4: Commit changes
```

### Workflow Design

#### Pattern Selection

**Pattern**: Pipeline
**Rationale**: Continuous flow through stages, overlap execution for efficiency

#### Workflow Diagram

```
File 1:  [Refactor] → [Review] → [Test] → [Commit]
File 2:             [Refactor] → [Review] → [Test] → [Commit]
File 3:                         [Refactor] → [Review] → [Test] → [Commit]
...

Stages:
  Stage 1: Refactor (Refactor agent)
  Stage 2: Review (Review agent)
  Stage 3: Test (Test agent)
  Stage 4: Commit (Commit agent)

Pipeline flow: File moves through stages, next file starts when stage 1 is free
```

#### Agent Assignments

```
Stage 1: Refactor agent (continuous)
Stage 2: Review agent (continuous)
Stage 3: Test agent (continuous)
Stage 4: Commit agent (continuous)

4 agents working in parallel, each on different files at different stages
```

#### Execution Plan

**Pipeline Execution**:

```
Time 0:00 - File 1 enters Stage 1 (Refactor)
Time 0:30 - File 1 → Stage 2 (Review), File 2 → Stage 1 (Refactor)
Time 1:00 - File 1 → Stage 3 (Test), File 2 → Stage 2, File 3 → Stage 1
Time 1:30 - File 1 → Stage 4 (Commit), File 2 → Stage 3, File 3 → Stage 2, File 4 → Stage 1
Time 2:00 - File 1 complete, File 2 → Stage 4, File 3 → Stage 3, File 4 → Stage 2, File 5 → Stage 1
...

Steady state: 4 files in pipeline simultaneously, one per stage
Throughput: 1 file every 30 minutes
Total time: ~11 hours (vs. 40 hours if sequential)
```

#### Coordination

**Mechanism**: Pipeline orchestrator

**Pipeline Rules**:
```
1. File enters Stage 1 when Stage 1 is free
2. File moves to next stage when:
   - Current stage completes successfully
   - Next stage is free
3. If stage fails:
   - Retry in same stage
   - If retry fails, remove file from pipeline
   - Pipeline continues with other files
4. Stages run independently, coordinated by orchestrator
```

**Handoffs**:
```
Stage 1 → Stage 2:
  Artifact: refactored_{file}.js
  Format: JavaScript file
  Validation: File compiles, no syntax errors

Stage 2 → Stage 3:
  Artifact: review_approved_{file}.json
  Format: {"file": "...", "approved": true, "comments": [...]}
  Validation: approved is true

Stage 3 → Stage 4:
  Artifact: test_results_{file}.json
  Format: {"file": "...", "passed": true, "tests_run": 50}
  Validation: passed is true

Stage 4 output:
  Artifact: Git commit hash
  Format: {"file": "...", "commit": "abc123"}
  Validation: Commit exists in repository
```

#### Error Handling

```
Stage 1 (Refactor) failure:
  - Retry: 2x
  - If fails: Skip file, log error, continue with next file
  - Alert: Notify engineer of failed file

Stage 2 (Review) failure:
  - If review rejects: Send back to Stage 1 with feedback (max 1 retry)
  - If review agent fails: Retry 1x
  - If still fails: Skip file

Stage 3 (Test) failure:
  - If tests fail: Send back to Stage 1 (max 1 retry)
  - If test agent fails: Retry 1x
  - If still fails: Rollback refactoring, skip file

Stage 4 (Commit) failure:
  - Retry: 3x (commit should not fail)
  - If fails: Escalate immediately (code is good but can't commit)
```

#### Monitoring

```
Metrics:
  - pipeline_throughput (files/hour)
  - stage_duration (per stage)
  - files_in_pipeline (current count)
  - files_completed
  - files_failed
  - stage_utilization (per stage)

Logs:
  - File enters pipeline
  - File moves between stages
  - File completes
  - File fails/skipped
  - Stage errors

Alerts:
  - Pipeline stalled (no progress for 1 hour)
  - Stage consistently slow (> 2x expected time)
  - High failure rate (> 20%)
  - Estimated completion > 2 days

Dashboard:
  - Pipeline visualization (which files in which stages)
  - Throughput graph
  - Failure rate
  - Estimated completion time
```

#### Success Criteria

- At least 18/20 files successfully refactored (90% success rate)
- All commits maintain passing tests
- Completed within 2 days
- No breaking changes introduced

---

## Example 4: Conditional Workflow - Feature Implementation with Branching

### Problem Statement

Design a workflow for implementing a feature that has different paths based on technology choices.

### Requirements

- Implement authentication feature
- Technology choice: OAuth vs. JWT (decided during workflow)
- Different implementation paths based on choice
- Both paths converge at testing
- Must complete within 1 week

### Task Decomposition (Input)

```
TASK-001: Analyze requirements
TASK-002: Evaluate technology options
TASK-003: Make technology decision

If OAuth chosen:
  TASK-004a: Implement OAuth flow
  TASK-005a: Integrate OAuth provider
  TASK-006a: Implement token validation

If JWT chosen:
  TASK-004b: Implement JWT generation
  TASK-005b: Implement JWT validation
  TASK-006b: Implement refresh tokens

Both paths:
  TASK-007: Write tests
  TASK-008: Security review
  TASK-009: Documentation
```

### Workflow Design

#### Pattern Selection

**Pattern**: Conditional (branching)
**Rationale**: Different execution paths based on decision

#### Workflow Diagram

```
[TASK-001: Analyze]
        |
        v
[TASK-002: Evaluate]
        |
        v
[TASK-003: Decide]
        |
        +------------------+
        |                  |
   if OAuth          if JWT
        |                  |
        v                  v
[TASK-004a: OAuth]  [TASK-004b: JWT Gen]
        |                  |
        v                  v
[TASK-005a: Provider] [TASK-005b: JWT Validate]
        |                  |
        v                  v
[TASK-006a: Validate] [TASK-006b: Refresh]
        |                  |
        +------------------+
                |
                v
        [TASK-007: Tests]
                |
                v
        [TASK-008: Security]
                |
                v
        [TASK-009: Docs]
                |
                v
            [Complete]
```

#### Agent Assignments

```
TASK-001: RequirementsAnalyzer agent
TASK-002: TechEvaluator agent
TASK-003: DecisionMaker agent (or human)
TASK-004a, 005a, 006a: OAuthImplementer agent
TASK-004b, 005b, 006b: JWTImplementer agent
TASK-007: TestWriter agent
TASK-008: SecurityReviewer agent
TASK-009: DocWriter agent
```

#### Execution Plan

**Phase 1: Analysis and Decision (1 day)**
- TASK-001: Analyze requirements (4 hours)
- TASK-002: Evaluate OAuth vs. JWT (4 hours)
- TASK-003: Make decision (human or agent) (1 hour)
- Output: Technology choice (OAuth or JWT)

**Phase 2: Implementation (3 days)**
- If OAuth: TASK-004a, 005a, 006a (3 days)
- If JWT: TASK-004b, 005b, 006b (3 days)
- Output: Working authentication implementation

**Phase 3: Validation and Documentation (2 days)**
- TASK-007: Write tests (1 day)
- TASK-008: Security review (0.5 day)
- TASK-009: Documentation (0.5 day)

**Total**: 6 days

#### Coordination

**Mechanism**: Conditional orchestrator

**Decision Logic**:
```
After TASK-003:
  decision = output of TASK-003
  
  if decision == "OAuth":
    execute TASK-004a, 005a, 006a in sequence
    skip TASK-004b, 005b, 006b
  elif decision == "JWT":
    execute TASK-004b, 005b, 006b in sequence
    skip TASK-004a, 005a, 006a
  else:
    error: invalid decision
  
  After implementation path completes:
    execute TASK-007, 008, 009 in sequence
```

**Handoffs**:
```
TASK-003 → TASK-004a or TASK-004b:
  Artifact: decision.json
  Format: {
    "technology": "OAuth" | "JWT",
    "rationale": "...",
    "requirements": {...}
  }
  Validation: technology field is "OAuth" or "JWT"

TASK-006a or TASK-006b → TASK-007:
  Artifact: implementation_complete.json
  Format: {
    "technology": "OAuth" | "JWT",
    "files": ["auth.js", "middleware.js", ...],
    "endpoints": ["/login", "/logout", ...]
  }
  Validation: All required files present, endpoints implemented
```

#### Error Handling

```
TASK-003 (Decision) failure:
  - If agent cannot decide: Escalate to human
  - If human unavailable: Use default (JWT)

Implementation path (TASK-004/005/006) failure:
  - Retry: 2x
  - If fails: Try alternative path (OAuth → JWT or JWT → OAuth)
  - If both fail: Escalate

TASK-008 (Security) failure:
  - If security issues found: Send back to implementation
  - Max 2 iterations
  - If still has issues: Escalate
```

#### Monitoring

```
Metrics:
  - workflow_phase (analysis|implementation|validation)
  - technology_chosen (OAuth|JWT|pending)
  - implementation_progress (% complete)
  - security_issues_found
  - test_coverage

Logs:
  - Decision made
  - Implementation path chosen
  - Implementation progress
  - Security issues
  - Tests passing

Alerts:
  - Decision delayed (> 1 day)
  - Implementation delayed (> 3 days)
  - Security issues found
  - Tests failing
```

#### Success Criteria

- Technology decision made
- Authentication implemented (OAuth or JWT)
- All tests passing
- Security review passed
- Documentation complete
- Completed within 1 week