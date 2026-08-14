# 07 — GA4 Reports and Charts

## Theory: Start with the Decision

A report is useful only when it connects data to a decision. Define this chain before opening GA4:

```text
Audience → business question → decision → dimensions + metrics
         → GA4 surface → chart/table → validation → interpretation
```

Example:

| Audience | Question | Decision | Dimension | Metrics |
| --- | --- | --- | --- | --- |
| Product owner | Where do users abandon registration? | Prioritize a journey step | Event name / error type | Users, event count, key events |
| QA/analytics | Is successful registration recorded once? | Approve or fix tracking | Date, platform, form ID | Event count, users |

## Core Reporting Knowledge

### Dimensions, metrics, and scope

- A **dimension** describes or groups data, such as event name, device category, or form ID.
- A **metric** measures data, such as users, sessions, event count, or key events.
- **Scope** identifies what a value describes: user, session, event, or item. Similar-looking fields from different scopes are not interchangeable.
- A collected event parameter is not automatically a reusable report dimension. Prefer a standard GA4 field; otherwise register an appropriate custom definition and allow time for new data to become reportable.

### Filters, comparisons, and segments

- A **filter** restricts the rows included in a report or Exploration.
- A **comparison** displays subsets side by side in Reports.
- A **segment** defines a reusable subset of users, sessions, or events within Explorations.

Write each condition in plain language and record whether it is inclusive or exclusive. Similar conditions can answer different questions when their scope differs.

### Reports versus Explorations

| Surface | Best use | Strength | Important caution |
| --- | --- | --- | --- |
| Standard/detail report | Repeated monitoring by a broad audience | Governed and discoverable navigation | Less analytical flexibility |
| Customized detail report | A stable recurring question | Saved dimensions, metrics, filters, and charts | Requires suitable permissions and publishing |
| Exploration | Investigation and segmentation | Flexible tables, segments, filters, and visualizations | Results can differ from Reports because of processing and feature behavior |

Use a detail report for a recurring shared view. Use an Exploration to investigate why something happened, compare cohorts, or test analytical cuts before promoting a stable view.

## Chart Selection Guide

| Analytical task | Preferred view | Avoid/misuse to watch for |
| --- | --- | --- |
| Change over time | Line chart | Treating incomplete recent periods as final |
| Compare categories | Bar chart | Too many categories or truncated context |
| Exact values / multiple measures | Table | Expecting a table alone to reveal a pattern |
| Composition with few categories | Donut/pie chart | Many slices or values that do not form a meaningful whole |
| Relationship between two numeric measures | Scatter plot, when available | Claiming causation from correlation |
| Journey progression | Funnel Exploration | Treating event counts as users or ignoring open/closed funnel rules |

Every visualization needs a question-led title, date range, units, filters/segments, and a note about incomplete or limited data. Keep a table available when exact values matter.

## Planning Templates

### Report requirements matrix

| ID | Audience | Business question | Decision | Frequency | Dimensions | Metrics | Filter/segment | Surface | Owner |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| R-01 | Product | Where does registration fail? | Select improvement | Weekly | Event name, error type | Users, event count | Registration events | Detail report | `[name]` |
| R-02 | Analytics | Does method affect completion? | Investigate tracking/journey | Ad hoc | Method | Users, key events | Registration users | Exploration | `[name]` |

### Field-readiness matrix

| Field | Meaning | Source | Scope | Standard/custom | Registered date | Expected availability | Cardinality/privacy risk |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `form_id` | Stable form identifier | Event parameter | Event | Custom dimension if required | YYYY-MM-DD | New data after processing | Low if controlled vocabulary |
| `method` | Registration method | Recommended parameter | Event | Verify available GA4 field | N/A | After processing | Low if controlled vocabulary |

Do not register identifiers with many unique values, timestamps, full URLs containing personal data, or free text as custom dimensions.

## Execution Process

### 1. Define and review the questions

- [ ] Name the audience, decision, owner, cadence, and 2–3 business questions.
- [ ] Define every dimension and metric in business language.
- [ ] Confirm the required scope and grain: one row/value per user, session, event, or item.
- [ ] Agree on the date range, timezone, key-event definition, attribution context, and success criteria.

### 2. Confirm report readiness

- [ ] Verify the event and parameters in the tracking plan, request evidence, and DebugView.
- [ ] Prefer standard dimensions and metrics.
- [ ] Register only necessary custom dimensions/metrics with correct scope and description.
- [ ] Record registration dates; do not expect a new custom definition to backfill historical data.
- [ ] Wait for normal processing before judging standard report results.

### 3. Build the reusable detail report

- [ ] Open **Reports → Library**, create a detail report from a suitable template or blank report.
- [ ] Add only dimensions and metrics required by R-01.
- [ ] Apply and document any saved filter.
- [ ] Select charts that match the analytical task and retain a useful table.
- [ ] Save with audience/purpose in the title and add it to the correct collection if approved.
- [ ] Confirm intended viewers can find and understand it.

Suggested POC report:

```text
Title: Product — Registration health
Primary dimension: Event name
Optional dimensions: Form ID, error type, device category
Metrics: Users, event count, key events
Filter: Event name matches approved registration events
Charts: Time series for trend + bar chart for event/error comparison
```

### 4. Build the free-form Exploration

- [ ] Open **Explore → Free form** and name the exploration with its question and owner.
- [ ] Import only necessary dimensions, metrics, and segments.
- [ ] Configure rows, columns, values, filters, and segment comparisons.
- [ ] Choose a visualization based on the question, not decoration.
- [ ] Add tabs only when each answers a distinct follow-up question.
- [ ] Share according to property governance and record limitations.

Suggested POC Exploration:

```text
Question: How do registration outcomes differ by method and device?
Rows: Method
Columns: Device category
Values: Users, event count, key events
Filter: Registration event family only
Views: Table/heat map for exact comparison; line chart tab for trend
```

### 5. QA and interpret

- [ ] Compare a controlled test period with tracking-plan and network/DebugView evidence.
- [ ] Check timezone, date range, filters, comparisons, segments, identity, and metric definitions.
- [ ] Check whether the newest data is still processing.
- [ ] Look for thresholding indicators, sampled Exploration results, or an `(other)` row.
- [ ] Review high-cardinality fields and incompatible dimension/metric combinations.
- [ ] Record whether attribution or reporting identity affects the result.
- [ ] Check totals and trends for plausibility; explain, do not silently dismiss, discrepancies.
- [ ] Have a stakeholder answer the original question using only the report and its notes.

## Interpretation Note Template

```text
Business question:
Decision supported:
Property / report / Exploration:
Date range and property timezone:
Population, filters, comparisons, and segments:
Dimensions and metrics (with scope):
Observed result:
Interpretation:
What this does not prove:
Freshness / thresholding / sampling / cardinality / attribution notes:
Tracking or data-quality caveats:
Owner and next review date:
```

## Definition of Done

- [ ] Requirements and field-readiness matrices are reviewed.
- [ ] One detail report and one free-form Exploration answer named questions.
- [ ] Chart choices and calculations are understandable without verbal explanation.
- [ ] Custom definitions, filters, comparisons, and segments are documented.
- [ ] Results have been reconciled with controlled evidence after processing.
- [ ] Relevant reporting limitations and privacy risks are recorded.
- [ ] Intended users can access the deliverables and explain the supported decision.
- [ ] An owner and review/retirement trigger are assigned.

## Official References

- [Create a detail report](https://support.google.com/analytics/answer/13844077)
- [Customize detail reports and charts](https://support.google.com/analytics/answer/10445879)
- [Understand detail reports](https://support.google.com/analytics/answer/10659476)
- [Free-form Explorations](https://support.google.com/analytics/answer/9327972)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
