# FD `calculation_action` — GA4/GTM Setup Journey

> This document is built phase by phase. Identifiers ending in `FAKE`, `SIMULATED` or marked `[placeholder]` are design data only and must not be used in production.

> **Document role:** MASTER PROJECT-CONTROL DOCUMENT. This file coordinates scope, status, decisions, record links, phase gates and handoffs. It does not replace project records and does not contain real runtime evidence.

> **Change Request boundary:** The FD project uses [00 — FD Change Request Governance](00-change-request-governance.md) as its project-control wrapper. Sections 01–10 are research/reference documents; the FD-REC files are the concrete FD project records.

> **Current project mode — SIMULATION ONLY:** The journey simulates a happy path between Application → Data Layer → GTM → GA4. It does not create or change a real account, property, stream or container; connect to source code; run browser/Network/DebugView tests; publish; or use production data. IDs, payloads, evidence, versions, statuses and acceptance results are simulated or placeholders.

## 0. Journey status

| Phase | Scope | Status |
|---|---|---|
| 0 | Simulated system inventory, environment, consent, access and QA baseline | **Completed — simulation documentation** |
| 1 | Measurement Plan and Event Contract | **Completed — simulation documentation** |
| 2 | Simulated Application Data Layer contract and handoff | **Completed — simulation documentation** |
| 3 | Simulated GTM Variables, Trigger, Tags and routing | **Completed — simulation documentation** |
| 4 | Simulated GA4 custom definitions, reports and charts | **Completed — simulation documentation** |
| 5 | Simulated Debug/QA and evidence; `[RUNTIME VERIFICATION]` is for a future runtime project only | **Completed — simulation documentation** |
| 6 | Simulated Release, monitoring and rollback | **Completed — simulation documentation** |

**Current scope:** Complete the happy-path documentation only. Phases 0–6 have simulated baselines, contracts, GTM records, reporting assets, QA expectations and release/monitoring controls approved for documentation. The Application retains the complete Calculation API snapshot internally and pushes only the approved analytics subset plus an opaque per-occurrence `event_id` to the Data Layer. GTM/GA4 receive the same approved scalar allowlist; the `inputs` object is never sent as one GA4 parameter. No live setup or runtime evidence exists.

**Runtime-readiness status:** **Blocked pending business/privacy decisions and runtime evidence.** Documentation completion does not authorize implementation or publication. Before a runtime project starts, the owners must resolve `FD-OPEN-001` through `FD-OPEN-004` below and then execute `FD-REC-11`.

### Runtime-readiness decision register

| Decision ID | Required decision before runtime | Owner | Exit condition | Status |
|---|---|---|---|---|
| `FD-OPEN-001` | Decide whether schema `3.0` may continue combining valid no-output with API error, timeout and cancellation, or open a new CR/schema that separates technical outcomes. | Product + Application + Analytics | Approved semantics and report/monitoring treatment; an exception has owner and review date if the combined value remains. | Open — runtime blocker |
| `FD-OPEN-002` | Decide whether `calculation_action` represents a key event. The current base event includes unsuccessful and technical terminal outcomes. | Product + Analytics | Key-event rationale, success condition, effective date and implementation method are approved. Until then, do not mark it as a key event. | Open — runtime blocker |
| `FD-OPEN-003` | Approve the consent region, CMP callback/update path, persistence, revocation/storage-cleanup behavior and policy version. | Privacy + Analytics | `FD-REC-05` has approved non-placeholder values and the consent QA matrix is runnable. | Open — runtime blocker |
| `FD-OPEN-004` | Approve controlled vocabularies/cardinality for categorical parameters and the retention/privacy treatment of opaque `event_id`. | Application + Analytics + Privacy | Parameter registry and privacy/retention decision are linked from `FD-REC-07`. | Open — runtime blocker |

### Status rules

- `Completed — simulation documentation` means the record, decision and simulated handoff are reviewed; it does not prove a real platform configuration.
- `Planned — simulation documentation` means the phase document is not complete; it is not a runtime backlog.
- `Blocked` means a required decision, access, environment or material defect is unresolved.

## 0.1 Standard phase workflow

For a new FD change, create or update the FD Change Request using Section 00 before approving the phase record.

1. Create or open the record required for the phase.
2. Record the requirement, owner, environment, expected result and status before describing setup.
3. Write the Application, GTM or GA4 setup for the phase; in this project, describe it without executing it.
4. Review dependencies, privacy, destination and duplicate risk.
5. Add a clearly labelled simulated example and acceptance criteria; never call it runtime evidence.
6. Update status, open items, owner and next action in this journey.

A phase is not complete merely because a simulated configuration is written. It is complete when the record and simulated handoff have been reviewed.

## 0.2 How to read the documents

| Type | Meaning | Runtime evidence? |
|---|---|---|
| `HOW-TO` | Instructions for creating or configuring Application, GTM or GA4 assets | No |
| `RECORD` | Project facts, decisions, owner, status, version, acceptance and handoff | May point to evidence; does not replace it |
| `EVIDENCE` | In this project, a simulated example only; a future runtime project stores real artifacts separately | No |
| `MASTER JOURNEY` | Phase coordination, status, decisions, blockers, links and next actions | No; it points to the source record |

Research files `01`–`10` are the playbook and source of the standard record structures. `FD-REC-xx` files are project records. Simulated values must be labelled `SIMULATED` or `[placeholder]` and must not be presented as verified production evidence.

### 0.2.1 How to use an FD record as a project template

Each `FD-REC-xx` file is both a completed simulation baseline and a reusable record template. When starting a real project:

1. Copy the relevant record; do not edit the simulation baseline in place.
2. Reset simulated IDs, owners, dates, statuses, approvals and evidence links.
3. Reset checklist marks to `[ ]` and mark them only when the project has the required decision or evidence.
4. Keep `Document status` separate from `Runtime status`; a completed document is not proof that the platform ran correctly.
5. Link the artifact or decision that supports each completed criterion.

The `Phase acceptance` blocks in this journey are summary gates. The detailed `Acceptance checklist / template criteria` in each record remains the operational Definition of Done for that record.

## 0.3 Record register

| Record ID | Project record | File | Standard source | Purpose | Status |
|---|---|---|---|---|---|
| FD-CR-00 | FD Change Request Governance | 00 — FD Change Request Governance | Project control template | Request intake, risk, impact, traceability, release, monitoring and closure wrapper | Template / simulation boundary |
| `FD-CR-001` | Solution-found value change | [`FD-CR-001`](fd-calculation-records/FD-CR-001-solution-found-value-change.md) | Historical change packet | Version 1 `No` → version 2 `No_solution`; schema `1.0` → `2.0`; downstream record propagation | Completed — historical simulation; superseded by `FD-CR-002`; runtime not executed |
| `FD-CR-002` | Runtime-readiness contract hardening | [`FD-CR-002`](fd-calculation-records/FD-CR-002-runtime-readiness-hardening.md) | Applied change packet | Schema `2.0` → `3.0`; minimized Data Layer, opaque `event_id`, strict required fields, report/consent/QA/release hardening | Approved — simulation documentation; runtime blocked |
| `FD-REC-00` | Phase 0 System Inventory Record | [`FD-REC-00`](fd-calculation-records/FD-REC-00-phase-0-system-inventory.md) | Phase 0 baseline | Environment, GA4/GTM foundation, consent, access, duplicate baseline and QA scope | Simulation baseline updated; runtime blocked |
| `FD-REC-01` | Application/Data Layer Handoff Record | [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) | Section 01 | Snapshot, payload, correlation, outcome rules and expected QA behavior | Completed — simulation documentation; `FD-CR-002` applied; runtime blocked |
| `FD-REC-02` | GTM Variable Inventory | [`FD-REC-02`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md) | Section 02 | Variable names, source paths, types, missing behavior and consumers | Completed — simulation documentation; `FD-CR-002` applied; runtime blocked |
| `FD-REC-03` | GTM Trigger Inventory | [`FD-REC-03`](fd-calculation-records/FD-REC-03-gtm-trigger-inventory.md) | Section 03 | Authoritative event, filters, frequency and controls | Approved — simulation documentation; `FD-CR-002` applied; runtime blocked |
| `FD-REC-04` | GTM Tag Inventory | [`FD-REC-04`](fd-calculation-records/FD-REC-04-gtm-tag-inventory.md) | Section 04 | Google tag, GA4 Event tag, mapping, consent, destination and count | Approved — simulation documentation; `FD-CR-002` applied; runtime blocked |
| `FD-REC-05` | Consent Decision Record | [`FD-REC-05`](fd-calculation-records/FD-REC-05-consent-decision.md) | Section 05 | CMP source, default/update behavior and `analytics_storage` | Design expanded; runtime blocked by `FD-OPEN-003` |
| `FD-REC-06` | Template Governance Decision | [`FD-REC-06`](fd-calculation-records/FD-REC-06-template-governance-decision.md) | Section 06 | Native Tag decision and custom-template exception boundary | Completed — simulation documentation |
| `FD-REC-07` | Measurement Plan & Event Contract | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) | Section 07 | Business question, event meaning, parameters, privacy and destination | Approved — simulation design; `FD-CR-002` applied; runtime blocked |
| `FD-REC-08` | Debug and QA Simulation Record | [`FD-REC-08`](fd-calculation-records/FD-REC-08-debug-qa.md) | Section 08 | Test setup, data safety, test matrix, scenario summary and evidence mapping | Design complete; runtime not executed |
| `FD-REC-09` | GA4 Report and Exploration Record | [`FD-REC-09`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md) | Section 09 | Field readiness, Report/Exploration configuration, formula and interpretation | Design complete; runtime blocked |
| `FD-REC-10` | Release and Monitoring Simulation Record | [`FD-REC-10`](fd-calculation-records/FD-REC-10-release-monitoring.md) | Section 10 | Version, approval, smoke test, threshold, rollback and observation | Design complete; release blocked |
| `FD-REC-11` | **[RUNTIME VERIFICATION] End-to-End Runtime Verification Record** | [`FD-REC-11`](fd-calculation-records/FD-REC-11-runtime-verification.md) | Sections 08 + 10 | Reconcile real runtime evidence from Application to GA4 and sign off release | Not applicable — simulation-only |

## 0.4 New project startup guide

Use this section when a new project introduces GA4 measurement through GTM. It explains what to prepare, which record to open first and how to move information from the Application contract to GTM, GA4, QA and release. It is an operating guide, not a replacement for the detailed records.

### 0.4.1 Prepare the project before opening a record

For an FD change, open 00 — FD Change Request Governance first. Create or update the FD Change Request, assign owners and risk, complete the impact assessment, and then select the detailed FD-REC records required by the change.

Collect the following information and identify an owner for each item:

| Preparation area | Minimum information | Responsible owner |
|---|---|---|
| Business context | Business question, user journey, business moment and event outcome | Product/business owner |
| Application flow | Source action, snapshot boundary, API/async behavior, validation, retry and duplicate rules | Application owner |
| Environments | QA/staging and production hostnames, release path and test-data policy | Engineering/release owner |
| GA4 destination | Property, web stream, Measurement ID and environment mapping | Analytics owner |
| GTM foundation | Container, workspace, environment, access and publishing owner | GTM owner |
| Consent/privacy | CMP source, default state, allowed consent categories, data classification and retention constraints | Privacy/Analytics owner |
| Reporting need | Required event counts, dimensions, metrics, user/event scope and processing-window expectation | Analytics/business owner |
| QA and release | Test scenarios, evidence locations, release window, monitoring owner and rollback owner | QA/release owner |

Do not start by creating a Variable, Trigger or Tag. First establish the event meaning, destination and privacy boundary in `FD-REC-07`.

### 0.4.2 Core and conditional records

Not every project needs every record. Select records based on the actual implementation scope:

| Record group | Records | Use rule |
|---|---|---|
| Core design and implementation | `FD-REC-00`, `FD-REC-07`, `FD-REC-01`, `FD-REC-02`, `FD-REC-03`, `FD-REC-04`, `FD-REC-05`, `FD-REC-08` | Use for a normal Application → Data Layer → GTM → GA4 event flow. |
| Template exception | `FD-REC-06` | Use when a custom GTM template, custom transformation or non-native destination is proposed. For a native GA4 Event tag, record the no-template decision and close the exception. |
| Reporting and analysis | `FD-REC-09` | Use when the project needs a recurring Report, Exploration, custom definitions or a documented metric. |
| Runtime verification | `FD-REC-11` | Use only after a real QA/staging or production run is authorized. Never use it for simulated examples. |
| Release and operations | `FD-REC-10` | Use when the change will be published, observed or rolled back in an environment. |

`FD-REC-00` and `FD-REC-07` are the normal starting records. `FD-REC-07` is the semantic source of truth; downstream records must not redefine the event meaning or approved parameter list.

### 0.4.3 Record application order

Apply the records in this order. A downstream record may be drafted in parallel, but it must not be approved until its upstream dependency is stable.

| Order | Record | Apply it when | Required input | Expected output / exit gate |
|---:|---|---|---|---|
| 0 | [`FD-REC-00`](fd-calculation-records/FD-REC-00-phase-0-system-inventory.md) | The project is opened | Environments, destinations, access, consent and duplicate-collection context | System and access baseline; unresolved access or destination risk is recorded. |
| 1 | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) | The business event is being defined | Business question, occurrence rule, population, parameters, consent and data classification | Approved event contract, parameter allowlist, schema version and ownership. |
| 2 | [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) | The Application must hand data to GTM | Event contract, snapshot/API flow, validation and outcome rules | Application/Data Layer envelope, snapshot boundary, idempotency and handoff contract. |
| 3 | [`FD-REC-05`](fd-calculation-records/FD-REC-05-consent-decision.md) | Collection depends on user consent | CMP states, consent categories, region, update/persistence/revocation policy and intended Tag behavior | Consent state-to-collection decision; denied/unknown behavior and runtime blockers are explicit. |
| 4 | [`FD-REC-02`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md) | GTM needs to read approved fields | Data Layer paths, parameter dictionary and consent/privacy decision | One Variable per approved source field, with type and missing-data behavior. |
| 5 | [`FD-REC-03`](fd-calculation-records/FD-REC-03-gtm-trigger-inventory.md) | GTM needs to decide when the event is eligible | Authoritative Application event, Variables and approved filters | One narrow authoritative Trigger, expected frequency and fail-closed routing. |
| 6 | [`FD-REC-04`](fd-calculation-records/FD-REC-04-gtm-tag-inventory.md) | GTM needs to send the event | Variables, Trigger, destination and approved consent decision | Google tag/GA4 Event tag mapping, destination, consent behavior and duplicate controls. |
| 7 | [`FD-REC-06`](fd-calculation-records/FD-REC-06-template-governance-decision.md) | A custom template is requested or considered | Native capability check and security/privacy requirements | Native Tag decision or a documented template exception with owner and review path. |
| 8 | [`FD-REC-09`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md) | The collected event must answer a reporting question | Approved fields, scope, grain and processing expectations | Report/Exploration design, field-readiness rules and interpretation limits. |
| 9 | [`FD-REC-08`](fd-calculation-records/FD-REC-08-debug-qa.md) | The implementation is ready for controlled QA | Approved records 01–07 and the reporting requirement | Test setup, data-safety check, scenario matrix and evidence plan. |
| 10 | [`FD-REC-11`](fd-calculation-records/FD-REC-11-runtime-verification.md) | A real runtime run is authorized | QA setup, approved build/container/stream and evidence location | Reconciled Application → Data Layer → GTM → Network → GA4 result. |
| 11 | [`FD-REC-10`](fd-calculation-records/FD-REC-10-release-monitoring.md) | The change is ready for release or observation | QA result, approvals, release version and rollback plan | Release decision, smoke test, monitoring window and closure/rollback decision. |

The order reflects dependency, not the order in which someone clicks through the tools. For example, create the GA4 property or stream in the approved environment during Phase 0, but do not treat the Measurement ID as a reason to skip the event contract.

### 0.4.4 How to copy and complete a record

For each selected record:

1. Copy the baseline file into the project documentation area; do not edit the FD simulation baseline in place.
2. Assign the project ID, record ID, version, owner, reviewer, environment and dates.
3. Replace every `SIMULATED`, `FAKE` and `[placeholder]` value with an approved project value, or keep it explicitly marked as pending.
4. Reset status fields and checklist marks to `Not started`, `TBD` and `[ ]`.
5. Complete the record from upstream decisions; do not invent a downstream value that is absent from `FD-REC-07`.
6. Link the supporting decision, configuration reference or evidence artifact beside the relevant item.
7. Mark a checklist item complete only when its decision or evidence is available and reviewed.
8. Update the corresponding phase row, open items, next action and handoff status in this journey.

The record's `Document status` answers whether the design or decision is documented. `Runtime status` answers whether the implementation was actually observed. A completed document may still have `Runtime status: Not executed`.

### 0.4.5 GTM and GA4 information-management rules

Use the following rules to keep GTM and GA4 information stable across projects:

1. **One semantic source of truth.** `FD-REC-07` owns event meaning, occurrence, approved parameters, consent boundary, destination and schema version. GTM and GA4 records consume that contract; they do not redefine it.
2. **Separate environments.** Record the QA/staging and production hostname, GTM environment/workspace/version and GA4 property/stream/Measurement ID separately. Do not use a production destination as a fallback for an unknown hostname.
3. **Keep GTM inventories explicit.** `FD-REC-02` owns Variable names and paths, `FD-REC-03` owns the authoritative Trigger, and `FD-REC-04` owns Tag mapping, destination and consent behavior. A change to one inventory must identify its consumers and downstream impact.
4. **Keep business logic in the Application.** The Application owns the complete API snapshot, correlation, validation, terminal outcome and idempotency. Its analytics adapter creates a minimized analytics subset for the Data Layer. GTM reads approved scalar fields; it does not reconstruct the API snapshot or calculate the business outcome.
5. **Use the narrowest event path.** One authoritative Application event should drive one GTM Trigger and one approved GA4 Event tag. Do not add broad click, page or DOM rules to compensate for a missing Application event.
6. **Minimize data.** Do not place the full API snapshot, raw API responses, secrets, PII or internal request tokens in the analytics Data Layer or GA4. Keep them in controlled Application logs. The only correlation value allowed in analytics is a random per-occurrence `event_id`, subject to the approved retention/privacy decision; do not register it as a GA4 custom dimension.
7. **Treat consent as a gate.** `FD-REC-05` records the default, update, denied and unknown states. Consent permission is not a Variable fallback and must not be bypassed by an exception Trigger.
8. **Manage GA4 definitions and reports as downstream assets.** Create custom definitions only for approved fields that reporting needs, record processing delay, and keep user-level metrics separate from event-level QA calculations.
9. **Version semantic changes.** If event meaning, occurrence, parameter type, destination or consent behavior changes, update the Schema Lifecycle Register in `FD-REC-07` before changing Variables, Triggers, Tags or Reports.
10. **Protect operational data.** Store credentials, secrets and unrestricted raw payloads outside these records. Link to controlled evidence locations and record access owner, retention and redaction rules.

### 0.4.6 Handoff gates between phases

Use these gates to decide whether the next record can be approved:

| Handoff | Minimum gate |
|---|---|
| `FD-REC-00` → `FD-REC-07` | Environment, destination, ownership, consent context and access risks are recorded. |
| `FD-REC-07` → `FD-REC-01` | Business meaning, occurrence rule, parameter allowlist, schema version and privacy boundary are approved. |
| `FD-REC-01` → `FD-REC-05` | Application/Data Layer envelope, minimized analytics subset, outcome, idempotency and prohibited-field rules are defined; privacy can approve the consent/data boundary. |
| `FD-REC-01`/`FD-REC-05` → `FD-REC-02`–`FD-REC-04` | Data contract and consent behavior are stable before GTM Variables, Trigger and Tags are approved. |
| `FD-REC-02`–`FD-REC-06` → `FD-REC-09` | GTM mapping, Trigger, Tag, destination, consent and template decisions are internally consistent. |
| `FD-REC-07`–`FD-REC-09` → `FD-REC-08` | The QA destination, synthetic-data boundary, expected count and evidence layers are known. |
| `FD-REC-08` → `FD-REC-11` | A real runtime run is authorized, with access, build/container/stream identifiers and evidence storage ready. |
| `FD-REC-08`/`FD-REC-11` → `FD-REC-10` | QA/runtime decision, open defects, release version, rollback owner and observation window are explicit. |

If a gate fails, keep the downstream record in `Draft`, `Pending` or `Blocked`; do not mark it `Approved` to keep the schedule moving.

### 0.4.7 Recommended project paths

Use the shortest path that matches the project scope.

**Minimal documentation path — one controlled event, no live release yet**

```text
FD-REC-00
→ FD-REC-07
→ FD-REC-01
→ FD-REC-05
→ FD-REC-02
→ FD-REC-03
→ FD-REC-04
→ FD-REC-08
```

**Full implementation path — reporting, runtime verification and release included**

```text
FD-REC-00
→ FD-REC-07
→ FD-REC-01
→ FD-REC-05
→ FD-REC-02
→ FD-REC-03
→ FD-REC-04
→ FD-REC-06 (record the native/no-template decision; expand only for an exception)
→ FD-REC-09
→ FD-REC-08
→ FD-REC-11
→ FD-REC-10
```

The minimal path is sufficient for design and implementation handoff. It is not sufficient to claim runtime correctness or production readiness. Those claims require the full path and the corresponding evidence.

## Handoff brief

This package is a simulation-management baseline for one FD GA4/GTM event. The journey coordinates status and decisions; `FD-REC-xx` files hold the detailed records; the `HOW-TO` sections are reference instructions and have not been executed.

### Owner proposal

The current combined semantics of `calculation_action` are retained for simulation; runtime approval is blocked until `FD-OPEN-001` is decided. Phase 2 documents the Application/Data Layer handoff and Phase 3 documents Variables, Trigger, Tags, consent and template governance. The simulated architecture uses one authoritative Application event, one GTM flow, separate QA/production destinations and hostname allowlisting.

### Decisions already approved

| Decision | Approved result | Source |
|---|---|---|
| Business meaning | v1: response `length > 0` → `solution_found="Yes"`; `[]` or contract-defined error → `"No"`. v2 renamed the combined outcome to `"No_solution"`; v3 retains it pending `FD-OPEN-001`. | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) |
| Negative cases | Input validation emits no event; API failure, timeout, cancellation and a stale attempt that terminalizes emit one `"No_solution"`; duplicate or late callbacks add no event | [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) |
| GA4 payload | Nine approved scalar fields, including opaque `event_id`; no full `inputs`, API response, internal token, secret or PII | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) |
| Key event | Pending `FD-OPEN-002`; do not mark the base event as a key event until its success condition is approved | `FD-REC-07` |
| Custom definitions | `solution_found`, `event_schema_version` and five categorical fields; no definitions for `event_id`, `app_name`, `fx` or `fy` | `FD-REC-07` |
| Environment | Two simulated GA4 destinations, one simulated GTM container and hostname routing | `FD-REC-00` |
| Consent | Default `analytics_storage=denied`; collection only when `granted`; denied/unknown suppresses | `FD-REC-05` |
| Implementation mode | Happy-path documentation only; no runtime action | Journey scope |

### Review boundary

- Phase 0 uses simulated values; real accounts, IDs, permissions and live changes were not verified.
- Schemas `1.0` and `2.0` are historical simulation baselines. Phase 1 approves schema `3.0` as the current simulation design after `FD-CR-002`; runtime migration remains pending. Phase 2 documents payload, correlation, outcome and QA expectations only.
- Phase 3 documents GTM/consent/template behavior; Phase 4 documents reporting behavior; Phase 5 documents QA expectations; Phase 6 documents release/monitoring controls. All seven phases (0–6) are complete as simulation documentation; no live implementation is implied.
- Raw API responses, request tokens, secrets, PII and the full `inputs` object are never approved for GA4.

### Handoff gate

```text
Review business meaning and occurrence rule
→ approve GA4 allowlist and consent/privacy
→ approve key-event/custom-definition decisions
→ approve schema 3.0 simulation design
→ resolve FD-OPEN-001 through FD-OPEN-004 before runtime
→ complete Phase 2 Application/Data Layer documentation
→ complete Phase 3 GTM documentation
→ complete Phase 4 reports/charts documentation
→ complete Phase 5 Debug/QA documentation
→ complete Phase 6 Release/Monitoring documentation
```

If a decision changes, update `FD-REC-07` and its Schema Lifecycle Register before creating downstream assets.

## Decision index

| Decision ID | Decision | Source | Status |
|---|---|---|---|
| `FD-DEC-00-001` | One simulated GTM web container with hostname routing for QA/production | `FD-REC-00` | Approved — simulated baseline |
| `FD-DEC-01-001` | Key-event decision for `calculation_action` | `FD-REC-07`, `FD-OPEN-002` | Pending; base event must not be marked as a key event yet |
| `FD-DEC-01-002` | v1 used `solution_found=Yes/No`; v2 introduced `Yes/No_solution`; v3 retains that combined outcome while hardening payload/operations | `FD-REC-07`, `FD-CR-001`, `FD-CR-002` | Approved for simulation; separation pending `FD-OPEN-001` |
| `FD-DEC-01-003` | Nine-field GA4 allowlist; minimized analytics Data Layer; no full API snapshot, response, internal token, secret or PII | `FD-REC-07` | Approved for simulation; `event_id` privacy/retention pending |
| `FD-DEC-01-004` | Create custom dimensions for `solution_found`, `event_schema_version` and five categorical fields in Phase 4 | `FD-REC-07` | Approved for simulation |
| `FD-DEC-02-001` | No Application action/runtime; use a happy-path payload assumption | `FD-REC-01` | Approved for simulation |
| `FD-DEC-03-001` | Use Version 2 Data Layer Variables; no GTM business logic or stale fallback | `FD-REC-02` | Approved for simulation |
| `FD-DEC-03-002` | Use one authoritative Custom Event Trigger; no click/page/DOM substitute | `FD-REC-03` | Approved for simulation |
| `FD-DEC-03-003` | Use native Google tag + native GA4 Event tag; no custom template | `FD-REC-04`, `FD-REC-06` | Approved for simulation |
| `FD-DEC-03-004` | Default `denied`; `granted` allows collection; `unknown` fails safe | `FD-REC-05` | Approved for simulation |
| `FD-DEC-04-001` | Use a Detail Report for recurring event counts, a Free-form Exploration for event-level QA, and no Funnel for the current one-event contract | `FD-REC-09` | Approved for simulation |
| `FD-DEC-04-002` | The current output metric is event-level, not user-level; v2/v3 `No_solution` combines empty response and terminal error, while v1 `No` remains historical | `FD-REC-09`, `FD-REC-07`, `FD-CR-001`, `FD-CR-002` | Approved for simulation; interpretation caveat remains |
| `FD-DEC-05-001` | Use P0 QA records for every future run; keep Evidence and Runtime Verification separate from simulated results | `FD-REC-08` | Approved for simulation |
| `FD-DEC-05-002` | No scenario is marked runtime Pass; Debug Session and Defect/Retest remain conditional | `FD-REC-08` | Approved for simulation |
| `FD-DEC-06-001` | Release gates, monitoring signals, thresholds and rollback are documented, but no live publish or observation is claimed | `FD-REC-10` | Approved for simulation |
| `FD-DEC-06-002` | Numeric thresholds remain uncalibrated until a real baseline exists; do not invent production percentages | `FD-REC-10` | Approved for simulation |

## 1. Scope and objective

### 1.1 Business objective

Measure a terminal FD calculation attempt and distinguish an output from a combined no-output/error outcome. The event is emitted only after the Application has correlated the response with the complete snapshot.

### 1.2 System boundary

```text
Application
  → immutable input snapshot and simulated Calculation API response
  → Data Layer message: calculation_action
  → GTM Variables and authoritative Custom Event Trigger
  → native Google tag / GA4 Event tag
  → simulated QA or production destination
```

Application owns the business meaning, response correlation, snapshot, outcome and idempotency. The Data Layer carries structured data for GTM. GTM reads approved scalars, applies consent/routing and sends the event. GA4 receives the approved event and later supports reports. GTM must not calculate the outcome.

## 2. Phase 0 — System inventory and access

The complete Phase 0 record is [`FD-REC-00`](fd-calculation-records/FD-REC-00-phase-0-system-inventory.md). It contains the simulated environment baseline, destinations, GTM foundation, consent, access, duplicate-collection baseline and QA matrix.

### Phase 0 simulated baseline

| Area | Simulated decision |
|---|---|
| QA URL | `https://app-staging.strongtie.com/fd` |
| Production URL | `https://app.strongtie.com/fd` |
| GA4 destinations | `G-FAKEFDQA01` and `G-FAKEFDPROD1` |
| GTM | `GTM-FAKEFD01`, workspace `WS-FD-CALC-001` |
| Routing | Hostname allowlist; unknown hostname blocked; no production default |
| Consent | `analytics_storage=denied` by default; `granted` allows collection |
| Duplicate baseline | One container, one Google tag, one event Tag, one request per occurrence |
| Access | Simulated role matrix for Developer, GTM, Analytics, QA and Publisher |

### Phase 0 acceptance

- [x] QA/staging and production hostnames recorded.
- [x] Simulated GA4 properties/streams/Measurement IDs recorded.
- [x] Simulated GTM account/container/workspace/environment recorded.
- [x] Google tag and hostname-routing design recorded.
- [x] Duplicate-collection inventory and future verification method recorded.
- [x] Consent baseline and `analytics_storage` mapping recorded.
- [x] Role matrix, QA URL, synthetic data and browser matrix recorded.
- [x] Real account/property/container verification and platform setup intentionally bypassed.
- [x] Phase 0 reviewed and approved as simulation documentation.

## 3. Google tag and duplicate-collection design

The simulated design uses one web container with hostname-to-Measurement-ID routing. An unknown hostname is blocked and never falls back to production. A future runtime project would inspect page source, GTM Preview/Tag Assistant and Network to confirm there is no second hard-coded, CMS or Application sender.

## 4. Consent policy

The simulated Consent Initialization flow sets `analytics_storage=denied` before normal Tags. A later CMP update may set `granted`; denied/unknown blocks analytics. No Exception Trigger bypass and no synthetic consent event is allowed. The real CMP, storage and revocation behavior are outside this project.

## 5. Access and QA baseline

`FD-REC-00` records simulated aliases, role scope, the QA URL, safe synthetic data and the browser matrix. A future runtime record must add the actual browser/device, consent state, GTM environment, GA4 stream and timestamp.

## 6. Phase 1 — Measurement Plan and Event Contract

Phase 1 is recorded in [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md), using this order:

```text
Project Context / Baseline
→ Journey / Event Coverage Matrix
→ Event Contract
→ Parameter Dictionary
→ Consent / Data Classification
→ Key-event / Custom-definition decisions
→ Traceability Matrix
→ Schema Lifecycle Register
```

### Phase 1 acceptance

- [x] Business question and `calculation_action` authoritative moment defined.
- [x] Output, no-output, error and validation behavior defined.
- [x] Complete snapshot separated from the GA4 scalar allowlist.
- [x] Consent, privacy, destination and routing baseline attached.
- [x] Retry, duplicate, stale and remount/replay rules defined.
- [x] `solution_found` allowlist and custom-definition decision recorded.
- [x] Schema `3.0` approved as the current simulation design; schemas `1.0`/`2.0` retained as history.

## 7. Phase 2 — Application/Data Layer handoff

Phase 2 is documented in [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md). It assumes a complete immutable snapshot, a matching simulated response and one terminal Data Layer push; it does not implement code or run the Application.

### Phase 2 contract

```text
valid input
  → immutable snapshot
  → simulated API request/response
  → correlate response to snapshot
  → classify output/no-output/error
  → one-event guard
  → one complete contract-valid, minimized calculation_action push
```

Rules: v1 `length > 0` → `Yes`; `[]`/terminal error → `No`. `FD-CR-001` changed that combined value to `No_solution` in schema `2.0`; `FD-CR-002` retains the outcome meaning in schema `3.0` while minimizing the payload and adding `event_id`. Invalid input → no event; automatic retry reuses the attempt/snapshot/event ID. A current attempt explicitly terminalized as stale emits one `No_solution` under the current combined contract, while old, duplicate, late, remount and replay callbacks add no event. The Application retains the complete API snapshot internally; the analytics Data Layer and GA4 receive only the approved analytics subset and opaque `event_id`.

### Phase 2 acceptance

- [x] `FD-REC-01` records envelope, snapshot, correlation and outcome rules.
- [x] Required and optional fields, prohibited fields and missing behavior are explicit.
- [x] `TC-FD-01`–`TC-FD-15` expected QA scenarios are documented.
- [x] Payload examples are labelled simulated; no runtime result is claimed.

## 8. Phase 3 — GTM Variables, Triggers, Tags, Consent and Template Governance

Phase 3 is documented in five records:

1. [`FD-REC-02 — GTM Variable Inventory`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md): GTM Data Layer Variables using Variable Version 2, nested paths, strict missing behavior and hostname routing.
2. [`FD-REC-03 — GTM Trigger Inventory`](fd-calculation-records/FD-REC-03-gtm-trigger-inventory.md): one authoritative Custom Event Trigger, filters, timing and count.
3. [`FD-REC-04 — GTM Tag Inventory`](fd-calculation-records/FD-REC-04-gtm-tag-inventory.md): native Google tag, GA4 Event tag, scalar mapping, consent and destination.
4. [`FD-REC-05 — Consent Decision Record`](fd-calculation-records/FD-REC-05-consent-decision.md): default/update behavior and `analytics_storage` baseline.
5. [`FD-REC-06 — Template Governance Decision`](fd-calculation-records/FD-REC-06-template-governance-decision.md): native Tag decision and future exception criteria.

### Phase 3 acceptance

- [x] Variables map one-to-one to the approved Data Layer and GA4 fields.
- [x] `FD - CE - calculation_action - Approved` is the only business Trigger.
- [x] No click/page/DOM alternate path exists for the same event.
- [x] Native Google tag and GA4 Event tag map the scalar allowlist only.
- [x] Unknown hostnames fail closed and production is never the default.
- [x] Consent is permission context; no exception bypass is used.
- [x] No custom template is required for the GA4 path.
- [x] One Application occurrence is expected to produce one push and one Trigger match; when consent permits, it produces one Tag fire/request, while denied/unknown produces zero analytics requests.

```text
Phase 3 status: Completed — simulation documentation
Execution state: Simulation only — no live GTM configuration, Preview, Network or publish
Next action: Resolve FD-OPEN-001 through FD-OPEN-004 before configuring the schema 3.0 GTM assets
```

## 9. Phase 4 — GA4 custom definitions, Reports and Explorations

Phase 4 is documented in [`FD-REC-09 — GA4 Report and Exploration Record`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md). It converts the approved event contract into a simulated reporting design; it does not create custom definitions, publish Reports, run Explorations or inspect processed GA4 data.

### Phase 4 decisions

1. The current FD analysis is an **event-level calculation-attempt rate**, not a user-level completion rate. The schema does not define a user-level population or User-ID requirement. Opaque `event_id` identifies an occurrence for QA/deduplication only and must never be interpreted as a user identity.
2. Register the approved event-scoped definitions, then use a **Detail Report** for recurring `calculation_action` counts and output distribution by `FD Design Method` and `FD Solution Found`.
3. Use a **Free-form Exploration** for event-level QA of field availability, schema version, method values and outcome values.
4. Mark **Funnel Exploration as N/A** because the current contract has no approved `calculation_start → calculation_action` sequence.
5. Use an approved export/BigQuery calculation only when a future requirement needs an exact reproducible ratio, distinct-user logic or joins that the GA4 UI cannot express.
6. Keep `(not set)`, `Unassigned`, invalid values and missing `solution_found` separate from the validated output rate.
7. Register `event_schema_version` as event-scoped **FD Event Schema Version** before using it in a GA4 Report or Exploration. `FD-REC-09` owns the complete parameter-to-display-name registration plan. Do not use `app_name` as a GA4 UI filter in the current dedicated FD streams; retain it for payload diagnostics only.
8. The release boundary is schema version plus effective date/release record; it is not an undefined `release/change boundary` dimension.

### Phase 4 output rate

```text
Numerator   = calculation_action events where schema = 3.0, design_method = X and solution_found = Yes
Denominator = calculation_action events where schema = 3.0, design_method = X and solution_found ∈ {Yes, No_solution}
Output rate = Numerator / Denominator
```

The numerator and denominator use the same property, stream, date range, method, consent-allowed population, event eligibility and event grain. In current schema `3.0`, `solution_found="No_solution"` still groups empty responses and terminal errors, so the metric is labelled a **combined output rate**, not a pure solution-success rate. Keep schemas `1.0`, `2.0` and `3.0` separate unless a normalized historical model is approved.

### Phase 4 acceptance

- [x] `FD-REC-09` uses the Section 09 Report Requirement, Field Readiness, Asset Configuration and Interpretation/Decision structure.
- [x] Population, event grain, scope, dimensions, metric and formula are explicit.
- [x] User-level reporting is explicitly not approved for the current FD schema.
- [x] Detail Report and event-level QA Exploration are separated.
- [x] Funnel is marked `N/A` because no approved start/finish sequence exists.
- [x] Export/BigQuery is documented as a conditional exact-calculation path.
- [x] `(not set)`, `Unassigned`, invalid values and missing `solution_found` behavior are defined.
- [x] Custom-definition readiness and processing delay are recorded.
- [x] No live Report, Exploration, chart or processed-data result is claimed.

```text
Phase 4 status: Completed — simulation documentation
Execution state: Simulation only — no GA4 custom definition, Report, Exploration or processed-data query
Next action: After runtime authorization, register the named definitions, create the assets and validate processed schema 3.0 data
```

## 10. Phase 5 — Debug/QA and evidence

Phase 5 is documented in [`FD-REC-08 — Debug and QA Simulation Record`](fd-calculation-records/FD-REC-08-debug-qa.md). It defines the QA package for a future runtime run while keeping all current values explicitly simulated.

### Phase 5 record order

```text
Test Run Setup
→ Data Safety Check
→ Required Test Matrix
→ Scenario Execution Summary
→ Evidence rows for material boundaries
→ Runtime Verification only when the run is real
→ Debug Session or Defect/Retest only when needed
```

### Phase 5 decisions

1. P0 records are mandatory for every future run: Test Run Setup, Data Safety Check, Required Test Matrix and Scenario Execution Summary.
2. Evidence rows are required for material or boundary-sensitive scenarios, but current rows remain expected evidence only.
3. `FD-REC-11 — [RUNTIME VERIFICATION]` is not used because no real Application → Data Layer → GTM → Network → GA4 run was performed.
4. Debug Session and Defect/Retest records are conditional; no failure or intermittent behavior was executed in this simulation.
5. No simulated scenario is labelled runtime `Pass`; actual counts remain `N/A — not executed`.

### Phase 5 acceptance

- [x] `FD-REC-08` uses the Section 08 QA record order and cross-references the approved contracts.
- [x] Test setup uses the simulated QA hostname, synthetic data and approved consent states.
- [x] Data Safety Check covers environment, payload, redaction, consent and cleanup.
- [x] Test Matrix covers output, no-output, validation, failure, timeout, stale, retry, privacy, the full consent lifecycle, routing, malformed contracts, browser regression, event-ID uniqueness and deployment-path/migration validation.
- [x] Scenario Execution Summary explicitly records that no scenario was executed.
- [x] Evidence mapping identifies the Application, Data Layer, GTM, consent, Network, DebugView and processed-data proof required in a real run.
- [x] Runtime Verification remains `Not applicable — simulation-only`.

```text
Phase 5 status: Completed — simulation documentation
Execution state: Simulation only — no browser, Preview, Network, DebugView or processed-data execution
Next action: Resolve the runtime blockers, then execute FD-REC-08 and record real evidence in FD-REC-11
```

## 11. Phase 6 — Release, Monitoring and Rollback

Phase 6 is documented in [`FD-REC-10 — Release and Monitoring Simulation Record`](fd-calculation-records/FD-REC-10-release-monitoring.md). It links the approved contract, implementation records, reporting design and QA package into a simulated release packet.

### Phase 6 decisions

1. The future live change is classified as **High risk** because it affects an event schema, a potential key-event decision, destination, consent and reporting.
2. Release gates 0–4 are defined, but only Gates 0–1 can be treated as documentation-ready in the current simulation. Gates 2–4 require runtime evidence or a real publish.
3. Monitoring must cover collection volume, combined output/no-outcome distribution, duplicates/missingness, destination, consent/privacy, freshness and vocabulary quality.
4. Thresholds are intentionally qualitative until a real baseline exists. No production percentage is invented in the simulation.
5. Rollback restores future GTM behavior only; it does not repair historical GA4 data or undo an irreversible filter.
6. Select the release path from verified runtime evidence. Because this simulation has no proof that v2 was deployed, the default is a controlled new v3 rollout. Only a target environment with verified live v2 uses the reviewed v2/v3 compatibility window before the final v3-only version; each path has its own coordinated rollback.
7. Runtime reconciliation uses opaque `event_id` across Application, Data Layer and Network evidence. Exact production duplicate analysis requires an approved export/BigQuery path; otherwise use aggregate Application-versus-GA4 counts and state that limitation.
8. Any hold, rollback or material defect requires an incident/affected-period record with first/last bad timestamps, impacted population, historical-data treatment, recovery evidence and closure owner.

### Phase 6 acceptance

- [x] `FD-REC-10` uses the Section 10 Release and Monitoring structure.
- [x] Release context, risk, affected journey, downstream consumers and owners are recorded.
- [x] Release packet links `FD-REC-01` through `FD-REC-09` and the runtime boundary.
- [x] Gates 0–4 are defined with their evidence requirements.
- [x] Monitoring signals cover volume, outcome, duplicates/missingness, destination, consent, freshness and vocabulary.
- [x] Critical/High/Medium/Low response policy is documented without invented production thresholds.
- [x] Smoke-test checks are recorded as `N/A — not executed`.
- [x] Containment, rollback, affected-period assessment and closure rules are documented.
- [x] No publish, monitoring run or production release decision is claimed as completed.

```text
Phase 6 status: Completed — simulation documentation
Execution state: Simulation only — no publish, smoke test, monitoring or rollback execution
Journey status: All documentation phases complete; runtime readiness blocked by FD-OPEN-001 through FD-OPEN-004 and runtime evidence
Next action: Resolve the blockers and Gates 0–3 before executing REL-FD-CALC-003
```

## 12. Journey closure and future runtime boundary

All seven phases (0–6) are complete as a coherent simulation package. The package is ready for team review and can be used as the design baseline for a separately authorized runtime project.

The following work is intentionally not complete because it is outside the current mode:

- creating or changing real GA4 properties, streams, custom definitions or Reports;
- creating or publishing a real GTM workspace/version;
- running Application, Data Layer, Preview, Network, DebugView, Realtime or processed-data tests;
- calibrating production thresholds from historical data;
- approving a real release, monitoring a production window or performing rollback.

`FD-REC-11 — [RUNTIME VERIFICATION]` remains `Not applicable — simulation-only` until a separate runtime project is explicitly authorized.

## 13. Official references used for Phase 0 and the platform boundary

- [GA4 — Set up Analytics for a website and/or app](https://support.google.com/analytics/answer/14183469)
- [Tag Manager — Create an account and container](https://support.google.com/tagmanager/answer/14842164)
- [Tag Manager — Workspaces](https://support.google.com/tagmanager/answer/7059647)
- [Tag Manager — Environments](https://support.google.com/tagmanager/answer/6311518)
- [Tag Manager — Add the Google tag in GTM](https://support.google.com/tagmanager/answer/14842872)
- [Tag Manager — Consent mode support](https://support.google.com/analytics/answer/10718549)
- [GA4 — About consent mode](https://support.google.com/analytics/answer/10000067)
- [GA4 — Verify consent mode](https://support.google.com/analytics/answer/14218557)
- [GA4 — Access and data-restriction management](https://support.google.com/analytics/answer/9305587)
- [GTM — Managing users and permissions](https://support.google.com/tagmanager/answer/6107011)
