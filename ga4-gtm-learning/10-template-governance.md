# Subtask 10: Govern GTM Templates

## Objective

Define how built-in, Community Template Gallery, and custom GTM tag/variable templates are selected, reviewed, tested, updated, and retired.

## Scope

- Built-in tag and variable templates.
- Community Template Gallery imports.
- Container custom tag and variable templates.
- Permission, provenance, security, update, ownership, and dependency review.

## Work Items

1. Inventory non-built-in templates and the tags/variables that use them.
2. Establish a selection hierarchy and approval criteria.
3. Define source, permission, code, test, update, and rollback reviews.
4. Test an approved template in a non-production workspace and document evidence.
5. Define monitoring, ownership, version review, and retirement rules.

## Deliverables / Outputs

- Template governance and selection guideline.
- Template/dependency inventory.
- Security and permission review checklist.
- Update and rollback procedure.

## Expected Result

The team favors supported templates, understands imported code and permissions, tests updates before acceptance, and can identify every dependent tag or variable.

## Acceptance Criteria

- [ ] Built-in, Gallery, and custom templates are clearly distinguished.
- [ ] Every non-built-in template has a source, owner, purpose, permissions review, consumers, and review date.
- [ ] Custom template code and tests receive technical/security review.
- [ ] Updates are tested in a workspace before being accepted and published.
- [ ] A rollback or replacement path is documented.

## Dependencies

- Scoped tag and variable inventory.
- GTM edit access and an approved non-production test destination.
- Security/privacy reviewer when a template requests sensitive permissions.

## Estimated Effort

**6 hours** — inventory 1h, governance 2h, security/code review 1.5h, testing/review 1.5h.

## Instructions / Answer

See [10-template-governance-answer.md](./10-template-governance-answer.md).
