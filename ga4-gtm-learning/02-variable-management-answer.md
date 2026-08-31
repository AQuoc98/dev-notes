# 02 — GTM Variable Management

## What is a GTM variable?

> A variable is a placeholder that provides a dynamic value for Tags and Triggers to use.

Variables are mainly used in two places:

- Triggers – determine WHEN a tag should fire. Example: `Page URL contains "/products"` → fire the GA4 tag.

- Tags – provide VALUES to send. Example: a purchase event can use variables such as `transaction_id = "ABC123"` and `value = 100`.

A simple way to remember:

```text
Variable = What is the value?
Trigger = When should it run?
Tag = What should be sent/run?
```

## Data Layer evaluation rules

These rules affect how Data Layer Variables behave:

- GTM processes Data Layer messages in first-in, first-out order.
- Values can remain available after an earlier push, so a later event can accidentally read stale context.
- Put the event name and all event-specific values in the same push.
- Use Data Layer Variable Version 2 when reading nested paths such as inputs.connection_type; Version 2 interprets dots as nested values.
- A later push with the same key overwrites the earlier value. It does not create a separate isolated object for every event.
- For optional values, define whether the key is omitted or explicitly cleared; do not rely on an undocumented previous value.

Recommended:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

Avoid splitting the event across messages:

```javascript
window.dataLayer.push({ inputs: { connection_type: "multi_ply_connection" } });
window.dataLayer.push({ event: "calculation_action", solution_found: true });
```

## Core variable decision flow

Use this short flow for day-to-day work. The detailed multi-project guidance below is the canonical reference for contracts, naming, folders, consent, testing, inventory, and retirement.

```text
CLASSIFY
Is the variable shared, project-specific, temporary, or deprecated?
        ↓
SEARCH
Does a canonical variable with the same source and meaning already exist?
        ↓
CONTRACT
Define source path, business meaning, type, allowed values, missing behavior,
privacy classification, owner, and consumers.
        ↓
SELECT TYPE
Use the simplest native GTM type that meets the requirement.
        ↓
NAME
Apply [SCOPE] - [TYPE] - [PURPOSE OR SOURCE].
        ↓
CONFIGURE
Apply environment routing, consent, privacy, and safe-failure behavior.
        ↓
TEST
Verify Data Layer → Variable → Tag → Network destination.
        ↓
RELEASE AND MAINTAIN
Review, version, inventory, deprecate, and retire safely.
```

### Quick type selection

| Requirement | Preferred GTM variable | Avoid |
| --- | --- | --- |
| Read an application-owned value | Data Layer Variable | DOM scraping |
| Reuse a non-secret fixed value | Constant | Repeating literals across tags |
| Map exact controlled inputs | Lookup Table | Custom JavaScript branches |
| Map controlled patterns | RegEx Table | Broad or overlapping regular expressions |
| Read URL components | URL Variable | Manual location parsing |
| Read an approved first-party cookie | First-Party Cookie | Unreviewed custom code |
| Perform an unavoidable synchronous transformation | Custom JavaScript | Moving business logic into GTM |

Custom JavaScript is the last option. It must be synchronous, deterministic, side-effect free, defensive against missing/invalid input, privacy-safe, documented, reviewed, and tested.

### Three missing-data outcomes

| Outcome | Use when |
| --- | --- |
| Omit | The field is optional or not applicable |
| Block / fail QA | The value is required for a valid event |
| Approved default | The business contract defines a real fallback |

Do not turn missing values into `unknown`, `N/A`, or empty strings without an approved semantic meaning. Unknown environments must not resolve to a production destination.

## Managing Variables Across Multiple Projects

When multiple projects share GTM standards or containers, variables should be managed through a consistent lifecycle rather than created independently whenever a new tracking requirement appears.

The recommended management flow is:

```text
Classify the requirement
        ↓
Search the variable inventory
        ↓
Reuse or design a canonical variable
        ↓
Choose the appropriate variable type
        ↓
Apply naming and folder standards
        ↓
Define source, type, values, and missing-data behavior
        ↓
Configure environment and consent behavior
        ↓
Test in GTM Preview
        ↓
Review and publish
        ↓
Maintain the inventory
        ↓
Deprecate and retire when no longer needed
```

### 1. Classify the variable

Before creating a variable, determine its scope and lifecycle.

#### Shared foundation

Use for values or configuration shared by multiple projects.

Examples:

```text
SHARED - DLV - consent_state
SHARED - LUT - environment
```

Typical shared concepts include:

- consent state,
- environment detection,
- common campaign information,
- shared configuration.

A variable should not become shared only because two projects currently use similar names. The source, meaning, type, allowed values, fallback, and consent behavior must also be compatible.

#### Project-specific

Use when the value belongs to one application's Data Layer contract or measurement plan.

Examples:

```text
FD - DLV - inputs - connection_type
WEB - DLV - page_type
```

For example, `SHARED - DLV - solution_found` is a shared calculation outcome because multiple projects use the same field, Boolean type, meaning, and missing-data behavior. Keep project-specific inputs such as `FD - DLV - inputs - connection_type` in the owning project namespace.

#### Temporary or experimental

Use for variables created for a limited experiment, migration, or proof of concept.

Example:

```text
FD - TEST - normalize_input
```

Temporary variables must have an owner and expected review or removal point.

They should not silently become permanent production dependencies.

#### Deprecated

Use when a variable has been replaced but cannot yet be safely removed.

Example:

```text
FD - OLD - calculation_status
```

with replacement:

```text
SHARED - DLV - solution_found
```

Deprecation provides time to migrate consumers before deletion.

### 2. Search before creating

Before creating a new variable, search the existing GTM container and variable inventory.

The objective is to answer:

```text
Does a canonical variable for this business concept already exist?
```

For example, before creating:

```text
FD - DLV - print - solution_found
```

search for existing variables that already read:

```text
solution_found
```

If this already exists:

```text
SHARED - DLV - solution_found
```

and its contract is compatible, reuse it.

Do not create:

```text
FD - DLV - calculation - solution_found
FD - DLV - print - solution_found
FD - DLV - download - solution_found
```

when all three read the same source with the same meaning.

The preferred design is:

```text
               SHARED - DLV - solution_found
                          ↓
             ┌────────────┼────────────┐
             ↓            ↓            ↓
      Calculation Tag   Print Tag   Download Tag
```

Reuse a variable only when the following characteristics are compatible:

- source,
- data type,
- business meaning,
- allowed values,
- missing-data behavior,
- environment behavior,
- consent requirements.

If any of these are materially different, create a project-specific variable instead.

### 3. Design the variable contract

Before configuring GTM, define what the variable represents.

At minimum, document:

| Property         | Description                              |
| ---------------- | ---------------------------------------- |
| Name             | Canonical GTM variable name              |
| Scope            | Shared, FD, WEB, etc.                    |
| Business meaning | What the value represents                |
| Source           | Exact Data Layer path or other source    |
| Type             | String, number, boolean, etc.            |
| Allowed values   | Controlled values when applicable        |
| Required         | Whether the value must exist             |
| Missing behavior | Omit, block/fail QA, or approved default |
| Environment      | Production, staging, QA, or all          |
| Consumers        | Tags, triggers, or other variables       |
| Owner            | Team responsible for the variable        |
| Privacy          | Data/privacy classification              |
| Status           | Active, experimental, deprecated         |
| Replacement      | Replacement variable when deprecated     |

For example:

| Property         | Value                                                                             |
| ---------------- | --------------------------------------------------------------------------------- |
| Name             | `SHARED - DLV - solution_found`                                                   |
| Scope            | Shared, used by multiple compatible projects                                      |
| Business meaning | Indicates whether a completed calculation return at least one valid output result |
| Source           | `solution_found`                                                                  |
| Type             | Boolean                                                                           |
| Allowed values   | `true`, `false`                                                                   |
| Required         | Yes for completed calculation events                                              |
| Missing behavior | Fail QA; do not silently replace with unknown or another fallback                 |
| Environment      | All                                                                               |
| Consumers        | Calculation-related GA4 tags across FD and other participating projects           |
| Owner            | Shared Analytics / Analytics Governance                                           |
| Status           | Active                                                                            |
| Privacy          | Non-sensitive business data                                                       |
| Replacement      | None                                                                              |

The contract should be defined before GTM starts transforming or interpreting the value.

### 4. Prefer application-owned Data Layer values

If the application already knows a business value, expose it through the Data Layer instead of reconstructing it in GTM.

Preferred:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

Then GTM simply reads:

```text
SHARED - DLV - solution_found
→ solution_found

FD - DLV - inputs - connection_type
→ inputs.connection_type
```

Avoid depending on UI labels such as:

```text
Multi-Ply Connection
```

when the application can provide the stable business value:

```text
multi_ply_connection
```

This prevents UI wording, localization, or layout changes from changing analytics data.

The preferred ownership model is:

```text
Application
    ↓
Business rules
Stable business values
    ↓
Data Layer contract
    ↓
GTM Variables
    ↓
Tracking configuration
    ↓
GA4
```

GTM should not become a second implementation of application business logic.

### 5. Choose the appropriate variable type

Use the simplest native GTM variable type that satisfies the requirement.

| Requirement                        | Preferred type      | Avoid                                |
| ---------------------------------- | ------------------- | ------------------------------------ |
| Read an approved Data Layer value  | Data Layer Variable | DOM scraping                         |
| Reuse a fixed non-secret value     | Constant            | Repeating the value in multiple tags |
| Map an exact input to an output    | Lookup Table        | Large JavaScript `if` chains         |
| Map controlled patterns            | RegEx Table         | Complex Custom JavaScript            |
| Read URL information               | URL Variable        | Manually parsing `location`          |
| Read a first-party cookie          | First-Party Cookie  | Custom JavaScript when unnecessary   |
| Read stable legacy page content    | DOM Element         | Scraping localized or unstable UI    |
| Perform unavoidable transformation | Custom JavaScript   | Using JS as the default solution     |

Use the following decision flow:

```text
Is it an appropriate built-in value?
        ↓ No

Is it in the Data Layer contract?
        ↓ No

Is it a fixed reusable value?
        ↓ No

Is it an exact mapping?
        ↓ No

Is it a controlled pattern?
        ↓ No

Can URL / Cookie / another native type solve it?
        ↓ No

Consider Custom JavaScript
```

Custom JavaScript should be the last option, not the first.

#### Lookup and RegEx table safeguards

For every Lookup Table or RegEx Table, document the input variable, matching rules, output type, default behavior, and owner. Test case differences, missing inputs, overlapping patterns, and unknown values. RegEx rules are evaluated from top to bottom, so put the most specific rules first. Do not use a production destination as an accidental default.

### 6. Apply naming standards

Use a predictable naming structure:

```text
[SCOPE] - [TYPE] - [PURPOSE OR SOURCE]
```

Each part has a specific responsibility:

- **SCOPE** identifies which project or group owns the variable, such as `FD`, `WEB`, or `SHARED`.
- **TYPE** identifies the GTM variable type, such as `DLV`, `CONST`, or `LUT`.
- **PURPOSE OR SOURCE** describes either the exact Data Layer source or the human-readable purpose of the variable.

Examples:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type

FD - LUT - Hostname to Measurement ID
FD - CONST - GA4 Measurement ID - Production

SHARED - DLV - consent_state
```

#### Use `snake_case` for Data Layer fields

When a GTM variable directly reads a value from the Data Layer, preserve the exact field name defined by the Data Layer contract.

For example, if the application sends:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  solution_found: true,
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

the corresponding variables should be named:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type
```

This provides a direct relationship between the GTM variable and its source:

```text
SHARED - DLV - solution_found
               ↓
Data Layer: solution_found
```

and:

```text
FD - DLV - inputs - connection_type
           ↓
Data Layer: inputs.connection_type
```

#### Use human-readable names for GTM-managed concepts

When the variable represents configuration, routing, mapping, transformation, or another concept created and managed inside GTM, use a human-readable description.

For example:

```text
FD - CONST - GA4 Measurement ID - Production
FD - LUT - Hostname to Measurement ID
WEB - RLT - Page Path to Page Type
SHARED - COOKIE - Consent State
```

These variables do not directly represent Data Layer keys.

For example:

```text
FD - CONST - GA4 Measurement ID - Production
```

contains a GTM-managed configuration value:

```text
G-XXXXXXXXXX
```

There is no corresponding Data Layer field such as:

```text
ga4_measurement_id_production
```

Therefore, a human-readable name better describes its purpose.

Similarly:

```text
FD - LUT - Hostname to Measurement ID
```

describes a GTM mapping:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

#### Naming decision rule

Use the following rule when choosing between `snake_case` and a human-readable name:

| Variable represents    | Naming approach                                                    | Example                                        |
| ---------------------- | ------------------------------------------------------------------ | ---------------------------------------------- |
| Exact Data Layer field | Preserve `snake_case`                                              | `SHARED - DLV - solution_found`                |
| Nested Data Layer path | Preserve the exact path segments                                   | `FD - DLV - inputs - connection_type`          |
| GTM configuration      | Human-readable                                                     | `FD - CONST - GA4 Measurement ID - Production` |
| Lookup mapping         | Human-readable                                                     | `FD - LUT - Hostname to Measurement ID`        |
| RegEx classification   | Human-readable                                                     | `WEB - RLT - Page Path to Page Type`           |
| DOM concept            | Human-readable                                                     | `WEB - DOM - Checkout Total`                   |
| Cookie                 | Human-readable unless preserving an exact cookie name is important | `SHARED - COOKIE - Consent State`              |
| Custom transformation  | Human-readable purpose                                             | `FD - JS - Normalize Method`                   |

A simple decision flow is:

```text
Does the variable directly represent
a Data Layer field/path?
        │
   ┌────┴────┐
   │         │
  Yes        No
   │         │
   ▼         ▼
Preserve    Use a
the exact   human-readable
field/path  purpose
   │         │
   ▼         ▼
solution_found
            GA4 Measurement ID - Production

inputs.connection_type
            Hostname to Measurement ID
```

The goal is not to make every variable follow the same text style. The goal is to make every variable easy to trace and understand.

#### Recommended type prefixes

| Prefix   | Type                | Example                                 |
| -------- | ------------------- | --------------------------------------- |
| `DLV`    | Data Layer Variable | `SHARED - DLV - solution_found`         |
| `CONST`  | Constant            | `FD - CONST - GA4 Measurement ID - QA`  |
| `LUT`    | Lookup Table        | `FD - LUT - Hostname to Measurement ID` |
| `RLT`    | RegEx Table         | `WEB - RLT - Page Path to Page Type`    |
| `URL`    | URL Variable        | `WEB - URL - Query - campaign_id`       |
| `DOM`    | DOM Element         | `WEB - DOM - Checkout Total`            |
| `COOKIE` | First-Party Cookie  | `SHARED - COOKIE - Consent State`       |
| `JS`     | Custom JavaScript   | `FD - JS - Normalize Method`            |

Names should make the **scope, type, source, and purpose** understandable without opening the variable configuration.

Avoid ambiguous names such as:

```text
Variable 3
New Variable
Connection
Test
GA Value
```

### 7. Organize variables into folders

Use a consistent folder structure so variables are easy to discover, review, maintain, and audit across projects.

A folder should represent the **responsibility or purpose** of a variable, while the variable namespace identifies its **project ownership**.

For example:

```text
FD - DLV - inputs - connection_type
│
└── FD = project ownership

02 - Project Data Layer
│
└── Data Layer = responsibility
```

This approach is easier to scale than creating separate folder structures for every project.

#### Recommended folder structure

```text
00 - Documentation

01 - Shared Foundations
02 - Project Data Layer
03 - Routing and Environment
04 - Consent and Privacy
05 - Measurement Configuration
06 - Marketing and Attribution
07 - Utilities and Transformations
08 - Third-Party Integrations

90 - Experiments
95 - Migration
99 - Deprecated
```

The folders have the following responsibilities:

| Folder                               | Purpose                                                                                                     | Example                                         |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `00 - Documentation`                 | Documentation, conventions, ownership references, and other governance-related resources                    | Variable registry or naming-standard references |
| `01 - Shared Foundations`            | Variables representing concepts or configuration shared across multiple projects                            | `SHARED - DLV - solution_found`                 |
| `02 - Project Data Layer`            | Variables reading project-specific Data Layer values                                                        | `FD - DLV - inputs - connection_type`           |
| `03 - Routing and Environment`       | Variables responsible for environment detection, hostname routing, or destination selection                 | `FD - LUT - Hostname to Measurement ID`         |
| `04 - Consent and Privacy`           | Variables related to consent state, privacy controls, and collection permissions                            | `SHARED - DLV - consent_state`                  |
| `05 - Measurement Configuration`     | Variables containing reusable analytics configuration such as Measurement IDs                               | `FD - CONST - GA4 Measurement ID - Production`  |
| `06 - Marketing and Attribution`     | Variables related to campaigns, attribution, and marketing parameters                                       | `WEB - URL - Query - utm_source`                |
| `07 - Utilities and Transformations` | Approved helper variables used for simple mappings, normalization, or transformations                       | `FD - LUT - Connection Type Mapping`            |
| `08 - Third-Party Integrations`      | Variables required by approved analytics or marketing integrations outside the primary measurement platform | Vendor-specific configuration variable          |
| `90 - Experiments`                   | Temporary variables used for experiments, proofs of concept, or short-term testing                          | `FD - TEST - normalize_input`                   |
| `95 - Migration`                     | Variables temporarily required while moving from an old contract or implementation to a new one             | `SHARED - DLV - solution_found - v2`            |
| `99 - Deprecated`                    | Variables that have been replaced and are waiting for safe removal                                          | `FD - OLD - calculation_status`                 |

#### Example organization

A container may therefore contain:

```text
01 - Shared Foundations
└── SHARED - DLV - solution_found

02 - Project Data Layer
├── FD - DLV - inputs - connection_type
├── FD - DLV - inputs - fx
└── FD - DLV - inputs - fy

03 - Routing and Environment
└── FD - LUT - Hostname to Measurement ID

04 - Consent and Privacy
└── SHARED - DLV - consent_state

05 - Measurement Configuration
├── FD - CONST - GA4 Measurement ID - Production
└── FD - CONST - GA4 Measurement ID - Staging

06 - Marketing and Attribution
└── WEB - URL - Query - utm_source

07 - Utilities and Transformations
└── FD - LUT - Connection Type Mapping

90 - Experiments
└── FD - TEST - normalize_input

95 - Migration
└── SHARED - DLV - solution_found - v2

99 - Deprecated
└── FD - OLD - calculation_status
```

#### Folder and namespace responsibilities

Folders and namespaces solve different problems.

The namespace identifies **who owns the variable or where it belongs**:

```text
SHARED - ...
FD - ...
WEB - ...
```

The folder identifies **what responsibility the variable has**:

```text
02 - Project Data Layer
03 - Routing and Environment
05 - Measurement Configuration
```

For example:

```text
FD - LUT - Hostname to Measurement ID
```

means:

```text
FD
→ Owned by the FD project

LUT
→ Lookup Table variable

Hostname to Measurement ID
→ Purpose of the variable

03 - Routing and Environment
→ Responsibility/category
```

This separation avoids creating deeply nested project-specific structures and makes variables with similar responsibilities easier to audit across projects.

#### Folder creation rules

Do not create a folder simply because it appears in the recommended structure. Create it only when the container has variables that belong to that responsibility.

For example, if the container has no marketing or attribution variables, there is no need to create:

```text
06 - Marketing and Attribution
```

Similarly, variables should not be moved into `99 - Deprecated` merely because they appear unused. Their consumers must first be checked across tags, triggers, templates, Lookup Tables, RegEx Tables, and Custom JavaScript.

The recommended lifecycle is:

```text
Active variable
      ↓
Replacement identified
      ↓
Consumers migrated
      ↓
Move to 99 - Deprecated
      ↓
Verify no remaining dependencies
      ↓
Remove through a reviewed release
```

Shared standards do not automatically share variables between GTM containers. If multiple containers follow the same standard, each container still requires its own approved implementation.

#### Folder numbering convention

Folder numbers do not need to be sequential.

Folders `00–89` are reserved for active functional categories, while `90–99` are reserved for temporary and lifecycle states:

```text
00–89 → Active categories
90    → Experiments
95    → Migration
99    → Deprecated
```

### 8. Define missing-data behavior

Every variable must have an explicit behavior when its source value is unavailable.

Use one of three strategies:

| Strategy         | Use when                                      | Example                                                                  |
| ---------------- | --------------------------------------------- | ------------------------------------------------------------------------ |
| Omit             | The value is optional or not applicable       | `inputs.shearPlane` is absent for a connection type that does not use it |
| Block / fail QA  | The value is required for a valid event       | `solution_found` is missing from a completed calculation event           |
| Approved default | The business contract defines a real fallback | `language = en` only when `en` is an approved default                    |

For an optional FD input:

```text
Data Layer field not present
        ↓
FD - DLV variable
        ↓
undefined
        ↓
No artificial fallback
        ↓
Parameter omitted from GA4 hit
```

Do not automatically convert missing values into:

```text
unknown
N/A
empty
```

unless those values have an approved business meaning.

For required values:

```text
Required value missing
        ↓
Invalid tracking contract
        ↓
Block / fail QA
        ↓
Fix the Data Layer implementation
```

Missing behavior is part of the variable contract, not an arbitrary GTM fallback.

### 9. Configure environment-dependent variables

Variables whose values depend on the environment must explicitly distinguish production, staging, QA, and development.

For example:

```text
FD - LUT - Hostname to Measurement ID
```

can map:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

The expected routing is:

```text
                     FD
                      │
          ┌───────────┴───────────┐
          │                       │
 app.strongtie.com      app-staging.strongtie.com
          │                       │
          ▼                       ▼
 Production ID               Staging ID
          │                       │
          ▼                       ▼
 Production GA4               Test GA4
```

Do not use the production Measurement ID as an uncontrolled default.

An unknown environment should normally resolve to:

```text
Unknown hostname
→ undefined
→ Production tag does not fire
```

#### Current FD finding

The current FD Measurement ID configuration identifies FD primarily by its application path:

```text
/fd.*
→ G-VKTPQCSRW3
```

Both production and staging use the `/fd/...` path:

```text
app.strongtie.com/fd/...
app-staging.strongtie.com/fd/...
```

Testing confirmed that an event generated from the staging FD application resolved to:

```text
G-VKTPQCSRW3
```

which is the production FD Measurement ID.

The current flow can therefore become:

```text
app-staging.strongtie.com/fd/...
        ↓
      /fd.*
        ↓
G-VKTPQCSRW3
        ↓
Production GA4
```

This means staging calculations, QA activity, and debugging traffic can pollute production analytics.

The Measurement ID routing should therefore distinguish both the application and its environment.

#### Recommended FD configuration

The Measurement ID mapping should consider the environment, not only the application path.

For example:

```text id="eb7vrn"
app.strongtie.com
+ /fd/*
→ G-VKTPQCSRW3

app-staging.strongtie.com
+ /fd/*
→ G-STAGING123
```

The expected routing should be:

```text id="t7g5wk"
                         FD
                          │
              ┌───────────┴───────────┐
              │                       │
      app.strongtie.com      app-staging.strongtie.com
              │                       │
              ▼                       ▼
       G-VKTPQCSRW3              G-STAGING123
              │                       │
              ▼                       ▼
       Production GA4              Test GA4
```

If analytics collection is not required on staging, the staging environment should instead resolve to no production destination:

```text id="4d3c5b"
app-staging.strongtie.com
+ /fd/*
→ undefined
→ Production GA4 tag does not fire
```

A dedicated staging Measurement ID is preferable when the team needs to test GA4 events, parameters, Data Layer changes, and reporting without affecting production data.

### 10. Apply consent and privacy rules

Variables must not expose prohibited, private, or security-sensitive data.

Do not create variables containing:

```text
Email addresses
Passwords
Access tokens
Authentication tokens
Secrets
Credit-card information
Unrestricted user input
```

Consent-related variables may be used to support tag behavior, but they must not bypass the consent configuration that controls collection. Consent Initialization should establish default consent before other tags fire, and consent updates must be processed before dependent events. Review each tag's built-in consent checks and additional consent settings; a consent variable alone is not a Consent Mode implementation.

Every variable inventory entry should include a privacy classification when relevant.

The matrix covers the main user-defined types in this guide. Web GTM also provides built-in or utility variables such as Event, Auto-Event, Element Visibility, Environment Name, Container ID/version, Debug Mode, Analytics Storage, Google tag Event Settings, and User-provided Data. Enable or document these only when a scoped measurement requirement needs them. Do not assume this web-variable guidance applies unchanged to server-side GTM or mobile containers.

Never store secrets in GTM Constants. Published GTM container configuration is available to the browser.

### 11. Custom JavaScript policy

Custom JavaScript should be used only when native GTM variable types cannot safely meet the requirement.

Before creating a Custom JavaScript variable, check whether the requirement can be handled by:

- Data Layer Variable,
- Constant,
- Lookup Table,
- RegEx Table,
- URL Variable,
- First-Party Cookie,
- or another built-in GTM variable type.

For example:

```text
Hostname → Measurement ID
```

should normally use a **Lookup Table**, not Custom JavaScript.

#### Requirements for Custom JavaScript

When Custom JavaScript is necessary, it must follow these rules:

| Requirement                  | Meaning                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Small and focused**        | Perform one clear transformation or calculation. Avoid large business logic.                           |
| **Deterministic**            | The same input should always produce the same output.                                                  |
| **Synchronous**              | Return the value immediately. Do not depend on asynchronous API calls.                                 |
| **Side-effect free**         | Do not modify the DOM, application state, cookies, local storage, or push new Data Layer events.       |
| **Safe input handling**      | Handle `undefined`, `null`, invalid values, and unexpected data types without throwing errors.         |
| **Defined return type**      | Clearly document what type and values the variable can return.                                         |
| **Defined failure behavior** | Specify what happens when transformation fails, such as returning `undefined`.                         |
| **Privacy-safe**             | Do not read or expose PII, secrets, tokens, unrestricted user input, or other prohibited data.         |
| **Documented**               | Explain its purpose, inputs, outputs, fallback behavior, and why a native GTM variable cannot be used. |
| **Owned**                    | Assign a project or team responsible for maintaining it.                                               |
| **Reviewed**                 | Require code review before publishing or making significant changes.                                   |
| **Tested**                   | Test normal, missing, invalid, and edge-case inputs in GTM Preview.                                    |

At minimum, test:

```text
normal value
undefined
null
empty value
unexpected type
invalid value
```

For example, avoid unsafe code such as:

```javascript
function () {
  return {{FD - DLV - inputs - connection_type}}.toLowerCase();
}
```

If the Data Layer value is `undefined`, this can fail.

Prefer defensive handling:

```javascript
function () {
  var value = {{FD - DLV - inputs - connection_type}};

  if (typeof value !== "string" || value === "") {
    return undefined;
  }

  return value.toLowerCase();
}
```

The expected behavior is now explicit:

```text
Valid string
→ transformed value

undefined / null / invalid type
→ undefined
→ follow the defined missing-data behavior
```

#### Do not move application business logic into GTM

Custom JavaScript should not become a permanent workaround for a poor Data Layer contract.

For example, if the application sends a UI label:

```text
"Multi-Ply Connection"
```

while analytics requires:

```text
multi_ply_connection
```

GTM may temporarily transform the value during migration.

However, the preferred long-term solution is for the application to provide the stable analytics value directly:

```javascript
dataLayer.push({
  inputs: {
    connection_type: "multi_ply_connection",
  },
});
```

Then GTM simply reads:

```text
inputs.connection_type
```

The preferred flow is:

```text
Application
    ↓
Business logic and stable values
    ↓
Data Layer
    ↓
Native GTM Variables
    ↓
GA4
```

instead of:

```text
Application
    ↓
Display/raw values
    ↓
Large Custom JavaScript logic in GTM
    ↓
GA4
```

If Custom JavaScript is introduced as a temporary migration solution, document:

```text
Owner
Reason
Replacement plan
Expected removal condition
```

### 12. Test before publishing

Every new or modified variable should be tested in GTM Preview.

At minimum, verify:

- normal values,
- missing values,
- optional/not-applicable values,
- invalid values,
- edge values,
- staging environment,
- production environment,
- consent behavior,
- tag consumers,
- final GA4 payload.

For an FD calculation event, verify:

```text
Data Layer message
        ↓
DLV values
        ↓
Environment routing
        ↓
Trigger
        ↓
GA4 Event Tag
        ↓
g/collect payload
        ↓
Correct GA4 Measurement ID
```

Do not stop testing at:

```text
Tag Fired ✓
```

Verify the final request as well.

For example:

```text
Staging
→ tid = Staging Measurement ID

Production
→ tid = Production Measurement ID
```

For optional variables:

```text
DLV = undefined
→ parameter absent from final GA4 hit
```

For required variables:

```text
DLV = undefined
→ QA failure / event blocked
```

### 13. Review and publish

Changes that affect shared variables require review from every affected project or owner.

Before publishing:

1. Confirm the variable contract.
2. Confirm existing consumers.
3. Test all affected environments.
4. Verify consent and privacy behavior.
5. Review shared dependencies.
6. Record test evidence.
7. Publish a versioned GTM container change.
8. Add a meaningful release note.

Example release note:

```text
FD: Separate staging and production GA4 Measurement ID routing by hostname.
```

This makes future auditing and rollback easier.

### 14. Maintain a variable inventory

Maintain one searchable registry of approved variables.

Recommended fields:

| Field            | Purpose                             |
| ---------------- | ----------------------------------- |
| Variable         | Canonical name                      |
| Scope            | Shared, FD, WEB, etc.               |
| Type             | DLV, LUT, Constant, etc.            |
| Exact source     | Data Layer path or input            |
| Business meaning | What the value represents           |
| Expected type    | String, boolean, number, etc.       |
| Allowed values   | Controlled values                   |
| Required         | Yes/No                              |
| Missing behavior | Omit, block, default                |
| Consumers        | Tags/triggers/variables             |
| Owner            | Responsible team                    |
| Environment      | All, production, staging, etc.      |
| Privacy          | Data classification                 |
| Status           | Active, test, deprecated            |
| Review date      | Last review                         |
| Replacement      | Canonical replacement if deprecated |

Example:

| Variable                         | Scope  | Source              | Consumers           | Missing behavior     | Owner        | Status     |
| -------------------------------- | ------ | ------------------- | ------------------- | -------------------- | ------------ | ---------- |
| `SHARED - DLV - consent_state`   | Shared | `consent_state`     | Shared tags         | Block where required | Governance   | Active     |
| `SHARED - DLV - solution_found`  | Shared | `solution_found`    | FD calculation tags | Fail QA              | Governance   | Active     |
| `FD - DLV - inputs - shearPlane` | FD     | `inputs.shearPlane` | FD calculation tag  | Omit                 | FD Analytics | Active     |
| `FD - OLD - calculation_status`  | FD     | Legacy              | None                | N/A                  | FD Analytics | Deprecated |

Search this inventory before creating a new variable.

### 15. Deprecate and retire safely

Do not delete a variable only because it appears unused.

First check whether it is consumed by:

```text
Tags
Triggers
Lookup Tables
RegEx Tables
Custom Templates
Custom JavaScript
```

Use the following retirement flow:

```text
Identify replacement
        ↓
Mark old variable deprecated
        ↓
Find all consumers
        ↓
Migrate consumers
        ↓
Test in Preview
        ↓
Publish reviewed change
        ↓
Monitor
        ↓
Remove old variable
```

For example:

```text
FD - OLD - calculation_status
```

may be replaced by:

```text
SHARED - DLV - solution_found
```

The old variable should remain in the Deprecated folder until all consumers have migrated and the replacement has been verified.

## Multi-project audit cases

When reviewing an existing container, look for the following issues:

| Audit case              | Example                                               | Action                                |
| ----------------------- | ----------------------------------------------------- | ------------------------------------- |
| Duplicate               | Multiple DLVs read the same `solution_found` contract | Consolidate to one canonical variable |
| DOM-dependent           | Variable reads localized UI text                      | Move the value into the Data Layer    |
| Environment leak        | Staging resolves to production Measurement ID         | Add explicit environment routing      |
| Missing-data masking    | `undefined` becomes `unknown` without approval        | Restore explicit omit/block behavior  |
| Unused                  | Variable has no approved consumers                    | Confirm ownership and retire          |
| Risky Custom JavaScript | JS reconstructs application business logic            | Redesign the Data Layer contract      |
| Temporary variable      | `TEST` variable became a permanent dependency         | Review and promote or retire          |

## Example: FD Calculation Variable Management Flow

Suppose the application emits:

```javascript
window.dataLayer.push({
  event: "calculation_action",
  event_schema_version: "1.0",
  app_name: "fd",
  solution_found: true,
  inputs: {
    country: "gb",
    connection_type: "clt_floor_floor_half_lap_joint",
    unit_system: "metric",
    fx: 1,
    fy: 0,
  },
});
```

### Step 1 — Define the Data Layer contract

| Field                    | Type    |    Required | Missing behavior         | Source                          |
| ------------------------ | ------- | ----------: | ------------------------ | ------------------------------- |
| `event`                  | string  |         Yes | Block                    | Application calculation handler |
| `event_schema_version`   | string  |         Yes | Fail QA                  | Application constant            |
| `app_name`               | string  |         Yes | Fail QA                  | Application constant            |
| `solution_found`         | boolean |         Yes | Fail QA                  | Calculation response            |
| `inputs.connection_type` | string  |         Yes | Fail QA                  | Calculation input snapshot      |
| `inputs.fx`              | number  | Conditional | Omit when not applicable | Calculation input snapshot      |
| `inputs.fy`              | number  | Conditional | Omit when not applicable | Calculation input snapshot      |

### Step 2 — Search the inventory

Check whether canonical variables already exist.

Reuse them when their contracts match.

### Step 3 — Create only missing canonical variables

For example:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type
FD - DLV - inputs - fx
FD - DLV - inputs - fy
FD - DLV - event_schema_version
```

Use the exact Data Layer paths and do not create tag-specific copies.

### Step 4 — Configure environment routing

Use:

```text
FD - LUT - Hostname to Measurement ID
```

to explicitly route:

```text
app.strongtie.com
→ Production Measurement ID

app-staging.strongtie.com
→ Staging Measurement ID
```

### Step 5 — Connect variables to the GA4 tag

The GA4 Event tag consumes the canonical variables:

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

### Step 6 — Apply missing-data rules

For required values:

```text
solution_found = undefined
→ QA failure
```

For conditionally applicable values:

```text
fx not applicable
→ undefined
→ omit from GA4 hit
```

Do not replace either case with an arbitrary `"unknown"` value.

### Step 7 — Test end-to-end

Verify:

```text
Application
    ↓
Data Layer contract
    ↓
Canonical GTM Variables
    ↓
Environment routing
    ↓
Trigger
    ↓
GA4 Event Tag
    ↓
Final g/collect payload
    ↓
Correct GA4 destination
```

Test normal calculations, no-solution responses, missing inputs, optional inputs, API errors, duplicate callbacks, staging, production, and consent states.

### Step 8 — Publish and maintain

After development, analytics, and QA review:

```text
Test evidence
    ↓
Versioned GTM release
    ↓
Release note
    ↓
Update Variable Registry
    ↓
Monitor
```

Any replaced variables should enter the deprecation workflow rather than being immediately deleted.

---

## Summary

A scalable GTM variable-management process should follow this lifecycle:

```text
CLASSIFY
What project or scope owns the variable?
        ↓
SEARCH
Does a canonical variable already exist?
        ↓
DESIGN
What is its source, meaning, type, and missing behavior?
        ↓
SELECT TYPE
What is the simplest native GTM variable type?
        ↓
NAME & ORGANIZE
Apply namespace and folder standards.
        ↓
CONFIGURE
Apply environment, consent, privacy, and fallback rules.
        ↓
TEST
Verify Data Layer → Variable → Tag → GA4 payload.
        ↓
PUBLISH
Review and release a versioned GTM change.
        ↓
MAINTAIN
Update ownership, consumers, and inventory.
        ↓
RETIRE
Deprecate and safely remove obsolete variables.
```

## References

- [Tag Manager Help — Variable](https://support.google.com/tagmanager/answer/13355320?hl=en): the role of variables in tags and triggers.
- [Tag Manager Help — User-defined variable types for web](https://support.google.com/tagmanager/answer/7683362?hl=en): supported user-defined variable types, including Data Layer, URL, cookie, DOM, and Custom JavaScript variables.
- [Tag Manager Help — Components of Google Tag Manager](https://support.google.com/tagmanager/answer/6103657?hl=en): the relationship between variables, the data layer, triggers, and tags.
- [Google for Developers — The data layer](https://developers.google.com/tag-platform/tag-manager/datalayer): data layer values, event processing, persistence, and naming considerations.
