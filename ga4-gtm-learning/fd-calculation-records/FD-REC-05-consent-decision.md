# FD-REC-05 — Consent Decision Record

> **SIMULATED — Phase 3 documentation only.** This record defines the consent baseline for GA4 `calculation_action`; it does not verify a real CMP or production behavior.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-05` |
| Record name | Consent Decision Record |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — Consent integration |
| Standard reference | [Section 05 — Consent Management](../05-consent-answer.md), [`FD-REC-00`](FD-REC-00-phase-0-system-inventory.md), [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |
| Downstream Tag consumer | [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md); this consent decision must be approved before the Tag configuration is approved |
| Change reading status | **Required — affected** |
| Linked Change Request | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md) |
| Why this matters | `FD-CR-002` makes the missing regional, CMP lifecycle, revocation and evidence decisions explicit runtime release gates. |
| Status | **Design expanded — simulation documentation; runtime blocked by `FD-OPEN-003`** |
| Value/evidence boundary | CMP, state and Tag behavior are simulated; real consent is not verified |
| Owner / reviewer | Privacy owner / Analytics owner — simulated aliases |
| Open items | `FD-OPEN-003`: real region, CMP callback/update path, persistence key/expiry, revocation/storage cleanup, policy version and evidence owner require privacy approval before runtime |
| Next action | Use this decision as the consent input for a future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` — `FD-CR-002` |

## 0.1 Source of the record format

This is a project record using the standard Section 05 Consent Decision structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Consent contract and state decisions | [Section 05 — Consent Management](../05-consent-answer.md) |
| GTM implementation, Consent Initialization and Tag behavior | [Section 05 — GTM implementation](../05-consent-answer.md) |
| Project baseline and environment | [`FD-REC-00`](FD-REC-00-phase-0-system-inventory.md) |
| Event/data classification and GA4 boundary | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |

## 1. Consent contract

| Decision | Simulated value |
|---|---|
| Consent source of truth | `Strongtie Consent Manager — SIMULATED` |
| Purpose and destination | FD product analytics to the approved QA/production GA4 stream selected by hostname |
| Google consent type | `analytics_storage` |
| Region/banner scope | `[TBD — privacy approval required before runtime]`; do not infer it from the simulated UK property setting |
| Default before user choice | `denied` |
| User grants analytics | `granted` |
| User rejects analytics | `denied` |
| CMP missing, delayed or unknown | Fail-safe `denied` |
| Update source and timing | `[TBD approved CMP callback]`; update on the same page immediately after the user changes the analytics choice and before a later business event |
| Persistence | `[TBD CMP first-party consent-store key, scope and expiry]`; direct landing/refresh must restore the approved state without relying on a previous Data Layer message |
| Revocation and storage cleanup | Update `analytics_storage` to `denied` immediately; do not replay events; `[TBD CMP cookie/identifier cleanup and downstream deletion policy]` |
| Consent Mode / policy version | Basic Consent Mode — `FD-CONSENT-1.0-simulated`; real policy/implementation version pending |
| `ad_storage`, `ad_user_data`, `ad_personalization` | Out of scope |
| Consent Initialization | Sets the default before normal Tags are evaluated |
| Replay after consent update | Not allowed unless separately approved |
| Owner, approval and evidence | Privacy owner + Analytics owner; approval/evidence location `[TBD before runtime]` |

## 2. Behavior from state to Tag

| Consent state | Google tag | `calculation_action` Event tag | Destination |
|---|---|---|---|
| `denied` | Follows approved built-in behavior; no unauthorized analytics storage | Block/suppress according to approved analytics behavior | No analytics destination |
| `granted` | Allowed for the approved environment | Trigger may send one event per valid occurrence | QA or production by hostname |
| `unknown` / initialization failure | Fail-safe denied | Block/suppress | No analytics destination |
| Update denied → granted | Apply the approved update before a later event | Later events may send; earlier events are not replayed | Approved destination only |
| Update granted → denied | Apply the update immediately on the same page | Later events are suppressed; earlier events are not replayed or deleted by GTM | No later analytics destination; storage cleanup follows approved CMP policy |
| Direct landing / refresh | Restore the stored choice through the approved CMP path during Consent Initialization | Tag behavior follows the restored state without duplicate updates/events | Approved destination only when restored state is granted |

Consent is permission context, not a business event. Do not push a fake `consent_granted` event to make a GA4 Event tag fire.

## 3. Implementation decisions

1. Use Consent Initialization for the default/update mechanism.
2. Use the Google tag's built-in consent behavior as the primary control.
3. Do not use an Exception Trigger to bypass consent.
4. Do not convert an unresolved state into `granted` with a GTM fallback.
5. Keep the business Trigger authoritative; consent only decides whether the Tag may proceed.
6. Record consent state beside any future runtime evidence.
7. Review GTM Consent Overview and record the intentional built-in/additional-consent setting for every affected Tag.
8. Block runtime approval until region, update callback, persistence, revocation/cleanup, policy version and evidence ownership have non-placeholder values.

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove real CMP or browser consent behavior.

- [x] Default, granted, denied and unknown states are defined.
- [x] `analytics_storage` is the only consent type in this GA4-only scope.
- [x] Consent Initialization precedes normal Tags in the simulated flow.
- [x] Denied/unknown does not send an unauthorized analytics event.
- [x] No fake consent event or exception bypass is used.
- [x] Real CMP and browser storage behavior are explicitly outside the current scope.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
- [ ] `FD-OPEN-003` is resolved with approved region, update, persistence, revocation/cleanup and policy-version values.
- [ ] Consent Overview plus granted/denied/update/revocation/direct-landing/SPA/environment evidence is attached for runtime.
