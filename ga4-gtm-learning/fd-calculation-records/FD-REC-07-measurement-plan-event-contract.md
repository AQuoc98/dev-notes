# FD-REC-07 — Measurement Plan & Event Contract

> This record applies the standard structure from [Section 07 — Measurement Plan](../07-measurement-plan-answer.md) to the FD project. It is not a new template. `FAKE`/`SIMULATED` values support design and review only; no live GA4/GTM setup exists.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-07` |
| Record name | Measurement Plan & Event Contract |
| Document type | `PROJECT RECORD` |
| Plan ID | `FD-MP-001` |
| Version | `3.0-simulated` — current simulation state after `FD-CR-002` |
| Phase | Phase 1 — Measurement Plan and Event Contract |
| Product / journey | FD web application / `J-FD-CALC-001` |
| Source of truth | Event Contract and Parameter Dictionary in this record |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for the historical outcome rename |
| Why this matters | This record owns the semantic and data contract; schema `3.0` minimizes the analytics payload, adds occurrence correlation and makes runtime gates explicit. |
| Baseline dependency | [`FD-REC-00 — Phase 0 System Inventory Record`](FD-REC-00-phase-0-system-inventory.md) |
| Status | **Approved — simulation design; `FD-CR-002` applied; runtime blocked** |
| Business owner | `[business owner — placeholder]` |
| Application owner | `fd-developer@strongtie.com` — simulated alias |
| Analytics owner | `fd-analytics-owner@strongtie.com` — simulated alias |
| GTM owner | `fd-gtm-implementer@strongtie.com` — simulated alias |
| Privacy/consent reviewer | `[privacy owner — placeholder]` |
| Value/evidence boundary | Contract decisions are approved for simulation; live configuration and runtime evidence are outside scope |
| Dependencies | `FD-REC-00` Phase 0 baseline; `FD-CR-001` outcome rename; `FD-CR-002` runtime-readiness hardening |
| Open items / risks | Live setup, runtime evidence and production approval remain outside scope; `FD-OPEN-001` through `FD-OPEN-004` in the master journey block runtime readiness |
| Next action | Resolve `FD-OPEN-001`–`004`, then use `FD-REC-01` through `FD-REC-11` as the schema `3.0` runtime packet |
| Created / last updated | `2026-09-04` / `2026-09-07` |

### Version history

| Contract version | Status | Change Request | Allowed values | Effective meaning |
|---|---|---|---|---|
| `1.0-approved` | Version 1 historical baseline | — | `Yes`, `No` | Empty response and terminal error both used `No` |
| `2.0-simulated` | Historical simulation baseline | `FD-CR-001` | `Yes`, `No_solution` | Outcome rename; full analytics snapshot and no cross-layer event identifier |
| `3.0-simulated` | Current simulation state | `FD-CR-002` | `Yes`, `No_solution` | Retains the combined outcome; minimizes the analytics payload, adds `event_id` and strict completeness gates |


## 0.2 Handoff summary

This record is handed to Business, Application, Analytics, Privacy and GTM owners before the simulation documents are completed. The FD Analytics/GTM Lead owns the semantic contract; functional owners approve decisions within their responsibility.

| Area | Current conclusion |
|---|---|
| Recommendation | Keep schemas `1.0` and `2.0` as historical baselines; use schema `3.0` after `FD-CR-002` as the current simulation contract |
| Live implementation | Out of scope; Phase 0 uses a simulated baseline and Phase 2 has no runtime execution |
| Main risk | Wrong business moment, full-snapshot collection or deployment before consent/destination approval |
| Go condition | `FD-OPEN-001`–`004` are resolved, schema `3.0` handoffs are accepted, consent/destination are approved and Phase 5 runtime evidence passes |
| Applied changes | `FD-CR-001` renamed the combined `No` outcome; `FD-CR-002` introduces schema `3.0` hardening without splitting that outcome |
| No-go condition | Any open runtime blocker, out-of-schema change, missing consent/routing decision or behavior that no longer matches the contract |

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
| Full snapshot boundary | Application retains the complete API snapshot internally. The analytics Data Layer contains only the approved subset; the nested `inputs` object is flattened to approved scalar GA4 parameters. |

## 2. Journey / Event Coverage Matrix

| Journey ID | Journey | Business question | Expected event sequence | Primary outcome | Status |
|---|---|---|---|---|---|
| `J-FD-CALC-001` | FD calculation | For each recorded attempt, did the system produce a solution? | One terminal calculation attempt → `calculation_action` | `solution_found="Yes"` or `"No_solution"` | Approved for simulation — schema `3.0`; runtime blocked |

After valid input is entered and the Application finishes an attempt, FD records whether a solution was found. A response array with `length > 0` sends `Yes`; `[]` sends `No_solution`. An API error, timeout, cancellation or stale terminal outcome also sends `No_solution` under this contract. Input validation does not create an event. The value `No_solution` remains a combined no-output/error outcome in this simulation; GA4 cannot calculate a pure no-solution rate without a separate error-status field, so reports must call it a combined No-outcome rate.

## 3. Event Contract

| Field | Current decision |
|---|---|
| Requirement / journey ID | `FD-MP-001` / `J-FD-CALC-001` |
| Event name / type | `calculation_action` / Custom event |
| Definition | A calculation attempt that the Application ends with a valid response or contract-defined error after a complete input snapshot exists |
| Authoritative moment / source | Application terminal outcome; not a click, render, route change or isolated input change |
| Valid occurrence | Complete snapshot sent to the API and matching response or permitted error; `length > 0` → `Yes`, `[]`/error → `No_solution` |
| Invalid input | UI validation → no event |
| API/server failure | HTTP 5xx/network error → one event with `solution_found="No_solution"` |
| Timeout / cancellation | One event with `solution_found="No_solution"` |
| Stale response | Terminalize the current attempt once as `No_solution`; ignore late callbacks for an old snapshot |
| Retry / duplicate callback | Automatic retry in one attempt does not create an event; intentional new submit/snapshot is a new occurrence; duplicate, remount and replay do not add events |
| Data Layer signal | `event`, opaque per-occurrence `event_id`, `event_schema_version="3.0"`, `app_name="fd"`, `solution_found="Yes"/"No_solution"`, minimized analytics `inputs` subset |
| GA4 mapping boundary | Map only approved scalars; never send the `inputs` object, API response or internal token |
| GTM mapping | One authoritative Custom Event Trigger and one GA4 Event Tag in Phase 3 |
| Environment / destination | QA `G-FAKEFDQA01`; production `G-FAKEFDPROD1`; unapproved hostname has no destination |
| Consent / privacy | Follow `FD-REC-00` and Section 05; denied/unknown suppresses analytics |
| Key-event status | **Pending `FD-OPEN-002`**; do not mark the base event as a key event before the business success condition is approved |
| Custom-definition status | `solution_found`, `event_schema_version` and five categorical fields are approved for event-scoped definitions; live creation remains outside this simulation. `event_id` and `app_name` are not registered. |

## 4. Parameter Dictionary

### 4.1 Mapping principles

- The complete Calculation API snapshot exists only in the Application snapshot store and controlled QA evidence.
- The analytics Data Layer message contains only the approved analytics subset plus opaque `event_id`.
- GA4 receives only approved scalar parameters for a defined analysis question.
- Numeric inputs such as `fx` and `fy` are not mapped in schema `3.0` because the current report has no approved consumer for them.
- Missing fields are contract errors; do not use empty strings, `unknown` or previous-event values.

### 4.1.1 Approved GA4 allowlist

```text
app_name
event_id
event_schema_version
solution_found
country
language
building_code
design_method
connection_type
```

`solution_found` must be the exact string `Yes` or `No_solution`; GTM does not convert or infer the value. `event_id` is a random UUID used for cross-layer occurrence reconciliation, is not a User-ID, and must not be registered as a custom dimension. Phase 4 defines the custom-definition requirement; live creation remains outside this simulation.

### 4.2 Top-level fields

| Parameter | Meaning | Type | Required | Allowed value | Missing/invalid behavior | Source | GA4 registration |
|---|---|---|---|---|---|---|---|
| `event_id` | Opaque analytics occurrence identifier | String/event | Yes | Random UUID; must not encode user/account/request/snapshot data | Application does not emit; GTM blocks malformed/missing values | Data Layer top-level | Collect for QA/export deduplication; never register as custom dimension |
| `event_schema_version` | Event contract version | String/event | Yes | `3.0` | Application does not emit | Data Layer top-level | Event-scoped custom dimension required for governed schema filtering; live creation pending |
| `app_name` | Product emitting the event | String/event | Yes | `fd` | Application does not emit | Data Layer top-level | Collect; no initial custom definition |
| `solution_found` | Whether output was produced; empty/error are `No_solution` | String/event | Yes | `Yes`, `No_solution` | Application does not emit; GTM never infers | Data Layer top-level | Event-scoped custom dimension; specified in Phase 4, live creation pending |

### 4.3 Approved categorical snapshot fields

| Parameter | Meaning | Type | Required | Allowed value | Missing/invalid behavior | Source | GA4 registration |
|---|---|---|---|---|---|---|---|
| `country` | Country used for calculation | String/event | Yes in snapshot | Controlled code, e.g. `gb` | Do not emit complete event | `inputs.country` | Approved; definition specified in Phase 4, live creation pending |
| `language` | Calculation language | String/event | Yes in snapshot | Controlled code, e.g. `en` | Do not emit complete event | `inputs.language` | Approved; definition specified in Phase 4, live creation pending |
| `building_code` | Selected building/code standard | String/event | Yes in snapshot | Controlled code list | Do not emit complete event | `inputs.building_code` | Approved; definition specified in Phase 4, live creation pending |
| `design_method` | Design method | String/event | Yes in snapshot | Controlled list, e.g. `lsd` | Do not emit complete event | `inputs.design_method` | Approved; definition specified in Phase 4, live creation pending |
| `connection_type` | Connection type | String/event | Yes in snapshot | Controlled list | Do not emit complete event | `inputs.connection_type` | Approved; definition specified in Phase 4, live creation pending |

### 4.4 Controlled vocabulary and cardinality gate

| Parameter | Required registry before runtime | Cardinality/quality rule | Display-name rule | Owner/status |
|---|---|---|---|---|
| `country` | Approved calculation-country codes; do not infer browser/geolocation country | Low controlled list; unexpected codes block the event | Use **FD Calculation Country** so analysts do not confuse it with GA4 geographic Country | Application + Analytics / pending `FD-OPEN-004` |
| `language` | Approved calculation-language codes | Low controlled list; document casing/locale format | Use **FD Calculation Language** so analysts do not confuse it with GA4 device/browser Language | Application + Analytics / pending `FD-OPEN-004` |
| `building_code` | Versioned building-code registry | Controlled list; review additions through a CR | **FD Building Code** | Product + Application / pending registry link |
| `design_method` | Versioned method registry | Low controlled list | **FD Design Method** | Product + Application / pending registry link |
| `connection_type` | Versioned connection-type registry | Monitor cardinality and `(other)` risk before adding codes | **FD Connection Type** | Product + Application / pending registry link |

`inputs` is a minimized analytics namespace in the Data Layer, not a GA4 parameter. Numeric inputs such as `fx`/`fy`, other API fields, API response bodies and internal correlation tokens remain outside the analytics payload. Any future numeric analytics use needs a stated business question, unit, privacy/cardinality review, GA4 custom-metric/export decision and a new CR.

## 5. Consent / Data Classification

| Data | Classification | Requirement | Denied behavior | Destination |
|---|---|---|---|---|
| `calculation_action`, opaque `event_id` and approved scalars | Analytics | `analytics_storage=granted`; `event_id` retention/privacy approval required | Suppress; no replay without a new decision | QA or production by hostname |
| Complete Calculation API snapshot | Internal Application QA data | Never placed in the analytics Data Layer or mapped to GA4 | Application privacy policy applies | Calculation API/Application QA only |
| API response, secret, token or PII | Restricted/prohibited | Must never enter GA4 | Remove/suppress and open a defect | None |

Baseline: default `denied`, CMP is source of truth, and unknown/error is fail-safe `denied`. Implementation detail belongs to Section 05 and Phase 3.

## 6. Key-event / Custom-definition decisions

| Decision ID | Event/parameter | Decision | Status |
|---|---|---|---|
| `FD-DEC-001` | `calculation_action` — key event | Pending; the base event currently includes no-output and technical terminal outcomes | Blocked by `FD-OPEN-002`; do not configure as key event |
| `FD-DEC-002` | `solution_found` — custom dimension | Required, event-scoped; values `Yes`/`No_solution` in schemas `2.0`/`3.0`; schema `1.0` used `Yes`/`No` | Approved; specified in Phase 4; live creation pending |
| `FD-DEC-003` | Categorical inputs | Collect `country`, `language`, `building_code`, `design_method`, `connection_type` | Confirmed |
| `FD-DEC-004` | Numeric inputs | Do not collect `fx`/`fy` in analytics schema `3.0`; no approved report consumer exists | Confirmed for data minimization |
| `FD-DEC-005` | `event_schema_version`, `app_name` | Collect both; register `event_schema_version` for governed report filtering; keep `app_name` diagnostic-only in the dedicated FD streams | Confirmed |
| `FD-DEC-006` | `event_id` | Collect opaque per-occurrence UUID for QA/export reconciliation; never register as a dimension or use as identity | Privacy/retention approval pending `FD-OPEN-004` |

## 7. Traceability Matrix

| Requirement / event | Application | Data Layer | GTM | Consent | Destination |
|---|---|---|---|---|---|
| Valid output | Matching response, `length > 0` | `calculation_action`, `Yes`, opaque `event_id`, minimized approved subset | Authoritative Trigger → GA4 Event Tag | `granted` | QA/prod by hostname |
| Valid no-output | Matching response `[]` | `calculation_action`, `No_solution`, opaque `event_id`, minimized approved subset | Same Trigger/Tag; no GTM inference | `granted` | QA/prod by hostname |
| Input validation | UI validation | No event | Trigger does not match | No analytics | None |
| API failure/timeout/cancel | Application classifies error | `calculation_action`, `No_solution` | Same Trigger/Tag | `granted` | QA/prod by hostname |
| Stale response | Current attempt terminalizes; late callback ignored | One `No_solution`; no late event | Same Trigger/Tag | `granted` | QA/prod by hostname |
| Duplicate callback | Repeated callback for one attempt | One push only | One Trigger match/request | Per consent | QA/prod by hostname |

Variable, Trigger and Tag names are simulated in `FD-REC-02`–`FD-REC-04`; Phase 1 defines the contract and handoff only.

## 8. Schema Lifecycle Register

| Change ID | Event/parameter | Current version | Proposed version | Change type | Affected consumers | Migration/handoff | Approval/date | Status |
|---|---|---|---|---|---|---|---|---|
| `FD-CHG-001` | `calculation_action` and parameter contract | None | `1.0` | Initial approval | Application, Data Layer, GTM, GA4, QA, reporting | Phase 2 uses the contract; Phase 3 maps GTM in simulation; Phase 4 defines reporting/custom-definition requirements | Business + Application + Analytics + Privacy / `2026-09-04` | Approved |
| `FD-CR-001` | `solution_found` and dependent schema contract | `1.0` | `2.0` | Rename approved combined `No` value to `No_solution`; breaking allowed-value change | Application, Data Layer, GTM, GA4, QA, reporting, release monitoring | Update `FD-REC-01`–`FD-REC-10`; separate pre-release `No` from post-release `No_solution`; runtime migration remains pending | Business + Application + Analytics + Privacy / `2026-09-07` | Approved for simulation; runtime pending |
| `FD-CR-002` | Analytics payload and operational controls | `2.0` | `3.0` | Minimize Data Layer, add `event_id`, remove `fx`/`fy`, enforce completeness and harden consent/report/QA/release gates | Application, Data Layer, GTM, GA4, QA, reporting, release monitoring | Verify the active baseline; deploy v3 directly when no live contract exists, or use temporary v2/v3 compatibility when live v2 is proved; close `FD-OPEN-001`–`004` before runtime approval | Business + Application + Analytics + Privacy / `[pending runtime approval]` | Approved for simulation; runtime blocked |

Changing `solution_found`, valid occurrence, required fields or allowed values requires a new lifecycle change and consumer review. `FD-CR-001` records v1→v2 and `FD-CR-002` records v2→v3; do not silently reuse an older schema for current events.

## 9. Downstream implementation constraints

| Consumer | Required constraint |
|---|---|
| Application / Data Layer | Keep the complete API snapshot internal and immutable; correlate with internal IDs; create one opaque `event_id`; emit only the minimized analytics subset after terminal outcome; validation does not emit; terminal errors emit `No_solution` under the current combined contract; use schema `3.0`. |
| GTM | One authoritative Custom Event Trigger; scalar allowlist only; no full `inputs` or API response; apply consent and hostname routing; store assets in Sections 02–04. |
| GA4 | Create only approved custom definitions; do not register every parameter automatically; preserve the approved business meaning in reports/charts. |
| QA / Evidence | Define output, no-output, validation, failure, stale, retry/duplicate, hostname and consent expectations; no runtime evidence exists here. |

## 10. Approval decision

### 10.1 Approved decision summary

| Decision area | Approved result |
|---|---|
| Business question | Measure terminal attempts and distinguish output from the current combined no-output/error outcome |
| Business moment | Application is authoritative; push only after response/error classification |
| Outcome mapping | `length > 0` → `Yes`; `[]` or API error → `No_solution` |
| Validation and duplicates | Validation does not emit; duplicate, stale/late, remount/replay callbacks add no events |
| Numeric domain | `fx`, `fy` remain Application/API inputs and are not analytics parameters in schema `3.0` |
| GA4 allowlist | Nine approved scalar fields including opaque `event_id`; no full API snapshot/`inputs`, response, internal token, secret or PII |
| Consent | `analytics_storage=denied` or `unknown` → suppress analytics |
| Custom definitions | `solution_found`, `event_schema_version` plus five categorical fields; no definition for `event_id` or `app_name` |
| Schema lifecycle | Schema `3.0` approved for simulation through `FD-CR-002`; runtime approval, migration and processed-data validation remain pending |

### 10.2 Approval boundary

| Item | Result |
|---|---|
| Record status | `Approved for simulation update — runtime blocked` |
| Schema | `3.0` |
| Phase 2 handoff | Documentation prepared through `FD-REC-01`; happy-path assumption only |
| Live GA4/GTM configuration | Not performed |
| Runtime evidence | Not applicable; simulated examples only |
| Open decisions | `FD-OPEN-001` outcome separation, `FD-OPEN-002` key-event status, `FD-OPEN-003` consent implementation and `FD-OPEN-004` vocabulary/`event_id` privacy block runtime readiness |
| Approval date | `2026-09-07` for `FD-CR-002` simulation design; runtime approval pending; baseline approval was `2026-09-04` |

## 11. Acceptance criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not replace business approval or runtime evidence.

- [x] Business question, authoritative occurrence and event grain are explicit.
- [x] Event contract, parameter allowlist, consent boundary and downstream use are documented.
- [x] Ownership, schema lifecycle and change-impact rules are recorded.
- [x] Dependent records are linked before implementation work begins.
- [x] Simulation approval is separated from production approval and runtime verification.
