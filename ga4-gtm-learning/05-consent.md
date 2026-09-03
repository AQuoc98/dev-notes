# 05 — Consent Management for GA4 in GTM

## Objective

Define how a visitor’s consent choice is received, applied to GTM, and verified so GA4 Tags collect only what the approved policy allows.

## Scope

- Consent defaults and updates in a web GTM container.
- CMP (Consent Management Platform), Consent Mode, Consent Initialization, built-in consent checks, and Additional Consent Checks.
- Consent contract: purpose, destination, consent type, region, default, update timing, persistence, revocation, unknown/failure behavior, owner, and version.
- Environment control, QA evidence for storage/Network/destination, and practical failure handling.
- FD calculation_action consent Journey.

Legal wording, regional policy, CMP selection, and advertising implementation remain with the relevant owners. Advertising consent types are included only when a tag inventory requires them.

## Outputs

1. A consent lifecycle: CMP/policy → Consent Initialization → default/stored state → same-page update → application event → tag consent evaluation → GA4 behavior.
2. A Basic versus Advanced Consent Mode decision note and an explicit state model: granted, denied, and unknown.
3. A CMP mapping and consent inventory for each consent-controlled Tag.
4. GTM guidance for Consent APIs, built-in checks, Additional Consent Checks, persistence, revocation, and environment alignment.
5. A QA matrix and evidence record covering consent state, Tag status, storage, request, destination, and downstream GA4 receipt.

## Acceptance criteria

- Defaults are explicit and established before measurement Tags are evaluated.
- Unknown or malformed consent is not treated as granted.
- Consent updates occur on the same page as the user’s choice and are persisted by the CMP or approved solution.
- Google Tags use built-in consent behavior; Additional Consent Checks are reserved for reviewed custom or third-party Tags.
- QA distinguishes a Trigger match, a Tag firing, a Network request, and downstream GA4 receipt.
- Data Layer and GA4 payloads contain no unnecessary PII, secrets, raw calculation inputs, or unrestricted user input.

## Out of scope

Legal decisions, final CMP/vendor selection, detailed advertising or campaign optimization, custom-template development beyond the approved Consent APIs, and publishing live production changes without review.

## Source

Detailed implementation: [05-consent-answer.md](./05-consent-answer.md).
