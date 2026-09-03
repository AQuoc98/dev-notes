# 08 — Debugging Workflow and Analytics QA

## Objective

Provide a practical workflow for validating an approved GA4/GTM change from the authoritative Application outcome through the Data Layer, GTM, consent, browser Network request, GA4 diagnostics, and processed data when required.

The workflow must identify the first failing layer, use the smallest sufficient test scope, and keep QA evidence safe and traceable.

## Scope

- Stable web GTM and GA4 validation.
- Application/Data Layer, Variables, Triggers, Tags, Google tag routing, consent, and collection-source ownership.
- GTM Preview/Tag Assistant, browser Network tools, GA4 DebugView/Realtime, and processed-data checks when a business decision depends on them.
- Happy path, failure, duplicate, retry, refresh/remount, consent, privacy, routing, SPA, browser, and regression cases.
- QA records: Test Run Setup, Data Safety Check, Required Test Matrix, Scenario Execution Summary, and Evidence Template.
- Conditional Debug Session and Defect/Retest records.
- One worked Registration Journey at the end of the detailed answer documents.

The validation path is:

Application state → Data Layer → GTM evaluation → consent → Network request → DebugView/Realtime → processed result when required.

## Outputs

1. A clear distinction between what Application, Data Layer, GTM, Network, DebugView, and processed data can prove.
2. A record-priority workflow: Test Run Setup → Data Safety Check → Required Test Matrix → Scenario Execution Summary → targeted Evidence → conditional investigation/retest.
3. Test matrices covering positive, negative, duplicate, consent, routing, privacy, SPA/browser, and regression behavior as applicable.
4. A material-event pass rule requiring evidence for business state, event/request count, payload, destination, consent, and relevant downstream result.
5. A first-failing-layer diagnosis table and a practical frontend-to-GTM handoff.
6. English and Vietnamese answer documents with the same QA record structure and terminology.

## Acceptance criteria

- The expected business moment, event, count, required parameters, destination, consent state, and negative cases come from the approved Measurement Plan.
- Test Run Setup and Data Safety Check are complete before execution or evidence export.
- One valid business occurrence produces the documented number of messages and requests; retries, refreshes, remounts, and duplicate sources are handled.
- GTM Preview confirms the intended Trigger/Tag evaluation, Network confirms the request and destination, and DebugView/Realtime confirms recent receipt when applicable.
- Processed-data validation is marked Pending only while the documented GA4 processing window is still open, with an owner and follow-up date.
- Evidence contains no PII, secrets, credentials, raw form input, or unapproved request tokens.
- A defect is closed only after the original scenario and relevant regression cases pass.

## Out of scope

Measurement design and event contracts are covered in Section 07. Detailed Data Layer, Variable, Trigger, Tag, Consent, and Template configuration is covered in Sections 01–06. Reports/Charts design is covered in Section 09. Production release approval and post-release monitoring are covered in Section 10. Ads, campaign optimization, attribution, and Google Ads operations are excluded.

## Source

- Detailed English implementation: [08-debug-qa-answer.md](./08-debug-qa-answer.md).
- Vietnamese implementation: [08-debug-qa-answer-vn.md](./08-debug-qa-answer-vn.md).
