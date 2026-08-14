# 10 — GTM Template Governance

## Theory

A template defines the configuration UI and sandboxed implementation for a GTM **tag type** or **variable type**. A tag or variable is an instance configured from that template. The Templates area lists custom/imported tag and variable templates in the container; it is not a list of every configured tag.

Use this selection order:

1. A Google-provided built-in template that meets the requirement.
2. A carefully reviewed Community Template Gallery template.
3. A custom template with a named maintainer, code/security review, and tests.
4. Custom HTML or Custom JavaScript only as a documented exception when safer supported options cannot meet the requirement.

Sandboxing reduces risk but does not make third-party code automatically trustworthy. Requested permissions determine what a template can access or do and must be reviewed against least privilege.

## Review Checklist

For every non-built-in template, record:

- Business need and why a built-in template is insufficient.
- Source repository/publisher, documentation, license, and maintenance activity.
- Imported version or commit and date.
- Requested permissions and whether each is necessary.
- Network endpoints, data read/sent, privacy and consent behavior.
- Code reviewer, security/privacy reviewer, owner, and dependent tags/variables.
- Template tests, Preview/network evidence, failure behavior, and rollback plan.

Never include secrets in template code or fields, and never approve collection of prohibited personal data.

## Inventory Template

| Template | Tag/variable | Origin/source | Version | Permissions/endpoints | Consumers | Owner | Last review | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `[name]` | Tag | Gallery / `[repo]` | `[SHA/version]` | `[summary]` | `[tags]` | `[owner]` | YYYY-MM-DD | Approved |

## Test and Update Workflow

1. Import or edit only in a dedicated non-production workspace.
2. Review the diff, code, fields, permissions, endpoints, and tests before creating consumers.
3. Add tests for normal, missing, invalid, denied-consent, and failure cases where applicable.
4. Validate the dependent tag/variable in Preview and inspect resulting network behavior.
5. Record approval and publish through normal version controls.
6. Treat a Gallery update as a new code change: read its change details, review new permissions/code, retest consumers, then accept and publish.
7. If retiring a template, inventory and migrate every dependent tag/variable before removal; retain a version/export for rollback.

## Custom Template Minimum Standard

- Clear field labels, validation, help text, and safe defaults.
- Only necessary sandboxed APIs and narrowly scoped permissions.
- Explicit success and failure behavior for tag templates.
- Automated template tests for permission and input boundaries.
- Maintainer, repository/source record, change history, and review cadence.

## Completion Checklist

- [ ] All non-built-in templates and consumers are inventoried.
- [ ] Selection rationale and least-privilege review are recorded.
- [ ] Code, endpoints, data handling, consent, and failure behavior are reviewed.
- [ ] Template tests and end-to-end Preview/network tests pass.
- [ ] Updates require review rather than automatic acceptance.
- [ ] Ownership, monitoring, rollback, and retirement paths exist.
- [ ] No secrets or prohibited personal data are exposed.

## Official References

- [Custom templates](https://support.google.com/tagmanager/answer/9334084)
- [Custom templates developer guide](https://developers.google.com/tag-platform/tag-manager/templates)
- [Community Template Gallery](https://developers.google.com/tag-platform/tag-manager/templates/gallery)
- [Custom template permissions](https://developers.google.com/tag-platform/tag-manager/templates/permissions)
- [Custom template tests](https://developers.google.com/tag-platform/tag-manager/templates/tests)
