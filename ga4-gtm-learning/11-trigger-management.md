# Subtask 11: Standardize GTM Trigger Management

## Objective

Define how GTM triggers are selected, scoped, named, tested, reused, and retired so tags fire at the intended business moment exactly as designed.

## Scope

- Page View, Initialization, Consent Initialization, Custom Event, click, form, history change, timer, and trigger group usage where applicable.
- Firing filters, exceptions, event timing, shared triggers, and dependencies.
- The selected flow/POC rather than a full-container rewrite.

## Work Items

1. Inventory scoped triggers and consuming tags.
2. Define trigger-type selection, naming, descriptions, ownership, and reuse rules.
3. Document timing and exception behavior, including consent initialization.
4. Test positive, negative, duplicate, SPA, navigation, and consent cases.
5. Define change and retirement checks.

## Deliverables / Outputs

- Trigger design and lifecycle guideline.
- Trigger inventory and consumer map.
- Trigger-type decision guide and test matrix.

## Expected Result

Every trigger has a precise event and filter definition, known consumers, verified timing, and evidence that it matches only when expected.

## Acceptance Criteria

- [ ] Every scoped trigger has a type, purpose, conditions, consumers, owner, and status.
- [ ] Consent Initialization is reserved for consent-setting logic.
- [ ] Custom Events use approved Data Layer event names.
- [ ] Positive and negative tests prove correct single-fire behavior.
- [ ] Exceptions and trigger groups have documented justification and tests.

## Dependencies

- Approved Data Layer contract and event naming standard.
- Scoped tag inventory and GTM Preview access.

## Estimated Effort

**6 hours** — inventory 1h, guideline 2h, test design 1h, validation/review 2h.

## Instructions / Answer

See [11-trigger-management-answer.md](./11-trigger-management-answer.md).
