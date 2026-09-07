# FD-REC-03 — GTM Trigger Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines Trigger conditions for `calculation_action`; it does not run GTM Preview or publish a live container.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-03` |
| Record name | GTM Trigger Inventory |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — GTM Triggers |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Variable dependency | [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Status | **Completed — simulation documentation** |
| Value/evidence boundary | Trigger names, filters and expected counts are simulated; no live evaluation exists |
| Owner / reviewer | FD GTM owner / Analytics owner — simulated aliases |
| Open items | Preview, Network and duplicate audits are outside the current scope |
| Next action | Use this inventory as input for a future authorized runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` |

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
| `FD - CE - calculation_action - Approved` | Custom Event | `calculation_action` | `app_name=fd`; `event_schema_version=1.0`; routing Variable is not blank | `FD - GA4 Event - calculation_action` | One match per valid Application occurrence |

## 2. Authoritative Trigger decision

`FD - CE - calculation_action - Approved` is the only Trigger allowed to send the FD calculation event. It listens to the Application Custom Event and checks only the approved schema, application and routing fields.

Do not add click, page-view, DOM, request-start, render or Window Loaded rules for the same business fact. Those signals cannot prove that the API response belongs to the correct snapshot or that `solution_found` was finalized.

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

## 4. Simulated evaluation

```text
event = calculation_action
AND app_name = fd
AND event_schema_version = 1.0
AND FD - LUT - Hostname to Measurement ID is not blank
→ Trigger matches once
→ one GA4 Event tag becomes eligible
```

## 5. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live Trigger evaluation.

- [x] One authoritative Custom Event Trigger is defined.
- [x] Conditions use approved application and schema fields only.
- [x] No broad click/page/DOM rule duplicates the business event.
- [x] Trigger Group and sequencing are explicitly not needed.
- [x] Unknown hostnames fail closed.
- [x] Expected frequency is one match per valid occurrence.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.

## 6. Cross-references

- Section 02 / [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md): Variable paths and missing-data behavior.
- Section 04 / [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md): Tag consumers and consent settings.
- Section 05 / [`FD-REC-05`](FD-REC-05-consent-decision.md): consent permission remains separate from Trigger logic.
- Section 08: future runtime verification requires Trigger-match evidence and request-count reconciliation.
