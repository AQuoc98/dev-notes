# Subtask 03: Standardize GTM Trigger Management

## Objective

Define how GTM triggers are selected from the correct business moment, scoped to the right event and page, named, filtered, tested, reused, published, monitored, and retired so tags fire at the intended moment with the intended frequency.

The work must distinguish between:

- a trigger matching and a tag actually sending a request;
- a user interaction and a confirmed business outcome;
- a shared trigger's intended reuse and accidental duplicate collection;
- configuration evidence and runtime evidence.

## Scope — Included Items

- Trigger selection from the business moment and authoritative source: Page View, Initialization, Consent Initialization, Custom Event, click, form, History Change, Element Visibility, Scroll Depth, YouTube Video, Timer, and Trigger Group where applicable.
- Trigger logic: multiple firing triggers, multiple conditions, blocking/exception triggers, consent checks, tag firing options, tag sequencing, and Trigger Group semantics.
- Page-load timing and event availability, including Data Layer message timing, DOM-dependent values, SPA navigation, browser navigation, and delayed or lazy-loaded containers.
- Filter design using stable variables, URL components, exact operators, regular expressions, CSS selectors, missing-value behavior, route boundaries, and false-positive testing.
- Custom Event design for application-owned events, including event naming, event-specific values, expected frequency, duplicate policy, and the distinction between intent and confirmed outcomes.
- Naming, descriptions, shared-trigger reuse, consumer mapping, ownership, versioning, publishing, monitoring, change control, and retirement.

## Scope — Excluded Items

- A full production-container rewrite or unrestricted audit of unrelated GTM assets.
- Full Consent Management Platform design or legal consent-policy decisions.
- Server-side tagging implementation.
- Remediation of unrelated tags, variables, GA4 reports, or application code beyond the selected trigger flow.
- Marking runtime tests as passed without GTM Preview, Network, and downstream evidence.

## Deliverables / Outputs

- One GTM trigger management guideline covering the trigger mental model, business-moment selection, trigger-type decision rules, page-load timing, Data Layer event availability, filters, regex/CSS selector standards, consent, exceptions, Trigger Groups, tag sequencing, reuse, change control, publishing, monitoring, and retirement.
- One trigger decision matrix explaining when to use each scoped trigger type and the main risk or boundary for that choice.
- One scoped trigger inventory and consumer map containing, for every trigger: ID/name, type/event, exact conditions, route/action scope, consuming tags and GA4 event outputs, exceptions, consent checks, Trigger Group/tag-sequencing membership, timing risk, frequency/duplicate policy, owner/reviewer, environment/version, status, and retirement condition.
- One test plan and evidence-ready test record covering valid, negative, missing/malformed, duplicate, SPA/navigation, consent, exception, Trigger Group, browser/navigation, and downstream Network/GA4 DebugView behavior. Results must identify environment, container version, tester, date, evidence, and status; unexecuted tests remain Pending.
- One change-control and retirement record describing affected consumers, approval, published version, rollback point, monitoring, replacement, and retirement condition.

## Instructions / Answer

See [03-trigger-management-answer.md](./03-trigger-management-answer.md).
