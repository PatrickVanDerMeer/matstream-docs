# Inventory and Sales Order — local regression verification

Date: 19 November 2026. Local SQL databases and localhost services only. This report records software test transactions, not physical receipt or dispatch. No external vendor/customer message or internet deployment is part of this test.

Status: **Passed for the recorded local scope.** Fresh Sales setup, browser Inventory/navigation checks, a final priced Sales Order workflow and a separate physical-stock shipment test passed after the regressions below were corrected. API/service workflow evidence and browser observations are distinguished explicitly.

## Test environment and setup

- Core database: `localhost/PMSoftware`; tenant database: `localhost/MatStream`.
- Core API `https://localhost:7273`; tenant API `https://localhost:7239`; Manage `https://localhost:7081`; workspace app `https://localhost:7053`.
- At the user's request, the seven previously active local workspaces (61, 66, 107, 109, 110, 111 and 112) were archived through the normal `WorkspaceManageService.DeleteWorkspaceAsync` application path with each workspace's existing owner. Other pre-existing rows were already archived. The cleanup verification found no remaining active workspace before creation.
- Fresh workspace **113**, **Inventory Sales Order Regression 2026-11-19**, slug `inventory-sales-20261119`. It was created through the normal localhost Core API with QA user16 as owner. User1 has Manage Admin/Editor membership and enabled tenant user450 in Administrators group1143 for local browser inspection.
- Sales Orders Configuration and Sales Orders Sample Data seeded through the real Manage setup service: **888 completed steps, 0 warnings**. Setup supplies the existing Sales, Purchasing and physical Warehouse/Rack/Storage Location structure.
- Existing workspace data was not relabelled or copied into the fresh test workspace. Private authentication artifacts are excluded from this report.

## Help-file test sequence

The acceptance sequence follows these guides:

1. [Create and confirm a Sales Order](../docs/workflows/create-sales-order.md): create Draft, add Library lines, review quantities/pricing and availability, save, Confirm.
2. [Create a Purchase Order from Material Requirements](../docs/workflows/arrange-missing-stock.md): inspect uncovered demand, create a linked PO, review supplier/unit/cost, Issue.
3. [Receive a complete supplier delivery](../docs/workflows/receive-warehouse-goods.md): generate the Receiving List, receive outstanding quantities into a storage-location leaf, inspect results and verify stock/commitment balances.
4. [Pick, pack and send a full order](../docs/workflows/pick-pack-send.md): inspect availability, generate Pick/Packing Lists, Mark Picked, Mark Packed, Ship, verify line completion and actual stock/reservation movement.

Receipt and fulfilment retries must be checked against final quantities and ledger evidence; a lifecycle label alone is not proof of stock posting.

## Browser navigation checks

| Check | Observed result |
|---|---|
| Fixed Inventory sidebar entry | Fresh workspace Inventory route opens |
| Existing physical structure | Warehouse hierarchy and storage-location leaves are visible in Inventory |
| Empty location | Contextual Stock tab and Add Item dialog open without an existing selected stock position |
| Explorer separation | Initial test found a Warehouse folder-tree leak below root while the Explorer grid correctly hid it. After correction, rebuild and Web restart, browser retest showed only Purchasing and Sales below root; Warehouse was absent from both tree and grid |
| Shared folder favorite | Created a 01-A favorite in Inventory, switched to Explorer and activated it; redirected to `/inventory?entity=17986`, selected the storage location and exposed Stock |
| Independent section navigation | Inventory restored 01-A while Explorer retained Sales Orders |
| Empty-location Add Item | Opened and cancelled successfully; no stock was posted by this UI check |
| Final stock row | Inventory shows ITM-00003 at 01-A with Stock Adjust and Ledger enabled |
| Stock Ledger dialog | Shows Adjustment at 01-A +6, SO Reservation +2, SO Shipment at 01-A −2 and reservation release −2, matching final on hand 4 / reserved 0 |
| Stock item properties | Stock Unit 1 (pc), Is Stock Item Yes, On Hand Stock Qty 4 |
| Adjust Stock dialog | Opened with Current On Hand 4; Apply disabled at zero adjustment; cancelled; final browser balance remained 4 |
| Older entity links | Cross-section route uses the same verified folder-location path; a separate historical entity link remains untested |

## Automated regression checks

| Suite | Passed | Skipped | Evidence |
|---|---:|---:|---|
| Manage Sales/stock/seed behavioral regressions | 50 | 0 | `regression-checks/manage-sales-stock.log` and `.trx` |
| Infrastructure Sales/stock/source regressions | 70 | 2 | `regression-checks/api-sales-stock.log` and `.trx` |
| **Total** | **120** | **2** | Skipped tests are not passing evidence |

The final Infrastructure run includes six supplier-selection policy tests and ten supplier currency/default tests. The earlier separate six-test run is included in the final total, not added again. These checks support the fresh-workspace browser and business-flow evidence.

## Regressions found during acceptance

- **Explorer tree scope:** the root's initial recursive load showed Warehouse in the left folder tree even though the grid hid it. The correction filters the same category role from initial and expanded folder data. Browser retest passed after rebuild and restart.
- **Standalone Sales procurement:** confirmation created MR18117, but PO creation skipped it with “Select the project supplier source and agree its terms on the MR before ordering.” The Engineering supplier-review gate was applying to ordinary Sales demand. The targeted correction adds `PurchaseSelection.RequireSelectionForSalesOrders`, default `false`, while retaining `RequireSelection` for non-sales/project demand. Reviewed supplier selection for Sales applies only when both `RequireSelection=true` and `RequireSelectionForSalesOrders=true`; the new flag alone has no effect when `RequireSelection=false`. No standalone administrator UI was added for this policy. An explicitly selected Sales source still requires its valid agreed version and terms; there is no fallback to an unreviewed source. Six focused supplier-selection policy tests passed; acceptance then created PO18118 with the correct source/vendor.

- **Standalone Sales PO issue:** after source selection was corrected, PO18118 was created with the expected vendor/source, but Draft → Issued was blocked by the Engineering vendor-share/PDF gate. The Sales help demonstration intentionally sends no vendor invitation. The correction seeds the portal gate only for Engineering. The fresh workspace was repaired through the normal API; issue and receiving subsequently passed without a vendor share.

- **Supplier currency default:** the fresh supplier Currency editor displayed configured category default EUR, but there was no own property-value row. `GetSupplySourcesAsync` ignored that default, leaving PO Currency blank. This is a source-resolution regression, not missing manual setup data. The resolver now uses the configured category-property default and then the property initial value for properties without inheritance, while preserving explicit projected source values. No currency is hardcoded. The final newly created PO18128 resolved EUR without adding an own supplier currency value; the priced workflow and focused default-resolution tests passed.

## Sales Order workflow results

The authenticated localhost API/service runner completed **30 assertions** on the final priced order, following the help-file sequence. It saved the order and Library line, checked a four-piece shortage without stock-unit errors, confirmed demand, created a linked MR and supplier PO, issued/received the PO, then picked, packed and shipped the Sales Order. These are API/service operations, not a claim that every workflow button was clicked in the browser.

| Record | ID | Final result |
|---|---:|---|
| Sales Order, final priced help workflow | 18124 | Shipped |
| Sales Order Line | 18125 | Complete |
| Linked Material Requirement | 18127 | Delivered |
| Purchase Order | 18128 | Received |
| Purchase Order Line | 18129 | Received 4 pc, remaining 0 |
| Help article | 18013, ITM-00002 | Receipt +4; shipment −4; final on hand 0 |
| Additional stock-item Sales Order | 18121 | Shipped |
| Additional Sales Order Line | 18122 | Complete |
| Additional stock article | 18015, ITM-00003 | Synthetic opening +6; shipment −2; final on hand 4 |
| Receiving/storage location | 17986, 01-A | Warehouse → Rack 01 → 01-A |

Both articles finish with **Reserved 0** and **On Order 0**. Ledger evidence distinguishes PO Receipt, Shipment and the labelled synthetic opening adjustment. The stock-item scenario confirmed a two-piece reservation, six pieces still on shelf after picking/packing, then the actual two-piece shipment deduction and reservation release.

The acceptance checks also verified:

- Whole-order receipt returns four pieces with no skipped lines or warnings, delivers MR18127 and settles PO18128.
- Retrying receipt cannot add the same four pieces twice.
- Packing before picking is rejected.
- Repeated whole-order Pick and Pack each change zero lines.
- Picking and packing preserve stock and leave the Sales header Confirmed until shipment.
- Receiving, Pick, Packing List and draft-invoice PDF endpoints return PDF files. Text extraction verified Receiving List quantities change from Ordered 4 / Received 0 / Outstanding 4 to 4 / 4 / 0; Pick and Packing Lists show 4 pc. The invoice, Receiving, Pick and Packing List PNGs were visually inspected; layouts and the expected quantities and totals were correct.

The test workspace and successful transactions are retained for inspection. Superseded incomplete orders 18105, 18106 and 18110 were cancelled through the normal APIs and are not counted as completed workflow evidence.

Commercial checks on the final order resolved **EUR 22.00 per pc**, quantity **4**, total **EUR 88.00**. The supplier PO resolved **EUR 15.40 per pc**, total **EUR 61.60**, independently from the customer selling price. The draft invoice rendered with the Sales amounts; this is document preparation, not accounting posting or an emailed invoice.

The separate physical-stock shipment test completed in the earlier 25-assertion run and remains as order18121/line18122. It was not recreated in the final priced-order rerun. 

## Evidence and limits

### Quick Search ribbon follow-up

Explorer and Inventory reserve ribbon space for Quick Search so it stays in the ribbon header without covering commands. Commands that do not fit remain available through the ribbon overflow or scrolling controls. Both the folder and main ribbons now refresh their layout when a pane resize finishes.

Local Chrome checks verified Explorer search for `SO-2026.0001` and Inventory search for `ITM-00003`. Inventory search uses the existing workspace search: the matching result was the completed Sales Order Line, not a stock-quantity filter. The search stayed on the ribbon at normal, 125% and 175% browser zoom. Explorer command overflow and Inventory's hidden Stock tab were reachable; Stock commands remained beside search using the ribbon's scrolling controls at reduced width. The web build and whitespace checks passed.

Direct splitter-drag verification remains incomplete: the computer-control tool did not move the pane dividers. Zoom exercised reduced available ribbon widths, but does not establish that the pane resize event fired. No stock or order data was changed during these search checks.

Local artifacts are under `tmp/inventory-sales-20261119/`: `workspace.json`, `setup-report.json`, `setup.ndjson`, `seed.log` and the Sales workflow runner/results. Private session files must not be published or copied into documentation.

Final priced workflow evidence is in `fresh-complete/`: `assertions.json`, `final-ids.json`, `final-stock.json`, pricing/source/receipt/transition results, `source-currency-default-proof.json` (zero own Currency rows, configured EUR default), and PDF files with extracted text and rendered PNGs. Earlier `assertions.json` and `final-stock.json` in the run root retain the separate physical-stock test.

The browser tested navigation, favorites, retained selection, stock display and stock dialogs. Sales/procurement state transitions and stock postings were exercised through authenticated local APIs/services. A historical entity-link browser check, separate-role permission rehearsal, scanner/partial delivery, returns and full Engineering Project acceptance were outside this run. The Zensical documentation build passed with no issues.
