# Subtask 02: Manage GTM Variables

## Objective

Define a practical system for creating, naming, choosing, reusing, testing, documenting, and retiring GTM variables across projects.

## Scope — Included Items

- What GTM variables are, how Tags and Triggers use them, and how Data Layer values are evaluated.
- The key management principles: clear purpose, correct type, clear naming, reuse, Data Layer first, simple transformations, environment separation, missing-data behavior, privacy, testing, and cleanup.
- Data Layer Variables, Constants, Lookup Tables, RegEx Tables, URL Variables, cookies, DOM variables, and Custom JavaScript.
- Multi-project scope, namespaces, folders, shared versus project-specific variables, ownership, and a central inventory.
- Variable contracts, nested Data Layer paths, allowed values, fallbacks, consent, environment routing, and privacy controls.
- Custom JavaScript restrictions, testing, review, publishing, deprecation, and retirement.
- The scoped FD calculation example and audit cases; not a full-container refactor.

## Deliverables / Outputs

- One GTM variable-management guideline covering Data Layer evaluation, variable principles, types, naming, folders, ownership, reuse, environments, consent, testing, publishing, and lifecycle.
- One variable inventory containing scope, name, type, exact source, business meaning, consumers, owner, environment, fallback, privacy classification, risk, and lifecycle status.
- One variable-type decision matrix and lookup/RegEx safeguards with practical examples.
- One step-by-step FD calculation variable-management example covering the Data Layer contract, inventory search, canonical variables, routing, tag mapping, missing data, testing, and publishing.
- One audit section covering duplicates, DOM dependencies, environment leaks, masked missing values, unused variables, risky Custom JavaScript, and temporary variables.

## Instructions / Answer

See [02-variable-management-answer.md](./02-variable-management-answer.md).
