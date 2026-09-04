# 04 — GTM Tag Management

## 1. Objective, scope, and outputs

This document defines how to design, configure, test, publish, and retire Google Tag Manager (GTM) Tags for stable GA4 measurement.

A Tag is the action GTM performs after its Trigger matches and its consent/settings allow it. For an FD calculation, the Tag sends the approved `calculation_action` event and parameters to the correct GA4 destination. The Tag should transport the contract; it should not decide whether the business result happened.

### In scope

- Google tag and GA4 Event tag design.
- Parameter mapping, Variables, Triggers, consent, sequencing, environment routing, and request count.
- Naming, descriptions, inventory, QA, publishing, monitoring, and retirement.
- FD `calculation_action` as the reference implementation.

### Out of scope

- Data Layer contract design; see Section 01.
- Variable source/type design; see Section 02.
- Trigger selection and filter design; see Section 03.
- Consent implementation; see Section 05.
- Custom-template governance; see Section 06.
- Measurement planning, Debug/QA, Reports, and Release Monitoring; see Sections 07–10.
- Advertising or campaign-specific Tags.

### Outputs

Each active Tag should have:

1. An approved purpose, event/action, destination, and owner.
2. A documented parameter allowlist with source and missing-data behavior.
3. An authoritative Trigger, consent behavior, environment routing, and expected count.
4. Preview, Network, and downstream validation evidence.
5. A versioned publication record and lifecycle status.

## 2. Overview: what a Tag does

### 2.1 Simple definitions

| GTM component | Practical meaning |
| --- | --- |
| Tag | The configured action that sends data to GA4 or another approved destination. |
| Google tag | Shared Google measurement configuration for the approved GA4 destination. It is one Tag type, not another name for every GTM Tag. |
| GA4 Event tag | Built-in Tag that sends one named event and its parameters to GA4. |
| Trigger | The rule that makes a Tag eligible to run; see Section 03. |
| Variable | The value source used by a Trigger or Tag; see Section 02. |
| Consent setting | The permission check that can prevent a Tag from sending. |
| Destination | The GA4 property/stream and Measurement ID that receive the request. |

The normal path is:

```text
Application confirms business fact
        ↓
Data Layer message
        ↓
Variables provide approved values
        ↓
Trigger matches
        ↓
Consent and exceptions allow
        ↓
Tag runs
        ↓
Environment-safe GA4 destination receives the request
```

The Application owns business truth. The Tag maps and sends approved data; it does not infer a result from a click, DOM text, or incomplete payload.

### 2.2 Google tag versus GA4 Event tag

Use one shared Google tag for the environment’s Google measurement configuration and a focused GA4 Event tag for each approved business event. A Google tag sets the destination/configuration; a GA4 Event tag sends the named event. Do not create duplicate event Tags only because the hostname or Measurement ID changes.

### 2.3 Tag readiness test

A Tag is not ready for production until the team can answer:

```text
What does it send?
When is it allowed to run?
Which values does it send?
Where does it send them?
What consent is required?
How many requests represent one business occurrence?
Who owns and can retire it?
```

## 3. Design the Tag

### 3.1 Tag contract

Record these fields before creating or changing a Tag:

| Property | What to record |
| --- | --- |
| Purpose | Business or operational requirement. |
| Tag type/template | Built-in Google tag, GA4 Event tag, or reviewed template. |
| Event/action | Exact event name or action. |
| Parameters | Approved names, source Variables, types, and required/optional status. |
| Trigger | Authoritative Trigger and conditions. |
| Consent | Required state and denied/update behavior. |
| Destination | Environment, GA4 property/stream, and Measurement ID source. |
| Expected count | Requests per valid business occurrence. |
| Sequencing | Genuine setup/cleanup dependency, if any. |
| Consumers | Reports, QA records, or downstream systems. |
| Owner/lifecycle | Accountable owner, status, and retirement condition. |

### 3.2 Design rules

- Create a Tag only for an approved requirement.
- Prefer built-in Google tag/GA4 Event tag types. Use a reviewed custom template only when a native type cannot meet the requirement. Use Custom HTML only as an approved exception.
- Map values from canonical Variables, preferably application-owned Data Layer values. Do not scrape DOM text or rebuild the business result in a Tag.
- Send only parameters in the Measurement Plan allowlist. Keep required, optional, type, privacy, and missing-data behavior explicit.
- Use one authoritative Trigger for the event: the single approved Trigger that represents the business moment. For FD, this is the application Custom Event `calculation_action` with only the required filters. Do not add broad click/page rules that send the same event; use a separate Trigger and event only when the contract intentionally measures a different fact.
- Configure consent through the approved design; do not create a custom “consent=true” bypass.
- Route destinations through reviewed environment configuration. Unknown hosts must fail closed, not fall back to production.
- Define one expected request per valid occurrence and remove overlapping legacy paths.
- Use tag sequencing only for a real dependency between Tags, never to manufacture an application workflow.

### 3.3 Native type decision

| Requirement | Preferred approach |
| --- | --- |
| Configure shared GA4 destination | Google tag with reviewed environment-safe Measurement ID. |
| Send a named GA4 event | Built-in GA4 Event tag. |
| Send a supported third-party action | Reviewed template with owner and permissions. |
| Unsupported behavior | Custom HTML only after security, privacy, and maintenance review. |

## 4. Configure the Tag in GTM

### 4.1 Search and reuse first

Search the container and inventory for an existing Google tag, GA4 Event tag, Trigger, Variables, exceptions, consent settings, sequencing, and destination mapping. Reuse only when purpose, event, parameters, consent, destination, and expected count remain compatible. Otherwise create a new focused Tag and document the difference.

### 4.2 Shared Google tag and environment routing

Use one reviewed Google tag per environment model. Route the Measurement ID through a Lookup Table or equivalent configuration:

```text
Known QA hostname         → QA Measurement ID
Known staging hostname    → staging/test Measurement ID
Known production hostname → production Measurement ID
Unknown hostname          → blank/blocked
```

Do not hard-code a production Measurement ID in an event Tag when controlled routing is available. Verify the actual `tid`/Measurement ID in the Network request; GTM Preview alone does not make a destination safe.

### 4.3 GA4 Event tag

Configure a built-in GA4 Event tag with:

```text
Event name: exact approved name and casing
Google tag: approved shared Google tag/configuration
Trigger: one authoritative Custom Event or other approved Trigger
Parameters: approved scalar allowlist only
Consent: approved analytics behavior
Expected count: documented requests per occurrence
```

Nested Data Layer paths are read through Version 2 Data Layer Variables (Section 02), then mapped to scalar GA4 parameter values. Do not send a nested object such as `inputs` as one parameter unless the contract explicitly supports it.

### 4.4 Parameter allowlist and types

For every parameter, verify:

- name and casing;
- source Variable and Data Layer path;
- string/number/Boolean type and unit;
- required or optional status;
- allowed values/cardinality;
- privacy classification;
- omit, block, or approved-default behavior when missing.

Do not send PII, credentials, tokens, secrets, raw user text, or an entire API response. Register a Custom Definition only when the field is needed for an approved report (Sections 07 and 09).

### 4.5 Consent, exceptions, and sequencing

These are separate controls:

- **Consent:** permission to collect. A Tag may be prevented from sending even when its Trigger matches if the required analytics consent is not granted.
- **Exception:** a documented blocking condition, such as an excluded environment or internal traffic rule. It blocks a Tag; it is not an alternative way to define the business event.
- **Tag sequencing:** the order in which dependent Tags run, such as setup before an event Tag. It does not wait for an API response, create missing Data Layer values, or replace the Application event.

Follow the approved consent design in Section 05. Test each control separately and document the expected result.

### 4.6 Firing behavior and overlap

Multiple firing Triggers on one Tag are alternatives (OR). Avoid overlapping Tags that send the same event for one business occurrence. Check duplicate container installation, generic and event-specific paths, SPA remounts, retries, and multiple analytics libraries.

## 5. Test and validate

### 5.1 GTM Preview

Use GTM Preview/Tag Assistant to inspect:

1. Data Layer event and values.
2. Variables used by the Trigger and Tag.
3. Trigger match and exceptions.
4. Consent state.
5. Tags Fired and Tags Not Fired.
6. Firing count for one controlled occurrence.

### 5.2 Network and GA4 validation

Validate the layers in order:

1. **Network request:** confirm that the browser actually sent the request. Check request count, event name/casing, parameter names/types, required and optional values, Measurement ID/destination, consent behavior, and absence of prohibited data.
2. **GA4 diagnostic view:** use DebugView or Realtime to check that GA4 can see the event during the test window. This is diagnostic evidence, not proof that processed Reports are already updated.
3. **Processed data:** use the documented Section 09 processing window before judging Reports or Explorations.

GTM Preview only proves that the Data Layer, Variables, Trigger, and Tag path behaved as configured. A Tag shown under **Tags Fired** does not prove that the correct request reached GA4. Link all evidence to Section 08.

### 5.3 Test coverage

| Case | Expected result |
| --- | --- |
| Valid business event | One Tag firing and one correct request. |
| Wrong event name/case | Tag does not fire. |
| Missing required Variable | Tag is blocked or QA fails according to the contract. |
| Optional Variable missing | Parameter is omitted. |
| Invalid input/API failure | No successful-outcome request. |
| Duplicate callback/retry/remount | No unintended duplicate request. |
| Consent denied/granted/updated | Behavior follows the approved consent design. |
| Unknown environment | No production request. |
| Wrong destination | Stop release and correct routing. |
| Existing overlapping Tag | Remove or document the separate purpose before release. |

## 6. Review, publish, and maintain

### 6.1 Naming and description

Use:

```text
[SCOPE] - [TYPE] - [EVENT OR PURPOSE] - [QUALIFIER]
```

Examples of type labels are `Google tag`, `GA4 Event`, `TPL` (reviewed template), and `HTML` (approved Custom HTML exception). Use the exact canonical event spelling. Avoid `New Tag`, `Tag 14`, `Copy`, `Test`, or `Temp` for active Tags.

The Tag description should record purpose, Measurement Plan reference, event name, parameter allowlist, Variables, Trigger, consent, destination, expected count, dependencies, owner, and retirement condition.

### 6.2 Inventory

Maintain one row per Tag:

```text
Tag name and type/template
Purpose and Measurement Plan reference
Event/action and parameter allowlist
Variables and source paths
Firing Trigger(s), exceptions, and sequencing
Consent behavior
Environment and destination/Measurement ID source
Expected request count
Consumers and dependencies
Owner, status, and replacement
Published version and QA/production evidence
Review date and retirement condition
```

### 6.3 Publish and monitor

Before publishing:

1. Confirm the Tag contract and all consumers.
2. Test positive, negative, duplicate, consent, and destination cases.
3. Verify the actual Network request and request count.
4. Confirm environment routing and privacy behavior.
5. Publish a versioned GTM change with release note, owner, evidence, and rollback point.
6. Update the inventory and monitor after release (Section 10).

### 6.4 Lifecycle and retirement

Use `Proposed → Development → QA → Active → Deprecated → Retired`. Retire a Tag only after its consumers are removed/replaced, no sequencing or shared dependency remains, the replacement passes QA, and a recoverable version is retained.

## 7. Cross-reference map

- [Section 01 — Data Layer Design](01-data-layer-design-answer.md): application-owned event timing, payload, and business truth.
- [Section 02 — Variable Management](02-variable-management-answer.md): canonical Variable source, types, and missing-data behavior.
- [Section 03 — Trigger Management](03-trigger-management-answer.md): authoritative Trigger, filters, exceptions, and frequency.
- [Section 05 — Consent Management](05-consent-answer.md): consent defaults, updates, and denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer.md): reviewed template use and governance.
- [Section 07 — Measurement Plan](07-measurement-plan-answer.md): event purpose, parameter approval, and owners.
- [Section 08 — Debug/QA](08-debug-qa-answer.md): Preview, Network, evidence, defects, and retests.
- [Section 09 — Reports and Charts](09-reports-charts-answer.md): field readiness and processed-data interpretation.
- [Section 10 — Release Monitoring](10-release-monitoring-answer.md): release gates, monitoring, incidents, and rollback.

## 8. Worked Journey: FD `calculation_action`

This is the only concrete walkthrough. Replace identifiers with approved project values.

### 8.1 Google tag

```text
Name: FD - Google tag - Primary
Type: Google tag
Measurement ID: {{LUT - Shared - GA4 Measurement ID by Hostname}}
Environment map:
  app-staging.example.com → QA Measurement ID
  app.example.com         → Production Measurement ID
  unknown host            → blank/blocked
```

### 8.2 GA4 Event tag

```text
Name: FD - GA4 Event - calculation_action
Type: GA4 Event
Google tag: FD - Google tag - Primary
Event name: calculation_action
Trigger: FD - CE - calculation_action - Approved
Consent: approved analytics consent behavior
Expected count: one request per accepted calculation occurrence
```

Parameter mapping:

```text
event_schema_version → {{FD - DLV - event_schema_version}}
app_name             → {{FD - DLV - app_name}}
solution_found       → {{SHARED - DLV - solution_found}}
connection_type      → {{FD - DLV - inputs - connection_type}}
fx                   → {{FD - DLV - inputs - fx}}
fy                   → {{FD - DLV - inputs - fy}}
```

### 8.3 Trigger

```text
Name: FD - CE - calculation_action - Approved
Type: Custom Event
Event name: calculation_action
Conditions:
  app_name equals fd
  event_schema_version equals 1.0
```

### 8.4 Application message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: "Yes",
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 8.5 Validation decision

```text
Valid output response
    → one Data Layer message
    → Trigger matches once
    → one GA4 Event Tag fires
    → one request reaches the QA/production Measurement ID

Valid response with no output
    → same flow with solution_found = "No"

Invalid input, API failure, stale response, duplicate callback,
unknown host, or denied consent
    → behavior follows the contract and Section 08 test record
```

## References

- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [Tag Manager Help — Google tag](https://support.google.com/tagmanager/answer/11994839?hl=en)
- [Tag Manager Help — GA4 Event tag](https://support.google.com/tagmanager/answer/9442095?hl=en)
