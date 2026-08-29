# Barcode scanning

MatStream treats a barcode as a property type, not a bolt-on. Any category can carry a barcode property, the value renders as a scannable Code 128 symbol, and the scanner dialogs look items up by that property — so picking parts onto an order becomes scan, scan, scan.

No driver or integration is needed. Any keyboard-wedge scanner works: it types the code into the focused field and sends **Enter**, which is exactly what the barcode inputs listen for.

---

## The Barcode property type

Add a property with the **Barcode** data type in Configuration → Property definitions, then assign it to the categories that need it.

| Aspect | Behaviour |
|---|---|
| Storage | A string of up to 255 characters |
| Display | Rendered as a **Code 128** symbol, so the value on screen can itself be scanned |
| Editor | The **Barcode editor** — a scanner-ready input with a visual preview and a clear button |

The property is an ordinary property in every other respect: it can be marked searchable, it obeys property permissions, and it can be set across many rows at once from the [Property Browser](property-browser.md).

!!! note "Name the property consistently"
    The scanner dialogs look up items by a named barcode field. If half your categories call it *Barcode* and the other half *EAN*, a scan will only find one of them. Pick one name and reuse the same property definition.

---

## Scanning on a Uses tab

Where a Uses tab shows a barcode column, each cell is a live scanner input.

- **Scan into the cell** — the scanner types the code and sends Enter, which fires the lookup immediately.
- **Click the scanner button** in the cell to open the scanner dialog instead.

This is the workflow for building an order or a pick list from physical stock: put the cursor in the row's barcode cell, scan, and the item resolves.

---

## The scanner dialogs

### Barcode scanner

Opened from the scanner button on a barcode cell. Scan or type a code and MatStream searches the **library** for an item whose barcode property matches exactly, ignoring case, within the categories that tab is configured for.

| Result | What happens |
|---|---|
| Found | The panel flashes **green** with a success tone and shows the item name, a quantity box and **Add to order** |
| Not found | The panel flashes **red** with an error tone and reports `'<code>' not found` |

Setting a quantity and clicking **Add to order** adds the row and clears the input, ready for the next scan. The dialog stays open, so a run of items is one continuous scanning motion.

### Barcode scanner — add items

The richer dialog, used when you are building up a list and may scan the same item more than once. It searches in three steps and stops at the first hit:

1. **The row you have selected** — if it matches, its quantity is updated. *"Same item as selected row — quantity updated."*
2. **The other rows already in the list** — if it matches, that row's quantity is updated. *"Found in list: …"*
3. **The library** — a new row is prepared. *"Found in library: … — not yet in list."*

If none of the three match, the dialog reports `'<code>' not found anywhere`.

The quantity has **+** and **−** steppers beside it, so scanning five of the same item can be one scan and four taps. Confirming either updates the existing row's quantity or adds the new row, then resets for the next scan — the dialog is designed to stay open for the whole picking session.

!!! note "Why the search order matters"
    Checking the selected row and then the visible list *before* the library is what makes re-scanning an item increment it instead of adding a duplicate line.

---

## Setting it up

1. **Create the property.** Configuration → Property definitions → new property, data type **Barcode**. Name it something you will reuse everywhere.
2. **Assign it** to the categories whose items carry barcodes — typically your Part, Assembly or Item definition categories.
3. **Populate it.** Import the codes with an [import profile](import-definitions.md), or set them in bulk from the [Property Browser](property-browser.md).
4. **Show the column** on the Uses tab where scanning happens, so the cells become scanner inputs.
5. **Check the tab's categories.** The dialogs restrict their library search to the categories the tab is configured for — an item in a category outside that list will not be found.

---

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| A scan does nothing | The scanner is not sending Enter as a suffix, or focus is not in the barcode field |
| "Not found" for an item you can see in the Library | The code is on a different property than the one the tab searches, or the item's category is outside the tab's configured categories |
| Scanning the same item adds a second line | You are in the plain **Barcode scanner** dialog, which always adds; use **add items** for increment-on-rescan |
| The value shows as text with no symbol | The property's data type is a plain string, not Barcode |

---

## Related

- [Property definitions](../admin/properties.md) — creating the Barcode property and assigning it to categories
- [Property Browser](property-browser.md) — setting barcode values across many rows at once
- [Uses & Where Used](where-used.md) — the tabs the scanner columns live on
- [Display Tabs](../admin/display-tabs.md) — configuring which categories a Uses tab covers
- [Stock management](stock.md) — what scanned items feed into
