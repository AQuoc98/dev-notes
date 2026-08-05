# 03 — GA4 Event and Parameter Naming

## Theory

### Event versus parameter

- An **event** states what happened: `sign_up`, `login`, `purchase`.
- A **parameter** adds context: `method: 'google'`.
- A **user property** describes a relatively persistent user attribute; it should not represent an action.
- A **custom dimension/metric** makes an eligible collected parameter available for reporting. Collection and registration are separate steps.

### Event selection hierarchy

Use the first applicable category:

1. Automatically collected event.
2. Enhanced measurement event.
3. GA4 recommended event with its documented parameters.
4. Custom event only when none of the above represents the business action.

Do not create a custom synonym such as `registration_complete` when recommended `sign_up` has the correct meaning.

## Naming Rules

- Use lowercase `snake_case` for custom event and parameter names.
- Start with a letter; use only letters, digits, and underscores.
- Keep event names within GA4’s 40-character collection limit.
- Do not use reserved event names or reserved prefixes (`google_`, `ga_`, `firebase_`).
- Use stable business vocabulary, not button text, color, position, CSS selector, or campaign copy.
- Use singular concepts consistently (`form_id`, not a mix of `form`, `form_name`, and `formID`).
- Store categorical context in parameters instead of generating many event names.
- Use controlled, documented values and consistent types.
- Do not put PII or unrestricted user-entered text in names or values.

### Good and bad examples

| Avoid | Prefer | Reason |
| --- | --- | --- |
| `click_green_signup_button` | `sign_up` + `method` | Business outcome, independent of UI |
| `formSubmit` | `generate_lead` or justified custom name | Consistent syntax and semantics |
| `download_pdf_pricing_2026` | `file_download` + file context | Prevent event-name explosion |
| `sign_up_jane@example.com` | `sign_up` | PII must not be sent |
| `validation_error_email_value` | `form_error` + controlled `error_type` | No raw user input |

## Parameter and Cardinality Principles

- Define type: string, integer, number, or boolean. Do not send `"29.99"` when the contract requires numeric `29.99`.
- Define required/optional status and allowed values.
- Use ISO 4217 currency codes such as `USD` when GA4 specifies currency.
- Avoid IDs or values with huge uniqueness as custom dimensions unless there is a justified analysis need.
- Do not register a custom definition when a predefined dimension/metric already exists.
- Categorical text generally maps to a custom dimension; quantitative event values may map to a custom metric.
- Mark an event as a key event only when it represents an agreed important business outcome. Do not use key-event status as a substitute for correct implementation.

## Example Taxonomy and Tracking Plan

| Business question | GA4 event | Definition and trigger | Parameters | Key event? |
| --- | --- | --- | --- | --- |
| How often is registration started? | `sign_up_start` (custom) | First meaningful engagement with registration, once per attempt | `form_id`, `method` if known | No |
| How many accounts are created? | `sign_up` (recommended) | Server confirms account creation, once | `method`, `form_id` | Candidate: Yes |
| Why does registration fail? | `sign_up_error` (custom) | Controlled application/server failure shown | `form_id`, `error_type`, `error_stage` | No |

`error_type` must be a controlled enum such as `validation`, `duplicate_account`, `server`, or `unknown`; never send the raw error message or entered value.

### Detailed parameter contract

| Event | Parameter | Type | Required | Allowed/example | Source | Custom definition? |
| --- | --- | --- | --- | --- | --- | --- |
| `sign_up` | `method` | string | Yes | `email`, `google`, `apple` | Auth context | Usually only if reporting requires it and no predefined dimension fits |
| `sign_up` | `form_id` | string | No | `account_registration` | Application constant | Event-scoped dimension if approved |
| `sign_up_error` | `error_type` | string | Yes | Controlled enum | Application error mapping | Event-scoped dimension if approved |

## Steps to Complete the Task

1. List the business questions before listing events.
2. Inventory current names and usage from GTM and GA4.
3. Check automatic, enhanced, and recommended events first.
4. Give each selected event one unambiguous definition and trigger point.
5. Define every parameter’s type, requirement, enum, source, privacy risk, and reporting need.
6. Review GA4 name/parameter limits and reserved names against current official documentation.
7. Assess cardinality using expected distinct values per day.
8. Decide which parameters actually require custom definitions.
9. Map old → new names and define a migration/cutover date; avoid indefinite double sending.
10. Obtain product, analytics, development, and QA approval.

## Completion Checklist

- [ ] Each event answers a named business question.
- [ ] Automatic/enhanced/recommended events were considered first.
- [ ] Custom events include written justification.
- [ ] Names comply with syntax, length, and reserved-name rules.
- [ ] Parameters have types, required flags, allowed values, sources, and examples.
- [ ] PII, free text, secrets, and high cardinality have been assessed.
- [ ] Custom definition and key-event decisions are recorded.
- [ ] Migration mapping and duplicate-prevention plan exist.
- [ ] The tracking plan is approved and versioned.

## Official References

- [GA4 event types and setup](https://developers.google.com/analytics/devguides/collection/ga4/events)
- [Recommended events](https://developers.google.com/analytics/devguides/collection/ga4/reference/events)
- [Event naming rules](https://support.google.com/analytics/answer/13316687)
- [Event collection limits](https://support.google.com/analytics/answer/9267744)
- [GA4 cardinality](https://support.google.com/analytics/answer/12226705)
- [Create custom metrics](https://support.google.com/analytics/answer/14239619)
