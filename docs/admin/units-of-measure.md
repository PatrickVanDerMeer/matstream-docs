# Units of Measure

MatStream distinguishes physical measurement units from Units of Quantity used by stock, BOMs and orders. These are separate reference lists; a quantity unit does not by itself define a physical conversion.

## Configure Units of Quantity

Open **Configuration → System Settings → Units of Quantity**. This requires `admin.workspace.settings`.

1. To add a unit, enter its **Symbol**, workspace **Name**, and **Available** setting, then click **Add unit**.
2. To change an existing unit, edit its workspace name or availability and click **Save unit** on that row.
3. Save each row separately; **Save settings** applies the workspace defaults above the unit list.

Names and availability are local to the workspace, including overrides of global units. Existing symbols are stable identifiers and cannot be changed. Add a new unit for a different symbol; duplicate symbols are refused. Disabling a unit removes it from normal new selections while existing records retain their unit identity.

The Stock tab retains its currently assigned unit in the list even if disabled. A stock unit cannot be changed while on-hand stock, reservations or on-order commitments exist. See [Stock management](../features/stock.md).

For **Stock Unit** property option descriptions, open **Configuration → Property Definitions**, edit the property, and use **Editor source settings**. Stored option values are quantity-unit IDs; preserve those IDs. See [Editor source settings](properties.md#editor-source-settings).

## Physical dimensions and conversion

The [Unit Converter](../features/unit-converter.md) reads the available physical dimensions and units, including factors and offsets needed for conversions such as temperature. Only units in the same dimension can be converted.

The current Configuration screen has no physical Units of Measure editor, **New Dimension** command or **New Unit** command. The property-definition editor also has no **Dimension** field. If the converter lacks a required physical unit, ask your MatStream support contact about the installed reference data. Adding a Unit of Quantity in System Settings does not add a unit to the converter.

## Related

- [System settings](system-settings.md) — workspace defaults and quantity-unit administration
- [Property definitions](properties.md) — numeric formats and option lists
- [Unit Converter](../features/unit-converter.md) — convert physical measurements
