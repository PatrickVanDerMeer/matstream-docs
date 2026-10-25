# Property Browser

The Property Browser is the right-hand pane in the Explorer and the Library. It shows the properties of whatever is selected and lets you edit them in place — including editing one property across many entities at once.

---

## Layout

Properties are shown in grouped accordion sections, in the order and grouping the category defines. Which sections you leave open is remembered per user, and separately for the Explorer and the Library, so each surface keeps the shape you gave it.

Properties you do not have permission to see are not shown. Property-level permissions are set on the property's category assignment — see [Property definitions](../admin/properties.md).

---

## Editing a single value

Click the **pencil** beside a value, or **double-click** the value itself. A small edit dialog opens for that one property, with the editor its data type and configuration call for: a date picker for dates, a list for list properties, a checkbox for Booleans, a barcode-aware editor for barcode properties.

**Save** writes the value; **Cancel** discards it. Each property is saved on its own — there is no pending-changes state across the pane.

### Editing a thumbnail

A **Thumbnail** property opens an image editor rather than a text field. Click **Choose image**, or focus the editor and paste an image from the clipboard. Click the preview for a larger view, or **Remove** to clear the picture. **Save** applies the change; **Cancel** discards it.

The same editor works on a multi-selection: saving applies the chosen image, or its removal, to the editable selected items. Read-only items are skipped and reported like other batch property edits. See [Thumbnail properties](../entities/properties.md#thumbnail-properties) for supported formats and upload limits.

!!! note "Read-only in a locked state"
    If the entity's lifecycle state forbids modification, the editors do not open. This is the lifecycle doing its job — released data is not editable in place.

---

## Editing many entities at once

Select several rows in the grid and the Property Browser switches to aggregate mode. This is the fastest way to set a material, a vendor or a project code across a whole selection.

### What you see

The pane shows how many entities and how many categories are in the selection, then the properties they have **in common**:

| Situation | Displayed as |
|---|---|
| Every selected entity has the same value | The value itself |
| The selected entities disagree | `<varies>` |
| Every selected entity is empty | Blank |

A mixed-category selection is fine. The pane shows the properties the selection actually shares, so selecting parts and assemblies together still lets you set the properties they both have.

### Writing a value

Open the property the same way — pencil or double-click — and the edit dialog opens for the whole selection.

When the selected entities disagree, the dialog says so plainly:

> Values differ across the selection (`<varies>`). Saving sets the same value on all.

The editor starts empty in that case, so you are always making a deliberate choice rather than accidentally propagating whichever value happened to be first. Save writes your value to **every selected entity** in one operation.

The result tells you what actually happened — for example *Saved 12 of 15. Skipped 3 (read-only)*. Entities are skipped, never silently ignored; the usual reason is that the entity is in a lifecycle state that forbids modification, or that you lack permission on that property or that record.

!!! warning "There is no “leave this one alone” option"
    Saving the dialog sets the value on the whole selection. If some of the selected entities should keep their existing value, narrow the selection first — you cannot exclude rows from within the dialog.

---

## What a batch edit does not do

- It does not bypass the lifecycle. Locked entities are skipped and reported.
- It does not bypass property permissions.
- It is **not transactional across the selection**. If some rows are skipped, the rows that succeeded stay written — the result tells you exactly which were which, so you can fix the remainder and repeat.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/wid/{wid}/entities/properties/aggregate` | The common properties of a selection, each flagged as shared or varying |
| `POST` | `/api/wid/{wid}/entities/property-values/read` | The aggregated value of one property across a selection |
| `POST` | `/api/wid/{wid}/entities/property-values/set` | Write one property to every entity in a selection |

---

## Related

- [Explorer](explorer.md) — where the Property Browser sits in the three-pane layout
- [Library](library.md) — the same pane over the catalogue
- [Properties](../entities/properties.md) — what properties are and how they behave
- [Property definitions](../admin/properties.md) — data types, editors, permissions and searchable flags
- [Lifecycle](../entities/lifecycle.md) — why a state can make properties read-only
