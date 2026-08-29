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

## Demand and supply lines

Two different kinds of line feed the stock picture, and the distinction matters.

### Material Requirement lines — demand

A **Material Requirement** records that something is *needed*: four of this part, by this date, for this project. Nothing has moved in stock, so **no ledger entry is written when a requirement is raised**.

A requirement carries:

| Field | Where it comes from |
|---|---|
| **Required Qty** | The quantity needed — taken from the BOM line when raised automatically |
| **Required By** | When it is needed |
| **Reserved Qty** | How much has been reserved from existing stock (from the ledger) |
| **Ordered Qty** | How much has been put on a purchase order |
| **Open Qty** | Required − Reserved − Ordered — what still needs sourcing |

**Open Qty is the buyer's working number.** A requirement is finished when it reaches zero, regardless of how it got there — partly from the shelf, partly bought.

### Purchase and sales order lines — supply and commitment

These categories carry the **Stock Line** behaviour, meaning the line *is* a stock movement. Their quantities are computed live from the ledger:

- A purchase-order line writes `OnOrderAdd` when it enters Ordered, and `OnOrderRelease` when it leaves.
- A sales-order line reserves and then ships.

### How a requirement gets satisfied

1. An engineer moves a part in the Explorer to **Ready for Procurement**. If the *Create Material Requirement* automation is configured, a requirement line is raised automatically against the owning project.
2. A buyer reviews open requirements and decides per line:
    - **Reserve from stock** — writes an `MR Reservation` movement, raising Reserved Qty.
    - **Order it** — creates a purchase-order line, which writes `OnOrderAdd` and raises Ordered Qty.
3. Both routes can be used on the same requirement, so partial coverage works: 4 needed, 2 reserved, 2 ordered, 0 open.
4. Receipt of the purchase order posts `PO Receipt`, moving the quantity from On Order to On Hand.

!!! warning "Do not create requirement or order lines by hand"
    Both category types are hidden from the New dialog for a reason. A line created by hand has no matching ledger entry, so its quantities read zero and stock figures are wrong. Always let the procurement process create them.

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
- [Procurement](procurement.md) — requirements, purchase orders, receiving and vendor documents
- [Barcode scanning](barcode.md) - scanning items onto orders and pick lists
