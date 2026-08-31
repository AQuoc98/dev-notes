# 08 — Debugging Workflow and Analytics QA

## 1. Overview

### Purpose

Use this section to verify a tracking change from the product outcome to the collected and processed GA4 data. The goal is to find the first failing layer, not merely to confirm that a GTM tag appears under **Tags Fired**.

The validation path is:

```text
Authoritative application state
  → Data Layer message
  → GTM trigger and variable evaluation
  → consent decision
  → GA4 network request
  → DebugView/Realtime diagnostic
  → processed report
```

Mark a material event **Pass** only when the business outcome occurred, the event was sent the expected number of times with the approved payload, the request reached the correct destination under the approved consent state, and the relevant downstream checks succeeded. If a downstream report is still within GA4's documented processing window, mark that check **Pending**, record the follow-up owner and date, and do not treat it as complete yet.

### Scope

This workflow covers stable web GTM and GA4 operations:

- application-to-Data-Layer contracts;
- variables, triggers, tags, Google tag routing, and collection-source ownership;
- GTM Preview/Tag Assistant, browser Network tools, GA4 DebugView/Realtime, and processed reports;
- positive, negative, duplicate, consent, privacy, routing, SPA/navigation, browser, and regression checks;
- evidence, defect, retest, and handoff records.

Media buying, campaign optimization, attribution strategy, and Google Ads operations are outside this section. Release approval and post-release monitoring remain in [Release & Monitoring](10-release-monitoring-answer.md).

### Test levels and stopping rule

Start at L0 and stop at the lowest level that answers the risk. A small documentation-only change may need only a focused contract check and test run; a new consent, ecommerce, or key business event needs the full sequence.

| Level | Prove | Use when | Exit evidence |
| --- | --- | --- | --- |
| L0 — Contract review | Event, parameters, count, source, destination, consent, and negative cases are defined | Before opening the debugger | Measurement Plan/Event Contract and preconditions are explicit |
| L1 — Isolated event | One controlled action produces the intended Data Layer signal and request | Every new or changed event | Expected event and request count are observed |
| L2 — Journey flow | Event order and business-state transitions are correct | Multi-event flows | Planned sequence has no missing or duplicate business event |
| L3 — Boundary/privacy | Invalid input, failure, retry, refresh, consent, PII, routing, and browser behavior | Material release or high-risk change | Known boundary cases match the approved contract |
| L4 — Regression | Adjacent events, shared tags, and destinations remain correct | Shared GTM/Google tag/consent/application changes | Baseline events remain correct |
| L5 — Processed data | Field availability, scope, count, filter, and interpretation | When a report or decision depends on the result | Processed result reconciles or discrepancy is recorded |
| L6 — Production observation | Published version and early impact | After production activation | Release version, smoke evidence, window, and owner are recorded |

### What each tool proves

| Tool/layer | Inspect | It proves | It does not prove |
| --- | --- | --- | --- |
| Application | Authoritative state and event call | The product exposed the intended signal | GTM received or routed it |
| Data Layer | Name, values, types, order, and count | GTM has a message to evaluate | A tag fired or a request succeeded |
| GTM Preview/Tag Assistant | Timeline, variables, fired/not-fired tags, consent, order | The previewed container evaluated the draft | Production uses that version or GA4 processed it |
| Browser Network | Request URL, Measurement ID, event, parameters, count, status | The browser attempted the intended collection request | GA4 populated every report |
| GA4 DebugView/Realtime | Device, event, parameters, timing, recent activity | GA4 received a diagnostic/recent signal | Historical processing or final attribution |
| Standard report/Exploration | Processed fields, scope, filters, freshness | Data is usable for the defined analysis | Upstream implementation was correct without upstream evidence |

See [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056), [Tag Assistant](https://support.google.com/tagmanager/answer/13355721), and [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382).

## 2. Core QA records

Use a small set of records. A QA report is a package of these records, not another independent template.

| Record | Priority | Apply when | Output |
| --- | --- | --- | --- |
| Test Run Setup Record | P0 — every run | Before testing | Environment, versions, consent, safe data, reset state, tester |
| Required Test Matrix | P0 — every behavior change | Before execution | Scenarios and expected outcomes |
| Evidence Template | P1 — material event or changed boundary | During/after execution | Layer-by-layer proof tied to a scenario ID |
| Debug Session Record | P2 — conditional | First failing layer is unclear or behavior is intermittent | Preserved state, investigation path, conclusion |
| Defect and Retest Record | P2 — conditional | A case fails, creates production risk, or needs retest | Defect, containment, fix, retest evidence |

Priority means: **P0** is mandatory, **P1** is required for material/high-impact changes, and **P2** is conditional.

### 2.1 Test Run Setup Record

Complete this reusable record once per test run before opening GTM Preview:

| Field | Value |
| --- | --- |
| Test run ID | `[run ID]` |
| Environment and URL | `[QA/staging URL]` |
| Application/build | `[commit/build]` |
| GTM container/workspace/version | `[container]` / `[workspace]` / `[version]` |
| GA4 property/stream/Measurement ID | `[property]` / `[stream]` / `[sanitized ID]` |
| Browser/device and date | `[browser/device/date]` |
| Test account/data | Synthetic account and safe values only |
| Consent state | `[granted/denied/unresolved by category]` |
| Tester/reviewer | `[name]` |
| Reset method | `[controlled profile, stored state, Preserve log, etc.]` |

Never put real names, email addresses, phone numbers, addresses, tokens, payment data, or free-form user content in test data or evidence.

Reset only the state required by the scenario. Record whether cookies/storage, consent, application journey state, Preview session, Network log, or browser extensions were kept or reset. Do not clear everything by default; that can destroy returning-user, retry, refresh, or multi-tab behavior under test.

### 2.2 Required Test Matrix

Use stable test IDs. The selected scenario can have a detailed L0 expectation block in the workflow, but that block is part of this matrix, not a separate template.

| ID | Case | Action | Expected |
| --- | --- | --- | --- |
| TC-01 | Happy path | Complete the valid flow | One canonical event with valid required parameters |
| TC-02 | Validation failure | Submit invalid input | No success event |
| TC-03 | Server failure | Force a failed response | No success event |
| TC-04 | Duplicate/retry | Submit or retry rapidly | One event per business occurrence |
| TC-05 | Refresh/back | Refresh or revisit the result | No unintended duplicate |
| TC-06 | SPA navigation | Enter, leave, and revisit the route | Planned route events; no duplicate business event |
| TC-07 | Missing optional | Omit an optional value | Omit or fallback exactly as documented |
| TC-08 | Missing required | Remove a required value | QA fails; no misleading success payload |
| TC-09 | Consent denied | Deny relevant consent | Approved consent behavior; no prohibited request |
| TC-10 | Consent granted | Grant relevant consent | Expected collection begins/updates |
| TC-11 | Routing | Run on the QA hostname | Request goes only to QA destination |
| TC-12 | Privacy | Inspect Data Layer and request | No PII, secret, raw form value, or unsafe URL |
| TC-13 | Browser | Test a supported browser/device | No material implementation difference |
| TC-14 | Regression | Run an adjacent journey | Existing events remain correct and non-duplicated |
| TC-15 | Collection source | Check all known collection paths | One canonical source or documented deduplication |

### 2.3 Evidence Template

Use one row per layer for a selected test case. `Pending` is valid only when runtime collection is complete and the normal GA4 processing window is not finished; record the follow-up owner and date.

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
| --- | --- | --- | --- | --- | --- | --- |
| `[ID]` | Application | Confirmed business state | `[actual]` | `[application log/link]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | Data Layer | One self-contained event with approved values | `[actual]` | `[capture/log]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | GTM | Intended trigger/tag evaluates once | `[actual]` | `[Tag Assistant]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | Collection source | One canonical source or documented deduplication | `[actual]` | `[source map/timeline]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | Network | Expected request count and destination | `[actual]` | `[redacted request]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | Consent | Approved behavior for the tested state | `[actual]` | `[consent evidence]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | DebugView/Realtime | Expected diagnostic/recent activity | `[actual]` | `[capture]` | Pass/Fail/Pending | `[ID/—]` |
| `[ID]` | Report | Processed result reconciles with the test | `[actual]` | `[report/follow-up]` | Pass/Fail/Pending | `[ID/—]` |

Evidence must include date, environment, version, property/stream, tester, browser, result, and known limitations. Redact identifiers and sensitive values.

### 2.4 Conditional records

#### Debug Session Record

Create this only after preserving the failing state or when behavior is intermittent:

```text
Debug session ID:
Test case/journey ID:
Business question or release:
Environment and URL:
Application/build and GTM version:
GA4 property/stream/Measurement ID:
Browser/device and consent state:
Expected business moment and event/count:
Canonical source and duplicate sources checked:
Layers checked:
Observed result:
First failing layer:
Evidence links:
Tester/date and reviewer/status:
Follow-up defect or decision:
```

#### Defect and Retest Record

Use this when a test fails or production risk requires tracking:

| Field | What to record |
| --- | --- |
| Defect and severity | Stable ID and Critical/High/Medium/Low classification |
| First failing layer | Application, Data Layer, GTM, consent, browser/network, GA4 setup, or reporting |
| Expected versus actual | Contract expectation and observed behavior |
| Reproduction | Test ID, URL, browser/device, consent state, steps, frequency |
| Impact and affected period | Events/users/reports/environments and known time range |
| Evidence | Sanitized application, Preview, Network, DebugView, or report evidence |
| Containment | Block, routing correction, pause, filter, or monitoring action |
| Root cause and fix | Confirmed cause, change/ticket, owner, target version |
| Retest result | Test ID, date, evidence, residual impact, reviewer decision |

## 3. Template application order and debugging procedure

### 3.1 Order and priority

1. **Set up the run (P0):** complete the Test Run Setup Record and control the browser/application state.
2. **Define coverage (P0):** create or update the Required Test Matrix from the Measurement Plan.
3. **Define the selected expectation (L0):** record business moment, event/count, required parameters, destination, consent, and negative cases for the scenario being tested. This is matrix detail, not a new template.
4. **Execute and summarize (P0):** run one scenario at a time and update the Scenario Execution Summary.
5. **Capture targeted proof (P1):** complete Evidence Template rows for every material or changed boundary.
6. **Escalate only when needed (P2):** create a Debug Session Record for unexplained behavior and a Defect and Retest Record for a tracked failure or retest.

Shortcuts:

- A normal material GTM/GA4 change uses Steps 1–5.
- A documentation-only change uses Steps 1–2 and evidence for the affected boundary.
- A happy path with no mismatch does not need the Debug Session or Defect records.
- Consent, routing, key-event, ecommerce, or shared-tag changes require full positive, negative, duplicate, and regression coverage.
- After a fix, rerun the same scenario ID with a new attempt timestamp and close the defect only after the relevant regression evidence passes.

### 3.2 Frontend checks before GTM Preview

GTM Preview verifies the integration; it does not replace application tests. Test the earliest layer that owns each failure:

| Test level | Prove | Typical approach |
| --- | --- | --- |
| Unit | Analytics adapter accepts only the approved event contract | TypeScript/project test runner |
| Service/integration | Confirmed API result emits once; validation/failure/cancel paths do not emit success | Mocked API or integration test |
| Component/SPA | Strict Mode, remount, route transition, double click, and retry do not duplicate | Component/browser test |
| Browser contract | The real page pushes the expected Data Layer message before GTM evaluates it | E2E test with Data Layer capture |

Assert the final Data Layer message, occurrence count, value types, and absence of prohibited fields. Do not assert only that an adapter function was called.

### 3.3 End-to-end debug steps

#### Step 1 — Confirm the expected behavior (L0 Contract Review)

Read the Measurement Plan before opening the debugger and fill this scenario-level expectation block:

```text
Action:
Business moment:
Expected Data Layer event:
Expected request count:
Required parameters:
Expected destination:
Expected consent:
Negative cases:
```

The block supplies the `Expected` values for the Evidence Template. Do not debug an implementation against an undefined expectation.

#### Step 2 — Start the correct preview session

1. Open the intended GTM container, workspace, environment, and QA URL.
2. Start Preview and confirm the connected container, version, and hostname.
3. Preserve the Network log when navigation or redirects are involved.
4. Confirm the controlled browser state and consent state recorded in the setup record.

#### Step 3 — Reproduce one action

Perform exactly one action, then stop. Confirm that the application reached the authoritative business state and that the Data Layer signal occurred once at the correct moment. Check event name, parameter names, types, values, optional-field behavior, and prohibited data.

If the business outcome did not happen, do not treat a button click as a success event.

#### Step 4 — Inspect GTM evaluation

At the exact event in Tag Assistant:

1. Match the Custom Event name exactly, including case.
2. Inspect every variable used by the trigger and tag.
3. Confirm the intended trigger matched and the tag fired once.
4. Check not-fired tags, blocking triggers, exceptions, consent requirements, sequencing, and tag settings.
5. Search for another tag or implementation that could send the same event.
6. Confirm the Google tag and event tag point to the intended destination.

One business occurrence should have one canonical collection source. If multiple sources are intentional, document ownership, deduplication, and expected request count.

#### Step 5 — Inspect the Network request

Filter Developer Tools for the GA4 collection request and verify:

- request count equals the contract;
- Measurement ID and destination are the QA/test values;
- event name, required parameters, types, and values are correct;
- no extra, prohibited, or sensitive fields are sent;
- consent-related signals match the approved design;
- blockers, browser privacy settings, CSP, redirects, or network errors did not alter the result.

Redact identifiers and payload values before sharing evidence.

#### Step 6 — Check GA4 DebugView and Realtime

Select the correct property and debug device. Confirm the event and required parameters appear with the expected count. Treat DebugView/Realtime as collection diagnostics, not proof of complete historical reporting. Client-side privacy controls or consent may prevent visibility.

Remove or scope any test-only debug setting after testing. Do not leave an all-user `debug_mode` configuration active in production.

#### Step 7 — Validate processed reporting data

After the documented processing window:

- select the correct property, stream, timezone, and date range;
- confirm the event and registered custom definitions are available;
- check metric/dimension scope, filters, `(other)`, thresholding, sampling, and incomplete recent dates;
- reconcile processed counts with test evidence;
- record a discrepancy instead of silently changing the conclusion.

### 3.4 Consent debugging

Treat consent as a runtime dimension. Add cases for default state, analytics denied, analytics granted, consent update after load, SPA navigation after each state, returning-user stored consent, and unresolved/failed CMP state. For each case record:

- default and update timing;
- tags expected to fire or remain blocked;
- whether a request was sent and which signals it carried;
- whether behavior matches the approved privacy design;
- whether DebugView visibility is expected.

Do not bypass the approved consent model with ad hoc triggers. Inspect consent requirements and additional consent checks in Tag Assistant. See [unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079).

### 3.5 Failure diagnosis

Diagnose the first failing layer:

| Symptom | First check | Likely layer | Capture |
| --- | --- | --- | --- |
| No Data Layer event | Business state, callback, event push | Application/Data Layer | Application log and test state |
| Data Layer event exists, tag does not fire | Exact event name, filters, variables, exceptions | GTM | Tag Assistant event and not-fired reason |
| Tag fires, no request | Google tag/config, consent, blocker, tag error | GTM/browser | Tag detail and Network log |
| Wrong Measurement ID | Environment lookup, Google tag, stream selection | Routing | Redacted request |
| Wrong parameter | Data Layer path, timing, type conversion | Contract/GTM | Data Layer/request comparison |
| Two requests for one action | Duplicate push, overlapping tags, remount, retry | Application/GTM | Timeline and request count |
| Request exists, DebugView is empty | Property/device, debug mode, consent/privacy, delay | GA4/debug setup | Property, device, consent, timestamp |
| DebugView is correct, report is wrong | Processing, definition delay, filters, scope, thresholding | GA4 reporting | Report settings and date range |

## 4. Practical guardrails and handoff

- **First failing layer is the diagnosis anchor.** Fixing a later symptom can hide the actual cause.
- **Freeze before changing.** Preserve the original state and evidence; do not refresh or change several settings at once.
- **Keep ownership explicit.** A second hardcoded `gtag`, CMS/plugin, Enhanced Measurement path, server-side path, Measurement Protocol call, or GTM tag is a duplicate source unless documented and deduplicated.
- **Keep evidence safe.** Use synthetic data, redact IDs and payload values, and never store credentials.
- **Keep results scoped.** A request in Network proves browser collection was attempted; DebugView proves a debuggable event was received; a processed report proves reporting availability after processing. Do not collapse these conclusions.
- **Handoff minimally.** A frontend handoff needs event name, business moment, payload schema, occurrence rule, source, consent assumptions, and test IDs. GTM owners need the variable/trigger/tag mapping and destination. QA owners need the expected matrix and evidence links.
- **Release boundary.** Use [Release & Monitoring](10-release-monitoring-answer.md) for approval, production activation, rollback ownership, and post-release observation.

## 5. Worked example — Registration Journey

This is the only worked example in the document. It is a non-production illustration; replace all sample IDs, values, and evidence placeholders with project data.

### 5.1 Filled L0 expectation block

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

### 5.2 Test run context

| Field | Recorded value |
| --- | --- |
| QA report ID | `QA-REG-001` |
| Measurement Plan | `MP-REG-001 / v1.0` |
| Journey | `J-REG-001` — Registration |
| Environment | QA/staging only |
| Application/GTM/GA4 | `[build]` / `[GTM version]` / `[QA property and stream]` |
| Browser and data | Controlled profile; synthetic account; safe values |
| Consent state | Analytics granted; advertising consent not needed for this test |
| Canonical source | Backend-confirmed account creation → application Data Layer → GTM → GA4 |
| Duplicate sources checked | No hardcoded `gtag`, plugin, second GTM tag, server-side path, or Measurement Protocol source for this event |
| Expected result | One `sign_up` with `method=email` and `form_id=registration` |

### 5.3 Scenario execution summary

| Test ID | Scenario | Observed result | Evidence | Status |
| --- | --- | --- | --- | --- |
| `TC-REG-001` | Form ready | One `registration_start`; no PII | `[application + Data Layer]` | Pass |
| `TC-REG-002` | Invalid input | Validation error; no `sign_up` | `[Preview + request]` | Pass |
| `TC-REG-003` | Server failure | Server error; no `sign_up` | `[application + request]` | Pass |
| `TC-REG-004` | Confirmed account creation | Backend success; one approved `sign_up` | `[application + Data Layer + Preview]` | Pass |
| `TC-REG-005` | Rapid double submit/retry | One confirmed account; one request | `[timeline + Network]` | Pass |
| `TC-REG-006` | Refresh/back/SPA remount | No duplicate `sign_up` | `[navigation timeline]` | Pass |
| `TC-REG-007` | Consent denied | Approved denied-state behavior; no prohibited data | `[consent + storage + Network]` | Pass |
| `TC-REG-008` | Wrong environment/destination | QA Measurement ID only | `[redacted request]` | Pass |
| `TC-REG-009` | User-ID out of scope | No email, phone, raw account ID, or unapproved User-ID | `[redacted payload]` | Pass |
| `TC-REG-010` | Collection-source ownership | Only canonical GTM path sent `sign_up` | `[source map + timeline]` | Pass |

### 5.4 Detailed Evidence Template — `TC-REG-004`

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
| --- | --- | --- | --- | --- | --- | --- |
| `TC-REG-004` | Application | Backend confirms account creation | `[success response]` | `[application log]` | Pass | `—` |
| `TC-REG-004` | Data Layer | One `sign_up` with approved parameters | `[payload]` | `[Data Layer capture]` | Pass | `—` |
| `TC-REG-004` | GTM | Intended trigger/tag fires once | `[timeline]` | `[Tag Assistant]` | Pass | `—` |
| `TC-REG-004` | Collection source | One canonical source | `[source map]` | `[source timeline]` | Pass | `—` |
| `TC-REG-004` | Network | One request to QA Measurement ID | `[count/payload]` | `[redacted request]` | Pass | `—` |
| `TC-REG-004` | Consent | Approved analytics behavior | `[state/signals]` | `[consent evidence]` | Pass | `—` |
| `TC-REG-004` | DebugView | One debuggable event with required parameters | `[event]` | `[DebugView capture]` | Pass | `—` |
| `TC-REG-004` | Report | Processed result reconciles with test | `[not yet available]` | `[follow-up record]` | Pending | `—` |

### 5.5 Frontend contract example

```typescript
window.dataLayer = [];

await completeConfirmedRegistration();

expect(window.dataLayer).toEqual([
  expect.objectContaining({
    event: "sign_up",
    event_schema_version: "1.0",
    method: "email",
    form_id: "registration",
  }),
]);
```

The application tests should also cover API failure, duplicate callback, invalid required values, and SPA remount. The expected event must be emitted only after the authoritative account-creation result.

### 5.6 Conclusion

Runtime collection QA passed: the backend confirmed account creation, the application emitted one self-contained `sign_up`, GTM selected the intended tag once, consent matched the approved design, and one request went to the QA Measurement ID. Processed reporting remains a follow-up until the documented GA4 processing window has completed.

## Official references

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Monitor events in DebugView](https://support.google.com/analytics/answer/7201382)
- [Troubleshoot tag setup on your website](https://support.google.com/analytics/answer/9311124)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
