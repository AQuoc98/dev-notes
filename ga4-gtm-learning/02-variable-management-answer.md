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

## Key aspects of variable management

| Aspect                       | Simple explanation                                                                                                                  |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| **Know its job**             | Each variable should have one clear responsibility.                                                                                 |
| **Use the right type**       | Use the simplest GTM variable type that can solve the problem.                                                                      |
| **Name it clearly**          | The variable name should clearly describe what data it contains and, when useful, where it comes from.                              |
| **Reuse it**                 | Create one shared variable for the same piece of information instead of creating duplicates.                                        |
| **Use the Data Layer first** | Read data provided by the application through the Data Layer instead of scraping it from the DOM.                                   |
| **Keep changes simple**      | Keep business logic and complex data transformations in the application. GTM should mainly read values and perform simple mappings. |
| **Separate environments**    | Keep tracking configuration for QA, staging, and production clearly separated.                                                      |
| **Handle missing data**      | Define what should happen when a required value is missing: omit it, block the tag, or use an approved default.                     |
| **Protect users**            | Do not expose PII, credentials, tokens, secrets, or other sensitive data through GTM variables.                                     |
| **Test and clean up**        | Test variables before release, document where they are used, and retire variables that are no longer needed.                        |

### Know its job

Each GTM variable should have one clear and predictable responsibility.

A variable should answer one simple question such as:

```text
What is the current connection type?
```

or:

```text
What is the current solution status?
```

For example:

```text
SHARED - DLV - solution_found
```

should only read from the Data Layer:

```text
solution_found
```

It should not also:

- rename the value,
- read text from the DOM,
- decide which Measurement ID to use,
- apply unrelated business rules,
- or combine several unrelated values.

Keeping variables focused makes them easier to understand, test, debug, and reuse.

A good variable behaves like this:

```text
Data Layer
    ↓
solution_found = true
    ↓
    SHARED - DLV - solution_found
    ↓
true
```

A poor design would make one variable responsible for several unrelated jobs:

```text
Read solution_found
+ check hostname
+ rename the value
+ choose GA4 Measurement ID
+ apply fallback logic
```

That makes the variable harder to maintain because one small change can affect several tracking behaviors.

A useful rule is:

> One variable should represent one clear piece of information or one simple transformation.

### Use the right type

Choose the simplest native GTM variable type that matches the requirement.

GTM already provides several variable types for common tasks. Use those before creating Custom JavaScript.

For example:

| Requirement                                       | Recommended GTM variable type |
| ------------------------------------------------- | ----------------------------- |
| Read `inputs.connection_type` from the Data Layer | Data Layer Variable           |
| Store a fixed Measurement ID                      | Constant                      |
| Map hostname to Measurement ID                    | Lookup Table                  |
| Read pathname, hostname, or query parameters      | URL Variable                  |
| Read a value from the page DOM                    | DOM Element Variable          |
| Perform logic that native GTM types cannot handle | Custom JavaScript             |

For example, suppose different domains use different GA4 Measurement IDs:

```text
qa.example.com   → G-QA123
www.example.com  → G-PROD456
```

This can be handled with a Lookup Table:

```text
Input:
{{Page Hostname}}

qa.example.com
→ G-QA123

www.example.com
→ G-PROD456
```

There is no need to write:

```js
function () {
  if ({{Page Hostname}} === 'qa.example.com') {
    return 'G-QA123';
  }

  if ({{Page Hostname}} === 'www.example.com') {
    return 'G-PROD456';
  }
}
```

Both approaches may work, but the Lookup Table is easier to read and maintain.

The principle is:

> Do not use a more complex variable type when GTM already provides a simpler native type for the same job.

Custom JavaScript should normally be the last option, not the default option.

### Name it clearly

A GTM variable name should clearly communicate:

- which project or application it belongs to,
- what type of variable it is,
- what value it contains,
- and, when useful, where the value comes from.

For example:

```text
FD - DLV - inputs - connection_type
```

is much clearer than:

```text
Variable 3
```

The first name immediately tells another developer:

```text
FD
→ belongs to the FD application

DLV
→ Data Layer Variable

inputs
→ comes from the inputs object

connection_type
→ contains the connection type
```

A consistent naming convention also makes searching and grouping variables easier.

For example:

```text
SHARED - DLV - solution_found
FD - DLV - inputs - connection_type
FD - DLV - inputs - country
FD - CONST - GA4 Measurement ID
FD - LUT - Hostname - GA4 Measurement ID
```

This is especially useful in containers with many tags, triggers, and variables.

Avoid names such as:

```text
Test Variable
New Variable
Connection
Variable 2
Temp
GA Value
```

These names provide little information and become difficult to understand later.

The main goal is:

> Someone who did not create the variable should still be able to understand its purpose from the name.

### Reuse it

Create one canonical GTM variable for one shared business concept.

If several tags need the same value, they should normally reuse the same variable.

For example:

```text
SHARED - DLV - solution_found
```

may be used by:

```text
Calculation Tag
Print Result Tag
Download Result Tag
Share Result Tag
```

The structure should look like:

```text
              SHARED - DLV - solution_found
                         ↓
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
Calculation Tag     Print Tag      Download Tag
```

Avoid creating:

```text
FD - DLV - calculation - solution_found
FD - DLV - print - solution_found
FD - DLV - download - solution_found
```

if all three variables read exactly the same Data Layer value.

Duplicate variables create several problems:

- more configuration to maintain,
- higher risk of inconsistent settings,
- harder debugging,
- harder migration,
- and uncertainty about which variable is the correct one.

A new variable is justified when something is meaningfully different, such as:

- the source is different,
- the expected type is different,
- the fallback is different,
- the business meaning is different,
- or a transformation is required.

The principle is:

> One shared business value should normally have one shared GTM variable.

### Use the Data Layer first

When the application already knows a value, provide it through the Data Layer instead of asking GTM to discover it from the page.

Suppose the user selects:

```text
Connection Type: Wood-to-Wood
```

The application already knows the internal value:

```text
wood_to_wood
```

It can push:

```js
dataLayer.push({
  event: "calculation_action",
  inputs: {
    connection_type: "wood_to_wood",
  },
});
```

GTM can then create:

```text
FD - DLV - inputs - connection_type
```

which reads:

```text
inputs.connection_type
```

The preferred flow becomes:

```text
Application
    ↓
Data Layer
    ↓
GTM Variable
    ↓
Tag
    ↓
GA4
```

Avoid this approach when possible:

```text
Application
    ↓
HTML / DOM
    ↓
GTM searches the page
    ↓
DOM Element Variable
    ↓
Tag
```

DOM-based tracking can break when UI implementation changes.

For example:

```html
<span class="connection-type"> Wood-to-Wood </span>
```

might later become:

```html
<div class="connection-type-value">Wood-to-Wood</div>
```

The business concept did not change, but DOM-based tracking may stop working.

The Data Layer provides a stable contract between the application and GTM.

The principle is:

> If the application already knows the value, expose that value through the Data Layer rather than scraping it from the UI.

### Keep changes simple

Business rules and complex transformations should normally be handled by the application rather than recreated inside GTM.

For example, the UI may display:

```text
CLT Floor-to-Floor Half-Lap Joint
```

but analytics may require the stable value:

```text
clt_floor_floor_half_lap_joint
```

The application should ideally provide that stable value directly:

```js
dataLayer.push({
  event: "calculation_action",
  inputs: {
    connection_type: "clt_floor_floor_half_lap_joint",
  },
});
```

GTM should then simply read:

```text
inputs.connection_type
```

Avoid duplicating business logic inside several GTM variables. Then a business rule change must be updated in several places. This creates a risk of inconsistent analytics.

A better separation is:

```text
Application
    ↓
Business rules
Normalization
Stable analytics values
    ↓
Data Layer
    ↓
GTM
Simple mappings / configuration
    ↓
GA4
```

GTM can still perform simple configuration tasks such as:

- hostname mapping,
- simple Lookup Tables,
- environment routing,
- value pass-through,
- simple formatting.

The principle is:

> Keep business logic close to the application that owns the data, and keep GTM focused on tracking configuration.

### Separate environments

Variables whose values depend on the environment should be configured explicitly so that development, QA, staging, and production use the correct values.

For example, if the same GTM container is used for both staging and production, the GA4 Measurement ID should not be hard-coded directly in every tag.

Instead, create a shared variable that returns the correct Measurement ID based on the current hostname.

For example, a Lookup Table can use:

```text id="hqgwph"
{{Page Hostname}}
```

as its input:

```text id="avbweo"
app-staging.example.com → G-STAGING123
app.example.com         → G-PROD456
```

The flow becomes:

```text id="ynpgnb"
Page Hostname
      ↓
GA4 Measurement ID Variable
      ↓
┌───────────────────────────────┐
│ app-staging.example.com       │
│ → G-STAGING123                │
│                               │
│ app.example.com               │
│ → G-PROD456                   │
└───────────────────────────────┘
      ↓
Google Tag / GA4 Event Tags
```

All relevant tags can then reuse the same variable:

```text id="n8k5rf"
{{FD - LUT - GA4 Measurement ID}}
```

This keeps environment-specific configuration in one place and reduces the risk of sending staging or test traffic to the production GA4 property.

Avoid using the production value as an automatic fallback:

```text id="x6fcgf"
Default:
G-PROD456
```

An unknown hostname could then accidentally send data to production.

A safer configuration is:

```text id="1tt9ye"
Unknown hostname
→ undefined
→ tag does not fire
```

This rule is especially useful when **one GTM container is shared across multiple environments**.

If staging and production already use completely separate GTM containers and each container has its own Measurement ID, a hostname Lookup Table may not be necessary. The important requirement is still the same: environment-dependent values must be clearly separated and controlled. A hostname Lookup Table is routing logic; it is not a replacement for GTM Environments, which publish specific container versions to development, QA, or live environments.

### Handle missing data

Every important variable should have a defined behavior when its value is unavailable.

Suppose the Data Layer contract requires:

```text
inputs.connection_type
```

but the application pushes:

```js
dataLayer.push({
  event: "calculation_action",
  inputs: {},
});
```

The GTM variable becomes:

```text
FD - DLV - inputs - connection_type
→ undefined
```

At this point, GTM should not make an arbitrary decision.

The expected behavior should already be defined.

There are three common strategies:

| Strategy         | When to use it                                      | Example                                                           |
| ---------------- | --------------------------------------------------- | ----------------------------------------------------------------- |
| Omit             | The parameter is optional                           | Do not send `product_category` when no value exists               |
| Block            | The value is required for the event to be valid     | Do not fire the calculation tag when `connection_type` is missing |
| Approved default | A real business default has been explicitly defined | Use `language = en` only if `en` is the approved fallback         |

Avoid silently converting missing required values into:

```text
unknown
```

For example:

```text
connection_type = undefined
```

becoming:

```text
connection_type = unknown
```

can hide a Data Layer problem.

GA4 may then show:

```text
calculation_action
connection_type = unknown
```

which looks like valid tracking data even though the application failed to provide a required field.

For required fields, it is usually better for QA to detect the issue:

```text
Required variable missing
        ↓
Tag blocked
        ↓
QA failure
        ↓
Fix Data Layer implementation
```

Optional fields can simply be omitted when the event contract allows it.

The principle is:

> Missing data should have an explicit behavior; it should never be handled accidentally or silently.

### Protect users

GTM variables must not expose personal, sensitive, confidential, or security-related information.

Avoid creating variables that contain:

```text
email address
password
access token
authentication token
session secret
credit card number
private user text
unrestricted form input
```

For example, this should not be pushed into the Data Layer:

```js
dataLayer.push({
  event: "sign_in",
  email: "user@example.com",
  access_token: "abc123...",
});
```

And GTM should not create variables such as:

```text
DLV - user_email
DLV - access_token
```

Even if the value is not intentionally sent to GA4, exposing it to the tracking layer creates unnecessary privacy and security risk.

Instead, analytics should use non-sensitive business information where appropriate:

```js
dataLayer.push({
  event: "sign_in",
  login_method: "google",
});
```

The principle is:

> Only expose data that is necessary, approved, and safe for analytics use.

### Test and clean up

Variable management does not end when a variable is created.

Every variable should be tested before release and reviewed throughout its lifecycle.

In GTM Preview, test different scenarios such as:

```text
Normal value
Missing value
Unexpected value
Empty string
Zero
false
Special characters
Different environments
Different events
```

For example:

```text
FD - DLV - inputs - connection_type
```

could be tested with:

```text
wood_to_wood
steel_to_wood
undefined
""
invalid_value
```

Check:

```text
What value does the variable return?
Which tags consume it?
Does the tag fire correctly?
What happens when the value is missing?
Does the behavior match the Data Layer contract?
```

Important shared variables should also have basic documentation.

Useful metadata may include:

```text
Variable:
FD - DLV - inputs - connection_type

Owner:
FD Analytics / Development Team

Source:
inputs.connection_type

Type:
Data Layer Variable

Consumers:
Calculation Complete
Print Result
Download Result

Required:
Yes

Missing behavior:
Block event

Environment:
All

Status:
Active
```

Variables should also be cleaned up when they are no longer needed.

A safe retirement flow is:

```text
Identify unused variable
    ↓
Check all Tags and Triggers
    ↓
Confirm replacement if applicable
    ↓
Mark as deprecated
    ↓
Test
    ↓
Remove in reviewed GTM release
```

Avoid leaving many variables such as:

```text
OLD - connection_type
connection_type_new
connection_type_new_2
test_connection
backup_connection
```

Unused variables increase container complexity and make future changes harder.

The principle is:

> Variables should have a lifecycle: create, test, document, reuse, review, deprecate, and remove.

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
