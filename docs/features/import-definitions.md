# Import Definitions

Import Definitions let you map structured external data — typically Excel spreadsheets, or metadata and parts lists published from CAD tools — into MatStream. Each definition is a reusable **profile** that says what kind of data is being read, how to interpret it, and where to put it.

---

## Overview

A profile has two tabs, **Profile** and **Configuration**:

1. **Profile type** — one of five types (see below) that decides what a row or file becomes.
2. **Category** — the category new entities are created in, or **Auto**, which lets the [category assignment rules](#category-assignment-rules) decide.
3. **Match keys** — the columns that identify an existing record. A row that matches is updated; a row that matches nothing is created.
4. **Column or field mappings** — which source columns are written to which properties. Only mapped columns are written; properties without a column are left untouched on an existing record.

---

## Profile types

| Profile type | Source | What one row or file becomes |
|---|---|---|
| **Main Item (metadata)** | A metadata JSON file, typically from an iLogic rule | One parent entity, such as a drawing. Files are attached by the integration afterwards. |
| **Parts List (children of a parent)** | An Excel parts list plus the parent's metadata | BOM rows linked as children of a parent entity that is located by its key. |
| **Library Items (flat Excel)** | An Excel sheet | One Library item per row — parts, assemblies, equipment. |
| **Structure (folders & occurrences)** | An Excel sheet | One node of the Explorer tree per row, placed by its path. |
| **Stock on Hand (Excel)** | An Excel sheet | The quantity of one item at one stock location. |

Profiles created before the current names existed may show older names in documents and logs: `File` and `MainItem` for the main-item type (formerly `DocumentWithBOM` and `ItemList`), and `ChildrenFromExcel` for the parts-list type. Existing profiles keep working.

### Main Item (metadata)

Publishes one parent entity from a metadata file.

- **Main item category** — the category of the parent. **Auto** applies the category assignment rules.
- **Upload metadata template** — choose a `.json` file with the same keys your integration will send. MatStream reads the key names so that the dropdowns in the mapping grids list them.
- **Search keys** — how an existing parent is found, so that publishing again updates it.
- **Field mappings** — which metadata key is written to which property.

### Parts List (children of a parent)

Links the rows of an Excel parts list as children of an existing parent. It has two tabs:

- **Parent Item** — the search key that locates the parent from the drawing's metadata. The drawing must be published first. If no matching parent is found, the parts list is not imported and the reason is logged.
- **Parts List (BOM)** — the **Default child category** (used for new items that do not exist yet; existing items keep their category), the **Header row** and **Start row**, an **Upload BOM template** (detects the Excel columns), and the **Search keys** and **Field mappings** for the child rows.

See [Inventor BOM import](../integrations/inventor-bom.md) for the full Inventor set-up.

### Library Items (flat Excel)

Use this to load or refresh the Library from a spreadsheet.

- **Sample Excel file** — pick a `.xlsx` or `.xlsm` file so MatStream can read its column names. The sample is stored with the profile, so the mapping dropdowns are already filled when you reopen it. The download button next to the file box saves the stored sample as a template.
- **Header row** and **First data row** — where the column names and the data start (1-based).
- **Library item category** — new rows are created here; existing items keep their own category. **Auto** applies the category rules.
- **Match keys** — how a row is matched to an existing item. A row that matches nothing is inserted. A row that matches more than one item is reported as ambiguous and skipped.
- **Column mappings** — only mapped columns are written.

### Structure (folders & occurrences)

Builds a branch of the Explorer tree from a sheet — for example a warehouse, its racks and its shelves.

- **Default node category** — used for a row that has no Category column. On **Auto**, the folder rules decide, exactly as the **New Folder** dialog does.
- Each row is one node. Paths are relative to the workspace root and may use `\` or `/`, for example `Warehouse\Rack 01\01-A`.
- A row deeper than its parents creates the levels above it. List each level as its own row, parents first, when you want to say what each level is.
- Re-importing the same sheet changes nothing: a path is matched, not inserted twice.
- **Match key** — map the **Folder Path** column and mark it as the key.
- **Column mappings** — the node's own properties, plus an optional **Category (by name)** column that names what each row is (Warehouse, Rack, Storage Location). The node's name is the last part of its path and is not mapped separately.

### Stock on Hand (Excel)

Sets the on-hand quantity of items at stock locations from a count sheet.

- **Item category** — narrows what the key columns may match. On **Auto**, the whole Library is searched. Only Definition categories qualify, because quantities are held against Library items.
- **Match keys** — a row is identified by the item **and** the shelf, so mark both as keys: the item's name (or a key property such as Part Number) and the **Location Path**.
- **Column mappings** — map **On Hand Qty**. It is the only value this import writes. A blank quantity cell is an error, not a zero.
- The quantity is a count, not a movement. The import writes the difference, so running an unchanged sheet again reports everything as unchanged instead of doubling your stock. Each real difference leaves one **Adjustment** in the ledger. See [Stock management](stock.md).
- Items and locations must already exist. Nothing here creates either, and stock sits only on a leaf location, never on the rack above it.

---

## Configuration screen

Profiles are maintained under **Configuration → Import Definitions**. The **Data Import Manager** page (`/w/{workspaceSlug}/data-import-manager`) lists the same profiles read-only and offers **Run import**.

### Profile tab

| Field | Description |
|---|---|
| Name | Human-readable name. Integrations such as MatStreamConnect select the profile by this name. |
| Description | Optional notes |
| Profile type | One of the five types above. The text under the dropdown describes the selected type. |
| Status | *This profile is enabled.* A disabled profile cannot be run and is skipped by MatStreamConnect. |
| Project metadata key | The metadata key whose value is matched to the Project Number when publishing. |

### Configuration tab

The contents depend on the profile type; see [Profile types](#profile-types). Every type uses the same two mapping grids:

**Search / match keys** — the columns that identify an existing record. Each key row has a **Key group**: **1 (primary)**, **2 (fallback 1)** or **3 (fallback 2)**. MatStream tries the primary key first and uses a fallback group only when the primary one finds nothing.

**Mappings** — each row maps a source column to a MatStream property:

| Column | Description |
|---|---|
| Excel column / metadata key | The source column, chosen from the detected list |
| MatStream property | The property that receives the value. For a Library Items profile, map a column to the **Name** field; a row without a resolvable Name is reported as an error. |
| Use for versioning | Include this column when deciding whether a change is significant enough to create a new version. |
| Clear when empty | Off (default): an empty cell leaves the current value alone. On: the sheet is authoritative for the field, and an empty cell removes the value. |

Multiple mappings of the same source column are allowed — for example, one column feeding two properties.

### Category assignment rules

Wherever a profile has a category picker with an **Auto (use category rules)** option, the **gear** button next to it opens **Category Assignment Rules**. Rules are shared by the workspace and are not tied to one profile.

Each rule has a name, a target category and a set of criteria built with a query builder (conditions combined with *and* / *or*). Use **New**, **Copy** and **Delete** in the dialog's toolbar; the header shows *Unsaved changes* or *Saved*. When a row is imported on **Auto**, the first matching rule decides its category.

**Example:** purchased parts (column "Type" = "Purchased") go to the Purchased Part category, make items (column "Type" = "Make") go to the Manufactured Part category, and everything else falls back to the default child category.

---

## Upsert behaviour

When an import runs:

1. For each row, the match keys are used to search for an existing record in MatStream.
2. If found → the mapped properties are updated in place. A row whose values already match is reported as **unchanged**.
3. If not found → a new record is created, with a generated number from the category's numbering scheme, and the mapped properties.

Records that exist in MatStream but are not present in the import file are **not deleted** — imports are additive only.

---

## Inventor Drawing BOM import (via MatStreamConnect)

Drawings and their parts lists can be published straight from Autodesk Inventor: an iLogic rule writes the drawing's iProperties to a metadata file, exports the PDF and the parts list, and calls the **MatStreamConnect** desktop app, which runs the matching Import Definitions:

```
Inventor (iLogic rule)
  → %TEMP%\<drawing>_meta.json  (iProperties, keys = field-mapping source columns)
  → %TEMP%\<drawing>.pdf        (drawing PDF)
  → %TEMP%\<drawing>_BOM.xlsx   (parts list)
  → MatStreamConnect.exe --publish-drawing  --profile "Inventor Main Item" --metadata … --file <pdf>
  → MatStreamConnect.exe --publish-children --profile "Inventor Parts List" --metadata … --bom  <xlsx>
      → MatStream creates/updates the drawing entity, attaches the PDF, upserts the BOM rows
```

Two definitions are involved: a **Main Item** profile for the drawing (`--profile "Inventor Main Item"`) and a **Parts List** profile for its rows (`--profile "Inventor Parts List"`). The `--profile` names must match the definitions' **Name** fields exactly.

The rules, the argument reference and the definition settings are in [Inventor BOM import](../integrations/inventor-bom.md); installation and setup of the desktop app in [MatStreamConnect](../integrations/matstream-connect.md).

---

## Running an import

Imports can be triggered:
- **Manually** — in the **Data Import Manager**, select a profile and click **Run import**, then follow the steps below.
- **Programmatically** via the REST API: `POST /api/wid/{workspaceSlug}/imports/{profileName}/run`.
- **From MatStreamConnect** (triggered by iLogic or other automation).

### The Run import dialog

An Excel run is checked first and written second, so nothing changes until you confirm.

1. **Pick the file.** Choose a `.xlsx` or `.xlsm` file. The dialog shows which row it reads as the header and which as the first data row. Rows hidden in the sheet, including rows hidden by a filter, are **not** imported; a warning says how many were skipped. To import everything, clear the filter in Excel and pick the file again.
2. **Check it (Next).** MatStream validates every row and writes nothing. The summary badges show the total rows and how many are **new**, **to update**, **unchanged**, **blocked** and **problems**. The grid lists each row with its result, name, key and a detail message; **Only problems** filters it down. Blocked and problem rows are left alone when you continue.
3. **Import.** Writes the new and updated rows in slices, with a progress bar, elapsed time and an estimate of the time left. **Stop** halts the run between slices; slices already written stay written. After the commit the grid shows what was actually written, and the footer shows the batch number.

After a run, an `ImportBatch` record is created with counters: `InsertCount`, `UpdateCount`, `SkipCount`, `ErrorCount`, and a `Notes` field with row-level error details.

---

## Permissions

Import configuration and execution are deliberately separate:

| Grant | Effect |
|---|---|
| `import.definitions.manage` | Open and maintain import definitions, mappings, criteria, and profiles |
| `import.run` | Stage, validate, commit, publish, and inspect import batches |

Someone may therefore run an approved profile without being able to redesign it. Imports also need
the entity and file capabilities used by that definition, such as `entity.create`, `entity.update`,
or `file.upload`.

MatStreamConnect and CAD/iLogic-triggered imports run under the signed-in user's token. That user
must hold `import.run` and the applicable entity/file grants; an integration does not bypass the
workspace grant model.

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
