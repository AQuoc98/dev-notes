# FD-REC-03 — GTM Trigger Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines Trigger conditions for `calculation_action`; it does not run GTM Preview or publish a live container.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-03` |
| Record name | GTM Trigger Inventory |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — GTM Triggers |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for the historical outcome rename |
| Why this matters | The current Trigger admits only a complete schema `3.0` message with `event_id` and all required analytics fields. |
| Variable dependency | [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Status | **Approved — simulation documentation; `FD-CR-002` applied; runtime blocked** |
| Value/evidence boundary | Trigger names, filters and expected counts are simulated; no live evaluation exists |
| Owner / reviewer | FD GTM owner / Analytics owner — simulated aliases |
| Open items | `FD-OPEN-001`–`004`, Preview, Network, duplicate audits, actual-baseline verification and the selected schema `3.0` rollout remain unresolved at runtime |
| Next action | Resolve the runtime blockers, then use this inventory in the coordinated compatibility release from `FD-REC-10` |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` — `FD-CR-002` |

## 0.1 Source of the record format

This is a project record using the standard Section 03 Trigger Inventory structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Trigger naming, authoritative-event rule and timing | [Section 03 — Trigger Management](../03-trigger-management-answer.md) |
| Variable dependencies and filters | [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Business moment, occurrence and event contract | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) |
| Consent and permission boundary | [Section 05 — Consent Management](../05-consent-answer.md), [`FD-REC-05`](FD-REC-05-consent-decision.md) |

## 1. Approved Trigger inventory

| Trigger name | Type | Event/source | Conditions | Consumers | Expected behavior |
|---|---|---|---|---|---|
| `FD - INIT - Google tag - Allowed Host` | Initialization / approved page setup | Page initialization | Hostname maps to a non-empty approved Measurement ID | `FD - Google tag - Primary` | One shared Google tag configuration per page/environment |
| `FD - CE - calculation_action - Approved` | Custom Event | `calculation_action` | `app_name=fd`; `event_schema_version=3.0`; opaque `event_id` present; `solution_found` is `Yes`/`No_solution`; all five required categorical fields present and valid; routing Variable is not blank | `FD - GA4 Event - calculation_action` | One match per valid Application occurrence; malformed messages fail closed |

## 2. Authoritative Trigger decision

`FD - CE - calculation_action - Approved` is the only final-state Trigger allowed to send the FD calculation event. It listens to the Application Custom Event and checks the approved schema, application, occurrence ID, outcome, required-field and routing conditions. These are contract-validity checks, not business-outcome calculation in GTM.

Do not add click, page-view, DOM, request-start, render or Window Loaded rules for the same business fact. Those signals cannot prove that the API response belongs to the correct snapshot or that `solution_found` was finalized. `FD-CR-001` changed the outcome vocabulary and `FD-CR-002` hardens admission; neither moves the authoritative business logic into GTM.

## 3. Trigger controls

| Control | Decision |
|---|---|
| Trigger Group | Not required. The Application event is already the complete business signal. |
| Tag sequencing | Not required for the event Tag. Google tag setup is independent from the API workflow. |
| Consent | Controlled by the Tag's approved consent behavior; no consent bypass. |
| Exception Trigger | No general consent exception. Unknown hostnames are blocked by routing and Trigger conditions. |
| Timing | Use the earliest point where the complete Data Layer message exists: the Application terminal outcome. |
| Frequency | One Trigger match per accepted calculation occurrence. |
| Unknown event/schema | No match; record a simulated QA failure if observed in a future run. |
| Missing/malformed required field | No match; Tag does not fire; open a contract defect. |
| Migration window | A temporary reviewed compatibility version may accept only the documented schema `2.0` legacy contract and schema `3.0` hardened contract during the coordinated rollout in `FD-REC-10`; retire it after v3 stabilization. |

## 4. Simulated evaluation

```text
event = calculation_action
AND app_name = fd
AND event_schema_version = 3.0
AND event_id is a non-empty opaque UUID
AND solution_found matches ^(?:Yes|No_solution)$
AND country, language, building_code, design_method and connection_type are present and valid
AND FD - LUT - Hostname to Measurement ID is not blank
→ Trigger matches once
→ one GA4 Event tag becomes eligible
```

## 5. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live Trigger evaluation.

- [x] One authoritative Custom Event Trigger is defined.
- [x] Conditions use approved application and schema fields only.
- [x] Missing or malformed required fields fail closed before Tag execution.
- [x] No broad click/page/DOM rule duplicates the business event.
- [x] Trigger Group and sequencing are explicitly not needed.
- [x] Unknown hostnames fail closed.
- [x] Expected frequency is one match per valid occurrence.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
