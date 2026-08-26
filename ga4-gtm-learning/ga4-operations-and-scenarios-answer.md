# 12 — GA4 Operations and Common Scenarios

## Operating Model

Treat GA4 administration as versioned production configuration. Property and stream settings can change collection, identity, attribution, privacy, and future reports. Record who changed what, why, when, evidence, approval, expected impact, and mitigation. Not every setting is retroactive, and some filters permanently prevent data from being processed.

## Property Baseline

| Area | Required decision/evidence |
| --- | --- |
| Structure | Account, property, streams, environments, owners, and business purpose |
| Core settings | Property timezone and reporting currency |
| Access | Least-privilege users/groups and periodic access review |
| Collection | Tag/stream IDs, enhanced measurement, consent, referrals, and cross-domain rules |
| Data quality | Internal/developer filters, key events, custom definitions, and campaign naming |
| Privacy | Prohibited-data checks, ads/signals choices, retention, and deletion process |
| Integrations | Approved Google Ads, Search Console, BigQuery, or other links and owners |
| Reporting | Identity/attribution context, report owners, processing expectations, and exports |

Use separate properties or streams only for a documented business and governance reason. A stream is not equivalent to a Universal Analytics view or a security boundary.

## Scenario Decision Matrix

| Scenario | Preferred approach | Critical checks |
| --- | --- | --- |
| Common page interactions | Review enhanced measurement first | Prevent overlap with custom events; test SPA page views |
| SPA navigation | Application route event or verified history-change design | Location/referrer semantics, back/forward, duplicates |
| Multiple owned domains | One suitable stream plus cross-domain measurement | Same tag ID, `_gl` survives navigation, consent, self-referrals |
| External payment/auth return | Unwanted referrals when it is not owned cross-domain measurement | Preserve original acquisition; do not hide tagging defects |
| Employee/test activity | Identify traffic and test filters before activation | Active exclusion is prospective and permanent |
| Manual campaigns | Govern controlled lowercase UTM values | Never use UTMs on internal links; no PII; auto-tagging precedence |
| Signed-in journey | GA4 User-ID according to policy | Stable non-PII ID, configuration setting, `null` on logout |
| Ecommerce | Recommended events and item schema | Unique transaction ID, currency/value, refunds, reconciliation |
| Offline/server interaction | Measurement Protocol supplements normal tagging | Identity linkage, time/session context, validation, consent, deduplication |
| Raw/long-term analysis | Assess BigQuery export or Data API | Surface differences, cost, access, retention, ownership |

## Example Flow — Cross-Domain Checkout

```text
User arrives on `www.example.com` from a campaign
→ browses products and starts checkout
→ follows a link to owned `checkout.example-pay.com`
→ both domains use the same approved web stream/tag ID
→ cross-domain configuration decorates navigation with `_gl`
→ checkout preserves the parameter through redirects
→ purchase returns to the original site without a self-referral or new user/session
→ QA validates acquisition, client/session continuity, consent, and purchase deduplication
```

If the checkout domain is an external provider that cannot participate in cross-domain measurement, evaluate unwanted-referral configuration while preserving the original campaign source.

## Enhanced Measurement and SPAs

Inventory enabled enhanced-measurement events and exact definitions. Do not deploy custom tracking with overlapping semantics without preventing duplicates. For an SPA, test initial load, route change, back/forward, relevant query/hash changes, title/location values, and consent states.

## Cross-Domain and Referral Scenarios

Use cross-domain measurement when one journey crosses owned domains that should share user/session context. Verify the same stream/tag ID and that linker parameters survive links, forms, redirects, and allowlists. Use unwanted referrals for third-party domains such as payment providers that should not replace the original source. Diagnose missing tags and linker failures before adding exclusions.

## Internal and Developer Traffic

Identify traffic, put the data filter in **Testing**, validate it with the test-filter dimension, approve it, and only then consider **Active**. Activation affects future data, and excluded data is unavailable in Analytics and BigQuery. Preserve an explicit QA/debug method rather than making tests invisible.

## Campaign Attribution

Maintain a campaign taxonomy and URL-generation process. Govern at least `utm_source`, `utm_medium`, and `utm_campaign`; values are case-sensitive. Never add campaign parameters to internal navigation because they can overwrite acquisition context. Document auto-tagging/manual-tagging precedence for linked advertising platforms.

## User-ID

Send a stable organization-controlled, non-PII identifier only for authenticated users under approved privacy and consent rules. Configure `user_id` as a Google tag/configuration setting—not an event parameter, user property, or custom dimension. Do not send it before sign-in; send `null` after logout. Test login mid-session, logout, account switching, multi-tab behavior, and reporting-identity implications.

## Ecommerce

Follow GA4’s recommended event and item schema. Set `currency` when sending `value`, preserve a unique `transaction_id` for deduplication/reconciliation, and define authoritative purchase/refund moments. Reconcile a controlled sample against the commerce backend; test refreshes, retries, partial refunds, coupons, multiple currencies, item arrays, and consent.

## Measurement Protocol

Use Measurement Protocol for offline, server, or device events that supplement client/app collection—not as an assumed replacement. Define identity linkage, timestamps, session association, privacy/consent, secret handling on a trusted server, validation, retry/idempotency, and reconciliation. UI event-modification rules do not necessarily apply, so send the intended final schema.

## Privacy, Retention, and Deletion

- Ensure URLs, titles, search terms, errors, User-ID, ecommerce fields, and campaigns contain no prohibited personal data.
- Record retention settings and which reporting surfaces they affect.
- Maintain an authorized user-data/property-data deletion process with evidence.
- Review signals, ads personalization, granular collection, consent mode, and product links against regional policy; technical capability is not legal approval.
- Restrict raw exports and linked products using least privilege, retention, and cost controls.

## Change and Incident Runbook

1. Capture baseline counts, affected settings/reports, owner, ticket, and expected effect.
2. Test with the safest available test or non-production mechanism.
3. Obtain analytics and privacy/security approval where relevant.
4. Record before/after configuration and activation time in the property timezone.
5. Monitor Realtime/DebugView, then processed reports/export after the expected delay.
6. If quality degrades, stop reversible sources/settings, annotate the affected period, quantify impact, and document that processed data may not be repairable.

## Completion Checklist

- [ ] Structure, owners, timezone, currency, access, and product links are current.
- [ ] Enhanced measurement and custom tagging cannot duplicate interactions.
- [ ] Applicable scenarios include decision, implementation, tests, owner, and review date.
- [ ] Filters are validated in Testing before activation.
- [ ] Attribution cases are verified with controlled journeys.
- [ ] Identity, ecommerce, and server events follow privacy and deduplication rules.
- [ ] Retention, deletion, consent, ads/signals, and export decisions have owners.
- [ ] Operational changes and incidents have evidence and affected-period notes.

## Official References

- [GA4 account structure](https://support.google.com/analytics/answer/9679158)
- [Enhanced measurement](https://support.google.com/analytics/answer/9216061)
- [Cross-domain measurement](https://support.google.com/analytics/answer/10071811)
- [Unwanted referrals](https://support.google.com/analytics/answer/10327750)
- [Filter internal traffic](https://support.google.com/analytics/answer/10104470)
- [Campaign URL parameters](https://support.google.com/analytics/answer/10917952)
- [Send User-ID](https://developers.google.com/analytics/devguides/collection/ga4/user-id)
- [Measure ecommerce](https://developers.google.com/analytics/devguides/collection/ga4/ecommerce)
- [Measurement Protocol](https://developers.google.com/analytics/devguides/collection/protocol/ga4)
- [Reporting surfaces comparison](https://support.google.com/analytics/answer/13644080)
