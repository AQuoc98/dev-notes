# Subtask 06: Govern GTM Templates

## Objective

Create a concise GTM Template Governance & Management Reference covering how built-in, Community Template Gallery, and custom tag/variable templates are selected, reviewed, tested, approved, updated, rolled back, deprecated, and retired.

## Required Scope

- **Fundamentals:** template definition; template versus configured tag/variable instance; Tag Template versus Variable Template; why templates do not eliminate security, privacy, consent, or operational risk.
- **Sources and selection:** built-in → reviewed Community Template Gallery → reviewed custom template → Custom HTML/Custom JavaScript only as a documented exception. Review publisher, provenance, repository, license, maintenance, source history, and dependencies.
- **Template anatomy:** purpose, type, fields, validation, sandboxed code, APIs, permissions, endpoints, data handling, consent, success/failure behavior, consumers, owner, version, and lifecycle. Mark non-applicable items as `None` or `Not applicable`.
- **Security:** sandbox limitations; API and permission review; least privilege with justification for each permission; approved endpoints and environment separation; data/PII/secrets controls; third-party code risk. State that sandboxing does not make a template automatically safe.
- **Template Decision Guide:** include a clearly labeled team-governance production gate covering: approved requirement; built-in option; reviewed Community Template; trustworthy and maintained source; necessary permissions; approved endpoints/data handling; consent; known consumers; testability; owner/version/update/rollback plan.
- **Operating standards:** safe fields and defaults, validation, deterministic missing/invalid/duplicate/denied-consent/timeout/failure behavior, non-production workspace, Preview, network checks, versioning, approvals, publishing, monitoring, update-impact analysis, rollback, migration, deprecation, and retirement.
- **Inventory and ownership:** maintain records for every non-built-in template covering name/type, purpose, source/repository, approved version/commit, permissions, endpoints, data/consent behavior, consumers, owner, review date, lifecycle, replacement, and rollback.
- **Evidence and examples:** include one Community Template evaluation with a concrete security/permission review record, plus common anti-patterns and preferred alternatives.

## Deliverables / Outputs

- One complete governance reference covering the required scope.
- One source-selection hierarchy and 10-point Template Decision Guide labeled as team governance.
- One reusable non-built-in template inventory table.
- One practical review record with evidence and decision.
- One test, update-impact, approval, publish, rollback, deprecation, and retirement workflow.
- One concise anti-patterns table and one `## References` section.

## Instructions / Answer

Use [06-template-governance-answer.md](./06-template-governance-answer.md) as the reference answer. Preserve its major topics: Theory, Template Types, Template Sources, Anatomy, Sandboxing and Permissions, Template Decision Guide, Design Standards, Practical Example, Inventory & Ownership, Test Workflow, Template Update Impact, Common Anti-patterns, and References.

Keep the distinction between Google’s documented technical behavior and team-created governance recommendations throughout the answer.
