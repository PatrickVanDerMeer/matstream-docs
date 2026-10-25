# Properties

Properties are the data fields that describe an entity. They are the 
structured information your team captures alongside each part, assembly, 
or document — things like material, weight, finish, supplier, or any 
other attribute relevant to your engineering process.

!!! tip "Coming from Autodesk Vault?"
    Properties in MatStream are the equivalent of Vault's iProperties 
    or custom user-defined properties. The key difference is that 
    MatStream properties are fully configurable per category by your 
    administrator — there are no hardcoded fields.

## Where do properties come from?

Properties are defined by your administrator at the **category** level. 
When an entity is assigned to a category, it automatically inherits all 
the properties defined for that category.

This means:

- A **Raw Material** entity might have properties like Material Grade, 
  Density, and Supplier
- A **Drawing** entity might have properties like Sheet Size, Scale, 
  and Drawing Standard
- A **Purchased Part** entity might have properties like Manufacturer, 
  Part Number, and Lead Time

## Property types

MatStream supports the following property types:

| Type | Description | Example |
|---|---|---|
| **Text** | Short free-form text, sometimes with suggestions or checked as an e-mail address | Material grade, e-mail |
| **Memo** | Long text, plain or formatted (rich text) | Description, notes |
| **Number** | Numeric value, optionally with a unit of measure | Weight (kg), length (mm) |
| **Date / time** | A date, a time, a date with time, or a date with quick ranges from today | Approval date, due date |
| **Boolean** | A yes/no toggle | Is approved, is hazardous |
| **List** | A dropdown with administrator-defined allowed values | Surface finish, colour |
| **Link** | A web address | Datasheet URL |
| **Icon** | An icon picked from the icon set | Status icon |
| **Barcode** | A value shown as a scannable symbol; accepts scanner input | EAN, stock number |
| **User** | A person picked from the workspace's members | Project Manager, Rate Person |
| **Item reference** | A link to another item, picked from a chosen category | Client, Preferred Vendor |
| **Calculated** | Arithmetic over the other properties of the same item | Line Total, Weighted Revenue |
| **Thumbnail** | A small image that visually identifies the item | Contact portrait, product picture |

Which editor you get for a property is chosen by your administrator — see [Editor types](../admin/properties.md#editor-types). Barcode scanning is described in [Barcode scanning](../features/barcode.md).

## Thumbnail properties

If your category has a Thumbnail property, its editor shows a picture preview and image controls:

1. Open an existing item and find the thumbnail property. Save a newly created item before adding its thumbnail.
2. Click **Choose image**, or focus the thumbnail editor and paste an image from the clipboard.
3. Check the preview. Click the picture to open a larger preview; click **Remove** to clear it.
4. Click **Save and Close** to store changes made in the item dialog. **Cancel** discards those pending thumbnail changes.

From the [Property Browser](../features/property-browser.md), open the property's edit dialog instead. Its **Save** button writes the thumbnail immediately; **Cancel** leaves the saved image unchanged. A multi-selection can set or remove the same thumbnail on several items, subject to their permissions and lifecycle locks.

Supported formats are **PNG, JPEG, WebP, BMP, and GIF**, with a **25 MB** upload limit. MatStream centre-crops the image to a square and stores a **512 × 512 WebP** thumbnail. Animated GIFs use the first frame. This is a visual identifier, not storage for the full original image.

Thumbnails follow category-property view/edit permissions. A read-only editor lets you view the picture but not replace or remove it. If the property is hidden by permissions, it is not displayed.

To see images beside item names, add the thumbnail property through the Library or Explorer [column chooser](../features/grid-defaults.md). An empty thumbnail stays blank in the grid. Images are not text-searchable, and the thumbnail column does not support sorting or filtering.

## Calculated properties

A calculated property has no editor. Its value is worked out from the other
properties on the same item, using a formula an administrator writes once on
the property definition:

```
[Quantity] * [Unit Price]
[Estimated Value] * [Probability %] / 100
```

Property names go in square brackets and are matched without regard to case.
The formula understands numbers, `+`, `-`, `*`, `/`, brackets and a leading
minus — and nothing else. Anything more involved belongs in an automation.

The value is recalculated and stored whenever the item is saved, so a
calculated property sorts, filters, exports and prints exactly like any other
number.

> **Note:** If any property the formula refers to is empty, the result is
> **blank rather than zero**. An opportunity with no probability entered has an
> unknown weighted revenue, not one of nought — and a report that showed 0.00
> would understate the total while looking complete. Dividing by zero is blank
> for the same reason.

Administrators are prevented from saving a formula that refers to a property
that does not exist, to a property that is not a number, to the calculated
property itself, or to another calculated property in a way that would form a
loop.

## Required properties

Some properties may be marked as **required** by your administrator. 
MatStream will prevent saving an entity if a required property has not 
been filled in.

Required properties are clearly indicated in the editing interface so 
you know what must be completed before you can save.

## Searchable properties

Administrators can mark properties as **searchable**, which makes them 
available as filter criteria in the entity library grid and search panels. 

If you cannot find a property as a filter option, it may not be marked 
as searchable. Contact your workspace administrator to request this.

## List-value properties

List properties present a fixed set of allowed values defined by your 
administrator. This ensures consistency across the catalogue — for 
example, all entities using a "Surface Finish" property will use the 
same controlled vocabulary rather than free-text variations.

You cannot type a value that is not in the list. If a value you need is 
missing, contact your administrator to have it added.

## Units of measure

Numeric properties can be associated with a **unit of measure** by your 
administrator. This links the value to a physical dimension — for 
example, a Weight property linked to the Mass dimension, so the system 
knows the value represents kilograms or grams.

This is particularly important for engineering properties where the unit 
context matters for data quality and downstream use.

## Property permissions

Not all properties are visible to all users. Your administrator can 
restrict individual properties to specific user groups. If you expect 
to see a property and it is not visible, your user group may not have 
access to it.

## Properties and versions

Property values are stored **per version**. This means:

- When you edit a property and save, a new version is created with the 
  updated values
- Previous versions retain their original property values
- You can always see the full history of how a property value has 
  changed over time by reviewing the version history

## Next steps

- [Revisions](revisions.md) — understand how revision schemes work
- [Lifecycle](lifecycle.md) — understand how lifecycle states control 
  when properties can be edited
- [Overview](overview.md) — back to the entities overview
