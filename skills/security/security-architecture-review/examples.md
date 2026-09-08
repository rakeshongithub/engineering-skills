# Security Architecture Review - Examples

## Example 1: SaaS Application - SOC 2 Compliance

### Context
- **System**: Multi-tenant SaaS application for project management
- **Users**: 10,000 organizations, 100,000 users
- **Goal**: Achieve SOC 2 Type II certification
- **Current State**: No formal security controls documented

### Attack Surface

**External:**
- Web application (React SPA)
- REST API (Node.js/Express)
- Mobile apps (iOS, Android)
- Third-party integrations (Slack, Google Workspace, GitHub)

**Internal:**
- PostgreSQL database
- Redis cache
- S3 for file storage
- Background job workers (Sidekiq)
- Admin dashboard

**Data Flows:**
- User → CloudFront CDN → API Gateway → Application servers
- Application → PostgreSQL (user data, project data)
- Application → S3 (file uploads)
- Application → Redis (session data, cache)

### Key Findings

#### Critical

**1. Weak Tenant Isolation**
- **Threat**: Information Disclosure, Elevation of Privilege
- **Problem**: Database queries don't consistently filter by tenant_id
- **Impact**: Risk of data leakage between organizations
- **Evidence**: Found 15 queries without tenant_id filter
- **Recommended Control**: 
  - Implement row-level security (RLS) in PostgreSQL
  - Add tenant_id to all queries via ORM middleware
  - Add integration tests to verify isolation
- **Effort**: Large (6-8 weeks)
- **Priority**: Critical

**2. Secrets in Environment Variables**
- **Threat**: Information Disclosure
- **Problem**: API keys and database passwords in plaintext .env files
- **Impact**: Credentials exposed in version control, logs, error messages
- **Evidence**: Found AWS keys, database passwords in .env.example
- **Recommended Control**:
  - Migrate to AWS Secrets Manager
  - Implement automatic secret rotation
  - Remove secrets from version control history
- **Effort**: Medium (2-3 weeks)
- **Priority**: Critical

**3. No Encryption at Rest**
- **Threat**: Information Disclosure
- **Problem**: PostgreSQL and S3 not encrypted
- **Impact**: SOC 2 compliance violation, data breach risk
- **Evidence**: Database encryption disabled, S3 buckets use default settings
- **Recommended Control**:
  - Enable PostgreSQL encryption (AWS RDS encryption)
  - Enable S3 bucket encryption (SSE-S3 or SSE-KMS)
  - Encrypt Redis with encryption in transit and at rest
- **Effort**: Small (1 week)
- **Priority**: Critical

#### High

**4. Missing Audit Logs**
- **Threat**: Repudiation, lack of detective controls
- **Problem**: No comprehensive audit trail for data access and modifications
- **Impact**: Cannot detect or investigate security incidents, SOC 2 requirement
- **Evidence**: Only application logs, no security event logs
- **Recommended Control**:
  - Implement audit logging for all data access and modifications
  - Log: who, what, when, where, result
  - Store logs in immutable storage (S3 with versioning and MFA delete)
  - Retain logs for 1 year minimum
- **Effort**: Medium (3-4 weeks)
- **Priority**: High

**5. Weak Password Policy**
- **Threat**: Spoofing (credential stuffing, brute force)
- **Problem**: No minimum password requirements, no MFA
- **Impact**: Account takeover risk
- **Evidence**: Passwords as short as 6 characters allowed, no complexity requirements
- **Recommended Control**:
  - Enforce minimum 12 characters
  - Require mix of uppercase, lowercase, numbers, symbols
  - Implement MFA (TOTP or SMS)
  - Add rate limiting on login attempts
  - Implement breach password detection (HaveIBeenPwned API)
- **Effort**: Medium (2-3 weeks)
- **Priority**: High

**6. Missing Rate Limiting**
- **Threat**: Denial of Service
- **Problem**: No rate limiting on API endpoints
- **Impact**: Vulnerable to DDoS, resource exhaustion, credential stuffing
- **Evidence**: Unlimited requests allowed per IP or user
- **Recommended Control**:
  - Implement rate limiting at API Gateway
  - Different limits for authenticated vs. unauthenticated requests
  - Per-endpoint limits (stricter for login, signup)
  - Return 429 Too Many Requests with Retry-After header
- **Effort**: Small (1 week)
- **Priority**: High

#### Medium

**7. Missing Security Headers**
- **Threat**: XSS, Clickjacking, MIME sniffing
- **Problem**: Security headers not configured
- **Impact**: Increased attack surface for client-side attacks
- **Evidence**: Missing CSP, X-Frame-Options, X-Content-Type-Options
- **Recommended Control**:
  ```
  Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  Referrer-Policy: strict-origin-when-cross-origin
  ```
- **Effort**: Small (2-3 days)
- **Priority**: Medium

**8. Insufficient Input Validation**
- **Threat**: Injection attacks (SQL, XSS, command injection)
- **Problem**: Inconsistent input validation and sanitization
- **Impact**: Potential for injection attacks
- **Evidence**: Found endpoints accepting unvalidated user input
- **Recommended Control**:
  - Implement input validation library (Joi, Yup)
  - Validate all user input on server side
  - Use parameterized queries (already using ORM, verify all queries)
  - Sanitize output (already using React, verify no dangerouslySetInnerHTML)
- **Effort**: Medium (2-3 weeks)
- **Priority**: Medium

### Compliance Assessment

**SOC 2 Trust Service Criteria:**

**CC6.1 - Logical and Physical Access Controls:**
- ❌ Missing: MFA, comprehensive audit logs
- ✅ Present: Authentication, authorization (needs strengthening)
- **Gap**: Implement MFA and audit logging

**CC6.6 - Encryption:**
- ❌ Missing: Encryption at rest
- ✅ Present: TLS for data in transit
- **Gap**: Enable database and S3 encryption

**CC6.7 - Restricted Access:**
- ❌ Missing: Tenant isolation verification
- ✅ Present: RBAC within tenants
- **Gap**: Implement and test tenant isolation

**CC7.2 - System Monitoring:**
- ❌ Missing: Security event monitoring, anomaly detection
- ✅ Present: Application performance monitoring
- **Gap**: Implement security monitoring and alerting

### Remediation Plan

**Immediate (0-1 month):**
1. Enable encryption at rest (database, S3, Redis) - 1 week
2. Migrate secrets to AWS Secrets Manager - 2 weeks
3. Implement rate limiting - 1 week
4. Add security headers - 2 days

**Short-term (1-3 months):**
1. Implement tenant isolation with RLS - 6-8 weeks
2. Implement comprehensive audit logging - 3-4 weeks
3. Implement MFA and strengthen password policy - 2-3 weeks
4. Improve input validation - 2-3 weeks

**Long-term (3-6 months):**
1. Implement security monitoring and alerting - 4-6 weeks
2. Conduct penetration testing - 2 weeks
3. Implement automated security scanning in CI/CD - 2 weeks
4. Complete SOC 2 audit - 3-4 months

**Total Effort**: ~20-24 weeks (5-6 months)

---

## Example 2: E-commerce Platform - PCI-DSS Compliance

### Context
- **System**: E-commerce platform processing credit card payments
- **Volume**: 50,000 transactions/month
- **Goal**: Achieve PCI-DSS compliance
- **Current State**: Using Stripe for payment processing, storing some card data

### Attack Surface

**External:**
- E-commerce website (Next.js)
- Payment API (Python/Django)
- Mobile app (React Native)
- Stripe integration

**Internal:**
- MySQL database
- Redis cache
- RabbitMQ message queue
- Order processing workers

**Cardholder Data Environment (CDE):**
- Payment API servers
- MySQL database (order data, last 4 digits of card)
- Redis (session data during checkout)

### Key Findings

#### Critical

**1. Storing Full Card Numbers**
- **Threat**: Information Disclosure
- **Problem**: Full card numbers stored in database for "customer convenience"
- **Impact**: PCI-DSS violation, massive liability in case of breach
- **Evidence**: `payment_methods` table contains `card_number` column
- **Recommended Control**:
  - **DO NOT STORE** full card numbers
  - Use Stripe's tokenization (already available)
  - Store only Stripe token and last 4 digits
  - Migrate existing data: tokenize with Stripe, delete card numbers
- **Effort**: Medium (3-4 weeks including data migration)
- **Priority**: Critical - Must fix before processing more transactions

**2. CDE Not Segmented**
- **Threat**: Lateral movement after breach
- **Problem**: Payment API on same network as other services
- **Impact**: PCI-DSS Requirement 1.2 violation, increased blast radius
- **Evidence**: All services in same VPC subnet
- **Recommended Control**:
  - Segment CDE into separate VPC or subnet
  - Implement strict firewall rules (only allow necessary traffic)
  - Use bastion host for administrative access
  - Document network diagram
- **Effort**: Medium (2-3 weeks)
- **Priority**: Critical

**3. No Encryption for Card Data in Transit (Internal)**
- **Threat**: Information Disclosure, Tampering
- **Problem**: Internal service-to-service communication not encrypted
- **Impact**: PCI-DSS Requirement 4.1 violation
- **Evidence**: Payment API → Database uses unencrypted connection
- **Recommended Control**:
  - Enable TLS for all database connections
  - Use TLS for all internal API calls
  - Implement mutual TLS (mTLS) for service-to-service auth
- **Effort**: Small (1-2 weeks)
- **Priority**: Critical

#### High

**4. Weak Access Controls to CDE**
- **Threat**: Elevation of Privilege
- **Problem**: All developers have production database access
- **Impact**: PCI-DSS Requirement 7 violation, insider threat
- **Evidence**: 15 developers have production DB credentials
- **Recommended Control**:
  - Implement principle of least privilege
  - Only 2-3 senior engineers have production access
  - All access via bastion host with MFA
  - All access logged and monitored
  - Quarterly access review
- **Effort**: Small (1 week)
- **Priority**: High

**5. No File Integrity Monitoring**
- **Threat**: Tampering (undetected)
- **Problem**: No monitoring for unauthorized changes to critical files
- **Impact**: PCI-DSS Requirement 11.5 violation
- **Evidence**: No FIM solution in place
- **Recommended Control**:
  - Implement file integrity monitoring (OSSEC, Tripwire, or AWS Systems Manager)
  - Monitor critical files: application code, configuration, system files
  - Alert on unauthorized changes
- **Effort**: Medium (2-3 weeks)
- **Priority**: High

**6. Missing Vulnerability Scanning**
- **Threat**: Exploitation of known vulnerabilities
- **Problem**: No regular vulnerability scanning
- **Impact**: PCI-DSS Requirement 11.2 violation
- **Evidence**: No scanning process documented
- **Recommended Control**:
  - Implement quarterly vulnerability scanning (Qualys, Nessus, or AWS Inspector)
  - Scan all CDE systems
  - Remediate critical and high vulnerabilities within 30 days
  - Use ASV (Approved Scanning Vendor) for external scans
- **Effort**: Small (1 week setup, ongoing process)
- **Priority**: High

### PCI-DSS Compliance Assessment

**Requirement 1: Firewall Configuration**
- ❌ Gap: CDE not segmented
- **Action**: Segment CDE, implement firewall rules

**Requirement 2: No Default Passwords**
- ✅ Compliant: All default passwords changed

**Requirement 3: Protect Stored Cardholder Data**
- ❌ Gap: Storing full card numbers (CRITICAL)
- **Action**: Remove card numbers, use tokenization

**Requirement 4: Encrypt Transmission**
- ❌ Gap: Internal traffic not encrypted
- **Action**: Enable TLS for all connections

**Requirement 5: Anti-Virus**
- ✅ Compliant: Anti-virus on all systems

**Requirement 6: Secure Systems and Applications**
- ❌ Gap: No vulnerability scanning
- **Action**: Implement quarterly scanning

**Requirement 7: Restrict Access**
- ❌ Gap: Too many users with CDE access
- **Action**: Implement least privilege

**Requirement 8: Unique IDs**
- ✅ Compliant: Unique user IDs, MFA for admin access

**Requirement 9: Physical Access**
- ✅ Compliant: Using AWS (inherits AWS physical security)

**Requirement 10: Track and Monitor**
- ❌ Gap: Incomplete audit logging
- **Action**: Implement comprehensive logging

**Requirement 11: Test Security**
- ❌ Gap: No vulnerability scanning, no penetration testing
- **Action**: Implement scanning and annual pen test

**Requirement 12: Security Policy**
- ❌ Gap: No formal security policy
- **Action**: Document security policies and procedures

### Remediation Plan

**URGENT (0-2 weeks):**
1. **STOP storing full card numbers** - Immediate
2. Migrate existing card data to Stripe tokens - 1 week
3. Enable TLS for internal connections - 1 week

**Immediate (2-6 weeks):**
1. Segment CDE network - 2-3 weeks
2. Implement least privilege access - 1 week
3. Implement file integrity monitoring - 2-3 weeks
4. Set up vulnerability scanning - 1 week

**Short-term (6-12 weeks):**
1. Enhance audit logging - 3-4 weeks
2. Document security policies - 2-3 weeks
3. Conduct penetration testing - 2 weeks
4. Complete SAQ (Self-Assessment Questionnaire) - 1 week

**Timeline to Compliance**: 3-4 months

---

## Example 3: Healthcare Platform - HIPAA Compliance

### Context
- **System**: Telemedicine platform connecting patients and doctors
- **Data**: Protected Health Information (PHI) - medical records, prescriptions, video consultations
- **Goal**: Ensure HIPAA compliance before launch
- **Current State**: MVP built, no security review conducted

### Attack Surface

**External:**
- Patient portal (React)
- Doctor portal (React)
- Mobile apps (iOS, Android)
- REST API (Node.js)
- WebRTC video service

**Internal:**
- PostgreSQL database (patient records, appointments)
- MongoDB (chat messages)
- S3 (medical documents, images)
- Twilio (video calls)
- SendGrid (email notifications)

**PHI Data:**
- Patient demographics, medical history, prescriptions
- Doctor notes, diagnoses
- Chat messages between patient and doctor
- Video call recordings (if enabled)
- Medical documents and images

### Key Findings

#### Critical

**1. PHI Accessible Without Authentication**
- **Threat**: Information Disclosure
- **Problem**: Medical document URLs in S3 are public (signed URLs with 7-day expiration)
- **Impact**: HIPAA violation, patient privacy breach
- **Evidence**: S3 bucket policy allows public read, URLs guessable
- **Recommended Control**:
  - Make S3 bucket private
  - Generate short-lived signed URLs (5-minute expiration)
  - Verify user authorization before generating URL
  - Add CloudFront with signed cookies for additional security
- **Effort**: Small (3-5 days)
- **Priority**: Critical

**2. No Encryption at Rest for PHI**
- **Threat**: Information Disclosure
- **Problem**: Database and S3 not encrypted
- **Impact**: HIPAA Security Rule violation (§164.312(a)(2)(iv))
- **Evidence**: PostgreSQL encryption disabled, S3 default encryption not enabled
- **Recommended Control**:
  - Enable PostgreSQL encryption (AWS RDS encryption with KMS)
  - Enable S3 bucket encryption (SSE-KMS)
  - Enable MongoDB encryption at rest
  - Document encryption key management procedures
- **Effort**: Small (1 week)
- **Priority**: Critical

**3. Missing Business Associate Agreements (BAAs)**
- **Threat**: Compliance violation
- **Problem**: No BAAs signed with third-party services (Twilio, SendGrid, AWS)
- **Impact**: HIPAA violation, legal liability
- **Evidence**: No BAAs on file
- **Recommended Control**:
  - Sign BAA with AWS
  - Sign BAA with Twilio (verify HIPAA-eligible service tier)
  - Sign BAA with SendGrid or switch to HIPAA-compliant email service
  - Document all third-party services handling PHI
  - Ensure all BAAs are in place before processing PHI
- **Effort**: Small (1-2 weeks, mostly administrative)
- **Priority**: Critical

#### High

**4. Insufficient Audit Logging**
- **Threat**: Repudiation, lack of accountability
- **Problem**: No comprehensive audit trail for PHI access
- **Impact**: HIPAA Security Rule violation (§164.312(b))
- **Evidence**: Only application logs, no security audit logs
- **Recommended Control**:
  - Log all PHI access: who accessed what PHI, when, from where
  - Log all PHI modifications: who changed what, when, old value, new value
  - Log authentication events: login, logout, failed attempts
  - Log administrative actions: user creation, permission changes
  - Store logs in tamper-proof storage (S3 with versioning, MFA delete)
  - Retain logs for 6 years (HIPAA requirement)
- **Effort**: Medium (4-6 weeks)
- **Priority**: High

**5. No Automatic Logout**
- **Threat**: Unauthorized access via unattended session
- **Problem**: Sessions never expire, no automatic logout
- **Impact**: HIPAA Security Rule violation (§164.312(a)(2)(iii))
- **Evidence**: JWT tokens valid for 30 days
- **Recommended Control**:
  - Implement automatic logout after 15 minutes of inactivity
  - Reduce JWT expiration to 1 hour
  - Implement refresh token mechanism
  - Show warning before logout ("You will be logged out in 1 minute")
- **Effort**: Small (1 week)
- **Priority**: High

**6. Video Call Recordings Not Encrypted**
- **Threat**: Information Disclosure
- **Problem**: Twilio video recordings stored unencrypted
- **Impact**: HIPAA violation if recordings contain PHI
- **Evidence**: Twilio default settings, no encryption configured
- **Recommended Control**:
  - Enable Twilio video encryption
  - Encrypt recordings at rest
  - Implement strict access controls for recordings
  - Auto-delete recordings after 30 days (or per retention policy)
  - Obtain patient consent before recording
- **Effort**: Small (1 week)
- **Priority**: High

### HIPAA Compliance Assessment

**Administrative Safeguards:**
- ❌ Gap: No security officer designated
- ❌ Gap: No workforce training on HIPAA
- ❌ Gap: No incident response plan
- **Action**: Designate security officer, implement training, create IR plan

**Physical Safeguards:**
- ✅ Compliant: Using AWS (inherits AWS physical security)
- ❌ Gap: No workstation security policy
- **Action**: Document workstation security requirements

**Technical Safeguards:**
- ❌ Gap: PHI accessible without authentication (S3)
- ❌ Gap: No encryption at rest
- ❌ Gap: Insufficient audit logging
- ❌ Gap: No automatic logout
- **Action**: Implement all technical controls listed above

**Organizational Requirements:**
- ❌ Gap: No BAAs with third parties
- **Action**: Sign BAAs with all vendors handling PHI

### Remediation Plan

**URGENT (0-1 week):**
1. Make S3 bucket private, implement proper access controls - 3 days
2. Sign BAAs with AWS, Twilio, SendGrid - 1 week (administrative)

**Immediate (1-4 weeks):**
1. Enable encryption at rest (database, S3, MongoDB) - 1 week
2. Implement automatic logout - 1 week
3. Encrypt video call recordings - 1 week
4. Implement audit logging - 4 weeks

**Short-term (1-3 months):**
1. Designate security officer - Immediate
2. Implement HIPAA training program - 2-3 weeks
3. Create incident response plan - 2-3 weeks
4. Document security policies and procedures - 4-6 weeks
5. Conduct risk assessment - 2-3 weeks
6. Implement additional technical controls - 4-6 weeks

**Long-term (3-6 months):**
1. Conduct HIPAA compliance audit - 2-3 months
2. Implement ongoing compliance monitoring - Ongoing
3. Annual security training - Annually

**Timeline to Compliance**: 4-6 months

**Critical Path**: BAAs must be signed before processing any PHI (1 week)

---

## Example 4: API Platform - General Security Review

### Context
- **System**: Public API platform for developers (similar to Stripe, Twilio)
- **Users**: 5,000 developers, 500,000 end users
- **Goal**: Security review before Series A funding
- **Current State**: Rapid growth, security not prioritized

### Key Findings

#### Critical

**1. API Keys in URLs**
- **Threat**: Information Disclosure
- **Problem**: API keys passed as query parameters instead of headers
- **Impact**: Keys logged in server logs, browser history, proxy logs
- **Evidence**: Documentation shows `GET /api/users?api_key=sk_live_...`
- **Recommended Control**:
  - Require API keys in `Authorization` header
  - Deprecate query parameter authentication
  - Rotate all existing API keys
  - Notify developers of change with 90-day migration period
- **Effort**: Medium (4-6 weeks including developer migration)
- **Priority**: Critical

**2. No API Key Rotation**
- **Threat**: Prolonged exposure after compromise
- **Problem**: API keys never expire, no rotation mechanism
- **Impact**: Compromised keys remain valid indefinitely
- **Evidence**: Keys created 2 years ago still active
- **Recommended Control**:
  - Implement API key expiration (1 year)
  - Provide key rotation mechanism in dashboard
  - Send expiration warnings (90, 30, 7 days before)
  - Allow multiple active keys during rotation
- **Effort**: Medium (3-4 weeks)
- **Priority**: Critical

#### High

**3. Insufficient Rate Limiting**
- **Threat**: Denial of Service, abuse
- **Problem**: Rate limits too high, easily bypassed
- **Impact**: API abuse, resource exhaustion, unfair usage
- **Evidence**: 10,000 requests/minute per key (too generous)
- **Recommended Control**:
  - Implement tiered rate limiting based on plan
  - Free tier: 100 req/min
  - Paid tier: 1,000 req/min
  - Enterprise: Custom limits
  - Implement burst limits (max 50 req/sec)
  - Return clear rate limit headers (X-RateLimit-Limit, X-RateLimit-Remaining)
- **Effort**: Small (1-2 weeks)
- **Priority**: High

**4. Webhook Signature Not Verified**
- **Threat**: Spoofing, Tampering
- **Problem**: Documentation doesn't emphasize webhook signature verification
- **Impact**: Attackers can send fake webhooks to customer endpoints
- **Evidence**: Only 20% of customers verify signatures
- **Recommended Control**:
  - Improve documentation with code examples
  - Add webhook signature verification to SDKs
  - Send security advisory to all customers
  - Consider making verification mandatory in next API version
- **Effort**: Small (1-2 weeks)
- **Priority**: High

**5. No IP Whitelisting for Webhooks**
- **Threat**: Spoofing
- **Problem**: Customers cannot restrict webhooks to platform IPs
- **Impact**: Harder for customers to secure webhook endpoints
- **Evidence**: Feature requested by enterprise customers
- **Recommended Control**:
  - Document static IP ranges for webhooks
  - Use dedicated IP addresses for webhook delivery
  - Publish IP ranges in documentation and via API
  - Notify customers 30 days before IP changes
- **Effort**: Medium (2-3 weeks)
- **Priority**: High

### Remediation Plan

**Immediate (0-2 months):**
1. Implement API key rotation mechanism - 3-4 weeks
2. Improve rate limiting - 1-2 weeks
3. Improve webhook security documentation - 1 week
4. Implement IP whitelisting for webhooks - 2-3 weeks

**Short-term (2-4 months):**
1. Migrate API keys from URLs to headers - 4-6 weeks
2. Rotate all existing API keys - 90-day migration period
3. Implement webhook signature verification in SDKs - 2-3 weeks

**Long-term (4-6 months):**
1. API v2 with mandatory webhook verification - 3-4 months
2. Security audit and penetration testing - 1 month
3. Bug bounty program - Ongoing

**Total Effort**: ~4-6 months to address all findings