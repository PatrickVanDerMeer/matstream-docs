# Property definitions

Properties are the data fields that appear on entities in MatStream. Your administrator defines which properties are available in the workspace and assigns them to categories. Users fill in property values when creating and editing entities.

---

## What a property definition controls

| Setting | What it controls |
|---|---|
| **Name** | The label shown to users on the entity detail page |
| **Data type** | The kind of value the property holds (text, number, date, etc.) |
| **Required** | Whether users must fill in this property before saving |
| **Searchable** | Whether this property is available as a filter criterion in Library search |
| **Enabled** | Whether this property is active and visible |

---

## Data types

MatStream supports the following property data types:

| Type | Description | Example |
|---|---|---|
| **Text** | Short free-form text, up to 255 characters | Manufacturer, Surface finish |
| **Memo** | Long free-form text, no length limit | Description, Notes, Comments |
| **Number** | Numeric value, optionally linked to a unit of measure | Mass, Length, Quantity |
| **Date** | A calendar date | Effective date, Expiry date |
| **Boolean** | A yes/no toggle | RoHS compliant, Approved for reuse |
| **List** | A dropdown with a controlled list of allowed values | Status, Category, Material |

Choosing the right data type matters — it determines how the value is stored, validated, and displayed. A date field enforces a valid date format; a list field ensures users select from approved values rather than typing free text.

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
4. Set **Required** if users must fill in this field before saving
5. Set **Searchable** if you want this property available as a filter in Library
6. Click **Save**

### Editing a property

Select a property and click **Edit**, or double-click it. You can change the name, required flag, and searchable flag. Changing the data type of an existing property is not supported — create a new property instead.

### Deleting a property

Select a property and click **Delete**. A property that is assigned to one or more categories cannot be deleted until it is unassigned from all categories.

!!! warning
    Deleting a property permanently removes it and all values stored against it across all entities. This action cannot be undone. Always verify a property is no longer needed before deleting it.

---

## Assigning properties to categories

Defining a property makes it available in the workspace, but it does not appear on any entity until it is assigned to a category. Properties are assigned in the **Properties** tab of the category configuration.

For each property assigned to a category you can configure:

- **Sort order** — the order in which the property appears on the entity detail page
- **Group** — optionally group related properties under a heading
- **Read-only groups** — restrict which user groups can edit this property

→ See [Categories](categories.md) for details on assigning properties.

---

## List values

When a property uses the **List** data type, you define the allowed values in the property definition. Users can only select from these values — they cannot type free text.

To manage list values, open the property definition and navigate to the **Values** tab. Add, reorder, or remove values as needed.

!!! note
    Removing a list value does not affect entities that already have that value stored. Existing values are preserved but will appear as unrecognised if the value is removed from the list.

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
