# Subtask 07: Configure and Interpret GA4 Reports and Charts

## Objective

Learn how GA4 reporting works and turn validated tracking data into a reusable report and an analysis-oriented Exploration that answer agreed business questions accurately.

## Scope

- Reporting concepts: dimensions, metrics, scope, date ranges, filters, comparisons, segments, and key events.
- Report readiness: standard versus custom definitions and whether collected parameters are available for reporting.
- GA4 Reports workspace: detail reports, charts, summary cards, collections, and publishing.
- GA4 Explorations: free-form tables and suitable visualizations for deeper analysis.
- Chart selection, interpretation, documentation, sharing, and report QA.
- Common limitations including processing delay, data freshness, thresholding, sampling, cardinality, attribution, and incompatible fields.

## Work Items

1. Define 2–3 business questions, audiences, decisions, dimensions, metrics, and success criteria.
2. Audit whether the validated POC data is report-ready; create only necessary custom definitions.
3. Map each question to the correct GA4 surface: standard report, customized detail report, or Exploration.
4. Create one reusable detail report with relevant dimensions, metrics, filters, and charts.
5. Create one free-form Exploration for deeper analysis with appropriate segments, filters, and visualization.
6. Validate report results against DebugView/network evidence and a controlled test dataset after processing.
7. Document interpretation, caveats, ownership, sharing, and maintenance.

## Deliverables / Outputs

- Report requirements and field-mapping matrix.
- Report-readiness/custom-definition inventory.
- One configured GA4 detail report and one free-form Exploration.
- Chart selection and interpretation guide.
- Report QA record with sanitized evidence and known limitations.

## Expected Result

The team can move from a business question to a trustworthy GA4 report or Exploration, choose a chart that fits the analytical task, and explain what the result does and does not prove.

## Acceptance Criteria

- [ ] Every report starts with a named audience, business question, decision, and owner.
- [ ] Dimensions and metrics are compatible and use the intended user, session, event, or item scope.
- [ ] Custom definitions are created only when a standard field cannot meet the need.
- [ ] One reusable detail report is configured, saved, and made discoverable to its audience.
- [ ] One free-form Exploration answers a deeper question using documented filters or segments.
- [ ] Each chart type is justified by the relationship being shown; titles, date range, units, and filters are clear.
- [ ] Results are validated after processing and discrepancies are explained or recorded.
- [ ] Data freshness, thresholding, sampling, cardinality, attribution, and privacy risks are checked where relevant.
- [ ] Evidence, interpretation notes, owner, review date, and maintenance trigger are documented.

## Dependencies

- Validated output from the POC and its tracking plan.
- Access to the correct GA4 property and sufficient historical/test data.
- GA4 Editor or Administrator access for creating/customizing reports, or support from someone with that role.
- Stakeholder agreement on the business questions and decisions.

## Estimated Effort

**10 hours** — requirements/theory 2h, readiness and field mapping 2h, report/Exploration setup 3h, QA and interpretation 2h, review/documentation 1h.

## Instructions / Answer

See [07-ga4-reports-and-charts-answer.md](./07-ga4-reports-and-charts-answer.md).
