# Changelog

## July 2026 — Current build

### New features

**Material Requirements**
A new **Material Requirement** category behaviour, distinct from Stock Line. 
A requirement records demand — required quantity and required-by date stored 
on the line — and posts nothing to the stock ledger until a buyer reserves 
stock or raises a purchase order against it. Requirements track *Reserved*, 
*Ordered* and *Open* quantities so partial coverage from stock and from 
purchasing can be seen on one line. Previously requirements shared the Stock 
Line behaviour, which made stock appear to be on order as soon as an engineer 
flagged an item.

**Raise requirements automatically from the Explorer**
The **Create Material Requirement** automation can be attached to a lifecycle 
transition, so moving an item to *Ready for Procurement* raises a requirement 
line against the owning project, linked back to the item that caused it. The 
project is found by walking up the folder tree, so one configuration serves 
every project.

**Automation settings dialog**
Built-in automations that need configuring now show a **gear** next to their 
name in the transition's Automations tab, opening a settings dialog with 
proper pickers for categories, folders, lifecycles and groups. A warning 
triangle marks an automation whose required settings are still missing. 
Previously these automations could be switched on but not configured, so they 
silently did nothing.

**Transition security seeded by the wizard**
The Setup Wizard now creates **Senior Engineers** and **Junior Engineers** 
groups and configures per-transition permissions on the Engineering Lifecycle, 
giving a working example of role-based release control out of the box.

**Engineering Lifecycle**
Part, Assembly, Weld Assembly, Instrument, Equipment and WPS now receive an 
Engineering Lifecycle (Work in Progress → For Review → Approved → Released → 
Obsolete). Previously these categories had no lifecycle at all and Change 
State reported "no lifecycle" on every part.

**BOM Folder**
The Setup Wizard adds a **BOM Folder** category and places a project's BOM in 
its own folder, so BOM columns are remembered separately from the project 
folder's. Administrators can seed default columns for a folder category.

### Changes

**Lifecycle naming**
All seeded lifecycles now end in " Lifecycle" — `Engineering Lifecycle`, 
`Procurement Lifecycle`, `Purchase Order Lifecycle` and so on. Most previously 
shared a name with a category, which made the Explorer's Lifecycle and Occ. 
State columns ambiguous.

**Category grouping**
Categories can be nested under a parent purely for organisation — for example 
Part, Assembly and Equipment under **Item**. Grouping is presentation only: 
there is no inheritance, and a parent must share its children's family type. 
The obsolete "is group" flag has been removed; use **Show in New dialog** to 
hide a category from the create pickers.

### Fixes

**Lifecycle column was always blank in the Explorer**
The Explorer grid never fetched the lifecycle name, so the Lifecycle column 
showed nothing on every row. Occ. State was unaffected.

**Purchase-order stock was never updated**
The *PO: Add to On Order* and *PO: Release from On Order* automations resolved 
the linked product incorrectly and exited before doing anything, so ordering 
never moved the On Order figure. Both now work.

**Numbering tokens were case-sensitive**
`{YYYY}` was left in the output as literal text instead of being replaced with 
the year, producing numbers like `PO-{YYY.Y}0001`. Tokens are now 
case-insensitive. Unrecognised tokens are still printed literally — check the 
Preview column after editing a template.

**Column layouts stored every column**
Saved Explorer layouts included every hidden column in the workspace, not just 
the visible ones, and included a malformed entry for the system property 
group. Layouts now store only what is shown.

**Stock lines had no link to their item**
Requirement and order lines were not linked to the definition they were for, 
so item name, description and part number appeared blank in list views.

**Setup Wizard**
Configuration templates can now be applied from a guided wizard in 
MatStream Manage instead of building categories by hand. The Explorer → 
Projects template seeds a Project folder category (lifecycle, numbering 
scheme, core properties) plus People, Documents, Tasks, and Milestones 
tabs in one step, with an option to also seed sample project data. Tree 
items show an explanation panel on the right when selected, and 
selecting a sample-data option no longer forces its parent item on or off.

**Milestones**
A new checkpoint concept for Projects — a dated event ("Design Freeze", 
"Delivery") with a responsible owner and its own status lifecycle 
(Planned → At Risk → Reached / Missed). Created from the **New 
Milestone** button on a Project's Milestones tab.

**Numbering schemes — Insert variable helper**
The Template field in the numbering scheme editor now has an Insert 
variable button listing the supported tokens (`{yyyy}`, `{week}`, and 
the zero-padded sequence counter), so templates can be built without 
memorizing the exact syntax. A separate Number Format mask can reformat 
the generated number with separators (e.g. `PR2026000001` → `PR.2026.000001`).

**Ribbon toolbars**
Entity tab toolbars (starting with Uses and General) are being migrated 
to a compact ribbon control, giving each tab room to grow additional 
actions (e.g. barcode scanning, change-state) without cluttering a flat 
button row.

### Fixes

- Fixed: new users occasionally landed on "You're not part of a 
  workspace yet" immediately after being added to one, even though 
  their membership was valid.
- Fixed: deleting a workspace now fully removes its data — folders, 
  entities, files, and search index — instead of sometimes leaving 
  orphaned records behind.
- Fixed: the "Create a workspace" link on the sign-in screen now opens 
  the correct page in MatStream Manage.

---

## June 2026

### New features

**AI Assistant (MatStream AI)**
A context-aware chat panel accessible from any page. The assistant knows which feature area you are in, answers questions about MatStream, and cites source documentation for each answer. Backed by retrieval-augmented generation on the workspace knowledge base.

**Dashboard**
The workspace home page now shows four widgets: Favourites (starred entities), Tasks (outstanding work items), KPIs (configurable metrics), and Activity (recent changes and lifecycle transitions).

**Stock Management**
Track on-hand inventory quantities against item–location pairs. Features include stock positions (queryable by item or location), stock adjustments (positive or negative quantity with a note), stock lines for purchase and sales order quantities, and a full movement ledger for traceability.

**Automations**
Define scripted event-driven actions that run when things happen in MatStream (e.g. entity state changes, imports). Scripts are authored in a Monaco editor with an inline API reference panel and a test runner. System automations are provided by MatStream; workspace automations are fully customisable.

**Print Templates**
Generate professional PDF or Excel documents from entity data without leaving MatStream. Administrators upload Word (.docx) or Excel (.xlsx) template files, assign them to categories, and users click Print in the entity toolbar to download a filled document instantly.

**Display Tab: Ledger**
A new Ledger display tab type shows the stock movement history for inventory entities, including current on-hand balance, reserved, and on-order quantities alongside a paginated movement log.

**Display Tab: History**
A History display tab shows the full revision history of an entity — all past versions with their dates and authors.

**Explorer: Folder management**
Folders are now first-class entities with a full context menu: New Folder, Add Files, Rename, Move To, Delete, Change Category, and Details. The Move To dialog prevents circular nesting.

**Explorer: Property browser**
A third pane in the Explorer shows property data for the selected entity. Properties are grouped into accordion sections; section state is persisted per user. An Edit button opens the full entity dialog.

**Explorer: File placeholders**
File-family entities can be marked as placeholders — structural reservations in the Explorer tree that don't yet have an attached file.

**Unit Converter**
An in-app unit conversion tool accessible from the Explorer ribbon. Converts values across all dimensions configured in the workspace (Length, Mass, Pressure, etc.) with the last active dimension tab persisted per user.

**Collaborate (scaffolded)**
A Collaborate page providing a tree view of Clients, Suppliers, and Third Parties. Full implementation in progress.

**Onboarding flow**
New workspaces and users are guided through a structured onboarding flow on first access.

**MatStreamConnect — File check-out / check-in**
MatStreamConnect now registers the `matstream://` URI scheme. Clicking an Open link in the browser downloads the file, locks it on the server, and opens it with the Windows default application. When the file is closed, MatStreamConnect prompts to check it back in. Manual check-in and undo-checkout are available from the tray menu.

**Import Definitions — Category assignment rules**
Import definitions now support category assignment rules: logic evaluated per BOM row that assigns different categories based on source column values, with operators Eq, NotEq, Contains, Regex, and IsEmpty.

**Lucene full-text search**
The Library and Explorer now use an Apache Lucene.NET index for entity search. The `MatStreamAnalyzer` normalises separators (dots, dashes, underscores, spaces) and generates edge n-grams for instant prefix matching. Case-insensitive. The index rebuilds automatically on entity create/update; admins can trigger a manual rebuild from workspace settings.

---

## April 2026

### New features

- Initial public build
- Workspaces and multi-tenancy
- Entity management with versioning, categories, and properties
- Lifecycle state machines with configurable transitions and permissions
- Revision and numbering schemes
- Entity links (Uses / Where-Used), BOM tree
- Display tab configuration (General, Uses, Where-Used)
- File attachments with PDF viewer
- Import and sync pipeline (Excel import)
- Autodesk Vault integration (upsert pipeline)
- Units of Measure (dimensions, units, UoQ)
- Access control (role capabilities, lifecycle-state permissions, entity-level grants)
- Background jobs
- Tasks
- Text Templates
- API keys
- Billing and subscriptions (Free, Team, Professional, Enterprise plans)
