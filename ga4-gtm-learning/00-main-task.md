# Parent Task: Research and Standardize GA4/GTM Tracking Best Practices

## Objective

Research and standardize the GA4/GTM measurement lifecycle—from application interaction and Data Layer events through GTM processing and GA4 collection to validation, reporting, visualization, and interpretation. The outcome must include reusable documentation, a verified proof of concept, and a report/chart example based on validated data.

## Scope

### In Scope

- Data Layer design for a website or SPA.
- GTM variable management.
- GTM tag design, firing controls, sequencing, consent, and lifecycle management.
- GTM folder strategy for organizing tags, triggers, and variables.
- GTM built-in, Gallery, and custom tag/variable template governance.
- GTM trigger design, timing, filtering, testing, and lifecycle management.
- GA4 property operations and common measurement scenarios.
- Event and parameter naming conventions.
- Debugging and analytics QA.
- Audit of one existing tracking flow.
- Proof of concept for one representative user journey.
- GA4 report readiness, standard/detail report configuration, Explorations, and chart selection.
- Interpretation and QA of one reusable report and one analysis-oriented Exploration.
- Documentation, review, and knowledge sharing.

### Out of Scope

- Full migration of the production container.
- External BI dashboards (for example, Looker Studio or Power BI).
- A complete production reporting suite for every stakeholder.
- Remediation of every issue discovered during the audit.
- Full Consent Management Platform design.
- Server-side tagging unless separately approved.

## Work Items

1. Select the target application, environment, and representative tracking flow.
2. Complete all twelve subtasks and store their deliverables.
3. Review the materials with development, QA, and analytics stakeholders.
4. Consolidate decisions, limitations, unresolved issues, and rollout recommendations.
5. Run a short knowledge-sharing session and capture feedback.

## Deliverables / Outputs

- Data Layer specification with example payloads.
- GTM variable management guideline and inventory template.
- GTM tag design guideline and tag inventory template.
- GTM folder taxonomy and organization checklist.
- GTM template selection, security-review, testing, and update guideline.
- GTM trigger design, inventory, and lifecycle guideline.
- GA4 property operations and common-scenario runbook.
- Event/parameter naming convention and tracking plan template.
- Debugging workflow and analytics QA checklist.
- Tracking flow audit report with prioritized findings.
- Tested proof of concept with supporting evidence.
- Report requirements matrix, one reusable GA4 detail report, and one Exploration with appropriate charts.
- Report/chart QA evidence and a short interpretation guide.
- Final summary, adoption recommendation, and rollout plan.

## Expected Result

- The team shares a consistent understanding of the data flow from the application to GA4.
- New tracking is structured, testable, and maintainable.
- Duplicate variables, hidden dependencies, and ambiguous naming are reduced.
- A repeatable QA process is available before publishing changes.
- The POC demonstrates that the proposed standards work in practice.
- Stakeholders can answer an agreed business question using a trustworthy report and an appropriate chart.
- The team understands dimensions, metrics, scope, filters, comparisons, segments, chart choice, and common GA4 reporting limitations.
- Audit findings are converted into a prioritized remediation backlog.

## Acceptance Criteria

- [ ] All twelve subtasks meet their individual acceptance criteria.
- [ ] Every deliverable is stored in a shared location with an owner and version.
- [ ] The POC successfully tracks the selected journey from the Data Layer to GA4 DebugView.
- [ ] Validated POC data is available in GA4 reporting and is used in the report/chart examples.
- [ ] No test payload sends PII or other sensitive data.
- [ ] Development and QA have reviewed the materials; material feedback is resolved or recorded.
- [ ] Recommendations are prioritized as Must/Should/Could and include rollout steps.
- [ ] Knowledge-sharing notes or a recording link are available.

## Dependencies

- Appropriate GTM workspace access and GA4 property access.
- GA4 Editor or Administrator access for report customization, or support from someone with that role.
- Test environment, GTM Preview/Tag Assistant, and GA4 DebugView.
- Read access to the implementation or support from its developers.
- Stakeholder confirmation of the user journey, business meaning, and expected data.
- Reviewers from development, QA, and analytics.

## Estimated Effort

**102 hours over approximately 10–11 weeks**:

- 98 hours for the twelve subtasks.
- 4 hours for kickoff, cross-review, consolidation, and knowledge sharing.

This estimate excludes access delays, standard-report processing time, and broad production rollout.

## Instructions / Answer

See [00-main-task-answer.md](./00-main-task-answer.md).
