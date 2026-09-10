# Banking: Legacy Payment Modernization

## Scenario

A bank needs to replace a nightly mainframe payment batch with incrementally deployable services while preserving settlement correctness, auditability, and regulatory reporting.

## Context and Constraints

- Core payment processing runs on a mainframe with COBOL batch jobs
- A relational reporting database is updated through overnight extracts
- Payment volume is 4 million records per day with strict reconciliation
- No uncontrolled change to settlement logic is permitted
- The first release must coexist with the legacy batch for one quarter
- Recovery point objective is 15 minutes; recovery time objective is 1 hour

## Recommended Workflow

Use [legacy-modernization.md](../../workflows/legacy-modernization.md):

1. `architecture-discovery` maps batch dependencies, file contracts, data ownership, and operational runbooks.
2. `technical-debt-analysis` ranks obsolete interfaces, manual controls, and fragile jobs by business impact.
3. `data-architecture-review` defines canonical payment records, lineage, retention, and reconciliation boundaries.
4. `tradeoff-analysis` compares strangler, parallel-run, and replacement strategies.
5. `migration-planning` defines a pilot, dual processing, reconciliation, cutover, and rollback.
6. `testing-strategy` adds characterization, golden-file, property, and end-to-end settlement tests.
7. `reliability-analysis`, `security-architecture-review`, and `backup-recovery` validate controls during coexistence.
8. `production-readiness` gates each migration increment.

## Migration Shape

1. Capture legacy inputs and outputs without changing behavior.
2. Implement a read-only calculation service and compare results against the mainframe.
3. Run both paths for a bounded cohort and reconcile every payment and aggregate.
4. Promote processing for the cohort only after reconciliation and recovery drills pass.
5. Expand by payment type or region, retaining a tested rollback path.

## Expected Outputs

- As-is dependency and data-flow map
- Target architecture and coexistence boundaries
- Decision record for the selected migration strategy
- Reconciliation specification and discrepancy procedure
- Phased migration plan with entry, exit, and rollback criteria

## Quality Gates

- Every migrated record has traceable lineage and an audit trail.
- Legacy and modern outputs reconcile within approved tolerances.
- Duplicate processing and partial failure behavior are tested.
- Rollback preserves settlement correctness and reporting continuity.
- Regulatory, security, recovery, and operational controls remain effective throughout the transition.
