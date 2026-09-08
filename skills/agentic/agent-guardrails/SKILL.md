# Agent Guardrails

**Purpose**: Implement safety and quality guardrails for AI agents to prevent harmful actions, ensure reliable operation, and maintain compliance with organizational policies.

---

## When to Use

Use this skill when:
- Deploying AI agents in production environments
- Agents have access to sensitive data or critical systems
- Agents can perform destructive operations (delete, modify, deploy)
- Compliance and regulatory requirements must be met
- Quality standards must be enforced
- Resource usage must be controlled
- Preventing agent errors from causing harm
- Building trust in agent-based systems

## When NOT to Use

Do not use this skill when:
- Agents operate in completely sandboxed environments with no risk
- Prototyping or early development (add guardrails before production)
- Guardrails would prevent legitimate agent functionality
- The overhead of guardrails outweighs the risk

---

## Inputs

### Required
- **Agent Capabilities**: What the agent can do
- **Risk Assessment**: Potential harmful actions and their impact
- **Compliance Requirements**: Regulatory and policy constraints
- **Quality Standards**: Expected output quality and performance

### Optional
- **Historical Incidents**: Past agent failures or issues
- **Resource Limits**: CPU, memory, API rate limits
- **User Permissions**: What different users can authorize agents to do

---

## Expected Outputs

### Primary Deliverables
1. **Guardrail Specification**: Detailed safety and quality constraints
2. **Validation Rules**: How to verify agent actions before execution
3. **Monitoring Plan**: How to detect guardrail violations
4. **Intervention Protocols**: What to do when guardrails are triggered

### Supporting Artifacts
- **Risk Matrix**: Mapping of actions to risk levels
- **Compliance Checklist**: Verification of regulatory requirements
- **Resource Budgets**: Limits on agent resource consumption
- **Audit Logs**: Record of all guardrail triggers and interventions

---

## Workflow

### Step 1: Identify Risks
**Objective**: Understand what could go wrong with agent execution.

**Actions**:
1. List all agent capabilities and actions
2. Identify potential harmful outcomes:
   - Data loss or corruption
   - Unauthorized access
   - Resource exhaustion
   - Compliance violations
   - Poor quality outputs
3. Assess impact and likelihood for each risk
4. Prioritize risks by severity
5. Document risk scenarios

**Output**: Risk assessment matrix

**Quality Check**: Are all significant risks identified?

---

### Step 2: Define Safety Constraints
**Objective**: Specify what agents must NOT do.

**Actions**:
1. For each high-risk action, define constraints:
   - **Data Protection**: No deletion without backup, no PII exposure
   - **Access Control**: No unauthorized system access
   - **Resource Limits**: No excessive CPU/memory/API usage
   - **Destructive Operations**: No irreversible changes without approval
2. Specify constraint enforcement points (before, during, after execution)
3. Define constraint validation logic
4. Document constraint rationale

**Output**: Safety constraint specification

**Quality Check**: Do constraints prevent identified risks?

---

### Step 3: Define Quality Guardrails
**Objective**: Ensure agent outputs meet quality standards.

**Actions**:
1. Define quality criteria:
   - **Correctness**: Output matches expected format and semantics
   - **Completeness**: All required fields present
   - **Consistency**: Output consistent with previous results
   - **Performance**: Execution time within acceptable range
2. Specify quality validation rules
3. Define quality thresholds (minimum acceptable quality)
4. Design quality scoring mechanisms

**Output**: Quality guardrail specification

**Quality Check**: Can quality be objectively measured?

---

### Step 4: Define Compliance Guardrails
**Objective**: Ensure agent actions comply with regulations and policies.

**Actions**:
1. Identify applicable regulations:
   - Data privacy (GDPR, CCPA)
   - Security standards (SOC 2, ISO 27001)
   - Industry regulations (HIPAA, PCI-DSS)
2. Translate regulations into enforceable rules:
   - Data retention policies
   - Audit logging requirements
   - Access control policies
3. Specify compliance validation checks
4. Document compliance evidence collection

**Output**: Compliance guardrail specification

**Quality Check**: Are all applicable regulations covered?

---

### Step 5: Implement Pre-Execution Validation
**Objective**: Prevent harmful actions before they occur.

**Actions**:
1. Design validation logic for each guardrail
2. Implement validation at agent entry points:
   ```python
   def execute_with_guardrails(agent, action, context):
       # Validate before execution
       validate_safety_constraints(action)
       validate_compliance(action, context)
       validate_resource_limits(action)
       
       # Execute if validation passes
       result = agent.execute(action, context)
       return result
   ```
3. Define validation failure actions:
   - Block action
   - Request approval
   - Use safer alternative
4. Add validation logging

**Output**: Pre-execution validation implementation

**Quality Check**: Does validation catch all constraint violations?

---

### Step 6: Implement Runtime Monitoring
**Objective**: Detect guardrail violations during execution.

**Actions**:
1. Identify monitorable metrics:
   - Resource usage (CPU, memory, API calls)
   - Execution time
   - Data access patterns
   - Output quality
2. Implement monitoring hooks:
   ```python
   def execute_with_monitoring(agent, action):
       monitor = RuntimeMonitor()
       
       with monitor.track():
           result = agent.execute(action)
       
       if monitor.violations:
           handle_violations(monitor.violations)
       
       return result
   ```
3. Define violation detection thresholds
4. Implement real-time alerting

**Output**: Runtime monitoring implementation

**Quality Check**: Can violations be detected in real-time?

---

### Step 7: Implement Post-Execution Validation
**Objective**: Verify agent outputs meet quality and safety standards.

**Actions**:
1. Design output validation logic:
   ```python
   def validate_output(output, expected_schema):
       # Quality checks
       assert output.completeness >= 0.95
       assert output.correctness >= 0.90
       
       # Safety checks
       assert not contains_pii(output)
       assert not contains_credentials(output)
       
       # Compliance checks
       assert output.audit_trail_complete
       
       return True
   ```
2. Implement rollback mechanisms for invalid outputs
3. Define output sanitization (remove sensitive data)
4. Add output logging and auditing

**Output**: Post-execution validation implementation

**Quality Check**: Are all outputs validated before use?

---

### Step 8: Implement Intervention Mechanisms
**Objective**: Take action when guardrails are triggered.

**Actions**:
1. Define intervention strategies:
   - **Block**: Prevent action from executing
   - **Throttle**: Slow down execution
   - **Degrade**: Use safer but less capable alternative
   - **Escalate**: Request human approval
   - **Rollback**: Undo completed action
2. Implement intervention logic:
   ```python
   def handle_guardrail_violation(violation):
       if violation.severity == "critical":
           block_action()
           notify_admin()
       elif violation.severity == "high":
           request_approval()
       elif violation.severity == "medium":
           throttle_execution()
       else:
           log_warning()
   ```
3. Design approval workflows
4. Implement rollback procedures

**Output**: Intervention mechanism implementation

**Quality Check**: Are interventions appropriate for each violation?

---

### Step 9: Implement Audit Logging
**Objective**: Record all agent actions and guardrail events for compliance and debugging.

**Actions**:
1. Define audit log schema:
   ```json
   {
     "timestamp": "2026-09-08T10:00:00Z",
     "agent_id": "code_reviewer_001",
     "action": "delete_file",
     "guardrails_checked": ["safety", "compliance"],
     "violations": [],
     "outcome": "allowed",
     "user": "[email protected]"
   }
   ```
2. Implement comprehensive logging:
   - All agent actions
   - All guardrail checks
   - All violations and interventions
   - All approvals and overrides
3. Ensure log immutability and integrity
4. Implement log retention and archival

**Output**: Audit logging implementation

**Quality Check**: Are logs complete and tamper-proof?

---

### Step 10: Test and Refine Guardrails
**Objective**: Validate guardrails work correctly and don't impede legitimate operations.

**Actions**:
1. Test safety guardrails:
   - Attempt harmful actions (in safe environment)
   - Verify they are blocked
2. Test quality guardrails:
   - Generate low-quality outputs
   - Verify they are rejected
3. Test compliance guardrails:
   - Attempt non-compliant actions
   - Verify they are blocked
4. Test false positive rate:
   - Execute legitimate actions
   - Verify they are allowed
5. Measure performance overhead
6. Refine guardrails based on results

**Output**: Tested and refined guardrails

**Quality Check**: Do guardrails prevent harm without blocking legitimate use?

---

## Decision Framework

### Guardrail Strictness

```
What is the risk of this action?
├─ Critical (data loss, security breach) → Block by default, require approval
├─ High (compliance violation, quality issue) → Validate strictly, log extensively
├─ Medium (resource usage, performance) → Monitor, throttle if needed
└─ Low (minor quality issue) → Log warning, allow

What is the impact of false positives?
├─ High (blocks critical functionality) → Relax guardrails, add override mechanism
├─ Medium (slows down workflow) → Balance strictness and usability
└─ Low (minor inconvenience) → Strict guardrails acceptable
```

### Intervention Strategy

| Violation Type | Severity | Intervention | Rationale |
|----------------|----------|--------------|----------|
| Data deletion | Critical | Block + Require approval | Irreversible |
| PII exposure | Critical | Block + Sanitize | Compliance risk |
| Resource exhaustion | High | Throttle + Alert | Availability risk |
| Quality below threshold | Medium | Retry + Degrade | Usability impact |
| Minor policy violation | Low | Log warning | Low risk |

---

## Quality Checklist

### Safety
- [ ] All high-risk actions identified
- [ ] Safety constraints defined and enforced
- [ ] Destructive operations require approval
- [ ] Data protection guardrails in place
- [ ] Access control enforced

### Quality
- [ ] Quality criteria defined
- [ ] Output validation implemented
- [ ] Quality thresholds enforced
- [ ] Low-quality outputs rejected or flagged

### Compliance
- [ ] Applicable regulations identified
- [ ] Compliance rules implemented
- [ ] Audit logging complete
- [ ] Evidence collection automated

### Performance
- [ ] Guardrail overhead acceptable (<10% latency increase)
- [ ] False positive rate low (<5%)
- [ ] Monitoring real-time

---

## Common Mistakes

### 1. Too Strict Guardrails
**Mistake**: Blocking too many legitimate actions.

**Why It Happens**: Over-cautious risk assessment.

**How to Avoid**: Test with real workloads; add override mechanisms.

**Recovery**: Relax guardrails; add approval workflows.

---

### 2. Too Loose Guardrails
**Mistake**: Not preventing harmful actions.

**Why It Happens**: Underestimating risks.

**How to Avoid**: Thorough risk assessment; test with adversarial inputs.

**Recovery**: Tighten guardrails; add more validation.

---

### 3. No Monitoring
**Mistake**: Not detecting violations in real-time.

**Why It Happens**: Focusing only on pre-execution validation.

**How to Avoid**: Implement runtime monitoring; set up alerts.

**Recovery**: Add monitoring hooks; implement alerting.

---

### 4. No Audit Logging
**Mistake**: Can't prove compliance or debug issues.

**Why It Happens**: Treating logging as optional.

**How to Avoid**: Implement comprehensive logging from the start.

**Recovery**: Add logging retroactively; backfill where possible.

---

### 5. High Performance Overhead
**Mistake**: Guardrails slow down agents significantly.

**Why It Happens**: Inefficient validation logic.

**How to Avoid**: Optimize validation; cache results; validate asynchronously where possible.

**Recovery**: Profile and optimize; reduce validation frequency.

---

## Examples

See [examples.md](./examples.md) for detailed scenarios:

1. **Data Protection Guardrails**: Preventing data loss and unauthorized access
2. **Access Control Guardrails**: Enforcing least-privilege access
3. **Resource Limit Guardrails**: Preventing resource exhaustion
4. **Compliance Guardrails**: Ensuring GDPR and SOC 2 compliance

---

## Related Skills

### Prerequisites
- **agent-task-decomposition**: Understanding agent capabilities
- **agent-workflow-design**: Understanding agent workflows
- **agent-tool-selection**: Understanding agent tools and their risks

### Commonly Followed By
- **agent-evaluation**: Measuring guardrail effectiveness
- **agent-observability**: Monitoring guardrail performance

### Works Well With
- **agent-handoff-design**: Guardrails at handoff boundaries
- **agent-context-engineering**: Validating context safety

---

## Skill Composition

### Pattern: Safe Agent Deployment
```
agent-task-decomposition
    ↓
agent-workflow-design
    ↓
agent-tool-selection
    ↓
agent-guardrails ← YOU ARE HERE
    ↓
agent-evaluation
```

---

## Evaluation Criteria

### Safety Metrics
- **Harmful Actions Prevented**: Percentage of harmful actions blocked (target: 100%)
- **False Positive Rate**: Percentage of legitimate actions blocked (target: <5%)
- **Incident Rate**: Number of safety incidents per 1000 executions (target: 0)

### Quality Metrics
- **Output Quality**: Percentage of outputs meeting quality standards (target: >95%)
- **Quality Violations Detected**: Percentage of low-quality outputs caught (target: >90%)

### Compliance Metrics
- **Compliance Rate**: Percentage of actions compliant with regulations (target: 100%)
- **Audit Completeness**: Percentage of actions with complete audit logs (target: 100%)

### Performance Metrics
- **Guardrail Overhead**: Latency increase due to guardrails (target: <10%)
- **Monitoring Latency**: Time to detect violations (target: <1s)

---

**Version**: 1.0.0  
**Last Updated**: 2026-09-08  
**Complexity**: Advanced  
**Estimated Time**: 2-3 hours