# Security Architecture Review - Step-by-Step Instructions

## Overview

This skill helps you systematically evaluate architecture from a security perspective to identify threats, vulnerabilities, and required controls.

## Step-by-Step Workflow

### Step 1: Understand Security Context (30-60 minutes)

**Actions:**
1. Define security objectives (confidentiality, integrity, availability)
2. Identify assets to protect (user data, business data, system resources)
3. Understand threat landscape (threat actors, motivations, attack vectors)
4. Review compliance requirements (GDPR, HIPAA, PCI-DSS, SOC 2)
5. Understand organization's risk tolerance
6. Review any existing threat models or security assessments

**Outputs:**
- Security context document summarizing objectives, assets, threats, and compliance requirements

### Step 2: Map Attack Surface (1-2 hours)

**Actions:**
1. **External attack surface:**
   - List all public APIs and endpoints
   - Identify web applications and mobile apps
   - Document authentication and authorization mechanisms
   - List third-party integrations
   - Review DNS and domain infrastructure

2. **Internal attack surface:**
   - Map internal APIs and services
   - Identify databases and data stores
   - Document message queues and event buses
   - List admin interfaces and tools
   - Review CI/CD pipelines

3. **Data flows:**
   - Map data in transit (client-server, service-service)
   - Map data at rest (databases, file storage, backups)
   - Document data processing flows
   - Identify data retention and deletion processes

4. **Trust boundaries:**
   - Identify network boundaries (Internet, DMZ, internal)
   - Document service-to-service trust relationships
   - Map user-to-application trust boundaries

**Outputs:**
- Attack surface map with all entry points, data flows, and trust boundaries

### Step 3: Identify Threats Using STRIDE (2-3 hours)

**Actions:**
1. For each component and data flow, assess:
   - **Spoofing**: Can an attacker impersonate a user or service?
   - **Tampering**: Can data be modified in transit or at rest?
   - **Repudiation**: Can users deny actions they performed?
   - **Information Disclosure**: Can sensitive data be accessed without authorization?
   - **Denial of Service**: Can the system be overwhelmed?
   - **Elevation of Privilege**: Can users gain unauthorized access?

2. Document each identified threat:
   - Threat description
   - Affected component or data flow
   - Attack scenario
   - Potential impact
   - Likelihood

**Outputs:**
- Comprehensive threat list categorized by STRIDE

### Step 4: Assess Existing Controls (2-3 hours)

**Actions:**
1. **Authentication:**
   - Review MFA implementation
   - Check password policies and storage (hashing, salting)
   - Assess session management
   - Review OAuth/OIDC implementation
   - Check API key management

2. **Authorization:**
   - Review RBAC or ABAC implementation
   - Verify principle of least privilege
   - Check authorization at every layer

3. **Data Protection:**
   - Verify encryption in transit (TLS 1.2+)
   - Verify encryption at rest
   - Review key management
   - Check data masking and tokenization
   - Review secure deletion processes

4. **Network Security:**
   - Review firewall rules and security groups
   - Check network segmentation
   - Assess VPN configuration
   - Review DDoS protection
   - Check WAF configuration

5. **Application Security:**
   - Review input validation
   - Check output encoding
   - Verify CSRF protection
   - Check XSS prevention
   - Review SQL injection prevention
   - Check dependency scanning

6. **Monitoring and Logging:**
   - Review security event logging
   - Check audit trail completeness
   - Assess anomaly detection
   - Review alerting configuration
   - Check log retention and protection

7. **Incident Response:**
   - Review incident response plan
   - Check backup and recovery procedures
   - Review communication protocols
   - Assess forensics capabilities

**Outputs:**
- Security controls inventory with assessment of effectiveness

### Step 5: Identify Gaps and Vulnerabilities (1-2 hours)

**Actions:**
1. Compare identified threats against existing controls
2. Identify gaps where threats are not adequately mitigated
3. Document vulnerabilities in existing controls
4. Categorize findings by severity:
   - **Critical**: Immediate risk of data breach or system compromise
   - **High**: Significant security risk
   - **Medium**: Moderate security risk
   - **Low**: Minor security improvement
5. Gather supporting evidence (configuration examples, code snippets, logs)

**Outputs:**
- Categorized list of security gaps and vulnerabilities with evidence

### Step 6: Assess Compliance (1-2 hours)

**Actions:**
1. For each applicable regulation (GDPR, HIPAA, PCI-DSS, SOC 2):
   - Review specific requirements
   - Assess current compliance status
   - Identify gaps
   - Document required controls

2. Create compliance gap analysis:
   - Requirement
   - Current state
   - Gap
   - Required action
   - Priority

**Outputs:**
- Compliance gap analysis for each applicable regulation

### Step 7: Develop Remediation Plan (2-4 hours)

**Actions:**
1. For each finding, document:
   - **Problem Statement**: What is the vulnerability or gap?
   - **Threat**: What threat does it enable?
   - **Impact**: What is the potential impact?
   - **Recommended Control**: What should be implemented?
   - **Implementation Guidance**: Specific steps, technologies, configuration
   - **Effort Estimate**: Small (days), Medium (weeks), Large (months)
   - **Priority**: Critical, High, Medium, Low

2. Group recommendations by timeline:
   - Immediate (0-1 month): Critical fixes
   - Short-term (1-3 months): High-priority improvements
   - Long-term (3-12 months): Medium and low-priority enhancements

3. Identify dependencies between recommendations
4. Estimate total effort and resource requirements

**Outputs:**
- Detailed remediation plan with prioritized, actionable recommendations

### Step 8: Document and Present (2-3 hours)

**Actions:**
1. **Executive Summary** (1 page):
   - Overall security posture (Red/Yellow/Green)
   - Top 3-5 critical risks
   - Compliance status
   - Recommended immediate actions

2. **Threat Assessment**:
   - Identified threats with likelihood and impact
   - Attack scenarios
   - Risk matrix

3. **Detailed Findings**:
   - All vulnerabilities and gaps
   - Severity ratings
   - Supporting evidence
   - Recommended controls

4. **Remediation Roadmap**:
   - Timeline-based action plan
   - Effort estimates
   - Resource requirements

5. **Compliance Assessment**:
   - Gaps against each regulation
   - Required controls
   - Timeline to compliance

6. Review with security and engineering teams for validation
7. Prepare presentation for stakeholders

**Outputs:**
- Complete security architecture review report
- Presentation deck
- Remediation tracking document

## Tips for Success

- **Think like an attacker**: Consider how you would exploit the system
- **Use structured threat modeling**: STRIDE provides comprehensive coverage
- **Focus on high-impact risks**: Not all vulnerabilities are equally important
- **Be specific**: Vague recommendations like "improve security" are not helpful
- **Consider defense-in-depth**: Multiple layers of security are better than one
- **Balance security and usability**: Controls should not make the system unusable
- **Validate findings**: Discuss with the team to avoid false positives
- **Provide implementation guidance**: Help the team understand how to fix issues
- **Track remediation**: Follow up to ensure fixes are implemented
- **Re-assess after changes**: Verify that controls work as intended

## Common Pitfalls to Avoid

- Following a checklist without understanding actual threats
- Recommending controls that don't align with risk tolerance
- Over-focusing on compliance while missing real security risks
- Ignoring people and process controls (focusing only on technology)
- Not threat modeling before identifying vulnerabilities
- Making vague recommendations without specific controls
- Implementing controls that make the system unusable
- Treating all findings equally instead of prioritizing
- Relying on a single control instead of defense-in-depth
- Not validating that controls actually work
- Ignoring insider threats
- Not tracking remediation or re-assessing after changes

## Validation Checklist

- [ ] All system components and data flows are mapped
- [ ] Attack surface is comprehensively identified
- [ ] Threats are assessed using STRIDE or similar model
- [ ] Existing controls are documented and evaluated
- [ ] Gaps and vulnerabilities are identified with severity ratings
- [ ] Compliance requirements are assessed
- [ ] Recommendations are specific and actionable
- [ ] Implementation guidance is provided
- [ ] Effort estimates and priorities are assigned
- [ ] Remediation plan is realistic and time-bound
- [ ] Findings are validated with security and engineering teams
- [ ] Executive summary highlights critical risks