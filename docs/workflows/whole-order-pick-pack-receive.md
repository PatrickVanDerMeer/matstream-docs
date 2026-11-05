# Print, then complete the whole order

Use these simple workflows when the warehouse handles all remaining quantities together.

Follow the departmental guides and movies: [Sales](create-sales-order.md), [Procurement](arrange-missing-stock.md), [Incoming Goods](receive-warehouse-goods.md), and [Dispatch](pick-pack-send.md). This page explains their shared behavior and administrator settings.

## Pick and pack a Sales Order

1. Confirm the Sales Order using the existing stock-confirmation workflow.
2. Select the order and use **Export / Print** to print its **Pick List**.
3. Collect the goods and tick the Done column on paper.
4. With the order selected in Explorer, open **Actions** and choose **Mark Picked**. Confirm that all lines are complete.
5. Print the **Packing List**, pack the goods, then choose **Mark Packed**.
6. Ship using the normal lifecycle transition.

Mark Packed requires every line to be fully picked. Completion fills the remaining line counters and leaves stock and lifecycle states unchanged. Stock moves when shipping. Repeating a completed action changes nothing. If one sales line cannot be updated, the entire action rolls back.

The order summary derives picking and packing progress from its line counters. There are no extra Picked or Packed header lifecycle states. Button and summary labels may differ if your administrator has customized them.

## Receive a Purchase Order

1. Issue the Purchase Order through its normal lifecycle.
2. Print the **Receiving List** from its Print choices.
3. Check the goods against the outstanding quantities, tick Done, and write the supplier delivery-note reference on paper.
4. Select the PO header in Explorer, open **Procurement**, and choose **Receive Order**.
5. Choose **Receive into** and confirm. All remaining quantities are received into that location; there are no line quantity inputs in this dialog.
6. Review received lines, skipped lines and warnings before closing.

A suggested location appears only when enabled by the administrator and unambiguous: the only location, or the single existing location shared by all outstanding items. Otherwise choose one.

Each purchase line commits separately. A later failure does not undo earlier receipts. Refresh and review warnings before retrying; a retry receives only quantities still outstanding. Lifecycle transitions can fail after goods have been received. Ask an administrator to repair the state; do not receive the physical goods again to repair a lifecycle warning.

The Receiving List includes ordered, already received and outstanding quantities. Outstanding follows the remaining on-order ledger balance, including releases and cancellations, so it can differ from ordered minus received. Printing never posts stock.

## Administrator setup

Run the Sales Orders and Procurement features in Manage to add missing whole-order defaults. The setup does not add new scanner or partial-receipt actions. Existing line-level and scanner configurations are preserved.

In **Configuration → System settings**, edit **Whole-order picking, packing and receiving** to enable or disable each operation, change button labels and confirmation descriptions, change progress labels, and control receiving-location suggestions. Server permissions still apply: sales requires access to the order and writable fulfilment properties; receiving requires ProcurementReceive and access to the destination location.

These options use MatStream's existing shared workspace settings. Personal preferences do not override them, and rerunning Manage adds only missing defaults.

Use **Print Templates** to edit the paper layouts, field mappings and category assignments. The receiving template maps OrderedQty, ReceivedQty and OutstandingQty. A setup rerun preserves existing metadata, maps, assignments and customized uploaded files; recognized bundled files may receive a bundled layout upgrade. Renaming a standard pick/pack/receiving template retains its identity through its document type. Preserve that document type when renaming it.

## Financial posting

New priced receipts and commitment releases use the remaining recorded commitment valuation, rather than a subsequently edited display price. Unpriced or historically inconsistent commitments remain unpriced and produce a reconciliation warning. Historical receipts are not repriced. Purchase ledger financial postings still have no currency attribution; review this limitation before relying on mixed-currency project totals.
