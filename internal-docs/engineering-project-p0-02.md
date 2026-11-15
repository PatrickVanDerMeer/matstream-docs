# P0-02: accepted quotation to project

Source review and local workflow test: 14 November 2026. Historical gap analysis below is retained for context.

Implementation update: automatic mapped field/default copying is implemented locally through the editable example script, extended createEntity properties API and administrator mapping editor. Creation/property/link rollback and accepted-version reads have SQL test coverage. A fresh local workspace installed the new seeded automation and passed the quotation acceptance workflow with Client, Customer Contact, Description and Currency copied into its generated project. See [the workflow test record](engineering-project-quotation-walkthrough.md). Existing workspace scripts require deliberate updating. Online deployment is deferred by the user; it is not a prerequisite for this local workflow test. Earlier source-review descriptions below record the original gap.

## Existing behavior

When CRM and Engineering Project configuration are installed, SalesPart.EnsureProjectIntegrationAsync seeds a stored Create Project from Quotation script attached to the Issued → Accepted transition. The script reads TargetCategoryId and TargetFolderId parameters, derives a name from the quotation name, and calls createEntity.

The runtime validates category/destination/source, creates and places the output, and writes a pinned Generated From link to the exact quotation version within a transaction. A Projects Where-Used tab on quotations lists linked outputs. Lifecycle automation has same-event retry protection; a deliberate new acceptance event is not the same retry and may create another output.

## Already configurable

| Choice | Existing mechanism |
|---|---|
| Whether/when acceptance creates an output | Lifecycle transition automation attachment/enabled state |
| Output category and landing folder | TargetCategoryId / TargetFolderId automation parameters |
| Output name | Editable stored script in Configuration → Automations |
| Category fields, defaults, tabs, lifecycle and permissions | Existing category/property/tab/lifecycle configuration |
| Project details after creation | Ordinary project property editing, subject to permissions |
| Assembly placement | Add ITM-00000 from Library as the separate planned step 3 |

The seeded destination is a fixed year folder chosen at configuration time. If a different destination is wanted, edit the parameters; automatic annual rollover is not established and is not a required feature for this story. Likewise, the name transformation is editable script behavior, not a dedicated naming-template UI. New business settings must be administrator-editable; current script parameters are the available surface.

## Actual limitation: project data is not copied

The generic CreateEntity action carries category, destination and name only. CreateEntityAsync creates/places/links the entity but has no quotation-to-project property mapping or child/template creation step.

Example: acceptance can create a project linked to quotation Q-001, but the link does not itself populate the project's Client, contact, owner or dates from Q-001. Category defaults may initialise some values; users can enter the remaining project details manually.

Automatic population is agreed required scope below. Manual completion remains existing behavior, but does not satisfy this handover requirement.

## Required fix: automatic handover through a script

Agreed requirement: the quotation acceptance automation script must create the project and automatically populate Client, contact and other relevant quotation fields. Manual entry is not the intended handover. Implementation remains on hold.

Client and contact are required mappings. Other candidate mappings include project description/scope, quotation reference, currency, responsible owner and agreed delivery date where the source has a corresponding value. Review source/target fields before assigning mappings: quotation validity is not a project completion date, and quotation value is not automatically a project budget. Administrators choose mappings and defaults through the application UI; no business fields are hardcoded into a special-case conversion.

Extend the existing generic output creation mechanism with validated target property values/mappings and an administrator UI for configuration. Avoid a hardcoded quotation/project conversion.

Settings: enabled mappings, source/target property identities, copy versus default value, empty-source behavior, compatible type/reference checks, required fields, and overwrite policy if a later explicit action updates an existing project. Support only fields actually assigned to the target category. Preserve the pinned accepted source version and transactional creation behavior.

Do not assume the separate createLinkedEntity primitive solves this: it requires an ordinary occurrence target and a source with a linked item and quantity. It is not a drop-in replacement for a quotation-to-Project folder conversion.

## Not required by the agreed workflow

- Automatic quotation-line-to-BOM conversion: step 3 deliberately adds the system assembly to the project.
- Automatic complete project templates, work packages or task generation: not specified as automatic requirements; validate existing configuration/defaults first.
- Dynamic year-folder routing, sophisticated naming policies or a dedicated conversion wizard: existing editable automation suffices until a concrete requirement says otherwise.
- Financial posting or automatic price/cost transfer: outside this step.

## Validation before declaring P0-02 complete

1. Both setup sections install the integration and attach an enabled script to the intended transition.
2. Acceptance creates the expected category in the configured folder and its source link opens the exact quotation version.
3. The created project is visible/openable/editable to the intended people; verify required details and initial lifecycle state.
4. Same-event retry does not duplicate the output; distinguish failed runs from deliberate reacceptance.
5. Invalid target settings give actionable errors, and users can discover the generated project.
6. Validate required automatic Client/contact and selected field mappings, then add ITM-00000 through step 3. Manual entry does not satisfy the agreed automatic handover.

## Agreed direction

Reuse and extend the existing project creation script/action to support automatic quotation-field handover. Keep mappings, defaults, empty-value handling and validation configurable through workspace administrator UI. Preserve the exact accepted source version and transaction/retry protections. No implementation in this planning task.

## Sources

- `MatStream.Web.Manage/Features/Setup/Templates/Parts/SalesPart.cs`, EnsureProjectIntegrationAsync.
- `MatStream.API/MatStream.Application/Services/Tenant/Scripting/ScriptContext.cs`, ScriptAction.CreateEntity.
- `MatStream.API/MatStream.Application/Services/Tenant/Scripting/ScriptAutomationService.cs`, CreateEntityAsync.
- `MatStream.API/MatStream.Application/Services/Tenant/Scripting/ScriptAutomationService.LinkedEntities.cs`, eligibility restrictions.
- `matstream-docs/docs/admin/automations-admin.md`, configuration and attachment surfaces.
