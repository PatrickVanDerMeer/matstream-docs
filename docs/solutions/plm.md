# Product Lifecycle Management (PLM)

MatStream is a natural fit for Product Lifecycle Management. It provides 
engineering teams with the structured data model, revision control, BOM 
traceability, and approval workflows that PLM requires — without the 
cost and complexity of traditional PLM systems.

---

## What is PLM?

Product Lifecycle Management is the discipline of managing a product's 
data and processes from initial concept through design, release, 
production, and eventual retirement.

PLM sits above PDM (Product Data Management) tools like Autodesk Vault:

| Layer | Tool | Focus |
|---|---|---|
| **PLM** | MatStream | Lifecycle, approvals, BOM traceability, structured data |
| **PDM** | Autodesk Vault | CAD files, file versions, check-in/check-out |
| **CAD** | Autodesk Inventor | Design and engineering |

MatStream does not replace Vault or Inventor. Your designers continue 
working in their familiar tools — MatStream adds the PLM layer on top.

---

## What MatStream manages in a PLM context

### Parts and assemblies

Parts and assemblies are the core objects in any PLM implementation. 
In MatStream they are configured as entity categories — for example:

- **Raw Material** — bulk materials with material grade, density, and 
  supplier properties
- **Purchased Part** — bought components with manufacturer, part number, 
  and lead time properties
- **Manufactured Part** — internally produced parts with manufacturing 
  process and tooling properties
- **Assembly** — multi-level assemblies with BOM children linked via 
  the Uses relationship
- **Drawing** — technical drawings linked to the part or assembly they 
  describe

Each category carries the exact properties your engineering process 
requires — configured by your administrator, not hardcoded by the vendor.

### Bills of Materials (BOM)

MatStream tracks the relationships between assemblies and their 
components as **links** between entities. This gives you:

- **Uses view** — a flat or tree view of everything an assembly 
  directly or indirectly uses
- **Where-used view** — which assemblies reference a given part, at 
  any level of the BOM
- **Multi-level BOM tree** — the full assembly structure expanded on 
  demand, lazy-loaded for performance on large assemblies
- **Impact analysis** — when a part changes, instantly see every 
  assembly affected via the where-used tree

### Engineering drawings

Drawings are entities in MatStream — linked to the part or assembly 
they describe. When a part is revised, the associated drawing revision 
is tracked independently, giving you full control over drawing release 
alongside part release.

PDF drawings can be viewed directly in MatStream via the built-in PDF 
viewer — no need to open a separate application.

### Revision control

Every formal release of a part or assembly is captured as a **revision** 
with a permanent, immutable label. Revision schemes are configurable 
per category:

- Mechanical parts: A → B → C → ...
- Documents and drawings: 1 → 2 → 3 → ...
- Long-lived items: AA → AB → ... → AZ → BA → ...

The full revision history is always available — you can view the exact 
state of any part at any previous revision, including its properties, 
files, and the BOM structure at that point in time.

### Lifecycle and approval workflows

Every part and assembly moves through a configurable lifecycle. A 
typical engineering lifecycle might look like:
Draft → In Review → Released → Obsolete
↓
Rejected → Draft (rework)

At each transition:
- Only authorised user groups can trigger the transition
- The transition is permanently logged
- Lifecycle state permissions control what can be edited and by whom

This gives you a formal, auditable approval process without the 
complexity of a traditional PLM workflow engine.

---

## Integration with Autodesk Vault

If your engineering team uses Autodesk Vault, MatStream can synchronise 
parts, assemblies, and property data from Vault automatically via the 
**Vault integration plugin**.

The sync pipeline:

1. The Vault plugin exports part and assembly data from Vault
2. Data is sent to MatStream via a secure REST endpoint
3. MatStream creates or updates entities using GUID-based matching — 
   so renames in Vault do not break the sync
4. Property values are mapped to MatStream properties
5. Assembly structures (parent-child relationships) are preserved

This means your Vault catalogue becomes the source of truth for CAD 
data, and MatStream becomes the source of truth for lifecycle status, 
formal revision history, and structured PLM data.

See [Syncing with Vault](../vault-integration/sync.md) for full details.

---

## A typical PLM workflow in MatStream

Part created in Inventor and checked into Vault by designer
Vault sync pushes part data into MatStream automatically
Engineer enriches the MatStream entity with PLM properties
(supplier, material grade, lead time)
Engineer submits the part for review
Quality engineer reviews and approves
Part released as revision A in MatStream
Part linked to the assembly that uses it
Six months later — supplier changes component
New revision B created in MatStream
Updated datasheet attached
Re-submitted, re-approved, re-released as revision B
All assemblies using the part are visible via where-used


---

## Who uses MatStream in a PLM context?

| Role | How they use MatStream |
|---|---|
| **Design engineer** | Creates entities, fills in properties, attaches drawings |
| **Senior engineer** | Submits parts for review, manages BOM links |
| **Quality engineer** | Reviews and approves lifecycle transitions |
| **Procurement** | Views released parts, supplier data, and lead times |
| **Management** | Views released product data and approval status |
| **Administrator** | Configures categories, lifecycles, and numbering schemes |

---

## Getting started with PLM in MatStream

1. **Configure categories** — define your part and assembly types with 
   the properties your process requires. See [Categories](../admin/categories.md).
2. **Configure lifecycle definitions** — define your approval states 
   and transitions. See [Lifecycle](../entities/lifecycle.md).
3. **Configure numbering schemes** — define your part numbering 
   templates. See [Numbering schemes](../admin/numbering.md).
4. **Set up user groups** — define who can create, review, and approve. 
   See [Users & groups](../admin/users-groups.md).
5. **Connect Vault** — if you use Autodesk Vault, set up the sync 
   pipeline. See [Syncing with Vault](../vault-integration/sync.md).
6. **Create your first part** — follow the [Quick start](../getting-started/quick-start.md) 
   guide for a step-by-step walkthrough.

---

## Next steps

- [Document Control](document-control.md) — manage controlled documents 
  alongside your engineering data
- [Quality Management](quality-management.md) — link quality records 
  to specific part revisions
- [Core concepts](../platform/core-concepts.md) — the building blocks 
  of the platform