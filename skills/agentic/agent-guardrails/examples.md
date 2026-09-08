# Agent Guardrails - Practical Examples

This document provides four comprehensive examples of implementing guardrails for AI agents in real-world scenarios.

---

## Example 1: Data Protection Guardrails

### Scenario

An AI agent is designed to help with database management tasks, including querying, updating, and maintaining customer data. The agent must prevent data loss, unauthorized access, and compliance violations (GDPR).

### Risks Identified

1. **Data Loss:** Agent may accidentally delete customer records
2. **Unauthorized Access:** Agent may access sensitive PII without authorization
3. **Data Corruption:** Agent may update data incorrectly
4. **Compliance Violations:** Agent may violate GDPR data retention and access policies

### Guardrails Implemented

#### 1. Pre-Execution Guardrails

**Input Validation:**
```python
class DataProtectionGuardrails:
    def validate_query(self, query, context):
        """Validate database query before execution."""
        errors = []
        
        # Block DELETE operations in production
        if context.environment == "production" and "DELETE" in query.upper():
            errors.append("DELETE operations not allowed in production")
        
        # Detect SQL injection
        if self._contains_sql_injection(query):
            errors.append("SQL injection detected")
        
        # Check for sensitive data access
        if self._accesses_sensitive_data(query):
            # Verify user has permission
            if not self._has_permission(context.user_id, "access_pii"):
                errors.append("User not authorized to access PII")
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
    
    def _contains_sql_injection(self, query):
        """Detect SQL injection patterns."""
        injection_patterns = [
            r"';\s*DROP\s+TABLE",
            r"';\s*DELETE\s+FROM",
            r"UNION\s+SELECT",
            r"--",
            r"/\*.*\*/"
        ]
        
        for pattern in injection_patterns:
            if re.search(pattern, query, re.IGNORECASE):
                return True
        
        return False
    
    def _accesses_sensitive_data(self, query):
        """Check if query accesses sensitive columns."""
        sensitive_columns = ["ssn", "credit_card", "password", "email", "phone"]
        
        for column in sensitive_columns:
            if column in query.lower():
                return True
        
        return False
```

**Permission Checks:**
```python
def check_permissions(self, operation, context):
    """Check if agent and user have required permissions."""
    errors = []
    
    # Agent permissions
    required_permissions = {
        "SELECT": ["read_data"],
        "INSERT": ["write_data"],
        "UPDATE": ["write_data", "modify_data"],
        "DELETE": ["write_data", "delete_data"]
    }
    
    operation_type = operation.upper().split()[0]
    required = required_permissions.get(operation_type, [])
    
    agent_permissions = self._get_agent_permissions(context.agent_id)
    missing = set(required) - set(agent_permissions)
    
    if missing:
        errors.append(f"Agent missing permissions: {missing}")
    
    # User authorization
    if not self._is_user_authorized(context.user_id, operation_type):
        errors.append(f"User not authorized for {operation_type} operation")
    
    # Environment check
    if context.environment == "production" and operation_type == "DELETE":
        if not context.production_delete_approved:
            errors.append("DELETE in production requires explicit approval")
    
    return {
        "passed": len(errors) == 0,
        "errors": errors
    }
```

#### 2. Runtime Monitoring

**Data Access Logging:**
```python
class DataAccessMonitor:
    def monitor_query_execution(self, query, context):
        """Monitor query execution and log data access."""
        # Log query execution
        self.audit_logger.log({
            "timestamp": datetime.utcnow(),
            "agent_id": context.agent_id,
            "user_id": context.user_id,
            "query": query,
            "environment": context.environment,
            "accessed_tables": self._extract_tables(query),
            "accessed_columns": self._extract_columns(query)
        })
        
        # Track sensitive data access
        if self._accesses_sensitive_data(query):
            self._log_sensitive_access(query, context)
            self._send_alert(f"Sensitive data accessed by {context.agent_id}")
```

**Rate Limiting:**
```python
def check_rate_limit(self, agent_id, operation_type):
    """Enforce rate limits on database operations."""
    limits = {
        "SELECT": 100,  # per minute
        "INSERT": 50,
        "UPDATE": 30,
        "DELETE": 10
    }
    
    limit = limits.get(operation_type, 100)
    
    # Count recent operations
    recent_ops = self._count_recent_operations(
        agent_id, 
        operation_type, 
        window_seconds=60
    )
    
    if recent_ops >= limit:
        return {
            "allowed": False,
            "reason": f"Rate limit exceeded: {recent_ops}/{limit} operations per minute"
        }
    
    return {"allowed": True}
```

#### 3. Post-Execution Validation

**Data Integrity Checks:**
```python
class DataIntegrityValidator:
    def validate_after_update(self, operation, result):
        """Validate data integrity after update."""
        errors = []
        
        # Check referential integrity
        if not self._check_referential_integrity(operation.table):
            errors.append("Referential integrity violation detected")
        
        # Check business rules
        if not self._validate_business_rules(operation.table):
            errors.append("Business rule violation detected")
        
        # Check for orphaned records
        orphaned = self._find_orphaned_records(operation.table)
        if orphaned:
            errors.append(f"Orphaned records detected: {orphaned}")
        
        # Rollback if validation failed
        if errors:
            self._rollback_transaction(operation.transaction_id)
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**GDPR Compliance Validation:**
```python
def validate_gdpr_compliance(self, operation, result):
    """Ensure operation complies with GDPR."""
    warnings = []
    
    # Check data retention
    if operation.type == "INSERT":
        if not self._has_retention_policy(operation.table):
            warnings.append("No data retention policy defined")
    
    # Check consent for data processing
    if self._processes_personal_data(operation):
        if not self._has_user_consent(operation.user_id):
            warnings.append("No user consent for data processing")
    
    # Log data processing activity
    self._log_data_processing(operation, result)
    
    return {
        "passed": True,
        "warnings": warnings
    }
```

### Results

**Metrics After Implementation:**
- **Data Loss Incidents:** 0 (down from 3 per month)
- **Unauthorized Access Attempts:** 12 blocked per month
- **GDPR Compliance:** 100% (all data processing logged and compliant)
- **False Positive Rate:** 1.2% (minimal impact on legitimate operations)
- **Performance Overhead:** 5.3ms average (2.1% of total query time)

**Key Learnings:**
1. Pre-execution validation prevents most issues before they occur
2. Audit logging is critical for compliance and incident investigation
3. Rate limiting prevents accidental or malicious bulk operations
4. Post-execution validation catches edge cases and ensures data integrity

---

## Example 2: Access Control Guardrails

### Scenario

An AI agent assists with cloud infrastructure management (AWS), including creating resources, modifying configurations, and managing access controls. The agent must prevent unauthorized access, privilege escalation, and resource abuse.

### Risks Identified

1. **Privilege Escalation:** Agent may grant itself or users excessive permissions
2. **Unauthorized Resource Access:** Agent may access resources outside its scope
3. **Resource Abuse:** Agent may create expensive resources or exceed quotas
4. **Security Misconfigurations:** Agent may create publicly accessible resources

### Guardrails Implemented

#### 1. Pre-Execution Guardrails

**Permission Boundary Enforcement:**
```python
class AccessControlGuardrails:
    def validate_iam_operation(self, operation, context):
        """Validate IAM operations to prevent privilege escalation."""
        errors = []
        
        # Check if operation attempts to modify permissions
        if operation.type in ["CreateRole", "AttachRolePolicy", "PutUserPolicy"]:
            # Verify agent is not granting itself permissions
            if operation.target == context.agent_id:
                errors.append("Agent cannot modify its own permissions")
            
            # Check for privilege escalation
            new_permissions = self._extract_permissions(operation.policy)
            agent_permissions = self._get_agent_permissions(context.agent_id)
            
            escalation = set(new_permissions) - set(agent_permissions)
            if escalation:
                errors.append(
                    f"Privilege escalation detected: attempting to grant {escalation}"
                )
        
        # Enforce permission boundaries
        if not self._within_permission_boundary(operation, context):
            errors.append("Operation exceeds agent permission boundary")
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
    
    def _within_permission_boundary(self, operation, context):
        """Check if operation is within agent's permission boundary."""
        # Get agent's permission boundary
        boundary = self._get_permission_boundary(context.agent_id)
        
        # Check if operation is allowed by boundary
        required_permissions = self._get_required_permissions(operation)
        
        for permission in required_permissions:
            if not self._is_allowed_by_boundary(permission, boundary):
                return False
        
        return True
```

**Resource Scope Validation:**
```python
def validate_resource_access(self, resource_arn, context):
    """Validate agent can access the requested resource."""
    errors = []
    
    # Parse resource ARN
    resource = self._parse_arn(resource_arn)
    
    # Check if resource is in agent's allowed scope
    allowed_scopes = self._get_allowed_scopes(context.agent_id)
    
    resource_scope = f"{resource.account}:{resource.region}:{resource.service}"
    
    if resource_scope not in allowed_scopes:
        errors.append(
            f"Resource {resource_arn} outside agent scope. "
            f"Allowed scopes: {allowed_scopes}"
        )
    
    # Check for cross-account access
    if resource.account != context.account_id:
        if not context.cross_account_approved:
            errors.append("Cross-account access requires approval")
    
    return {
        "passed": len(errors) == 0,
        "errors": errors
    }
```

**Security Configuration Validation:**
```python
def validate_security_configuration(self, resource_config):
    """Validate resource configuration for security best practices."""
    errors = []
    
    # Check for public access
    if resource_config.get("public_access", False):
        errors.append("Public access not allowed without explicit approval")
    
    # Check for encryption
    if resource_config.type in ["S3Bucket", "RDSInstance", "EBSVolume"]:
        if not resource_config.get("encryption_enabled", False):
            errors.append("Encryption required for data storage resources")
    
    # Check for overly permissive security groups
    if resource_config.type == "SecurityGroup":
        for rule in resource_config.get("ingress_rules", []):
            if rule.get("cidr") == "0.0.0.0/0" and rule.get("port") != 443:
                errors.append(
                    f"Overly permissive security group rule: {rule}"
                )
    
    return {
        "passed": len(errors) == 0,
        "errors": errors
    }
```

#### 2. Runtime Monitoring

**Access Pattern Monitoring:**
```python
class AccessPatternMonitor:
    def monitor_access(self, agent_id, resource, operation):
        """Monitor access patterns for anomalies."""
        # Log access
        self.audit_logger.log({
            "timestamp": datetime.utcnow(),
            "agent_id": agent_id,
            "resource": resource,
            "operation": operation
        })
        
        # Check for unusual access patterns
        if self._is_unusual_access(agent_id, resource):
            self._send_alert(
                f"Unusual access pattern detected for agent {agent_id}"
            )
            
            # Require additional verification
            return {
                "allowed": False,
                "reason": "Unusual access pattern - additional verification required"
            }
        
        return {"allowed": True}
    
    def _is_unusual_access(self, agent_id, resource):
        """Detect unusual access patterns."""
        # Get agent's historical access patterns
        historical_access = self._get_access_history(agent_id)
        
        # Check if resource is frequently accessed
        access_frequency = historical_access.get(resource, 0)
        
        # Flag if this is first-time access to sensitive resource
        if access_frequency == 0 and self._is_sensitive_resource(resource):
            return True
        
        # Check for access at unusual times
        if self._is_unusual_time():
            return True
        
        return False
```

**Resource Quota Enforcement:**
```python
def enforce_resource_quota(self, agent_id, resource_type):
    """Enforce resource creation quotas."""
    quotas = {
        "EC2Instance": 10,
        "S3Bucket": 20,
        "RDSInstance": 5,
        "LambdaFunction": 50
    }
    
    quota = quotas.get(resource_type, 100)
    
    # Count existing resources
    existing_count = self._count_resources(agent_id, resource_type)
    
    if existing_count >= quota:
        return {
            "allowed": False,
            "reason": f"Resource quota exceeded: {existing_count}/{quota} {resource_type}s"
        }
    
    return {"allowed": True}
```

#### 3. Post-Execution Validation

**Permission Verification:**
```python
class PermissionVerifier:
    def verify_permissions_after_change(self, operation, result):
        """Verify permissions are correct after IAM change."""
        errors = []
        
        # Get actual permissions after change
        actual_permissions = self._get_actual_permissions(operation.target)
        
        # Compare with intended permissions
        intended_permissions = self._get_intended_permissions(operation)
        
        # Check for unintended permissions
        unintended = set(actual_permissions) - set(intended_permissions)
        if unintended:
            errors.append(f"Unintended permissions granted: {unintended}")
        
        # Check for missing permissions
        missing = set(intended_permissions) - set(actual_permissions)
        if missing:
            errors.append(f"Intended permissions not granted: {missing}")
        
        # Rollback if verification failed
        if errors:
            self._rollback_iam_change(operation)
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**Security Posture Validation:**
```python
def validate_security_posture(self, resource):
    """Validate resource security posture after creation."""
    warnings = []
    
    # Check for public access
    if self._is_publicly_accessible(resource):
        warnings.append(f"Resource {resource.id} is publicly accessible")
    
    # Check for encryption
    if not self._is_encrypted(resource):
        warnings.append(f"Resource {resource.id} is not encrypted")
    
    # Check for logging enabled
    if not self._has_logging_enabled(resource):
        warnings.append(f"Logging not enabled for {resource.id}")
    
    # Check for compliance tags
    if not self._has_compliance_tags(resource):
        warnings.append(f"Compliance tags missing for {resource.id}")
    
    return {
        "passed": True,
        "warnings": warnings
    }
```

### Results

**Metrics After Implementation:**
- **Privilege Escalation Attempts:** 8 blocked per month
- **Unauthorized Access Attempts:** 15 blocked per month
- **Security Misconfigurations:** 0 (down from 5 per month)
- **Resource Quota Violations:** 12 prevented per month
- **False Positive Rate:** 3.5%
- **Performance Overhead:** 12.1ms average (4.2% of total operation time)

**Key Learnings:**
1. Permission boundaries are critical for preventing privilege escalation
2. Anomaly detection catches unusual access patterns early
3. Post-execution verification ensures intended state matches actual state
4. Security configuration validation prevents misconfigurations

---

## Example 3: Resource Limit Guardrails

### Scenario

An AI agent performs code analysis and testing tasks, which can be computationally expensive. The agent must prevent resource exhaustion, cost overruns, and denial-of-service scenarios.

### Risks Identified

1. **CPU Exhaustion:** Agent may consume excessive CPU, slowing down other processes
2. **Memory Exhaustion:** Agent may cause out-of-memory errors
3. **API Rate Limit Violations:** Agent may exceed external API rate limits
4. **Cost Overruns:** Agent may incur excessive cloud costs
5. **Infinite Loops:** Agent may get stuck in infinite loops

### Guardrails Implemented

#### 1. Pre-Execution Guardrails

**Resource Estimation:**
```python
class ResourceLimitGuardrails:
    def estimate_resources(self, task):
        """Estimate resource requirements before execution."""
        # Estimate based on task type and input size
        estimates = {
            "cpu_cores": self._estimate_cpu(task),
            "memory_mb": self._estimate_memory(task),
            "execution_time_seconds": self._estimate_time(task),
            "api_calls": self._estimate_api_calls(task),
            "estimated_cost": self._estimate_cost(task)
        }
        
        return estimates
    
    def validate_resource_availability(self, estimates, context):
        """Validate sufficient resources are available."""
        errors = []
        
        # Check CPU availability
        available_cpu = self._get_available_cpu()
        if estimates["cpu_cores"] > available_cpu:
            errors.append(
                f"Insufficient CPU: need {estimates['cpu_cores']}, "
                f"available {available_cpu}"
            )
        
        # Check memory availability
        available_memory = self._get_available_memory()
        if estimates["memory_mb"] > available_memory:
            errors.append(
                f"Insufficient memory: need {estimates['memory_mb']}MB, "
                f"available {available_memory}MB"
            )
        
        # Check budget
        remaining_budget = self._get_remaining_budget(context.user_id)
        if estimates["estimated_cost"] > remaining_budget:
            errors.append(
                f"Insufficient budget: need ${estimates['estimated_cost']}, "
                f"remaining ${remaining_budget}"
            )
        
        # Check API quota
        remaining_quota = self._get_remaining_api_quota(context.agent_id)
        if estimates["api_calls"] > remaining_quota:
            errors.append(
                f"Insufficient API quota: need {estimates['api_calls']}, "
                f"remaining {remaining_quota}"
            )
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**Timeout Configuration:**
```python
def configure_timeout(self, task):
    """Configure execution timeout based on task complexity."""
    # Base timeout
    base_timeout = 300  # 5 minutes
    
    # Adjust based on task complexity
    complexity_multiplier = {
        "simple": 1.0,
        "moderate": 2.0,
        "complex": 5.0
    }
    
    multiplier = complexity_multiplier.get(task.complexity, 1.0)
    timeout = base_timeout * multiplier
    
    # Cap at maximum timeout
    max_timeout = 3600  # 1 hour
    timeout = min(timeout, max_timeout)
    
    return timeout
```

#### 2. Runtime Monitoring

**Resource Usage Tracking:**
```python
class ResourceMonitor:
    def __init__(self):
        self.usage = {}
    
    def track_usage(self, agent_id):
        """Track real-time resource usage."""
        import psutil
        
        process = self._get_agent_process(agent_id)
        
        usage = {
            "cpu_percent": process.cpu_percent(interval=1),
            "memory_mb": process.memory_info().rss / 1024 / 1024,
            "execution_time": time.time() - process.create_time(),
            "api_calls": self._count_api_calls(agent_id)
        }
        
        self.usage[agent_id] = usage
        
        # Check limits
        violations = self._check_limits(agent_id, usage)
        
        if violations:
            return self._handle_violations(agent_id, violations)
        
        return {"status": "ok"}
    
    def _check_limits(self, agent_id, usage):
        """Check if usage exceeds limits."""
        violations = []
        
        limits = self._get_limits(agent_id)
        
        if usage["cpu_percent"] > limits["max_cpu_percent"]:
            violations.append({
                "type": "cpu_exceeded",
                "current": usage["cpu_percent"],
                "limit": limits["max_cpu_percent"]
            })
        
        if usage["memory_mb"] > limits["max_memory_mb"]:
            violations.append({
                "type": "memory_exceeded",
                "current": usage["memory_mb"],
                "limit": limits["max_memory_mb"]
            })
        
        if usage["execution_time"] > limits["max_execution_time"]:
            violations.append({
                "type": "timeout",
                "current": usage["execution_time"],
                "limit": limits["max_execution_time"]
            })
        
        return violations
    
    def _handle_violations(self, agent_id, violations):
        """Handle resource limit violations."""
        for violation in violations:
            if violation["type"] == "cpu_exceeded":
                # Throttle agent
                self._throttle_agent(agent_id)
            
            elif violation["type"] == "memory_exceeded":
                # Terminate agent to prevent OOM
                self._terminate_agent(agent_id, "Memory limit exceeded")
            
            elif violation["type"] == "timeout":
                # Terminate agent
                self._terminate_agent(agent_id, "Execution timeout")
        
        return {
            "status": "violations_detected",
            "violations": violations
        }
```

**API Rate Limiting:**
```python
class APIRateLimiter:
    def __init__(self):
        self.call_history = {}
    
    def check_rate_limit(self, agent_id, api_name):
        """Check if API call is within rate limit."""
        # Get rate limit for API
        limit = self._get_rate_limit(api_name)
        
        # Count recent calls
        recent_calls = self._count_recent_calls(
            agent_id, 
            api_name, 
            window_seconds=limit["window"]
        )
        
        if recent_calls >= limit["max_calls"]:
            # Calculate retry after
            retry_after = self._calculate_retry_after(
                agent_id, 
                api_name, 
                limit["window"]
            )
            
            return {
                "allowed": False,
                "reason": "Rate limit exceeded",
                "retry_after_seconds": retry_after
            }
        
        # Record call
        self._record_call(agent_id, api_name)
        
        return {"allowed": True}
```

#### 3. Post-Execution Validation

**Cost Validation:**
```python
class CostValidator:
    def validate_cost(self, agent_id, actual_cost):
        """Validate actual cost against estimate and budget."""
        warnings = []
        
        # Get estimated cost
        estimated_cost = self._get_estimated_cost(agent_id)
        
        # Check if actual cost significantly exceeds estimate
        if actual_cost > estimated_cost * 1.5:
            warnings.append(
                f"Actual cost (${actual_cost}) significantly exceeds "
                f"estimate (${estimated_cost})"
            )
        
        # Check if budget exceeded
        budget = self._get_budget(agent_id)
        total_cost = self._get_total_cost(agent_id)
        
        if total_cost > budget:
            warnings.append(
                f"Budget exceeded: ${total_cost} / ${budget}"
            )
        
        # Log cost
        self._log_cost(agent_id, actual_cost)
        
        return {
            "passed": True,
            "warnings": warnings
        }
```

**Resource Cleanup:**
```python
def cleanup_resources(self, agent_id):
    """Clean up resources after agent execution."""
    # Terminate agent process
    self._terminate_agent_process(agent_id)
    
    # Release allocated resources
    self._release_cpu(agent_id)
    self._release_memory(agent_id)
    
    # Close API connections
    self._close_api_connections(agent_id)
    
    # Delete temporary files
    self._delete_temp_files(agent_id)
    
    # Log resource usage
    self._log_resource_usage(agent_id)
```

### Results

**Metrics After Implementation:**
- **CPU Exhaustion Incidents:** 0 (down from 8 per month)
- **Out-of-Memory Errors:** 0 (down from 5 per month)
- **API Rate Limit Violations:** 0 (down from 20 per month)
- **Cost Overruns:** 0 (down from 3 per month, average $500 each)
- **Timeout Incidents:** 3 per month (agents terminated gracefully)
- **False Positive Rate:** 2.8%
- **Performance Overhead:** 3.2ms average (1.1% of total execution time)

**Key Learnings:**
1. Resource estimation before execution prevents most issues
2. Real-time monitoring enables early intervention
3. Graceful termination prevents cascading failures
4. Cost tracking prevents budget overruns

---

## Example 4: Compliance Guardrails (GDPR, SOC 2, HIPAA)

### Scenario

An AI agent processes customer data for analytics and reporting. The agent must comply with GDPR (data privacy), SOC 2 (security controls), and HIPAA (healthcare data protection).

### Compliance Requirements

1. **GDPR:**
   - User consent for data processing
   - Right to be forgotten (data deletion)
   - Data retention limits
   - Audit trail of data processing
   - Data minimization

2. **SOC 2:**
   - Access controls
   - Audit logging
   - Change management
   - Incident response

3. **HIPAA:**
   - PHI (Protected Health Information) encryption
   - Access controls for PHI
   - Audit logging of PHI access
   - Breach notification

### Guardrails Implemented

#### 1. Pre-Execution Guardrails

**Consent Verification (GDPR):**
```python
class GDPRGuardrails:
    def verify_consent(self, user_id, processing_purpose):
        """Verify user consent for data processing."""
        errors = []
        
        # Check if user has given consent
        consent = self._get_user_consent(user_id)
        
        if not consent:
            errors.append(f"No consent found for user {user_id}")
        elif processing_purpose not in consent.purposes:
            errors.append(
                f"User {user_id} has not consented to {processing_purpose}"
            )
        elif consent.expired:
            errors.append(f"Consent for user {user_id} has expired")
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
    
    def check_data_retention(self, data, retention_policy):
        """Check if data is within retention period."""
        errors = []
        
        for record in data:
            age_days = (datetime.utcnow() - record.created_at).days
            
            if age_days > retention_policy.max_retention_days:
                errors.append(
                    f"Record {record.id} exceeds retention period: "
                    f"{age_days} days > {retention_policy.max_retention_days} days"
                )
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**Access Control Verification (SOC 2, HIPAA):**
```python
class AccessControlGuardrails:
    def verify_access_controls(self, agent_id, data_classification):
        """Verify agent has appropriate access controls."""
        errors = []
        
        # Get agent's access level
        access_level = self._get_access_level(agent_id)
        
        # Check if access level is sufficient for data classification
        required_level = {
            "public": "basic",
            "internal": "standard",
            "confidential": "elevated",
            "phi": "hipaa_authorized"  # HIPAA
        }
        
        required = required_level.get(data_classification)
        
        if not self._has_sufficient_access(access_level, required):
            errors.append(
                f"Agent {agent_id} access level ({access_level}) insufficient "
                f"for {data_classification} data (requires {required})"
            )
        
        # Check for HIPAA authorization
        if data_classification == "phi":
            if not self._is_hipaa_authorized(agent_id):
                errors.append(
                    f"Agent {agent_id} not authorized to access PHI"
                )
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**Encryption Verification (HIPAA):**
```python
def verify_encryption(self, data, data_classification):
    """Verify data encryption for sensitive data."""
    errors = []
    
    # PHI must be encrypted
    if data_classification == "phi":
        if not self._is_encrypted(data):
            errors.append("PHI must be encrypted at rest and in transit")
        
        # Verify encryption strength
        encryption_algorithm = self._get_encryption_algorithm(data)
        if encryption_algorithm not in ["AES-256", "RSA-2048"]:
            errors.append(
                f"Weak encryption algorithm: {encryption_algorithm}. "
                "HIPAA requires AES-256 or RSA-2048"
            )
    
    return {
        "passed": len(errors) == 0,
        "errors": errors
    }
```

#### 2. Runtime Monitoring

**Audit Logging (GDPR, SOC 2, HIPAA):**
```python
class ComplianceAuditLogger:
    def log_data_processing(self, agent_id, operation, data):
        """Log data processing activity for compliance."""
        # Determine data classification
        classification = self._classify_data(data)
        
        # Create audit log entry
        log_entry = {
            "timestamp": datetime.utcnow().isoformat(),
            "agent_id": agent_id,
            "operation": operation,
            "data_classification": classification,
            "data_subjects": self._extract_data_subjects(data),
            "processing_purpose": operation.purpose,
            "legal_basis": operation.legal_basis,  # GDPR
            "data_fields": self._extract_data_fields(data),
            "user_id": operation.user_id,
            "ip_address": operation.ip_address
        }
        
        # Store audit log (immutable, tamper-proof)
        self._store_audit_log(log_entry)
        
        # Alert if PHI accessed (HIPAA)
        if classification == "phi":
            self._send_phi_access_alert(log_entry)
```

**Data Minimization (GDPR):**
```python
class DataMinimizationMonitor:
    def monitor_data_access(self, agent_id, requested_fields, purpose):
        """Ensure agent only accesses necessary data."""
        warnings = []
        
        # Determine minimum required fields for purpose
        required_fields = self._get_required_fields(purpose)
        
        # Check for excessive data access
        excessive = set(requested_fields) - set(required_fields)
        
        if excessive:
            warnings.append(
                f"Agent requesting excessive data fields: {excessive}. "
                f"Only {required_fields} required for {purpose}"
            )
            
            # Block excessive fields
            return {
                "allowed_fields": required_fields,
                "blocked_fields": excessive
            }
        
        return {
            "allowed_fields": requested_fields,
            "blocked_fields": []
        }
```

#### 3. Post-Execution Validation

**Data Deletion Verification (GDPR Right to be Forgotten):**
```python
class DataDeletionValidator:
    def verify_deletion(self, deletion_request):
        """Verify data deletion for GDPR compliance."""
        errors = []
        
        user_id = deletion_request.user_id
        
        # Check all data stores
        data_stores = [
            "primary_database",
            "analytics_database",
            "backup_storage",
            "logs",
            "cache"
        ]
        
        for store in data_stores:
            remaining_data = self._find_user_data(user_id, store)
            
            if remaining_data:
                errors.append(
                    f"User data still present in {store}: {remaining_data}"
                )
        
        # Verify deletion logged
        if not self._is_deletion_logged(user_id):
            errors.append(f"Deletion not logged for user {user_id}")
        
        return {
            "passed": len(errors) == 0,
            "errors": errors
        }
```

**Breach Detection (HIPAA):**
```python
class BreachDetector:
    def detect_breach(self, agent_result):
        """Detect potential data breaches."""
        breaches = []
        
        # Check for unauthorized PHI access
        if self._contains_phi(agent_result.data):
            if not self._is_authorized_phi_access(agent_result.agent_id):
                breaches.append({
                    "type": "unauthorized_phi_access",
                    "severity": "critical",
                    "description": "Unauthorized access to PHI detected"
                })
        
        # Check for PHI in logs
        if self._phi_in_logs(agent_result.logs):
            breaches.append({
                "type": "phi_in_logs",
                "severity": "high",
                "description": "PHI found in application logs"
            })
        
        # Check for unencrypted PHI transmission
        if self._unencrypted_phi_transmission(agent_result):
            breaches.append({
                "type": "unencrypted_phi",
                "severity": "critical",
                "description": "PHI transmitted without encryption"
            })
        
        # Trigger breach notification if needed
        if breaches:
            self._trigger_breach_notification(breaches)
        
        return breaches
```

**Compliance Report Generation:**
```python
def generate_compliance_report(self, agent_id, time_period):
    """Generate compliance report for audit."""
    report = {
        "agent_id": agent_id,
        "time_period": time_period,
        "gdpr_compliance": self._check_gdpr_compliance(agent_id, time_period),
        "soc2_compliance": self._check_soc2_compliance(agent_id, time_period),
        "hipaa_compliance": self._check_hipaa_compliance(agent_id, time_period)
    }
    
    return report

def _check_gdpr_compliance(self, agent_id, time_period):
    """Check GDPR compliance."""
    return {
        "consent_verification": self._audit_consent_verification(agent_id, time_period),
        "data_retention": self._audit_data_retention(agent_id, time_period),
        "data_minimization": self._audit_data_minimization(agent_id, time_period),
        "deletion_requests": self._audit_deletion_requests(agent_id, time_period),
        "audit_trail": self._verify_audit_trail(agent_id, time_period)
    }

def _check_hipaa_compliance(self, agent_id, time_period):
    """Check HIPAA compliance."""
    return {
        "phi_encryption": self._audit_phi_encryption(agent_id, time_period),
        "access_controls": self._audit_access_controls(agent_id, time_period),
        "audit_logging": self._audit_logging(agent_id, time_period),
        "breach_incidents": self._audit_breach_incidents(agent_id, time_period)
    }
```

### Results

**Metrics After Implementation:**
- **GDPR Compliance:** 100% (all data processing logged, consent verified)
- **SOC 2 Compliance:** 100% (access controls enforced, audit trail complete)
- **HIPAA Compliance:** 100% (PHI encrypted, access authorized, breaches detected)
- **Compliance Violations:** 0
- **Audit Findings:** 0 (down from 12 per quarter)
- **Data Breach Incidents:** 0
- **False Positive Rate:** 1.5%
- **Performance Overhead:** 8.7ms average (3.1% of total processing time)

**Key Learnings:**
1. Comprehensive audit logging is critical for compliance
2. Pre-execution consent verification prevents most violations
3. Encryption and access controls are foundational
4. Automated compliance reporting reduces audit burden
5. Data minimization reduces risk and improves performance

---

## Summary

These four examples demonstrate comprehensive guardrail implementations for:

1. **Data Protection:** Preventing data loss, unauthorized access, and corruption
2. **Access Control:** Preventing privilege escalation and unauthorized resource access
3. **Resource Limits:** Preventing resource exhaustion and cost overruns
4. **Compliance:** Ensuring GDPR, SOC 2, and HIPAA compliance

### Common Success Factors

- **Layered Validation:** Pre-execution, runtime, and post-execution checks
- **Comprehensive Logging:** Audit trail for debugging and compliance
- **Graceful Intervention:** Block, throttle, degrade, escalate, or rollback based on severity
- **Continuous Monitoring:** Real-time tracking and alerting
- **Regular Review:** Tune guardrails based on metrics and feedback

### Recommended Next Steps

1. Implement guardrails incrementally (start with highest risks)
2. Test thoroughly before production deployment
3. Monitor metrics and tune thresholds
4. Document all guardrails and configurations
5. Review and update regularly based on new risks and requirements
