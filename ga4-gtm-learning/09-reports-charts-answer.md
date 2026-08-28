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

## Shared Terminology

Use these plain-language meanings when discussing a report with product, marketing, engineering, QA, or leadership. In this document, **audience** means the people who read the report; it does not mean a GA4 Audience used for targeting.

| Term | Plain meaning | Registration example |
| --- | --- | --- |
| Audience | The people who will use the report and the decision they need to make. | Product team deciding which registration method needs investigation. |
| Population | The exact users, sessions, events, or items included in the analysis. | Users who entered the registration journey. |
| Grain | What one row or one counted unit represents. | One user, one session, one event, or one ecommerce item. |
| Scope | The level at which a value belongs. | `method` belongs to an event; the scope of `device category` must be checked for the selected GA4 surface. |
| Dimension | A label or category used to group data. | `method`, `device category`, or `event_name`. |
| Metric | A number that is counted, summed, or calculated. | Users, event count, revenue, or completion rate. |
| Denominator | The base number used to calculate a rate. | Users with `registration_start` when calculating completion rate. |
| Field readiness (whether the data field is ready) | Whether a field is safe and technically available for a report—not merely present in a request. | `method` is collected, registered if needed, processed, compatible, and approved for reporting. |
| GA4 surface | The GA4 area where the analysis is built or viewed. | Reports for recurring monitoring; Explorations for investigation. |

The practical distinction is:

```text
Population = who or what is included?
Grain      = what does one row or count represent?
Scope      = at what level does each value belong?
Readiness  = can this field be used safely and correctly yet?
```

## Core Reporting Concepts

### Dimensions, metrics, and scope

| Concept | Easy explanation | Example | Common mistake |
| --- | --- | --- | --- |
| Dimension | A label used to split data into groups. | `method`, `device category`, `event_name` | Treating a label as a number. |
| Metric | A number that tells us how much or how many. | Users, event count, key events, revenue | Comparing numbers with different bases. |
| Scope | The level where a value belongs. | User, session, event, or item | Using an event value to answer a user-level question. |
| User scope | A value describes the user across their activity. | User property | Treating it as if it changed for every event. |
| Session scope | A value describes one visit/session. | Session source/medium | Mixing session acquisition with user acquisition. |
| Event scope | A value describes one event occurrence. | Event name or event parameter | Assuming event count equals unique users. |
| Item scope | A value describes one product/item inside ecommerce data. | Item name or item category | Adding item rows to event totals without checking grain. |

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

**Population** answers: “Who or what is included?” **Grain** answers: “What does one row or one counted unit represent?” These must be written before choosing a chart.

Examples:

```text
Population: Users who entered the registration journey
Grain:     One user counted once
Question:  What percentage of these users completed registration?

Population: All sign_up events sent in the QA period
Grain:     One event occurrence
Question:  Was one request sent for each confirmed account?
```

Record:

- date range and property timezone;
- the population in plain language;
- grain: user, session, event, or item;
- inclusion/exclusion conditions;
- comparison periods or segments;
- identity setting/reporting identity if relevant;
- attribution model and traffic-source scope if relevant;
- key-event definition and deduplication assumption;
- expected data freshness.

Do not calculate a user-level rate with an event count unless the difference is explicitly intended and documented.

### Step 3 — Confirm field readiness (can this field be used yet?)

**Field readiness** means the field has passed all relevant checks: it is collected, registered when required, processed and available, compatible with the selected report, safe to use, and approved for the stated question. A parameter appearing in a Network request does not automatically make it ready for reporting.

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

| Field             | Meaning                | Source              | Scope (data level)         | Standard/custom    | Registration date | Expected availability  | Risk/notes            |
| ----------------- | ---------------------- | ------------------- | -------------------------- | ------------------ | ----------------- | ---------------------- | --------------------- |
| `event_name`      | Canonical event        | GA4 event           | Event                      | Standard           | N/A               | Standard processing    | Stable                |
| `method`          | Registration method    | `sign_up` parameter | Event                      | Custom if required | YYYY-MM-DD        | After processing delay | Controlled values     |
| `form_id`         | Stable form identifier | `sign_up` parameter | Event                      | Custom if required | YYYY-MM-DD        | After processing delay | Avoid free text       |
| `device_category` | Device category        | GA4 collection      | User/session/event context | Standard           | N/A               | Standard processing    | Scope must be checked |

### Step 4 — Choose the GA4 area (surface)

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

### Step 7 — Build, publish, and document the asset

#### A. Create a detail report

You need the **Editor** or **Administrator** role to create or customize a report. Use a detail report when the question is stable and will be monitored repeatedly.

1. In GA4, open **Reports → Library**.
2. In the **Reports** section, select **+ Create new report → Create detail report**.
3. Choose **Blank** or start from a suitable template. A report based on a template may receive future template updates; record whether the report remains linked or is unlinked.
4. In **Customize report**, configure the dimension picker, metrics, report filter, and the two charts. Add only fields that are ready and compatible.
5. Set the default dimension, default sort metric, chart type, filters/comparisons, title, description, owner, and maintenance trigger.
6. Click **Save**, enter a clear report name, and save again.
7. Open the saved report from **Reports**, not only from Library, and review whether it answers the original question.

A detail report contains two charts and a table. The charts reflect the data shown in the table, so changing the dimension, filter, comparison, or default sort can change the chart data. Document the table configuration and the chart purpose, not only the visual type. See [create a detail report](https://support.google.com/analytics/answer/13844077) and [customize detail reports](https://support.google.com/analytics/answer/10445879).

#### B. Create a summary card and overview report

An overview report is a high-level page made from summary cards. A summary card is created from a detail report and can link users to the underlying detail report.

To create a summary card:

1. Open the relevant detail report and click **Customize report**.
2. Under **SUMMARY CARDS**, select **+ Create new card**.
3. Choose the dimension dropdown, metric dropdown, visualization, and optional card filter.
4. Click **Apply**, then **Save → Save changes to current report**.

To create an overview report:

1. Go to **Reports → Library**.
2. Select **+ Create new report → Create overview report**.
3. Select **+ Add cards**, choose the required cards, and arrange them in the desired order. An overview report can contain up to 16 summary cards.
4. Save and name the report.

Custom summary cards may appear in the overview report’s **Summary Cards** tab only after the source detail report is included in at least one report collection. See [create a summary card](https://support.google.com/analytics/answer/13819308) and [create an overview report](https://support.google.com/analytics/answer/13823841).

#### C. Add the report to the left navigation

Saving a report in Library does not automatically make it visible in the property’s left navigation. An Editor or Administrator must add it to a collection:

1. In **Reports → Library**, create a collection or edit an existing collection.
2. Create or choose a topic.
3. Drag the detail or overview report into the topic.
4. Click **Save**, then use the collection’s **More → Publish** action.

Use a collection for reports that a defined audience needs to find repeatedly. Keep exploratory work private or shared as an Exploration until the question, definitions, and ownership are stable. See [customize report navigation](https://support.google.com/analytics/answer/10460557).

#### D. Create a chart in a free-form Exploration

Use an Exploration for an investigation, a flexible comparison, or a question that is not yet stable.

1. Open **Explore → Free form** or start from an Exploration template.
2. In **Variables**, add only the required dimensions, metrics, and segments.
3. In **Tab Settings**, place dimensions in **Rows/Columns**, metrics in **Values**, and apply the required filters or segment comparisons.
4. Under **Visualization**, choose a table, bar, line, donut, scatterplot, or geo map according to the analytical task.
5. Add another tab only for a distinct follow-up question; do not mix unrelated questions in one tab.
6. Use a clear name, save the Exploration, and record its date range, configuration, limitations, and owner.

The **Data quality** indicator should be reviewed before interpreting the result. Explorations can be shared and exported, but a shared Exploration is view-only for other users; they must duplicate it to edit. See [free-form Exploration](https://support.google.com/analytics/answer/9327972) and [get started with Explorations](https://support.google.com/analytics/answer/7579450).

#### E. Share or export the result

For a saved report, open it from **Reports**, select **Share this report**, and choose **Share Link** or **Download File**. Reports can be exported to PDF, CSV, or Google Sheets. For an Exploration, use **Share exploration** or **Export data** and choose the required format. Do not share a Library customization screen as if it were the saved report. See [share and export reports](https://support.google.com/analytics/answer/9317657).

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

Open the **Data quality** indicator next to the report or Exploration title before finalizing the analysis. Record whether the result is unsampled, thresholded, or sampled and explain any effect on the decision. See [GA4 data quality](https://support.google.com/analytics/answer/12856703).

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

### Reports and Explorations can legitimately differ

Do not treat every difference between a Report and an Exploration as an implementation defect. Compare the selected fields, filters, comparisons/segments, date range, data-retention window, low-user thresholding, behavioral modeling, and processing time. Reports and Explorations can support different fields and use different filtering behavior, so record which surface is authoritative for the decision. See [data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379) and [data compatibility](https://support.google.com/analytics/answer/11608978).

## Report Requirements Template

**Purpose:** Use this template before building a report or Exploration. It defines the business question, decision, who/what is included, what one row represents, the fields, GA4 area, cadence, and owner so the output remains useful after the original analyst leaves.

| ID   | Audience     | Business question                               | Decision            | Cadence     | Population (who/what is included) | Grain (one row/count represents) | Dimensions                  | Metrics                 | Filter/segment      | GA4 area       | Owner    |
| ---- | ------------ | ----------------------------------------------- | ------------------- | ----------- | --------------------------------- | -------------------------------- | --------------------------- | ----------------------- | ------------------- | -------------- | -------- |
| R-01 | Product      | Which method has lower registration completion? | Prioritize UX work  | Weekly      | Users in registration journey     | One user                         | Method, device, date        | Users, key events, rate | Registration events | Detail report  | `[name]` |
| R-02 | Analytics/QA | Is confirmed registration sent once?            | Approve/fix release | Per release | Events in test period             | One event occurrence             | Event name, method, form ID | Event count             | QA traffic          | Exploration    | `[name]` |

## Interpretation Note Template

**Purpose:** Use this note to explain what a report or Exploration means, what was observed, what decision it supports, and what it cannot prove. It prevents a chart from being reused without its denominator, scope, limitations, or attribution context.

```text
Business question:
Decision supported:
Audience and owner:
GA4 property / stream:
Report or Exploration:
Date range and property timezone:
Population (who/what is included):
Grain (what one row/count represents):
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
Population (who/what is included):
Grain (what one row/count represents):
Date range/timezone:
Dimensions with scope (data level):
Metrics and formulas:
Filters/comparisons/segments:
Chart/table configuration:
Custom definitions required:
Field-readiness record (is the field ready for reporting?):
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

## Official References

- [Reports in the Analytics app](https://support.google.com/analytics/answer/9924671)
- [Create a detail report](https://support.google.com/analytics/answer/13844077)
- [GA4 detail report](https://support.google.com/analytics/answer/10659476)
- [Customize detail reports](https://support.google.com/analytics/answer/10445879)
- [Create a summary card](https://support.google.com/analytics/answer/13819308)
- [Create an overview report](https://support.google.com/analytics/answer/13823841)
- [Customize report navigation](https://support.google.com/analytics/answer/10460557)
- [Share and export reports](https://support.google.com/analytics/answer/9317657)
- [Get started with Explorations](https://support.google.com/analytics/answer/7579450)
- [Free-form Exploration](https://support.google.com/analytics/answer/9327972)
- [Path Exploration](https://support.google.com/analytics/answer/9317498)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
- [Data compatibility](https://support.google.com/analytics/answer/11608978)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379)
- [About data thresholds](https://support.google.com/analytics/answer/9383630)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
