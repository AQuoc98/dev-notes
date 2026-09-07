# FD-REC-08 — Debug and QA Simulation Record

> **SIMULATED — Phase 5 documentation only.** This record defines the QA package for `calculation_action`; it does not run the Application, GTM Preview, Network, DebugView, Realtime or processed-data checks.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-08` |
| Record name | Debug and QA Simulation Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 5 — Debug/QA and evidence |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Collection dependencies | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md), [`FD-REC-05`](FD-REC-05-consent-decision.md) |
| Reporting dependency | [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md) |
| Status | **Completed — simulation documentation** |
| Execution state | Simulation only — no runtime or platform evidence |
| Value/evidence boundary | Expected results and evidence pointers are simulated; no actual test was executed |
| Primary owner | FD QA owner — simulated alias |
| Reviewers | Application, GTM, Analytics and Privacy owners — simulated aliases |
| Open items | Runtime execution, real evidence retention and defect/retest records remain outside this project |
| Next action | Keep this QA package as the baseline for any future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-07` / `2026-09-07` |

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
3. Payload fields, types and values match the allowlist.
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
| Environment | Pass — QA hostname only | Never use a production destination for this run |
| Test data | Pass — synthetic account and safe values | No real customer data |
| Payload review | Pass — no PII, credentials, secrets or raw API response | Remove prohibited fields before any future push |
| URL and logs | Pass — no token, sensitive query string or account identifier | Redact logs before sharing |
| Evidence handling | Pass — controlled location and redaction required | Runtime artifacts require access control |
| Consent | Pass — state recorded for every scenario | Denied/unknown must follow `FD-REC-05` |
| Cleanup | Pending owner assignment in a future runtime project | Remove preview links, debug settings and temporary data after the run |

The result is a simulated safety decision, not evidence that a real payload or artifact was inspected.

## 4. Required Test Matrix

The matrix copies the stable contract from `FD-REC-01` and `FD-REC-07`. Each row is an expected scenario, not an executed test.

| Test ID | Case | Simulated action | Expected outcome |
|---|---|---|---|
| `TC-FD-01` | Happy path with output | Complete valid input; response array has `length > 0` | One `calculation_action`; `solution_found="Yes"` |
| `TC-FD-02` | Valid no-output | Complete valid input; response is `[]` | One event; `solution_found="No"` |
| `TC-FD-03` | Input validation failure | Submit invalid or incomplete input | No `calculation_action` |
| `TC-FD-04` | Server/API failure | Simulate HTTP 5xx or network failure | One terminal error event; `solution_found="No"` |
| `TC-FD-05` | Stale response | New valid snapshot terminalizes an older attempt | One `"No"` for the terminalized attempt; late callback ignored |
| `TC-FD-06` | Retry/duplicate callback | Retry or deliver the same callback more than once | One event per occurrence |
| `TC-FD-07` | Timeout | Simulate request timeout | One terminal error event; `solution_found="No"` |
| `TC-FD-08` | Remount/replay | Remount component or replay callback | No additional event |
| `TC-FD-09` | Intentional resubmit/new snapshot | User starts a new attempt | One new occurrence and one new event |
| `TC-FD-10` | Prohibited field | Include token, secret, PII or raw response in an internal example | Field removed before analytics message; QA defect if exposed |
| `TC-FD-11` | Missing/invalid required field | Remove envelope or required snapshot field | No misleading success event; contract defect |
| `TC-FD-12` | Missing optional `fx`/`fy` | Omit one optional numeric field | Omit the field; never reuse a prior value |
| `TC-FD-13` | Invalid numeric field | Use wrong type or range outside `0–500 kN` | Input validation; no event |
| `TC-FD-14` | Cancellation | Cancel the current attempt under the contract | One `"No"`; later callback ignored |
| `TC-FD-15` | UI changes after request | Change inputs after snapshot creation | Event uses the original immutable snapshot |
| `TC-FD-16` | Consent denied | Run with `analytics_storage=denied` | Suppress/block analytics according to `FD-REC-05`; no bypass |
| `TC-FD-17` | Consent unknown | CMP is delayed or unresolved | Fail-safe denied; no unauthorized analytics event |
| `TC-FD-18` | Hostname routing | Use QA hostname and inspect destination expectation | QA Measurement ID only; unknown hostname blocked |

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
| `TC-FD-16`–`TC-FD-18` | `[simulated]` | Consent and destination guardrails | Contract-specific | N/A — not executed | N/A — simulation | Future Evidence rows required |

The summary does not mark a simulated scenario as Pass. It records expected behavior and the evidence that a runtime project would need.

## 6. Evidence Template mapping

For a future material run, create one evidence row per relevant layer. Current values are expected, not observed.

| Test ID | Layer | Expected proof | Current status | Future artifact |
|---|---|---|---|---|
| `TC-FD-01` | Application | Terminal output state reached once and matches the snapshot | Not executed | Sanitized Application log |
| `TC-FD-01` | Data Layer | One complete `calculation_action` message with schema `1.0` | Not executed | Data Layer capture |
| `TC-FD-01` | GTM Preview/Tag Assistant | One authoritative Trigger match and one Event Tag evaluation | Not executed | Preview session |
| `TC-FD-01` | Consent | `analytics_storage=granted` permits the approved Tag behavior | Not executed | Consent evidence |
| `TC-FD-01` | Network | One QA request with the approved destination and scalar parameters | Not executed | Redacted Network capture |
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
- [x] Scenario Execution Summary is present and explicitly marked not executed.
- [x] Evidence Template mapping identifies the proof required at each layer.
- [x] Runtime Verification is kept separate and remains not applicable.
- [x] Debug Session and Defect/Retest records remain conditional.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.

## 9. Cross-references

- Section 07 / [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md): event contract, occurrence, allowlist and consent decisions.
- Section 09 / [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md): reporting field readiness and processed-data limitations.
- Section 10: release gates, smoke checks, monitoring and rollback after QA is authorized.
- [`FD-REC-11`](FD-REC-11-runtime-verification.md): future real end-to-end runtime record only.
