# Agent Guardrails - Quick Reference

## Overview

**Purpose:** Implement safety and quality guardrails to prevent harmful actions and ensure reliable agent operation.

**Complexity:** Advanced  
**Estimated Time:** 2-3 hours  
**Category:** Agentic Engineering

---

## When to Use

- Implementing AI agents that can perform high-risk operations (data deletion, access control changes, resource creation)
- Ensuring agents comply with security policies and regulatory requirements
- Preventing resource exhaustion and cost overruns
- Enforcing quality standards for agent outputs
- Implementing audit logging for compliance

---

## Quick Start

### 1. Identify Risks

```yaml
risks:
  - id: R1
    description: "Agent may delete production data"
    severity: critical
    constraint: "Block DELETE operations in production"
  
  - id: R2
    description: "Agent may exceed API rate limits"
    severity: high
    constraint: "Limit to 100 API calls per minute"
```

### 2. Implement Three-Layer Validation

**Pre-Execution:**
- Input validation
- Permission checks
- Precondition validation
- Resource availability

**Runtime Monitoring:**
- Action logging
- Resource usage tracking
- Anomaly detection
- Rate limiting

**Post-Execution:**
- Output validation
- Side effect verification
- State consistency checks
- Quality assessment

### 3. Define Intervention Strategies

- **Block:** Prevent action entirely
- **Throttle:** Rate-limit actions
- **Degrade:** Reduce functionality
- **Escalate:** Require human approval
- **Rollback:** Undo changes

---

## Guardrail Templates

### Data Protection Guardrail

```python
class DataProtectionGuardrail:
    def validate(self, operation, context):
        errors = []
        
        # Block DELETE in production
        if context.environment == "production" and operation.type == "DELETE":
            errors.append("DELETE not allowed in production")
        
        # Verify permissions
        if not self._has_permission(context.user_id, operation.type):
            errors.append("User not authorized")
        
        # Detect SQL injection
        if self._contains_sql_injection(operation.query):
            errors.append("SQL injection detected")
        
        return {"passed": len(errors) == 0, "errors": errors}
```

### Access Control Guardrail

```python
class AccessControlGuardrail:
    def validate(self, operation, context):
        errors = []
        
        # Prevent privilege escalation
        if operation.target == context.agent_id:
            errors.append("Agent cannot modify its own permissions")
        
        # Check permission boundary
        if not self._within_boundary(operation, context):
            errors.append("Operation exceeds permission boundary")
        
        return {"passed": len(errors) == 0, "errors": errors}
```

### Resource Limit Guardrail

```python
class ResourceLimitGuardrail:
    def validate(self, agent_id, usage):
        violations = []
        
        limits = self._get_limits(agent_id)
        
        if usage["cpu_percent"] > limits["max_cpu_percent"]:
            violations.append("CPU limit exceeded")
        
        if usage["memory_mb"] > limits["max_memory_mb"]:
            violations.append("Memory limit exceeded")
        
        if usage["execution_time"] > limits["max_execution_time"]:
            violations.append("Timeout")
        
        return violations
```

### Compliance Guardrail (GDPR)

```python
class GDPRGuardrail:
    def validate(self, user_id, processing_purpose):
        errors = []
        
        # Verify consent
        consent = self._get_user_consent(user_id)
        if not consent or processing_purpose not in consent.purposes:
            errors.append("No user consent for data processing")
        
        # Check retention
        if self._exceeds_retention(user_id):
            errors.append("Data exceeds retention period")
        
        return {"passed": len(errors) == 0, "errors": errors}
```

---

## Common Guardrail Patterns

### Pattern 1: Safety Guardrails

**Purpose:** Prevent harmful actions

**Examples:**
- Block DELETE operations in production
- Prevent unauthorized data access
- Restrict resource creation to quotas
- Enforce encryption for sensitive data

**Implementation:**
```python
# Pre-execution validation
if operation.is_destructive() and context.environment == "production":
    if not context.approved:
        return {"allowed": False, "reason": "Destructive operation requires approval"}
```

### Pattern 2: Quality Guardrails

**Purpose:** Ensure output quality

**Examples:**
- Validate output schema
- Check completeness and correctness
- Enforce consistency rules
- Detect sensitive data in outputs

**Implementation:**
```python
# Post-execution validation
if not self._validate_schema(output):
    return {"passed": False, "errors": ["Invalid output schema"]}

if self._contains_pii(output):
    return {"passed": False, "errors": ["Output contains PII"]}
```

### Pattern 3: Compliance Guardrails

**Purpose:** Enforce regulatory requirements

**Examples:**
- GDPR consent verification
- HIPAA PHI encryption
- SOC 2 access controls
- Audit logging

**Implementation:**
```python
# GDPR compliance
if not self._has_user_consent(user_id, purpose):
    return {"allowed": False, "reason": "No user consent"}

# HIPAA compliance
if data_classification == "phi" and not self._is_encrypted(data):
    return {"allowed": False, "reason": "PHI must be encrypted"}
```

---

## Decision Trees

### Intervention Strategy Selection

```
Violation Detected
    |
    ├─ Severity: Critical?
    │   ├─ Yes → BLOCK or ROLLBACK
    │   └─ No → Continue
    |
    ├─ Severity: High?
    │   ├─ Yes → ESCALATE (require approval)
    │   └─ No → Continue
    |
    ├─ Severity: Medium?
    │   ├─ Yes → THROTTLE (rate limit)
    │   └─ No → Continue
    |
    └─ Severity: Low?
        ├─ Yes → DEGRADE (reduce functionality)
        └─ No → ALLOW
```

### Guardrail Type Selection

```
Risk Identified
    |
    ├─ Risk Type: Data Loss/Corruption?
    │   └─ Implement: Data Protection Guardrails
    |
    ├─ Risk Type: Unauthorized Access?
    │   └─ Implement: Access Control Guardrails
    |
    ├─ Risk Type: Resource Exhaustion?
    │   └─ Implement: Resource Limit Guardrails
    |
    └─ Risk Type: Compliance Violation?
        └─ Implement: Compliance Guardrails
```

---

## Metrics and Thresholds

### Success Metrics

- **Guardrail Pass Rate:** >95%
- **False Positive Rate:** <5%
- **False Negative Rate:** <1%
- **Performance Overhead:** <10%
- **Incident Prevention:** 100% of critical risks prevented

### Performance Thresholds

```yaml
resource_limits:
  cpu:
    max_percent: 80
    warning_percent: 60
  
  memory:
    max_mb: 2048
    warning_mb: 1536
  
  execution_time:
    max_seconds: 300
    warning_seconds: 240
  
  api_calls:
    max_per_minute: 100
    warning_per_minute: 80
```

---

## Troubleshooting

### Issue: High False Positive Rate

**Symptoms:** Legitimate actions frequently blocked

**Solutions:**
1. Review and tune guardrail thresholds
2. Analyze blocked actions to identify patterns
3. Implement more granular guardrails
4. Add override mechanisms for authorized users
5. Collect user feedback and adjust

### Issue: Performance Overhead

**Symptoms:** Guardrails add significant latency

**Solutions:**
1. Optimize slow guardrail checks (caching, indexing)
2. Use asynchronous validation where possible
3. Implement sampling for low-risk checks
4. Parallelize independent checks
5. Profile and optimize hot paths

### Issue: Incomplete Coverage

**Symptoms:** Violations not caught by guardrails

**Solutions:**
1. Conduct thorough risk assessment
2. Review incident reports for missed violations
3. Add guardrails for identified gaps
4. Benchmark against industry standards
5. Regular security audits

### Issue: Poor Audit Trail

**Symptoms:** Insufficient logging for debugging/compliance

**Solutions:**
1. Log all guardrail checks with full context
2. Include timestamps, agent IDs, and reasons
3. Ensure logs are immutable and tamper-proof
4. Implement log retention policy
5. Enable log analysis and alerting

---

## Checklist

### Implementation Checklist

- [ ] All risks identified and prioritized
- [ ] Guardrails designed for each risk
- [ ] Pre-execution validation implemented
- [ ] Runtime monitoring implemented
- [ ] Post-execution validation implemented
- [ ] Intervention mechanisms implemented
- [ ] Audit logging implemented
- [ ] Guardrails tested (unit, integration, negative)
- [ ] Documentation complete
- [ ] Monitoring dashboard deployed

### Quality Checklist

- [ ] Guardrail pass rate >95%
- [ ] False positive rate <5%
- [ ] False negative rate <1%
- [ ] Performance overhead <10%
- [ ] All critical risks prevented
- [ ] Audit trail complete and compliant
- [ ] Stakeholder approval obtained

---

## Related Skills

**Requires:**
- agent-task-decomposition
- agent-workflow-design
- agent-context-engineering
- agent-instruction-design
- agent-tool-selection
- agent-handoff-design

**Commonly Followed By:**
- agent-evaluation
- agent-observability
- agentic-workflow-review

**Works With:**
- security-architecture-review
- production-readiness

---

## Additional Resources

- **Full Documentation:** [SKILL.md](SKILL.md)
- **Step-by-Step Instructions:** [instructions.md](instructions.md)
- **Practical Examples:** [examples.md](examples.md)
- **OWASP Top 10 for LLM Applications:** https://owasp.org/www-project-top-10-for-large-language-model-applications/
- **NIST AI Risk Management Framework:** https://www.nist.gov/itl/ai-risk-management-framework

---

**Last Updated:** 2026-09-08  
**Version:** 1.0.0
