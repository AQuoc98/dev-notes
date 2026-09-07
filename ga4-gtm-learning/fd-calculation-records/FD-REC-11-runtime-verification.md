# FD-REC-11 — [RUNTIME VERIFICATION] End-to-End Runtime Verification Record

> **Use this record only when a future project actually runs the Application → Data Layer → GTM → GA4 flow.** It stores references to real runtime artifacts and is not valid for the current simulation-only journey. Do not mark simulated examples as runtime verification.

`[RUNTIME VERIFICATION]` is a governance label for this record, not a GTM Tag name.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-11` |
| Record tag | **`[RUNTIME VERIFICATION]`** |
| Record name | End-to-End Runtime Verification Record |
| Document type | `PROJECT RECORD` |
| Purpose | Prove one approved event flow across Application, Data Layer, GTM, Network, consent and GA4 |
| Project / journey | `FD web application / J-FD-CALC-001` |
| Source of truth | `FD-REC-07` schema `3.0` after `FD-CR-002` and `FD-REC-08` QA Run/Evidence Record |
| Change reading status | **Required — affected** |
| Linked Change Requests | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for historical lineage |
| Why this matters | Future verification must prove the schema `3.0` minimized payload, the same opaque `event_id`, strict admission and consent lifecycle end to end. |
| Runtime verification ID | `[RV-FD-YYYYMMDD-001]` |
| Linked test run / scenario | `[QA run ID]` / `[TC-FD-01]`; future runtime must validate schema `3.0`, `Yes`/`No_solution` and no current-schema use of legacy fields |
| Current status | `Not applicable — simulation-only` |
| Future runtime statuses | `Draft` / `In review` / `Completed — verified` / `Blocked` |
| Runtime environment | `[QA/staging/production]` |
| Application/build | `[commit, build or deploy ID]` |
| GTM container/workspace/version | `[container] / [workspace] / [version]` |
| GA4 property/stream/Measurement ID | `[property] / [stream] / [sanitized ID]` |
| Hostname and URL | `[approved hostname and test URL]` |
| Browser/device | `[browser version] / [device]` |
| Consent state | `[state by consent category]` |
| Tester / reviewer | `[name]` / `[name]` |
| Started / completed | `[timestamp]` / `[timestamp]` |
| Template use | Copy this record for an authorized runtime project; replace all placeholders and keep checklist items unchecked until evidence is attached |
| Value/evidence boundary | Real runtime artifacts only; simulated values must be labelled `SIMULATED` and kept out of this record |

## 0.1 Source of the record format

This is a future-project record that combines the runtime evidence and release-gate requirements from Sections 08 and 10. It is not a Google-provided form and it is not applicable to the current simulation-only journey.

| Record component | Reference |
|---|---|
| Test setup, data-safety and evidence layers | [Section 08 — Debug and QA](../08-debug-qa-answer.md) |
| Runtime verification and scenario-result structure | [Section 08 — Runtime Verification](../08-debug-qa-answer.md#28-runtime-verification-runtime-verification-record) |
| Release gate, smoke check, monitoring and rollback handoff | [Section 10 — Release Monitoring](../10-release-monitoring-answer.md) |
| Current FD scope and simulated status | [`11-fd-calculation-journey.md`](../11-fd-calculation-journey.md) |

## 1. Entry criteria

Mark each item before collecting evidence:

- [ ] Section 07 event contract and parameter allowlist are approved.
- [ ] `FD-OPEN-001` through `FD-OPEN-004` in the master journey are resolved; key-event configuration remains off unless explicitly approved.
- [ ] Application build and GTM version are identified and accessible.
- [ ] Target hostname, GA4 property/stream and consent state are approved.
- [ ] Test account/data is synthetic or explicitly allowlisted.
- [ ] Section 08 Test Run Setup and Data Safety Check are complete.
- [ ] No production customer data, PII, secret, credential or unapproved token is used.
- [ ] The tester has access to Preview/Tag Assistant, Network and the selected GA4 diagnostic surface.

If any entry criterion is false, set status to `Blocked` or `Pending verification`; do not report a runtime Pass.

## 2. Runtime verification matrix

Use one row for each layer. Every artifact must include the runtime verification ID, timestamp, environment and redaction status.

| Layer | Expected proof | Observed result | Artifact ID/link | Result |
|---|---|---|---|---|
| Application | Approved business state reached once; response belongs to the expected internal snapshot; one opaque `event_id` created | `[actual state/result/event_id]` | `[sanitized app log or test evidence]` | `Pass/Fail/Pending` |
| Data Layer | One self-contained minimized `calculation_action` message with approved schema, required values and the same `event_id` | `[event/count/payload summary]` | `[Data Layer capture]` | `Pass/Fail/Pending` |
| GTM Preview / Tag Assistant | Authoritative Trigger matched once; Variables resolved; intended Tag fired once; no blocking reason | `[timeline/count/variable summary]` | `[Preview session]` | `Pass/Fail/Pending` |
| Consent | Current consent state produced the approved allow/block behavior | `[state and tag/request behavior]` | `[consent evidence]` | `Pass/Fail/Pending` |
| Browser Network | Expected request count, destination, Measurement ID, event name, same `event_id`, types and nine allowlisted parameters | `[request count and redacted payload]` | `[redacted Network capture]` | `Pass/Fail/Pending` |
| GA4 DebugView / Realtime | Intended property received the expected recent event and parameters | `[event/count/parameters]` | `[DebugView or Realtime capture]` | `Pass/Fail/Pending` |
| Processed data | Report/Exploration result is available and supports the approved use, when required | `[result or processing state]` | `[report evidence/follow-up]` | `Pass/Fail/Pending/N/A` |

## 3. Count and destination reconciliation

| Check | Expected | Actual | Result |
|---|---:|---:|---|
| Application authoritative occurrences | `[n]` | `[n]` | `Pass/Fail` |
| Data Layer messages | `[n]` | `[n]` | `Pass/Fail` |
| GTM authoritative Trigger matches | `[n]` | `[n]` | `Pass/Fail` |
| GA4 Event Tag fires | `[n]` | `[n]` | `Pass/Fail` |
| GA4 Network requests | `[n]` | `[n]` | `Pass/Fail` |
| DebugView/Realtime events | `[n]` | `[n]` | `Pass/Fail/Pending` |
| Intended destination only | `[QA/prod stream]` | `[observed destination]` | `Pass/Fail` |
| Duplicate/secondary source | `0` or approved count | `[actual]` | `Pass/Fail` |
| Unique `event_id` reconciliation | One occurrence ID appears once at Application, Data Layer and Network; processed/export check when approved | `[actual]` | `Pass/Fail/Pending` |

Do not call the event verified when counts disagree. Open a Section 08 Defect and Retest Record and link it here.

## 4. Runtime result decision

| Decision | Rule |
|---|---|
| `Pass` | Business state, count, payload, destination, consent and all required downstream checks have matching runtime evidence. |
| `Pending` | Collection evidence passes, but only the documented GA4 processing window remains open; owner, follow-up date and expected check are recorded. |
| `Fail` | Any required layer or count/destination check fails. |
| `Blocked` | Runtime verification cannot start or continue because access, environment, privacy or test-data criteria are unresolved. |
| `N/A` | A downstream check is not required by the approved contract; record the reason. |

**Final result:** `[Pass / Pending / Fail / Blocked]`  
**First failing layer:** `[Application / Data Layer / GTM / Consent / Network / GA4 / Processed data / N/A]`  
**Defect/retest reference:** `[ID / —]`  
**Release decision reference:** `[Section 10 Release Record / —]`

## 5. Artifact index and retention

| Artifact ID | Artifact type | Location | Redacted? | Access owner | Retention/expiry |
|---|---|---|---|---|---|
| `[RV-E-01]` | Application/Data Layer evidence | `[controlled location]` | `Yes/No` | `[owner]` | `[period]` |
| `[RV-E-02]` | GTM Preview/Tag Assistant | `[controlled location]` | `Yes/No` | `[owner]` | `[period]` |
| `[RV-E-03]` | Network request | `[controlled location]` | `Yes/No` | `[owner]` | `[period]` |
| `[RV-E-04]` | GA4 DebugView/Realtime or processed report | `[controlled location]` | `Yes/No` | `[owner]` | `[period]` |

Do not store raw PII, credentials, secrets, unrestricted form input or unapproved request tokens. Keep only the minimum artifact needed to support the decision.

## 6. Review and handoff

| Role | Name | Decision/date |
|---|---|---|
| Tester | `[name]` | `[completed/date]` |
| Application owner | `[name]` | `[review/date]` |
| GTM owner | `[name]` | `[review/date]` |
| Analytics owner | `[name]` | `[review/date]` |
| Privacy reviewer | `[name/N/A with reason]` | `[review/date]` |
| Release approver | `[name/N/A for QA-only]` | `[decision/date]` |

Link this record from the Section 08 Scenario Execution Summary and the Section 10 Release Record. The current FD journey must keep this record at `Not applicable — simulation-only` until a separate runtime project is explicitly authorized.

## 7. Acceptance checklist / template criteria

> This record is a reusable runtime template. Do not mark an item complete from a simulation. Each checked item must reference a retained runtime artifact or an approved decision.

- [ ] Entry criteria, environment and consent state are recorded.
- [ ] Application evidence matches the complete internal snapshot; Data Layer evidence contains only the minimized analytics subset and same opaque `event_id`.
- [ ] GTM Preview/Tag Assistant evidence shows the authoritative Trigger and Tag behavior.
- [ ] Network evidence reconciles request count, payload, destination and consent behavior.
- [ ] GA4 DebugView/Realtime or processed-data evidence is recorded according to the approved processing window.
- [ ] Final result, first failing layer, defect/retest reference and release decision are completed.

**Template status:** `Not started`

**Current journey status:** `Not applicable — simulation-only`
