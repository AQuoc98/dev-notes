# FD-CR-001 — Change `solution_found` from `No` to `No_solution`

> **SIMULATED — Historical Change Request packet only.** This packet records the v1→v2 outcome rename. It was never implemented at runtime and is superseded by the current schema `3.0` design in [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md).

## 0. Change Request metadata

| Field | Value |
|---|---|
| Change Request ID | `FD-CR-001` |
| Project / journey | FD web application / `J-FD-CALC-001` / `calculation_action` |
| Request type | Modify / Schema and semantic value |
| Status | **Completed — simulation documentation; superseded by `FD-CR-002`; runtime not executed** |
| Historical baseline | Schema `1.0-approved`; `solution_found` values `Yes` / `No` |
| Historical simulation update | Schema `2.0-simulated`; `solution_found` values `Yes` / `No_solution` |
| Current project state | Schema `3.0-simulated` under [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md); CR-001 must not be used as the current implementation contract |
| Requester / business owner | `[FD product owner — simulated]` |
| Technical / QA owner | `fd-gtm-implementer@strongtie.com` / `fd-qa@strongtie.com` — simulated aliases |
| Privacy reviewer / approver | `[privacy owner — placeholder]` / `fd-publisher@strongtie.com` — simulated aliases |
| GA4 scope | Simulated QA/production FD properties and streams; `solution_found` values and schema/report-filter interpretation changed; no live definition or key-event change occurred |
| GTM scope | `GTM-FAKEFD01` / `WS-FD-CALC-001`; schema Variable validation, authoritative Trigger filter and native GA4 Event tag mapping |
| Application scope | FD web app on approved QA/production hostnames; v2 outcome classifier/build remained a simulated placeholder and was never deployed |
| Effective release / migration boundary | `REL-FD-CALC-002`; no runtime effective timestamp; schema `1.0` and schema `2.0` remain separate historical simulation populations |
| Evidence location / retention | N/A — no runtime evidence; simulation records remain in this documentation packet |
| External ticket/reference | N/A — local simulation packet |
| Created / target / publish / closure | `2026-09-07` / `2026-09-15` simulated target / `[not published]` / `2026-09-07` documentation closure |
| Runtime status | Not executed; no live publish, monitoring or processed-data validation; current runtime readiness is governed by `FD-CR-002` |
| Governance | [00 — FD Change Request Governance](../00-change-request-governance.md) |

## 1. Change summary

The simulated product request renamed the combined no-output/error value from `No` to `No_solution` so reviewers and report consumers could interpret it more explicitly.

This change **renames the existing combined outcome**. It does not split terminal errors into a separate value. Empty responses, API errors, timeouts and cancellation/stale terminal outcomes continue to use `No_solution`. `FD-CR-002` retains that meaning in schema `3.0`; any future separation still requires another Change Request/schema and resolution of `FD-OPEN-001` in the master journey.

| Item | Version 1 baseline | Version 2 simulated state |
|---|---|---|
| Event | `calculation_action` | `calculation_action` |
| Schema | `1.0` | `2.0` |
| Parameter | `solution_found` | `solution_found` |
| Allowed values | `Yes`, `No` | `Yes`, `No_solution` |
| Output mapping | Non-empty response → `Yes` | Non-empty response → `Yes` |
| No-output mapping | Empty response → `No` | Empty response → `No_solution` |
| Error mapping | Terminal error → `No` | Terminal error → `No_solution` |

This before/after table is historical. It does not override the current schema `3.0` payload, required-field, consent, reporting or release controls in `FD-CR-002` and the current FD records.

## 2. Risk and acceptance criteria

| Field | Decision |
|---|---|
| Business purpose | Make the combined no-output/error vocabulary explicit for reviewers and report consumers. |
| Risk | High — the allowed value, schema version and all downstream consumers changed together. |
| Risk rationale | A partial Application/GTM/report migration could drop events, admit the wrong value or mix incompatible historical populations. |
| Historical comparability | The old `No` value belongs to the pre-change period. Reports must not silently merge it with `No_solution`; document the effective release boundary. |
| Minimum acceptance criteria | `Yes` remains unchanged; every valid no-output/error outcome emits `No_solution`; no new `No` value is emitted after the effective release; the schema is `2.0`; QA, reports and monitoring use the new value. |
| Rollback | During a hypothetical runtime migration, keep a reviewed v1/v2 compatibility version active, restore Application schema `1.0`, verify it, then restore the matching final v1 GTM version. No rollback was executed. |

## 3. Impact assessment

| Record | Historical reading status for CR-001 | Required v1→v2 update |
|---|---|---|
| `FD-REC-00` | Context only — baseline meaning unchanged | Update simulated outcome examples and historical QA scope |
| `FD-REC-01` | Required — affected | Update outcome mapping, payload examples, schema and expected QA results |
| `FD-REC-02` | Required — affected | Update schema validation and `solution_found` allowed values |
| `FD-REC-03` | Required — affected | Change the schema filter from `1.0` to `2.0`; preserve authoritative firing logic |
| `FD-REC-04` | Required — affected | Update the schema/value mapping expectation |
| `FD-REC-05` | Not applicable | Consent state and `analytics_storage` behavior did not change under CR-001 |
| `FD-REC-06` | Not applicable | Native Variables/Tags remained sufficient; no custom template was introduced |
| `FD-REC-07` | Required — affected | Add the schema lifecycle entry and update the value contract first |
| `FD-REC-08` | Required — affected | Update expected values, negative cases and evidence plan |
| `FD-REC-09` | Required — affected | Update filters, formulas and historical interpretation |
| `FD-REC-10` | Required — affected | Use `REL-FD-CALC-002` and document monitoring/rollback |
| `FD-REC-11` | Context only — not changed | Remained not applicable because no runtime run occurred |

The first implementation layer would have been the Application outcome classifier. Every downstream consumer through monitoring required coordinated migration. These statuses describe CR-001's historical impact; readers implementing the current state must follow each record's `FD-CR-002` instruction.

### 3.1 Direct record links

| Record group | Direct links |
|---|---|
| Baseline and contract | [`FD-REC-00`](FD-REC-00-phase-0-system-inventory.md), [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Application and GTM | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md), [`FD-REC-02`](FD-REC-02-gtm-variable-inventory.md), [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| Consent and template decision | [`FD-REC-05`](FD-REC-05-consent-decision.md), [`FD-REC-06`](FD-REC-06-template-governance-decision.md) |
| QA, reporting and operations | [`FD-REC-08`](FD-REC-08-debug-qa.md), [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md), [`FD-REC-10`](FD-REC-10-release-monitoring.md), [`FD-REC-11`](FD-REC-11-runtime-verification.md) |
| Superseding change | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md) |

## 4. Record update order

1. Update `FD-REC-07` as the semantic/schema source of truth and add the v1→v2 lifecycle entry.
2. Update `FD-REC-01` and the `FD-REC-00` historical QA baseline.
3. Confirm `FD-REC-05`/`FD-REC-06` are unaffected by CR-001.
4. Update `FD-REC-02`–`FD-REC-04` so GTM consumes only the schema `2.0` value contract.
5. Update `FD-REC-09` so schemas `1.0`/`2.0` remain separately interpretable.
6. Update `FD-REC-08` and retain `FD-REC-11` as not applicable until a real run exists.
7. Update `FD-REC-10` with the release, monitoring and rollback packet.
8. Update the master journey register, phase status and decision index.

This sequence is retained as historical traceability. Current implementation work follows the v2→v3 order in `FD-CR-002`.

## 5. Simulated release and monitoring plan

| Item | Decision |
|---|---|
| Release | `REL-FD-CALC-002` / `GTM-FD-CALC-002` placeholder; hypothetical temporary mutually exclusive v1/v2 paths → deploy Application v2 → observe v1 cessation → publish final v2-only GTM version |
| Environments | QA first; production only after full QA, runtime evidence and cross-functional approval |
| Smoke test | Exercise safe v1/v2 payloads; prove one mutually exclusive path/request; verify `Yes`, `No_solution`, consent and hostname routing; final v2 path rejects schema `1.0` |
| Monitoring | Distribution of `Yes`/`No_solution`; legacy `No`; invalid/missing outcomes; duplicate count; destination; consent; report freshness |
| Baseline/threshold | Would require complete real schema `1.0` data and explicit thresholds; none was calibrated in this simulation |
| Observation/escalation | Immediate smoke, named observation window, processed-data follow-up, monitoring owner and escalation path were required but never executed |
| Historical boundary | Keep schema `1.0`/`No` separate from schema `2.0`/`No_solution`; no normalized model was approved |
| Containment/rollback | Keep compatibility active while restoring Application v1; verify the v1 path; then restore final v1-only GTM if required |

## 6. Closure boundary

CR-001 is closed only at the simulation-documentation level: the before/after decision and historical propagation are recorded, but no runtime release occurred. It is superseded by CR-002 and must not be reopened as the current contract. Any live work starts from schema `3.0` and the open decisions in the master journey.

| Closure item | Current result |
|---|---|
| Change record, before/after state and impact assessment | Complete for simulation |
| Affected-record propagation | Complete as historical lineage; current records now also apply `FD-CR-002` |
| Live Application/GTM/GA4 change | Not performed |
| Runtime QA and end-to-end reconciliation | Not performed; `FD-REC-11` remained not applicable |
| Release/monitoring/processed-data validation | Not performed |
| Documentation status | Completed — historical simulation documentation |
| Current-contract status | Superseded by `FD-CR-002` / schema `3.0-simulated` |
| Runtime closure status | Not executed; no production closure claim |
