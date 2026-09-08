# Agent Handoff Design - Examples

This document provides detailed examples of agent handoff design in various multi-agent workflows.

---

## Example 1: Code Review Workflow

### Scenario

A multi-agent code review workflow with three agents:
1. **Code Analyzer**: Analyzes code for issues
2. **Code Reviewer**: Reviews code and suggests improvements
3. **Code Fixer**: Applies fixes based on review

### Workflow Design

```
[Code Analyzer] → [Code Reviewer] → [Code Fixer]
```

**Handoff Points**:
- Handoff 1: Code Analyzer → Code Reviewer (synchronous)
- Handoff 2: Code Reviewer → Code Fixer (synchronous)

---

### Handoff 1: Code Analyzer → Code Reviewer

#### Context Dependencies

**Code Reviewer Needs**:
- Analyzed files (from Code Analyzer)
- Issues found (from Code Analyzer)
- Code metrics (from Code Analyzer)
- Project context (from initial request)
- Coding standards (from project config)

#### Context Transfer Schema

```json
{
  "handoff_id": "analyzer_to_reviewer_001",
  "timestamp": "2026-09-08T10:30:00Z",
  "source_agent": {
    "name": "code_analyzer",
    "version": "1.2.0",
    "execution_time_ms": 1250
  },
  "task_results": {
    "analyzed_files": [
      {
        "path": "src/auth/login.js",
        "lines_of_code": 150,
        "complexity": 12,
        "issues": [
          {
            "type": "security",
            "severity": "high",
            "line": 45,
            "message": "Potential SQL injection",
            "code_snippet": "db.query('SELECT * FROM users WHERE id = ' + userId)"
          },
          {
            "type": "performance",
            "severity": "medium",
            "line": 78,
            "message": "Inefficient loop",
            "code_snippet": "for (let i = 0; i < users.length; i++) {...}"
          }
        ]
      },
      {
        "path": "src/auth/logout.js",
        "lines_of_code": 80,
        "complexity": 6,
        "issues": []
      }
    ],
    "summary": {
      "total_files": 2,
      "total_issues": 2,
      "high_severity": 1,
      "medium_severity": 1,
      "low_severity": 0
    },
    "metrics": {
      "average_complexity": 9,
      "total_lines": 230,
      "test_coverage": 65
    }
  },
  "metadata": {
    "workflow_id": "code_review_workflow_123",
    "project_name": "auth-service",
    "branch": "feature/login-improvements",
    "commit_sha": "abc123def456"
  },
  "context": {
    "project_type": "nodejs",
    "coding_standards": {
      "style_guide": "airbnb",
      "max_complexity": 10,
      "min_test_coverage": 80
    },
    "user_preferences": {
      "auto_fix": true,
      "review_depth": "thorough"
    }
  },
  "state": {
    "completed_tasks": ["code_analysis"],
    "remaining_tasks": ["code_review", "code_fix", "test_verification"],
    "errors": []
  }
}
```

#### Handoff Protocol

**Mechanism**: Synchronous (Code Reviewer waits for Code Analyzer)

**Trigger Conditions**:
- Code Analyzer completes successfully
- At least one file analyzed
- Context schema valid

**Validation**:
```python
def validate_analyzer_to_reviewer_handoff(context):
    # Required fields present
    assert "task_results" in context
    assert "analyzed_files" in context["task_results"]
    assert len(context["task_results"]["analyzed_files"]) > 0
    
    # Data types correct
    assert isinstance(context["task_results"]["analyzed_files"], list)
    assert isinstance(context["metadata"]["workflow_id"], str)
    
    # Values valid
    for file in context["task_results"]["analyzed_files"]:
        assert "path" in file
        assert "issues" in file
        assert file["complexity"] >= 0
    
    return True
```

**Timeout**: 30 seconds  
**Retries**: 3 attempts with exponential backoff (1s, 2s, 4s)

**Error Handling**:
```python
try:
    context = code_analyzer.execute(files)
    validate_analyzer_to_reviewer_handoff(context)
    result = code_reviewer.execute(context)
except ValidationError as e:
    logger.error(f"Handoff validation failed: {e}")
    notify_team("Invalid handoff context")
    raise
except TimeoutError:
    logger.error("Code Analyzer timed out")
    # Retry with smaller file set
    context = code_analyzer.execute(files[:10])
    result = code_reviewer.execute(context)
except Exception as e:
    logger.error(f"Handoff failed: {e}")
    # Escalate to human
    notify_human("Manual review required")
    raise
```

---

### Handoff 2: Code Reviewer → Code Fixer

#### Context Dependencies

**Code Fixer Needs**:
- Review results (from Code Reviewer)
- Suggested fixes (from Code Reviewer)
- Original files (from Code Analyzer)
- Project context (from initial request)

#### Context Transfer Schema

```json
{
  "handoff_id": "reviewer_to_fixer_001",
  "timestamp": "2026-09-08T10:32:00Z",
  "source_agent": {
    "name": "code_reviewer",
    "version": "1.1.0",
    "execution_time_ms": 2100
  },
  "task_results": {
    "reviewed_files": [
      {
        "path": "src/auth/login.js",
        "review_status": "needs_fixes",
        "suggested_fixes": [
          {
            "issue_id": "security_001",
            "line": 45,
            "original_code": "db.query('SELECT * FROM users WHERE id = ' + userId)",
            "fixed_code": "db.query('SELECT * FROM users WHERE id = ?', [userId])",
            "rationale": "Use parameterized queries to prevent SQL injection",
            "priority": "high"
          },
          {
            "issue_id": "performance_001",
            "line": 78,
            "original_code": "for (let i = 0; i < users.length; i++) {...}",
            "fixed_code": "users.forEach(user => {...})",
            "rationale": "Use forEach for better readability and performance",
            "priority": "medium"
          }
        ]
      },
      {
        "path": "src/auth/logout.js",
        "review_status": "approved",
        "suggested_fixes": []
      }
    ],
    "summary": {
      "files_needing_fixes": 1,
      "total_fixes": 2,
      "high_priority_fixes": 1,
      "medium_priority_fixes": 1
    }
  },
  "metadata": {
    "workflow_id": "code_review_workflow_123",
    "project_name": "auth-service",
    "branch": "feature/login-improvements"
  },
  "context": {
    "previous_results": {
      "analyzer_output": "<reference to analyzer output>"
    },
    "user_preferences": {
      "auto_fix": true,
      "require_approval": false
    }
  },
  "state": {
    "completed_tasks": ["code_analysis", "code_review"],
    "remaining_tasks": ["code_fix", "test_verification"],
    "errors": []
  }
}
```

#### Handoff Protocol

**Mechanism**: Synchronous

**Validation**:
```python
def validate_reviewer_to_fixer_handoff(context):
    assert "reviewed_files" in context["task_results"]
    assert len(context["task_results"]["reviewed_files"]) > 0
    
    for file in context["task_results"]["reviewed_files"]:
        assert "review_status" in file
        assert "suggested_fixes" in file
        
        for fix in file["suggested_fixes"]:
            assert "line" in fix
            assert "original_code" in fix
            assert "fixed_code" in fix
    
    return True
```

**Performance Optimization**:
- Transfer only files needing fixes (not approved files)
- Use references for large original files
- Compress fix suggestions if > 100KB

---

### Results

**Handoff Metrics**:
- Handoff 1 success rate: 98.5%
- Handoff 2 success rate: 99.2%
- Average handoff latency: 150ms
- Context transfer size: 50KB average

**Key Learnings**:
1. Synchronous handoffs work well for tightly coupled agents
2. Validation catches 95% of context errors
3. Context compression reduces transfer time by 60%
4. Retry logic handles 80% of transient failures

---

## Example 2: CI/CD Pipeline

### Scenario

A CI/CD pipeline with four agents:
1. **Build Agent**: Compiles code
2. **Test Agent**: Runs tests
3. **Security Agent**: Runs security scans
4. **Deploy Agent**: Deploys to production

### Workflow Design

```
[Build Agent] → [Test Agent] → [Security Agent] → [Deploy Agent]
                      ↓
               [Parallel Test Agents]
                 - Unit Tests
                 - Integration Tests
                 - E2E Tests
```

**Handoff Points**:
- Handoff 1: Build Agent → Test Agent (asynchronous)
- Handoff 2: Test Agent → Security Agent (event-driven)
- Handoff 3: Security Agent → Deploy Agent (synchronous with approval)

---

### Handoff 1: Build Agent → Test Agent

#### Context Dependencies

**Test Agent Needs**:
- Build artifacts (from Build Agent)
- Build metadata (version, commit, timestamp)
- Test configuration (which tests to run)

#### Context Transfer Schema

```json
{
  "handoff_id": "build_to_test_001",
  "timestamp": "2026-09-08T11:00:00Z",
  "source_agent": {
    "name": "build_agent",
    "version": "2.0.0",
    "execution_time_ms": 45000
  },
  "task_results": {
    "build_status": "success",
    "artifacts": [
      {
        "name": "app.jar",
        "path": "s3://builds/app-v1.2.3.jar",
        "size_bytes": 15728640,
        "checksum": "sha256:abc123..."
      },
      {
        "name": "app-tests.jar",
        "path": "s3://builds/app-tests-v1.2.3.jar",
        "size_bytes": 5242880,
        "checksum": "sha256:def456..."
      }
    ],
    "build_metadata": {
      "version": "1.2.3",
      "commit_sha": "abc123def456",
      "branch": "main",
      "build_number": 1234
    }
  },
  "metadata": {
    "pipeline_id": "cicd_pipeline_789",
    "project_name": "payment-service",
    "environment": "staging"
  },
  "context": {
    "test_configuration": {
      "test_suites": ["unit", "integration", "e2e"],
      "parallel_execution": true,
      "timeout_minutes": 30
    }
  },
  "state": {
    "completed_tasks": ["build"],
    "remaining_tasks": ["test", "security_scan", "deploy"],
    "errors": []
  }
}
```

#### Handoff Protocol

**Mechanism**: Asynchronous (Build Agent completes, Test Agent starts later)

**Implementation**:
```python
# Build Agent
def build_agent_execute(source_code):
    artifacts = compile_code(source_code)
    context = create_handoff_context(artifacts)
    
    # Push to queue (non-blocking)
    test_queue.push(context)
    
    logger.info("Build complete, test queued")
    return {"status": "success", "handoff_id": context["handoff_id"]}

# Test Agent (runs separately)
def test_agent_execute():
    while True:
        context = test_queue.pop()  # Blocks until available
        
        validate_build_to_test_handoff(context)
        
        # Run tests
        test_results = run_tests(context["task_results"]["artifacts"])
        
        # Trigger next handoff
        emit_event("tests_complete", test_results)
```

**Validation**:
```python
def validate_build_to_test_handoff(context):
    assert context["task_results"]["build_status"] == "success"
    assert len(context["task_results"]["artifacts"]) > 0
    
    for artifact in context["task_results"]["artifacts"]:
        assert "path" in artifact
        assert "checksum" in artifact
        
        # Verify artifact exists
        assert artifact_exists(artifact["path"])
        
        # Verify checksum
        actual_checksum = calculate_checksum(artifact["path"])
        assert actual_checksum == artifact["checksum"]
    
    return True
```

**Error Handling**:
```python
try:
    validate_build_to_test_handoff(context)
except ArtifactNotFoundError as e:
    logger.error(f"Build artifact missing: {e}")
    # Retry build
    rebuild_and_retry()
except ChecksumMismatchError as e:
    logger.error(f"Build artifact corrupted: {e}")
    # Rebuild
    rebuild_and_retry()
except Exception as e:
    logger.error(f"Handoff failed: {e}")
    notify_team("Build-to-test handoff failed")
    raise
```

---

### Handoff 2: Test Agent → Security Agent

#### Context Dependencies

**Security Agent Needs**:
- Test results (from Test Agent)
- Build artifacts (from Build Agent)
- Security scan configuration

#### Context Transfer Schema

```json
{
  "handoff_id": "test_to_security_001",
  "timestamp": "2026-09-08T11:15:00Z",
  "source_agent": {
    "name": "test_agent",
    "version": "1.5.0",
    "execution_time_ms": 180000
  },
  "task_results": {
    "test_status": "passed",
    "test_summary": {
      "total_tests": 1250,
      "passed": 1248,
      "failed": 2,
      "skipped": 0,
      "coverage": 87.5
    },
    "failed_tests": [
      {
        "name": "test_payment_timeout",
        "suite": "integration",
        "error": "Timeout after 5s"
      },
      {
        "name": "test_refund_edge_case",
        "suite": "unit",
        "error": "AssertionError: Expected 100, got 99.99"
      }
    ]
  },
  "metadata": {
    "pipeline_id": "cicd_pipeline_789",
    "project_name": "payment-service"
  },
  "context": {
    "previous_results": {
      "build_artifacts": "s3://builds/app-v1.2.3.jar"
    },
    "security_configuration": {
      "scan_types": ["sast", "dast", "dependency_check"],
      "severity_threshold": "high"
    }
  },
  "state": {
    "completed_tasks": ["build", "test"],
    "remaining_tasks": ["security_scan", "deploy"],
    "errors": []
  }
}
```

#### Handoff Protocol

**Mechanism**: Event-driven (Test Agent emits event, Security Agent listens)

**Implementation**:
```python
# Test Agent
def test_agent_execute(context):
    test_results = run_tests(context)
    
    handoff_context = create_handoff_context(test_results)
    
    # Emit event (non-blocking)
    event_bus.emit("tests_complete", handoff_context)
    
    logger.info("Tests complete, security scan triggered")
    return test_results

# Security Agent (event listener)
@event_bus.on("tests_complete")
def on_tests_complete(event):
    context = event.data
    
    validate_test_to_security_handoff(context)
    
    # Only proceed if tests passed
    if context["task_results"]["test_status"] == "passed":
        security_results = run_security_scan(context)
        event_bus.emit("security_scan_complete", security_results)
    else:
        logger.warning("Tests failed, skipping security scan")
        notify_team("Tests failed, pipeline stopped")
```

**Validation**:
```python
def validate_test_to_security_handoff(context):
    assert "test_status" in context["task_results"]
    assert "test_summary" in context["task_results"]
    assert context["task_results"]["test_summary"]["total_tests"] > 0
    
    # Verify test coverage meets threshold
    coverage = context["task_results"]["test_summary"]["coverage"]
    assert coverage >= 80, f"Coverage {coverage}% below threshold 80%"
    
    return True
```

---

### Handoff 3: Security Agent → Deploy Agent

#### Context Dependencies

**Deploy Agent Needs**:
- Security scan results (from Security Agent)
- Build artifacts (from Build Agent)
- Deployment configuration
- Approval status (from human or automated approval)

#### Context Transfer Schema

```json
{
  "handoff_id": "security_to_deploy_001",
  "timestamp": "2026-09-08T11:30:00Z",
  "source_agent": {
    "name": "security_agent",
    "version": "1.3.0",
    "execution_time_ms": 120000
  },
  "task_results": {
    "security_status": "passed",
    "vulnerabilities": {
      "critical": 0,
      "high": 0,
      "medium": 2,
      "low": 5
    },
    "scan_details": [
      {
        "type": "sast",
        "status": "passed",
        "issues_found": 3
      },
      {
        "type": "dast",
        "status": "passed",
        "issues_found": 2
      },
      {
        "type": "dependency_check",
        "status": "passed",
        "issues_found": 2
      }
    ]
  },
  "metadata": {
    "pipeline_id": "cicd_pipeline_789",
    "project_name": "payment-service",
    "environment": "production"
  },
  "context": {
    "previous_results": {
      "build_artifacts": "s3://builds/app-v1.2.3.jar",
      "test_results": "passed"
    },
    "deployment_configuration": {
      "strategy": "blue_green",
      "rollback_enabled": true,
      "health_check_url": "/health"
    },
    "approval": {
      "required": true,
      "approved_by": "[email protected]",
      "approved_at": "2026-09-08T11:28:00Z"
    }
  },
  "state": {
    "completed_tasks": ["build", "test", "security_scan"],
    "remaining_tasks": ["deploy"],
    "errors": []
  }
}
```

#### Handoff Protocol

**Mechanism**: Synchronous with approval gate

**Implementation**:
```python
def security_to_deploy_handoff(context):
    validate_security_to_deploy_handoff(context)
    
    # Check approval
    if context["context"]["approval"]["required"]:
        if not context["context"]["approval"].get("approved_by"):
            logger.info("Waiting for deployment approval")
            approval = wait_for_approval(timeout=3600)  # 1 hour
            context["context"]["approval"] = approval
    
    # Deploy
    deploy_agent.execute(context)

def validate_security_to_deploy_handoff(context):
    assert context["task_results"]["security_status"] == "passed"
    
    # No critical or high vulnerabilities
    vulns = context["task_results"]["vulnerabilities"]
    assert vulns["critical"] == 0, "Critical vulnerabilities found"
    assert vulns["high"] == 0, "High vulnerabilities found"
    
    # Approval present if required
    if context["context"]["approval"]["required"]:
        assert "approved_by" in context["context"]["approval"]
        assert "approved_at" in context["context"]["approval"]
    
    return True
```

**Error Handling**:
```python
try:
    validate_security_to_deploy_handoff(context)
    deploy_agent.execute(context)
except SecurityVulnerabilitiesError as e:
    logger.error(f"Security vulnerabilities found: {e}")
    notify_team("Deployment blocked by security issues")
    raise
except ApprovalTimeoutError:
    logger.warning("Deployment approval timeout")
    notify_team("Deployment approval needed")
    raise
except DeploymentError as e:
    logger.error(f"Deployment failed: {e}")
    # Rollback
    rollback_deployment()
    raise
```

---

### Results

**Handoff Metrics**:
- Handoff 1 (Build → Test): 99.8% success rate, 200ms latency
- Handoff 2 (Test → Security): 99.5% success rate, 100ms latency
- Handoff 3 (Security → Deploy): 98.0% success rate, 500ms latency
- Average context transfer size: 100KB

**Key Learnings**:
1. Asynchronous handoffs reduce pipeline latency by 40%
2. Event-driven handoffs enable parallel execution
3. Approval gates prevent unauthorized deployments
4. Artifact checksums catch 100% of corruption issues

---

## Example 3: Bug Triage System

### Scenario

A multi-agent bug triage system with four agents:
1. **Detection Agent**: Detects bugs from logs/reports
2. **Classification Agent**: Classifies bug severity and type
3. **Assignment Agent**: Assigns bugs to appropriate teams
4. **Notification Agent**: Notifies teams and creates tickets

### Workflow Design

```
[Detection Agent] → [Classification Agent] → [Assignment Agent] → [Notification Agent]
```

**Handoff Points**:
- Handoff 1: Detection → Classification (asynchronous, queue-based)
- Handoff 2: Classification → Assignment (synchronous)
- Handoff 3: Assignment → Notification (asynchronous, fan-out)

---

### Handoff 1: Detection Agent → Classification Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "detection_to_classification_001",
  "timestamp": "2026-09-08T12:00:00Z",
  "source_agent": {
    "name": "detection_agent",
    "version": "1.0.0"
  },
  "task_results": {
    "detected_bugs": [
      {
        "bug_id": "BUG-12345",
        "source": "error_logs",
        "error_message": "NullPointerException at PaymentService.java:145",
        "stack_trace": "...",
        "frequency": 127,
        "first_seen": "2026-09-08T10:00:00Z",
        "last_seen": "2026-09-08T11:55:00Z",
        "affected_users": 45
      }
    ]
  },
  "metadata": {
    "workflow_id": "bug_triage_workflow_456",
    "environment": "production"
  },
  "state": {
    "completed_tasks": ["detection"],
    "remaining_tasks": ["classification", "assignment", "notification"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Asynchronous, queue-based (handles high volume)

**Implementation**:
```python
# Detection Agent (producer)
def detection_agent_execute(logs):
    bugs = detect_bugs(logs)
    
    for bug in bugs:
        context = create_handoff_context(bug)
        bug_queue.push(context)  # Non-blocking
    
    logger.info(f"Detected {len(bugs)} bugs, queued for classification")
    return {"bugs_detected": len(bugs)}

# Classification Agent (consumer)
def classification_agent_execute():
    while True:
        context = bug_queue.pop(timeout=60)  # Wait up to 60s
        
        if context:
            validate_detection_to_classification_handoff(context)
            classification = classify_bug(context)
            
            # Next handoff
            assignment_agent.execute(classification)
```

**Performance Optimization**:
- Batch processing: Process 10 bugs at once
- Priority queue: High-frequency bugs processed first
- Parallel consumers: 5 classification agents running in parallel

---

### Handoff 2: Classification Agent → Assignment Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "classification_to_assignment_001",
  "timestamp": "2026-09-08T12:05:00Z",
  "source_agent": {
    "name": "classification_agent",
    "version": "1.1.0"
  },
  "task_results": {
    "bug_id": "BUG-12345",
    "classification": {
      "severity": "critical",
      "type": "null_pointer_exception",
      "component": "payment_service",
      "impact": "high",
      "urgency": "immediate"
    },
    "analysis": {
      "root_cause": "Missing null check in payment processing",
      "affected_code": "PaymentService.java:145",
      "suggested_fix": "Add null check before accessing payment object"
    }
  },
  "metadata": {
    "workflow_id": "bug_triage_workflow_456"
  },
  "context": {
    "previous_results": {
      "detection_data": {
        "frequency": 127,
        "affected_users": 45
      }
    }
  },
  "state": {
    "completed_tasks": ["detection", "classification"],
    "remaining_tasks": ["assignment", "notification"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Synchronous (immediate assignment needed)

**Validation**:
```python
def validate_classification_to_assignment_handoff(context):
    assert "classification" in context["task_results"]
    
    classification = context["task_results"]["classification"]
    assert classification["severity"] in ["critical", "high", "medium", "low"]
    assert classification["type"] is not None
    assert classification["component"] is not None
    
    return True
```

---

### Handoff 3: Assignment Agent → Notification Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "assignment_to_notification_001",
  "timestamp": "2026-09-08T12:06:00Z",
  "source_agent": {
    "name": "assignment_agent",
    "version": "1.0.0"
  },
  "task_results": {
    "bug_id": "BUG-12345",
    "assignment": {
      "team": "payments_team",
      "assignee": "[email protected]",
      "priority": "P0",
      "sla": "4_hours"
    }
  },
  "metadata": {
    "workflow_id": "bug_triage_workflow_456"
  },
  "context": {
    "previous_results": {
      "classification": {
        "severity": "critical",
        "component": "payment_service"
      },
      "detection_data": {
        "frequency": 127,
        "affected_users": 45
      }
    },
    "notification_targets": [
      {"type": "email", "address": "[email protected]"},
      {"type": "slack", "channel": "#payments-alerts"},
      {"type": "pagerduty", "service": "payment-service"}
    ]
  },
  "state": {
    "completed_tasks": ["detection", "classification", "assignment"],
    "remaining_tasks": ["notification"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Asynchronous, fan-out (multiple notification channels)

**Implementation**:
```python
def assignment_to_notification_handoff(context):
    validate_assignment_to_notification_handoff(context)
    
    # Fan-out to multiple notification agents
    notification_tasks = []
    for target in context["context"]["notification_targets"]:
        task = notification_agent.execute_async(context, target)
        notification_tasks.append(task)
    
    # Wait for all notifications (with timeout)
    results = asyncio.gather(*notification_tasks, timeout=30)
    
    logger.info(f"Notifications sent: {len(results)}")
    return results
```

---

### Results

**Handoff Metrics**:
- Handoff 1: 99.9% success rate, 50ms latency, 1000 bugs/minute throughput
- Handoff 2: 99.5% success rate, 100ms latency
- Handoff 3: 98.5% success rate, 200ms latency (fan-out to 3 channels)
- Average end-to-end latency: 5 seconds (detection to notification)

**Key Learnings**:
1. Queue-based handoffs handle high-volume bug detection
2. Batch processing improves throughput by 10x
3. Fan-out pattern enables multi-channel notifications
4. Priority queues ensure critical bugs processed first

---

## Example 4: Documentation Generation

### Scenario

A multi-agent documentation generation system with four agents:
1. **Extraction Agent**: Extracts code structure and comments
2. **Generation Agent**: Generates documentation from extracted data
3. **Validation Agent**: Validates documentation quality
4. **Publishing Agent**: Publishes documentation to website

### Workflow Design

```
[Extraction Agent] → [Generation Agent] → [Validation Agent] → [Publishing Agent]
                              ↓
                       [Parallel Generators]
                         - API Docs
                         - User Guide
                         - Architecture Docs
```

**Handoff Points**:
- Handoff 1: Extraction → Generation (synchronous)
- Handoff 2: Generation → Validation (asynchronous, batch)
- Handoff 3: Validation → Publishing (event-driven)

---

### Handoff 1: Extraction Agent → Generation Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "extraction_to_generation_001",
  "timestamp": "2026-09-08T13:00:00Z",
  "source_agent": {
    "name": "extraction_agent",
    "version": "2.0.0",
    "execution_time_ms": 30000
  },
  "task_results": {
    "extracted_data": {
      "classes": [
        {
          "name": "PaymentService",
          "file": "src/services/PaymentService.java",
          "methods": [
            {
              "name": "processPayment",
              "signature": "public Payment processPayment(Order order, PaymentMethod method)",
              "description": "Processes a payment for the given order",
              "parameters": [
                {"name": "order", "type": "Order", "description": "The order to process payment for"},
                {"name": "method", "type": "PaymentMethod", "description": "The payment method to use"}
              ],
              "returns": {"type": "Payment", "description": "The processed payment"},
              "throws": [
                {"type": "PaymentException", "description": "If payment processing fails"}
              ]
            }
          ]
        }
      ],
      "apis": [
        {
          "endpoint": "/api/payments",
          "method": "POST",
          "description": "Create a new payment",
          "request_body": {...},
          "responses": {...}
        }
      ]
    },
    "statistics": {
      "total_classes": 45,
      "total_methods": 320,
      "total_apis": 28,
      "documentation_coverage": 75
    }
  },
  "metadata": {
    "workflow_id": "docs_generation_workflow_789",
    "project_name": "payment-service",
    "version": "1.2.3"
  },
  "context": {
    "generation_configuration": {
      "output_formats": ["markdown", "html"],
      "include_examples": true,
      "include_diagrams": true
    }
  },
  "state": {
    "completed_tasks": ["extraction"],
    "remaining_tasks": ["generation", "validation", "publishing"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Synchronous (generation depends on complete extraction)

**Validation**:
```python
def validate_extraction_to_generation_handoff(context):
    extracted = context["task_results"]["extracted_data"]
    
    assert "classes" in extracted or "apis" in extracted
    assert len(extracted.get("classes", [])) > 0 or len(extracted.get("apis", [])) > 0
    
    # Verify documentation coverage meets threshold
    coverage = context["task_results"]["statistics"]["documentation_coverage"]
    assert coverage >= 70, f"Documentation coverage {coverage}% below threshold 70%"
    
    return True
```

**Context Continuity**:
- Use references for large extracted data (store in S3, pass URL)
- Compress extracted data if > 1MB
- Include only summary statistics in handoff context

---

### Handoff 2: Generation Agent → Validation Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "generation_to_validation_001",
  "timestamp": "2026-09-08T13:15:00Z",
  "source_agent": {
    "name": "generation_agent",
    "version": "1.3.0",
    "execution_time_ms": 60000
  },
  "task_results": {
    "generated_docs": [
      {
        "type": "api_docs",
        "format": "markdown",
        "path": "s3://docs/api-docs-v1.2.3.md",
        "size_bytes": 524288,
        "sections": 28
      },
      {
        "type": "user_guide",
        "format": "markdown",
        "path": "s3://docs/user-guide-v1.2.3.md",
        "size_bytes": 1048576,
        "sections": 15
      },
      {
        "type": "architecture_docs",
        "format": "markdown",
        "path": "s3://docs/architecture-v1.2.3.md",
        "size_bytes": 262144,
        "sections": 8
      }
    ],
    "statistics": {
      "total_pages": 51,
      "total_sections": 51,
      "total_size_bytes": 1834008
    }
  },
  "metadata": {
    "workflow_id": "docs_generation_workflow_789",
    "project_name": "payment-service",
    "version": "1.2.3"
  },
  "context": {
    "validation_configuration": {
      "check_links": true,
      "check_spelling": true,
      "check_formatting": true,
      "check_completeness": true
    }
  },
  "state": {
    "completed_tasks": ["extraction", "generation"],
    "remaining_tasks": ["validation", "publishing"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Asynchronous, batch (validate multiple docs in parallel)

**Implementation**:
```python
def generation_to_validation_handoff(context):
    validate_generation_to_validation_handoff(context)
    
    # Batch validation
    docs = context["task_results"]["generated_docs"]
    validation_tasks = []
    
    for doc in docs:
        task = validation_agent.validate_async(doc)
        validation_tasks.append(task)
    
    # Wait for all validations
    results = asyncio.gather(*validation_tasks)
    
    return results
```

---

### Handoff 3: Validation Agent → Publishing Agent

#### Context Transfer Schema

```json
{
  "handoff_id": "validation_to_publishing_001",
  "timestamp": "2026-09-08T13:20:00Z",
  "source_agent": {
    "name": "validation_agent",
    "version": "1.0.0",
    "execution_time_ms": 15000
  },
  "task_results": {
    "validation_status": "passed",
    "validated_docs": [
      {
        "type": "api_docs",
        "path": "s3://docs/api-docs-v1.2.3.md",
        "validation_result": {
          "status": "passed",
          "issues": []
        }
      },
      {
        "type": "user_guide",
        "path": "s3://docs/user-guide-v1.2.3.md",
        "validation_result": {
          "status": "passed_with_warnings",
          "issues": [
            {"type": "spelling", "severity": "low", "message": "Possible typo: 'recieve'"}
          ]
        }
      },
      {
        "type": "architecture_docs",
        "path": "s3://docs/architecture-v1.2.3.md",
        "validation_result": {
          "status": "passed",
          "issues": []
        }
      }
    ]
  },
  "metadata": {
    "workflow_id": "docs_generation_workflow_789",
    "project_name": "payment-service",
    "version": "1.2.3"
  },
  "context": {
    "publishing_configuration": {
      "destination": "https://docs.example.com/payment-service/v1.2.3",
      "notify_team": true
    }
  },
  "state": {
    "completed_tasks": ["extraction", "generation", "validation"],
    "remaining_tasks": ["publishing"]
  }
}
```

#### Handoff Protocol

**Mechanism**: Event-driven (publish when validation complete)

**Implementation**:
```python
# Validation Agent
def validation_agent_execute(context):
    validation_results = validate_docs(context)
    
    handoff_context = create_handoff_context(validation_results)
    
    # Emit event
    event_bus.emit("validation_complete", handoff_context)
    
    return validation_results

# Publishing Agent (event listener)
@event_bus.on("validation_complete")
def on_validation_complete(event):
    context = event.data
    
    validate_validation_to_publishing_handoff(context)
    
    # Only publish if validation passed
    if context["task_results"]["validation_status"] == "passed":
        publish_docs(context)
        notify_team("Documentation published")
    else:
        logger.warning("Validation failed, not publishing")
        notify_team("Documentation validation failed")
```

**Validation**:
```python
def validate_validation_to_publishing_handoff(context):
    assert "validation_status" in context["task_results"]
    assert "validated_docs" in context["task_results"]
    assert len(context["task_results"]["validated_docs"]) > 0
    
    # All docs must have validation results
    for doc in context["task_results"]["validated_docs"]:
        assert "validation_result" in doc
        assert "status" in doc["validation_result"]
    
    return True
```

---

### Results

**Handoff Metrics**:
- Handoff 1: 99.0% success rate, 300ms latency
- Handoff 2: 98.5% success rate, 500ms latency (parallel validation)
- Handoff 3: 99.5% success rate, 100ms latency
- Average end-to-end time: 2 minutes (extraction to publishing)

**Key Learnings**:
1. Using references for large data reduces handoff latency by 80%
2. Parallel validation improves throughput by 3x
3. Event-driven publishing enables conditional workflows
4. Batch processing reduces overhead by 50%

---

## Summary

These examples demonstrate various handoff patterns:

1. **Synchronous Handoffs**: Code Review (tight coupling, immediate dependency)
2. **Asynchronous Handoffs**: CI/CD Pipeline (decoupled, queue-based)
3. **Event-Driven Handoffs**: Bug Triage (high volume, fan-out)
4. **Mixed Patterns**: Documentation Generation (combines all patterns)

**Key Takeaways**:
- Choose handoff mechanism based on coupling and performance requirements
- Always validate context at handoff boundaries
- Use references for large data transfers
- Implement comprehensive error handling and retry logic
- Monitor handoff metrics to identify bottlenecks
- Design for failure (timeouts, retries, rollbacks)

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08