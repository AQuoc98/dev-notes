# 02 — GTM Variable Management

## 1. Objective, scope, and outputs

This document defines how to design, configure, review, and retire Google Tag Manager (GTM) Variables for stable GA4 measurement.

A GTM Variable is a named placeholder for a value that can change. A Trigger can use that value to decide whether a Tag fires; a Tag can use it to populate event parameters. Variables should expose approved application data, not recreate application business logic.

### In scope

- Choosing a source and the simplest native GTM Variable type.
- Data Layer Variable behavior, nested paths, persistence, and stale-value prevention.
- Scope, reuse, naming, folders, environment routing, consent, and missing-data rules.
- Custom JavaScript guardrails, QA, inventory, deprecation, and retirement.
- FD `calculation_action` Variable setup as the reference pattern.

### Out of scope

- Detailed Trigger or Tag design; see Sections 03 and 04.
- Consent-management implementation; see Section 05.
- Custom-template development; see Section 06.
- Measurement questions and field approval; see Section 07.
- Debug evidence, reports, and release monitoring; see Sections 08–10.
- Advertising or campaign-specific variable design.

### Outputs

Each approved Variable should have:

1. A contract record with source, type, meaning, allowed values, and missing-data behavior.
2. A canonical name and folder.
3. Known consumers and environment/consent behavior.
4. QA evidence and a versioned publication record.
5. An inventory entry with an owner and lifecycle status.

## 2. Overview: what a Variable does

### 2.1 Simple definitions

| Component | Role in Variable management |
| --- | --- |
| Data Layer Variable (DLV) | Reads an application-owned value from a Data Layer key or nested path. This is the preferred source for business values. |
| Constant | Provides a fixed, non-secret value that can be reused. |
| Lookup Table (LUT) | Maps an exact input value to an approved output, such as a hostname to a Measurement ID. |
| RegEx Table (RLT) | Maps controlled text patterns to outputs. Rules are evaluated from top to bottom. |
| URL Variable | Reads a defined component of the current URL. |
| First-Party Cookie | Reads an approved cookie value. |
| DOM Element | Reads page content only when no stable application/Data Layer source exists. It is fragile for business values. |
| Custom JavaScript | Performs a small synchronous transformation when no native type is sufficient. It is the last option. |

The relationship is:

```text
Application
    ↓ publishes approved values
Data Layer
    ↓ read by
GTM Variables
    ↓ used by
Triggers and Tags
    ↓ send to
GA4 or another approved destination
```

Variable = “What value should be used?”

Trigger = “When should the Tag run?”

Tag = “What should be sent or executed?”

### 2.2 Ownership boundary

The Application knows which value is correct and whether the business result actually happened. It also normalizes data before publishing it to the Data Layer. A GTM Variable only reads that value or performs a simple, approved mapping; it does not decide the business result. If a value is missing or wrong, check the Application and Data Layer first instead of hiding the defect with a broad GTM fallback.

## 3. Variable decision workflow

Use this order for every new or changed Variable:

```text
1. Classify scope and lifecycle
        ↓
2. Search the container and inventory
        ↓
3. Define the Variable contract
        ↓
4. Choose the simplest native type
        ↓
5. Name and organize it
        ↓
6. Configure missing data, environment, consent, and privacy
        ↓
7. Test the complete path
        ↓
8. Review, publish, inventory, and maintain
```

Do not create a Variable before steps 1–3 are complete.

### 3.1 Classify scope and lifecycle

| Classification | Use when | Naming examples |
| --- | --- | --- |
| Shared | Source, meaning, type, allowed values, missing behavior, consent, and environment behavior are compatible across projects. | `SHARED - DLV - solution_found` |
| Project-specific | The value belongs to one application or Measurement Plan. | `FD - DLV - inputs - connection_type` |
| Temporary/experiment | A migration, proof of concept, or time-limited test needs an isolated Variable. | `FD - TEST - normalize_input` |
| Deprecated | A replacement exists, but consumers still need to migrate safely. | `FD - OLD - calculation_status` |

Similar names are not enough to justify a shared Variable. Reuse only when the full contract matches. Shared standards can be used in several containers, but a Variable still needs to be created and approved in each container.

### 3.2 Search before creating

Search the container and the Variable inventory for the same Data Layer key, purpose, and type. Prefer one canonical Variable used by multiple compatible Tags or Triggers. Do not create tag-specific copies such as `FD - DLV - print - solution_found` and `FD - DLV - download - solution_found` when both read the same approved `solution_found` field.

Before reusing a Variable, compare:

- source key/path;
- data type and allowed values;
- business meaning;
- required/optional status and missing behavior;
- environment and consent behavior;
- owners and downstream consumers.

If a material difference exists, use a project-specific Variable and document why.

### 3.3 Define the Variable contract

Record these properties before configuring GTM:

| Property | What to record |
| --- | --- |
| Name and scope | Canonical GTM name and owning project/group. |
| Business meaning | What the value represents and which question it supports. |
| Source | Exact Data Layer key/path or native source. |
| Type | String, number, Boolean, URL component, lookup output, and so on. |
| Allowed values/units | Controlled values, pattern, or numeric unit. |
| Required | Whether a valid event needs the value. |
| Missing behavior | Omit, block/fail QA, or approved default. |
| Environment | QA, staging, production, or all. |
| Consent/privacy | Collection dependency and data classification. |
| Consumers | Tags, Triggers, Lookup/RegEx Tables, or other Variables. |
| Owner/status | Responsible team and Active, Test, Deprecated, or Retired status. |
| Replacement/review date | Required for lifecycle management. |

## 4. Source and type selection

### 4.1 Source priority

Prefer a stable application-owned Data Layer value over a value scraped from the page:

| Requirement | Preferred Variable | Avoid |
| --- | --- | --- |
| Read an approved application value | Data Layer Variable | DOM scraping or ad-hoc JavaScript |
| Reuse a fixed non-secret value | Constant | Repeating literals in Tags |
| Map an exact controlled input | Lookup Table | Long JavaScript branches |
| Map controlled patterns | RegEx Table | Broad or overlapping patterns |
| Read URL information | URL Variable | Manual `location` parsing |
| Read an approved first-party cookie | First-Party Cookie | Unreviewed custom code |
| Read stable legacy page content | DOM Element | Localized or unstable UI text |
| Transform a value synchronously | Custom JavaScript | Moving business logic into GTM |

### 4.2 Data Layer evaluation rules

GTM processes queued Data Layer messages in first-in, first-out order. Values can remain available after an earlier push, and a later push with the same key overwrites the earlier value. Therefore:

- initialize one `window.dataLayer` and use `.push()`; do not overwrite the array after GTM loads;
- put `event` and all event-specific values in the same message;
- use Data Layer Variable Version 2 for nested paths such as `inputs.connection_type`;
- keep field names and casing consistent;
- define whether an optional field is omitted or explicitly cleared;
- never let a previous value silently stand in for a missing value.

The Data Layer is a transport object. It is not an isolated snapshot store for each event, so same-message completeness is required for reliable Variable evaluation.

### 4.3 Missing-data behavior

Choose one explicit outcome:

| Outcome | Use when | Result |
| --- | --- | --- |
| Omit | The field is optional or not applicable. | The Variable returns no value and the Tag omits the parameter. |
| Block/fail QA | The field is required for a valid event. | The event path is blocked or marked as a contract defect. |
| Approved default | The business contract defines a real fallback. | The documented default is used. |

Do not convert missing values to `unknown`, `N/A`, empty string, or a previous value without an approved meaning. Unknown environments must fail closed and never fall back to a production destination.

## 5. Naming and folder standards

### 5.1 Names

Use:

```text
[SCOPE] - [TYPE] - [PURPOSE OR SOURCE]
```

Recommended type prefixes:

| Prefix | GTM type | Example |
| --- | --- | --- |
| `DLV` | Data Layer Variable | `FD - DLV - inputs - connection_type` |
| `CONST` | Constant | `FD - CONST - GA4 Measurement ID - QA` |
| `LUT` | Lookup Table | `FD - LUT - Hostname to Measurement ID` |
| `RLT` | RegEx Table | `WEB - RLT - Page Path to Page Type` |
| `URL` | URL Variable | `WEB - URL - Query - campaign_id` |
| `DOM` | DOM Element | `WEB - DOM - Checkout Total` |
| `COOKIE` | First-Party Cookie | `SHARED - COOKIE - Consent State` |
| `JS` | Custom JavaScript | `FD - JS - Normalize Method` |

For a direct Data Layer Variable, preserve the exact `snake_case` key/path. For GTM-managed configuration or mapping, use a human-readable purpose. Avoid names such as `Variable 3`, `New Variable`, `Connection`, or `Test`.

### 5.2 Folders

Folders describe responsibility; the namespace in the Variable name describes ownership. Use only folders that the container actually needs:

```text
00 - Documentation
01 - Shared Foundations
02 - Project Data Layer
03 - Routing and Environment
04 - Consent and Privacy
05 - Measurement Configuration
07 - Utilities and Transformations
08 - Third-Party Integrations (only when approved)
90 - Experiments
95 - Migration
99 - Deprecated
```

Do not create a folder merely because it appears in this list. Do not move an apparently unused Variable to `99 - Deprecated` until all consumers have been checked.

## 6. Configure common Variable patterns

### 6.1 Data Layer Variables

Use the exact Data Layer key and select Version 2 for nested paths. Keep the Variable one-to-one with the contract; do not add business interpretation or fallback logic.

### 6.2 Environment routing

Use a reviewed Lookup Table or equivalent configuration to map known QA, staging, and production hosts to their approved destinations. Unknown hosts must return blank/undefined or block the dependent Tag. Never use the production Measurement ID as an uncontrolled default.

### 6.3 Consent and privacy

A consent Variable can support configuration, but it does not replace Consent Mode or the approved consent setup. Consent defaults and updates must be established before dependent Tags fire; see Section 05. Do not create Variables containing PII, credentials, tokens, payment data, unrestricted text, or secrets. Published GTM configuration is browser-visible, so never store secrets in Constants.

### 6.4 Custom JavaScript policy

Use Custom JavaScript only when a native Variable type cannot meet the requirement. It must be:

- small, synchronous, deterministic, and side-effect free;
- defensive for `undefined`, `null`, invalid types, and unexpected values;
- explicit about return type and failure behavior;
- privacy-safe, documented, owned, reviewed, and tested.

It must not call an API, modify application state/DOM/cookies, push another Data Layer event, or reconstruct an application business result. If used temporarily to migrate a legacy value, record the owner, reason, replacement plan, and removal condition.

## 7. Test, publish, and maintain

### 7.1 Test before publishing

In **GTM Preview**, check which value the Variable returns and whether the Tag/Trigger that uses it behaves correctly. Open the **browser Network panel** only when the Variable contributes to an outbound request and you need to confirm the actual request: whether it was sent, how many times, which parameters it contains, and whether it reaches the correct GA4 Measurement ID/destination.

For every new or changed Variable, check:

- normal, missing, optional, invalid, and edge values;
- nested Data Layer paths and same-push completeness;
- QA/staging/production routing and unknown hosts;
- granted, denied, and updated consent states;
- all Tag and Trigger consumers;
- final request parameter, count, and GA4 destination.

For FD, verify the full chain:

```text
Data Layer message
    → canonical Variable values
    → environment routing
    → Trigger
    → GA4 Event Tag
    → Network request
    → correct GA4 Measurement ID
```

“Tag Fired” alone is not sufficient evidence. Link the result to the Section 08 Evidence Template.

### 7.2 Review and publish

Before publishing:

- For a **shared** Variable (used by multiple projects or Tags), check every consumer so another flow is not broken.
- For an **environment-sensitive** Variable (a value that changes between QA, staging, and production), test every environment and ensure there is no fallback path to production.

Then complete these steps:

1. Confirm the Variable contract and all consumers.
2. Test every affected environment and consent state.
3. Review privacy and missing-data behavior.
4. Record QA evidence and owners.
5. Publish a versioned GTM change with a meaningful note.

Example note: `FD: separate staging and production Measurement ID routing by hostname.`

### 7.3 Variable inventory

Maintain one searchable registry with at least:

```text
Variable name and scope
GTM type and exact source
Business meaning and allowed values/units
Required and missing-data behavior
Environment and consent/privacy classification
Consumers
Owner and status
Review date
Replacement, when applicable
```

### 7.4 Deprecate and retire

Do not delete a Variable because it appears unused. Check Tags, Triggers, Lookup/RegEx Tables, Custom Templates, Custom JavaScript, and other containers or documentation references. Use:

```text
Identify replacement
    → mark old Variable Deprecated
    → migrate consumers
    → test in Preview
    → publish a reviewed version
    → monitor
    → remove the old Variable
```

## 8. Audit checklist and cross-references

| Audit finding | First action |
| --- | --- |
| Multiple Variables read the same contract | Consolidate to one canonical Variable. |
| Variable scrapes localized UI text | Add the stable value to the Data Layer. |
| Staging resolves to production | Add explicit environment routing and fail-closed behavior. |
| Missing data becomes `unknown` without approval | Restore omit/block/default semantics. |
| Custom JavaScript reconstructs business logic | Redesign the application/Data Layer contract. |
| Test Variable became a production dependency | Promote it deliberately or retire it. |
| Variable has no approved consumer | Confirm ownership and remove safely. |

- [Section 01 — Data Layer Design](01-data-layer-design-answer.md): application-owned event contract and payload shape.
- [Section 03 — Trigger Management](03-trigger-management-answer.md): use Variable values in Custom Event Trigger conditions.
- [Section 04 — Tag Management](04-tag-management-answer.md): map canonical Variables to GA4 Event parameters.
- [Section 05 — Consent Management](05-consent-answer.md): consent defaults, updates, and denied-state behavior.
- [Section 06 — Template Governance](06-template-governance-answer.md): governance when a custom template is unavoidable.
- [Section 07 — Measurement Plan](07-measurement-plan-answer.md): field approval, owners, and reporting questions.
- [Section 08 — Debug/QA](08-debug-qa-answer.md): Variable evidence, test matrix, and defect/retest records.
- [Section 09 — Reports and Charts](09-reports-charts-answer.md): field readiness and processed-data interpretation.
- [Section 10 — Release Monitoring](10-release-monitoring-answer.md): release gates, monitoring, and rollback.

## 9. Worked example: FD Variable setup

This is the only concrete FD walkthrough. Replace project IDs and hostnames with approved values.

### 9.1 Contract

| Data Layer path | Type | Required | Missing behavior | Consumer |
| --- | --- | --- | --- | --- |
| `event` | string | Yes | Block | Custom Event Trigger |
| `event_schema_version` | string | Yes | Fail QA | Trigger and GA4 Event Tag |
| `app_name` | string | Yes | Fail QA | Trigger and GA4 Event Tag |
| `solution_found` | boolean | Yes | Fail QA | GA4 Event Tag |
| `inputs.connection_type` | string | Yes | Fail QA | GA4 Event Tag |
| `inputs.fx` | number | Conditional | Omit when not applicable | GA4 Event Tag |
| `inputs.fy` | number | Conditional | Omit when not applicable | GA4 Event Tag |

### 9.2 Data Layer message

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### 9.3 Canonical Variables

```text
SHARED - DLV - solution_found
  Data Layer Variable Name: solution_found

FD - DLV - event_schema_version
  Data Layer Variable Name: event_schema_version

FD - DLV - inputs - connection_type
  Data Layer Variable Name: inputs.connection_type
  Version: 2

FD - DLV - inputs - fx
  Data Layer Variable Name: inputs.fx
  Version: 2

FD - DLV - inputs - fy
  Data Layer Variable Name: inputs.fy
  Version: 2
```

### 9.4 Environment lookup

```text
FD - LUT - Hostname to Measurement ID

app.example.com         → production Measurement ID
app-staging.example.com → QA/staging Measurement ID
unknown host            → blank/blocked
```

### 9.5 GA4 Tag mapping and missing values

```text
solution_found
  → {{SHARED - DLV - solution_found}}

connection_type
  → {{FD - DLV - inputs - connection_type}}

fx
  → {{FD - DLV - inputs - fx}}

fy
  → {{FD - DLV - inputs - fy}}
```

```text
Optional fx is not applicable
    → Variable returns undefined
    → fx is omitted from the GA4 request

Required solution_found is missing
    → QA failure / event blocked
```

### 9.6 End-to-end test

```text
Application pushes one complete calculation_action message
    → GTM Preview shows the expected DLV values
    → hostname maps to the QA Measurement ID
    → approved Trigger matches
    → one GA4 Event Tag fires
    → Network request contains approved scalar parameters
    → request reaches the intended GA4 destination
```

Test a valid output, valid no-output, invalid input, API failure, stale response, duplicate callback, missing required field, unknown host, and consent denied/granted. Record evidence in Section 08 before publishing.

## References

- [Tag Manager Help — Variable](https://support.google.com/tagmanager/answer/13355320?hl=en)
- [Tag Manager Help — User-defined variable types for web](https://support.google.com/tagmanager/answer/7683362?hl=en)
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en)
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer)
