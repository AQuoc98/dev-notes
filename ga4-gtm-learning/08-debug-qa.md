# 08 — Debugging Workflow and Analytics QA

## Objective

Create a concise, practical workflow for frontend developers and GTM owners to validate an approved tracking change from the authoritative application outcome through the Data Layer, GTM, consent, network request, GA4 diagnostics, and processed reporting data.

The document must help the team identify the first failing layer, record only the evidence needed for the risk, and apply a consistent path from test setup to handoff.

## Scope

### Included

- Stable web GTM and GA4 validation patterns.
- Application-to-Data-Layer contracts, variables, triggers, tags, Google tag routing, and collection-source ownership.
- GTM Preview/Tag Assistant, browser Network tools, GA4 DebugView/Realtime, and processed reports.
- Positive, negative, duplicate, consent, privacy, routing, SPA/navigation, browser, and regression checks.
- Core records: Test Run Setup Record, Required Test Matrix, Evidence Template, plus conditional Debug Session and Defect/Retest records.
- L0 expected-behavior detail as part of the Required Test Matrix, not as a separate template.
- A single worked Registration Journey example placed at the end of the answer documents.
- Matching English and Vietnamese structure and terminology.

### Excluded

- Media buying, campaign optimization, attribution strategy, and Google Ads operations.
- Building a complete automated analytics test framework.
- Fixing every defect discovered during QA.
- Redefining the Measurement Plan, consent policy, or reporting requirements.
- Treating DebugView as a replacement for processed reporting validation.
- Release approval and post-release monitoring; these remain in Section 10.

## Outputs

- [English answer](./08-debug-qa-answer.md): cleaned and reordered as Overview → Core QA records → Debug procedure → Practical guardrails/handoff → Registration Journey example → Official references.
- [Vietnamese answer](./08-debug-qa-answer-vn.md): same structure, scope, template order, and GA4/GTM terminology.
- A focused template workflow that distinguishes mandatory records from conditional investigation and defect records.
- A Registration Journey example that demonstrates how the records connect without introducing additional standalone templates.
