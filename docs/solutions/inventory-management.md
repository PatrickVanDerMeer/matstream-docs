# Inventory Management

MatStream provides a flexible foundation for managing inventory data — 
products, stock levels, suppliers, and procurement information — fully 
integrated with your engineering, quality, and document control data.

Because inventory items are entities in MatStream, they benefit from 
the same structured properties, lifecycle control, revision history, 
and relationship tracking as any other item in the platform. A product 
in your catalogue is linked to its engineering specification, its 
supplier, its quality records, and its stock information — all in one 
place.

---

## What is inventory management in MatStream?

Inventory management in MatStream means treating products, stock items, 
and suppliers as first-class entities — connected to the rest of your 
organisational data rather than isolated in a separate system.

This is what sets MatStream apart from standalone inventory management 
systems: your product record is not just a stock count — it is linked 
to the engineering specification it must meet, the supplier who provides 
it, the quality records raised against it, and the assemblies that use it.

---

## Typical inventory entity types

Your administrator configures the inventory entity types that match 
your organisation's needs. Common examples include:

| Category | What it represents |
|---|---|
| **Product** | A finished or semi-finished product in your catalogue |
| **Stock item** | A purchased or manufactured item held in inventory |
| **Supplier** | An organisation that supplies materials, components, or services |
| **Purchase order** | A formal order placed with a supplier |
| **Stock location** | A physical location where stock is held |
| **Batch / lot** | A specific production batch or delivery lot |

Each category carries the properties, lifecycle, and numbering scheme 
appropriate to that item type — all configured by your administrator.

---

## Product catalogue management

Products in MatStream are entities — with structured properties that 
capture everything your organisation needs to know about each product:

- Product name, description, and category
- Unit of measure — pieces, metres, kilograms, litres
- Current stock level and reorder point
- Supplier and supplier part number
- Lead time and minimum order quantity
- Linked engineering specification and drawing
- Linked quality records

### Product lifecycle

Products move through a configurable lifecycle in MatStream. A typical 
product lifecycle might look like:
Draft → Under review → Active → Discontinued
↓
Rejected → Draft (rework required)

Only approved, active products are visible to downstream systems — 
ensuring your product catalogue always reflects your current, approved 
range.

---

## Supplier management

Suppliers are entities in MatStream — linked to every product and 
purchased part they supply, every purchase order placed with them, 
and every quality record raised against their deliveries.

A supplier entity might carry:

- Supplier name, address, and contact details
- Supplier classification (approved, conditional, suspended)
- Linked approved products and purchased parts
- Linked non-conformance records
- Linked supplier deviation requests
- Performance data — on-time delivery, rejection rate

### Supplier approval lifecycle

Suppliers move through a formal approval lifecycle:
Prospective → Under evaluation → Approved → Conditionally approved
↓
Suspended → Re-evaluation required

Only approved or conditionally approved suppliers can be linked to 
active products — enforcing your approved supplier list (ASL) directly 
in the platform.

---

## Stock management

Stock levels in MatStream are properties on stock item entities — 
updated as stock moves in and out. Properties might include:

| Property | Description |
|---|---|
| Current stock | The current quantity on hand |
| Reorder point | The level at which a reorder is triggered |
| Reorder quantity | The standard quantity to order |
| Unit of measure | Pieces, metres, kg, litres, etc. |
| Stock location | Linked to a stock location entity |
| Batch / lot | Linked to a specific batch or lot entity |

Stock movements can be captured as linked entities — a goods receipt 
linked to a purchase order and a supplier, or a stock adjustment linked 
to a non-conformance that resulted in scrapped material.

---

## Integration with webshops and external systems

MatStream's API makes it straightforward to integrate stock and product 
data with external systems:

### Webshop integration

Product entities in MatStream can serve as the source of truth for 
your webshop catalogue. The MatStream API allows external systems to:

- Read current product data — name, description, price, unit of measure
- Read current stock levels — updated in MatStream as stock moves
- Push stock updates back into MatStream from warehouse management 
  systems or point-of-sale systems

This means your webshop always reflects your current approved product 
catalogue and up-to-date stock levels — without manual synchronisation.

### ERP integration

MatStream can exchange data with ERP systems via the API:

- Products and stock items created in MatStream can be pushed to the ERP
- Stock movements recorded in the ERP can update MatStream
- Purchase orders in the ERP can be linked to supplier entities in MatStream

!!! note
    Webshop and ERP integrations require custom development using the 
    MatStream API. MatStream provides the API and data model — 
    the integration layer is built to match your specific external 
    system. Contact your MatStream administrator or implementation 
    partner for guidance.

---

## Units of measure

MatStream has a built-in units of measure system — particularly 
important for inventory management where items are measured in 
different physical units:

| Dimension | Example units |
|---|---|
| Mass | kg, g, lb, oz |
| Length | mm, cm, m, inch, ft |
| Volume | ml, l, m³, gallon |
| Quantity | pieces, pairs, boxes, pallets |

Numeric properties on stock items and products are linked to a 
specific dimension — so the system knows that a weight property 
represents kilograms, not litres. This prevents unit confusion 
and supports downstream data quality.

---

## Traceability across inventory, engineering, and quality

The real power of inventory management in MatStream is the ability 
to trace any item across the full breadth of your organisational data:
Product
└── Linked to engineering specification (PLM)
└── Linked to approved drawing revision
└── Linked to supplier
└── Linked to supplier NCRs
└── Linked to current stock level
└── Linked to batch / lot records
└── Linked to goods receipt
└── Linked to purchase order
└── Linked to customer complaints
└── Linked to corrective actions

Every link is bidirectional and permanent. From the product record 
you can navigate to its specification, its supplier history, its 
stock movements, and its quality records — without leaving MatStream.

---

## Satisfying inventory control requirements

MatStream's inventory management capability supports common inventory 
control requirements:

| Requirement | How MatStream satisfies it |
|---|---|
| Approved supplier list | Supplier lifecycle with approval states |
| Product catalogue control | Product lifecycle with approval workflow |
| Stock traceability | Batch / lot entities linked to goods receipts and usage |
| Unit of measure consistency | Built-in UoM system linked to numeric properties |
| Audit trail | Immutable version history and transition log on all records |
| Integration with external systems | REST API for webshop and ERP connectivity |

---

## A typical inventory workflow in MatStream

New supplier identified
→ Supplier entity created: SUP-2026-0012
→ Supplier goes through approval lifecycle
→ Approved and added to the approved supplier list
New purchased part sourced from supplier
→ Purchased part entity created: PP-2026-0088
→ Linked to supplier entity
→ Engineering specification linked
→ Part goes through PLM approval lifecycle
→ Released as revision A
Purchase order placed
→ Purchase order entity created: PO-2026-0234
→ Linked to supplier and purchased part
Goods received
→ Goods receipt entity created
→ Linked to purchase order and batch / lot
→ Stock level updated on the stock item entity
Non-conformance found on incoming inspection
→ NCR entity created: NCR-2026-0056
→ Linked to the batch and the purchased part revision
→ Linked to the supplier
→ Disposition decided — material scrapped
→ Stock level adjusted
→ CAPA raised with supplier
Product made available on webshop
→ MatStream API pushes product data to webshop
→ Stock level synced automatically


---

## Who uses inventory management in MatStream?

| Role | How they use MatStream |
|---|---|
| **Procurement** | Manages suppliers, purchase orders, and stock levels |
| **Warehouse** | Records goods receipts, stock movements, and locations |
| **Quality** | Raises NCRs against incoming batches and manages supplier quality |
| **Engineering** | Links products to specifications and approved drawings |
| **Sales / webshop** | Reads current product catalogue and stock levels via API |
| **Management** | Views stock levels, supplier performance, and product status |
| **Administrator** | Configures inventory categories, lifecycles, and numbering |

---

## Getting started with inventory management in MatStream

1. **Configure inventory categories** — define your product, supplier, 
   and stock item types. See [Categories](../admin/categories.md).
2. **Configure lifecycle definitions** — define your product and supplier 
   approval workflows. See [Lifecycle](../entities/lifecycle.md).
3. **Configure numbering schemes** — define your product and supplier 
   numbering templates. See [Numbering schemes](../admin/numbering.md).
4. **Configure units of measure** — set up the physical dimensions and 
   units relevant to your products.
5. **Set up user groups** — define who can manage products, suppliers, 
   and stock. See [Users & groups](../admin/users-groups.md).
6. **Connect external systems** — integrate your webshop or ERP via 
   the MatStream API.

---

## Next steps

- [PLM](plm.md) — manage engineering parts and assemblies linked to 
  your products
- [Quality Management](quality-management.md) — manage NCRs and CAPAs 
  linked to your inventory records
- [Document Control](document-control.md) — manage specifications and 
  procedures linked to your products
- [Core concepts](../platform/core-concepts.md) — the building blocks 
  of the platform