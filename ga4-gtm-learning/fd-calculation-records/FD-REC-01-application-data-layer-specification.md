# FD-REC-01 — Application/Data Layer Handoff Record

> This record applies the structure from [Section 01 — Data Layer Design](../01-data-layer-design-answer.md) to the FD journey. It is a simulated happy-path handoff: the Application is assumed to retain a complete internal snapshot and push only its approved analytics subset to the Data Layer for GTM. It does not connect to Application source code and is not QA or production evidence.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-01` |
| Record name | Application/Data Layer Handoff Record |
| Document type | `PROJECT RECORD` |
| Record version | `0.7-management-ready` — `FD-CR-002` simulation hardening |
| Phase | Phase 2 — Data Layer contract and handoff |
| Product / journey | FD web application / `J-FD-CALC-001` |
| Source of truth | [FD-REC-07 — Measurement Plan & Event Contract](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for the historical outcome rename |
| Why this matters | Schema `3.0` minimizes the Data Layer, adds opaque `event_id`, removes unused analytics numerics and makes required-field behavior fail closed. |
| Execution state | Simulation only — happy-path assumption |
| Baseline dependency | [`FD-REC-00 — Phase 0 System Inventory Record`](FD-REC-00-phase-0-system-inventory.md); `FD-CR-001`; `FD-CR-002` |
| Status | **Completed — simulation documentation; `FD-CR-002` applied; runtime blocked** |
| Review state | Payload handoff, outcome rules, privacy boundary and expected QA behavior prepared at documentation level |
| Evidence boundary | Happy-path payload is a contract assumption; no code implementation or runtime evidence exists |
| Primary owner | FD Analytics/GTM Lead — simulated owner |
| Application owner | `fd-developer@strongtie.com` — simulated alias |
| Analytics/GTM reviewer | `fd-analytics-owner@strongtie.com`, `fd-gtm-implementer@strongtie.com` — simulated aliases |
| Approver / reviewer | Analytics/GTM Lead; Application owner; Privacy reviewer when the payload boundary changes |
| Dependencies | `FD-REC-00` Phase 0 baseline; `FD-REC-07` schema `3.0-simulated`; `FD-CR-001`; `FD-CR-002` |
| Open items / risks | `FD-OPEN-001`–`004`, Application implementation, GTM/GA4 configuration, QA and release evidence remain unresolved at runtime |
| Next action | Resolve the blockers, then implement this handoff and verify it through `FD-REC-08`/`FD-REC-11` |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Created / last updated | `2026-09-04` / `2026-09-07` |

### Version history

| Record version | Contract/schema | Status | Change Request | Summary |
|---|---|---|---|---|
| `0.5` | `1.0` | Version 1 historical baseline | — | `solution_found=No` for empty/error terminal outcomes |
| `0.6` | `2.0` | Historical simulation baseline | `FD-CR-001` | Rename the combined `No` outcome to `No_solution`; runtime not executed |
| `0.7` | `3.0` | Current simulation state | `FD-CR-002` | Minimize Data Layer, add opaque `event_id`, remove `fx`/`fy` from analytics and enforce required fields; runtime blocked |


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
| Required event fields | `event`, `event_id`, `event_schema_version`, `app_name`, `solution_found`, minimized analytics `inputs` |
| Optional analytics fields | None in schema `3.0`; `fx`, `fy` and the rest of the complete Calculation API snapshot remain Application-internal and are not analytics fields |
| Output mapping | Response array with `length > 0` → `solution_found: "Yes"` |
| No-output mapping | Response array is `[]` → `solution_found: "No_solution"` |
| Error mapping | API error, timeout or cancellation/stale error that terminalizes the attempt → `solution_found: "No_solution"` once |
| Input validation | UI input validation → no calculation event |
| Response correlation | Response must belong to the immutable snapshot; a late callback for a terminal attempt or old snapshot must not create another event |
| Privacy boundary | The complete Calculation API snapshot remains inside the Application/controlled QA evidence. The analytics Data Layer contains only the approved analytics subset; GTM/GA4 receives the same scalar allowlist. |
| Consent boundary | The Data Layer message is simulated at Application level; GA4 collection must follow the `analytics_storage` policy in Phase 3 |
| Schema version | `3.0` — `FD-CR-002`; combined outcome vocabulary inherited from `FD-CR-001` |
| Consumers | GTM Variables/Trigger/Tags, GA4 event, QA evidence and later reporting |

## 2. Event envelope

| Field | Type | Required | Value/rule | Source |
|---|---|---|---|---|
| `event` | string | Yes | Exactly `calculation_action` | Application Data Layer contract |
| `event_id` | string | Yes | Random opaque UUID created once per attempt; not a user/request/snapshot identifier | Application analytics adapter |
| `event_schema_version` | string | Yes | Exactly `3.0` | Application constant |
| `app_name` | string | Yes | Exactly `fd` | Application constant |
| `solution_found` | string | Yes | Only `Yes` or `No_solution` | Application outcome classifier |
| `inputs` | object | Yes | Minimized immutable analytics subset linked to the internal attempt snapshot | Application analytics adapter |

GTM must not change the type or infer a new value for `solution_found`. Correlation keys, request tokens and API response bodies, if simulated, exist only in the Application internal log.

## 3. Input snapshot schema

The complete Calculation API snapshot must preserve the input set sent to the API and must not be mutated by newer state. It stays in the Application snapshot store. The analytics adapter derives a minimized immutable subset for the Data Layer only after the attempt terminalizes. An automatic retry within the same attempt does not create a new occurrence. The lifecycle and correlation keys below are handoff requirements for the Application team. The `solution_found` value change is tracked in `FD-CR-001`; this record does not split the combined no-output/error outcome.

### 3.1 Approved tracking-contract fields

Required/optional status in this table follows the Parameter Dictionary in `FD-REC-07`.

| Path | Type | Required in complete snapshot | Rule |
|---|---|---:|---|
| `inputs.country` | string | Yes | Controlled country code, for example `gb` |
| `inputs.language` | string | Yes | Controlled language code, for example `en` |
| `inputs.building_code` | string | Yes | Controlled building-code identifier |
| `inputs.design_method` | string | Yes | Controlled design-method code |
| `inputs.connection_type` | string | Yes | Controlled connection-type code |

### 3.2 Example fields outside the tracking contract

The complete internal Calculation API snapshot may contain `fx`, `fy`, `unit_system`, `fastener_installation`, `load_duration`, thickness, grade, density, `contact_length`, `predrilled`, `fastener_angle` and `service_class`. These fields are outside the schema `3.0` analytics contract and must not be copied into the analytics Data Layer merely because they exist in the API request. Their Application validation, units and retention belong to the product/API contract.

### 3.3 Missing and invalid data behavior

- If a required envelope/contract field is missing or invalid, do not emit a message that violates the contract; record the issue for QA under `FD-REC-07`.
- `country`, `language`, `building_code`, `design_method` and `connection_type` are required analytics fields. If any is missing or invalid, do not emit the analytics message; do not omit the parameter downstream.
- If an Application input such as `fx` or `fy` is invalid under the product/API contract, apply input validation and do not open a calculation attempt. These values are not analytics fields in schema `3.0`.
- Do not infer a complete allowed-value list from one example. The controlled vocabulary registry and Application validation must be approved before runtime under `FD-OPEN-004`.
- Prohibited fields must not appear in the analytics message. The Application removes them before the push; GTM maps only approved scalars and never sends the full `inputs` object to GA4.

GA4 maps only these approved scalars: `event_id`, `app_name`, `event_schema_version`, `solution_found`, `country`, `language`, `building_code`, `design_method` and `connection_type`. There is no GA4 parameter named `inputs`. `event_id` is not registered as a custom dimension and is retained only under the future approved privacy/evidence policy.

### 3.4 Identity and correlation model

The simulated Application uses three internal correlation identities plus one analytics occurrence ID. Internal identities support state storage, request logs and review notes only; they must not be placed in the Data Layer message for GTM/GA4.

| Identity | Scope | Creation and usage rule |
|---|---|---|
| `attempt_id` | One business occurrence | Created after input validation passes. An attempt may have transport retries but only one terminal outcome and at most one event. |
| `snapshot_id` | One immutable input snapshot | Created with the attempt. One attempt has one snapshot; retries reuse it. A new valid input creates a new snapshot and attempt. |
| `request_id` | One transport request | Each initial request and retry gets a new request ID. The request record references `attempt_id`, `snapshot_id` and `retry_index`. |
| `event_id` | One analytics occurrence | Random opaque UUID created with the attempt and reused across Application evidence, the one Data Layer message and the one GA4 request. It must not encode a user, account, snapshot or request ID and must not be registered as a GA4 custom dimension. |

Minimum Application correlation registry:

```javascript
{
  request_id,
  attempt_id,
  snapshot_id,
  event_id,
  snapshot,
  retry_index,
  status: "in_flight" | "retryable_error" | "terminal",
  event_emitted: false,
}
```

Correlation is valid when `request_id` exists in the registry, the request belongs to a non-terminal attempt and the response references the same `snapshot_id`. An unknown request, terminal attempt or snapshot mismatch is invalid; the Application ignores it and does not push the Data Layer. A retryable transport error moves the request to `retryable_error` so a new request can reuse the same attempt/snapshot/event ID. When retry policy ends or the error is non-retryable, the attempt terminalizes and emits `"No_solution"` once under the current combined contract. This is a management requirement, not implemented code.

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
  → push one complete contract-valid, minimized calculation_action message
```

### 4.1 Terminal outcome rules

Data Layer actions follow `FD-REC-07`; Attempt state is a management label for this simulation.

| Situation | Attempt state | Data Layer action |
|---|---|---|
| Response array `length > 0` | `terminal: output` | Push one event with `solution_found: "Yes"` |
| Response `[]` | `terminal: no_output` | Push one event with `solution_found: "No_solution"` |
| API error | `terminal: error` | Push one event with `solution_found: "No_solution"` |
| Timeout | `terminal: error` | Push one event with `solution_found: "No_solution"` |
| Cancellation/stale error before terminal state | `terminal: error` | Push one event with `solution_found: "No_solution"` once |
| Late response after terminal state | `ignored` | Do not push an event |
| UI input validation | `rejected_before_request` | Do not push an event |

#### 4.1.1 Response/error examples and mapping

| Example ID | Simulated API result | Retry policy | Terminal outcome | Data Layer result |
|---|---|---|---|---|
| `EX-FD-OUTPUT-001` | Response array contains one solution | No retry | `output` | One event, `solution_found: "Yes"` |
| `EX-FD-EMPTY-001` | Response is `[]` | No retry | `no_output` | One event, `solution_found: "No_solution"` |
| `EX-FD-HTTP-503-001` | HTTP/server failure | Non-retryable in this example | `error` | One event, `solution_found: "No_solution"` |
| `EX-FD-NETWORK-RETRY-001` | Network error first, then output | Retry at most once; keep attempt/snapshot | `output` after retry | One event only, `solution_found: "Yes"` |
| `EX-FD-TIMEOUT-001` | Request timeout | Terminal error | `error` | One event, `solution_found: "No_solution"` |
| `EX-FD-CANCEL-001` | Application cancellation/stale | Terminal error | `error` | One event, `solution_found: "No_solution"` |

A retryable error is an intermediate transport state and does not create an event. When retry policy ends or an error is non-retryable, the attempt terminalizes and emits `"No_solution"` once. These rows are review examples, not runtime evidence.

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
| `terminal_no_output` | Response is `[]` | Emit `"No_solution"` once |
| `terminal_error` | Final API error, timeout, cancellation or stale error | Emit `"No_solution"` once |
| `event_emitted` | Attempt is locked after the Data Layer push | Ignore all later callbacks |
| `ignored_callback` | Old, unknown or mismatched callback | No state change and no push |

When new valid input starts while an old attempt is active, the old attempt becomes a stale error and emits `"No_solution"` once if it has not emitted under the current combined contract. The new attempt receives different IDs. A late callback for the old attempt is ignored and cannot update the new attempt or create another event. Internal IDs never appear in the GTM/GA4 Data Layer payload; only the opaque analytics `event_id` is allowed. Whether stale/cancelled attempts should remain analytics outcomes must be resolved under `FD-OPEN-001` before runtime.

## 5. Happy-path Data Layer payload assumption

This is a simulated payload shape assumed to have been pushed by the Application. It contains only the minimized analytics subset derived from the complete internal Calculation API snapshot. GTM maps the approved scalar fields to GA4; the `inputs` object itself is not sent as a GA4 parameter.

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_id: "9bdbe18f-68af-4b6a-ae04-00f3f84406c1", // simulated opaque occurrence ID
  event_schema_version: "3.0",
  app_name: "fd",
  solution_found: "Yes", // response.length > 0; use "No_solution" for [] or an error
  inputs: {
    country: "gb",
    language: "en",
    building_code: "en_1995_1_1_2004_a2_2014",
    design_method: "lsd",
    connection_type: "clt_floor_floor_half_lap_joint",
  },
});
```

All required analytics fields must be in the same Application push. Do not complete the current message with values left over from a previous event. The complete API snapshot, internal identities and API result are not part of this message.

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

The matrix retains `TC-FD-01`–`TC-FD-06` from Phase 0 and adds negative/edge cases through `TC-FD-15`. Expected outcome values reflect schema `3.0` under `FD-CR-002`; the current project simulates only the happy path and has no runtime result.

| Test ID | Scenario | Expected result |
|---|---|---|
| `TC-FD-01` | Response has output | One event, `solution_found: "Yes"` |
| `TC-FD-02` | Response is `[]` | One event, `solution_found: "No_solution"` |
| `TC-FD-03` | UI input validation | No event |
| `TC-FD-04` | Server failure: HTTP 5xx/network error | One event, `solution_found: "No_solution"` |
| `TC-FD-05` | Stale response / attempt terminalized by stale or cancellation | One `"No_solution"` event for the attempt; late callback adds nothing |
| `TC-FD-06` | Retry/duplicate callback | One event for the occurrence |
| `TC-FD-07` | Timeout | One event, `solution_found: "No_solution"` |
| `TC-FD-08` | Remount/replay | No additional event |
| `TC-FD-09` | User resubmits or creates a new snapshot | New occurrence and new event |
| `TC-FD-10` | Prohibited field in message | Field is absent; Application removes it before the push |
| `TC-FD-11` | Missing/invalid required field | No invalid message; record a QA defect |
| `TC-FD-12` | Optional internal Application input absent | Follow the product/API contract; no invented analytics parameter and no stale value |
| `TC-FD-13` | Internal Application input has wrong type/range | Input validation; no attempt and no analytics event |
| `TC-FD-14` | Cancellation | One `"No_solution"` event; later callback adds nothing |
| `TC-FD-15` | UI changes after request creation | Event uses the initial snapshot, not newer state |

## 8. Approved handoff and evidence boundary

### 8.1 Expected happy-path handoff result

- The happy-path Data Layer message is assumed to have the correct envelope, minimized analytics subset, opaque `event_id` and schema `3.0` outcome mapping.
- For a response with `length > 0`, `solution_found` is `"Yes"`; for an empty/error terminal outcome it is `"No_solution"`; GTM is assumed to map the approved scalar allowlist to GA4.
- The full `inputs` object, internal identity/correlation, API response and prohibited fields are not mapped to GA4.
- Input validation, duplicate callback, remount/replay and late callback follow the simulated contract; no runtime execution is in scope.
- `TC-FD-01`–`TC-FD-15` is an expected matrix for Phase 5, not a test result.

### 8.2 Consumer constraints

- The Application team must implement the snapshot, correlation, validation, outcome and idempotency contract in this record.
- GTM maps only the approved scalar allowlist from `FD-REC-07`; it does not map the `inputs` object, internal identity, request ID, retry index or API response.
- A future QA run uses the full `TC-FD-01`–`TC-FD-25` matrix in `FD-REC-08`; this Application record owns the subset `TC-FD-01`–`TC-FD-15` and must not report simulated Pass/Fail results.

### 8.3 Evidence boundary

Payloads, response/error examples and the expected QA matrix are simulated/documented values. There is no Application log, runtime DevTools evidence, live GTM/GA4 configuration or production evidence. Code, runtime artifacts, test results and implementation state are outside this record.

## 9. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove Application implementation or runtime collection.

- [x] Event envelope, snapshot boundary, outcome rules and idempotency rules are documented.
- [x] Approved scalar fields and prohibited fields are separated.
- [x] Missing, invalid, stale, duplicate and retry behavior is defined.
- [x] The handoff to GTM is traceable to `FD-REC-07`.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
