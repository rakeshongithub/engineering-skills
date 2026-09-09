# CI/CD Design - Quick Reference

**Design continuous integration and continuous deployment (CI/CD) pipelines that automate building, testing, security scanning, and deployment of software to enable fast, safe, and reliable releases.**

---

## Quick Start

### When to Use This Skill

✅ Designing CI/CD for new projects or services  
✅ Improving existing CI/CD pipelines (slow, unreliable, insecure)  
✅ Implementing DevOps or platform engineering practices  
✅ Migrating from manual deployments to automation  
✅ Adopting microservices or cloud-native architecture  
✅ Improving deployment frequency and reliability  

### When NOT to Use

❌ For one-time scripts or prototypes  
❌ Without version control  
❌ Without automated tests  
❌ For systems with no deployment (libraries, SDKs)  
❌ Without team buy-in  

---

## 10-Step Workflow

1. **Define CI/CD Requirements** (30-60 min) — Deployment frequency, quality gates, rollback, compliance
2. **Design Build Strategy** (45-90 min) — Build tools, caching, versioning, artifacts
3. **Design Testing Strategy** (60-90 min) — Test types, coverage, parallelization, failure handling
4. **Design Security Scanning Strategy** (45-90 min) — SAST, dependency scanning, container scanning, secrets scanning
5. **Design Deployment Strategy** (60-90 min) — Deployment method, promotion flow, rollback, monitoring
6. **Design Environment Strategy** (30-60 min) — Environments, parity, provisioning, access control
7. **Select CI/CD Platform** (30-60 min) — Platform evaluation, cost analysis, migration plan
8. **Design Pipeline Architecture** (60-90 min) — CI/CD pipelines, triggers, dependencies, reusability
9. **Implement Secrets Management** (30-60 min) — Secrets tool, rotation, injection, access control
10. **Document and Train** (30-60 min) — Strategy doc, usage guide, troubleshooting, runbooks, training

**Total Time:** 4-8 hours

---

## Key Inputs

**Required:**
- Application architecture (monolith, microservices, serverless)
- Tech stack (languages, frameworks, build tools)
- Deployment targets (cloud, on-prem, Kubernetes, serverless)
- Team structure (size, skills, responsibilities)
- Release requirements (frequency, approval process, rollback needs)

**Optional:**
- Existing CI/CD (current pipelines and pain points)
- Compliance requirements (SOC 2, HIPAA, PCI DSS)
- Security requirements (SAST, DAST, dependency scanning)

---

## Key Outputs

1. **CI/CD Strategy Document** — Pipeline architecture, build/test/deploy strategy
2. **Pipeline Definitions** — CI, CD, release, rollback pipelines
3. **Environment Strategy** — Dev, staging, production, promotion flow
4. **Security and Compliance** — SAST, DAST, dependency scanning, secrets management
5. **Documentation** — Usage guide, troubleshooting, runbooks

---

## CI/CD Platform Comparison

| Platform | Best For | Pros | Cons |
|----------|----------|------|------|
| **GitHub Actions** | GitHub projects, simple pipelines | Native integration, free for public repos, easy YAML | Can be expensive for private repos |
| **GitLab CI** | GitLab projects, complex pipelines | Built-in, powerful, generous free tier | GitLab-only, learning curve |
| **Jenkins** | Complex pipelines, self-hosted | Highly customizable, free, large ecosystem | Complex setup, maintenance overhead |
| **CircleCI** | Fast builds, Docker-native | Fast, good Docker support, easy config | Can be expensive, limited free tier |
| **AWS CodePipeline** | AWS-native applications | Native AWS integration, serverless | AWS-only, limited features |

---

## Deployment Strategy Comparison

| Strategy | Downtime | Risk | Resources | Complexity | Rollback Speed |
|----------|----------|------|-----------|------------|----------------|
| **Rolling** | Brief | Medium | 1x | Low | Slow (minutes) |
| **Blue/Green** | None | Low | 2x | Medium | Instant (seconds) |
| **Canary** | None | Very Low | 1.1x | High | Fast (seconds) |
| **Feature Flags** | None | Very Low | 1x | High | Instant (toggle) |

**Recommendation:**
- **Rolling:** Standard deployments, acceptable brief downtime
- **Blue/Green:** Zero-downtime required, fast rollback needed
- **Canary:** Risk mitigation critical, gradual rollout desired
- **Feature Flags:** Decouple deployment from release, A/B testing

---

## Security Scanning Tools

### SAST (Static Application Security Testing)
- **SonarQube** — Code quality + security, self-hosted or cloud
- **Snyk Code** — Developer-friendly, IDE integration
- **Checkmarx** — Enterprise-grade, comprehensive
- **Semgrep** — Open-source, customizable rules

### Dependency Scanning
- **Dependabot** — GitHub native, automatic PRs
- **Snyk** — Comprehensive, fix suggestions
- **OWASP Dependency-Check** — Open-source, CLI-based
- **npm audit** — Built-in for npm projects

### Container Scanning
- **Trivy** — Fast, comprehensive, open-source
- **Snyk Container** — Developer-friendly
- **Clair** — Open-source, API-based
- **Docker Scan** — Built-in to Docker CLI

### Secrets Scanning
- **git-secrets** — Prevent committing secrets
- **TruffleHog** — Find secrets in Git history
- **GitHub Secret Scanning** — Automatic for public repos
- **GitGuardian** — Real-time alerts

---

## Quality Checklist

### Build
- [ ] Builds are fast (< 10 min)
- [ ] Builds are reproducible
- [ ] Build caching implemented
- [ ] Artifacts versioned and stored

### Testing
- [ ] Unit tests run on every commit
- [ ] Integration tests run on every PR
- [ ] E2E tests run before deployment
- [ ] Test coverage > 80%
- [ ] Tests are fast (< 15 min total)

### Security
- [ ] SAST scanning enabled
- [ ] Dependency scanning enabled
- [ ] Container scanning enabled
- [ ] Secrets scanning enabled
- [ ] Security gates block critical vulnerabilities

### Deployment
- [ ] Deployments are automated
- [ ] Deployments are fast (< 15 min)
- [ ] Rollback is automated
- [ ] Health checks validate deployment
- [ ] Deployment notifications sent

### Compliance
- [ ] Audit logs enabled
- [ ] Approval gates for production
- [ ] Change tracking implemented
- [ ] Compliance scans pass

---

## Common Mistakes

❌ **No automated tests** — CI/CD without tests deploys broken code faster  
✅ **Implement tests first** — Start with unit tests

❌ **Slow pipelines** — 30+ minute pipelines slow development  
✅ **Optimize builds** — Caching, parallelization, fail fast

❌ **No rollback strategy** — Bad deployment with no way to rollback quickly  
✅ **Design rollback first** — Test rollback regularly

❌ **Secrets in code** — Hardcoded secrets committed to Git  
✅ **Use secrets management** — Never commit secrets

❌ **No security scanning** — Vulnerabilities deployed to production  
✅ **Scan on every build** — Catch vulnerabilities early

❌ **Manual approval bottlenecks** — Waiting hours/days for approvals  
✅ **Automate where possible** — Use time-based windows

❌ **Environment drift** — Staging and production have different configs  
✅ **Use Infrastructure as Code** — Ensure environment parity

❌ **No monitoring integration** — Deployments succeed but application fails  
✅ **Integrate health checks** — Metrics, alerts into deployment

---

## Quick Examples

### Example 1: Node.js Microservice (GitHub Actions)

**Pipeline:** Lint → Unit Tests → Build → Integration Tests → Security Scan → Docker Build → Deploy  
**Deployment:** Canary (5% → 100%)  
**Rollback:** Automated (error rate > 1%)  
**Time:** 20 minutes  

### Example 2: Java Spring Boot (GitLab CI)

**Pipeline:** Build → Unit Tests → Integration Tests → SAST → Dependency Check → Package → Deploy  
**Deployment:** Blue/Green (zero downtime)  
**Rollback:** Manual (switch back to blue)  
**Time:** 35 minutes  

### Example 3: Python FastAPI (CircleCI)

**Pipeline:** Lint → Unit Tests → Integration Tests → Security Scan → Docker Build → Deploy  
**Deployment:** Rolling (Cloud Run automatic)  
**Rollback:** Instant (Cloud Run revisions)  
**Time:** 10 minutes  

### Example 4: Monorepo with Nx (GitHub Actions)

**Pipeline:** Affected Projects → Lint → Test → Build → Security Scan → Deploy (selective)  
**Deployment:** Selective (only changed services)  
**Rollback:** Per-service  
**Time:** 13 minutes (selective)  

---

## Success Metrics

### Excellent
- Deployment frequency: Multiple times per day
- Pipeline time: < 10 min (build), < 15 min (deploy)
- Deployment success rate: > 95%
- Time to rollback: < 5 minutes (automated)
- Test coverage: > 80%
- Security: Zero critical vulnerabilities

### Good
- Deployment frequency: Daily/weekly
- Pipeline time: < 20 min
- Deployment success rate: > 85%
- Time to rollback: < 15 minutes
- Test coverage: > 70%

### Needs Improvement
- Deployment frequency: Monthly or less
- Pipeline time: > 30 min
- Deployment success rate: < 75%
- Time to rollback: > 30 minutes
- Test coverage: < 60%

---

## Related Skills

**Prerequisites:**
- `version-control` — Git workflow, branching strategy
- `testing-strategy` — Automated testing approach
- `architecture-discovery` — Understand system structure

**Commonly Followed By:**
- `deployment-strategy` — Choose deployment approach (blue/green, canary)
- `observability-design` — Monitor deployments
- `production-readiness` — Validate before production

**Works With:**
- `infrastructure-as-code` — Automate infrastructure provisioning
- `security-architecture-review` — Security scanning and compliance
- `disaster-recovery` — Backup and recovery automation

---

## Additional Resources

**Documentation:**
- [SKILL.md](./SKILL.md) — Comprehensive skill documentation
- [instructions.md](./instructions.md) — Step-by-step workflow guide
- [examples.md](./examples.md) — Real-world implementation examples

**Books:**
- "Continuous Delivery" by Jez Humble and David Farley
- "The DevOps Handbook" by Gene Kim et al.
- "Accelerate" by Nicole Forsgren et al.

**Tools:**
- GitHub Actions: https://docs.github.com/actions
- GitLab CI: https://docs.gitlab.com/ee/ci/
- Jenkins: https://www.jenkins.io/doc/
- CircleCI: https://circleci.com/docs/

---

**Version:** 1.0.0  
**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 4-8 hours
