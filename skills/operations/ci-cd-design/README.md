# CI/CD Design Skill

## Quick Reference

**Purpose**: Design continuous integration and continuous deployment pipelines that automate software delivery from code commit to production deployment with quality gates, security checks, and rollback capabilities.

**Complexity**: Intermediate to Advanced  
**Estimated Time**: 2-4 weeks for comprehensive pipeline design and implementation

## When to Use This Skill

✅ **Use when**:
- Automating software delivery processes
- Setting up new projects or migrating legacy systems
- Improving deployment frequency and reliability
- Enforcing quality gates and security standards
- Supporting multiple environments (dev, staging, production)
- Implementing DevOps practices
- Scaling development teams
- Reducing deployment risk and downtime

❌ **Don't use when**:
- Debugging existing pipelines (use troubleshooting skills)
- One-time deployments
- Purely infrastructure provisioning (use IaC skills)
- Application architecture design
- Security auditing of existing pipelines

## Key Inputs

- Application architecture and technology stack
- Infrastructure details and deployment targets
- Quality requirements and testing strategy
- Deployment strategy and rollback requirements
- Team structure and branching strategy
- Security and compliance requirements

## Key Outputs

- Pipeline architecture document with diagrams
- Detailed pipeline specifications
- CI/CD configuration files (pipeline-as-code)
- Environment configuration and infrastructure-as-code
- Quality gate definitions
- Deployment procedures and runbooks
- Security implementation (scanning, secret management)
- Team enablement materials

## 10-Step Workflow

1. **Requirements Gathering**: Stakeholder interviews, current state assessment, success criteria
2. **Pipeline Architecture**: Platform selection, stage definitions, integration planning
3. **Build Stage Design**: Triggers, environment setup, caching, artifact creation
4. **Testing Strategy**: Unit, integration, E2E tests with parallel execution
5. **Security & Quality Gates**: SAST, dependency scanning, container scanning, code quality
6. **Artifact Management**: Repository setup, versioning, publishing, retention
7. **Deployment Automation**: Strategy selection, environment config, rollback procedures
8. **Monitoring Integration**: Pipeline metrics, deployment tracking, validation, alerting
9. **Documentation & Training**: Developer guides, troubleshooting, runbooks, training
10. **Validation & Improvement**: End-to-end testing, performance benchmarks, continuous improvement

## Quick Decision Framework

### Platform Selection

- **Jenkins**: Maximum flexibility, on-premises, complex workflows
- **GitLab CI**: Integrated platform, GitOps, built-in security
- **GitHub Actions**: GitHub integration, easy to use, cloud-native
- **CircleCI**: Fast builds, Docker support, managed service
- **AWS CodePipeline**: AWS-native, managed service, AWS integration

### Deployment Strategy

- **Blue-Green**: Zero downtime, instant rollback, duplicate infrastructure
- **Canary**: Gradual rollout, real traffic testing, risk mitigation
- **Rolling**: Incremental updates, no duplicate infrastructure, slower rollback
- **Recreate**: Simplest approach, downtime acceptable, no version mixing

## Common Pitfalls to Avoid

1. ❌ Overly complex pipelines
2. ❌ Insufficient environment parity
3. ❌ Ignoring pipeline performance
4. ❌ Inadequate rollback strategy
5. ❌ Poor secret management
6. ❌ Skipping quality gates
7. ❌ Manual steps in deployment
8. ❌ Lack of monitoring integration
9. ❌ No pipeline maintenance plan
10. ❌ Insufficient documentation

## Success Metrics

**Deployment Metrics**:
- Deployment frequency: Daily or more
- Lead time: < 1 day from commit to production
- MTTR: < 1 hour
- Change failure rate: < 15%

**Pipeline Metrics**:
- Build success rate: > 90%
- Pipeline execution time: < 30 minutes
- Test coverage: > 80%
- Security scan coverage: 100%

**Quality Metrics**:
- Production incidents from deployments: < 5%
- Rollback rate: < 10%
- Critical vulnerabilities in production: 0

## Related Skills

- Infrastructure as Code
- Container Orchestration
- Monitoring and Observability
- Security Scanning
- Test Automation
- Configuration Management
- Secret Management

## Quick Start

1. Read [SKILL.md](SKILL.md) for comprehensive documentation
2. Follow [instructions.md](instructions.md) for step-by-step guidance
3. Review [examples.md](examples.md) for real-world implementations
4. Check [skill.json](skill.json) for metadata and relationships

## Examples

See [examples.md](examples.md) for detailed examples:

1. **E-Commerce Platform**: Microservices with canary deployments
2. **Banking Application**: Compliance-focused with blue-green deployment
3. **Multi-Cloud SaaS**: Progressive delivery across AWS, Azure, GCP
4. **AI/ML Pipeline**: Model training and deployment automation

## Additional Resources

- **Tools**: Jenkins, GitLab CI, GitHub Actions, CircleCI, Spinnaker, ArgoCD
- **Concepts**: GitOps, Progressive Delivery, Feature Flags, Canary Analysis
- **Standards**: DORA metrics, DevOps best practices, Security scanning

---

**Version**: 1.0.0  
**Category**: Operations  
**Last Updated**: 2024