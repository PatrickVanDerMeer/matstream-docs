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

Use the **column chooser** (grid toolbar) to add or remove columns. Column widths and sort order are saved per user.

### Thumbnail column

A category can have a **Thumbnail** property for a contact portrait, product picture, or another visual identifier. Add that property through the column chooser to show pictures beside the Library rows, as in a contact list. It is a configurable property column, not a required default column.

Rows with no saved thumbnail show an empty cell. Thumbnail columns do not support sorting or filtering; use Name, Category, or other property columns to narrow or order the list. The usual entity and property-view permissions apply to the images too.

To add, replace, or remove a picture, open the item or use its [Property Browser](property-browser.md). See [Thumbnail properties](../entities/properties.md#thumbnail-properties) for image formats and save behaviour, and [Property definitions](../admin/properties.md#thumbnail-properties) for category setup.

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
