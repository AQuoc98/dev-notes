# Subtask 02: Standardize GTM Variable Management

## Objective

Define how GTM variables are created, named, reused, tested, and retired so the container remains understandable and avoids duplication or hidden dependencies.

## Scope

- Built-in and user-defined variables.
- Data Layer Variables, Constants, Lookup Tables, RegEx Tables, and Custom JavaScript.
- Naming, folders, ownership, documentation, and lifecycle.
- Preference for Data Layer Variables over DOM scraping.
- Inventory for the selected flow/POC, not a full-container refactor.

## Work Items

1. Inventory relevant variables and map their consumers.
2. Classify variables by source, purpose, environment, and risk.
3. Define naming prefixes such as `DLV -`, `CONST -`, `LUT -`, and `JS -`.
4. Define when Custom JavaScript is allowed and how it must be reviewed and tested.
5. Document reuse, deprecation, ownership, and description rules.
6. Create before/after examples and a variable review checklist.

## Deliverables / Outputs

- Variable management guideline.
- Inventory template: name, type, source, consumers, owner, and status.
- Naming matrix and variable-type decision guide.
- List of duplicate or risky variables in the selected flow.

## Expected Result

Variables are searchable and traceable, shared logic is reused, and Custom JavaScript and DOM dependencies are controlled.

## Acceptance Criteria

- [ ] The guideline covers naming, descriptions, folders, reuse, testing, and deprecation.
- [ ] The inventory identifies unused, duplicate, or risky variables in scope.
- [ ] Selection criteria exist for Data Layer Variables, Lookup Tables, and Custom JavaScript.
- [ ] Every POC variable follows the guideline and has a description.
- [ ] A reviewer can trace each variable to its source and consuming tags/triggers.

## Dependencies

- Draft Data Layer contract.
- Read access to the GTM container/workspace.
- Tag and trigger inventory for the selected flow.

## Estimated Effort

**8 hours** — inventory 2h, guideline 3h, examples/template 1.5h, review 1.5h.

## Instructions / Answer

See [02-variable-management-answer.md](./02-variable-management-answer.md).
