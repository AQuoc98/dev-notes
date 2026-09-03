# 09 — GA4 Reports, Explorations, Charts, and Interpretation

## 1. Overview

### 1.1 Objective

Turn validated GA4 data into a reproducible Report or Exploration that answers one clear business question and supports a documented decision. A chart is only the presentation layer; the population, grain, scope, fields, formula, and limitations must be defined first.

### 1.2 Scope

- Report requirements, population, grain, scope, field readiness, dimensions, metrics, filters, segments, formulas, charts, owners, and maintenance.
- GA4 detail and overview Reports, summary cards, collections, sharing, and export.
- Free-form, Funnel, Path, and other Explorations when they fit the question.
- Separate user-level reporting from event-level collection QA.
- Practical checks for data freshness, compatibility, Data quality, thresholding, sampling, identity, consent, and cardinality.

### 1.3 Out of scope

- Event meaning and collection contract: see Section 07 — Measurement Plan.
- Runtime Data Layer, GTM, consent, Network, and DebugView validation: see Section 08 — Debug/QA.
- Production activation and post-release monitoring: see Section 10 — Release Monitoring.
- Ads, campaign optimization, attribution operations, or an external BI dashboard.

### 1.4 Analysis chain

~~~text
Audience and decision
→ question
→ population and grain
→ dimensions, metrics, and formula
→ Report or Exploration
→ chart/table
→ data-quality and limitation checks
→ interpretation and action
~~~

## 2. Core concepts

### 2.1 Plain definitions

| Term | Easy explanation | GA4 example | Use it to decide |
|---|---|---|---|
| Audience | The person or team who will read the result and act on it. This is not a GA4 Audience used for targeting. | Product team reviewing registration health. | Who owns the decision and how much detail is needed? |
| Population | The complete set of users, sessions, events, or items included after the stated filters and exclusions. | Users who fired registration_start in a date range. | Who or what is actually being counted? |
| Grain | The unit represented by one row or one count. | One distinct user versus one sign_up event. | Is the question user-level, session-level, event-level, or item-level? |
| Scope | The level to which a field belongs and at which GA4 can combine it with metrics. | Event-scoped method versus a user property. | Can this dimension and metric be used together without changing the question? |
| Dimension | A descriptive label used to group, split, or filter data. | method, event_name, or device category. | Which categories should appear as rows, columns, or breakdowns? |
| Metric | A numeric value that GA4 counts, sums, or calculates. | Users, event count, key events, or revenue. | What quantity should be compared or reported? |
| Denominator | The base population of a rate; it defines what “100%” means. | Users with registration_start for method = X. | What is the correct base for the numerator? |
| Cohort | The starting population selected by a start event and time rule before later events are measured. | Users whose registration_start occurred during the cohort window. | Which users are eligible for the completion calculation? |
| Event sequence | The required order in which events must occur for the result to count. | registration_start → sign_up. | Did the completion follow the intended start event? |
| Completion window | The maximum time after the start event in which a later event counts as completion. | sign_up within 24 hours of registration_start. | When does a completion still belong to the start cohort? |
| User metric | The GA4 user-count metric used on both sides of the rate, such as Total users or Active users. | The same user metric for registration_start and sign_up. | Are both sides counting users in the same way? |
| Rate | A percentage that shows how much of the denominator reached the numerator. | 70 completed users ÷ 100 started users = 70%. | Is the result a proportion, not just a raw count? |
| Field readiness | A field has an approved meaning, is collected, available after processing, compatible, privacy-safe, and approved for the question. | method is available as a processed custom dimension after the expected window. | Can the field be used in a Report or Exploration now? |
| Report | A saved and governed view for a stable question that will be monitored repeatedly. | Weekly registration health by method. | Should this asset be maintained and published? |
| Exploration | A flexible workspace for investigating, comparing, or testing a question that may still change. | Free-form event-level QA or a funnel investigation. | Is this analysis exploratory rather than a governed Report? |

Always write the three checks below before choosing a visualization:

~~~text
Population = who or what is included?
Grain = what does one row or count represent?
Scope = at what level does each value belong?
~~~

In short: Audience tells us who acts, Population tells us who is included, Grain tells us what is counted, Scope tells us where each value belongs, and Field readiness tells us whether the result is safe to use.

### 2.2 Scope and metric discipline

| Scope | Describes | Example | Risk |
|---|---|---|---|
| User | A user across activity. | Users or a user property. | Counting the same user as multiple events. |
| Session | One visit. | Session source/medium. | Mixing session and user acquisition. |
| Event | One event occurrence. | Event name or event parameter. | Treating event count as unique users. |
| Item | One ecommerce item. | Item name or category. | Adding item rows to event totals without checking grain. |

Use the same counting unit and comparable population rules for both sides of a rate. For a method-specific registration completion rate, define:

- **Cohort/denominator:** distinct users whose `registration_start` occurred in the reporting cohort with method = X.
- **Numerator:** those same cohort users who later completed `sign_up` with method = X within the approved completion window.
- **User metric:** the same GA4 user metric (for example, Total users or Active users) on both sides.
- **Rate:** numerator ÷ denominator.

Example: if 100 distinct users start with method = email during 1–7 September and 70 of those same users complete sign_up with method = email within 24 hours, the email registration rate is 70 ÷ 100 = 70%. Under this cohort and sequence rule, the numerator is a subset of the denominator. A sign_up from a user who started before the cohort window is not included. Do not replace the denominator with all users (includes people who never started), all events (changes the grain and can count duplicates), or page views (measures a different action).

If the Report counts starts and completions independently by event date without a cohort and completion window, label the result as an event-window ratio, not a validated completion rate.

The method value must be present and controlled on both events. If it is missing or uncontrolled, exclude it from the validated rate or report it separately; do not silently combine it with another method.

### 2.3 Reports versus Explorations

| Surface | Use it for | Practical rule |
|---|---|---|
| Overview Report | High-level summary for a topic. | Use summary cards that link to governed detail Reports. |
| Detail Report | A stable question monitored repeatedly. | Publish only after definitions, fields, and ownership are stable. |
| Free-form Exploration | Flexible comparison or investigation. | Record rows, columns, values, segments, filters, and date range. |
| Funnel Exploration | Completion and drop-off through defined steps. | State whether the funnel counts users or events and whether it is open or closed. |
| Path Exploration | Discovering next or previous behavior. | Treat it as exploratory, not proof of causation. |
| Cohort Exploration | Retention or repeated behavior. | Define the cohort and observation period before interpretation. |

Google describes a detail Report as a table with two charts, while Explorations provide more flexible analysis. Do not promote every exploratory tab into a governed Report.

### 2.4 Filters, comparisons, and segments

- A **filter** restricts rows or events in the current asset.
- A **comparison** shows subsets side by side in Reports.
- A **segment** defines a reusable subset of users, sessions, or events in Explorations.

Write the condition in plain language and include its scope. “Users who completed sign_up” is different from “events where event_name equals sign_up”.

## 3. Report and Exploration workflow

### Step 1 — Define the question and decision

Record one decision-relevant question:

~~~text
Audience:
Question:
Decision or action:
Cadence:
Owner:
~~~

Use a name that describes the question or decision. Avoid names such as Dashboard 1, Test, or All Events.

### Step 2 — Define population, grain, and scope

Record:

- property, stream, timezone, and date range;
- population and exclusions in plain language;
- grain: user, session, event, or item;
- identity/reporting identity when relevant;
- filters, comparisons, segments, and comparison period;
- numerator, denominator, cohort, sequence, completion window, user metric, and key-event definition for a rate;
- expected freshness and processing state.

Do not use an event count to answer a user-level question unless that difference is intentional and documented.

### Step 3 — Confirm field readiness

Section 07 is the source of truth for event and parameter meaning. This step checks only whether the approved fields are available and suitable for the selected reporting surface.

Before building:

1. Confirm the event and parameter are in the approved Measurement Plan.
2. Use a standard dimension or metric when it has the required meaning and scope.
3. Confirm the field is collected and visible in the appropriate GA4 surface.
4. Register a custom dimension or metric only for an approved recurring need.
5. Record registration date and expected availability.
6. Check privacy, allowed values, cardinality, quotas, and compatibility.
7. Test the field after processing; a Network parameter alone is not report-ready.

Custom dimensions and metrics are created from collected custom data. Google notes that they may take up to 24–48 hours before they are available for Reports or Explorations, so record the expected availability instead of treating a newly registered field as immediately ready.

### Step 4 — Choose the GA4 surface

Use a Detail Report for a stable recurring question. Use an Exploration for investigation, flexible segment comparison, a funnel/path, or an event-level QA view. Keep the selected surface in the asset record.

### Step 5 — Choose dimensions, metrics, and formula

Choose the smallest compatible set:

~~~text
Question:
Dimensions and scopes:
Metrics and units:
Filter/comparison/segment:
Numerator:
Denominator:
Formula:
~~~

For any percentage, the numerator and denominator must use the same property, stream, date range, population rules, identity, and dimension value. For a cross-event rate, also record the same cohort, event sequence, completion window, and user metric. If the UI cannot express the formula exactly, use an approved Exploration or export; do not silently substitute a different denominator.

For a cross-event user rate, choose the implementation explicitly:

| Need | Preferred GA4 surface | Practical rule |
|---|---|---|
| Stable monitoring of a metric that GA4 provides directly | Detail Report | Use when the metric and denominator are native and the formula is visible. Do not present two separate counts as a calculated rate. |
| User completion and drop-off in the GA4 UI | Funnel Exploration | Use when the steps, user counting, method filter, and open/closed rule can represent the requirement. Save the configuration and document its rules. |
| Exact distinct-user numerator ÷ denominator across events, custom joins, or repeatable extracts | Approved export or BigQuery | Use when the GA4 UI cannot represent the formula exactly. Document identity, event-time logic, schema, access, and processing delay. |

If the requirement needs an exact two-event rate and neither a Detail Report nor a Funnel Exploration can represent it without changing the population, use the approved export/BigQuery calculation. Do not silently replace it with event count, total users, or page views.

Decision order: use a Detail Report for a native metric, a Funnel Exploration for an in-UI completion flow, and an approved export/BigQuery calculation only when the exact cross-event logic cannot be represented in GA4.

### Step 6 — Select the chart

| Analytical task | Suitable chart | Check |
|---|---|---|
| Trend | Line | Date granularity and incomplete recent dates. |
| Category comparison | Bar | Controlled number of categories and compatible scope. |
| Exact values | Table | Units, sorting, and denominator remain visible. |
| Few-part composition | Donut/pie | Parts form a meaningful whole. |
| Numeric relationship | Scatterplot | Association is not causation. |
| Step progression | Funnel | User/event counting and funnel rules are explicit. |
| Journey discovery | Path | Loops and noise are expected; do not infer causality. |
| Geography | Geo map | Small groups and privacy thresholds are checked. |

Keep a table or visible formula whenever exact values or denominators matter.

### Step 7 — Build, QA, and document

Build the asset, then check:

- property, stream, timezone, date range, fields, scopes, and filters;
- Data quality indicator, freshness, thresholding, sampling, and cardinality;
- table values versus chart values;
- interpretation, limitation, owner, and maintenance trigger.

Use Section 08 evidence when collection quality is relevant. Do not use a report or chart to replace runtime QA.

## 4. Canonical records

Use one record per purpose. The requirement record defines the question, the field-readiness record confirms usable fields, the asset record makes the saved view reproducible, and the interpretation note records the decision. Keep shared definitions referenced rather than duplicated across records.

### 4.1 Report Requirement Record

| Field | Value |
|---|---|
| Requirement ID | [stable ID] |
| Audience / owner | [team and named owner] |
| Business question | [one question] |
| Decision/action | [what the result may change] |
| Cadence | [one-off, weekly, release-based] |
| Population/exclusions | [who/what is included] |
| Grain | [user/session/event/item] |
| Dimensions and scopes | [approved fields] |
| Metrics and formula | [units, numerator, denominator] |
| Filters/comparisons/segments | [inclusion logic] |
| GA4 surface | [Detail Report, Overview, or Exploration] |
| Review trigger | [contract, product, or data change] |

### 4.2 Field Readiness Record

| Field | Value |
|---|---|
| Field-readiness ID | [stable ID] |
| Field and meaning | [event/parameter/property and business meaning] |
| Source | [GA4 event, parameter, user property, or export] |
| Scope | [user/session/event/item] |
| Standard/custom | [standard or custom] |
| Collection confirmed | [date/evidence reference] |
| Registration | [not required or custom definition/date] |
| Expected availability | [processing window] |
| Compatibility/privacy | [status and caveat] |
| Cardinality/quota | [risk and decision] |
| Owner/status | [owner and Ready/Pending/Blocked] |

### 4.3 Asset Configuration Record

Use one record for one saved Detail Report, Overview Report, or Exploration:

~~~text
Asset ID:
Requirement ID:
Field-readiness IDs:
Name and surface:
GA4 property/stream/timezone:
Date range:
Population and grain:
Dimensions with scope:
Metrics and formulas:
Filters/comparisons/segments:
Chart/table configuration:
Data-quality and limitation notes:
Access/share/export location:
Version/last updated:
Owner and maintenance trigger:
Retirement condition:
~~~

### 4.4 Interpretation and Decision Note

~~~text
Asset ID:
Requirement ID:
Observed result:
Interpretation:
Decision/action:
What the result does not prove:
Date range and freshness:
Thresholding/sampling/cardinality:
Identity/consent/attribution context:
Section 08 evidence or data-quality caveat:
Owner and due date:
Review/retirement trigger:
~~~

## 5. Practical implementation

### 5.1 Create a Detail Report

Use this for a stable, repeatedly monitored question:

1. Open Reports → Library.
2. Choose Create new report → Create detail report.
3. Start blank or from a suitable template.
4. In Customize report, add only ready and compatible dimensions, metrics, filters, and the two charts.
5. Set the title, default dimension, sort metric, description, owner, and maintenance trigger.
6. Save, open the report from Reports, and verify that the table answers the requirement.
7. Record the configuration in the Asset Configuration Record.

Changing the table dimension, sorting metric, filter, or comparison can change the charts. Document the table and formula, not only the chart type.

### 5.2 Create an Overview Report and summary cards

Use an Overview Report for a topic-level summary:

1. Create or open a Detail Report.
2. Add only summary cards that represent stable decisions.
3. Open Reports → Library and create or edit the Overview Report.
4. Add cards to a topic, arrange their order, save, and publish the collection.
5. Keep the linked Detail Report as the source of exact values.

### 5.3 Create a Free-form Exploration

Use this for analytical investigation or to inspect processed event counts after the runtime checks in Section 08:

1. Open Explore → Free form.
2. Add only required dimensions, metrics, and segments.
3. Put dimensions in Rows/Columns and metrics in Values.
4. Apply exact filters and record their case-sensitive logic.
5. Choose the chart that matches the task.
6. Name, save, and record the date range, property, configuration, owner, and limitations.

Review the Data quality indicator before interpreting the result. A shared Exploration is not automatically a governed Report.

### 5.4 Share or export

Use the saved Report or Exploration itself when sharing or exporting. Record the asset ID, version, date range, and recipient or location. Do not share a Library customization screen as if it were the final Report.

### 5.5 Access and publish permissions

Use the least access needed for each action. Exact labels can vary with inherited account/property permissions and data restrictions, so confirm the current GA4 role matrix with the property administrator.

| Action | Practical access | Control |
|---|---|---|
| View a shared Report, Overview, or Exploration | Viewer or higher | Owner grants access to the intended audience. |
| Create, edit, and share an Exploration | Analyst or higher, subject to property restrictions | Keep the asset owner and sharing scope documented. |
| Customize a Detail/Overview Report or publish a collection | Editor or Administrator | Require review before changing a shared navigation or published collection. |
| Change custom definitions or other property-level settings | Editor/Administrator as allowed by the property | Record the change and its expected processing window. |
| Export or share sensitive data | Role access plus approved data-handling rules | Confirm recipient, destination, retention, and privacy approval. |

Publishing a Report collection changes what other users see; saving an Exploration does not automatically publish it as a Report.

## 6. QA and limitations

### 6.1 Configuration checklist

- [ ] Correct property, stream, timezone, and date range.
- [ ] Dimensions and metrics have compatible scopes.
- [ ] Population, filters, comparisons, segments, numerator, and denominator match the requirement.
- [ ] Custom definitions are ready, not duplicated, and still approved.
- [ ] Table and chart values reconcile where expected.
- [ ] Title, units, date granularity, breakdown, and legend are understandable.
- [ ] Access is appropriate for the intended audience.

### 6.2 Data-quality checklist

- [ ] Collection evidence from Section 08 is available when needed.
- [ ] Freshness and processing status are recorded.
- [ ] Data quality indicator is reviewed.
- [ ] Thresholding, sampling, and cardinality or (other) effects are recorded.
- [ ] Recent incomplete dates are not treated as final.
- [ ] Consent, identity, and attribution context are documented when relevant.

### 6.3 Interpretation checklist

- [ ] The conclusion answers the original question.
- [ ] Observation is separated from interpretation.
- [ ] Numerator, denominator, grain, and date range are explicit.
- [ ] Association is not presented as causation.
- [ ] Tracking defects and data limitations are disclosed.
- [ ] The decision/action, owner, and due date are recorded.

### 6.4 Common limitations

- **Freshness:** Realtime is recent activity; Reports and Explorations may change while data is processed. Do not judge a period that is still incomplete.
- **Thresholding:** Small or sensitive populations may be hidden or reduced. A blank result does not prove that no event occurred.
- **Sampling:** Large or complex queries may use a sample. Record the data-quality indicator and do not present sampled counts as exact.
- **Cardinality:** High-cardinality dimensions can produce an (other) row and reduce interpretability. Avoid routine custom dimensions for unique IDs, timestamps, or free text.
- **Identity and consent:** User counts, attribution, and cross-device results depend on reporting identity, consent, and configuration.
- **Surface differences:** Reports and Explorations can differ because of supported fields, filters, segments/comparisons, date range, low-user handling, and processing time. Compare configuration before opening a collection defect.

### 6.5 Missing and invalid values

- **`(not set)`:** The requested dimension value was not available for the row or event. Keep it separate while investigating the source; do not count it as a valid method.
- **`Unassigned`:** GA4 could not map a value to the selected grouping or classification. Treat it as an attribution/classification issue, not as the same condition as `(not set)`.
- **Invalid or uncontrolled value:** A value outside the approved list, wrong type, empty string, or unexpected casing fails the field contract. Exclude it from the validated rate or map it to an explicitly approved category; never silently rename it.
- **Reporting action:** Quantify these rows separately, link the issue to Section 08 collection evidence, and record the impact in the Interpretation and Decision Note.

## 7. Cross-reference map

| Section | Use it for |
|---|---|
| 01 — Data Layer Design | Approved event payload and field source. |
| 02 — Variable Management | Stable values exposed to GTM. |
| 03 — Trigger Management | Authoritative event selection. |
| 04 — Tag Management | Event destination and parameter mapping. |
| 05 — Consent | Allowed collection and denied behavior. |
| 06 — Template Governance | Reusable template ownership and lifecycle. |
| 07 — Measurement Plan | Business question, event contract, scope, and field approval. |
| 08 — Debug/QA | Runtime evidence for collection and consent. |
| 10 — Release Monitoring | Post-publication checks and ownership. |

## 8. Worked example — Registration Reporting Journey

This section illustrates how a Registration requirement becomes a user-level Report and an event-level QA Exploration. Values in brackets are placeholders for the approved property, stream, dates, owner, and evidence. Runtime setup and evidence are maintained in Section 08 and referenced by ID.

### 8.1 Registration reporting requirement

Record type: Report Requirement Record.

| Field | Recorded value |
|---|---|
| Requirement ID | REQ-REG-001 |
| Audience / owner | Product team / [named owner] |
| Business question | Which registration method has the lowest validated completion rate? |
| Decision/action | Investigate a method with a persistently lower rate; do not infer causation from the rate alone. |
| Cadence | Weekly and after a release affecting registration or tracking. |
| Population/exclusions | Approved production users; cohort starts with `registration_start` and controlled `method`; exclude unapproved test traffic. |
| Grain | One distinct user per method and reporting period. |
| Cohort/sequence/completion window | Users with `registration_start` in [cohort window] and method = X; count a later `sign_up` with method = X within [completion window]. |
| Dimensions and scopes | `method` (event-scoped); required event and parameter fields follow the approved Section 07 contract; add another compatible breakdown only when documented. |
| Metrics and formula | User metric: [Total users or Active users], used on both sides. Numerator: cohort users with `sign_up`, `method = X`; denominator: cohort users with `registration_start`, `method = X`; rate = numerator ÷ denominator. |
| Filters/comparisons/segments | Same property, stream, date range, reporting identity, cohort, completion window, method value, and exclusions on both sides of the rate. |
| GA4 surface | Saved Detail Report or approved user-level Funnel Exploration. |
| Review trigger | Registration event contract, method values, field approval, consent behavior, or product flow changes. |
| Section 08 reference | QA run `[QA-REG-RUN-001]` and evidence IDs maintained in Section 08. |

### 8.2 Registration field readiness

Record type: Field Readiness Record, maintained separately for each field used by the assets.

| Field-readiness ID | Field and meaning | Source | Scope | Standard/custom | Collection confirmed | Registration | Expected availability | Compatibility/privacy | Cardinality/quota | Owner/status |
|---|---|---|---|---|---|---|---|---|---|---|
| FR-REG-001 | `registration_start` — registration flow started | Application data layer → GTM → GA4 event | Event | Custom event | Section 08 evidence: [ID] | Not applicable | [processing window] | Approved event name and consent behavior | Low controlled cardinality | [owner] / Ready |
| FR-REG-002 | `sign_up` — account creation confirmed by the server | Application data layer → GTM → GA4 event | Event | Recommended GA4 event | Section 08 evidence: [ID] | Not applicable | [processing window] | Only emitted after server confirmation | Low controlled cardinality | [owner] / Ready |
| FR-REG-003 | `method` — controlled registration method | Application data layer → GTM → GA4 parameter | Event | Recommended/custom parameter; register a custom dimension when needed in reporting | Section 08 evidence: [ID] | [custom definition ID/date, if needed] | [processing window] | Controlled values; no free text or identifiers | Controlled list | [owner] / Ready |
| FR-REG-004 | `form_id` — approved form identifier | Application data layer → GTM → GA4 parameter | Event | Custom parameter; register only when needed | Section 08 evidence: [ID] | [custom definition ID/date, if needed] | [processing window] | Use an approved non-PII value | Keep the list bounded | [owner] / Ready or Pending |

### 8.3 User-level registration Report configuration

Record type: Asset Configuration Record.

~~~text
Asset ID: R-REG-001
Requirement ID: REQ-REG-001
Field-readiness IDs: FR-REG-001, FR-REG-002, FR-REG-003, FR-REG-004
Name and surface: Registration completion by method — Detail Report (or approved user-level Funnel Exploration)
GA4 property/stream/timezone: [property] / [web stream] / [timezone]
Date range: [start] → [end]
Population and grain: Approved users whose registration_start occurs in [cohort window]; one distinct user per method and reporting period; exclude test traffic
Dimensions with scope: method (event-scoped)
Metrics and formulas: [Total users or Active users] for the same cohort; users(sign_up, method = X within [completion window]) ÷ users(registration_start, method = X); implementation path [Funnel Exploration / approved export or BigQuery]
Filters/comparisons/segments: Same method, property, stream, date range, identity, cohort, completion window, and exclusions for numerator and denominator
Chart/table configuration: Table or bar chart by method; keep numerator, denominator, and formula visible
Data-quality and limitation notes: [freshness, thresholding, sampling, (other), cardinality, identity, consent]
Access/share/export location: [link or location]
Version/last updated: v1.0 / [date]
Owner and maintenance trigger: [owner] / review after contract, field, consent, or flow change
Retirement condition: Requirement or registration flow is removed
~~~

Select the implementation path using the decision order in Step 5: Funnel Exploration when the UI preserves the cohort and sequence; otherwise use the approved export/BigQuery calculation. A Detail Report is suitable only for a directly supported metric and must not imply an unsupported cross-event rate.

### 8.4 Event-level registration QA Exploration

Record type: Asset Configuration Record.

~~~text
Asset ID: EX-REG-QA-001
Requirement ID: REQ-REG-001 (runtime acceptance supporting the product question)
Field-readiness IDs: FR-REG-001, FR-REG-002, FR-REG-003, FR-REG-004
Name and surface: Registration event QA — Explore → Free form
GA4 property/stream/timezone: [same as R-REG-001]
Date range: [controlled QA window]
Population and grain: Controlled QA traffic; one event occurrence
Dimensions with scope: event_name, method, form_id (event-scoped)
Metrics and formulas: event count; no user-level completion-rate calculation
Filters/comparisons/segments: QA window, approved registration event family, test/build identifier
Chart/table configuration: Table with event_name rows and method/form_id columns
Data-quality and limitation notes: Expected one registration_start at journey start and one sign_up per confirmed account; processed-count and runtime evidence reference Section 08 QA run [ID]
Access/share/export location: [link or location]
Version/last updated: v1.0 / [date]
Owner and maintenance trigger: [owner] / review after event contract, mapping, or consent change
Retirement condition: QA workflow or registration contract is removed
~~~

This Exploration checks duplicates, missing events, wrong timing, wrong destination, and missing parameters. It does not prove the user-level rate or request count.

### 8.5 Registration interpretation and decision

Record type: Interpretation and Decision Note. Chart configuration is stored in each Asset Configuration Record; the outcome is recorded once here.

~~~text
Asset ID: R-REG-001; EX-REG-QA-001
Requirement ID: REQ-REG-001
Observed result: [user metric]; cohort [range]; completion window [window]; [numerator] / [denominator] = [rate] by method; event-level count [value]
Interpretation: [describe the observed difference or collection defect, not a causal claim]
Decision/action: [investigate, accept, rollback, or monitor] / [owner] / [due date]
What the result does not prove: method caused the difference; event-level QA proves the user-level rate; a processed report proves request count
Date range and freshness: [range] / [freshness and processing status]
Thresholding/sampling/cardinality: [status and impact]
Identity/consent/attribution context: [reporting identity, consent state, attribution caveat]
Section 08 evidence or data-quality caveat: [QA run/evidence IDs and unresolved gaps]
Owner and due date: [owner] / [date]
Review/retirement trigger: contract, field, consent, release, or reporting-surface change
~~~

Runtime test setup, scenario results, and evidence are maintained in Section 08. Reference their IDs from the Report Requirement Record or the Interpretation and Decision Note.

## Official references

- [GA4 detail reports](https://support.google.com/analytics/answer/10659476)
- [Overview reports](https://support.google.com/analytics/answer/10659551)
- [Create a detail report](https://support.google.com/analytics/answer/13844077)
- [Customize detail reports](https://support.google.com/analytics/answer/10445879)
- [Create an overview report](https://support.google.com/analytics/answer/13823841)
- [Create a summary card](https://support.google.com/analytics/answer/13819308)
- [Customize report navigation](https://support.google.com/analytics/answer/10460557)
- [Free-form Exploration](https://support.google.com/analytics/answer/9327972)
- [Get started with Explorations](https://support.google.com/analytics/answer/7579450)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Data quality](https://support.google.com/analytics/answer/12856703)
- [Data sampling](https://support.google.com/analytics/answer/13331292)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
- [Data differences between Reports and Explorations](https://support.google.com/analytics/answer/9371379)
