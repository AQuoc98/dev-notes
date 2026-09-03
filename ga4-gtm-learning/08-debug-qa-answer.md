# 08 — Debugging Workflow and Analytics QA

## 1. Overview

### 1.1 Objective

Verify an approved GA4/GTM change from the authoritative application outcome to the browser request and the relevant GA4 diagnostic or processed result. The goal is to find the first failing layer and record only the evidence needed for the risk.

### 1.2 Scope

- Stable web collection with GTM and GA4.
- Application/Data Layer, Variables, Triggers, Tags, Google tag routing, consent, and collection-source ownership.
- GTM Preview/Tag Assistant, browser Network tools, GA4 DebugView/Realtime, and processed-data checks when a business decision depends on them.
- Positive, negative, duplicate, consent, privacy, routing, SPA/navigation, browser, and regression cases.
- Test setup, data-safety, scenario, evidence, defect, retest, and handoff records.

### 1.3 Out of scope

- Measurement design or event meaning: see Section 07.
- Detailed Data Layer, Variable, Trigger, Tag, Consent, or Template configuration: see Sections 01–06.
- Report or chart design: see Section 09.
- Production release approval and post-release observation: see Section 10.
- Ads, campaign optimization, attribution strategy, and Google Ads operations.

### 1.4 Validation path

~~~text
Authoritative application state
→ Data Layer message
→ GTM Trigger and Variable evaluation
→ consent decision
→ GA4 Network request
→ DebugView/Realtime diagnostic
→ processed result when required
~~~

### 1.5 Material-event pass rule

A material event is **Pass** only when all applicable checks are evidenced:

1. The authoritative business state occurred.
2. The event and request counts match the approved contract.
3. The payload contains the approved fields, types, and values.
4. The request used the intended environment and destination.
5. Consent behavior matched the approved state.
6. The relevant downstream result was observed.

Mark only the downstream check **Pending** when collection is complete but the normal GA4 processing window has not finished. Record the owner, follow-up date, property, stream, and expected check. Pending is not a completed pass.

### 1.6 What each layer proves

| Layer/tool | Use it to prove | It does not prove |
|---|---|---|
| Application | The business state and outcome were actually reached. | GTM received or sent the event. |
| Data Layer | The expected message, values, types, order, and count were published. | A Tag fired or a request succeeded. |
| GTM Preview / Tag Assistant | The previewed container evaluated the event, consent, Variables, Triggers, and Tags as expected. | Production uses that draft or GA4 processed the event. |
| Browser Network | The browser attempted a request with the expected destination, event, parameters, and count. | The event is available in every GA4 report. |
| GA4 DebugView / Realtime | GA4 received a recent or diagnostic signal from the selected device. | Historical processing or final report completeness. |
| Processed Report / Exploration | The processed fields, scope, filters, and counts support the planned use. | Upstream implementation was correct without Application/GTM/Network evidence. |

Use Google's [GTM Preview and debug mode](https://support.google.com/tagmanager/answer/6107056), [Tag Assistant](https://support.google.com/tagmanager/answer/13355721), and [GA4 DebugView](https://support.google.com/analytics/answer/7201382) for the relevant layer.

## 2. QA records and application order

### 2.1 Record priority

QA is a package of records. Do not create a new template for every scenario.

| Priority | Record | Apply when | Output |
|---|---|---|---|
| P0 | Test Run Setup Record | Every test run | Environment, versions, browser, consent, reset state, tester. |
| P0 | Data Safety Check | Before collecting or exporting evidence | Synthetic data, redaction, safe destination, and cleanup confirmed. |
| P0 | Required Test Matrix | Every behavior or configuration change | Scenarios, actions, and expected outcomes. |
| P0 | Scenario Execution Summary | Every executed scenario in a material run | One concise result, evidence links, and follow-up status. |
| P1 | Evidence Template | Material events or changed boundaries | Layer-by-layer proof tied to a scenario ID. |
| P2 | Debug Session Record | First failing layer is unclear or behavior is intermittent | Preserved state, investigation path, and conclusion. |
| P2 | Defect and Retest Record | A failure, production risk, or retest must be tracked | Reproduction, containment, fix, retest, and residual impact. |

P0 is mandatory. P1 is required for a material or boundary-sensitive change. P2 is conditional.

### 2.2 Order of use

~~~text
Test Run Setup
→ Data Safety Check
→ Required Test Matrix
→ Scenario Execution Summary
→ Evidence rows for material boundaries
→ Debug Session or Defect/Retest only when needed
~~~

The selected scenario's expectation block belongs inside the Required Test Matrix. It is not another standalone template. If the requirement is rejected at contract review, stop and record the decision; do not create GTM assets.

### 2.3 Test Run Setup Record

Complete once for each run before opening Preview:

| Field | Value |
|---|---|
| Test run ID | [run ID] |
| Environment and URL | [QA/staging URL] |
| Application/build | [commit or build] |
| GTM container/workspace/version | [container] / [workspace] / [version] |
| GA4 property/stream/Measurement ID | [property] / [stream] / [sanitized ID] |
| Browser/device/date | [browser] / [device] / [date] |
| Test account/data | Synthetic account and safe values only |
| Consent state | [granted/denied/unresolved by category] |
| Reset method | [controlled profile, stored state, Preserve log] |
| Tester/reviewer | [name] |

Reset only the state required by the scenario. State whether cookies/storage, consent, application state, Preview, Network log, and extensions were kept or reset.

### 2.4 Data Safety Check

Complete before performing the action or sharing a session:

| Check | Expected |
|---|---|
| Environment | QA or staging hostname; no production destination. |
| Test data | Synthetic account, safe values, and no real customer data. |
| Payload review | No PII, credentials, payment data, unrestricted input, or secrets. |
| URL and logs | No sensitive query string, token, or account identifier. |
| Evidence handling | Screenshots, exports, and Network logs are redacted and access-controlled. |
| Consent | The test state and permitted categories are recorded. |
| Cleanup | Test-only debug settings, preview links, and temporary data have an owner for removal. |

If any check fails, stop the run, remove or redact the unsafe data, and record the containment action.

### 2.5 Required Test Matrix

Use stable test IDs and copy expected values from Section 07:

| ID | Case | Action | Expected outcome |
|---|---|---|---|
| TC-01 | Happy path | Complete the valid flow once. | One canonical event with all required parameters. |
| TC-02 | Validation failure | Submit invalid input. | No success event. |
| TC-03 | Server failure | Force or simulate a failed response. | No success event; failure classification is correct. |
| TC-04 | Valid no-output | Use a valid response that produces no output, when the contract defines it. | One agreed event with the no-output value. |
| TC-05 | Duplicate/retry | Double-submit or retry rapidly. | One event per business occurrence. |
| TC-06 | Refresh/back/remount | Refresh, go back, or remount the component. | No unintended duplicate. |
| TC-07 | Missing required | Remove a required field. | QA failure; no misleading success payload. |
| TC-08 | Missing optional | Omit an optional field. | Omit or use the documented fallback. |
| TC-09 | Consent denied/granted | Run both relevant consent states and an update after load. | Approved blocked, reduced, or collected behavior. |
| TC-10 | Routing/privacy | Use QA hostname and inspect payload. | QA destination only; no prohibited data. |
| TC-11 | Browser/SPA | Test supported browser, navigation, and route restoration. | No material difference or duplicate. |
| TC-12 | Regression/source ownership | Check adjacent events and all known collection paths. | Baseline remains correct; one canonical source or documented deduplication. |

Add ecommerce, key-event, User-ID, or shared-tag cases only when the change affects them.

### 2.6 Scenario Execution Summary

Use one row for each executed scenario. This is the run summary, not the detailed evidence:

| Test ID | Started | Action/result summary | Expected count | Actual count | Status | Evidence/defect/follow-up |
|---|---|---|---:|---:|---|---|
| [ID] | [timestamp] | [one sentence] | [n] | [n] | Pass/Fail/Pending | [IDs and owner/date] |

The summary must state whether the business state occurred, not only whether a Tag appeared under Tags Fired.

### 2.7 Evidence Template

Use one row per relevant layer for a selected scenario:

| Test ID | Layer | Expected | Actual | Evidence | Result | Defect |
|---|---|---|---|---|---|---|
| [ID] | Application | Authoritative state occurred | [actual] | [sanitized log] | Pass/Fail/Pending | [ID/—] |
| [ID] | Data Layer | One self-contained approved message | [actual] | [capture] | Pass/Fail/Pending | [ID/—] |
| [ID] | GTM | Intended Trigger/Tag evaluates once | [actual] | [Tag Assistant] | Pass/Fail/Pending | [ID/—] |
| [ID] | Network | Expected request count and destination | [actual] | [redacted request] | Pass/Fail/Pending | [ID/—] |
| [ID] | Consent | Approved behavior for this state | [actual] | [consent evidence] | Pass/Fail/Pending | [ID/—] |
| [ID] | DebugView/Realtime | Expected diagnostic/recent event | [actual] | [capture] | Pass/Fail/Pending | [ID/—] |
| [ID] | Processed data | Result available after processing, when required | [actual] | [report/follow-up] | Pass/Fail/Pending | [ID/—] |

Evidence should include run ID, date, environment, version, property/stream, browser, tester, result, and known limitations. Redact identifiers and sensitive values.

### 2.8 Conditional records

#### Debug Session Record

Create this only after preserving the failing or intermittent state:

~~~text
Debug session ID:
Test run/scenario ID:
Environment and URL:
Application/build and GTM version:
GA4 property/stream/Measurement ID:
Browser/device and consent state:
Expected business moment, event, and count:
Canonical and duplicate sources checked:
Layers checked:
Observed result and first failing layer:
Evidence links:
Tester/date, reviewer/status:
Follow-up defect or decision:
~~~

#### Defect and Retest Record

Use for a tracked failure or required retest:

| Field | Record |
|---|---|
| Defect/severity | Stable ID and Critical/High/Medium/Low. |
| First failing layer | Application, Data Layer, GTM, consent, Network, GA4 setup, or processed data. |
| Expected versus actual | Contract expectation and observed result. |
| Reproduction | Test ID, URL, browser/device, consent, steps, and frequency. |
| Impact/containment | Affected events, users, environments, period, and immediate action. |
| Root cause/fix | Confirmed cause, change or ticket, owner, and target version. |
| Retest | New attempt timestamp, evidence, residual impact, and reviewer decision. |

## 3. Practical execution

### 3.1 Before Preview

1. Open the approved Measurement Plan and identify the event, business moment, required parameters, count, destination, consent, and negative cases.
2. Complete Test Run Setup and Data Safety Check.
3. Select the smallest matrix that covers the change risk.
4. For a material event, write the scenario expectation block:

~~~text
Action:
Business moment:
Expected Data Layer event:
Expected request count:
Required parameters:
Expected destination:
Expected consent:
Negative cases:
~~~

5. Run frontend contract tests first when the Application owns the business result. GTM Preview cannot prove an API callback, deduplication guard, or component lifecycle rule.

### 3.2 Reproduce one scenario

Use one controlled action, then stop. Confirm that the Application reached the authoritative state and published the expected Data Layer message once. Do not treat a click, render, route change, or request start as business success unless the contract explicitly says so.

### 3.3 Inspect GTM Preview / Tag Assistant

At the exact event:

1. Match the Custom Event name, including case.
2. Inspect every Variable used by the Trigger and Tag.
3. Confirm the intended Trigger matched and the Tag fired once.
4. Check not-fired reasons, blocking Triggers, exceptions, consent requirements, sequencing, and Tag settings.
5. Search for another Tag, plugin, hardcoded gtag, server-side path, or Measurement Protocol call that could send the same event.
6. Confirm the Google tag and event Tag use the intended environment and Measurement ID.

Preview proves the selected draft evaluation, not production behavior or GA4 processing.

### 3.4 Inspect the Network request

Filter for the GA4 collection request and verify:

- request count equals the contract;
- Measurement ID and destination are the QA/test values;
- event name, required parameters, types, and values are correct;
- optional fields follow the contract;
- no extra, prohibited, or sensitive fields are sent;
- consent-related signals match the approved design;
- blockers, CSP, redirects, browser privacy, and network errors did not alter the result.

Preserve the request only after redacting identifiers and payload values.

### 3.5 Check DebugView / Realtime

Select the intended property and debug device. Confirm the event and required parameters appear with the expected count. DebugView and Realtime are diagnostic/recent-activity tools; they do not prove historical processing or report completeness. Consent and client-side privacy controls can prevent visibility.

Use Preview or Tag Assistant to enable debug mode for the tester's device. Remove test-only settings afterward; never leave an all-user debug configuration active in production.

### 3.6 Check processed data when required

Only perform this step when the business decision depends on processed GA4 data:

1. Wait for the documented data-freshness window.
2. Select the correct property, stream, timezone, date range, dimensions, metrics, and filters.
3. Confirm the event and any registered custom definitions are available.
4. Check scope, thresholding, sampling, cardinality, and incomplete recent dates.
5. Reconcile the result with the test evidence.
6. Record a discrepancy or Pending follow-up; do not silently change the expected result.

### 3.7 Consent cases

For consent-controlled changes, add cases for:

- default state before the banner choice;
- analytics denied;
- analytics granted;
- update after the user chooses;
- stored consent on a returning visit;
- SPA navigation after each state;
- unresolved or failed CMP state.

For each case record tag behavior, request presence and signals, storage behavior, destination, and expected DebugView visibility. Use the approved built-in consent behavior first; do not bypass it with an ad hoc Trigger.

## 4. Diagnosis, guardrails, and handoff

### 4.1 First-failing-layer diagnosis

| Symptom | Check first | Likely layer | Capture |
|---|---|---|---|
| No Data Layer event | Business state, callback, and application push | Application/Data Layer | App log and Data Layer capture |
| Data Layer event but no Tag | Exact event name, filters, Variables, exceptions | GTM | Tag Assistant timeline and not-fired reason |
| Tag fires but no request | Google tag/config, consent, blocker, Tag error | GTM/browser | Tag detail and Network |
| Wrong destination or Measurement ID | Environment lookup, stream, Google tag | Routing | Redacted request |
| Wrong parameter | Data Layer path, timing, type conversion | Contract/GTM | Data Layer versus request |
| Two requests for one action | Duplicate push, overlapping Tag, remount, retry, second source | Application/GTM | Timeline and request count |
| Request exists but DebugView is empty | Property/device, debug mode, consent, delay | GA4/debug setup | Property, device, consent, timestamp |
| DebugView is correct but processed result is wrong | Processing, definition delay, scope, filters, thresholding | GA4 processed data | Report settings and follow-up |

### 4.2 Operational guardrails

- Preserve the original state and evidence before changing configuration.
- Change one layer at a time; do not refresh and change several settings before capture.
- Keep one canonical collection source per business event, or document ownership and deduplication.
- Keep business logic in the Application; GTM transports approved values.
- Use synthetic data and redact exported Tag Assistant sessions, screenshots, and Network logs.
- Treat a Network request, DebugView event, and processed report as different levels of proof.
- Close a defect only after the same scenario and relevant regression cases pass.

### 4.3 Handoff

Frontend handoff includes event name, business moment, payload schema, occurrence/deduplication rule, source, consent assumptions, build, and test IDs. GTM handoff includes Variable/Trigger/Tag mapping, environment, destination, and expected count. QA handoff includes the matrix, scenario summaries, evidence links, Pending owner/date, and open defects.

Use [Release Monitoring](10-release-monitoring-answer.md) for production activation, rollback ownership, and post-release observation.

## 5. Worked example — Registration Journey

This example is non-production. Replace all IDs, values, dates, and evidence placeholders with project data. It demonstrates how the records connect; it does not create additional templates.

### 5.1 Test run context

| Field | Recorded value |
|---|---|
| Test run ID | QA-REG-RUN-001 |
| Measurement Plan | MP-REG-001 / v1.0 |
| Journey | J-REG-001 — Registration |
| Environment | QA/staging only |
| Application/GTM/GA4 | [build] / [GTM version] / [QA property and stream] |
| Browser/data | Controlled profile; synthetic account; safe values |
| Consent | Analytics granted; advertising consent not required for this test |
| Canonical source | Backend-confirmed account creation → Application Data Layer → GTM → GA4 |
| Duplicate sources checked | No hardcoded gtag, plugin, second GTM Tag, server-side path, or Measurement Protocol source |

### 5.2 Data Safety Check

| Check | Result |
|---|---|
| QA hostname and Measurement ID | Pass — [redacted QA ID] |
| Synthetic account and safe values | Pass |
| PII, credentials, tokens, and raw form content | Pass — none present |
| Evidence redaction and access | Pass |
| Test-only debug setting cleanup owner | [name/date] |

### 5.3 Selected scenario expectation

~~~text
Action: Complete a valid registration
Business moment: Server confirms account creation
Expected Data Layer event: one sign_up
Expected request count: one
Required parameters: method, form_id
Expected destination: QA stream only
Expected consent: approved analytics behavior
Negative cases: invalid input, server failure, double submit, refresh
~~~

### 5.4 Scenario Execution Summary

| Test ID | Scenario | Expected | Actual | Status | Evidence/follow-up |
|---|---|---|---|---|---|
| TC-REG-01 | Valid registration | One sign_up with method and form_id | Backend confirmed account; one message/request | Pass | EV-REG-01 |
| TC-REG-02 | Invalid input | No sign_up | Validation error; no sign_up request | Pass | EV-REG-02 |
| TC-REG-03 | Server failure | No sign_up | Server error; no sign_up request | Pass | EV-REG-03 |
| TC-REG-04 | Double submit/retry | One event per account | One account; one request | Pass | EV-REG-04 |
| TC-REG-05 | Refresh/back/remount | No duplicate sign_up | No duplicate | Pass | EV-REG-05 |
| TC-REG-06 | Consent denied | Approved denied behavior | No prohibited collection | Pass | EV-REG-06 |
| TC-REG-07 | QA routing/privacy | QA destination; no PII | QA Measurement ID; safe payload | Pass | EV-REG-07 |
| TC-REG-08 | Processed result | Available after freshness window | Not yet available | Pending | Owner [name], [date] |

### 5.5 Evidence for TC-REG-01

| Layer | Expected | Actual | Evidence | Result |
|---|---|---|---|---|
| Application | Server confirms account creation | [success response] | [sanitized app log] | Pass |
| Data Layer | One self-contained sign_up | [event and payload] | [Data Layer capture] | Pass |
| GTM | One authoritative Trigger/Tag evaluation | [timeline] | [Tag Assistant] | Pass |
| Network | One request to QA Measurement ID | [count and redacted payload] | [Network log] | Pass |
| Consent | Approved analytics behavior | [state/signals] | [consent capture] | Pass |
| DebugView/Realtime | One event with method and form_id | [event] | [DebugView capture] | Pass |
| Processed data | Result after processing window | [not yet available] | [follow-up] | Pending |

### 5.6 Conclusion

The runtime collection passes for the QA stream because the server confirmed account creation, the Application published one event, GTM evaluated one authoritative path, consent matched the approved state, and one request reached the intended destination. Processed-data validation remains Pending until its documented freshness window is complete.

## Official references

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [Tag Assistant troubleshooting](https://support.google.com/tagmanager/answer/10039345)
- [Share or export Tag Assistant sessions](https://support.google.com/tagmanager/answer/15212893)
- [Monitor events in GA4 DebugView](https://support.google.com/analytics/answer/7201382)
- [GA4 data freshness](https://support.google.com/analytics/answer/11198161)
- [Consent mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Unblock Google tags when using consent mode](https://support.google.com/tagmanager/answer/12962079)
- [Avoid sending personally identifiable information](https://support.google.com/analytics/answer/6366371)
