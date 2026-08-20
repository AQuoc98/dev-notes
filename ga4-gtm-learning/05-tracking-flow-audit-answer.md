# 05 — Audit One Tracking Flow

## Theory

A tracking-flow audit compares three states:

- **Expected:** the approved business meaning and tracking plan.
- **Implemented:** application pushes and GTM configuration.
- **Observed:** collection requests and GA4 results.

The audit is not merely a tag inventory. It must establish traceability, test behavior, assess risk, and convert findings into actionable work.

### Audit principles

- Freeze scope to one representative journey.
- Record evidence before proposing changes.
- Separate fact, inference, limitation, and recommendation.
- Score business/data/privacy risk, not aesthetic preference.
- Do not remediate production during an audit unless separately authorized.

## Scope Template

| Item | Decision |
| --- | --- |
| Journey | `[e.g., registration]` |
| Start/end | `[first action]` → `[confirmed outcome]` |
| Environment | `[QA/staging]` |
| GTM container/workspace/version | `[ID]` |
| GA4 property/stream | `[name / measurement ID]` |
| Consent cases | `[granted/denied/region]` |
| Stakeholder/owner | `[name]` |
| Exclusions | `[explicit exclusions]` |

## Steps to Complete the Audit

1. Obtain stakeholder confirmation of the journey and expected business outcome.
2. Draw the expected event sequence and complete the tracking plan.
3. Inspect the code/Data Layer producers and identify authoritative trigger points.
4. Inventory every scoped GTM tag, trigger, variable, consent rule, and destination.
5. Execute the full test matrix from `04`, capturing evidence at every layer.
6. Compare expected, implemented, and observed names, values, types, counts, timing, and routing.
7. Check PII, free-text, consent, cardinality, DOM coupling, Custom JavaScript, and environment isolation.
8. Log each finding with evidence, impact, likelihood, severity, owner, and recommendation.
9. Prioritize Must/Should/Could and quick-win/long-term items.
10. Review factual accuracy with development, QA, analytics, and the business owner.

## Example Flow — Audit a Registration Event

```text
Stakeholder expects one confirmed registration
→ reproduce the journey and capture baseline counts
→ trace application success callback and Data Layer push
→ inventory trigger, variables, tag, consent, and destination
→ compare network payload with DebugView
→ find that both a form-click tag and `sign_up` custom-event tag send success
→ classify duplicate counting as Must fix
→ recommend retaining only the authoritative custom-event implementation
```

The evidence links the observed duplicate to its exact source and gives the POC a measurable before/after target.

## As-is Flow Map Template

```text
[User action]
  → [application handler / state]
  → dataLayer event: [name]
  → GTM trigger: [name]
  → GTM variables: [names]
  → GA4 tag: [name]
  → destination: [measurement ID]
  → observed GA4 event: [name]
```

Annotate the map with evidence IDs and flag any unverified link.

## Audit Checklist

### Application and Data Layer

- [ ] Event represents confirmed business state, not a fragile click proxy.
- [ ] Event occurs once under repeat clicks, retries, refresh, and SPA navigation.
- [ ] Required fields are present in the same event push with correct types.
- [ ] Schema version, ownership, and source are known.
- [ ] No PII, secrets, raw form text, or sensitive information is exposed.

### GTM

- [ ] Tags, triggers, and variables follow naming and documentation standards.
- [ ] Trigger conditions are neither too broad nor too narrow.
- [ ] Consumers and dependencies are traceable.
- [ ] Duplicate/unused variables and tags are identified.
- [ ] DOM and Custom JavaScript dependencies are justified.
- [ ] Environment routing and consent checks are correct.

### GA4 and data quality

- [ ] Recommended event/parameter definitions are followed where applicable.
- [ ] Event and parameter names/types match the tracking plan.
- [ ] Requests arrive once in the intended property/stream.
- [ ] Cardinality and custom-definition needs are assessed.
- [ ] Key-event configuration matches the approved business decision.

## Findings Register

| ID | Finding/evidence | Impact | Likelihood | Severity | Recommendation | Priority | Effort | Owner |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| F-01 | `sign_up` fires on click before server success; E-07 | Inflated registrations | High | High | Push after confirmed response | Must/quick win | M | Development |

### Priority definitions

- **Must:** privacy/compliance exposure, key-outcome corruption, broken routing, or release blocker.
- **Should:** meaningful quality, reliability, or maintainability improvement.
- **Could:** beneficial optimization with low present risk.

Priority is not severity alone. Consider impact, likelihood, effort, dependency, and business urgency.

## Executive Summary Template

```text
Scope: [journey/environment/date]
Overall confidence: High / Medium / Low
Tests: [passed] / [total]
Findings: [critical], [high], [medium], [low]
Top risks: [1–3 concise risks]
POC candidate: [finding/use case and why]
Recommendation: [what to do next]
Limitations: [access, traffic, browser, consent, reporting delay, etc.]
```

## Completion Checklist

- [ ] Scope and expected outcome are stakeholder-approved.
- [ ] The full flow is traced through application, Data Layer, GTM, request, and GA4.
- [ ] Happy, negative, duplicate, SPA, and consent cases are covered where applicable.
- [ ] Every finding has reproducible evidence and separates fact from inference.
- [ ] Privacy, consent, naming, types, cardinality, missing data, and duplicates are assessed.
- [ ] Findings have severity, priority, effort, recommendation, and proposed owner.
- [ ] Limitations and unverified areas are explicit.
- [ ] Review comments are resolved or recorded.
- [ ] One evidence-based POC candidate is selected.

## Official References

- [GTM Preview and Tag Assistant](https://support.google.com/tagmanager/answer/6107056)
- [GA4 DebugView](https://support.google.com/analytics/answer/7201382)
- [GTM container export](https://support.google.com/tagmanager/answer/6106997)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
