# GA4/GTM Advanced Tracking — Learning Spike and Standardization

This folder separates Jira-ready task descriptions from their detailed answers/instructions. Each numbered item has two files: the base `.md` file is suitable for Jira, while the matching `-answer.md` file contains theory, examples, templates, execution steps, and completion checklists.

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
| `10` | `10-ga4-operations.md` | `10-ga4-operations-answer.md` |
| `11` | `11-release-monitoring.md` | `11-release-monitoring-answer.md` |

Vietnamese counterparts for the current advanced sections:

- `07`: `07-measurement-plan-answer-vn.md`
- `08`: `08-debug-qa-answer-vn.md`
- `09`: `09-reports-charts-answer-vn.md`
- `10`: `10-ga4-operations-answer-vn.md`

## Recommended Sequence

1. Data Layer design.
2. Variable management best practices.
3. Trigger design and lifecycle management.
4. Tag design and lifecycle management.
5. Consent management and governance.
6. Template selection and governance.
7. Measurement plan, event taxonomy, and parameter contract.
8. Debugging workflow and analytics QA.
9. GA4 report readiness, Reports, Explorations, charts, and interpretation.
10. GA4 property operations and common scenarios.
11. Release management and analytics monitoring.

## How to Use These Files

- Copy the base file for each prefix into the corresponding Jira task or subtask.
- Use its matching `-answer.md` file while completing the work.
- Start with `00`; use `01`–`06` for the GTM implementation foundation; use `07` to define the measurement contract; use `08` to validate collection; use `09` to build reports and Explorations; use `10` for property operations; and use `11` to release and monitor changes.
- Replace bracketed placeholders and complete each answer checklist with links to sanitized evidence.
- Re-check linked official Google documentation during implementation because platform limits and interfaces can change.

## References

- [Google Analytics Help](https://support.google.com/analytics/)
- [Google Analytics Developer Documentation](https://developers.google.com/analytics)
- [Google Tag Manager Help](https://support.google.com/tagmanager/)
