# GTM Management and GA4 Validation — Learning and Standardization

This folder separates Jira-ready task descriptions from their detailed answers/instructions. Each numbered item has two files: the base `.md` file is suitable for Jira, while the matching `-answer.md` file contains theory, examples, templates, execution steps, and completion checklists.

This curriculum is written for frontend developers who manage GTM across team projects. It focuses on stable, maintainable web practices: clean Data Layer contracts, governed Variables/Triggers/Tags, consent, templates, QA, release, and monitoring. GA4 is the downstream validation layer when a GTM change affects collection, custom definitions, key events, DebugView, reports, or Explorations. Google Ads is mentioned only when it is a downstream consumer or affects consent and measurement integrity; media buying, campaign optimization, and Ads operations are outside scope. App/Firebase, server-side GTM, and offline/Measurement Protocol implementation are also outside the core scope unless a project explicitly adds them.

## Document Structure

| Prefix | Jira description | Answer / instructions |
| ------ | ---------------- | --------------------- |
| `00` | `00-main-task.md` | `00-main-task-answer.md` |
| `01` | `01-data-layer-design.md` | `01-data-layer-design-answer.md` |
| `02` | `02-variable-management.md` | `02-variable-management-answer.md` |
| `03` | `03-trigger-management.md` | `03-trigger-management-answer.md` |
| `04` | `04-tag-management.md` | `04-tag-management-answer.md` |
| `05` | `05-consent.md` | `05-consent-answer.md` |
| `06` | `06-template-governance.md` | `06-template-governance-answer.md` |
| `07` | `07-measurement-plan.md` | `07-measurement-plan-answer.md` |
| `08` | `08-debug-qa.md` | `08-debug-qa-answer.md` |
| `09` | `09-reports-charts.md` | `09-reports-charts-answer.md` |
| `10` | `10-release-monitoring.md` | `10-release-monitoring-answer.md` |

Vietnamese counterparts are available for Sections `01`–`10` using the `-answer-vn.md` suffix. Section `00` currently has an English answer only.

## Recommended Sequences

### Learning order for frontend developers

1. Start with `00` for the operating model.
2. Learn the application-owned Data Layer and frontend adapter pattern in `01`.
3. Learn GTM Variables, Triggers, and Tags in `02`–`04`.
4. Practice application tests, GTM Preview, Network inspection, and GA4 verification in `08`.
5. Add Consent and Template Governance from `05`–`06` when the container uses those capabilities.
6. Learn workspace, version, release, rollback, and monitoring practices in `10`.
7. Use `07` and `09` as supporting GA4 references for event contracts, custom definitions, reporting readiness, and downstream impact.

### Execution order for a real tracking change

1. Define a lightweight requirement/event contract using `07`.
2. Implement the application signal and Data Layer contract using `01`.
3. Configure or reuse Variables, Triggers, and Tags using `02`–`04`.
4. Apply Consent or Template controls from `05`–`06` when relevant.
5. Test from application code through Data Layer, GTM, Network, and GA4 using `08`.
6. Confirm reporting impact with the relevant parts of `09`.
7. Release and monitor through `10`.

## How to Use These Files

- Copy the base file for each prefix into the corresponding Jira task or subtask.
- Use its matching `-answer.md` file while completing the work.
- Start with `00`, then use the detailed source answer for the GTM or GA4 area being implemented.
- Replace bracketed placeholders and complete each answer checklist with links to sanitized evidence.
- Re-check linked official Google documentation during implementation because platform limits and interfaces can change.

## References

- [Google Analytics Help](https://support.google.com/analytics/)
- [Google Analytics Developer Documentation](https://developers.google.com/analytics)
- [Google Tag Manager Help](https://support.google.com/tagmanager/)
