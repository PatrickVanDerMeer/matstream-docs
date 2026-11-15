# Local project supplier-selection test — 15 November 2026

## Scope and environment

Only local services and workspace 108 (`cwt3qp`, Engineering Workflow Test) were used. Project 15858 is Lube Oil Skid Project. No deployment, supplier invitation, external sharing, email, receipt or payment was performed.

Seed installs ten ordinary agreement fields on MR and PO line categories, the editable PropertyChange script **Purchasing: Prepare Agreed Supplier Terms**, field mappings/default currency, and the source-property automation assignment. The installation marker preserves later administrator customizations. The local workspace received this configuration through focused repository-based adoption, without rerunning unrelated setup.

## Browser checks

- Motor MR 15984: selected alternate offer 15987 under Library motor 15768. Script copied supplier 15986, source version 21710, EUR 350, lead 20 working days and supplier item number LOCAL-MOTOR-21. Buyer changed agreed price to 299 and confirmed agreement.
- Pump MR 15985: selected source 15751 under Library pump 15750, distinguished using Choose columns → Supplier Item Number APL-100880. Script copied supplier 15708, version 21370, lead 30 and price 875. Buyer changed agreed price to 825 and confirmed agreement. Blank source currency used configured EUR fallback.
- Ordering the pump before agreement created no PO and displayed a review-required explanation.
- Selected both MRs together: PO-2026.0001 (15988) and PO-2026.0002 (15994) created. Browser showed the motor line at 299.00.
- Automation editor showed editable script and Parameters JSON including PurchaseSelection, TermMappings, ReferencePropertyDefIds and DefaultCurrency. Closed with Cancel.

## Catalogue change and stored evidence

After agreeing the motor terms, repository SaveNewVersionAsync created source version 21719 with catalogue price 399. MR stayed at negotiated 299 with agreed source version 21710. SQL assertions for browser-created POs passed:

| Item | PO line | Vendor | Price | Currency | Quantity/unit | Pinned source version | Latest source version | MR state |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ITM-00021 | 15989 | 15986 | 299 | EUR | 1 pc | 21710 | 21719 | Ordered |
| ITM-00012 | 15995 | 15708 | 825 | EUR | 1 pc | 21370 | 21370 | Ordered |

Association links use ExplicitVersion (3). Agreement fields are stored on PO lines alongside actual commercial fields; vendor is stored on each PO header. Library supplier ordering was not changed.

Local harness commands are --adopt-purchase-selection, --prepare-selection-offers, --change-offer-after-agreement, --selection-audit and --verify-selection-orders in tmp/distribution-channel-local. Fixtures are labelled LOCAL TEST. Winning Supplier Quotation was empty in this browser test; optional quotation-file pinning has not yet been exercised through the browser.

## Checks

- Application/API and Manage builds passed.
- 31 focused API tests passed: copy/default/clear/manual override/no-refresh, source and supplier access guards, and existing property automation tests.
- Setup preservation test passed: installed policy performs its marker read without rewriting administrator configuration.
- Native stored-data assertions passed for both browser-created POs.
- Strict Zensical documentation build passed.

## Follow-up: lifecycle handover resolved

The switch seed and retry behavior were fixed later on 15 November. Both project positions now reach Procurement / Ordered without automation errors. See [handover test](engineering-project-procurement-handover-test.md). The following findings preserve the original test results.

## Original remaining findings

The existing switch_source_lifecycle automation warned for both linked project BOM positions: no switch is configured to the requested lifecycle. MR states changed to Ordered and PO transactions completed. This is an outstanding lifecycle configuration/seed validation issue. Do not describe the full procurement lifecycle as validated.

Project-specific RVD propagation, MR-to-PO required date copying and payment milestones remain separate fixes. Agreed lead time is retained for traceability, without automatic project planning or Promised Delivery updates. The source picker lists workspace Supply Sources; server-side ordering rejects a source belonging to another item. No dedicated RFQ comparison or offer-expiry workflow is claimed.

Screenshots 137–142 are in docs/images/engineering-project; the public guide links the relevant images.
