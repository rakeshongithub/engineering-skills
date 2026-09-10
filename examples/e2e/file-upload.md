# E2E: File Upload

## Journey and Risks

A user uploads a document, observes validation and progress, and views the processed result. Risks include invalid file handling, oversized payloads, malware scanning failure, duplicate submission, and leaked private documents.

## E2E Plan

- Use generated fixtures for valid, invalid, oversized, and scan-failure files.
- Verify upload progress, cancellation, retry, validation messages, and final status.
- Assert tenant and user authorization on the result view and download path.
- Keep the real scan service in a smoke test and use controlled responses for broad failure coverage.
- Capture network and application evidence when processing remains pending.

## Quality Gates

- Test files contain no sensitive production data.
- Invalid and malicious-file responses are safe and actionable.
- A failed or retried upload cannot expose another user's file.
- Cleanup removes uploaded objects and processing records.
