# FD-REC-09 — GA4 Report and Exploration Record

> **SIMULATED — Phase 4 documentation only.** This record defines the GA4 reporting assets for `calculation_action`; it does not create custom definitions, publish Reports, run Explorations or inspect processed GA4 data.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-09` |
| Record name | GA4 Report and Exploration Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 4 — GA4 custom definitions, Reports and Explorations |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for historical interpretation |
| Why this matters | Current assets require a registered schema dimension, a schema `3.0` filter and an explicit boundary from schemas `1.0`/`2.0`. |
| Collection dependency | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| QA dependency | Section 08; runtime evidence is not available in the current project |
| Status | **Design complete — simulation documentation; `FD-CR-002` applied; runtime blocked** |
| Execution state | Simulation only — no live GA4 property or processed-data query |
| Value/evidence boundary | Asset names, definitions, formulas and expected results are simulated; no Report/Exploration is configured or published |
| Primary owner | FD Analytics owner — simulated alias |
| Reviewers | Business owner, Application owner, GTM owner, Privacy reviewer — simulated aliases |
| Open items | `FD-OPEN-001`/`004`, custom-definition creation, field availability, baseline and processed-data validation remain unresolved |
| Next action | Resolve the blockers, register the approved dimensions, then build the schema `3.0` assets and validate processed data |
| Created / last updated | `2026-09-07` / `2026-09-07` — `FD-CR-002` |

## 1. Report Requirement Record

| Field | Recorded value |
|---|---|
| Requirement ID | `REQ-FD-OUTCOME-001` |
| Audience / owner | FD product and analytics team / FD Analytics owner |
| Business question | Among recorded FD calculation attempts, what proportion returned an output, and how does the result vary by approved `design_method`? |
| Decision/action | Investigate a persistently low output share or an unexpected method-level difference; do not treat `No_solution` as a pure no-solution result because the contract still groups empty responses and terminal errors. Keep pre-release `No` and post-release `No_solution` separate unless a migration rule is approved. |
| Cadence | Weekly and after a release that changes calculation tracking or the `calculation_action` contract |
| Property / stream | QA and production destinations from `FD-REC-00`; simulated IDs only |
| Population / exclusions | Valid `calculation_action` events in the approved FD property/stream with registered `event_schema_version=3.0`, approved `design_method` and consent-allowed collection; exclude unapproved test traffic and keep schemas `1.0`/`2.0` as separate historical populations |
| Grain | Event-level calculation attempt. The Application contract expects one event per terminalized occurrence. |
| User-level report | Not approved for this schema. No user-level business question or user-scoped field is defined in `FD-REC-07`; do not relabel event counts as users. |
| Event-level rate | For method X in one schema/release population: numerator = `calculation_action` events with `solution_found=Yes` and `design_method=X`; denominator = `calculation_action` events with `design_method=X` and `solution_found` in `{Yes, No_solution}`; rate = numerator ÷ denominator. Do not mix schemas `1.0`, `2.0` and `3.0` without an approved normalized historical model. |
| Required dimensions | `event_name` plus registered `FD Design Method`, `FD Solution Found` and `FD Event Schema Version`; release boundary is the schema plus effective date from `FD-REC-10`, not a separate undefined dimension |
| Required metric | Event count |
| Same-scope rule | Numerator and denominator use the same property, stream, date range, event eligibility, method value, consent policy and event grain. |
| Review trigger | Contract/schema, allowed-value, consent, routing, Tag, custom-definition or reporting requirement change |

The one-event-per-occurrence rule is what makes this an event-level attempt rate. If a future requirement asks for a distinct-user rate, the Measurement Plan must first add an approved identity and user-level population rule; this record must not infer one.

## 2. Field Readiness Record

Field readiness is simulated because no live event, custom definition or processed GA4 row exists in this project.

| Field | Meaning and source | Scope | Registration / readiness | Compatibility and limitation | Status |
|---|---|---|---|---|---|
| `calculation_action` | Canonical event from Application Data Layer → GTM | Event | Standard event name; expected after collection | Must be available in the selected property/stream after processing | Approved for simulation; runtime pending |
| `solution_found` | `Yes`/`No_solution` outcome from Application under schema `3.0`; source `inputs` is not used | Event parameter | Event-scoped custom dimension approved in `FD-REC-07`; create before a live Report/Exploration | `No_solution` still combines empty response and terminal error; schema `1.0` used `No` | Approved for simulation; processed availability pending |
| `design_method` | Approved calculation method; source `inputs.design_method` | Event parameter | Event-scoped custom dimension approved in `FD-REC-07` | Controlled values only; invalid values are not valid method rows | Approved for simulation; processed availability pending |
| `country` | Source `inputs.country` | Event parameter | Event-scoped custom dimension approved | Use only when the analysis requires it | Approved for simulation; processed availability pending |
| `language` | Source `inputs.language` | Event parameter | Event-scoped custom dimension approved | Use only when the analysis requires it | Approved for simulation; processed availability pending |
| `building_code` | Source `inputs.building_code` | Event parameter | Event-scoped custom dimension approved | Controlled code list; review cardinality | Approved for simulation; processed availability pending |
| `connection_type` | Source `inputs.connection_type` | Event parameter | Event-scoped custom dimension approved | Controlled list; review cardinality | Approved for simulation; processed availability pending |
| `event_schema_version` | Top-level contract version | Event parameter | Event-scoped custom dimension required before the governed assets are created | Filter current assets to `3.0`; use `1.0`/`2.0` only in explicitly historical analysis | Approved for simulation; creation/processed availability pending |
| `app_name` | Top-level product identifier | Event parameter | Collect for payload diagnostics; no custom definition in the dedicated FD streams | Do not use as a GA4 UI dimension/filter unless a future shared-stream requirement approves registration | Approved for simulation; not used by current assets |
| `event_id` | Opaque per-occurrence UUID | Event parameter | Collect for runtime/Network and approved export deduplication; never register as a custom dimension | High-cardinality; not User-ID; privacy/retention pending `FD-OPEN-004` | Approved design; runtime/privacy pending |
| `fx`, `fy` | Application/API numeric inputs | Not in analytics schema `3.0` | Not collected or registered | Add only through a new approved measurement requirement and CR | Not applicable |

### 2.1 Custom-definition registration plan

Create these event-scoped custom dimensions before building the governed GA4 UI assets. The display name is the field selected in Reports/Explorations; the event parameter is the value sent by GTM.

| GA4 display name | Event parameter | Scope | Registration status |
|---|---|---|---|
| `FD Solution Found` | `solution_found` | Event | Approved design; live creation/processed availability pending |
| `FD Event Schema Version` | `event_schema_version` | Event | Approved design; required for current asset filters; live creation/processed availability pending |
| `FD Calculation Country` | `country` | Event | Approved design; live creation/processed availability pending |
| `FD Calculation Language` | `language` | Event | Approved design; live creation/processed availability pending |
| `FD Building Code` | `building_code` | Event | Approved design; live creation/processed availability pending |
| `FD Design Method` | `design_method` | Event | Approved design; live creation/processed availability pending |
| `FD Connection Type` | `connection_type` | Event | Approved design; live creation/processed availability pending |

Do not register `event_id` or `app_name` for the current dedicated FD streams. Record the real definition creation date, property, creator and first processed-availability date in the runtime project. Do not assume the GA4 UI definition backfills values collected before registration; set the validated report start boundary from observed processed availability.

### 2.2 Missing, invalid and unassigned values

| Value | Treatment in this record |
|---|---|
| `(not set)` | Keep as a separate data-quality row; it means the requested dimension was unavailable. Do not count it as an approved `design_method`. |
| `Unassigned` | Treat as a GA4 classification/attribution issue; do not merge it with `(not set)`. |
| Invalid value | A value outside the approved list, wrong type, empty string or unexpected casing is excluded from the validated rate and linked to a QA/contract issue. |
| Missing `solution_found` | Event is not eligible for the validated rate; do not convert the missing value to `No` or `No_solution`. |

## 3. Asset Configuration Records

### 3.1 Stable Detail Report — FD calculation outcome distribution

| Field | Configuration |
|---|---|
| Asset ID | `FD-REP-001` |
| Requirement ID | `REQ-FD-OUTCOME-001` |
| Name / surface | `FD Calculation Outcomes by Design Method` — Detail Report |
| Population / grain | Valid `calculation_action` events; one event represents one terminalized attempt |
| Date range / timezone | Selected reporting range / `Europe/London` from the simulated baseline |
| Dimensions | Registered `FD Design Method`, `FD Solution Found` |
| Metric | Event count |
| Filters | `event_name=calculation_action`, registered `FD Event Schema Version` exactly `3.0`; use the approved FD property/stream and exclude invalid/unapproved test traffic |
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
| Dimensions | `event_name`, registered `FD Event Schema Version`, `FD Solution Found`, `FD Design Method`, `FD Calculation Country`, `FD Calculation Language`; use release effective date plus schema version for the release boundary |
| Metric | Event count |
| Filter | `event_name=calculation_action` and registered `FD Event Schema Version` exactly `3.0` in the approved FD property/stream |
| Breakdown | Registered `FD Solution Found` and `FD Design Method`; keep `(not set)`, `Unassigned` and invalid values visible for investigation |
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
Numerator   = event_count(calculation_action, schema = 3.0, design_method = X, solution_found = Yes)
Denominator = event_count(calculation_action, schema = 3.0, design_method = X,
                          solution_found ∈ {Yes, No_solution})
Output rate = Numerator / Denominator
```

The denominator must use the same method, event name, schema version, property, stream, date range, consent-allowed population and event grain. Do not replace it with all events, all users, page views or a different method.

This is an **event-level attempt rate**, not a user-level completion rate. Because `solution_found=No_solution` includes terminal errors, the metric must be labelled **combined output rate** or **combined No-outcome rate**, not pure solution success rate. Schemas `1.0` and `2.0` must remain separate from current schema `3.0` unless a normalized historical model is approved.

If an exact ratio cannot be reproduced from the selected GA4 surface, export the approved counts or use BigQuery after a separate access/privacy decision. Do not silently substitute another denominator.

## 5. Interpretation and Decision Note

| Field | Simulated entry |
|---|---|
| Note ID | `FD-INT-001` |
| Asset IDs | `FD-REP-001`, `FD-EXP-001` |
| Observed result | No processed result exists; expected design is one event per terminalized attempt and one approved `solution_found` value per event. |
| Interpretation | After runtime processing, compare output and combined No-outcome counts by approved `design_method`; keep invalid, `(not set)` and `Unassigned` rows separate. |
| What this does not prove | It does not prove user-level completion, causation, API correctness, consent execution, GTM firing or production data quality. |
| Data limitations | `No_solution` combines empty response and terminal error pending `FD-OPEN-001`; version 1 uses `No`; custom dimensions may require processing time; thresholding, sampling and cardinality must be checked after collection. `event_id` is not available as a GA4 UI dimension. |
| Decision/action | Pending runtime data; investigate a method only after field readiness and data-quality checks pass. |
| Owner / review trigger | FD Analytics owner / contract, field, consent, release or reporting-surface change |
| Status | Simulated interpretation note; no decision based on live data |

## 6. Acceptance criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live GA4 assets or processed data.

- [x] `FD-REC-09` uses the Section 09 record structure.
- [x] Business question, population, grain, scope and event-level formula are explicit.
- [x] User-level reporting is explicitly marked not approved for the current schema.
- [x] Detail Report and event-level QA Exploration are separated.
- [x] Funnel is marked `N/A` because no approved start/finish sequence exists.
- [x] Export/BigQuery is documented as a conditional exact-calculation path.
- [x] `(not set)`, `Unassigned`, invalid values and missing `solution_found` behavior are defined.
- [x] Parameter names and GA4 display names are explicitly mapped; `event_id` and `app_name` are excluded from registration.
- [x] Custom-definition readiness and processing delay are recorded.
- [x] Interpretation limitations do not treat `No_solution` as a pure no-solution metric and preserve the version 1 `No` boundary.
- [x] Simulation boundary is explicit and no live report result is claimed.
