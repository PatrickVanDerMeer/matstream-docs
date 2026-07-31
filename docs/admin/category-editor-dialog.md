# Category editor dialog

The category editor dialog is the main interface for creating and
editing categories in MatStream. It uses a deferred save model —
all changes are held in memory and only written to the database
when you click **Save** or **Save and Close**.

---

## Opening the dialog

The dialog opens from **Administration → Categories**:

- Click **New** to create a new category
- Select an existing category and click **Edit** to edit it
- Double-click a category row to edit it

---

## General tab

The top section of the dialog contains the category's core settings:

| Field | Description |
|---|---|
| **Name** | Required. The display name of the category. Must be unique in the workspace — MatStream matches categories by name, so two categories cannot share one. Maximum 100 characters. |
| **Description** | Optional. A description of the category's purpose. Maximum 255 characters. |
| **Behavior type** | An optional extra capability — Project, Task, Stock Line, Material Requirement, Stock Location or Filter Item. See [Category behaviors](categories.md#category-behaviors). |
| **Numbering scheme** | The template used to generate unique entity numbers. |
| **Revision scheme** | The sequence used for formal revision labels. |
| **Version creation policy** | When editing an entity creates a new version rather than updating the current one. |
| **Is a file** | Whether entities of this category carry an attached file. |
| **Show in New dialog** | When unticked, the category is hidden from the New dialog, Change Category, document upload and data import. It remains fully editable here. Use it for heading categories and for anything whose entities must only be created by a process. |
| **Suppress children in Explorer** | Stops the Explorer expanding this category's entities into sub-items. |

Changes to these fields mark the dialog as dirty immediately.

!!! note "Lifecycles are on their own tab"
    A category can carry more than one lifecycle, with one marked as the 
    default that new entities start in. Use the **Lifecycles** tab rather 
    than the General tab to assign them.

!!! warning "Changing the behaviour of a category in use"
    Behaviour determines which derived properties an entity has and how the 
    platform treats it. Changing it on a category that already has entities 
    can leave existing rows without the data the new behaviour expects — for 
    example, switching a line category from Stock Line to Material 
    Requirement leaves old lines with ledger entries but no stored quantity. 
    Decide the behaviour before creating entities where you can.

---

## Properties tab

The Properties tab shows the properties assigned to this category.
It is split into two panes:

- **Left pane** — the assigned properties grid
- **Right pane** — the permissions for the selected property

### Assigned properties grid

Each row represents one property assignment. Columns:

| Column | Editable | Description |
|---|---|---|
| Property | No | The name of the property definition |
| Group | Yes | Optional group heading shown on the entity form |
| Required | Yes | Whether the field is mandatory |
| Default Value | Yes | Pre-filled value for new entities |

Rows can be dragged to change sort order. The sort order determines
the sequence in which properties appear on the entity detail page.

### Adding properties

Click the **+** button to open the property picker dialog. Select
one or more properties and confirm. The selected properties are added
to the grid immediately as unsaved rows.

### Removing properties

Select a row and click the **×** button. The row is flagged for
removal in memory. The actual deletion happens when you save.

### Property permissions

When you select a property row, the right pane shows which workspace
groups can view and edit that property. Permissions are loaded lazily
from the API the first time you select a property. Changes are held
in memory until you save.

---

## Display Tabs tab

The Display Tabs tab shows the display tabs assigned to this category.
It is split into two panes:

- **Left pane** — the assigned display tabs grid
- **Right pane** — the permissions for the selected tab

### Assigned display tabs grid

Each row represents one display tab assignment. Columns:

| Column | Description |
|---|---|
| Enabled | Whether the tab is enabled |
| Display Tab | The name of the display tab definition |
| Type | The tab type (Uses, Where-Used, etc.) |
| Display Name | An optional override for the tab label shown to users |

Rows can be dragged to change sort order. The sort order determines
the sequence in which tabs appear on the entity detail page.

### Adding display tabs

Click the **+** button to open the display tab picker dialog. Select
one or more tabs and confirm. The selected tabs are added to the grid
immediately as unsaved rows.

### Removing display tabs

Select a row and click the **×** button. The row is flagged for
removal in memory. The actual deletion happens when you save.

### Tab permissions

When you select a tab row, the right pane shows which workspace groups
can view and edit that tab. Permissions are loaded lazily from the API
the first time you select a tab. Changes are held in memory until you
save.

---

## Save behaviour

The dialog uses a fully deferred save model:

| Action | What happens |
|---|---|
| Edit a field | Marked dirty in memory. No API call. |
| Add a property | Added to in-memory VM list. No API call. |
| Remove a property | Flagged `IsDeleted` in memory. No API call. |
| Reorder properties | Sort order updated in memory. No API call. |
| Add a display tab | Added to in-memory VM list. No API call. |
| Remove a display tab | Flagged `IsDeleted` in memory. No API call. |
| Reorder display tabs | Sort order updated in memory. No API call. |
| Change perm group | Written back to VM immediately via in-memory callback. No API call. |
| Click **Save** | All pending changes flushed to the API. Dialog stays open. |
| Click **Save and Close** | All pending changes flushed to the API. Dialog closes. |
| Click **Cancel** | All in-memory state discarded. Dialog closes. No API calls. |
| Close dialog (× button) | Same as Cancel. |

### Save order

When saving, the dialog processes changes in this order:

1. Save/create the category definition (name, description, lifecycle, etc.)
2. Set assigned property IDs (add/remove assignments)
3. Update dirty property assignment fields (group, required, default value, sort order)
4. Save property permissions (upsert/delete perm rows)
5. Assign new display tabs
6. Update dirty display tab fields (sort order, display name, etc.)
7. Remove deleted display tabs
8. Save display tab permissions (upsert/delete perm rows)

---

## Dirty state and Save button

The **Save** and **Save and Close** buttons are disabled when there
are no unsaved changes. The footer shows:

- **Saved** — no pending changes
- **Unsaved changes** — one or more changes are pending

Changes that trigger the dirty state:

- Any field edit on the General tab
- Adding, removing, or reordering a property
- Editing group, required, or default value on a property
- Adding or removing a permission group on a property or tab
- Adding, removing, or reordering a display tab

---

## Technical reference

### Key files

| Layer | File | Purpose |
|---|---|---|
| Dialog | `CategoryEditorDialog.razor` | Top-level dialog, Save/Cancel buttons |
| Panel | `CategoryPanel.razor` | Hosts the General / Properties / Display Tabs tab strip |
| Properties tab | `CategoryTabProperties.razor` | Owns property VM list, lazy perm loading, SaveAsync |
| Display Tabs tab | `CategoryTabDisplayTabs.razor` | Owns tab VM list, lazy perm loading, SaveAsync |
| Properties grid | `CategoryPropertiesGrid.razor` | Data-down grid, drag-and-drop reorder |
| Display tabs grid | `CategoryTabsGrid.razor` | Data-down grid, drag-and-drop reorder |
| Prop perm grid | `CategoryPropertyPermGrid.razor` | In-memory perm grid for a single property |
| Tab perm grid | `CategoryTabPermGrid.razor` | In-memory perm grid for a single display tab |
| ViewModels | `CategoryPropViewModel.cs` | In-memory state for one property assignment |
| ViewModels | `CategoryTabViewModel.cs` | In-memory state for one display tab assignment |
| ViewModels | `PermRowVm.cs` | In-memory state for one permission row |

### API endpoints used by the editor

| Method | Route | When called |
|---|---|---|
| `GET` | `categories/{id}` | On dialog open (edit mode) |
| `POST` | `categories` | On save (new category) |
| `PUT` | `categories/{id}` | On save (existing category) |
| `GET` | `categories/{id}/assigned-properties` | On Properties tab first render |
| `PUT` | `categories/{id}/assigned-properties` | On save (add/remove properties) |
| `PATCH` | `categories/category-properties/{id}` | On save (dirty property fields) |
| `GET` | `categories/category-properties/{id}/permissions` | On first property selection |
| `POST` | `categories/category-properties/{id}/permissions/upsert` | On save (perm changes) |
| `DELETE` | `categories/category-properties/permissions/{id}` | On save (perm deletions) |
| `GET` | `categories/{id}/assigned-tabs` | On Display Tabs tab first render |
| `POST` | `categories/{id}/assigned-tabs/{displayTabId}` | On save (new tab assignments) |
| `PATCH` | `categories/category-tabs/{id}` | On save (dirty tab fields) |
| `DELETE` | `categories/{id}/assigned-tabs/{categoryTabId}` | On save (tab removals) |
| `GET` | `categories/category-tabs/{id}/permissions` | On first tab selection |
| `POST` | `categories/category-tabs/{id}/permissions/upsert` | On save (perm changes) |
| `DELETE` | `categories/category-tabs/permissions/{id}` | On save (perm deletions) |
