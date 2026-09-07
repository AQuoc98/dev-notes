# FD-REC-02 — GTM Variable Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines the Variables required for `calculation_action`; it does not create or edit Variables in a live GTM container.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-02` |
| Record name | GTM Variable Inventory |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — GTM Variables |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `1.0` |
| Application handoff | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) |
| Status | **Completed — simulation documentation** |
| Value/evidence boundary | Names, paths and expected values are simulated; no live GTM configuration exists |
| Owner / reviewer | FD GTM owner / Analytics owner — simulated aliases |
| Open items | Live container verification is outside the current scope |
| Next action | Use this inventory as input for a future authorized runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` |

## 0.1 Source of the record format

This is a project record using the standard Section 02 Variable Inventory structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Variable contract, naming and review fields | [Section 02 — Variable Management](../02-variable-management-answer.md) |
| Nested Data Layer paths and missing-data behavior | [Section 01 — Data Layer Design](../01-data-layer-design-answer.md), [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) |
| Approved parameter names and types | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Simulated values in this record | FD calculation flow and approved schema `1.0` |

## 1. Approved Variable inventory

| Variable name | GTM type | Data Layer path / source | Type | Required | Missing/invalid behavior | Consumers |
|---|---|---|---|---|---|---|
| `FD - DLV - event_schema_version` | Data Layer Variable v2 | `event_schema_version` | string | Yes | Block the event and record a QA defect unless it is `1.0` | Trigger, GA4 Event tag |
| `FD - DLV - app_name` | Data Layer Variable v2 | `app_name` | string | Yes | Block the event and record a QA defect unless it is `fd` | Trigger, GA4 Event tag |
| `FD - DLV - solution_found` | Data Layer Variable v2 | `solution_found` | string | Yes | Allow only `Yes` or `No`; otherwise block | GA4 Event tag |
| `FD - DLV - inputs - country` | Data Layer Variable v2 | `inputs.country` | string | Approved | Omit when absent; never reuse a previous value | GA4 Event tag |
| `FD - DLV - inputs - language` | Data Layer Variable v2 | `inputs.language` | string | Approved | Omit when absent; never reuse a previous value | GA4 Event tag |
| `FD - DLV - inputs - building_code` | Data Layer Variable v2 | `inputs.building_code` | string | Approved | Omit when absent; never reuse a previous value | GA4 Event tag |
| `FD - DLV - inputs - design_method` | Data Layer Variable v2 | `inputs.design_method` | string | Approved | Omit when absent; never reuse a previous value | GA4 Event tag |
| `FD - DLV - inputs - connection_type` | Data Layer Variable v2 | `inputs.connection_type` | string | Approved | Omit when absent; never reuse a previous value | GA4 Event tag |
| `FD - DLV - inputs - fx` | Data Layer Variable v2 | `inputs.fx` | number | Optional | Omit when absent or invalid; do not use a fallback | GA4 Event tag |
| `FD - DLV - inputs - fy` | Data Layer Variable v2 | `inputs.fy` | number | Optional | Omit when absent or invalid; do not use a fallback | GA4 Event tag |
| `FD - LUT - Hostname to Measurement ID` | Lookup Table or equivalent | Current hostname → approved destination | string | Yes for routing | Blank/block for an unknown hostname; never default to production | Google tag, GA4 Event tag gate |

The `event` value itself is the GTM Custom Event signal `calculation_action`; it is not recreated by a Variable.

## 2. Design decisions

1. Read business values from the Application-owned Data Layer. Do not scrape DOM text or infer the calculation result in GTM.
2. Use Data Layer Variable Version 2 for nested paths under `inputs`.
3. Keep each Variable one-to-one with an approved field. Do not add hidden normalization, fallback or business rules.
4. Treat `fx` and `fy` as optional numeric parameters and omit them when absent or invalid.
5. Return a Measurement ID only for a known approved hostname. Unknown hostnames fail closed.
6. Never expose PII, credentials, secrets, raw API responses or internal request tokens through a Variable.

## 3. Simulated handoff

```text
Application pushes one complete calculation_action message
  → Data Layer Variables read approved scalar fields
  → Trigger checks event/schema/app/routing conditions
  → GA4 Event tag maps the same Variables once
```

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live GTM configuration.

- [x] Every Variable maps to a field approved in `FD-REC-07`.
- [x] Nested paths use Data Layer Variable Version 2.
- [x] Required and optional missing-data behavior is explicit.
- [x] No Variable calculates `solution_found` or reconstructs the Application snapshot.
- [x] Unknown hostnames have no production fallback.
- [x] Consumers are listed before a future reuse or change.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.

## 5. Cross-references

- Section 01 / [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md): Data Layer envelope and snapshot boundary.
- Section 03 / [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md): authoritative Trigger and routing filters.
- Section 04 / [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md): GA4 Tag parameter mapping.
- Section 05 / [`FD-REC-05`](FD-REC-05-consent-decision.md): consent is permission, not a Variable fallback.
- Section 08: runtime evidence belongs to a separately authorized project.
