# CI/CD Design - Comprehensive Examples

This document provides four detailed, real-world examples of CI/CD pipeline design covering diverse scenarios and industries.

## Example 1: E-Commerce Platform - Microservices CI/CD

[See SKILL.md Example 1 for complete details]

**Summary**: Microservices platform with 12 services, GitHub Actions for CI, ArgoCD for GitOps-based CD, canary deployments with automated analysis, and Istio service mesh for traffic management.

**Key Highlights**:
- Deployment frequency: Multiple times per day
- Zero-downtime deployments with canary strategy
- Automated rollback on failed canary analysis
- 99.97% uptime achieved
- MTTR reduced from 2 hours to 15 minutes

---

## Example 2: Banking Application - Compliance-Focused CI/CD

[See SKILL.md Example 2 for complete details]

**Summary**: Monolithic Java application with strict regulatory requirements (SOC2, PCI-DSS), Jenkins for self-hosted CI/CD, comprehensive audit logging, separation of duties, and blue-green deployment.

**Key Highlights**:
- Complete audit trail for all changes
- Mandatory security and CAB approval gates
- Blue-green deployment for instant rollback
- Deployment time reduced from 3 weeks to 4 hours
- Zero security incidents related to deployment

---

## Example 3: Multi-Cloud SaaS Platform - Progressive Delivery

[See SKILL.md Example 3 for complete details]

**Summary**: 30 microservices deployed across AWS, Azure, and GCP, GitHub Actions for CI, Spinnaker for multi-cloud CD, progressive delivery with feature flags, and unified monitoring with Datadog.

**Key Highlights**:
- Deployment frequency: 50+ times per day
- Single pipeline deploying to 3 cloud providers
- 99.95% deployment success rate
- Automated canary analysis with Kayenta
- Feature flags for safe experimentation

---

## Example 4: AI/ML Model Deployment Pipeline

[See SKILL.md Example 4 for complete details]

**Summary**: Machine learning model training and deployment pipeline with MLflow for model registry, Kubeflow for training, AWS SageMaker for serving, canary deployments with performance monitoring, and drift detection.

**Key Highlights**:
- Model deployment time reduced from days to hours
- Complete model lineage tracking
- Automated model conversion for multiple formats
- Canary deployments with automated performance monitoring
- 95% reduction in deployment errors

---

## Additional Example Scenarios

### Example 5: Mobile Application CI/CD

**Context**: iOS and Android mobile applications with React Native codebase.

**Pipeline Design**:

```yaml
# .github/workflows/mobile-ci-cd.yml
name: Mobile CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Unit tests
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
  
  build-ios:
    needs: test
    runs-on: macos-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Install CocoaPods
        run: |
          cd ios
          pod install
      
      - name: Build iOS app
        run: |
          cd ios
          xcodebuild -workspace MyApp.xcworkspace \
            -scheme MyApp \
            -configuration Release \
            -archivePath MyApp.xcarchive \
            archive
      
      - name: Export IPA
        run: |
          cd ios
          xcodebuild -exportArchive \
            -archivePath MyApp.xcarchive \
            -exportPath build \
            -exportOptionsPlist ExportOptions.plist
      
      - name: Upload to TestFlight
        uses: apple-actions/upload-testflight-build@v1
        with:
          app-path: ios/build/MyApp.ipa
          issuer-id: ${{ secrets.APPSTORE_ISSUER_ID }}
          api-key-id: ${{ secrets.APPSTORE_API_KEY_ID }}
          api-private-key: ${{ secrets.APPSTORE_API_PRIVATE_KEY }}
  
  build-android:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Set up JDK
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: '17'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build Android app
        run: |
          cd android
          ./gradlew assembleRelease
      
      - name: Sign APK
        uses: r0adkll/sign-android-release@v1
        with:
          releaseDirectory: android/app/build/outputs/apk/release
          signingKeyBase64: ${{ secrets.ANDROID_SIGNING_KEY }}
          alias: ${{ secrets.ANDROID_KEY_ALIAS }}
          keyStorePassword: ${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
          keyPassword: ${{ secrets.ANDROID_KEY_PASSWORD }}
      
      - name: Upload to Google Play (Internal Testing)
        uses: r0adkll/upload-google-play@v1
        with:
          serviceAccountJsonPlainText: ${{ secrets.GOOGLE_PLAY_SERVICE_ACCOUNT }}
          packageName: com.company.myapp
          releaseFiles: android/app/build/outputs/apk/release/app-release-signed.apk
          track: internal
```

**Key Features**:
- Separate builds for iOS and Android
- Automated upload to TestFlight and Google Play
- Code signing integrated into pipeline
- Internal testing track for validation

---

### Example 6: Infrastructure as Code CI/CD

**Context**: Terraform infrastructure management with automated testing and deployment.

**Pipeline Design**:

```yaml
# .github/workflows/terraform-ci-cd.yml
name: Terraform CI/CD

on:
  push:
    branches: [main]
    paths:
      - 'terraform/**'
  pull_request:
    branches: [main]
    paths:
      - 'terraform/**'

env:
  TF_VERSION: '1.5.0'
  AWS_REGION: us-east-1

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: ${{ env.TF_VERSION }}
      
      - name: Terraform Format Check
        run: terraform fmt -check -recursive
        working-directory: terraform
      
      - name: Terraform Init
        run: terraform init -backend=false
        working-directory: terraform
      
      - name: Terraform Validate
        run: terraform validate
        working-directory: terraform
      
      - name: TFLint
        uses: terraform-linters/setup-tflint@v3
      
      - name: Run TFLint
        run: tflint --recursive
        working-directory: terraform
  
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Run tfsec
        uses: aquasecurity/tfsec-action@v1.0.0
        with:
          working_directory: terraform
          soft_fail: false
      
      - name: Run Checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: terraform
          framework: terraform
          soft_fail: false
  
  plan:
    needs: [validate, security-scan]
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: ${{ env.TF_VERSION }}
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}
      
      - name: Terraform Init
        run: terraform init
        working-directory: terraform
      
      - name: Terraform Plan
        id: plan
        run: |
          terraform plan -out=tfplan -no-color
          terraform show -json tfplan > plan.json
        working-directory: terraform
      
      - name: Upload plan
        uses: actions/upload-artifact@v3
        with:
          name: terraform-plan
          path: terraform/tfplan
      
      - name: Comment PR with plan
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const plan = fs.readFileSync('terraform/plan.json', 'utf8');
            const output = `#### Terraform Plan 📖
            
            <details><summary>Show Plan</summary>
            
            \`\`\`json
            ${plan}
            \`\`\`
            
            </details>`;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: output
            });
  
  apply:
    needs: plan
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Terraform
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: ${{ env.TF_VERSION }}
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}
      
      - name: Terraform Init
        run: terraform init
        working-directory: terraform
      
      - name: Download plan
        uses: actions/download-artifact@v3
        with:
          name: terraform-plan
          path: terraform
      
      - name: Terraform Apply
        run: terraform apply -auto-approve tfplan
        working-directory: terraform
      
      - name: Terraform Output
        id: output
        run: terraform output -json > outputs.json
        working-directory: terraform
      
      - name: Upload outputs
        uses: actions/upload-artifact@v3
        with:
          name: terraform-outputs
          path: terraform/outputs.json
```

**Key Features**:
- Terraform validation and security scanning
- Plan preview in pull requests
- Manual approval for production apply
- Artifact storage for plans and outputs

---

### Example 7: Monorepo CI/CD with Affected Detection

**Context**: Monorepo with multiple applications and libraries, only building and deploying affected projects.

**Pipeline Design**:

```yaml
# .github/workflows/monorepo-ci-cd.yml
name: Monorepo CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  detect-changes:
    runs-on: ubuntu-latest
    outputs:
      affected: ${{ steps.affected.outputs.projects }}
    
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install Nx
        run: npm install -g nx
      
      - name: Detect affected projects
        id: affected
        run: |
          if [ "${{ github.event_name }}" == "pull_request" ]; then
            BASE="origin/${{ github.base_ref }}"
          else
            BASE="HEAD~1"
          fi
          
          AFFECTED=$(nx affected:apps --base=$BASE --head=HEAD --plain)
          echo "Affected projects: $AFFECTED"
          echo "projects=$AFFECTED" >> $GITHUB_OUTPUT
  
  build-and-test:
    needs: detect-changes
    runs-on: ubuntu-latest
    if: needs.detect-changes.outputs.affected != ''
    
    strategy:
      matrix:
        project: ${{ fromJson(needs.detect-changes.outputs.affected) }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint ${{ matrix.project }}
        run: nx lint ${{ matrix.project }}
      
      - name: Test ${{ matrix.project }}
        run: nx test ${{ matrix.project }} --coverage
      
      - name: Build ${{ matrix.project }}
        run: nx build ${{ matrix.project }} --prod
      
      - name: Upload build artifact
        uses: actions/upload-artifact@v3
        with:
          name: ${{ matrix.project }}-build
          path: dist/apps/${{ matrix.project }}
  
  deploy:
    needs: [detect-changes, build-and-test]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && needs.detect-changes.outputs.affected != ''
    
    strategy:
      matrix:
        project: ${{ fromJson(needs.detect-changes.outputs.affected) }}
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Download build artifact
        uses: actions/download-artifact@v3
        with:
          name: ${{ matrix.project }}-build
          path: dist/apps/${{ matrix.project }}
      
      - name: Deploy ${{ matrix.project }}
        run: |
          echo "Deploying ${{ matrix.project }}"
          # Deployment logic here
```

**Key Features**:
- Affected project detection with Nx
- Only build and test changed projects
- Parallel builds for affected projects
- Independent deployments per project

---

### Example 8: Serverless Application CI/CD

**Context**: AWS Lambda functions with API Gateway, deployed using Serverless Framework.

**Pipeline Design**:

```yaml
# .github/workflows/serverless-ci-cd.yml
name: Serverless CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  AWS_REGION: us-east-1

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      dynamodb:
        image: amazon/dynamodb-local
        ports:
          - 8000:8000
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Unit tests
        run: npm run test:unit -- --coverage
      
      - name: Integration tests
        env:
          DYNAMODB_ENDPOINT: http://localhost:8000
        run: npm run test:integration
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
  
  security:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Run Snyk security scan
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          command: test
          args: --severity-threshold=high
      
      - name: Scan for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.repository.default_branch }}
          head: HEAD
  
  deploy-dev:
    needs: [test, security]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/develop'
    environment:
      name: development
      url: https://dev-api.myapp.com
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}
      
      - name: Deploy to dev
        run: npx serverless deploy --stage dev --verbose
      
      - name: Run smoke tests
        run: npm run test:smoke -- --baseUrl=https://dev-api.myapp.com
  
  deploy-prod:
    needs: [test, security]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://api.myapp.com
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
      
      - name: Install dependencies
        run: npm ci
      
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ env.AWS_REGION }}
      
      - name: Deploy to production
        run: npx serverless deploy --stage prod --verbose
      
      - name: Run smoke tests
        run: npm run test:smoke -- --baseUrl=https://api.myapp.com
      
      - name: Monitor deployment
        run: |
          # Monitor CloudWatch metrics for 10 minutes
          python scripts/monitor-lambda.py \
            --function-name myapp-prod \
            --duration 600 \
            --error-threshold 0.01
```

**Key Features**:
- Local DynamoDB for integration testing
- Serverless Framework deployment
- Environment-specific deployments
- Post-deployment monitoring

---

## Comparison Matrix

| Example | Industry | Architecture | CI Platform | CD Platform | Deployment Strategy | Key Feature |
|---------|----------|--------------|-------------|-------------|---------------------|-------------|
| 1. E-Commerce | Retail | Microservices | GitHub Actions | ArgoCD | Canary | GitOps with automated analysis |
| 2. Banking | Finance | Monolith | Jenkins | Ansible | Blue-Green | Compliance and audit trails |
| 3. SaaS | Technology | Microservices | GitHub Actions | Spinnaker | Canary | Multi-cloud deployment |
| 4. AI/ML | Technology | ML Pipeline | GitHub Actions | SageMaker | Canary | Model versioning and drift detection |
| 5. Mobile | Consumer | Mobile App | GitHub Actions | TestFlight/Play | Staged Rollout | App store deployment |
| 6. IaC | Infrastructure | Terraform | GitHub Actions | Terraform | Plan/Apply | Infrastructure validation |
| 7. Monorepo | Technology | Monorepo | GitHub Actions | Custom | Independent | Affected detection |
| 8. Serverless | Technology | Serverless | GitHub Actions | Serverless Framework | Direct | Lambda deployment |

---

## Lessons Learned Across Examples

### Common Success Patterns

1. **Pipeline as Code**: All examples use declarative pipeline definitions
2. **Automated Testing**: Comprehensive test automation at multiple levels
3. **Security Integration**: Security scanning integrated into every pipeline
4. **Progressive Delivery**: Gradual rollouts reduce deployment risk
5. **Monitoring Integration**: Deployment verification through monitoring
6. **Rollback Capability**: Quick rollback mechanisms for all deployments
7. **Environment Parity**: Consistent environments across dev/staging/prod
8. **Documentation**: Comprehensive documentation for all pipelines

### Common Challenges and Solutions

1. **Challenge**: Slow pipeline execution
   - **Solution**: Caching, parallelization, incremental builds

2. **Challenge**: Flaky tests
   - **Solution**: Test isolation, retry mechanisms, flaky test detection

3. **Challenge**: Secret management
   - **Solution**: Dedicated secret management tools (Vault, cloud KMS)

4. **Challenge**: Deployment failures
   - **Solution**: Automated rollback, canary deployments, health checks

5. **Challenge**: Multi-environment complexity
   - **Solution**: Environment-specific configurations, infrastructure as code

6. **Challenge**: Compliance requirements
   - **Solution**: Audit logging, approval workflows, separation of duties

7. **Challenge**: Cost optimization
   - **Solution**: Resource pooling, caching, efficient resource usage

8. **Challenge**: Team adoption
   - **Solution**: Training, documentation, gradual rollout, feedback loops