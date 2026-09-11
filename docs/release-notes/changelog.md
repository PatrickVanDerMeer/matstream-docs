# Changelog

## September 2026 — Current build

### New features

**Workspace backup and restore**
MatStream Manage can now stream a complete workspace to a versioned
`.msworkspace` package and restore it into a selected workspace. Packages carry
workspace-owned tenant data and referenced files, verify file checksums, and
remap identifiers when restored to another workspace or tenant server. The new
**Backup and Restore** tab also keeps a durable history of completed and failed
operations. See [Backup and restore](../admin/backup-restore.md).

**Time tracking**
Hours are entered on a weekly grid, submitted as a week, and approved by
somebody else — nobody can approve their own timesheet, whatever permissions
they hold. Approved entries are never edited: a correction is a new entry
carrying negative hours and pointing at the one it reverses. Entering your own
hours needs no permission beyond an Editor seat; everything about somebody
else's time is gated. See [Time tracking](../features/time-tracking.md).

**Hour rates, and hours that cost something**
An Hour Rate is a Library item saying what one hour costs and sells for from a
given date. Changing a rate means saving a **new version** with a new
*Effective From* — the card's version history is the rate history. When a week
is approved each entry is priced from the version that applied on the day it
was worked, so a week straddling a rise costs two different amounts, and the
figures are copied onto the entry so a later edit to the card cannot restate
them. Where no rate applies the hours are approved with **no cost** rather than
a cost of zero.

**Opportunities and quotations**
Company → Opportunity → Quotation → Project, as ordinary entities. Opportunities
carry value, probability and a calculated weighted revenue, and move through a
pipeline that lets you record a loss from any live stage and step backwards when
a deal does. Quotations are numbered, have priced lines, and are **revised rather
than edited** once issued — the customer is holding a copy. An accepted quotation
becomes a project carrying the client, currency and contract value, and recording
which *version* of the quotation it was sold on. See
[Opportunities & quotations](../features/opportunities-quotations.md).

**Calculated properties**
A property whose value is arithmetic over the other properties of the same item
— `[Quantity] * [Unit Price]`, `[Estimated Value] * [Probability %] / 100`. The
result is stored, so it sorts, filters, exports and prints like any other number.
A missing input gives a **blank rather than a zero**. See
[Properties](../entities/properties.md).

**Activities and a timeline**
Calls, emails, meetings, site visits and notes can be logged against
opportunities, companies and projects, dated by when they *happened* rather than
when they were typed. The timeline merges those with the events the system
recorded — state changes, timesheet decisions, file check-ins — newest first.

**Project fields and portfolio views**
Projects gain a Project Manager, start and due dates, priority, type, currency,
contracted revenue, progress and a forecast comment, with **Project Portfolio**
and **Active Projects** views over them. Companies gain tabs showing their
opportunities, quotations, projects and purchase orders.

**Assigned to me**
The dashboard's task widget showed everyone's most recently changed tasks under
a "Tasks to do" heading. It now shows the tasks assigned to you, with overdue
ones marked.

### Changes

**Property permissions are enforced**
Property-level permissions were configurable but had no effect. They are now
applied wherever property values are read or written, including exports and
print templates. A restricted property is **redacted rather than hidden**, so a
grid keeps its shape.

**Items under approval, or in a locked state, refuse edits everywhere**
The rule that a lifecycle state can forbid editing was applied on one save path
only, so a released drawing could not be renamed but could have its whole bill
of materials rewritten. It now applies to structure and quantity edits, line
removal, renaming, category changes, both property paths, file upload,
check-in, file removal and check-out.

**Saved views that filtered on "not equals" matched everything**
Several seeded document-control views exclude closed or cancelled items. That
operator was never implemented, so those rules were ignored and the views showed
everything. Fixed, along with eight other missing filter operators — and an
unrecognised operator is now refused instead of silently matching every row.

**Lifecycle criteria that check a property value now work**
Property-based transition criteria could be configured and were never
evaluated. They are evaluated now, and a criterion that could never be satisfied
is refused when it is saved rather than sitting there looking enforced.

**A cleaner background scheduler**
Scheduled jobs now hold a lease and remember their own state, so two instances
of the API no longer each run every job, and a job that has been failing keeps
backing off instead of restarting at its shortest retry after a recycle.

### Not yet reachable

Some of this release's work is in place but has no screen yet, and is listed
here so it is not mistaken for missing:

- **Notification rules** exist, with delivery on the scheduler, but can only be
  created directly in the database — there is no administration screen.
- **Approval requests** are recorded and locked correctly, but nothing raises
  one yet.
- **Quotation → project conversion**, the **activity timeline**, the
  **bill-of-materials cost roll-up** and the **back-dated rate-change report**
  are available through the API only.
- **Quotations cannot be printed**: the field mapping is ready and the Word
  template is not.

---

## August 2026

### New features

**Purchase orders, receiving and cancellation**
Selected material requirements now become purchase orders — **one order per 
vendor** — with an option to merge identical items into a single order line 
(quantities summed, earliest date kept, coverage still tracked per 
requirement). Receiving posts to the stock ledger against a chosen location 
and settles the ordered quantity on the originating requirement. Cancelling 
keeps the order — it may already have been sent — and returns its uncovered 
requirements to *Requested*. Requirements with no vendor anywhere in the 
line → source → definition chain are reported and skipped instead of blocking 
the rest of the order. See [Procurement](../features/procurement.md).

**Required vendor documents**
A library item can declare the documents its vendor must supply — datasheets, 
material certificates, ITPs, FAT reports, declarations of conformity. Each 
project occurrence receives its own document rows from those templates, with 
their own *Required → Submitted → Accepted* lifecycle, so vendor paperwork is 
chased per project rather than per catalogue item.

**Edit a property across many rows**
Selecting several entities makes the Property Browser aggregate their values: 
a shared value shows as itself, a mixed one as `<varies>`. Setting a value 
writes it to every selected entity in one operation and reports how many were 
written and which were skipped, with the reason. Works across categories, on 
the properties the selection has in common. See 
[Property Browser](../features/property-browser.md).

**PDF content in the search index**
Text is now extracted from PDF attachments and indexed alongside entity data, 
so a search for a tag number or a note *inside* a drawing returns the drawing. 
CAD-plotted PDFs are read word by word rather than glyph by glyph — without 
that, plotted title blocks index as `H a l f  C o u p l i n g` and are 
effectively unsearchable. File content sits in its own field so identifier 
matching keeps its own behaviour.

**Files are stored once, by content**
Uploads are hashed with SHA-256 and matched against the workspace's existing 
files on hash and byte length. Identical content is stored once however many 
entities, versions or revisions reference it, and the duplicate is discarded 
before it reaches permanent storage. A background sweep reclaims content no 
version references any more.

**Version history: Details, Restore and Purge**
The History tab gained three row actions. **Details** opens a past version 
read-only; **Restore** brings its content forward as a new version at the tip 
(never editing in place, so the intervening history survives); **Purge** 
permanently removes a historic version, refused with a stated reason when it 
is the current version, the last remaining version, or pinned by a parent.

**Workspace defaults for new users**
The gear in any column chooser now offers **Save as initial default (new 
users)** and **Reset to default settings**. Grid state resolves in three 
tiers — your own layout, then the workspace default, then the built-in 
fallback — so changing the default does not disturb colleagues who have 
already customised the view. See 
[Grids, columns & defaults](../features/grid-defaults.md).

**Category visibility per folder category**
Object Visibility in the Explorer ribbon is now scoped to the folder category 
you are standing in, the same way column layouts already were, and is 
seedable as a workspace default. A Project folder can hide Material 
Requirement lines while a buyer's folder shows nothing else.

**Filter snapshots**
A filter's membership can be frozen at a point in time by attaching the 
**Capture Filter Snapshot** automation to a lifecycle transition, which pins 
the membership to the released revision. Filters also gained a self-anchoring 
**Parent subtree** scope, so one filter category can be dropped into any 
project and scope itself to that project with nothing to configure per 
instance. See [Filter Items & Snapshots](../features/filter-items.md).

**Lifecycle criteria are now evaluated**
Transition criteria were configurable but never enforced. They are now 
evaluated on every transition, graded as **error** (blocks) or **warning** 
(reports and lets the user proceed), and include predefined checks such as 
*the entity has a stored file* and *all linked items are released*.

### Changes

**Setup Wizard reorganised**
The wizard now offers two templates — **Engineering Project** and 
**Integrations** — instead of the previous Explorer / Library / Integrations 
groups. Each template's options appear directly in the tree and can be 
checked on their own, with a description of the selected item in the panel 
on the right. **Engineering Project Sample Data** is now a separate option, 
checked by default, so the sample project can be left out without giving up 
the configuration.

**User groups seeded with the Engineering Project**
Applying the Engineering Project configuration now creates **Engineering**, 
**Purchasing**, **Production** and **Document Control** groups, ready to be 
granted lifecycle transition permissions. The **Administrators** group is 
unchanged — it is still created with the workspace itself, whether or not the 
wizard is ever run.

**Setup progress and summary**
The wizard shows a progress bar while it runs, and the summary reports how 
many items each part of the setup created. Items that already exist advance 
the bar but stay out of the log, so the log shows only what was actually 
added.

**Faster setup**
A full setup with sample data now completes in around 45 seconds, roughly a 
third faster than before, after removing duplicated work from the seeding 
sequence.

**Document Control temporarily unavailable in the wizard**
The Document Control option has been withdrawn while it is folded into the 
Engineering Project configuration. Workspaces that already have controlled 
documents are unaffected.

## July 2026

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
