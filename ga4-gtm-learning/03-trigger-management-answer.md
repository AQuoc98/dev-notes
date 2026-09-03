# 03 — GTM Trigger Management

## 1. Objective, scope, and outputs

This document defines how to choose, configure, test, publish, and retire Google Tag Manager (GTM) Triggers for stable GA4 measurement.

A Trigger is a rule that listens for an event and decides whether a Tag is eligible to run. A Trigger does not send data by itself. The Tag, consent settings, exceptions, sequencing, browser, and destination configuration still determine whether a request is made.

### In scope

- Selecting the authoritative event source and the correct Trigger type.
- Firing logic, exceptions, filters, timing, Trigger Groups, and environment scope.
- Naming, reuse, inventory, QA, release, and retirement.
- FD `calculation_action` as the reference Custom Event pattern.

### Out of scope

- Variable source/type design; see Section 02.
- Tag and GA4 destination configuration; see Section 04.
- Consent implementation; see Section 05.
- Custom-template governance; see Section 06.
- Measurement planning, Debug/QA evidence, Reports, and Release Monitoring; see Sections 07–10.
- Advertising or campaign-specific trigger design.

### Outputs

Each active Trigger should have:

1. A business event and authoritative source.
2. A documented firing rule, filters, exceptions, consent, and expected frequency.
3. An owner, consumers, environment scope, and lifecycle status.
4. Preview, Network, and downstream evidence appropriate to the Tag.
5. A versioned publication and recoverable change record.

## 2. Overview: how Trigger logic works

### 2.1 Simple definitions

| GTM concept | Practical meaning |
| --- | --- |
| Firing Trigger | The condition that makes a Tag eligible to run. A Tag can have more than one; they behave as alternatives (OR). |
| Trigger condition | A test inside one Trigger. All conditions in that Trigger must be true (AND). |
| Exception/blocking Trigger | A condition that prevents the Tag from running. One matching exception is enough to block it. |
| Consent setting | A separate permission check that can prevent a Tag even when its Trigger matches. |
| Trigger Group | A gate that waits for all member Triggers to occur. It confirms occurrence, not order, and should not recreate an application workflow. |

For a Tag with firing Triggers `F1`, `F2`, exceptions `B1`, `B2`, and a consent requirement:

```text
Tag is eligible when:
(F1 matches OR F2 matches)
AND NOT (B1 matches OR B2 matches)
AND consent/settings allow it
AND sequencing requirements are satisfied
```

The practical rule is: use the smallest scope that still matches the approved business moment. For example, an FD calculation should use the application Custom Event `calculation_action` plus only the required application/schema filters. Do not replace a missing application event with a broad click, page, or DOM rule; those signals cannot prove the calculation result.

## 3. Choose the Trigger

### 3.1 Define the business moment first

Before opening GTM, record:

```text
Business event and definition:
Authoritative source and timing:
Exact event name and casing:
Required filters and Variables:
Required event-specific values:
Expected frequency:
Consent and timing requirement:
Environment scope:
Consuming Tags:
Owner and retirement condition:
```

An input change, click, request start, or component render is not automatically a confirmed business outcome. For FD, the application should push `calculation_action` only after the matching API response is classified (Section 01).

### 3.2 Trigger type selection

| Requirement | Preferred Trigger | Use it when |
| --- | --- | --- |
| Establish consent before tracking | Consent Initialization | Consent defaults or updates must be ready before other Tags. |
| Run early configuration | Initialization | Setup must run before normal page triggers; not as the default for business events. |
| Measure a page starting to load | Page View | The page load itself is the measurement moment. |
| Read elements after HTML parsing | DOM Ready | Required DOM content is unavailable at Page View. |
| Wait for all page resources | Window Loaded | The requirement genuinely depends on images/scripts/resources finishing. |
| Measure an application-confirmed result | Custom Event | The application pushes a named Data Layer event after the result is known. |
| Measure a click or UI intent | Click: All Elements | The click itself is the question; it does not prove success. |
| Measure an anchor click | Click: Just Links | The `<a>` link click is the question. |
| Measure a native form submit | Form Submission | The site uses a standard HTML form submission. React/AJAX forms usually need an application Custom Event. |
| Measure SPA navigation | History Change | Browser history changes without a full page load and no better router event exists. |
| Measure visibility | Element Visibility | A specific element becoming visible is the question. |
| Measure scroll/video engagement | Scroll Depth or YouTube Video | Engagement is the question, not a confirmed business result. |
| Measure a time condition | Timer | A bounded interval and stop condition are documented. |
| Require several independent conditions | Trigger Group | All member conditions must occur; their order is not guaranteed. |

### 3.3 Timing

Use the earliest Trigger at which all required data exists:

```text
Consent Initialization → consent setup
Initialization         → early setup
Page View              → page starts loading
DOM Ready              → HTML is parsed
Window Loaded          → page resources finish
Application event      → confirmed business result
```

Choose the earliest stage at which every required value is available. If a Trigger runs too early, its data may not exist yet. If it runs too late, tracking is delayed and the event may be missed when the user leaves. When the Application already emits the authoritative business event, do not replace it with a later DOM Ready or Window Loaded Trigger.

## 4. Configure the Trigger

### 4.1 Search and reuse

Search the GTM container and tracking inventory before creating a Trigger. Check for the same event name, similar page/click rules, existing Tags, Variables, exceptions, Trigger Groups, sequencing, and consent requirements.

Reuse a Trigger only when its consumers require the same event, scope, timing, filters, consent behavior, missing-data behavior, and expected frequency. A shared Trigger can affect many Tags; review every consumer before changing it.

### 4.2 Create in GTM

1. Open **Triggers → New**.
2. Enter the approved Trigger name.
3. Open **Trigger Configuration** and select the approved type.
4. Choose **All** only when every matching occurrence is in scope.
5. Choose **Some** and add bounded conditions when scope is narrower.
6. Save, attach the Trigger only to approved Tags, and review exceptions/consent.

### 4.3 Filters and operators

A filter has this shape:

```text
Variable + Operator + Expected value
```

Use the smallest condition that answers the requirement:

| Need | Prefer |
| --- | --- |
| One exact value | `equals` |
| Text anywhere in a value | `contains` |
| Known prefix/suffix | `starts with` / `ends with` |
| Controlled pattern | `matches RegEx` |
| Case-insensitive pattern | `matches RegEx (ignore case)` only when the contract allows it |
| Specific element | `matches CSS selector` |
| Numeric comparison | `less than`, `greater than`, and so on |

Inside one Trigger, conditions are AND. On one Tag, separate firing Triggers are OR. Do not add `All Pages` and a page-specific Trigger expecting AND; `All Pages` already makes the Tag eligible on every page.

### 4.4 URL, RegEx, and CSS rules

- Use `Page Path` for route matching, `Page URL` for the full URL, a dedicated URL Variable for query parameters, and `Click URL` for link destinations.
- Anchor RegEx when boundaries matter, for example `^/products(?:/|$)`, and test both intended matches and near-misses.
- Prefer stable IDs or owned attributes for CSS selectors. Avoid generated framework classes, layout classes, and visible text.
- Document case sensitivity and test `undefined`, empty, invalid, and unexpected values.

### 4.5 Missing and invalid values

For a required value, fail closed:

```text
Required Variable missing or invalid
        ↓
Trigger does not match
        ↓
Tag does not fire
        ↓
QA records a contract defect
```

Do not convert missing values to `unknown`, empty string, or a previous value unless the contract explicitly defines that meaning.

### 4.6 Trigger Groups and tag sequencing

These are two different mechanisms:

- **Trigger Group:** waits until every member Trigger has matched at least once, then makes the Tag eligible. It does not guarantee the order or prove that the members belong to the same business transaction.
- **Tag sequencing:** controls the order in which Tags run, such as running a setup Tag before an event Tag. It does not make a Trigger wait for an API response.

Use a Trigger Group only when independent signals genuinely all need to be present. Use tag sequencing only for a documented Tag dependency. Neither mechanism should recreate an Application workflow or pair an API request with its response; for FD, one application Custom Event is the preferred pattern.

## 5. Test and validate

### 5.1 GTM Preview

Use GTM Preview/Tag Assistant to inspect the event timeline, Data Layer values, Variables, Trigger match, Tags Fired, Tags Not Fired, exceptions, and consent state:

1. Connect to the approved QA/staging URL.
2. Perform one controlled action.
3. Select the relevant event in the timeline.
4. Confirm the event name and required values.
5. Inspect every Variable used by the Trigger and Tag.
6. Confirm matched filters and blocking conditions.
7. Repeat for negative, duplicate, and edge cases.

### 5.2 Network and downstream validation

Preview proves the GTM path; it does not prove that GA4 received the intended request. When the Trigger feeds an outbound Tag, inspect the browser Network panel or equivalent hit details and verify:

- request exists and count matches the contract;
- event name, parameter names, and types are correct;
- required values are present and optional values follow the contract;
- the Measurement ID/destination is correct for the environment;
- consent behavior is correct;
- no PII, credentials, tokens, secrets, or unrestricted user input is sent.

Use GA4 DebugView/Realtime as downstream diagnostic evidence. A Tag shown as fired is not enough evidence by itself. Link the result to the Section 08 Evidence Template.

### 5.3 Required test coverage

| Case | Expected Trigger behavior |
| --- | --- |
| Valid event | Matches once at the authoritative moment. |
| Wrong event name/case | Does not match. |
| Similar URL, selector, or action | Does not match an unrelated case. |
| Missing/malformed required value | Does not match or follows documented block behavior. |
| Invalid input or failed server response | No successful-outcome match. |
| Double click/retry/repeated callback | No unintended duplicate. |
| SPA route/reload/back-forward/revisit | Behavior follows the route contract without duplicate page views. |
| Consent denied/granted/updated | Matches or blocks according to approved consent behavior. |
| Exception condition | Intended Tag is blocked; unrelated Tags are unchanged. |
| Trigger Group incomplete/complete | Fires only after all required members occur. |
| Supported browsers/navigation | Tracking does not break submission or navigation. |

## 6. Publish, inventory, and retire

### 6.1 Naming and description

Use:

```text
[SCOPE] - [TYPE] - [BUSINESS EVENT OR PURPOSE] - [QUALIFIER]
```

Recommended prefixes include `CI` (Consent Initialization), `INIT`, `PV`, `DOM`, `WL`, `CE` (Custom Event), `CLK`, `LINK`, `FORM`, `HC` (History Change), `VIS`, `TMR`, `GRP`, and `EXC` (Exception). Never use `Trigger 1`, `New Trigger`, `Test`, or `Temp` for an active item.

The description should record business purpose, event/source, filters, consuming Tags, exceptions, consent, expected frequency, owner, environment, and retirement condition.

### 6.2 Inventory

Maintain one row per Trigger:

```text
Trigger name and type
Exact event or page-load source
All filters, operators, values, regex flags, and selector scope
Consuming Tags and events sent
Exceptions, Trigger Groups, and sequencing
Consent behavior and timing risk
Expected frequency
Owner/reviewer and environment
Workspace/published version
Status and retirement condition
```

### 6.3 Review and publish

Before publishing a shared or environment-sensitive Trigger:

1. Confirm the Measurement Plan and Trigger contract.
2. Review all consumers and duplicate paths.
3. Test affected environments, consent states, negative cases, and request count.
4. Attach Preview/Network/DebugView evidence.
5. Publish a versioned GTM change with an owner, release note, and rollback point.
6. Update the inventory and notify affected owners.

### 6.4 Retirement

Retire only after consuming Tags are removed/replaced, no Trigger Group or sequencing dependency remains, the replacement passes positive/negative/duplicate/consent/downstream tests, and a recoverable version is retained.

## 7. Cross-reference map

- [Section 01 — Data Layer Design](01-data-layer-design-answer.md): application-owned event timing and business truth.
- [Section 02 — Variable Management](02-variable-management-answer.md): Variable source, nested paths, and missing-data behavior.
- [Section 04 — Tag Management](04-tag-management-answer.md): attach Triggers to Google/GA4 Tags and verify destination.
- [Section 05 — Consent Management](05-consent-answer.md): consent defaults, updates, and denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer.md): governance for custom templates.
- [Section 07 — Measurement Plan](07-measurement-plan-answer.md): business definition, expected frequency, and owners.
- [Section 08 — Debug/QA](08-debug-qa-answer.md): Preview, evidence, defects, and retests.
- [Section 09 — Reports and Charts](09-reports-charts-answer.md): downstream interpretation after collection.
- [Section 10 — Release Monitoring](10-release-monitoring-answer.md): release gates, observation, and rollback.

## 8. Worked Journey: FD `calculation_action`

This is the only concrete walkthrough. Replace the project identifiers with approved values.

### 8.1 Contract

```text
Business event: calculation_action
Authoritative moment: matching FD API response has been classified
Expected frequency: one event per accepted calculation occurrence
Required values: event_schema_version, app_name, solution_found, approved inputs
Trigger: FD - CE - calculation_action - Approved
Consumer: FD - GA4 Event - calculation_action
Environment: QA/staging/production according to routing
```

### 8.2 Application message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 8.3 Trigger configuration

```text
Name: FD - CE - calculation_action - Approved
Type: Custom Event
Event name: calculation_action
Conditions:
  app_name equals fd
  event_schema_version equals 1.0
Expected: one match per accepted calculation occurrence
```

### 8.4 Test decision

```text
Valid output response
    → one Data Layer event
    → Trigger matches once
    → one GA4 Tag firing/request

Valid response without output
    → one Data Layer event with solution_found = false
    → same expected count

Invalid input, timeout, server failure, stale response, duplicate callback,
unknown environment, or denied consent
    → behavior follows the contract and Section 08 test record
```

## References

- [Tag Manager Help — About triggers](https://support.google.com/tagmanager/answer/7679316?hl=en)
- [Tag Manager Help — Custom event trigger](https://support.google.com/tagmanager/answer/7679219?hl=en)
- [Tag Manager Help — Best practices for trigger configuration](https://support.google.com/tagmanager/answer/7679102?hl=en)
- [Tag Manager Help — Preview and debug containers](https://support.google.com/tagmanager/answer/6107056?hl=en)
