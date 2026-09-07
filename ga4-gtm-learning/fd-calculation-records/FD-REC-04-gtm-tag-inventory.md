# FD-REC-04 — GTM Tag Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines the Google tag and GA4 Event tag for `calculation_action`; it does not create or publish a live Tag.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-04` |
| Record name | GTM Tag Inventory |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — GTM Tags and routing |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for the historical outcome rename |
| Why this matters | Schema `3.0` adds `event_id`, minimizes the payload, removes unused numeric fields and requires strict field validation. |
| Variable dependency | [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Trigger dependency | [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md) |
| Status | **Approved — simulation documentation; `FD-CR-002` applied; runtime blocked** |
| Value/evidence boundary | Tag configuration and mapping are simulated; no request or destination evidence exists |
| Owner / reviewer | FD GTM owner / Analytics owner — simulated aliases |
| Open items | `FD-OPEN-001`–`004`, actual-baseline verification, live GTM configuration, Network, DebugView and the selected schema `3.0` rollout remain unresolved at runtime |
| Next action | Resolve the runtime blockers, then configure and verify the schema `3.0` Tag through `FD-REC-08`/`FD-REC-11` |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` — `FD-CR-002` |

## 0.1 Source of the record format

This is a project record using the standard Section 04 Tag Inventory structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Tag name, type, Trigger, destination and count | [Section 04 — Tag Management](../04-tag-management-answer.md) |
| Parameter source, type and missing behavior | [Section 04 — Tag parameter allowlist](../04-tag-management-answer.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Approved event parameters and Data Layer paths | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) |
| Consent and destination decisions | [`FD-REC-05`](FD-REC-05-consent-decision.md), [`FD-REC-00`](FD-REC-00-phase-0-system-inventory.md) |

## 1. Approved Tag inventory

| Tag name | Type | Trigger | Destination | Consent | Expected count | Status |
|---|---|---|---|---|---:|---|
| `FD - Google tag - Primary` | Google tag | `FD - INIT - Google tag - Allowed Host` | Hostname lookup → QA or production Measurement ID | Built-in approved analytics behavior | One setup/configuration per page | Simulated |
| `FD - GA4 Event - calculation_action` | GA4 Event tag | `FD - CE - calculation_action - Approved` | Same Google tag destination | `analytics_storage=granted` required; denied/unknown blocks | One request per valid occurrence | Simulated |

## 2. GA4 Event parameter mapping

| GA4 parameter | Source Variable | Type | Required/optional | Missing behavior |
|---|---|---|---|---|
| `event_id` | `FD - DLV - event_id` | string | Required | Block unless it is a valid opaque UUID; do not register as a custom dimension |
| `event_schema_version` | `FD - DLV - event_schema_version` | string | Required | Send only `3.0`; otherwise do not send and record a Trigger/QA failure |
| `app_name` | `FD - DLV - app_name` | string | Required | Do not send; Trigger/QA failure |
| `solution_found` | `FD - DLV - solution_found` | string | Required | Allow only `Yes`/`No_solution`; otherwise block/QA failure |
| `country` | `FD - DLV - inputs - country` | string | Required | Block/QA failure when missing or invalid |
| `language` | `FD - DLV - inputs - language` | string | Required | Block/QA failure when missing or invalid |
| `building_code` | `FD - DLV - inputs - building_code` | string | Required | Block/QA failure when missing or invalid |
| `design_method` | `FD - DLV - inputs - design_method` | string | Required | Block/QA failure when missing or invalid |
| `connection_type` | `FD - DLV - inputs - connection_type` | string | Required | Block/QA failure when missing or invalid |

The nested `inputs` object, complete API snapshot, API response and internal request/snapshot/attempt tokens are not sent to GA4. Only the nine approved scalar parameters above are mapped.

## 3. Routing, consent and duplicate control

- The Google tag receives a Measurement ID only from the approved hostname lookup.
- Unknown hostnames return blank/blocked; production is never the default.
- The event Tag uses one authoritative Trigger and no alternate click/page path.
- Consent uses the approved Consent Mode/built-in behavior; no custom bypass parameter is used.
- The event Tag does not calculate `solution_found` or transform the business outcome. It maps the schema `3.0` Application value supplied by the Variable; the historical `No` → `No_solution` decision belongs to `FD-REC-01`/`FD-REC-07` under `FD-CR-001`.
- When consent permits collection, a future runtime run must reconcile one Application occurrence → one Data Layer `event_id` → one Tag fire → one Network request with the same `event_id`; denied/unknown scenarios must reconcile to zero Tag requests.

### 3.1 Tag governance and lifecycle

| Control | Google tag | GA4 Event tag |
|---|---|---|
| Consumer | GA4 configuration and all approved GA4 Event tags in the FD container | `FD-REP-001`, `FD-EXP-001`, QA and monitoring records |
| Environment | Hostname lookup; QA/prod explicit; unknown blocked | Inherits the approved Google-tag destination and Trigger gate |
| Sequencing | Initialization only; not used to reconstruct the Application workflow | No sequencing required |
| Owner/status | FD GTM owner / simulated, runtime blocked | FD GTM owner / simulated, runtime blocked |
| Review/retirement | Review on destination, consent or consumer change; retain a recoverable version before retirement | Deprecate only after reports/tests/consumers migrate and the replacement is monitored |

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live Tag firing or Network delivery.

- [x] Built-in Google tag and GA4 Event tag satisfy the approved GA4 requirement.
- [x] Every parameter maps to a canonical Variable in `FD-REC-02`.
- [x] One authoritative Trigger is used.
- [x] Consent and destination behavior are explicit.
- [x] Every schema `3.0` analytics parameter is required; missing or invalid values fail closed and no stale fallback is used.
- [x] No custom template is required for the GA4 path.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
