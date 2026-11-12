# Library

The Library is the flat, search-based surface for browsing and managing entities across the entire workspace catalogue. It is the primary place to find an entity when you know part of its name or number, or when you want to filter by category, lifecycle state, or property values.

---

## Accessing the Library

Navigate to the Library via the main navigation bar.

The Library entry is shown when your groups supply `library.view`. Reading its records also
requires `entity.read`; opening stored files requires `file.read`.

URL: `/w/{workspaceSlug}/library`

---

## Quick search

The search bar at the top of the Library uses the [Lucene full-text index](search.md). As you type, results update immediately using prefix matching — you do not need to type the full identifier.

**Tips:**
- Separators (dots, dashes, underscores, spaces) are normalised, so `HTC.000.001`, `HTC-000-001`, and `HTC000001` all find the same entity.
- Search is case-insensitive.
- Only properties marked **searchable** in the category definition are included in full-text results.

For detailed search behaviour, see [Search](search.md).

---

## Filtering

The filter panel (toggle with the filter icon) lets you narrow results by:

- **Category** — show only entities of one or more categories
- **Lifecycle state** — filter by current state (e.g. Draft, Released)
- **Property values** — filter on any searchable property with text, number range, or date range conditions

Filters run as SQL queries and are combined with the Lucene text search result set.

---

## Grid columns

The Library grid displays entities in a configurable table. Default columns:

| Column | Description |
|---|---|
| Icon | Category icon and colour |
| Number | Entity number (from the numbering scheme) |
| Name | Entity name / description |
| Category | Category name |
| Revision | Current revision label |
| State | Current lifecycle state |
| Modified | Last modified date |
| Parent | The current owning parent of an occurrence |
| Project | The owning project, where applicable |
| Path | The parent chain identifying where the record belongs |

Use the **column chooser** (grid toolbar) to add or remove columns. Column widths and sort order are saved per user.

### Finding occurrences

Library search can include selected occurrence categories as well as definitions. Setup enables **Company Address** and **Explorer-owned BOM Position** records. Use the category filter to narrow the results, then use **Parent**, **Project** and **Path** to distinguish occurrences with similar names. These columns describe the record's current ownership; a linked Library definition is not its owning parent.

An administrator controls which categories appear through **Configuration → Categories → General → Visible in Library search**. An occurrence category also has an **Occurrence scope in Library search** setting. See [Library visibility and occurrence scope](../admin/categories.md#library-visibility-and-occurrence-scope).

Visibility does not grant access. Entity, category and property permissions still apply, and inaccessible parent or project context is omitted. Pickers used by other workflows retain their own category filters.

### Finding sample data

Sample records created by setup have a Boolean custom property named **Sample Data**, set to **Yes**. Add it through the column chooser and filter **Sample Data = Yes** to find demonstration records. It is a regular property, with no badge or name prefix.

To filter without adding a column, open **Advanced filters**, select **Sample Data**, keep
**Equals**, select **True** (the Boolean equivalent of Yes), and click **Search**. Clear any
quick-search text first if you want sample records across the whole Library.

Review the filtered results before deleting them, particularly records linked into a real project. Normal deletion permissions and reference checks still apply. Setup preserves existing values and marks recognizable older samples rather than assuming every existing record is sample data.

### Thumbnail column

A category can have a **Thumbnail** property for a contact portrait, product picture, or another visual identifier. Add that property through the column chooser to show pictures beside the Library rows, as in a contact list. It is a configurable property column, not a required default column.

Rows with no saved thumbnail show an empty cell. Thumbnail columns do not support sorting or filtering; use Name, Category, or other property columns to narrow or order the list. The usual entity and property-view permissions apply to the images too.

To add, replace, or remove a picture, open the item or use its [Property Browser](property-browser.md). See [Thumbnail properties](../entities/properties.md#thumbnail-properties) for image formats and save behaviour, and [Property definitions](../admin/properties.md#thumbnail-properties) for category setup.

---

## Selecting rows

The Library uses highlighted rows for selection, without selection checkboxes.

- **Click** a row to select it on its own.
- **Ctrl+click** rows to add them to or remove them from the selection.
- **Shift+click** another row to select a range.

To change a property for several entities, select the rows, then open the property in the **Property Browser** using its pencil button or by double-clicking its value. Saving applies the value to the editable selected entities. Check the result for any skipped rows. See [Editing many entities at once](property-browser.md#editing-many-entities-at-once).

---

## Actions

Right-click a row or select it and use the ribbon to:

| Action | Description |
|---|---|
| Open / Details | Open the entity detail page |
| New | Create a new entity (opens entity creation dialog) |
| Delete | Delete the entity (subject to lifecycle-state permissions) |
| Change state | Move the entity to a different lifecycle state |
| Export | Export selected rows to Excel |

### Project-specific items

A Library item can belong to one project instead of the whole workspace — for example a part you only ever need for one build. A project-owned item is hidden from the general Library, so everyone else's searches stay clean, but it can still be found and used from its project.

**To make items project-specific:**

1. Select one or more items in the Library results.
2. In the ribbon, click **Make project-specific…**. It needs the `entity.update` grant.
3. In the **Make project-specific** dialog, choose the **Owning project** and click **Apply**.

**To give them back:** select the items and click **Return to Library**. They become general Library items again.

**To see them:** open **Search options** (the small down-arrow at the right end of the search box) and tick **Show project items** to include project-owned items in the results. The **Project** list in the same menu narrows the results to a single project; **All / general Library** removes that filter. The **Project** column shows each item's owner.

When a project is deleted, MatStream asks what happens to the items it owns. See [Deleting a project](explorer.md#deleting-a-project).

---

## Creating entities from the Library

Click **New** in the Library ribbon. Select a category from the picker, fill in the required properties, and save. The entity is assigned a number from the category's numbering scheme and appears in the Library immediately.

---

## Grants used by Library actions

| Action | Required grant |
|---|---|
| Open the Library | `library.view` |
| Read entity rows and details | `entity.read` |
| Create | `entity.create` |
| Edit | `entity.update` |
| Delete | `entity.delete` |
| Rename | `entity.rename` |
| Change category | `entity.category.change` |
| Change lifecycle | `entity.lifecycle.change` |
| Change state | `entity.state.change`, plus permission for the selected transition |
| Open a stored file | `file.read` |
| Export to Excel | `reports.export` |
| Generate a document from a print template | `print.render` |

Buttons are disabled when the operation grant is absent, and the API independently enforces the
same rule. Category, property, display-tab, lifecycle-state, and transition permissions still
apply after the grant check.

---

## Related

- [Search](search.md) — how the Lucene index works
- [Explorer](explorer.md) — structure-based hierarchical navigation
- [Entities overview](../entities/overview.md) — entity concepts and versioning
- [Categories](../admin/categories.md) — configure categories and their properties
- [Property Browser](property-browser.md) — editing properties, including across many rows
- [Grids, columns & defaults](grid-defaults.md) — saved layouts and workspace defaults
- [Uses & Where Used](where-used.md) — what a catalogue item is used by
- [Price Lists](price-lists.md) — manage dated sales prices for Library items
