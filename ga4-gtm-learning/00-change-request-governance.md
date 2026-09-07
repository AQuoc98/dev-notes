# 00 — FD Change Request Governance for GA4/GTM

## 1. Purpose and scope

This document is the project-control layer for the FD calculation journey. It governs FD request intake, ownership, risk, impact assessment, traceability, evidence, release handoff, monitoring, and closure.

It applies the research and reference material in Sections 01–10 to one FD project. Those sections contain knowledge, reusable templates, record structures, and examples; they are not project records.

It does not define the technical implementation of Variables, Triggers, Tags, consent, templates, reports, or runtime tests. Sections 01–10 provide the reusable standards and record structures; project-specific decisions belong in the applicable `FD-REC-*` records.

> **Project application:** This governance template is applied to the FD calculation journey. Sections 01–10 remain research/reference documents; concrete FD project records live under fd-calculation-records/ and are coordinated by [11 — FD calculation journey](11-fd-calculation-journey.md). Do not add FD Change Request IDs to the research files themselves.

## 2. Required Change Request Record

Create one Change Request Record before approving a governed design or starting implementation. A simulation/documentation-only change also needs a CR when it changes the current design baseline. Every affected record must link every applicable CR and identify which one governs its current state.

| Field | Required content |
|---|---|
| Change Request ID | Stable unique ID, for example CR-001. |
| Project / journey | FD web application / calculation_action / journey ID. |
| Requester | Person or team requesting the change. |
| Business owner | Person accountable for the business outcome. |
| Technical owner | Person accountable for implementation coordination. |
| QA owner | Person accountable for validation. |
| Approver/publisher | Person accountable for approval and publication. |
| Change mode | Runtime implementation, simulation/design, documentation-only, or incident response. |
| Request type | New, Modify, Deprecate, Retire, Hotfix, Consent, Routing, Report-only, Documentation-only, Composite/Umbrella, or Incident. |
| Risk level | Low, Medium, or High, with rationale. |
| Business purpose | Decision or outcome the change must support. |
| Acceptance criteria | Observable conditions for approval and closure. |
| GA4 scope | Property, web stream, Measurement ID, key-event or custom-definition impact. |
| GTM scope | Account, container, workspace, environment, and affected assets. |
| Application scope | Product, journey, URL/hostname, application version, and build. |
| Change summary | What will change and what will remain unchanged. |
| Change reading instruction | For each CR linked from each record, explicitly mark `Required — affected`, `Context only — not changed`, `Historical lineage — superseded`, or `Not applicable`, with a reason. |
| Version transition / before-after state | Historical baseline, current design state, proposed state, and contract/schema version before and after the change. |
| CR lineage | `Supersedes`, `Superseded by`, current governing CR, and any historical-only CR links. |
| Runtime baseline verification | Observed deployed Application build, GTM version, GA4 destination/schema and evidence timestamp, or an explicit statement that no live baseline is verified. |
| Effective release / migration boundary | Named release, effective date, and how pre-change and post-change data are handled. |
| Record update order | Ordered list of project records that must be updated after approval and before closure. |
| Historical comparability / rollback decision | Whether old and new values may be compared or merged, and which coordinated state must be restored on rollback. |
| Impact assessment | Affected events, parameters, consumers, consent, reports, data quality, and historical comparability. |
| Linked records | Affected `FD-REC-*` project records, governing Section 01–10 references and external ticket/reference. |
| Evidence location | Sanitized evidence location, access restriction, and retention period. |
| Deployment-path decision | Direct deployment, compatibility migration, report/document-only path, or N/A; the choice must follow runtime-baseline evidence. |
| Release plan | Target environment, named version, release window, smoke test, and rollback/containment path for the selected deployment path. |
| Monitoring plan | Signal, baseline, threshold, observation window, owner, and escalation path. |
| Open decisions/blockers | Decision ID, owner, exit condition, target/review date and effect on design or runtime approval. |
| Document status and dates | Draft/review/design approval/documentation completion/supersession plus created, target and documentation-completion dates. |
| Runtime status and dates | Not planned/not started/blocked/approved/in QA/published/monitoring/closed plus publish and runtime-closure dates. |

## 3. Document and runtime lifecycles

Track two independent statuses. A completed design document is not evidence that the change was implemented, and a superseded CR is not evidence that its target schema ever ran.

### 3.1 Document lifecycle

```text
Draft → Review → Approved — design → Documentation complete → Superseded
```

| Document status | Meaning |
|---|---|
| Draft | The request, scope or before/after state is being clarified. |
| Review | Impact, ownership, privacy, acceptance and record-update order are under review. |
| Approved — design | The proposed design may become the current design baseline; this does not authorize runtime work. |
| Documentation complete | The CR and affected simulation/design records are internally consistent and linked. |
| Superseded | A later approved CR owns the current design. Retain this CR as immutable historical lineage and link its successor. |

### 3.2 Runtime lifecycle

```text
Documentation-only: Not planned
Runtime intended: Not started → Blocked or Approved for implementation
                  → In QA → Published → Monitoring → Closed
```

| Runtime status | Meaning |
|---|---|
| Not planned | Documentation-only or simulation work has no authorized runtime scope. |
| Not started | Runtime work is intended but has not begun. |
| Blocked | A required decision, access, baseline, evidence, privacy condition or material defect prevents progress. |
| Approved for implementation | Required owners approved the selected runtime scope and deployment path. |
| In QA | An identified Application/GTM version exists in an approved test environment and is being validated. |
| Published | The named version was published to the intended environment. |
| Monitoring | Post-publish observation and processed-data checks are open. |
| Pending | Only a documented dependency remains, with owner, due date and expected check. |
| Exception | A non-blocking residual risk was explicitly accepted with owner, mitigation, reviewer and expiry/review date. |
| Closed | Runtime acceptance, evidence, monitoring, affected-period assessment and follow-up are complete. |

Rules:

- Never use `Approved — design` as authorization to implement or publish.
- A simulation CR may be `Documentation complete` while runtime is `Not planned`, `Not started` or `Blocked`.
- `Superseded` applies to document/contract lineage; record separately whether the superseded version was ever deployed.
- Do not leave a published runtime change in `Approved for implementation` or `In QA`.
- Do not set runtime status to `Closed` while a blocking defect, missing processed-data check or unowned follow-up remains.

## 4. Required impact assessment

Before design approval or implementation, answer each item. Use N/A with a reason when a layer is not affected.

- [ ] Application or Data Layer changes.
- [ ] Event name, business meaning, occurrence rule, or schema version changes.
- [ ] Required or optional parameter, data type, allowed value, or missing-data behavior changes.
- [ ] GTM Variable, Trigger, Tag, exception, sequencing, or workspace changes.
- [ ] Consent default, update, revoke, CMP, policy, or privacy classification changes.
- [ ] Template source, version, permission, endpoint, or consumer changes.
- [ ] GA4 property, web stream, Measurement ID, key event, custom definition, identity, or data-filter changes.
- [ ] Report, Exploration, chart, metric, dimension, scope, population, or interpretation changes.
- [ ] Duplicate, overlap, routing, request-count, or legacy collection-path risk.
- [ ] Regression scope for adjacent journeys, browsers, routes, or environments.
- [ ] Historical comparability and affected-period impact.
- [ ] Rollback or containment limitations.
- [ ] Current design baseline versus verified live baseline, including evidence that the prior schema is or is not deployed.
- [ ] Direct-deployment versus compatibility-migration path and the matching rollback order.
- [ ] Superseded CRs, current governing CR and downstream records that retain historical lineage.

The request must identify the first layer expected to change and the downstream layers that require validation. A change that affects only documentation or report layout must be explicitly classified as report-only or documentation-only.

A Composite/Umbrella CR is allowed only when its changes share one business objective, schema transition, approval set and release boundary and must be deployed atomically. If a component can be approved, released, rolled back or closed independently, create a separate CR and link it as a dependency instead.

## 5. Traceability rules

Maintain one reference chain:

Change Request
  → predecessor/successor CR lineage and current governing contract
  → approved requirement or Measurement Plan
  → selected FD project records based on Sections 01–06
  → FD-REC-09 report/configuration impact
  → verified live baseline and selected deployment path
  → FD-REC-08 QA evidence and defect/retest records
  → FD-REC-11 end-to-end runtime verification when a real run exists
  → FD-REC-10 Release and Monitoring Records
  → closure decision and affected-period assessment

Use the governing Change Request ID in filenames, ticket links, release notes, evidence folders and monitoring records. When a project record has been changed by more than one CR, retain all applicable IDs and clearly mark the current governing CR; do not overwrite historical lineage with the latest ID.

The Section 01–10 files are referenced sources. The Change Request ID belongs in the FD project records and their evidence, not in the research files.

### 5.1 Contract-state and runtime-baseline model

Do not use “current” without naming which state it describes:

| State | Meaning | Evidence |
|---|---|---|
| Historical design baseline | An older documented schema/decision retained for interpretation | Prior CR and record version history |
| Current design baseline | The latest design approved for documentation and downstream planning | Governing CR and current `FD-REC-*` records |
| Proposed target | A change still in Draft/Review and not yet the design baseline | Open CR before/after section |
| Verified live baseline | What the target environment is actually running now | Application build, GTM version, GA4 destination/schema, timestamp and runtime evidence |
| Published target | The named version observed after release | Release record, smoke test and monitoring evidence |

The deployment path must follow the verified live baseline:

- If no live event contract exists, use a controlled direct deployment of the approved target.
- If an older live contract is verified, define compatible Application/GTM states, a migration window and a coordinated rollback.
- If the live baseline is unknown, runtime status is `Blocked`; do not assume the latest documented simulation was deployed.

### 5.2 Change versioning and record navigation

Use three layers of traceability:

- The CR chain owns why versions changed, which CR is current, which CR was superseded and whether each version was ever deployed.
- The current Change Request owns purpose, before/after design, impact, approval, baseline evidence, deployment path, release, monitoring and closure.
- Each affected FD record owns the current design state for its layer. It links the governing CR and relevant historical CRs, shows its record/schema version and retains a short version history or before/after note.

Keep the stable record ID, such as `FD-REC-02`, while changing its record version. Do not create a new record file for every small change. A new project or a separately governed major baseline may use a copied record packet.

Before design approval, keep the current design state unchanged and put the proposal in the CR/draft records. After design approval, affected records may show the new **current design state** while runtime remains unchanged. Only after publication and verification may the release record identify the target as the **published runtime state**. Retain older states as historical context.

Do not make a new team member infer whether a linked Change Request is relevant from the ID alone. Every project record that links to a Change Request must show a **Change reading status**:

| Change reading status | Meaning for a new reader |
|---|---|
| **Required — affected** | Open this CR before relying on the current/proposed state, implementing the record, reviewing QA, approving a release or interpreting its affected period. The record's contract, behavior, schema or acceptance expectation changed. |
| **Context only — not changed** | The record is linked so the reader can understand the project-wide impact, but its own decision/state did not change. Open the Change Request only when reviewing the overall change or its dependency. |
| **Historical lineage — superseded** | This CR explains an older state but does not govern current implementation. Follow its `Superseded by` link before acting. |
| **Not applicable** | The Change Request does not affect this record. The reader does not need to open it for normal use; the record must state the reason for the N/A classification. |

The record must also include a short **Why this matters** sentence. If it links multiple CRs, use one row per CR with CR ID, reading status and reason; do not use one aggregate status for all links. The direct link is the navigation mechanism; the per-CR status and reason are the decision mechanism.

Each section owns its layer-specific decision:

| Section | Owns |
|---|---|
| 01 | Application/Data Layer contract and schema handoff. |
| 02 | Variable source, value behavior, consumers, and lifecycle. |
| 03 | Trigger source, filters, overlap, and firing behavior. |
| 04 | Tag mapping, destination, consent setting, and request behavior. |
| 05 | Consent contract, state transitions, and privacy behavior. |
| 06 | Template source, permissions, endpoints, version, and rollback/export. |
| 07 | Business meaning, event contract, parameter approval, and schema lifecycle. |
| 08 | Runtime test result, evidence, defects, and retest. |
| 09 | Report requirement, field readiness, asset configuration, and interpretation. |
| 10 | Release decision, monitoring, incident, rollback, and closure. |

## 6. Evidence and data-safety rules

- [ ] Use the smallest evidence set that proves the required decision.
- [ ] Identify environment, application/build, GTM version, GA4 property/stream, browser/device, tester, reviewer, and timestamp.
- [ ] Separate configuration evidence from runtime delivery evidence.
- [ ] Mark simulated examples as simulated; never present them as runtime results.
- [ ] Use synthetic/test data and approved test identities.
- [ ] Redact PII, credentials, secrets, raw form values, tokens, and unrestricted user input.
- [ ] Record known limitations and processing-window follow-ups.
- [ ] Restrict access to evidence according to project policy.

## 7. Risk and approval baseline

| Risk | Typical change | Minimum control |
|---|---|---|
| Low | Documentation or report layout with no collection impact. | Owner review and applicable record. |
| Medium | Variable, Trigger, Tag, routing, or non-breaking parameter change. | Impact assessment, Section 08 QA, named version, approval, smoke test, and short monitoring. |
| High | Event/schema meaning, consent/privacy, destination, key event, property setting, permanent filter, or material data-quality change. | Cross-functional approval, full QA, safe production smoke test, rollback/containment owner, monitoring, and processed-data validation. |

The project may adjust this baseline, but any stricter rule must be recorded in the Change Request Record.

For simulation/design work, document the controls required by the risk level but keep runtime status `Not planned`, `Not started` or `Blocked`. Execute those controls only after runtime authorization; do not mark them complete from a paper review.

## 8. Definitions of done

### 8.1 Documentation/design completion

A CR may be marked `Documentation complete` only when:

- [ ] Purpose, before/after state, risk, owners and acceptance criteria are complete and reviewed.
- [ ] Impact assessment is complete, including unaffected layers marked N/A with a reason.
- [ ] The current governing CR, superseded CRs and current design schema are explicit.
- [ ] Every affected record links the CR with a per-CR reading status and reason.
- [ ] The record update order, report impact, QA plan, release-path decision rules, monitoring fields and rollback alternatives are documented.
- [ ] Simulated values and missing runtime evidence are clearly labelled.
- [ ] Blocking decisions have owners, exit criteria and target/review dates.

Documentation completion does not authorize implementation and must not be reported as runtime `Closed`.

### 8.2 Runtime closure

A runtime CR may be marked `Closed` only when:

- [ ] The verified live baseline and selected deployment path are recorded with evidence.
- [ ] Required positive, negative, duplicate, consent, privacy, routing, migration/direct-deployment and regression tests pass.
- [ ] The intended Application build, GA4 property/stream, GTM version, environment and destination are verified.
- [ ] Production smoke testing is complete when required.
- [ ] Monitoring has an owner, baseline or expected range, threshold, observation window and escalation path.
- [ ] Required processed-data validation is complete, or a valid Pending item has an owner, due date and expected check.
- [ ] Defects, exceptions, residual risk, rollback/containment result and affected period are recorded.
- [ ] Release and Monitoring Records are closed or linked to an approved follow-up.
- [ ] Publish and runtime-closure dates are recorded.

A documentation-only CR uses runtime status `Not planned`. A simulation CR that could later be implemented uses `Not started` or `Blocked`; a future runtime project must revalidate the baseline and approvals rather than inheriting a simulated release decision.

## 9. Reusable Change Request template

Change Request ID:

Change mode (runtime / simulation-design / documentation-only / incident):

Requester:

Business owner:

Technical owner:

QA owner:

Approver/publisher:

Request type:

Composite/Umbrella scope and atomicity reason, or N/A:

Risk level and rationale:

Business purpose:

Acceptance criteria:

GA4 property/stream/Measurement ID:

GTM account/container/workspace/environment:

Application/journey/URL/build:

Change summary:

Change reading instruction for each linked record and each CR (Required / Context only / Historical lineage / N/A) and reason:

Historical design baseline / version:

Current design baseline / governing CR:

Proposed state / target version:

Supersedes / Superseded by:

Verified live baseline (Application build / GTM version / GA4 destination-schema / timestamp / evidence), or `No verified live baseline`:

Effective release / migration boundary:

Deployment path (direct / compatibility migration / report-document only / N/A) and evidence-based rationale:

Record update order:

Historical comparability / rollback decision:

Impact assessment:

Linked `FD-REC-*` project records and governing Section 01–10 references:

Evidence location/access/retention:

Target release/version/window:

Smoke-test method:

Rollback or containment path:

Monitoring signal/baseline/threshold/window/owner/escalation:

Open decisions/blockers (ID / owner / exit condition / target or review date / affected lifecycle):

Document status:

Runtime status:

Created/target/documentation-complete/published/runtime-closed dates:

Open follow-up, owner, and due date:

## 10. Section handoff checklist

Before handing work from one section to another:

- [ ] The governing Change Request ID and relevant historical CR IDs are present in the linked records.
- [ ] Each linked CR has its own reading status and reason; no aggregate status hides which CR is current.
- [ ] The current requirement, design version, verified live version, owner, document status and runtime status are clear.
- [ ] Supersedes/Superseded-by links and historical interpretation are complete.
- [ ] The deployment path follows verified runtime-baseline evidence; unknown baseline blocks runtime handoff.
- [ ] Expected behavior and acceptance criteria are not redefined downstream.
- [ ] Evidence links are sanitized and accessible to the next owner.
- [ ] Unaffected layers are marked N/A with a reason.
- [ ] Pending, Blocked and Exception items have owners, exit criteria and dates.
- [ ] The next section's required input is complete.

## 11. How to use this document in the FD journey

Use this document as the control sheet for one governed FD change, including simulation/design changes that alter the current design baseline. Open or update a Change Request when a requirement, event contract, application mapping, Data Layer value, GTM Variable/Trigger/Tag, QA expectation, report/configuration, release or monitoring behavior changes.

The research documents in Sections 01–10 provide knowledge, reusable templates, record structures, and examples. They are reference material, not project records. Project-specific decisions, owners, statuses, evidence, and approvals belong in the FD records and are coordinated by this document.

### Recommended workflow

1. **Open one Change Request for one logical change.** Assign a unique ID such as `FD-CR-001`; record the mode, requester, reason, scope, target release, owner, document status and runtime status. Use a Composite/Umbrella CR only when the included changes are atomic under one schema/release boundary.
2. **Describe the before/after behavior.** State the current behavior, proposed behavior, affected FD journey, and acceptance criteria in observable terms.
3. **Classify impact and risk.** Select the change type, mark High/Medium/Low risk, and list every affected layer. If a layer is not affected, write `N/A` and explain why.
4. **Use Sections 01–10 as the reference library.** Select only the templates, knowledge, record structures, or examples needed to assess and implement the change. Do not copy the CR ID into the research documents.
5. **Route the change through the FD records.** Update the relevant `FD-REC-*` records, retain historical CR links, mark the current governing CR, assign reviewers and attach sanitized evidence when it exists. A typical sequence is contract → application/Data Layer → consent → GTM → report/configuration → QA → release and monitoring.
6. **Verify the live baseline and select the deployment path.** Do not infer that a simulated or approved design is deployed. Choose direct deployment when no live contract exists; use a compatibility migration only when an older live contract is evidenced.
7. **Review, approve, and execute.** Design approval updates the design baseline only. Runtime implementation requires separate authorization and resolved blockers.
8. **Complete the correct lifecycle.** Mark documentation complete when the design DoD passes. Mark runtime closed only after QA, release, monitoring, affected-period and processed-data requirements pass.

### Rules that prevent common mistakes

- Keep project-specific values and decisions in `fd-calculation-records/`; keep Sections 01–10 reusable.
- Never silently change an approved event value, required field, occurrence rule, or allowed-value list. Open a new CR or a clearly versioned amendment.
- Do not mark runtime QA as `Pass` when the test has only been simulated or reviewed on paper.
- Do not call a design “current” without distinguishing current design from verified live state.
- Do not remove a superseded CR from record history, and do not implement from it without following its successor link.
- Do not use one aggregate reading status when a record links multiple CRs.
- Use the CR as the navigation point: a reviewer should be able to move from the request to the affected records, evidence, release, and monitoring result.

## 12. Worked example — Change `solution_found` from `No` to `No_solution`

This is the historical simulation example represented by [`FD-CR-001`](fd-calculation-records/FD-CR-001-solution-found-value-change.md), not the current implementation contract and not a request to change the research documents. The product team decides that the combined value `No` is too vague and should become `No_solution`. Error remains part of the combined no-output/error class; splitting it into a separate `Error` value requires another Change Request. In the current FD journey, `FD-CR-001` is superseded by [`FD-CR-002`](fd-calculation-records/FD-CR-002-runtime-readiness-hardening.md) and schema `3.0`.

### Change Request summary

| Item | Example decision |
|---|---|
| Change Request ID | `FD-CR-001` (example only) |
| Project / journey | FD `calculation_action` |
| Change mode | Simulation/design; runtime was not executed |
| Before | `solution_found = No` |
| After | `solution_found = No_solution` |
| Change type | Modify / Schema and semantic value |
| Risk | High — the allowed-value vocabulary and downstream report logic change |
| Reason | Make the no-solution outcome explicit and reduce ambiguity for analysts and reviewers |
| Minimum acceptance criteria | `Yes` remains unchanged; valid no-output/error terminal cases emit `No_solution` under the approved combined semantics; the old `No` value is not emitted after release; reports, QA expectations, and monitoring use the new value; invalid, duplicate, consent and routing behavior remains correct |
| Document/runtime status | Documentation complete and later superseded / `Not started — never executed` |

### Impact assessment and execution plan

| Layer | FD project action | Reference |
|---|---|---|
| Contract and lifecycle | Update the parameter dictionary, allowed values, semantics, version, and approval history. Record the old value and effective release. | Section 07 / `FD-REC-07` |
| Application and Data Layer | Update the mapping that emits `No_solution`; verify that invalid data is not emitted and that empty/error outcomes follow the approved combined semantics. | Section 01 / `FD-REC-01` |
| GTM Variable and Tag | Update the approved-value validation and GA4 parameter mapping. | Sections 02 and 04 / `FD-REC-02`, `FD-REC-04` |
| Trigger | Update the schema filter from `1.0` to `2.0` even though the authoritative firing moment is unchanged. | Section 03 / `FD-REC-03` |
| Consent | Mark `N/A` with a reason if consent behavior and tag blocking are unchanged. | Section 05 / `FD-REC-05` |
| Template | Mark `N/A` with a reason if no custom template or template permission changes. | Section 06 / `FD-REC-06` |
| QA and evidence | Update expected values and negative cases; execute runtime tests for happy path, no-solution, invalid, duplicate, consent, routing, and processed-data checks. | Section 08 / `FD-REC-08` |
| Reports and configuration | Update dimensions, filters, calculated fields, formulas, dashboards, and documentation that distinguish `Yes` from `No_solution`. | Section 09 / `FD-REC-09` |
| Release and monitoring | Verify the live baseline first. Use direct deployment if no live v1 contract exists, or mutually exclusive v1/v2 compatibility when live v1 is evidenced; publish a named version, smoke test, monitor and document rollback/affected period. | Section 10 / `FD-REC-10` |

### Closure decision

For this simulation, mark the CR `Documentation complete` only after affected records and historical interpretation are consistent; keep runtime `Not started` because no release evidence exists. A real implementation may be marked runtime `Closed` only when the selected deployment path, QA, intended destination, smoke test, monitoring window, affected-period assessment and processed-data checks pass. When a later CR replaces the design, mark document status `Superseded`, link the successor and retain whether the older schema was ever deployed.
