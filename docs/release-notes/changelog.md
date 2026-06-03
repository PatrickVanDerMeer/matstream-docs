# Changelog

## June 2026 — Current build

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
