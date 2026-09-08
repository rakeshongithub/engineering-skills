# Requirements Analysis - Detailed Instructions

## Overview

This guide provides step-by-step instructions for conducting thorough requirements analysis.

## Step 1: Preparation

### Gather Materials
- Collect all existing documentation
- Identify stakeholder contacts
- Set up a requirements document template
- Schedule stakeholder meetings if needed

### Set Context
- Understand the business domain
- Review existing system (if applicable)
- Identify related projects or dependencies
- Note any regulatory or compliance requirements

## Step 2: Stakeholder Engagement

### Identify Stakeholders
- **Primary stakeholders**: Those directly affected by the solution
- **Secondary stakeholders**: Those indirectly affected
- **Decision makers**: Those who approve requirements
- **Subject matter experts**: Those with domain knowledge

### Conduct Interviews

**Questions to ask:**
- What problem are you trying to solve?
- Who experiences this problem?
- What does success look like?
- What are your biggest concerns?
- What constraints do we need to consider?
- What happens if we don't solve this?

**Interview tips:**
- Listen more than you talk
- Ask "why" to understand root causes
- Ask for examples and scenarios
- Probe vague statements for specifics
- Take detailed notes

## Step 3: Requirements Extraction

### Functional Requirements

**Template:**
```
FR-[ID]: [Actor] shall be able to [action] [object] [condition]

Example:
FR-1: Users shall be able to reset their password via email
FR-2: The system shall send a notification when payment fails
FR-3: Administrators shall be able to view audit logs for the past 90 days
```

**Categories to consider:**
- User actions and workflows
- System behaviors and rules
- Data processing and transformations
- Integration requirements
- Reporting and analytics
- Administration and configuration

### Non-Functional Requirements

**Performance:**
```
NFR-P-[ID]: [Metric] shall be [value] under [conditions]

Example:
NFR-P-1: API response time shall be < 200ms for 95th percentile
NFR-P-2: System shall support 10,000 concurrent users
NFR-P-3: Database queries shall complete in < 100ms
```

**Security:**
```
NFR-S-[ID]: [Security requirement]

Example:
NFR-S-1: All data in transit shall be encrypted using TLS 1.3
NFR-S-2: Passwords shall be hashed using bcrypt with cost factor 12
NFR-S-3: API shall implement rate limiting of 100 requests/minute per user
```

**Scalability:**
```
NFR-SC-[ID]: [Scalability requirement]

Example:
NFR-SC-1: System shall scale horizontally to handle 10x current load
NFR-SC-2: Database shall support up to 100M records
```

**Reliability:**
```
NFR-R-[ID]: [Reliability requirement]

Example:
NFR-R-1: System shall have 99.9% uptime (< 8.76 hours downtime/year)
NFR-R-2: Data shall be backed up every 6 hours with 30-day retention
NFR-R-3: System shall recover from failures within 5 minutes
```

**Usability:**
```
NFR-U-[ID]: [Usability requirement]

Example:
NFR-U-1: UI shall be accessible (WCAG 2.1 Level AA)
NFR-U-2: System shall support Chrome, Firefox, Safari, Edge (latest 2 versions)
NFR-U-3: Mobile app shall work offline with data sync when online
```

## Step 4: Acceptance Criteria

### Format: Given-When-Then

```
Given [initial context]
When [action occurs]
Then [expected outcome]
```

**Example for FR-1 (Password Reset):**
```
AC-1.1:
Given a user has forgotten their password
When they click "Forgot Password" and enter their email
Then they receive a password reset link via email within 2 minutes

AC-1.2:
Given a user receives a password reset link
When they click the link within 24 hours
Then they can set a new password

AC-1.3:
Given a password reset link is older than 24 hours
When a user clicks the link
Then they see an error message and can request a new link
```

### Make Criteria Testable

**Good (testable):**
- API response time < 200ms for 95% of requests
- Password must contain at least 8 characters, 1 uppercase, 1 number
- User receives email confirmation within 5 minutes

**Bad (not testable):**
- System should be fast
- Password should be secure
- User should receive confirmation quickly

## Step 5: Assumptions and Constraints

### Document Assumptions

```
A-[ID]: [Assumption] - [Impact if wrong]

Example:
A-1: Users have access to email - If wrong, need alternative authentication
A-2: Third-party API is available 99.9% of time - If wrong, need fallback
A-3: Users are familiar with similar systems - If wrong, need more onboarding
```

### Document Constraints

**Technical constraints:**
- Must integrate with existing authentication system
- Must use company-approved cloud provider
- Must support existing database schema

**Business constraints:**
- Budget: $X
- Timeline: Y weeks
- Team size: Z engineers

**Regulatory constraints:**
- Must comply with GDPR
- Must meet SOC 2 requirements
- Must follow industry-specific regulations

## Step 6: Open Questions

### Track Unknowns

```
Q-[ID]: [Question] | Owner: [Who can answer] | Priority: [H/M/L] | Status: [Open/Answered]

Example:
Q-1: What is the expected data retention period? | Owner: Legal | Priority: H | Status: Open
Q-2: Should we support social login (Google, Facebook)? | Owner: Product | Priority: M | Status: Open
Q-3: What is the expected growth rate? | Owner: Business | Priority: H | Status: Open
```

### Prioritize Questions

**High priority:**
- Blocks design or implementation
- Affects architecture decisions
- Has significant cost/timeline impact

**Medium priority:**
- Affects implementation details
- Impacts user experience
- Influences technology choices

**Low priority:**
- Nice-to-know information
- Doesn't block progress
- Can be decided later

## Step 7: Validation

### Review with Stakeholders

**Prepare review:**
- Organize requirements by category
- Highlight key decisions and tradeoffs
- Prepare questions for clarification
- Estimate effort for each requirement (rough)

**During review:**
- Walk through each requirement
- Confirm understanding is correct
- Clarify any ambiguities
- Discuss priorities
- Get feedback on scope

**After review:**
- Update requirements based on feedback
- Resolve open questions
- Get formal sign-off
- Communicate final requirements to team

### Validation Checklist

- [ ] All stakeholders have reviewed requirements
- [ ] Requirements are clear and unambiguous
- [ ] Acceptance criteria are defined and testable
- [ ] Priorities are agreed upon
- [ ] Scope is clearly defined
- [ ] Constraints are documented
- [ ] Assumptions are validated
- [ ] Open questions are tracked
- [ ] Requirements are feasible within constraints
- [ ] Sign-off obtained from decision makers

## Step 8: Documentation

### Requirements Document Structure

```
1. Executive Summary
   - Problem statement
   - Goals and objectives
   - Success criteria

2. Stakeholders
   - Primary stakeholders
   - Secondary stakeholders
   - Decision makers

3. Functional Requirements
   - Organized by feature or user workflow
   - Each with acceptance criteria

4. Non-Functional Requirements
   - Performance
   - Security
   - Scalability
   - Reliability
   - Usability
   - Maintainability

5. Constraints
   - Technical constraints
   - Business constraints
   - Regulatory constraints

6. Assumptions
   - Listed with impact analysis

7. Open Questions
   - Tracked with owners and priorities

8. Scope
   - In scope
   - Out of scope
   - Future considerations

9. Dependencies
   - External dependencies
   - Internal dependencies

10. Appendix
    - Glossary
    - References
    - Supporting documentation
```

## Tips for Success

### Communication
- Use clear, simple language
- Avoid technical jargon when talking to non-technical stakeholders
- Use examples and scenarios to illustrate requirements
- Visualize workflows with diagrams when helpful

### Collaboration
- Involve stakeholders early and often
- Build consensus on priorities
- Be transparent about tradeoffs
- Document decisions and rationale

### Quality
- Be specific, not vague
- Be measurable, not subjective
- Be realistic, not aspirational
- Be complete, not partial

### Iteration
- Requirements evolve - that's normal
- Update documentation as understanding improves
- Communicate changes to all stakeholders
- Maintain version history

## Common Pitfalls

### Pitfall 1: Analysis Paralysis
**Problem**: Spending too much time analyzing without making progress
**Solution**: Set a time box, focus on must-have requirements first, iterate

### Pitfall 2: Premature Design
**Problem**: Jumping to solutions before understanding requirements
**Solution**: Focus on "what" not "how", separate requirements from design

### Pitfall 3: Scope Creep
**Problem**: Requirements keep expanding without control
**Solution**: Clearly define scope, use prioritization, defer nice-to-haves

### Pitfall 4: Missing Stakeholders
**Problem**: Not involving key stakeholders early
**Solution**: Identify all stakeholders upfront, engage them throughout

### Pitfall 5: Vague Requirements
**Problem**: Requirements that are open to interpretation
**Solution**: Use specific, measurable language, define acceptance criteria

## Next Steps

After completing requirements analysis:

1. **System Design**: Design the system architecture
2. **Architecture Review**: Validate the architecture
3. **Technical Specification**: Create detailed implementation specs
4. **Estimation**: Estimate effort and timeline
5. **Planning**: Create implementation plan
