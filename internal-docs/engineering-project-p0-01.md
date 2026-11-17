# P0-01: catalogue, BOM and RVD readiness

Source assessment: 14 November 2026. No implementation or live workspace changes. Existing tests were inspected, not run. This narrows P0-01; Quality review, submission-state mappings, notifications and payment logic belong to later P0 items.

## Implementation update — 15 November 2026

Project additions and reasoned exclusions now freeze on MR creation and flow to independent PO-line checklists. Local motor/pump tests passed. The original assessment below is historical; controlled later amendment UI remains outside this slice. See [test evidence](engineering-project-rvd-test.md).

## Conclusion

The core catalogue and RVD propagation machinery exists. P0-01 needs assessment of project-specific checklist changes and an end-to-end readiness check. One enabled RVD occurrence category is the supported configuration; multi-category routing is excluded from P0. Do not rebuild the catalogue, MR or PO checklist mechanism.

## What exists

| Area | Current behavior |
|---|---|
| Articles | Shared engineering catalogue includes ITM-00000 system, ITM-00006 header, ITM-00012 pump/filter set, ITM-00021 motor and other components |
| BOM | Both BOMs seed positions, quantities and units; BOM seeding uses onlyIfEmpty to avoid replacing an existing structure |
| Drawings | Catalogue attaches ITM-00000.pdf and ITM-00006.pdf through Drawing entities |
| Requirements | Reusable RVD Library definitions and item-version assignments; pump has six sample requirements, motor four |
| Project insertion | Insert From Library links the occurrence to its article and materialises occurrence-owned RVD placeholders |
| MR freeze | StockLineService resolves/pins the article version, pins the source occurrence and synchronizes/pins its RVDs |
| PO creation | PurchaseOrderService passes the ordered article version; StockLineService creates a separate pinned PO-line-owned RVD checklist before sharing |
| Reconciliation | Matches by requirement definition identity to avoid ordinary duplicate creation, records source link, marks removed requirements orphaned and removes legacy inherited RVD links |
| File ownership | Library requirements cannot receive supplier files; supplier files belong on occurrence-owned requirements |

Important distinction: the inspected MR path synchronizes the source project occurrence; it does not establish a separate MR-owned vendor-file checklist. The PO line owns supplier submissions. The project and PO checklists are separate entities and must not be presented as one shared document record.

## What the administrator can configure already

| Choice | Existing application surface | Qualification |
|---|---|---|
| Catalogue descriptions and article properties | Library item editor | Subject to category/state permissions |
| Assembly contents, quantities, units and positions | Item Uses/BOM tab | Use deliberate revision/version changes; seed defaults are not the business rule |
| RVD definitions, names and descriptions | Library records in RVD definition category | File-less reusable requirements |
| Item RVD assignments | Item RVD Uses tab; Add from Library / Remove | Requires enabled ribbon actions and edit rights |
| Supply sources, supplier references, lead times and source order | Item supply-source/Suppliers configuration | Verify actual assigned tabs and permissions in the workspace |
| Units and stock-unit properties | Units of Measure and item properties | Validate pc versus length units before creating demand |
| Tab visibility, row filters, allowed definition/occurrence categories, Add/Remove actions | Configuration → Display Tabs and category assignments | Shared tab definitions can affect several categories |
| Category lifecycle, version/file policy and permissions | Configuration → Categories / Lifecycle Definitions / groups | Keep exactly one enabled RVD occurrence category |
| Sample installation | Manage Setup catalogue selections | Useful initial data, not an ongoing authority over administrator choices |

These are existing mechanisms, not a claim that the exact target workspace is already configured correctly. The RVD definition and occurrence categories are different: hiding the occurrence category from New does not mean engineers cannot add Library requirements through a configured Uses tab.

## Confirmed issues and development scope

### Project-specific requirements do not have a proven propagation contract

The PO checklist is derived from the pinned Library item version. The inspected path does not copy project-added/removed requirement decisions into the PO checklist. Reconciliation also treats definitions absent from the Library template set as stale, marking their placements orphaned. Consequently, allowing Add/Remove on a project RVD tab is not proof that project overrides are supported throughout procurement.

**Required P0-01 fix:** project-specific requirements are real business requirements and must be supported. This is agreed development scope, with implementation still on hold.

**Required behavior:** effective requirements equal Library defaults plus project additions minus authorised project exclusions. Distinguish each source/decision, resolve the effective checklist at a defined MR freeze boundary and carry that exact result through procurement to each PO line. Preserve provenance, uploaded files and other projects. Later changes to frozen MRs/orders require explicit review; never silently rewrite issued requirements. Do not mutate the shared Library article to satisfy one project's exception.

**Administrator settings:** whether overrides are allowed, who can add/exclude, required reason/approval, and how an approved change affects existing MRs/POs. Proposed controls belong on RVD policy plus occurrence-level review actions.

**Acceptance:** one project's added/excluded requirement reaches its intended PO only, survives synchronization, retains provenance and leaves other projects unchanged. Exclusion is not the same as accepting or waiving a submitted document; the Quality waiver process remains P0-08.

## Validation gaps, not confirmed missing features

1. Confirm selected Setup packages produce the actual catalogue, both drawings and expected BOMs in a clean practice workspace.
2. Verify quantity/unit propagation, especially 496 mm pipe, 1135 mm hose and 1673 mm tubing versus piece quantities.
3. Add the system to a fresh project and confirm nested project demand is materialised correctly without a second independent header demand.
4. Create pump/motor MRs and POs through the intended path; inspect pinned article versions, source links and separate PO-owned checklists.
5. Check later Library changes do not silently alter frozen requirements; review what happens on article re-link, copying structure and repeated synchronization.
6. Verify permission/tab settings permit administrator maintenance while supplier files remain prohibited on Library templates.
7. Verify direct PO creation without an explicit article version, if offered in the UI: the inspected StockLineService synchronizes the PO checklist inside the supplied-version branch. The MR-derived path does supply it; other entry points need caller tracing/testing before declaring a defect.

The existing PurchaseOrderVendorDocumentTests checks that the PO-line path requests a pinned checklist and propagates a failure. It uses mocked repositories and is not proof of full SQL or portal behavior.

## Minimal scope proposed for P0-01

1. Treat Setup as one-time production initialisation. Test reruns are outside the production P0 gaps; no tracking or restore workflow is proposed.
2. Use one enabled RVD occurrence category and document this limitation; no category-routing development is planned.
3. Implement the required effective-checklist/freeze contract and project override propagation, including administrator UI for permissions, reasons/approvals and treatment of existing procurement. Implementation remains on hold pending completion of planning.
4. Run the readiness scenarios above and repair only demonstrated gaps. A diagnostic screen is optional; do not create one solely because testing is needed.
5. Document administrator maintenance and separate project/PO ownership. No new notification, approval, payment or completion engine belongs in this item.

All introduced settings must have workspace-administrator UI, validation and useful editable defaults. No hidden special-case article IDs, labels, option descriptions or workflow rules.

## Evidence

Repository-root-relative paths:

- `MatStream.Web.Manage/Features/Setup/Templates/Parts/EngineeringSampleArticles.cs`
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/EngineeringSampleCatalogue.cs`
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/RequiredVendorDocumentsPart.cs`
- `MatStream.Web.Manage/Features/Setup/Templates/TemplateSeedContext.cs` (`SeedDefinitionUsesAsync`)
- `MatStream.API/MatStream.Application/Services/Tenant/EntityService.InsertFromLibrary.cs`
- `MatStream.API/MatStream.Application/Services/Tenant/StockLineService.cs`
- `MatStream.API/MatStream.Application/Services/Tenant/Procurement/PurchaseOrderService.cs`
- `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlEntityRepository.RequiredVendorDocuments.cs`
- `MatStream.API/MatStream.Infrastructure.Tests/Stock/PurchaseOrderVendorDocumentTests.cs`
- `MatStream.Web.Assembly/Features/DisplayTabs/UsesDisplayTab.razor`
- `matstream-docs/docs/admin/display-tabs.md`
