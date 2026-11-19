# Inventory

Open **Inventory**, directly below **Library** in the workspace sidebar to manage the existing warehouse structure and stock. The page is available at `/w/{workspaceSlug}/inventory`. **Inventory** is a fixed navigation label; there is no administrator setting to rename it.

## Find a location

Browse a Warehouse folder, expand its racks, and select a storage-location leaf, for example **Warehouse → Rack 01 → 01-A**. Warehouse folders and their contents are shown here instead of in Explorer. Projects, assemblies and other workspace folders remain in [Explorer](explorer.md).

The move preserves the existing entities, category names, stock positions, ledger and reservations. **Warehouse**, **Rack** and **Storage Location** still describe the physical hierarchy; they have not been renamed to Inventory. Warehouse folders are identified by their category's **Warehouse** business role, so renaming a physical warehouse does not change where it appears.

## Work with stock

Use the familiar folder tree, entity grid, properties and preview panels. Select a storage location to inspect its stock positions. The contextual **Stock** ribbon provides the existing stock actions, including adjustments, ledger inspection, and adding or removing an item at a location, when the selection and permissions allow them.

Item definitions remain available in Library with their configured **Stock** display tab. Purchase-order receiving and material-requirement reservation workflows keep their existing actions and permissions. See [Stock management](stock.md) for quantities, units, replenishment and ledger behaviour, and [Receive a complete supplier delivery](../workflows/receive-warehouse-goods.md) for receipts.

## Access

Inventory uses the existing navigation and entity-reading permissions. Stock operations still require the applicable `stock.view`, `stock.manage` or `stock.reserve` grant and the relevant entity and lifecycle access. Moving the page does not grant access to records or actions that a user could not use before.
