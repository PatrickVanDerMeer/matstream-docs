# Stock Management

MatStream's stock management layer tracks on-hand inventory quantities for items across locations. Stock data is linked to entity records, so inventory quantities live alongside the full part lifecycle, BOM, and document context.

---

## Concepts

### Items and locations

In MatStream, both inventory **items** (parts, materials, products) and **locations** (warehouses, shelves, bins) are regular entities in the workspace. Any entity in the correct category can be an item or a location. Your administrator defines which categories serve as item types and which serve as location types.

### Stock position

A **stock position** is the current quantity of a specific item at a specific location. It tracks four quantities:

| Quantity | Description |
|---|---|
| On Hand | Physical units currently in stock |
| Reserved | Units committed to outbound orders (sales, manufacturing) but not yet shipped |
| On Order | Units on open purchase or manufacturing orders, not yet received |
| Available | On Hand minus Reserved — what is freely available to allocate |

### Stock ledger

Every change to a stock position is recorded as an immutable ledger entry. The ledger provides full traceability: who made the change, when, why (via a note), and what the delta was.

---

## Viewing stock on an entity

Open any item entity and navigate to the **Stock** display tab (if configured for that category). The tab shows:

- Current on-hand, reserved, on-order, and available quantities per location
- A movement ledger showing all historical adjustments and transactions

Open a location entity and navigate to its **Stock** tab to see all items held at that location.

---

## Adjusting stock manually

Manual adjustments are used for inventory counts, write-offs, receipts without a purchase order, and corrections.

1. On the item entity's Stock tab, click **Adjust Stock** (or use the tray icon from the ribbon).
2. Select the location.
3. Enter the **adjustment quantity** — positive to add stock, negative to remove.
4. Optionally enter a **note** explaining the reason.
5. Click **Apply**.

The adjustment is recorded in the ledger immediately. The on-hand quantity updates.

!!! note "Zero delta"
    The Apply button is disabled when the adjustment quantity is zero, preventing empty ledger entries.

---

## Movement types

The ledger distinguishes the following movement types:

| Movement type | Trigger |
|---|---|
| Adjustment | Manual adjustment via the Adjust Stock dialog |
| PO Receipt | Purchase order goods receipt posted |
| SO Reservation | Sales order line reserved stock |
| SO Release | Sales order reservation released |
| SO Shipment | Sales order shipped |
| On Order Added | Purchase or manufacturing order line created |
| On Order Released | Purchase or manufacturing order line cancelled or closed |
| MR Reservation | Material requirement reservation |
| MR Release | Material requirement reservation released |

---

## Stock lines

Stock lines represent open order quantities — material requirements, sales order lines, or purchase order lines. They write `OnOrderAdd` or reservation ledger entries when created, and `OnOrderRelease` entries when closed.

Stock lines are created programmatically (e.g. from an import or automation) or manually from the entity detail page.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/wid/{slug}/stock/positions/by-item/{itemEntityId}` | Stock positions for an item across all locations |
| `GET` | `/api/wid/{slug}/stock/positions/by-location/{locationEntityId}` | All items at a location |
| `GET` | `/api/wid/{slug}/stock/ledger/by-item/{itemEntityId}` | Movement ledger for an item |
| `GET` | `/api/wid/{slug}/stock/ledger/by-location/{locationEntityId}` | Movement ledger for a location |
| `POST` | `/api/wid/{slug}/stock/adjustments` | Post a manual adjustment |
| `POST` | `/api/wid/{slug}/stock/lines` | Create a stock line |

---

## Setting up stock management

1. **Define categories** for items and locations in Configuration → Categories.
2. **Add a Stock display tab** to the item and location categories (Configuration → Categories → Display Tabs). Set the tab type to `Stock` / `Ledger`.
3. **Create location entities** — create entities in a location category for each warehouse, shelf, or bin.
4. **Start adjusting** — open an item entity and use the Stock tab to post initial on-hand quantities.

---

## Related

- [Display Tabs](../admin/display-tabs.md) — configure the Stock and Ledger tabs on categories
- [Entities overview](../entities/overview.md) — entity concepts
- [Explorer](explorer.md) — navigate to item and location entities
