# FD-REC-00 — Phase 0 System Inventory Record

> **[RECORD]** Phase 0 result for the FD `calculation_action` project. IDs, accounts, permissions and platform configuration in this record are simulated values approved for design work; no live verification has been performed.

## 0. Record metadata

| Field | Value |
|---|---|
| Record ID | `FD-REC-00` |
| Record name | Phase 0 System Inventory Record |
| Document type | `PROJECT RECORD` |
| Purpose | Store the FD environment, GA4/GTM foundation, consent, access, duplicate-collection and QA-scope baseline |
| Project / journey | FD web application / `J-FD-CALC-001` |
| Phase | Phase 0 — System inventory and access |
| Source of truth | This record for the Phase 0 baseline; semantic event decisions belong to `FD-REC-07` |
| Change reading status | **Required — affected** |
| Linked Change Requests | [`FD-CR-002 — Runtime-readiness hardening`](FD-CR-002-runtime-readiness-hardening.md); [`FD-CR-001`](FD-CR-001-solution-found-value-change.md) for historical lineage |
| Why this matters | `FD-CR-002` changes the duplicate-control baseline, analytics data boundary, consent readiness and QA scope owned by Phase 0. |
| Owner | FD Analytics/GTM Lead — simulated owner |
| Approver / reviewer | Analytics/GTM Lead; Privacy reviewer; Leadership |
| Version | `0.4-management-ready` — `FD-CR-002` simulation update |
| Status | **Simulation baseline updated; runtime blocked** |
| Value/evidence boundary | Hostnames were supplied by the requester; IDs, accounts, permissions and platform setup are simulated values |
| Dependencies | QA/staging and production hostnames supplied by the requester |
| Open items / risks | `FD-OPEN-001`–`004`, real accounts/access, consent implementation, duplicate audit, active-runtime baseline and schema `3.0` rollout path remain unresolved |
| Next action | Resolve Phase 0 runtime facts and blockers before authorizing the schema `3.0` release packet |
| Created / last updated | `2026-09-06` / `2026-09-07` — `FD-CR-002` |

## 1. Environment and hostname baseline

| Item | Value |
|---|---|
| QA/staging URL | `https://app-staging.strongtie.com/fd` |
| QA/staging hostname | `app-staging.strongtie.com` |
| Production URL | `https://app.strongtie.com/fd` |
| Production hostname | `app.strongtie.com` |
| Product | FD web application |
| Simulated region | UK / `gb` |
| Simulated timezone | `Europe/London` |
| Simulated default currency | `GBP` — property setting only; not sent with this event |

The hostnames are project-scope inputs supplied by the requester, not simulated identifiers.

## 2. GA4 destination baseline

QA and production use separate GA4 properties so QA cannot contaminate production data.

| Item | QA | Production |
|---|---|---|
| Analytics account | `Strongtie Analytics — SIMULATED` | `Strongtie Analytics — SIMULATED` |
| Property name | `FD Web — QA — SIMULATED` | `FD Web — Production — SIMULATED` |
| Property ID | `123456789` | `123456790` |
| Web stream name | `FD Web QA — app-staging — SIMULATED` | `FD Web Production — app — SIMULATED` |
| Stream ID | `9876543210` | `9876543211` |
| Stream URL | `https://app-staging.strongtie.com` | `https://app.strongtie.com` |
| Measurement ID | `G-FAKEFDQA01` | `G-FAKEFDPROD1` |
| Timezone | `Europe/London` | `Europe/London` |
| Currency | `GBP` | `GBP` |
| Enhanced Measurement | Enabled; verify that it does not create `calculation_action` | Enabled; verify that it does not create `calculation_action` |
| Value state | Simulated | Simulated |

For a real deployment, replace these with real property, stream and Measurement IDs, owner, creation date and verification evidence. Do not share real Measurement IDs outside the approved access boundary.

## 3. GTM foundation baseline

| Item | Value |
|---|---|
| GTM account | `Strongtie Web Tag Management — SIMULATED` |
| Web container | `FD Web — app.strongtie.com — SIMULATED` |
| Container ID | `GTM-FAKEFD01` |
| Container type | Web |
| Main workspace | `Default — SIMULATED` |
| Working workspace | `WS-FD-CALC-001 — calculation_action` |
| QA custom environment | `ENV-FD-STAGING` |
| QA destination URL | `https://app-staging.strongtie.com/fd` |
| Live environment | `Live — Production` |
| Production destination URL | `https://app.strongtie.com/fd` |
| Value state | Simulated |

## 4. Google tag and routing decision

### 4.1 Google tag baseline

| Item | Value |
|---|---|
| Tag name | `FD - Google tag - Primary` |
| Tag type | Google tag |
| Tag ID source | `{{FD - LUT - Hostname to Measurement ID}}` |
| Trigger | Initialization with the approved hostname allowlist |
| QA destination | `G-FAKEFDQA01` |
| Production destination | `G-FAKEFDPROD1` |
| Unknown hostname | Does not fire; no destination is selected |
| Consent | Built-in Google tag consent behavior; `analytics_storage` follows the consent baseline |
| Value state | Simulated design; not configured live |

### 4.2 Decision

Use one web container for QA and production because both hostnames use the same tag logic. Hostname allowlisting and lookup routing select the correct destination:

| Hostname | Measurement ID |
|---|---|
| `app-staging.strongtie.com` | `G-FAKEFDQA01` |
| `app.strongtie.com` | `G-FAKEFDPROD1` |

Hostnames outside the allowlist must be blocked. Do not use a production Measurement ID as a default. If the environments later require separate lifecycle or tag policies, reopen this decision in the release record.

## 5. Duplicate-collection baseline

The desired baseline is one authoritative source for each business fact:

| Collection path | Expected baseline | Verification in a real setup |
|---|---|---|
| One GTM container snippet | 1 container `GTM-FAKEFD01` | Page source, Tag Assistant |
| One Google tag in GTM | 1 canonical Google tag | GTM Tags, Preview |
| One GA4 Event tag for `calculation_action` | 1 | GTM Tags, workspace search |
| Hard-coded `gtag.js`/GA4 script | 0 | Page source, Network |
| CMS/plugin GA4 integration | 0 | CMS/application configuration |
| Application `gtag('event', ...)` for the same event | 0 | Source code |
| Application manual GA4/Measurement Protocol sender | 0, out of scope | Source code, server configuration |
| Enhanced Measurement creating `calculation_action` | 0 | GA4 Events, DebugView |
| Legacy click/DOM Tag for the same business fact | 0 | GTM Tags/Triggers |
| One calculation occurrence | 1 opaque `event_id` → 1 minimized Data Layer push → 1 Trigger match → 1 GA4 request only when consent permits; denied/unknown → 0 analytics requests | Application evidence + GTM Preview + Network; approved export when exact production deduplication is required |

The live duplicate audit was not performed in Phase 0 because it is outside the simulation scope.

## 6. Consent baseline

| Item | Value |
|---|---|
| CMP | `Strongtie Consent Manager — SIMULATED` |
| CMP version | `1.0.0-simulated` |
| Source of truth | CMP consent store |
| Purpose | Analytics measurement for FD |
| Google consent type | `analytics_storage` |
| Region/banner scope | `[TBD — privacy approval required before runtime]` |
| Default before choice | `denied` |
| User grants analytics | `granted` |
| User rejects analytics | `denied` |
| CMP delay/failure/unknown | Fail-safe: `denied` |
| Consent Mode | Basic Consent Mode — simulated baseline |
| `ad_storage`, `ad_user_data`, `ad_personalization` | Out of scope |
| Update/persistence/revocation | Defined as runtime-blocking details in `FD-REC-05`; real values are pending |
| Policy status | Simulated baseline; `FD-OPEN-003` real privacy approval is pending |
| Value state | Simulated |

Collection to GA4 is allowed only when the consent policy allows it. `denied` or `unknown` must suppress the analytics event under the approved decision.

## 7. Access baseline

The aliases below are simulated values, not real accounts.

| Role | Simulated account/permission | GTM container | GA4 property | Responsibility |
|---|---|---:|---:|---|
| Developer | `fd-developer@strongtie.com` | Read | Analyst or Viewer | Application Data Layer and source debugging |
| GTM implementer | `fd-gtm-implementer@strongtie.com` | Edit | Viewer | Variables, Triggers, Tags and Preview |
| Analytics owner | `fd-analytics-owner@strongtie.com` | Approve | Editor | Measurement Plan, custom definitions, reports and review |
| QA reviewer | `fd-qa@strongtie.com` | Read | Analyst | QA, DebugView/Realtime and evidence |
| Publisher | `fd-publisher@strongtie.com` | Publish | Viewer or Editor according to property changes | Publish after approval and smoke test |
| Backup GTM admin | `strongtie-tag-admin@strongtie.com` | Account Admin | Not required | Break-glass administration |
| Backup GA4 admin | `strongtie-analytics-admin@strongtie.com` | Not required | Administrator | User management and recovery |

Real permissions, inherited access, active administrators and access-review dates have not been verified.

## 8. QA scope baseline

### 8.1 Test baseline

| Item | Value |
|---|---|
| Primary QA URL | `https://app-staging.strongtie.com/fd` |
| Tag Assistant session | Expected to be used only on QA if a future project opens runtime; not run in this project |
| Test run reference | `QA-FD-CALC-RUN-001` |
| Build reference | `fd-web-simulated-build-003` |
| Test identity | `fd-qa-synthetic-001` — internal label; never sent to GA4 |
| Consent reset | Fresh browser profile or reset consent according to the test record |

### 8.2 Simulated data boundary

The values below are safe examples for setup/QA design. Do not use real user data, tokens, emails or raw user text.

| Scenario | Simulated API result | Event expectation |
|---|---|---|
| `TC-FD-01` valid output | Response has `length > 0` for the snapshot | 1 event, `solution_found: "Yes"` |
| `TC-FD-02` valid no-output | Response is `[]` | Schema `3.0`: one event with `No_solution` and an opaque `event_id`; schemas `1.0`/`2.0` remain history |
| `TC-FD-03` invalid input | UI shows input validation | No `calculation_action` |
| `TC-FD-04` server failure | HTTP 5xx or network error | Schema `3.0`: one event with combined `No_solution` and an opaque `event_id` |
| `TC-FD-05` stale response | Attempt terminalized by stale/cancellation | 1 error event; late callback ignored |
| `TC-FD-06` retry/duplicate callback | Multiple callbacks for one occurrence | Exactly 1 event |

The complete API snapshot remains in the Application/controlled QA evidence. The analytics Data Layer contains only the minimized schema `3.0` subset and opaque `event_id`; internal correlation tokens, API response bodies and sensitive data do not belong in the analytics Data Layer or GA4 payload.

### 8.3 Browser matrix

| Group | Browser matrix |
|---|---|
| Desktop primary | Latest stable Chrome — expected matrix for Tag Assistant, GTM Preview and Network; not run in this project |
| Desktop regression | Latest stable Edge and Firefox |
| Apple desktop | Latest stable Safari |
| Mobile | Latest stable Chrome Android and Safari iOS |
| Evidence baseline | Chrome desktop with a fresh profile |

Each future QA evidence record must include browser version, device, consent state, GTM environment, GA4 property/stream and timestamp.

## 9. Acceptance checklist / current simulation status

> When this record is copied to a project, start with all boxes unchecked. The checked items below describe this simulation baseline only; they do not prove a live GA4/GTM foundation.

- [x] Environment, destination, consent, access and QA-scope fields are documented.
- [x] Duplicate-collection and evidence boundaries are explicit.
- [x] Live configuration and runtime verification are not claimed.

The project copy must attach the relevant account, container, consent and access evidence before marking these criteria complete.

## 9.1 Acceptance decision and evidence boundary

| Item | Result |
|---|---|
| Documentation scope | Environment, GA4/GTM foundation, consent, access, duplicate baseline and QA scope |
| Real account/property/container/Measurement ID | Bypassed — simulated values used; no live verification |
| Real Phase 0 platform setup | Bypassed — no live GA4/GTM changes |
| Real duplicate audit | Not performed; only an expected baseline exists |
| Real CMP verification | Not performed; only a simulated consent baseline exists |
| Acceptance decision | Phase 0 reviewed/approved at the simulated-baseline level |
| Reviewer | Requester |
| Review date | `2026-09-04` |
| Phase 1 entry | Approved for simulation only; runtime entry blocked by `FD-OPEN-001`–`004` |

This record is a management baseline, not evidence of an account, platform configuration, runtime collection or QA execution. `FD-CR-001` records the historical v1→v2 outcome rename; `FD-CR-002` updates the current schema `3.0` data, consent, duplicate and QA baselines. A real deployment must replace `Simulated` with `Configured`/`Verified`, close all runtime blockers and attach the corresponding evidence in the applicable phase record.
