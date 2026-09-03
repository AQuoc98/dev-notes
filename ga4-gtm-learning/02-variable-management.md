# 02 — GTM Variable Management

## Objective

Define a small, reusable system for creating, naming, configuring, testing, publishing, and retiring GTM Variables used by stable GA4 tracking.

## Scope

- Variable source selection and native type preference.
- Data Layer Variables, nested paths, constants, lookup/RegEx tables, URL/cookie values, DOM values, and limited Custom JavaScript.
- Variable contracts, naming, folders, scope, reuse, owners, environments, consent, and missing-data rules.
- Search-before-create, inventory, QA, deprecation, and retirement.
- FD calculation_action Variable setup.

## Outputs

1. A Variable decision workflow: classify → search → contract → choose type → configure → test → publish → maintain.
2. A type/source decision matrix that prefers application/Data Layer values and uses DOM or Custom JavaScript only when justified.
3. A Variable contract and inventory containing source, type, meaning, consumers, allowed values, fallback, privacy, owner, environment, and lifecycle status.
4. Practical rules for nested Data Layer paths, environment routing, missing required values, and consent-aware use.
5. An FD example mapping approved fields to the GA4 Tag and a focused audit checklist.

## Acceptance criteria

- Variables read approved values; they do not recreate application business logic.
- Required missing values cause a documented QA failure or block; optional values are omitted or handled as specified.
- Environment routing fails closed for unknown hosts.
- Shared Variables are reused only when source, semantics, consent, and lifecycle are compatible.
- New or changed Variables are tested in GTM Preview and, when needed, the browser Network panel before publishing.

## Out of scope

Detailed Trigger, Tag, consent, measurement-plan, Debug/QA, report, or release design; use Sections 03–10. Advertising-specific Variables are excluded.

## Source

Detailed implementation: [02-variable-management-answer.md](./02-variable-management-answer.md).
