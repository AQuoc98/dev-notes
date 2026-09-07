# FD-REC-08 — Debug and QA Simulation Record

> **SIMULATED — Phase 5 documentation only.** This record defines the QA package for `calculation_action`; it does not run the Application, GTM Preview, Network, DebugView, Realtime or processed-data checks.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-08` |
| Record name | Debug and QA Simulation Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 5 — Debug/QA and evidence |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for the historical outcome rename |
| Why this matters | QA must prove the minimized schema `3.0` payload, cross-layer `event_id`, strict contract rejection, consent lifecycle and the evidence-selected deployment path. |
| Collection dependencies | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md), [`FD-REC-05`](FD-REC-05-consent-decision.md) |
| Reporting dependency | [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md) |
| Status | **Design complete — simulation documentation; `FD-CR-002` applied; runtime not executed** |
| Execution state | Simulation only — no runtime or platform evidence |
| Value/evidence boundary | Expected results and evidence pointers are simulated; no actual test was executed |
| Primary owner | FD QA owner — simulated alias |
| Reviewers | Application, GTM, Analytics and Privacy owners — simulated aliases |
| Open items | `FD-OPEN-001`–`004`, runtime-baseline verification, execution, evidence retention and defect/retest records remain unresolved |
| Next action | Resolve the blockers, then execute this schema `3.0` QA package and retain schemas `1.0`/`2.0` only as separate historical populations |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-07` / `2026-09-07` — `FD-CR-002` |

## 0.1 Source of the record format

This record combines the mandatory P0 QA records and conditional P1/P2 records defined in Section 08. It is not a Google-provided form and does not create a new template family.

| Record component | Reference |
|---|---|
| Objective, validation path and material-event pass rule | [Section 08 — Debug and QA](../08-debug-qa-answer.md) |
| Test Run Setup, Data Safety Check and Required Test Matrix | [Section 08 — QA record priority](../08-debug-qa-answer.md#21-record-priority) |
| Scenario Execution Summary and Evidence Template | [Section 08 — Scenario and evidence records](../08-debug-qa-answer.md#26-scenario-execution-summary) |
| Runtime Verification boundary | [`FD-REC-11`](FD-REC-11-runtime-verification.md) |
| Event expectations and negative cases | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |

## 1. QA scope and pass rule

### 1.1 Objective

The future runtime QA run must identify the first failing layer in:

```text
Application state
→ Data Layer message
→ GTM Trigger and Variable evaluation
→ consent decision
→ GA4 Network request
→ DebugView/Realtime
→ processed result when required
```

This record currently documents the expected behavior only.

### 1.2 Material-event pass rule

For a future runtime run, `calculation_action` can be marked **Pass** only when the following all have matching evidence:

1. The Application reached the authoritative terminal state.
2. Counts match the approved one-occurrence/one-event contract.
3. The same opaque `event_id` appears once across Application evidence and Data Layer and, when consent permits collection, the outbound request; denied/unknown scenarios instead prove zero analytics requests. Payload fields, types and values match the allowlist.
4. Destination and environment are correct.
5. Consent behavior matches the consent state.
6. Required downstream result is observed.

`Pending` is allowed only for a documented GA4 processing window with an owner, follow-up date, property, stream and expected check. It is not a completed Pass.

## 2. Test Run Setup Record

| Field | Simulated value |
|---|---|
| Test run ID | `QA-FD-CALC-RUN-001` |
| Environment and URL | QA/staging / `https://app-staging.strongtie.com/fd` |
| Application/build | `fd-web-simulated-build-001` |
| GTM container/workspace/version | `GTM-FAKEFD01` / `WS-FD-CALC-001` / `[SIMULATED-VERSION-001]` |
| GA4 property/stream/Measurement ID | `FD Web — QA — SIMULATED` / `FD Web QA — app-staging — SIMULATED` / `G-FAKEFDQA01` |
| Browser/device/date | Chrome desktop / synthetic profile / `[simulated timestamp]` |
| Test account/data | `fd-qa-synthetic-001`; safe values only |
| Consent state | `analytics_storage=granted` for the primary happy path; denied/unknown are negative scenarios |
| Reset method | Fresh profile; clear only scenario-required Application and consent state |
| Tester/reviewer | FD QA owner / Analytics owner — simulated aliases |

This setup is a future-run template populated with safe simulated values. It does not claim that a browser session or GTM/GA4 asset was opened.

## 3. Data Safety Check

| Check | Simulated result | Rule |
|---|---|---|
| Environment | Design-complete — QA hostname specified; not verified | Never use a production destination for this run |
| Test data | Design-complete — synthetic account specified; not verified | No real customer data |
| Payload review | Design-complete — prohibited-data rule specified; not inspected | Remove prohibited fields before any future push |
| URL and logs | Design-complete — redaction rule specified; not inspected | Redact logs before sharing |
| Evidence handling | Design-complete — controlled location required; not verified | Runtime artifacts require access control |
| Consent | Design-complete — scenario state required; not verified | Denied/unknown must follow `FD-REC-05` |
| Cleanup | Pending owner assignment in a future runtime project | Remove preview links, debug settings and temporary data after the run |

The result is a simulated safety decision, not evidence that a real payload or artifact was inspected.

## 4. Required Test Matrix

The matrix copies the stable contract from `FD-REC-01` and `FD-REC-07`. Each row is an expected scenario, not an executed test.

| Test ID | Case | Simulated action | Expected outcome |
|---|---|---|---|
| `TC-FD-01` | Happy path with output | Complete valid input; response array has `length > 0` | One `calculation_action`; `solution_found="Yes"` |
| `TC-FD-02` | Valid no-output | Complete valid input; response is `[]` | One event; `solution_found="No_solution"` |
| `TC-FD-03` | Input validation failure | Submit invalid or incomplete input | No `calculation_action` |
| `TC-FD-04` | Server/API failure | Simulate HTTP 5xx or network failure | One terminal error event; `solution_found="No_solution"` |
| `TC-FD-05` | Stale response | New valid snapshot terminalizes an older attempt | One `"No_solution"` for the terminalized attempt; late callback ignored |
| `TC-FD-06` | Retry/duplicate callback | Retry or deliver the same callback more than once | One event per occurrence |
| `TC-FD-07` | Timeout | Simulate request timeout | One terminal error event; `solution_found="No_solution"` |
| `TC-FD-08` | Remount/replay | Remount component or replay callback | No additional event |
| `TC-FD-09` | Intentional resubmit/new snapshot | User starts a new attempt | One new occurrence and one new event |
| `TC-FD-10` | Prohibited field | Include token, secret, PII or raw response in an internal example | Field removed before analytics message; QA defect if exposed |
| `TC-FD-11` | Missing/invalid required field | Remove envelope or required snapshot field | No misleading success event; contract defect |
| `TC-FD-12` | Optional internal Application input absent | Omit an Application-only optional value | Product/API behavior remains valid; no invented analytics parameter or stale value |
| `TC-FD-13` | Invalid internal Application input | Use a wrong type or out-of-range product/API value | Input validation; no attempt and no analytics event |
| `TC-FD-14` | Cancellation | Cancel the current attempt under the contract | One `"No_solution"`; later callback ignored |
| `TC-FD-15` | UI changes after request | Change inputs after snapshot creation | Event uses the original immutable snapshot |
| `TC-FD-16` | Consent denied | Run with `analytics_storage=denied` | Suppress/block analytics according to `FD-REC-05`; no bypass |
| `TC-FD-17` | Consent unknown | CMP is delayed or unresolved | Fail-safe denied; no unauthorized analytics event |
| `TC-FD-18` | Hostname routing | Test QA, production and an unknown hostname with approved safe methods | QA→QA, production→production, unknown blocked; no production fallback |
| `TC-FD-19` | Consent granted | Start clean, grant analytics, then run one event | Same-page update precedes the event; approved storage/request behavior; one request |
| `TC-FD-20` | Consent revocation | Start granted, run one event, revoke, then run another | Revocation update occurs immediately; later event suppressed; no duplicate/replay; cleanup follows CMP policy |
| `TC-FD-21` | Direct landing and refresh | Open a deep link and refresh without a prior route | Stored choice/default initializes correctly before normal Tags; no duplicate update/event |
| `TC-FD-22` | SPA route change | Navigate without full reload, then run one event | Consent persists; one business event and no duplicate consent update |
| `TC-FD-23` | CMP slow or blocked | Delay/block the CMP in QA | Fail-safe denied remains; no analytics request or accidental grant |
| `TC-FD-24` | Wrong event/schema/app/required field | Change casing/schema/app or remove each required field | Trigger does not match; Tag/request count is zero; contract defect recorded |
| `TC-FD-25` | Browser/device regression | Run the approved path in the Phase 0 browser matrix | Product action works; count, consent, payload and destination stay consistent |
| `TC-FD-26` | `event_id` uniqueness | Run two intentional occurrences, including a resubmit of the same input | Each occurrence has a different opaque UUID; each ID is stable within its own cross-layer evidence |
| `TC-FD-27` | Deployment path / v2→v3 compatibility | Prove the actual runtime baseline. For a new deployment, exercise v3 and legacy rejection; if live v2 exists, exercise v2/v3 on compatibility and then v2 on final v3-only | Selected path matches evidence; exactly one request per eligible payload; final v3-only version rejects v2 |

## 5. Scenario Execution Summary

The summary below is intentionally labelled simulated. In a real run, replace `N/A — not executed` with actual counts and evidence IDs.

| Test ID | Started | Action/result summary | Expected count | Actual count | Status | Evidence/defect/follow-up |
|---|---|---|---:|---:|---|---|
| `TC-FD-01` | `[simulated]` | Output response for the approved snapshot | 1 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-02` | `[simulated]` | Empty response for the approved snapshot | 1 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-03` | `[simulated]` | Invalid input rejected before request | 0 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-04` | `[simulated]` | Terminal API failure | 1 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-05` | `[simulated]` | Stale attempt and late callback | 1 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-06` | `[simulated]` | Retry/duplicate callback | 1 | N/A — not executed | N/A — simulation | Future Evidence row required |
| `TC-FD-07`–`TC-FD-15` | `[simulated]` | Validation, timeout, replay, privacy and snapshot guardrails | Contract-specific | N/A — not executed | N/A — simulation | Future Evidence rows required |
| `TC-FD-16`–`TC-FD-27` | `[simulated]` | Consent lifecycle, destination, malformed-contract, browser, event-ID uniqueness and migration guardrails | Contract-specific | N/A — not executed | N/A — simulation | Future Evidence rows required |

The summary does not mark a simulated scenario as Pass. It records expected behavior and the evidence that a runtime project would need.

## 6. Evidence Template mapping

For a future material run, create one evidence row per relevant layer. Current values are expected, not observed.

| Test ID | Layer | Expected proof | Current status | Future artifact |
|---|---|---|---|---|
| `TC-FD-01` | Application | Terminal output state reached once and matches the internal snapshot; one opaque `event_id` created | Not executed | Sanitized Application log |
| `TC-FD-01` | Data Layer | One minimized `calculation_action` message with schema `3.0`, the same `event_id` and approved outcome/required fields | Not executed | Data Layer capture |
| `TC-FD-01` | GTM Preview/Tag Assistant | One authoritative Trigger match and one Event Tag evaluation | Not executed | Preview session |
| `TC-FD-01` | Consent | `analytics_storage=granted` permits the approved Tag behavior | Not executed | Consent evidence |
| `TC-FD-01` | Network | One QA request with the approved destination, same `event_id` and nine scalar parameters | Not executed | Redacted Network capture |
| `TC-FD-01` | DebugView/Realtime | One recent event in the intended QA stream | Not executed | DebugView/Realtime capture |
| `TC-FD-01` | Processed data | Report/Exploration fields available after processing, if required | Not executed | Report follow-up |

Repeat the row set for the negative and routing scenarios when the relevant layer is affected. Do not use a Network request as proof that a processed Report is ready.

## 7. Conditional records

| Record | Current decision |
|---|---|
| `[RUNTIME VERIFICATION]` `FD-REC-11` | Not applicable — no real runtime run was performed |
| Debug Session Record | Not required; no intermittent or first-failing layer was observed because no run occurred |
| Defect and Retest Record | Not required; no failure was executed or reproduced |
| Processed-data follow-up | Required only in a future run if a Report/Exploration result is part of the acceptance decision |

## 8. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove that a runtime test was executed.

- [x] `FD-REC-08` uses the Section 08 QA record order.
- [x] Test Run Setup is defined with a QA destination and synthetic data boundary.
- [x] Data Safety Check covers environment, payload, consent, redaction and cleanup.
- [x] Test Matrix covers positive, negative, duplicate, consent, privacy, routing and snapshot cases.
- [x] Consent update/revocation, direct landing, SPA, CMP failure, malformed contract, browser regression, event-ID uniqueness and deployment-path/migration cases are explicit.
- [x] Scenario Execution Summary is present and explicitly marked not executed.
- [x] Evidence Template mapping identifies the proof required at each layer.
- [x] Runtime Verification is kept separate and remains not applicable.
- [x] Debug Session and Defect/Retest records remain conditional.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
