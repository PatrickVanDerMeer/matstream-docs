# Display Tabs

Display tabs are the panels that appear at the bottom of an entity detail page. Each tab shows a different type of related information — BOM children, where-used parents, property history, stock levels, or a movement ledger. Administrators configure which tabs appear for each category.

---

## Accessing display tab configuration

1. Go to **Configuration → Categories**.
2. Select a category.
3. Click the **Display Tabs** tab in the category editor panel.

The tab list shows all display tabs currently assigned to this category. Drag rows to reorder them and save the category.

---

## Tab types

| Type | What it shows |
|---|---|
| **General** | Editable property fields for the entity version |
| **Uses** | Configurable lists of structural/BOM children, associations or requirements outside the BOM |
| **Where-Used** | BOM parents — entities that link to this entity |
| **History** | All past revisions of the entity with dates and authors |
| **Ledger** | Stock movement history (on-hand delta, reserved delta, on-order delta per movement) |
| **Inherit** | Properties inherited from a linked Definition entity (for Occurrence entities) |
| **Filter Result** | Results of a configurable filter query scoped to this entity |
| **Stock** | Where the item is stocked and what is promised against it (Definition categories) |
| **Shares** | Who the entity is shared with outside the workspace |
| **Sales Pricing** | Price List periods and prices, or an item's Sales prices, depending on the category's tab setup. See [Price Lists](../features/price-lists.md). |
| **3D Model** | The entity's geometry as a rotatable, measurable model — uploaded as a GLB or published from Inventor. See [3D Model viewer](../features/3d-model.md) |
| **Permissions** | Entity access rules, subject to permission-management access. |
| **Planning** | Project schedule, or task planning details for a Task-behavior category. See [Project planning](../features/planning.md). |
| **Photo Gallery** | Versioned photo attachments, folder photos or read-only filter results. See [Photo Gallery](../features/photo-gallery.md). |
| **Booked Hours** | Weekly project time entries with person, task, hour type and status. Requires project/tab access and time-view or project-management authorization. |


**General** is a system tab; the ordinary new-definition type picker does not offer it.

**Uses** is the current BOM/list tab type. The old Uses Tree Grid type is retired. **Where Used** has a Renderer setting for Flat List (Grid) or Tree Grid.

Filter Result supports property criteria and base fields including Name, Version, Revision, Category, Lifecycle and lifecycle state. Configure the scope and criteria, then verify the returned rows. Lifecycle-state filtering is implemented; it is not limited to a display-only state column.

---

## Adding a tab to a category

1. Open **Configuration → Display Tabs** to create or edit a reusable display-tab definition.
2. Set its name, display name, type and applicable settings, filters and ribbon actions; save the definition.
3. Open **Configuration → Categories**, edit the category and select **Display Tabs**.
4. Use **+** to select existing definitions in the picker.
5. Set the assignment's display name, enabled state and group permissions, drag to reorder, then save the category.

Tab definitions and category assignments are separate. Editing a shared definition can affect every category using it. The category picker assigns existing definitions; it does not create a definition in place.

---

## Tab settings

Each tab type has its own settings stored as a JSON object. The display-tab definition editor provides structured Filter, Settings and applicable Ribbons panels:

### Uses / Where-Used settings

A Uses definition separates row/source selection, display settings and ribbon commands. Category assignments then control where the shared definition appears and who can use it.

**Settings and ribbon configuration**

| Setting | Description |
|---|---|
| Relationship | **Structural children / BOM**, **Associations to existing items**, or **Requirements outside the BOM**. The relationship determines whether rows contribute to the structural BOM. |
| Allow Add from Library | Show the **Add from Library** button. Turn this **off** for tabs whose rows must be created by a process rather than by hand. |
| Allow Remove | Allow rows to be unlinked |
| Bump version when link is added | Create a new entity version whenever a row is added |
| Bump version when link is removed | Create a new entity version whenever a row is removed |
| Default Columns | Starting visible columns. |
| Show rows from the linked definition (read-only) | Include definition-owned rows on occurrences; those rows cannot be edited or removed here. |
| Show selection checkboxes | Offer row selection checkboxes. |
| Column total / Footer label | Sum a numeric column in loaded rows; this display total is not stored on the parent. |

**Filter tab — which rows appear**

The **Filter** tab's category tree separates categories by family:

- **Definitions** limit the **Add from Library** search.
- **Occurrences** limit displayed rows and the category templates offered by **New**.

For an assembly BOM, select the desired definition categories such as Part and Assembly, and the occurrence category used by its rows, such as BOM Position. Selecting only a definition category does not configure the occurrence rows. Check cascaded child selections before saving. The optional Property Filter further narrows the results.

The Milestones tab is a Uses definition with Milestone occurrence rows and a New action for that template. Its seeded configuration does not require a Library item for each checkpoint.

### Spare Parts tabs

For spare parts, use **Uses** with **Requirements outside the BOM** and an occurrence category carrying the requirement properties. Engineering setup supplies **Spare Part Requirements**, displayed as **Spare Parts**, with **Spare Part Requirement** as its default occurrence category.

Configure the allowed Library definition categories separately from the displayed occurrence categories. Engineers use **Add from Library** to create requirement rows. Structural **New** and file creation are not offered for this relationship. The **Show rows from the linked definition (read-only)** setting is disabled: project recommendations become independently owned requirement rows linked to their source recommendations.

On the requirement category's **Properties** assignments, choose **Source requirement** as the inheritance source for properties that should support project overrides. Assign **Requirement Quantity** to the numeric property used for demand totals. The spare's Library part reference remains separate from the source recommendation relationship.

Administrators can change service-period options, labels and descriptions in the property-definition editor; configure tab labels, columns and actions here; and set category-tab permissions on the assignments. Existing settings are preserved when Engineering setup is rerun. See [Spare parts](../features/spare-parts.md), including the current shared-inherited-line limitation.

For an existing installation, deploy the spare-parts API and web updates, apply `20261110_requirement_relationships.sql` through the normal database migration process, then rerun Engineering setup to seed the configuration. Creating a display tab alone does not install the required database support.

### Uses ribbons

In **Ribbons**, configure the display name, enabled state, buttons, icons and ordered actions for this Uses tab. A New action selects from the occurrence templates chosen in Filter. Built-in actions can be combined with Open Scanner, Change State, Edit Property, Run Automation and Refresh. Dialog actions must come last. The preview is a preview, not a live execution of the action.

These labels and workflow choices are administrator settings. Save the definition after editing; modifying a shared definition affects its assignments. Actions still enforce the user's grants and record permissions.

### General tab settings

**Show Stored File** controls the stored-file section. Property assignments and their group/order belong to the category's Properties configuration; tab access is controlled by category-tab permissions.

### Photo Gallery settings

Choose **Photo source** (Automatic, Versioned attachments, Folder children or read-only Filter results), default upload category, visible photo categories, default Gallery/List layout, mosaic size, page size and allowed actions. The upload category must have Photo behavior. See [Photo Gallery](../features/photo-gallery.md).

### Ledger tab settings

| Setting | Description |
|---|---|
| Balance property keys | Which numeric property keys to show as the "current balance" summary above the ledger |
| Editable Property Keys | Property keys exposed for editing by the ledger configuration. |
| Default Columns | Starting visible columns. |

---

## Enabling and disabling tabs

Tabs can be toggled on or off without deleting their configuration. This is useful for rolling out a new tab type to users gradually.

- **Enabled** — the tab appears on entity detail pages.
- **Disabled** — the tab is hidden from users but its configuration is preserved.

---

## Tab permissions

Tab access can be restricted per user group. In the Display Tabs section of a category, select a tab and set its **View** and **Edit** permissions. A user with View but not Edit can read the tab without changing it. A user with neither permission does not see the tab. If no group permissions are defined, the tab has no additional group restriction; normal entity and workspace grants still apply.

For an item's **Sales** tab, these permissions control whether prices are editable, view-only, or hidden. This is useful for limiting access to sensitive price or stock information. See [Price Lists](../features/price-lists.md#permissions).

---

## Sort order

Drag category-tab assignment rows into the desired order and save the category. There is no separate sort-order editing dialog for each assignment.

---

## Related

- [Categories](categories.md) — configure the category that owns the display tabs
- [Properties](properties.md) — define the properties shown on General tabs
- [Lifecycle definitions](lifecycle.md) — lifecycle-state permissions that apply on General tabs
- [Stock management](../features/stock.md) — set up Ledger tabs for inventory entities
- [3D Model viewer](../features/3d-model.md) — what the 3D Model tab shows and how models get there
- [Tasks — Milestones](../features/tasks.md#milestones) — how the Milestones tab is used day-to-day
