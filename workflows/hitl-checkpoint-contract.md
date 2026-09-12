# HITL Checkpoint Contract

Use this contract whenever a workflow needs a human to review evidence, approve a decision, or authorize an action.

## Checkpoint Schema

```yaml
checkpoint:
  id: <stable-workflow-checkpoint-id>
  trigger: <condition that pauses the workflow>
  risk_level: low | medium | high | critical
  decision_owner: <role accountable for the decision>
  required_evidence:
    - <artifact, test result, review, or operational signal>
  proposed_action: <what the workflow will do after approval>
  allowed_before_approval:
    - <read-only analysis or reversible preparation>
  prohibited_before_approval:
    - <merge, deploy, cutover, destructive action, or risk acceptance>
  decision: approve | approve_with_conditions | reject | request_changes | escalate
  rationale: <why the decision was made>
  conditions: <required follow-up controls, if any>
  expires_at: <time or event when approval is no longer valid>
  audit_reference: <ticket, ADR, change record, or incident reference>
  follow_up_owner: <role or named owner>
```

## Lifecycle

1. **Prepare:** The workflow gathers the required evidence and records its current state.
2. **Pause:** It stops all prohibited actions and presents the checkpoint to the decision owner.
3. **Decide:** The owner records a decision, rationale, conditions, expiry, and audit reference.
4. **Resume or route:** Approval resumes only the named dependent steps. Rejection or requested changes route back to the specified step; escalation routes to the accountable authority.
5. **Verify:** Conditions and follow-up actions are tracked before the checkpoint is closed.

## Minimum Rules

- Approval must come from a role with authority over the risk, not only the person executing the workflow.
- Approval is specific to the evidence and proposed action; a changed scope or expired evidence requires a new decision.
- `approve_with_conditions` is not complete until conditions have owners and due dates.
- A timeout, missing approver, or ambiguous decision is an escalation, not an approval.
- Emergency actions may use an emergency approver, but require retrospective review and an audit reference.

## Common Checkpoints

| Checkpoint                  | Typical approver                 | Blocking action                               |
| --------------------------- | -------------------------------- | --------------------------------------------- |
| Design approval             | System or domain owner           | Implementation against an unapproved design   |
| Security/data approval      | Security, privacy, or data owner | Release with unresolved control or data risk  |
| Migration cutover           | Service or business owner        | Traffic or data cutover                       |
| Release decision            | Release or service owner         | Production promotion                          |
| Risk acceptance             | Accountable risk owner           | Proceeding with an open high or critical risk |
| Generated artifact adoption | Technical reviewer               | Merging or publishing agent-generated output  |
| Incident closure            | Incident or service owner        | Closing follow-up work without evidence       |
