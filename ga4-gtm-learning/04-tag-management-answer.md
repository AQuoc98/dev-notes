# 04 — GTM Tag Management

## Theory

### What is a tag?

A tag defines an action that GTM performs when the required conditions are met.

To understand a tag, answer three simple questions:

1. **What does the tag do?**  
   Example: Send the `calculation_action` event to GA4.

2. **When should the tag do it?**  
   Example: When the `calculation_action` Data Layer event occurs and its trigger matches.

3. **Where should the data go?**  
   Example: Send the event to the approved FD GA4 destination.

A simplified GTM flow is:

```text
Application / Browser
        ↓
Data Layer event and values
        ↓
Variables
        ↓
Triggers and exceptions
        ↓
Consent evaluation
        ↓
Tag
        ↓
Environment-safe destination
        ↓
Network request and downstream platform
```

### What is the Google tag?

The Google tag is a Google measurement configuration that connects a site to supported Google destinations, such as GA4 and Google Ads. It is one tag type within GTM, not a synonym for every GTM tag.

```text
GTM tag
├── Google tag                 ← shared Google measurement configuration
├── GA4 Event tag              ← sends a named GA4 event
├── Google Ads conversion tag
├── Approved template tag
└── Custom HTML tag            ← exception requiring review
```

The old global site tag (`gtag.js`) and the Google tag are related Google measurement concepts, but a GTM container still contains many kinds of tags.

## Common Tag Types

Choose the most specific supported tag type for the requirement.

| Requirement                                 | Preferred tag type             | Example                                                                  |
| ------------------------------------------- | ------------------------------ | ------------------------------------------------------------------------ |
| Configure shared Google measurement         | Google tag                     | Configure the FD GA4 destination using approved environment routing      |
| Send a GA4 business event                   | GA4 Event                      | Send `calculation_action` with approved parameters                       |
| Send a Google Ads conversion                | Google Ads Conversion Tracking | Send a confirmed purchase conversion                                     |
| Support an approved third-party integration | Reviewed template              | Use a reviewed Community Template with a named owner                     |
| Implement unsupported custom behavior       | Custom HTML                    | An approved exception after security, privacy, and implementation review |

Prefer built-in GTM tag types or reviewed templates. Custom HTML is not a default alternative when a supported tag type already provides the required behavior.

## Anatomy of a Tag

A production tag should be understandable through the following components:

| Component       | Question to answer                                                    |
| --------------- | --------------------------------------------------------------------- |
| Purpose         | Why does this tag exist, and what requirement does it satisfy?        |
| Type/template   | What supported tag type or reviewed template is used?                 |
| Event/action    | What measurement or operational action does it perform?               |
| Parameters      | Which contract-defined values are sent?                               |
| Variables       | Where does each value come from, and what happens when it is missing? |
| Trigger         | What authoritative condition makes the tag eligible to run?           |
| Exceptions      | When must it not run? Are the exceptions required and tested?         |
| Consent         | What consent state is required, and what happens when it changes?     |
| Destination     | Which environment and property/stream receive the data?               |
| Firing behavior | How many requests are expected for one business occurrence?           |
| Sequencing      | Is there a genuine setup or cleanup dependency?                       |
| Consumers       | Which teams, reports, audiences, or downstream systems use the data?  |
| Owner           | Who approves changes, answers questions, and confirms retirement?     |
| Lifecycle       | Is the tag proposed, in QA, active, deprecated, or retired?           |

If any of these cannot be answered, the tag is not ready for production.

## Design Standard

- **Create a tag only for a documented measurement or operational requirement.** Every production tag must have a clear purpose and owner.  
  **Example:** Create `GA4 Event - FD - calculation_action` only when `calculation_action` is defined in the approved FD measurement plan.

- **Prefer built-in GTM tag types or reviewed templates.** Use Custom HTML only when no suitable supported option exists and the implementation has been reviewed.  
  **Example:** Use a GA4 Event tag to send `calculation_action` instead of writing a custom `gtag()` call in a Custom HTML tag.

- **Map parameters from approved variables and prefer application-owned Data Layer values over DOM or raw form-field collection.** Never send PII, credentials, secrets, or unrestricted user input.  
  **Example:** Use `{{FD - DLV - solution_found}}` rather than reading result text from the page.

- **Send only parameters defined by the measurement contract.** Required and optional parameters must follow their documented missing-value behavior.  
  **Example:** Omit optional `product_category` when unavailable; fail QA when required `connection_type` is missing instead of sending `unknown`.

- **Use the simplest reliable trigger that uniquely represents the intended measurement condition.** Add filters or exceptions only when required by the tracking contract and when their behavior is understood and tested.  
  **Example:** If the application pushes `event: "calculation_action"`, use that exact Custom Event instead of adding click and DOM conditions.

- **Avoid overlapping tags that send the same measurement for the same business occurrence.** Define one canonical tracking path and the expected request count.  
  **Example:** One completed calculation should produce exactly one `calculation_action` request, not one from a generic FD tag and another from a calculation-specific tag.

- **Do not use tag sequencing as a substitute for an application or Data Layer contract.** Use and document sequencing only when a genuine setup or cleanup dependency exists.  
  **Example:** Do not fire Tag B after Tag A just to construct calculation data; the application should provide the complete event and values.

- **Define and test the consent behavior required by the tag.** Do not bypass the approved consent design with custom trigger logic.  
  **Example:** Respect the approved analytics consent behavior instead of creating a custom `consent = true` trigger as a replacement.

- **Do not treat “Tag Fired” in GTM Preview as proof of successful collection.** Verify the actual request, destination, event name, parameters, consent state, and expected request count.  
  **Example:** After `calculation_action` fires, verify that one request reaches the expected GA4 Measurement ID with the approved payload.

- **Do not duplicate tags only to handle environment differences.** Route environment-dependent configuration, such as GA4 Measurement IDs, through reviewed shared configuration where appropriate.  
  **Example:** Use one FD GA4 Event tag with an environment-safe configuration variable rather than separate staging and production copies.

- **Environment routing must fail safely.** Unknown or non-production environments must not accidentally send data to the production destination.  
  **Example:** An unknown hostname returns no Measurement ID or blocks the tag instead of falling back to production.

- **Review a tag’s triggers, exceptions, variables, consent settings, sequencing, destination, and consumers before modifying or retiring it.**  
  **Example:** Before removing `GA4 Event - FD - calculation_action`, check every reference, downstream consumer, replacement, and production dependency.

## Tag Decision Guide

Before creating a tag, answer these questions in order:

1. Is there an approved measurement or operational requirement?
   - **No:** Do not create the tag.
2. Does an existing tag already satisfy the requirement?
   - **Yes:** Reuse it only if its purpose, destination, consent, and contract remain compatible.
3. Is there a built-in GTM tag type?
   - **Yes:** Prefer it.
4. Is there a reviewed template for the requirement?
   - **Yes:** Prefer the reviewed template over Custom HTML.
5. Are all required values available through approved variables?
   - **No:** Fix the application/Data Layer or variable contract first.
6. Is there an authoritative trigger?
   - **No:** Improve the application signal where possible; do not compensate with increasingly broad DOM or route logic.
7. Is consent behavior defined?
   - **No:** Define it before release.
8. Is the destination environment-safe?
   - **No:** Fix routing before creating or publishing the tag.
9. Can positive, negative, duplicate, consent, and destination cases be tested?
   - **No:** Do not publish.
10. Is there a named owner and inventory record?
    - **No:** Assign ownership before the tag becomes production-active.

## Naming Standard

Use a predictable format:

```text
[TYPE] - [SCOPE] - [EVENT OR PURPOSE] - [QUALIFIER]
```

Examples:

```text
Google tag - FD - Primary
GA4 Event - FD - calculation_action
Google Ads - Web - purchase
CE - FD - calculation_action
DLV - FD - solution_found
LUT - Shared - GA4 Measurement ID by Environment
```

Naming rules:

- Use the same canonical event spelling and casing as the measurement contract.
- Include scope when the container serves more than one product or application.
- Name Custom Event triggers with `CE`; name Data Layer Variables with `DLV`; name lookup tables with `LUT`.
- Avoid names such as `New Tag`, `Test`, `Copy`, or `Tag 14` in a shared container.
- If a legacy design must remain, include `Legacy` in the name where that helps distinguish it from the preferred path.

The tag description must record the business purpose, owner, requirement or ticket, event name, parameter contract, destination, consent behavior, expected firing count, dependencies, and retirement condition.

## Tag Firing Behavior

### Events create the timeline

GTM evaluates triggers on each event in the Data Layer/event queue. Page lifecycle events generally progress through stages such as:

```text
Consent Initialization
        ↓
Initialization
        ↓
Container Loaded / Page View
        ↓
DOM Ready
        ↓
Window Loaded
        ↓
Application and user events
```

Custom Events occur in the order the application pushes them. For FD, the meaningful sequence may be:

```text
Input updated
        ↓
Calculation completes
        ↓
calculation_action pushed
        ↓
GA4 Event tag evaluated and possibly fired
```

### Triggers do not form a sequence

Multiple firing triggers on one tag are generally alternative conditions:

```text
Trigger A matches OR Trigger B matches
        ↓
Tag may fire
```

The order in which triggers appear in the GTM UI is not execution order. A Trigger Group confirms that its member conditions have occurred; it should not be used to manufacture a business process that belongs in the application.

### Expected request count is part of the contract

Document the expected frequency explicitly:

```text
One confirmed calculation
        ↓
One calculation_action request
```

Check for duplicate causes, including:

- repeated Data Layer pushes;
- generic and event-specific trigger overlap;
- SPA navigation or component remounts;
- retries, callbacks, and API completion handlers;
- multiple tags sending the same event;
- tag sequencing that unintentionally repeats a tag.

Use GTM’s supported firing options where appropriate, but do not rely on a firing option to correct a broken business-event contract. The application or Data Layer should define whether two completed calculations are two legitimate occurrences.

## Environment & Destination Management

Environment routing must be centralized, reviewable, and fail-safe.

### Required controls

- Keep the Google tag/GA4 configuration in a shared, reviewed location where practical.
- Route environment-dependent values through a reviewed configuration variable or lookup table, for example `{{LUT - Shared - GA4 Measurement ID by Environment}}`.
- Map known environments explicitly, such as local, QA, staging, and production.
- Do not hard-code a production Measurement ID in an event tag when a controlled shared configuration is available.
- Do not create duplicate tags solely because the destination differs by environment.
- Treat an unknown hostname, missing environment, or invalid mapping as a block or no-send outcome.
- Verify the actual Measurement ID in the outgoing request; GTM Preview does not by itself make a request safe for production.
- Keep destination, stream/property, region, and data governance assumptions in the inventory and tag description.

Example routing behavior:

```text
Known QA hostname         → QA Measurement ID
Known staging hostname    → staging/test Measurement ID
Known production hostname → production Measurement ID
Unknown hostname          → undefined / tag blocked
```

If a separate container or workspace is required for an environment, document why and keep the configuration contract consistent. Environment separation should not create divergent tracking logic without review.

## Inventory & Ownership

The inventory is the operational record for the container, not just a list of tag names.

Minimum fields:

| Field                | What to record                                          |
| -------------------- | ------------------------------------------------------- |
| Tag                  | Exact GTM tag name                                      |
| Type/template        | Built-in type or reviewed template                      |
| Purpose/requirement  | Business reason and tracking-plan reference             |
| Event/action         | Exact event or operational action                       |
| Firing triggers      | All triggers and their conditions                       |
| Exceptions           | Blocking conditions and rationale                       |
| Variables/parameters | Source, type, required/optional behavior                |
| Consent              | Required state and denied/update behavior               |
| Destination          | Environment, property/stream, Measurement ID source     |
| Expected count       | For example, `1 per calculation`                        |
| Environment          | Local, QA, staging, production, or controlled routing   |
| Consumers            | Reports, audiences, exports, or systems depending on it |
| Owner                | Accountable business/analytics owner and maintainer     |
| Status               | Proposed, Development, QA, Active, Deprecated, Retired  |
| Dependencies         | Google tag, sequencing, templates, legacy paths         |
| Replacement          | Approved replacement, if any                            |
| Evidence             | Version, ticket, QA and production validation links     |
| Review date          | Next scheduled review or expiry date                    |

Ownership means more than being listed in a spreadsheet. The owner must approve the measurement purpose, parameter contract, destination, consent behavior, and retirement decision.

## Review and Test Workflow

Use this workflow for a new tag and for material changes to an existing tag:

1. Trace the business requirement to the approved tracking plan and Data Layer contract.
2. Search the container and inventory for existing tags, triggers, variables, legacy paths, and consumers.
3. Select the simplest approved tag type and document why it is appropriate.
4. Verify or create approved variables with correct scope, types, and missing-value behavior.
5. Configure the shared Google tag/destination through environment-safe routing.
6. Review firing triggers, exceptions, consent settings, sequencing, and expected request count.
7. Run Preview tests for valid, invalid, repeated, navigation/SPA, and consent cases.
8. Confirm the actual network request, destination, payload, consent state, and request count.
9. Obtain the required peer/business/privacy review and record the evidence.
10. Publish a focused version with release notes and a rollback plan.
11. Perform a production smoke test after publishing.
12. Monitor volume, quality, consent behavior, and duplication; update the inventory.

Review “Tag Fired” as one diagnostic signal, not as end-to-end collection proof.

## Tag Lifecycle

Recommended statuses:

```text
Proposed
    ↓
Development
    ↓
QA
    ↓
Active
    ↓
Deprecated
    ↓
Retired
```

- **Proposed** — a requirement exists, but implementation is not approved.
- **Development** — configuration is being built or changed.
- **QA** — the implementation is under validation and is not yet production-approved.
- **Active** — approved, published, inventoried, and monitored.
- **Deprecated** — still present temporarily, but an approved replacement or retirement plan exists.
- **Retired** — no active requirement or consumer remains; configuration can be removed after dependency review.

Lifecycle rules:

- Pause only as a short, documented diagnostic or rollback measure; do not allow paused tags to become permanent clutter.
- Before deletion, inspect all trigger, variable, sequencing, template, destination, and consumer dependencies.
- Retain a versioned recovery point and evidence before removal.
- Record the owner, replacement, retirement reason, date, and approval.
- Deprecate legacy `webApps` consumers only after verifying the authoritative event path and actual production request count.
- Recheck reports, audiences, exports, and downstream uses before retiring an event.

## Common Anti-patterns

| Anti-pattern                                                          | Risk                                       | Preferred approach                                              |
| --------------------------------------------------------------------- | ------------------------------------------ | --------------------------------------------------------------- |
| Custom HTML for a supported GA4 event                                 | Unnecessary code and harder review         | Use the built-in GA4 Event tag                                  |
| DOM scraping or raw form collection                                   | Fragile, privacy, and data-quality risk    | Use approved application-owned Data Layer values                |
| Hard-coded production Measurement ID                                  | Environment leakage                        | Use controlled shared routing with safe failure                 |
| Separate copies of the same tag per environment                       | Drift and duplicate maintenance            | Reuse shared logic and route configuration centrally            |
| Generic and event-specific tags for one occurrence                    | Duplicate collection                       | Define one canonical tracking path and expected count           |
| Broad `webApps`/route trigger without a contract                      | Hidden coupling and accidental matches     | Prefer an exact business Custom Event; bound any legacy filters |
| Unanchored or case-insensitive regex used to hide inconsistent values | Unexpected matches and contract drift      | Use canonical values and explicit, bounded patterns             |
| Click trigger for a completed calculation                             | Click does not prove business success      | Trigger on the authoritative completion event                   |
| Tag sequencing used to build business logic                           | GTM becomes an application workflow engine | Provide complete event data from the application                |
| “Tag Fired” used as collection evidence                               | Does not prove payload or destination      | Validate the network request and downstream receipt             |
| Permanent paused or copied tags                                       | Container clutter and unclear ownership    | Track lifecycle and retire obsolete configuration               |
| Unknown owner or missing inventory                                    | Unsafe changes and slow incident response  | Assign ownership and record dependencies                        |
| Placeholder values for required fields                                | Silent data-quality failures               | Fail QA and fix the upstream contract                           |
| Consent bypass through custom triggers                                | Privacy and governance failure             | Use the approved consent design and test state changes          |

## Detailed Best-Practice Example — Create One Production-Quality FD GA4 Event Tag

This example shows the complete lifecycle for one tag. Names are examples and must be aligned with the team’s actual naming and measurement-plan conventions.

### Step 1 — Start from the measurement contract

Do not begin by clicking **New Tag**. Begin with an approved contract.

| Contract field         | Example                                                                         |
| ---------------------- | ------------------------------------------------------------------------------- |
| Business requirement   | Measure one completed FD calculation                                            |
| Event name             | `calculation_action`                                                            |
| Business occurrence    | The application has completed the calculation and the result state is available |
| Authoritative signal   | Data Layer `event: "calculation_action"`                                        |
| Expected request count | Exactly one request per completed calculation                                   |
| Required parameter     | `solution_found`: boolean from the approved Data Layer contract                 |
| Required parameter     | `connection_type`: approved enumerated value                                    |
| Optional parameter     | `product_category`: approved value; omit when unavailable                       |
| Missing required value | Fail QA and block release; do not send a placeholder                            |
| Missing optional value | Omit the parameter unless the contract specifies another behavior               |
| Privacy rule           | No PII, credentials, secrets, raw form values, or unrestricted user input       |
| Consent rule           | Follow the approved analytics consent matrix                                    |
| Destination            | FD GA4 destination selected by environment-safe configuration                   |
| Owner                  | Named FD analytics/product owner and GTM maintainer                             |
| Consumers              | Approved GA4 reports, explorations, or downstream processes                     |

The measurement contract is the source of truth. If the contract is incomplete, resolve that gap before configuration.

### Step 2 — Check for existing paths and reuse approved assets

Before creating anything:

1. Search the container inventory for an existing `calculation_action` tag, trigger, or dispatcher consumer.
2. Inspect references to any existing generic FD tag, `webApps` trigger, and legacy `app_action` conditions.
3. Determine whether the canonical path already exists and whether a new tag would create a duplicate.
4. Reuse an approved Google tag/configuration, variables, consent settings, and templates when their contracts are compatible.
5. Record the legacy path as a dependency if it must remain during migration.

Do not assume that a tag with a similar name is safe to reuse. Compare its purpose, event name, parameters, consent, destination, and request count.

### Step 3 — Verify or create the approved variables

Use application-owned Data Layer values. Do not read the DOM or raw fields to reconstruct the event.

Example variables:

| Variable                                           | Type                          | Source                                              | Validation                                    |
| -------------------------------------------------- | ----------------------------- | --------------------------------------------------- | --------------------------------------------- |
| `FD - DLV - solution_found`                        | Data Layer Variable           | `solution_found` on the `calculation_action` event  | Must be boolean and present for a valid event |
| `FD - DLV - connection_type`                       | Data Layer Variable           | `connection_type` on the `calculation_action` event | Must match the approved enum                  |
| `FD - DLV - product_category`                      | Data Layer Variable           | `product_category` on the event                     | Optional; omit when absent                    |
| `LUT - Shared - GA4 Measurement ID by Environment` | Lookup/configuration variable | Approved environment mapping                        | Unknown environment returns no production ID  |

For each variable, verify:

- the key spelling and casing;
- the event scope and value type;
- the value is available at the moment the trigger is evaluated;
- missing-value behavior;
- no PII or unrestricted user input can enter the payload;
- the variable is not an accidental alias for a legacy field.

If a required value is unavailable, fix the application/Data Layer contract rather than adding a DOM scrape or a default placeholder.

### Step 4 — Configure the environment-safe Google tag and destination

Reuse or configure the reviewed FD Google tag/shared Google measurement configuration.

Recommended configuration behavior:

```text
Known FD QA/staging environment
        → approved non-production Measurement ID

Known FD production environment
        → approved production Measurement ID

Unknown or unsupported environment
        → no valid Measurement ID / tag blocked
```

Use a reviewed shared configuration variable or lookup table. Do not paste the production Measurement ID directly into the event tag if centralized routing is available. Confirm which GA4 property/stream belongs to each environment and record it in the inventory.

The event tag must not silently fall back from a missing or unknown environment to production.

### Step 5 — Choose the GA4 Event tag type

Create or reuse a built-in GA4 Event tag:

```text
Tag type: GA4 Event
Name:     GA4 Event - FD - calculation_action
Google tag / configuration: approved FD shared Google tag
Event name: calculation_action
```

Do not implement this event in Custom HTML or in a custom `gtag()` call when the supported GA4 Event tag provides the required behavior.

### Step 6 — Map only approved parameters

Map the contract to reviewed variables:

| GA4 parameter      | GTM value                         | Behavior                                                     |
| ------------------ | --------------------------------- | ------------------------------------------------------------ |
| `solution_found`   | `{{FD - DLV - solution_found}}`   | Required boolean; missing value fails QA                     |
| `connection_type`  | `{{FD - DLV - connection_type}}`  | Required approved enum; missing/invalid value blocks release |
| `product_category` | `{{FD - DLV - product_category}}` | Optional; omit when unavailable                              |

Do not add convenient extras such as raw input text, URL fragments containing user data, form values, API responses, or internal secrets. A parameter belongs in the tag only when it is in the approved measurement contract.

If GTM’s configuration would send an empty or undefined value contrary to the contract, adjust the variable/tag configuration or fix the contract before publishing.

### Step 7 — Use the authoritative Custom Event trigger

Create or reuse:

```text
Name:       CE - FD - calculation_action
Trigger:    Custom Event
Event name: calculation_action
Condition:  All Custom Events, unless the approved contract requires an explicit FD scope filter
```

The application should push the event only after the completed calculation is a real business occurrence:

```javascript
dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  connection_type: "approved_value",
  product_category: "approved_value",
});
```

Do not add click, DOM, or broad page-path conditions just because they were present in the legacy implementation. Add an application scope condition only when the container has multiple producers of the same event and the measurement contract explicitly requires it.

### Step 8 — Define consent behavior

Apply the approved consent settings to the GA4 Event tag and verify how the tag behaves when consent is:

- granted before the event;
- denied before the event;
- unavailable or not yet initialized;
- changed after page load;
- updated before a repeated calculation.

For this example, assume the approved FD policy requires analytics consent before the event tag sends. Under that policy:

```text
Analytics consent granted
        → tag may send if the event and parameters are valid

Analytics consent denied/unavailable
        → tag follows the approved blocked or privacy-preserving behavior
          and must not be forced to send by a custom trigger
```

If the organization uses Consent Mode with approved cookieless behavior, document that exact behavior in the consent matrix and validate the resulting request. Do not treat “not fired” or “fired” as sufficient evidence by itself.

### Step 9 — Complete naming, description, owner, and dependencies

Use:

```text
Tag:     GA4 Event - FD - calculation_action
Trigger: CE - FD - calculation_action
```

Example tag description:

```text
Purpose: Measure one completed FD calculation.
Contract: FD measurement plan / calculation_action.
Event: calculation_action.
Parameters: solution_found (required), connection_type (required),
product_category (optional; omit when unavailable).
Destination: FD Google tag selected by environment-safe configuration.
Consent: Approved analytics consent behavior; see consent matrix.
Expected count: One request per completed calculation.
Owner: FD Analytics / named maintainer.
Legacy dependency: webApps dispatcher path is not the preferred trigger;
verify consumers before migration or retirement.
Retirement: When the contract is replaced and all consumers migrate.
```

Record the owner, approver, implementation ticket, destination mapping, and any legacy consumer in the inventory before release.

### Step 10 — Run Preview tests, including negative and duplicate cases

Use GTM Preview/Tag Assistant to inspect the event timeline, Data Layer values, trigger result, tags fired, and tags not fired. Test at least:

| Test case                            | Expected result                                                                            |
| ------------------------------------ | ------------------------------------------------------------------------------------------ |
| Valid completed calculation          | `calculation_action` event is present; tag is eligible and sends once when consent permits |
| `solution_found = false`             | Tag still follows the contract; boolean is sent as the approved value                      |
| Missing required `connection_type`   | Tag does not produce an invalid production request; QA fails and release is blocked        |
| Missing optional `product_category`  | Event is sent without `product_category` if consent and other required values permit       |
| Different application event          | `CE - FD - calculation_action` does not match                                              |
| Click without completed calculation  | Tag does not fire                                                                          |
| Similar/legacy `webApps` event       | Preferred tag does not fire unless an explicit migration design says it should             |
| Repeated identical push              | Request count follows the contract; duplicate behavior is investigated, not ignored        |
| Two calculations                     | Two legitimate occurrences produce two requests, one per completion                        |
| Consent granted                      | Behavior matches the approved consent matrix                                               |
| Consent denied/unavailable           | Tag is blocked or behaves according to the approved privacy-preserving design              |
| Consent changes before a calculation | Result follows the documented update behavior                                              |
| SPA navigation/remount               | No extra request occurs without a new completed calculation                                |
| QA/staging hostname                  | Non-production destination is selected                                                     |
| Production hostname                  | Production destination is selected only for the known production environment               |
| Unknown hostname                     | No production fallback; tag is blocked or has no valid destination                         |

Preview proves how GTM evaluated the event. It does not by itself prove that the destination received the intended request.

### Step 11 — Validate the network request and payload

For each allowed positive case, inspect the browser Network panel or Tag Assistant Hit Details and confirm:

- the request was made to the expected Google endpoint;
- the Measurement ID/property is the correct environment destination;
- the event name is exactly `calculation_action`;
- required parameters are present and correctly typed;
- optional parameters follow their missing-value behavior;
- no PII, secrets, raw form values, or unexpected parameters are present;
- consent behavior is represented as approved;
- the request count is exactly one per completed calculation;
- DebugView or another downstream check receives the event where applicable.

The minimum evidence is not merely:

```text
Tag Fired ✓
```

It is:

```text
Correct event
Correct payload
Correct consent behavior
Correct Measurement ID/destination
Correct request count
```

### Step 12 — Publish with version and evidence

Before publishing:

1. Review the tag, trigger, variables, exceptions, consent settings, Google tag/configuration, destination, and references.
2. Confirm the legacy `webApps` path will not create a duplicate request.
3. Save Preview screenshots or exported evidence showing positive, negative, duplicate, consent, and destination cases.
4. Record the workspace/version, change ticket, reviewer, owner, release notes, and rollback plan.
5. Publish a focused version with a clear version name, for example `FD calculation_action GA4 Event - initial production release`.

If a required parameter, destination mapping, consent setting, or request-count result is unresolved, do not publish.

### Step 13 — Perform a production smoke test and monitor

After publishing:

- run one controlled production calculation that is safe for analytics QA;
- confirm the request reaches the production Measurement ID and contains the approved payload;
- verify that exactly one request is generated;
- confirm no test/staging destination receives production traffic;
- check GA4 DebugView or the approved downstream validation view where applicable;
- monitor event volume, parameter completeness, error signals, and duplicate-rate indicators;
- record the smoke-test time, environment, evidence, and result.

Do not declare success from the publish confirmation alone. A published container can still have a wrong trigger, destination, consent state, or payload.

### Step 14 — Record inventory and lifecycle state

Add the tag and its dependencies to the inventory:

```text
Tag:              GA4 Event - FD - calculation_action
Type:             GA4 Event
Purpose:          One event per completed FD calculation
Trigger:          CE - FD - calculation_action
Parameters:       solution_found, connection_type, product_category (optional)
Consent:          Approved analytics consent behavior
Destination:       Environment-safe FD Google tag / GA4 destination
Expected count:   1 per completed calculation
Environment:      QA, staging, and production through reviewed routing
Owner:             FD Analytics / named maintainer
Status:            Active after production smoke test
Consumers:         Approved GA4 reports and downstream uses
Legacy path:       webApps dispatcher inventoried and tracked for migration
Replacement:       None, or the approved future replacement
Evidence:          Version, ticket, Preview, network, and smoke-test records
```

Set the lifecycle status to `Active` only after the production smoke test passes. If the tag replaces a legacy dispatcher consumer, mark the legacy tag/trigger `Deprecated` only after traffic and consumer verification.

## References

- [Tag Manager Help — About Tags](https://support.google.com/tagmanager/answer/3281060?hl=en): tag purpose, supported tag templates, custom tags, and custom templates.
- [Google Analytics — Set up events](https://developers.google.com/analytics/devguides/collection/ga4/events): GA4 event implementation, parameters, and validation in Realtime and DebugView.
- [Tag Manager Help — Preview and debug containers](https://support.google.com/tagmanager/answer/6107056?hl=en): validating which tags fired, in what order, and what data they processed.
- [Tag Manager Help — Publishing, versions, and approvals](https://support.google.com/tagmanager/answer/6107163?hl=en): version snapshots, publish history, rollback, and approvals where available.
- [Tag Manager Help — Environments](https://support.google.com/tagmanager/answer/6311518?hl=en-GB): publishing container versions to development, staging, and production environments.
