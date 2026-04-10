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
| **Text** | Free-form text input | Material grade, description |
| **Number** | Numeric value, optionally with a unit of measure | Weight (kg), length (mm) |
| **Date** | A calendar date | Approval date, expiry date |
| **Boolean** | A yes/no toggle | Is approved, is hazardous |
| **List** | A dropdown with administrator-defined allowed values | Surface finish, colour |

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