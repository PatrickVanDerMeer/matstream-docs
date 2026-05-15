# Explorer

The Explorer is a hierarchical tree browser for navigating and managing entities in a structured, folder-based layout. It complements the Library, which is a flat search-based surface — the Explorer is for structure-first navigation: assemblies, BOMs, projects, stock locations, and any other hierarchy your workspace uses.

---

## Layout

The Explorer page (`/w/{workspaceSlug}/explorer`) is a three-pane splitter:

| Pane | Component | Purpose |
|---|---|---|
| Left (20%) | `FolderBrowser` | Folder tree — navigate workspace folders |
| Centre (60%) | `ExplorerTreeGridBrowser` | Entity tree grid — shows entities in the selected folder |
| Right (remaining) | `PropertyBrowser` | Property detail — shows properties of the selected entity |

All three panes are resizable by dragging their splitter borders. Pane sizes are not currently persisted across sessions.

---

## Folder Browser (left pane)

The folder browser shows the workspace folder tree. Folders are entities of the Folder family type — they are stored in `tbl_Entity` / `tbl_EntityVer` like any other entity.

### Behaviour

- Folders load lazily on expand — root children load on first open.
- The last selected folder is persisted per workspace user (`WorkspaceUserSettingKeys.LastFolderId`) and restored on next open via `RestoreLastFolderAsync`.
- Selecting a folder updates the centre pane immediately.
- `FullRefreshAsync` reloads all currently visible (expanded) branches without collapsing the tree, then calls `RestoreLastFolderAsync` to reselect the last folder.

### Context menu

Right-clicking a folder node opens a context menu with:

| Command | Action |
|---|---|
| New Folder... | Prompts for a name and creates a child folder |
| Add Files... | Opens a file upload dialog to attach files to the folder |
| Rename | Inline rename prompt |
| Move To... | Opens `MoveToFolderDialog` |
| Delete | Deletes the folder (with confirmation) |
| Change Category... | Reassigns the folder entity to a different category |
| Details... | Opens the entity edit dialog for full property editing |

### Move To folder dialog

`MoveToFolderDialog` opens when the user selects **Move To...** from the folder context menu.

**Behaviour:**
- Displays the full folder tree in a 420px modal dialog.
- The folder being moved is shown greyed-out with an "(current)" label — it cannot be selected as its own target.
- Descendants of the folder being moved are also blocked as targets (preventing circular nesting).
- Moving a folder to its existing parent is blocked.
- The **Move Here** button is enabled only when a valid target is selected.
- On success, the `OnMoved` callback fires with the updated `FolderNodeDto` and the tree refreshes.

**API call:** `POST /api/wid/{workspaceSlug}/folders/move` with `{ Id, NewParentId }`.

---

## Explorer Tree Grid (centre pane)

`ExplorerTreeGridBrowser` displays the entities within the selected folder as a lazy-loading tree grid. It uses `EntityTreeGridLoadOnDemand`.

### Ribbon

The Explorer ribbon (simplified layout) contains:

| Group | Actions |
|---|---|
| New | Split button: pick a template category (Occurrence or File family), then opens `EntityDefinitionEditDialog` to fill in details |
| Refresh | Reloads the current folder contents |
| Tools | Unit Converter |

### Columns

Default columns: Icon, Name, Description, State. Additional columns can be toggled via the column chooser. Conditional formatting rules highlight lifecycle states: Released (green), In Work (amber), Obsolete (red).

### Show Folders toggle

A toggle controls whether folder entities appear inline in the entity grid alongside regular entities. The preference is persisted per user via `WorkspaceUserSettingKeys.ExplorerShowFolders`.

### Child expansion suppression

For entity types that represent order lines (e.g. Sales Order Lines, Purchase Order Lines), child expansion is suppressed in the Explorer grid — the tree does not attempt to expand these nodes into sub-items. This is controlled by the `SuppressChildExpansion` parameter passed to the SQL query, resolved dynamically by checking whether the parent entity's category matches known order-line category names rather than hardcoding IDs.

### Linking entities

Entities can be linked to definitions (Library entities) via drag-and-drop or the **Link Definition** context action, which opens `EntityDefinitionSearchDialog`. If a version conflict is detected on link, `DefinitionLinkOverrideDialog` offers resolution options.

### Selection

When a row is selected, `SelectedEntityChanged` fires and the right-hand `PropertyBrowser` updates to show that entity's properties. Selection is fire-and-forget (lazy) to avoid blocking UI responsiveness.

Double-clicking a row opens the entity edit dialog. Double-click works even if the row was not previously selected — the event reads from `args.RowData` directly, not from the cached `_selected` field.

---

## Property Browser (right pane)

`PropertyBrowser` shows structured property data for the currently selected entity. It has two tabs: **Occurrence** (occurrence-level properties) and **Definition** (definition-level properties).

### Layout

- A mini-ribbon below the tab header contains an **Edit** (pencil) button. The ribbon height matches the Explorer header for visual alignment across all three panes.
- Properties are displayed in a resizable two-column grid (Name / Value). Column widths can be adjusted by dragging the column separator.
- Properties are grouped into accordion sections, one per property group as configured in the workspace.

### Behaviour

- Changing the selected entity in the centre pane triggers `@key="property-browser"` keyed re-render if needed, ensuring stale data is not displayed.
- The expanded/collapsed state of accordion sections is persisted per user via `WorkspaceUserSettingKeys.ExplorerPropertyBrowserAccordion`.
- The Edit button opens the full entity edit dialog for the currently displayed entity.

---

## Entity placeholder support

Entities of the File family type support an `IsPlaceholder` flag (`EV_IsPlaceholder` in `tbl_EntityVer`). A placeholder entity has no attached file — it acts as a structural reservation in the Explorer tree.

Visual indicators in the Explorer grid:
- Placeholder entity (intentional): placeholder icon
- File entity with no file attached (unintentional): warning icon

The placeholder checkbox is only visible in the edit dialog for File family entities.

---

## Keyboard and UX notes

- The folder tree restores the last selected folder automatically on page load.
- Renaming a folder preserves its position in the tree without full reload.
- Moving a folder via `MoveToFolderDialog` triggers a `FullRefreshAsync` on the folder tree after success to reflect the new position.
- The search overlay in the Explorer ribbon applies quick text filtering to the entity grid.

---

## Related

- [Library](library.md) — flat search surface for entities
- [Folders API](../api/folders.md) — REST endpoints for folder operations
- [Categories](../admin/categories.md) — configure entity types shown in the Explorer
- [Entity placeholder](../entities/placeholder.md) — file placeholder entities