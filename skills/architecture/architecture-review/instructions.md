# Architecture Review - Step-by-Step Instructions

## Overview

This skill helps you systematically evaluate an existing architecture to identify quality issues, risks, and improvement opportunities.

## Step-by-Step Workflow

### Step 1: Understand the Context (30-60 minutes)

**Actions:**
1. Review business requirements and goals
2. Understand user needs and usage patterns
3. Identify scale requirements (current and projected)
4. Note compliance and regulatory requirements
5. Understand team structure and capabilities

**Outputs:**
- Context document summarizing business goals, scale, and constraints

### Step 2: Gather Architecture Artifacts (1-2 hours)

**Actions:**
1. Collect architecture diagrams (system, component, deployment)
2. Review Architecture Decision Records (ADRs)
3. Examine technical design documents
4. Review API specifications and data models
5. Inspect source code repository structure
6. Review infrastructure-as-code
7. Collect performance metrics and availability data

**Outputs:**
- Organized collection of all architecture artifacts

### Step 3: Review Architectural Qualities (2-4 hours)

**Actions:**
1. **Scalability**: Assess ability to handle 10x load, identify bottlenecks
2. **Reliability**: Review uptime, failure handling, redundancy
3. **Performance**: Analyze response times, caching, database optimization
4. **Security**: Check authentication, authorization, encryption, vulnerabilities
5. **Maintainability**: Assess documentation, modularity, technical debt
6. **Cost Efficiency**: Review resource utilization and optimization opportunities

**Outputs:**
- Quality assessment for each architectural attribute

### Step 4: Identify Issues and Risks (1-2 hours)

**Actions:**
1. Document all findings
2. Categorize by severity (Critical, High, Medium, Low)
3. Gather supporting evidence (metrics, code examples)
4. Assess impact and likelihood

**Outputs:**
- Categorized list of issues with evidence

### Step 5: Develop Recommendations (2-4 hours)

**Actions:**
1. For each finding, define:
   - Problem statement
   - Recommended solution
   - Alternative approaches
   - Effort estimate (Small/Medium/Large)
   - Priority (Must-fix/Should-fix/Nice-to-fix)
2. Ensure recommendations are specific and actionable

**Outputs:**
- Prioritized recommendations with effort estimates

### Step 6: Create Action Plan (1-2 hours)

**Actions:**
1. Group recommendations by timeline:
   - Immediate (0-3 months)
   - Short-term (3-6 months)
   - Long-term (6-12 months)
2. Identify dependencies between recommendations
3. Create realistic roadmap

**Outputs:**
- Time-bound action plan

### Step 7: Document and Present Findings (2-3 hours)

**Actions:**
1. Write executive summary (1 page)
2. Document detailed findings
3. Create or update architecture diagrams
4. Prepare presentation for stakeholders
5. Review with technical team for validation

**Outputs:**
- Complete architecture review report
- Updated architecture diagrams
- Presentation deck

## Tips for Success

- **Be objective**: Focus on facts and data, not opinions
- **Be specific**: Vague findings like "improve performance" are not helpful
- **Prioritize ruthlessly**: Not everything needs to be fixed immediately
- **Consider constraints**: Recommendations must be realistic given budget and timeline
- **Validate findings**: Discuss with the team before finalizing
- **Focus on impact**: Prioritize high-impact, reasonable-effort improvements

## Common Pitfalls to Avoid

- Reviewing only documentation without examining code or metrics
- Recommending a complete rewrite instead of incremental improvements
- Ignoring business context and focusing only on technology
- Making claims without supporting data
- Not prioritizing findings
- Creating recommendations that are too vague to act on