# CI/CD Design - Step-by-Step Instructions

This guide provides detailed instructions for designing continuous integration and continuous deployment (CI/CD) pipelines that automate building, testing, security scanning, and deployment of software.

**Estimated Time:** 4-8 hours  
**Complexity:** Advanced  
**Prerequisites:** Version control, testing strategy, architecture understanding

---

## Overview

CI/CD design involves creating automated pipelines that transform code changes into production deployments safely and reliably. This skill covers the complete pipeline design process from requirements gathering to implementation and documentation.

**Key Outcomes:**
- Automated build, test, and deployment pipelines
- Fast feedback loops (< 15 minutes)
- Integrated security scanning
- Reliable rollback mechanisms
- Clear deployment strategy

---

## Step 1: Define CI/CD Requirements (30-60 minutes)

### Objective

Understand what needs to be automated and why, establishing clear goals for the CI/CD system.

### Actions

#### 1.1 Define Deployment Frequency Goals

**Questions to Answer:**
- How often do we want to deploy? (multiple times per day, daily, weekly, on-demand)
- What's our current deployment frequency?
- What's blocking more frequent deployments?

**Document:**
```markdown
## Deployment Frequency Goals

**Current State:** Weekly manual deployments
**Target State:** Multiple deployments per day (automated)
**Blockers:** Manual testing, manual approvals, no rollback strategy
```

#### 1.2 Identify Manual Deployment Pain Points

**Common Pain Points:**
- Manual steps that are error-prone
- Long deployment times (> 1 hour)
- Frequent deployment failures
- Difficult rollbacks
- Lack of visibility into deployment status
- Environment configuration drift

**Template:**
```markdown
## Current Deployment Pain Points

1. **Manual database migration** — Error-prone, takes 30 minutes
2. **Manual configuration updates** — Frequently forgotten, causes failures
3. **No automated rollback** — Rollback takes 2+ hours
4. **No deployment visibility** — Don't know if deployment succeeded until users report issues
```

#### 1.3 Define Quality Gates

**Quality Gates to Consider:**
- **Testing:** Unit tests pass (> 80% coverage), integration tests pass, e2e tests pass
- **Security:** No critical vulnerabilities, no hardcoded secrets, dependencies up to date
- **Performance:** Build time < 10 min, deployment time < 15 min
- **Code Quality:** Linting passes, code review approved

**Template:**
```markdown
## Quality Gates

### Must Pass (Block Deployment)
- [ ] All unit tests pass (> 80% coverage)
- [ ] All integration tests pass
- [ ] No critical security vulnerabilities (SAST, dependency scan)
- [ ] No hardcoded secrets detected
- [ ] Code review approved

### Should Pass (Warn)
- [ ] E2E tests pass (can be flaky)
- [ ] No high-severity security vulnerabilities
- [ ] Performance benchmarks within acceptable range
```

#### 1.4 Establish Deployment Approval Requirements

**Approval Strategies:**
- **Fully automated:** No manual approval (use for dev, staging)
- **Time-based windows:** Deploy only during business hours
- **Manual approval:** Require human approval (use for production)
- **Automated with monitoring:** Deploy automatically, rollback if metrics degrade

**Template:**
```markdown
## Deployment Approvals

**Development:** Fully automated (no approval)
**Staging:** Fully automated (no approval)
**Production:** Manual approval required OR automated during business hours (9am-5pm)
```

#### 1.5 Define Rollback Requirements

**Rollback Considerations:**
- **Time to rollback:** How quickly can we rollback? (< 5 min, < 15 min, < 1 hour)
- **Rollback automation:** Automated or manual?
- **Database rollback:** How to handle database migrations?
- **Rollback testing:** Do we test rollback regularly?

**Template:**
```markdown
## Rollback Requirements

**Time to Rollback:** < 5 minutes (automated)
**Rollback Method:** Automated rollback to previous version
**Database Rollback:** Use backward-compatible migrations (no rollback needed)
**Rollback Testing:** Test rollback monthly in staging
```

#### 1.6 Identify Compliance Requirements

**Common Compliance Requirements:**
- **SOC 2:** Audit logs, access control, change tracking
- **HIPAA:** Data encryption, access logs, audit trails
- **PCI DSS:** Security scanning, vulnerability management
- **GDPR:** Data privacy, consent management

**Template:**
```markdown
## Compliance Requirements

**SOC 2:**
- [ ] Audit logs for all deployments
- [ ] Access control (RBAC) for production deployments
- [ ] Change tracking (who deployed what, when)

**PCI DSS:**
- [ ] Security scanning (SAST, DAST, dependency)
- [ ] Vulnerability remediation process
```

### Quality Checklist

- [ ] Deployment frequency goals defined and documented
- [ ] Manual deployment pain points identified (at least 3)
- [ ] Quality gates established (must pass vs. should pass)
- [ ] Approval process documented for each environment
- [ ] Rollback requirements clear (time, automation, testing)
- [ ] Compliance requirements identified and documented
- [ ] Requirements reviewed with team and stakeholders

### Common Mistakes

❌ **Setting unrealistic deployment frequency goals** — Don't aim for 10 deployments/day if you currently deploy monthly  
✅ **Set incremental goals** — Weekly → daily → multiple per day

❌ **Ignoring compliance requirements** — Leads to failed audits  
✅ **Identify compliance early** — Build compliance into pipeline from start

❌ **No rollback strategy** — Leads to prolonged outages  
✅ **Design rollback first** — Test rollback before first deployment

---

## Step 2: Design Build Strategy (45-90 minutes)

### Objective

Define how code is built, packaged, and versioned in the CI pipeline.

### Actions

#### 2.1 Choose Build Tools

**By Language/Framework:**
- **JavaScript/TypeScript:** npm, yarn, pnpm, webpack, vite
- **Java:** Maven, Gradle
- **Python:** pip, poetry, setuptools
- **Go:** go build
- **C#:** MSBuild, dotnet build
- **Docker:** Multi-stage Docker builds

**Decision Criteria:**
- Team familiarity
- Build speed
- Caching support
- Ecosystem support

**Example:**
```markdown
## Build Tools

**Primary:** npm (team familiar, good caching)
**Docker:** Multi-stage builds (smaller images)
**Rationale:** Team already uses npm, good caching support, fast builds
```

#### 2.2 Define Build Triggers

**Common Triggers:**
- **Push to branch:** Trigger on every commit to main/develop
- **Pull request:** Trigger on PR open/update
- **Tag:** Trigger on version tag (v1.2.3)
- **Schedule:** Nightly builds
- **Manual:** On-demand builds

**Best Practices:**
- Build on every commit to main
- Build on every PR (prevent breaking main)
- Tag-based builds for releases

**Example:**
```yaml
# GitHub Actions example
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  release:
    types: [published]
```

#### 2.3 Design Artifact Strategy

**Artifact Types:**
- **Docker images:** For containerized applications
- **JAR/WAR files:** For Java applications
- **npm packages:** For JavaScript libraries
- **Binaries:** For compiled languages (Go, Rust, C++)
- **Static assets:** For frontend applications

**Artifact Storage:**
- **Docker Registry:** Docker Hub, ECR, GCR, ACR
- **Artifact Repository:** Artifactory, Nexus, npm registry
- **Cloud Storage:** S3, GCS, Azure Blob

**Example:**
```markdown
## Artifact Strategy

**Type:** Docker images
**Registry:** AWS ECR (private)
**Naming:** myapp:${GIT_SHA} (immutable tags)
**Retention:** Keep last 30 images, delete older
```

#### 2.4 Establish Versioning Strategy

**Versioning Schemes:**
- **Semantic Versioning:** v1.2.3 (major.minor.patch)
- **Git SHA:** Use commit hash (immutable, traceable)
- **Date-based:** 2024-01-15-abc123
- **Build number:** 1.2.3-build.456

**Best Practices:**
- Use Git SHA for Docker images (immutable)
- Use semantic versioning for releases
- Tag production deployments

**Example:**
```markdown
## Versioning Strategy

**Docker Images:** myapp:${GIT_SHA} (e.g., myapp:a1b2c3d)
**Releases:** Semantic versioning (v1.2.3)
**Production Tags:** myapp:${GIT_SHA} + myapp:v1.2.3 + myapp:latest
```

#### 2.5 Define Build Caching Strategy

**Caching Opportunities:**
- **Dependencies:** npm node_modules, Maven .m2, pip cache
- **Build outputs:** Compiled code, transpiled files
- **Docker layers:** Multi-stage builds, layer caching

**Best Practices:**
- Cache dependencies (biggest time saver)
- Use cache keys based on lock files (package-lock.json, pom.xml)
- Invalidate cache when dependencies change

**Example:**
```yaml
# GitHub Actions caching example
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

#### 2.6 Plan Build Matrix (if needed)

**When to Use Build Matrix:**
- Multiple OS support (Linux, macOS, Windows)
- Multiple language versions (Node 16, 18, 20)
- Multiple database versions (PostgreSQL 13, 14, 15)

**Example:**
```yaml
# GitHub Actions matrix example
strategy:
  matrix:
    os: [ubuntu-latest, macos-latest, windows-latest]
    node-version: [16, 18, 20]
runs-on: ${{ matrix.os }}
steps:
  - uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node-version }}
```

### Quality Checklist

- [ ] Build tools selected and documented
- [ ] Build triggers defined (push, PR, tag, schedule)
- [ ] Artifact type and storage location defined
- [ ] Versioning strategy established (Git SHA, semantic versioning)
- [ ] Caching strategy planned (dependencies, build outputs)
- [ ] Build matrix defined (if needed for multi-OS/version support)
- [ ] Build time estimated (target < 10 minutes)

### Common Mistakes

❌ **No build caching** — Builds take 20+ minutes  
✅ **Cache dependencies** — Reduce build time to < 5 minutes

❌ **Mutable artifact tags** — Using "latest" tag, can't trace deployments  
✅ **Immutable tags** — Use Git SHA, always traceable

❌ **Building on every branch** — Wastes CI resources  
✅ **Build on main and PRs only** — Efficient resource usage

---

## Step 3: Design Testing Strategy (60-90 minutes)

### Objective

Define automated testing in the CI/CD pipeline to ensure code quality and prevent regressions.

### Actions

#### 3.1 Identify Test Types

**Test Pyramid:**
```
       /\        E2E Tests (few, slow, expensive)
      /  \       
     /____\      Integration Tests (some, medium speed)
    /      \     
   /________\    Unit Tests (many, fast, cheap)
```

**Test Types:**
- **Unit Tests:** Test individual functions/classes (fast, 1-5 min)
- **Integration Tests:** Test component interactions (medium, 5-15 min)
- **E2E Tests:** Test full user flows (slow, 15-30 min)
- **Performance Tests:** Test response times, throughput (varies)
- **Security Tests:** SAST, DAST, dependency scanning (5-15 min)

**Example:**
```markdown
## Test Types

**Unit Tests:** Jest, 2,500 tests, ~3 minutes
**Integration Tests:** Supertest, 150 tests, ~8 minutes
**E2E Tests:** Playwright, 30 tests, ~12 minutes
**Total Test Time:** ~23 minutes
```

#### 3.2 Define Test Execution Order

**Best Practice: Fail Fast**
- Run fast tests first (unit tests)
- Run slow tests later (e2e tests)
- Stop pipeline on first failure (save time)

**Example Pipeline:**
```
1. Lint (1 min) → FAIL FAST
2. Unit Tests (3 min) → FAIL FAST
3. Build (5 min)
4. Integration Tests (8 min) → FAIL FAST
5. E2E Tests (12 min) → FAIL FAST
6. Security Scan (10 min)
```

#### 3.3 Establish Test Coverage Requirements

**Coverage Targets:**
- **Excellent:** > 80% coverage
- **Good:** 60-80% coverage
- **Needs Improvement:** < 60% coverage

**Coverage Types:**
- **Line coverage:** Percentage of lines executed
- **Branch coverage:** Percentage of branches executed
- **Function coverage:** Percentage of functions called

**Best Practices:**
- Set minimum coverage threshold (e.g., 80%)
- Block PRs that decrease coverage
- Focus on critical paths (auth, payment, data processing)

**Example:**
```yaml
# Jest coverage configuration
"jest": {
  "coverageThreshold": {
    "global": {
      "branches": 80,
      "functions": 80,
      "lines": 80,
      "statements": 80
    }
  }
}
```

#### 3.4 Design Test Parallelization

**Parallelization Strategies:**
- **Test file parallelization:** Run test files in parallel
- **Test suite parallelization:** Run different test suites in parallel (unit, integration)
- **Sharding:** Split tests across multiple machines

**Example:**
```yaml
# GitHub Actions parallel jobs
jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - run: npm run test:unit
  
  integration-tests:
    runs-on: ubuntu-latest
    steps:
      - run: npm run test:integration
  
  e2e-tests:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        shard: [1, 2, 3, 4]
    steps:
      - run: npm run test:e2e -- --shard=${{ matrix.shard }}/4
```

#### 3.5 Plan Test Environment Provisioning

**Test Environment Needs:**
- **Database:** PostgreSQL, MySQL, MongoDB
- **Cache:** Redis, Memcached
- **Message Queue:** RabbitMQ, Kafka
- **External Services:** Mock APIs, test accounts

**Provisioning Options:**
- **Docker Compose:** Local development, CI
- **Testcontainers:** Programmatic container management
- **Cloud Services:** AWS RDS, managed services

**Example:**
```yaml
# GitHub Actions with services
services:
  postgres:
    image: postgres:14
    env:
      POSTGRES_PASSWORD: test
    options: >-
      --health-cmd pg_isready
      --health-interval 10s
      --health-timeout 5s
      --health-retries 5
  redis:
    image: redis:7
```

#### 3.6 Define Test Failure Handling

**Failure Strategies:**
- **Fail fast:** Stop pipeline on first failure (save time)
- **Continue on error:** Run all tests, report all failures
- **Retry flaky tests:** Retry failed tests (e2e tests often flaky)
- **Quarantine flaky tests:** Disable flaky tests, fix separately

**Best Practices:**
- Fail fast for unit/integration tests (should be reliable)
- Retry e2e tests (often flaky due to timing)
- Track flaky tests, fix or disable

**Example:**
```yaml
# Retry flaky e2e tests
- name: E2E Tests
  run: npm run test:e2e
  continue-on-error: false
  timeout-minutes: 30
  # Retry up to 3 times
  uses: nick-invision/retry@v2
  with:
    timeout_minutes: 30
    max_attempts: 3
    command: npm run test:e2e
```

### Quality Checklist

- [ ] Test types identified (unit, integration, e2e, performance, security)
- [ ] Test execution order defined (fail fast strategy)
- [ ] Test coverage requirements set (> 80% target)
- [ ] Test parallelization planned (reduce total time)
- [ ] Test environment provisioning designed (Docker, cloud services)
- [ ] Test failure handling defined (fail fast, retry, quarantine)
- [ ] Total test time estimated (target < 15 minutes)

### Common Mistakes

❌ **Running slow tests first** — Wastes time waiting for feedback  
✅ **Fail fast** — Run unit tests first, stop on failure

❌ **No test parallelization** — Tests take 30+ minutes  
✅ **Parallelize tests** — Reduce to < 15 minutes

❌ **Ignoring flaky tests** — Pipeline fails randomly  
✅ **Track and fix flaky tests** — Reliable pipeline

---

## Step 4: Design Security Scanning Strategy (45-90 minutes)

### Objective

Integrate security scanning into the pipeline to detect vulnerabilities early.

### Actions

#### 4.1 Choose SAST Tools (Static Application Security Testing)

**SAST Tools:**
- **SonarQube:** Code quality and security (self-hosted or cloud)
- **Snyk Code:** Developer-friendly, IDE integration
- **Checkmarx:** Enterprise-grade, comprehensive
- **Semgrep:** Open-source, customizable rules

**Selection Criteria:**
- Language support
- False positive rate
- Integration with CI/CD
- Cost

**Example:**
```markdown
## SAST Tool Selection

**Tool:** SonarQube Cloud
**Languages:** JavaScript, TypeScript, Java, Python
**Integration:** GitHub Actions, PR decoration
**Cost:** $10/month per developer
**Rationale:** Good language support, low false positives, team familiar
```

#### 4.2 Choose Dependency Scanning Tools

**Dependency Scanning Tools:**
- **Dependabot:** GitHub native, automatic PRs
- **Snyk:** Comprehensive, fix suggestions
- **OWASP Dependency-Check:** Open-source, CLI-based
- **npm audit:** Built-in for npm projects

**Best Practices:**
- Scan on every build
- Auto-fix low-risk vulnerabilities
- Alert on critical vulnerabilities

**Example:**
```yaml
# GitHub Actions with Snyk
- name: Dependency Scan
  run: npx snyk test --severity-threshold=high
```

#### 4.3 Choose Container Scanning Tools

**Container Scanning Tools:**
- **Trivy:** Fast, comprehensive, open-source
- **Snyk Container:** Developer-friendly
- **Clair:** Open-source, API-based
- **Docker Scan:** Built-in to Docker CLI

**What They Scan:**
- OS vulnerabilities (base image)
- Application dependencies
- Misconfigurations

**Example:**
```yaml
# Trivy container scan
- name: Container Scan
  run: |
    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
      aquasec/trivy image myapp:${{ github.sha }}
```

#### 4.4 Define Security Gate Thresholds

**Severity Levels:**
- **Critical:** Block deployment, immediate fix required
- **High:** Block deployment, fix within 7 days
- **Medium:** Warn, fix within 30 days
- **Low:** Informational, fix when convenient

**Example Policy:**
```markdown
## Security Gate Thresholds

**Block Deployment:**
- Critical vulnerabilities (CVSS >= 9.0)
- High vulnerabilities in production dependencies (CVSS >= 7.0)

**Warn (Don't Block):**
- High vulnerabilities in dev dependencies
- Medium vulnerabilities (CVSS 4.0-6.9)

**Informational:**
- Low vulnerabilities (CVSS < 4.0)
```

#### 4.5 Plan Secrets Scanning

**Secrets Scanning Tools:**
- **git-secrets:** Prevent committing secrets
- **TruffleHog:** Find secrets in Git history
- **GitHub Secret Scanning:** Automatic for public repos
- **GitGuardian:** Comprehensive, real-time alerts

**What to Scan For:**
- API keys
- Database passwords
- Private keys
- OAuth tokens
- AWS access keys

**Example:**
```yaml
# TruffleHog scan
- name: Secrets Scan
  run: |
    docker run --rm -v "$PWD:/pwd" \
      trufflesecurity/trufflehog:latest \
      filesystem /pwd --fail
```

#### 4.6 Establish Vulnerability Remediation Process

**Remediation Workflow:**
1. **Detection:** Security scan finds vulnerability
2. **Triage:** Assess severity, impact, exploitability
3. **Prioritization:** Critical → High → Medium → Low
4. **Remediation:** Update dependency, patch, workaround
5. **Verification:** Re-scan, verify fix
6. **Documentation:** Document in security log

**SLA by Severity:**
- **Critical:** Fix within 24 hours
- **High:** Fix within 7 days
- **Medium:** Fix within 30 days
- **Low:** Fix within 90 days

**Example:**
```markdown
## Vulnerability Remediation SLA

**Critical (CVSS >= 9.0):**
- Triage: Within 2 hours
- Fix: Within 24 hours
- Owner: Security team + on-call engineer

**High (CVSS 7.0-8.9):**
- Triage: Within 24 hours
- Fix: Within 7 days
- Owner: Engineering team lead
```

### Quality Checklist

- [ ] SAST tool selected and configured
- [ ] Dependency scanning enabled (Dependabot, Snyk, npm audit)
- [ ] Container scanning configured (Trivy, Snyk Container)
- [ ] Security gate thresholds defined (block on critical, warn on high)
- [ ] Secrets scanning enabled (git-secrets, TruffleHog)
- [ ] Vulnerability remediation process documented (SLA by severity)
- [ ] Security scan integrated into CI pipeline

### Common Mistakes

❌ **No security scanning** — Vulnerabilities reach production  
✅ **Scan on every build** — Catch vulnerabilities early

❌ **Blocking on all vulnerabilities** — Pipeline always fails  
✅ **Threshold-based blocking** — Block on critical, warn on others

❌ **No remediation process** — Vulnerabilities pile up  
✅ **SLA-based remediation** — Clear ownership and timelines

---

## Step 5: Design Deployment Strategy (60-90 minutes)

### Objective

Define how code is deployed to environments safely and reliably.

### Actions

#### 5.1 Choose Deployment Method

**Deployment Methods:**
- **Rolling Deployment:** Update instances one at a time (simple, some downtime)
- **Blue/Green Deployment:** Deploy to new environment, switch traffic (zero downtime, 2x resources)
- **Canary Deployment:** Deploy to small subset, gradually increase (low risk, complex)
- **Feature Flags:** Deploy code, enable features gradually (decouple deploy from release)

**Selection Criteria:**
- **Downtime tolerance:** Zero downtime required?
- **Risk tolerance:** How much risk can we accept?
- **Resource availability:** Can we afford 2x resources?
- **Complexity tolerance:** How complex can deployment be?

**Example:**
```markdown
## Deployment Method

**Method:** Canary Deployment
**Rationale:** 
- Zero downtime required (e-commerce)
- Low risk tolerance (payment processing)
- Resources available (Kubernetes auto-scaling)
- Team comfortable with complexity

**Canary Strategy:**
- Deploy to 5% of traffic
- Monitor for 10 minutes
- If error rate < 1%, promote to 100%
- If error rate >= 1%, rollback
```

#### 5.2 Define Environment Promotion Flow

**Typical Flow:**
```
Development → Staging → Production
```

**Advanced Flow:**
```
Development → QA → Staging → Canary → Production
```

**Promotion Criteria:**
- **Development → Staging:** All tests pass
- **Staging → Production:** Manual approval + smoke tests pass

**Example:**
```markdown
## Environment Promotion Flow

**Development:**
- Trigger: Every commit to main
- Approval: None (automatic)
- Tests: Unit + integration

**Staging:**
- Trigger: Successful deployment to dev
- Approval: None (automatic)
- Tests: E2E + smoke tests

**Production:**
- Trigger: Manual or scheduled
- Approval: Engineering manager
- Tests: Smoke tests + health checks
```

#### 5.3 Design Deployment Automation

**Deployment Tools:**
- **Kubernetes:** kubectl, Helm, Kustomize
- **Cloud-native:** AWS ECS, Google Cloud Run, Azure Container Apps
- **Serverless:** AWS Lambda, Google Cloud Functions, Azure Functions
- **Traditional:** Ansible, Terraform, CloudFormation

**Example (Kubernetes):**
```yaml
# Deployment automation with kubectl
- name: Deploy to Production
  run: |
    kubectl set image deployment/myapp \
      myapp=myregistry/myapp:${{ github.sha }} \
      --namespace=production
    kubectl rollout status deployment/myapp -n production
```

#### 5.4 Establish Deployment Gates

**Gate Types:**
- **Automated Gates:** Tests pass, security scans pass, health checks pass
- **Manual Gates:** Human approval (production deployments)
- **Time-based Gates:** Deploy only during business hours

**Example:**
```markdown
## Deployment Gates

**Development:**
- [Automated] All unit tests pass
- [Automated] All integration tests pass

**Staging:**
- [Automated] All tests pass
- [Automated] Security scans pass
- [Automated] Smoke tests pass

**Production:**
- [Automated] All tests pass
- [Automated] Security scans pass
- [Manual] Engineering manager approval
- [Time-based] Deploy only Mon-Fri 9am-5pm
```

#### 5.5 Plan Deployment Monitoring

**Monitoring During Deployment:**
- **Health checks:** Is the application responding?
- **Error rate:** Are errors increasing?
- **Response time:** Is performance degrading?
- **Resource usage:** CPU, memory, disk usage

**Example:**
```yaml
# Health check during deployment
- name: Wait for Deployment
  run: kubectl rollout status deployment/myapp -n production

- name: Health Check
  run: |
    for i in {1..30}; do
      STATUS=$(curl -s -o /dev/null -w '%{http_code}' https://myapp.com/health)
      if [ $STATUS -eq 200 ]; then
        echo "Health check passed"
        exit 0
      fi
      sleep 10
    done
    echo "Health check failed"
    exit 1
```

#### 5.6 Define Rollback Strategy

**Rollback Triggers:**
- **Automated:** Error rate > threshold, health checks fail
- **Manual:** Engineer initiates rollback

**Rollback Methods:**
- **Kubernetes:** `kubectl rollout undo`
- **Blue/Green:** Switch traffic back to blue environment
- **Canary:** Stop canary, route all traffic to stable version

**Example:**
```markdown
## Rollback Strategy

**Automated Rollback Triggers:**
- Error rate > 1% for 5 minutes
- Health checks fail for 3 consecutive checks
- Response time > 2 seconds (p95) for 5 minutes

**Rollback Method:**
- Kubernetes: `kubectl rollout undo deployment/myapp -n production`
- Time to rollback: < 5 minutes

**Rollback Testing:**
- Test rollback monthly in staging
- Document rollback runbook
```

### Quality Checklist

- [ ] Deployment method chosen (rolling, blue/green, canary)
- [ ] Environment promotion flow defined (dev → staging → prod)
- [ ] Deployment automation designed (kubectl, Helm, Terraform)
- [ ] Deployment gates established (automated, manual, time-based)
- [ ] Deployment monitoring planned (health checks, error rate, response time)
- [ ] Rollback strategy defined (automated triggers, rollback method, testing)
- [ ] Deployment time estimated (target < 15 minutes)

### Common Mistakes

❌ **No rollback strategy** — Prolonged outages  
✅ **Automated rollback** — Fast recovery (< 5 minutes)

❌ **No deployment monitoring** — Don't know if deployment succeeded  
✅ **Health checks + metrics** — Immediate feedback

❌ **Manual deployment steps** — Error-prone, slow  
✅ **Fully automated** — Fast, reliable

---

## Step 6: Design Environment Strategy (30-60 minutes)

### Objective

Define environments and ensure consistency across them.

### Actions

#### 6.1 Define Environments

**Common Environments:**
- **Development:** Developer testing, rapid iteration
- **QA:** Quality assurance testing
- **Staging:** Production-like environment, final testing
- **Canary:** Small subset of production traffic
- **Production:** Live user traffic

**Example:**
```markdown
## Environments

**Development:**
- Purpose: Developer testing
- Deployment: Every commit to main
- Data: Synthetic test data
- Scale: 1 instance, small resources

**Staging:**
- Purpose: Final testing before production
- Deployment: Automatic after dev deployment
- Data: Anonymized production data
- Scale: 10% of production scale

**Production:**
- Purpose: Live user traffic
- Deployment: Manual approval
- Data: Real user data
- Scale: Auto-scaling (2-20 instances)
```

#### 6.2 Establish Environment Parity

**Parity Principles:**
- **Same configuration:** Use same environment variables, secrets
- **Same infrastructure:** Use same Kubernetes manifests, Terraform configs
- **Different scale:** Staging can be smaller, but same architecture

**Example:**
```markdown
## Environment Parity

**Same Across All Environments:**
- Kubernetes manifests (deployment, service, ingress)
- Docker images (same image, different tag)
- Environment variable structure

**Different Per Environment:**
- Database connection strings
- API keys (dev vs. prod)
- Resource limits (staging: 1 CPU, prod: 4 CPU)
- Replica count (staging: 2, prod: 10)
```

#### 6.3 Design Environment Provisioning

**Provisioning Tools:**
- **Terraform:** Infrastructure as Code (IaC)
- **CloudFormation:** AWS-native IaC
- **Helm:** Kubernetes package manager
- **Pulumi:** IaC with programming languages

**Best Practices:**
- Use IaC for all environments
- Version control IaC configs
- Automate environment creation

**Example:**
```hcl
# Terraform example
module "environment" {
  source = "./modules/environment"
  
  environment_name = var.environment_name
  replica_count    = var.replica_count
  cpu_limit        = var.cpu_limit
  memory_limit     = var.memory_limit
}

# staging.tfvars
environment_name = "staging"
replica_count    = 2
cpu_limit        = "1000m"
memory_limit     = "2Gi"

# production.tfvars
environment_name = "production"
replica_count    = 10
cpu_limit        = "4000m"
memory_limit     = "8Gi"
```

#### 6.4 Plan Environment Access Control

**Access Control Principles:**
- **Development:** All developers have access
- **Staging:** All developers have read access, limited write access
- **Production:** Read-only for most, write access for on-call engineers

**Example:**
```markdown
## Environment Access Control

**Development:**
- Developers: Full access (read, write, deploy)
- CI/CD: Full access

**Staging:**
- Developers: Read access, deploy via CI/CD only
- CI/CD: Full access

**Production:**
- Developers: No direct access
- On-call Engineers: Read access, emergency deploy
- CI/CD: Deploy access only
- Engineering Managers: Approval for deployments
```

#### 6.5 Define Environment-Specific Configuration

**Configuration Management:**
- **Environment Variables:** Different per environment
- **Secrets:** Different per environment (dev vs. prod API keys)
- **Feature Flags:** Enable features per environment

**Example:**
```yaml
# Kubernetes ConfigMap per environment
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: staging
data:
  DATABASE_URL: "postgres://staging-db:5432/myapp"
  API_URL: "https://api.staging.myapp.com"
  LOG_LEVEL: "debug"

---
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: production
data:
  DATABASE_URL: "postgres://prod-db:5432/myapp"
  API_URL: "https://api.myapp.com"
  LOG_LEVEL: "info"
```

#### 6.6 Establish Environment Cleanup

**Ephemeral Environments:**
- **PR Environments:** Create environment per PR, delete when PR merged
- **Feature Environments:** Create environment per feature, delete when feature complete

**Cleanup Strategies:**
- **Automatic:** Delete after PR merged or feature complete
- **Scheduled:** Delete environments older than 7 days
- **Manual:** Delete on-demand

**Example:**
```yaml
# GitHub Actions: Create PR environment
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  deploy-pr-env:
    runs-on: ubuntu-latest
    steps:
      - name: Create PR Environment
        run: |
          kubectl create namespace pr-${{ github.event.pull_request.number }}
          helm install myapp ./charts/myapp \
            --namespace pr-${{ github.event.pull_request.number }} \
            --set image.tag=${{ github.sha }}

# Cleanup when PR closed
on:
  pull_request:
    types: [closed]

jobs:
  cleanup-pr-env:
    runs-on: ubuntu-latest
    steps:
      - name: Delete PR Environment
        run: kubectl delete namespace pr-${{ github.event.pull_request.number }}
```

### Quality Checklist

- [ ] Environments defined (dev, staging, production)
- [ ] Environment parity established (same config, different scale)
- [ ] Environment provisioning automated (Terraform, Helm)
- [ ] Access control planned (RBAC per environment)
- [ ] Environment-specific configuration designed (ConfigMaps, Secrets)
- [ ] Environment cleanup strategy established (ephemeral environments)

### Common Mistakes

❌ **Environment drift** — Staging and production have different configs  
✅ **Environment parity** — Use same configs, different values

❌ **Manual environment creation** — Slow, error-prone  
✅ **IaC for all environments** — Fast, consistent

❌ **No environment cleanup** — Costs pile up  
✅ **Automatic cleanup** — Delete ephemeral environments

---

## Step 7: Select CI/CD Platform (30-60 minutes)

### Objective

Choose the right CI/CD tool for your team and project.

### Actions

#### 7.1 Evaluate CI/CD Platforms

**Popular Platforms:**

**GitHub Actions:**
- **Pros:** Native GitHub integration, free for public repos, easy YAML, large marketplace
- **Cons:** Can be expensive for private repos, limited self-hosted options
- **Best for:** GitHub-hosted projects, simple to medium complexity pipelines

**GitLab CI:**
- **Pros:** Built-in, powerful features, generous free tier, self-hosted option
- **Cons:** GitLab-only, learning curve
- **Best for:** GitLab-hosted projects, complex pipelines, self-hosted needs

**Jenkins:**
- **Pros:** Highly customizable, free, large ecosystem, extensive plugins
- **Cons:** Complex setup, maintenance overhead, dated UI
- **Best for:** Complex pipelines, self-hosted, extensive customization

**CircleCI:**
- **Pros:** Fast, good Docker support, easy config
- **Cons:** Can be expensive, limited free tier
- **Best for:** Fast builds, Docker-native applications

**AWS CodePipeline:**
- **Pros:** Native AWS integration, serverless, pay-per-use
- **Cons:** AWS-only, limited features vs. others
- **Best for:** AWS-native applications

#### 7.2 Consider Integration with Existing Tools

**Integration Points:**
- **Version Control:** GitHub, GitLab, Bitbucket
- **Cloud Provider:** AWS, GCP, Azure
- **Monitoring:** Datadog, New Relic, Prometheus
- **Communication:** Slack, Microsoft Teams
- **Issue Tracking:** Jira, Linear, GitHub Issues

**Example:**
```markdown
## Integration Requirements

**Must Have:**
- GitHub integration (native)
- AWS integration (deploy to ECS)
- Slack notifications

**Nice to Have:**
- Datadog integration (deployment tracking)
- Jira integration (link deployments to tickets)
```

#### 7.3 Evaluate Cost

**Cost Factors:**
- **Free tier:** Minutes per month, concurrent builds
- **Paid tier:** Per-minute pricing, per-user pricing
- **Self-hosted:** Infrastructure costs, maintenance time

**Example:**
```markdown
## Cost Comparison

**GitHub Actions:**
- Free: 2,000 minutes/month (private repos)
- Paid: $0.008/minute (Linux), $0.016/minute (macOS)
- Estimated: ~$50/month (5,000 minutes)

**GitLab CI:**
- Free: 400 minutes/month
- Paid: $19/user/month (10,000 minutes)
- Estimated: ~$190/month (10 users)

**Jenkins (Self-Hosted):**
- Free: Open-source
- Infrastructure: ~$100/month (AWS EC2)
- Maintenance: ~10 hours/month
```

#### 7.4 Assess Ease of Use

**Ease of Use Factors:**
- **Configuration:** YAML, UI, Groovy (Jenkins)
- **Learning curve:** How long to get started?
- **Documentation:** Quality and completeness
- **Community:** Size and activity

**Example:**
```markdown
## Ease of Use Assessment

**GitHub Actions:**
- Configuration: YAML (easy)
- Learning curve: 1-2 days
- Documentation: Excellent
- Community: Large, active

**Jenkins:**
- Configuration: Groovy (complex)
- Learning curve: 1-2 weeks
- Documentation: Good, but scattered
- Community: Large, but fragmented
```

#### 7.5 Consider Scalability

**Scalability Factors:**
- **Concurrent builds:** How many builds can run simultaneously?
- **Build minutes:** Monthly limit
- **Self-hosted runners:** Can we add more capacity?

**Example:**
```markdown
## Scalability Assessment

**GitHub Actions:**
- Concurrent builds: 20 (free), 60 (paid)
- Build minutes: 2,000/month (free), unlimited (paid)
- Self-hosted: Yes, easy to add runners

**GitLab CI:**
- Concurrent builds: Unlimited (self-hosted)
- Build minutes: 400/month (free), 10,000/month (paid)
- Self-hosted: Yes, built-in
```

#### 7.6 Plan Migration (if replacing existing CI/CD)

**Migration Steps:**
1. **Audit existing pipelines:** Document current pipelines
2. **Create migration plan:** Prioritize pipelines to migrate
3. **Migrate one pipeline:** Test new platform
4. **Migrate remaining pipelines:** Batch migration
5. **Decommission old platform:** After all pipelines migrated

**Example:**
```markdown
## Migration Plan (Jenkins → GitHub Actions)

**Phase 1: Preparation (Week 1)**
- Audit all Jenkins pipelines (15 pipelines)
- Document pipeline logic
- Set up GitHub Actions

**Phase 2: Pilot (Week 2)**
- Migrate 1 simple pipeline (myapp-frontend)
- Test thoroughly
- Document learnings

**Phase 3: Batch Migration (Weeks 3-4)**
- Migrate 10 medium-complexity pipelines
- Run in parallel with Jenkins (validation)

**Phase 4: Final Migration (Week 5)**
- Migrate remaining 4 complex pipelines
- Decommission Jenkins
```

### Quality Checklist

- [ ] CI/CD platforms evaluated (GitHub Actions, GitLab CI, Jenkins, etc.)
- [ ] Integration with existing tools assessed (Git, cloud, monitoring)
- [ ] Cost analyzed (free tier, paid tier, self-hosted)
- [ ] Ease of use considered (YAML config, learning curve, documentation)
- [ ] Scalability assessed (concurrent builds, build minutes, self-hosted)
- [ ] Migration plan created (if replacing existing CI/CD)
- [ ] Decision documented with rationale

### Common Mistakes

❌ **Choosing based on cost alone** — Cheapest option may not be best  
✅ **Consider total cost of ownership** — Include maintenance time

❌ **Not considering team skills** — Complex tool, steep learning curve  
✅ **Choose tool team can use effectively** — Faster time to value

❌ **No migration plan** — Chaotic migration  
✅ **Phased migration** — Pilot → batch → final

---

## Step 8: Design Pipeline Architecture (60-90 minutes)

### Objective

Define pipeline stages, flow, and reusability.

### Actions

#### 8.1 Design CI Pipeline

**Typical CI Pipeline Stages:**
```
Checkout → Install → Lint → Unit Test → Build → Integration Test → Security Scan → Artifact
```

**Example:**
```yaml
# GitHub Actions CI Pipeline
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      # 1. Checkout
      - uses: actions/checkout@v3
      
      # 2. Install dependencies
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      
      # 3. Lint (fail fast)
      - run: npm run lint
      
      # 4. Unit tests (fail fast)
      - run: npm run test:unit
      
      # 5. Build
      - run: npm run build
      
      # 6. Integration tests
      - run: npm run test:integration
      
      # 7. Security scan
      - run: npx snyk test
      
      # 8. Build and push artifact (Docker image)
      - run: |
          docker build -t myapp:${{ github.sha }} .
          docker push myregistry/myapp:${{ github.sha }}
```

#### 8.2 Design CD Pipeline

**Typical CD Pipeline Stages:**
```
Artifact → Deploy → Health Check → Smoke Test → Monitor
```

**Example:**
```yaml
# GitHub Actions CD Pipeline
name: CD

on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches: [main]

jobs:
  deploy-staging:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    steps:
      # 1. Deploy
      - run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=staging
      
      # 2. Wait for rollout
      - run: kubectl rollout status deployment/myapp -n staging
      
      # 3. Health check
      - run: curl -f https://staging.myapp.com/health
      
      # 4. Smoke tests
      - run: npm run test:smoke -- --env=staging
  
  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      # Same as staging, but with manual approval
      - run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=production
      - run: kubectl rollout status deployment/myapp -n production
      - run: curl -f https://myapp.com/health
      - run: npm run test:smoke -- --env=production
```

#### 8.3 Define Pipeline Triggers

**Common Triggers:**
- **Push:** Trigger on every commit to specific branches
- **Pull Request:** Trigger on PR open/update
- **Tag:** Trigger on version tag (release)
- **Schedule:** Nightly builds, weekly security scans
- **Manual:** On-demand deployments
- **Workflow Dispatch:** Manual trigger with parameters

**Example:**
```yaml
# Multiple triggers
on:
  # Trigger on push to main
  push:
    branches: [main]
  
  # Trigger on PR
  pull_request:
    branches: [main]
  
  # Trigger on release tag
  push:
    tags:
      - 'v*.*.*'
  
  # Trigger on schedule (nightly)
  schedule:
    - cron: '0 2 * * *'
  
  # Manual trigger
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'staging'
```

#### 8.4 Establish Pipeline Dependencies

**Dependency Types:**
- **Sequential:** Steps run one after another
- **Parallel:** Steps run simultaneously
- **Conditional:** Steps run based on conditions

**Example:**
```yaml
# Parallel jobs
jobs:
  # Run in parallel
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - run: npm run test:unit
  
  integration-tests:
    runs-on: ubuntu-latest
    steps:
      - run: npm run test:integration
  
  # Run after both complete
  deploy:
    needs: [unit-tests, integration-tests]
    runs-on: ubuntu-latest
    steps:
      - run: kubectl apply -f deployment.yaml
```

#### 8.5 Plan Pipeline Reusability

**Reusability Strategies:**
- **Reusable Workflows:** Share workflows across repos
- **Composite Actions:** Bundle multiple steps
- **Templates:** Use templates for common patterns

**Example:**
```yaml
# Reusable workflow (.github/workflows/deploy.yml)
name: Deploy

on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
      image_tag:
        required: true
        type: string

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ inputs.image_tag }} \
            --namespace=${{ inputs.environment }}

# Use reusable workflow
jobs:
  deploy-staging:
    uses: ./.github/workflows/deploy.yml
    with:
      environment: staging
      image_tag: ${{ github.sha }}
```

#### 8.6 Design Pipeline Notifications

**Notification Channels:**
- **Slack:** Pipeline status, deployment notifications
- **Email:** Failure notifications
- **Dashboard:** Real-time pipeline status
- **GitHub:** PR comments, commit statuses

**Example:**
```yaml
# Slack notification
- name: Notify Slack
  if: always()
  run: |
    STATUS="${{ job.status }}"
    COLOR="good"
    if [ "$STATUS" != "success" ]; then
      COLOR="danger"
    fi
    
    curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
      -H 'Content-Type: application/json' \
      -d '{
        "attachments": [{
          "color": "'$COLOR'",
          "title": "Deployment to Production",
          "text": "Status: '$STATUS'\nCommit: ${{ github.sha }}",
          "footer": "GitHub Actions"
        }]
      }'
```

### Quality Checklist

- [ ] CI pipeline designed (checkout → build → test → scan → artifact)
- [ ] CD pipeline designed (artifact → deploy → test → monitor)
- [ ] Pipeline triggers defined (push, PR, tag, schedule, manual)
- [ ] Pipeline dependencies established (sequential, parallel, conditional)
- [ ] Pipeline reusability planned (reusable workflows, templates)
- [ ] Pipeline notifications designed (Slack, email, dashboard)
- [ ] Pipeline documented (README, diagrams)

### Common Mistakes

❌ **Monolithic pipelines** — One giant pipeline, hard to maintain  
✅ **Modular pipelines** — Separate CI and CD, reusable workflows

❌ **No parallelization** — Sequential jobs, slow pipeline  
✅ **Parallel jobs** — Run tests in parallel, faster feedback

❌ **No notifications** — Don't know when pipeline fails  
✅ **Slack notifications** — Immediate feedback

---

## Step 9: Implement Secrets Management (30-60 minutes)

### Objective

Securely manage secrets in CI/CD pipelines.

### Actions

#### 9.1 Choose Secrets Management Tool

**Secrets Management Tools:**
- **GitHub Secrets:** Built-in for GitHub Actions
- **GitLab CI/CD Variables:** Built-in for GitLab CI
- **HashiCorp Vault:** Enterprise-grade, self-hosted
- **AWS Secrets Manager:** AWS-native
- **Azure Key Vault:** Azure-native
- **Google Secret Manager:** GCP-native

**Selection Criteria:**
- **Integration:** Native integration with CI/CD platform
- **Cost:** Free tier, paid tier
- **Features:** Rotation, versioning, audit logs

**Example:**
```markdown
## Secrets Management Tool

**Tool:** AWS Secrets Manager
**Rationale:**
- Native AWS integration (deploy to ECS)
- Automatic rotation support
- Audit logs (CloudTrail)
- Cost: ~$0.40/secret/month
```

#### 9.2 Define Secret Types

**Common Secret Types:**
- **API Keys:** Third-party services (Stripe, SendGrid)
- **Database Passwords:** PostgreSQL, MySQL
- **Certificates:** SSL/TLS certificates
- **SSH Keys:** Deploy keys, server access
- **OAuth Tokens:** GitHub tokens, Google OAuth

**Example:**
```markdown
## Secret Types

**API Keys:**
- STRIPE_API_KEY (payment processing)
- SENDGRID_API_KEY (email)
- DATADOG_API_KEY (monitoring)

**Database:**
- DATABASE_PASSWORD (PostgreSQL)

**Certificates:**
- SSL_CERTIFICATE (HTTPS)
- SSL_PRIVATE_KEY (HTTPS)

**Tokens:**
- GITHUB_TOKEN (CI/CD)
```

#### 9.3 Establish Secret Rotation Policy

**Rotation Frequency:**
- **High-risk secrets:** Rotate every 30 days (database passwords, API keys)
- **Medium-risk secrets:** Rotate every 90 days (certificates)
- **Low-risk secrets:** Rotate every 180 days (read-only tokens)

**Rotation Methods:**
- **Automatic:** AWS Secrets Manager, Vault
- **Manual:** Rotate manually, update in secrets manager

**Example:**
```markdown
## Secret Rotation Policy

**Database Passwords:**
- Rotation: Every 30 days (automatic)
- Tool: AWS Secrets Manager
- Process: Lambda function rotates password, updates RDS

**API Keys:**
- Rotation: Every 90 days (manual)
- Process: Generate new key, update in AWS Secrets Manager, revoke old key
```

#### 9.4 Design Secret Injection

**Injection Methods:**
- **Environment Variables:** Most common, easy to use
- **Files:** For certificates, config files
- **Vault Agent:** Sidecar container fetches secrets

**Example:**
```yaml
# Kubernetes: Inject secrets as environment variables
apiVersion: v1
kind: Deployment
metadata:
  name: myapp
spec:
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        env:
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        - name: STRIPE_API_KEY
          valueFrom:
            secretKeyRef:
              name: stripe-secret
              key: api-key
```

#### 9.5 Plan Secret Access Control

**Access Control Principles:**
- **Least privilege:** Only grant access to secrets that are needed
- **Environment-specific:** Dev secrets != prod secrets
- **Audit logs:** Track who accessed what secrets

**Example:**
```markdown
## Secret Access Control

**Development Secrets:**
- Access: All developers
- Secrets: Dev database password, dev API keys

**Production Secrets:**
- Access: CI/CD pipeline, on-call engineers
- Secrets: Prod database password, prod API keys
- Audit: CloudTrail logs all access
```

#### 9.6 Implement Secret Scanning

**Secret Scanning Tools:**
- **git-secrets:** Prevent committing secrets
- **TruffleHog:** Find secrets in Git history
- **GitHub Secret Scanning:** Automatic for public repos
- **GitGuardian:** Real-time alerts

**Example:**
```yaml
# Pre-commit hook: Prevent committing secrets
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/awslabs/git-secrets
    rev: master
    hooks:
      - id: git-secrets

# CI: Scan for secrets in code
- name: Scan for Secrets
  run: |
    docker run --rm -v "$PWD:/pwd" \
      trufflesecurity/trufflehog:latest \
      filesystem /pwd --fail
```

### Quality Checklist

- [ ] Secrets management tool selected (Vault, AWS Secrets Manager, GitHub Secrets)
- [ ] Secret types defined (API keys, database passwords, certificates)
- [ ] Secret rotation policy established (frequency, automation)
- [ ] Secret injection method designed (environment variables, files)
- [ ] Secret access control planned (least privilege, environment-specific)
- [ ] Secret scanning implemented (git-secrets, TruffleHog)

### Common Mistakes

❌ **Hardcoded secrets** — Secrets committed to Git  
✅ **Secrets management tool** — Never commit secrets

❌ **No secret rotation** — Secrets never change  
✅ **Automatic rotation** — Rotate every 30-90 days

❌ **Same secrets for all environments** — Dev and prod use same keys  
✅ **Environment-specific secrets** — Different keys per environment

---

## Step 10: Document and Train (30-60 minutes)

### Objective

Enable the team to use CI/CD effectively through documentation and training.

### Actions

#### 10.1 Write CI/CD Strategy Document

**Strategy Document Sections:**
1. **Overview:** CI/CD goals, deployment frequency
2. **Architecture:** Pipeline stages, flow diagram
3. **Build Strategy:** Build tools, caching, versioning
4. **Testing Strategy:** Test types, coverage, parallelization
5. **Security Strategy:** SAST, dependency scanning, secrets management
6. **Deployment Strategy:** Deployment method, rollback, monitoring
7. **Environment Strategy:** Environments, parity, access control
8. **Tool Selection:** CI/CD platform, rationale

**Example:**
```markdown
# CI/CD Strategy

## Overview

**Goal:** Deploy to production multiple times per day with confidence
**Current State:** Weekly manual deployments
**Target State:** Automated deployments, < 15 min pipeline

## Architecture

```
CI Pipeline: Checkout → Lint → Test → Build → Scan → Artifact
CD Pipeline: Deploy → Health Check → Smoke Test → Monitor
```

## Build Strategy

- **Tool:** npm with caching
- **Artifacts:** Docker images (myapp:${GIT_SHA})
- **Versioning:** Git SHA for traceability

## Testing Strategy

- **Unit Tests:** 2,500 tests, ~3 min
- **Integration Tests:** 150 tests, ~8 min
- **E2E Tests:** 30 tests, ~12 min
- **Coverage:** > 80% required

## Security Strategy

- **SAST:** SonarQube Cloud
- **Dependency Scanning:** Snyk
- **Container Scanning:** Trivy
- **Secrets Scanning:** TruffleHog

## Deployment Strategy

- **Method:** Canary (5% → 100%)
- **Rollback:** Automated (error rate > 1%)
- **Monitoring:** Health checks, error rate, response time

## Environment Strategy

- **Environments:** Dev, Staging, Production
- **Parity:** Same config, different scale
- **Access Control:** RBAC per environment

## Tool Selection

- **Platform:** GitHub Actions
- **Rationale:** Native GitHub integration, easy YAML, team familiar
```

#### 10.2 Create Pipeline Usage Guide

**Usage Guide Sections:**
1. **How to Add a New Service:** Step-by-step guide
2. **How to Trigger a Deployment:** Manual deployment
3. **How to Rollback:** Emergency rollback procedure
4. **How to Add a New Environment:** Create new environment
5. **How to Add Secrets:** Add secrets to secrets manager

**Example:**
```markdown
# Pipeline Usage Guide

## How to Add a New Service

1. **Create repository** from template:
   ```bash
   gh repo create myorg/new-service --template myorg/service-template
   ```

2. **Copy CI/CD workflow** from template:
   ```bash
   cp .github/workflows/ci.yml new-service/.github/workflows/
   ```

3. **Update service name** in workflow:
   ```yaml
   env:
     SERVICE_NAME: new-service
   ```

4. **Commit and push**:
   ```bash
   git add .
   git commit -m "Add CI/CD pipeline"
   git push
   ```

5. **Verify pipeline runs** in GitHub Actions tab

## How to Trigger a Manual Deployment

1. Go to **Actions** tab in GitHub
2. Select **CD** workflow
3. Click **Run workflow**
4. Select environment (staging, production)
5. Enter Git SHA or tag
6. Click **Run workflow**

## How to Rollback

**Automatic Rollback:**
- Pipeline automatically rolls back if error rate > 1%

**Manual Rollback:**
1. Find previous successful deployment SHA:
   ```bash
   kubectl get deployment myapp -n production -o yaml | grep image
   ```

2. Rollback using kubectl:
   ```bash
   kubectl rollout undo deployment/myapp -n production
   ```

3. Verify rollback:
   ```bash
   kubectl rollout status deployment/myapp -n production
   ```
```

#### 10.3 Document Troubleshooting

**Common Issues:**
1. **Pipeline fails on lint:** Fix linting errors
2. **Tests fail:** Check test logs, fix failing tests
3. **Deployment fails:** Check health checks, rollback
4. **Secrets not found:** Add secrets to secrets manager

**Example:**
```markdown
# Troubleshooting Guide

## Pipeline Fails on Lint

**Symptom:** Pipeline fails with "Linting errors found"

**Solution:**
1. Run lint locally:
   ```bash
   npm run lint
   ```

2. Fix errors or run auto-fix:
   ```bash
   npm run lint -- --fix
   ```

3. Commit and push:
   ```bash
   git add .
   git commit -m "Fix linting errors"
   git push
   ```

## Deployment Fails

**Symptom:** Deployment fails with "Health check failed"

**Solution:**
1. Check application logs:
   ```bash
   kubectl logs deployment/myapp -n production
   ```

2. Check health endpoint:
   ```bash
   curl https://myapp.com/health
   ```

3. If unhealthy, rollback:
   ```bash
   kubectl rollout undo deployment/myapp -n production
   ```

4. Fix issue, redeploy
```

#### 10.4 Create Runbooks

**Runbooks to Create:**
1. **Deployment Runbook:** Step-by-step deployment process
2. **Rollback Runbook:** Emergency rollback procedure
3. **Incident Response Runbook:** What to do when pipeline fails

**Example:**
```markdown
# Deployment Runbook

## Pre-Deployment Checklist

- [ ] All tests pass in staging
- [ ] Code review approved
- [ ] Security scans pass
- [ ] Deployment window confirmed (Mon-Fri 9am-5pm)
- [ ] On-call engineer notified

## Deployment Steps

1. **Trigger deployment:**
   - Go to GitHub Actions
   - Run CD workflow
   - Select "production"
   - Enter Git SHA

2. **Monitor deployment:**
   - Watch pipeline logs
   - Check health endpoint: `curl https://myapp.com/health`
   - Monitor error rate in Datadog

3. **Verify deployment:**
   - Run smoke tests: `npm run test:smoke -- --env=production`
   - Check key user flows
   - Monitor for 15 minutes

4. **Post-Deployment:**
   - Notify team in Slack
   - Update deployment log
   - Monitor for 1 hour

## Rollback Procedure

**If error rate > 1% or critical bug found:**

1. **Immediate rollback:**
   ```bash
   kubectl rollout undo deployment/myapp -n production
   ```

2. **Verify rollback:**
   ```bash
   kubectl rollout status deployment/myapp -n production
   curl https://myapp.com/health
   ```

3. **Notify team:**
   - Post in #incidents Slack channel
   - Page on-call engineer

4. **Post-Mortem:**
   - Create incident report
   - Root cause analysis
   - Action items
```

#### 10.5 Conduct Team Training

**Training Topics:**
1. **CI/CD Overview:** Goals, architecture, benefits
2. **Pipeline Usage:** How to use pipelines, trigger deployments
3. **Troubleshooting:** Common issues, how to debug
4. **Best Practices:** Commit often, write tests, monitor deployments

**Training Format:**
- **Workshop:** 2-hour hands-on session
- **Documentation:** Written guides, videos
- **Office Hours:** Weekly Q&A sessions

**Example Agenda:**
```markdown
# CI/CD Training Workshop

**Duration:** 2 hours

## Agenda

**Part 1: Overview (30 min)**
- CI/CD goals and benefits
- Pipeline architecture
- Demo: Watch a deployment

**Part 2: Hands-On (60 min)**
- Exercise 1: Trigger a deployment to staging
- Exercise 2: Add a new test to pipeline
- Exercise 3: Rollback a deployment

**Part 3: Best Practices (20 min)**
- Commit often, small changes
- Write tests for all code
- Monitor deployments
- Use feature flags

**Part 4: Q&A (10 min)**
- Open questions
- Troubleshooting tips
```

#### 10.6 Establish Support Process

**Support Channels:**
- **Slack:** #ci-cd-support channel
- **Office Hours:** Weekly 1-hour session
- **Documentation:** Confluence, GitHub Wiki
- **On-Call:** 24/7 support for production issues

**Example:**
```markdown
## CI/CD Support

**Slack Channel:** #ci-cd-support
- Ask questions
- Report issues
- Share tips

**Office Hours:** Every Wednesday 2-3pm
- Live Q&A
- Troubleshooting help
- Feature requests

**Documentation:** https://wiki.mycompany.com/cicd
- Strategy document
- Usage guides
- Troubleshooting
- Runbooks

**On-Call:** For production incidents
- Page: @cicd-oncall
- Escalation: Engineering manager
```

### Quality Checklist

- [ ] CI/CD strategy document written (overview, architecture, strategies)
- [ ] Pipeline usage guide created (add service, trigger deployment, rollback)
- [ ] Troubleshooting guide documented (common issues, solutions)
- [ ] Runbooks written (deployment, rollback, incident response)
- [ ] Team training conducted (workshop, documentation, office hours)
- [ ] Support process established (Slack, office hours, on-call)

### Common Mistakes

❌ **No documentation** — Team doesn't know how to use CI/CD  
✅ **Comprehensive docs** — Strategy, usage, troubleshooting, runbooks

❌ **No training** — Team struggles with new system  
✅ **Hands-on training** — Workshop, exercises, Q&A

❌ **No support** — Team blocked on issues  
✅ **Support channels** — Slack, office hours, on-call

---

## Summary

You've now completed the CI/CD design process! You should have:

✅ **CI/CD requirements defined** — Deployment frequency, quality gates, rollback, compliance  
✅ **Build strategy designed** — Build tools, caching, versioning, artifacts  
✅ **Testing strategy designed** — Test types, coverage, parallelization, failure handling  
✅ **Security scanning integrated** — SAST, dependency scanning, container scanning, secrets scanning  
✅ **Deployment strategy designed** — Deployment method, promotion flow, rollback, monitoring  
✅ **Environment strategy designed** — Environments, parity, provisioning, access control  
✅ **CI/CD platform selected** — Platform evaluation, cost analysis, migration plan  
✅ **Pipeline architecture designed** — CI/CD pipelines, triggers, dependencies, reusability  
✅ **Secrets management implemented** — Secrets tool, rotation, injection, access control  
✅ **Documentation and training completed** — Strategy doc, usage guide, troubleshooting, runbooks, training

**Next Steps:**
1. **Implement pipelines** — Create pipeline definitions (YAML, Groovy)
2. **Test pipelines** — Deploy to dev, staging, verify
3. **Train team** — Conduct workshop, share documentation
4. **Monitor and improve** — Track metrics, optimize pipelines
5. **Iterate** — Continuous improvement based on feedback

**Success Metrics:**
- Deployment frequency: Multiple times per day
- Pipeline time: < 15 minutes
- Deployment success rate: > 95%
- Time to rollback: < 5 minutes
- Test coverage: > 80%

---

## Additional Resources

**Books:**
- "Continuous Delivery" by Jez Humble and David Farley
- "The DevOps Handbook" by Gene Kim et al.
- "Accelerate" by Nicole Forsgren et al.

**Tools:**
- GitHub Actions: https://docs.github.com/actions
- GitLab CI: https://docs.gitlab.com/ee/ci/
- Jenkins: https://www.jenkins.io/doc/
- Snyk: https://snyk.io/
- Trivy: https://aquasecurity.github.io/trivy/

**Communities:**
- DevOps subreddit: r/devops
- CNCF Slack: https://slack.cncf.io/
- GitHub Community: https://github.community/
