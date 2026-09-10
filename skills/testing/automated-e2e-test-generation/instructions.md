# Automated E2E Test Generation and Maintenance Instructions

1. Define user intent, risk, scope, and change boundary.
2. Gather approved context while excluding secrets and unrelated data.
3. Generate or propose tests using semantic selectors and existing fixtures.
4. Validate user-visible assertions and preserve failure coverage.
5. Run targeted tests, reliability checks, visual or contract checks as relevant.
6. Present the patch, evidence, limitations, and uncovered cases for human review.
7. Monitor generated tests after merge and improve generation rules.

Automation may propose changes; it must not decide that changed behavior is correct.
