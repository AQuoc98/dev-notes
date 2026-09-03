# 03 — GTM Trigger Management

## Objective

Define how to select, scope, test, publish, monitor, and retire Triggers so each Tag evaluates the approved business moment once and in the correct environment.

## Scope

- Authoritative event source and Trigger-type selection.
- Trigger logic: firing conditions, OR/AND behavior, exceptions, consent, timing, and filters.
- Custom Event patterns for application-confirmed outcomes.
- Page-load, DOM, URL, SPA, navigation, click, visibility, and other scoped Trigger types when they represent the measurement question.
- Trigger Group versus tag sequencing, naming, reuse, inventory, QA, release, and retirement.
- FD calculation_action as the reference Custom Event.

## Outputs

1. A Trigger decision matrix with preferred type, timing, boundary, and main risk.
2. A Trigger contract/inventory containing event, authoritative source, conditions, consumers, exceptions, consent, expected frequency, owner, environment, and lifecycle.
3. Rules for narrow filters, stable Variables, exact/RegEx operators, missing values, and duplicate prevention.
4. A test record covering valid, negative, duplicate, SPA/navigation, consent, exception, Trigger Group, Network, and GA4 DebugView evidence.
5. An FD calculation_action Journey showing one application event and one authoritative Trigger match.

## Acceptance criteria

- Use the narrowest Trigger that represents the approved business moment.
- Application Custom Events are preferred for confirmed outcomes; click, page, or DOM rules do not replace a missing business event.
- Firing Triggers behave as alternatives (OR); conditions inside one Trigger must all pass (AND); any matching exception blocks the Tag.
- Timing does not create stale or missing values, and a Trigger Group is not used to recreate application workflow.
- A Trigger match is kept separate from Tag execution, Network delivery, and downstream GA4 receipt.

## Out of scope

Detailed Variable source design, Tag configuration, consent-policy design, custom-template development, reports, or release operations; use Sections 02, 04–10. Advertising-specific Triggers are excluded.

## Source

Detailed implementation: [03-trigger-management-answer.md](./03-trigger-management-answer.md).
