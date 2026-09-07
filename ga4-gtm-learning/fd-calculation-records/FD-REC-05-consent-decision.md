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
| Tag dependency | [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| Status | **Completed — simulation documentation** |
| Value/evidence boundary | CMP, state and Tag behavior are simulated; real consent is not verified |
| Owner / reviewer | Privacy owner / Analytics owner — simulated aliases |
| Open items | Real CMP mapping, storage cleanup and revocation behavior remain out of scope |
| Next action | Use this decision as the consent input for a future runtime project |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` |

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
| Google consent type | `analytics_storage` |
| Default before user choice | `denied` |
| User grants analytics | `granted` |
| User rejects analytics | `denied` |
| CMP missing, delayed or unknown | Fail-safe `denied` |
| Consent Mode | Basic Consent Mode — simulated baseline |
| `ad_storage`, `ad_user_data`, `ad_personalization` | Out of scope |
| Consent Initialization | Sets the default before normal Tags are evaluated |
| Replay after consent update | Not allowed unless separately approved |

## 2. Behavior from state to Tag

| Consent state | Google tag | `calculation_action` Event tag | Destination |
|---|---|---|---|
| `denied` | Follows approved built-in behavior; no unauthorized analytics storage | Block/suppress according to approved analytics behavior | No analytics destination |
| `granted` | Allowed for the approved environment | Trigger may send one event per valid occurrence | QA or production by hostname |
| `unknown` / initialization failure | Fail-safe denied | Block/suppress | No analytics destination |
| Update denied → granted | Apply the approved update before a later event | Later events may send; earlier events are not replayed | Approved destination only |

Consent is permission context, not a business event. Do not push a fake `consent_granted` event to make a GA4 Event tag fire.

## 3. Implementation decisions

1. Use Consent Initialization for the default/update mechanism.
2. Use the Google tag's built-in consent behavior as the primary control.
3. Do not use an Exception Trigger to bypass consent.
4. Do not convert an unresolved state into `granted` with a GTM fallback.
5. Keep the business Trigger authoritative; consent only decides whether the Tag may proceed.
6. Record consent state beside any future runtime evidence.

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove real CMP or browser consent behavior.

- [x] Default, granted, denied and unknown states are defined.
- [x] `analytics_storage` is the only consent type in this GA4-only scope.
- [x] Consent Initialization precedes normal Tags in the simulated flow.
- [x] Denied/unknown does not send an unauthorized analytics event.
- [x] No fake consent event or exception bypass is used.
- [x] Real CMP and browser storage behavior are explicitly outside the current scope.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.

## 5. Cross-references

- Section 04 / [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md): Tag consent settings and destination behavior.
- Section 03 / [`FD-REC-03`](FD-REC-03-gtm-trigger-inventory.md): Trigger logic remains separate from consent permission.
- Section 08: future runtime verification must record consent state and request behavior.
- Section 10: a material consent change requires Release and Monitoring records.
