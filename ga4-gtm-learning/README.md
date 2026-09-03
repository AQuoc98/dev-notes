# GTM Management and GA4 Validation — Learning and Standardization

This folder separates short task briefs from detailed implementation guides. Each numbered item has a base `.md` file for a project task or checklist and a matching `-answer.md` file that contains definitions, examples, templates, execution steps, and completion criteria.

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

1. Start with `00` for the operating model and team vocabulary.
2. Learn `07` first so every implementation starts with an approved business question and event contract.
3. Learn the application-owned Data Layer and frontend adapter pattern in `01`.
4. Learn GTM Variables, Triggers, and Tags in `02`–`04`.
5. Add Consent and Template Governance from `05`–`06` when the container uses those capabilities.
6. Practice application tests, GTM Preview, Network inspection, and GA4 verification in `08`.
7. Learn report readiness and interpretation in `09`.
8. Learn workspace, version, release, rollback, and monitoring practices in `10`.

### Execution order for a real tracking change

1. Define a lightweight requirement/event contract using `07`.
2. Implement the application signal and Data Layer contract using `01`.
3. Configure or reuse Variables, Triggers, and Tags using `02`–`04`.
4. Apply Consent or Template controls from `05`–`06` when relevant.
5. Test from application code through Data Layer, GTM, Network, and GA4 using `08`.
6. Confirm reporting impact with the relevant parts of `09`.
7. Release and monitor through `10`.

## First-project baseline

Use the following as the default team process until the project defines a stricter one:

1. Select one journey, application environment, GTM container/workspace, GA4 property/stream, and named owners.
2. Complete the Section `07` requirement and Event Contract before editing GTM.
3. Implement the application/Data Layer handoff in `01`; configure only approved Variables, Triggers, Tags, Consent controls, and Templates in `02`–`06`.
4. Run Section `08` QA in a non-production destination. Do not publish when the first failing layer, prohibited data, wrong destination, or duplicate key event is unresolved.
5. Build or update Section `09` reports/Explorations only after fields and collection evidence are ready.
6. Create the Section `10` Release Record, obtain approval, publish the named version to the intended environment, and run the production smoke test.
7. Complete the Monitoring Record, processed-data check, affected-period assessment, and closure or incident follow-up.

## Minimum record set by change type

| Change type | Required records | Conditional records |
|---|---|---|
| New or changed event | `07` Event Contract, `01` Data Layer, `02`–`04` implementation, `08` QA, `10` Release + Monitoring | `05` Consent, `06` Template, `09` Report/Exploration |
| Variable, Trigger, or Tag only | `02`/`03`/`04` inventory, `08` QA | `07` when event meaning, destination, consent, or schema changes; `10` when the change is material |
| Consent or privacy change | `05` Consent record, `08` QA, `10` Release + Monitoring | `04` Tag impact, `07` decision, `09` data-quality impact |
| Custom template | `06` Template + Deployment records, `08` QA, `10` Release + Monitoring | `01`–`05` according to consumers |
| Report or Exploration only | `09` Report Requirement, Field Readiness, Asset Configuration, Interpretation/Decision | `10` Release Record when the change is material |

## Team operating rules

- One source of truth per decision: `07` owns event meaning, `08` owns runtime evidence, `09` owns report configuration, and `10` owns release/monitoring outcome.
- Keep development/QA destinations separate from production; an unknown hostname must fail safely.
- Keep workspaces small and independently testable; do not mix unrelated cleanup or hotfixes.
- Assign `Low`, `Medium`, or `High` risk before choosing approval depth and monitoring scope.
- Production smoke tests use an approved synthetic/test identity or allowlist; never use real customer data for validation.
- Use `N/A` with a reason for an unaffected layer; never leave a required record ambiguous.
- Sanitize evidence. Do not store PII, secrets, raw form values, credentials, or unapproved request tokens.
- Use `Pending` only for a documented GA4 processing window; assign an owner and follow-up date.
- Record the affected period even after a successful rollback. GTM rollback does not repair historical GA4 data.

## Common record statuses

Use one status vocabulary across project records:

`Draft` → `Review` → `Approved` → `In QA` → `Published`/`Monitoring` → `Closed`

- `Pending` means collection or implementation is complete but a documented dependency, such as GA4 processing, is still open; include an owner and follow-up date.
- `Blocked` means a release or test cannot proceed because a required decision, evidence, access, privacy condition, or defect is unresolved.
- `Exception` means an approved non-blocking risk with owner, mitigation, reviewer, and due date.
- For assets, use `Active`, `Deprecated`, and `Retired` after the operational status above.

## How to Use These Files

- Copy the base file for each prefix into the corresponding project task or issue tracker (Jira is optional).
- Use its matching `-answer.md` file while completing the work.
- Start with `00`, then use the detailed source answer for the GTM or GA4 area being implemented.
- Replace bracketed placeholders and complete each answer checklist with links to sanitized evidence.
- Re-check linked official Google documentation during implementation because platform limits and interfaces can change.

## References

- [Google Analytics Help](https://support.google.com/analytics/)
- [Google Analytics Developer Documentation](https://developers.google.com/analytics)
- [Google Tag Manager Help](https://support.google.com/tagmanager/)
