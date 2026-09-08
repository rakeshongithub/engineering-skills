# Requirements Analysis

## Purpose

Transform vague, incomplete, or ambiguous requirements into clear, actionable engineering requirements that can be implemented.

## When to Use

- Starting a new feature or project with unclear requirements
- Stakeholders provide high-level business goals without technical details
- Requirements are scattered across multiple sources (emails, meetings, documents)
- Need to bridge the gap between business language and engineering specifications
- Before beginning system design or architecture work
- When requirements seem contradictory or incomplete

## When NOT to Use

- Requirements are already well-defined and technically detailed
- Working on a bug fix with clear reproduction steps
- Implementing a well-documented API or standard
- Requirements are purely technical (use technical-specification instead)
- During code review or refactoring activities

## Inputs

- Business requirements (user stories, feature requests, business goals)
- Stakeholder conversations and meeting notes
- Existing documentation (PRDs, business cases, wireframes)
- User feedback or support tickets
- Competitive analysis or market research
- Constraints (budget, timeline, technical limitations)
- Existing system architecture and capabilities

## Expected Outputs

- **Structured Requirements Document** containing:
  - Functional requirements (what the system must do)
  - Non-functional requirements (performance, security, scalability)
  - User requirements (who will use it and how)
  - System requirements (technical capabilities needed)
  - Constraints and assumptions
  - Success criteria and acceptance criteria
  - Out-of-scope items (what will NOT be included)
- **Clarification Questions** for stakeholders
- **Requirements Traceability Matrix** linking business goals to technical requirements
- **Risk Assessment** of unclear or conflicting requirements

## Workflow

### 1. Gather Information
- Collect all available requirement sources
- Identify all stakeholders and their perspectives
- Review existing system documentation
- Understand business context and goals

### 2. Extract Core Requirements
- Identify explicit requirements (clearly stated)
- Identify implicit requirements (assumed but not stated)
- Separate functional from non-functional requirements
- Distinguish "must-have" from "nice-to-have"

### 3. Analyze and Structure
- Group related requirements by domain/feature
- Identify dependencies between requirements
- Detect conflicts or contradictions
- Assess completeness and clarity

### 4. Clarify Ambiguities
- List unclear or missing information
- Formulate specific questions for stakeholders
- Identify assumptions that need validation
- Document areas requiring further investigation

### 5. Define Acceptance Criteria
- Convert requirements into testable criteria
- Define success metrics
- Establish quality standards
- Specify edge cases and error scenarios

### 6. Document and Validate
- Create structured requirements document
- Review with stakeholders for accuracy
- Ensure technical feasibility
- Get sign-off on finalized requirements

## Decision Framework

### Requirement Completeness Check
- **Who**: Is the user/actor clearly identified?
- **What**: Is the desired functionality clearly described?
- **Why**: Is the business value or purpose explained?
- **When**: Are timing or sequencing requirements specified?
- **Where**: Is the context or environment defined?
- **How**: Are quality standards and constraints specified?

### Requirement Quality Assessment
- **Clear**: Can it be understood without ambiguity?
- **Testable**: Can success be objectively verified?
- **Feasible**: Is it technically and economically viable?
- **Necessary**: Does it support a business goal?
- **Prioritized**: Is its relative importance defined?
- **Traceable**: Can it be linked to business objectives?

### Conflict Resolution
- Identify stakeholder with decision authority
- Analyze business impact of each option
- Assess technical implications and costs
- Document the decision and rationale
- Update requirements accordingly

## Quality Checklist

- [ ] All functional requirements are clearly stated
- [ ] Non-functional requirements (NFRs) are explicitly defined
- [ ] Each requirement has clear acceptance criteria
- [ ] Requirements are prioritized (must-have, should-have, nice-to-have)
- [ ] Conflicts and contradictions are resolved
- [ ] Assumptions are documented
- [ ] Out-of-scope items are explicitly listed
- [ ] Success metrics are defined
- [ ] Requirements are traceable to business goals
- [ ] Technical feasibility has been assessed
- [ ] Security and compliance requirements are identified
- [ ] Performance and scalability requirements are specified
- [ ] User experience requirements are captured
- [ ] Integration requirements are documented
- [ ] Data requirements are defined
- [ ] Error handling and edge cases are considered

## Common Mistakes

1. **Accepting vague requirements without clarification**
   - Don't assume you understand stakeholder intent
   - Always ask for specific examples and scenarios

2. **Mixing requirements with implementation details**
   - Focus on WHAT is needed, not HOW it will be built
   - Keep requirements solution-agnostic when possible

3. **Ignoring non-functional requirements**
   - Performance, security, and scalability are critical
   - Don't wait until implementation to discover NFRs

4. **Failing to prioritize**
   - Not all requirements are equally important
   - Use MoSCoW (Must, Should, Could, Won't) or similar framework

5. **Not documenting assumptions**
   - Implicit assumptions lead to misunderstandings
   - Make all assumptions explicit and validate them

6. **Skipping stakeholder validation**
   - Requirements analysis is collaborative
   - Get feedback early and often

7. **Over-engineering requirements**
   - Don't add requirements stakeholders didn't ask for
   - Stick to stated needs and validated assumptions

8. **Ignoring constraints**
   - Budget, timeline, and technical constraints are requirements too
   - Document and communicate limitations clearly

## Examples

### Example 1: E-commerce Feature Request

**Input**: "We need a shopping cart feature."

**Analysis Output**:

**Functional Requirements**:
- Users can add products to cart
- Users can update quantities in cart
- Users can remove items from cart
- Cart persists across sessions
- Cart shows real-time pricing and availability
- Cart supports discount codes

**Non-Functional Requirements**:
- Cart operations complete within 200ms
- Support 10,000 concurrent users
- 99.9% availability
- PCI DSS compliance for payment data

**Clarification Questions**:
- Should guests have carts, or only logged-in users?
- How long should abandoned carts be retained?
- Should cart sync across devices?
- What happens to cart items if product becomes unavailable?
- Are there quantity limits per item or per cart?

**Acceptance Criteria**:
- User can add item to cart and see updated count
- Cart total updates when quantities change
- Cart persists after logout and login
- Out-of-stock items show appropriate message
- Discount codes apply correctly to eligible items

### Example 2: API Performance Requirement

**Input**: "The API needs to be fast."

**Analysis Output**:

**Clarified Requirements**:
- 95th percentile response time < 100ms for read operations
- 95th percentile response time < 500ms for write operations
- Support 1,000 requests per second per endpoint
- Graceful degradation under load (return cached data if needed)

**Acceptance Criteria**:
- Load tests demonstrate sustained 1,000 RPS
- P95 latency meets targets under normal load
- API returns appropriate errors when rate limited
- Monitoring alerts trigger if P95 exceeds thresholds

### Example 3: Security Requirement

**Input**: "Make it secure."

**Analysis Output**:

**Specific Requirements**:
- Authentication: OAuth 2.0 with JWT tokens
- Authorization: Role-based access control (RBAC)
- Data encryption: TLS 1.3 in transit, AES-256 at rest
- Session management: 30-minute timeout, secure cookies
- Input validation: Sanitize all user inputs
- Audit logging: Log all authentication and authorization events
- Compliance: GDPR, SOC 2 Type II

**Acceptance Criteria**:
- All endpoints require valid authentication
- Users can only access resources they're authorized for
- Sensitive data is encrypted in database
- Failed login attempts are logged and rate-limited
- Security scan shows no high/critical vulnerabilities

## Related Skills

### Prerequisites
- None (this is typically a starting skill)

### Commonly Followed By
- `requirement-clarification` - For deeper investigation of ambiguities
- `system-design` - To design system architecture based on requirements
- `architecture-decision` - For making technology choices
- `technical-specification` - To create detailed implementation specs
- `tradeoff-analysis` - When requirements conflict or have competing solutions

### Works With
- `architecture-discovery` - Understanding existing system capabilities
- `technical-feasibility` - Validating requirement viability
- `risk-analysis` - Identifying requirement-related risks

### Alternative To
- `requirement-clarification` - More focused on resolving ambiguities
- `technical-specification` - More implementation-focused

## Skill Composition

Typical workflow combining this skill:

```
Business Request
       ↓
requirements-analysis (this skill)
       ↓
requirement-clarification (if needed)
       ↓
system-design
       ↓
architecture-review
       ↓
technical-specification
```

## Evaluation Criteria

### Quality Indicators
- Requirements are unambiguous and testable
- All stakeholders agree on documented requirements
- No major gaps or missing information
- NFRs are explicitly defined
- Acceptance criteria are clear and measurable

### Red Flags
- Vague language ("fast", "secure", "user-friendly" without specifics)
- Missing NFRs (performance, security, scalability)
- No prioritization
- Conflicting requirements not resolved
- Assumptions not documented
- No acceptance criteria

### Success Metrics
- Engineering team can start design without further clarification
- Requirements are stable (minimal changes during implementation)
- Acceptance criteria can be directly converted to tests
- Stakeholders approve the documented requirements
- No major requirement gaps discovered during implementation