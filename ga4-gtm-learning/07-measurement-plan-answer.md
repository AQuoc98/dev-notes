# 07 — GA4/GTM Measurement Plan

## Purpose

A measurement plan is the contract between a business question and the data that will be collected, routed, validated, reported, and acted on. It is not a list of every click. It defines what must be measured, why it matters, when it is true, what data is allowed, and how another person can verify it.

Do not start with a GTM tag. Start with the business state that must be observed.

## Project Context and Baseline

**Purpose:** Use this record to define the property, stream, platform, environment, owners, and dates that the plan applies to. It prevents the same event name from being interpreted differently across products or environments.

Before defining individual events, freeze the context in which the plan applies. The same event name can have different owners, destinations, consent behavior, or reporting meaning across products and environments.

| Field                          | Required value                                                    |
| ------------------------------ | ----------------------------------------------------------------- |
| Plan ID and version            | `MP-REG-001 / v1.0`                                               |
| Business/product area          | `[product or journey]`                                            |
| GA4 account/property           | `[account] / [property]`                                          |
| GA4 data stream                | `[web/app stream name and ID]`                                    |
| Google tag / Measurement ID    | `[tag ID or sanitized reference]`                                 |
| GTM account/container          | `[container name and ID]`                                         |
| Platform and collection source | Web client-side via GTM, app SDK, server, or Measurement Protocol |
| Environments                   | Local, QA, staging, production                                    |
| Property timezone/currency     | `[timezone] / [currency]` where relevant                          |
| Business owner                 | `[name/team]`                                                     |
| Analytics owner                | `[name/team]`                                                     |
| Technical owner                | `[name/team]`                                                     |
| Privacy/consent reviewer       | `[name/team or N/A]`                                              |
| Effective date                 | `YYYY-MM-DD`                                                      |
| Next review/expiry             | `YYYY-MM-DD`                                                      |
| Status                         | Proposed, approved, QA, active, deprecated, or retired            |

The plan should state whether it covers only web client-side collection or also app, server-side, offline, or Measurement Protocol events. If a source is out of scope, say so explicitly rather than leaving the reader to infer it.

## Core Concepts

### The simple mental model

A measurement plan turns a business question into data that GA4 can collect and a report that someone can use. The easiest way to understand the main terms is to treat an event as a sentence:

- **Event = the action or outcome.** It answers: “What happened?” Examples: `sign_up`, `purchase`.
- **Event parameter = the details of that action.** It answers: “What else do we need to know?” Examples: `method`, `form_id`, `value`.
- **User property = information about the user.** It describes a user and usually changes less often than events. Example: `account_type = business`.
- **Dimension = the field used to group or filter a report.** It answers: “How do we want to split the data?” Examples: event name, device category, or `form_id`.
- **Metric = a number that can be counted, summed, or calculated.** Examples: users, event count, and revenue.
- **Key event = an event marked as important to the business.** Examples: `sign_up` or `purchase` when those outcomes matter to the business.

For example:

```text
Event:             purchase
Event parameters:  transaction_id = ORD-123, value = 49.90, currency = USD
User property:     account_type = business
Report dimension:  account_type
Report metrics:    purchase count, revenue
Business outcome:  purchase may be marked as a key event
```

This distinction matters because collection and reporting are different things. An event parameter is data collected with an event; it does not automatically become a reusable custom dimension or metric. Before registering a custom definition, check whether a standard GA4 field already answers the question, whether the scope is correct, and whether the reporting surface actually needs the field.

### Event categories

When planning an event, choose the most specific category that already fits. Use this order:

1. Check whether GA4 already collects it automatically.
2. Check whether Enhanced Measurement can collect it.
3. Check whether a Google recommended event matches the business meaning.
4. Create a custom event only when the previous options do not fit.

| Category                | In simple terms                                                                 | Examples                                                | Planning rule                                                                                     |
| ----------------------- | ------------------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Automatically collected | GA4 collects it when the basic setup is present.                                | `page_view`, `session_start`                            | Check the existing data first so you do not create a duplicate event.                             |
| Enhanced Measurement    | GA4 can collect common website interactions when the setting is enabled.        | Scrolls, outbound clicks, site search, video engagement | Review the setting and test SPA/DOM behavior before recreating the interaction in GTM.            |
| Recommended event       | Google provides a standard name and parameter set for a common business action. | `login`, `sign_up`, `generate_lead`, `purchase`         | Prefer the prescribed name and parameters because they support standard reports and integrations. |
| Custom event            | A meaningful business action that is not covered by the options above.          | `calculation_complete`, `quote_requested`               | Use only when the business meaning is real and no recommended event fits.                         |

Recommended events should use the prescribed parameters where available. For example, use `sign_up` instead of inventing `signup_done` when both describe the same action. This keeps the schema consistent and avoids splitting the same business meaning across multiple event names. See [recommended events](https://support.google.com/analytics/answer/9267735) and [about events](https://support.google.com/analytics/answer/9322688).

### Collection truth versus reporting truth

The plan should distinguish what each checkpoint proves:

| Checkpoint               | What it proves                                            | What it does not prove                                                                              |
| ------------------------ | --------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Application              | The product knows that the business action happened.      | It does not prove that analytics received the data.                                                 |
| Data Layer               | The action and its payload were exposed to GTM.           | It does not prove that GTM sent the correct request.                                                |
| GTM                      | The trigger and tag evaluated and attempted to send data. | It does not prove that GA4 processed every field for reporting.                                     |
| GA4 DebugView            | GA4 received a recent event and its visible parameters.   | It does not guarantee that the field is registered, correctly scoped, or available in every report. |
| Reports and Explorations | GA4 processed the data into a usable reporting view.      | It does not prove that the business interpretation is correct without validation.                   |

The flow is therefore:

```text
Business action happens
        ↓
Application exposes it once in the Data Layer
        ↓
GTM reads it and sends the correct request
        ↓
GA4 receives and processes it
        ↓
The report answers the business question
```

An event can appear in the Data Layer and DebugView but still be unusable for a recurring report because the parameter is missing, mis-scoped, unregistered, delayed, high-cardinality, or prohibited by privacy rules.

## Cardinality and High-Cardinality Dimensions

### What is cardinality?

Cardinality refers to the number of unique values assigned to a dimension. Some dimensions have a fixed number of unique values. For example, the Device dimension can have up to three values—desktop, tablet, and mobile—so its cardinality in that example is three. Other dimensions, such as Item ID, Page path, and Page location, can have many possible unique values. Ecommerce sites can have hundreds of thousands of items, and websites can have hundreds of thousands of unique pages; these dimensions are expected to be high cardinality.

### How to read “dimension/value pattern”

In this table:

- A **dimension** is the question or field used to split a report, such as `method` or `item_id`.
- A **value** is the answer stored in that field, such as `email`, `google`, or `SKU-123`.
- A **dimension/value pattern** describes the type of values that field is expected to contain and how widely those values may vary.

For example, `method = email` means `method` is the dimension and `email` is one of its values. A small, approved set of values is usually easier to use in recurring reports than a field that receives a new value for every page, product, user, or transaction.

| Pattern in plain language                                                                     | Example                                         | Usual cardinality      | What to do in the measurement plan                                                                                                     |
| --------------------------------------------------------------------------------------------- | ----------------------------------------------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Small, controlled category** — only a few allowed values                                    | `device_category = desktop/mobile/tablet`       | Low                    | Good for regular report breakdowns. Document the allowed values.                                                                       |
| **Small approved list** — a short list maintained by the team                                 | `method = email/google/apple`                   | Low                    | Good for recurring reports and comparisons. Do not mix spellings or extra labels.                                                      |
| **Business ID with a limited list** — an ID reused across a small number of business objects  | `form_id = registration/newsletter`             | Low to medium          | Use approved IDs and review whether the list is growing.                                                                               |
| **Page or product ID** — identifies many pages or products                                    | `page_location`, `page_path`, `item_id`         | Medium to high         | Use when a specific business question needs it. Avoid registering another custom dimension unnecessarily.                              |
| **ID for each individual occurrence** — usually unique for one order, request, or transaction | `transaction_id`, `order_id`, request ID        | High                   | Collect only for an approved ecommerce or operational need. Keep it out of routine custom dimensions unless there is a clear use case. |
| **User or session ID** — identifies a person, browser, or session                             | `user_id`, session ID, client ID                | Very high              | Do not register it as a custom dimension. Use the appropriate identity, export, or source-system mechanism.                            |
| **Free text or generated value** — can change on every occurrence                             | Search text, raw error message, UUID, timestamp | Very high or unbounded | Do not use as a routine report dimension. Normalize, group into categories, redact, or keep it out of GA4.                             |

### What is a high-cardinality dimension?

Google defines a high-cardinality dimension as a dimension with more than 500 unique values in one day. High-cardinality dimensions increase the number of rows in a report, which makes it more likely that the report will reach its row limit. When that happens, data beyond the limit is condensed into the `(other)` row. Google also states that GA4 has a cardinality limit of 50,000 values; after that limit, cardinality control is implemented. Properties can have dimensions with any number of values, but high-cardinality dimensions should be used only when the information is necessary for the business. See [GA4 cardinality](https://support.google.com/analytics/answer/12226705?hl=en).

The 500-value and 50,000-value figures describe Google Analytics behavior; they are not a universal rule that forbids collection. Check the current property, report surface, and official documentation before implementation.

High-cardinality does not mean “never collect this value.” It means the value needs a deliberate purpose, destination, access policy, and analysis method. A product or transaction identifier may be necessary for reconciliation, while still being inappropriate as a recurring custom report dimension.

### Why high-cardinality dimensions should be limited

1. **Report detail can be condensed.** A high-cardinality dimension increases report rows and makes it more likely that the report reaches its row limit. Data beyond the limit can be grouped into an `(other)` row, so value-level detail is less complete.
2. **Analysis becomes noisy.** A table containing thousands of one-off URLs, IDs, timestamps, or error strings is difficult to interpret and rarely supports a stable business decision.
3. **Custom-definition quota is consumed.** Registering a unique identifier as a custom dimension spends a limited property resource without creating a useful recurring breakdown. Google recommends using predefined fields where possible and avoiding unnecessary high-cardinality custom dimensions.
4. **Privacy and access risk increases.** A unique value can act as a linkable identifier, and free text can contain PII or secrets. High cardinality is not itself a privacy violation, but it increases the need for data minimization and review.
5. **Cross-report consistency suffers.** A high-cardinality field may behave differently across Reports, Explorations, exports, and APIs. A plan should state which surface is authoritative for the intended analysis.

### Decision framework

Before adding a dimension or registering a custom definition, ask:

1. Is the field required for a documented business question and decision?
2. Is it a category, or is it an identifier for one user/item/session/occurrence?
3. Can the value be reduced to a controlled category without losing the decision-making signal?
4. Does a standard dimension, recommended parameter, ecommerce field, User-ID mechanism, or source-system report already solve the need?
5. Does the report need aggregated values, while reconciliation needs the raw identifier elsewhere?
6. What is the expected number of distinct values per day and over the retention period?
7. Is the value safe under the privacy, consent, access, and retention policy?

Use these patterns:

| Need                      | Avoid                                                    | Prefer                                                                                               |
| ------------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Analyze error causes      | Raw error message or stack trace                         | Controlled `error_type`, `error_code`, and `error_category`                                          |
| Compare page groups       | Full URL with query/user content                         | Approved `page_type`, route group, or content group                                                  |
| Compare products          | Unbounded free-text product label                        | Stable approved `item_id`, category, or product group at the correct scope                           |
| Reconcile purchases       | Registering every `transaction_id` as a report dimension | Keep the recommended transaction field for its purpose and reconcile with the commerce source/export |
| Identify signed-in users  | Custom dimension containing user IDs                     | Use the approved User-ID mechanism; never expose it as a routine report dimension                    |
| Analyze durations/amounts | Timestamp or raw numeric string as a category            | Use a metric or documented buckets such as `0–10s`, `11–30s`, `31s+`                                 |

If raw detail is genuinely required, document the approved destination, access, retention, cost, and owner. Depending on the use case, a governed export or source-system analysis may be more appropriate than a GA4 custom dimension. See [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

## Measurement-Plan Workflow

This workflow moves from a business decision to a reliable report. Each step should answer one practical question: what decision do we need to support, what happened, what details are needed, where does the signal come from, and how will we verify and use it?

### Define the decision

Write the business question in a form that can change an action:

```text
Decision owner: Product team
Question: Which registration methods have the highest completion rate?
Decision: Prioritize UX work for a method with materially lower completion.
Success criterion: The report distinguishes valid starts and confirmed completions by method.
```

Avoid questions that only ask for a vanity count, such as “How many buttons were clicked?” unless that count has a clear operational use.

### Identify the authoritative business moment

For each event, describe the state transition that makes it true. In plain language, this is the moment when the application or backend has enough evidence to say that the business action really happened. It is not always the moment when a user clicks a button.

```text
User clicks Submit
  → client validates input
  → server confirms account creation
  → application pushes sign_up
```

The canonical `sign_up` event belongs after confirmed success, not merely after a click. The application or backend response should own business truth where applicable; GTM should route the approved signal rather than infer success from the page presentation.

Record negative and duplicate behavior as part of the contract:

- Invalid form: no `sign_up`.
- Server failure: no `sign_up`.
- Rapid double click: one event for one confirmed account.
- Refresh of a confirmation page: no new event unless the business defines a new occurrence.

### Select the event type and name

Apply this order:

1. Is the interaction already automatically collected or covered by enhanced measurement?
2. Is there a recommended event whose meaning matches the requirement?
3. If not, define a custom event with a stable business meaning.
4. Check the name against the current GA4 naming rules and collection limits.

Use lowercase `snake_case` names as the team convention. GA4 event names are case-sensitive, must begin with a letter, and should not contain spaces; reserved names and prefixes must be avoided. Treat the official [event naming rules](https://support.google.com/analytics/answer/13316687) and [collection limits](https://support.google.com/analytics/answer/9267744) as the current authority.

Never encode variable business values into the event name:

```text
Good:  search with parameter search_term = "shoes"
Bad:   search_shoes

Good:  sign_up with parameter method = "email"
Bad:   sign_up_email
```

### Define parameters and types

Add a parameter only when it helps answer an approved question. Each parameter needs:

**Core definition**

- canonical name and meaning;
- source and authoritative owner;
- type, scope, and required/optional status;
- allowed values;
- behavior when the value is missing or invalid.

**Quality and reporting checks**

- privacy and consent classification;
- expected cardinality and volume;
- reporting destination or custom-definition decision.

Prefer controlled vocabularies:

```text
method: "email" | "google" | "apple"
form_id: "registration" | "newsletter"
result: "success" | "validation_error" | "server_error"
```

Do not use `unknown` to conceal a broken required value. Use an explicit null/omit policy for optional values and fail QA for missing required values.

Do not send names, email addresses, phone numbers, addresses, free-form text, access tokens, passwords, payment details, or other prohibited personal data to GA4. Avoid exposing them in URLs, logs, screenshots, or QA evidence. If GTM temporarily reads a value for validation or consent logic, it must not forward that value to GA4. A stable controlled identifier can still be sensitive; document its purpose, access, retention, and privacy approval.

### Parameter naming, limits, and schema rules

The parameter dictionary must be checked against the current GA4 naming rules and collection limits before implementation. In addition to event names, review reserved event-parameter names, reserved user-property names, reserved item-parameter names, and prohibited prefixes. Do not create a custom parameter that conflicts with a system field or begins with a reserved prefix.

Record these constraints without treating them as permanent product constants:

- maximum event-name, parameter-name, and value lengths;
- maximum number of event parameters and user properties;
- maximum item count and item-scoped custom parameters for ecommerce;
- allowed primitive type and conversion behavior;
- array/object structure where the source is ecommerce or server-side;
- whether the field is omitted, explicitly supported as `null`, or rejected when unavailable;
- whether the value is safe for URLs, logs, exports, and screenshots.

Use the official [event naming rules](https://support.google.com/analytics/answer/13316687) and [event collection limits](https://support.google.com/analytics/answer/9267744) as the source of truth. Re-check them before implementation because limits and reserved names can change. Do not send `null` by default; use the value behavior supported by the chosen implementation.

Add a schema version when the event contract is likely to evolve:

```text
event: "purchase"
schema_version: "2"
transaction_id: "ORDER-123"
currency: "USD"
value: 99.00
```

Use schema versions deliberately. Do not add a version parameter to every event if the team cannot operate or interpret it; in that case, version the measurement-plan record and document the deployed contract in the inventory.

### Define Data Layer and GTM mapping

The mapping should be explicit and traceable:

| Measurement field | Application/Data Layer     | GTM object                 | GA4 destination              |
| ----------------- | -------------------------- | -------------------------- | ---------------------------- |
| Event name        | `event: "sign_up"`         | Custom Event trigger       | GA4 Event name `sign_up`     |
| Method            | `method: "email"`          | DLV `method`               | Event parameter `method`     |
| Form ID           | `form_id: "registration"`  | DLV `form_id`              | Event parameter `form_id`    |
| Consent           | CMP/approved consent state | Consent settings/variables | Approved collection behavior |
| Destination       | Environment configuration  | Google tag/stream mapping  | QA or production stream      |

The application should push the event and its values together whenever possible:

```javascript
window.dataLayer = window.dataLayer || [];
window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "registration",
});
```

GTM should not reconstruct a business event from fragile DOM text, button labels, or raw form values when the application can provide an authoritative signal. A Custom Event trigger listens for the event value pushed to the Data Layer; see [GTM Custom Event triggers](https://support.google.com/tagmanager/answer/7679219).

### Decide key events

Mark an event as a key event only when it represents a meaningful business outcome and the organization has agreed how it will be used. Do not mark every interaction as a key event. For each candidate, record:

- business owner and decision supported;
- exact success condition;
- expected volume and frequency;
- whether it is used for advertising or bidding;
- deduplication rule;
- consent/privacy dependency;
- downstream reports, audiences, or exports;
- approval and change owner.

#### Validate collection before marking a key event

Marking an event as a key event only tells GA4 that the event is important to the business. It does not correct an event that was collected incorrectly.

For example, the correct `sign_up` flow is:

```text
User clicks Submit
  → input is valid
  → the application/backend confirms account creation
  → one sign_up event is sent
  → sign_up is marked as a key event
```

The following implementation is unsafe:

```text
User clicks Submit
  → sign_up is sent immediately
  → the server returns an error
```

It can count a registration that never succeeded. A rapid double click, retry, or refresh can also send the same success event more than once. If that event is a key event, GA4 may report inflated business outcomes; when the event is used for Google Ads, the incorrect data can also affect advertising decisions or bidding.

Before marking an event as a key event, verify that:

- one confirmed business occurrence produces one event;
- the event fires only after the success condition is confirmed;
- invalid input, server failure, retries, and refreshes do not create false successes or duplicates;
- required parameters, consent behavior, and destination are correct.

An event that is collected correctly but not yet marked as a key event is mainly a classification problem. A duplicated or premature key event is a data-quality problem that can mislead reports, business decisions, and downstream advertising.

### User-ID and user-property contract

If the product has authenticated users, add an explicit identity section to the plan. Do not treat `user_id` as an ordinary event parameter or create a high-cardinality custom dimension from it.

Record:

- the approved non-PII identifier and its source of truth;
- the exact moment it becomes available, usually after approved sign-in;
- whether it is set at configuration level or through the approved Google tag implementation;
- behavior before sign-in, after logout, account switching, and session continuation;
- consent and privacy requirements;
- access, retention, export, and deletion implications;
- reporting identity and cross-device expectations;
- test cases for sign-in mid-session, logout, multi-tab behavior, and account switching.

For user properties, record the property name, meaning, allowed values, update timing, unset behavior, scope, retention, and reportability. User properties describe users; event parameters describe event occurrences. See [send User-IDs](https://developers.google.com/analytics/devguides/collection/ga4/user-id).

### Decide reporting readiness

Collecting a parameter and reporting on a parameter are separate steps. First confirm that the value is being collected correctly; then decide whether GA4 needs a standard field, a custom definition, an export, or no reporting field at all.

For each parameter, choose one of these outcomes:

| Outcome                           | When to use                                                                          |
| --------------------------------- | ------------------------------------------------------------------------------------ |
| Standard dimension/metric         | GA4 already exposes the required meaning and scope.                                  |
| Recommended event parameter       | Google provides a prescribed parameter that supports standard reporting.             |
| Event-scoped custom dimension     | A controlled descriptive parameter is required in reports/Explorations.              |
| Event-scoped custom metric        | A numeric quantity is required and no standard metric fits.                          |
| Keep collected but not reportable | Useful for debugging or downstream processing but not worth a GA4 custom definition. |
| Do not collect                    | No approved business use, excessive risk, PII, or uncontrolled cardinality.          |

Custom definitions should be registered only after the parameter is being collected and validated. Google’s current guidance states that new custom definitions can take time to become reportable and that high-cardinality fields may be condensed under `(other)` or otherwise degrade analysis. See [custom dimensions and metrics](https://support.google.com/analytics/answer/14240153).

## Measurement-Plan Template

This template set is the working record for turning business requirements into maintainable event contracts. Use one row per event contract; do not collapse materially different business moments into one vague row. Each template has a different job:

- The **journey coverage matrix** shows which events are needed across a user journey.
- The **event contract** defines one event and its business meaning.
- The **parameter dictionary** defines the fields carried by events.
- The **traceability matrix** connects the application, Data Layer, GTM, GA4, reports, and QA evidence.
- The decision and lifecycle records explain why a field is reportable and how changes are managed.

The completed example later in this document includes a few derived views and handoffs. Those views make the plan easier for implementation, QA, and reporting teams to read; they are not additional canonical templates. Keep the canonical event, parameter, consent, traceability, decision, and lifecycle records as the source of truth.

### Journey and event coverage matrix

**Purpose:** Use this matrix to manage a complete journey with multiple events. It gives product, analytics, development, QA, and reporting owners one view of coverage instead of requiring them to read each event row separately.

| Journey ID | Journey      | Business question                    | Event sequence                                          | Primary key event | Report ID | QA/Evidence ID | Owner        | Status |
| ---------- | ------------ | ------------------------------------ | ------------------------------------------------------- | ----------------- | --------- | -------------- | ------------ | ------ |
| J-REG-001  | Registration | Where do users abandon registration? | `registration_start` → `registration_error` → `sign_up` | `sign_up`         | R-REG-001 | TC-REG-001     | Product team | Active |

### Event contract template

**Purpose:** Use one record for one canonical event. This is the source of truth for what the event means, when it is true, what it sends, where it goes, and who owns it.

| Field                               | Example                                                                     |
| ----------------------------------- | --------------------------------------------------------------------------- |
| Plan ID / version                   | MP-REG-001 / v1.2                                                           |
| Requirement ID / journey            | REQ-REG-001 / J-REG-001                                                     |
| Business area                       | Registration                                                                |
| Business question                   | Which registration methods have the lowest completion rate?                 |
| Decision owner                      | Product team                                                                |
| Decision                            | Prioritize UX work for a method with materially lower completion.           |
| GA4 property / stream               | `[property] / [web stream]`                                                 |
| GTM container / environment         | `[container] / QA → production`                                             |
| Platform / collection source        | Web client-side via GTM                                                     |
| Collection-source ownership         | GTM is the canonical client-side source; no undocumented second source     |
| Event name                          | `sign_up`                                                                   |
| Schema version                      | `v1`                                                                        |
| Event type                          | Recommended                                                                 |
| Business definition                 | Account creation confirmed by the server                                    |
| Authoritative business moment       | Backend/application confirms account creation                               |
| Source system / source of truth     | Registration application / account-creation response                        |
| Source owner                        | Registration application                                                    |
| Data Layer signal                   | `event: "sign_up"`                                                          |
| GTM trigger                         | `CE - Web - sign_up`                                                        |
| GA4 tag                             | `GA4 Event - Web - sign_up`                                                 |
| Environment / routing               | QA stream; production stream after approval                                 |
| Expected occurrence/frequency       | One per confirmed account creation; expected daily volume `[range]`         |
| Deduplication/idempotency           | Account creation ID or server-confirmed occurrence; no duplicate on refresh |
| Key event                           | Yes, pending product approval                                               |
| Google Ads conversion               | No / pending separate advertising approval                                  |
| Consent requirement/denied behavior | Approved analytics behavior; denied behavior documented                     |
| Required parameters                 | `method`, `form_id`                                                         |
| Optional parameters                 | `account_type` if approved                                                  |
| Negative cases                      | Invalid input, server failure, duplicate submit, refresh                    |
| Data classification/privacy status  | Internal; no PII; controlled vocabulary only                                |
| Downstream consumers                | Reports, Exploration, audience, Ads import, exports                         |
| Report / custom-definition ID       | `R-REG-001` / `[CD-ID or N/A]`                                              |
| QA / evidence ID                    | `TC-REG-001` / `[evidence link]`                                            |
| Release / monitoring ID             | `[release ID]` / `[monitor ID]`                                             |
| Owner / reviewer                    | `[name/team]`                                                               |
| Lifecycle/status/review date        | QA / YYYY-MM-DD / `[next review]`                                           |

### Parameter dictionary template

**Purpose:** Use this dictionary to define every approved event parameter before implementation. It prevents different teams from using the same field name with different meanings, types, values, or privacy behavior.

For reporting readiness, link to the [Field-readiness inventory](09-reports-charts-answer.md) instead of copying the full report template into this document. Record the report field or custom-definition ID below.

| Event     | Parameter      | Meaning                   | Type   | Scope | Required? | Allowed values             | Missing/invalid behavior | Source of truth | Schema | Validation rule             | Report/custom-definition ID | Privacy          | Consent            | Cardinality/volume |
| --------- | -------------- | ------------------------- | ------ | ----- | --------- | -------------------------- | ------------------------ | --------------- | ------ | --------------------------- | --------------------------- | ---------------- | ------------------ | ------------------ |
| `sign_up` | `method`       | Registration method       | string | Event | Yes       | `email`, `google`, `apple` | QA failure               | Application     | `v1`   | Controlled vocabulary       | Standard/review             | Internal         | Analytics approved | Low / `[range]`    |
| `sign_up` | `form_id`      | Stable form identifier    | string | Event | Yes       | Approved IDs               | QA failure               | Application     | `v1`   | Approved ID, bounded length | `[CD-ID if needed]`         | Internal         | Analytics approved | Low / `[range]`    |
| `sign_up` | `account_type` | Approved account category | string | Event | No        | Controlled list            | Omit                     | Application     | `v1`   | Controlled vocabulary       | `[CD-ID if needed]`         | Sensitive review | `[consent]`        | `[range]`          |

### Traceability matrix template

**Purpose:** Use this matrix to prove that one business requirement is connected end to end. It is the index that lets a reviewer move from the requirement to implementation, request evidence, reporting, QA, consent, and release records.

| Requirement/Event ID    | Application state | Data Layer            | GTM                  | Consent behavior            | Request evidence        | GA4/report field      | QA/Evidence ID | Release ID     | Status | Owner    |
| ----------------------- | ----------------- | --------------------- | -------------------- | --------------------------- | ----------------------- | --------------------- | -------------- | -------------- | ------ | -------- |
| REQ-REG-001 / `sign_up` | Server success    | `sign_up` pushed once | CE trigger + GA4 tag | Approved analytics behavior | One request, correct ID | Event count/key event | TC-01/TC-03    | `[release ID]` | QA     | `[name]` |

### Key-event and custom-definition decision record

**Purpose:** Use this record to explain why an event is marked as a key event or why a parameter needs a custom dimension/metric. It prevents important GA4 settings from being changed without a business reason, owner, or privacy review.

```text
Decision ID:
Event/parameter:
Requirement/journey ID:
Business question:
Decision owner:
Business purpose:
Success condition:
Expected occurrence and volume:
Deduplication rule:
Mark as GA4 key event? [Yes/No]
Reason:
Use for Google Ads conversion/bidding? [Yes/No/Pending]
Standard field checked:
Custom dimension/metric required? [Yes/No]
Custom definition ID/status:
Cardinality and quota review:
Consent/privacy impact:
Report/audience/export consumers:
QA/Evidence ID:
Approval and change owner:
Effective/review date:
```

### Schema change and lifecycle register

**Purpose:** Use this register to manage changes to event meaning, parameter type/scope, allowed values, or downstream behavior. It is different from a release record: this register describes the contract change, while the release record describes the deployment.

| Change ID  | Event/parameter  | Current version | Proposed version | Change type          | Affected consumers       | Migration plan                   | Approval owner  | Effective date | Status   |
| ---------- | ---------------- | --------------- | ---------------- | -------------------- | ------------------------ | -------------------------------- | --------------- | -------------- | -------- |
| CH-REG-001 | `sign_up.method` | `v1`            | `v2`             | Allowed-value change | Report, Exploration, Ads | Dual-write and migrate consumers | Analytics owner | YYYY-MM-DD     | Proposed |

### Consent and data classification matrix

**Purpose:** Use this matrix to record how event/parameter data is classified and what happens when the relevant consent is denied. Keep detailed consent implementation and test cases in [Consent Management](05-consent-answer.md).

| Event/parameter  | Data classification | Consent requirement | Denied behavior                            | Destination    | Retention       | Privacy owner | Evidence/approval | Status   |
| ---------------- | ------------------- | ------------------- | ------------------------------------------ | -------------- | --------------- | ------------- | ----------------- | -------- |
| `sign_up.method` | Internal            | Analytics consent   | Omit or block according to approved design | GA4 web stream | Property policy | Privacy team  | `[link]`          | Approved |

## Completed Example — Registration Journey

This example shows what a completed, implementation-ready Measurement Plan can look like. It is a sample, not a copy-paste production contract. Replace the property, stream, owner, URLs, allowed values, evidence links, and approval names with project-approved values.

### Template selection for this registration example

This Registration Journey has three events, one approved business outcome, a client-side web collection path, and a reporting use case. It does **not** require every template in this learning set. Use the following selection:

| Level | Template or record | Canonical source / relationship | Why it applies to this example |
| --- | --- | --- | --- |
| **Required for tracking design and implementation** | Project Context and Baseline | Canonical plan-level context record | Fixes the property, stream, environment, owners, source, and version that the contract applies to. |
| **Required for tracking design and implementation** | Journey and event coverage matrix | Canonical Measurement Plan template | Registration has a multi-event journey; the matrix confirms that start, error, and confirmed completion are covered. |
| **Required for tracking design and implementation** | Event contract, one record per event | Canonical Event contract template | Defines the business meaning, authoritative moment, timing, parameters, deduplication, destination, and owner. |
| **Required for tracking design and implementation** | Parameter dictionary | Canonical Parameter dictionary template | Defines `form_id`, `method`, and `error_type`, including type, scope, allowed values, privacy, consent, and invalid behavior. |
| **Required for tracking design and implementation** | Traceability matrix | Canonical Traceability matrix; the mapping view below is derived from it | Connects application state, Data Layer, GTM, consent, request destination, GA4 fields, QA evidence, and release ID. |
| **Optional derived view** | Event coverage summary / Event plan | Derived from the Journey coverage matrix and Event contract records | Provides a compact reader-friendly summary; it is not a second source of truth. |
| **Optional derived view** | Data Layer, GTM, and destination mapping | Derived from the Traceability matrix and routing fields in Event contracts | Makes implementation routing easy to read; it must not redefine business meaning. |
| **Required before production collection** | Consent and data classification matrix | Canonical Consent and data classification matrix | Records what may be collected, what happens when consent is denied, and what data is prohibited. |
| **Required before production collection** | Section 08 Required Test Matrix and Evidence Template | Canonical QA records in [Debug/QA](08-debug-qa-answer.md) | Proves positive, negative, duplicate, consent, privacy, and routing behavior across the collection layers. |
| **Required in this example because `sign_up` is a key event or custom fields are used** | Key-event and custom-definition decision record | Canonical decision record in this Measurement Plan | Separately justifies the GA4 key event and the registration of `method`/`error_type`; key-event marking does not validate collection. |
| **Required only if the report is part of the deliverable** | Section 09 Report Requirements and Field-readiness templates | Canonical reporting records in [Reports and Charts](09-reports-charts-answer.md) | Defines the completion-rate question, denominator, report fields, and availability. Tracking can be implemented without building the final report. |
| **Required before production activation when the change is material** | Approval record and Section 10 Release Record | Project governance record plus [Release & Monitoring](10-release-monitoring-answer.md) | Records the accountable approval, deployment version, smoke test, observation window, and rollback/monitoring decision. |

The minimum tracking packet for this example is therefore: **context → journey coverage → event contracts → parameter dictionary → traceability → consent/privacy → QA evidence**. Add the key-event/custom-definition record because this example uses those decisions. Add reporting, release, and monitoring records only when the corresponding deliverable or production change is in scope.

#### Not required for this Registration example

| Template or record | Why it is out of scope or deferred |
| --- | --- |
| Ecommerce event and item schema addendum | The journey does not measure products, carts, purchases, refunds, items, or revenue. |
| Server/offline event addendum | The example uses a browser → Data Layer → GTM → GA4 web-stream path. Add it only if `sign_up` is also sent from a backend/offline source. |
| User-ID and user-property contract | Registration completion is measured without an authenticated identity field. Add it only when the feature includes approved sign-in identity behavior. |
| Report Configuration, Chart Specification, and Interpretation Note | These are needed after a report/chart is actually built or published, not merely to define the tracking contract. |
| Schema change and lifecycle register | Not needed for the initial `v1` contract; use it when changing event meaning, parameter type/scope, allowed values, or downstream consumers. |
| GA4 Operations scenario/change/governance templates | Not needed unless this feature changes a property setting, access, filter, attribution, identity, integration, or other operational state. |

The sections below intentionally show some deferred or derived views so the complete lifecycle is visible. They should not be interpreted as additional mandatory templates. Maintain one canonical record and link to it rather than copying the same decision into multiple tables.

The example deliberately separates:

- the **business truth**: an account was actually created;
- the **collection contract**: which event and parameters are sent, when, and once per occurrence;
- the **reporting contract**: which collected fields are registered and used in a report;
- the **governance decision**: whether `sign_up` is a GA4 key event and whether it is imported into Google Ads.

Marking an event as a key event does not repair bad collection. The contract must be valid and the event must pass QA before it is used for business or advertising decisions.

### 1. Context, decision, and scope

```text
Plan ID/version: MP-REG-001 / v1.0
Journey ID: J-REG-001
Business area: Product growth and account creation
Business owner: Product Growth team
Analytics owner: Product Analytics
Technical owner: Registration application team
GTM owner: Web Analytics Engineering
QA owner: Analytics QA

Business question: Where do users abandon registration, and is confirmed account creation recorded once?
Decision supported: Prioritize the registration step with the largest validated user drop-off and investigate the responsible UX or technical issue.
Population: Users who enter the registration journey in the approved QA or production web property.
Grain: User-level completion rate and event-level delivery/duplicate checks.
Source of truth: Registration application and account-creation service response.
Authoritative success moment: The backend confirms that the account has been created.
Collection path: Web application → Data Layer → GTM → GA4 web stream.
Environments: QA stream first; production stream only after approval and release validation.
Key event: sign_up, after the business and QA approvals below.
Google Ads conversion: Not imported in this example; requires a separate advertising decision.
```

The source of truth is the account-creation response, not the form submit click, button state, URL, thank-you component, or a front-end success message that can appear before the backend confirms the account. This distinction protects the completion rate from premature success events.

### 2. Journey and event coverage matrix — required

| Journey ID | Step        | Business state                                        | Event                | Event classification | Expected occurrence                                 | Primary consumer                        |
| ---------- | ----------- | ----------------------------------------------------- | -------------------- | -------------------- | --------------------------------------------------- | --------------------------------------- |
| J-REG-001  | 1. Start    | Registration form is opened and ready for the user    | `registration_start` | Custom event         | Once per intended journey start; not on SPA remount | Funnel entry and start-volume QA        |
| J-REG-001  | 2. Error    | A planned validation or server error is visibly shown | `registration_error` | Custom event         | Once per approved displayed error occurrence        | Error breakdown and troubleshooting     |
| J-REG-001  | 3. Complete | Account-creation service confirms a new account       | `sign_up`            | Recommended event    | Once per confirmed account                          | Completion rate and key-event reporting |

Do not create a separate success event for the submit button. If a user clicks Submit but validation fails or the server rejects the request, the journey has not reached `sign_up`.

### 3. Event coverage summary — optional derived view

This is a compact view of the Journey and event coverage matrix above and the detailed Event contract records below. Use it for orientation; the detailed contract remains the source of truth.

| Event                | Business definition                                                       | Authoritative moment/source                         | Required parameters     | Expected occurrence and deduplication                                                              | Key event decision  | Report use                                  |
| -------------------- | ------------------------------------------------------------------------- | --------------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------- | ------------------- | ------------------------------------------- |
| `registration_start` | The user has entered a registration journey and the form is ready to use. | Registration application state; form-ready callback | `form_id`               | One per intended journey start; suppress duplicate pushes caused by remounts                       | No                  | Funnel entry and start-user denominator     |
| `registration_error` | An approved validation or server error is shown to the user.              | Registration application error state                | `form_id`, `error_type` | One per displayed error occurrence; do not send hidden or raw error text                           | No                  | Error count and controlled error breakdown  |
| `sign_up`            | The account-creation service confirms that a new account exists.          | Backend/application success response                | `method`, `form_id`     | One per confirmed account; no event on failure, retry before success, or confirmation-page refresh | Yes, after approval | Completion rate and GA4 key-event reporting |

The three rows use different event classifications intentionally: `sign_up` follows GA4’s recommended event taxonomy, while the journey-specific start and error states are custom events. A custom event is appropriate only because these business moments are not replaced by a suitable automatic or recommended event.

### 4. Event contract records — required, one per event

#### `registration_start`

```text
Event type: Custom
Business meaning: A user has entered the registration journey and the form is usable.
Authoritative source: Registration application form-ready state.
Trigger condition: The form becomes ready after the user intentionally enters registration.
Do not trigger on: Generic page load, SPA component remount, hidden pre-render, or every field focus.
Expected frequency: One per intended journey start.
Required parameter: form_id = registration.
Negative cases: A page view without entering the form; component remount; browser back/forward that does not create a new intended journey.
Downstream use: Funnel entry, start-user denominator, start-volume QA.
Owner: Registration application team.
```

#### `registration_error`

```text
Event type: Custom
Business meaning: A planned validation or server error is visible to the user.
Authoritative source: Registration application error state and approved error taxonomy.
Trigger condition: An approved error is rendered or announced to the user.
Do not trigger on: Hidden errors, developer logs, raw server messages, stack traces, email addresses, or passwords.
Expected frequency: One per approved displayed error occurrence.
Required parameters: form_id = registration; error_type from the controlled list.
Negative cases: No error shown; duplicate rendering of the same error; unapproved error type.
Downstream use: Error breakdown, QA diagnosis, and product investigation.
Owner: Registration application team.
```

#### `sign_up`

```text
Event type: Recommended event
Business meaning: A new account has been confirmed by the account-creation service.
Authoritative source: Successful backend/application account-creation response.
Trigger condition: The application receives a confirmed success state and has not already emitted the event for that account creation.
Do not trigger on: Submit click, client-side validation pass, loading state, optimistic UI, payment/auth redirect before confirmation, or page refresh.
Expected frequency: One per confirmed account creation.
Required parameters: method from the controlled list; form_id = registration.
Deduplication: Application-level idempotency and one client emission per confirmed business occurrence; refresh must not emit again.
Downstream use: Completion rate and optional GA4 key event.
Owner: Registration application team with Analytics review.
```

If the organization later sends `sign_up` from a server/offline source, choose one authoritative collection path or document a deduplication design before release. Do not send the same confirmed account from both the browser and server without a proven way to prevent double counting.

### 5. Parameter dictionary and data minimization — required

| Event                                                 | Parameter        | Meaning                                          | Type/scope                       | Required?             | Allowed values                                  | Missing/invalid behavior                                           | Source of truth                        | GA4 reporting decision                                                                                   | Privacy/cardinality                       |
| ----------------------------------------------------- | ---------------- | ------------------------------------------------ | -------------------------------- | --------------------- | ----------------------------------------------- | ------------------------------------------------------------------ | -------------------------------------- | -------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| `registration_start`, `registration_error`, `sign_up` | `form_id`        | Stable identifier for the registration form      | String / event                   | Yes                   | `registration` and other approved bounded IDs   | Block or fail QA; do not use arbitrary text                        | Registration application               | Collect for QA/routing; register as a custom dimension only if multiple forms require recurring analysis | Internal, non-PII, low cardinality        |
| `registration_error`                                  | `error_type`     | Controlled reason category for the visible error | String / event                   | Yes                   | `validation`, `duplicate_email`, `server_error` | Use only the approved taxonomy; omit or fail QA for unknown values | Application error mapping              | Register as an event-scoped custom dimension if error breakdown is an approved recurring use case        | Internal, non-PII, bounded vocabulary     |
| `sign_up`                                             | `method`         | Method used to create the account                | String / event                   | Yes                   | `email`, `google`, `apple`                      | Do not send the event with an unapproved value; fail QA            | Registration application/auth response | Register as an event-scoped custom dimension for the registration health report                          | Internal, non-PII, low cardinality        |
| Data Layer only                                       | `schema_version` | Version of the application/Data Layer contract   | String / implementation metadata | Yes in the Data Layer | `v1`                                            | Block release if missing or unexpected                             | Application contract                   | Do not send to GA4 unless a separate reporting use is approved                                           | Internal metadata; not a GA4 report field |

Do not collect email, phone number, password, raw error message, stack trace, account name, raw database ID, session ID, timestamp-as-a-dimension, or a free-text form value for this journey. A QA correlation ID may exist in internal logs, but it must not be sent to GA4 unless separately reviewed and justified.

`schema_version` is shown in the Data Layer examples to support implementation and QA. It is not automatically a GA4 event parameter or custom dimension. Collection truth and reporting truth are separate decisions.

### 6. Data Layer, GTM, and destination mapping — derived implementation view

This is an optional, readable routing view generated from the [Traceability matrix template](#traceability-matrix-template) and the routing fields in each Event contract. It connects the approved application signal to routing and destinations; it does not redefine the business event and must not be maintained as a second source of truth.

```javascript
// schema_version is implementation metadata and is not sent to GA4 by default.
window.dataLayer.push({
  event: "registration_start",
  form_id: "registration",
  schema_version: "v1",
});

window.dataLayer.push({
  event: "registration_error",
  form_id: "registration",
  error_type: "validation",
  schema_version: "v1",
});

window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "registration",
  schema_version: "v1",
});
```

| Event                | Data Layer signal            | GTM trigger/tag                                                          | Destination                 | Routing rule                                                        |
| -------------------- | ---------------------------- | ------------------------------------------------------------------------ | --------------------------- | ------------------------------------------------------------------- |
| `registration_start` | `event = registration_start` | `CE - Web - registration_start` → `GA4 Event - Web - registration_start` | QA or production web stream | Fire once for the intended journey start                            |
| `registration_error` | `event = registration_error` | `CE - Web - registration_error` → `GA4 Event - Web - registration_error` | QA or production web stream | Fire only for an approved visible error and controlled `error_type` |
| `sign_up`            | `event = sign_up`            | `CE - Web - sign_up` → `GA4 Event - Web - sign_up`                       | QA or production web stream | Fire only after confirmed account creation and once per occurrence  |

The GTM trigger routes the application signal; it does not decide that a button click means an account was created. The GA4 Event tag must map only the approved parameters and must not forward the entire Data Layer object.

### 7. Consent and data classification matrix — required before production

| Data                                                   | Classification                | Consent requirement                          | Behavior when consent is denied                                                   | Destination                | Retention/owner                 |
| ------------------------------------------------------ | ----------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------- | ------------------------------- |
| `form_id`, `method`, `error_type`                      | Internal, controlled, non-PII | Follow the approved analytics consent design | Block, omit, or use the approved Consent Mode behavior; never bypass the decision | GA4 web stream             | Property policy / Privacy owner |
| Email, phone, password, raw error text, raw account ID | Prohibited for this contract  | Not applicable                               | Do not collect or forward                                                         | None                       | Privacy owner                   |
| User-ID after authenticated sign-in                    | Separate identity contract    | Separate approved conditions                 | Clear or omit according to the identity contract                                  | GA4 identity configuration | Identity owner                  |

The exact denied behavior must be copied from the approved Consent Management design for the property. “Consent denied” is not a reason to send the same prohibited parameter through another tag or destination.

### 8. Key-event and custom-definition decision record — required in this example

```text
Decision ID: DEC-REG-001
Event: sign_up
Business purpose: Measure confirmed account creation and registration completion.
Mark as GA4 key event: Yes, after collection QA passes and Product approves.
Reason: Confirmed account creation is an outcome the business intends to monitor; start and error events are supporting diagnostics, not outcomes.
Google Ads conversion/bidding: No in this example; a separate advertising owner must approve any import or bidding use.
Deduplication rule: One event per confirmed account creation; no event on failed validation, server failure, retry before success, or refresh.
Required evidence: Application success state, Data Layer, GTM, Network, consent, DebugView, and processed report evidence.
```

The key-event label is downstream configuration. It does not validate the event name, timing, parameters, consent, destination, or duplicate behavior. A duplicated or premature `sign_up` would be more harmful than leaving an otherwise valid event unmarked because it could distort product decisions and advertising outcomes.

### 9. Reporting readiness and report requirements — conditional handoff to Section 09

This section is a reporting handoff, not a duplicate of the full reporting template set. The canonical report configuration, chart specification, and interpretation note live in [Reports and Charts](09-reports-charts-answer.md); the Measurement Plan stores the relevant IDs and contract decisions.

#### Field-readiness decision

| Field            | Collection status                 | Scope          | Reporting status                                                    | Rationale/limitation                                                                 |
| ---------------- | --------------------------------- | -------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `event_name`     | Collected as standard GA4 field   | Event          | Ready                                                               | Use the standard event name rather than creating a separate event category parameter |
| `method`         | Collected on `sign_up`            | Event          | Register as an event-scoped custom dimension after QA               | Needed for recurring completion breakdown; controlled low-cardinality values         |
| `error_type`     | Collected on `registration_error` | Event          | Register only if the product team approves recurring error analysis | Useful for diagnosis; keep the vocabulary bounded                                    |
| `form_id`        | Collected on all journey events   | Event          | Keep collected for QA/routing; custom dimension optional            | One current form does not justify a new report field; reassess if forms multiply     |
| `schema_version` | Data Layer metadata               | Implementation | Not reportable by default                                           | Used to identify contract version during QA, not a business dimension                |

Register custom definitions only after collection has passed QA. Record the registration date, expected availability delay, scope, cardinality review, and report that consumes each definition. Do not create a custom dimension for every parameter merely because the parameter is present in the request.

#### Report requirements

| Report ID | Audience       | Business question                                                   | Decision                                 | Population/grain                              | Dimensions                                      | Metrics/formula                                        | Surface                                        | Owner             |
| --------- | -------------- | ------------------------------------------------------------------- | ---------------------------------------- | --------------------------------------------- | ----------------------------------------------- | ------------------------------------------------------ | ---------------------------------------------- | ----------------- |
| R-REG-001 | Product Growth | Which registration method has the lowest validated completion rate? | Prioritize UX or technical investigation | Users entering registration; user-level rate  | `method`, `device category`, date               | Users with `sign_up` / users with `registration_start` | Detail report + funnel Exploration             | Product Analytics |
| R-REG-002 | Analytics/QA   | Is confirmed account creation sent once with valid values?          | Approve or fix the release               | Event occurrences in a controlled test period | `event_name`, `method`, `form_id`, `error_type` | Event count and duplicate review                       | Free-form Exploration + processed event report | Analytics QA      |

For R-REG-001, “completion rate” means the documented user-level calculation:

```text
Registration completion rate
  = users with at least one valid sign_up
    / users with at least one valid registration_start
```

The numerator and denominator must use the same date range, population, identity context, and approved registration journey. `sign_up` event count is not the same as completed users, and a lower rate by method is a reason to investigate—not proof that the method caused the drop.

### 10. QA and evidence matrix — required before production; canonical records live in Section 08

This section summarizes the QA contract for the journey. The canonical test cases, debug session records, defect records, and evidence are maintained in [Debug/QA](08-debug-qa-answer.md); the Measurement Plan links their IDs and expected outcomes.

| Test ID    | Scenario                         | Expected application/Data Layer result                           | Expected tag/request result                                   | Report/decision evidence                                | Status    |
| ---------- | -------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------- | --------- |
| TC-REG-001 | Form opens and is ready          | One `registration_start`; `form_id=registration`; no PII         | One request to the QA Measurement ID                          | Event appears once in DebugView/test report             | Pass/Fail |
| TC-REG-002 | Invalid input                    | No `sign_up`; approved `registration_error` only if visible      | No success request; error request has controlled `error_type` | Error breakdown is explainable                          | Pass/Fail |
| TC-REG-003 | Server failure                   | No `sign_up`; controlled server error only if planned            | No success request                                            | Failure is recorded without a false completion          | Pass/Fail |
| TC-REG-004 | Confirmed account creation       | One valid `sign_up` with approved `method` and `form_id`         | One request to the intended QA/production destination         | Completion/key-event data is plausible after processing | Pass/Fail |
| TC-REG-005 | Rapid double submit/retry        | One confirmed business occurrence and one `sign_up`              | No duplicate success request                                  | Event count reconciles with application result          | Pass/Fail |
| TC-REG-006 | Refresh/back/SPA remount         | No duplicate `sign_up`; no unintended extra start                | No unexpected duplicate request                               | Duplicate review is documented                          | Pass/Fail |
| TC-REG-007 | Consent denied                   | Behavior matches the approved consent design; no prohibited data | No unauthorized request or parameter                          | Consent evidence is attached                            | Pass/Fail |
| TC-REG-008 | Wrong environment or destination | Test is blocked or routed to the intended stream                 | Request contains the approved Measurement ID only             | Routing evidence is attached                            | Pass/Fail |
| TC-REG-009 | User-ID out-of-scope check       | Registration tracking does not introduce an identity field       | No email/phone/raw identifier or unapproved User-ID parameter | Identity tracking remains separately scoped              | Pass/Fail/Out of scope |
| TC-REG-010 | Collection source ownership     | Only the canonical application → Data Layer path is used; no undocumented duplicate source | One request or documented deduplication behavior | Source map and request timeline are attached | Pass/Fail |

For each test, store the same `Test ID` across application evidence, Data Layer inspection, GTM Preview, Network request, consent state, DebugView, and processed reporting. Record the first failing layer rather than marking a test as passed because the GTM tag fired.

### 11. Traceability matrix and consumers — canonical record

This is the canonical traceability record for the example. Section 6 is only a derived routing view of this record; do not maintain both independently.

| Requirement/event                  | Application/source                 | Data Layer                               | GTM                                  | Consent                     | Request/destination                | GA4/report field                | QA/release evidence                    | Owner/status     |
| ---------------------------------- | ---------------------------------- | ---------------------------------------- | ------------------------------------ | --------------------------- | ---------------------------------- | ------------------------------- | -------------------------------------- | ---------------- |
| REQ-REG-001 / `registration_start` | Form-ready state                   | `registration_start` once                | Custom Event trigger + GA4 Event tag | Approved analytics behavior | One request to approved web stream | Event name, users, funnel entry | TC-REG-001/006/007; `[release ID]`     | Product app / QA |
| REQ-REG-001 / `registration_error` | Visible approved error             | `registration_error` once per occurrence | Custom Event trigger + GA4 Event tag | Approved analytics behavior | Controlled `error_type` only       | Event name, `error_type`        | TC-REG-002/003/007; `[release ID]`     | Product app / QA |
| REQ-REG-001 / `sign_up`            | Backend-confirmed account creation | `sign_up` once                           | Custom Event trigger + GA4 Event tag | Approved analytics behavior | One request to approved web stream | Event name, `method`, key event | TC-REG-004/005/006/007; `[release ID]` | Product app / QA |

Consumers are explicitly limited to the approved registration detail report, funnel Exploration, DebugView/processed QA report, and optional GA4 key-event reporting. Google Ads import is not a hidden consumer; it requires a separate approval record.

### 12. Approval record and schema lifecycle — conditional

#### Example approval record

| Gate                  | Reviewer                          | Evidence or decision                                                                  | Status                             |
| --------------------- | --------------------------------- | ------------------------------------------------------------------------------------- | ---------------------------------- |
| Business scope        | Product Growth + Analytics        | Question, decision, journey, population, and owner approved                           | Pass                               |
| Event semantics       | Product + Application + Analytics | Backend-confirmed success, controlled error taxonomy, and deduplication rule approved | Pass                               |
| Technical feasibility | Application + GTM                 | Data Layer signals, environment routing, and parameter mapping confirmed              | Pass                               |
| Privacy/consent       | Privacy + Analytics               | No PII; denied behavior linked to approved Consent Management design                  | Pass                               |
| Reporting readiness   | Reporting + Analytics             | `method` and, if approved, `error_type` custom definitions; user-level rate defined   | Pass with registration pending     |
| QA/release readiness  | QA + Implementation               | Positive, negative, duplicate, boundary, consent, privacy, and routing cases ready    | Pass for QA; production pending    |
| Final plan decision   | Final plan owner                  | Approved for QA implementation; production activation follows release evidence        | Approved with controlled exception |

```text
Plan ID/version: MP-REG-001 / v1.0
Approval outcome: Approved with controlled exception
Exception: Custom definitions are registered only after QA evidence passes; production activation requires release approval.
Exception owner/due date: Product Analytics / YYYY-MM-DD
Evidence location: [sanitized ticket, QA evidence, report configuration, release record]
Next review: On schema change, consent change, report change, or scheduled quarterly review.
```

“Approved with controlled exception” does not mean that unresolved business truth can be implemented. The exception is limited to delayed reporting registration and production activation; the event contract itself is complete and testable.

#### Example schema lifecycle register

| Change ID  | Event/parameter  | Current version | Proposed version | Change type                   | Affected consumers                            | Migration/approval action                                                                              | Status   |
| ---------- | ---------------- | --------------- | ---------------- | ----------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------ | -------- |
| CH-REG-001 | `sign_up.method` | `v1`            | `v2`             | Add approved method `passkey` | Registration report, Exploration, QA taxonomy | Update allowed values, dual-test, review cardinality, update report descriptions, then approve release | Proposed |

Adding a new method is not only a code change. It changes the parameter contract, QA cases, report interpretation, and possibly the key-event or advertising audience. Use a new schema version and update affected consumers before the new value is released.

## Common Anti-Patterns

| Anti-pattern                                                  | Why it fails                                              | Better approach                                                             |
| ------------------------------------------------------------- | --------------------------------------------------------- | --------------------------------------------------------------------------- |
| Track every click without a decision                          | Creates noise and maintenance cost.                       | Start from a decision and measurable outcome.                               |
| Fire a success event on button click                          | A click is not proof of business success.                 | Use confirmed application/backend state.                                    |
| Encode values in event names                                  | Fragments reporting and makes the schema unstable.        | Use one event with controlled parameters.                                   |
| Send raw form fields or create dimensions for every parameter | Creates privacy risk, quota pressure, and report clutter. | Send only approved fields and register only the fields needed for analysis. |
| Duplicate Enhanced Measurement with GTM                       | Counts one interaction more than once.                    | Check existing automatic collection before custom tagging.                  |
| Rename events casually                                        | Breaks reports, key events, audiences, and baselines.     | Use a versioned migration and consumer-impact review.                       |

## Official References

- [About events](https://support.google.com/analytics/answer/9322688)
- [Enhanced measurement events](https://support.google.com/analytics/answer/9216061?hl=en)
- [Recommended events](https://support.google.com/analytics/answer/9267735)
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
