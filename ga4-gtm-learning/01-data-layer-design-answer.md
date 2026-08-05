# 01 — Data Layer Design

## Theory

### What is a Data Layer?

A Data Layer is a structured JavaScript interface through which an application exposes business events and contextual values to GTM. GTM temporarily reads these messages and uses them in triggers, variables, and tags.

It is a **contract**, not a database and not merely an array of arbitrary values. It separates business meaning from UI markup so a CSS, layout, or text change does not silently break analytics.

### Data Layer principles

1. **Business-oriented:** describe an outcome (`sign_up`), not a UI gesture (`green_button_click`).
2. **Authoritative:** push from the code that knows the outcome. For example, push success after the server confirms it, not when the user clicks Submit.
3. **Stable:** producers and consumers agree on names, types, allowed values, and timing.
4. **Event-scoped:** include the values needed for that event in the same push.
5. **Minimal:** collect only data that answers an approved business question.
6. **Privacy-safe:** never expose PII, credentials, tokens, or unrestricted user input.
7. **Deterministic:** one business occurrence produces one event.
8. **Implementation-independent:** avoid DOM selectors, presentation text, and framework internals.
9. **Versioned:** incompatible contract changes require migration and coordination.
10. **Testable:** every field has an explicit type, source, and validation rule.

GTM processes Data Layer messages in first-in, first-out order. Tags triggered by an event evaluate that message before GTM moves to the next message. Therefore, push the event and its required values together.

## Recommended Contract

Use one global Data Layer and never overwrite it after GTM loads:

```javascript
window.dataLayer = window.dataLayer || [];
```

Example success event:

```javascript
window.dataLayer.push({
  event: 'sign_up',
  event_schema_version: '1.0',
  method: 'email',
  form_id: 'account_registration',
  page_type: 'registration'
});
```

Invalid example:

```javascript
window.dataLayer.push({
  event: 'submit_button_clicked', // UI action, not confirmed outcome
  email: 'person@example.com',    // PII: prohibited
  total: 'true'                   // unclear name and wrong/unclear type
});
```

### Contract table

| Field | Type | Required | Allowed/example | Source | Rule |
| --- | --- | --- | --- | --- | --- |
| `event` | string | Yes | `sign_up` | Application outcome handler | Stable business name |
| `event_schema_version` | string | Yes | `1.0` | Application constant | Increment for incompatible changes |
| `method` | string | Yes | `email`, `google`, `apple` | Authentication method enum | No email address |
| `form_id` | string | Yes | `account_registration` | Application constant | Stable ID, not DOM ID |
| `page_type` | string | No | `registration` | Route metadata | Controlled vocabulary |

### Reset and stale-data rule

GTM’s internal Data Layer model can retain values. Do not rely on a previous push to supply current event values. Put event-specific values in the same push. For ecommerce, clear stale ecommerce data before the next ecommerce event when required by the implementation pattern:

```javascript
window.dataLayer.push({ ecommerce: null });
window.dataLayer.push({
  event: 'view_item',
  ecommerce: {
    currency: 'USD',
    value: 29.99,
    items: [{ item_id: 'SKU_123', item_name: 'Example product', price: 29.99 }]
  }
});
```

### SPA rule

For a single-page application:

- Emit a route event after the router has committed and title/context are final.
- Choose either GA4 enhanced-measurement history tracking or a manual page-view design; prevent double collection.
- Do not emit business success events merely because a component rendered.
- Guard against repeated subscriptions, React development double effects, retries, and back/forward navigation.

## Steps to Complete the Task

1. Pick one journey and list its business outcomes.
2. Trace each value to its authoritative application source.
3. Define one row per event/field using the contract table above.
4. Mark required/optional fields, types, enums, nullability, and examples.
5. Define exact trigger timing and deduplication responsibility.
6. Document SPA, asynchronous response, retry, and reset behavior.
7. Review every value for PII, secrets, sensitive data, and cardinality.
8. Add valid, invalid, edge, and denied-consent examples.
9. Have development confirm feasibility and QA derive test cases.
10. Version and approve the contract before GTM configuration.

## Review Checklist

- [ ] Each event represents a business fact and has an owner.
- [ ] The exact push point is documented.
- [ ] Required values are in the same push as `event`.
- [ ] Types and allowed values are unambiguous.
- [ ] Duplicate prevention and retry behavior are defined.
- [ ] SPA route behavior and page-view ownership are defined.
- [ ] No DOM scraping is required for contract data.
- [ ] No prohibited personal or sensitive data appears in examples.
- [ ] Compatible versus breaking version changes are defined.
- [ ] Development and QA approve the contract.

## Deliverable Template

| Event | Business definition | Trigger point | Field | Type | Required | Allowed values | Source | Owner | Test ID |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `sign_up` | Account created successfully | Successful server response, once | `method` | string | Yes | `email`, `google`, `apple` | Auth response/context | Product | TC-01 |

## Official References

- [The Data Layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
- [GTM components and Data Layer guidance](https://support.google.com/tagmanager/answer/6103657)
- [Consent implementation](https://developers.google.com/tag-platform/security/guides/consent)
- [Avoid sending PII](https://support.google.com/analytics/answer/6366371)
