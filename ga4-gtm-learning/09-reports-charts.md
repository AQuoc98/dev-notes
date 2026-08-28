# Subtask 09: Design, Build, and Interpret GA4 Reports and Charts

## Objective

Define a practical workflow for turning validated GA4 data into reports, charts, and Explorations that answer a clear business question and support a documented decision.

## Scope — Included Items

- Shared terminology: **population** (who/what is included), **grain** (what one row or count represents), **scope** (user/session/event/item level), and **field readiness**.
- GA4 Reports: detail reports, overview reports, summary cards, collections, customization, publishing, sharing, and export.
- Explorations: free-form, funnel, path, cohort, and other suitable investigation techniques.
- Dimension/metric selection, filters, comparisons, segments, denominators, chart choice, interpretation, and ownership.
- QA and limitations: processing delay, data quality, thresholding, sampling, incompatible fields, attribution/identity, and `(other)`/cardinality effects.

## Scope — Excluded Items

- A complete reporting suite for every stakeholder.
- External BI dashboards such as Looker Studio or Power BI.
- Business conclusions unsupported by validated tracking data or an agreed analytical question.

## Workflow

1. Define the audience, business question, decision, cadence, and owner.
2. Define the population, grain, date range, filters, comparison/segment, numerator, and denominator.
3. Confirm field readiness: collected, registered when required, processed, compatible, safe, and approved for the question.
4. Choose the GA4 surface: Reports for recurring monitoring; Explorations for flexible investigation.
5. Select dimensions, metrics, and chart types that match the analytical task.
6. Build, publish, share, or export the asset; document its configuration and maintenance trigger.
7. QA the result using Data Layer, network, DebugView, processed data, and the GA4 Data quality indicator. Record limitations and interpretation.

## Deliverables / Outputs

- One report-requirements matrix containing audience, question, decision, population, grain, dimensions, metrics, filters, GA4 area, cadence, and owner.
- One field-readiness inventory for the dimensions and metrics used.
- One reusable detail report and one fit-for-purpose Exploration based on validated data.
- Chart specification and interpretation note for the published analysis.
- One QA/configuration record covering data quality, freshness, identity, attribution, privacy, sampling, cardinality, discrepancies, sharing, and maintenance.

## Dependencies

- Completed Measurement Plan and validated event flow from Sections 01–08.
- Correct GA4 property and web stream, sufficient processed data, and approved business question.
- GA4 Editor or Administrator access for report customization/publishing, or an approved operator with that access.
- Stakeholder agreement on the decision, reporting cadence, owner, and source-of-truth surface.

## Instructions / Answer

See [09-reports-charts-answer.md](./09-reports-charts-answer.md).
