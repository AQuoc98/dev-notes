# FD-REC-09 — GA4 Report and Exploration Record

> **SIMULATED — Phase 4 documentation only.** This record defines the GA4 reporting assets for `calculation_action`; it does not create custom definitions, publish Reports, run Explorations or inspect processed GA4 data.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-09` |
| Record name | GA4 Report and Exploration Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 4 — GA4 custom definitions, Reports and Explorations |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Collection dependency | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| QA dependency | Section 08; runtime evidence is not available in the current project |
| Status | **Completed — simulation documentation** |
| Execution state | Simulation only — no live GA4 property or processed-data query |
| Value/evidence boundary | Asset names, definitions, formulas and expected results are simulated; no Report/Exploration is configured or published |
| Primary owner | FD Analytics owner — simulated alias |
| Reviewers | Business owner, Application owner, GTM owner, Privacy reviewer — simulated aliases |
| Open items | Create the assets and confirm field availability only when a runtime project is authorized |
| Next action | Keep this reporting design as the baseline for any future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-07` / `2026-09-07` |

## 0.1 Source of the record format

This is a project record using the standard Section 09 records: Report Requirement, Field Readiness, Asset Configuration and Interpretation/Decision. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Population, grain, scope, surface and rate discipline | [Section 09 — Reports, Explorations, Charts and Interpretation](../09-reports-charts-answer.md) |
| Event meaning, parameter allowlist and consent boundary | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Collection and QA evidence boundary | [Section 08 — Debug and QA](../08-debug-qa-answer.md) |
| Release and monitoring handoff | [Section 10 — Release Monitoring](../10-release-monitoring-answer.md) |

## 1. Report Requirement Record

| Field | Recorded value |
|---|---|
| Requirement ID | `REQ-FD-OUTCOME-001` |
| Audience / owner | FD product and analytics team / FD Analytics owner |
| Business question | Among recorded FD calculation attempts, what proportion returned an output, and how does the result vary by approved `design_method`? |
| Decision/action | Investigate a persistently low output share or an unexpected method-level difference; do not treat `No` as a pure no-solution result because the contract groups empty responses and terminal errors. |
| Cadence | Weekly and after a release that changes calculation tracking or the `calculation_action` contract |
| Property / stream | QA and production destinations from `FD-REC-00`; simulated IDs only |
| Population / exclusions | Valid `calculation_action` events with `app_name=fd`, `event_schema_version=1.0`, approved `design_method` and consent-allowed collection; exclude unapproved test traffic |
| Grain | Event-level calculation attempt. The Application contract expects one event per terminalized occurrence. |
| User-level report | Not approved for this schema. No user-level business question or user-scoped field is defined in `FD-REC-07`; do not relabel event counts as users. |
| Event-level rate | For method X: numerator = `calculation_action` events with `solution_found=Yes` and `design_method=X`; denominator = `calculation_action` events with `design_method=X` and `solution_found` in `{Yes, No}`; rate = numerator ÷ denominator. |
| Required dimensions | `design_method`, `solution_found`, `event_name`, `event_schema_version`, `app_name` |
| Required metric | Event count |
| Same-scope rule | Numerator and denominator use the same property, stream, date range, event eligibility, method value, consent policy and event grain. |
| Review trigger | Contract/schema, allowed-value, consent, routing, Tag, custom-definition or reporting requirement change |

The one-event-per-occurrence rule is what makes this an event-level attempt rate. If a future requirement asks for a distinct-user rate, the Measurement Plan must first add an approved identity and user-level population rule; this record must not infer one.

## 2. Field Readiness Record

Field readiness is simulated because no live event, custom definition or processed GA4 row exists in this project.

| Field | Meaning and source | Scope | Registration / readiness | Compatibility and limitation | Status |
|---|---|---|---|---|---|
| `calculation_action` | Canonical event from Application Data Layer → GTM | Event | Standard event name; expected after collection | Must be available in the selected property/stream after processing | Approved for simulation; runtime pending |
| `solution_found` | `Yes`/`No` outcome from Application; source `inputs` is not used | Event parameter | Event-scoped custom dimension approved in `FD-REC-07`; create before a live Report/Exploration | `No` combines empty response and terminal error | Approved for simulation; processed availability pending |
| `design_method` | Approved calculation method; source `inputs.design_method` | Event parameter | Event-scoped custom dimension approved in `FD-REC-07` | Controlled values only; invalid values are not valid method rows | Approved for simulation; processed availability pending |
| `country` | Source `inputs.country` | Event parameter | Event-scoped custom dimension approved | Use only when the analysis requires it | Approved for simulation; processed availability pending |
| `language` | Source `inputs.language` | Event parameter | Event-scoped custom dimension approved | Use only when the analysis requires it | Approved for simulation; processed availability pending |
| `building_code` | Source `inputs.building_code` | Event parameter | Event-scoped custom dimension approved | Controlled code list; review cardinality | Approved for simulation; processed availability pending |
| `connection_type` | Source `inputs.connection_type` | Event parameter | Event-scoped custom dimension approved | Controlled list; review cardinality | Approved for simulation; processed availability pending |
| `event_schema_version` | Top-level contract version | Event parameter | Collect for validation; no initial custom definition | Filter to `1.0` for this asset | Approved for simulation; processed availability pending |
| `app_name` | Top-level product identifier | Event parameter | Collect for validation; no initial custom definition | Filter to `fd` for this asset | Approved for simulation; processed availability pending |
| `fx`, `fy` | Optional numeric inputs | Event parameter | Approved for collection; not registered in the initial release | Do not use as dimensions; range is `0–500 kN` | Approved for simulation; not used in this asset |

### 2.1 Missing, invalid and unassigned values

| Value | Treatment in this record |
|---|---|
| `(not set)` | Keep as a separate data-quality row; it means the requested dimension was unavailable. Do not count it as an approved `design_method`. |
| `Unassigned` | Treat as a GA4 classification/attribution issue; do not merge it with `(not set)`. |
| Invalid value | A value outside the approved list, wrong type, empty string or unexpected casing is excluded from the validated rate and linked to a QA/contract issue. |
| Missing `solution_found` | Event is not eligible for the validated rate; do not convert the missing value to `No`. |

## 3. Asset Configuration Records

### 3.1 Stable Detail Report — FD calculation outcome distribution

| Field | Configuration |
|---|---|
| Asset ID | `FD-REP-001` |
| Requirement ID | `REQ-FD-OUTCOME-001` |
| Name / surface | `FD Calculation Outcomes by Design Method` — Detail Report |
| Population / grain | Valid `calculation_action` events; one event represents one terminalized attempt |
| Date range / timezone | Selected reporting range / `Europe/London` from the simulated baseline |
| Dimensions | `design_method`, `solution_found` |
| Metric | Event count |
| Filters | `event_name=calculation_action`, `app_name=fd`, `event_schema_version=1.0`; exclude invalid and unapproved test traffic |
| Chart/table | Table with event count by method and outcome; bar chart for method comparison; line chart only when a time trend is required |
| Formula | Use the table counts to calculate event-level output rate; do not present two independent counts as a rate unless the numerator/denominator rule is visible. |
| Maintenance trigger | `FD-REC-07` schema, allowed-value, consent or Tag mapping change |
| Status | Simulated configuration only; not created or published |

This Detail Report is the governed surface for recurring counts and outcome distribution. It is not a user-level report.

### 3.2 Event-level QA Exploration — field and payload readiness

| Field | Configuration |
|---|---|
| Asset ID | `FD-EXP-001` |
| Requirement ID | `REQ-FD-OUTCOME-001` |
| Name / surface | `FD calculation_action — Event QA` — Free-form Exploration |
| Purpose | Inspect event-level rows and verify that approved dimensions, `solution_found`, schema version and method values are usable after processing |
| Dimensions | `event_name`, `event_schema_version`, `app_name`, `solution_found`, `design_method`, `country`, `language` |
| Metric | Event count |
| Filter | `event_name=calculation_action` and `app_name=fd` |
| Breakdown | `solution_found` and `design_method`; keep `(not set)`, `Unassigned` and invalid values visible for investigation |
| Date range | Small approved QA/review window; never infer production trend from it |
| Status | Simulated configuration only; not created or shared |

This Exploration is event-level QA. It does not prove Application, Data Layer, GTM or consent behavior; those checks belong to Section 08.

### 3.3 Funnel and Export/BigQuery decision

| Surface | Decision | Reason |
|---|---|---|
| Funnel Exploration | `N/A` for the current FD contract | There is no approved `calculation_start` → `calculation_action` sequence. A funnel would invent a start step or misrepresent one event as a multi-step journey. |
| Export / BigQuery | Conditional, not required for the current simulation | Use only when the team needs a repeatable exact rate across large periods, distinct users, custom joins or logic that GA4 UI cannot represent. Document identity, event-time logic, schema, access and processing delay before use. |
| Detail Report | Preferred for recurring event counts and outcome distribution | Stable, governed view after field readiness is confirmed. |
| Free-form Exploration | Preferred for event-level QA and investigation | Flexible breakdown for method, outcome and invalid-value review. |

## 4. Event-level output-rate formula

For an approved method X and one reporting window:

```text
Numerator   = event_count(calculation_action, design_method = X, solution_found = Yes)
Denominator = event_count(calculation_action, design_method = X,
                          solution_found ∈ {Yes, No})
Output rate = Numerator / Denominator
```

The denominator must use the same method, event name, schema version, property, stream, date range, consent-allowed population and event grain. Do not replace it with all events, all users, page views or a different method.

This is an **event-level attempt rate**, not a user-level completion rate. Because `solution_found=No` includes terminal errors, the metric must be labelled **combined output rate** or **combined No-outcome rate**, not pure solution success rate.

If an exact ratio cannot be reproduced from the selected GA4 surface, export the approved counts or use BigQuery after a separate access/privacy decision. Do not silently substitute another denominator.

## 5. Interpretation and Decision Note

| Field | Simulated entry |
|---|---|
| Note ID | `FD-INT-001` |
| Asset IDs | `FD-REP-001`, `FD-EXP-001` |
| Observed result | No processed result exists; expected design is one event per terminalized attempt and one approved `solution_found` value per event. |
| Interpretation | After runtime processing, compare output and combined No-outcome counts by approved `design_method`; keep invalid, `(not set)` and `Unassigned` rows separate. |
| What this does not prove | It does not prove user-level completion, causation, API correctness, consent execution, GTM firing or production data quality. |
| Data limitations | `No` combines empty response and terminal error; custom dimensions may require processing time; thresholding, sampling and cardinality must be checked after collection. |
| Decision/action | Pending runtime data; investigate a method only after field readiness and data-quality checks pass. |
| Owner / review trigger | FD Analytics owner / contract, field, consent, release or reporting-surface change |
| Status | Simulated interpretation note; no decision based on live data |

## 6. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live GA4 assets or processed data.

- [x] `FD-REC-09` uses the Section 09 record structure.
- [x] Business question, population, grain, scope and event-level formula are explicit.
- [x] User-level reporting is explicitly marked not approved for the current schema.
- [x] Detail Report and event-level QA Exploration are separated.
- [x] Funnel is marked `N/A` because no approved start/finish sequence exists.
- [x] Export/BigQuery is documented as a conditional exact-calculation path.
- [x] `(not set)`, `Unassigned`, invalid values and missing `solution_found` behavior are defined.
- [x] Custom-definition readiness and processing delay are recorded.
- [x] Interpretation limitations do not treat `No` as a pure no-solution metric.
- [x] Simulation boundary is explicit and no live report result is claimed.

## 7. Cross-references

- Section 07 / [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md): event meaning, allowlist, consent and schema.
- Section 08: collection evidence, event-level QA and runtime verification when authorized.
- Section 10: release impact, post-release checks and monitoring when this asset is later deployed.
- Sections 01–06: Application/Data Layer, Variable, Trigger, Tag, consent and template dependencies.
