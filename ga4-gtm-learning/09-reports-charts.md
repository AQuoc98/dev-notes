# 09 — GA4 Reports, Explorations, Charts, and Interpretation

## Objective

Turn an approved measurement requirement into a reproducible GA4 Report or Exploration that answers one business question and supports a documented decision.

## Scope

- Population, grain, scope, dimensions, metrics, formulas, filters, charts, ownership, and maintenance.
- GA4 Detail/Overview Reports, summary cards, Free-form/Funnel/Path Explorations, sharing, and export.
- Rate design: cohort, event sequence, completion window, user metric, and consistent numerator/denominator.
- Separation of user-level reporting from event-level collection QA.
- Field readiness, data quality, missing/invalid values, consent/identity context, and access/publish controls.
- Canonical records: Report Requirement, Field Readiness, Asset Configuration (including chart/table configuration), and Interpretation/Decision.

## Outputs

1. A workflow from question → population/grain/scope → field readiness → GA4 surface → formula → chart → interpretation.
2. A canonical record set for each maintained Report or Exploration.
3. A Registration example with a cohort, completion window, consistent user metric, and method on both sides of the rate.
4. Separate user-level reporting and event-level QA, with runtime evidence referenced from Section 08.
5. Synchronized English and Vietnamese answer documents.

## Acceptance criteria

- The asset has an approved requirement, audience, owner, population, grain, scope, date range, and decision.
- Fields are collected, registered when needed, processed, compatible, privacy-safe, and approved.
- A cross-event rate defines the cohort, event sequence, completion window, user metric, and matching numerator/denominator rules.
- The implementation path is explicit: native Detail Report, Funnel Exploration, or approved export/BigQuery fallback.
- Chart/table configuration, freshness, thresholding, sampling, cardinality, consent, identity, and limitations are recorded.
- `(not set)`, `Unassigned`, and invalid values are handled explicitly and their impact is documented.
- Runtime collection evidence comes from Section 08; a Report or chart does not replace Debug/QA.
- Access, publish responsibility, maintenance trigger, and review owner are recorded.

## Out of scope

Event and collection design is covered in Section 07. Detailed Data Layer, GTM, consent, Network, and DebugView validation is covered in Section 08. Production release monitoring is covered in Section 10. Ads, campaign optimization, attribution operations, and external BI dashboards are excluded.

## Source

- Detailed English implementation: [09-reports-charts-answer.md](./09-reports-charts-answer.md).
- Vietnamese implementation: [09-reports-charts-answer-vn.md](./09-reports-charts-answer-vn.md).
