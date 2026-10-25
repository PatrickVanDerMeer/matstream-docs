# Display Tabs

Display tabs are the panels that appear at the bottom of an entity detail page. Each tab shows a different type of related information — BOM children, where-used parents, property history, stock levels, or a movement ledger. Administrators configure which tabs appear for each category.

---

## Accessing display tab configuration

1. Go to **Configuration → Categories**.
2. Select a category.
3. Click the **Display Tabs** tab in the category editor panel.

The tab list shows all display tabs currently assigned to this category. Drag rows to reorder them, or use the **Sort Order** field.

---

## Tab types

| Type | What it shows |
|---|---|
| **General** | Editable property fields for the entity version |
| **Uses** | BOM children — entities this entity links to (flat grid or tree) |
| **Where-Used** | BOM parents — entities that link to this entity |
| **History** | All past revisions of the entity with dates and authors |
| **Ledger** | Stock movement history (on-hand delta, reserved delta, on-order delta per movement) |
| **Inherit** | Properties inherited from a linked Definition entity (for Occurrence entities) |
| **Filter Result** | Results of a configurable filter query scoped to this entity |
| **Stock** | Where the item is stocked and what is promised against it (Definition categories) |
| **Shares** | Who the entity is shared with outside the workspace |
| **Sales Pricing** | Price List periods and prices, or an item's Sales prices, depending on the category's tab setup. See [Price Lists](../features/price-lists.md). |
| **3D Model** | The entity's geometry as a rotatable, measurable model — uploaded as a GLB or published from Inventor. See [3D Model viewer](../features/3d-model.md) |

**Uses** comes in two forms — *Uses (flat list)* and *Uses (tree)* — chosen by the tab type itself rather than by a setting.

!!! warning "Filter Result cannot filter on lifecycle state"
    A Filter Result tab can filter by category and by property values, but **not 
    by lifecycle state** — a state condition is silently ignored and the tab 
    returns everything. You can still *show* Lifecycle and Occ. State as columns 
    and sort on them, which is the usual workaround for a list like "open 
    requirements".

---

## Adding a tab to a category

1. In the **Display Tabs** tab of the category editor, click **Add**.
2. Select a tab type from the picker.
3. Fill in the **Name** and optionally an **Icon** (Bootstrap icon class, e.g. `bi-list`).
4. Set the **Sort Order** to control its position in the tab bar.
5. Configure the **Settings** (see below).
6. Click **Save**.

---

## Tab settings

Each tab type has its own settings stored as a JSON object. The category editor provides a structured form for the most common settings:

### Uses / Where-Used settings

A Uses tab is configured across three places in the display tab dialog.

**General tab — what gets created**

| Setting | Description |
|---|---|
| Default Occurrence Category | The category created when a user adds a row. This is what the new line *becomes*. |

**Settings tab — what the user may do**

| Setting | Description |
|---|---|
| Allow Add | Show the **Add from Library** button. Turn this **off** for tabs whose rows must be created by a process rather than by hand. |
| Allow Remove | Allow rows to be unlinked |
| Allow Reorder | Allow rows to be dragged into a different order |
| Bump version when link is added | Create a new entity version whenever a row is added |
| Bump version when link is removed | Create a new entity version whenever a row is removed |
| Default Columns | Which columns are visible by default. Leave empty to show all. |

**Filter tab — which rows appear**

A category tree with checkboxes, plus an optional property filter.

!!! danger "Tick the occurrence category, not the definition"
    The Filter tab decides **which rows the tab displays**, so it must list the 
    **occurrence** categories the rows *are* — `BOM Position`, `Purchase Order 
    Line` — and never the definition categories they point at (`Part`, 
    `Assembly`). Ticking the definition category is the single most common 
    configuration mistake and produces a tab that is **completely empty**, with 
    no error to explain why.

    The category tree also **cascades**: ticking a parent category ticks all its 
    children. If your line categories are grouped under a parent — as Material 
    Requirement Line and Purchase Order Line are under Purchase Order — tick the 
    child on its own, then confirm only one category ended up selected.

!!! note "Library search scope is not editable here"
    Which *definition* categories the **Add from Library** picker offers is a 
    separate setting from the row filter, and the display tab dialog does not 
    expose it. Tabs created by the Setup Wizard have it set correctly; a tab you 
    create by hand falls back to offering the row-filter categories. If you need 
    a hand-made tab to search a different library scope, ask for it to be seeded 
    rather than configured.

!!! example "The Milestones tab"
    The Milestones tab on the Project category (seeded by the Setup Wizard) is a 
    regular Uses tab with its Default Occurrence Category set to Milestone, a 
    row filter of Milestone, and default columns of Name / State / Due / 
    Responsible. Library search is switched off in the seeded configuration, 
    since milestones aren't library items — they're created directly with the 
    tab's **New Milestone** button.

### General tab settings

| Setting | Description |
|---|---|
| Property groups | Which property groups to show on this tab |
| Read-only override | Force all fields on this tab to read-only regardless of lifecycle-state permissions |

### Ledger tab settings

| Setting | Description |
|---|---|
| Balance property keys | Which numeric property keys to show as the "current balance" summary above the ledger |
| Page size | How many ledger rows to load per page |

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

Tabs appear in the entity detail page in ascending sort order. Set the **Sort Order** field to a number (e.g. 10, 20, 30) to control the sequence. Leaving gaps (multiples of 10) makes it easy to insert tabs in between later.

---

## Related

- [Categories](categories.md) — configure the category that owns the display tabs
- [Properties](properties.md) — define the properties shown on General tabs
- [Lifecycle definitions](lifecycle.md) — lifecycle-state permissions that apply on General tabs
- [Stock management](../features/stock.md) — set up Ledger tabs for inventory entities
- [3D Model viewer](../features/3d-model.md) — what the 3D Model tab shows and how models get there
- [Tasks — Milestones](../features/tasks.md#milestones) — how the Milestones tab is used day-to-day
