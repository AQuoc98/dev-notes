# 02 — GTM Variable Management

## Theory

### What is a GTM variable?

A variable is a named placeholder whose value is evaluated when a tag or trigger needs it. A variable can read a Data Layer key, hold a constant, map one value to another, parse a URL, or calculate a value.

Variables make configuration reusable, but every variable is also a dependency. Good management makes its source, purpose, consumers, owner, and lifecycle visible.

### Variable design principles

- Prefer application-owned Data Layer values over DOM scraping.
- Use the simplest native variable type that solves the problem.
- Give one business concept one canonical variable.
- Keep transformation separate from collection.
- Avoid Custom JavaScript unless native variables cannot express the requirement.
- Never use variables to collect prohibited data.
- Document fallback behavior; do not silently turn missing data into misleading data.

## Naming Standard

| Prefix | Type | Example |
| --- | --- | --- |
| `DLV -` | Data Layer Variable | `DLV - method` |
| `CONST -` | Constant | `CONST - GA4 Measurement ID - QA` |
| `LUT -` | Lookup Table | `LUT - Hostname to Measurement ID` |
| `RLT -` | RegEx Table | `RLT - Page Path to Page Type` |
| `URL -` | URL variable | `URL - Query - campaign_id` |
| `DOM -` | DOM Element | `DOM - Checkout Total` (exception/risky) |
| `COOKIE -` | First-party Cookie | `COOKIE - Consent State` |
| `JS -` | Custom JavaScript | `JS - Normalize Method` (exception) |
| `EV -` | Event name | `EV - Current Event` |

Names should explain source and purpose. Put environment-specific routing in a lookup table rather than duplicating tags.

## Variable Decision Guide

1. Is the value a built-in page/click/form property that is stable and appropriate? Use a built-in variable.
2. Is it in the approved Data Layer contract? Use a Data Layer Variable.
3. Is it a fixed, non-secret value reused by tags? Use a Constant.
4. Is the output selected from an exact input mapping? Use a Lookup Table.
5. Does a controlled pattern determine the output? Use a RegEx Table and test overlaps/order.
6. Can a native URL, cookie, or DOM type solve it safely? Use that type, noting privacy and fragility.
7. Only then consider Custom JavaScript, with code review, tests, failure behavior, and an owner.

Never store secrets in GTM Constants: published container code is available to the browser.

## Standard Configuration for the Example

| Name | Type/source | Used by | Fallback | Risk/status |
| --- | --- | --- | --- | --- |
| `DLV - method` | DLV v2, `method` | `GA4 Event - sign_up` | `undefined`; fail QA | Active/low |
| `DLV - form_id` | DLV v2, `form_id` | `GA4 Event - sign_up` | Omit if optional | Active/low |
| `DLV - event_schema_version` | DLV v2 | QA/diagnostics | None | Active/low |
| `LUT - Hostname to Measurement ID` | Lookup by Page Hostname | Google tag | QA ID default; production explicitly mapped | Active/medium |

For nested keys, document the exact path and Data Layer Variable version. Verify how missing and nested values behave in Preview rather than assuming.

## Custom JavaScript Policy

Custom JavaScript is allowed only when:

- Native templates and variable types cannot meet the requirement.
- The code is deterministic, synchronous, small, and side-effect free.
- It does not read raw form fields or expose private data.
- Its return type and failure/default behavior are documented.
- It has tests for null, undefined, unexpected types, and normal inputs.
- A reviewer and owner are named.

Do not use Custom JavaScript to patch a deficient Data Layer contract indefinitely. Record a replacement issue.

## Steps to Complete the Task

1. Export or inspect the scoped GTM workspace/container.
2. List each variable used by the flow’s tags and triggers.
3. Record type, source, consumers, owner, environment, fallback, and status.
4. Identify exact duplicates, near-duplicates, unused variables, DOM dependencies, secrets, and Custom JavaScript.
5. Choose the canonical variable for each concept.
6. Apply the naming scheme and add meaningful GTM descriptions.
7. Replace avoidable transformations with Data Layer values or native tables.
8. Test each variable at the relevant event in Preview—not only on page load.
9. Mark old variables `Deprecated - [replacement] - [date]`; remove only after consumer checks.
10. Attach the completed inventory and reviewer approval.

## Inventory Template

| Variable | Type | Exact source | Consumers | Owner | Environment | Fallback | Risk | Status/replacement |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `DLV - method` | DLV v2 | `method` | `GA4 Event - sign_up` | Analytics | All | None | Low | Active |

## Completion Checklist

- [ ] Every scoped variable has a documented source and consumer.
- [ ] Duplicate, unused, DOM, and Custom JavaScript variables are identified.
- [ ] Every POC variable follows the naming standard and has a description.
- [ ] Environment mapping cannot accidentally send QA data to production or vice versa.
- [ ] Missing and invalid input behavior is tested.
- [ ] Deprecation includes replacement, owner, date, and verified consumer removal.
- [ ] No secret or prohibited personal data is stored or read.
- [ ] Another reviewer can trace application value → variable → trigger/tag.

## Official References

- [Variables in GTM](https://support.google.com/tagmanager/answer/13355320)
- [User-defined web variable types](https://support.google.com/tagmanager/answer/7683362)
- [Container export and import](https://support.google.com/tagmanager/answer/6106997)

