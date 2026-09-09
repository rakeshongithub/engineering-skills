# CI/CD Design

**Category:** Operations  
**Complexity:** Advanced  
**Estimated Time:** 4-8 hours

---

## Purpose

Design continuous integration and continuous deployment (CI/CD) pipelines that automate building, testing, security scanning, and deployment of software to enable fast, safe, and reliable releases.

---

## When to Use

- Designing CI/CD for new projects or services
- Improving existing CI/CD pipelines (slow, unreliable, insecure)
- Implementing DevOps or platform engineering practices
- Migrating from manual deployments to automation
- Adopting microservices or cloud-native architecture
- Improving deployment frequency and reliability
- Implementing security scanning and compliance checks
- Reducing deployment time and manual errors

---

## When NOT to Use

- **For one-time scripts or prototypes** — manual deployment may suffice
- **Without version control** — CI/CD requires source control
- **Without automated tests** — CI/CD amplifies bad code without tests
- **For systems with no deployment** — libraries, SDKs may not need CD
- **Without team buy-in** — requires cultural adoption
- **For legacy systems with no tests** — add tests first

---

## Inputs

### Required

- **Application architecture** — monolith, microservices, serverless
- **Tech stack** — languages, frameworks, build tools
- **Deployment targets** — cloud, on-prem, Kubernetes, serverless
- **Team structure** — size, skills, responsibilities
- **Release requirements** — frequency, approval process, rollback needs

### Optional

- **Existing CI/CD** — current pipelines and pain points
- **Compliance requirements** — SOC 2, HIPAA, PCI DSS
- **Security requirements** — SAST, DAST, dependency scanning
- **Performance requirements** — build time, deployment time
- **Budget constraints** — CI/CD platform costs
- **Integration requirements** — Slack, Jira, monitoring tools

---

## Expected Outputs

### Primary Deliverables

1. **CI/CD Strategy Document**
   - Pipeline architecture
   - Build strategy
   - Testing strategy
   - Deployment strategy
   - Security scanning strategy

2. **Pipeline Definitions**
   - CI pipeline (build, test, scan)
   - CD pipeline (deploy to environments)
   - Release pipeline (production deployment)
   - Rollback pipeline

3. **Environment Strategy**
   - Development, staging, production environments
   - Environment promotion flow
   - Environment parity

4. **Security and Compliance**
   - SAST/DAST integration
   - Dependency scanning
   - Secrets management
   - Compliance gates

### Supporting Artifacts

- **Tool selection** — CI/CD platform (GitHub Actions, GitLab CI, Jenkins)
- **Infrastructure as Code** — pipeline definitions, environment configs
- **Deployment automation** — scripts, Helm charts, Terraform
- **Monitoring integration** — deployment tracking, rollback triggers
- **Documentation** — pipeline usage, troubleshooting, runbooks

---

## Workflow

### Step 1: Define CI/CD Requirements

**Objective:** Understand what needs to be automated and why.

**Actions:**
- Define deployment frequency goals (daily, weekly, on-demand)
- Identify manual deployment pain points
- Define quality gates (tests, security, performance)
- Establish deployment approval requirements
- Define rollback requirements (time to rollback, automation)
- Identify compliance requirements (audit logs, approvals)

**Quality Check:**
- [ ] Deployment frequency goals defined
- [ ] Quality gates identified
- [ ] Approval process documented
- [ ] Rollback requirements clear
- [ ] Compliance requirements understood

### Step 2: Design Build Strategy

**Objective:** Define how code is built and packaged.

**Actions:**
- Choose build tools (Maven, Gradle, npm, Docker)
- Define build triggers (push, PR, tag, schedule)
- Design artifact strategy (Docker images, JARs, npm packages)
- Establish versioning strategy (semantic versioning, git SHA)
- Define build caching strategy (speed optimization)
- Plan build matrix (multiple OS, language versions)

**Quality Check:**
- [ ] Build tools selected
- [ ] Build triggers defined
- [ ] Artifact strategy clear
- [ ] Versioning strategy established
- [ ] Caching strategy planned

### Step 3: Design Testing Strategy

**Objective:** Define automated testing in CI/CD pipeline.

**Actions:**
- Identify test types (unit, integration, e2e, performance)
- Define test execution order (fast tests first)
- Establish test coverage requirements (e.g., > 80%)
- Design test parallelization (speed optimization)
- Plan test environment provisioning
- Define test failure handling (fail fast, retry)

**Quality Check:**
- [ ] Test types identified
- [ ] Test execution order defined
- [ ] Coverage requirements set
- [ ] Parallelization planned
- [ ] Failure handling defined

### Step 4: Design Security Scanning Strategy

**Objective:** Integrate security scanning into pipeline.

**Actions:**
- Choose SAST tools (SonarQube, Snyk, Checkmarx)
- Choose dependency scanning (Dependabot, Snyk, OWASP)
- Choose container scanning (Trivy, Clair, Snyk)
- Define security gate thresholds (block on critical, warn on high)
- Plan secrets scanning (detect hardcoded secrets)
- Establish vulnerability remediation process

**Quality Check:**
- [ ] SAST tool selected
- [ ] Dependency scanning enabled
- [ ] Container scanning configured
- [ ] Security gates defined
- [ ] Secrets scanning enabled

### Step 5: Design Deployment Strategy

**Objective:** Define how code is deployed to environments.

**Actions:**
- Choose deployment method (rolling, blue/green, canary)
- Define environment promotion flow (dev → staging → prod)
- Design deployment automation (scripts, Helm, Terraform)
- Establish deployment gates (manual approval, automated checks)
- Plan deployment monitoring (health checks, metrics)
- Define rollback strategy (automatic, manual, time-based)

**Quality Check:**
- [ ] Deployment method chosen
- [ ] Promotion flow defined
- [ ] Automation designed
- [ ] Gates established
- [ ] Rollback strategy clear

### Step 6: Design Environment Strategy

**Objective:** Define environments and their purpose.

**Actions:**
- Define environments (dev, staging, production, canary)
- Establish environment parity (same config, different scale)
- Design environment provisioning (IaC, automation)
- Plan environment access control (RBAC)
- Define environment-specific configuration (env vars, secrets)
- Establish environment cleanup (ephemeral environments)

**Quality Check:**
- [ ] Environments defined
- [ ] Parity established
- [ ] Provisioning automated
- [ ] Access control planned
- [ ] Configuration management designed

### Step 7: Select CI/CD Platform

**Objective:** Choose the right CI/CD tool.

**Actions:**
- Evaluate CI/CD platforms (GitHub Actions, GitLab CI, Jenkins, CircleCI)
- Consider integration with existing tools (Git, cloud, monitoring)
- Evaluate cost (free tier, per-minute pricing, self-hosted)
- Assess ease of use (YAML config, UI, learning curve)
- Consider scalability (concurrent builds, build minutes)
- Plan migration (if replacing existing CI/CD)

**Quality Check:**
- [ ] Platforms evaluated
- [ ] Integration assessed
- [ ] Cost analyzed
- [ ] Ease of use considered
- [ ] Decision documented

### Step 8: Design Pipeline Architecture

**Objective:** Define pipeline stages and flow.

**Actions:**
- Design CI pipeline (checkout → build → test → scan → artifact)
- Design CD pipeline (artifact → deploy → test → monitor)
- Define pipeline triggers (push, PR, manual, schedule)
- Establish pipeline dependencies (parallel, sequential)
- Plan pipeline reusability (templates, shared workflows)
- Design pipeline notifications (Slack, email, dashboard)

**Quality Check:**
- [ ] CI pipeline designed
- [ ] CD pipeline designed
- [ ] Triggers defined
- [ ] Dependencies clear
- [ ] Reusability planned

### Step 9: Implement Secrets Management

**Objective:** Securely manage secrets in CI/CD.

**Actions:**
- Choose secrets management tool (Vault, AWS Secrets Manager, GitHub Secrets)
- Define secret types (API keys, database passwords, certificates)
- Establish secret rotation policy
- Design secret injection (environment variables, files)
- Plan secret access control (who can access what)
- Implement secret scanning (prevent commits)

**Quality Check:**
- [ ] Secrets tool selected
- [ ] Secret types defined
- [ ] Rotation policy set
- [ ] Injection method designed
- [ ] Access control planned

### Step 10: Document and Train

**Objective:** Enable team to use CI/CD effectively.

**Actions:**
- Write CI/CD strategy document
- Create pipeline usage guide (how to add new services)
- Document troubleshooting (common issues, solutions)
- Create runbooks (pipeline failures, rollbacks)
- Conduct team training (pipeline usage, best practices)
- Establish support process (Slack channel, office hours)

**Quality Check:**
- [ ] Strategy documented
- [ ] Usage guide created
- [ ] Troubleshooting documented
- [ ] Runbooks written
- [ ] Training conducted

---

## Decision Framework

### CI/CD Platform Selection

**GitHub Actions:**
- Best for: GitHub-hosted projects, simple pipelines
- Pros: Native GitHub integration, free for public repos, easy YAML
- Cons: Limited self-hosted options, can be expensive for private repos

**GitLab CI:**
- Best for: GitLab-hosted projects, complex pipelines
- Pros: Built-in, powerful features, free tier generous
- Cons: GitLab-only, learning curve

**Jenkins:**
- Best for: Complex pipelines, self-hosted, extensive plugins
- Pros: Highly customizable, free, large ecosystem
- Cons: Complex setup, maintenance overhead, UI dated

**CircleCI:**
- Best for: Fast builds, Docker-native
- Pros: Fast, good Docker support, easy config
- Cons: Can be expensive, limited free tier

**AWS CodePipeline:**
- Best for: AWS-native applications
- Pros: Native AWS integration, serverless
- Cons: AWS-only, limited features vs. others

### Deployment Strategy Selection

**Rolling Deployment:**
- Use when: Standard deployment, acceptable brief downtime
- Pros: Simple, resource-efficient
- Cons: Risky (all instances updated), slow rollback

**Blue/Green Deployment:**
- Use when: Zero-downtime required, fast rollback needed
- Pros: Zero downtime, instant rollback
- Cons: 2x resources, database migrations complex

**Canary Deployment:**
- Use when: Risk mitigation critical, gradual rollout desired
- Pros: Low risk, gradual validation
- Cons: Complex, requires traffic splitting

---

## Quality Checklist

### Build
- [ ] Builds are fast (< 10 min)
- [ ] Builds are reproducible (same input = same output)
- [ ] Build caching implemented
- [ ] Artifacts versioned and stored
- [ ] Build failures are clear and actionable

### Testing
- [ ] Unit tests run on every commit
- [ ] Integration tests run on every PR
- [ ] E2E tests run before deployment
- [ ] Test coverage tracked (> 80%)
- [ ] Tests are fast (< 15 min total)
- [ ] Test failures are actionable

### Security
- [ ] SAST scanning enabled
- [ ] Dependency scanning enabled
- [ ] Container scanning enabled
- [ ] Secrets scanning enabled
- [ ] Security gates block critical vulnerabilities
- [ ] Vulnerability remediation process defined

### Deployment
- [ ] Deployments are automated
- [ ] Deployments are fast (< 15 min)
- [ ] Rollback is automated
- [ ] Health checks validate deployment
- [ ] Deployment notifications sent
- [ ] Deployment tracked in monitoring

### Compliance
- [ ] Audit logs enabled
- [ ] Approval gates for production
- [ ] Change tracking implemented
- [ ] Compliance scans pass

---

## Common Mistakes

### 1. No Automated Tests

**Problem:** CI/CD without tests deploys broken code faster.

**Solution:** Implement automated tests before CI/CD. Start with unit tests.

### 2. Slow Pipelines

**Problem:** 30+ minute pipelines slow development.

**Solution:** Optimize builds (caching, parallelization), run fast tests first.

### 3. No Rollback Strategy

**Problem:** Bad deployment with no way to rollback quickly.

**Solution:** Design automated rollback, test rollback regularly.

### 4. Secrets in Code

**Problem:** Hardcoded secrets committed to Git.

**Solution:** Use secrets management, enable secrets scanning.

### 5. No Security Scanning

**Problem:** Vulnerabilities deployed to production.

**Solution:** Integrate SAST, dependency scanning, container scanning.

### 6. Manual Approval Bottlenecks

**Problem:** Waiting hours/days for manual approvals.

**Solution:** Automate approvals where possible, use time-based windows.

### 7. Environment Drift

**Problem:** Staging and production have different configurations.

**Solution:** Use Infrastructure as Code, ensure environment parity.

### 8. No Monitoring Integration

**Problem:** Deployments succeed but application fails.

**Solution:** Integrate health checks, metrics, alerts into deployment.

### 9. Complex Pipelines

**Problem:** Pipelines are hard to understand and maintain.

**Solution:** Keep pipelines simple, use templates, document well.

### 10. No Training

**Problem:** Team doesn't know how to use CI/CD.

**Solution:** Provide training, documentation, and support.

---

## Examples

### Example 1: Node.js Microservice (GitHub Actions)

**Pipeline:**
```yaml
name: CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Unit tests
        run: npm run test:unit
      
      - name: Build
        run: npm run build
      
      - name: Integration tests
        run: npm run test:integration
      
      - name: Security scan (Snyk)
        run: npx snyk test
      
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Container scan
        run: docker scan myapp:${{ github.sha }}
      
      - name: Push to registry
        if: github.ref == 'refs/heads/main'
        run: |
          docker tag myapp:${{ github.sha }} myregistry/myapp:${{ github.sha }}
          docker push myregistry/myapp:${{ github.sha }}
  
  deploy-staging:
    needs: build-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=staging
      
      - name: Wait for rollout
        run: kubectl rollout status deployment/myapp -n staging
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging
  
  deploy-production:
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=production
      
      - name: Wait for rollout
        run: kubectl rollout status deployment/myapp -n production
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=production
      
      - name: Notify Slack
        run: |
          curl -X POST ${{ secrets.SLACK_WEBHOOK }} \
            -d '{"text":"Deployed myapp:${{ github.sha }} to production"}'
```

### Example 2: Java Spring Boot (GitLab CI)

**Pipeline:**
```yaml
stages:
  - build
  - test
  - security
  - package
  - deploy

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"

cache:
  paths:
    - .m2/repository
    - target/

build:
  stage: build
  image: maven:3.8-openjdk-17
  script:
    - mvn clean compile
  artifacts:
    paths:
      - target/

unit-test:
  stage: test
  image: maven:3.8-openjdk-17
  script:
    - mvn test
  coverage: '/Total.*?(\d+\.\d+)%/'
  artifacts:
    reports:
      junit: target/surefire-reports/TEST-*.xml
      coverage_report:
        coverage_format: cobertura
        path: target/site/cobertura/coverage.xml

integration-test:
  stage: test
  image: maven:3.8-openjdk-17
  services:
    - postgres:14
  variables:
    POSTGRES_DB: testdb
    POSTGRES_USER: test
    POSTGRES_PASSWORD: test
  script:
    - mvn verify -Pintegration-tests

sast:
  stage: security
  image: maven:3.8-openjdk-17
  script:
    - mvn sonar:sonar \
        -Dsonar.host.url=$SONAR_URL \
        -Dsonar.login=$SONAR_TOKEN

dependency-check:
  stage: security
  image: owasp/dependency-check:latest
  script:
    - /usr/share/dependency-check/bin/dependency-check.sh \
        --scan . \
        --format JSON \
        --out dependency-check-report.json
  artifacts:
    paths:
      - dependency-check-report.json

package:
  stage: package
  image: maven:3.8-openjdk-17
  script:
    - mvn package -DskipTests
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  only:
    - main

deploy-staging:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/myapp \
        myapp=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA \
        --namespace=staging
    - kubectl rollout status deployment/myapp -n staging
  environment:
    name: staging
    url: https://staging.myapp.com
  only:
    - main

deploy-production:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/myapp \
        myapp=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA \
        --namespace=production
    - kubectl rollout status deployment/myapp -n production
  environment:
    name: production
    url: https://myapp.com
  when: manual
  only:
    - main
```

### Example 3: Python FastAPI (CircleCI)

**Pipeline:**
```yaml
version: 2.1

orbs:
  python: circleci/python@2.1.1
  docker: circleci/docker@2.2.0

jobs:
  build-and-test:
    docker:
      - image: cimg/python:3.11
    steps:
      - checkout
      - python/install-packages:
          pkg-manager: pip
      - run:
          name: Lint
          command: |
            pip install flake8
            flake8 .
      - run:
          name: Unit tests
          command: |
            pip install pytest pytest-cov
            pytest --cov=app tests/unit
      - run:
          name: Integration tests
          command: pytest tests/integration
      - run:
          name: Security scan
          command: |
            pip install safety
            safety check
  
  build-docker:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - setup_remote_docker
      - docker/check
      - docker/build:
          image: myapp
          tag: << pipeline.git.revision >>
      - run:
          name: Container scan
          command: |
            docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
              aquasec/trivy image myapp:<< pipeline.git.revision >>
      - docker/push:
          image: myapp
          tag: << pipeline.git.revision >>
  
  deploy-staging:
    docker:
      - image: cimg/base:stable
    steps:
      - run:
          name: Deploy to staging
          command: |
            # Deploy using your tool (kubectl, helm, etc.)
            kubectl set image deployment/myapp \
              myapp=myregistry/myapp:<< pipeline.git.revision >> \
              --namespace=staging
  
  deploy-production:
    docker:
      - image: cimg/base:stable
    steps:
      - run:
          name: Deploy to production
          command: |
            kubectl set image deployment/myapp \
              myapp=myregistry/myapp:<< pipeline.git.revision >> \
              --namespace=production

workflows:
  build-test-deploy:
    jobs:
      - build-and-test
      - build-docker:
          requires:
            - build-and-test
          filters:
            branches:
              only: main
      - deploy-staging:
          requires:
            - build-docker
      - hold-production:
          type: approval
          requires:
            - deploy-staging
      - deploy-production:
          requires:
            - hold-production
```

### Example 4: Modern CI/CD with Feature Flags

**Strategy:**
- Deploy to production frequently (multiple times per day)
- Use feature flags to control feature rollout
- Decouple deployment from release

**Pipeline:**
```yaml
# Simplified - deploys to production on every merge to main
name: Continuous Deployment

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build and test
        run: |
          npm ci
          npm run test
          npm run build
      
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Security scan
        run: docker scan myapp:${{ github.sha }}
      
      - name: Push to registry
        run: |
          docker tag myapp:${{ github.sha }} myregistry/myapp:${{ github.sha }}
          docker push myregistry/myapp:${{ github.sha }}
      
      - name: Deploy to production (canary)
        run: |
          # Deploy to 5% of traffic
          kubectl set image deployment/myapp-canary \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=production
      
      - name: Monitor canary
        run: |
          # Wait 10 minutes, monitor error rate
          sleep 600
          ERROR_RATE=$(curl -s https://metrics.myapp.com/error_rate)
          if [ "$ERROR_RATE" -gt "1" ]; then
            echo "Canary failed, rolling back"
            kubectl rollout undo deployment/myapp-canary -n production
            exit 1
          fi
      
      - name: Promote to full production
        run: |
          kubectl set image deployment/myapp \
            myapp=myregistry/myapp:${{ github.sha }} \
            --namespace=production
      
      - name: Update feature flags
        run: |
          # New features are deployed but disabled by default
          # Enable gradually using feature flag service
          curl -X POST https://featureflags.myapp.com/api/flags \
            -d '{"flag":"new-checkout","enabled":false,"version":"${{ github.sha }}"}'
```

---

## Related Skills

### Prerequisites
- **version-control** — Git workflow, branching strategy
- **testing-strategy** — Automated testing approach
- **architecture-discovery** — Understand system structure

### Commonly Followed By
- **deployment-strategy** — Choose deployment approach (blue/green, canary)
- **observability-design** — Monitor deployments
- **production-readiness** — Validate before production

### Related Skills
- **infrastructure-as-code** — Automate infrastructure provisioning
- **security-architecture-review** — Security scanning and compliance
- **disaster-recovery** — Backup and recovery automation

---

## Skill Composition

### DevOps Workflow

```
version-control (Git workflow)
      ↓
ci-cd-design (this skill)
      ↓
deployment-strategy (blue/green, canary)
      ↓
observability-design (monitor deployments)
      ↓
incident-analysis (when deployments fail)
```

---

## Evaluation Criteria

### Excellent
- Fully automated CI/CD (no manual steps)
- Fast pipelines (< 10 min build, < 15 min deploy)
- Comprehensive testing (unit, integration, e2e)
- Security scanning integrated (SAST, dependency, container)
- Automated rollback
- High deployment frequency (multiple per day)
- Low deployment failure rate (< 5%)

### Good
- Mostly automated (some manual approvals)
- Reasonable pipeline speed (< 20 min)
- Good test coverage (> 80%)
- Basic security scanning
- Manual rollback process
- Regular deployments (daily/weekly)

### Needs Improvement
- Manual deployment steps
- Slow pipelines (> 30 min)
- Low test coverage (< 60%)
- No security scanning
- No rollback strategy
- Infrequent deployments (monthly)
- High failure rate (> 15%)

---

## Tags

`operations`, `ci-cd`, `devops`, `automation`, `deployment`, `pipeline`, `testing`, `security`, `continuous-integration`, `continuous-deployment`, `github-actions`, `gitlab-ci`, `jenkins`

---

## Version

**1.0.0** — Initial release
