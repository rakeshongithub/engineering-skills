# Security Architecture Review

## Purpose

Systematically evaluate architecture from a security perspective to identify threats, vulnerabilities, and required controls.

## When to Use

- Before launching a new system or major feature that handles sensitive data
- When preparing for security audits or compliance certifications (SOC 2, ISO 27001, HIPAA, PCI-DSS)
- After a security incident to prevent recurrence
- When integrating with third-party systems or APIs
- During architecture reviews for systems handling authentication, authorization, or payments
- When expanding to new markets with different regulatory requirements
- Before processing new types of sensitive data (PII, PHI, financial data)
- When significant architecture changes are planned

## When NOT to Use

- For code-level security reviews (use code-security-review instead)
- For penetration testing (use penetration-testing instead)
- For general architecture reviews (use architecture-review instead)
- For compliance-only assessments without architecture changes (use compliance-audit instead)
- For incident response (use incident-response instead)

## Inputs

- **architecture**: System architecture diagrams, component descriptions, data flows
- **security-requirements**: Security objectives, compliance requirements, risk tolerance
- **threat-model**: Known threats, attack vectors, threat actors (if available)
- **compliance-requirements**: Regulatory requirements (GDPR, HIPAA, PCI-DSS, SOC 2, etc.)
- **data-classification**: Types of data handled, sensitivity levels

## Expected Outputs

- **security-findings**: Identified vulnerabilities, threats, and security gaps
- **threat-assessment**: Likelihood and impact analysis for identified threats
- **required-controls**: Security controls needed to mitigate threats
- **remediation-plan**: Prioritized action items with implementation guidance
- **compliance-gaps**: Gaps against regulatory requirements

## Workflow

### 1. Understand Security Context

**Define security objectives:**
- Confidentiality requirements (data protection)
- Integrity requirements (data accuracy, tamper protection)
- Availability requirements (uptime, DDoS protection)
- Compliance requirements (GDPR, HIPAA, PCI-DSS, SOC 2)

**Identify assets to protect:**
- User data (PII, credentials, preferences)
- Business data (financial, proprietary, customer)
- System resources (servers, databases, APIs)
- Intellectual property (code, algorithms, designs)

**Understand threat landscape:**
- Who are the threat actors? (External attackers, insiders, competitors)
- What are their motivations? (Financial gain, espionage, disruption)
- What is the organization's risk tolerance?
- What is the regulatory environment?

### 2. Map Attack Surface

**External attack surface:**
- Public APIs and endpoints
- Web applications and mobile apps
- Authentication and authorization mechanisms
- Third-party integrations
- DNS and domain infrastructure

**Internal attack surface:**
- Internal APIs and services
- Databases and data stores
- Message queues and event buses
- Admin interfaces and tools
- CI/CD pipelines and infrastructure

**Data flows:**
- Data in transit (client ↔ server, service ↔ service)
- Data at rest (databases, file storage, backups)
- Data processing (transformations, aggregations)
- Data retention and deletion

**Trust boundaries:**
- Internet ↔ DMZ
- DMZ ↔ Internal network
- Service ↔ Service
- User ↔ Application
- Application ↔ Database

### 3. Identify Threats (STRIDE Model)

For each component and data flow, assess:

**Spoofing:**
- Can an attacker impersonate a user or service?
- Is authentication strong enough?
- Are tokens or sessions vulnerable to theft?

**Tampering:**
- Can data be modified in transit or at rest?
- Are there integrity checks?
- Can configuration be tampered with?

**Repudiation:**
- Can users deny actions they performed?
- Are audit logs comprehensive and tamper-proof?
- Is non-repudiation required?

**Information Disclosure:**
- Can sensitive data be accessed without authorization?
- Is data encrypted in transit and at rest?
- Are error messages leaking information?

**Denial of Service:**
- Can the system be overwhelmed?
- Are there rate limits and throttling?
- Is there protection against DDoS attacks?

**Elevation of Privilege:**
- Can users gain unauthorized access?
- Is the principle of least privilege followed?
- Are there privilege escalation vulnerabilities?

### 4. Assess Existing Controls

**Authentication:**
- Multi-factor authentication (MFA)
- Password policies and storage (hashing, salting)
- Session management
- OAuth/OIDC implementation
- API key management

**Authorization:**
- Role-based access control (RBAC)
- Attribute-based access control (ABAC)
- Principle of least privilege
- Authorization checks at every layer

**Data Protection:**
- Encryption in transit (TLS 1.2+)
- Encryption at rest (database, file storage)
- Key management (rotation, storage)
- Data masking and tokenization
- Secure deletion

**Network Security:**
- Firewalls and security groups
- Network segmentation
- VPNs for remote access
- DDoS protection
- WAF (Web Application Firewall)

**Application Security:**
- Input validation and sanitization
- Output encoding
- CSRF protection
- XSS prevention
- SQL injection prevention
- Dependency scanning

**Monitoring and Logging:**
- Security event logging
- Audit trails
- Anomaly detection
- Alerting on suspicious activity
- Log retention and protection

**Incident Response:**
- Incident response plan
- Backup and recovery procedures
- Communication protocols
- Forensics capabilities

### 5. Identify Gaps and Vulnerabilities

**Categorize findings by severity:**

**Critical:**
- Unauthenticated access to sensitive data
- Unencrypted transmission of credentials or PII
- SQL injection or remote code execution vulnerabilities
- Missing encryption at rest for regulated data
- Hardcoded secrets in code or configuration

**High:**
- Weak authentication mechanisms (no MFA)
- Insufficient authorization checks
- Missing rate limiting or DDoS protection
- Inadequate logging and monitoring
- Vulnerable dependencies

**Medium:**
- Missing security headers
- Weak password policies
- Insufficient input validation
- Missing CSRF protection
- Overly permissive security groups

**Low:**
- Information disclosure in error messages
- Missing security documentation
- Lack of security training for developers
- Missing security testing in CI/CD

### 6. Assess Compliance

**For each applicable regulation:**

**GDPR (EU data protection):**
- Lawful basis for processing
- Data minimization
- Right to access, rectification, erasure
- Data breach notification
- Privacy by design

**HIPAA (US healthcare):**
- Access controls
- Audit controls
- Integrity controls
- Transmission security
- Encryption

**PCI-DSS (payment card data):**
- Network segmentation
- Encryption of cardholder data
- Access controls
- Monitoring and testing
- Security policies

**SOC 2 (service organization controls):**
- Security
- Availability
- Processing integrity
- Confidentiality
- Privacy

### 7. Develop Remediation Plan

**For each finding:**

**Problem Statement:**
- What is the vulnerability or gap?
- What is the threat it enables?
- What is the potential impact?

**Recommended Control:**
- What security control should be implemented?
- How does it mitigate the threat?
- What are alternative approaches?

**Implementation Guidance:**
- Specific steps to implement the control
- Technologies or tools to use
- Configuration examples

**Effort Estimate:**
- Small (days): Configuration changes, enabling features
- Medium (weeks): Implementing new controls, integrations
- Large (months): Major architecture changes, encryption rollout

**Priority:**
- **Critical**: Must fix immediately (days)
- **High**: Must fix soon (weeks)
- **Medium**: Should fix (months)
- **Low**: Nice to fix (backlog)

### 8. Document and Present

**Executive Summary:**
- Overall security posture (Red/Yellow/Green)
- Top 3-5 critical risks
- Compliance status
- Recommended immediate actions

**Threat Assessment:**
- Identified threats with likelihood and impact
- Attack scenarios
- Risk matrix

**Detailed Findings:**
- All vulnerabilities and gaps
- Severity ratings
- Supporting evidence
- Recommended controls

**Remediation Roadmap:**
- Immediate actions (0-1 month)
- Short-term actions (1-3 months)
- Long-term actions (3-12 months)

**Compliance Assessment:**
- Gaps against each applicable regulation
- Required controls for compliance
- Timeline to compliance

## Decision Framework

### Threat Severity

**Critical:**
- Enables unauthorized access to sensitive data
- Allows remote code execution or system compromise
- Violates regulatory requirements with severe penalties
- High likelihood and high impact

**High:**
- Enables privilege escalation or data tampering
- Allows denial of service attacks
- Significantly increases attack surface
- Medium-to-high likelihood or high impact

**Medium:**
- Enables information disclosure
- Allows limited unauthorized access
- Violates security best practices
- Low-to-medium likelihood or medium impact

**Low:**
- Minor security improvements
- Defense-in-depth enhancements
- Low likelihood and low impact

### Control Selection

**Preventive Controls:**
- Stop threats before they occur
- Examples: Firewalls, authentication, encryption, input validation
- **Use when**: Threat is well-understood and preventable

**Detective Controls:**
- Detect threats when they occur
- Examples: Logging, monitoring, intrusion detection, anomaly detection
- **Use when**: Prevention is not feasible or as defense-in-depth

**Corrective Controls:**
- Respond to and recover from threats
- Examples: Incident response, backups, failover, patching
- **Use when**: Threats cannot be fully prevented or detected

**Compensating Controls:**
- Alternative controls when primary controls are not feasible
- Examples: Additional monitoring when encryption is not possible
- **Use when**: Primary control is too costly or technically infeasible

## Quality Checklist

- [ ] All system components and data flows are mapped
- [ ] Attack surface is comprehensively identified
- [ ] Threats are assessed using a structured model (STRIDE or similar)
- [ ] Existing security controls are documented and evaluated
- [ ] Vulnerabilities and gaps are identified with severity ratings
- [ ] Compliance requirements are assessed
- [ ] Recommended controls are specific and actionable
- [ ] Implementation guidance is provided for each control
- [ ] Effort estimates and priorities are assigned
- [ ] Remediation plan is realistic and time-bound
- [ ] Findings are validated with security and engineering teams
- [ ] Executive summary is clear and highlights critical risks

## Common Mistakes

- **Checklist-only approach**: Following a checklist without understanding the actual threats
- **Ignoring business context**: Recommending controls that don't align with risk tolerance
- **Over-focusing on compliance**: Meeting compliance requirements but missing actual security risks
- **Technology-only solutions**: Ignoring people and process controls
- **No threat modeling**: Identifying vulnerabilities without understanding attack scenarios
- **Vague recommendations**: Saying "improve security" without specific controls
- **Ignoring usability**: Implementing controls that make the system unusable
- **No prioritization**: Treating all findings equally instead of focusing on critical risks
- **Missing defense-in-depth**: Relying on a single control instead of layered security
- **No validation**: Not testing that controls actually work as intended
- **Ignoring insider threats**: Focusing only on external attackers
- **No follow-up**: Not tracking remediation or re-assessing after changes

## Examples

See [examples.md](examples.md) for detailed examples of security architecture reviews for various scenarios.

## Related Skills

- **Requires**: 
  - architecture-discovery (to understand the architecture)
- **Commonly followed by**: 
  - architecture-decision (to decide on security controls)
  - security-implementation (to implement controls)
- **Alternative to**: 
  - architecture-review (for general architecture assessment)
- **Works with**: 
  - threat-modeling (for detailed threat analysis)
  - compliance-audit (for regulatory compliance)
  - penetration-testing (for validation)
  - code-security-review (for implementation-level security)

## Skill Composition

Typical workflow:

```
architecture-discovery
        ↓
security-architecture-review
        ↓
threat-modeling (detailed)
        ↓
architecture-decision
        ↓
security-implementation
        ↓
penetration-testing (validation)
```

Compliance-focused workflow:

```
compliance-requirements-analysis
        ↓
security-architecture-review
        ↓
gap-analysis
        ↓
remediation-planning
        ↓
compliance-audit
```

## Evaluation Criteria

### Completeness
- Are all system components and data flows reviewed?
- Are all relevant threat categories assessed?
- Are compliance requirements fully evaluated?
- Is a remediation plan provided?

### Accuracy
- Are threats correctly identified and assessed?
- Are severity ratings appropriate?
- Are recommended controls effective against identified threats?
- Are effort estimates realistic?

### Actionability
- Are findings specific and clear?
- Is implementation guidance provided?
- Are priorities well-defined?
- Can the team act on the recommendations?

### Risk-Focused
- Are critical risks prioritized?
- Do recommendations align with risk tolerance?
- Is defense-in-depth considered?
- Are both preventive and detective controls included?