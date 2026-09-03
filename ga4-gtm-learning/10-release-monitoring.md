# 10 — GTM Release Management and GA4 Monitoring

## Objective

Move a material GTM/GA4 change from an approved requirement to a controlled release, observation window, incident response, or documented closure.

## Scope

- Change classification, ownership, workspace/environment/version control, approval, publication, smoke testing, monitoring, containment, and rollback.
- Release Record and Monitoring Record linked to Sections 01–09.
- Practical checks for event volume, duplicate/missingness, parameter quality, destination, consent, processed reports, and data quality.
- Release gates 0–4: requirement, implementation, QA, publish, and post-publish readiness.
- Risk/status classification, monitoring cadence, escalation channel, and evidence access/retention.
- Registration release journey as the worked example at the end of the detailed answer.

## Outputs

1. A minimal, traceable Release Record with requirement, implementation, QA, report impact, version, environment, approval, rollback, and owner references.
2. A Monitoring Record with signal definitions, population/grain/scope, baseline, thresholds, observation window, escalation, and decision.
3. A practical release flow: workspace → Preview/QA → named version → approved publication → production smoke test → processed-data validation → Go/Hold/Close or Incident.
4. An incident and rollback runbook that distinguishes GTM recovery from Application, consent, GA4 property, and historical-data problems.
5. Minimum versus optional monitoring signals, with calibrated baseline, thresholds, cadence, escalation, and evidence retention.
6. Cross-references to the source-of-truth records in Sections 01–09; no duplicate QA matrix, measurement contract, report formula, or template internals.

## Acceptance criteria

- The change has an approved requirement, owner, affected journey, target environment/property, expected event/count, destination, consent impact, and rollback/mitigation path.
- The Release Record contains risk level, status, approvers, publisher, evidence location/access, and retention where applicable.
- Section 08 runtime evidence and Section 09 report impact are linked when applicable.
- A named version is approved and published only to the intended environment; production smoke evidence uses an approved safe test method and records payload, count, destination, and consent.
- Monitoring covers both transport and semantic failure, uses a calibrated baseline, defines cadence/escalation, and keeps processed-data checks pending only within a documented processing window.
- Incident closure records severity, affected period, containment/rollback, residual data impact, and corrective follow-up.

## Out of scope

Event contracts and measurement design are owned by Section 07. Detailed implementation and Debug/QA are owned by Sections 01–06 and 08. Reports/Charts design is owned by Section 09. Ads, campaign optimization, attribution operations, and external BI are excluded.

## Source

- Detailed English implementation: [10-release-monitoring-answer.md](./10-release-monitoring-answer.md).
- Vietnamese implementation: [10-release-monitoring-answer-vn.md](./10-release-monitoring-answer-vn.md).
