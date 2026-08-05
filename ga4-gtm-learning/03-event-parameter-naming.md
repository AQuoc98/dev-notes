# Subtask 03: Define Event and Parameter Naming Conventions

## Objective

Create a shared measurement language that represents business actions, aligns with GA4, and avoids names that are ambiguous or coupled to UI labels.

## Scope

- Event and parameter naming rules.
- Criteria for recommended versus custom GA4 events.
- Required/common parameters, allowed values, and data types.
- Custom dimensions, metrics, and key-event rules.
- Tracking plan template for the selected user journey.

## Work Items

1. Inventory current events and parameters in scope.
2. Map them to business questions and the target taxonomy.
3. Define lowercase/snake_case syntax, vocabulary, and discouraged terms.
4. Map business action → Data Layer event → GA4 event.
5. Define required parameters, types, values, and cardinality risks.
6. Create a tracking plan template with correct/incorrect examples.

## Deliverables / Outputs

- Event and parameter naming convention.
- Event taxonomy and controlled vocabulary.
- Completed tracking plan for the sample flow.
- Mapping/migration notes for non-compliant existing names.

## Expected Result

Events have stable business meaning, parameters are consistent and reportable, and duplicate meanings, UI-coupled names, and unnecessary cardinality are reduced.

## Acceptance Criteria

- [ ] Rules include correct/incorrect examples and exception handling.
- [ ] Every sample-flow event has a definition, trigger, and parameters.
- [ ] Each parameter has a type, required flag, allowed values, and source.
- [ ] Recommended GA4 events are used where appropriate; custom events are justified.
- [ ] PII, cardinality, and custom-definition requirements are assessed.

## Dependencies

- Measurement requirements and business questions.
- Data Layer specification.
- Access to current GA4 event data or a relevant export.

## Estimated Effort

**7 hours** — inventory/research 2h, convention 2.5h, template/mapping 1.5h, review 1h.

## Instructions / Answer

See [03-event-parameter-naming-answer.md](./03-event-parameter-naming-answer.md).

