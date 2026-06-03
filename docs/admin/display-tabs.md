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

| Setting | Description |
|---|---|
| Show as tree | Display children in a hierarchical tree rather than a flat grid |
| Show toolbar | Show the Add / Remove / Details toolbar above the grid |
| Bump revision on link | Automatically increment the entity revision when a link is added or removed |
| Filter categories | Only show linked entities that belong to selected categories |
| Default columns | Which columns are visible by default in the grid |

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

Tab visibility can be restricted per user group. In the Display Tabs section of a category, select a tab and click **Permissions** to set which groups can see it. If a user is not in any permitted group, the tab is hidden from them.

This is useful for hiding internal or sensitive tabs (e.g. cost data, stock levels) from users who do not need them.

---

## Sort order

Tabs appear in the entity detail page in ascending sort order. Set the **Sort Order** field to a number (e.g. 10, 20, 30) to control the sequence. Leaving gaps (multiples of 10) makes it easy to insert tabs in between later.

---

## Related

- [Categories](categories.md) — configure the category that owns the display tabs
- [Properties](properties.md) — define the properties shown on General tabs
- [Lifecycle definitions](lifecycle.md) — lifecycle-state permissions that apply on General tabs
- [Stock management](../features/stock.md) — set up Ledger tabs for inventory entities
