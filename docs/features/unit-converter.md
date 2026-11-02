# Unit Converter

The Unit Converter is a built-in tool that converts values between units of measurement. It reads the installed physical unit and dimension reference data. Its list is separate from stock and order Units of Quantity.

---

## Opening the Unit Converter

Access the Unit Converter from the **Tools** group in the Explorer ribbon. It opens as a modal dialog over the current page.

---

## Using the converter

1. The dialog opens with a tab for each **dimension** defined in your workspace (Length, Mass, Pressure, Temperature, etc.).
2. Select the tab for the dimension you want to convert.
3. Choose the **From unit** and **To unit** from the dropdowns.
4. Enter the value to convert.
5. The result updates immediately.

The last active dimension tab is remembered and restored next time you open the converter.

---

## Available dimensions and units

The converter lists the physical dimensions and units available to your workspace. Common dimensions include:

| Dimension | Example units |
|---|---|
| Length | mm, cm, m, in, ft |
| Mass | g, kg, lb, oz |
| Pressure | Pa, kPa, MPa, bar, psi |
| Temperature | °C, °F, K |
| Volume | ml, l, m³, gal |
| Force | N, kN, lbf |

The current Configuration screen has no physical-dimension or conversion-unit editor. If a required unit is missing, ask your MatStream support contact about the installed reference data. Administrators can manage stock and order quantity units in **System Settings**, but that does not extend this converter.

---

## Related

- [Units of Measure](../admin/units-of-measure.md) — quantity-unit administration and physical reference data
- [Properties](../admin/properties.md) — numeric formats and editors
- [Explorer](explorer.md) — the ribbon where the Unit Converter is accessible
