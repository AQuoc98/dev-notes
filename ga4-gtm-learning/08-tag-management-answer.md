# 08 — GTM Tag Management

## Theory

A GTM tag is a configured integration or code fragment that runs in response to an event when its firing conditions are satisfied. A tag normally combines a tag template, settings and parameter mappings, one or more firing triggers, and optional trigger exceptions. Tags send or act on data; triggers decide when they may run; variables supply values.

For GA4 web measurement, distinguish the site-wide **Google tag** from **GA4 Event tags** that send named events and parameters. Confirm current Google guidance in the GTM interface because tag names and settings can evolve.

## Design Standard

- Create a tag only for a documented measurement or operational requirement.
- Prefer a supported native or approved template over Custom HTML.
- Map values from approved variables; never read raw form fields or send PII, credentials, or secrets.
- Use the narrowest reliable event trigger. Add an exception only when its behavior and precedence are understood and tested.
- Do not use tag sequencing as a substitute for an application/Data Layer contract. Document it when a genuine setup/cleanup dependency exists.
- Set and test consent requirements. A firing indication alone does not prove that a valid request reached the intended destination.
- Avoid duplicate tags for environment differences; route controlled identifiers through reviewed configuration where appropriate.

## Naming Standard

Use `[platform/type] - [event or purpose] - [qualifier]`, for example:

- `Google tag - Web - Primary`
- `GA4 Event - sign_up`
- `Google Ads Conversion - Purchase - Production`

The description should state business purpose, owner, ticket/spec, expected event, parameters, destination, consent behavior, and retirement condition.

## Inventory Template

| Tag | Type/template | Purpose | Firing triggers | Exceptions | Variables/parameters | Consent | Destination | Owner | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `GA4 Event - sign_up` | GA4 Event | Confirmed registration | `CE - sign_up` | None | `method`, `form_id` | `[required state]` | `[stream/ID]` | Analytics | Active |

## Review and Test Workflow

1. Trace the business requirement to the tracking plan and Data Layer event.
2. Select the simplest approved tag template and document why it is appropriate.
3. Map every field to a variable or controlled literal and check types and missing-value behavior.
4. Review firing triggers, exceptions, consent settings, sequencing, and destination.
5. In Preview, run successful, invalid, repeated, navigation/SPA, and relevant consent cases.
6. Confirm the outgoing network request and GA4 DebugView—not merely GTM’s “tag fired” state.
7. Record workspace/version, evidence, approver, release notes, and rollback plan.
8. After publishing, perform a production smoke test and monitor unexpected volume or duplication.

## Lifecycle Rules

- Pause only as a short, documented diagnostic or rollback measure; do not let paused tags become permanent clutter.
- Before deletion, verify all trigger, variable, sequencing, and template dependencies and retain an exported/versioned recovery point.
- Mark replacement, owner, and retirement date; remove obsolete tags in a reviewed workspace.

## Completion Checklist

- [ ] Each scoped tag has one documented purpose and owner.
- [ ] Triggers, exceptions, parameters, consent, sequencing, and destination are reviewed.
- [ ] Positive and negative cases prove correct single-fire behavior.
- [ ] Network and destination evidence agrees with GTM Preview.
- [ ] Custom HTML has explicit justification, security review, and tests.
- [ ] Release and rollback evidence identifies the container version.
- [ ] No prohibited personal data or secrets are collected.

## Official References

- [About tags](https://support.google.com/tagmanager/answer/3281060)
- [About triggers](https://support.google.com/tagmanager/answer/7679316)
- [Firing triggers and trigger exceptions](https://support.google.com/tagmanager/answer/7679318)
- [Preview and debug containers](https://support.google.com/tagmanager/answer/6107056)
