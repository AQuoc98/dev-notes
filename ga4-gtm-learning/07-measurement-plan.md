# 07 — GA4/GTM Measurement Plan

## Objective

Define before implementation what business question to measure, which business moment is authoritative, how the event and parameter contract works, how valid occurrences are deduplicated, which consent and destination rules apply, and who owns the schema version.

This is a design and approval document; it is not a runtime Debug/QA guide or a Reports/Charts build guide.

## Scope

- Stable client-side web collection with Google Tag Manager (GTM) and Google Analytics 4 (GA4).
- Business questions, authoritative moments, event type/name, parameter schema, occurrence, deduplication, consent/privacy, and destination.
- Measurement decisions for Data Layer/GTM handoff, key events, custom definitions, identity, cardinality, and data minimization.
- Canonical records: Project Context, Journey/Event Coverage, Event Contract, Parameter Dictionary, Traceability, Decision Record, Consent/Data Classification, and Schema Lifecycle.
- Handoff requirements for Sections 01–06 and the worked Registration Journey.

## Outputs

1. A standard workflow: business question → authoritative moment → event/parameter contract → Data Layer/GTM handoff → review and version.
2. An Event Contract, Parameter Dictionary, mapping, consent, destination, owner, and lifecycle status for each canonical event, applied in the documented P0–P2 priority order.
3. Explicit decisions for key events, custom definitions, identity, and data classification where required.
4. A handoff that Section 08 can test and Section 09 can use for Reports/Charts without redefining the event.
5. A reproducible Registration Journey at the end of the detailed answer.

## Acceptance criteria

- Every event has a business question and an authoritative Application or server moment.
- One valid occurrence produces one message under documented count/deduplication rules; validation failure, timeout, cancellation, and server failure are separate outcomes.
- Required parameters have documented types, allowed values, and missing-data behavior; optional parameters are not invented.
- The Data Layer message is self-contained; GTM maps only approved scalar fields and has an authoritative Trigger, consent behavior, and destination.
- PII, secrets, raw input, and request tokens are excluded from GA4 unless covered by a separate approved contract.
- Owner, reviewer, schema version, effective date, and migration/retirement status are recorded.
- Debug/QA and Reports/Charts are referenced but their execution procedures are not embedded in this plan.

## Out of scope

Detailed Data Layer, Variable, Trigger, Tag, Consent, and Template configuration; see Sections 01–06. Runtime Debug/QA is covered in Section 08, Reports/Charts in Section 09, and Release Monitoring in Section 10. Ads, campaign, attribution, and Google Ads operations are excluded.

## Source

Detailed implementation: [07-measurement-plan-answer.md](./07-measurement-plan-answer.md).
Vietnamese counterpart: [07-measurement-plan-answer-vn.md](./07-measurement-plan-answer-vn.md).
