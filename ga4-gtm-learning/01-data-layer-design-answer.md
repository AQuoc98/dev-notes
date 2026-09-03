# 01 — Data Layer Design

## 1. Objective and scope

This document defines how the frontend application publishes reliable, privacy-safe business events for Google Tag Manager (GTM) and Google Analytics 4 (GA4).

The Data Layer is the contract boundary between application code and GTM. The application owns the business truth; GTM reads approved values and routes them to GA4. The design is intended for stable, repeatable GTM/GA4 implementation, not advertising or campaign optimization.

### In scope

- Event naming, occurrence rules, payload schema, types, allowed values, and versioning.
- A complete, self-contained `dataLayer.push()` message for each approved occurrence.
- A typed frontend analytics adapter and safeguards for asynchronous APIs and SPA lifecycles.
- Privacy, consent boundary, duplicate prevention, validation, and handoff to GTM.
- FD `calculation_action` as the canonical implementation pattern.

### Out of scope

- GTM Variables, Triggers, Tags, or GA4 report configuration in detail; see Sections 02–04 and 09.
- Consent-management implementation details; see Section 05.
- Template governance, release monitoring, or incident operations; see Sections 06, 08, and 10.
- Google Ads, media buying, campaign optimization, or advertising attribution.

## 2. Overview: system boundary and event lifecycle

### 2.1 Component roles

These definitions follow the roles described in Google's Tag Manager and GA4 documentation:

| Component                 | Plain-language meaning and responsibility                                                                                                                                                 |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Application (website/app) | Product code that renders the UI, receives input, calls APIs, decides whether the business outcome happened, and publishes the approved event.                                            |
| Data Layer                | A JavaScript object, normally `window.dataLayer`, used by GTM and `gtag.js` to pass structured event data and variables to tags. It carries data; it does not send data to GA4 by itself. |
| Google Tag Manager (GTM)  | The tag-management system that reads Data Layer values through Variables, listens with Triggers, applies consent/routing rules, and runs Tags that send data to a destination.            |
| Google Analytics 4 (GA4)  | The Analytics property that receives events and parameters from a Google tag or GA4 Event tag and processes them for Realtime, DebugView, Reports, and Explorations.                      |

In one sentence: the Application publishes the fact, the Data Layer carries it, GTM routes and sends it, and GA4 receives and analyzes it.

### 2.2 Event lifecycle

```text
Application confirms a business fact
        ↓
Application pushes one complete Data Layer message
        ↓
GTM processes the message in queue order
        ↓
GTM Variables read approved fields
        ↓
GTM Trigger identifies the event
        ↓
Consent and destination rules are evaluated
        ↓
GA4 Event tag sends the approved payload
```

Boundary rule: the Application owns business truth, the Data Layer carries the message, GTM routes and sends it, and GA4 receives and analyzes it. The event is pushed only after the Application can prove the business outcome, once for each valid occurrence.

## 3. Core design rules

### 3.1 Name the business fact, not the UI action

Use a stable business event name such as `sign_up`, `purchase`, or `calculation_action`. Do not name an event after a button color, CSS selector, component, or screen position. UI implementations can change while the business meaning remains stable.

### 3.2 Define when one calculation is valid

Before implementation, write down when a business event counts as having happened. Examples include:

- a server-confirmed account creation;
- a purchase accepted with an authoritative transaction;
- one accepted FD input snapshot whose matching calculation response has been classified.

An input change, click, request start, or component render is not enough on its own. For FD, a valid response that produces no solution is still one valid calculation (`solution_found = false`). Invalid input, cancellation, timeout, or server failure means that the calculation did not complete; do not record it as a successful calculation. If failures need to be measured, define a separate error event.

### 3.3 Make every message self-contained

Put the event name and all required event-specific values in the same push. Do not rely on values left by a previous message. This prevents GTM Variables from reading stale data when the application emits events in quick succession.

### 3.4 Use an explicit, versioned contract

A `contract` is the shared set of rules that the Application, Data Layer, GTM, and GA4 must follow. For every field, document its name, data type, required/optional status, source, allowed values or unit, privacy classification, and missing-data behavior. Keep values stable for machine use; do not make GTM convert UI display labels into canonical values. `event_schema_version` identifies the version of this rule set. When a change makes existing code or configuration incompatible, increment the version and update the Application, GTM, QA, and reporting records together.

### 3.5 Collect the minimum useful data

Every field must answer an approved measurement question. Do not spread the whole form or application state into the Data Layer merely because it is available. Never include email addresses, names, credentials, access tokens, passwords, unrestricted comments, raw user text, or sensitive API output.

### 3.6 Keep business logic in the application

The application or API response decides `solution_found`, transaction validity, registration success, and other outcomes. The analytics adapter only validates and publishes the approved snapshot. GTM should transport and route data, not calculate or infer it.

### 3.7 Treat consent as a separate boundary

The application may create a Data Layer message before consent is granted, but the message is not proof that collection is allowed. Consent defaults, updates, and tag behavior are defined in Section 05 and verified in Section 08.

## 4. Contract design and schema

### 4.1 Contract record

Create one record before coding:

```text
Event name:
Business definition:
Valid occurrence:
Emission timing:
Expected frequency:
Required fields:
Optional fields:
Source of each field:
Allowed values and units:
Privacy classification:
Data Layer schema version:
Owner and approver:
GTM/GA4 consumers:
```

The record is the source for the Measurement Plan (Section 07), GTM asset design (Sections 02–04), QA expectations (Section 08), and report field readiness (Section 09).

### 4.2 Event envelope (common frame) for FD

An `event envelope` is the outer frame of a message. It tells us which event the message represents, which version it uses, which application sent it, what result was produced, and where the input snapshot is stored. The FD calculation contract uses this stable frame:

| Field                  | Type    | Required | Source and rule                                                                                                          |
| ---------------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------ |
| `event`                | string  | Yes      | Application constant; exact value `calculation_action`.                                                                  |
| `event_schema_version` | string  | Yes      | Application constant; current version `1.0`.                                                                             |
| `app_name`             | string  | Yes      | Application constant; exact value `fd`.                                                                                  |
| `solution_found`       | boolean | Yes      | Matching API response; `true` only when that response produced output, `false` only for a valid response with no output. |
| `inputs`               | object  | Yes      | Complete approved snapshot associated with the matching API request.                                                     |

If a business system requires literal `Yes`/`No` strings instead of Boolean values, change the contract deliberately and update the schema version, GTM Variables, QA matrix, and reports together. Do not silently convert types in GTM.

### 4.3 FD `inputs` schema

Keep these fields in the snapshot managed by the Application when they are approved by the Measurement Plan. Use stable machine-readable codes and natural numeric/Boolean types; document units for every numeric value.

| Field                                            | Type    | Rule                                                        |
| ------------------------------------------------ | ------- | ----------------------------------------------------------- |
| `country`                                        | string  | Controlled country code.                                    |
| `language`                                       | string  | Controlled language code.                                   |
| `building_code`                                  | string  | Controlled design-code identifier.                          |
| `design_method`                                  | string  | Controlled design-method enum.                              |
| `unit_system`                                    | string  | Controlled unit-system enum.                                |
| `connection_type`                                | string  | Controlled connection-type enum; review cardinality.        |
| `fastener_installation`                          | string  | Controlled installation enum.                               |
| `fx`, `fy`                                       | number  | Numeric input with documented unit/meaning.                 |
| `load_duration`                                  | string  | Controlled load-duration enum.                              |
| `main_member_thickness`, `side_member_thickness` | number  | Numeric thickness with documented unit.                     |
| `side_member_grade`, `main_member_grade`         | string  | Controlled material-grade enum.                             |
| `side_member_density`, `main_member_density`     | number  | Numeric density with documented unit.                       |
| `contact_length`                                 | number  | Numeric length with documented unit.                        |
| `predrilled`                                     | boolean | Boolean; do not use `Yes`/`No` unless the contract changes. |
| `fastener_angle`                                 | number  | Numeric angle with documented unit.                         |
| `service_class`                                  | string  | Controlled service-class enum.                              |

The Application should retain the complete input snapshot sent to the API so QA can match a response to the exact input set. In GTM/GA4, map only approved scalar fields—single-value fields such as `connection_type`, `unit_system`, or `solution_found`—when they are on the reporting allowlist; do not send the whole `inputs` object as one parameter. If the Application uses a temporary token to match a request with its response, keep that token in application logs. Do not send it to GA4 unless it has been separately approved because it is not normally a reporting field.

### 4.4 Naming and data-shape rules

- Use lower `snake_case` for event and payload keys.
- Use one canonical event name; do not create aliases such as `fd_calc`, `calculate_click`, and `calculation_done` for the same business fact.
- Keep nested objects only when the contract benefits from a clear namespace such as `inputs`. GTM reads nested paths with Data Layer Variable Version 2 (Section 02).
- Put `event` and all required values in the same push.
- Omit optional values only when omission is defined; never replace missing data with an empty string, `unknown`, or a previous value.
- Preserve natural types. Normalization from UI labels or strings belongs in application code.

## 5. Frontend implementation pattern

### 5.1 Use one typed analytics adapter (an adapter with type checking)

Keep raw `dataLayer.push()` calls out of individual UI components. A small application-owned adapter makes event names and payload types searchable, reviewable, mockable, and testable. The adapter must not contain product decision logic.

### 5.2 Snapshot and asynchronous API handling

A `snapshot` is a complete “picture” of the input values at one point in time. When an event depends on an API, use this sequence:

1. Normalize the current inputs to the defined types and values.
2. Create a complete snapshot and do not change it after sending the request.
3. Send that exact snapshot to the API.
4. When the response returns, verify which snapshot it belongs to using a sequence, request token, or equivalent internal mechanism.
5. Classify the response as output, no output, invalid input, cancelled request, timeout, or server failure.
6. Push one complete event only for response types allowed by the contract.

If the user has entered a newer value, treat the earlier response as stale and do not use it to create an event for the newer value. Do not misclassify a failed request as a “no output” result.

### 5.3 SPA and component-lifecycle safeguards

- Do not emit business success from mount, render, route restoration, or a generic click handler.
- Define debounce or commit behavior for rapidly changing inputs; do not emit one business event for every raw keystroke unless that is explicitly the contract.
- Guard retries, duplicate callbacks, React Strict Mode, remounts, and websocket replays with source-level idempotency.
- Choose one canonical source for SPA page views: Enhanced Measurement, GTM History Change, or an application/router event. Do not overlap sources.
- Keep analytics transport non-blocking. A tracking failure must be observable in development/QA without breaking the product action.

### 5.4 Application contract tests

Automate at least these checks:

- a confirmed success emits one event with the exact name, version, types, and allowed values;
- a valid no-output response emits the agreed event with `solution_found = false`;
- validation failure, API failure, timeout, cancellation, and abandoned UI emit no successful event;
- stale responses, retries, duplicate callbacks, remounts, and Strict Mode do not create duplicates;
- the message contains the complete approved snapshot and no prohibited field;
- optional fields are omitted rather than invented.

## 6. Validation and handoff to GTM

Before handing the event to GTM, the frontend owner provides:

```text
Event name and schema version:
Occurrence and timing rule:
Expected count per occurrence:
Data Layer key/path and type for every field:
Allowed values and units:
Required versus optional fields:
Valid no-output and failure semantics:
Privacy review:
Consent dependency:
QA build/hostname:
Application test evidence:
```

The GTM implementer then maps approved paths to Variables, creates one authoritative Custom Event Trigger, applies consent and environment routing, and maps an allowlist to the GA4 Event tag. See Sections 02–05 for the implementation details.

## 7. Operational notes and guardrails

### 7.1 Stale data and persistence

GTM can retain Data Layer values across messages. A message that omits a field may therefore appear to have a value from an earlier event. Same-push completeness and explicit missing-data behavior are the controls; do not fix stale values with ad-hoc Custom JavaScript in GTM.

### 7.2 Duplicate prevention

One valid occurrence should produce one application message. Check for duplicate container installation, overlapping GTM Tags, retries, remounts, Strict Mode, SPA route restoration, and multiple analytics libraries. Legitimate separate calculations must remain separate events.

### 7.3 Scope and privacy

Use the full snapshot only where it is needed for application QA or an approved internal record. Send to GA4 only the scalar allowlist needed for a documented question. Never expose PII, secrets, raw free text, or API response bodies in browser-visible analytics payloads.

### 7.4 Debugging boundary

Data Layer presence proves that application code pushed a message; it does not prove that GTM fired, consent allowed collection, the request reached the intended stream, or processed GA4 data is available. Verify each boundary using the Section 08 evidence sequence.

## 8. Cross-reference map

- [Section 02 — Variable Management](02-variable-management-answer.md): canonical Data Layer Variables, nested Version 2 paths, naming, and missing-data behavior.
- [Section 03 — Trigger Management](03-trigger-management-answer.md): Custom Event Trigger filters, exceptions, and firing-count controls.
- [Section 04 — Tag Management](04-tag-management-answer.md): Google tag, GA4 Event tag, parameter allowlist, consent, and destination routing.
- [Section 05 — Consent Management](05-consent-answer.md): consent defaults, updates, and denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer.md): reviewed templates and change governance when a custom template is needed.
- [Section 07 — Measurement Plan](07-measurement-plan-answer.md): business questions, field approval, ownership, and contract versioning.
- [Section 08 — Debug/QA](08-debug-qa-answer.md): test setup, expected behavior, evidence, defects, and retests.
- [Section 09 — Reports and Charts](09-reports-charts-answer.md): field readiness, event-level QA, user-level interpretation, and processing windows.
- [Section 10 — Release Monitoring](10-release-monitoring-answer.md): release gates, smoke tests, observation, incidents, and rollback.

## 9. Examples and worked Journey

The following examples are intentionally kept at the end so the preceding sections remain the reusable contract and implementation guidance.

### 9.1 Business event naming

Avoid coupling an event to a UI detail:

```javascript
// Avoid
window.dataLayer.push({ event: "green_button_click" });

// Prefer: stable business fact
window.dataLayer.push({ event: "sign_up" });
```

### 9.2 Typed adapter example

```typescript
type AnalyticsEvent =
  | {
      event: "sign_up";
      event_schema_version: "1.0";
      method: "email" | "google" | "apple";
      form_id: string;
    }
  | {
      event: "calculation_action";
      event_schema_version: "1.0";
      app_name: "fd";
      solution_found: boolean;
      inputs: {
        connection_type: string;
        unit_system: "metric" | "imperial";
      };
    };

export function track(event: AnalyticsEvent): void {
  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push(event);
}
```

The application service calls the adapter only after the authoritative result is known:

```typescript
const result = await accountService.createAccount(input);

if (result.ok) {
  track({
    event: "sign_up",
    event_schema_version: "1.0",
    method: result.method,
    form_id: "registration",
  });
}
```

### 9.3 FD `calculation_action` Journey

```text
User changes an FD input
→ application creates a complete input snapshot
→ application sends that snapshot to the calculation API
→ API returns a response for that snapshot
→ application determines whether the response produced output
→ application sets solution_found to true or false
→ application pushes one complete calculation_action message
→ GTM maps approved fields and sends the event to GA4
```

Canonical payload for a valid response that produced output:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    country: "gb",
    language: "en",
    building_code: "en_1995_1_1_2004_a2_2014",
    design_method: "lsd",
    unit_system: "metric",
    connection_type: "clt_floor_floor_half_lap_joint",
    fastener_installation: "typical",
    fx: 1,
    fy: 0,
    load_duration: "medium_term",
    main_member_thickness: 180,
    side_member_thickness: 180,
    side_member_grade: "c24",
    side_member_density: 350,
    main_member_grade: "c24",
    main_member_density: 350,
    contact_length: 3000,
    predrilled: false,
    fastener_angle: 90,
    service_class: "service_class_1",
  },
});
```

For a valid response with no output, keep the same complete snapshot and set `solution_found: false`. Do not emit this successful event for invalid input, timeout, cancellation, or server failure unless a separately approved error contract exists.

### 9.4 Optional ecommerce shape

The same Data Layer rules apply to ecommerce, but the contract has event-level and item-level scope. Use this only when the project has an approved ecommerce measurement requirement:

```javascript
window.dataLayer.push({
  event: "purchase",
  ecommerce: {
    transaction_id: "T_12345",
    value: 30.03,
    currency: "USD",
    items: [
      {
        item_id: "SKU_12345",
        price: 10.01,
        quantity: 3,
      },
    ],
  },
});
```

Keep `items` as an array, preserve numeric types, use an authoritative `transaction_id`, and define retry/replay deduplication. See the ecommerce guidance in the Measurement Plan and Debug/QA sections before implementing.

## References

- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [Google Analytics — Measure ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
