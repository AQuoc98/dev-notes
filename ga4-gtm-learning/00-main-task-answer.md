# 00 — GA4/GTM Tracking Standardization Guide

## Goal

Create a repeatable measurement lifecycle:

```text
Business question
  → tracking plan
  → application dataLayer.push()
  → GTM trigger + variables + tag
  → GA4 collection request
  → DebugView / reports
  → QA evidence and sign-off
```

The six guides in this folder explain both the theory and the work required to produce a verified proof of concept (POC).

## Core Theory

### What are GA4 and GTM?

- **Google Analytics 4 (GA4)** collects event-based behavioral data and makes it available for analysis.
- **Google Tag Manager (GTM)** manages tags that read application data, decide when to run, and send data to tools such as GA4.
- GTM is a delivery and routing layer. It does not define the business meaning of data; the tracking plan and Data Layer contract do.

### What is a tracking plan?

A tracking plan is the human-readable measurement contract. It connects a business question to an event, its precise trigger, parameters, data source, owner, and validation method. It must exist before GTM configuration begins.

### What is analytics QA?

Analytics QA verifies meaning and transport. A tag firing is insufficient: the right event must fire once, at the right business moment, with valid values, under the right consent state, and arrive in the intended GA4 property.

## Working Example

Use a non-production sign-up journey unless the team selects another flow:

1. User views the registration form.
2. User submits valid or invalid data.
3. The server confirms successful account creation.
4. The application pushes `sign_up` only after confirmed success.
5. GTM reads `method` and `form_id`, then sends the recommended GA4 `sign_up` event.

Never send form values such as name, email, phone number, address, free text, or authentication tokens.

## Required Access and Roles

| Role | Responsibility |
| --- | --- |
| Product/analytics | Defines business questions, event meaning, and success criteria |
| Developer | Implements the Data Layer contract at the authoritative application state |
| GTM implementer | Configures variables, triggers, tags, consent requirements, and versions |
| QA | Executes positive, negative, duplicate, SPA, and consent cases |
| Approver/publisher | Reviews evidence and controls release |

Required access:

- Source code or developer support.
- A test environment and test data.
- A dedicated GTM workspace with edit access.
- GTM Preview/Tag Assistant.
- The correct GA4 test property or web stream and DebugView access.
- The applicable privacy and consent requirements.

## Completion Sequence

### Phase 1 — Agree

- [ ] Select one application, environment, journey, GA4 stream, and GTM container.
- [ ] Name the business owner, developer, analytics owner, QA reviewer, and publisher.
- [ ] Write the business questions and measurable success criteria.
- [ ] Confirm privacy, consent, and retention constraints.

### Phase 2 — Specify

- [ ] Complete [01-data-layer-design-answer.md](./01-data-layer-design-answer.md).
- [ ] Complete [02-variable-management-answer.md](./02-variable-management-answer.md).
- [ ] Complete [03-event-parameter-naming-answer.md](./03-event-parameter-naming-answer.md).
- [ ] Obtain development, analytics, and QA review before implementation.

### Phase 3 — Validate the existing state

- [ ] Prepare the workflow in [04-debugging-analytics-qa-answer.md](./04-debugging-analytics-qa-answer.md).
- [ ] Audit one existing flow with [05-tracking-flow-audit-answer.md](./05-tracking-flow-audit-answer.md).
- [ ] Turn findings into Must/Should/Could remediation items.

### Phase 4 — Prove and decide

- [ ] Build the non-production POC in [06-proof-of-concept-answer.md](./06-proof-of-concept-answer.md).
- [ ] Capture Data Layer, GTM, network, DebugView, consent, and negative-test evidence.
- [ ] Record reviewer sign-off and an explicit adopt/revise/reject decision.
- [ ] Create the rollout backlog, owners, dependencies, and rollback approach.

## Definition of Done

- [ ] All `01`–`06` checklists are complete or exceptions are documented.
- [ ] Every event has a business definition, trigger point, source, owner, and test.
- [ ] The POC is traceable from application action to GA4 DebugView.
- [ ] No missing or duplicate event is observed in the agreed test cases.
- [ ] Values and types agree at the Data Layer, GTM, and GA4 request layers.
- [ ] No PII, secrets, raw form values, or fine-grained location are collected.
- [ ] Relevant granted and denied consent states have been tested.
- [ ] Evidence identifies date, environment, container version/workspace, GA4 stream, tester, and result.
- [ ] Development, QA, and analytics feedback is resolved or recorded.
- [ ] A versioned rollout plan and prioritized backlog exist.

## Evidence Index Template

| ID | Layer | Evidence | Location | Owner | Date | Result |
| --- | --- | --- | --- | --- | --- | --- |
| E-01 | Data Layer | Successful `sign_up` push | `[link/path]` | `[name]` | YYYY-MM-DD | Pass |
| E-02 | GTM | Tag Assistant event and fired tag | `[link/path]` | `[name]` | YYYY-MM-DD | Pass |
| E-03 | Network | GA4 request parameters | `[link/path]` | `[name]` | YYYY-MM-DD | Pass |
| E-04 | GA4 | DebugView event and parameters | `[link/path]` | `[name]` | YYYY-MM-DD | Pass |

## Official References

- [GTM components](https://support.google.com/tagmanager/answer/6103657)
- [The Data Layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [Set up GA4 events](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [GTM preview and debug](https://support.google.com/tagmanager/answer/6107056)
- [GA4 DebugView](https://support.google.com/analytics/answer/7201382)
- [Avoid sending PII](https://support.google.com/analytics/answer/6366371)
