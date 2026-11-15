# P0-06: long-lead items and supplier offers

> **15 November 2026 — implementation update:** The first project-specific award slice is implemented and locally tested: per-MR source selection, script-copied editable terms, agreement confirmation, PO snapshots and pinned offer/optional quotation links. See the [tested workflow](../docs/workflows/engineering-supplier-selection.md) and [test evidence](engineering-project-supplier-selection-test.md). The assessment below is the earlier baseline. Broader RFQ, payment and RVD work remains separate.

Source review: 14 November 2026. Planning only; no implementation or live workspace tests. Supplier offers in this item are distinct from the customer quotation in P0-02.

## Existing capabilities and administrator configuration

| Requirement | Existing mechanism |
|---|---|
| Multiple suppliers per article | Item-owned Supply Source occurrences linked to Supplier definitions |
| Supplier-specific terms | Supplier Item Number, Lead Time (working days), Unit Price, Currency, Price Updated On and Ordering Text |
| Default supplier | First supplier source by saved row order; users with configured edit permissions can reorder |
| Source fields, extra metadata and views | Category properties, property definitions, Display Tabs and columns |
| Quotation PDF attachment | Documents/file mechanism on an appropriate configured file-bearing document category; do not attach a direct file to Supply Source, whose seeded file policy is None |
| Long-lead flag | An ordinary configurable Boolean/option property assigned to project BOM occurrences; no dedicated engine is needed for manual classification |
| Material need dates | Existing demand/need-date fields and procurement process |
| Review/award record | Generic categories, reference properties, lifecycles, permissions and tasks can model supplier offers/selection |

Exact additional category/tab configuration must be validated in the target workspace. Generic configuration capacity is not evidence that a ready-made Supplier Quotation/Award screen already exists.

## Concrete gap: project award does not select the source used by purchasing

PurchaseOrderService currently resolves the current Library item's supply sources and chooses the first source by SortOrder and link identity. It reads that source's current supplier, price, currency and ordering text. Its own comment states the MR pins the engineering item version, not the supplier list.

Example: the motor's first Library source is supplier A. Project X selects supplier B's offer for this order. Attaching B's winning PDF to the motor or recording a project reference does not instruct the current PO path to use B. Reordering the shared Library supplier list can affect other purchases and is not an adequate project-specific award mechanism.

An additional gap is stable purchase provenance: the chosen supplier/offer version and agreed terms must be recorded on the demand/order, rather than re-derived from mutable catalogue defaults. The current code carries selected source identity through preparation, but that alone does not establish a durable awarded-offer reference or frozen terms.

## Proposed required behavior for this workflow

Agreed required fix: project-specific supplier/source selection and agreed offer terms must drive MR-to-PO creation, with durable offer provenance and controlled changes. This is confirmed scope, not optional functionality. Implementation remains on hold.

1. Keep reusable Library sources/defaults. Allow a project-specific selected source and winning supplier quotation for a particular demand occurrence/MR.
2. Keep the winning PDF accessible from the item as requested, but also link the exact offer to project demand and resulting PO line. Document attachment alone must not determine supplier selection.
3. Create the PO using the approved project choice and agreed offer terms. Do not silently substitute today's first Library source. Preserve supplier, source identity, offer reference/version, agreed price/currency, delivery commitment and ordering text as applicable.
4. Make later changes explicit: an expired/withdrawn offer, altered terms or invalid source must prompt review; existing issued PO terms must not be silently refreshed.
5. Revalidate the approved selection/terms between preview and commit, preventing a background supplier-list change from silently changing the purchase.

This requires an extension to the procurement source-selection and provenance path. It does not justify a full tender-management system, automatic lowest-price award or a separate sourcing engine.

## Configuration versus development

| Work | Classification |
|---|---|
| Manual Long Lead flag, views, ordinary offer/reference fields and review states | Existing configuration, with target-workspace validation |
| Gathering offers and uploading/attaching PDFs | Existing file/entity mechanisms; validate required tabs and permissions |
| Project-specific offer/source choice wired into PO creation | Development/integration needed |
| Frozen offer/term provenance and explicit change handling | Development/integration needed where existing references/version policies do not suffice |
| Automatic lead-time threshold classification | Optional, not required by the stated manual long-lead example |
| Full RFQ portal, bid comparison scoring and automatic award | Not required by this item |

## Administrator settings

Use existing property/category/lifecycle/tab/automation UI first. Add missing selection/mapping controls in application UI.

- Long-lead field/choices and edit permissions; any later automatic threshold/date basis must be editable, not hardcoded.
- Offer document/record category, reference fields, required metadata, lifecycle and selection/approval permissions.
- Scope of source choice (project occurrence/MR) and identity-based mappings to supplier/source/offer fields.
- Selection precedence: explicit project award first; catalogue default only when policy permits and no award exists. An invalid explicit award must not silently fall back.
- Which agreed terms populate the PO, handling of absent values, offer-validity checks and authorised override requirements.
- Document/price visibility so client transmittals/portal shares do not expose confidential supplier quotations.

Engineer/buyer records the actual selected offer and long-lead designation using configured rights. Administrator configuration sets defaults and policy, not a fixed supplier for every project.

## Acceptance

Two projects use the same motor article but award different suppliers/offers. Each PO uses its own award without reordering the Library list. Catalogue price/source changes do not rewrite accepted selections or issued PO terms. Winning documents are traceable from the item/project/PO with access controls. Invalid/expired/missing selections follow the configured review policy. Retry does not duplicate orders. Pump/motor long-lead classification can be edited in the application, with no article-number special cases.

## Sources

`MatStream.Web.Manage/Features/Setup/Templates/Parts/SupplySourcePart.cs`; `MatStream.API/MatStream.Application/Services/Tenant/Procurement/PurchaseOrderService.cs` (source selection in preparation and line creation); `MatStream.API/MatStream.Application/Services/Tenant/StockLineService.cs`; `matstream-docs/docs/features/procurement.md`, `entities/files.md`, `admin/display-tabs.md`.
