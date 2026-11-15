# Automation usage and visibility: required enhancement

Planning only, 14 November 2026. No implementation.

## Request and current state

Add a Used in column comparable to Property Definitions, and allow workspace administrators to hide situation-specific scripts from the main Automations list without disabling execution.

The inspected Automation model has Enabled but no separate visibility setting. AutomationDefinitions has no Used in column. Existing stored attachment records can provide usage information, so this is feasible without inventing manual usage labels.

## Proposed behavior

- Used in is derived from actual configuration references, not entered by an administrator and not inferred from the script's Context value.
- Summarise exact attachments, for example `Quotation Lifecycle: Issued → Accepted`, category lifecycle switches, category/property script assignments, import assignments and configured ribbon actions where they reference scripts. Inventory all supported attachment mechanisms before defining coverage; flag unsupported reference forms rather than claiming a complete dependency scan.
- Include disabled attachments as such. A shared lifecycle may serve multiple categories; show the exact transition and optionally its applicable categories without presenting those as separate execution attachments.
- Allow sorting/filtering by usage and drilling into the attachment configuration. Batch-load usage; do not query separately per row. Include usage in export.
- Add an administrator-editable **Hide from main Automations list** flag and a **Show hidden** filter. Hidden scripts remain enabled, execute normally, remain accessible from their attachment/editor and appear when Show hidden is selected.
- Do not automatically hide everything used once: one attachment does not necessarily mean a script is disposable or private. The administrator decides. Keep existing picker behavior unless a separate, explicit reuse-visibility policy is agreed.
- Hiding is organisation, not access control. Keep existing read/edit permissions and audit configuration changes. Show a filtered-empty message when scripts are hidden rather than saying no automations exist.

## Feasibility and scope

Feasible; the grid column/filter is straightforward. The substantive work is aggregating all actual attachment sources consistently, adding the separate persisted visibility setting and keeping list, editor, attachment navigation and export behavior aligned. This is a moderate enhancement, not a reason to redesign the automation system. Raw script-text references cannot be promised as reliable Used in results.

## Acceptance

The project-creation script reports its quotation transition. Multi-use scripts report all recognised attachments. Unused and disabled-attached scripts remain distinguishable. Hiding changes display only, Show hidden restores discoverability, attached editing still works, and no execution behavior changes.

## Source pointers

`MatStream.Web.Assembly/Features/Automations/Components/AutomationDefinitions.razor`; `MatStream.API/MatStream.Domain/Models/Tenant/Automation/Automation.cs`; `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlAutomationRepository.cs`; lifecycle transition/category lifecycle switch/category property/import automation attachment models.
