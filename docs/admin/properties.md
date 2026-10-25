# Property definitions

Properties are the data fields that appear on entities in MatStream. Your administrator defines which properties are available in the workspace and assigns them to categories. Users fill in property values when creating and editing entities.

---

## What a property definition controls

| Setting | What it controls |
|---|---|
| **Name** | The label shown to users on the entity detail page |
| **Description** | An explanatory note for administrators |
| **Data type** | The kind of value the property holds (text, number, date, etc.) — fixed once saved |
| **Editor type** | The control users type or pick the value with — see [Editor types](#editor-types) |
| **Editor source settings** (⚙) | Extra configuration some editors need, such as the options of a list |
| **Formula** | Calculated properties only: the arithmetic that produces the value |
| **Initial value** | The value a new entity starts with — see [Initial values](#initial-values) |
| **Required** | Whether users must fill in this property before saving |
| **Unique** | Whether two entities may share the same value — see [Unique values](#unique-values) |
| **Searched** | Whether this property is available as a filter criterion in Library search |
| **Enabled** | Whether this property is active and visible |

---

## Data types

The data type decides how a value is stored, validated, sorted and displayed. It cannot be changed after the property is saved, so choose carefully.

| Data type | Holds | Example |
|---|---|---|
| **String** | Short text, up to 255 characters | Manufacturer, Part number, E-mail |
| **Memo** | Long text with no length limit, optionally formatted | Description, Notes, Comments |
| **DateTime** | A date, a time, or both — the editor decides which | Effective date, Due date |
| **Int** | A whole number | Quantity, Revision count |
| **BigInt** | A very large whole number | External system id |
| **Double** | A floating-point number | Mass, Length |
| **Decimal** | An exact decimal number — use for money and quantities that must add up | Unit price, Required qty |
| **Bool** | Yes / no | RoHS compliant, Approved for reuse |
| **Link** | A web address | Datasheet URL, Supplier portal |
| **IconCss** | A CSS icon class, shown as the icon itself | Status icon |
| **Enum** | One value from a fixed list defined on the property | Status, Surface finish |
| **EntityRef** | A reference to another item, picked from chosen categories | Client, Preferred vendor |
| **Barcode** | A barcode value, displayed as a scannable Code 128 symbol | EAN, Stock number |
| **WorkspaceUserRef** | A workspace member | Project manager, Buyer |
| **Calculated** | A number computed from other properties of the same item | Line total, Weighted revenue |
| **Thumbnail** | A small image that visually identifies the item | Contact portrait, product picture |

Numbers of any numeric type can be linked to a [unit of measure](units-of-measure.md).

### Notes per data type

- **WorkspaceUserRef** always refers to a single *user*, never a group. It only offers the **User picker** — a plain number field would let someone type a valid user id and silently assign the wrong person.
- **EntityRef** stores which item is referenced, not its name, so renaming the referenced item never breaks the link.
- **Barcode** has its own page: [Barcode scanning](../features/barcode.md).
- **Calculated** values are worked out from a formula and cannot be typed into. Choosing this data type shows a **Formula** field in the property dialog, e.g. `[Quantity] * [Unit Price]`. The formula is checked when you save: it is refused if it names a property that does not exist, a property that is not a number, the property itself, or another calculated property in a way that would form a loop. See [Calculated properties](../entities/properties.md#calculated-properties) for the syntax and the blank-not-zero rule.

### Thumbnail properties

To make an image available on a category:

1. Create a property definition with data type **Thumbnail**. New workspaces include a **Thumbnail** property that you can reuse.
2. Assign it to the category in the category editor's **Properties** tab.
3. Set its position, group, and view/edit permissions like any other category property.
4. Users can then upload or paste an image through the thumbnail editor. Add the property as a grid column to display the picture in Library or Explorer.

Thumbnail uses its own image editor, not a text field or a manually entered file ID. **Searched** is disabled for this datatype; images are not included in text search, and thumbnail columns cannot be sorted or filtered.

The image is stored as a square, centre-cropped **512 × 512 WebP** thumbnail. Use a Document or Photo Gallery when you need to retain original files or manage multiple pictures, rather than treating the thumbnail as an original-image archive.

See [Thumbnail properties](../entities/properties.md#thumbnail-properties) for upload formats, limits, and save behaviour.

---

## Editor types

The editor type decides which control users see when they fill in the value — in the item dialog, the [Property Browser](../features/property-browser.md) and editable grids. The **Editor type** list in the property dialog only offers the editors that suit the chosen data type; leave it on the default unless you need something different.

| Editor | What users get | Available for | Source settings (⚙) |
|---|---|---|---|
| **Text box** | Single-line plain text | String (default), Link, IconCss, Calculated (default) | — |
| **Multiline text box** | Multi-row plain text | String, Memo | — |
| **Text box + combo** | Free text with a list of suggestions | String | Suggestion list |
| **Rich text (RTF)** | Formatted text editor | String, Memo (default) | Optional editor settings (JSON) |
| **Combo box** | Pick from a fixed list; no free text | String, Int, Enum (default) | Option list |
| **Date picker** | Calendar, date only | DateTime | — |
| **Date & time picker** | Calendar plus time | DateTime (default) | — |
| **Date range picker** | Calendar with quick ranges counted from today | DateTime | — |
| **Time picker** | Time only | DateTime | — |
| **Check box** | Yes / no toggle | Bool (default) | — |
| **Numeric** | Number input | Int, BigInt, Double, Decimal (default for all four) | — |
| **E-mail** | Text validated as an e-mail address | String | — |
| **URL** | Text validated as a web address | String, Link (default) | — |
| **Icon picker** | Choose an icon from the icon set | IconCss (default) | — |
| **Entity picker** | Search and select another item | EntityRef (only) | Categories to pick from |
| **Barcode editor** | Scanner-ready input with a symbol preview | Barcode (only) | — |
| **User picker** | Search and select a workspace member | WorkspaceUserRef (only) | — |
| **Thumbnail** | Image upload / paste with a preview | Thumbnail (only) | — |

A few combinations worth knowing:

- **String + Combo box** gives a list property whose stored value is plain text; **Int + Combo box** stores a number behind each label (use `value|label` pairs, see below).
- **String + Text box + combo** suggests values but still accepts anything typed — use it when the list is a convenience, not a rule.
- **Calculated** uses a text box only to give the value its normal shape; it is always read-only.

### Editor source settings

Editors marked with a source setting enable the **⚙** button next to the **Editor type** list. A short summary of the current settings appears below the list.

- **Combo box** and **Text box + combo** — enter the options, **one per line**. Tick **Use value|label pairs** to store a value different from what users see, e.g. `1|Receipt`, `2|Issue`. For a combo box these are the only values allowed; for text box + combo they are only suggestions.
- **Entity picker** — choose which categories the picker searches, so a *Client* property only offers customers.
- **Rich text (RTF)** — optional JSON settings for the editor; `{}` gives the default editor.

!!! note
    Removing an option does not change items that already store it. Their value is preserved but appears as unrecognised until you add the option back or edit the item.

### Initial values

**Initial value** is filled in when a new item is created. Type a fixed value or pick one of the functions:

| Function | Fills in |
|---|---|
| `=Date()` | Today's date |
| `=Time()` | The current time |
| `=Now()` | The current date and time |
| `=User()` | The user creating the item |
| `=Workspace()` | The workspace's short name (slug) |

### Unique values

Tick **Unique** to stop two items having the same value. When an item is saved, MatStream checks the latest version of every other item in the workspace and refuses the save if the value is already in use, naming the property. Empty values are never considered duplicates. The check applies to text values.

---

## System properties

Some properties are marked as system properties. These are created automatically by MatStream and are available in every workspace. System properties cannot be deleted but can be assigned to categories like any other property.

### Derived system properties

A second kind of system property is not stored at all — it is **calculated when the entity is read**. Stock figures are the clearest example: a purchase-order line's *Ordered Qty*, *Received Qty* and *Line Total* are summed from the stock ledger every time you look at the line, and cannot be typed into.

Which derived properties an entity has depends on its category's **behaviour**:

| Behaviour | Derived properties |
|---|---|
| Stock Line | Ordered Qty, Received Qty, Reserved Qty, Shipped Qty, Unit Price, Line Total |
| Material Requirement | Reserved Qty, Ordered Qty, Open Qty |
| Stock Location | On Hand, Reserved, Available, On Order |

These appear in the property browser and the column chooser like any other property, but are read-only.

### System keys

Every system property has a stable **system key** — a short identifier such as `RequiredQty` or `OnHandQty` — used internally to find that property regardless of its display name or which workspace it is in.

A stored property definition can also carry a system key. That is how a normal, editable property becomes something the platform can locate reliably: *Required Qty* on a Material Requirement Line is an ordinary decimal property that a buyer can change, but it carries the system key `RequiredQty` so the procurement process knows where to write the quantity.

!!! warning "A system key must be unique across the workspace"
    A key can belong to a derived system property **or** to a stored property definition, never both. Two properties sharing a key will cause the column chooser to fail to open. If the column chooser reports an error mentioning a duplicate key, this is the cause — find the two properties sharing that key and clear it from one of them.

---

## Managing property definitions

Navigate to **Configuration → Property definitions** to manage your workspace properties.

### Creating a property

1. Click **New** in the toolbar
2. Enter a **Name** — this is the label users will see
3. Select a **Data type**
4. Check the **Editor type** — it defaults to the usual editor for the data type. If the ⚙ button is enabled, click it to set the options or picker categories (see [Editor source settings](#editor-source-settings))
5. For a **Calculated** property, enter the **Formula**. Otherwise, optionally set an **Initial value**
6. Set **Required** if users must fill in this field before saving, **Unique** if no two items may share a value, and **Searched** if you want this property available as a filter in Library
7. Click **Save**

### Editing a property

Select a property and click **Edit**, or double-click it. You can change everything except the data type: name, description, editor type and its source settings, formula, initial value, and the Required, Unique and Searched flags. Changing the data type of an existing property is not supported — create a new property instead.

**Renaming a property used in a formula** updates those formulas for you: renaming *Unit Price* to *Sales Price* turns `[Unit Price]` into `[Sales Price]` in every calculated property that uses it, so their values keep working. Two changes are refused while a formula still uses the property: a new name containing `[` or `]`, which a formula cannot refer to, and changing it to a data type that is not a number.

### Deleting a property

Select a property and click **Delete**. A property that is assigned to one or more categories cannot be deleted until it is unassigned from all categories.

A property used in the formula of a calculated property cannot be deleted either — the calculated value would silently go blank. The message names the formulas that use it; remove the property from those formulas first, then delete it.

!!! warning
    Deleting a property permanently removes it and all values stored against it across all entities. This action cannot be undone. Always verify a property is no longer needed before deleting it.

---

## Assigning properties to categories

Defining a property makes it available in the workspace, but it does not appear on any entity until it is assigned to a category. Properties are assigned in the **Properties** tab of the category configuration.

For each property assigned to a category you can configure:

- **Sort order** — the order in which the property appears on the entity detail page
- **Group** — optionally group related properties under a heading
- **Permissions** — which workspace groups can view and edit this property

Permissions are set **per assignment**, not per property. The same property
assigned to two categories is restricted separately in each, so restricting
`Unit Price` on Purchase Order Line does nothing to `Unit Price` elsewhere.

A property with no permission rows is visible to everyone; adding the first
row restricts it to the groups named there and removes it from all others.

→ See [Category editor — property permissions](category-editor-dialog.md#property-permissions)
for the full rules, and [Categories](categories.md) for details on assigning properties.

---

## Base properties

When your workspace is created, MatStream seeds the following properties automatically:

| Property | Type | Searchable |
|---|---|---|
| Description | Memo | Yes |
| Birth Date | Date | No |
| Comments | Memo | Yes |

These are available immediately and can be assigned to any category.

---

## Related

- [Categories](categories.md) — assign properties to categories and configure display settings
- [Configuration overview](configuration.md) — recommended configuration order
- [Properties](../entities/properties.md) — how users interact with properties on entities
- [Barcode scanning](../features/barcode.md) - the Barcode data type and the scanner dialogs
