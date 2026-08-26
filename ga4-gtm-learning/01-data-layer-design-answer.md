# 01 — Data Layer Design

## What is a Data Layer, GTM and GA?

```text
Website/App
↓
Data Layer
↓
GTM - processes Data Layer messages in first-in, first-out order
↓
GA4 / Ads / other tools
```

A **Data Layer** is a JavaScript object used to store structured data that Google Tag Manager can read and send to GA4.

**GTM (Google Tag Manager)** is a tool for managing and deploying tracking tags on a website or app without needing to change application code every time.

**GA (Google Analytics)** is a platform for collecting, analyzing, and reporting user behavior on websites and apps.

## Data Layer principles

### 1 - Describe business facts

Event should describe what happened from a business perspective, rather than the UI action the user performed

**bad**

```js
dataLayer.push({
  event: "green_button_click",
});
```

**good**

```js
dataLayer.push({
  event: "sign_up",
});
```

The UI may change from a green button to a blue button, or the button text may change from "Create Account" to "Register", but the business outcome remains the same: the user signed up.

This keeps analytics independent of UI implementation.

### 2 - Emit reliable events

Push an event only when the business outcome has actually been confirmed, and emit it once per occurrence.

**bad**

```js
const handleSubmit = () => {
  dataLayer.push({
    event: "sign_up",
  });

  createAccount();
};
```

**good**

```js
const handleSubmit = async () => {
  const response = await createAccount();

  if (response.success) {
    dataLayer.push({
      event: "sign_up",
    });
  }
};
```

### 3 - Use a clear, stable contract

The team should agree on the exact structure of each event

```js
dataLayer.push({
  event: "calculation_completed",
  country: "us",
  connection_type: "ledger",
  result_count: 12,
});
```

```text
event
  type: string
  value: "calculation_completed"

country
  type: string
  allowed values: "us" | "ca" | "uk" | ...

connection_type
  type: string
  source: calculation input

result_count
  type: number
  source: API response
```

### 4 - Keep each event self-contained

Each event should contain all the information required to understand that event.

**avoid**

```js
dataLayer.push({
  country: "us",
});

dataLayer.push({
  connection_type: "ledger",
});

dataLayer.push({
  event: "calculation_completed",
});
```

**prefer**

```js
dataLayer.push({
  event: "calculation_completed",
  country: "us",
  connection_type: "ledger",
  result_count: 12,
});
```

```text
What happened?
→ calculation_completed

Where?
→ us

Which connection type?
→ ledger

How many results?
→ 12
```

### 5 - Collect only safe, useful data

Every field should answer an approved business question.

For example, if the business wants to know: Which connection types are calculated most frequently?

Then this is useful

```js
{
  event: 'calculation_completed',
  connection_type: 'ledger'
}
```

Avoid sending the entire application state just because it is available. That object may contain unnecessary or sensitive information.

```js
dataLayer.push({
  event: "calculation_completed",
  ...formData,
});
```

.
Never send values such as:

```js
{
email: 'user@example.com', // PII ❌
access_token: 'eyJ...', // Secret ❌
password: '...', // Secret ❌
user_comment: inputValue // Unrestricted input ❌
}
```

### Summary

```text

1. WHAT happened?
   → Describe the business outcome

2. DID it actually happen?
   → Emit only when confirmed, once

3. WHAT does the event look like?
   → Define a stable contract

4. CAN the event stand alone?
   → Include all required context

5. DO we really need this data?
   → Collect only useful and safe data
   Together, these principles make the Data Layer business-oriented, reliable, consistent, self-contained, and privacy-safe.
```

## Example review: FD Calculation Action

The original FD payload is a useful starting point, but it does not fully satisfy the principles because it uses a generic UI-oriented event name, display labels as values, mixed naming conventions, and strings for numeric and Boolean concepts. The corrected example below makes the business fact, timing, types, and contract explicit while retaining the complete calculation snapshot needed by the approved reporting questions.

## FD Calculation Action — Business Flow and Data Layer Contract

### Business logic and event flow

The business wants to measure how often users change FD calculation inputs and how often those calculations produce a solution. The **Calculation Action** journey begins whenever a user changes an input value:

```text
User changes an FD input value
→ application captures the complete input snapshot
→ application sends that snapshot to the calculation API
→ API returns for the same snapshot
→ application determines whether the response produced output
→ application sets `solution_found` to `Yes` or `No`
→ application pushes one complete `calculation_action` message
→ GTM maps the approved fields and sends the event to GA4
```

The input change initiates the journey, but the application pushes the event only after the matching API response is known. This makes `solution_found` authoritative:

- `"Yes"`: the API returned a result that was produced in the output section.
- `"No"`: the API response produced no result in the output section.

### Canonical event fields

| Field                  | Type    | Required | Allowed/example      | Source                    | Rule                                                           |
| ---------------------- | ------- | -------- | -------------------- | ------------------------- | -------------------------------------------------------------- |
| `event`                | string  | Yes      | `calculation_action` | Application constant      | Stable business event name                                     |
| `event_schema_version` | string  | Yes      | `1.0`                | Application constant      | Increment for incompatible contract changes                    |
| `app_name`             | string  | Yes      | `fd`                 | Application constant      | Stable application identifier                                  |
| `solution_found`       | boolean | Yes      | `true`, `false`      | Matching API response     | `true` only when that response produced output                 |
| `inputs`               | object  | Yes      | See the table below  | Current calculation state | One complete snapshot associated with the matching API request |

### `inputs` fields

The current UI supplies display-form values, but the canonical contract uses stable machine-readable values and natural types. Keep the mapping from UI/API values to these values in application code, not in GTM.

| Field                   | Type    | Example                          | Source/UI mapping                |
| ----------------------- | ------- | -------------------------------- | -------------------------------- |
| `country`               | string  | `gb`                             | `United Kingdom`                 |
| `language`              | string  | `en`                             | `English`                        |
| `building_code`         | string  | `en_1995_1_1_2004_a2_2014`       | `EN 1995-1-1:2004/A2:2014`       |
| `design_method`         | string  | `lsd`                            | `Limit States Design (LSD)`      |
| `unit_system`           | string  | `metric`                         | `Metric`                         |
| `connection_type`       | string  | `clt_floor_floor_half_lap_joint` | `CLT Floor-Floor Half-Lap Joint` |
| `fastener_installation` | string  | `typical`                        | `Typical`                        |
| `fx`                    | number  | `1`                              | `fxInput: "1"`                   |
| `fy`                    | number  | `0`                              | `fyInput: "0"`                   |
| `load_duration`         | string  | `medium_term`                    | `Medium Term`                    |
| `main_member_thickness` | number  | `180`                            | `tm: "180"`                      |
| `side_member_thickness` | number  | `180`                            | `ts: "180"`                      |
| `side_member_grade`     | string  | `c24`                            | `sgs: "350 (C24)"`               |
| `side_member_density`   | number  | `350`                            | `sgs: "350 (C24)"`               |
| `main_member_grade`     | string  | `c24`                            | `sgm: "350 (C24)"`               |
| `main_member_density`   | number  | `350`                            | `sgm: "350 (C24)"`               |
| `contact_length`        | number  | `3000`                           | `contactLength: "3000"`          |
| `predrilled`            | boolean | `false`                          | `predrill: "No"`                 |
| `fastener_angle`        | number  | `90`                             | `alphaFastener: "90"`            |
| `service_class`         | string  | `service_class_1`                | `Service Class 1`                |

### Complete Data Layer message

This is the complete message for a calculation that produced a solution:

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

This design follows the Data Layer principles:

- **Business fact:** `calculation_action` identifies a completed FD calculation attempt, independent of a field label, component, or button.
- **Reliable event:** the matching API response is the authoritative trigger, and one completed change produces one event.
- **Stable contract:** field names, string types, allowed values, and sources are explicit. Breaking changes require a coordinated contract version update.
- **Self-contained message:** `solution_found` and the complete input snapshot are sent together.
- **Safe and useful data:** irrelevant UI text and GTM-owned metadata are omitted, and the payload contains no PII, credentials, or unrestricted user text.

## References

- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer): data layer structure, `dataLayer.push()`, event processing order, persistence, naming, and troubleshooting.
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en): how tags, triggers, variables, and the data layer work together.
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events): GA4 event names, parameters, custom events, and validation in Realtime and DebugView.
