# Production warehouse receipt — local verification

Date: 17 November 2026. Local SQL/services only. No actual manufacture, physical receipt, internet deployment or external messaging is claimed.

## Implementation

- `ProductionReceiptSettings` maps the production category, item/source/completed-quantity/location/Stock Unit properties, completed-state IDs, current-completion condition, messages and ledger note.
- `Warehouse: Receive completed production` runs `matstream.actions.receiveProduction();` through the normal stored-automation runner. The action must be its script's only write action.
- Native service validates the authenticated Editor actor, Stock Manage grant, entity read access, current order version/category/state, original Generated From source version, pinned manufactured definition, positive completed quantity, matching enabled stock unit and real workspace stock shelf.
- Default `RequireCurrentCompletion=true` reuses the production-completion evidence checks; no second inspection checklist is introduced.
- Real StockMovementService posts movement type **11 ProductionReceipt** into the chosen pinned inventory position. Reserved and On Order quantities are unchanged.
- `tbl_ProductionReceipt` stores order/source/item version IDs, quantity/unit/location, receiver and UTC timestamp. It uses int identifiers, positive-quantity constraint and a unique workspace/order key. Journal and inventory writes share a serializable transaction; rollback is all-or-nothing.
- Receipt retries return the existing receipt; editing fields later does not relocate, resize or duplicate posted stock. No partial receipts/returns/reversal UI is introduced.
- A configurable Memo editor provides status/check/receipt controls. Historical order views cannot post; technical record read-only status does not itself remove the separate Stock Manage action on the current order.
- Focused **Production Warehouse Receipt** Manage template and Engineering Project inclusion; `Setup.ProductionReceipt.v1` preserves administrator customisations. New inspection setups include Warehouse read-only evidence access; existing permissions are not rewritten.
- Repeatable migration `20261117_production_receipt.sql`, embedded with API migrations; workspace purge clears receipt journals before their referenced entities/versions. Unit references also retain their UoQ definition through an FK.

## Automated verification

| Check | Result |
|---|---|
| Focused receipt tests | 9 passed, including isolated SQL integration |
| API regression | 1,426 passed; 52 skipped (not passing evidence) |
| Manage regression | 312 passed |
| API / Manage / Web Assembly builds | Passed, zero warnings/errors in application builds |
| Strict documentation build | Passed; existing two unlisted navigation pages remain informational |

The isolated SQL test applies the real receipt migration twice, uses the real stock-position/ledger repositories and exercises:

- Completed-state requirement; wrong-workspace location; unit mismatch; non-pinned definition; denied entity access.
- Denied **Stock Manage** with Entity Read still granted, and stale order version.
- A forced journal-insert failure **after stock writes**: both ledger and position roll back to empty.
- Advancing the Library latest pointer from manufactured A to B: receipt still enters pinned version A. Advancing a source with only an ambiguous legacy shared link blocks; an explicit historical source pin still qualifies.
- Two independent request-scoped contexts posting concurrently plus a sequential retry: one journal, one stock movement and one pc total.
- Receipt display retains original revision/name and disables another receipt.

Unit tests cover stock precision/range and a failing script. Manage's installed-marker test verifies that reruns return before changing scripts/mappings/defaults.

## Full native engineering workflow test

Workspace 108 **Engineering Workflow Test**, actor 360, dedicated test project 16002. Header order **16118**, final order **16119**. The ordinary completion rehearsal stages synthetic current accepted inspections, material stock/reservations and accepted supplier documents within an outer rollback transaction, completes the header and then final assembly through normal lifecycle services, and exercises receipt before rolling everything back.

Both manually created readiness fixtures initially lacked the normal production-order Generated From link, location and unit. Receipt correctly blocked them. For this controlled transaction, the helper explicitly supplied the known source references and pc unit; the actual normal production-order creation script already supplies source provenance and copies quantity/unit. No unverified latest revision was silently substituted.

The helper then used the real stored receipt script/automation runner:

| Order | Manufactured item/version | Result |
|---|---|---|
| 16118 | ITM-00006 revision 0, version 21644; source 16115/version 21970 | 1 pc into 01-A (location 15682), pinned; retry returned same receipt |
| 16119 | ITM-00000 revision 0, version 21647; source 16116/version 21971 | 1 pc into 01-A, pinned; retry returned same receipt |

Stale-version calls failed. Ledger deltas were exactly +1 for each output, with no duplicate receipt. Stock/reservations, new inspection acceptances/versions/states, temporary provenance/unit references and both journals were rolled back. Receipt identity values consumed during rollback are not reused or renumbered. Original test orders remain Draft with their original blockers.

Evidence: `tmp/production-receipt-native-test.log` and `tmp/production-receipt-final-audit.txt`.

## Browser verification and settings correction

The existing local browser session remained authenticated. Restarted the stale local Blazor dev server after rebuilt WebAssembly assets initially failed to instantiate; no authentication weakening or code-level runtime workaround was used.

Browser verified:

1. Receipt fields and live blocked status render on the header order.
2. The first picker configuration incorrectly searched Library definitions. Storage Locations are Folder-family records. Corrected the seed and local receipt property's editable picker JSON with `FamilyType: 8`; no new special picker is required.
3. The corrected Search Records dialog lists stock shelves; selected **01-A**, saved, reopened and confirmed persistence.
4. **Receive completed goods** remains disabled for the Draft/unfulfilled test order even with a saved shelf.
5. Cleared and saved the temporary destination; reopened and verified the original missing-location and production blockers. No browser stock receipt was posted during this initial blocked-order check.

Screenshots: `208-production-receipt-location-picker.png`, `209-production-receipt-blocked.png`. The positive browser continuation below now also proves receipt posting. Distinct Warehouse account permissions were not exercised. Fresh Manage Setup UI installation remains separate validation; local configuration was installed through a guarded native test helper, while source seed and preservation tests were built and checked.

## Positive browser continuation — 17 November 2026

Using the existing signed-in local account, prepared clearly synthetic current inspection evidence, material prerequisites and supplier-document acceptances through the guarded native fixture helper. Completed both orders through normal lifecycle services. Their known Generated From references and pc placement units were staged explicitly because these manually created fixtures lacked them. Receipt completion checks stayed enabled; no authentication or permission bypass was used.

In the browser, selected shelf **01-A** through Search Records, saved/reopened each order, checked readiness and clicked **Receive completed goods**:

| Order | Browser receipt | Pinned manufactured version | Quantity / destination |
|---|---|---|---|
| 16118, ITM-00006 | 5 | 21644, revision 0 | 1 pc / 01-A |
| 16119, ITM-00000 | 6 | 21647, revision 0 | 1 pc / 01-A |

Both panels showed received status and disabled another receipt. Rechecking the final order retained receipt 6. The warehouse grid showed exactly one of each output; reopening the header ledger showed its +1 movement with zero Reserved/On Order deltas. Found and corrected a missing StockController display mapping: movement 11 displayed `Unknown (11)`; it now displays **Production Receipt**. Rebuilt API successfully with zero warnings/errors, restarted it and verified the corrected label in the browser.

Screenshots **210–215** document each ready/received panel, both shelf quantities and the ledger. Screenshot **216** records the restored empty test shelf. The guide includes the full walkthrough.

Cleanup used the guarded fixture helper: archived synthetic journals 5/6 locally, reversed their output stock with explicit adjustment movements, removed only those synthetic journal rows and restored the original current Draft orders, inspection/RVD evidence, temporary input stock/reservations, provenance/units and receipt automation parameters. Historical synthetic versions and receipt/reversal ledger movements remain traceable. This is disposable local-test cleanup, not a product receipt-return feature. The browser refreshed shelf 01-A and confirmed zero items afterwards.

Fresh Manage Setup UI installation and a distinct Warehouse-user rehearsal remain separate validation. One account performed multiple test roles; this does not prove role separation or real physical inspection/manufacture.

## Limits / next step

This feature receives the full output once. It does not issue/consume component BOM quantities, reserve output to a project, calculate production cost, transfer stock, reverse receipts or pay anyone. The manufactured header must be issued when used in final assembly to avoid double-counting loose and incorporated stock. Those input-issue/reservation-release and later as-built/client handover steps remain separate work.

[Workflow guide](../docs/workflows/engineering-production-receipt.md).
