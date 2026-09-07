# FD-REC-06 — Template Governance Decision

> **SIMULATED — Phase 3 documentation only.** This record decides the template approach for the GA4 `calculation_action` flow; it does not import, build or publish a custom template.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-06` |
| Record name | Template Governance Decision |
| Document type | `PROJECT RECORD` |
| Phase | Phase 3 — Template governance |
| Source of truth | [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md), [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md) |
| Change reading status | **Context only — not changed** |
| Linked Change Requests | [`FD-CR-002`](FD-CR-002-runtime-readiness-hardening.md), [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) — context only |
| Why this matters | Both changes remain within native Variables/Tags and introduce no custom template. No CR reading is required for normal template-governance use. |
| Status | **Completed — simulation documentation** |
| Decision | Native Google tag + native GA4 Event tag; no custom template |
| Value/evidence boundary | Governance decision is simulated; no Template Editor or dependency evidence exists |
| Owner / reviewer | GTM owner / Security-Privacy reviewer — simulated aliases |
| Open items | Re-open only when a future requirement is outside native Tag capability |
| Next action | Keep the GA4 path on native Tags; create a separate template record only for a real future requirement |
| Template use | Copy this record for a project; reset simulated values, statuses, dates, approvals and checklist marks before use |
| Last updated | `2026-09-07` |

## 0.1 Source of the record format

This is a project record using the standard Section 06 Template Governance structure. It is not a Google-provided form.

| Record component | Reference |
|---|---|
| Template contract, review and implementation workflow | [Section 06 — Template Governance](../06-template-governance-answer.md) |
| Native Tag capability and Tag contract | [Section 04 — Tag Management](../04-tag-management-answer.md) |
| Consent and change-control dependency | [Section 05 — Consent Management](../05-consent-answer.md) |
| Current FD requirement and Tag inventory | [`FD-REC-04`](FD-REC-04-gtm-tag-inventory.md), [`FD-REC-07`](FD-REC-07-measurement-plan-event-contract.md) |

## 1. Requirement and capability check

| Requirement | Native capability | Decision |
|---|---|---|
| Configure an approved GA4 destination | Google tag with reviewed Measurement ID routing | Native Google tag |
| Send `calculation_action` | GA4 Event tag with approved parameters | Native GA4 Event tag |
| Apply analytics consent | Built-in Google tag consent behavior and approved Consent Mode | Native behavior |
| Map Data Layer scalars | Data Layer Variables + GA4 Event tag fields | Native Variables/Tag |
| Send to a separate non-GA4 endpoint | Not required by the current FD scope | Out of scope; separate future decision |
| Calculate `solution_found` | Must remain in the Application | Never implement in a template |

## 2. Governance decision

The current FD GA4 requirement does not justify a custom Tag or Variable Template. A custom template would add code, permissions, dependencies and rollback surface without solving a current capability gap.

```text
Approved Application/Data Layer contract
  → native Data Layer Variables
  → authoritative Custom Event Trigger
  → native Google tag + GA4 Event tag
  → approved consent and destination behavior
```

No Community Template is imported. No Custom HTML or Custom JavaScript is used for this GA4 path.

## 3. Conditions for a future template exception

Re-open this decision only when all of the following are documented:

- The requirement cannot be met by a built-in Tag, Variable or simple configuration.
- The destination, payload allowlist, expected count and consent policy are approved.
- The template source, version, owner, permissions, endpoints and dependent consumers are known.
- Unit tests, Preview, Network and rollback/export evidence are defined.
- Security and privacy review accepts the sandbox permissions and data handling.

If a future destination is a separate internal endpoint, create a new template deployment record under Section 06. Do not change the meaning or delivery path of the GA4 Event tag.

## 4. Acceptance checklist / template criteria

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove a custom template was reviewed or deployed.

- [x] Built-in Google tag and GA4 Event tag satisfy the approved FD GA4 requirement.
- [x] No custom template is imported or built for `calculation_action`.
- [x] The Application remains the owner of business outcome and snapshot logic.
- [x] Template exception criteria are documented for future scope changes.
- [x] No template permission, endpoint or version is claimed as configured.
- [x] Simulation boundary is explicit and no runtime Pass is claimed.
