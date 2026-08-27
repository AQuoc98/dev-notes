# Subtask 08: Create a Debugging Workflow and Analytics QA Plan

## Objective

Create a repeatable, evidence-based workflow that verifies tracking from the user interaction through the Data Layer, GTM evaluation, network request, GA4 DebugView, and processed reporting data before and after release.

## Scope — Included Items

- GTM Preview/Tag Assistant, browser developer tools, network requests, GA4 DebugView, Realtime, and processed-report checks.
- Positive, negative, boundary, duplicate, SPA/navigation, consent, routing, and regression cases.
- Diagnosis of missing, duplicated, misnamed, mistimed, blocked, or misrouted events.
- Test evidence, defect severity, pre-publish gates, post-publish smoke testing, and reviewer sign-off.
- One completed sample QA report for a representative event flow.

## Scope — Excluded Items

- Building a complete automated analytics test framework.
- Fixing every defect discovered during the audit.
- Treating DebugView as a replacement for processed reporting validation.

## Deliverables / Outputs

- One end-to-end debugging playbook with a layer-by-layer decision tree.
- One reusable analytics QA checklist and test-case matrix.
- One evidence template covering Data Layer, GTM, network, consent, DebugView, and reports.
- One defect severity and triage guide.
- One completed sample test report with positive, negative, duplicate, consent, and routing cases.

## Dependencies

- Measurement plan, Data Layer contract, naming conventions, and selected event flow.
- Test environment, safe test data, browser developer tools, GTM Preview/Tag Assistant, and GA4 DebugView access.
- Correct GA4 property, web stream, Measurement ID, and GTM workspace/version.
- Release owner and QA reviewer.

## Instructions / Answer

See [08-debug-qa-answer.md](./08-debug-qa-answer.md).
