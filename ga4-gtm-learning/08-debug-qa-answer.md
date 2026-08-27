# 08 — Debugging Workflow and Analytics QA

## Purpose

Debugging should identify the first failing layer, not just confirm that a tag appears as fired. Analytics QA verifies occurrence, payload, routing, consent, privacy, and reporting behavior.

Use this pipeline:

```text
User interaction
  → application state
  → Data Layer message
  → GTM event/variable evaluation
  → tag decision and consent
  → GA4 network request
  → GA4 DebugView/Realtime
  → processed report/Exploration
```

If an event is missing in DebugView, the cause could be the application, Data Layer, trigger, variable, tag, consent, browser/network, Measurement ID, property selection, debug mode, or processing. Test each boundary in order.

## What Each Tool Proves

| Tool/layer | What to inspect | What it proves | What it does not prove |
| --- | --- | --- | --- |
| Application | The authoritative state and event call | The product can expose the intended signal | GTM received or routed it |
| Data Layer | Event name, values, types, order, count | GTM has a message to evaluate | A tag fired or a request was sent |
| GTM Preview/Tag Assistant | Event timeline, variables, fired/not-fired tags, consent, order | The previewed container evaluated the draft as expected | Production is using the same version or GA4 accepted the request |
| Browser Network | Request URL, Measurement ID, event name, parameters, count, status | The browser attempted the intended collection request | GA4 has processed the event into every report |
| GA4 DebugView | Debug device, event, parameters, timing | GA4 received a debuggable event | Normal reports are complete or attribution is final |
| Realtime | Current users/events and basic collection | The property is receiving recent activity | Historical processing and final attribution |
| Standard report/Exploration | Processed dimensions, metrics, filters, scope, freshness | The data is usable for the defined analysis | That the implementation was correct without upstream evidence |

GTM Preview and debug mode lets a tester inspect which tags fired, which did not, the order of events, and the data processed by the previewed container. See [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056). Tag Assistant also provides troubleshooting diagnostics for missing, duplicated, or incorrectly configured Google tags; see [Tag Assistant](https://support.google.com/tagmanager/answer/13355721).

## Prepare a Safe Test

Record before testing:

| Field | Example |
| --- | --- |
| Test ID | TC-REG-001 |
| Environment | QA/staging, not production |
| URL and application version | `[sanitized URL]`, `[commit/build]` |
| GTM container/workspace/version | `[container]`, `[workspace]`, `[version]` |
| GA4 property and web stream | `[property]`, `[stream]` |
| Measurement ID | `G-XXXXXXX` or sanitized evidence |
| Browser/device | Chrome, desktop, version/date |
| Account/data | Synthetic account and safe values |
| Consent state | Granted, denied, or unresolved by category |
| Expected event | `sign_up` once with `method` and `form_id` |
| Tester/reviewer | `[name]` |

Use synthetic values. Never put real names, email addresses, phone numbers, addresses, authentication tokens, payment data, or free-form user content into the test or evidence.

## End-to-End Debugging Workflow

### Step 1 — Confirm the expected behavior

Read the measurement plan before opening the debugger. Write:

```text
Action: Complete a valid registration
Business moment: Server confirms account creation
Expected Data Layer event: one sign_up
Expected request count: one
Required parameters: method, form_id
Expected destination: QA stream only
Expected consent: approved analytics behavior
Negative cases: invalid input, server failure, double submit, refresh
```

Do not debug an implementation against an undefined expectation.

### Step 2 — Start the correct preview session

1. Open the intended GTM container and workspace.
2. Start Preview and connect to the QA URL.
3. Confirm the page reports that the intended container is connected.
4. Check the environment, container ID, and previewed version.
5. Preserve the network log if navigation or redirects are involved.
6. Use a clean or controlled browser state when cookies, consent, or SPA state could affect the result.

The preview interface is session-specific; do not assume a colleague is viewing the same draft unless a shared preview session was created.

### Step 3 — Reproduce one action

Perform exactly one test action, then stop. Avoid clicking around before capturing the relevant event. Repeat only after resetting the test state.

Check:

- Did the application reach the intended business state?
- Did the Data Layer event occur once?
- Did it occur at the correct moment?
- Are the event name, parameter names, types, and values correct?
- Are optional values omitted as designed?
- Is any prohibited data present?

Example Data Layer message:

```javascript
{
  event: 'sign_up',
  method: 'email',
  form_id: 'registration'
}
```

If the business outcome did not happen, a success event must not be used merely because the button was clicked.

### Step 4 — Inspect GTM evaluation

At the exact event in Tag Assistant:

1. Confirm the Custom Event name matches the contract exactly, including case.
2. Inspect every variable used by the trigger and tag.
3. Confirm the expected trigger matched.
4. Confirm tags that should fire fired once.
5. Confirm tags that should not fire are in the not-fired state and understand why.
6. Check blocking triggers, exceptions, consent requirements, additional consent, sequencing, and tag settings.
7. Search for another tag that could send the same event.
8. Confirm the Google tag and GA4 Event tag point to the intended destination.

Remember:

```text
Multiple firing triggers on one tag → alternative conditions (OR)
Multiple conditions in one trigger → all must match (AND)
Any matching exception/blocking condition → tag is blocked
```

“Tag Fired” means GTM selected the tag for execution. It does not prove that a network request succeeded or that GA4 processed the intended payload.

### Step 5 — Inspect the network request

Use browser Developer Tools and filter for the GA4 collection request, commonly containing `collect`. Inspect the actual request rather than relying on the GTM UI.

Verify:

- the request count equals the expected count;
- the destination Measurement ID is the QA/test ID;
- the event name is correct;
- required parameters are present with the correct values and types;
- no extra or prohibited fields are sent;
- consent-related signals match the approved design;
- the request is not blocked by an extension, browser privacy setting, Content Security Policy, or network error;
- redirects/navigation did not discard required linker or campaign information.

Never copy live identifiers or sensitive payloads into a public ticket. Redact identifiers and values while retaining enough context to prove the result.

### Step 6 — Check GA4 DebugView

DebugView displays events and user properties collected from a debug-enabled device in near real time. For a website, Tag Assistant or GTM Preview can enable the device debug signal; alternatively, `debug_mode` can be configured for an approved test scope. Select the correct property and debug device, then confirm the event appears once with the expected parameters.

Google notes that events may not appear in DebugView when client-side privacy controls or consent mode prevent Analytics storage/collection. DebugView and Realtime also have limited attribution behavior, so do not use them as the final source for historical acquisition conclusions. See [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382).

After testing, ensure debug traffic is separated or filtered according to the property’s approved data-quality design. Do not leave an all-user `debug_mode` setting active in production.

### Step 7 — Validate processed reporting data

Once the normal processing window has passed:

- select the correct property, stream, timezone, and date range;
- confirm the event appears in the Events report;
- confirm custom definitions are available only after the documented registration/processing delay;
- check metric and dimension scope;
- compare expected counts with the test evidence;
- check for `(other)`, thresholding, sampling, incompatible fields, and incomplete recent dates;
- record any discrepancy instead of silently adjusting the conclusion.

DebugView is a collection diagnostic, not proof that standard reports are immediately populated.

## Decision Tree for Common Failures

```text
Expected event missing
  ├─ Business state happened?
  │   ├─ No → fix test precondition/application flow
  │   └─ Yes
  ├─ Data Layer event pushed once?
  │   ├─ No → fix application/Data Layer contract
  │   └─ Yes
  ├─ GTM custom event name/variables match?
  │   ├─ No → fix trigger/variable mapping
  │   └─ Yes
  ├─ Tag fired and consent allowed it?
  │   ├─ No → inspect trigger, exception, consent, sequencing
  │   └─ Yes
  ├─ Network request exists and destination is correct?
  │   ├─ No → inspect tag config, Google tag, blocker, CSP, network
  │   └─ Yes
  ├─ DebugView device/property/debug mode correct?
  │   ├─ No → fix debug selection or test configuration
  │   └─ Yes
  └─ Processed report missing?
      → wait for processing, then check registration, scope, filters, thresholds, and date range
```

### Failure diagnosis matrix

| Symptom | First checks | Likely layer | Evidence to capture |
| --- | --- | --- | --- |
| No Data Layer event | Business success, app callback, event push | Application/Data Layer | Console/app log and test state |
| Data Layer event exists, tag not fired | Exact event name, trigger filters, variables, exceptions | GTM | Tag Assistant event and not-fired reason |
| Tag fired, no request | Google tag/config, consent, blocker, tag error | GTM/browser | Tag details, console, network log |
| Request has wrong ID | Environment lookup, Google tag, stream selection | Routing | Redacted request URL/payload |
| Request has wrong parameter | DLV path, variable timing, type conversion | Data contract/GTM | Data Layer plus request comparison |
| Two requests for one action | Duplicate push, overlapping tags, SPA remount, retry | Application/GTM | Timeline and request count |
| Request exists, DebugView empty | Wrong property/device, debug mode, consent/privacy, delay | GA4/debug setup | Property, device, consent, timestamp |
| DebugView correct, report wrong | Processing, custom-definition delay, filters, scope, thresholding | GA4 reporting | Report settings and date range |
| QA data in production | Environment routing or wrong stream | Release/routing | Request destination, version, hostname |

## Required Test Matrix

Use test IDs that remain stable across releases.

| ID | Case | Action | Expected |
| --- | --- | --- | --- |
| TC-01 | Happy path | Complete valid flow | One canonical event, all required parameters valid |
| TC-02 | Validation failure | Submit invalid input | No success/key event |
| TC-03 | Server failure | Force failed response | No success/key event |
| TC-04 | Double submit | Click/submit rapidly | One event for one business occurrence |
| TC-05 | Refresh/back | Refresh or revisit confirmation | No unintended duplicate |
| TC-06 | SPA route | Enter, leave, and revisit route | Page/route events follow plan; no duplicate business event |
| TC-07 | Missing optional | Omit optional value | Omit or fallback exactly as documented |
| TC-08 | Missing required | Force required value missing | QA fails; no misleading success payload |
| TC-09 | Consent denied | Deny relevant consent | Behavior matches approved consent design; no prohibited request |
| TC-10 | Consent granted | Grant relevant consent | Expected collection begins/updates correctly |
| TC-11 | Routing | Run on QA hostname | Request goes only to QA destination |
| TC-12 | Privacy | Inspect Data Layer and request | No PII, secret, raw form value, or unsafe URL |
| TC-13 | Browser | Test supported browser/device | No material implementation difference |
| TC-14 | Regression | Run adjacent journey | Existing events remain correct and non-duplicated |

## Consent Debugging

Consent is a runtime condition and a debugging dimension. Test at least:

1. default state before the user interacts with the banner;
2. analytics denied;
3. analytics granted;
4. advertising denied/granted where relevant;
5. consent update after the page has loaded;
6. navigation and SPA transitions after each state;
7. returning user with stored consent;
8. unresolved or failed CMP state.

For each case, record:

- consent default and update timing;
- which tags were expected to fire or remain blocked;
- whether a request was sent and which signals it carried;
- whether the behavior matches the approved privacy design;
- whether DebugView visibility is expected under that state.

Do not create ad hoc trigger logic that bypasses the approved consent model. If a tag is unexpectedly blocked, inspect the tag’s consent requirements and any additional consent checks in Tag Assistant. See [unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079).

## Evidence Template

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
| --- | --- | --- | --- | --- | --- | --- |
| TC-01 | Application | Confirmed success | `[actual]` | `[link]` | Pass/Fail | `[ID]` |
| TC-01 | Data Layer | One `sign_up` with valid values | `[actual]` | `[screenshot/log]` | Pass/Fail | `[ID]` |
| TC-01 | GTM | Correct tag fires once | `[actual]` | `[Tag Assistant]` | Pass/Fail | `[ID]` |
| TC-01 | Network | One request to QA ID | `[actual]` | `[redacted HAR/screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | Consent | Approved state | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | DebugView | Event visible once | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | Report | Processed field/count plausible | `[actual]` | `[report link/screenshot]` | Pass/Fail | `[ID]` |

Evidence must identify the date, environment, version, property/stream, tester, browser, result, and known limitations. Redact sensitive values and never store credentials in evidence.

## Debug/QA Template Set

These templates turn a debugging session into a repeatable record. Use the evidence template for layer-by-layer proof, the session record for the overall test context, and the defect record when the expected result is not met. Keep release approval and monitoring records in [Release & Monitoring](11-release-monitoring-answer.md).

| Template | Purpose | Use when |
| --- | --- | --- |
| Required Test Matrix | Defines the scenarios that must be tested. | Planning coverage before implementation or release. |
| Evidence Template | Connects one test case across application, Data Layer, GTM, Network, consent, DebugView, and reports. | Recording proof for a Pass/Fail result. |
| Debug Session Record | Captures the test context, expected behavior, layers checked, and overall conclusion. | Running a focused investigation or release smoke test. |
| Defect and Retest Record | Records the first failing layer, impact, containment, fix, and retest result. | When a test fails or production behavior is suspect. |

### Debug session record template

**Purpose:** Use one record for one focused debug session. It prevents evidence from losing the property, version, consent state, or expected request count that gives the result meaning.

```text
Debug session ID:
Test case/journey ID:
Business question or release:
Environment and URL:
Application/build version:
GTM container/workspace/version:
GA4 property/stream/Measurement ID:
Browser/device:
Consent state:
Expected business moment:
Expected Data Layer event and count:
Expected tag/request and destination:
Layers checked:
Observed result:
First failing layer:
Evidence links:
Tester/date:
Reviewer/status:
Follow-up defect or decision:
```

### Defect and retest record template

**Purpose:** Use this record to turn a failed test into an actionable defect. It keeps the original evidence and affected period attached to the fix instead of relying on an informal message.

| Field | What to record |
| --- | --- |
| Defect ID and severity | Stable ID and Critical/High/Medium/Low classification. |
| First failing layer | Application, Data Layer, GTM, Network, consent, GA4 setup, or reporting. |
| Expected versus actual | The contract expectation and the observed behavior. |
| Reproduction | Test ID, URL, browser/device, consent state, steps, and frequency. |
| Impact and affected period | Events/users/reports/environments affected and first/last known time. |
| Evidence | Sanitized Preview, Network, DebugView, report, or application evidence. |
| Containment | Temporary block, routing correction, filter decision, or monitoring action. |
| Root cause and fix | Confirmed cause, change/ticket, owner, and target version. |
| Retest result | Test ID, date, evidence, residual impact, and reviewer decision. |

Do not close a defect only because the tag now appears under **Tags Fired**. Close it after the relevant downstream evidence and affected-period assessment are recorded.

## Defect Severity

| Severity | Definition | Examples |
| --- | --- | --- |
| Critical | Privacy/security breach or widespread production corruption | PII sent; production traffic routed to a test/unauthorized destination |
| High | Key business outcome missing, duplicated, or materially wrong | Purchases double-counted; sign-ups missing at scale |
| Medium | Important context wrong or a material scenario/browser affected | Wrong method; SPA route misses an event |
| Low | Documentation or maintainability issue with low current data impact | Missing description; inconsistent naming |

Severity should consider impact, affected volume, duration, privacy risk, detectability, and recoverability. A low-volume PII leak is still high/critical because privacy risk is not determined only by event volume.

## Pre-Publish Checklist

- [ ] Test uses the intended environment, property, stream, and GTM workspace/version.
- [ ] Measurement plan and Data Layer contract are approved.
- [ ] Positive, negative, duplicate, boundary, consent, routing, and privacy cases pass.
- [ ] Every expected event has the correct name, timing, values, types, and count.
- [ ] Every prohibited event/value is absent.
- [ ] Network requests use the intended destination and do not expose PII/secrets.
- [ ] Consent defaults, updates, and blocked behavior are evidenced.
- [ ] No overlapping tag or enhanced-measurement path creates duplicates.
- [ ] Workspace contains only intended changes or unrelated changes are explicitly recorded.
- [ ] Version name, description, owner, reviewer, rollback version, and release window are ready.

## Post-Publish Checklist

- [ ] Confirm the published container version and environment.
- [ ] Run an approved production smoke test with safe data.
- [ ] Check the actual destination and request count.
- [ ] Check Realtime/DebugView as appropriate without treating them as final historical reporting.
- [ ] Validate processed reports after the expected processing window.
- [ ] Compare event volume, missingness, duplicates, parameter quality, and key events with baseline.
- [ ] Record release time, timezone, evidence, result, and follow-up defects.
- [ ] Decide whether rollback, remediation, or continued monitoring is required.

## Definition of Done

- [ ] The first failing layer can be identified for missing and duplicated events.
- [ ] The test matrix includes positive, negative, duplicate, boundary, SPA, consent, routing, and privacy cases.
- [ ] Evidence connects the same test case across Data Layer, GTM, network, DebugView, and reporting.
- [ ] The correct property, stream, environment, and version are captured.
- [ ] Consent and privacy behavior are tested rather than assumed.
- [ ] DebugView and Realtime are explicitly distinguished from processed reports.
- [ ] Defects have severity, owner, evidence, and retest status.
- [ ] A reviewer signs off or documents an accepted exception.

## Official References

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Troubleshoot tag setup on your website](https://support.google.com/analytics/answer/9311124)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
