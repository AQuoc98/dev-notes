# FD-REC-10 — Release and Monitoring Simulation Record

> **SIMULATED — Phase 6 documentation only.** This record defines the release, monitoring, containment and rollback plan for `calculation_action`; it does not publish GTM, activate GA4 settings or monitor a live environment.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-10` |
| Record name | Release and Monitoring Simulation Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 6 — Release, monitoring and rollback |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), schema `3.0`, `FD-CR-002` |
| Change reading status | **Required — affected** |
| Change Requests to read | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for historical lineage |
| Why this matters | Release, monitoring and rollback must use the verified runtime baseline while proving the new payload, occurrence and consent controls. |
| Implementation records | [`FD-REC-01`](FD-REC-01-application-data-layer-specification.md) through [`FD-REC-06`](FD-REC-06-template-governance-decision.md) |
| Reporting record | [`FD-REC-09`](FD-REC-09-ga4-report-exploration.md) |
| QA record | [`FD-REC-08`](FD-REC-08-debug-qa.md) |
| Runtime record | [`FD-REC-11`](FD-REC-11-runtime-verification.md), not applicable in this simulation |
| Status | **Design complete — simulation documentation; `FD-CR-002` applied; release blocked** |
| Execution state | Simulation only — no workspace, version, publish, smoke test or monitoring run |
| Value/evidence boundary | Release fields, gates, thresholds and outcomes are simulated; no production decision is claimed |
| Primary owner | FD Analytics/GTM Lead — simulated owner |
| Reviewers | Application, GTM, Analytics, QA and Privacy owners — simulated aliases |
| Open items | `FD-OPEN-001`–`004`, actual deployed-baseline verification, real approval, publication, observation and threshold calibration remain unresolved |
| Next action | Resolve the blockers, then execute `REL-FD-CALC-003` through the documented compatibility sequence |
| Created / last updated | `2026-09-07` / `2026-09-07` — `FD-CR-002` |

## 1. Release Record

### 1.1 Release context

| Field | Simulated value |
|---|---|
| Release ID | `REL-FD-CALC-003` |
| Project/change ID | `J-FD-CALC-001` / `FD-CR-002` |
| Status | `Blocked — simulation release design only`; future live status flow: Draft → Review → Approved → In QA → Published → Monitoring → Closed |
| Risk level | High for a future live release because the change affects Data Layer shape, required fields, occurrence reconciliation, consent gates and reporting |
| Risk rationale | An incompatible Application/GTM window, missing `event_id`, wrong destination, duplicate source or incomplete consent state can create loss, duplication or unauthorized collection |
| Change title | Harden the FD analytics contract for runtime readiness |
| Business purpose | Preserve terminal-attempt measurement while minimizing analytics data, enabling occurrence reconciliation and making release gates enforceable |
| Change type | Breaking schema `2.0`→`3.0` migration across Application, Data Layer, GTM, GA4, QA, reporting and monitoring |
| Measurement Plan | `FD-MP-001`, schema `3.0-simulated`; schemas `1.0`/`2.0` are historical baselines |
| Affected journey | `J-FD-CALC-001` |
| Target environment | Simulated QA first, then simulated production after approval |
| GA4 destination | `G-FAKEFDQA01` for QA; `G-FAKEFDPROD1` for production |
| GTM container/workspace | `GTM-FAKEFD01` / `WS-FD-CALC-001` |
| Source/build | `fd-web-simulated-build-003` |
| Changed objects/settings | Application analytics adapter, schema/`event_id` Variables, Trigger admission, GA4 Event allowlist, custom-definition plan, consent lifecycle record, QA matrix, reports and monitoring |
| Expected collection | One minimized Data Layer push → one mutually exclusive Trigger match and, when consent permits, one Tag fire/request with the same `event_id` per valid schema `3.0` occurrence; denied/unknown yields zero analytics requests |
| Consent/privacy impact | `analytics_storage=denied` by default; granted required; no complete API snapshot, internal token, secret or PII in the analytics Data Layer/GA4; opaque `event_id` approval pending |
| Report impact | `FD-REP-001` and `FD-EXP-001`; register/filter `event_schema_version=3.0`, use `No_solution`, and keep schemas `1.0`/`2.0` as separate historical populations |
| Rollback/mitigation | Use the compatibility sequence in Section 5.0; restore the last known-good Application/GTM contract pair without creating an incompatible window; preserve the affected period and investigate Application/property causes separately |
| Release window | `[simulated window]`; no real publication |
| Observation window | Immediate signal check plus processed-data follow-up after the documented GA4 processing window |

### 1.2 Roles and ownership

| Role | Simulated owner | Responsibility |
|---|---|---|
| Requester/product owner | `[FD product owner]` | Business purpose and success criterion |
| Developer | `fd-developer@strongtie.com` | Application/Data Layer and build context |
| GTM implementer | `fd-gtm-implementer@strongtie.com` | Variables, Trigger, Tags, consent, routing and workspace |
| Analytics owner | `fd-analytics-owner@strongtie.com` | Event/report impact, field readiness, baseline and interpretation |
| QA reviewer | `fd-qa@strongtie.com` | Section 08 QA package and evidence review |
| Privacy reviewer | `[privacy owner]` | Data, consent, access and destination risk |
| Publisher/approver | `fd-publisher@strongtie.com` | Gate decision, publication and rollback approval |
| Monitoring/incident owner | `[FD monitoring owner]` | Observation, escalation and affected-period assessment |

These are simulated aliases. No person has been granted or exercised access in this project.

## 2. Release packet and traceability

| Required packet item | Simulated reference/status |
|---|---|
| Approved Measurement Plan/schema | `FD-REC-07`, schema `3.0` after `FD-CR-002`; schemas `1.0`/`2.0` retained as historical baselines |
| Application/Data Layer handoff | `FD-REC-01` — available |
| GTM Variables/Trigger/Tag/Consent/Template records | `FD-REC-02` through `FD-REC-06` — available |
| Section 08 QA result and evidence | `FD-REC-08` — expected results only; no runtime evidence |
| Runtime Verification | `FD-REC-11` — `N/A`, no real run |
| Report/configuration impact | `FD-REC-09` — simulated configuration only |
| Named GTM version/publisher/environment | Simulated placeholders; not created |
| Monitoring Record | Section 3 of this record — simulated |
| Rollback/mitigation path | Section 4 of this record — simulated |

```text
Measurement Plan/schema decision
  → Application/Data Layer and GTM records
  → Section 08 QA package
  → Section 09 report impact
  → named version and target environment
  → Monitoring Record and affected-period assessment
```

## 3. Release gates

| Gate | Simulated decision | Required evidence in a future live project |
|---|---|---|
| Gate 0 — Requirement readiness | Documentation prepared; runtime blocked by `FD-OPEN-001`–`004` | Approved outcome/key-event decision, schema, vocabularies, report impact, consent/privacy/destination, verified active-runtime baseline and rollback approach |
| Gate 1 — Implementation readiness | Pass at documentation level | Focused workspace, safe routing, naming, expected count, overlap check and template review |
| Gate 2 — QA readiness | Pending — no runtime execution | Section 08 positive/negative/duplicate/consent/privacy/routing evidence and processed-data follow-up when required |
| Gate 3 — Publish readiness | Not applicable — no live publisher/version | Named version, approvals, target environment, release window and rollback path |
| Gate 4 — Post-publish readiness | Not applicable — no publish/smoke test | Smoke result, version/destination check, immediate signals, processed-data validation and Monitoring outcome |

The current simulation may document Gates 0–1, but it cannot mark Gates 2–4 as passed without runtime evidence.

## 4. Monitoring Record

### 4.1 Monitoring context

| Field | Simulated value |
|---|---|
| Monitoring ID | `MON-FD-CALC-001` |
| Release ID | `REL-FD-CALC-003` |
| Status | `Draft — simulated monitoring plan; no observation`; future live flow: Draft → Review → Monitoring → Pending/Closed/Blocked |
| Risk level | High |
| Asset/event monitored | `calculation_action`, `FD-REP-001`, `FD-EXP-001` |
| Business outcome | One terminal calculation attempt is recorded with `solution_found=Yes` or combined `No_solution`; v1 `No` remains pre-release history; interpretation remains blocked by `FD-OPEN-001` |
| Population/grain/scope | Valid event-level attempts; same property, stream, method, date range, consent and schema rules as `FD-REC-09` |
| Source of truth | Application occurrence/event-ID record, Data Layer/GTM/Network counts, and processed GA4 report after the processing window |
| Baseline period/completeness | `[TBD from real pre-release data]`; record complete days, known outages, schema population and day-of-week pattern |
| Warning / release-blocking threshold | `[TBD after baseline calibration]`; privacy or wrong destination is immediately release-blocking regardless of volume |
| Observation window/check frequency | Immediate smoke check; `[TBD short-window cadence]`; processed-data follow-up after the documented GA4 processing window |
| Monitoring owner | `[FD monitoring owner]` |
| Escalation owner | `[FD release/incident owner]` |
| Escalation channel / response target | `[TBD channel and severity response SLA]` |
| Evidence location | `[controlled location — future live project]` |
| Status in current project | No observation performed; simulated plan only |

### 4.2 Minimum signals

| Signal | Definition | Expected simulated behavior | Response if abnormal |
|---|---|---|---|
| Collection volume | Count of `calculation_action` by approved period/method | One event per terminalized occurrence; no fixed daily volume | Check Application, Data Layer, Trigger and Tag first |
| Output/no-outcome distribution | `solution_found=Yes` vs `No_solution` | Schema `3.0` values only `Yes`/`No_solution`; `No_solution` includes terminal errors; compare schemas `1.0`/`2.0` separately | Check Application outcome mapping, release boundary and invalid-value rows |
| Duplicate/missingness | Repeated opaque `event_id`; Application occurrences versus Data Layer/Network/GA4 counts; missing required-field rate | One unique `event_id` and at most one request per occurrence; no missing required fields | Hold/contain if material; use approved export for exact production deduplication or document aggregate limitation |
| Destination | Hostname and Measurement ID | QA to QA; production to production; unknown blocked | Release-blocking if misrouted |
| Consent/privacy | Request and Tag behavior by consent state | Granted may collect; denied/unknown suppresses | Stop and escalate for unauthorized collection |
| Report freshness | Processed availability and incomplete date status | Pending until the expected processing window is complete | Record owner/follow-up; do not close early |
| Vocabulary/data quality | Invalid, `(not set)`, `Unassigned` and `(other)` rows | Keep separately; never silently merge into valid methods | Open data-quality follow-up |

### 4.3 Baseline and thresholds

Do not use a universal percentage threshold. A future live project must record its baseline period, completeness, day-of-week pattern, expected range, metric definition, owner and escalation path. The simulated starting severity policy is:

| Severity | Simulated trigger | Action |
|---|---|---|
| Critical | PII or unauthorized destination | Stop collection and escalate immediately |
| High | Material event absent, duplicated at scale or materially miscounted; approved key event absent when `FD-OPEN-002` is resolved as Yes | Hold/revert and open an incident |
| Medium | Required parameter missing for a material subset or one route/browser affected | Investigate and assign remediation |
| Low | Small vocabulary/documentation drift with no decision impact | Track and correct in the next change |

The exact numeric warning and release-blocking thresholds remain to be calibrated from real baseline data; this simulation intentionally does not invent production percentages.

## 5. Smoke test and rollback simulation

### 5.0 Deployment-path selection and coordinated schema migration

Before release, inspect the actual Application/Data Layer, GTM and GA4 state and name the last-known-good versions. The current project has no evidence that schema `2.0` was ever deployed, so do not assume a live v2 baseline.

**Path A — no live FD event contract (the default from current evidence):**

1. Publish the reviewed schema `3.0` GTM assets to QA with the v3-only contract gate.
2. Deploy Application schema `3.0` to QA.
3. Run the full consent, destination, payload, `event_id`, count and processed-data checks before production approval.

**Path B — live schema `2.0` is verified in the target environment:**

1. Publish a temporary, reviewed GTM compatibility version with two mutually exclusive paths: the documented legacy schema `2.0` path and the hardened schema `3.0` path. The v3 path requires `event_id`, the minimized payload and all current fields; the v2 path retains only its historical contract.
2. Smoke-test both paths with safe v2 and v3 payloads, consent lifecycle states and destinations; prove that exactly one path fires for each payload.
3. Deploy Application schema `3.0` and confirm the same opaque `event_id` across Application, Data Layer and Network evidence.
4. Observe until new schema `2.0` traffic stops for the approved window; investigate stragglers instead of merging populations.
5. Publish the final v3-only GTM Trigger/Tag version and repeat the smoke and processed-data checks.

For Path A rollback, restore the named pre-release Application/GTM state. For Path B, restore Application v2 while compatibility is active, verify the legacy path, then restore the approved v2-only GTM version if required. Record every version and timestamp in the affected-period assessment.

### 5.1 Smoke test plan

| Check | Expected simulated result | Actual status |
|---|---|---|
| Hostname and environment | Approved QA or production hostname | N/A — not executed |
| Published GTM version | Named approved version | N/A — not published |
| GA4 destination | Intended Measurement ID only | N/A — not executed |
| Consent | Approved state controls collection | N/A — not executed |
| Application outcome | One terminal calculation outcome: `Yes` or `No_solution`; no post-release `No` | N/A — not executed |
| Data Layer/GTM | One minimized push with one opaque `event_id`, one Trigger match, one Tag fire | N/A — not executed |
| Network | One redacted request with the same `event_id` and nine-field scalar allowlist | N/A — not executed |
| DebugView/Realtime | Recent event when applicable | N/A — not executed |
| Processed report | Available after processing window | N/A — not executed |

### 5.2 Rollback runbook

1. Confirm severity and identify the first failing layer.
2. Identify the last known-good Application build, GTM version and compatible schema pair for the affected environment.
3. Decide whether the fault is Application/Data Layer, GTM, consent, GA4 property or another system; do not assume a GTM-only rollback can correct an Application contract failure.
4. Obtain publisher/incident-owner approval.
5. Follow the coordinated rollback order from Section 5.0 so Application and GTM remain schema-compatible; publish a previous GTM version only when its accepted contract matches the active Application build.
6. Re-run the relevant smoke and regression checks.
7. Reconcile destination, count, consent, DebugView/Realtime and processed-data behavior.
8. Record rollback time, version, evidence, affected period and remaining impact.
9. Create the corrective change and regression cases before re-release.

Rollback changes future GTM behavior; it does not delete processed GA4 events, repair historical counts or undo a permanent data filter.

## 6. Incident and closure decisions

| Decision | Rule | Current simulated result |
|---|---|---|
| Go | Gates 0–3 pass and monitoring/rollback ownership is ready | Not applicable — no live release |
| Hold | Requirement undefined, wrong destination, prohibited data, unresolved duplicate or first failing layer unknown | Documented as the stop condition |
| Accept exception | Residual risk is bounded with owner, mitigation, reviewer and due date | Not requested |
| Close | Gate 4, observation window, processed-data check and affected-period assessment complete | Not applicable — no live observation |
| Incident | Material missing, duplicate, misrouted, malformed, privacy or semantic issue | No incident executed |

### 6.1 Incident and affected-period assessment template

Complete this record when a release is held, reverted or found to have a material collection defect. Do not close the release from a successful rollback alone.

| Field | Runtime value required |
|---|---|
| Incident / release ID | `[incident ID]` / `REL-FD-CALC-003` |
| Detection signal and first failing layer | `[monitor/alert/test]` / `[Application, Data Layer, GTM, consent, Network, GA4 or report]` |
| First bad / last bad timestamp | `[timestamp with timezone]` / `[timestamp with timezone or still active]` |
| Affected environment/destination/schema | `[hostname]` / `[property-stream]` / `[schema]` |
| Affected population and estimated count | `[same-scope definition]` / `[count and method/limitation]` |
| Consent/privacy impact | `[none, suspected or confirmed; reviewer and action]` |
| Containment and rollback | `[action, Application build, GTM version, approver and timestamps]` |
| Recovery verification | `[smoke/retest/runtime-verification evidence IDs]` |
| Historical-data treatment | `[report annotation, exclusion/normalization decision; processed events are not rewritten]` |
| Corrective change and owner | `[new CR/defect]` / `[owner and due date]` |
| Closure decision | `[Closed, Monitoring, Blocked or accepted exception]` with reviewer/date |

Closure requires the observation window, processed-data follow-up, affected-period boundary, historical reporting treatment and corrective owner to be complete.

## 7. Acceptance criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove a live release, smoke test or monitoring run.

- [x] `FD-REC-10` uses the Section 10 Release and Monitoring structure.
- [x] Release context, risk, scope, affected journey and downstream consumers are recorded.
- [x] Release packet links `FD-REC-01` through `FD-REC-09` and the future runtime record boundary.
- [x] Gates 0–4 are defined with the correct runtime evidence requirement.
- [x] Monitoring signals cover volume, outcome, duplicates/missingness, destination, consent, freshness and vocabulary.
- [x] Threshold policy distinguishes Critical/High/Medium/Low without inventing production percentages.
- [x] Deployment path is selected from verified runtime evidence; direct v3 deployment and conditional v2→v3 migration each have a compatible rollback path.
- [x] Smoke-test plan records all checks as `N/A — not executed`.
- [x] Rollback, containment, affected-period and closure rules are documented.
- [x] Simulation boundary is explicit and no live release or production decision is claimed.
