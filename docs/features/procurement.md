# Procurement

Procurement in MatStream runs on the same entities as engineering. A material requirement, a purchase order and its lines are all governed entities with their own categories, lifecycles, properties and permissions — so demand, ordering and receipt form one traceable chain instead of three spreadsheets.

This page covers the buyer's workflow. For the stock quantities behind it, see [Stock management](stock.md).

---

## The chain

```
Engineering item  →  Material Requirement  →  Purchase Order line  →  Receipt  →  Stock
   (Explorer)          (demand, no stock)      (on order)            (on hand)
```

Each step is linked to the one before it, so any purchase order line can be traced back to the requirement that justified it, and from there to the item and project that caused the demand.

| Link | What it bonds |
|---|---|
| Requirement source | The requirement line → the project occurrence that generated it |
| Fulfilment link | The purchase-order line → the requirement line it fulfils |

---

## Raising a requirement

A **Material Requirement** records that something is needed: this quantity, by this date, for this project. Raising one moves nothing in stock and writes no ledger entry.

Requirements are normally raised automatically. Attach the **Create Material Requirement** automation to a lifecycle transition — typically the one that moves an item to *Ready for Procurement* — and releasing an item raises a requirement against its owning project, linked back to the item that caused it.

The owning project is found by walking up the folder tree from the item, so one automation configuration serves every project in the workspace.

!!! warning "Do not create requirement lines by hand"
    Requirement and order-line categories are hidden from the New dialog deliberately. A line created by hand has no matching links or ledger entries, so its quantities read zero and the stock picture is wrong.

---

## Reading a requirement line

Every requirement tracks three quantities against what was asked for:

| Quantity | Meaning |
|---|---|
| **Required Qty** | What is needed |
| **Reserved Qty** | Covered from existing stock |
| **Ordered Qty** | Covered by a purchase order |
| **Open Qty** | Required − Reserved − Ordered — what still needs sourcing |

**Open Qty is the buyer's working number.** A requirement is finished when it reaches zero, no matter how it got there: four needed can be two reserved from the shelf and two bought, and the line is then complete.

---

## Covering a requirement from stock

1. Open the requirement line, or select it in the buyer's working list.
2. Choose **Reserve from stock**.
3. Pick the location to reserve from and the quantity.
4. Apply.

The reservation writes an `MR Reservation` movement to the ledger and raises Reserved Qty. Reserved stock stays on hand but is no longer *available* — see [Stock management](stock.md) for how the two differ.

Existing reservations for an item can be reviewed and released again from the item's Stock tab.

---

## Raising purchase orders

Select one or more requirement lines and choose **Create purchase order**. The dialog shows what you selected and how it will be grouped.

**One purchase order is created per vendor.** Selecting twenty requirements across six vendors produces six orders, each covering its own lines.

| Option | Effect |
|---|---|
| **Merge identical items into one order line** | The same item appearing on several requirements becomes one order line. Quantities are summed and the earliest required date is kept. Each requirement is still tracked separately, so coverage stays per requirement. |

When the dialog finishes it reports three things:

- **Created** — each order number, with its line count and how many requirements it covers.
- **Skipped** — requirements that could not be ordered, each with its reason. A skipped line never blocks the rest of the order.
- **Warnings** — anything worth knowing that did not stop the order.

Requirements that were covered move to **Ordered**, and their Ordered Qty rises.

### How the vendor is chosen

Two vendor properties are in play, and they mean different things:

| Property | Where it lives | Meaning |
|---|---|---|
| **Preferred Vendor** | The requirement, inherited from the item definition | The demand side's preference — who we would like to buy this from |
| **Vendor** | The purchase order | Who was actually ordered from |

MatStream resolves the vendor by walking the chain: the requirement line's own value first, then the occurrence that sourced it, then the library definition. A requirement with no vendor anywhere in that chain is reported in **Skipped** and left for you to deal with — it never blocks the other lines.

---

## Receiving

1. Open the purchase order and choose **Receive purchase order lines**.
2. Set **Receive into** — the stock location the goods are arriving at.
3. Enter the received quantity per line.
4. Receive.

Receipt posts a `PO Receipt` movement, moving quantity from On Order to On Hand at the receiving location, and settles the ordered quantity on the originating requirement. Partial receipts are supported: receive what arrived and the rest stays outstanding.

---

## Cancelling an order

A whole order or named lines of it can be cancelled.

- A cancelled order is **kept, never deleted** — it may already have been sent to the vendor.
- The order moves to **Cancelled**.
- Requirements that the cancelled lines were covering return to **Requested**, with their Ordered Qty released, so they reappear as open demand.
- Lines that have already received goods, or that have nothing outstanding, are reported as skipped rather than failing the whole call.

---

## On Order quantities

The On Order figure is maintained by two lifecycle automations rather than by the order screens directly:

| Automation | When it runs |
|---|---|
| **PO: Add to On Order** | A purchase-order line enters *Ordered* — adds the ordered quantity to On Order |
| **PO: Release from On Order** | A line leaves *Ordered* — releases the On Order quantity again |

If On Order never moves in your workspace, check that both automations are attached to the purchase-order lifecycle transitions and that their settings are complete — an automation with missing required settings is marked with a warning triangle in the transition's Automations tab.

---

## Required vendor documents

Buying an item often means chasing paperwork as well as goods. A library item can declare the documents its vendor must supply — general arrangement drawings, technical datasheets, IOM manuals, material certificates, inspection and test plans, FAT reports, pressure test certificates, declarations of conformity, recommended spare parts lists.

- On the library item, these are **requirement templates**: they define what is needed, not the files themselves.
- When the item is placed in a project — and again when procurement freezes the item revision — the project occurrence receives **its own document rows** from those templates.
- Each row has its own lifecycle: *Required → Submitted → Accepted*, so the state of vendor documentation is visible per project.

!!! warning "Vendor files belong to the occurrence, never the library item"
    The library row is a template shared by every project that uses the item. Uploading a vendor's certificate onto it would attach one project's paperwork to every other project using that part.

---

## Permissions

Procurement is gated the same way as everything else: by group membership on lifecycle transitions. The Setup Wizard's Engineering Project configuration seeds a **Purchasing** group for exactly this purpose.

Restrict the transitions that raise requirements, place orders and receive goods to the groups that should perform them. A transition with no permission rows is open to everyone.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/wid/{wid}/purchase-orders/from-requirements` | Create purchase orders from selected requirement lines, one per vendor |
| `POST` | `/api/wid/{wid}/purchase-orders/receive` | Receive lines into a stock location |
| `POST` | `/api/wid/{wid}/purchase-orders/cancel` | Cancel a whole order or named lines |
| `POST` | `/api/wid/{wid}/stock/reservations` | Reserve stock against a requirement |
| `POST` | `/api/wid/{wid}/stock/reservations/release` | Release a reservation |
| `GET` | `/api/wid/{wid}/stock/reservations/by-item/{itemEntityId}` | Existing reservations for an item |

---

## Setting up procurement

1. Apply the **Engineering Project** template in the Setup Wizard — it seeds the material requirement, purchase order, stock and required vendor document categories with their lifecycles.
2. Attach **Create Material Requirement** to the engineering transition that signals an item is ready to buy, and complete its settings (target category, project category, lifecycle).
3. Attach **PO: Add to On Order** and **PO: Release from On Order** to the purchase-order lifecycle transitions.
4. Create your stock locations, and confirm buyers can reach the receiving location.
5. Set **Preferred Vendor** on library items so requirements inherit a vendor instead of being skipped.

---

## Related

- [Stock management](stock.md) — positions, ledger, reservations and movement types
- [Lifecycle definitions](../admin/lifecycle.md) — transitions, criteria and automations
- [Automations](automations.md) — how the built-in procurement automations are configured
- [Categories](../admin/categories.md) — category behaviours, including Material Requirement and Purchase Order
- [Workspace setup](../getting-started/workspace-setup.md) — what the Engineering Project template seeds
