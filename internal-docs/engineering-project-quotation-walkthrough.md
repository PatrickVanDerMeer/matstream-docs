# First workflow test: quotation to project

Status updated 14 November 2026: quotation-to-project copying is implemented locally and the four seeded mappings passed the actual acceptance workflow in a fresh workspace. No online deployment. Use the workflow as the practical test. Existing automated checks are supporting evidence.

## Local workflow evidence

Replaced the disposable local Test Sales Order workspace at the user's request. Created Engineering Workflow Test (`cwt3qp`, workspace 108) on the same account. Setup completed with CRM Configuration, CRM Sample Opportunity, Engineering Project Configuration, Engineering Project Sample Data and Document Control Configuration.

The freshly seeded Create Project from Quotation script contained Client and Customer Contact required mappings, plus optional Description and Currency mappings. Lube Oil Skid Quotation (entity 15670) was issued. Acceptance correctly blocked on non-final quotation lines. All three lines were Released through Change State; acceptance then succeeded and created Lube Oil Skid Project (entity 15858) under Projects / 2026, In Preparation. Its properties showed Northwind Manufacturing, Eva de Vries, the sample quotation description and EUR.

UI testing found the field mapping controls too narrow and an entered default not committed before saving. The editor was changed to show mappings across the full width and persist default input while typing. After a clean UI build and local app restart, saving and reopening confirmed the default persisted. Created a second quotation through New (allocated Q-2026.0001, entity 15859), selected Northwind Manufacturing and Eva de Vries through Search Library, saved, issued and accepted it. Its generated Project (entity 15860) displayed Title “Lube oil system”, Client Northwind Manufacturing, Customer Contact Eva de Vries and Currency EUR. Empty optional Description remained blank. This proves the transition inherited the edited automation parameters. The Title default is a test-workspace choice; it was not added as a universal seed default. The second quotation had no lines and was used only for the default-mapping test.

The [public walkthrough](../docs/workflows/engineering-quotation-project.md) contains the actual screenshots. Quotation PDF generation/dispatch, non-admin role permissions and subsequent engineering stages have not been exercised by this test.

## Example we will follow

A named example client requests one lube-oil system ITM-00000, including header ITM-00006. Sales creates an opportunity/quotation, reviews price/scope, issues it, records the client's acceptance and gets a project automatically with the same client/contact. The engineer then adds ITM-00000 to the project in the next workflow step.

Let the configured numbering schemes allocate actual quotation/project numbers. Do not invent IDs or assume existing sample records are fresh.

## Configuration to check at the point of use

1. Sales/CRM and Engineering Project configuration installed; intended Sales user can create/read/update the relevant records and use the permitted transitions.
2. Client and contact exist and the quotation can select them. Configure the Project category to carry Client/contact and other agreed target properties as needed.
3. Quotation fields include description/scope, quantity, price/currency, date, validity and commercial terms. Decide exact customer scope/exclusions without inventing a production budget from the selling price.
4. Check the current quotation line lifecycle: the seeded Issued → Accepted transition requires lifecycle-controlled child lines to be Final. Confirm what that means in this workspace and show the user any blocking line; do not silently remove the criterion.
5. Create Project from Quotation script enabled/attached to the chosen acceptance transition. Set target Project category, destination folder and desired project naming in editable parameters/script. The seeded folder is fixed; automatic year rollover is not required.
6. Configure field mappings: Client → Client, Customer Contact → corresponding project contact and Description → scope/description where appropriate. Map currency and commercial terms only to meaningful target fields. Owner/delivery date require actual source data or an explicit administrator-selected default; they are not assumed to be present on the quotation.
7. Check customer quotation output. Existing help says its print template is missing; inspect actual installed templates before declaring it absent. If absent, create/configure the template and issue/PDF attachment action as required to send the quotation. A basic acceptance/project rehearsal can be inspected separately, but document dispatch remains unproven until output exists.

## Implemented field-copying fix

The createEntity action now accepts mapped target property values; the administrator edits mappings/defaults/required flags through the automation UI. Source values are read from the exact triggering quotation version using stable property IDs, with typed target validation and transactional project/property/source-link creation. The seed uses the shared editable example script and leaves transition parameters unset so that administrator edits to automation mappings take effect.

All mappings, defaults, empty-value/required-field behavior and target settings must be configurable by the workspace administrator in application UI. Provide an editable example script. Do not hardcode client IDs, property labels or commercial policy.

## Walkthrough evidence

| Action | Expected result | What we record |
|---|---|---|
| Create quotation and system line | Correct client/contact, scope, quantity, currency and price | Actual fields, record numbers and any missing configuration |
| Issue/send quotation | Correct customer-facing document/version | Template/output availability and access |
| Record acceptance | Permitted transition with clear line blockers/evidence | Actual state and automation result; state change alone does not prove project creation succeeded |
| Open generated project | Correct destination/name; automatically copied fields | Compare each mapped value to accepted quotation |
| Open source reference | Exact accepted quotation version | Traceability and later-version behavior |
| Inspect retry/failure behavior safely in practice data | No duplicate project from replay of same event; failure visible | Existing result/run history; deliberate reacceptance is a different event |

Classify each observation as Works, Configure, or Fix. No application changes are authorised by this document.
