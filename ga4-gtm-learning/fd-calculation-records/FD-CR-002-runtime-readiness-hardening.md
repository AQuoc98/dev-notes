# FD-CR-002 — Runtime-readiness contract hardening

> **SIMULATED — Change Request packet only.** This packet updates the FD simulation design after the cross-document review. It does not authorize or claim a live Application, GTM or GA4 change.

## 0. Change Request metadata

| Field | Value |
|---|---|
| Change Request ID | `FD-CR-002` |
| Project / journey | FD web application / `J-FD-CALC-001` / `calculation_action` |
| Request type | Modify / Data minimization, observability and runtime readiness |
| Status | **Approved — simulation documentation; runtime blocked** |
| Current historical baseline | Schema `2.0-simulated` after `FD-CR-001` |
| Proposed/current simulation update | Schema `3.0-simulated` |
| Requester / business owner | `[FD product owner — simulated]` |
| Technical / QA owner | `fd-gtm-implementer@strongtie.com` / `fd-qa@strongtie.com` — simulated aliases |
| Privacy reviewer / approver | `[privacy owner — placeholder]` / `fd-publisher@strongtie.com` — simulated aliases |
| GA4 scope | Simulated QA/production FD properties and streams; seven event-scoped custom definitions; base key event remains off |
| GTM scope | `GTM-FAKEFD01` / `WS-FD-CALC-001`; Variables, authoritative Trigger, native GA4 Event tag and compatibility version |
| Application scope | FD web app on approved QA/production hostnames; analytics adapter in `fd-web-simulated-build-003` |
| Effective release / migration boundary | `REL-FD-CALC-003`; effective timestamp `[pending runtime release]`; schemas `1.0`/`2.0` remain separate history |
| Evidence location / retention | `[controlled runtime location, access owner and retention period — pending]`; no runtime evidence exists |
| External ticket/reference | N/A — local simulation packet; attach the real delivery ticket before runtime implementation |
| Created / target / publish / closure | `2026-09-07` / `[pending]` / `[not published]` / `[not closed at runtime]` |
| Runtime status | Not executed; `FD-OPEN-001` through `FD-OPEN-004` remain runtime blockers |
| Governance | [00 — FD Change Request Governance](../00-change-request-governance.md) |

## 1. Change summary

Schema `3.0-simulated` hardens the analytics handoff without changing the existing combined `solution_found` meaning:

| Area | Schema `2.0` | Schema `3.0-simulated` |
|---|---|---|
| Data Layer payload | Complete/example `inputs` shape could expose fields not used by analytics | Complete Calculation API snapshot stays in the Application; Data Layer receives only the approved analytics subset |
| Occurrence reconciliation | Internal IDs excluded and no shared analytics occurrence key | Add required random opaque `event_id`; never use as User-ID or custom dimension |
| Numeric fields | `fx`/`fy` approved for collection without a current report consumer | Remove from analytics payload; retain only in the Application/API contract |
| Required categorical fields | Downstream records allowed omission despite the event contract | Missing/invalid required fields fail closed before the Tag sends |
| Reporting schema filter | `event_schema_version` used by assets without registration | Require an event-scoped custom dimension before GA4 UI assets use the filter |
| Key event | Previously approved despite mixed outcomes | Set to Pending; do not configure until `FD-OPEN-002` is resolved |
| Consent/QA/release | Partial consent lifecycle and no evidence-based rollout choice | Add runtime blockers, consent lifecycle tests, direct-v3 or conditional v2→v3 path selection and coordinated rollback |

`FD-CR-002` does **not** separate valid no-solution from API error, timeout, cancellation or stale terminalization. That semantic decision remains `FD-OPEN-001` and requires another CR/schema if changed.

## 2. Risk and acceptance criteria

| Field | Decision |
|---|---|
| Business purpose | Make the FD event implementable and auditable without sending unused API fields or treating documentation as runtime evidence |
| Risk | High — required payload, schema filter, report registration and rollout behavior change |
| Risk rationale | An incompatible Application/GTM window, malformed required fields, unapproved consent behavior or an uncorrelated duplicate can cause missing, duplicate, misrouted or unauthorized collection |
| Historical comparability | Keep schema `1.0`, `2.0` and `3.0` populations separate unless an explicit normalized model is approved |
| Minimum acceptance criteria | Current records use schema `3.0`; one opaque `event_id` is consistent across layers; only nine approved scalars reach GA4; missing/invalid required fields fail closed; `fx`/`fy` are absent; reporting uses a registered schema dimension; key-event configuration remains off; consent/runtime gates are explicit |
| Rollback | Verify the actual deployed baseline first. For a new v3 deployment, restore the named pre-release Application/GTM state; only when live v2 is verified should compatibility remain active while Application v2 and the matching GTM version are restored |

## 3. Impact assessment

| Record | Reading status | Required update |
|---|---|---|
| `FD-REC-00` | Required — affected | Update duplicate, consent and minimized-payload baseline |
| `FD-REC-01` | Required — affected | Minimized Data Layer, `event_id`, schema `3.0`, no analytics `fx`/`fy` |
| `FD-REC-02` | Required — affected | Add `event_id`, remove `fx`/`fy`, enforce required fields, update schema filter |
| `FD-REC-03` | Required — affected | Update final Trigger to schema `3.0` and fail-closed contract checks |
| `FD-REC-04` | Required — affected | Nine-field mapping and required-field behavior |
| `FD-REC-05` | Required — affected | Add region/update/persistence/revocation runtime gates |
| `FD-REC-06` | Context only | Native-template decision remains unchanged |
| `FD-REC-07` | Required — affected | Current contract, parameter dictionary, key-event and lifecycle decisions |
| `FD-REC-08` | Required — affected | Add `event_id`, consent lifecycle, malformed contract and browser cases |
| `FD-REC-09` | Required — affected | Schema `3.0`, registered schema dimension and feasible asset definitions |
| `FD-REC-10` | Required — affected | Release `REL-FD-CALC-003`, evidence-based deployment path and measurable monitoring |
| `FD-REC-11` | Required — affected | Runtime entry gate and `event_id` reconciliation |

The first implementation layer is the Application analytics adapter. Every downstream consumer through monitoring must be updated and validated; `FD-REC-06` is context-only because native Tags remain sufficient.

### 3.1 Direct record links

| Record group | Direct links |
|---|---|
| Baseline and contract | [`FD-REC-00`](FD-REC-00-phase-0-system-inventory.md), [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Application and GTM | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| Consent and template decision | [`FD-REC-05`](FD-REC-05-consent-decision.md), [`FD-REC-06`](FD-REC-06-template-governance-decision.md) |
| QA, reporting and operations | [`FD-REC-08`](FD-REC-08-debug-qa.md), [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md), [`FD-REC-10`](FD-REC-10-release-monitoring.md), [`FD-REC-11`](FD-REC-11-runtime-verification.md) |

## 4. Record update order

1. Update `FD-REC-07` as the semantic/schema source of truth and add the lifecycle entry.
2. Update `FD-REC-01` and `FD-REC-00` for the Application payload, occurrence, data and consent baseline.
3. Update `FD-REC-05` before approving downstream Tag behavior.
4. Update `FD-REC-02`–`FD-REC-04`; confirm `FD-REC-06` remains context-only.
5. Update `FD-REC-09` field registration and reporting filters.
6. Update `FD-REC-08` and `FD-REC-11` with the runnable test/evidence contract.
7. Update `FD-REC-10` with the compatibility release, monitoring and affected-period controls.
8. Update the master journey register, gates, status and decision index.

## 5. Simulated release and monitoring plan

| Item | Decision |
|---|---|
| Release | `REL-FD-CALC-003`; first verify the actual runtime baseline. If no live FD contract exists, deploy v3 directly through QA. If live v2 is evidenced, use mutually exclusive v2/v3 GTM paths → deploy Application v3 → observe v2 cessation → publish final v3-only GTM version |
| Environments | QA first; production only after Gates 0–3, runtime evidence and cross-functional approval |
| Smoke test | Schema/app/event/outcome/required-field checks; one matching `event_id` across Application/Data Layer/Network; nine-field allowlist; consent lifecycle and destination routing |
| Monitoring | Volume; output/combined-no-outcome distribution; repeated `event_id`; missing/invalid fields; destination; consent/privacy; freshness; vocabulary quality |
| Baseline/threshold | Calibrate from complete real pre-release data; privacy or wrong destination is immediately release-blocking; numeric thresholds remain pending real data |
| Observation/escalation | Immediate smoke plus a named short-window cadence and processed-data follow-up; owner/channel/SLA must be filled in `FD-REC-10` before publish |
| Containment/rollback | Restore the named pre-release state for a direct v3 deployment. For a verified live-v2 migration, keep compatibility active while restoring Application v2, verify the legacy path, then restore final v2-only GTM if required; record the affected period |

## 6. Closure boundary

The documentation update may close when every affected record links this CR and consistently describes schema `3.0-simulated`. Runtime remains blocked until the master journey open decisions are resolved, real QA evidence exists, the evidence-based release path is executed, monitoring thresholds are calibrated and processed GA4 data is validated.

| Closure item | Current result |
|---|---|
| Change record, before/after state and impact assessment | Complete for simulation |
| Affected-record propagation | Complete for simulation; all required records link `FD-CR-002` |
| Live Application/GTM/GA4 implementation | Not performed |
| Runtime QA and end-to-end reconciliation | Not performed; `FD-REC-11` remains not applicable |
| Consent/privacy and business decisions | Blocked by `FD-OPEN-001`–`004` |
| Release/monitoring/processed-data validation | Not performed |
| Documentation status | Approved — simulation documentation |
| Runtime closure status | Blocked; not closed |
