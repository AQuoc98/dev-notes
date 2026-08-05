# Subtask 05: Audit One Tracking Flow

## Objective

Evaluate one existing tracking flow end to end, identify gaps against the proposed standards, and create a risk/value-prioritized remediation backlog.

## Scope

- One representative journey, such as form submission, sign-up, or purchase.
- Application instrumentation, Data Layer, GTM configuration, and GA4 payload.
- Naming, variable dependencies, trigger accuracy, duplicates, missing events, consent, and data quality.
- Audit and recommendations only; fixes are limited to the agreed POC.

## Work Items

1. Confirm the flow, expected behavior, environments, and stakeholders.
2. Trace each step from user action to GA4.
3. Inventory tags/triggers/variables and compare them with the tracking plan.
4. Test happy path, negative path, refresh/back, SPA navigation, and applicable consent states.
5. Record findings with evidence, impact, likelihood, and recommendations.
6. Classify Must/Should/Could items, quick wins, and proposed owners.

## Deliverables / Outputs

- As-is tracking-flow map.
- Audit report with evidence.
- Findings register with severity, impact, recommendation, and proposed owner.
- Prioritized remediation backlog and short executive summary.

## Expected Result

The team understands whether the flow is reliable, where risks exist, and what to address first. The POC use case is selected using audit evidence.

## Acceptance Criteria

- [ ] The stakeholder confirms the flow and expected business outcome.
- [ ] The trace covers application, Data Layer, GTM, and GA4 request/DebugView.
- [ ] Every finding includes evidence, impact, severity, and recommendation.
- [ ] Duplicate/missing events, parameter quality, PII, and consent are assessed.
- [ ] Recommendations are prioritized Must/Should/Could and split into quick wins/long-term work.
- [ ] Audit limitations are clearly documented.

## Dependencies

- Selected flow and suitable test account/data.
- Code, GTM, and GA4 access.
- Tracking plan or expected behavior from stakeholders.
- Draft QA checklist.

## Estimated Effort

**10 hours** — scope/baseline 1.5h, trace/testing 4h, analysis 2h, report/review 2.5h.

## Instructions / Answer

See [05-tracking-flow-audit-answer.md](./05-tracking-flow-audit-answer.md).

