# 05 — Consent Management for GA4 in Google Tag Manager

## 1. Objective, scope, and outputs

### Objective

Define a repeatable way to receive a visitor’s consent choice, apply it to Google Tag Manager (GTM), and verify that GA4 tags collect only what the approved policy allows.

### Scope

- Consent defaults and updates for a web GTM container.
- The Consent Initialization trigger, Consent Mode, built-in consent checks, and additional consent checks.
- CMP (Consent Management Platform) mapping, persistence, revocation, and environment control.
- QA evidence for storage, requests, destinations, and GA4 receipt.
- A practical FD calculation_action example.

Legal wording, the final regional policy, CMP vendor selection, and advertising implementation are owned by the relevant privacy or business owners. Advertising consent types are mentioned only when a tag inventory requires them; they are not the core of this guide.

### Outputs

Every consent-controlled tag should have:

1. An approved purpose, destination, and required consent types.
2. A recorded default and update path.
3. A GTM configuration and owner.
4. A QA record covering consent state, tag behavior, storage, request, and destination.

## 2. Overview: consent is a control applied to measurement

### 2.1 Plain definitions

| Term | Practical meaning |
| --- | --- |
| **Consent state** | The current value for a consent type: granted, denied, or not yet initialized. |
| **CMP** | The banner or preference center that obtains and stores the visitor’s choice. |
| **Consent Mode** | Google’s mechanism for passing consent state to Google tags so their storage and measurement behavior can change. |
| **Consent Initialization - All Pages** | GTM’s earliest trigger, used only by tags or templates that set or update consent. |
| **Built-in consent check** | Consent-aware behavior already provided by a Google tag. |
| **Additional consent check** | A GTM firing gate for a reviewed custom or third-party tag. |
| **Downstream** | A system after GTM that receives or processes the result; for this guide, GA4 DebugView is the first check and processed GA4 Reports are a later check. |

Data Layer, Variables, Triggers, Tags, and GA4 are defined in Sections 01–04. Consent does not replace any of them; it supplies an approval context when a tag is evaluated.

### 2.2 Lifecycle

~~~text
CMP / approved policy
        ↓
Consent Initialization - All Pages
        ↓
Set explicit defaults and apply any stored choice
        ↓
User chooses or changes preferences → send an update on the same page
        ↓
Application event enters the Data Layer
        ↓
Trigger matches → tag evaluates built-in/additional consent checks
        ↓
GA4 request, limited consent-aware behavior, or blocked tag
~~~

An application event can exist in the Data Layer even when analytics consent is denied. The event is not proof of permission. The tag’s consent behavior and the resulting request are the evidence.

### 2.3 Basic versus Advanced Consent Mode

Choose the mode with the privacy owner and record it in the consent contract. Verify the real tag and network behavior; a visible banner alone does not prove the mode.

| Mode | Before a choice | When analytics consent is denied | QA implication |
| --- | --- | --- | --- |
| **Basic** | Google tags are blocked until the user interacts with the banner. | Blocked tags should not send data. | Expect no GA4 request from the blocked tag before a choice. |
| **Advanced** | Google tags load with the configured default, commonly denied where opt-in applies. | Consent-aware tags may send limited cookieless signals and must not use denied analytics storage. | Check both storage and the contents of any request. |

The exact behavior depends on the tag, consent type, configuration, and Google product. Do not promise “zero network requests” without testing the selected implementation.

## 3. Consent contract: decide before configuring GTM

Record these decisions before creating or changing a consent tag:

| Contract item | Required decision |
| --- | --- |
| Purpose and destination | What data is collected, why, and where it goes. |
| Consent type | Which consent type controls the purpose; start with analytics_storage for GA4-only measurement. |
| Regions | Where the default and banner apply. |
| Default state | State before a usable stored choice is available. |
| Update source and timing | Which CMP callback or approved template sends the update, and when. |
| Persistence | Where the CMP stores the choice for the next page or visit. |
| Revocation | What changes immediately and how previously stored data is handled. |
| Unknown/failure behavior | Fail-safe behavior when the CMP is slow, blocked, or returns an invalid value. |
| Mode and version | Basic or Advanced Consent Mode and the policy/implementation version. |
| Owner and evidence | Responsible owner, ticket, approval, and QA location. |

### 3.1 State model

- **granted**: the approved purpose may use the allowed storage or data behavior.
- **denied**: the purpose must not use the denied storage or identifier; the tag is blocked or follows the selected consent-aware behavior.
- **Not set / unknown**: no reliable state exists. Treat it as an initialization failure, not as permission.

For an Additional Consent Check, every required type must be granted when the tag is evaluated. A later consent update does not retroactively approve an earlier tag execution.

### 3.2 Consent types used by a tag inventory

| Consent type | Controls | Use in this GA4-focused guide |
| --- | --- | --- |
| analytics_storage | Analytics storage such as analytics cookies. | Primary control for GA4 measurement when policy requires it. |
| ad_storage | Advertising-related storage. | Add only when an approved advertising tag exists. |
| ad_user_data | Sending user data to Google for advertising purposes. | Separate advertising data-use decision; not implied by analytics consent. |
| ad_personalization | Use of data for personalized advertising. | Separate advertising decision. |
| functionality_storage | Storage needed for site functionality, such as language settings. | Classify with the privacy owner; do not copy an analytics default blindly. |
| personalization_storage | Storage for personalization, such as recommendations. | Add only for an approved personalization purpose. |
| security_storage | Storage for authentication, fraud prevention, or security. | Usually essential, but confirm the organization’s policy. |

## 4. Implementation in GTM

### 4.1 Inventory the source of truth

Use one approved CMP or consent service as the source of truth for the current choice. Before editing GTM, record:

- CMP categories and their mapping to Google consent types.
- The container and environment that receive the state.
- The callback, template, or integration that sends updates.
- The owner and policy version.

Prefer an official or reviewed CMP integration/template. If a custom GTM consent template is necessary, use the Tag Manager Consent APIs **setDefaultConsentState** and **updateConsentState**. Do not replace these APIs with gtag consent calls inside a Custom HTML tag; Google notes that queued gtag commands may be processed after the next message.

### 4.2 Set the order with Consent Initialization

1. Use **Consent Initialization - All Pages** for the CMP/default-consent tag or template.
2. Set a default for every consent type used by the container, before measurement tags can send data.
3. Apply a stored choice, or send the user’s new choice through the approved update path.
4. Use **Initialization** for other early tags that do not manage consent.
5. Let normal application events and measurement tags run only after the consent state is available.

Consent Initialization always runs before Initialization and all other triggers. It is not a general “run early” trigger. If the CMP is asynchronous, use the integration’s documented waiting mechanism; do not add arbitrary exception triggers to compensate for a race.

### 4.3 Defaults, updates, persistence, and revocation

**Default**

- Set an explicit value for every consent type used.
- Scope regional defaults only when the approved policy requires different treatment.
- For a strict analytics opt-in, analytics_storage = denied is a common starting point; confirm the actual policy.
- Do not let an absent or malformed CMP value become an accidental granted.

**Update**

- Convert the CMP choice to the approved Google consent types.
- Send the update immediately on the page where the visitor confirms or changes preferences.
- Persist the choice in the CMP or approved consent store for subsequent pages.
- Re-evaluate future tags using the new state.

**Revocation**

- Send a new update when a user changes granted to denied.
- Document whether and how the CMP removes client storage or requests downstream deletion; Consent Mode does not define that process for the organization.
- Do not replay business events that occurred before consent unless the measurement plan explicitly allows it and duplicate prevention is defined.

### 4.4 Configure tag consent settings

Google tags that support Consent Mode have built-in consent behavior. Review the built-in consent types in each Google tag and use that behavior as the primary control.

For custom or third-party tags, choose one reviewed Additional Consent Checks setting:

| GTM setting | Use |
| --- | --- |
| **Not set** | Temporary default while the tag is awaiting review; it must not be treated as an approval. |
| **No additional consent required** | Explicitly record why built-in behavior or the tag’s approved design is sufficient. |
| **Require additional consent for tag to fire** | Add every consent type that must be granted before the tag can run. |

Do not add redundant Additional Consent Checks to a Google tag, and do not use an exception Trigger to override built-in Consent Mode behavior. Consent checks answer “may this tag run?”; Trigger logic still answers “does this event match?”

### 4.5 Environment and change control

Keep CMP configuration, consent defaults, container IDs, destinations, and policy versions aligned between staging and production. A consent change requires:

1. Contract and inventory update.
2. Privacy/business review when applicable.
3. GTM Preview and browser Network/storage testing.
4. A published container version with a rollback reference.
5. Post-release monitoring for unexpected requests, blocked tags, or environment drift.

## 5. QA and evidence

### 5.1 Test order

1. Start from a clean browser profile or clear the documented CMP storage.
2. Record the expected default and the expected behavior for the selected Consent Mode.
3. Open GTM Preview and inspect Consent Initialization before any application event.
4. Exercise the approved user choice, then run the business event.
5. Compare GTM status, browser storage, Network requests, and GA4 DebugView.
6. Repeat for denial, change/revocation, slow CMP, direct landing, SPA navigation, and each environment.

Section 08 defines the detailed evidence and pass/fail report format. This section defines what consent-specific evidence must be present.

### 5.2 Minimum matrix: what to do and what to verify

Run each row as a separate test. Start from the stated browser condition, perform the action, and record the evidence in the last column.

| Test scenario | Action | Pass condition |
| --- | --- | --- |
| First visit with no stored choice | Clear the documented CMP storage, load the page, and inspect Consent Initialization before clicking the banner. | The approved default is visible before normal tags run; no tag silently treats an unknown state as granted. |
| Analytics consent granted, then business event | Select the approved analytics option, confirm the consent update, and run one business event. | The update occurs on the same page; the GA4 tag follows built-in behavior; approved storage and one expected request are observed. |
| Analytics consent denied, then business event | Deny analytics, confirm the denied state, and run the same event. | Denied analytics storage is not used. Basic blocks the tag, or Advanced shows only the documented limited behavior. |
| Change or revoke consent | Start with granted, run one event, revoke analytics in the preference center, then run a second event. | The revoke update is sent on the current page; the second event follows the denied behavior; no duplicate business event is created. |
| CMP is slow or blocked | Throttle or block the CMP in a test environment, then load the page and run the event. | The approved default and fail-safe behavior remain in place; a missing CMP does not become accidental granted consent. |
| Direct landing and refresh | Open a deep link directly, then refresh without visiting another page first. | Consent state is initialized from the approved source; the test does not depend on an earlier Data Layer message. |
| SPA route change | Change routes without a full page reload and run the event once. | Stored consent remains available; no duplicate consent update or business event is produced. |
| Staging versus production | Repeat the approved path in each environment and compare IDs and destinations. | CMP configuration, container, measurement ID, destination, and policy version all match the environment record. |

### 5.3 Evidence and pass criteria

Capture:

- Consent state and order in GTM Preview, including the Consent Initialization event.
- Tag status, built-in checks, Additional Consent Checks, and matched Trigger.
- Cookies/storage and identifier creation before and after each choice.
- Network endpoint, timing, event name, consent-related fields, identifiers, and payload allowlist.
- GA4 DebugView or another approved downstream check. Downstream means the system after GTM: it confirms whether the request was received or processed, not merely that GTM allowed the tag to run.
- CMP record, policy version, environment, timestamp, and tester.

A consent test passes only when the documented state, storage behavior, request behavior, destination, and downstream result agree. “Downstream result” means the next system confirms the expected receipt or processing result; it does not mean that every report is updated immediately. “Banner appeared” or “Trigger matched” alone is not sufficient evidence.

## 6. Operational notes and common failures

### Rules to keep the implementation predictable

- Consent state is context, not a business event. Do not push a fake consent_granted event to make a GA4 tag fire.
- Keep one source of truth. Multiple CMPs, snippets, plugins, or containers can overwrite each other’s state.
- Treat missing, malformed, or stale state as not approved until it is resolved; never convert it silently to granted.
- Make consent updates idempotent so a repeated CMP callback does not create duplicate updates or business events.
- Keep raw calculation inputs, email addresses, account IDs, credentials, and unrestricted user input out of the Data Layer and GA4 payload unless separately approved.

### Common failures and the practical response

| Failure or symptom | What it usually means | Practical response |
| --- | --- | --- |
| A tag fires before the default is visible | Consent Initialization is missing, too late, or attached to the wrong tag. | Fix the Consent Initialization setup and retest from a clean browser state. |
| A denied choice still creates analytics storage | The Google tag or a custom tag is bypassing consent controls. | Inspect built-in/additional checks, hard-coded scripts, and third-party plugins; remove the bypass. |
| The same update or event appears twice | Multiple callbacks, containers, or SPA handlers are firing. | Keep one source of truth and add idempotency/duplicate protection. |
| GTM says “fired” but no request is visible | A browser restriction, ad blocker, or network failure may be involved. | Separate configuration evidence from delivery evidence; test in an approved clean browser and record the limitation. |
| Staging and production send to different places | Environment configuration drift exists. | Compare CMP mapping, container version, IDs, destinations, and policy version before publishing. |
| Consent changes but old cookies remain | Revocation and client-storage cleanup are separate from Consent Mode. | Document the CMP/browser cleanup behavior and test it with the privacy owner. |
| Server-side or iframe data ignores the new state | Consent was not propagated to the downstream component. | Add an explicit propagation contract and a separate end-to-end test. |

Review the GTM Consent Overview page after every material tag change so each tag has an intentional consent setting.

## 7. Cross-reference with the other sections

- **Section 01 — Data Layer Design:** business event and payload contract; a Data Layer message is not consent proof.
- **Section 02 — Variable Management:** map only approved scalar fields; missing values must not be converted into permission.
- **Section 03 — Trigger Management:** a Trigger match is separate from the consent decision; keep the event Trigger narrow and authoritative.
- **Section 04 — Tag Management:** Google tag/GA4 configuration, parameter allowlist, and request validation.
- **Section 07 — Measurement Plan:** define the consent expectation for each material event before implementation.
- **Section 08 — Debug and QA:** use the full evidence template and pass/fail decision.
- **Section 09 — Reports and Charts:** validate processed GA4 data only after the documented processing window.
- **Section 10 — Release Monitoring:** monitor the first production release for consent regressions and unexpected destinations.

## 8. Worked Journey: FD calculation_action

This example applies the approved FD event contract without sending raw inputs or personal data.

### Setup record

~~~text
CMP:                 approved web CMP
Default:             analytics_storage = denied until the approved choice
Update:              CMP callback → updateConsentState
GA4 tag:             GA4 - Event - calculation_action
Trigger:             CE - calculation_action - All
Destination:         approved GA4 web stream only
Additional check:    none; rely on the Google tag built-in analytics consent behavior
~~~

### Application message

~~~javascript
dataLayer.push({
  event: "calculation_action",
  calculation_action: "completed",
  calculation_type: "eligibility",
  calculation_outcome: "qualified"
});
~~~

### Expected flow

1. Consent Initialization sets the approved default.
2. The visitor grants or denies analytics consent; the CMP sends the update on the same page.
3. The application completes the calculation and pushes one calculation_action message.
4. The Trigger matches and the GA4 tag is evaluated.
5. With granted, the event is sent once with the approved scalar parameters.
6. With denied, the tag follows the selected Basic or Advanced behavior and must not use denied analytics storage.
7. If consent changes after the calculation, do not replay the event unless the measurement plan explicitly allows it.

### Acceptance evidence

- Correct default and update are visible before the event.
- Exactly one application message, one Trigger match, and one approved destination are observed.
- No email, account ID, raw financial input, or internal request token is present.
- Storage and Network behavior match the selected Consent Mode.
- GA4 DebugView receives the event only under the approved behavior.

## References

- [Google for Developers — Set up consent mode on websites](https://developers.google.com/tag-platform/security/guides/consent): defaults, updates, same-page timing, and Tag Manager Consent APIs.
- [Tag Manager Help — Tag Manager consent mode support](https://support.google.com/tagmanager/answer/10718549?hl=en): Consent Initialization, built-in checks, Additional Consent Checks, consent types, and Consent Overview.
- [Google for Developers — Consent mode overview](https://developers.google.com/tag-platform/security/concepts/consent-mode): basic and advanced Consent Mode behavior.
