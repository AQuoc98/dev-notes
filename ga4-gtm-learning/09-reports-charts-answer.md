# 09 — GA4 Reports, Explorations, Charts, and Interpretation

## Purpose

A report is not a collection of attractive charts. It is a repeatable answer to a business question. Build the question and decision first, then choose the GA4 surface, dimensions, metrics, filters, segments, chart, validation method, and owner.

Use this chain:

```text
Audience
  → business question
  → decision
  → population and scope
  → dimensions + metrics
  → Report or Exploration
  → chart/table
  → QA and limitations
  → interpretation and action
```

## Core Reporting Concepts

### Dimensions, metrics, and scope

| Concept       | Meaning                                        | Example                                 | Common mistake                                                           |
| ------------- | ---------------------------------------------- | --------------------------------------- | ------------------------------------------------------------------------ |
| Dimension     | Describes or groups data                       | Event name, device category, `form_id`  | Treating a label as a numeric measure                                    |
| Metric        | Numeric measurement or calculation             | Users, event count, key events, revenue | Comparing metrics with different denominators                            |
| User scope    | Describes a user across activity               | User ID, user property                  | Interpreting a user value as an event value                              |
| Session scope | Describes a visit/session                      | Session source/medium                   | Mixing session and user acquisition questions                            |
| Event scope   | Describes one event occurrence                 | Event name, event parameter             | Assuming event count equals users or conversions                         |
| Item scope    | Describes a product/item in an ecommerce array | Item name, item category                | Combining item-level data with event-level totals without checking grain |

Always write the grain of the question:

```text
How many users completed registration?
→ user-level denominator, key event count may not be the same as users.

How many registration events were sent?
→ event-level count and duplicate behavior are relevant.
```

If a metric and dimension are incompatible, GA4 may disable the combination or return a result that does not answer the intended question. Do not force a chart when the underlying scope is wrong.

### Reports versus Explorations

| Surface                   | Best use                                 | Strength                                                     | Caution                                                     |
| ------------------------- | ---------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| Reports snapshot/overview | High-level monitoring                    | Discoverable summary for broad audiences                     | Limited detail and analytical flexibility                   |
| Detail report             | Recurring, governed operational question | Saved dimensions, metrics, charts, and table                 | Requires suitable configuration and publishing access       |
| Free-form Exploration     | Flexible comparison and investigation    | Rows, columns, values, segments, filters, and visualizations | Can be easy to misread; configuration must be documented    |
| Funnel exploration        | Step-by-step progression                 | Compare completion/drop-off through defined steps            | User/event counting and open/closed funnel settings matter  |
| Path exploration          | Discover next/previous behavior          | Visualizes journeys and loops                                | It is exploratory, not proof of causation                   |
| Cohort exploration        | Retention or repeated behavior           | Compare groups over time                                     | Requires a meaningful cohort definition and sufficient data |

Google describes a detail report as a report with two charts and a table; Explorations provide more advanced techniques and flexible analysis. See [GA4 detail reports](https://support.google.com/analytics/answer/10659476) and [get started with Explorations](https://support.google.com/analytics/answer/7579450).

### Filters, comparisons, and segments

- **Filter:** restricts the data shown by a condition.
- **Comparison:** shows report subsets side by side in the Reports workspace.
- **Segment:** defines a reusable subset of users, sessions, or events in Explorations.

Write the condition in plain language and record its scope:

```text
Include users who completed sign_up
versus
Include events where event_name = sign_up
```

These can produce different results. Never use a user segment when the question is about event delivery volume, or an event filter when the decision requires unique users, without documenting the difference.

## Report Design Workflow

### Step 1 — Define the question and decision

Example:

```text
Audience: Product owner
Question: Which registration method has the lowest completion rate?
Decision: Prioritize a method-specific UX investigation.
Cadence: Weekly
Owner: Product analytics
```

The report title should make the purpose clear:

```text
Product — Registration health by method
Analytics — Registration event delivery QA
```

Avoid titles such as `Dashboard 1`, `Test`, or `All Events`.

### Step 2 — Define the population and grain

Record:

- date range and property timezone;
- user/session/event/item population;
- inclusion/exclusion conditions;
- comparison periods or segments;
- identity setting/reporting identity if relevant;
- attribution model and traffic-source scope if relevant;
- key-event definition and deduplication assumption;
- expected data freshness.

### Step 3 — Confirm field readiness

Before building a report:

1. Verify that the event and parameter are present in the measurement plan and DebugView.
2. Prefer a standard dimension/metric when it carries the correct meaning and scope.
3. Confirm that the event parameter is actually collected.
4. Register a custom dimension or metric only when it is necessary for approved analysis.
5. Record registration date and expected availability.
6. Check cardinality, privacy, allowed values, and quota.
7. Test the field in an appropriate report or Exploration after processing.

Google notes that custom dimensions and metrics are created from collected custom data and may take 24–48 hours to become available for reporting/advertising. The exact current behavior and limits should be checked in the property and official documentation. See [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

### Field-readiness inventory

**Purpose:** Use this inventory to track whether a dimension or metric is ready for reporting. It separates “the parameter is being collected” from “the field is registered, processed, compatible, and safe to use in a report.”

| Field             | Meaning                | Source              | Scope                      | Standard/custom    | Registration date | Expected availability  | Risk/notes            |
| ----------------- | ---------------------- | ------------------- | -------------------------- | ------------------ | ----------------- | ---------------------- | --------------------- |
| `event_name`      | Canonical event        | GA4 event           | Event                      | Standard           | N/A               | Standard processing    | Stable                |
| `method`          | Registration method    | `sign_up` parameter | Event                      | Custom if required | YYYY-MM-DD        | After processing delay | Controlled values     |
| `form_id`         | Stable form identifier | `sign_up` parameter | Event                      | Custom if required | YYYY-MM-DD        | After processing delay | Avoid free text       |
| `device_category` | Device category        | GA4 collection      | User/session/event context | Standard           | N/A               | Standard processing    | Scope must be checked |

### Step 4 — Choose the surface

Use a **detail report** when the same audience will repeatedly monitor a stable question. Use an **Exploration** when the analyst needs to compare segments, test hypotheses, inspect a funnel/path, or explore a question that is not yet stable.

Do not turn every ad hoc Exploration into a published report. Promote it only after the question, definitions, filters, and ownership are stable.

### Step 5 — Choose dimensions and metrics

Choose the smallest set that answers the question:

```text
Question: Are sign-ups recorded once by method and device?
Dimensions: method, device category, date
Metrics: users, event count, key events
Filter: approved registration event family
```

For rates, document numerator and denominator. For example:

```text
Completion rate = users with sign_up / users with registration_start
```

Do not call `sign_up event count / page views` a completion rate unless that is explicitly the agreed business definition. Avoid mixing event counts with user counts without explaining the grain.

### Step 6 — Select the chart by analytical task

| Task                                | Preferred visualization | Why                                          | Caution                                          |
| ----------------------------------- | ----------------------- | -------------------------------------------- | ------------------------------------------------ |
| Trend over time                     | Line chart              | Shows direction and change                   | Recent/incomplete data may look artificially low |
| Compare categories                  | Bar chart               | Supports ranking and side-by-side comparison | Too many categories reduce readability           |
| Exact values                        | Table                   | Preserves values and multiple fields         | Patterns may be harder to see                    |
| Composition with few categories     | Donut/pie               | Shows parts of a meaningful whole            | Avoid many slices or unrelated totals            |
| Relationship between numeric fields | Scatterplot             | Shows association                            | Correlation is not causation                     |
| Step progression                    | Funnel                  | Shows movement through defined steps         | Check user/event counting and funnel rules       |
| Journey discovery                   | Path                    | Shows common next/previous actions           | It is exploratory and may include loops/noise    |
| Geography                           | Geo map                 | Compares approved geographic groups          | Privacy thresholds and small groups matter       |

Charts are a communication layer. Keep the table or calculation visible whenever exact values or denominators matter.

### Step 7 — Build and document the report

For a detail report:

1. Open **Reports → Library** or the appropriate customization area.
2. Choose a suitable template or blank detail report.
3. Add only approved dimensions and metrics.
4. Set the default dimension and useful secondary dimensions.
5. Add the chart(s) that match the question.
6. Apply only the documented filters/comparisons.
7. Use a clear title and description with owner and maintenance trigger.
8. Add it to the correct collection/topic if the audience needs navigation.
9. Have a reviewer answer the original question using only the saved report.

Google’s [customize detail reports](https://support.google.com/analytics/answer/10445879) documentation should be rechecked before implementation because UI permissions and limits can change.

For a free-form Exploration:

1. Open **Explore → Free form**.
2. Import only the needed dimensions, metrics, and segments.
3. Set rows, columns, values, filters, and segment comparisons.
4. Select table, bar, line, donut, scatterplot, or map based on the question.
5. Use separate tabs only for distinct follow-up questions.
6. Name the Exploration with question, audience, owner, and date/version.
7. Record the configuration and limitations in the interpretation note.

Free-form Explorations support tables/graphs, nested rows, multiple metrics, segments, and filters. See [free-form Exploration](https://support.google.com/analytics/answer/9327972).

## Chart and Report QA

### Configuration checks

- [ ] Correct property, stream, timezone, and date range.
- [ ] Correct dimensions, metrics, scope, and compatible combinations.
- [ ] Filter/comparison/segment logic matches the written requirement.
- [ ] Key-event definition and metric name are current.
- [ ] Custom definitions are registered, available, and not duplicated unnecessarily.
- [ ] Chart title, units, date granularity, breakdown, and legend are understandable.
- [ ] Table values and chart values reconcile where they should.
- [ ] The report does not expose restricted or unnecessary data.
- [ ] The intended audience has the required access.

### Data-quality checks

- [ ] Compare a controlled test period with Data Layer, network, and DebugView evidence.
- [ ] Check processing delay and incomplete recent dates.
- [ ] Check thresholding indicators and whether low-volume data is hidden.
- [ ] Check sampling indicators in applicable Explorations.
- [ ] Check `(other)` rows and high-cardinality dimensions.
- [ ] Check report identity and attribution context.
- [ ] Check event count, users, and key-event count for plausible relationships.
- [ ] Explain discrepancies rather than silently changing filters or dates.

### Interpretation checks

- [ ] The conclusion answers the original question.
- [ ] The conclusion separates observation from interpretation.
- [ ] Association is not presented as causation.
- [ ] The denominator and time period are explicit.
- [ ] Known tracking defects and data limitations are disclosed.
- [ ] The decision/action and owner are recorded.

## Limitations to Record

### Freshness and processing

Realtime and DebugView are designed for recent activity and diagnostics. Standard reports and Explorations may use processed data with delays. Do not judge a release from an incomplete date or compare a partially processed current period with a complete historical period without a note.

### Thresholding and privacy

GA4 may limit or hide data when a report could expose individual users, especially for small populations or sensitive dimensions. A blank or reduced result is not proof that no event occurred.

### Sampling and data volume

Large or complex analyses may be sampled depending on the surface and property configuration. Record any sampling indicator and avoid presenting sampled results as exact counts.

### Cardinality

High-cardinality dimensions can cause values to be grouped into `(other)` or reduce interpretability. Do not use unique user IDs, session IDs, timestamps, raw URLs, or free text as routine custom dimensions. Use controlled IDs or a different analysis surface when appropriate.

### Attribution and identity

Acquisition results depend on attribution settings, reporting identity, lookback windows, consent, cross-domain setup, and data freshness. A DebugView result is not final attribution. Document the attribution/identity context whenever the decision depends on source, medium, campaign, or cross-device behavior.

## Report Requirements Template

**Purpose:** Use this template before building a report or Exploration. It defines the business question, decision, population, grain, fields, surface, cadence, and owner so the output remains useful after the original analyst leaves.

| ID   | Audience     | Business question                               | Decision            | Cadence     | Population/scope              | Dimensions                  | Metrics                 | Filter/segment      | Surface       | Owner    |
| ---- | ------------ | ----------------------------------------------- | ------------------- | ----------- | ----------------------------- | --------------------------- | ----------------------- | ------------------- | ------------- | -------- |
| R-01 | Product      | Which method has lower registration completion? | Prioritize UX work  | Weekly      | Users in registration journey | Method, device, date        | Users, key events, rate | Registration events | Detail report | `[name]` |
| R-02 | Analytics/QA | Is confirmed registration sent once?            | Approve/fix release | Per release | Events in test period         | Event name, method, form ID | Event count             | QA traffic          | Exploration   | `[name]` |

## Interpretation Note Template

**Purpose:** Use this note to explain what a report or Exploration means, what was observed, what decision it supports, and what it cannot prove. It prevents a chart from being reused without its denominator, scope, limitations, or attribution context.

```text
Business question:
Decision supported:
Audience and owner:
GA4 property / stream:
Report or Exploration:
Date range and property timezone:
Population and grain (user/session/event/item):
Filters, comparisons, and segments:
Dimensions and metrics with scope:
Calculation/denominator:
Observed result:
Interpretation:
What this does not prove:
Freshness/processing notes:
Thresholding/sampling/cardinality notes:
Identity/attribution notes:
Tracking or data-quality caveats:
Action, owner, and due date:
Review/retirement trigger:
```

## Report and Chart Template Set

These templates manage the report after the measurement plan has defined the event and parameter contract. Use the report requirements template for the request, the field-readiness inventory for input availability, the configuration record for the saved asset, the chart specification for visual choices, and the interpretation note for the published conclusion.

| Template                     | Purpose                                                                                          | Use when                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ |
| Report Requirements Template | Defines the question, decision, population, grain, fields, surface, cadence, and owner.          | Before creating a report or Exploration.                                       |
| Field-readiness Inventory    | Confirms that dimensions and metrics are collected, registered, processed, compatible, and safe. | Before using a field in a report.                                              |
| Report Configuration Record  | Records the exact saved report/Exploration configuration and maintenance information.            | After a report is built or materially changed.                                 |
| Chart Specification Record   | Explains why a chart type, breakdown, metric, and date granularity were chosen.                  | When a chart will support a recurring decision or be shared with stakeholders. |
| Interpretation Note Template | Records the observed result, interpretation, limitations, and action.                            | When publishing or reviewing an analysis.                                      |

### Report configuration record template

**Purpose:** Use one record for one saved detail report or Exploration. It makes the asset reproducible and gives the next owner enough information to review or rebuild it.

```text
Report/Exploration ID:
Name and surface:
GA4 property/stream:
Collection/topic or Exploration:
Business question:
Decision and owner:
Population and grain:
Date range/timezone:
Dimensions with scope:
Metrics and formulas:
Filters/comparisons/segments:
Chart/table configuration:
Custom definitions required:
Field-readiness record:
Access/sharing:
Report URL or saved location:
Version/last updated:
Maintenance trigger:
Retirement condition:
Reviewer:
```

### Chart specification record template

**Purpose:** Use this record to make the visual choice explicit. A chart should communicate an analytical task, not merely make the report look attractive.

| Field                      | What to record                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------- |
| Chart ID and report ID     | Stable IDs for the chart and the saved report/Exploration.                            |
| Analytical task            | Trend, category comparison, composition, relationship, funnel, path, or exact values. |
| Chart type                 | Line, bar, table, donut/pie, scatterplot, funnel, path, or map.                       |
| Dimension/breakdown        | The field on the axis, rows, series, or slices, including its scope.                  |
| Metric and denominator     | The value shown and the denominator for any rate or percentage.                       |
| Date granularity           | Day, week, month, or another documented period.                                       |
| Included population/filter | Exact inclusion, exclusion, comparison, or segment logic.                             |
| Reason and caveat          | Why this chart answers the task and what it must not imply.                           |
| Owner/review date          | Person/team responsible for maintenance and next review.                              |

Do not use a chart specification as a substitute for the interpretation note. The specification describes how the chart is built; the interpretation note describes what the data means for a decision.

## Example: Registration Report and Exploration

### Reusable detail report

```text
Title: Product — Registration health
Primary dimension: Event name or approved registration dimension
Secondary dimensions: Method, form ID, device category
Metrics: Users, event count, key events
Filter: Approved registration event family
Charts: Line trend + bar comparison
Table: Exact values and percentages with documented denominator
```

### Free-form Exploration

```text
Question: How do registration outcomes differ by method and device?
Rows: Method
Columns: Device category
Values: Users, event count, key events
Filter: Registration event family
Visualization: Table/heat map for exact comparison; line tab for trend
```

The output should say what was observed, what action it supports, and what it cannot prove. For example, a lower completion rate by method may justify investigation; it does not by itself prove that the method caused the drop.

## Definition of Done

- [ ] Every report/Exploration answers a named business question and supports a decision.
- [ ] Audience, owner, cadence, date range, population, grain, scope, and filters are documented.
- [ ] Standard fields were preferred and custom definitions have rationale, registration date, and availability notes.
- [ ] One reusable detail report and one appropriate Exploration are configured.
- [ ] Chart choices match analytical tasks and retain exact values where needed.
- [ ] Results were reconciled with validated collection evidence after processing.
- [ ] Freshness, thresholding, sampling, cardinality, identity, attribution, and discrepancies are recorded.
- [ ] An independent reviewer can interpret the report without verbal context.
- [ ] Owner, maintenance trigger, sharing permissions, and retirement condition are assigned.

## Official References

- [Reports in the Analytics app](https://support.google.com/analytics/answer/9924671)
- [GA4 detail report](https://support.google.com/analytics/answer/10659476)
- [Customize detail reports](https://support.google.com/analytics/answer/10445879)
- [Get started with Explorations](https://support.google.com/analytics/answer/7579450)
- [Free-form Exploration](https://support.google.com/analytics/answer/9327972)
- [Path Exploration](https://support.google.com/analytics/answer/9317498)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9377334)
- [About data thresholds](https://support.google.com/analytics/answer/9383630)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
