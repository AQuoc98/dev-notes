# 06 — GTM Template Governance

## 1. Objective, scope, and outputs

### Objective

Provide a lightweight control for deciding when a GTM custom template is needed, reviewing its code and permissions, testing its consumers, and retiring it safely.

### Scope

- Custom tag templates and custom variable templates in a web GTM container.
- Built-in, Community Template Gallery, and organization-owned templates.
- Template fields, validation, sandboxed JavaScript, APIs, permissions, endpoints, consent, tests, versions, and ownership.
- Dependency impact on configured Tags and Variables.
- FD calculation_action as the reference decision pattern.

Detailed Data Layer, Variable, Trigger, Tag, and Consent behavior belongs to Sections 01–05. This section governs the reusable template layer, not every configuration created from a template.

### Outputs

Every production-active template should have:

1. An approved requirement and a documented decision to use a template.
2. A template contract covering fields, validation, data, endpoints, consent, permissions, and completion behavior.
3. An owner, source/version record, consumer inventory, tests, and rollback/export record.
4. A review result for each update and its dependent Tag/Variable instances.

## 2. Overview: what a GTM template is

### 2.1 Template versus configured instance

A template defines how a reusable tag type or variable type works. A Tag or Variable is a configured instance created from that template.

| Object | Practical meaning |
| --- | --- |
| **Template** | Defines the configuration fields, validation, sandboxed code, APIs, and permissions. |
| **Tag instance** | Performs an action when its Trigger and consent settings allow it. |
| **Variable instance** | Returns a value for a Trigger or Tag. |

Changing one Tag instance normally affects only that Tag. Changing the underlying template can affect every dependent instance, so it is a code and dependency change.

Custom templates are useful when an approved requirement cannot be met by a supported built-in Tag, Variable, or a simple configuration. Google describes them as a safer, permission-based alternative to unrestricted Custom HTML or Custom JavaScript.

### 2.2 Template types

| Type | What it must do |
| --- | --- |
| **Tag template** | Execute an approved action, send only to approved endpoints, and call the documented success/failure path. |
| **Variable template** | Read approved input, validate or transform it, and return a value; it does not send a request. |

### 2.3 Source selection order

Use the least complex supported option:

1. Google-provided built-in option.
2. Reviewed Community Template Gallery template.
3. Organization-owned custom template with source and security review.
4. Custom HTML or Custom JavaScript only as a documented exception.

Do not import or build a template merely because it is available. Start with an approved business or measurement requirement.

## 3. Template contract and review

### 3.1 Required contract

Record these items before approval:

| Item | Decision to record |
| --- | --- |
| Purpose and type | Why it exists and whether it is a Tag or Variable template. |
| Fields and defaults | Inputs shown in GTM, required/optional status, help text, and safe defaults. |
| Validation | Accepted types, allowed values, normalization, and invalid-input behavior. |
| Data handling | Values read, transformed, stored, or sent; prohibited data is excluded. |
| Endpoints | Exact HTTPS destinations and environment-specific URLs. |
| APIs and permissions | Each sandbox API and the reason it is needed. |
| Consent | Required consent, denied/unknown behavior, and update behavior. |
| Completion behavior | Tag success/failure, timeout, retry, and duplicate handling; Variable return on missing/invalid input. |
| Consumers | Dependent Tags/Variables and criticality. |
| Ownership and lifecycle | Owner, reviewer, version, status, review date, and retirement condition. |

### 3.2 Sandbox and permissions

Custom template code runs in GTM’s sandboxed JavaScript environment, not as unrestricted page JavaScript. It uses approved sandbox APIs through the template’s declared permissions.

Sandboxing reduces access; it does not make a template automatically safe. Apply least privilege:

- Request only the APIs and permissions required by the documented purpose.
- Restrict network permissions to exact approved HTTPS URL match patterns.
- Do not request page, cookie, storage, or Data Layer access when the template does not need it.
- Do not place credentials, secrets, or unrestricted user input in fields or code.
- Review a Community Template as third-party code, including its source, publisher, maintenance, license, endpoints, and update history.

### 3.3 Review gate

Approve a template only when all answers are “yes”:

1. Is the requirement approved and still unsatisfied by a built-in option?
2. Is the source trustworthy, maintained, and versioned?
3. Are fields, validation, code behavior, endpoints, consent, and permissions understood?
4. Are consumers, owner, tests, and rollback/export records known?
5. Can the template be tested in a non-production workspace?

If any answer is “no”, keep the template out of production.

## 4. Implementation workflow

### 4.1 Build or import

1. Create an inventory record before importing or coding.
2. Use the Template Editor’s Info, Fields, Code, and Permissions areas.
3. Give each field a clear label, help text, type, required/optional rule, and safe default.
4. Validate inputs at the template boundary.
5. Keep the implementation limited to the approved action or value.
6. Declare exact endpoint and permission requirements.
7. Export or commit the approved source/version so it can be restored.

For a tag template, use the GTM success/failure callbacks to report completion. For a variable template, return a deterministic value or an explicitly documented undefined result.

### 4.2 Test before publish

Use the template’s unit tests where available, then test representative consumers:

1. Normal valid input.
2. Missing and invalid input.
3. Denied or unknown consent.
4. Duplicate invocation, timeout, and network failure.
5. Correct endpoint, environment, payload, and request count.
6. GTM Preview behavior of each critical dependent Tag or Variable.

Google template unit tests can run code with sample inputs and assertions. They do not replace validation checks or real permission/network testing.

### 4.3 Inventory and ownership

Maintain one record for every non-built-in template:

~~~text
name | type | purpose | source/repository | approved version
permissions | endpoints | consent | consumers
owner | reviewer | last review | status | replacement/retirement condition
~~~

The owner is accountable for maintenance, security/privacy review, consumer impact analysis, incident response, updates, and retirement. A template without an owner should not be production-active.

### 4.4 Update and dependency impact

Treat every template update as a code change:

1. Identify all dependent Tags and Variables.
2. Review the source diff, exact version, fields, defaults, validation, permissions, endpoints, and consent behavior.
3. Retest critical and high-volume consumers.
4. Record the approved version, change owner, evidence, and rollback/export path.
5. Publish only after the same review standard as a new template.

Never accept an automatic Gallery update without checking its impact.

### 4.5 Retirement

Before deleting a template:

1. Find every dependent Tag and Variable.
2. Migrate or remove those consumers.
3. Confirm no approved requirement still depends on the template.
4. Retain the final version, decision, evidence, and rollback record.
5. Mark the template Deprecated or Retired according to the repository policy.

## 5. Operational notes and anti-patterns

| Anti-pattern | Risk | Preferred action |
| --- | --- | --- |
| Importing without a requirement or review | Unknown code and permissions become production-active. | Apply the review gate first. |
| Using Custom HTML when a supported option exists | Unnecessary maintenance and access risk. | Prefer built-in or reviewed custom templates. |
| Broad permissions or wildcard endpoints | The template can read or send more than intended. | Apply least privilege and exact HTTPS allowlists. |
| No consumer inventory | Updates or deletion break dependent Tags/Variables. | Inventory consumers before approval, update, or retirement. |
| No owner/version/rollback | Incidents and updates cannot be controlled. | Record an accountable owner and recoverable version. |
| Template reads DOM to infer a business result | UI changes silently change measurement. | Keep business truth in the Application/Data Layer (Section 01). |
| Template sends PII, secrets, or raw inputs | Privacy and security exposure. | Enforce the approved data contract and payload allowlist. |

Template governance does not replace the controls in Sections 01–05: the Application still owns business truth, Variables map values, Triggers select the business moment, Tags send approved data, and Consent controls permission.

## 6. Cross-reference with the other sections

- **Section 01 — Data Layer Design:** approved event contract and privacy-safe payload.
- **Section 02 — Variable Management:** prefer native Variables; document consumers of a Variable Template.
- **Section 03 — Trigger Management:** use the authoritative application event; a template must not infer success from a broad UI rule.
- **Section 04 — Tag Management:** Tag type, parameter allowlist, destination, request count, and validation.
- **Section 05 — Consent:** Consent Initialization and consent behavior for a template or dependent Tag.
- **Section 07 — Measurement Plan:** the approved requirement that justifies the template.
- **Section 08 — Debug and QA:** evidence and pass/fail record.
- **Section 10 — Release Monitoring:** production monitoring after a template or dependency update.

## 7. Worked Journey: FD calculation_action

### Requirement

Send the approved FD calculation_action event to GA4 with the Google tag and GA4 Event tag.

### Governance decision

No custom Tag Template is needed. The built-in Google tag and GA4 Event tag already provide the required destination, event configuration, consent behavior, and Preview/GA4 validation. Use the native Data Layer Variables from Section 02 and the authoritative Custom Event Trigger from Section 03.

~~~text
Approved FD event contract
        ↓
Built-in Google tag + GA4 Event tag satisfy the requirement
        ↓
No custom template is imported or built
        ↓
Validate one event, one request, approved parameters, consent, and destination
~~~

If a future requirement cannot use built-in options, use the custom-template deployment flow in subsection 8 below.

## 8. Worked Journey: when a custom template is genuinely required

### Scenario and gap

The team must send an approved calculation_action summary to an approved non-GA4 endpoint as part of an internal measurement workflow. The built-in Google tag and GA4 Event tag can send to GA4, but they cannot target this endpoint or use its reviewed request envelope. A custom Tag Template is therefore justified for this separate destination.

The template must not calculate the FD result. The Application remains responsible for solution_found and the Data Layer contract; the template only maps and sends approved scalar fields.

### Deployment record

> This is a team governance template, not a form provided by Google. Its fields are derived from the template contract and review gate above, the Tag/Consent contracts in Sections 04–05, and the QA evidence sequence in Section 08.

~~~text
Requirement:       one approved calculation_action summary → approved endpoint
Built-in gap:      GA4 tags cannot target the required endpoint/envelope
Source:            reviewed Community Template or organization-owned source
Input allowlist:   calculation_action, calculation_type, solution_found
Consent:           required analytics/measurement consent; denied or unknown blocks
Endpoint:          exact HTTPS staging and production URLs
Permissions:       only read approved values and send to the allowlisted endpoint
Owner/version:     named owner, source revision, review date, rollback export
~~~

### Deployment steps

1. Record the requirement, destination, payload allowlist, expected count, consent rule, owner, and environment.
2. Confirm that a built-in Tag, Variable, or simple configuration cannot meet the requirement.
3. Prefer a reviewed Community Template; otherwise create an organization-owned template with source history.
4. In the Template Editor, define clear fields, validation, safe defaults, sandbox code, exact endpoint permissions, and success/failure behavior.
5. Configure the dependent Tag with the authoritative application Trigger and the approved Additional Consent Check. Do not use a broad click or DOM Trigger.
6. Run template unit tests for valid, missing, invalid, and duplicate input. Add timeout, denied-consent, and network-failure coverage where the template supports those paths.
7. Test the dependent Tag in a non-production container: GTM Preview → request payload/count → endpoint receipt.
8. Review source/version, permissions, endpoints, data handling, consent, consumer inventory, and evidence with the owner.
9. Publish a version with a rollback/export record, then monitor the first production release.

### Acceptance evidence

- The requirement and built-in gap are documented.
- The template sends only the approved scalar fields to the correct environment endpoint.
- Denied or unknown consent prevents the request according to the contract.
- One valid calculation occurrence produces one template execution and one request.
- Invalid, no-output, duplicate, timeout, and network-failure behavior is documented and tested.
- GTM Preview, Network, endpoint receipt, owner approval, version, and rollback evidence are stored.

## References

- [Google for Developers — Custom templates quick start guide](https://developers.google.com/tag-platform/tag-manager/templates)
- [Google for Developers — Sandboxed JavaScript](https://developers.google.com/tag-platform/tag-manager/templates/sandboxed-javascript)
- [Google for Developers — Custom template permissions](https://developers.google.com/tag-platform/tag-manager/templates/permissions)
- [Google for Developers — Custom template tests](https://developers.google.com/tag-platform/tag-manager/templates/tests)
