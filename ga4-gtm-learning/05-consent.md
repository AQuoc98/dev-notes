# Jira Work Request: Research Consent Management and Governance in GTM

## Objective

Research and document how consent should be collected, communicated, enforced, tested, and governed in Google Tag Manager (GTM) for GA4, Google Ads, and approved third-party Tags.

The guide must explain how the Consent Management Platform (CMP), Consent Mode, GTM consent settings, Triggers, Tags, and downstream destinations work together. Privacy or legal owners must approve the policy and regional defaults.

## Scope

- Explain the three consent responsibilities: obtain, communicate, and enforce.
- Distinguish consent from Trigger logic and Tag execution.
- Explain Basic and Advanced Consent Mode, including why `denied` does not always mean zero Network requests.
- Define the states `granted`, `denied`, and unknown/uninitialized.
- Document the main consent types:
  - `analytics_storage`;
  - `ad_storage`;
  - `ad_user_data`;
  - `ad_personalization`;
  - `functionality_storage`;
  - `personalization_storage`;
  - `security_storage`.
- Separate storage consent from advertising data-use consent.
- Define the correct order: Consent Initialization → consent defaults/CMP state → consent updates → normal Triggers and Tags.
- Document explicit and region-aware defaults, same-page updates, persistence, revocation, and CMP category mapping.
- Explain GTM built-in consent checks for Google Tags and additional consent checks for reviewed custom or third-party Tags.
- Explain how Data Layer events, Variables, Triggers, consent state, Tags, Google tag, and GA4 destinations connect.
- Distinguish Trigger matched, Tag fired, browser storage, Network request, and downstream GA4 receipt.
- Define naming, ownership, inventory, environment, lifecycle, change-control, release, monitoring, and rollback requirements.
- Include QA and failure cases such as CMP race conditions, missing defaults, stale or duplicate consent, SPA navigation, cross-domain/iframe behavior, ad blockers, server-side tagging, revocation, and environment drift.
- Include an FD `calculation_action` example without unnecessary personal or sensitive data.

## Deliverables / Outputs

- A concise GTM consent-management and governance guide.
- Basic versus Advanced Consent Mode comparison.
- Consent-state and granted/denied behavior model.
- Consent Initialization and CMP integration flow.
- CMP integration contract covering source of truth, category mapping, timing, updates, persistence, revocation, and evidence.
- Built-in versus additional consent-check guidance.
- Consent inventory and ownership template.
- QA matrix covering:
  - first visit and default state;
  - accept, reject, granular choice, change, and revoke;
  - storage, Network, and destination behavior;
  - direct landing, slow CMP, SPA, multiple tabs, and regions;
  - staging, production, browsers, and downstream GA4 validation.
- Failure-mode guidance, change-control requirements, and audit checklist.
- FD `calculation_action` implementation and QA example.
- Detailed answer: [05-consent-answer.md](./05-consent-answer.md).

## Acceptance Criteria

- Consent defaults are explicit and established before measurement Tags run.
- Unknown or uninitialized consent is not treated as permission.
- Basic and Advanced Consent Mode behavior is clearly distinguished and verified through storage and Network evidence.
- `analytics_storage`, advertising storage, and advertising data-use signals are handled separately.
- Google built-in consent checks are used as the primary control; redundant exception Triggers are avoided.
- Third-party and custom Tags have explicit consent behavior and do not assume automatic Google Consent Mode enforcement.
- Consent updates occur on the same page as the user’s choice and are persisted by the CMP or approved solution.
- QA verifies consent state, Tag behavior, storage, request content, destination, event payload, and expected outcome.
- The Data Layer and requests contain no unnecessary PII, credentials, secrets, account identifiers, or unrestricted user input.
- Region, browser, environment, SPA, race-condition, revocation, and server-side behavior is addressed.
- Every Tag has a documented purpose, vendor, destination, consent requirement, owner, inventory record, and review date.
- Changes include privacy review when required, test evidence, approver, published version, monitoring, and rollback plan.

## Out of Scope

- Making legal or jurisdiction-specific decisions.
- Selecting the final CMP, legal basis, retention policy, or regional policy without privacy/legal approval.
- Publishing live changes to GTM, GA4, CMP, server-side tagging, or vendor configurations.
