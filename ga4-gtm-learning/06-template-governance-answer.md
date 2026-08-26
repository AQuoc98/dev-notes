# 06 — GTM Template Governance & Management Reference

## Theory

### What is a GTM template?

A template defines the configuration UI and sandboxed implementation for a GTM **tag type** or **variable type**. It determines which fields a user can configure, how those fields are validated, what the implementation can do, and which permissions it requires.

The Templates area contains custom and imported tag and variable templates in the container. It is not a list of every configured tag or variable.

### Template vs Tag vs Variable

A template defines **how a type works**. A tag or variable is a configured **instance** created from that template.

```text
Template: "Vendor Analytics Tag"
        ↓ defines
Configuration fields, validation, permissions, and sandboxed implementation
        ↓ creates
Tag instances
        ↓
Vendor Analytics — Purchase
Vendor Analytics — Sign Up
```

| Object       | Role                                                                     | Example                      |
| ------------ | ------------------------------------------------------------------------ | ---------------------------- |
| **Template** | Defines a reusable tag or variable type                                  | A vendor event template      |
| **Tag**      | Configured instance that performs an action when eligible to run         | Send `purchase` to a vendor  |
| **Variable** | Configured instance that returns or transforms a value for use elsewhere | Normalize a product category |

Changing a tag instance normally affects only that instance. Changing the underlying template can affect every dependent tag or variable instance, so template changes require dependency analysis and regression testing.

### Why templates exist

Templates provide a controlled way to package functionality for GTM users. They can:

- expose a clear configuration UI instead of requiring users to edit implementation code;
- validate inputs before a tag or variable is used;
- run implementation logic in GTM's sandboxed environment;
- declare the APIs and permissions they need;
- standardize vendor integrations and internal patterns;
- make review, testing, ownership, versioning, and retirement possible.

Templates improve consistency and reduce avoidable implementation risk, but they do not remove the need for security, privacy, consent, or operational review.

## Template Types

GTM custom templates are primarily used to define two types:

| Type                  | Purpose                                                                      | Example                                     |
| --------------------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| **Tag Template**      | Defines an action a configured tag can perform                               | Send an approved event to a vendor endpoint |
| **Variable Template** | Defines how a configured variable calculates, normalizes, or returns a value | Return a normalized campaign value          |

A useful mental model is:

```text
Tag Template      → performs an action
Variable Template → returns a value
```

The distinction matters during review. A tag template must document execution, destination, consent, and success/failure behavior. A variable template must document input sources, transformation rules, validation, and what it returns when input is missing or invalid.

## Template Sources

| Source                         | Meaning                                                   | Governance expectation                                                                                       |
| ------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Built-in**                   | A Google-provided template or supported GTM option        | Preferred when it meets the requirement; follow normal configuration and testing controls                    |
| **Community Template Gallery** | A template published by a third party through the Gallery | Review publisher, source, license, maintenance, code, permissions, endpoints, consent, and updates           |
| **Custom**                     | A template developed and maintained by the organization   | Requires an owner, source history, security/privacy review, tests, version control, and lifecycle management |

### Preferred selection order

Use the safest supported option that satisfies the approved requirement:

1. A Google-provided built-in template that meets the requirement.
2. A carefully reviewed Community Template Gallery template.
3. A custom template with a named maintainer, code/security review, and tests.
4. Custom HTML or Custom JavaScript only as a documented exception when safer supported options cannot meet the requirement.

The last option is an exception path, not a shortcut around template governance.

## Anatomy of a Template

A production template should be understandable through the following components. If a component does not apply, record `None` or `Not applicable` rather than leaving its behavior unclear.

| Component                    | Question to answer                                                                                                            |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Purpose**                  | Why does this template exist, and what approved business or measurement requirement does it satisfy?                          |
| **Type**                     | Is it a tag template or a variable template?                                                                                  |
| **Fields**                   | What configuration does the user provide? Are labels, help text, and defaults clear?                                          |
| **Validation**               | Which values are accepted, rejected, required, or normalized?                                                                 |
| **Sandboxed implementation** | What does the template code do, step by step?                                                                                 |
| **APIs**                     | Which GTM sandboxed APIs does it use, and why?                                                                                |
| **Permissions**              | What resources or capabilities may the implementation access?                                                                 |
| **Endpoints**                | Where can a tag send data? Are destinations explicit and approved?                                                            |
| **Data handling**            | What data can it read, transform, store, or send? Is prohibited personal data excluded?                                       |
| **Consent**                  | What consent state is required, and what happens when consent is denied, unknown, or changes?                                 |
| **Success/failure**          | How does a tag report or handle success, timeout, invalid input, and network failure? What does a variable return on failure? |
| **Consumers**                | Which configured tags, variables, teams, reports, or downstream systems depend on it?                                         |
| **Owner**                    | Who maintains it, approves changes, answers questions, and confirms retirement?                                               |
| **Version**                  | Which imported, released, or source commit version is approved?                                                               |
| **Lifecycle**                | Is it Proposed, Under Review, Approved, Active, Deprecated, or Retired?                                                       |

```text
Template
├── Configuration UI and fields
├── Validation
├── Sandboxed implementation
├── APIs and permissions
├── Data, endpoints, and consent behavior
├── Tests
└── Ownership, version, and lifecycle metadata
        ↓
Dependent tag or variable instances
```

## Sandboxing and Permissions

Custom templates run in GTM's sandboxed JavaScript environment rather than unrestricted page JavaScript. The sandbox limits what template code can do directly. Access to sensitive capabilities is provided through approved sandboxed APIs and template permissions.

Conceptually:

```text
Template code
      ↓
Sandboxed API
      ↓
Permission check
      ↓
Allowed resource or action
```

### Least privilege

> A template should receive only the permissions required to perform its documented purpose.

For example, a template that only needs to send an approved request to `analytics.vendor.example` should not request unrelated storage, cookie, page, or network capabilities. Each permission should have a documented business and technical justification.

### Important security principle

```text
Sandboxed ≠ automatically safe
```

Sandboxing reduces the implementation's access, but a template can still be unsafe or unsuitable if it has overly broad permissions, sends data to an unknown endpoint, handles data incorrectly, has a malicious or unmaintained source, or changes behavior without review. Community templates must therefore be reviewed as third-party code.

## Template Decision Guide

Before importing, approving, or creating a template, use this production gate:

1. **Is there an approved requirement?**  
   If no, do not import or build the template.
2. **Can a built-in template satisfy it?**  
   If yes, use the built-in option.
3. **Is a reviewed Community Template available?**  
   If yes, evaluate it before building custom code.
4. **Is the source trustworthy and maintained?**  
   If no, reject it or investigate a safer alternative.
5. **Are all requested permissions necessary?**  
   If no, reduce them or reject the template.
6. **Are endpoints and data handling understood and approved?**  
   If no, do not approve it.
7. **Is consent behavior defined?**  
   If no, define it before production use.
8. **Are dependent tags and variables known?**  
   If no, inventory consumers before release or update.
9. **Can the template and representative consumers be tested?**  
   If no, do not publish it.
10. **Is there an owner, version record, and update/rollback plan?**  
    If no, assign ownership and define the operating process first.

## Design Standards

Every approved template should follow these standards:

- Start with a documented business or measurement requirement.
- Prefer the least powerful supported implementation that meets the requirement.
- Use clear field labels, help text, safe defaults, and explicit required/optional behavior.
- Validate inputs at the template boundary; do not rely only on downstream systems to reject bad values.
- Keep permissions narrowly scoped and explain each one.
- Make endpoints explicit, environment-aware, and approved.
- Document what data is read, transformed, stored, and sent.
- Define consent behavior before implementation is made production-active.
- Define deterministic behavior for missing, invalid, denied-consent, duplicate, timeout, and network-failure cases.
- Avoid secrets in template code, fields, examples, or configuration.
- Never approve collection or transmission of prohibited personal data.
- Keep source, version, change history, tests, owner, and dependent consumers discoverable.
- Make updates reviewable and reversible.

## Practical Example — Evaluate a Community Tag Template

### Scenario

The team needs to send a `purchase_completed` event to **Vendor X Analytics** when an approved purchase is completed. GTM has no built-in tag that supports Vendor X's required request format.

### Evaluation

```text
Requirement:
Send one approved purchase_completed event to Vendor X
        ↓
Built-in template available?
No
        ↓
Community Template Gallery template available?
Yes — Vendor X Analytics Tag
        ↓
Review publisher, repository, license, maintenance, and documentation
        ↓
Review fields, code, permissions, endpoint, data handling, and consent
        ↓
Import into a non-production workspace
        ↓
Test normal, invalid, duplicate, denied-consent, and network-failure cases
        ↓
Approve the exact version and record consumers, owner, and rollback plan
```

### Concrete review record

| Review area      | Evidence or decision                                                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Purpose          | Send one `purchase_completed` event after the application confirms a completed purchase                                                                      |
| Type             | Tag Template                                                                                                                                                 |
| Data inputs      | `transaction_id`, `currency`, `value`, and approved item summary from the data layer                                                                         |
| Trigger contract | Authoritative `purchase_completed` event; not a generic button click                                                                                         |
| Endpoint         | `https://collect.vendor-x.example/events`; production and staging destinations are explicitly separated                                                      |
| Permissions      | Only the sandboxed capabilities required to read approved values and send the request                                                                        |
| Consent          | Send only when the approved analytics/marketing consent requirement is satisfied; denied or unresolved consent prevents the request                          |
| Success/failure  | Record or expose the documented success path; do not retry in a way that creates duplicate purchases unless retry behavior is explicitly designed and tested |
| Consumers        | `Vendor X — Purchase Completed` tag and the Vendor X conversion workflow                                                                                     |
| Decision         | Approve only the reviewed version; reject if permissions are broader than necessary or maintenance is unclear                                                |

## 10. Inventory & Ownership

Maintain a central inventory for every non-built-in template and its dependent instances.

| Template | Type           | Purpose              | Origin/source      | Version         | Permissions | Endpoints                 | Consent               | Consumers          | Owner           | Last review  | Status | Replacement             |
| -------- | -------------- | -------------------- | ------------------ | --------------- | ----------- | ------------------------- | --------------------- | ------------------ | --------------- | ------------ | ------ | ----------------------- |
| `[name]` | Tag / Variable | `[approved purpose]` | Gallery / `[repo]` | `[SHA/version]` | `[summary]` | `[approved destinations]` | `[required behavior]` | `[tags/variables]` | `[team/person]` | `YYYY-MM-DD` | Active | `[replacement or None]` |

Ownership must cover more than the person who imported the template. The owner is accountable for maintenance, review cadence, consumer impact analysis, update decisions, incident response, and retirement. A template without a named owner should not become production-active.

## Test Workflow

1. Import or edit the template only in a dedicated non-production workspace.
2. Review fields, validation, implementation, APIs, permissions, endpoints, data handling, consent, and tests before creating consumers.
3. Test the template directly where supported, including normal, missing, invalid, denied-consent, and failure cases.
4. Test representative dependent tags or variables in GTM Preview.
5. Inspect the resulting network behavior: destination, payload, request count, and environment.
6. Test negative and duplicate cases so one business occurrence does not create unintended duplicate requests.
7. Record evidence, defects, approved version, owner, and rollback/export information.
8. Publish through normal version controls only after review approval.

## Template Update Impact

A template update is a code and dependency change, not merely a metadata change. One template can be used by many configured tag or variable instances:

```text
Community Template v1
        ↓
Tag A   Tag B   Tag C

Template updated to v2
        ↓
Potential behavior change for A, B, and C
```

An update can affect every dependent instance through changed implementation code, fields, defaults, validation, permissions, endpoints, data handling, consent behavior, or success/failure behavior. Before accepting an update:

1. Identify all dependent tags and variables.
2. Review the change details, source diff, and exact version.
3. Review added, removed, or changed permissions and endpoints.
4. Review fields, defaults, validation, and consent behavior.
5. Retest representative consumers, including critical and high-volume ones.
6. Record the approved version and rollback path.
7. Publish the update only after the same approval standard is met.

Treat a Gallery update as a new code change even when it is offered as an automatic or routine update.

## Common Anti-patterns

| Anti-pattern                                             | Problem                                                         | Preferred approach                                                               |
| -------------------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Importing a Gallery template without review              | Third-party code and permissions are trusted implicitly         | Review publisher, source, permissions, endpoints, data, consent, and maintenance |
| Using custom code when a supported template exists       | More implementation and maintenance risk than necessary         | Follow the preferred selection order                                             |
| Broad permissions                                        | The template can access more than its purpose requires          | Apply least privilege and justify every permission                               |
| Unknown network endpoints                                | Data destination cannot be governed or validated                | Document and approve exact endpoints and environments                            |
| Automatic updates without review                         | Dependent instances may change behavior unexpectedly            | Treat every update as a new code change                                          |
| No consumer inventory                                    | Updates or retirement can break dependent tags/variables        | Inventory consumers before approval, update, or removal                          |
| No owner                                                 | Security, maintenance, and retirement decisions become orphaned | Assign a named accountable owner                                                 |
| Production editing                                       | Changes are harder to test, review, and roll back               | Use a non-production workspace and normal version controls                       |
| Secrets in template code or fields                       | Credentials can be exposed and are difficult to rotate safely   | Never include secrets; use approved server-side or platform mechanisms           |
| Reading the DOM to compensate for missing data contracts | UI changes can silently break measurement                       | Prefer an authoritative data-layer contract                                      |
| Removing a template before its consumers                 | Dependent tags or variables can fail or become unmanaged        | Migrate consumers first and retain a rollback record                             |

## References

- [Google for Developers — Custom templates quick start guide](https://developers.google.com/tag-platform/tag-manager/templates): custom tag and variable templates, fields, code, permissions, preview/testing, and import/export.
- [Google for Developers — Sandboxed JavaScript](https://developers.google.com/tag-platform/tag-manager/templates/sandboxed-javascript): the restricted execution model and available `require`-based functionality.
- [Google for Developers — Custom template permissions](https://developers.google.com/tag-platform/tag-manager/templates/permissions): permission detection, configuration, specificity, and `queryPermission`.
- [Google for Developers — Custom template APIs](https://developers.google.com/tag-platform/tag-manager/templates/api): sandboxed APIs for tags, variables, data layer, consent, storage, networking, and testing.
- [Google for Developers — Tests](https://developers.google.com/tag-platform/tag-manager/templates/tests): unit testing custom template behavior before deployment.
- [Tag Manager Help — Publishing, versions, and approvals](https://support.google.com/tagmanager/answer/6107163?hl=en): version history, publishing, rollback, and approval workflows where available.
