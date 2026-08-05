# 06 — Proof of Concept

## Theory

A proof of concept (POC) is a deliberately bounded experiment that tests whether the proposed Data Layer, naming, variable, consent, and QA standards work together. It is not a production rollout.

A useful POC begins with a falsifiable hypothesis and ends with a decision:

> If the application emits the approved contract at authoritative business moments, GTM can send accurate, privacy-safe GA4 events exactly once across the agreed test cases.

The outcome must be **adopt**, **revise**, or **reject**, supported by evidence.

## Proposed POC: Registration Journey

### Boundaries

- Non-production application and GTM workspace.
- QA GA4 web stream/property.
- Three related events: `sign_up_start`, `sign_up_error`, and recommended `sign_up`.
- No production publishing, enterprise schema, dashboard, or unrelated container refactor.

### Success criteria

- Each intended event fires exactly once at its defined moment.
- Invalid or failed submissions never send `sign_up`.
- Required parameter names, values, and types match at Data Layer, GTM, network, and DebugView.
- QA traffic reaches only the QA measurement destination.
- No email, name, phone, token, raw error, or other prohibited/sensitive value is sent.
- Relevant consent-granted and consent-denied behavior matches the approved consent design.
- All required tests pass and an independent reviewer can reproduce the result.

## Reference Data Layer Implementation

```javascript
window.dataLayer = window.dataLayer || [];

// Emit once when the user first meaningfully engages with registration.
window.dataLayer.push({
  event: 'sign_up_start',
  event_schema_version: '1.0',
  form_id: 'account_registration'
});

// Emit a controlled category, never a raw server/user-facing error message.
window.dataLayer.push({
  event: 'sign_up_error',
  event_schema_version: '1.0',
  form_id: 'account_registration',
  error_type: 'validation',
  error_stage: 'submit'
});

// Emit only after the server confirms that the account was created.
window.dataLayer.push({
  event: 'sign_up',
  event_schema_version: '1.0',
  form_id: 'account_registration',
  method: 'email'
});
```

Implementation must use application state to prevent duplicate start/success pushes. The exact mechanism depends on the framework and request lifecycle and must be code-reviewed.

## GTM Configuration Inventory

| Component | Suggested name | Configuration |
| --- | --- | --- |
| Variable | `DLV - form_id` | DLV v2, key `form_id` |
| Variable | `DLV - method` | DLV v2, key `method` |
| Variable | `DLV - error_type` | DLV v2, key `error_type` |
| Variable | `DLV - error_stage` | DLV v2, key `error_stage` |
| Variable | `LUT - Hostname to Measurement ID` | Exact QA/production mapping; safe default |
| Trigger | `CE - sign_up_start` | Custom Event equals `sign_up_start` |
| Trigger | `CE - sign_up_error` | Custom Event equals `sign_up_error` |
| Trigger | `CE - sign_up` | Custom Event equals `sign_up` |
| Tag | `GA4 Event - sign_up_start` | Event + approved parameters |
| Tag | `GA4 Event - sign_up_error` | Event + controlled error parameters |
| Tag | `GA4 Event - sign_up` | Recommended event + `method`; approved extra context |

Use the current native Google tag and GA4 event tag templates. Do not deploy gtag.js through Custom HTML.

## Steps to Complete the POC

### 1. Plan

- [ ] Confirm POC owner, reviewers, environment, workspace, GA4 stream, timeline, and exclusions.
- [ ] Approve the hypothesis, success criteria, tracking plan, and Data Layer contract.
- [ ] Record the audit finding/baseline the POC is intended to improve.

### 2. Configure safely

- [ ] Create a dedicated GTM workspace named with ticket/use case/owner.
- [ ] Export or record the starting container version for rollback/diff.
- [ ] Implement Data Layer pushes in the test environment.
- [ ] Create variables, triggers, and tags using `02` naming and descriptions.
- [ ] Configure the QA measurement destination and verify consent requirements.
- [ ] Review workspace changes to exclude unrelated edits.

### 3. Test each layer

- [ ] Run TC-01 through TC-10 from `04` where applicable.
- [ ] Inspect the selected Data Layer message and variables in Tag Assistant.
- [ ] Confirm the intended tag fires once and unintended tags do not.
- [ ] Confirm request destination, event name, parameters, types, and request count.
- [ ] Confirm event/parameters in the correct GA4 DebugView.
- [ ] Scan Data Layer and requests for PII, secrets, free text, and sensitive data.
- [ ] Capture sanitized evidence with timestamps and configuration IDs.

### 4. Review and decide

- [ ] Have QA reproduce the critical happy and negative cases independently.
- [ ] Compare before/after counts, complexity, dependencies, and defects where a baseline exists.
- [ ] Record limitations and deviations from the standards.
- [ ] Choose adopt, revise, or reject and explain why.
- [ ] Create rollout/remediation items with owner, effort, dependency, and rollback plan.

## Test Result Template

| Test | Data Layer | GTM | Network | DebugView | Privacy/consent | Overall | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Valid sign-up | Pass | Pass | Pass | Pass | Pass | Pass | E-01–E-04 |
| Invalid form | Pass | Pass | N/A/no success request | N/A | Pass | Pass | E-05 |
| Double click | Pass | Pass | Pass: one request | Pass: one event | Pass | Pass | E-06 |
| Consent denied | `[result]` | `[result]` | `[expected mode behavior]` | `[result]` | Pass/Fail | `[result]` | E-07 |

## Before/After Template

| Measure | Before | POC | Improvement/remaining issue |
| --- | --- | --- | --- |
| Success trigger | Button click | Confirmed server response | Removes failed attempts |
| Duplicate count | `[observed]` | `[observed]` | `[result]` |
| DOM dependencies | `[count]` | `0` target | `[result]` |
| Traceable variables | `[count/%]` | `100%` target | `[result]` |
| Required tests passed | `[count/%]` | `100%` target | `[result]` |

## Decision Record

```text
Decision: Adopt / Revise / Reject
Date:
Decision owner:
Evidence reviewed:
Success criteria passed:
Success criteria failed:
Limitations:
Required revisions:
Rollout scope and sequence:
Monitoring and rollback:
Reviewers/sign-off:
```

## Definition of Done

- [ ] Scope, hypothesis, and success criteria were agreed before implementation.
- [ ] All components comply with approved contracts and naming rules.
- [ ] No missing/duplicate success events occur in agreed cases.
- [ ] Values/types agree at all four validation layers.
- [ ] Privacy and relevant consent cases pass.
- [ ] Evidence is complete, sanitized, dated, and reproducible.
- [ ] Baseline comparison and limitations are documented.
- [ ] Reviewer sign-off exists.
- [ ] Adopt/revise/reject decision and owned next steps are recorded.
- [ ] No production change was made as part of the POC unless separately approved.

## Official References

- [The Data Layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Set up GA4 events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [GTM Preview](https://support.google.com/tagmanager/answer/6107056)
- [Publishing, versions, and approvals](https://support.google.com/tagmanager/answer/6107163)
- [Consent Mode](https://developers.google.com/tag-platform/security/concepts/consent-mode)
