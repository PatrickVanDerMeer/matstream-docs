# Text Templates

Text Templates let administrators define reusable text strings with placeholders that are resolved at runtime against entity property values. They are used to auto-generate consistent descriptions, labels, file names, and other structured text output.

---

## Accessing text templates

Go to **Configuration → Text Templates** in the workspace settings.

---

## How text templates work

A template is a named string containing fixed text and **placeholders** in `{PropertyKey}` syntax. When the template is rendered against an entity, each placeholder is replaced with the value of the matching property from that entity.

**Example template:**

```
{PD_PartNumber} - {PD_Description} ({PD_Material})
```

Rendered against an entity with Part Number `MAT-0042`, Description `Bracket`, Material `Aluminium 6061`:

```
MAT-0042 - Bracket (Aluminium 6061)
```

---

## Creating a text template

1. Go to **Configuration → Text Templates**.
2. Click **New**.
3. Enter a **Name** (used to identify the template when selecting it in other areas of the platform).
4. Enter the **Template** string with `{PropertyKey}` placeholders.
5. Optionally add a **Description** for the administrator notes.
6. Save.

---

## Placeholder syntax

Placeholders reference **property definition keys** — the internal key assigned when a property definition is created (e.g. `PD_Description`, `PD_Material`). Keys are case-sensitive.

If a placeholder key does not match any property on the entity being rendered, it is left blank (not shown as an error).

**Supported placeholder types:**

| Property type | Rendered as |
|---|---|
| Text | The text value |
| Number | Formatted number string |
| Date | ISO date string (`YYYY-MM-DD`) |
| Boolean | `true` or `false` |
| List value | The selected list item label |

---

## Use cases

Text templates are used in several places across MatStream:

| Use case | Description |
|---|---|
| Auto-generated entity descriptions | Assign a template to a category to auto-populate the description field when an entity is created or updated |
| Part labels | Generate a formatted label string for printing or export |
| Export file names | Use a template for the file name when exporting or generating documents |
| Import field defaults | Use template output as the default value for a mapped field in an import definition |
| Notification message bodies | Automation scripts can call a template to build a consistent notification string |

---

## Assigning a template to a category

To auto-populate an entity's description when it is created:

1. Go to **Configuration → Categories**.
2. Select the category.
3. In the **General** tab, find **Auto-description template**.
4. Select the text template from the dropdown.
5. Save.

When a new entity in this category is created or saved, the description field is automatically populated with the rendered template output.

---

## Related

- [Categories](categories.md) — assign templates to categories
- [Properties](properties.md) — define the properties referenced in placeholder keys
- [Automations](../features/automations.md) — use templates in automation scripts
