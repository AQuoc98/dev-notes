# 01 — Data Layer Design

## Objective

Define how the frontend application publishes one reliable, privacy-safe business event that GTM can read and GA4 can receive.

The Application owns business truth, the Data Layer carries the structured message, GTM maps and routes approved fields, and GA4 receives and processes the event.

## Scope

- Business-event naming and valid-occurrence rules.
- A self-contained, versioned event contract.
- Field types, allowed values, required/optional fields, and missing-data behavior.
- Application-owned snapshots for asynchronous API flows and SPA safety.
- Data minimization, consent boundary, duplicate prevention, and handoff to GTM.
- FD calculation_action as the reference pattern.

## Outputs

1. A simple Application → Data Layer → GTM → GA4 lifecycle.
2. A contract record for event name, schema version, fields, types, allowed values, source, timing, and privacy classification.
3. A complete dataLayer.push message for each valid occurrence.
4. Frontend adapter and contract-test guidance for asynchronous responses and duplicate callbacks.
5. A privacy-safe FD calculation_action Journey and validation checklist.

## Acceptance criteria

- The event describes a confirmed business fact, not a click or UI state.
- One valid occurrence produces one complete message.
- A valid no-output result is distinct from invalid input, timeout, cancellation, or server failure.
- The message contains only approved fields and no unnecessary PII, secrets, or unrestricted input.
- GTM receives stable scalar fields; business logic remains in the Application.

## Out of scope

Detailed GTM Variable, Trigger, Tag, consent, reporting, or release configuration; use Sections 02–05 and 07–10. Advertising and campaign optimization are excluded.

## Source

Detailed implementation: [01-data-layer-design-answer.md](./01-data-layer-design-answer.md).
