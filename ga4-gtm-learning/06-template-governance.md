# 06 — GTM Template Governance

## Objective

Define when a GTM custom template is needed, how it is reviewed and tested, and how its dependent Tags/Variables are updated or retired safely.

## Scope

- Custom Tag Template and Custom Variable Template versus configured instances.
- Built-in, Community Template Gallery, and organization-owned template selection.
- Fields, validation, sandboxed JavaScript, APIs, permissions, endpoints, consent, ownership, versioning, and lifecycle.
- Dependency impact, unit tests, GTM Preview, Network validation, publish, rollback, and retirement.
- Two practical journeys: FD calculation_action with built-in Tags, and a separate non-GA4 endpoint case where a custom template is genuinely required.

Detailed Data Layer, Variable, Trigger, Tag, and Consent behavior is covered in Sections 01–05.

## Outputs

1. A template decision gate: approved requirement → built-in option → reviewed Community template → reviewed organization-owned template.
2. A template contract and inventory covering purpose, fields, validation, data, endpoints, consent, permissions, consumers, owner, version, and lifecycle.
3. A build/import, test, approval, publish, update-impact, rollback, and retirement workflow.
4. A Journey showing why FD calculation_action normally uses built-in Google/GA4 Tags.
5. Custom-template deployment guidance for a documented requirement that cannot use built-in options, including a team-created Deployment record (not a Google-provided form).

## Acceptance criteria

- No template is imported or built without an approved requirement and named owner.
- Permissions use least privilege and exact approved HTTPS endpoints.
- Sandbox code, data handling, consent behavior, success/failure paths, and dependent consumers are reviewed.
- Tests cover valid, missing, invalid, duplicate, denied-consent, timeout, and network-failure cases as applicable.
- Every update has impact analysis, an approved version, evidence, and a recoverable rollback/export record.
- A template is not used to infer business truth that belongs in the Application/Data Layer.
- The custom-template Deployment record identifies the built-in gap, approved fields, consent, exact endpoints, permissions, owner, version, and rollback.

## Out of scope

Detailed event, Variable, Trigger, Tag, consent-policy, measurement-plan, Debug/QA, report, or release design; use Sections 01–05 and 07–10. Advertising and campaign optimization are excluded.

## Source

Detailed implementation: [06-template-governance-answer.md](./06-template-governance-answer.md).
