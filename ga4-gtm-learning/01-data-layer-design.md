# Jira Work Request: Research Data Layer Design

## Objective

Research and document how a Data Layer connects an application to GTM and GA4. Define a simple, reusable design that is independent of the UI and suitable for projects of any size.

## Scope

- Define the roles of the Data Layer, GTM, and GA4.
- Explain five principles: business-focused events, reliable timing, a stable contract, self-contained events, and safe/useful data.
- Describe the fields a contract should define: names, types, allowed values, sources, timing, and schema version.
- Include guidance for UI-independent values, asynchronous outcomes, data minimization, PII, secrets, and unrestricted input.
- Use generic examples such as `sign_up` and `calculation_completed`; do not define an enterprise-wide schema or implement tracking.

## Deliverables / Outputs

- A short Data Layer design document with a simple application → Data Layer → GTM → GA4 flow.
- A clear explanation of the five principles.
- Contract examples showing field definitions, valid/invalid events, correct types, complete context, and privacy-safe data.
- The detailed response and reference: [01-data-layer-design-answer.md](./01-data-layer-design-answer.md).
