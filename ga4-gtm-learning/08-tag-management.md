# Subtask 08: Standardize GTM Tag Management

## Objective

Define how GTM tags are selected, configured, named, tested, approved, monitored, and retired so data is sent once, to the correct destination, under the correct consent state.

## Scope

- Google tag, GA4 Event tags, and other tags used by the selected flow/POC.
- Firing triggers, exceptions, sequencing, consent settings, parameters, and destinations.
- Native templates versus Custom HTML, ownership, descriptions, and lifecycle.
- A scoped inventory rather than a full production-container refactor.

## Work Items

1. Inventory the scoped tags and map each to its purpose, trigger, variables, consent requirements, and destination.
2. Define tag naming, description, ownership, testing, approval, and retirement rules.
3. Define when shared settings, exceptions, sequencing, and Custom HTML are permitted.
4. Test positive, negative, duplicate, consent-denied, SPA, and failure cases.
5. Record dependencies and publication/rollback evidence.

## Deliverables / Outputs

- Tag design and lifecycle guideline.
- Tag inventory and dependency map.
- Naming matrix and tag-type decision guide.
- Test evidence for the POC tags.

## Expected Result

Every scoped tag has a clear business purpose, controlled firing behavior, known inputs and destination, an owner, and reproducible QA evidence.

## Acceptance Criteria

- [ ] Every POC tag has a documented purpose, type, triggers/exceptions, inputs, consent requirement, and destination.
- [ ] Naming, description, ownership, test, approval, and retirement rules are defined.
- [ ] Custom HTML and sequencing require documented justification and review.
- [ ] Tests prove the tag fires once when expected and not when prohibited.
- [ ] A reviewer can trace Data Layer event → variables → trigger → tag → destination.

## Dependencies

- Approved Data Layer contract and event naming standard.
- GTM workspace and Preview/Tag Assistant access.
- Applicable consent requirements and destination identifiers.

## Estimated Effort

**7 hours** — inventory 1.5h, guideline 2h, configuration review 1.5h, testing/review 2h.

## Instructions / Answer

See [08-tag-management-answer.md](./08-tag-management-answer.md).
