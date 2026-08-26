# Workflow Guide: Setting Up GTM to GA4 for Website Data Collection

This guide describes a controlled, from-zero workflow for collecting website events with Google Tag Manager (GTM) and sending them to Google Analytics 4 (GA4).

The primary example is a confirmed `sign_up` event. A `sign_in` variant is included where the implementation is the same but the business event is a successful login.

The guide follows the principles in:

- `01-data-layer-design-answer.md`
- `02-variable-management-answer.md`
- `03-trigger-management-answer.md`
- `04-tag-management-answer.md`

The central rule is:

```text
Application confirms the business fact
        ↓
Application pushes one complete Data Layer event
        ↓
GTM reads approved values through Variables
        ↓
Trigger identifies the event
        ↓
Consent and exceptions are evaluated
        ↓
GA4 Event tag sends the approved payload
        ↓
Network request and GA4 receipt are verified
```

GTM should route and configure measurement. It should not reconstruct a confirmed business outcome from a button click, DOM text, raw form fields, or a broad page rule when the application can provide an authoritative event.

---

## 1. Scope and expected outcome

At the end of this workflow, the website should be able to:

1. Push a valid, privacy-safe `sign_up` or `sign_in` event to the Data Layer.
2. Let GTM read the event values through canonical Variables.
3. Fire one GA4 Event tag for each valid business occurrence.
4. Send the event to the correct GA4 environment and web stream.
5. Respect the approved consent design.
6. Prove the result through GTM Preview, the browser Network panel, and GA4 DebugView or another approved downstream check.
7. Publish a documented, versioned, maintainable container change.

This guide assumes:

- the website team can add the GTM installation and Data Layer pushes;
- there is a separate or clearly identified QA/staging environment;
- the team has an approved consent-management design;
- the team can access the GA4 property, web stream, and GTM container;
- production data must not accidentally be sent from unknown or non-production environments.

If the measurement requirement, event contract, consent behavior, destination, or owner is unknown, stop at the design stage and resolve it before configuring GTM.

---

## 2. The three GTM building blocks

Use this mental model throughout the setup:

```text
Variable = What is the value?
Trigger  = When should it run?
Tag      = What should be sent or run?
```

### Data Layer

The Data Layer is the application-to-GTM interface. It carries structured messages such as:

```javascript
window.dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "register",
});
```

The application owns the meaning and timing of this message.

### Variable

A Variable exposes a value for a Trigger or Tag to use. For example:

```text
DLV - Website - method
→ reads method from the current Data Layer event
```

### Trigger

A Trigger decides whether a Tag is eligible to run. A Trigger does not send data by itself.

For a tag with multiple firing triggers, the logic is generally:

```text
(Trigger A matches OR Trigger B matches)
AND no exception matches
AND consent/settings allow it
AND any genuine sequencing requirement is satisfied
```

Multiple conditions inside one Trigger are `AND` conditions. Multiple firing Triggers on one Tag are alternatives, not a sequence.

### Tag

A Tag performs the action, such as sending a named GA4 event. A production Tag must have a clear purpose, approved parameters, an authoritative Trigger, consent behavior, a destination, an owner, and an expected request count.

---

## 3. Start with the measurement contract

Do not begin by creating a Tag in GTM. First define exactly what should be measured.

### 3.1 Confirmed `sign_up` contract

Use `sign_up` only when the account creation has actually succeeded. The recommended business flow is:

```text
User submits registration form
→ application validates the form
→ server creates the account
→ application receives a successful response
→ application pushes one sign_up event
→ GTM sends the GA4 sign_up event
```

The submit click is not the successful signup. It may be useful as a separate intent event, but it must not share the successful `sign_up` Tag.

Example contract:

| Contract field | Approved example |
| --- | --- |
| Business event | Account creation confirmed by the server |
| GA4 event name | `sign_up` |
| Authoritative source | Application Data Layer push after successful account creation |
| Required parameter | `method`, for example `email`, `google`, or `apple` |
| Required parameter | `form_id`, for example `register` |
| Optional parameter | `event_schema_version`, if included in the approved measurement plan |
| Expected frequency | Exactly one event per confirmed account creation |
| Failed validation | No `sign_up` event |
| Server/API failure | No `sign_up` event |
| Missing required value | Fail QA and block the invalid Tag path; do not send a placeholder |
| Optional value missing | Omit it unless the contract defines another behavior |
| Consent | Follow the approved analytics consent matrix |
| Destination | Environment-specific GA4 web stream selected safely |
| Privacy | No email address, password, access token, credential, secret, or unrestricted user input |
| Owner | Named product/analytics owner and GTM maintainer |
| Consumers | Approved GA4 reports, key-event analysis, or downstream processes |

### 3.2 Confirmed `sign_in` variant

For a successful login, use the same workflow with this contract:

| Contract field | Approved example |
| --- | --- |
| Business event | Authentication completed successfully |
| GA4 event name | `sign_in` |
| Authoritative source | Application Data Layer push after successful authentication |
| Required parameter | `method`, for example `email`, `google`, or `apple` |
| Expected frequency | Exactly one event per successful login occurrence, according to the tracking plan |
| Failed authentication | No `sign_in` event |
| Privacy | Do not send email, username, password, token, session ID, or authentication response data |

Do not send both `sign_up` and `sign_in` for the same account-creation occurrence unless the measurement plan explicitly defines both events and their separate meanings.

### 3.3 Data Layer design rules

The event must:

- describe a business fact rather than a UI detail;
- be emitted only after the business outcome is confirmed;
- be emitted once per occurrence;
- use stable, exact event names and parameter names;
- contain its event-specific values in the same push;
- use natural data types and approved enumerated values;
- contain only safe, useful data;
- avoid PII, credentials, secrets, tokens, unrestricted text, and full application state.

Good:

```javascript
window.dataLayer.push({
  event: "sign_up",
  event_schema_version: "1.0",
  method: "email",
  form_id: "register",
});
```

Avoid splitting one event across several pushes:

```javascript
window.dataLayer.push({ method: "email" });
window.dataLayer.push({ event: "sign_up" });
```

GTM processes Data Layer messages in first-in, first-out order, and values can remain available after an earlier push. Splitting the event can make a later event read stale context.

Avoid sending sensitive or unnecessary values:

```javascript
window.dataLayer.push({
  event: "sign_up",
  email: "user@example.com",       // PII: do not send
  password: "...",                 // secret: do not send
  access_token: "...",             // credential: do not send
  ...formData,                      // uncontrolled and unsafe
});
```

### 3.4 Application implementation examples

The application should push only after the authoritative success response.

```javascript
async function submitRegistration(form) {
  const response = await createAccount(form);

  if (!response.success) {
    return;
  }

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: "sign_up",
    event_schema_version: "1.0",
    method: form.authMethod,     // approved enum, not an email address
    form_id: "register",
  });
}
```

```javascript
async function submitLogin(credentials) {
  const response = await authenticate(credentials);

  if (!response.success) {
    return;
  }

  window.dataLayer = window.dataLayer || [];
  window.dataLayer.push({
    event: "sign_in",
    event_schema_version: "1.0",
    method: "email",
  });
}
```

The application should also prevent duplicate pushes from repeated callbacks, retries, component remounts, or double handling of the same successful response. If two independent successful account creations are valid occurrences, they should produce two events; deduplication must not hide legitimate business events.

---

## 4. Create and configure GA4 from zero

Complete the GA4 destination before building the GTM event Tag.

### Step 1 — Create or select the Analytics account

1. Sign in with the organization-owned Google account.
2. Create a new Analytics account if one does not exist, or select the approved account.
3. Confirm the account owner, administrators, property owner, and access model.
4. Do not create a duplicate property if an approved property already exists.

Record:

```text
GA4 account:       [account name / ID]
Property:          [property name / ID]
Owner:             [owner]
Environment scope: [QA/staging/production]
```

### Step 2 — Create or select the GA4 property

1. Create the approved GA4 property for the website, or reuse the existing property when its ownership, environment, and data-governance rules match the requirement.
2. Set the reporting time zone and currency deliberately because they affect reports.
3. Confirm whether QA/staging data belongs in a separate test property or a controlled test stream.
4. Keep production and test destinations clearly separated.

Do not use a production property as an unplanned development destination. If the organization uses one property with separate streams or filtering, document that design before implementation.

### Step 3 — Create the Web data stream

1. Add a Web data stream for the approved website origin.
2. Record the stream name, URL, stream ID, and Measurement ID.
3. Decide which automatically collected or Enhanced Measurement events are approved. Do not enable features merely because they are available.
4. Check whether automatic events could overlap with the custom GTM event path.

Record:

```text
GA4 property:     [property]
Web stream:       [stream name / stream ID]
Measurement ID:   G-XXXXXXXXXX
Environment:      [QA/staging/production]
```

The Measurement ID is a destination value. It is not a substitute for the event name, Trigger, consent rule, or event contract.

### Step 4 — Configure GA4 reporting definitions after data is validated

GA4 can receive a named event and its parameters without a separate event-definition step. After the event is observed and the contract is approved:

- confirm `sign_up` and/or `sign_in` appears in the Events view;
- mark the event as a key event only if the business has approved that classification;
- create Custom Definitions for custom parameters such as `form_id` only when those parameters are needed in GA4 reports or Explorations;
- use the exact parameter spelling and scope from the contract;
- do not create definitions for PII or sensitive values.

The collection setup and the reporting setup are related but separate:

```text
GTM sends the event and parameters
        ↓
GA4 receives the event
        ↓
Approved parameters are registered for reporting when needed
```

---

## 5. Create and install the GTM container

### Step 1 — Create or select the GTM account

1. Sign in with the organization-owned account.
2. Create or select the approved GTM account.
3. Create a Web container for the website if no suitable container exists.
4. Confirm container permissions, owners, reviewers, and publishing rights.
5. Use a separate container or documented environment routing when the environment design requires it.

Record:

```text
GTM account:       [account]
Container:         [container name / ID]
Workspace:         [workspace]
Owner:             [owner]
Environment model: [separate containers or controlled routing]
```

### Step 2 — Install GTM on the website

Use the installation snippets generated by GTM for the correct Web container. Place the script in the required document location and the fallback element immediately after the opening body element, according to the generated instructions.

Before installing:

- verify the Container ID belongs to the intended environment;
- make sure the application initializes `window.dataLayer` before the GTM snippet when the site sends early events;
- coordinate installation with the web team and consent-management owner;
- do not paste a production Container ID into a QA site or vice versa.

After installing:

1. Load the site in the intended environment.
2. Use the browser source or Network panel to confirm the container loads.
3. Connect GTM Preview before creating the event Tag if possible.
4. Verify that the container is not installed twice.

Duplicate container installation can create duplicate tags and requests even when the individual GTM configuration is correct.

### Step 3 — Initialize the Data Layer safely

The website should initialize the Data Layer before the GTM container can process application events:

```html
<script>
  window.dataLayer = window.dataLayer || [];
</script>
```

The exact installation depends on the site architecture. The important contract is that the event is pushed to the same `dataLayer` instance observed by GTM.

---

## 6. Plan the GTM assets before creating them

Search first. A mature GTM container may already contain a Tag, Trigger, Variable, exception, consent setting, legacy dispatcher, or downstream consumer for the same event.

Search for:

- `sign_up`, `signup`, `account_created`, and similar event names;
- `sign_in`, `signin`, `login`, and similar event names;
- Tags that already send the same GA4 event;
- Custom Event Triggers and their filters;
- Data Layer Variables for the required parameters;
- Google tags and Measurement ID variables;
- consent initialization and analytics consent settings;
- exceptions, Trigger Groups, and sequencing dependencies;
- duplicated or legacy GTM containers on the website;
- GA4 Enhanced Measurement events that could overlap.

Reuse an existing asset only when its meaning and behavior match the new contract:

```text
Event
Scope
Timing
Filters
Consent behavior
Missing-data behavior
Duplicate behavior
Destination
```

If a shared asset has multiple consumers, review every consumer before changing it. A small Trigger or Variable change can alter unrelated Tags.

### Recommended folder structure

Use the organization’s existing structure when one exists. A simple structure for a new container is:

```text
00 - Governance
01 - Shared Configuration
02 - Website Data Layer
03 - Website Triggers
04 - Website GA4 Tags
05 - Exceptions and Consent
99 - Deprecated
```

Do not leave live assets named `New Tag`, `Variable 3`, `Test`, `Temp`, or `Copy`.

---

## 7. Create the GTM Variables

A Variable should have one clear responsibility and one canonical name. Prefer application-owned Data Layer values over DOM scraping.

### 7.1 Enable only useful built-in Variables

In GTM, enable built-in Variables needed for the workflow, such as:

- Page Hostname, for environment routing;
- Page Path or Page URL, when a route boundary is part of the contract;
- Event, for event inspection and filters;
- other click or URL Variables only when the measurement requirement explicitly needs them.

Do not enable or use DOM and click Variables as a substitute for an application event that already exists.

### 7.2 Create Data Layer Variables for the event

For the `sign_up` example, create or reuse:

| GTM Variable name | Type | Data Layer key | Required behavior |
| --- | --- | --- | --- |
| `DLV - Website - method` | Data Layer Variable | `method` | Required approved enum; missing/invalid value fails QA and must not create a valid conversion |
| `DLV - Website - form_id` | Data Layer Variable | `form_id` | Required for `sign_up`; exact approved value or approved set |
| `DLV - Website - event_schema_version` | Data Layer Variable | `event_schema_version` | Required if included in the measurement contract |
| `DLV - Website - app_name` | Data Layer Variable | `app_name` | Required only when the shared container needs application scope |

For `sign_in`, the same `DLV - Website - method` may be reused if the allowed values and missing-data rules are identical.

For nested objects, use Data Layer Variable Version 2 and the exact nested path. Example:

```text
Data Layer push:
inputs: {
  method: "email"
}

Variable:
DLV - Website - inputs - method
Data Layer Variable Name: inputs.method
Version: 2
```

Do not use a nested path when the event contract defines a flat field, and do not create tag-specific aliases for the same canonical value.

### 7.3 Create the environment destination Variable

Use a reviewed Constant or Lookup Table for Measurement ID management.

Example:

```text
LUT - Shared - GA4 Measurement ID by Hostname
```

| Page Hostname | Destination |
| --- | --- |
| `qa.example.com` | QA Measurement ID |
| `staging.example.com` | Staging Measurement ID |
| `www.example.com` | Production Measurement ID |
| unknown hostname | No valid destination / tag blocked |

Required controls:

- map known environments explicitly;
- do not hard-code the production Measurement ID in every event Tag;
- do not make an unknown hostname fall back to production;
- test a missing mapping and an unknown hostname;
- record the mapping in the inventory;
- keep destination configuration separate from business event logic.

If one environment requires a separate container or workspace, document that choice and keep the event contract consistent.

### 7.4 Define missing-data behavior

Every Variable used by a Tag or Trigger needs a documented missing-value rule:

```text
Required value missing or invalid
        ↓
Trigger does not match or Tag is blocked
        ↓
No misleading business event is sent
        ↓
QA identifies the upstream contract issue
```

Do not silently convert a missing required value to `unknown`, an empty string, a random default, or a value retained from an earlier Data Layer message.

For optional values, choose one approved behavior:

```text
Optional value unavailable
        ↓
Omit the parameter
```

or, only if the contract explicitly requires it:

```text
Optional value unavailable
        ↓
Use the documented approved default
```

### 7.5 Variable review checklist

Before using a Variable in a Tag, check:

- the name explains its scope, type, and purpose;
- the Data Layer key spelling and casing are exact;
- the value exists on the same event as the `event` key;
- the value type is correct;
- the Variable does not read PII, secrets, tokens, or unrestricted input;
- the Variable is reused where the contract is identical;
- any Lookup Table has explicit known-environment mappings;
- the unknown/default behavior is safe;
- all dependent Tags and Triggers are inventoried.

---

## 8. Create the GTM Triggers

### 8.1 Consent Initialization and initialization setup

If the website uses a Consent Management Platform (CMP), configure the approved consent defaults and updates through the organization’s consent design.

- Use Consent Initialization for consent configuration that must be established before other Tags.
- Use Initialization only for genuine early setup that must happen before normal page triggers.
- Do not use either as the default Trigger for a business event.
- Do not bypass consent with a custom `consent = true` Trigger.

Document the behavior for consent granted, denied, unavailable, and changed after page load.

For the shared Google tag, create the initialization Trigger only when the approved setup requires early configuration:

```text
Name:      INIT - Website - Google tag - Supported Environment
Type:      Initialization
Condition: LUT - Shared - GA4 Measurement ID by Hostname does not equal blank
```

The exact condition syntax depends on the GTM Variable configuration. The required behavior is more important than the label:

```text
Known supported hostname with valid Measurement ID
        → Google tag may run

Unknown hostname, missing mapping, or invalid destination
        → Google tag does not run
        → no production fallback
```

If the environment-routing Variable cannot be safely used in the Trigger condition, use a documented exception or an equivalent reviewed block. Do not rely on an undefined value being handled safely without testing it.

### 8.2 Create the confirmed signup Trigger

Create:

```text
Name:       CE - Website - sign_up - Confirmed
Type:       Custom Event
Event name: sign_up
```

Use the exact event name and casing from the Data Layer contract. The application should push the event only after successful account creation.

If the contract is shared across multiple applications, add only the approved scope condition, such as:

```text
app_name equals website
```

If required values must be checked in GTM to fail closed, add the minimum approved filters, for example:

```text
DLV - Website - method matches RegEx ^(?:email|google|apple)$
DLV - Website - form_id matches RegEx ^register$
```

Use `All Custom Events` for the exact event when the application is the sole approved producer and the Data Layer contract is validated upstream. Do not add click, DOM, or broad page-path conditions merely because they are available.

### 8.3 Create the confirmed sign-in Trigger variant

Create:

```text
Name:       CE - Website - sign_in - Confirmed
Type:       Custom Event
Event name: sign_in
```

Use it only when the application has confirmed successful authentication. A failed login, a click on the login button, or a page view is not a confirmed `sign_in` event.

### 8.4 Trigger filter rules

Use the smallest reliable filter:

| Requirement | Preferred approach |
| --- | --- |
| Exact event name | Custom Event with exact name and casing |
| Exact value | `equals` |
| Approved value set | Bounded `matches RegEx` or approved lookup, with documented values |
| Route only | Page Path, when route is genuinely part of the contract |
| Full URL | Page URL only when host, protocol, path, and query are relevant |
| Link destination | Click URL for link-specific tracking |
| DOM state | DOM Ready or Element Visibility only when no authoritative application event exists |

Anchor regular expressions when boundaries matter. For example:

```text
^(?:email|google|apple)$
^/products(?:/|$)
```

Do not use an unanchored pattern that can match unintended values. Use case-insensitive matching only if the application contract allows case differences.

### 8.5 Exceptions and Trigger Groups

Use an exception only for a documented blocking condition, such as approved internal traffic handling or an unsupported environment. One matching exception can block a Tag.

Use a Trigger Group only when several independent conditions must all have occurred and that semantics are truly required. A Trigger Group confirms occurrence, but it does not guarantee event order. It must not be used to invent a business workflow that belongs in the application.

### 8.6 Trigger review checklist

- The Trigger represents the authoritative business moment.
- The event name is exact and stable.
- All required values are available when the event is evaluated.
- Conditions are necessary, bounded, and understood.
- Missing/invalid required values fail safely.
- No click or page-view path can duplicate the business event.
- Exceptions are documented and tested.
- Multiple firing Triggers are understood as OR logic.
- The Trigger’s consumers and expected request counts are inventoried.

---

## 9. Create the GTM Tags

### 9.1 Create the shared Google tag

Create or reuse one approved shared Google tag for the website environment model.

```text
Name:           Google tag - Website - Primary
Type:           Google tag
Measurement ID: {{LUT - Shared - GA4 Measurement ID by Hostname}}
Trigger:        INIT - Website - Google tag - Supported Environment
```

The exact Trigger timing depends on the approved setup:

- use Initialization when the shared configuration must run before normal page triggers;
- use an approved page-load Trigger when earlier initialization is not required;
- do not use early setup as a reason to fire business-event Tags before their data exists.

The shared configuration must:

- use the correct GA4 Measurement ID for the current environment;
- be blocked or have no valid destination for unsupported environments;
- follow the approved consent settings;
- not be duplicated in separate Tags only to handle environment differences;
- be recorded in the inventory with its owner and destination map.

### 9.2 Create the GA4 Event Tag for `sign_up`

Create a built-in GA4 Event Tag:

```text
Name:        GA4 Event - Website - sign_up
Type:        GA4 Event
Google tag:  Google tag - Website - Primary
Event name:  sign_up
Trigger:     CE - Website - sign_up - Confirmed
```

Map only the approved parameters:

| GA4 parameter | GTM value | Behavior |
| --- | --- | --- |
| `method` | `{{DLV - Website - method}}` | Required approved enum |
| `form_id` | `{{DLV - Website - form_id}}` | Required for `sign_up` if in the contract |
| `event_schema_version` | `{{DLV - Website - event_schema_version}}` | Include only if approved |
| `app_name` | `{{DLV - Website - app_name}}` | Include only if approved and needed for scope/reporting |

Do not add raw email, username, password, access token, API response, free-text form input, or convenient application state.

Use the built-in GA4 Event Tag instead of Custom HTML or a custom `gtag()` call when the supported Tag type provides the required behavior.

### 9.3 Create the GA4 Event Tag for `sign_in`

For the variant:

```text
Name:        GA4 Event - Website - sign_in
Type:        GA4 Event
Google tag:  Google tag - Website - Primary
Event name:  sign_in
Trigger:     CE - Website - sign_in - Confirmed
```

Map `method` and any other approved parameters. Do not copy the `sign_up` Tag and change the event name without reviewing its complete contract, owner, consent behavior, destination, and expected frequency.

### 9.4 Configure consent behavior

Apply the approved consent requirements in the Tag’s consent settings and the CMP integration.

Test the Tag when consent is:

- granted before the event;
- denied before the event;
- unavailable or not initialized;
- updated after page load;
- changed before a repeated event.

The Tag must not be forced to fire by a custom Trigger when consent is denied. If the approved design uses Consent Mode or another privacy-preserving behavior, document that behavior and verify the actual request rather than inferring it from the GTM status alone.

### 9.5 Add a complete Tag description

Example:

```text
Purpose: Measure one server-confirmed account creation.
Ticket/contract: [tracking plan or ticket reference].
Event: sign_up.
Parameters: method and form_id required; optional fields follow the contract.
Destination: Website Google tag selected by environment-safe routing.
Consent: Approved analytics consent matrix.
Expected count: One request per confirmed account creation.
Owner: [analytics/product owner]; maintainer: [GTM owner].
Dependencies: Data Layer contract, shared Google tag, consent configuration.
Retirement: When the approved replacement is live and all consumers migrate.
```

If the purpose, parameters, destination, consent, expected count, owner, or retirement rule cannot be described, the Tag is not ready for production.

---

## 10. End-to-end build sequence

Use this sequence for a new setup:

```text
Define contract
      ↓
Create/select GA4 property and Web stream
      ↓
Create/select GTM Web container
      ↓
Install GTM and initialize Data Layer
      ↓
Implement confirmed application event
      ↓
Search existing GTM assets and consumers
      ↓
Create/reuse Variables
      ↓
Create consent and initialization setup
      ↓
Create Custom Event Trigger
      ↓
Create/reuse shared Google tag
      ↓
Create GA4 Event Tag
      ↓
Preview positive and negative cases
      ↓
Validate Network request and destination
      ↓
Validate GA4 DebugView/reporting setup
      ↓
Review, version, publish, smoke-test, monitor
```

### Build record

Complete this before calling the implementation ready:

```text
Business event:              sign_up / sign_in
Authoritative application moment:
Data Layer event name:       [exact name and casing]
Required parameters:         [names, types, allowed values]
Optional parameters:         [names and missing behavior]
Expected request count:      [for example, 1 per confirmed occurrence]
GA4 property:                [property]
GA4 Web stream:              [stream]
QA Measurement ID:           [ID]
Production Measurement ID:   [ID]
GTM container:               [container]
Consent policy:              [reference and behavior]
Owner/approver:              [names]
Implementation ticket:       [ticket]
Legacy/overlapping paths:    [none or documented paths]
```

---

## 11. Test and validate the implementation

Testing must follow the complete collection path:

```text
Application success/failure
        ↓
Data Layer message
        ↓
Variables
        ↓
Trigger
        ↓
Exceptions and consent
        ↓
Tag
        ↓
Network request
        ↓
Correct GA4 destination
        ↓
GA4 DebugView or approved downstream evidence
```

“Tag Fired” in GTM Preview is one diagnostic signal. It does not prove that the correct request was sent to the correct destination with the correct payload.

### 11.1 Connect GTM Preview

1. Open the intended QA or staging website.
2. Start GTM Preview/Tag Assistant for the correct container.
3. Confirm the preview session is connected to the intended environment.
4. Clear or record the initial event timeline.
5. Perform one controlled test action.
6. Select the `sign_up` or `sign_in` Custom Event in the timeline.

### 11.2 Positive test: confirmed signup

Perform a successful test registration using a safe QA account or approved test data.

Expected result:

```text
Application receives account-created success
        ↓
One Data Layer push with event = sign_up
        ↓
method and form_id are present and valid
        ↓
CE - Website - sign_up - Confirmed matches
        ↓
GA4 Event - Website - sign_up is eligible
        ↓
Consent allows the approved behavior
        ↓
One request reaches the QA GA4 destination
```

Inspect:

- the exact Data Layer event name and casing;
- all event-specific values on the same push;
- each Variable’s resolved value;
- Trigger conditions and exceptions;
- Tags Fired and Tags Not Fired;
- consent state;
- the request count.

### 11.3 Negative and edge-case tests

At minimum, test the following:

| Test | Setup | Expected result |
| --- | --- | --- |
| Valid success | Server confirms account creation | One valid `sign_up` request when consent permits |
| Failed validation | Form validation fails | No `sign_up` event and no success request |
| Server/API failure | Account creation fails | No `sign_up` event |
| Click without success | Click Submit but do not complete account creation | No `sign_up` event |
| Wrong event name | Push `signup` or another name | `sign_up` Trigger does not match |
| Wrong case | Push `Sign_Up` | Exact Trigger does not match |
| Missing required value | Omit `method` or `form_id` | Invalid event is blocked or fails QA; no placeholder is sent |
| Invalid enum | Push an unapproved `method` | Trigger/Tag does not produce a valid production request |
| Optional value missing | Omit an approved optional value | Parameter is omitted according to the contract |
| Double click | Submit twice during one successful occurrence | No unintended duplicate request |
| Retry/callback | Repeat the same success callback | Duplicate behavior is blocked or investigated according to the contract |
| Two real signups | Complete two separate account creations | Two requests, one per legitimate occurrence |
| Reload/navigation | Reload or navigate without a new success | No new `sign_up` request |
| SPA remount | Remount the form/component | No event without a new confirmed outcome |
| Consent granted | Consent is granted before success | Approved request behavior occurs |
| Consent denied | Consent is denied before success | Tag is blocked or uses the approved privacy-preserving behavior |
| Consent update | Change consent before a later success | Behavior follows the consent matrix |
| QA/staging host | Run on a known non-production host | Non-production Measurement ID is used |
| Production host | Run the approved smoke test | Production Measurement ID is used only on the known production host |
| Unknown host | Run on an unsupported host | No production fallback; Tag is blocked or has no valid destination |
| Legacy path | Trigger a legacy dispatcher event | No duplicate preferred `sign_up` request unless migration explicitly requires it |
| Privacy review | Inspect Data Layer and request payload | No PII, secrets, tokens, or unrestricted input |

For `sign_in`, substitute successful authentication and verify that failed authentication never produces the success event.

### 11.4 Validate the Network request

For every allowed positive case, inspect the browser Network panel or Tag Assistant Hit Details. Confirm:

- a request was actually sent;
- it is sent to the expected Google collection endpoint;
- the request contains the correct GA4 Measurement ID/property/stream;
- the event name is exactly `sign_up` or `sign_in`;
- required parameters are present;
- parameter names have the approved spelling and casing;
- parameter types and values follow the contract;
- optional parameters follow their missing-value behavior;
- consent behavior is represented as designed;
- no PII, passwords, tokens, secrets, or unrestricted user input appears;
- the number of requests matches the business occurrence count;
- a QA request never uses the production destination;
- a production smoke test never uses the QA/staging destination.

Minimum evidence:

```text
Correct event name
Correct parameter payload
Correct consent behavior
Correct Measurement ID/destination
Correct request count
No prohibited data
```

### 11.5 Validate in GA4 DebugView

Use GA4 DebugView or the organization’s approved downstream evidence as an additional confirmation:

1. Open the test property or stream.
2. Ensure the test request is eligible for debug validation according to the organization’s setup.
3. Locate the event by its exact name.
4. Inspect the event parameters and values.
5. Confirm that the event is arriving in the expected property and stream.
6. Compare the observed count with the browser request count.

DebugView is useful downstream evidence, but it should not replace Network validation. The network request is the primary transport-level evidence that GTM sent the intended payload to the intended destination.

### 11.6 Test record

Complete one record for each implementation:

```text
Environment:            [QA/staging/production URL]
GTM container:          [container ID]
Workspace/version:      [workspace / published version]
GA4 property/stream:    [test property and web stream]
Browser/device:         [browser and version]
Consent state:          [state before and during test]
Tester/date:            [name / YYYY-MM-DD]
Evidence:               [Preview, Network, DebugView, sanitized captures]
```

| Test ID | Setup/action | Expected result | Actual result | Evidence | Status |
| --- | --- | --- | --- | --- | --- |
| T01 | Confirmed signup | One valid `sign_up` request | [record] | [link] | Pending |
| T02 | Failed validation/API response | No success event/request | [record] | [link] | Pending |
| T03 | Missing/invalid required value | No misleading valid request | [record] | [link] | Pending |
| T04 | Click without confirmed success | No `sign_up` request | [record] | [link] | Pending |
| T05 | Duplicate callback/double submit | Count follows contract | [record] | [link] | Pending |
| T06 | Consent denied/granted/updated | Approved consent behavior | [record] | [link] | Pending |
| T07 | QA/staging/production routing | Correct destination per environment | [record] | [link] | Pending |
| T08 | Unknown environment | No production fallback | [record] | [link] | Pending |
| T09 | Network payload | Correct event, values, type, and count | [record] | [link] | Pending |
| T10 | GA4 DebugView/downstream | Event arrives in expected destination | [record] | [link] | Pending |

Do not mark a test `Pass` without evidence. Do not claim that a source-level configuration is validated when GTM Preview or network evidence has not been collected.

---

## 12. Review, publish, and monitor

### 12.1 Pre-publish review

Before publishing, confirm:

1. The measurement contract is complete and approved.
2. The application pushes the event only at the authoritative business moment.
3. The Data Layer event is self-contained and privacy-safe.
4. Variables use exact keys, types, paths, and missing-data rules.
5. The Trigger is exact, bounded, and not duplicated by another path.
6. The shared Google tag and Measurement ID routing are environment-safe.
7. The GA4 Event Tag uses a supported built-in Tag type.
8. Only approved parameters are mapped.
9. Consent behavior is documented and tested.
10. Positive, negative, duplicate, navigation, consent, and environment tests are complete.
11. Network and downstream evidence confirm the payload and destination.
12. All consumers, owners, dependencies, and legacy paths are inventoried.

If a required value, destination mapping, consent result, expected count, or network result is unresolved, do not publish.

### 12.2 Publish a focused GTM version

Publish through the normal review process:

- use a focused workspace change;
- name the container version clearly;
- include the ticket, purpose, event, destination, consent behavior, and expected count in the release note;
- record reviewer and owner;
- retain Preview, Network, and downstream evidence;
- retain a recoverable version for rollback;
- update the GTM inventory after publishing.

Example release note:

```text
Version: v[XXX]
Change: Add confirmed Website sign_up GA4 event path.
Data Layer event: sign_up.
Expected behavior: One request per server-confirmed account creation.
Environments tested: QA, staging, production smoke test.
Consent: Approved analytics consent matrix.
Destination: Environment-safe GA4 Measurement ID routing.
Evidence: [Preview / Network / DebugView links].
Owner/reviewer: [names].
Rollback: Revert to version [XXX].
```

### 12.3 Production smoke test

After publishing:

1. Perform one controlled production signup or sign-in test using approved test data and a safe workflow.
2. Confirm the event is pushed once.
3. Confirm the request uses the production Measurement ID only on the known production host.
4. Confirm the payload contains the approved event and parameters.
5. Confirm no staging/QA destination receives the production event.
6. Confirm the expected downstream evidence where available.
7. Record the time, tester, environment, request count, and evidence.

### 12.4 Monitor after release

Monitor for:

- unexpected event spikes or drops;
- duplicate events;
- missing or malformed parameters;
- invalid enum values;
- wrong destination or environment leakage;
- consent behavior changes;
- differences between application success counts and GA4 event counts;
- unexpected legacy and preferred paths firing together.

An event can be technically collected and still be wrong. Monitoring should compare collection against the business occurrence and the documented expected frequency.

---

## 13. Inventory and lifecycle management

Record the following for each live Variable, Trigger, Tag, and shared configuration:

```text
Name and ID:
Type/template:
Business purpose:
Data Layer event/source:
Conditions and filters:
Variables/parameters:
Exceptions:
Consent behavior:
Destination/environment:
Expected frequency:
Consuming Tags or reports:
Owner/maintainer/reviewer:
Ticket and published version:
Status:
Dependencies:
Replacement:
Retirement condition:
Last review date:
Evidence links:
```

Recommended lifecycle:

```text
Proposed → Development → QA → Active → Deprecated → Retired
```

Before changing a shared asset:

1. Record the current published version or retain a recoverable export.
2. Review every reference and consumer.
3. Document the old behavior, new behavior, affected consumers, and expected count.
4. Test the changed path and all existing consumer paths.
5. Review Preview, Network, consent, and downstream evidence.
6. Publish with a rollback point and release note.
7. Update the inventory and notify affected owners.

Retire an asset only when no active Tag, Trigger Group, sequencing dependency, report, audience, export, or approved business requirement still depends on it. Deprecation should be documented before deletion.

---

## 14. Common mistakes to avoid

| Mistake | Why it is unsafe | Correct approach |
| --- | --- | --- |
| Fire `sign_up` on the Submit click | Click means intent, not account creation | Push after server-confirmed success |
| Split event fields across Data Layer pushes | Later events can read stale values | Push the event and all event-specific values together |
| Read email/password from the DOM | Fragile and privacy risk | Send only approved non-sensitive parameters |
| Use a broad `All Pages` Trigger for a business event | Fires outside the business moment | Use the exact Custom Event |
| Add `All Pages` and a page filter expecting AND logic | Multiple firing Triggers are OR logic | Put all required conditions inside the correct Trigger |
| Use `unknown` for a missing required value | Hides upstream data failures | Block/fail QA and fix the contract |
| Create separate copies for QA and production without governance | Configuration drift and duplicate maintenance | Centralize reviewed environment routing or document separation |
| Let an unknown hostname use production | Environment leakage | Block or return no valid destination |
| Use Custom HTML for a supported GA4 event | More code and harder review | Use the built-in GA4 Event Tag |
| Rely only on “Tag Fired” | Does not prove payload or destination | Inspect Network and downstream receipt |
| Use a Trigger Group to build application workflow | GTM is not the business process owner | Push one authoritative application event |
| Add a generic legacy path and a new event path together | One occurrence can create duplicate requests | Inventory, migrate, verify, and retire deliberately |
| Delete old assets immediately | Dependencies can be missed | Deprecate, monitor, retain recovery, then retire |

---

## 15. Final go-live checklist

### GA4

- [ ] Approved GA4 property exists.
- [ ] Correct Web data stream exists.
- [ ] Measurement ID is recorded for every environment.
- [ ] QA/staging and production destinations are separated or safely routed.
- [ ] Enhanced Measurement choices and overlap risks are documented.
- [ ] `sign_up`/`sign_in` is approved as a key event if required.
- [ ] Custom Definitions are created only for approved reporting parameters.

### Website and Data Layer

- [ ] GTM is installed once on the intended website.
- [ ] `window.dataLayer` is initialized before early GTM processing.
- [ ] The application pushes the event only after the confirmed business outcome.
- [ ] The event name and parameter names use exact approved casing.
- [ ] Event-specific values are in the same Data Layer push.
- [ ] Required values have correct types and allowed values.
- [ ] Failed actions do not push success events.
- [ ] Duplicate callbacks and retries are handled according to the event contract.
- [ ] No PII, credentials, secrets, tokens, or unrestricted input is present.

### GTM Variables

- [ ] Canonical Data Layer Variables exist and are reused.
- [ ] Nested paths use Data Layer Variable Version 2 where needed.
- [ ] Measurement ID routing is explicit and fail-safe.
- [ ] Unknown environments cannot use production.
- [ ] Required and optional missing-value behavior is documented.
- [ ] Variable names, folders, owners, and dependencies are recorded.

### GTM Triggers

- [ ] Consent Initialization is configured when required.
- [ ] Initialization is used only for genuine early setup.
- [ ] The business-event Trigger is an exact authoritative Custom Event.
- [ ] Click/page/DOM Triggers do not duplicate the confirmed event.
- [ ] Filters and regex boundaries are documented and tested.
- [ ] Exceptions are necessary, documented, and tested.
- [ ] Expected request frequency is recorded.

### GTM Tags

- [ ] One approved shared Google tag/configuration is used where appropriate.
- [ ] The GA4 Event Tag uses the built-in supported type.
- [ ] Only approved parameters are mapped.
- [ ] Consent behavior is configured according to the approved matrix.
- [ ] Tag description includes purpose, contract, destination, owner, count, and retirement.
- [ ] No generic and specific paths overlap for the same occurrence.
- [ ] Lifecycle status and inventory record are complete.

### Testing and release

- [ ] GTM Preview positive test passed.
- [ ] Failed action test passed.
- [ ] Missing/invalid value test passed.
- [ ] Duplicate/retry test passed.
- [ ] Navigation/SPA test passed where relevant.
- [ ] Consent granted/denied/updated tests passed.
- [ ] QA/staging routing test passed.
- [ ] Production routing and controlled smoke test passed.
- [ ] Unknown-environment test cannot fall back to production.
- [ ] Browser Network request was inspected.
- [ ] Event name, parameters, types, consent, destination, and count were verified.
- [ ] GA4 DebugView or approved downstream evidence was checked.
- [ ] Version, ticket, evidence, reviewer, owner, release note, and rollback point were recorded.
- [ ] Post-release monitoring is defined.

---

## Summary

The complete zero-to-production workflow is:

```text
Define the business event
        ↓
Write the Data Layer contract
        ↓
Create the GA4 property and Web stream
        ↓
Create and install the GTM Web container
        ↓
Implement one confirmed, self-contained Data Layer event
        ↓
Create canonical Variables
        ↓
Create consent and initialization setup
        ↓
Create an exact Custom Event Trigger
        ↓
Create the shared Google tag
        ↓
Create the built-in GA4 Event Tag
        ↓
Test positive, negative, duplicate, consent, privacy, and environment cases
        ↓
Validate the actual Network request and GA4 destination
        ↓
Publish with evidence and a rollback point
        ↓
Smoke-test production and monitor
```

For a confirmed signup, the essential implementation is:

```text
Application confirms account creation
        ↓
dataLayer.push({
  event: "sign_up",
  method: "email",
  form_id: "register"
})
        ↓
CE - Website - sign_up - Confirmed
        ↓
GA4 Event - Website - sign_up
        ↓
Correct environment-safe GA4 destination
        ↓
One validated request
```

For a confirmed login, replace the business event with `sign_in` and keep the same controls. The quality standard is not merely that a Tag fires; it is that the correct business fact is collected once, with approved values, under the correct consent state, in the correct GA4 environment, with evidence.
