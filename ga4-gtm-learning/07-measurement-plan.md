# 07 — Measurement Plan for GA4/GTM

## Objective

Create a practical Measurement Plan that connects a business question to a maintainable event contract, Data Layer signal, GTM mapping, consent/privacy behavior, QA evidence, and reporting requirement.

The answer should help frontend developers and GTM owners decide what to measure, when an event is true, which parameters are allowed, and how the change will be reviewed and maintained.

## Scope

### Included

- Stable web client-side collection through GTM and GA4.
- Business decision, authoritative business moment, event naming/type, parameters, schema, occurrence, deduplication, and ownership.
- Data Layer, variables, triggers, tags, Google tag routing, environment destination, consent, privacy, cardinality, and custom-definition decisions.
- Canonical records: Project Context/Baseline, Journey/Event Coverage Matrix, Event Contract, Parameter Dictionary, Traceability Matrix, Consent/Data Classification Matrix, Key-Event/Custom-Definition Decision Record, and Schema Lifecycle Register.
- Practical frontend/GTM handoff, ecommerce addendum, anti-patterns, and links to Section 08 QA and Section 09 reporting.
- A single completed Registration Journey example placed at the end of the answer documents.
- Matching English and Vietnamese structure and terminology.

### Excluded

- Media buying, campaign optimization, attribution strategy, and Google Ads operations.
- Building or operating a complete automated analytics framework.
- Redefining the product Measurement Plan, consent policy, or reporting requirements outside this section.
- Treating a GA4 report as the source of truth for transactional accounting.
- Release approval and post-release monitoring, which remain in Section 10.

## Outputs

- [English answer](./07-measurement-plan-answer.md): cleaned and reordered as Overview → Measurement-Plan workflow → Canonical records/templates → Implementation handoff/practical notes → Registration Journey example → Official references; the Journey instantiates the canonical records in Section 3.
- [Vietnamese answer](./07-measurement-plan-answer-vn.md): same structure, scope, canonical-record coverage, and GA4/GTM terminology.
- A lean set of canonical planning records, with derived views clearly separated from source-of-truth records.
- A worked Registration Journey showing how business meaning, event contracts, parameters, consent, mapping, reporting, traceability, and approval connect without adding ad-focused content.
