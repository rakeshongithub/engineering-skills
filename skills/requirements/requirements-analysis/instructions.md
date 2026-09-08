# Requirements Analysis - Step-by-Step Instructions

## Overview
This guide provides a systematic approach to analyzing and documenting requirements.

## Step 1: Gather All Available Information (30-60 minutes)

### Actions:
1. Collect all requirement sources:
   - User stories
   - Feature requests
   - Meeting notes
   - Email threads
   - Wireframes/mockups
   - Existing documentation

2. Identify stakeholders:
   - Product managers
   - Business owners
   - End users
   - Technical leads
   - Compliance/legal teams

3. Review existing system:
   - Current architecture
   - Technical constraints
   - Integration points
   - Performance baselines

### Deliverable:
- Consolidated list of all requirement sources
- Stakeholder contact list
- System context document

## Step 2: Extract and Categorize Requirements (1-2 hours)

### Actions:
1. Read through all sources and extract requirements
2. Categorize each requirement:
   - **Functional**: What the system must do
   - **Non-Functional**: Quality attributes (performance, security, etc.)
   - **User**: User experience and interaction
   - **System**: Technical capabilities
   - **Business**: Business rules and constraints

3. Use a template for each requirement:
   ```
   ID: REQ-001
   Type: Functional
   Priority: Must-Have
   Description: [Clear statement]
   Rationale: [Why it's needed]
   Acceptance Criteria: [How to verify]
   Dependencies: [Related requirements]
   ```

### Deliverable:
- Categorized requirement list
- Initial priority assignments

## Step 3: Analyze Completeness and Clarity (1-2 hours)

### Actions:
1. For each requirement, ask:
   - Is it clear and unambiguous?
   - Is it testable?
   - Is it complete (who, what, why, when, where, how)?
   - Is it necessary?
   - Is it feasible?

2. Identify gaps:
   - Missing information
   - Unclear terminology
   - Undefined edge cases
   - Missing NFRs

3. Detect conflicts:
   - Contradictory requirements
   - Competing priorities
   - Resource constraints

### Deliverable:
- Gap analysis document
- List of conflicts requiring resolution
- Clarification questions

## Step 4: Formulate Clarification Questions (30-60 minutes)

### Actions:
1. Create specific questions for each gap or ambiguity
2. Group questions by stakeholder
3. Prioritize questions (blocking vs. nice-to-know)

### Question Template:
```
Context: [Brief background]
Question: [Specific question]
Options: [If applicable, list alternatives]
Impact: [Why this matters]
Decision Needed By: [Date]
```

### Deliverable:
- Structured question list for stakeholders
- Meeting agenda for clarification session

## Step 5: Define Acceptance Criteria (1-2 hours)

### Actions:
1. For each requirement, define how success will be measured
2. Use Given-When-Then format:
   ```
   Given [initial context]
   When [action occurs]
   Then [expected outcome]
   ```

3. Include:
   - Happy path scenarios
   - Edge cases
   - Error conditions
   - Performance criteria

### Example:
```
Requirement: User can add items to shopping cart

Acceptance Criteria:
- Given a logged-in user viewing a product
  When they click "Add to Cart"
  Then the item appears in their cart with quantity 1
  And the cart count updates
  And a confirmation message displays

- Given a guest user
  When they add an item to cart
  Then the item is stored in session
  And persists for 24 hours
```

### Deliverable:
- Acceptance criteria for all requirements
- Test scenario outline

## Step 6: Document and Structure (2-3 hours)

### Actions:
1. Create requirements document with sections:
   - Executive Summary
   - Scope and Objectives
   - Functional Requirements
   - Non-Functional Requirements
   - User Requirements
   - System Requirements
   - Constraints and Assumptions
   - Out of Scope
   - Acceptance Criteria
   - Appendices

2. Create requirements traceability matrix:
   ```
   Business Goal | Requirement ID | Acceptance Criteria | Test Case
   ```

3. Add diagrams where helpful:
   - User flow diagrams
   - Data flow diagrams
   - Context diagrams

### Deliverable:
- Complete requirements document
- Traceability matrix
- Supporting diagrams

## Step 7: Review and Validate (1-2 hours)

### Actions:
1. Self-review checklist:
   - All requirements are clear and testable
   - NFRs are explicitly stated
   - Priorities are assigned
   - Conflicts are resolved
   - Assumptions are documented

2. Peer review:
   - Technical lead reviews feasibility
   - Architect reviews system implications
   - QA reviews testability

3. Stakeholder review:
   - Present to business stakeholders
   - Confirm understanding
   - Get sign-off

### Deliverable:
- Reviewed and approved requirements document
- Sign-off from key stakeholders

## Step 8: Finalize and Handoff (30 minutes)

### Actions:
1. Incorporate feedback
2. Version and baseline the document
3. Store in accessible location
4. Notify downstream teams (design, architecture, development)

### Deliverable:
- Final requirements document (v1.0)
- Handoff notification to next phase

## Tips for Success

- **Start broad, then narrow**: Gather everything first, then filter and refine
- **Be specific**: Replace vague terms with measurable criteria
- **Document assumptions**: Make implicit knowledge explicit
- **Prioritize ruthlessly**: Not everything can be a must-have
- **Iterate**: Requirements analysis is rarely one-and-done
- **Stay solution-agnostic**: Focus on the problem, not the implementation

## Common Pitfalls to Avoid

- Accepting "make it fast" without defining what "fast" means
- Skipping non-functional requirements
- Not documenting out-of-scope items
- Failing to get stakeholder sign-off
- Over-specifying implementation details
- Ignoring existing system constraints