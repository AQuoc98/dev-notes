# 04 — GTM Tag Management

## Objective

Define how GTM Tags are selected, configured, tested, approved, published, monitored, and retired so approved data is sent once to the correct GA4 destination.

## Scope

- Shared Google tag and focused GA4 Event tags.
- Parameter allowlist, Variable mapping, authoritative Trigger, consent, exceptions, sequencing, environment routing, and expected request count.
- Native templates versus reviewed Custom HTML, naming, descriptions, ownership, inventory, and lifecycle.
- Preview, browser Network, GA4 DebugView, and downstream validation.
- FD calculation_action as the reference Tag flow.

## Outputs

1. A Tag decision workflow and readiness checklist: purpose → event → parameters → Trigger → consent → destination → count → QA.
2. A Tag contract/inventory containing purpose, type, event, parameters and sources, Trigger, consent, destination, owner, environment, status, and retirement condition.
3. Rules for one shared Google tag per environment, one focused Event tag per approved event, native templates first, and no business-logic inference in GTM.
4. A validation record proving Trigger match, Tag status, request payload, approved destination, and downstream receipt.
5. An FD calculation_action Journey with valid/no-output and failure expectations.

## Acceptance criteria

- The Application decides the business result; the Tag only maps and sends approved fields.
- The parameter allowlist is explicit, typed, privacy-safe, and handles missing values.
- The Tag has one authoritative Trigger and a documented expected count.
- Built-in Google consent behavior is the primary control; exceptions and sequencing are not redundant workarounds.
- Preview, Network, and downstream evidence agree before publish.

## Out of scope

Detailed Data Layer, Variable, Trigger, consent, measurement-plan, Debug/QA, report, or release design; use Sections 01–03 and 05–10. Advertising-specific Tags are excluded.

## Source

Detailed implementation: [04-tag-management-answer.md](./04-tag-management-answer.md).
