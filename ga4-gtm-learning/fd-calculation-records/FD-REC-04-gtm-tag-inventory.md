# FD-REC-04 — GTM Tag Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines the Google tag and GA4 Event tag for `calculation_action`; it does not create or publish a live Tag.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-04` |
| Record name | GTM Tag Inventory |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — GTM Tags and routing |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Variable dependency | [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md) |
| Trigger dependency | [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md) |
| Status | **Completed — simulation documentation** |
| Value/evidence boundary | Tag configuration and mapping are simulated; no request or destination evidence exists |
| Owner / reviewer | FD GTM owner / Analytics owner — simulated aliases |
| Open items | Live GTM configuration, Network and DebugView checks are outside the current scope |
| Next action | Use this inventory as input for a future authorized runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` |

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
| `event_schema_version` | `FD - DLV - event_schema_version` | string | Required | Do not send; Trigger/QA failure |
| `app_name` | `FD - DLV - app_name` | string | Required | Do not send; Trigger/QA failure |
| `solution_found` | `FD - DLV - solution_found` | string | Required | Allow only `Yes`/`No`; otherwise block/QA failure |
| `country` | `FD - DLV - inputs - country` | string | Approved | Omit when absent |
| `language` | `FD - DLV - inputs - language` | string | Approved | Omit when absent |
| `building_code` | `FD - DLV - inputs - building_code` | string | Approved | Omit when absent |
| `design_method` | `FD - DLV - inputs - design_method` | string | Approved | Omit when absent |
| `connection_type` | `FD - DLV - inputs - connection_type` | string | Approved | Omit when absent |
| `fx` | `FD - DLV - inputs - fx` | number | Optional | Omit when absent or invalid |
| `fy` | `FD - DLV - inputs - fy` | number | Optional | Omit when absent or invalid |

The nested `inputs` object, API response, request token and full snapshot are not sent to GA4.

## 3. Routing, consent and duplicate control

- The Google tag receives a Measurement ID only from the approved hostname lookup.
- Unknown hostnames return blank/blocked; production is never the default.
- The event Tag uses one authoritative Trigger and no alternate click/page path.
- Consent uses the approved Consent Mode/built-in behavior; no custom bypass parameter is used.
- The event Tag does not calculate `solution_found` or transform the business outcome.
- A future runtime run must reconcile one Application occurrence → one Tag fire → one Network request.

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live Tag firing or Network delivery.

- [x] Built-in Google tag and GA4 Event tag satisfy the approved GA4 requirement.
- [x] Every parameter maps to a canonical Variable in `FD-REC-02`.
- [x] One authoritative Trigger is used.
- [x] Consent and destination behavior are explicit.
- [x] Optional fields omit safely; no stale fallback values are used.
- [x] No custom template is required for the GA4 path.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.

## 5. Cross-references

- Section 03 / [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md): authoritative Trigger and expected frequency.
- Section 05 / [`FD-REC-05`](FD-REC-05-consent-decision.md): consent initialization and denied-state behavior.
- Section 06 / [`FD-REC-06`](FD-REC-06-template-governance-decision.md): native Tag decision and custom-template exception boundary.
- Section 08: future runtime evidence must verify Tag evaluation, request count and destination.
