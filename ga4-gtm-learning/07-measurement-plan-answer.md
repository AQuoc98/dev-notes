# 07 — GA4/GTM Measurement Plan

## 1. Overview

### Purpose

A Measurement Plan is the contract between a business question and the data the team will collect, route, validate, and report. It defines what must be measured, when the event is true, which fields are allowed, who owns the signal, and how another person can verify it.

Start with the business outcome, not with a GTM tag or a list of clicks.

For a normal frontend/GTM change, the minimum contract contains:

- requirement and journey IDs;
- business meaning and authoritative moment;
- event name, required/optional parameters, types, and allowed values;
- occurrence and deduplication rule;
- Data Layer source and GTM mapping;
- consent/privacy behavior and destination;
- reporting/custom-definition decision;
- QA, owner, version, and review date.

### Scope

This section focuses on stable web client-side collection through GTM and GA4. It supports frontend developers, GTM owners, analytics reviewers, and QA. Advertising activation, campaign optimization, attribution strategy, and Google Ads operations are outside the core plan.

### Measurement chain

```text
Business question
  → authoritative business moment
  → event and parameter contract
  → application/Data Layer signal
  → GTM mapping and destination
  → consent/privacy decision
  → QA evidence
  → report or downstream consumer
```

### Core terms

| Term | Practical meaning | Planning decision |
| --- | --- | --- |
| Event | A business action or outcome | Define when it becomes true and how often it may occur |
| Event parameter | Detail attached to one event occurrence | Define meaning, type, allowed values, scope, and missing-value behavior |
| User property | A relatively stable attribute of a user | Define source, update/unset behavior, consent, and scope |
| Dimension | A field used to group or filter data | Use a controlled value set and register it only when reporting needs it |
| Metric | A number that can be counted, summed, or calculated | Define unit, source, aggregation, and validation |
| Key event | An event marked as important in GA4 | Mark only after the event contract and collection QA are correct |

Collection and reporting are separate. A parameter can be collected without being registered as a custom definition, and a registered definition does not repair a bad payload.

### Choose the event type

Use this order before creating a new GTM event:

1. Check whether GA4 already collects the interaction automatically.
2. Check whether Enhanced Measurement covers it in the current web stream.
3. Check whether a recommended GA4 event matches the business meaning.
4. Create a custom event only when the previous options do not fit.

Use the official [event naming rules](https://support.google.com/analytics/answer/13316687), [recommended events](https://support.google.com/analytics/answer/9267735), and [collection limits](https://support.google.com/analytics/answer/9267744) as the current authority. Keep event names lowercase `snake_case`, stable, and independent of changing business values.

### Collection truth versus reporting truth

| Checkpoint | It proves | It does not prove |
| --- | --- | --- |
| Application | The product knows the business state | Analytics received it |
| Data Layer | The state and payload were exposed to GTM | GTM sent the correct request |
| GTM | The trigger/tag evaluated and attempted collection | GA4 processed every field for reporting |
| DebugView/Realtime | GA4 received a recent diagnostic signal | Historical processing or final report availability |
| Report/Exploration | GA4 processed data into a usable view | The original implementation was correct without upstream evidence |

## 2. Measurement-Plan workflow

Work through these steps in order. Each step should produce a decision that the next owner can use without guessing.

### Step 1 — Define the decision

Write a question that can change a product, technical, or operational action. Record the decision owner, population, success criterion, and expected use of the result. Do not create events only to produce vanity counts.

### Step 2 — Identify the authoritative business moment

Describe the state transition that makes the event true. Prefer the application or backend result over a click, DOM label, route, or visual confirmation. Record the success condition, invalid/failure behavior, retry behavior, refresh behavior, and deduplication rule.

### Step 3 — Select the event name and type

Use an existing automatic, Enhanced Measurement, or recommended event when its meaning matches. Otherwise define one custom event with a stable business definition. Do not encode a changing value into the event name; keep that value as a controlled parameter.

### Step 4 — Define parameters and schema

For every parameter, record:

- canonical name and meaning;
- source of truth and owner;
- type and scope;
- required or optional status;
- allowed values and normalization;
- missing/invalid behavior;
- consent/privacy classification;
- expected cardinality and volume;
- reporting or export destination.

Fail QA for a missing required value. For an optional value, document whether it is omitted or handled by an approved fallback. Do not use a generic `unknown` value to hide a broken source.

Check current reserved names, prefixes, lengths, parameter counts, item limits, and primitive types against the official [event naming rules](https://support.google.com/analytics/answer/13316687) and [collection limits](https://support.google.com/analytics/answer/9267744). Re-check them before implementation because platform limits can change.

Add a schema version only when the team can operate and interpret it. Otherwise version the Measurement Plan and inventory rather than adding unused metadata to every event.

### Step 5 — Define Data Layer and GTM mapping

Specify the exact application signal, Data Layer path, GTM variable/trigger/tag, Google tag configuration, environment routing, and destination. The application should publish the event and its values together whenever possible. GTM should route the approved signal, not infer business success from fragile DOM state or raw form fields.

### Step 6 — Decide key-event and custom-definition status

For a proposed key event or custom dimension/metric, record the business reason, owner, success condition, consent/privacy impact, expected volume, and consumers. Validate collection before marking a key event. Register a custom definition only after the parameter has passed QA and a recurring report actually needs it.

### Step 7 — Define identity and user properties when needed

Treat authenticated identity separately from event parameters. Record the approved non-PII identifier, source, availability timing, behavior before sign-in/after logout/account switch, consent, access, retention, and deletion implications. For user properties, record name, meaning, allowed values, update/unset behavior, scope, and reportability. See [send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id).

### Step 8 — Define reporting readiness

For every collected field, choose one outcome:

| Outcome | Use when |
| --- | --- |
| Standard field/metric | GA4 already provides the required meaning and scope |
| Recommended parameter | A prescribed parameter supports the intended reporting |
| Event-scoped custom dimension | A controlled descriptive field is needed in recurring reports |
| Event-scoped custom metric | A numeric quantity is needed and no standard metric fits |
| Collected but not reportable | Useful for QA/routing/export but not worth a custom definition |
| Do not collect | No approved use, excessive risk, PII, or uncontrolled cardinality |

Record processing delay, scope, cardinality, and the report or export that consumes the field. See [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153) and Section 09 for report readiness.

### Step 9 — Review, approve, and version

Before implementation, confirm business owner, technical owner, analytics reviewer, privacy/consent reviewer, target environment, version, effective date, next review date, and affected consumers. A schema change must update the event contract, parameter dictionary, Data Layer/GTM mapping, QA cases, reports, and handoff records together.

## 3. Canonical plan records and templates

Keep the canonical records below as the source of truth. Derived summaries and routing views may help readers, but they must not redefine the contract.

### 3.1 Project Context and Baseline

Use once per plan/version:

| Field | Value |
| --- | --- |
| Plan ID/version | `[plan ID] / [version]` |
| Product/business area | `[product or journey]` |
| GA4 account/property/stream | `[account] / [property] / [stream]` |
| Google tag/Measurement ID | `[tag or sanitized ID]` |
| GTM account/container | `[container]` |
| Platform/source | Web client-side via GTM; document any other source explicitly |
| Environments | Local, QA/staging, production |
| Timezone/currency | `[timezone] / [currency if relevant]` |
| Business/analytics/technical owner | `[teams]` |
| Privacy/consent reviewer | `[team or N/A]` |
| Effective/next review date | `[YYYY-MM-DD] / [YYYY-MM-DD]` |
| Status | Proposed, approved, QA, active, deprecated, or retired |

### 3.2 Journey and Event Coverage Matrix

Use for a multi-event flow. It defines coverage, not the detailed payload:

| Journey ID | Journey | Business question | Planned event sequence | Primary outcome | Report ID | QA ID | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[ID]` | `[journey]` | `[question]` | `[event A] → [event B] → [event C]` | `[outcome event]` | `[report]` | `[QA]` | `[owner]` | `[status]` |

### 3.3 Event Contract

Use one record per canonical event:

| Field | Value |
| --- | --- |
| Requirement/journey ID | `[requirement] / [journey]` |
| Business question/decision | `[question and action]` |
| Event name/type | `[event_name] / automatic, enhanced, recommended, or custom` |
| Business definition | `[what the event means]` |
| Authoritative moment/source | `[application/backend state]` |
| Expected occurrence/deduplication | `[count, retry, refresh, idempotency rule]` |
| Data Layer signal | `[event path and payload owner]` |
| GTM trigger/tag | `[trigger] / [tag]` |
| Environment/destination | `[QA and production routing]` |
| Required/optional parameters | `[names] / [names]` |
| Consent/privacy behavior | `[approved state and denied behavior]` |
| Key event status | `[yes/no/pending and reason]` |
| Reporting/custom-definition status | `[standard/custom/not reportable]` |
| QA/evidence ID | `[test IDs/evidence link]` |
| Owner/reviewer/version | `[teams] / [version/date]` |

### 3.4 Parameter Dictionary

Use one row per approved event parameter:

| Event | Parameter | Meaning | Type | Scope | Required? | Allowed values | Missing/invalid behavior | Source | Privacy/consent | Cardinality/volume | Report field |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[event_name]` | `[parameter]` | `[meaning]` | `[string/number]` | `[event/item/user]` | `[yes/no]` | `[controlled list]` | `[omit/fail/fallback]` | `[application/system]` | `[classification/state]` | `[estimate]` | `[standard/custom/N/A]` |

### 3.5 Traceability Matrix

Use this as the index from requirement to implementation and evidence:

| Requirement/event | Application state | Data Layer | GTM | Consent | Request/destination | GA4/report field | QA/evidence | Release | Owner/status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[requirement] / [event]` | `[state]` | `[signal]` | `[trigger/tag]` | `[behavior]` | `[request]` | `[field/report]` | `[IDs]` | `[release]` | `[owner/status]` |

### 3.6 Key-Event and Custom-Definition Decision Record

Use only when a key event or custom definition is being proposed:

```text
Decision ID:
Event/parameter:
Requirement/journey ID:
Business question and decision:
Success condition and expected occurrence:
Deduplication rule:
Mark as GA4 key event? [Yes/No/Pending]
Standard field checked:
Custom dimension/metric required? [Yes/No]
Cardinality/quota review:
Consent/privacy impact:
Report/export consumers:
QA/evidence ID:
Owner, approval, effective date:
```

This record governs classification; it does not replace collection QA.

### 3.7 Consent and Data Classification Matrix

Use before production collection:

| Event/parameter | Classification | Consent requirement | Denied behavior | Destination | Retention/owner | Evidence/status |
| --- | --- | --- | --- | --- | --- | --- |
| `[event/parameter]` | `[internal/sensitive/prohibited]` | `[category]` | `[block/omit/approved Consent Mode behavior]` | `[stream/none]` | `[policy/owner]` | `[link/status]` |

Keep the detailed implementation and test cases in [Consent Management](05-consent-answer.md). Never send PII, secrets, passwords, payment data, raw form values, or uncontrolled free text to GA4.

### 3.8 Schema Lifecycle Register

Use when an event meaning, parameter type/scope, allowed value, or downstream interpretation changes:

| Field | Template value | Usage |
| --- | --- | --- |
| Change ID | `[change ID]` | Stable identifier for the schema change |
| Event/parameter | `[event.parameter]` | Event or parameter affected by the change |
| Current version | `[v1]` | Version currently implemented and documented |
| Proposed version | `[v2]` | Version to be introduced after approval |
| Change type | `[type/value/meaning]` | Type, allowed-value, meaning, scope, or other schema change |
| Affected consumers | `[reports/GTM/app]` | Reports, exports, GTM objects, application code, or other dependants |
| Migration/QA action | `[migration and test plan]` | Required migration, compatibility, and QA work |
| Approval owner | `[owner]` | Person or team accountable for approval |
| Effective date | `[date]` | Date the proposed version becomes effective |
| Status | `[status]` | Proposed, approved, migrating, active, deprecated, or retired |

## 4. Implementation handoff and practical notes

### Data Layer and GTM handoff

The implementation handoff should contain the exact mapping, not a screenshot without names:

| Plan field | Application/Data Layer | GTM object | GA4 destination |
| --- | --- | --- | --- |
| Event | `[event value]` | Custom Event trigger | Event name |
| Parameter | `[path and type]` | Data Layer Variable | Event parameter |
| Consent | `[approved state]` | Consent settings/variables | Approved collection behavior |
| Destination | `[environment]` | Google tag/stream mapping | QA or production stream |

Link the plan to [Debug/QA](08-debug-qa-answer.md) for test IDs and evidence, and to [Reports and Charts](09-reports-charts-answer.md) for report field readiness. Do not maintain a second, conflicting event contract inside GTM.

### Cardinality and data minimization

Cardinality is the number of distinct values in a dimension. A controlled category is usually suitable for recurring reports; a value unique to a user, session, request, or occurrence is usually not. Before collecting or registering a high-cardinality field, ask:

1. What documented decision requires it?
2. Can it be reduced to a controlled category?
3. Is a standard field, ecommerce field, User-ID mechanism, export, or source-system report more appropriate?
4. What are the expected daily values, privacy risks, retention, and access policy?

Prefer controlled error categories over raw error text, route groups over full URL values, and report dimensions over unique operational identifiers. If raw detail is genuinely required, document its destination and governance instead of turning it into a routine GA4 custom dimension.

### Ecommerce addendum

For ecommerce, extend the normal contract with:

| Area | Required decision |
| --- | --- |
| Business moment | When the product/cart/transaction state becomes true |
| Transaction identity | Authoritative transaction ID, retry/replay behavior, and deduplication owner |
| Monetary values | Numeric value, ISO currency, tax/shipping treatment, and source of truth |
| Items | Complete item array, stable item identity, numeric price/quantity, approved taxonomy |
| Reporting | Standard ecommerce fields first; custom item definitions only for approved recurring analysis |
| Reconciliation | Comparison with the commerce/order system; GA4 is not the accounting ledger |

Section 01 defines the Data Layer payload, Sections 02–04 map and send it, Section 08 tests payload and duplicates, and Sections 09–10 reconcile and monitor it.

### Common anti-patterns

| Anti-pattern | Why it fails | Better approach |
| --- | --- | --- |
| Track every click without a decision | Noise and maintenance cost | Start from a measurable outcome |
| Fire success on click | Click is not proof of business success | Use the authoritative application/backend state |
| Encode values in event names | Splits reporting and destabilizes schema | One event with controlled parameters |
| Send raw form fields or free text | Privacy and data-quality risk | Allowlist fields and normalize values |
| Register every parameter as a custom dimension | Consumes quota and creates report clutter | Register only recurring, approved report fields |
| Duplicate automatic/enhanced collection in GTM | Counts one interaction more than once | Check existing collection before adding a tag |
| Rename a live event casually | Breaks reports, QA baselines, and consumers | Version the contract and review impact |

## 5. Worked example — Registration Journey

This is the only worked Journey example in the document. It is a non-production illustration; replace sample IDs, owners, values, destinations, and evidence with project-approved data. Every subsection below follows the corresponding canonical record in Section 3.

### 5.1 Project Context and Baseline

| Field | Recorded value |
| --- | --- |
| Plan ID/version | `MP-REG-001 / v1.0` |
| Product/business area | Account registration |
| GA4 account/property/stream | `[project account] / [project property] / QA and production web streams` |
| Google tag/Measurement ID | `[project Google tag / Measurement ID]` |
| GTM account/container | `[project GTM account / container]` |
| Platform/source | Web client-side via application Data Layer → GTM → GA4 |
| Environments | Local, QA/staging, production |
| Timezone/currency | `[project timezone] / N/A` |
| Business/analytics/technical owner | Product team / Analytics QA / Frontend and GTM owner |
| Privacy/consent reviewer | Privacy owner |
| Effective/next review date | `[YYYY-MM-DD] / [YYYY-MM-DD]` |
| Status | Proposed — pending QA and approval |

### 5.2 Journey and Event Coverage Matrix

| Journey ID | Journey | Business question | Planned event sequence | Primary outcome | Report ID | QA ID | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `J-REG-001` | Registration | Where do users abandon registration? | `registration_start` → `[registration_error]*` → `sign_up` | `sign_up` | `R-REG-001` | `TC-REG-001` | Product team | Proposed |

`registration_error` is an optional, repeatable failure branch. It is not a mandatory step before `sign_up`.

### 5.3 Event Contract

#### `registration_start`

| Field | Recorded value |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | Where do users abandon registration? / improve the entry-to-completion funnel |
| Event name/type | `registration_start` / custom |
| Business definition | A registration method is selected and the form is ready for the user to begin |
| Authoritative moment/source | Application confirms the selected method and form readiness |
| Expected occurrence/deduplication | Once per intended entry; no duplicate on remount unless the plan defines a new entry |
| Data Layer signal | `event: registration_start` with `form_id` and `method`; application-owned payload |
| GTM trigger/tag | `CE - Web - registration_start` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production after approval → production stream |
| Required/optional parameters | `form_id`, `method` / none |
| Consent/privacy behavior | Approved analytics consent; block, omit, or use the approved Consent Mode behavior when denied |
| Key event status | No — funnel entry only |
| Reporting/custom-definition status | Event name and users; register `method` only if a recurring report requires it; `form_id` is collected but not reportable |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend and GTM owner / Analytics QA / `v1.0` |

#### `registration_error`

| Field | Recorded value |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | Which registration failures need attention? / separate validation from server-error remediation |
| Event name/type | `registration_error` / custom |
| Business definition | An approved validation or server error is shown to the user |
| Authoritative moment/source | Application classifies and displays the approved error category |
| Expected occurrence/deduplication | Once per visible error occurrence; do not repeat the same display without a new occurrence |
| Data Layer signal | `event: registration_error` with `form_id`, `method`, and `error_type`; application-owned payload |
| GTM trigger/tag | `CE - Web - registration_error` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production after approval → production stream |
| Required/optional parameters | `form_id`, `method`, `error_type` / none |
| Consent/privacy behavior | Approved analytics consent; block, omit, or use the approved Consent Mode behavior when denied |
| Key event status | No — diagnostic journey event |
| Reporting/custom-definition status | `error_type` is collected; register it as an event-scoped custom dimension only if recurring error analysis is approved |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend and GTM owner / Analytics QA / `v1.0` |

#### `sign_up`

| Field | Recorded value |
| --- | --- |
| Requirement/journey ID | `REQ-REG-001 / J-REG-001` |
| Business question/decision | Which methods complete registration? / measure confirmed completion and classify it as a key event |
| Event name/type | `sign_up` / recommended |
| Business definition | Account creation is confirmed by the backend |
| Authoritative moment/source | Application receives the successful account-creation result |
| Expected occurrence/deduplication | One per confirmed account; no duplicate on retry or refresh; deduplication is owned by the application/backend |
| Data Layer signal | `event: sign_up` with `form_id` and `method`; application-owned payload |
| GTM trigger/tag | `CE - Web - sign_up` / GA4 Event tag |
| Environment/destination | QA/staging → QA stream; production after approval → production stream |
| Required/optional parameters | `method`, `form_id` / none |
| Consent/privacy behavior | Approved analytics consent; block, omit, or use the approved Consent Mode behavior when denied |
| Key event status | Pending — mark Yes only after collection QA and Product approval |
| Reporting/custom-definition status | Use the recommended `sign_up` event; register `method` as an event-scoped custom dimension after QA if the recurring report requires it; `form_id` is collected but not reportable |
| QA/evidence ID | `TC-REG-001 / [evidence link]` |
| Owner/reviewer/version | Frontend and GTM owner / Analytics QA / `v1.0` |

### 5.4 Parameter Dictionary

| Event | Parameter | Meaning | Type | Scope | Required? | Allowed values | Missing/invalid behavior | Source | Privacy/consent | Cardinality/volume | Report field |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `registration_start` | `form_id` | Stable form identifier | string | Event | Yes | Approved form IDs | Fail QA; do not send the event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `registration_start` | `method` | Selected registration method | string | Event | Yes | `email`, `google`, `apple` | Fail QA; do not send the event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension if recurring report is approved |
| `registration_error` | `form_id` | Form that displayed the error | string | Event | Yes | Approved form IDs | Fail QA; do not send the event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `registration_error` | `method` | Selected registration method at failure | string | Event | Yes | `email`, `google`, `apple` | Fail QA; do not send the event | Application registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension if recurring report is approved |
| `registration_error` | `error_type` | Controlled error category | string | Event | Yes | `validation`, `server_error` | Fail QA; do not send the event | Application error classification | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension only if recurring error analysis is approved |
| `sign_up` | `form_id` | Form used for the confirmed account creation | string | Event | Yes | Approved form IDs | Fail QA; do not send the event | Application/backend registration state | Controlled non-PII; approved analytics consent | Low; controlled IDs | Collected, not reportable |
| `sign_up` | `method` | Registration method used for the confirmed account | string | Event | Yes | `email`, `google`, `apple` | Fail QA; do not send the event | Application/backend registration state | Controlled non-PII; approved analytics consent | Low; controlled list | Event-scoped custom dimension after QA if recurring report is approved |

Do not collect email, phone, password, raw account ID, raw error text, or free-form form values in this contract.

### 5.5 Data Layer, GTM, and destination mapping

This concrete handoff follows the standard `Event` / `Parameter` / `Consent` / `Destination` rows. The application owns business truth; GTM routes the approved signal and maps only allowlisted parameters.

| Plan field | Application/Data Layer | GTM object | GA4 destination |
| --- | --- | --- | --- |
| Event | `event: registration_start`, `event: registration_error`, or `event: sign_up` | Matching Custom Event trigger → GA4 Event tag | Event name on the QA or production web stream |
| Parameter | `form_id`, `method`, and `error_type` from the application payload; explicit type and allowlist | Data Layer Variables mapped by name; no full-object forwarding | Event parameters |
| Consent | Approved analytics-consent state exposed to the tag configuration | Consent settings and consent-aware trigger/tag behavior | Approved collection behavior or denied behavior |
| Destination | Local/QA staging uses the QA stream; production uses the production stream after approval | Google tag and environment-specific stream mapping | QA or production web stream |

#### Per-event routing view (derived)

| Event | Data Layer payload | GTM object | Allowed parameters |
| --- | --- | --- | --- |
| `registration_start` | `event` + `form_id` + `method` | `CE - Web - registration_start` → GA4 Event tag | `form_id`, `method` |
| `registration_error` | `event` + `form_id` + `method` + `error_type` | `CE - Web - registration_error` → GA4 Event tag | `form_id`, `method`, `error_type` |
| `sign_up` | `event` + `form_id` + `method` | `CE - Web - sign_up` → GA4 Event tag | `form_id`, `method` |

### 5.6 Consent and Data Classification Matrix

| Event/parameter | Classification | Consent requirement | Denied behavior | Destination | Retention/owner | Evidence/status |
| --- | --- | --- | --- | --- | --- | --- |
| `registration_start.form_id`, `registration_start.method` | Internal, controlled, non-PII | Approved analytics consent | Block, omit, or use the approved Consent Mode behavior | QA stream; production stream after approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| `registration_error.form_id`, `registration_error.method`, `registration_error.error_type` | Internal, controlled, non-PII | Approved analytics consent | Block, omit, or use the approved Consent Mode behavior | QA stream; production stream after approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| `sign_up.form_id`, `sign_up.method` | Internal, controlled, non-PII | Approved analytics consent | Block, omit, or use the approved Consent Mode behavior | QA stream; production stream after approval | Project retention policy / Privacy owner | `TC-REG-001 / pending QA` |
| Any event / email, phone, password, raw error text, raw account ID | Prohibited | Not applicable | Do not collect or forward | None | Not retained / Privacy owner | `PROHIBITED / enforced` |
| User-ID after authenticated sign-in | Separate identity contract | Separate approved conditions | Clear or omit per the identity contract | Approved identity configuration only | Identity retention policy / Identity owner | `N/A / separate review` |

Keep the detailed implementation and test cases in [Consent Management](05-consent-answer.md). Never send PII, secrets, passwords, payment data, raw form values, or uncontrolled free text to GA4.

### 5.7 Key-Event and Custom-Definition Decision Record

```text
Decision ID: DEC-REG-001
Event/parameter: sign_up; sign_up.method
Requirement/journey ID: REQ-REG-001 / J-REG-001
Business question and decision: Which methods complete registration? Mark confirmed account creation as a key event after validation.
Success condition and expected occurrence: Backend confirms account creation; one occurrence per confirmed account.
Deduplication rule: No event on failed validation, server failure, retry before success, or refresh; one event per confirmed account.
Mark as GA4 key event? [Pending → Yes after QA and Product approval]
Standard field checked: Recommended sign_up event; standard event count and user metrics checked.
Custom dimension/metric required? [Yes — event-scoped custom dimension for method after QA; no custom metric]
Cardinality/quota review: Method has a low controlled value set; form_id is collected for traceability but not registered as a recurring custom dimension.
Consent/privacy impact: method and form_id are controlled non-PII and require approved analytics consent.
Report/export consumers: R-REG-001 / Product Analytics; R-REG-002 / Analytics QA.
QA/evidence ID: TC-REG-001 / [evidence link]
Owner, approval, effective date: Product owner + Analytics QA + Privacy owner / pending / [YYYY-MM-DD]
```

`registration_error.error_type` has no decision record yet; open one only when recurring error analysis is approved.

### 5.8 Derived reporting requirements

This is a derived reporting view. The Event Contract and Parameter Dictionary remain the source of truth.

| Report ID | Question | Population/grain | Dimensions | Metrics/formula | Surface | Owner |
| --- | --- | --- | --- | --- | --- | --- |
| `R-REG-001` | Which method has the lowest validated completion rate? | Users who select a method and enter registration | `method`, device category, date | Users with `sign_up` / users with `registration_start` | Detail report + funnel Exploration | Product Analytics |
| `R-REG-002` | Is confirmed account creation sent once with valid values? | Controlled test event occurrences | Event name, method, form ID, error type | Event count and duplicate review | Exploration + processed event report | Analytics QA |

The completion-rate numerator and denominator must use the same date range, population, identity context, journey definition, and `method` source. Event count is not the same as completed-user count.

### 5.9 Traceability Matrix and approval

| Requirement/event | Application state | Data Layer | GTM | Consent | Request/destination | GA4/report field | QA/evidence | Release | Owner/status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `REQ-REG-001 / registration_start` | Selected method and form-ready state | `event: registration_start` + `form_id` + `method` | `CE - Web - registration_start` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production after approval | Event name, users, `method`, funnel entry | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |
| `REQ-REG-001 / registration_error` | Approved visible validation/server error | `event: registration_error` + `form_id` + `method` + `error_type` | `CE - Web - registration_error` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production after approval | Event name, `error_type` | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |
| `REQ-REG-001 / sign_up` | Backend-confirmed account creation | `event: sign_up` + `form_id` + `method` | `CE - Web - sign_up` → GA4 Event tag | Approved behavior | GA4 request → QA stream; production after approval | Event name, `method`, pending key event | `TC-REG-001 / [evidence]` | `[release ID]` | Frontend + GTM / Proposed |

Approval requires business meaning, technical mapping, privacy/consent behavior, QA evidence, report readiness, and release reference to be explicit. Production activation is handled by Section 10.

### 5.10 Schema Lifecycle Register

This is a new `v1.0` plan, so no schema migration is currently proposed. Keep the register entry to make the lifecycle state explicit:

| Field | Recorded value | Notes |
| --- | --- | --- |
| Change ID | `N/A-REG-001` | No schema migration proposed for this plan version |
| Event/parameter | All Registration events and parameters | Reopen the record for any affected event or parameter |
| Current version | `v1.0` | Version currently documented |
| Proposed version | — | No new version proposed |
| Change type | No change | No meaning, type, scope, or allowed-value change |
| Affected consumers | — | No migration consumers identified |
| Migration/QA action | Reopen this register before changing event meaning, parameter type/scope, or allowed values | Update the contract, mapping, QA cases, and reports together |
| Approval owner | Product + Analytics + Privacy owners | Required for any future schema change |
| Effective date | — | Not applicable while no change is proposed |
| Status | Not applicable | Register remains available for the next schema change |

## Official references

- [About events](https://support.google.com/analytics/answer/9322688)
- [Enhanced measurement events](https://support.google.com/analytics/answer/9216061?hl=en)
- [Recommended events](https://support.google.com/analytics/answer/9267735)
- [Measure ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
- [Custom events](https://support.google.com/analytics/answer/12229021?hl=en)
- [Event naming rules](https://support.google.com/analytics/answer/13316687)
- [Event parameters](https://support.google.com/analytics/answer/13675006)
- [Event collection limits](https://support.google.com/analytics/answer/9267744)
- [Custom dimensions and metrics](https://support.google.com/analytics/answer/14240153)
- [Cardinality](https://support.google.com/analytics/answer/12226705?hl=en)
- [About key events](https://support.google.com/analytics/answer/9267568)
- [Send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id)
- [GTM Custom Event trigger](https://support.google.com/tagmanager/answer/7679219)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
