# 04 — Debugging Workflow and Analytics QA

## Theory

Analytics is a pipeline. Test each boundary separately:

```text
Interaction → application state → Data Layer → GTM evaluation
→ GA4 network request → GA4 processing/DebugView → reporting
```

A failure downstream does not identify the cause. For example, a missing DebugView event may originate in the application, Data Layer, trigger, consent state, request blocking, wrong measurement ID, or GA4 processing.

### Four validation questions

1. **Occurrence:** Did the event happen exactly when intended?
2. **Payload:** Are names, values, and types correct?
3. **Routing:** Did the intended tag send to the intended stream/property?
4. **Governance:** Were privacy, consent, naming, and evidence rules satisfied?

## End-to-End Debugging Steps

### 1. Prepare

- Record environment, URL, browser, test account/data, GTM workspace/version, GA4 measurement ID, consent state, and expected result.
- Use non-production identifiers and safe synthetic inputs.
- Open browser developer tools and preserve the network log when navigation is involved.

### 2. Validate the Data Layer

- Connect GTM Preview/Tag Assistant.
- Perform exactly one test action.
- Select the relevant event and inspect the Data Layer message.
- Confirm event name, timing, values, types, schema version, and one occurrence.
- Confirm negative actions do not produce success events.

### 3. Validate GTM

- Confirm the expected custom event appears in the event timeline.
- Check variables at that event, not at an unrelated event.
- Verify which tags fired and did not fire, including trigger conditions and blocking/consent reasons.
- Confirm the Google tag and GA4 event tag use the intended measurement ID/configuration.
- Look for two tags sending the same event.

### 4. Validate the network request

- Filter the Network panel for GA4 collection requests (commonly `collect`).
- Inspect the request payload/query parameters.
- Confirm event name, measurement ID, required event parameters, values, and request count.
- Search URLs, titles, and parameters for email, phone, name, tokens, or raw form content.
- Account for blockers, browser privacy features, Content Security Policy, and network errors.

### 5. Validate GA4 DebugView

- Preview mode generally enables a debug signal; alternatively configure `debug_mode` for the test device/event as appropriate.
- Select the correct GA4 property and debug device.
- Confirm the event appears once with expected parameters.
- Treat DebugView as validation of collection, not proof that standard reports are immediately populated.

### 6. Record and retest

- Capture evidence at each layer with timestamps and IDs.
- Log actual versus expected behavior and severity.
- Retest the fix plus adjacent regression cases.
- Obtain independent reviewer sign-off.

## Example Flow — Diagnose a Missing Event

```text
Expected `sign_up` is absent from DebugView
→ reproduce with a known test case
→ confirm whether `dataLayer.push()` occurred once
→ inspect GTM event variables and trigger conditions
→ inspect tag consent and firing status
→ inspect the GA4 network request and destination ID
→ verify DebugView device/property
→ record the first failing layer, fix it, and rerun positive and negative tests
```

For example, if the Data Layer event exists but the custom-event trigger uses `signup`, the first failing layer is GTM trigger evaluation—not GA4 processing.

## Required Test Matrix

| ID | Case | Action | Expected |
| --- | --- | --- | --- |
| TC-01 | Happy path | Complete valid sign-up | One `sign_up`; correct parameters |
| TC-02 | Validation failure | Submit invalid form | No `sign_up`; controlled error event only if planned |
| TC-03 | Double click | Click Submit rapidly | One confirmed success event |
| TC-04 | Server failure | Simulate failed response | No success event |
| TC-05 | Refresh/back | Navigate after success | No repeated success event |
| TC-06 | SPA revisit | Leave and return to route | Page events follow design; no duplicate business event |
| TC-07 | Consent denied | Deny relevant storage | Behavior matches approved basic/advanced consent design |
| TC-08 | Consent granted | Grant relevant storage | Full expected behavior after timely update |
| TC-09 | Data boundary | Missing/unknown optional value | Documented omit/fallback; no misleading value |
| TC-10 | Routing | Run in QA hostname | Request goes only to QA measurement destination |

## Evidence Template

| Test ID | Layer | Expected | Actual | Evidence link | Result | Defect |
| --- | --- | --- | --- | --- | --- | --- |
| TC-01 | Data Layer | One valid `sign_up` | `[actual]` | `[screenshot/log]` | Pass/Fail | `[ID]` |
| TC-01 | GTM | Correct tag fires once | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | Network | Correct GA4 payload | `[actual]` | `[HAR/screenshot]` | Pass/Fail | `[ID]` |
| TC-01 | DebugView | Event visible once | `[actual]` | `[screenshot]` | Pass/Fail | `[ID]` |

Do not attach evidence containing PII or live credentials. Redact safely if needed.

## Severity Guide

| Severity | Definition | Examples |
| --- | --- | --- |
| Critical | Privacy/security breach or widespread corrupted production data | PII sent; QA routed to production at scale |
| High | Key business outcome missing, duplicated, or materially wrong | Purchases/sign-ups double counted |
| Medium | Important context wrong or one scenario/browser affected | Incorrect method; SPA route missed |
| Low | Maintainability or documentation issue with little current data impact | Poor description; inconsistent folder |

## Pre-publish Checklist

- [ ] Tracking plan, Data Layer contract, and GTM inventory are approved.
- [ ] Workspace contains only intended changes or unrelated changes are identified.
- [ ] All required test cases pass with evidence.
- [ ] No PII or secret is present in Data Layer or requests.
- [ ] Consent defaults and updates occur in the approved order.
- [ ] No duplicate request/tag is observed.
- [ ] Correct environment and measurement ID are confirmed.
- [ ] Version name, description, owner, rollback version, and reviewer are ready.

## Post-publish Checklist

- [ ] Smoke-test the production journey with approved safe test data.
- [ ] Confirm correct container version and GA4 destination.
- [ ] Check Realtime/DebugView as appropriate, then later validate normal reporting/export.
- [ ] Monitor event counts, parameter null rates, duplicates, and anomalies against baseline.
- [ ] Record release time, evidence, outcome, and rollback decision window.
- [ ] Close or create follow-up defects.

## Official References

- [Preview and debug GTM containers](https://support.google.com/tagmanager/answer/6107056)
- [GA4 DebugView](https://support.google.com/analytics/answer/7201382)
- [Tag Assistant](https://support.google.com/tagmanager/answer/13355721)
- [Consent Mode implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending PII](https://support.google.com/analytics/answer/6366371)
