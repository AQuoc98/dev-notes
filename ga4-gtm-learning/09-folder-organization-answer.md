# 09 — GTM Folder Organization

## Theory

GTM folders group tags, triggers, and user-defined variables for navigation and maintenance. Moving an item into a folder does not change how it fires, create a namespace, restrict access, or control publishing. Built-in variables are not managed like user-defined container items.

## Recommended Strategy

Choose one primary dimension rather than mixing taxonomies unpredictably. For this learning project, organize by business capability or journey because it keeps a tag with its triggers and variables:

- `Journey - Registration`
- `Journey - Checkout`
- `Shared - GA4 Foundation`
- `Shared - Consent`
- `Temporary - [ticket] - [owner] - [cleanup date]`

Alternatives such as platform/vendor, project, or team can work, but document the choice. Do not create folders that merely repeat item types (`Tags`, `Triggers`, `Variables`), because GTM already exposes those views.

## Placement Rules

- Put a component in the folder of its primary owner/use case.
- Put genuinely reused components in a clearly named `Shared` folder and list their consumers in descriptions/inventory.
- Leave an item unfiled only under an explicit rule; otherwise treat it as triage debt.
- Give temporary migration or campaign folders an owner and removal/review date.
- Keep names descriptive because search and inventories remain important even with folders.
- Reorganize in a dedicated workspace, review the change set, run Preview, and publish through the normal approval process.

## Inventory Template

| Folder | Purpose | Included components | Owner | Review/cleanup date | Notes |
| --- | --- | --- | --- | --- | --- |
| `Journey - Registration` | Registration measurement | `GA4 Event - sign_up`, its trigger and DLVs | Analytics | Quarterly | POC scope |

## Completion Checklist

- [ ] Folder taxonomy and exceptions are documented.
- [ ] Shared items have known consumers and owners.
- [ ] Temporary folders have cleanup dates.
- [ ] Empty, duplicate, and obsolete folders are reviewed.
- [ ] Moving items caused no unexpected configuration changes.
- [ ] Preview and change-set review pass before publication.

## Official References

- [Folders](https://support.google.com/tagmanager/answer/6231791)
- [Organize your containers](https://support.google.com/tagmanager/answer/6261285)
