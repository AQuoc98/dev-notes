# 07 — GA4/GTM Measurement Plan

## 1. Objective, scope, and outputs

### 1.1 Objective

The Measurement Plan defines what the team will measure, why it matters, when an event is valid, and how the implementation will be handed off. It turns a business requirement into an approved event and parameter contract that the Application, Data Layer, GTM, consent controls, and GA4 can implement consistently.

This is a design and approval document. It is not a runtime debug log and it is not a guide for building GA4 Reports, Explorations, or charts.

### 1.2 Scope

- Stable web, client-side collection with Google Tag Manager (GTM) and Google Analytics 4 (GA4).
- Business questions, authoritative business moments, event names, parameters, occurrence rules, deduplication, consent, privacy, destination, ownership, and schema versioning.
- Decisions about key events, custom definitions, identity, cardinality, and data minimization when the requirement needs them.
- Canonical records and implementation handoff for Sections 01–06.
- A worked Registration Journey at the end of this document.

### 1.3 Out of scope

- Runtime test execution, evidence, and pass/fail decisions: see Section 08 — Debug/QA.
- Processed-data analysis, GA4 Reports, Explorations, and chart design: see Section 09 — Reports/Charts.
- Production release monitoring and rollback: see Section 10 — Release Monitoring.
- Ads, campaign setup, attribution optimization, or Google Ads operations.

### 1.4 Outputs

1. An approved plan for each Journey or measurement requirement.
2. One Event Contract and Parameter Dictionary for every canonical event.
3. Data Layer, GTM, consent, destination, owner, and lifecycle references that implementers can follow.
4. Key-event, custom-definition, identity, and data-classification decisions where applicable.
5. A versioned handoff that Sections 08–10 can use without redefining the event meaning.

## 2. Overview: what the plan controls

### 2.1 Measurement chain

~~~text
Business question
→ authoritative business moment
→ event and parameter contract
→ Application publishes a complete Data Layer message
→ GTM maps the approved fields, consent, and destination
→ GA4 receives and processes the event
→ Debug/QA and Reports/Charts use the approved contract
~~~

The Application owns the business truth. The Data Layer carries structured data for GTM. GTM routes and maps approved fields. GA4 receives and processes the request. The plan records the decisions and handoff; it does not replace the runtime validation in Section 08 or the analysis work in Section 09.

### 2.2 Core terms

| Term | Practical meaning |
|---|---|
| Event | A named interaction or business state sent to GA4. |
| Event parameter | A field describing that event, such as method or form_id. |
| Occurrence | One valid business occurrence counted under the event's rules; a click or retry is not automatically a new occurrence. |
| Contract | The shared rules for an event and its fields: meaning, types, allowed values, missing-data behavior, privacy, owner, and version. |
| User property | A relatively stable attribute of a user, not a per-event value. |
| Key event | An event the business explicitly treats as an important outcome. |
| Custom definition | A GA4 dimension or metric registration that makes an approved parameter available for analysis. |
| Schema version | The version of the event and parameter contract; it changes only through review. |

### 2.3 Choose the event type and name

Use the least custom solution that represents the requirement:

1. Use automatically collected events when GA4 already captures the behavior.
2. Check Enhanced Measurement for supported web interactions.
3. Use a Google recommended event when it matches the business moment.
4. Create a custom event only when the previous options do not represent the requirement.

Use lower-case, stable, descriptive names (for example, sign_up or calculation_action). Do not encode values in event names, such as sign_up_email. Put variable values in parameters. Check Google's naming rules, reserved names, and collection limits before approval.

## 3. Measurement-Plan workflow

### 3.1 Define the decision

Record:

- The business question or decision the measurement supports.
- The user action or business state being measured.
- The population or Journey scope.
- The success condition and the intended consumer.
- The business owner and technical owner.

If nobody can state what decision the event supports, do not add the event yet.

### 3.2 Define the authoritative moment

The authoritative moment is the application or backend state that proves the business action occurred. Prefer an application event or a confirmed server result over a click, page view, DOM selector, or route change.

For each event, specify:

- Which state transition creates the event.
- Which source is authoritative (application or server).
- What counts as one valid occurrence.
- How retries, refreshes, remounts, double-submit, cancellation, timeout, and server failure are treated.
- How an idempotency or deduplication rule prevents duplicate business occurrences.

Keep a valid “no result” outcome separate from validation failure, timeout, cancellation, or server failure. A missing required state is not a valid occurrence.

### 3.3 Define the event and parameter contract

For every event, agree on:

- Canonical event name and event type.
- Plain-language definition and valid occurrence.
- Required and optional parameters.
- Parameter meaning, data type, scope, allowed values, source, and expected volume.
- Missing, invalid, and not-applicable behavior.
- Consent category, privacy classification, destination, owner, and schema version.

Required fields must be present before handoff. Optional fields may be omitted only when the contract says so. Do not use a generic value such as unknown to hide an implementation defect.

### 3.4 Define the Data Layer and GTM handoff

The plan must identify the exact application event, Data Layer fields, GTM Variables, Trigger, Tag, environment, consent behavior, and destination.

The application publishes one complete event message at the authoritative moment. GTM reads that message and forwards only the approved scalar fields. Keep internal snapshots or request tokens in application logs for correlation; do not send them to GA4 unless separately approved.

### 3.5 Decide key-event, custom-definition, and identity status

Document the decision, not just the desired outcome:

- Why the event is or is not a key event.
- Whether standard or recommended GA4 fields already meet the need.
- Whether a custom definition is needed for a recurring analysis question.
- Whether the parameter is suitable for a custom definition (controlled values, useful scope, acceptable cardinality).
- Whether User-ID is required under a separate identity contract.
- Owner, approval, status, and effective date.

Do not register every parameter automatically. A parameter can be collected without being registered as a custom definition.

### 3.6 Apply privacy and data minimization

Classify every event and parameter before implementation. Define the allowed destination and behavior when consent is denied.

Do not send email addresses, phone numbers, passwords, payment data, raw free text, unrestricted form input, internal request tokens, or raw account identifiers to GA4 unless a separate approved contract explicitly permits it. Prefer controlled categories and stable IDs that do not identify a person.

### 3.7 Review, approve, and version

Before implementation or a breaking change, review the contract with the business owner, frontend/application owner, analytics owner, privacy/consent reviewer, and GTM owner.

Record:

- Plan and schema version.
- Affected events and parameters.
- Environments and destination streams.
- Effective date, next review date, and status.
- Consumers that need migration.

Any semantic change updates the Event Contract, Parameter Dictionary, Data Layer/GTM mapping, consent decision, and handoff. Retire old fields deliberately; do not silently reuse their meaning.

## 4. Canonical plan records

These records are the source of truth for planning. Section 08 references them when it executes tests, and Section 09 derives analysis requirements from them; neither section should redefine the event contract.

### Record priority and usage order

Do not fill every record at once. Use the smallest set that can approve the requirement, then add implementation and lifecycle records as the work progresses.

| Priority | Record | Use it when | Required? |
|---|---|---|---|
| P0 | Project Context / Baseline | Starting a new product, Journey, environment, or measurement scope. | Always |
| P0 | Journey / Event Coverage Matrix | Turning business questions into a list of candidate events. | Always |
| P0 | Event Contract | Approving an event’s meaning, authoritative moment, occurrence, and destination. | Always for each event |
| P0 | Parameter Dictionary | Approving the fields, types, values, source, privacy, and missing-data behavior. | Always when the event has parameters |
| P1 | Consent / Data Classification | Before any field is exposed to GTM or sent to a destination. | Always for collected data |
| P1 | Key-Event / Custom-Definition Decision Record | When an event may be a key event or a parameter may need GA4 registration. | Conditional; record “Not required” when reviewed and rejected |
| P1 | Traceability Matrix | After the Application/Data Layer/GTM mapping is known and before handoff. | Required before implementation handoff |
| P2 | Schema Lifecycle Register | Adding, modifying, deprecating, or retiring an event or parameter. | Required for every schema change |

Recommended order for a new event:

~~~text
Project Context
→ Journey/Event Coverage
→ Event Contract
→ Parameter Dictionary
→ Consent/Data Classification
→ Key-Event/Custom-Definition decision (if applicable)
→ Traceability
→ Schema Lifecycle entry
~~~

For a change to an existing event, open the current Schema Lifecycle entry first, assess affected consumers, then update the Event Contract, Parameter Dictionary, consent decision, and Traceability Matrix before approving the new version. If the requirement is rejected, stop after recording the decision; do not create GTM assets.

### 4.1 Project Context / Baseline

| Field | Record |
|---|---|
| Plan ID / version | Stable identifier and current version. |
| Product / Journey | Product area and Journey covered. |
| GA4 property / stream | Property and web stream used for the environment. |
| Google tag / Measurement ID | Configured collection destination. |
| GTM container | Container and workspace used for implementation. |
| Platform / source | Web application and collection source. |
| Environments | Local, QA, staging, and production rules. |
| Timezone / currency | Defaults used by the measurement context. |
| Owners | Business, application, analytics, GTM, and privacy owners. |
| Effective / next review | Dates for approval and revalidation. |
| Status | Draft, approved, deprecated, or retired. |

### 4.2 Journey / Event Coverage Matrix

| Journey ID | Journey | Business question | Planned event sequence | Primary outcome | Owner | Status |
|---|---|---|---|---|---|---|
| J-… | … | … | … → … | … | … | Draft/Approved |

Keep one row per Journey or requirement. Add an event only when it supports a stated question.

### 4.3 Event Contract

| Field | Required decision |
|---|---|
| Requirement / Journey ID | Requirement that justifies the event. |
| Business question / decision | What the event will answer. |
| Event name / type | Canonical name and automatic, Enhanced Measurement, recommended, or custom type. |
| Definition | Plain-language meaning. |
| Authoritative moment / source | Application or server state that proves occurrence. |
| Valid occurrence / deduplication | Count, retry, refresh, cancellation, and idempotency rules. |
| Data Layer signal | Exact event and payload path. |
| GTM mapping | Approved Variables, authoritative Trigger, and Tag. |
| Environment / destination | Stream and routing rule. |
| Required / optional parameters | Contractual field list. |
| Consent / privacy | Allowed behavior and data classification. |
| Key-event status | Yes, No, or Pending with rationale. |
| Custom-definition status | Required, Not required, or Pending with rationale. |
| Owner / reviewer / version | Accountability and change control. |

### 4.4 Parameter Dictionary

| Event | Parameter | Meaning | Type / scope | Required? | Allowed values | Missing or invalid | Source | Privacy / consent | Cardinality / volume | GA4 registration |
|---|---|---|---|---|---|---|---|---|---|---|
| … | … | … | … | Yes/No | Controlled list | Omit, reject, or handoff fail | Application/Data Layer | … | Low/Medium/High | Standard/Custom/Not registered |

“GA4 registration” is a planning decision. It does not describe how to build a report.

### 4.5 Traceability Matrix

| Requirement / event | Application state | Data Layer | GTM | Consent | Destination | Owner / status |
|---|---|---|---|---|---|---|
| … | … | … | … | … | … | … |

### 4.6 Key-Event / Custom-Definition Decision Record

~~~text
Decision ID:
Event or parameter:
Requirement / Journey ID:
Business question:
Success condition and valid occurrence:
Deduplication rule:
Key event: Yes / No / Pending
Standard or recommended field checked:
Custom definition: Required / Not required / Pending
Cardinality and quota review:
Consent and privacy impact:
Owner / approver:
Effective date and status:
~~~

This record governs classification and approval. Runtime validation belongs to Section 08; report or Exploration design belongs to Section 09.

### 4.7 Consent / Data Classification

| Event / parameter | Classification | Consent requirement | Denied behavior | Destination | Owner / status |
|---|---|---|---|---|---|
| … | Analytics / Sensitive / Restricted | … | Suppress, reduce, or approved alternative | QA/Production stream | … |

Use Section 05 for consent-mode implementation details.

### 4.8 Schema Lifecycle Register

| Change ID | Event / parameter | Current version | Proposed version | Change type | Affected consumers | Migration / handoff action | Approval / effective date | Status |
|---|---|---|---|---|---|---|---|---|
| … | … | v… | v… | Add/Modify/Deprecate | Application, GTM, GA4, analysis | … | … | Proposed/Approved/Retired |

## 5. Implementation handoff and practical notes

### 5.1 Handoff minimum

| Plan item | Application / Data Layer | GTM object | Destination |
|---|---|---|---|
| Event name | Publishes the complete approved message | One authoritative Custom Event Trigger and one GA4 Event Tag | Approved QA or production stream |
| Parameter | Uses the approved type and value set | Data Layer Variables map only approved fields | GA4 event parameters |
| Consent | Exposes no restricted data when consent is denied | Consent checks follow Section 05 | Allowed collection behavior |
| Version | Follows the active contract | Workspace/change references the plan version | Same semantic version in handoff |

### 5.2 Cardinality and data minimization

Cardinality is the number of distinct values a field can produce. High-cardinality free text is difficult to analyze and can create noisy GA4 data. Prefer short, controlled categories and omit values that are not required. Create a custom definition only when the business has a recurring analysis need and the value set is stable.

### 5.3 Ecommerce addendum

For ecommerce, define the business moment, authoritative transaction ID, retry and deduplication rule, numeric value, currency, item schema, and reconciliation owner. Keep ecommerce semantics in the event contract and use the approved Data Layer schema from Section 01.

### 5.4 Keep planning, QA, and reporting separate

| Document | Main question | Output |
|---|---|---|
| Measurement Plan (Section 07) | What should be measured and what does it mean? | Approved contract and handoff. |
| Debug/QA (Section 08) | Did the runtime produce the approved event, payload, consent, destination, and count? | Evidence and pass/fail result. |
| Reports/Charts (Section 09) | How should processed GA4 data answer the approved question? | Report, Exploration, or chart specification. |

The plan may record reportability or custom-definition status, but it should not contain QA procedures or chart recipes. Keeping these documents separate prevents a test scenario or a visualization from becoming a second, conflicting event definition.

### 5.5 Anti-patterns to reject

| Anti-pattern | Corrective rule |
|---|---|
| Track every click or DOM change | Track the approved business moment. |
| Fire success on a click before confirmation | Wait for the application or server state that proves success. |
| Encode values in event names | Keep one stable event name and use parameters. |
| Send raw form input or PII | Classify, minimize, and suppress unless separately approved. |
| Register every parameter as a custom definition | Register only recurring, approved analysis fields. |
| Duplicate automatic, Enhanced Measurement, and custom collection | Choose one authoritative source and document exceptions. |
| Rename or reuse a field silently | Version, migrate, and retire through the lifecycle register. |

## 6. Cross-reference map

| Section | Use it for |
|---|---|
| 01 — Data Layer Design | Message shape, event payload, and application-to-GTM boundary. |
| 02 — Variable Management | Variable naming, scope, reuse, and value validation. |
| 03 — Trigger Management | One narrow, authoritative trigger for the approved event. |
| 04 — Tag Management | GA4 Event Tag, configuration, sequencing, and destination mapping. |
| 05 — Consent | Consent state, denied behavior, and consent checks. |
| 06 — Template Governance | Reusable templates, ownership, review, and deployment records. |
| 08 — Debug/QA | Execute tests and collect evidence against this contract. |
| 09 — Reports/Charts | Build analysis views from the processed event and parameter definitions. |
| 10 — Release Monitoring | Monitor production changes after approved deployment. |

## 7. Worked Journey: Registration

This example shows only planning decisions. Use Section 08 to test the implementation and Section 09 to design analysis views.

### 7.1 Project context

~~~text
Plan ID: REG-MP-001
Version: 1.0
Product: Web registration
Platform: Client-side web application
Environments: QA stream first; production after approval
Status: Approved for implementation
~~~

### 7.2 Journey / Event Coverage

| Journey ID | Business question | Planned sequence | Primary outcome | Owner / status |
|---|---|---|---|---|
| J-REG-001 | Which registration methods complete successfully? | registration_start → registration_error (when applicable) → sign_up | sign_up | Product + Analytics / Approved |

### 7.3 Event Contract summary

| Event | Type | Authoritative moment | Valid occurrence | Required parameters |
|---|---|---|---|---|
| registration_start | Custom | Application has accepted the registration method and form is ready | Once per intended registration attempt; no remount duplicate | form_id, method |
| registration_error | Custom | Application classifies and displays an approved registration error | Once per visible error; retries may create a new occurrence | form_id, method, error_type |
| sign_up | Recommended | Backend confirms account creation for the submitted registration | Once per created account; no retry or refresh duplicate | form_id, method |

### 7.4 Parameter Dictionary

| Event | Parameter | Type | Allowed values | Missing or invalid behavior | GA4 registration |
|---|---|---|---|---|---|
| registration_start | form_id | String | Approved form IDs | Handoff fail; do not invent a value | Not registered unless needed |
| registration_start | method | String | email, google, apple | Handoff fail | Custom dimension only if approved |
| registration_error | form_id | String | Approved form IDs | Handoff fail | Not registered unless needed |
| registration_error | method | String | email, google, apple | Handoff fail | Custom dimension only if approved |
| registration_error | error_type | String | validation, server_error, other approved categories | Omit or reject per contract | Custom dimension only if recurring |
| sign_up | form_id | String | Approved form IDs | Handoff fail | Not registered unless needed |
| sign_up | method | String | email, google, apple | Handoff fail | Custom dimension only if approved |

Do not send email, phone, password, raw form content, request tokens, or raw account identifiers to GA4.

### 7.5 Mapping, consent, and destination

| Plan item | Approved decision |
|---|---|
| Data Layer | Application pushes one complete message for each authoritative event. |
| GTM | One Custom Event Trigger per event and one GA4 Event Tag; map only form_id, method, and approved error_type. |
| Consent | Follow the approved analytics behavior from Section 05; suppress or reduce data when consent is denied. |
| Destination | QA stream during validation; production only after approval. |
| Key event | sign_up: Pending until the business owner approves the success definition. |
| Custom definition | method: Pending; register only if recurring analysis requires it. |
| Identity | No User-ID unless a separate identity contract is approved. |

### 7.6 Registration schema lifecycle

~~~text
Change ID: REG-CHG-001
Event/parameter: sign_up.method
Current version: v1
Proposed version: v1
Change type: Initial approval
Affected consumers: Application, Data Layer, GTM, GA4, analysis
Migration action: None; implement the approved value set
Approval owner: Product + Analytics
Effective date: After QA approval
Status: Approved
~~~

## Official references

- Google Analytics — About events: https://support.google.com/analytics/answer/9322688
- Enhanced Measurement: https://support.google.com/analytics/answer/9216061
- Recommended events: https://developers.google.com/analytics/devguides/collection/ga4/reference/events
- Custom events: https://support.google.com/analytics/answer/12229021
- Event naming rules: https://support.google.com/analytics/answer/13316687
- Event parameters: https://support.google.com/analytics/answer/13594907
- Event collection limits: https://support.google.com/analytics/answer/9267744
- Custom dimensions and metrics: https://support.google.com/analytics/answer/14240153
- User-ID: https://support.google.com/analytics/answer/9213390
- GTM Custom Event trigger: https://support.google.com/tagmanager/answer/7679219
- Avoid sending personally identifiable information: https://support.google.com/analytics/answer/6366371
