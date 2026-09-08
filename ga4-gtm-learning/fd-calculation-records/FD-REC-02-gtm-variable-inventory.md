# FD-REC-02 — GTM Variable Inventory

> **SIMULATED — Phase 3 documentation only.** This record defines the Variables required for `calculation_action`; it does not create or edit Variables in a live GTM container.

## 0. Record metadata

| Field                   | Value                                                                                                                             |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Record ID               | `FD-REC-02`                                                                                                                       |
| Record name             | GTM Variable Inventory                                                                                                            |
| Document type           | `PROJECT RECORD`                                                                                                                  |
| Record revision         | `r3` — runtime-readiness hardening                                                                                                  |
| Phase                   | Phase 3 — GTM Variables                                                                                                           |
| Source of truth         | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`                                                         |
| Change reading status   | **Required — affected**                                                                                                           |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for history |
| Why this matters        | Schema `3.0` adds opaque `event_id`, removes unused numeric analytics fields and makes required-field behavior fail closed.        |
| Application handoff     | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md)                                                                  |
| Status                  | **Completed — simulation documentation; current state after [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md); runtime blocked** |
| Value/evidence boundary | Names, paths and expected values are simulated; no live GTM configuration exists                                                  |
| Owner / reviewer        | FD GTM owner / Analytics owner — simulated aliases                                                                                |
| Open items              | Live container verification, controlled vocabulary approval, actual-baseline verification and the selected schema `3.0` rollout are outside the current scope |
| Next action             | Use this schema `3.0` inventory after `FD-OPEN-001`–`004` are resolved; retain schemas `1.0`/`2.0` as history                    |
| Last updated            | `2026-09-07` — [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md)                                                           |


### Version history

> Keep this table generic. `Record revision` identifies the revision of this inventory document; `Schema/contract` identifies the Application/Data Layer contract consumed by GTM. Do not create a new record file for every field change.

| Record revision | Schema/contract | Effective release | Change type | Change summary | Change Request | Status |
|---|---|---|---|---|---|---|
| `r1` | Schema `1.0` | Pre-change baseline | Initial | Initial Variable inventory; `solution_found` allowed `Yes`/`No` | — | Historical |
| `r2` | Schema `2.0` | `REL-FD-CALC-002` — simulated | Modify | Update schema filter to `2.0` and rename `No` to `No_solution` | [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) | Historical simulation baseline |
| `r3` | Schema `3.0` | `REL-FD-CALC-003` — simulated | Modify | Add `event_id`, remove `fx`/`fy` analytics Variables and enforce required-field blocking | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) | Current simulation state; runtime blocked |

### Variable/field change detail

> Add one row for each affected Variable or contract field. This makes a future field addition, type change, required/optional change or deprecation traceable without hard-coding one field into the version-history header.

| Schema/contract | Change type | Affected Variable / field | Before | After | Compatibility / migration note | Change Request |
|---|---|---|---|---|---|---|
| `2.0` | Modify | `FD - DLV - event_schema_version` | `1.0` | `2.0` | Schema-filter consumers must migrate together; retain `1.0` as historical context | [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) |
| `2.0` | Modify | `FD - DLV - solution_found` | `Yes` / `No` | `Yes` / `No_solution` | Pre-release `No` and post-release `No_solution` must remain separately interpretable | [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) |
| `3.0` | Modify | `FD - DLV - event_schema_version` | `2.0` | `3.0` | Select the release path from runtime evidence; use compatibility only when live v2 is verified | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) |
| `3.0` | Add | `FD - DLV - event_id` | Not present | Required opaque UUID | Do not register as a custom dimension or derive from user/internal IDs | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) |
| `3.0` | Retire | `FD - DLV - inputs - fx`, `FD - DLV - inputs - fy` | Optional analytics Variables | Application/API only | Remove consumers; future analytics use requires a new CR | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) |
| `3.0` | Modify | Five categorical Variables | Downstream omission allowed | Required; missing/invalid blocks | Align Variable, Trigger and Tag behavior with the event contract | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) |

#### Future field-addition example

The following is an example only; it is not an active change in this simulation. In that case, the next inventory revision would be `r4` and the contract could become schema `3.1` after a separate approved Change Request.

| Schema/contract | Change type | Affected Variable / field | Before | After | Compatibility / migration note | Change Request |
|---|---|---|---|---|---|---|
| `3.1` | Add | `FD - DLV - solution_reason` | Not present | Optional string from `solution_reason` | Backward-compatible only if omitted schema `3.0` payloads remain valid; update contract, Tag mapping, QA and reporting as applicable | `[future CR — required]` |


## 1. Approved Variable inventory

| Variable name                           | GTM type                   | Data Layer path / source                | Type   | Required        | Missing/invalid behavior                                         | Consumers                      | Introduced in | Last changed in |
| --------------------------------------- | -------------------------- | --------------------------------------- | ------ | --------------- | ---------------------------------------------------------------- | ------------------------------ | -------------- | --------------- |
| `FD - DLV - event_schema_version`       | Data Layer Variable v2     | `event_schema_version`                  | string | Yes             | Block the event and record a QA defect unless it is `3.0`        | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - DLV - event_id`                   | Data Layer Variable v2     | `event_id`                              | string | Yes             | Block unless it is a non-empty opaque UUID; never derive it in GTM | Trigger, GA4 Event tag, QA reconciliation | `r3` | `r3` |
| `FD - DLV - app_name`                   | Data Layer Variable v2     | `app_name`                              | string | Yes             | Block the event and record a QA defect unless it is `fd`         | Trigger, GA4 Event tag         | `r1`           | `r1`            |
| `FD - DLV - solution_found`             | Data Layer Variable v2     | `solution_found`                        | string | Yes             | Allow only `Yes` or `No_solution`; otherwise block               | GA4 Event tag                  | `r1`           | `r2`            |
| `FD - DLV - inputs - country`           | Data Layer Variable v2     | `inputs.country`                        | string | Yes             | Block when missing/invalid; never reuse a previous value         | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - DLV - inputs - language`          | Data Layer Variable v2     | `inputs.language`                       | string | Yes             | Block when missing/invalid; never reuse a previous value         | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - DLV - inputs - building_code`     | Data Layer Variable v2     | `inputs.building_code`                  | string | Yes             | Block when missing/invalid; never reuse a previous value         | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - DLV - inputs - design_method`     | Data Layer Variable v2     | `inputs.design_method`                  | string | Yes             | Block when missing/invalid; never reuse a previous value         | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - DLV - inputs - connection_type`   | Data Layer Variable v2     | `inputs.connection_type`                | string | Yes             | Block when missing/invalid; never reuse a previous value         | Trigger, GA4 Event tag         | `r1`           | `r3`            |
| `FD - LUT - Hostname to Measurement ID` | Lookup Table or equivalent | Current hostname → approved destination | string | Yes for routing | Blank/block for an unknown hostname; never default to production | Google tag, GA4 Event tag gate | `r1`           | `r1`            |

The `event` value itself is the GTM Custom Event signal `calculation_action`; it is not recreated by a Variable.

## 2. Design decisions

1. Read business values from the Application-owned Data Layer. Do not scrape DOM text or infer the calculation result in GTM. `FD-CR-001` changed the historical outcome value and `FD-CR-002` hardens the current payload; neither moves business logic into GTM.
2. Use Data Layer Variable Version 2 for nested paths under `inputs`.
3. Keep each Variable one-to-one with an approved field. Do not add hidden normalization, fallback or business rules.
4. Treat every listed categorical analytics field as required. Missing or invalid values fail closed; they are not silently omitted by the Tag.
5. Return a Measurement ID only for a known approved hostname. Unknown hostnames fail closed.
6. Never expose PII, credentials, secrets, raw API responses or internal request tokens through a Variable.
7. Use `event_id` only as an opaque occurrence ID for cross-layer QA and duplicate reconciliation. Do not expose `attempt_id`, `snapshot_id` or `request_id`, and do not register `event_id` as a GA4 custom dimension.

### 2.1 Inventory governance

| Control | Decision |
|---|---|
| Environment | QA and production through the approved hostname lookup; unknown host blocked |
| Consent/privacy | Analytics data; Event Tag requires the approved `analytics_storage` behavior from `FD-REC-05` |
| Allowed values | Exact schema/app/outcome values plus the controlled vocabulary registry required by `FD-OPEN-004` |
| Owner/status | FD GTM owner / simulated current state; runtime activation is blocked |
| Review trigger | Schema, source path, required/optional rule, consent, destination or consumer change |
| Retirement | Deprecate, migrate all Trigger/Tag/report consumers, test, publish and monitor before removal |

## 3. Simulated handoff

```text
Application pushes one complete calculation_action message
  → Data Layer Variables read the minimized approved analytics fields
  → Trigger checks event/schema/app/event_id/required fields/routing conditions
  → GA4 Event tag maps the same Variables once
```

## 4. Acceptance criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove live GTM configuration.

- [x] Every active Variable maps to a field approved in `FD-REC-07` schema `3.0`.
- [x] Nested paths use Data Layer Variable Version 2.
- [x] Required-field missing/invalid behavior fails closed and is consistent with `FD-REC-07`.
- [x] No Variable calculates `solution_found` or reconstructs the Application snapshot.
- [x] Unknown hostnames have no production fallback.
- [x] Consumers are listed before a future reuse or change.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
