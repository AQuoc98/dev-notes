# FD `calculation_action` — Master project control

> **Simulation/runtime boundary.** This packet documents a simulated Application → Data Layer → GTM → GA4 design only. No account, property, stream, container, source code, browser/Network/DebugView run, publish, production data or runtime evidence exists. Values marked `SIMULATED`, `FAKE` or `[placeholder]` are design data and must never be treated as runtime proof.

## 1. Current status

| Area | Status |
|---|---|
| Documentation phases 0–6 | Complete at simulation-design level |
| Current contract | Schema `3.0` simulated; `calculation_action` terminal event; `Yes` / `No_solution` |
| Runtime readiness | **Blocked**; no live implementation or runtime evidence |
| Immediate next action | Resolve `FD-OPEN-001`–`004`, then execute `FD-REC-11` in an authorized QA/staging project |

`FD-REC-07` is the semantic source of truth. The Application retains the complete Calculation API snapshot and emits only the approved scalar analytics subset plus one opaque per-occurrence `event_id`; `inputs` is never sent as a single GA4 parameter.

### Runtime blockers

| ID | Required decision | Owner | Exit condition |
|---|---|---|---|
| `FD-OPEN-001` | Decide whether valid no-output may remain combined with API error, timeout and cancellation, or whether a new CR/schema must separate technical outcomes. | Product + Application + Analytics | Approved semantics and report/monitoring treatment; exception owner/date if combined |
| `FD-OPEN-002` | Decide whether the mixed-outcome `calculation_action` is a key event. | Product + Analytics | Rationale, success condition, effective date and implementation method; default remains off |
| `FD-OPEN-003` | Approve consent region, CMP callback/update, persistence, revocation/cleanup and policy version. | Privacy + Analytics | `FD-REC-05` has approved values and a runnable consent matrix |
| `FD-OPEN-004` | Approve categorical vocabularies/cardinality and privacy/retention for opaque `event_id`. | Application + Analytics + Privacy | Registry and privacy decision linked from `FD-REC-07` |

## 2. Record register

| Record | Role | Status / path |
|---|---|---|
| [`FD-REC-00`](fd-calculation-records/FD-REC-00-phase-0-system-inventory.md) | Environment, destination, access, consent and duplicate baseline | Active; runtime blocked |
| [`FD-REC-07`](fd-calculation-records/FD-REC-07-measurement-plan-event-contract.md) | Semantic contract, allowlist and schema lineage | **Active source of truth**; runtime blocked |
| [`FD-REC-01`](fd-calculation-records/FD-REC-01-application-data-layer-specification.md) | Application/Data Layer handoff | Active; consumes REC-07 |
| [`FD-REC-05`](fd-calculation-records/FD-REC-05-consent-decision.md) | Consent decision and fail-closed behavior | Active/conditional; `FD-OPEN-003` |
| [`FD-REC-02`](fd-calculation-records/FD-REC-02-gtm-variable-inventory.md) | Approved GTM variables and missing-data rules | Active; consumes REC-01/07 |
| [`FD-REC-03`](fd-calculation-records/FD-REC-03-gtm-trigger-inventory.md) | Authoritative trigger and admission filters | Active; consumes REC-01/02/07 |
| [`FD-REC-04`](fd-calculation-records/FD-REC-04-gtm-tag-inventory.md) | Native tags, scalar mapping, routing and consent | Active; consumes REC-02/03/05/07 |
| [`FD-REC-06`](fd-calculation-records/FD-REC-06-template-governance-decision.md) | Native-tag decision; custom-template exception policy | Conditional governance; native path selected |
| [`FD-REC-09`](fd-calculation-records/FD-REC-09-ga4-report-exploration.md) | Event-level metric, definitions, reports and interpretation | Conditional; processed data pending |
| [`FD-REC-08`](fd-calculation-records/FD-REC-08-debug-qa.md) | QA matrix, safety checks and evidence plan | Active gate; not executed |
| [`FD-REC-11`](fd-calculation-records/FD-REC-11-runtime-verification.md) | End-to-end runtime verification | Conditional; not applicable until authorized runtime |
| [`FD-REC-10`](fd-calculation-records/FD-REC-10-release-monitoring.md) | Release, smoke, monitoring, rollback and closure | Conditional; release blocked |

### Historical/change records

| Record | Meaning |
|---|---|
| [`FD-CR-001`](fd-calculation-records/FD-CR-001-solution-found-value-change.md) | Historical v1 `No` → v2 `No_solution`; simulation only, superseded, excluded from active implementation path |
| [`FD-CR-002`](fd-calculation-records/FD-CR-002-runtime-readiness-hardening.md) | Current simulated v2 → v3 hardening; applied to active records, runtime still blocked |

Keep both for audit lineage. Neither is runtime evidence; current work starts from `FD-REC-07` schema `3.0` and the open decisions.

## 3. Dependency order and handoff gates

Before approving a governed design or starting implementation, create or update the [FD Change Request Governance record](00-change-request-governance.md). Record the requester, business/technical owners, scope, risk, impact, current versus target schema, runtime-baseline status and affected-record update order. Documentation-only changes still require a CR when they change the design baseline.

| Order | Record/phase | Handoff gate |
|---:|---|---|
| 0 | `CR` → `REC-00` — intake/inventory | Scope, owners, risk/impact and current runtime baseline are recorded; affected records identified |
| 1 | `REC-07` — contract | Event meaning, occurrence/grain, schema, scalar allowlist, vocabularies, privacy and owners approved |
| 2 | `REC-01` — Application/Data Layer | Immutable snapshot, outcome rules, one opaque `event_id`, idempotency and minimized payload defined |
| 3 | `REC-05` — consent | Default/update/persistence/revocation approved; denied/unknown fail-closed |
| 4 | `REC-02` → `REC-04` — GTM | Variables, narrow authoritative trigger, native tag mapping, hostname and consent routing agree with REC-07 |
| 5 | `REC-06` — template governance | Native capability checked; any exception has security/privacy owner and review path |
| 6 | `REC-09` — reporting | Event-level metric, registered definitions, schema filter and interpretation limits documented |
| 7 | `REC-08` — QA | Synthetic-data/safety gate and positive, negative, consent, routing, duplicate and schema tests runnable |
| 8 | `REC-11` — runtime | Authorized build/version/environment; Application → Data Layer → GTM → Network → GA4 evidence reconciles |
| 9 | `REC-10` — release | QA pass, approval, smoke, monitoring owner/window and compatible rollback ready |

Downstream records may be drafted in parallel but cannot be approved before their upstream contract is stable. `REC-11` must never be completed from simulated values.

### Phase summary

| Phase | Deliverable | Current gate |
|---|---|---|
| 0 | System/access/destination/consent baseline | Simulation complete; runtime values pending |
| 1–2 | Event contract and Application/Data Layer handoff | Simulation complete; REC-07 governs |
| 3 | GTM variables, trigger, tags and routing | Simulation complete; native path; runtime blocked |
| 4 | GA4 definitions, reports and event-level metric | Design complete; creation/processed data pending |
| 5 | Debug/QA and runtime evidence | Test plan complete; execution pending |
| 6 | Release, monitoring and rollback | Design complete; no release/observation |

## 4. Mandatory technical decisions

- **Semantic source of truth:** `FD-REC-07` owns event meaning, occurrence, schema, approved parameters, consent boundary and destination. Downstream records consume it and do not redefine it.
- **Application ownership:** the Application is authoritative for the terminal outcome and immutable snapshot; validation, remount, replay and duplicate callbacks do not create extra events.
- **Payload boundary:** GA4 receives only nine approved scalars: `app_name`, `event_id`, `event_schema_version`, `solution_found`, `country`, `language`, `building_code`, `design_method`, `connection_type`. `fx`, `fy`, full `inputs`, API responses, internal tokens, secrets and PII stay out.
- **Scalar allowlist / opaque ID:** GTM admits only the allowlist. `event_id` is an opaque UUID for cross-layer reconciliation, never User-ID or a custom dimension; privacy/retention remains `FD-OPEN-004`.
- **Consent fail-closed:** default, denied, unknown, delayed or errored consent suppresses analytics; granted collection follows `FD-REC-05`.
- **Hostname fail-closed:** approved QA routes only to QA, production only to production; unknown hostname has no fallback destination.
- **Event-level metric:** report `Yes / (Yes + No_solution)` as the event-level output rate at occurrence grain within schema `3.0`. Because `No_solution` still combines no-output and technical outcomes, do not label this a pure solution-success rate; a combined no-output/error rate would use `No_solution / (Yes + No_solution)`. Never infer a user-level conversion rate.
- **Schema lineage:** v1 `No`, v2 `No_solution`, and v3 minimized payload remain separate historical populations. `FD-CR-001` is superseded; `FD-CR-002` is current simulated change.

## 5. Closure and next action

This package is complete only as a simulation baseline. Runtime closure is **not claimed**. Go requires resolved blockers, QA and `FD-REC-11` retained evidence, reconciled counts/destination/consent, and completed `FD-REC-10` observation and affected-period treatment. Rollback must restore a compatible Application/GTM schema pair; it cannot rewrite processed GA4 data.

**Next action:** resolve `FD-OPEN-001`–`004`, update affected records and approvals, verify the actual deployed baseline, then run the dependency order through `FD-REC-11` before `FD-REC-10` release approval.
