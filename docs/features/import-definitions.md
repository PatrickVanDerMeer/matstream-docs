# Import Definitions

Import Definitions let you map structured external data — typically Excel spreadsheets or BOM exports from CAD tools — into MatStream entities. Each definition is a reusable profile that specifies what to read, how to interpret it, and where to put it.

---

## Overview

An import definition configures:

1. **Source** — what kind of file is being imported (Excel, Inventor BOM via MatStreamConnect, etc.)
2. **Configuration type** — `DocumentWithBOM` (a parent entity + child BOM rows) or `ItemList` (a flat list of entities)
3. **Target category** — the MatStream category that the top-level (main) entity belongs to
4. **Default child category** — the category applied to BOM child rows that do not already exist in MatStream
5. **Field mappings** — which source columns map to which MatStream properties
6. **Category assignment rules** — logic to assign different categories to child rows based on column values
7. **Key fields** — which fields uniquely identify an entity (used for upsert matching: create if not found, update if found)

---

## Import configurations

### DocumentWithBOM

The source file contains a **main item** (e.g. a drawing or assembly) and a set of **BOM rows** (child components or line items).

- The main item is created or updated as a single entity.
- Each BOM row is created or updated as a child entity linked to the main item via `tbl_EntityLink`.
- The BOM hierarchy can be flat (all rows at depth 1) or structured (if a hierarchy column is configured).

### ItemList

The source file contains a list of independent entities. Each row becomes its own entity with no parent-child linking.

---

## Configuration screen

Import Definitions are managed under **Configuration → Import Definitions**. The editor has three tabs:

### General tab

| Field | Description |
|---|---|
| Name | Human-readable name for this profile |
| Description | Optional notes |
| Source | Identifier for the data source (e.g. "Inventor Drawing BOM") |
| Configuration | DocumentWithBOM or ItemList |
| Target category | Category for the main item |
| Default child category | Fallback category for child rows not matched by assignment rules |
| Header row index | Row number of the column header in the Excel file (1-based) |
| Data start row index | First row of data (1-based) |
| Hierarchy column | Column letter/name containing the BOM level indicator (e.g. "A", "Level") |
| Enabled | Whether this profile is active |

### Field mappings tab

Field mappings connect source columns to MatStream property definition keys. There are two sets of mappings:

**Main item mappings** — applied to the top-level entity.
**BOM row mappings** — applied to each child row entity.

Each mapping row specifies:

| Field | Description |
|---|---|
| Source column | Column name or letter in the source file |
| Target property | MatStream property definition key (e.g. `PD_Description`) |
| Is key | Whether this field is used for upsert matching |
| Default value | Value to use if the source column is empty |
| Trim / Upper / Lower | Text transformations applied before writing |
| Regex | Optional regex pattern to extract a substring from the source value |
| Replace | JSON-encoded replacement rules (find → replace) |

Multiple mappings for the same source column are allowed — for example, mapping one column to two different properties.

### Category assignment rules tab

Category assignment rules allow child rows to be assigned to different categories based on the values in source columns, rather than always using the default child category.

Rules are evaluated in `SortOrder` order. The first matching rule wins.

Each rule specifies:

| Field | Description |
|---|---|
| Source column | Column whose value is tested |
| Operator | `Eq`, `NotEq`, `Contains`, `Regex`, `IsEmpty` |
| Value | The value to test against |
| Action | Currently always `SetCategory` |
| Action value | The `CategoryID` to assign when the rule matches |
| Enabled | Whether this rule is active |
| Description | Optional human-readable note |

**Example:** A BOM import where standard purchased parts (column "Type" = "Purchased") should go to the Purchased Part category, while make items (column "Type" = "Make") should go to the Manufactured Part category, and everything else falls back to the default child category.

---

## Upsert behaviour

When an import runs:

1. For each row, the key fields are used to search for an existing entity in MatStream.
2. If found → the entity's properties are updated in-place (no new version is created unless the entity is in a released state).
3. If not found → a new entity is created with a generated number (using the category's numbering scheme) and the mapped properties.

Entities that exist in MatStream but are not present in the import file are **not deleted** — imports are additive only.

---

## Inventor Drawing BOM import (via MatStreamConnect)

MatStream supports importing a drawing's BOM directly from Autodesk Inventor using an iLogic rule and the **MatStreamConnect** desktop companion app.

### Flow

```
Inventor (iLogic rule)
  → Exports BOM to Excel in %TEMP%
  → Reads iProperties (Part Number, Description, Revision, Author, etc.)
  → Launches MatStreamConnect via process call
      → MatStreamConnect reads the Excel + metadata
      → Calls POST /api/wid/{workspaceSlug}/imports/{profileName}/run
      → Attaches the drawing PDF (if configured)
      → MatStream creates/updates the main entity + BOM children
```

### iLogic rule

The iLogic rule in Inventor:
1. Checks that MatStreamConnect is installed.
2. Reads iProperties from the active drawing document.
3. Exports the BOM from the active drawing to a temporary Excel file.
4. Builds a metadata JSON string from the iProperties.
5. Calls MatStreamConnect with the profile name, Excel path, metadata JSON, and optionally the drawing PDF path.

The profile name must match an Import Definition configured in MatStream with `Source = "Inventor Drawing BOM"` (or whichever name the iLogic rule passes).

### MatStreamConnect

MatStreamConnect is a Windows desktop companion application that:
- Runs as a system tray application.
- Can be opened by double-clicking the tray icon or via the **Open** context menu item.
- Reads its workspace connection from `%APPDATA%\MatStream\MatStreamConnect\connection.json`.
- `connection.json` stores: `WorkspaceSlug`, `WorkspaceId`, `TenantApiBaseUrl`, `CoreApiBaseUrl`, `WorkspaceName`.
- The `CoreApiBaseUrl` is preserved when the user re-authenticates (not overwritten with the default).
- Accepts command-line arguments from iLogic rules to trigger import runs without user interaction.

---

## Running an import

Imports can be triggered:
- **Manually** via the import run button in the Import Definitions UI (upload an Excel file).
- **Programmatically** via the REST API: `POST /api/wid/{workspaceSlug}/imports/{profileName}/run`.
- **From MatStreamConnect** (triggered by iLogic or other automation).

After a run, an `ImportBatch` record is created with counters: `InsertCount`, `UpdateCount`, `SkipCount`, `ErrorCount`, and a `Notes` field with row-level error details.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/wid/{slug}/imports/definitions` | List all import definitions |
| `GET` | `/api/wid/{slug}/imports/definitions/{id}` | Get one definition with maps + criteria |
| `POST` | `/api/wid/{slug}/imports/definitions` | Create a definition |
| `PUT` | `/api/wid/{slug}/imports/definitions/{id}` | Update a definition |
| `DELETE` | `/api/wid/{slug}/imports/definitions/{id}` | Delete a definition |
| `POST` | `/api/wid/{slug}/imports/{profileName}/run` | Run an import by profile name |
| `GET` | `/api/wid/{slug}/imports/batches` | List recent import batch results |

---

## Related

- [Categories](../admin/categories.md) — configure the target categories for imported entities
- [Numbering schemes](../admin/numbering.md) — schemes used when creating new entities during import
- [MatStreamConnect](../integrations/matstream-connect.md) — the desktop companion for CAD-triggered imports
- [Vault integration](../vault-integration/sync.md) — alternative import path for Autodesk Vault users