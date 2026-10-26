# Sales Order walkthrough verification

## Latest result — 26 October 2026

The main walkthrough is verified through shipment. Scanner now renders Add, Change State, Pick and Pack. Packed 2 pc of 100.080.420 successfully, then changed SO-2026.0001 to Shipped; Change State reported 1 of 1 succeeded. Reopened the order: parent v4 is Shipped and the line is Complete. Screenshots were added to the public guide without action URLs, QR tokens, or the signed-in email.

Stock availability after shipment reports Required 0 pc, Available 2, Reserved 0, Shortage 0, Material Requirement Qty 2, Stock unit unset, Status Already shipped. The sample article is not configured as a stock item; this run verifies the order workflow and shortage purchasing/receipt, not stock-item shipment deductions. The guide now distinguishes stock-item handling. The earlier missing-stock-unit setup warning remains relevant for reservations.

Partial shipment, remaining delivery, cancellation, phone camera scanning and other users' permission combinations were not live-tested. Earlier notes below record the blockers found and repaired during the walkthrough.

Tested 25 October 2026 in Sales Order Walkthrough (`4nmkcm`), order SO-2026.0001.

- Sales Orders Configuration rerun completed successfully; sample data was not reapplied.
- Published app reports v3.1.138. Old Action Link footer is absent.
- Lines > Scanner > Pick opens the embedded picking workflow.
- Entering article 100.080.420 and clicking Find now opens the correct article confirmation.
- Entering quantity 2 works.
- Submitting Pick fails with: `Item is in 'Confirmed' and cannot be modified.`
- The line still reports Ordered 2, Picked 0, Packed 0. Pack and shipment remain unverified.

## Cause found in source

`SalesOrderActionWorkflow.ExecuteAsync` writes Picked/Packed Quantity through `IEntityService.SetPropertyValuesAsync`. That method applies the normal entity modification guard. The wizard deliberately makes confirmed Sales Order Lines non-modifiable, so a normal confirmed order cannot record picking.

A fix needs a narrowly scoped fulfilment operation that validates the order/line relationship, active lifecycle states, actor and property permissions, approvals, quantity bounds, transaction/idempotency behavior, and audit/version handling. It must preserve the general edit lock on confirmed orders. This requires a Tenant API change and deployment before the live walkthrough can continue.

## Fix prepared locally

The Tenant API now calls a dedicated fulfilment quantity method. It validates the expected line version, Sales Order Line category, active line state, assigned decimal property/system key, read access, write permissions, and editor seat. The workflow still validates the scanned article/line relationship and cumulative quantity bounds inside its existing transaction and request receipt handling, and now requires a Confirmed or Shipped Incomplete parent order. Normal property edits retain their lifecycle guard. Version policy and audit attribution use the existing property persistence path. The current entity guard does not implement the approval check described in its older interface comments; this change does not add or remove approval handling.

Validation: Tenant API build passed without warnings or errors. All 42 Action Link/fulfilment tests passed, including local isolated SQL tests for transaction rollback, concurrency, request retry receipts, and shipment behavior. Seventeen regression cases exercise the actual entity property service with controlled repositories. Live Pick/Pack/Ship remains pending Tenant API publication and retest.

## Other observed issue

Retest on 26 October after Tenant API publication: Pick succeeded for 2 pc of 100.080.420. Standalone workflow confirms Ordered 2, Picked 2, Packed 0. Screenshot added to the help guide. Pack cannot yet be opened from the live ribbon: it is the fourth button in a single default Column collection, which only renders three items. Changed the custom Uses ribbon group to Row orientation locally; Assembly publication and retest are pending. No packing or shipment has been recorded.

The phone QR panel overlaps the nested Pick article dialog. Capture future help images using Open on this device, or correct the scanner dialog layout before taking screenshots. Do not publish QR codes or action-link tokens in documentation.
