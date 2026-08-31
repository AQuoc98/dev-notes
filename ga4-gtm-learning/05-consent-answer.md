# 05 - Google Tag Manager Consent Management and Governance

## What consent means in GTM

Consent is the user’s permission for a specific type of storage or data use. In Google Tag Manager (GTM), consent signals tell Google tags and other tags how they may behave.

Consent management has three separate responsibilities:

1. **Obtain** the user’s choice through a Consent Management Platform (CMP), banner, or another approved consent solution.
2. **Communicate** that choice to GTM and Google products as consent states.
3. **Enforce** the choice through built-in tag behavior, additional consent checks, tag configuration, and governance controls.

Consent is not the same as a trigger. A trigger identifies an event that qualifies a tag to run; consent determines whether the tag may run and, for consent-aware tags, what data or storage behavior is allowed.

## Why consent matters

Consent management helps the organization:

- Respect user privacy choices and applicable regulatory requirements.
- Control cookies, identifiers, and advertising-related data use.
- Prevent unapproved collection while preserving privacy-safe measurement where supported.

Consent Mode is a signal-and-behavior mechanism. It does **not** create the consent banner, decide the organization’s legal basis, or automatically make every third-party tag compliant.

## Consent Mode

Google Consent Mode lets a website communicate the user’s consent state to Google. Google tags then adjust cookie, identifier, and measurement behavior according to that state.

### Basic and advanced implementations

| Implementation            | Before the user chooses                                                                    | If the user denies                                                                               | Measurement implication                                                 |
| ------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------- |
| **Basic Consent Mode**    | Google tags are blocked from loading until the banner interaction.                         | No data is sent to Google by those blocked tags, including the consent status.                   | Modeling is based on a general model.                                   |
| **Advanced Consent Mode** | Google tags load with configured defaults, normally denied where an opt-in policy applies. | Consent-aware Google tags can send limited cookieless signals and do not use the denied storage. | Can support more detailed, advertiser-specific modeling where eligible. |

Choose the implementation with the privacy owner and document the decision. Do not infer the implementation from whether a banner is visible; verify the actual tag and network behavior.

### Important nuance: denied does not always mean zero network requests

`denied` generally means that the relevant Google tag must not use the denied storage or identifier. In advanced Consent Mode, a tag may still send limited cookieless pings, such as consent-state or measurement signals, depending on the tag, consent type, configuration, and Google product behavior.

Therefore, QA must check both:

- Whether cookies, local storage, or identifiers are created or read.
- Whether requests are sent, what they contain, and whether they are limited to the approved behavior.

Basic Consent Mode has a different expectation: blocked Google tags should not send data before a user interaction.

## Consent states

Each consent type should have an explicit operational state:

- **`granted`** — the user or approved policy allows the relevant storage or use.
- **`denied`** — the user or approved policy does not allow it.
- **Not set / unknown** — no usable state has been established. Treat this as an implementation defect or an uninitialized state, not as permission.

For an additional consent check in GTM, the tag only passes when all required consent types are `granted`. A `denied` or unresolved state should prevent that additional check from passing.

### Key consent types

| Consent type              | Controls                                                                                                  | Governance interpretation                                                                                      |
| ------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `analytics_storage`       | Storage used for analytics measurement, such as analytics cookies.                                        | Required for analytics storage where the policy requires opt-in.                                               |
| `ad_storage`              | Storage used for advertising, including Google Ads cookies and identifiers used by supported Google tags. | Required for advertising storage. Do not treat it as a substitute for the advertising data-use controls below. |
| `ad_user_data`            | Sending user data to Google for advertising-related purposes.                                             | Review separately from cookie storage; it is a data-use signal, not simply a cookie flag.                      |
| `ad_personalization`      | Use of data for personalized advertising.                                                                 | Review separately from collection and storage. A user may allow measurement but not personalized ads.          |
| `functionality_storage`   | Storage supporting site or app functionality, such as language or session preferences.                    | Often essential or functional, but classify it by the organization’s policy.                                   |
| `personalization_storage` | Storage used for personalization, such as content or video recommendations.                               | Permit only for the approved personalization purpose.                                                          |
| `security_storage`        | Storage used for security, authentication, fraud prevention, or user protection.                          | Usually a security control; confirm the required treatment with the privacy and security owners.               |

The first four types are commonly associated with Google advertising and analytics controls. The last three are additional privacy storage types supported in GTM and should be mapped to the organization’s CMP categories.

### Granted and denied behavior

Use this table as a governance model, then verify the exact behavior for each tag and vendor.

| State             | Google-tag expectation                                                                                                                    | Third-party-tag expectation                                                                                 |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| `granted`         | The tag may use the approved storage or data behavior, subject to its configuration and destination.                                      | The tag may run only if its purpose and required consent have been approved.                                |
| `denied`          | The tag must not use the denied storage or identifier. It may be blocked or may send limited cookieless signals in advanced Consent Mode. | Block the tag or apply the vendor’s documented consent-aware behavior. Do not assume automatic enforcement. |
| Not set / unknown | Establish a default before measurement. Never rely on an accidental default.                                                              | Treat as not approved until the state is resolved.                                                          |

Also consider the difference between **revoking consent** and **removing previously stored data**. Consent Mode does not by itself define the organization’s cookie-deletion or data-retention process. Verify how the CMP, browser, vendor, and server-side systems handle revocation.

## Page-load order and Consent Initialization

Consent must be established before normal page triggers and measurement tags are evaluated.

Recommended logical order:

```text
Page starts
   ↓
Consent Initialization - All Pages
   ↓
Set consent defaults and load/read the CMP state
   ↓
Apply any stored user choice through the consent update
   ↓
Initialization and normal triggers
   ↓
Google tag, GA4, Ads, and other tags evaluate consent
   ↓
User changes preferences → update consent on the same page
```

### Consent Initialization versus Initialization

- Use **Consent Initialization - All Pages** only for tags or templates that set or update consent, such as the CMP integration or a default-consent template.
- Use the normal **Initialization** trigger for other tags that need to run early but do not manage consent.
- Consent Initialization is designed to run before other triggers, including Initialization.

If the CMP is asynchronous, the implementation must handle the race between the CMP and measurement tags. Use the approved CMP integration or an appropriate waiting mechanism. Do not solve the race by adding arbitrary exception triggers to Google tags.

## Default consent and consent updates

### Default consent

Set an explicit default for every consent type used by the implementation. Defaults should reflect the approved policy and may be region-specific.

For a strict opt-in policy, a typical starting point for measurement and advertising is:

```text
analytics_storage  = denied
ad_storage          = denied
ad_user_data        = denied
ad_personalization  = denied
```

Do not copy this example blindly to functionality or security storage. A default is a policy decision and must not break essential site functions.

The default must be set before commands or tags that send measurement data. For a custom GTM consent template, use Tag Manager’s consent APIs such as `setDefaultConsentState` rather than relying on a queued `gtag('consent', ...)` call inside Custom HTML.

### Consent updates

When the user accepts, rejects, or changes a category:

1. Convert the CMP choice to the approved Google consent types.
2. Send the update immediately on the page where the choice occurs.
3. Persist the choice in the CMP or approved consent store.
4. Re-evaluate future tags and events using the new state.

Do not wait until after navigation or page unload. An update made just before a page transition may not complete, and the next page can start with an incomplete state. Consent Mode does not store the user’s choice for you; the CMP or consent solution must do that.

## CMP integration

The CMP is normally responsible for the user interface, category descriptions, preference storage, and consent records. GTM is responsible for receiving the state and applying it to tags.

Document the integration contract:

| Contract item    | Required decision                                                                  |
| ---------------- | ---------------------------------------------------------------------------------- |
| Source of truth  | Which CMP or consent service owns the current choice?                              |
| Category mapping | Which CMP categories map to each Google consent type?                              |
| Initial state    | What defaults apply before the CMP is ready, and in which regions?                 |
| Update event     | Which callback, template, or API call sends a changed choice?                      |
| Timing           | How is an asynchronous CMP prevented from racing normal tags?                      |
| Revocation       | How are cookies, identifiers, and downstream permissions handled after withdrawal? |
| Evidence         | Where are the policy version, consent record, and test evidence stored?            |

Prefer a supported CMP integration or a reviewed community template. If the organization maintains a custom integration, place it under change control and test the consent API calls, race conditions, regional behavior, and failure path.

## GTM consent settings

GTM has two related mechanisms:

### Built-in consent checks

Consent-aware Google tags contain built-in logic that changes tag behavior based on the consent state. Common Google tags with built-in Consent Mode support include:

- Google tag
- Google Analytics / GA4
- Google Ads
- Floodlight
- Conversion Linker

For these tags, use the built-in checks as the primary control. Review the built-in consent types in the tag’s consent settings.

### Additional consent checks

Additional consent checks are GTM firing gates. They are useful for custom, partner, or third-party tags that do not have suitable built-in consent behavior.

Available settings include:

- **Not set** — no additional checks configured; this is the default and must be reviewed.
- **No additional consent required** — an explicit reviewed decision that no additional consent is needed beyond any built-in behavior.
- **Require additional consent for tag to fire** — the tag fires only when every specified consent type is `granted`.

Governance rules:

- Do not add additional consent checks to Google tags merely to duplicate their built-in checks.
- Do not use exception triggers to block Google tags when Consent Mode is already controlling their behavior.
- Use additional checks for reviewed third-party or custom tags that must not fire without a specific consent type.
- Record why a tag is marked “No additional consent required.”
- Enable and review GTM’s Consent Overview regularly.

Additional consent checks control whether a tag fires. They do not replace a CMP, a consent default, a Consent Mode update, or a legal policy.

## How the components fit together

```text
CMP / consent policy
        │  default + update states
        ▼
Consent Initialization / consent APIs
        │
        ▼
GTM consent state ───────────────┐
        │                         │
        │                         ├─ Built-in consent checks
        │                         └─ Additional consent checks
        ▼
Data Layer event + variables → Trigger matches → Tag is evaluated
                                                    │
                                                    ▼
                           Google tag / GA4 destination / partner endpoint
```

Relationship summary:

- **Data Layer** carries structured business events and values. It is not itself proof of consent.
- **Triggers** decide whether an event matches a tag’s firing conditions.
- **Consent** is evaluated when the tag is considered for execution and can control firing or behavior.
- **Tags** transform configuration and data into a measurement or marketing action.
- **The Google tag** is a central Google measurement connection that can send data to linked destinations such as GA4 and Google Ads.
- **GA4 destinations** receive events from the Google tag or GA4 event tags according to the configured measurement and consent behavior.

### Trigger matched versus data actually sent

These are different observations:

| Observation                          | What it proves                                            | What it does not prove                                                              |
| ------------------------------------ | --------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| Trigger matched                      | The event and trigger conditions were true.               | That the tag executed or sent a request.                                            |
| Tag fired in Preview                 | GTM allowed the tag to execute in that container session. | That a full measurement payload was sent, or that a vendor accepted it.             |
| Request visible in the network panel | A request was made to an endpoint.                        | That every Data Layer value was included or that the request was legally permitted. |
| Cookie or storage value exists       | A storage operation occurred.                             | That the value came from the expected tag or has the intended purpose.              |

When investigating a discrepancy, inspect the event timeline, consent state, tag status, tag parameters, browser storage, and network request together.

## Management workflow

Use the following workflow for new tags and material consent changes:

1. **Request** — document the business purpose, vendor, destination, event, and expected outcome.
2. **Classify** — identify storage, data use, personal data, advertising, personalization, and security implications.
3. **Map** — map the purpose to one or more consent types and document the legal/privacy decision.
4. **Inventory** — record the tag, trigger, variables, built-in checks, additional checks, owner, and environment.
5. **Implement** — configure defaults, CMP updates, built-in behavior, and additional checks.
6. **Test** — validate Preview, browser storage, network requests, user flows, regions, and failure paths.
7. **Review** — obtain analytics/GTM, engineering, privacy, and security approval as applicable.
8. **Publish** — publish a version with a change description and rollback reference.
9. **Monitor** — check diagnostics, tag behavior, consent rates, data quality, and unexpected vendors.
10. **Review or retire** — periodically revalidate purpose, consent mapping, vendor terms, and actual behavior; remove unused tags.

## Ownership and inventory

Assign clear owners for privacy/legal, Analytics/GTM, CMP/engineering, business/marketing, QA/data quality, and security. Record at least:

```text
Tag name and ID; vendor and endpoint; business purpose; data collected and classification;
destination; cookies/storage/identifiers; built-in and additional consent checks;
trigger and exception configuration; CMP category mapping; regions and environments;
owner and backup owner; last review date; change ticket, approval, and QA evidence.
```

## QA, Preview, and network testing

### Minimum test matrix

| Scenario                          | Expected checks                                                                                      |
| --------------------------------- | ---------------------------------------------------------------------------------------------------- |
| New visitor, no prior choice      | Correct default state appears before normal tags.                                                    |
| Accept all                        | Consent types update to the approved granted state; approved tags and storage work.                  |
| Reject analytics                  | Analytics storage is not used; verify the expected basic/advanced network behavior.                  |
| Reject advertising only           | Advertising storage and data-use signals remain denied while permitted analytics behavior continues. |
| Granular choice                   | Each CMP category maps to the correct Google consent types.                                          |
| Change or revoke choice           | Update is sent on the current page; future behavior changes; cleanup behavior is documented.         |
| Direct landing page               | No dependency on a previous page or prior Data Layer event.                                          |
| Slow or blocked CMP               | Defaults and fail-safe behavior remain correct.                                                      |
| SPA route change                  | Consent state persists and events do not duplicate.                                                  |
| Multiple tabs / returning visitor | Stored choice is handled consistently and does not silently override a newer choice.                 |
| Region-specific visitor           | Correct regional banner, defaults, and tag behavior are applied.                                     |
| Staging / production              | Correct container, CMP configuration, IDs, and destinations are used.                                |

### What to inspect

1. **GTM Preview / Tag Assistant:** consent state at each event, Consent Initialization order, trigger matches, fired and blocked tags, built-in checks, and additional checks.
2. **Browser storage:** cookies, local storage, session storage, and identifier creation before and after each choice.
3. **Network panel:** request timing, endpoint, consent parameters, identifiers, event name, and whether the request is limited or full.
4. **GA4 DebugView or equivalent:** whether the intended event and parameters arrive after the approved consent state.
5. **CMP record:** whether the choice, category, policy version, and timestamp are recorded according to policy.

Do not mark a test as passed solely because the banner appeared or the trigger matched. Capture evidence for the consent state, storage, request, destination, and expected outcome.

## Failure modes and edge cases

Watch for these conditions:

- **CMP timing:** the CMP loads too late, the default is missing, or an update is cancelled during navigation. Fix the order or use an approved waiting strategy.
- **Stale or duplicate consent:** stored consent survives a policy change, or multiple CMPs, containers, snippets, or plugins send conflicting states.
- **Hard-coded or ungoverned tags:** site code, CMS, app, vendor plugin, or partner tag bypasses the GTM consent design.
- **Google tag over-blocking:** exception triggers or additional checks stop a built-in consent-aware tag from behaving as intended.
- **SPA, cross-domain, or iframe behavior:** route changes duplicate updates/events, or consent is not transferred consistently.
- **Revocation and server-side gaps:** the new state is sent, but cookies, server-side records, or downstream enforcement are not handled according to policy.
- **Sensitive data leakage:** calculation inputs, account identifiers, email addresses, or other personal data are pushed or sent before the approved state.
- **Environment drift:** staging and production have different CMP mappings, defaults, container versions, or destination IDs.

## Change control

Require a documented change for:

- Adding, removing, or repurposing a tag or destination.
- Changing a consent default or regional rule.
- Changing CMP categories or the mapping to Google consent types.
- Changing Consent Mode from basic to advanced, or vice versa.
- Changing a Google tag’s built-in or additional consent settings.
- Changing event parameters that may contain personal or sensitive data.
- Adding server-side forwarding or a new downstream vendor.

Each change should include purpose, impact assessment, inventory updates, privacy review where required, test evidence, approver, publish version, and rollback plan.

## FD-style example: `calculation_action`

Assume FD has a calculator that sends a GA4 event when a user completes a calculation. The event must not contain unnecessary personal data.

### Data Layer event

```javascript
dataLayer.push({
  event: "calculation_action",
  calculation_action: "completed",
  calculation_type: "eligibility",
  calculation_outcome: "qualified",
});
```

Recommended setup:

```text
Tag:     GA4 - Event - calculation_action
Trigger: CE - calculation_action - All
Event:   calculation_action
Params:  calculation_action, calculation_type, calculation_outcome
Consent: Built-in analytics_storage behavior
```

### Expected flow

1. The CMP and `CMP - Consent - Default` run through Consent Initialization.
2. The approved default is applied, for example `analytics_storage = denied` before an opt-in choice.
3. The user completes the FD calculation and the Data Layer event is pushed.
4. The `calculation_action` trigger matches.
5. GTM evaluates the GA4 tag and its built-in consent behavior.
6. If analytics consent is granted, the event can be sent according to the approved Google tag and GA4 event configuration.
7. If analytics consent is denied, behavior depends on the selected basic or advanced Consent Mode implementation: the tag may be blocked, or limited cookieless signals may be sent without analytics storage.

Do not blindly replay an event that occurred before consent after the user grants consent. Decide whether the business event is still valid, whether replay is allowed by policy, and how to prevent duplicate key events, Ads conversions, or calculation records.

### QA acceptance criteria for the example

- The event is not sent to an unapproved destination.
- `calculation_action` is not confused with a consent type; it is a business event name or parameter.
- No email, account ID, raw financial input, or other unnecessary personal data is included.
- With analytics denied, the expected storage and network behavior is observed for the chosen Consent Mode implementation.
- With analytics granted, GA4 receives the event and documented parameters once.
- Consent changes do not create duplicate calculation events.

## References

- [Google for Developers — Consent mode overview](https://developers.google.com/tag-platform/security/concepts/consent-mode): consent states, basic versus advanced consent mode, and how Google tags adapt their behavior.
- [Google for Developers — Set up consent mode on websites](https://developers.google.com/tag-platform/security/guides/consent): default and updated consent states, Tag Manager Consent APIs, and implementation guidance.
- [Tag Manager Help — Tag Manager consent mode support](https://support.google.com/tagmanager/answer/10718549?hl=en): Consent Initialization, built-in consent checks, additional consent checks, and Consent Overview.
- [Tag Manager Help — About consent mode](https://support.google.com/tagmanager/answer/10000067?hl=en): consent-mode behavior, cookieless pings, and Google tag support.
