# Grids, columns & defaults

Every grid in MatStream — Explorer, Library, Uses, Where Used, History, Filter Results — remembers how you set it up. Grid state is a stored setting, not a per-session accident, and an administrator can decide what a brand-new user sees before they have set anything up at all.

---

## The three tiers

When a grid loads, it looks for its layout in this order and stops at the first thing it finds:

| Tier | Source | Applies to |
|---|---|---|
| **1** | Your own saved layout | Just you |
| **2** | The workspace default | Everyone who has never customised this view — including everyone added from now on |
| **3** | The built-in fallback | Used when neither of the above exists |

The consequence worth knowing: **changing the workspace default does not disturb colleagues who have already customised the view.** Their tier-1 layout still wins. It changes what new users see, and what anyone gets after a reset.

---

## What is stored

| Stored | Not stored |
|---|---|
| Which columns are visible, their order and widths | Columns hidden in the workspace but never shown |
| Sort order and page size | Your current row selection |
| Filters and grouping | |
| Frozen columns | |
| Number and date formats | |
| Conditional formatting | |
| Splitter pane sizes and panel visibility | |

Layouts store only what is actually shown, so a saved layout stays small and does not carry every property definition in the workspace along with it.

---

## Saving and resetting

Open the **column chooser** on any grid and click the **gear**. Two actions are offered:

### Save as initial default (new users)

Stores the current settings as the workspace default. Users who have never customised this view — including everyone added from now on — will see it. Colleagues who already customised it keep their own.

### Reset to default settings

Discards your personal settings for this view and goes back to the workspace default. Affects only you.

In the column chooser, Reset previews the default in the grid first, so **Cancel** still works if it is not what you expected.

!!! note "Save as default is sometimes unavailable"
    The option is hidden where there is nothing to save it against — most commonly an Explorer grid outside a category folder, which has no tier-2 key of its own. Step into a folder that has a category and the option appears.

---

## Explorer layouts are per folder category

This is the part that surprises people. In the Explorer, your column layout is remembered **separately for each folder category**, not for the Explorer as a whole.

If your workspace has a *Project* folder category and a *BOM Folder* category, standing in a project shows one set of columns and stepping into its BOM folder shows another — each with its own widths, order and visibility. Change columns while in a BOM folder and you have changed them for every BOM folder, not for projects.

This is deliberate: a project folder wants Client, Milestones and dates; a BOM folder wants Part Number, Material and Manufacturer. Giving a folder type its own category is the supported way to give it its own view.

Folders with no category fall back to a single shared Explorer layout.

---

## Category visibility is per folder category too

The **Object Visibility** dropdown in the Explorer ribbon controls which entity categories appear in the grid — untick a category and its entities are hidden from the tree.

Like column layouts, this choice is scoped to the folder category you are standing in. A *Project* folder can hide Material Requirement lines while a buyer's folder shows nothing else, without the two settings fighting each other.

The same tier rules apply: an administrator can seed the visibility for a folder category as the workspace default, and any user can still override it for themselves.

---

## Seeding defaults for a new workspace

The practical sequence for an administrator setting up a workspace:

1. Create the folder categories that need their own view — *Project*, *BOM Folder*, and so on.
2. Stand in a folder of that category.
3. Set the columns, widths, order, sort and object visibility you want new users to get.
4. Column chooser → gear → **Save as initial default (new users)**.
5. Repeat per folder category and per grid that matters — the Library grid, the Uses and Where Used tabs, and History each have their own default.

---

## Related

- [Explorer](explorer.md) — the three-pane layout and the ribbon
- [Library](library.md) — the catalogue grid and its filters
- [Uses & Where Used](where-used.md) — relational grids using the same machinery
- [Display Tabs](../admin/display-tabs.md) — which tabs exist on a category, and their configured columns
- [Categories](../admin/categories.md) — creating the folder categories these settings are keyed to
