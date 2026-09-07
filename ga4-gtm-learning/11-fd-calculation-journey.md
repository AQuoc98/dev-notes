# FD `calculation_action` — GA4/GTM Setup Journey

> This document is built phase by phase. Identifiers ending in `FAKE`, `SIMULATED` or marked `[placeholder]` are design data only and must not be used in production.

> **Document role:** MASTER PROJECT-CONTROL DOCUMENT. This file coordinates scope, status, decisions, record links, phase gates and handoffs. It does not replace project records and does not contain real runtime evidence.

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

**Current scope:** Complete the happy-path documentation only. Phases 0–6 have simulated baselines, contracts, GTM records, reporting assets, QA expectations and release/monitoring controls approved for documentation. The Application is assumed to push a complete Data Layer message, while GTM/GA4 are assumed to receive only the approved scalar allowlist; the `inputs` object is never sent as one GA4 parameter. No live setup or runtime evidence exists.

### Status rules

- `Completed — simulation documentation` means the record, decision and simulated handoff are reviewed; it does not prove a real platform configuration.
- `Planned — simulation documentation` means the phase document is not complete; it is not a runtime backlog.
- `Blocked` means a required decision, access, environment or material defect is unresolved.

## 0.1 Standard phase workflow

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
| `FD-REC-00` | Phase 0 System Inventory Record | [`FD-REC-00`](fd-calculation-records/FD-REC-00-phase-0-system-inventory.md) | Phase 0 baseline | Environment, GA4/GTM foundation, consent, access, duplicate baseline and QA scope | Completed — simulation documentation |
| `FD-REC-01` | Application/Data Layer Handoff Record | [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) | Section 01 | Snapshot, payload, correlation, outcome rules and expected QA behavior | Completed — simulation documentation |
| `FD-REC-02` | GTM Variable Inventory | [`FD-REC-02`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md) | Section 02 | Variable names, source paths, types, missing behavior and consumers | Completed — simulation documentation |
| `FD-REC-03` | GTM Trigger Inventory | [`FD-REC-03`](fd-calculation-records/FD-REC-03-gtm-trigger-inventory.md) | Section 03 | Authoritative event, filters, frequency and controls | Completed — simulation documentation |
| `FD-REC-04` | GTM Tag Inventory | [`FD-REC-04`](fd-calculation-records/FD-REC-04-gtm-tag-inventory.md) | Section 04 | Google tag, GA4 Event tag, mapping, consent, destination and count | Completed — simulation documentation |
| `FD-REC-05` | Consent Decision Record | [`FD-REC-05`](fd-calculation-records/FD-REC-05-consent-decision.md) | Section 05 | CMP source, default/update behavior and `analytics_storage` | Completed — simulation documentation |
| `FD-REC-06` | Template Governance Decision | [`FD-REC-06`](fd-calculation-records/FD-REC-06-template-governance-decision.md) | Section 06 | Native Tag decision and custom-template exception boundary | Completed — simulation documentation |
| `FD-REC-07` | Measurement Plan & Event Contract | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) | Section 07 | Business question, event meaning, parameters, privacy and destination | Approved — simulation design |
| `FD-REC-08` | Debug and QA Simulation Record | [`FD-REC-08`](fd-calculation-records/FD-REC-08-debug-qa.md) | Section 08 | Test setup, data safety, test matrix, scenario summary and evidence mapping | Completed — simulation documentation |
| `FD-REC-09` | GA4 Report and Exploration Record | [`FD-REC-09`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md) | Section 09 | Field readiness, Report/Exploration configuration, formula and interpretation | Completed — simulation documentation |
| `FD-REC-10` | Release and Monitoring Simulation Record | [`FD-REC-10`](fd-calculation-records/FD-REC-10-release-monitoring.md) | Section 10 | Version, approval, smoke test, threshold, rollback and observation | Completed — simulation documentation |
| `FD-REC-11` | **[RUNTIME VERIFICATION] End-to-End Runtime Verification Record** | [`FD-REC-11`](fd-calculation-records/FD-REC-11-runtime-verification.md) | Sections 08 + 10 | Reconcile real runtime evidence from Application to GA4 and sign off release | Not applicable — simulation-only |

## Handoff brief

This package is a simulation-management baseline for one FD GA4/GTM event. The journey coordinates status and decisions; `FD-REC-xx` files hold the detailed records; the `HOW-TO` sections are reference instructions and have not been executed.

### Owner proposal

The semantic design of `calculation_action` is approved. Phase 2 documents the Application/Data Layer handoff and Phase 3 documents Variables, Trigger, Tags, consent and template governance. The simulated architecture uses one authoritative Application event, one GTM flow, separate QA/production destinations and hostname allowlisting.

### Decisions already approved

| Decision | Approved result | Source |
|---|---|---|
| Business meaning | Terminal calculation outcome: response `length > 0` → `solution_found="Yes"`; `[]` or contract-defined error → `"No"` | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) |
| Negative cases | Input validation emits no event; API failure, timeout, cancellation and stale outcome emit one `"No"`; duplicate callback emits once | [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) |
| GA4 payload | Ten approved fields; no full `inputs`, API response, token, secret or PII | [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) |
| Key event | `calculation_action` is a key event | `FD-REC-07` |
| Custom definitions | `solution_found` and five categorical fields; no initial definitions for `fx`, `fy`, `app_name`, `event_schema_version` | `FD-REC-07` |
| Environment | Two simulated GA4 destinations, one simulated GTM container and hostname routing | `FD-REC-00` |
| Consent | Default `analytics_storage=denied`; collection only when `granted`; denied/unknown suppresses | `FD-REC-05` |
| Implementation mode | Happy-path documentation only; no runtime action | Journey scope |

### Review boundary

- Phase 0 uses simulated values; real accounts, IDs, permissions and live changes were not verified.
- Phase 1 approved schema `1.0`; Phase 2 documents payload, correlation, outcome and QA expectations only.
- Phase 3 documents GTM/consent/template behavior; Phase 4 documents reporting behavior; Phase 5 documents QA expectations; Phase 6 documents release/monitoring controls. All seven phases (0–6) are complete as simulation documentation; no live implementation is implied.
- Raw API responses, request tokens, secrets, PII and the full `inputs` object are never approved for GA4.

### Handoff gate

```text
Review business meaning and occurrence rule
→ approve GA4 allowlist and consent/privacy
→ approve key-event/custom-definition decisions
→ approve schema 1.0
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
| `FD-DEC-01-001` | `calculation_action` is a key event | `FD-REC-07` | Approved |
| `FD-DEC-01-002` | `solution_found` uses `Yes`/`No`; `[]` and error both mean `No` | `FD-REC-07` | Approved |
| `FD-DEC-01-003` | Ten-field GA4 allowlist; no full object, response, secret or PII | `FD-REC-07` | Approved |
| `FD-DEC-01-004` | Create custom dimensions for `solution_found` and five categorical fields in Phase 4 | `FD-REC-07` | Approved |
| `FD-DEC-02-001` | No Application action/runtime; use a happy-path payload assumption | `FD-REC-01` | Approved for simulation |
| `FD-DEC-03-001` | Use Version 2 Data Layer Variables; no GTM business logic or stale fallback | `FD-REC-02` | Approved for simulation |
| `FD-DEC-03-002` | Use one authoritative Custom Event Trigger; no click/page/DOM substitute | `FD-REC-03` | Approved for simulation |
| `FD-DEC-03-003` | Use native Google tag + native GA4 Event tag; no custom template | `FD-REC-04`, `FD-REC-06` | Approved for simulation |
| `FD-DEC-03-004` | Default `denied`; `granted` allows collection; `unknown` fails safe | `FD-REC-05` | Approved for simulation |
| `FD-DEC-04-001` | Use a Detail Report for recurring event counts, a Free-form Exploration for event-level QA, and no Funnel for the current one-event contract | `FD-REC-09` | Approved for simulation |
| `FD-DEC-04-002` | The current output metric is event-level, not user-level; `No` combines empty response and terminal error | `FD-REC-09`, `FD-REC-07` | Approved for simulation |
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
- [x] Schema `1.0` approved for the simulation.

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
  → one complete calculation_action push
```

Rules: `length > 0` → `Yes`; `[]`/terminal error → `No`; invalid input → no event; automatic retry reuses the attempt/snapshot; duplicate, stale, late, remount and replay callbacks add no event. Application may retain the complete snapshot internally, while GTM/GA4 receives only approved scalars.

### Phase 2 acceptance

- [x] `FD-REC-01` records envelope, snapshot, correlation and outcome rules.
- [x] Required and optional fields, prohibited fields and missing behavior are explicit.
- [x] `TC-FD-01`–`TC-FD-15` expected QA scenarios are documented.
- [x] Payload examples are labelled simulated; no runtime result is claimed.

## 8. Phase 3 — GTM Variables, Triggers, Tags, Consent and Template Governance

Phase 3 is documented in five records:

1. [`FD-REC-02 — GTM Variable Inventory`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md): Version 2 Data Layer Variables, nested paths, missing behavior and hostname routing.
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
- [x] One Application occurrence is expected to produce one push, one Trigger match, one Tag fire and one request in a future runtime run.

```text
Phase 3 status: Completed — simulation documentation
Execution state: Simulation only — no live GTM configuration, Preview, Network or publish
Next action: Open Phase 4 — GA4 Reports and Explorations simulation
```

## 9. Phase 4 — GA4 custom definitions, Reports and Explorations

Phase 4 is documented in [`FD-REC-09 — GA4 Report and Exploration Record`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md). It converts the approved event contract into a simulated reporting design; it does not create custom definitions, publish Reports, run Explorations or inspect processed GA4 data.

### Phase 4 decisions

1. The current FD analysis is an **event-level calculation-attempt rate**, not a user-level completion rate. The schema does not define a user-level population or identity requirement.
2. Use a **Detail Report** for recurring `calculation_action` counts and output distribution by `design_method` and `solution_found`.
3. Use a **Free-form Exploration** for event-level QA of field availability, schema version, method values and outcome values.
4. Mark **Funnel Exploration as N/A** because the current contract has no approved `calculation_start → calculation_action` sequence.
5. Use an approved export/BigQuery calculation only when a future requirement needs an exact reproducible ratio, distinct-user logic or joins that the GA4 UI cannot express.
6. Keep `(not set)`, `Unassigned`, invalid values and missing `solution_found` separate from the validated output rate.

### Phase 4 output rate

```text
Numerator   = calculation_action events where design_method = X and solution_found = Yes
Denominator = calculation_action events where design_method = X and solution_found ∈ {Yes, No}
Output rate = Numerator / Denominator
```

The numerator and denominator use the same property, stream, date range, method, consent-allowed population, event eligibility and event grain. Because `solution_found="No"` groups empty responses and terminal errors, the metric is labelled a **combined output rate**, not a pure solution-success rate.

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
Next action: Open Phase 5 — Debug/QA and evidence simulation
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
- [x] Test Matrix covers output, no-output, validation, failure, timeout, stale, retry, privacy, consent and routing cases.
- [x] Scenario Execution Summary explicitly records that no scenario was executed.
- [x] Evidence mapping identifies the Application, Data Layer, GTM, consent, Network, DebugView and processed-data proof required in a real run.
- [x] Runtime Verification remains `Not applicable — simulation-only`.

```text
Phase 5 status: Completed — simulation documentation
Execution state: Simulation only — no browser, Preview, Network, DebugView or processed-data execution
Next action: Open Phase 6 — Release and Monitoring simulation
```

## 11. Phase 6 — Release, Monitoring and Rollback

Phase 6 is documented in [`FD-REC-10 — Release and Monitoring Simulation Record`](fd-calculation-records/FD-REC-10-release-monitoring.md). It links the approved contract, implementation records, reporting design and QA package into a simulated release packet.

### Phase 6 decisions

1. The future live change is classified as **High risk** because it affects an event schema, key event, destination, consent and reporting.
2. Release gates 0–4 are defined, but only Gates 0–1 can be treated as documentation-ready in the current simulation. Gates 2–4 require runtime evidence or a real publish.
3. Monitoring must cover collection volume, combined output/no-outcome distribution, duplicates/missingness, destination, consent/privacy, freshness and vocabulary quality.
4. Thresholds are intentionally qualitative until a real baseline exists. No production percentage is invented in the simulation.
5. Rollback restores future GTM behavior only; it does not repair historical GA4 data or undo an irreversible filter.

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
Journey status: All documentation phases complete; runtime project remains out of scope
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
