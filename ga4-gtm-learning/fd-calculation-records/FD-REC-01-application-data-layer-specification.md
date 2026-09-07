# FD-REC-01 — Application/Data Layer Handoff Record

> This record applies the structure from [Section 01 — Data Layer Design](../01-data-layer-design-answer.md) to the FD journey. It is a simulated happy-path handoff: the Application is assumed to have created a complete snapshot and pushed a Data Layer message for GTM. It does not connect to Application source code and is not QA or production evidence.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-01` |
| Record name | Application/Data Layer Handoff Record |
| Document type | `PROJECT RECORD` |
| Record version | `0.5-management-ready` |
| Phase | Phase 2 — Data Layer contract and handoff |
| Product / journey | FD web application / `J-FD-CALC-001` |
| Source of truth | [FD-REC-07 — Measurement Plan & Event Contract](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Execution state | Simulation only — happy-path assumption |
| Baseline dependency | [`FD-REC-00 — Phase 0 System Inventory Record`](FD-REC-00-phase-0-system-inventory.md) |
| Status | **Completed — simulation documentation** |
| Review state | Payload handoff, outcome rules, privacy boundary and expected QA behavior prepared at documentation level |
| Evidence boundary | Happy-path payload is a contract assumption; no code implementation or runtime evidence exists |
| Primary owner | FD Analytics/GTM Lead — simulated owner |
| Application owner | `fd-developer@strongtie.com` — simulated alias |
| Analytics/GTM reviewer | `fd-analytics-owner@strongtie.com`, `fd-gtm-implementer@strongtie.com` — simulated aliases |
| Approver / reviewer | Analytics/GTM Lead; Application owner; Privacy reviewer when the payload boundary changes |
| Dependencies | `FD-REC-00` Phase 0 baseline; `FD-REC-07` schema `1.0-approved` |
| Open items / risks | GTM/GA4/QA/release phases are described as simulation only; no live verification is in scope |
| Next action | Keep this handoff as the collection dependency for any future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-04` / `2026-09-06` |

### 0.1 Source of the record format

This record converts the approved Section 01 Data Layer structure into a project-specific FD handoff. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Data Layer envelope, snapshot and event lifecycle | [Section 01 — Data Layer Design](../01-data-layer-design-answer.md) |
| Approved event meaning and parameter dictionary | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Simulated application flow and current payload boundary | FD calculation flow supplied for this journey |
| Expected QA scenarios | [Section 08 — Debug and QA](../08-debug-qa-answer.md) |

## 1. Contract record

| Field | FD decision |
|---|---|
| Event name | `calculation_action` |
| Business definition | A calculation attempt with a terminal outcome after a complete input snapshot is sent to the simulated Calculation API and the Application classifies the response or error under the contract |
| Valid occurrence | One valid attempt with exactly one terminal outcome and at most one Data Layer push |
| Emission timing | After response correlation and outcome classification; never at input change, click, request start, render or remount |
| Expected frequency | One event for each occurrence terminalized by the Application; no fixed rate because frequency depends on user behavior |
| Required event fields | `event`, `event_schema_version`, `app_name`, `solution_found`, `inputs` |
| Optional fields | Within `inputs`, `fx` and `fy` are optional under `FD-REC-07`; do not invent fallback values |
| Output mapping | Response array with `length > 0` → `solution_found: "Yes"` |
| No-output mapping | Response array is `[]` → `solution_found: "No"` |
| Error mapping | API error, timeout or cancellation/stale error that terminalizes the attempt → `solution_found: "No"` once |
| Input validation | UI input validation → no calculation event |
| Response correlation | Response must belong to the immutable snapshot; a late callback for a terminal attempt or old snapshot must not create another event |
| Privacy boundary | Complete `inputs` is for Application/Data Layer and QA only; GTM/GA4 receives only the approved scalar allowlist |
| Consent boundary | The Data Layer message is simulated at Application level; GA4 collection must follow the `analytics_storage` policy in Phase 3 |
| Schema version | `1.0` |
| Consumers | GTM Variables/Trigger/Tags, GA4 event, QA evidence and later reporting |

## 2. Event envelope

| Field | Type | Required | Value/rule | Source |
|---|---|---|---|---|
| `event` | string | Yes | Exactly `calculation_action` | Application Data Layer contract |
| `event_schema_version` | string | Yes | Exactly `1.0` | Application constant |
| `app_name` | string | Yes | Exactly `fd` | Application constant |
| `solution_found` | string | Yes | Only `Yes` or `No` | Application outcome classifier |
| `inputs` | object | Yes | Complete immutable snapshot linked to the attempt | Application snapshot store |

GTM must not change the type or infer a new value for `solution_found`. Correlation keys, request tokens and API response bodies, if simulated, exist only in the Application internal log.

## 3. Input snapshot schema

The complete snapshot must preserve the input set sent to the API and must not be mutated by newer state. An automatic retry within the same attempt does not create a new occurrence. The lifecycle and correlation keys below are handoff requirements for the Application team.

### 3.1 Approved tracking-contract fields

Required/optional status in this table follows the Parameter Dictionary in `FD-REC-07`.

| Path | Type | Required in complete snapshot | Rule |
|---|---|---:|---|
| `inputs.country` | string | Yes | Controlled country code, for example `gb` |
| `inputs.language` | string | Yes | Controlled language code, for example `en` |
| `inputs.building_code` | string | Yes | Controlled building-code identifier |
| `inputs.design_method` | string | Yes | Controlled design-method code |
| `inputs.connection_type` | string | Yes | Controlled connection-type code |
| `inputs.fx` | number/decimal | No | If present: `0–500`, unit `kN` |
| `inputs.fy` | number/decimal | No | If present: `0–500`, unit `kN` |

### 3.2 Example fields outside the tracking contract

The example in Section 5 retains `unit_system`, `fastener_installation`, `load_duration`, thickness, grade, density, `contact_length`, `predrilled`, `fastener_angle` and `service_class` from the supplied example. This record does not define required/optional status, domain or unit rules for them and does not add them to the GA4 allowlist. Their presence in an example does not make them required for tracking schema `1.0`.

### 3.3 Missing and invalid data behavior

- If a required envelope/contract field is missing or invalid, do not emit a message that violates the contract; record the issue for QA under `FD-REC-07`.
- If `fx` or `fy` is absent, omit that key. Do not replace it with `0`, an empty string, `unknown` or a value from a previous event. A valid supplied `0` must be preserved.
- If `fx` or `fy` has the wrong type or is outside `0–500 kN`, apply input validation and do not emit under the approved contract.
- Do not infer a complete allowed-value list from one example. Validation for required fields, `fx` and `fy` is an Application requirement and belongs in the expected QA matrix.
- Prohibited fields must not appear in the analytics message. The Application removes them before the push; GTM maps only approved scalars and never sends the full `inputs` object to GA4.

GA4 maps only these approved scalars: `app_name`, `event_schema_version`, `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`, `fx` and `fy`. There is no GA4 parameter named `inputs`.

### 3.4 Identity and correlation model

The simulated Application uses three internal identities. They support state storage, request logs and review notes only; they must not be placed in the Data Layer message for GTM/GA4.

| Identity | Scope | Creation and usage rule |
|---|---|---|
| `attempt_id` | One business occurrence | Created after input validation passes. An attempt may have transport retries but only one terminal outcome and at most one event. |
| `snapshot_id` | One immutable input snapshot | Created with the attempt. One attempt has one snapshot; retries reuse it. A new valid input creates a new snapshot and attempt. |
| `request_id` | One transport request | Each initial request and retry gets a new request ID. The request record references `attempt_id`, `snapshot_id` and `retry_index`. |

Minimum Application correlation registry:

```javascript
{
  request_id,
  attempt_id,
  snapshot_id,
  snapshot,
  retry_index,
  status: "in_flight" | "retryable_error" | "terminal",
  event_emitted: false,
}
```

Correlation is valid when `request_id` exists in the registry, the request belongs to a non-terminal attempt and the response references the same `snapshot_id`. An unknown request, terminal attempt or snapshot mismatch is invalid; the Application ignores it and does not push the Data Layer. A retryable transport error moves the request to `retryable_error` so a new request can reuse the same attempt/snapshot. When retry policy ends or the error is non-retryable, the attempt terminalizes and emits `"No"` once. This is a management requirement, not implemented code.

## 4. Simulated Application flow and handoff behavior

The flow below is a happy-path assumption. State names are record labels; the payload is assumed to have been pushed by the Application, with no code or runtime implementation in this journey.

```text
Idle
  → input changed
  → validate current input
      → invalid: validation result; no new attempt and no calculation_action
      → valid: create new attempt and immutable snapshot
  → send initial simulated API request
  → receive response/error
  → correlate request with attempt and snapshot
  → if a newer valid snapshot starts, terminalize older attempt as stale error
  → classify terminal outcome
  → apply one-event-per-attempt guard
  → push one complete calculation_action message
```

### 4.1 Terminal outcome rules

Data Layer actions follow `FD-REC-07`; Attempt state is a management label for this simulation.

| Situation | Attempt state | Data Layer action |
|---|---|---|
| Response array `length > 0` | `terminal: output` | Push one event with `solution_found: "Yes"` |
| Response `[]` | `terminal: no_output` | Push one event with `solution_found: "No"` |
| API error | `terminal: error` | Push one event with `solution_found: "No"` |
| Timeout | `terminal: error` | Push one event with `solution_found: "No"` |
| Cancellation/stale error before terminal state | `terminal: error` | Push one event with `solution_found: "No"` once |
| Late response after terminal state | `ignored` | Do not push an event |
| UI input validation | `rejected_before_request` | Do not push an event |

#### 4.1.1 Response/error examples and mapping

| Example ID | Simulated API result | Retry policy | Terminal outcome | Data Layer result |
|---|---|---|---|---|
| `EX-FD-OUTPUT-001` | Response array contains one solution | No retry | `output` | One event, `solution_found: "Yes"` |
| `EX-FD-EMPTY-001` | Response is `[]` | No retry | `no_output` | One event, `solution_found: "No"` |
| `EX-FD-HTTP-503-001` | HTTP/server failure | Non-retryable in this example | `error` | One event, `solution_found: "No"` |
| `EX-FD-NETWORK-RETRY-001` | Network error first, then output | Retry at most once; keep attempt/snapshot | `output` after retry | One event only, `solution_found: "Yes"` |
| `EX-FD-TIMEOUT-001` | Request timeout | Terminal error | `error` | One event, `solution_found: "No"` |
| `EX-FD-CANCEL-001` | Application cancellation/stale | Terminal error | `error` | One event, `solution_found: "No"` |

A retryable error is an intermediate transport state and does not create an event. When retry policy ends or an error is non-retryable, the attempt terminalizes and emits `"No"` once. These rows are review examples, not runtime evidence.

### 4.2 Approved snapshot lifecycle

| Step | Simulated rule |
|---|---|
| Input accepted | After validation passes, the Application creates an attempt and captures a complete snapshot from the input state at that moment. |
| Snapshot created | The snapshot is normalized under the contract and kept immutable; later UI changes cannot mutate it. |
| Request sent | The first request uses the attempt snapshot; an automatic retry reuses it. |
| New valid snapshot | A new valid input state creates a new attempt and snapshot; occurrence follows `FD-REC-07`. |
| Invalid input | Validation failure creates no attempt, sends no calculation event and does not create a `"No"` outcome for invalid input. |
| Attempt terminal | After response/error classification, the attempt becomes terminal and passes through the one-event guard. |
| Snapshot retention | Retain the snapshot long enough for correlation and message creation; release it under the approved Application policy after terminal/evidence handling. |

### 4.3 Response correlation and stale handling

#### Callback-correlation rules

1. Find `request_id` in the registry.
2. Confirm that the response references the expected `attempt_id` and `snapshot_id`.
3. Reject the callback if the attempt is terminal, `event_emitted=true`, the request is unknown/finished or the snapshot does not match.
4. Pass only the current valid callback to the outcome classifier.

#### Simulated state transitions

| State | Meaning | Transition |
|---|---|---|
| `rejected_before_attempt` | Input validation failed | End; no attempt, request or event |
| `active_snapshot` | Attempt and immutable snapshot exist | Send the first request |
| `request_in_flight` | Current request awaits a response | `terminal` or `retry_pending` |
| `retry_pending` | A transport error may be retried | Create a new `request_id`, keep attempt/snapshot |
| `terminal_output` | Response has `length > 0` | Emit `"Yes"` once |
| `terminal_no_output` | Response is `[]` | Emit `"No"` once |
| `terminal_error` | Final API error, timeout, cancellation or stale error | Emit `"No"` once |
| `event_emitted` | Attempt is locked after the Data Layer push | Ignore all later callbacks |
| `ignored_callback` | Old, unknown or mismatched callback | No state change and no push |

When new valid input starts while an old attempt is active, the old attempt becomes a stale error and emits `"No"` once if it has not emitted. The new attempt receives different IDs. A late callback for the old attempt is ignored and cannot update the new attempt or create another event. Internal IDs never appear in the GTM/GA4 Data Layer payload.

## 5. Happy-path Data Layer payload assumption

This is a simulated payload shape assumed to have been pushed by the Application. GTM is assumed to receive the complete snapshot and map only approved scalar fields to GA4. The full `inputs` object is not sent as a GA4 parameter.

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: "Yes", // response.length > 0; use "No" for [] or an error
  inputs: {
    country: "gb",
    language: "en",
    building_code: "en_1995_1_1_2004_a2_2014",
    design_method: "lsd",
    unit_system: "metric",
    connection_type: "clt_floor_floor_half_lap_joint",
    fastener_installation: "typical",
    fx: 1,
    fy: 0,
    load_duration: "medium_term",
    main_member_thickness: 180,
    side_member_thickness: 180,
    side_member_grade: "c24",
    side_member_density: 350,
    main_member_grade: "c24",
    main_member_density: 350,
    contact_length: 3000,
    predrilled: false,
    fastener_angle: 90,
    service_class: "service_class_1",
  },
});
```

All required snapshot fields must be in the same Application push. Do not complete the current message with values left over from a previous event. Internal identities and the API result are not part of this message.

## 6. Occurrence and idempotency contract

| Scenario | Expected behavior |
|---|---|
| Automatic retry within one attempt | No new occurrence and no new event |
| Duplicate callback | Keep only the first event push |
| Remount/replay callback | No new event |
| User resubmits the same snapshot | New event only if the Application opens a new attempt |
| User creates a new snapshot | New occurrence and event if the Application opens a new attempt under `FD-REC-07` |
| Old snapshot responds after a new snapshot | Do not use the old response for the new snapshot; no late event |

The Application must guard with `event_emitted`, `current_request_id` and registry request state. Automatic retries reuse the attempt/snapshot; old, repeated, replayed or post-cancellation callbacks never create another event.

## 7. Expected QA scenarios

The matrix retains `TC-FD-01`–`TC-FD-06` from Phase 0 and adds negative/edge cases through `TC-FD-15`. It describes expected behavior for a future project; the current project simulates only the happy path and has no runtime result.

| Test ID | Scenario | Expected result |
|---|---|---|
| `TC-FD-01` | Response has output | One event, `solution_found: "Yes"` |
| `TC-FD-02` | Response is `[]` | One event, `solution_found: "No"` |
| `TC-FD-03` | UI input validation | No event |
| `TC-FD-04` | Server failure: HTTP 5xx/network error | One event, `solution_found: "No"` |
| `TC-FD-05` | Stale response / attempt terminalized by stale or cancellation | One `"No"` event for the attempt; late callback adds nothing |
| `TC-FD-06` | Retry/duplicate callback | One event for the occurrence |
| `TC-FD-07` | Timeout | One event, `solution_found: "No"` |
| `TC-FD-08` | Remount/replay | No additional event |
| `TC-FD-09` | User resubmits or creates a new snapshot | New occurrence and new event |
| `TC-FD-10` | Prohibited field in message | Field is absent; Application removes it before the push |
| `TC-FD-11` | Missing/invalid required field | No invalid message; record a QA defect |
| `TC-FD-12` | Missing optional `fx`/`fy` | Omit the missing field; do not reuse the prior event value |
| `TC-FD-13` | `fx`/`fy` wrong type or outside range | Input validation; no event |
| `TC-FD-14` | Cancellation | One `"No"` event; later callback adds nothing |
| `TC-FD-15` | UI changes after request creation | Event uses the initial snapshot, not newer state |

## 8. Approved handoff and evidence boundary

### 8.1 Expected happy-path handoff result

- The happy-path Data Layer message is assumed to have the correct envelope, complete snapshot and schema `1.0` outcome mapping.
- For a response with `length > 0`, `solution_found` is `"Yes"`; GTM is assumed to map the approved scalar allowlist to GA4.
- The full `inputs` object, internal identity/correlation, API response and prohibited fields are not mapped to GA4.
- Input validation, duplicate callback, remount/replay and late callback follow the simulated contract; no runtime execution is in scope.
- `TC-FD-01`–`TC-FD-15` is an expected matrix for Phase 5, not a test result.

### 8.2 Consumer constraints

- The Application team must implement the snapshot, correlation, validation, outcome and idempotency contract in this record.
- GTM maps only the approved scalar allowlist from `FD-REC-07`; it does not map the `inputs` object, internal identity, request ID, retry index or API response.
- A future QA simulation uses `TC-FD-01`–`TC-FD-15`; the current project must not report runtime Pass/Fail results.

### 8.3 Evidence boundary

Payloads, response/error examples and the expected QA matrix are simulated/documented values. There is no Application log, runtime DevTools evidence, live GTM/GA4 configuration or production evidence. Code, runtime artifacts, test results and implementation state are outside this record.

## 9. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove Application implementation or runtime collection.

- [x] Event envelope, snapshot boundary, outcome rules and idempotency rules are documented.
- [x] Approved scalar fields and prohibited fields are separated.
- [x] Missing, invalid, stale, duplicate and retry behavior is defined.
- [x] The handoff to GTM is traceable to `FD-REC-07`.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
