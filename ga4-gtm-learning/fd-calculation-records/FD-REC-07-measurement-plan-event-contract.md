# FD-REC-07 — Measurement Plan & Event Contract

> This record applies the standard structure from [Section 07 — Measurement Plan](../07-measurement-plan-answer.md) to the FD project. It is not a new template. `FAKE`/`SIMULATED` values support design and review only; no live GA4/GTM setup exists.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-07` |
| Record name | Measurement Plan & Event Contract |
| Document type | `PROJECT RECORD` |
| Plan ID | `FD-MP-001` |
| Version | `1.0-approved` |
| Phase | Phase 1 — Measurement Plan and Event Contract |
| Product / journey | FD web application / `J-FD-CALC-001` |
| Source of truth | Event Contract and Parameter Dictionary in this record |
| Baseline dependency | [`FD-REC-00 — Phase 0 System Inventory Record`](FD-REC-00-phase-0-system-inventory.md) |
| Status | **Approved — simulation design** |
| Business owner | `[business owner — placeholder]` |
| Application owner | `fd-developer@strongtie.com` — simulated alias |
| Analytics owner | `fd-analytics-owner@strongtie.com` — simulated alias |
| GTM owner | `fd-gtm-implementer@strongtie.com` — simulated alias |
| Privacy/consent reviewer | `[privacy owner — placeholder]` |
| Value/evidence boundary | Contract decisions are approved for simulation; live configuration and runtime evidence are outside scope |
| Dependencies | `FD-REC-00` Phase 0 baseline |
| Open items / risks | Live setup, runtime evidence and production approval remain outside scope |
| Next action | Keep `FD-REC-09`, `FD-REC-08` and `FD-REC-10` as the baseline for any future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-04` / `2026-09-06` |

## 0.1 Source of the record format

This is the project-specific implementation of the standard Section 07 record structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Measurement Plan, record priority and event contract | [Section 07 — Measurement Plan](../07-measurement-plan-answer.md) |
| Data Layer envelope and snapshot boundary | [Section 01 — Data Layer Design](../01-data-layer-design-answer.md), [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) |
| GTM consumers and mapping boundary | Sections 02–06, [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| Simulated business values | FD calculation flow supplied for this journey |

## 0.2 Handoff summary

This record is handed to Business, Application, Analytics, Privacy and GTM owners before the simulation documents are completed. The FD Analytics/GTM Lead owns the semantic contract; functional owners approve decisions within their responsibility.

| Area | Current conclusion |
|---|---|
| Recommendation | Schema `1.0` is approved for simulation; Phase 3 records follow the happy-path assumption |
| Live implementation | Out of scope; Phase 0 uses a simulated baseline and Phase 2 has no runtime execution |
| Main risk | Wrong business moment, full-snapshot collection or deployment before consent/destination approval |
| Go condition | Schema `1.0`, Phase 2 handoff, Phase 3 records and Phase 4 reporting design are reviewed; Phase 5 simulation may continue |
| No-go condition | An out-of-schema change, missing consent/routing decision or simulated behavior that no longer matches the contract |

## 0.3 Purpose of the record set

`FD-REC-07` keeps one source of truth from the business question through Application, Data Layer, GTM, GA4, QA and reporting. It must answer:

1. What business fact does the event measure, and when is an occurrence valid?
2. Which fields may pass to GA4, and which must be removed?
3. Which consent, privacy, environment and destination rules apply?
4. Which consumers must migrate when the contract changes?

| Record area | Purpose | Risk controlled |
|---|---|---|
| Project Context / Baseline | Anchor product, journey, hostnames, streams, container, owners and consent | QA/production or destination confusion |
| Journey / Event Coverage Matrix | Convert the business question into the minimum event scope | Tracking every click/UI state without a decision use |
| Event Contract | Define meaning, authoritative moment, valid occurrence, failure and deduplication | Early measurement, no-output/error confusion and duplicates |
| Parameter Dictionary | Define name, type, values, source, required/optional, privacy and registration | Full-object/PII collection, wrong types and excessive definitions |
| Consent / Data Classification | Define collection behavior under `analytics_storage` | Consent bypass and restricted data in GA4 |
| Key-event / Custom-definition decisions | Separate collection from key-event and custom-definition decisions | Treating every calculation as a conversion |
| Traceability Matrix | Connect requirement → Application → Data Layer → GTM → consent → destination | Broken handoffs and unclear ownership |
| Schema Lifecycle Register | Track version, change, consumers, migration and approval | Silent semantic changes |

## 1. Project Context / Baseline

| Field | Value |
|---|---|
| Product / journey | FD calculation / `J-FD-CALC-001` |
| Business purpose | Measure calculation attempts with a terminal outcome and distinguish output from no-output/error |
| Platform / source | Client-side FD web application; Application is the business source of truth |
| QA hostname / stream | `app-staging.strongtie.com` / `FD Web — QA — SIMULATED` / `G-FAKEFDQA01` |
| Production hostname / stream | `app.strongtie.com` / `FD Web — Production — SIMULATED` / `G-FAKEFDPROD1` |
| GTM | `GTM-FAKEFD01`, workspace `WS-FD-CALC-001` |
| Environment routing | Hostname allowlist and hostname-to-Measurement-ID routing from `FD-REC-00`; unknown hostnames blocked |
| Timezone / currency | `Europe/London` / `GBP`; not event parameters |
| Consent baseline | `analytics_storage=denied` before consent; analytics allowed only when `granted` |
| Full snapshot boundary | Application may retain the API snapshot for QA; the full `inputs` object is not one GA4 parameter |

## 2. Journey / Event Coverage Matrix

| Journey ID | Journey | Business question | Expected event sequence | Primary outcome | Status |
|---|---|---|---|---|---|
| `J-FD-CALC-001` | FD calculation | For each recorded attempt, did the system produce a solution? | One terminal calculation attempt → `calculation_action` | `solution_found="Yes"` or `"No"` | Approved — schema `1.0` |

After valid input is entered and the Application finishes an attempt, FD records whether a solution was found. A response array with `length > 0` sends `Yes`; `[]` sends `No`. An API error, timeout, cancellation or stale terminal outcome also sends `No` under this contract. Input validation does not create an event. Because empty response and error are grouped under `No`, GA4 cannot calculate a pure no-solution rate without a separate error-status field; reports must call it a combined No-outcome rate.

## 3. Event Contract

| Field | Current decision |
|---|---|
| Requirement / journey ID | `FD-MP-001` / `J-FD-CALC-001` |
| Event name / type | `calculation_action` / Custom event |
| Definition | A calculation attempt that the Application ends with a valid response or contract-defined error after a complete input snapshot exists |
| Authoritative moment / source | Application terminal outcome; not a click, render, route change or isolated input change |
| Valid occurrence | Complete snapshot sent to the API and matching response or permitted error; `length > 0` → `Yes`, `[]`/error → `No` |
| Invalid input | UI validation → no event |
| API/server failure | HTTP 5xx/network error → one event with `solution_found="No"` |
| Timeout / cancellation | One event with `solution_found="No"` |
| Stale response | Terminalize the current attempt once as `No`; ignore late callbacks for an old snapshot |
| Retry / duplicate callback | Automatic retry in one attempt does not create an event; intentional new submit/snapshot is a new occurrence; duplicate, remount and replay do not add events |
| Data Layer signal | `event`, `event_schema_version="1.0"`, `app_name="fd"`, `solution_found="Yes"/"No"`, complete `inputs` |
| GA4 mapping boundary | Map only approved scalars; never send the `inputs` object, API response or internal token |
| GTM mapping | One authoritative Custom Event Trigger and one GA4 Event Tag in Phase 3 |
| Environment / destination | QA `G-FAKEFDQA01`; production `G-FAKEFDPROD1`; unapproved hostname has no destination |
| Consent / privacy | Follow `FD-REC-00` and Section 05; denied/unknown suppresses analytics |
| Key-event status | **Yes** |
| Custom-definition status | `solution_found` and five categorical fields are approved for event-scoped definitions; live creation remains outside this simulation |

## 4. Parameter Dictionary

### 4.1 Mapping principles

- The complete snapshot exists in the Application request and complete Data Layer message.
- GA4 receives only approved scalar parameters for a defined analysis question.
- Numeric scope contains only `fx` and `fy`; other numeric inputs are not mapped.
- Missing fields are contract errors; do not use empty strings, `unknown` or previous-event values.

### 4.1.1 Approved GA4 allowlist

```text
app_name
event_schema_version
solution_found
country
language
building_code
design_method
connection_type
fx
fy
```

`solution_found` must be the exact string `Yes` or `No`; GTM does not convert or infer the value. Phase 4 defines the custom-definition requirement; live creation remains outside this simulation.

### 4.2 Top-level fields

| Parameter | Meaning | Type | Required | Allowed value | Missing/invalid behavior | Source | GA4 registration |
|---|---|---|---|---|---|---|---|
| `event_schema_version` | Event contract version | String/event | Yes | `1.0` | Application does not emit | Data Layer top-level | Collect; no initial custom definition |
| `app_name` | Product emitting the event | String/event | Yes | `fd` | Application does not emit | Data Layer top-level | Collect; no initial custom definition |
| `solution_found` | Whether output was produced; empty/error are `No` | String/event | Yes | `Yes`, `No` | Application does not emit; GTM never infers | Data Layer top-level | Event-scoped custom dimension; specified in Phase 4, live creation pending |

### 4.3 Approved categorical snapshot fields

| Parameter | Meaning | Type | Required | Allowed value | Missing/invalid behavior | Source | GA4 registration |
|---|---|---|---|---|---|---|---|
| `country` | Country used for calculation | String/event | Yes in snapshot | Controlled code, e.g. `gb` | Do not emit complete event | `inputs.country` | Approved; definition specified in Phase 4, live creation pending |
| `language` | Calculation language | String/event | Yes in snapshot | Controlled code, e.g. `en` | Do not emit complete event | `inputs.language` | Approved; definition specified in Phase 4, live creation pending |
| `building_code` | Selected building/code standard | String/event | Yes in snapshot | Controlled code list | Do not emit complete event | `inputs.building_code` | Approved; definition specified in Phase 4, live creation pending |
| `design_method` | Design method | String/event | Yes in snapshot | Controlled list, e.g. `lsd` | Do not emit complete event | `inputs.design_method` | Approved; definition specified in Phase 4, live creation pending |
| `connection_type` | Connection type | String/event | Yes in snapshot | Controlled list | Do not emit complete event | `inputs.connection_type` | Approved; definition specified in Phase 4, live creation pending |

### 4.4 Numeric snapshot fields

| Parameter | Meaning | Type | Required | Allowed value | Missing/invalid behavior | Source | GA4 registration |
|---|---|---|---|---|---|---|---|
| `fx` | Input `fx` | Number/decimal/event | No | `0–500`, `kN` | Wrong type/range → validation; no event | `inputs.fx` | Approved; not registered initially |
| `fy` | Input `fy` | Number/decimal/event | No | `0–500`, `kN` | Wrong type/range → validation; no event | `inputs.fy` | Approved; not registered initially |

`inputs` is a composite Application/Data Layer object, not a GA4 parameter. Other example keys, API response bodies and correlation tokens are outside the GA4 payload.

## 5. Consent / Data Classification

| Data | Classification | Requirement | Denied behavior | Destination |
|---|---|---|---|---|
| `calculation_action` and approved scalars | Analytics | `analytics_storage=granted` | Suppress; no replay without a new decision | QA or production by hostname |
| Complete `inputs` snapshot | Internal Application QA data | Not mapped to GA4 | Application privacy policy applies | Calculation API/Application QA only |
| API response, secret, token or PII | Restricted/prohibited | Must never enter GA4 | Remove/suppress and open a defect | None |

Baseline: default `denied`, CMP is source of truth, and unknown/error is fail-safe `denied`. Implementation detail belongs to Section 05 and Phase 3.

## 6. Key-event / Custom-definition decisions

| Decision ID | Event/parameter | Decision | Status |
|---|---|---|---|
| `FD-DEC-001` | `calculation_action` — key event | Yes; material product outcome | Approved in review |
| `FD-DEC-002` | `solution_found` — custom dimension | Required, event-scoped; values `Yes`/`No` | Approved; specified in Phase 4; live creation pending |
| `FD-DEC-003` | Categorical inputs | Collect `country`, `language`, `building_code`, `design_method`, `connection_type` | Confirmed |
| `FD-DEC-004` | Numeric inputs | Collect optional `fx` and `fy`, `kN`, `0–500`; no initial definitions | Confirmed |
| `FD-DEC-005` | `event_schema_version`, `app_name` | Collect for contract/diagnostics; no initial definitions | Confirmed |

## 7. Traceability Matrix

| Requirement / event | Application | Data Layer | GTM | Consent | Destination |
|---|---|---|---|---|---|
| Valid output | Matching response, `length > 0` | `calculation_action`, `Yes`, complete snapshot | Authoritative Trigger → GA4 Event Tag | `granted` | QA/prod by hostname |
| Valid no-output | Matching response `[]` | `calculation_action`, `No`, complete snapshot | Same Trigger/Tag; no GTM inference | `granted` | QA/prod by hostname |
| Input validation | UI validation | No event | Trigger does not match | No analytics | None |
| API failure/timeout/cancel | Application classifies error | `calculation_action`, `No` | Same Trigger/Tag | `granted` | QA/prod by hostname |
| Stale response | Current attempt terminalizes; late callback ignored | One `No`; no late event | Same Trigger/Tag | `granted` | QA/prod by hostname |
| Duplicate callback | Repeated callback for one attempt | One push only | One Trigger match/request | Per consent | QA/prod by hostname |

Variable, Trigger and Tag names are simulated in `FD-REC-02`–`FD-REC-04`; Phase 1 defines the contract and handoff only.

## 8. Schema Lifecycle Register

| Change ID | Event/parameter | Current version | Proposed version | Change type | Affected consumers | Migration/handoff | Approval/date | Status |
|---|---|---|---|---|---|---|---|---|
| `FD-CHG-001` | `calculation_action` and parameter contract | None | `1.0` | Initial approval | Application, Data Layer, GTM, GA4, QA, reporting | Phase 2 uses the contract; Phase 3 maps GTM in simulation; Phase 4 defines reporting/custom-definition requirements | Business + Application + Analytics + Privacy / `2026-09-04` | Approved |

Changing `solution_found`, valid occurrence, required fields or allowed values requires a new lifecycle change and consumer review. Do not silently reuse schema `1.0`.

## 9. Downstream implementation constraints

| Consumer | Required constraint |
|---|---|
| Application / Data Layer | Keep the snapshot immutable; correlate with `attempt_id`, `snapshot_id`, `request_id` and `retry_index`; emit only after terminal outcome; validation does not emit; terminal errors emit `No`; use schema `1.0`. |
| GTM | One authoritative Custom Event Trigger; scalar allowlist only; no full `inputs` or API response; apply consent and hostname routing; store assets in Sections 02–04. |
| GA4 | Create only approved custom definitions; do not register every parameter automatically; preserve the approved business meaning in reports/charts. |
| QA / Evidence | Define output, no-output, validation, failure, stale, retry/duplicate, hostname and consent expectations; no runtime evidence exists here. |

## 10. Approval decision

### 10.1 Approved decision summary

| Decision area | Approved result |
|---|---|
| Business question | Measure terminal attempts and distinguish output/no-output |
| Business moment | Application is authoritative; push only after response/error classification |
| Outcome mapping | `length > 0` → `Yes`; `[]` or API error → `No` |
| Validation and duplicates | Validation does not emit; duplicate, stale/late, remount/replay callbacks add no events |
| Numeric domain | `fx`, `fy`: number/decimal, `kN`, `0–500`, optional |
| GA4 allowlist | Ten approved fields; no full `inputs`, response, token, secret or PII |
| Consent | `analytics_storage=denied` or `unknown` → suppress analytics |
| Custom definitions | `solution_found` plus five categorical fields; no initial definitions for `fx`, `fy`, `app_name`, `event_schema_version` |
| Schema lifecycle | Schema `1.0` approved; semantic changes require a new lifecycle change |

### 10.2 Approval boundary

| Item | Result |
|---|---|
| Record status | `Approved` |
| Schema | `1.0` |
| Phase 2 handoff | Documentation prepared through `FD-REC-01`; happy-path assumption only |
| Live GA4/GTM configuration | Not performed |
| Runtime evidence | Not applicable; simulated examples only |
| Open decisions | None in Phase 1; `solution_found="No"` is the combined no-output/error outcome |
| Approval date | `2026-09-04` |

## 11. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not replace business approval or runtime evidence.

- [x] Business question, authoritative occurrence and event grain are explicit.
- [x] Event contract, parameter allowlist, consent boundary and downstream use are documented.
- [x] Ownership, schema lifecycle and change-impact rules are recorded.
- [x] Dependent records are linked before implementation work begins.
- [x] Simulation approval is separated from production approval and runtime verification.
