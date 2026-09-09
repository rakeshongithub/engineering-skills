# CI/CD Design - Comprehensive Examples

This document provides four detailed examples of CI/CD pipeline designs for different scenarios and technology stacks.

---

## Example 1: Modern Node.js Microservices (GitHub Actions + Kubernetes)

### Context

**Company:** E-commerce platform with 10 microservices  
**Tech Stack:** Node.js, TypeScript, React, PostgreSQL, Redis  
**Infrastructure:** AWS EKS (Kubernetes)  
**Team:** 15 engineers, 3 teams  
**Current State:** Manual deployments, weekly releases, 2-hour deployment process  
**Goal:** Automated deployments, multiple deployments per day, < 15 min pipeline

### Requirements

- **Deployment Frequency:** Multiple deployments per day
- **Quality Gates:** > 80% test coverage, no critical vulnerabilities
- **Compliance:** SOC 2 (audit logs, access control)
- **Rollback:** Automated, < 5 minutes
- **Environments:** Development, Staging, Production

### CI/CD Strategy

#### Build Strategy

- **Build Tool:** npm with caching
- **Artifacts:** Docker images tagged with Git SHA
- **Versioning:** Git SHA for traceability, semantic versioning for releases
- **Caching:** npm dependencies cached, Docker layer caching

#### Testing Strategy

- **Unit Tests:** Jest, 2,500 tests, ~3 minutes
- **Integration Tests:** Supertest, 150 tests, ~8 minutes
- **E2E Tests:** Playwright, 30 critical flows, ~12 minutes
- **Coverage Requirement:** > 80% (enforced)
- **Parallelization:** Run unit, integration, e2e in parallel

#### Security Strategy

- **SAST:** SonarQube Cloud (code quality + security)
- **Dependency Scanning:** Snyk (automatic PRs for fixes)
- **Container Scanning:** Trivy (scan Docker images)
- **Secrets Scanning:** TruffleHog (prevent secrets in Git)
- **Security Gates:** Block on critical vulnerabilities

#### Deployment Strategy

- **Method:** Canary deployment (5% → 100%)
- **Promotion Flow:** Dev (automatic) → Staging (automatic) → Production (manual approval)
- **Rollback:** Automated if error rate > 1% or health checks fail
- **Monitoring:** Health checks, error rate, response time (p95)

### Pipeline Implementation

#### CI Pipeline (.github/workflows/ci.yml)

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  REGISTRY: 123456789012.dkr.ecr.us-east-1.amazonaws.com
  SERVICE_NAME: user-service

jobs:
  # Job 1: Lint and Unit Tests (fail fast)
  lint-and-unit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Unit tests
        run: npm run test:unit -- --coverage
      
      - name: Check coverage
        run: |
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          if (( $(echo "$COVERAGE < 80" | bc -l) )); then
            echo "Coverage $COVERAGE% is below 80%"
            exit 1
          fi
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
  
  # Job 2: Integration Tests (parallel with unit tests)
  integration-tests:
    runs-on: ubuntu-latest
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
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: postgres://postgres:test@localhost:5432/test
          REDIS_URL: redis://localhost:6379
  
  # Job 3: Build and Security Scan
  build-and-scan:
    needs: [lint-and-unit, integration-tests]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
      
      - name: SAST Scan (SonarQube)
        run: |
          npm run sonar-scanner -- \
            -Dsonar.projectKey=${{ env.SERVICE_NAME }} \
            -Dsonar.organization=myorg \
            -Dsonar.host.url=https://sonarcloud.io \
            -Dsonar.login=${{ secrets.SONAR_TOKEN }}
      
      - name: Dependency Scan (Snyk)
        run: npx snyk test --severity-threshold=high
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
      
      - name: Build Docker image
        run: |
          docker build -t ${{ env.SERVICE_NAME }}:${{ github.sha }} .
      
      - name: Container Scan (Trivy)
        run: |
          docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy:latest image \
            --severity HIGH,CRITICAL \
            --exit-code 1 \
            ${{ env.SERVICE_NAME }}:${{ github.sha }}
      
      - name: Secrets Scan (TruffleHog)
        run: |
          docker run --rm -v "$PWD:/pwd" \
            trufflesecurity/trufflehog:latest \
            filesystem /pwd --fail
      
      - name: Configure AWS credentials
        if: github.ref == 'refs/heads/main'
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Login to ECR
        if: github.ref == 'refs/heads/main'
        run: |
          aws ecr get-login-password --region us-east-1 | \
            docker login --username AWS --password-stdin ${{ env.REGISTRY }}
      
      - name: Push to ECR
        if: github.ref == 'refs/heads/main'
        run: |
          docker tag ${{ env.SERVICE_NAME }}:${{ github.sha }} \
            ${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }}
          docker push ${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }}
  
  # Job 4: E2E Tests (run after build)
  e2e-tests:
    needs: build-and-scan
    runs-on: ubuntu-latest
    strategy:
      matrix:
        shard: [1, 2, 3, 4]
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install Playwright
        run: npx playwright install --with-deps
      
      - name: E2E tests
        run: npx playwright test --shard=${{ matrix.shard }}/4
      
      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: playwright-report-${{ matrix.shard }}
          path: playwright-report/
```

#### CD Pipeline (.github/workflows/cd.yml)

```yaml
name: CD

on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches: [main]

env:
  REGISTRY: 123456789012.dkr.ecr.us-east-1.amazonaws.com
  SERVICE_NAME: user-service

jobs:
  # Deploy to Development
  deploy-dev:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Update kubeconfig
        run: aws eks update-kubeconfig --name my-cluster --region us-east-1
      
      - name: Deploy to dev
        run: |
          kubectl set image deployment/${{ env.SERVICE_NAME }} \
            ${{ env.SERVICE_NAME }}=${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }} \
            --namespace=dev
      
      - name: Wait for rollout
        run: kubectl rollout status deployment/${{ env.SERVICE_NAME }} -n dev
      
      - name: Health check
        run: |
          for i in {1..30}; do
            STATUS=$(curl -s -o /dev/null -w '%{http_code}' https://dev.myapp.com/health)
            if [ $STATUS -eq 200 ]; then
              echo "Health check passed"
              exit 0
            fi
            sleep 10
          done
          echo "Health check failed"
          exit 1
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=dev
  
  # Deploy to Staging
  deploy-staging:
    needs: deploy-dev
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Update kubeconfig
        run: aws eks update-kubeconfig --name my-cluster --region us-east-1
      
      - name: Deploy to staging
        run: |
          kubectl set image deployment/${{ env.SERVICE_NAME }} \
            ${{ env.SERVICE_NAME }}=${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }} \
            --namespace=staging
      
      - name: Wait for rollout
        run: kubectl rollout status deployment/${{ env.SERVICE_NAME }} -n staging
      
      - name: Health check
        run: curl -f https://staging.myapp.com/health
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging
      
      - name: Run E2E tests
        run: npx playwright test --grep @critical
  
  # Deploy to Production (Canary)
  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Update kubeconfig
        run: aws eks update-kubeconfig --name my-cluster --region us-east-1
      
      # Step 1: Deploy to canary (5% traffic)
      - name: Deploy to canary
        run: |
          kubectl set image deployment/${{ env.SERVICE_NAME }}-canary \
            ${{ env.SERVICE_NAME }}=${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }} \
            --namespace=production
      
      - name: Wait for canary rollout
        run: kubectl rollout status deployment/${{ env.SERVICE_NAME }}-canary -n production
      
      # Step 2: Monitor canary for 10 minutes
      - name: Monitor canary
        run: |
          echo "Monitoring canary for 10 minutes..."
          sleep 600
          
          # Check error rate
          ERROR_RATE=$(curl -s "https://api.datadog.com/api/v1/query?query=avg:myapp.error_rate{env:production,canary:true}" \
            -H "DD-API-KEY: ${{ secrets.DATADOG_API_KEY }}" \
            -H "DD-APPLICATION-KEY: ${{ secrets.DATADOG_APP_KEY }}" | jq '.series[0].pointlist[-1][1]')
          
          echo "Canary error rate: $ERROR_RATE%"
          
          if (( $(echo "$ERROR_RATE > 1" | bc -l) )); then
            echo "Canary failed: error rate too high"
            kubectl rollout undo deployment/${{ env.SERVICE_NAME }}-canary -n production
            exit 1
          fi
          
          echo "Canary successful, promoting to production"
      
      # Step 3: Promote to full production
      - name: Deploy to production
        run: |
          kubectl set image deployment/${{ env.SERVICE_NAME }} \
            ${{ env.SERVICE_NAME }}=${{ env.REGISTRY }}/${{ env.SERVICE_NAME }}:${{ github.sha }} \
            --namespace=production
      
      - name: Wait for production rollout
        run: kubectl rollout status deployment/${{ env.SERVICE_NAME }} -n production
      
      - name: Health check
        run: curl -f https://myapp.com/health
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=production
      
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
                "fields": [
                  {"title": "Service", "value": "'${{ env.SERVICE_NAME }}'", "short": true},
                  {"title": "Status", "value": "'$STATUS'", "short": true},
                  {"title": "Commit", "value": "'${{ github.sha }}'", "short": true},
                  {"title": "Author", "value": "'${{ github.actor }}'", "short": true}
                ],
                "footer": "GitHub Actions"
              }]
            }'
```

### Results

**Before CI/CD:**
- Deployment frequency: Weekly
- Deployment time: 2 hours (manual)
- Deployment success rate: 70%
- Time to rollback: 1-2 hours

**After CI/CD:**
- Deployment frequency: 5-10 times per day
- Pipeline time: 12 minutes (CI) + 8 minutes (CD) = 20 minutes
- Deployment success rate: 98%
- Time to rollback: < 2 minutes (automated)
- Test coverage: 85%
- Zero critical vulnerabilities in production

---

## Example 2: Java Spring Boot Monolith (GitLab CI + AWS ECS)

### Context

**Company:** Financial services company  
**Tech Stack:** Java 17, Spring Boot, PostgreSQL, Angular frontend  
**Infrastructure:** AWS ECS (Fargate)  
**Team:** 8 engineers  
**Current State:** Manual deployments, monthly releases, 4-hour deployment process  
**Compliance:** PCI DSS, SOC 2  
**Goal:** Automated deployments, weekly releases, < 30 min pipeline

### Requirements

- **Deployment Frequency:** Weekly (production), daily (staging)
- **Quality Gates:** > 70% test coverage, no high/critical vulnerabilities
- **Compliance:** PCI DSS (security scanning, audit logs), SOC 2
- **Rollback:** Manual approval, < 10 minutes
- **Environments:** Development, QA, Staging, Production

### CI/CD Strategy

#### Build Strategy

- **Build Tool:** Maven with dependency caching
- **Artifacts:** JAR file + Docker image
- **Versioning:** Semantic versioning (v1.2.3) + Git SHA
- **Caching:** Maven .m2 repository, Docker layers

#### Testing Strategy

- **Unit Tests:** JUnit 5, 1,800 tests, ~5 minutes
- **Integration Tests:** Spring Boot Test, 120 tests, ~15 minutes
- **Security Tests:** OWASP Dependency-Check, SonarQube
- **Coverage Requirement:** > 70%

#### Security Strategy

- **SAST:** SonarQube (self-hosted)
- **Dependency Scanning:** OWASP Dependency-Check
- **Container Scanning:** Clair
- **Compliance:** PCI DSS scans, audit logs
- **Security Gates:** Block on high/critical vulnerabilities

#### Deployment Strategy

- **Method:** Blue/Green deployment (zero downtime)
- **Promotion Flow:** Dev → QA → Staging → Production
- **Rollback:** Manual, switch back to blue environment
- **Monitoring:** CloudWatch metrics, health checks

### Pipeline Implementation

#### .gitlab-ci.yml

```yaml
stages:
  - build
  - test
  - security
  - package
  - deploy-dev
  - deploy-qa
  - deploy-staging
  - deploy-production

variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"
  REGISTRY: 123456789012.dkr.ecr.us-east-1.amazonaws.com
  SERVICE_NAME: payment-service

cache:
  paths:
    - .m2/repository
    - target/

# Stage 1: Build
build:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean compile
  artifacts:
    paths:
      - target/
    expire_in: 1 hour

# Stage 2: Unit Tests
unit-test:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn test
  coverage: '/Total.*?(\d+\.\d+)%/'
  artifacts:
    reports:
      junit: target/surefire-reports/TEST-*.xml
      coverage_report:
        coverage_format: cobertura
        path: target/site/cobertura/coverage.xml

# Stage 2: Integration Tests
integration-test:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  services:
    - postgres:14
  variables:
    POSTGRES_DB: testdb
    POSTGRES_USER: test
    POSTGRES_PASSWORD: test
  script:
    - mvn verify -Pintegration-tests
  artifacts:
    reports:
      junit: target/failsafe-reports/TEST-*.xml

# Stage 3: SAST Scan
sast:
  stage: security
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn sonar:sonar \
        -Dsonar.host.url=$SONAR_URL \
        -Dsonar.login=$SONAR_TOKEN \
        -Dsonar.qualitygate.wait=true
  allow_failure: false

# Stage 3: Dependency Check (PCI DSS requirement)
dependency-check:
  stage: security
  image: owasp/dependency-check:latest
  script:
    - /usr/share/dependency-check/bin/dependency-check.sh \
        --scan . \
        --format JSON \
        --failOnCVSS 7 \
        --out dependency-check-report.json
  artifacts:
    paths:
      - dependency-check-report.json
    expire_in: 30 days

# Stage 4: Package
package:
  stage: package
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn package -DskipTests
    - docker build -t $SERVICE_NAME:$CI_COMMIT_SHA .
    - docker tag $SERVICE_NAME:$CI_COMMIT_SHA $REGISTRY/$SERVICE_NAME:$CI_COMMIT_SHA
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 week

# Stage 4: Container Scan
container-scan:
  stage: package
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker pull $REGISTRY/$SERVICE_NAME:$CI_COMMIT_SHA
    - docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
        aquasec/trivy:latest image \
        --severity HIGH,CRITICAL \
        --exit-code 1 \
        $REGISTRY/$SERVICE_NAME:$CI_COMMIT_SHA

# Stage 4: Push to ECR
push-image:
  stage: package
  image: docker:latest
  services:
    - docker:dind
  script:
    - apk add --no-cache aws-cli
    - aws ecr get-login-password --region us-east-1 | \
        docker login --username AWS --password-stdin $REGISTRY
    - docker push $REGISTRY/$SERVICE_NAME:$CI_COMMIT_SHA
  only:
    - main
    - develop

# Stage 5: Deploy to Development
deploy-dev:
  stage: deploy-dev
  image: amazon/aws-cli:latest
  script:
    - |
      aws ecs update-service \
        --cluster my-cluster \
        --service $SERVICE_NAME-dev \
        --force-new-deployment \
        --region us-east-1
    - |
      aws ecs wait services-stable \
        --cluster my-cluster \
        --services $SERVICE_NAME-dev \
        --region us-east-1
  environment:
    name: development
    url: https://dev.myapp.com
  only:
    - develop

# Stage 6: Deploy to QA
deploy-qa:
  stage: deploy-qa
  image: amazon/aws-cli:latest
  script:
    - |
      aws ecs update-service \
        --cluster my-cluster \
        --service $SERVICE_NAME-qa \
        --force-new-deployment \
        --region us-east-1
    - |
      aws ecs wait services-stable \
        --cluster my-cluster \
        --services $SERVICE_NAME-qa \
        --region us-east-1
  environment:
    name: qa
    url: https://qa.myapp.com
  only:
    - main

# Stage 7: Deploy to Staging
deploy-staging:
  stage: deploy-staging
  image: amazon/aws-cli:latest
  script:
    - |
      # Blue/Green: Deploy to green environment
      aws ecs update-service \
        --cluster my-cluster \
        --service $SERVICE_NAME-staging-green \
        --task-definition $SERVICE_NAME:$CI_COMMIT_SHA \
        --force-new-deployment \
        --region us-east-1
    - |
      aws ecs wait services-stable \
        --cluster my-cluster \
        --services $SERVICE_NAME-staging-green \
        --region us-east-1
    - |
      # Switch traffic to green
      aws elbv2 modify-listener \
        --listener-arn $STAGING_LISTENER_ARN \
        --default-actions Type=forward,TargetGroupArn=$STAGING_GREEN_TG_ARN \
        --region us-east-1
  environment:
    name: staging
    url: https://staging.myapp.com
  only:
    - main

# Stage 8: Deploy to Production (Manual)
deploy-production:
  stage: deploy-production
  image: amazon/aws-cli:latest
  script:
    - |
      # Blue/Green: Deploy to green environment
      aws ecs update-service \
        --cluster my-cluster \
        --service $SERVICE_NAME-production-green \
        --task-definition $SERVICE_NAME:$CI_COMMIT_SHA \
        --force-new-deployment \
        --region us-east-1
    - |
      aws ecs wait services-stable \
        --cluster my-cluster \
        --services $SERVICE_NAME-production-green \
        --region us-east-1
    - |
      # Health check green environment
      for i in {1..30}; do
        STATUS=$(curl -s -o /dev/null -w '%{http_code}' https://green.myapp.com/health)
        if [ $STATUS -eq 200 ]; then
          echo "Health check passed"
          break
        fi
        sleep 10
      done
    - |
      # Switch traffic to green (manual approval required)
      aws elbv2 modify-listener \
        --listener-arn $PRODUCTION_LISTENER_ARN \
        --default-actions Type=forward,TargetGroupArn=$PRODUCTION_GREEN_TG_ARN \
        --region us-east-1
    - |
      # Create audit log (PCI DSS requirement)
      echo "Deployment to production: $CI_COMMIT_SHA by $GITLAB_USER_EMAIL at $(date)" | \
        aws s3 cp - s3://audit-logs/deployments/$(date +%Y-%m-%d-%H-%M-%S).log
  environment:
    name: production
    url: https://myapp.com
  when: manual
  only:
    - main
```

### Results

**Before CI/CD:**
- Deployment frequency: Monthly
- Deployment time: 4 hours (manual)
- Deployment success rate: 60%
- Time to rollback: 2-4 hours
- PCI DSS compliance: Manual audits

**After CI/CD:**
- Deployment frequency: Weekly (production), daily (staging)
- Pipeline time: 25 minutes (CI) + 10 minutes (CD) = 35 minutes
- Deployment success rate: 95%
- Time to rollback: < 5 minutes (switch back to blue)
- Test coverage: 75%
- PCI DSS compliance: Automated security scans, audit logs
- Zero high/critical vulnerabilities in production

---

## Example 3: Python FastAPI Microservices (CircleCI + Google Cloud Run)

### Context

**Company:** SaaS startup (ML platform)  
**Tech Stack:** Python 3.11, FastAPI, PostgreSQL, Redis, React frontend  
**Infrastructure:** Google Cloud Run (serverless)  
**Team:** 5 engineers  
**Current State:** Manual deployments, bi-weekly releases  
**Goal:** Automated deployments, daily releases, < 10 min pipeline

### Requirements

- **Deployment Frequency:** Daily (production), multiple per day (staging)
- **Quality Gates:** > 80% test coverage, no critical vulnerabilities
- **Rollback:** Automated, instant (Cloud Run revisions)
- **Environments:** Development, Staging, Production
- **Cost:** Minimize CI/CD costs (startup budget)

### CI/CD Strategy

#### Build Strategy

- **Build Tool:** pip with dependency caching
- **Artifacts:** Docker images (Cloud Run)
- **Versioning:** Git SHA
- **Caching:** pip cache, Docker layer caching

#### Testing Strategy

- **Unit Tests:** pytest, 800 tests, ~2 minutes
- **Integration Tests:** pytest with testcontainers, 60 tests, ~5 minutes
- **Coverage Requirement:** > 80%

#### Security Strategy

- **SAST:** Bandit (Python security linter)
- **Dependency Scanning:** Safety (Python dependency checker)
- **Container Scanning:** Trivy
- **Security Gates:** Block on critical vulnerabilities

#### Deployment Strategy

- **Method:** Rolling deployment (Cloud Run automatic)
- **Promotion Flow:** Dev (automatic) → Staging (automatic) → Production (automatic with monitoring)
- **Rollback:** Instant (Cloud Run revisions)
- **Monitoring:** Cloud Monitoring, error rate, latency

### Pipeline Implementation

#### .circleci/config.yml

```yaml
version: 2.1

orbs:
  python: circleci/python@2.1.1
  gcp-cli: circleci/gcp-cli@3.1.0

jobs:
  # Job 1: Build and Test
  build-and-test:
    docker:
      - image: cimg/python:3.11
    steps:
      - checkout
      
      - restore_cache:
          keys:
            - v1-deps-{{ checksum "requirements.txt" }}
            - v1-deps-
      
      - run:
          name: Install dependencies
          command: |
            python -m venv venv
            . venv/bin/activate
            pip install -r requirements.txt
            pip install -r requirements-dev.txt
      
      - save_cache:
          key: v1-deps-{{ checksum "requirements.txt" }}
          paths:
            - venv
      
      - run:
          name: Lint
          command: |
            . venv/bin/activate
            flake8 app tests
            black --check app tests
            mypy app
      
      - run:
          name: Security Scan (Bandit)
          command: |
            . venv/bin/activate
            bandit -r app -f json -o bandit-report.json
      
      - run:
          name: Dependency Scan (Safety)
          command: |
            . venv/bin/activate
            safety check --json > safety-report.json || true
            # Fail on critical vulnerabilities
            if grep -q '"severity": "critical"' safety-report.json; then
              echo "Critical vulnerabilities found"
              exit 1
            fi
      
      - run:
          name: Unit Tests
          command: |
            . venv/bin/activate
            pytest tests/unit --cov=app --cov-report=xml --cov-report=html
      
      - run:
          name: Check Coverage
          command: |
            . venv/bin/activate
            COVERAGE=$(python -c "import xml.etree.ElementTree as ET; tree = ET.parse('coverage.xml'); print(tree.getroot().attrib['line-rate'])")
            COVERAGE_PCT=$(python -c "print(float($COVERAGE) * 100)")
            echo "Coverage: $COVERAGE_PCT%"
            if (( $(echo "$COVERAGE_PCT < 80" | bc -l) )); then
              echo "Coverage below 80%"
              exit 1
            fi
      
      - run:
          name: Integration Tests
          command: |
            . venv/bin/activate
            pytest tests/integration
      
      - store_test_results:
          path: test-results
      
      - store_artifacts:
          path: htmlcov
  
  # Job 2: Build and Push Docker Image
  build-docker:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - setup_remote_docker:
          docker_layer_caching: true
      
      - run:
          name: Build Docker image
          command: |
            docker build -t myapp:${CIRCLE_SHA1} .
      
      - run:
          name: Container Scan (Trivy)
          command: |
            docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
              aquasec/trivy:latest image \
              --severity HIGH,CRITICAL \
              --exit-code 1 \
              myapp:${CIRCLE_SHA1}
      
      - gcp-cli/setup:
          version: latest
      
      - run:
          name: Authenticate with GCP
          command: |
            echo $GCLOUD_SERVICE_KEY | gcloud auth activate-service-account --key-file=-
            gcloud config set project $GCP_PROJECT_ID
      
      - run:
          name: Push to GCR
          command: |
            gcloud auth configure-docker
            docker tag myapp:${CIRCLE_SHA1} gcr.io/$GCP_PROJECT_ID/myapp:${CIRCLE_SHA1}
            docker push gcr.io/$GCP_PROJECT_ID/myapp:${CIRCLE_SHA1}
  
  # Job 3: Deploy to Development
  deploy-dev:
    docker:
      - image: cimg/base:stable
    steps:
      - gcp-cli/setup
      
      - run:
          name: Authenticate with GCP
          command: |
            echo $GCLOUD_SERVICE_KEY | gcloud auth activate-service-account --key-file=-
            gcloud config set project $GCP_PROJECT_ID
      
      - run:
          name: Deploy to Cloud Run (dev)
          command: |
            gcloud run deploy myapp-dev \
              --image gcr.io/$GCP_PROJECT_ID/myapp:${CIRCLE_SHA1} \
              --platform managed \
              --region us-central1 \
              --allow-unauthenticated \
              --set-env-vars "ENV=dev,DATABASE_URL=$DEV_DATABASE_URL" \
              --max-instances 5
      
      - run:
          name: Health Check
          command: |
            URL=$(gcloud run services describe myapp-dev --platform managed --region us-central1 --format 'value(status.url)')
            curl -f $URL/health
  
  # Job 4: Deploy to Staging
  deploy-staging:
    docker:
      - image: cimg/base:stable
    steps:
      - gcp-cli/setup
      
      - run:
          name: Authenticate with GCP
          command: |
            echo $GCLOUD_SERVICE_KEY | gcloud auth activate-service-account --key-file=-
            gcloud config set project $GCP_PROJECT_ID
      
      - run:
          name: Deploy to Cloud Run (staging)
          command: |
            gcloud run deploy myapp-staging \
              --image gcr.io/$GCP_PROJECT_ID/myapp:${CIRCLE_SHA1} \
              --platform managed \
              --region us-central1 \
              --allow-unauthenticated \
              --set-env-vars "ENV=staging,DATABASE_URL=$STAGING_DATABASE_URL" \
              --max-instances 10
      
      - run:
          name: Health Check
          command: |
            URL=$(gcloud run services describe myapp-staging --platform managed --region us-central1 --format 'value(status.url)')
            curl -f $URL/health
  
  # Job 5: Deploy to Production
  deploy-production:
    docker:
      - image: cimg/base:stable
    steps:
      - gcp-cli/setup
      
      - run:
          name: Authenticate with GCP
          command: |
            echo $GCLOUD_SERVICE_KEY | gcloud auth activate-service-account --key-file=-
            gcloud config set project $GCP_PROJECT_ID
      
      - run:
          name: Deploy to Cloud Run (production)
          command: |
            gcloud run deploy myapp \
              --image gcr.io/$GCP_PROJECT_ID/myapp:${CIRCLE_SHA1} \
              --platform managed \
              --region us-central1 \
              --allow-unauthenticated \
              --set-env-vars "ENV=production,DATABASE_URL=$PRODUCTION_DATABASE_URL" \
              --max-instances 50 \
              --no-traffic  # Deploy without traffic initially
      
      - run:
          name: Gradual Traffic Shift
          command: |
            # Get new revision
            NEW_REVISION=$(gcloud run revisions list --service myapp --platform managed --region us-central1 --format 'value(name)' --limit 1)
            
            # Shift 10% traffic to new revision
            gcloud run services update-traffic myapp \
              --to-revisions $NEW_REVISION=10 \
              --platform managed \
              --region us-central1
            
            # Monitor for 5 minutes
            sleep 300
            
            # Check error rate (simplified - use real monitoring)
            URL=$(gcloud run services describe myapp --platform managed --region us-central1 --format 'value(status.url)')
            STATUS=$(curl -s -o /dev/null -w '%{http_code}' $URL/health)
            
            if [ $STATUS -eq 200 ]; then
              # Shift 100% traffic
              gcloud run services update-traffic myapp \
                --to-revisions $NEW_REVISION=100 \
                --platform managed \
                --region us-central1
              echo "Deployment successful"
            else
              # Rollback
              gcloud run services update-traffic myapp \
                --to-revisions $NEW_REVISION=0 \
                --platform managed \
                --region us-central1
              echo "Deployment failed, rolled back"
              exit 1
            fi

workflows:
  build-test-deploy:
    jobs:
      - build-and-test
      - build-docker:
          requires:
            - build-and-test
          filters:
            branches:
              only:
                - main
                - develop
      - deploy-dev:
          requires:
            - build-docker
          filters:
            branches:
              only: develop
      - deploy-staging:
          requires:
            - build-docker
          filters:
            branches:
              only: main
      - hold-production:
          type: approval
          requires:
            - deploy-staging
          filters:
            branches:
              only: main
      - deploy-production:
          requires:
            - hold-production
          filters:
            branches:
              only: main
```

### Results

**Before CI/CD:**
- Deployment frequency: Bi-weekly
- Deployment time: 30 minutes (manual)
- Deployment success rate: 80%
- Time to rollback: 15-30 minutes

**After CI/CD:**
- Deployment frequency: Daily (production), 5-10 times per day (staging)
- Pipeline time: 7 minutes (CI) + 3 minutes (CD) = 10 minutes
- Deployment success rate: 99%
- Time to rollback: Instant (Cloud Run revisions)
- Test coverage: 85%
- CI/CD cost: ~$50/month (CircleCI free tier + GCP costs)

---

## Example 4: Monorepo with Multiple Services (GitHub Actions + Nx)

### Context

**Company:** B2B SaaS platform  
**Tech Stack:** TypeScript, Next.js, NestJS, PostgreSQL, Redis  
**Infrastructure:** Vercel (frontend), AWS ECS (backend)  
**Team:** 12 engineers, 2 teams  
**Architecture:** Monorepo with 3 frontend apps, 5 backend services, shared libraries  
**Current State:** Manual deployments, weekly releases  
**Goal:** Automated deployments, deploy only changed services, < 15 min pipeline

### Requirements

- **Deployment Frequency:** Multiple deployments per day
- **Selective Deployment:** Deploy only changed services (not entire monorepo)
- **Quality Gates:** > 80% test coverage, no critical vulnerabilities
- **Rollback:** Automated, per-service
- **Environments:** Development, Staging, Production

### CI/CD Strategy

#### Build Strategy

- **Build Tool:** Nx (monorepo build system)
- **Artifacts:** Docker images per service
- **Versioning:** Git SHA per service
- **Caching:** Nx computation caching, npm dependencies
- **Selective Builds:** Build only affected projects

#### Testing Strategy

- **Unit Tests:** Jest, ~5,000 tests across all projects, ~8 minutes (parallel)
- **Integration Tests:** Supertest, ~300 tests, ~10 minutes (parallel)
- **E2E Tests:** Playwright, ~50 critical flows, ~15 minutes (parallel)
- **Coverage Requirement:** > 80% per project
- **Selective Testing:** Test only affected projects

#### Security Strategy

- **SAST:** SonarQube Cloud
- **Dependency Scanning:** Snyk
- **Container Scanning:** Trivy
- **Security Gates:** Block on critical vulnerabilities

#### Deployment Strategy

- **Method:** Rolling deployment (per service)
- **Promotion Flow:** Dev (automatic) → Staging (automatic) → Production (manual approval)
- **Selective Deployment:** Deploy only changed services
- **Rollback:** Automated per service
- **Monitoring:** Datadog, per-service dashboards

### Pipeline Implementation

#### .github/workflows/ci.yml

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NX_CLOUD_ACCESS_TOKEN: ${{ secrets.NX_CLOUD_ACCESS_TOKEN }}

jobs:
  # Job 1: Determine affected projects
  affected:
    runs-on: ubuntu-latest
    outputs:
      affected-apps: ${{ steps.affected.outputs.apps }}
      affected-libs: ${{ steps.affected.outputs.libs }}
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - name: Get affected projects
        id: affected
        run: |
          AFFECTED_APPS=$(npx nx print-affected --type=app --select=projects | tr ',' '\n' | jq -R -s -c 'split("\n")[:-1]')
          AFFECTED_LIBS=$(npx nx print-affected --type=lib --select=projects | tr ',' '\n' | jq -R -s -c 'split("\n")[:-1]')
          echo "apps=$AFFECTED_APPS" >> $GITHUB_OUTPUT
          echo "libs=$AFFECTED_LIBS" >> $GITHUB_OUTPUT
          echo "Affected apps: $AFFECTED_APPS"
          echo "Affected libs: $AFFECTED_LIBS"
  
  # Job 2: Lint affected projects
  lint:
    needs: affected
    if: needs.affected.outputs.affected-apps != '[]' || needs.affected.outputs.affected-libs != '[]'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - name: Lint affected
        run: npx nx affected --target=lint --parallel=3
  
  # Job 3: Test affected projects
  test:
    needs: affected
    if: needs.affected.outputs.affected-apps != '[]' || needs.affected.outputs.affected-libs != '[]'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - name: Test affected
        run: npx nx affected --target=test --parallel=3 --coverage
      
      - name: Check coverage
        run: |
          # Check coverage for each affected project
          for project in $(npx nx print-affected --type=app,lib --select=projects | tr ',' ' '); do
            COVERAGE=$(cat coverage/$project/coverage-summary.json | jq '.total.lines.pct')
            echo "$project coverage: $COVERAGE%"
            if (( $(echo "$COVERAGE < 80" | bc -l) )); then
              echo "$project coverage below 80%"
              exit 1
            fi
          done
  
  # Job 4: Build affected projects
  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - name: Build affected
        run: npx nx affected --target=build --parallel=3
      
      - name: Security scan affected
        run: |
          npx nx affected --target=security-scan --parallel=3
  
  # Job 5: Build and push Docker images for affected backend services
  docker:
    needs: [affected, build]
    if: needs.affected.outputs.affected-apps != '[]'
    runs-on: ubuntu-latest
    strategy:
      matrix:
        app: ${{ fromJson(needs.affected.outputs.affected-apps) }}
    steps:
      - uses: actions/checkout@v3
      
      - name: Check if app needs Docker
        id: check
        run: |
          # Only build Docker for backend services (not frontend)
          if [ -f "apps/${{ matrix.app }}/Dockerfile" ]; then
            echo "needs-docker=true" >> $GITHUB_OUTPUT
          else
            echo "needs-docker=false" >> $GITHUB_OUTPUT
          fi
      
      - name: Build Docker image
        if: steps.check.outputs.needs-docker == 'true'
        run: |
          docker build -f apps/${{ matrix.app }}/Dockerfile \
            -t ${{ matrix.app }}:${{ github.sha }} .
      
      - name: Container scan
        if: steps.check.outputs.needs-docker == 'true'
        run: |
          docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy:latest image \
            --severity HIGH,CRITICAL \
            --exit-code 1 \
            ${{ matrix.app }}:${{ github.sha }}
      
      - name: Configure AWS credentials
        if: steps.check.outputs.needs-docker == 'true' && github.ref == 'refs/heads/main'
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Push to ECR
        if: steps.check.outputs.needs-docker == 'true' && github.ref == 'refs/heads/main'
        run: |
          aws ecr get-login-password --region us-east-1 | \
            docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com
          docker tag ${{ matrix.app }}:${{ github.sha }} \
            123456789012.dkr.ecr.us-east-1.amazonaws.com/${{ matrix.app }}:${{ github.sha }}
          docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/${{ matrix.app }}:${{ github.sha }}
```

#### .github/workflows/cd.yml

```yaml
name: CD

on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches: [main]

jobs:
  # Job 1: Determine affected apps
  affected:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    outputs:
      affected-apps: ${{ steps.affected.outputs.apps }}
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - run: npm ci
      
      - name: Get affected apps
        id: affected
        run: |
          AFFECTED_APPS=$(npx nx print-affected --type=app --select=projects | tr ',' '\n' | jq -R -s -c 'split("\n")[:-1]')
          echo "apps=$AFFECTED_APPS" >> $GITHUB_OUTPUT
          echo "Affected apps: $AFFECTED_APPS"
  
  # Job 2: Deploy affected frontend apps to Vercel
  deploy-frontend:
    needs: affected
    if: needs.affected.outputs.affected-apps != '[]'
    runs-on: ubuntu-latest
    strategy:
      matrix:
        app: ${{ fromJson(needs.affected.outputs.affected-apps) }}
    steps:
      - uses: actions/checkout@v3
      
      - name: Check if frontend app
        id: check
        run: |
          # Check if app is frontend (Next.js)
          if [ -f "apps/${{ matrix.app }}/next.config.js" ]; then
            echo "is-frontend=true" >> $GITHUB_OUTPUT
          else
            echo "is-frontend=false" >> $GITHUB_OUTPUT
          fi
      
      - name: Deploy to Vercel
        if: steps.check.outputs.is-frontend == 'true'
        run: |
          npx vercel deploy \
            --token ${{ secrets.VERCEL_TOKEN }} \
            --scope myorg \
            --prod \
            --cwd apps/${{ matrix.app }}
  
  # Job 3: Deploy affected backend services to ECS
  deploy-backend:
    needs: affected
    if: needs.affected.outputs.affected-apps != '[]'
    runs-on: ubuntu-latest
    strategy:
      matrix:
        app: ${{ fromJson(needs.affected.outputs.affected-apps) }}
    steps:
      - uses: actions/checkout@v3
      
      - name: Check if backend service
        id: check
        run: |
          # Check if app is backend (NestJS)
          if [ -f "apps/${{ matrix.app }}/Dockerfile" ]; then
            echo "is-backend=true" >> $GITHUB_OUTPUT
          else
            echo "is-backend=false" >> $GITHUB_OUTPUT
          fi
      
      - name: Configure AWS credentials
        if: steps.check.outputs.is-backend == 'true'
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Deploy to ECS
        if: steps.check.outputs.is-backend == 'true'
        run: |
          # Update ECS service
          aws ecs update-service \
            --cluster my-cluster \
            --service ${{ matrix.app }} \
            --force-new-deployment \
            --region us-east-1
          
          # Wait for deployment
          aws ecs wait services-stable \
            --cluster my-cluster \
            --services ${{ matrix.app }} \
            --region us-east-1
      
      - name: Health check
        if: steps.check.outputs.is-backend == 'true'
        run: |
          URL=$(aws ecs describe-services \
            --cluster my-cluster \
            --services ${{ matrix.app }} \
            --region us-east-1 \
            --query 'services[0].loadBalancers[0].targetGroupArn' \
            --output text)
          # Simplified - use actual health check endpoint
          curl -f https://${{ matrix.app }}.myapp.com/health
```

### Results

**Before CI/CD:**
- Deployment frequency: Weekly (all services)
- Deployment time: 1-2 hours (manual, all services)
- Deployment success rate: 75%
- Build time: 30+ minutes (entire monorepo)

**After CI/CD:**
- Deployment frequency: 10-15 deployments per day (selective)
- Pipeline time: 8 minutes (CI, only affected) + 5 minutes (CD, only affected) = 13 minutes
- Deployment success rate: 97%
- Build time: 3-8 minutes (only affected projects)
- Test coverage: 85% across all projects
- Cost savings: 70% reduction in CI/CD time (selective builds)

---

## Comparison Matrix

| Aspect | Example 1 (Node.js) | Example 2 (Java) | Example 3 (Python) | Example 4 (Monorepo) |
|--------|---------------------|------------------|--------------------|-----------------------|
| **Platform** | GitHub Actions | GitLab CI | CircleCI | GitHub Actions + Nx |
| **Infrastructure** | AWS EKS | AWS ECS | Google Cloud Run | Vercel + AWS ECS |
| **Deployment** | Canary | Blue/Green | Rolling | Selective (per service) |
| **Pipeline Time** | 20 min | 35 min | 10 min | 13 min (selective) |
| **Deployment Frequency** | 5-10/day | Weekly | Daily | 10-15/day |
| **Rollback Time** | < 2 min | < 5 min | Instant | < 2 min (per service) |
| **Test Coverage** | 85% | 75% | 85% | 85% |
| **Compliance** | SOC 2 | PCI DSS, SOC 2 | None | None |
| **Team Size** | 15 engineers | 8 engineers | 5 engineers | 12 engineers |
| **Cost** | ~$200/month | ~$150/month | ~$50/month | ~$300/month |

---

## Key Takeaways

### 1. Choose the Right Deployment Strategy

- **Canary:** Best for high-risk changes, gradual rollout (Example 1)
- **Blue/Green:** Best for zero-downtime, instant rollback (Example 2)
- **Rolling:** Best for simplicity, cost-effectiveness (Example 3)
- **Selective:** Best for monorepos, deploy only changed services (Example 4)

### 2. Optimize for Speed

- **Caching:** npm, Maven, Docker layers (all examples)
- **Parallelization:** Run tests in parallel (all examples)
- **Selective Builds:** Build only affected projects (Example 4)
- **Fail Fast:** Run fast tests first (all examples)

### 3. Integrate Security Early

- **SAST:** SonarQube, Bandit (all examples)
- **Dependency Scanning:** Snyk, OWASP, Safety (all examples)
- **Container Scanning:** Trivy, Clair (all examples)
- **Secrets Scanning:** TruffleHog (Example 1)

### 4. Automate Rollback

- **Automated Triggers:** Error rate, health checks (Examples 1, 3)
- **Manual Approval:** For critical systems (Example 2)
- **Instant Rollback:** Cloud Run revisions (Example 3)
- **Per-Service Rollback:** For microservices (Example 4)

### 5. Monitor Deployments

- **Health Checks:** Verify deployment success (all examples)
- **Metrics:** Error rate, latency, throughput (Examples 1, 3)
- **Notifications:** Slack, email (Example 1)
- **Audit Logs:** For compliance (Example 2)

### 6. Match CI/CD to Team Size and Maturity

- **Small Team (5 engineers):** Simple pipeline, minimal overhead (Example 3)
- **Medium Team (8-15 engineers):** Comprehensive pipeline, automation (Examples 1, 2)
- **Large Team (12+ engineers):** Monorepo, selective builds (Example 4)

---

## Additional Resources

- **GitHub Actions Examples:** https://github.com/actions/starter-workflows
- **GitLab CI Examples:** https://docs.gitlab.com/ee/ci/examples/
- **CircleCI Examples:** https://circleci.com/docs/2.0/sample-config/
- **Nx Monorepo CI:** https://nx.dev/recipes/ci
