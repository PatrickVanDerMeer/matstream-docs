# Units of Measure

MatStream's Units of Measure (UoM) system lets numeric property values carry unit context. This is important for engineering properties like mass, length, pressure, and volume — it ensures that a value of `25` is understood as `25 mm` or `25 kg`, not just a bare number.

---

## Accessing UoM configuration

Go to **Configuration → Units of Measure** in the workspace settings.

---

## Dimensions

A **dimension** is a physical or logical category of measurement — Length, Mass, Temperature, Pressure, Volume, and so on. Dimensions are the top-level grouping for units.

### Adding a dimension

1. Click **New Dimension**.
2. Enter a **Name** (e.g. `Length`).
3. Optionally add a **Description**.
4. Save.

---

## Units

A **unit** belongs to a dimension and represents a specific measurement scale (e.g. mm, cm, m, in, ft all belong to the Length dimension).

### Adding a unit

1. Select a dimension.
2. Click **New Unit**.
3. Fill in:

| Field | Description |
|---|---|
| Name | Full name (e.g. `Millimetre`) |
| Abbreviation | Short label (e.g. `mm`) |
| Conversion factor | Multiplier to convert this unit to the dimension's base unit |
| Is base unit | Tick for the reference unit of this dimension (e.g. `m` for Length) |

4. Save.

**Conversion example:** If the base unit for Length is `m` (metres), then `mm` has a conversion factor of `0.001` (1 mm = 0.001 m).

---

## Unit of Quantity (UoQ)

In addition to physical measurement units, MatStream supports **quantity units** for procurement and inventory contexts:

- Pieces (pcs)
- Metres (m)
- Kilograms (kg)
- Litres (l)
- Sets, Rolls, Sheets, etc.

UoQ values are used on stock positions, order quantities, and BOM quantity fields. They are configured separately from physical dimensions.

---

## Linking units to property definitions

Once dimensions and units are configured, numeric property definitions can be linked to a dimension. This tells MatStream what kind of measurement the value represents.

To link a property to a dimension:

1. Go to **Configuration → Properties**.
2. Select a numeric property definition.
3. In the **Dimension** field, pick the relevant dimension.
4. Save.

When users enter a value for this property, the unit abbreviation is shown as a suffix in the input field (e.g. `25 mm`).

---

## Unit Converter

The built-in [Unit Converter](../features/unit-converter.md) tool uses the dimensions and units defined here. All custom units are automatically available in the converter without any extra configuration.

---

## Related

- [Properties](properties.md) — link properties to dimensions
- [Unit Converter](../features/unit-converter.md) — in-app conversion tool
- [Stock management](../features/stock.md) — UoQ fields on stock positions and lines
