**Status:** Research source complete; final DOCX/PPTX pending — **Last Updated:** 14 August 2026

#### Table of Contents

- [Learning map](#learning-map)
- [Operational scenarios](#operational-scenarios)
- [Final artifact checklist](#final-artifact-checklist)

## Learning Map

1. [Data Layer design](./01-data-layer-design-answer.md)
2. [GTM variable management](./02-variable-management-answer.md)
3. [GA4 event and parameter naming](./03-event-parameter-naming-answer.md)
4. [Debugging and analytics QA](./04-debugging-analytics-qa-answer.md)
5. [Tracking-flow audit](./05-tracking-flow-audit-answer.md)
6. [Proof of concept](./06-proof-of-concept-answer.md)
7. [GA4 reports and charts](./07-ga4-reports-and-charts-answer.md)
8. [GTM tag management](./08-tag-management-answer.md)
9. [GTM folder organization](./09-folder-organization-answer.md)
10. [GTM template governance](./10-template-governance-answer.md)
11. [GTM trigger management](./11-trigger-management-answer.md)
12. [GA4 operations and scenarios](./12-ga4-operations-and-scenarios-answer.md)

```text
Business question → tracking plan → application/Data Layer
→ GTM variables + trigger + tag → consent-aware request
→ GA4 processing → reporting/Exploration/export → decision and monitoring
```

## Operational Scenarios

The final document and presentation should cover applicable examples for:

- Traditional navigation and SPA routing.
- Enhanced measurement versus custom tracking and duplicate prevention.
- Cross-domain journeys and unwanted third-party referrals.
- Internal/developer traffic and irreversible filter activation.
- Campaign UTMs and advertising auto-tagging.
- User-ID login, logout, and account switching.
- Ecommerce purchase/refund reconciliation and transaction deduplication.
- Offline/server events through Measurement Protocol.
- Consent, privacy, retention, and deletion.
- Missing, duplicated, malformed, misrouted, or delayed data.
- Reports versus Explorations, Data API, and BigQuery export.

## Final Artifact Checklist

- [ ] GA4 and GTM terms are accurate; tags, triggers, variables, folders, and templates are identified as GTM concepts.
- [ ] Architecture separates business meaning, transport, processing, and reporting.
- [ ] Each scenario states when to use it, configuration location, owner, tests, risks, and evidence.
- [ ] Examples contain no real IDs, credentials, User-IDs, PII, or production payloads.
- [ ] UI paths and limits are rechecked against current official documentation.
- [ ] The presentation links to the detailed runbook instead of copying it verbatim.
- [ ] Both artifacts share terminology, example journey, decisions, and sources.
- [ ] DOCX and PPTX are rendered and visually inspected before delivery.
