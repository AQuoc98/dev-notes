# 11 — GTM Trigger Management

## Theory

A trigger listens for a GTM event and evaluates conditions that tell a tag whether it may fire. Variables provide values used in those conditions. A trigger match proves only that GTM attempted the tag—not that a valid request reached GA4.

## Trigger-Type Decision Guide

1. Use **Consent Initialization** only for tags that establish or update consent state before other triggers.
2. Use **Initialization** for exceptional setup logic that must run before normal page-view triggers, not as the analytics default.
3. Use **Custom Event** for application-owned Data Layer events and exact approved event names.
4. Use **History Change** only when SPA routing is the authoritative moment and the application cannot provide a better event contract.
5. Use click, link, or form triggers only for stable browser interactions; scope them to tested pages/elements and do not treat a click as proof of business success.
6. Use timers, element visibility, trigger groups, and regex only with a documented need, bounds, and failure/duplicate tests.

## Design Standard

- Name triggers `[type] - [event/purpose] - [qualifier]`, such as `CE - sign_up` or `HC - SPA Route Change`.
- Match exact custom-event names unless a reviewed, anchored regex is genuinely required.
- Prefer stable business/Data Layer values over DOM text, CSS presentation, or brittle selectors.
- Document case sensitivity, missing values, regex behavior, timing, and exceptions.
- Reuse a trigger only when every consumer has identical firing semantics.
- Do not add blocking triggers merely to compensate for misunderstood consent-mode behavior.

## Example Flow — Evaluate the `sign_up` Trigger

```text
Application pushes `event: sign_up` after server confirmation
→ GTM creates the `sign_up` event in its timeline
→ `CE - sign_up` matches the exact event name
→ required variables are evaluated at that event
→ the GA4 Event tag fires once
→ `signup`, invalid submission, and route revisit do not match
→ Preview and network evidence confirm the outcome
```

If several tags consume this trigger, every consumer is regression-tested before its conditions are changed.

## Inventory Template

| Trigger | Type/event | Conditions | Consuming tags | Exceptions/group | Timing risk | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `CE - sign_up` | Custom Event: `sign_up` | All matching events | `GA4 Event - sign_up` | None | Duplicate application push | Analytics | Active |

## Required Tests

- Expected event and values: matches once.
- Similar name, wrong route, missing/malformed/wrong-case value: does not match or follows documented behavior.
- Repeated click, retry, back/forward, and SPA revisit: no unintended duplicate.
- Consent default/update timing: matches approved behavior.
- Exceptions and trigger-group order: observed explicitly in Preview.
- Downstream tag: network and destination behavior agrees with the trigger evaluation.

## Lifecycle Workflow

1. Trace the trigger to an approved business event and list all consumers.
2. Inspect Preview’s event timeline and variable values at that event.
3. Review new listeners for performance and unintended page coverage.
4. Regression-test every consumer before editing a shared trigger.
5. Before deletion, replace/remove consumers and retain a recoverable version/export.

## Completion Checklist

- [ ] Type, event, conditions, consumers, exceptions, timing, owner, and status are recorded.
- [ ] Event names match the Data Layer contract.
- [ ] Consent and initialization triggers use their intended timing roles.
- [ ] DOM/listener triggers are narrowly scoped and tested.
- [ ] Duplicate, negative, SPA, and consent cases pass.
- [ ] Shared-trigger changes include consumer regression evidence.
- [ ] Network/DebugView evidence confirms downstream behavior.

## Official References

- [About triggers](https://support.google.com/tagmanager/answer/7679316)
- [Trigger types](https://support.google.com/tagmanager/answer/7679320)
- [Trigger best practices](https://support.google.com/tagmanager/answer/7679102)
- [Firing triggers and exceptions](https://support.google.com/tagmanager/answer/7679318)
- [Custom event trigger](https://support.google.com/tagmanager/answer/7679219)
