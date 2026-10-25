# Price Lists

A Price List holds sales prices for Library items. One Price List can have several **periods**, each with its own currency, date range, enabled status, and prices. A price in one period never carries over automatically to another.

For example, *Price List Europe* can have one EUR period for 2026 and another for 2027. The periods cannot have overlapping dates. An item without a price in the selected period is unpriced, even if it had a price in an earlier period. A price of `0` is different: it is an intentional zero price.

## Create a Price List and a period

1. In the [Library](library.md), create a definition in the **Price List** category and give it a name.
2. Open the Price List and select its **Price List** ribbon tab.
3. Click **Add Period**. Enter **Valid From**, **Valid Until**, and **Currency**, then click **Save period**.

The Price List tab shows the saved periods in a grid with **Name**, **Currency**, **Valid From**, **Valid Until**, and **Enabled**. The Name combines the Price List name, currency, and dates, for example `Price List Europe - EUR - 1/1/2026 - 1/1/2027`.

Select a period row for the ribbon actions. Double-click a row to open its prices on the **Edit** ribbon tab. You can also choose a period from the dropdown on the Edit ribbon; disabled periods remain selectable there.

!!! note "Periods are saved separately from price edits"
    **Save period**, **Remove Period**, and changes to **Enabled** take effect immediately. **Cancel** in the Price List dialog discards staged *price* edits, but it does not undo one of those already-saved period actions.

## Enable or disable a period

Use the **Enabled** checkbox on the **Price List** tab. A checked period can supply prices to the item's Sales tab and to sales documents whose date falls within that period. An unchecked period stays in the Price List for review and editing, but is excluded from those sales views and price lookups.

If another user changed the period after you opened it, refresh the list before changing Enabled again.

## Edit prices

1. Select the period in the **Edit** ribbon dropdown.
2. Click **Add from Library** to choose an item, then enter its price. You can price different kinds of Library definitions, such as parts, services, or documents, when they are available in the Library search.
3. Edit existing prices in the grid. Use its column filters, sorting, and pager to find rows. To remove several prices, select their checkboxes and click **Remove selected**.
4. Click **Save and Close** to store the price changes. Click **Cancel** to discard them.

Prices display with two decimal places, such as `1000,00`. The stored price can have up to four decimal places. A price must be zero or greater.

The price grid shows only one page at a time, but Save and Close works with the complete period. Removing a price row affects only that period; it does not delete the Library item or a price in another period.

## Export and import Excel

Select a period on the **Price List** tab and use **Export to Excel** or **Import from Excel**. The exported workbook has a **Prices** sheet with **Item ID**, **Name**, **Description**, **Unit Price**, **Unit ID**, and **Unit**, plus an **Info** sheet that identifies the Price List and currency. The **Units** sheet lists the available Unit IDs and symbols. Set the Unit ID for every priced row; the Unit column is a readable label.

To copy prices into another period of the *same* Price List:

1. Export the source period and, if needed, edit the prices in Excel.
2. Select the destination period and click **Import from Excel**.
3. Review the preview counts for **added**, **changed**, **unchanged**, and especially **removed** prices.
4. Click **Save and Close** to commit the replacement. Reopen the period to check the result. **Cancel** leaves the saved period unchanged.

Import is a **full replacement**, not a merge. If a previously priced item is omitted from the workbook, or its Unit Price cell is blank, that item will have no price in the destination period. Enter numeric `0` if the intended price is zero.

**Item ID is optional when Name is unique.** Import uses a nonblank Item ID first. If its cell is blank, or the entire Item ID column has been removed, import looks for a Library item with the given Name. If none or several match, the preview shows a row-specific error; enter the correct Item ID to resolve an ambiguous name. An invalid nonblank Item ID is an error and does not fall back to Name.

The workbook must belong to the selected Price List and match the destination period's currency. Hidden rows, formulas in imported fields, duplicate items, inaccessible items, and invalid prices are rejected. Workbooks are limited to 20 MB.

!!! important "Preview is not a save"
    The browser sends the workbook to the API for preview. A successful preview stages its rows in the dialog. **Save and Close** sends those reviewed rows for validation and saving; it does not upload the workbook a second time. The save replaces the period's prices together, so a failed save leaves the previous prices intact.

## View prices from an item's Sales tab

A Library item's **Sales** tab lists its prices in **enabled** Price List periods. A disabled period does not appear there, even though its prices remain in the Price List itself. Prices display with two to four decimal places, preserving small prices such as €0.032/mm.

If you can edit the Sales tab, use the search button in the new row's **Price List** column to choose an available list and period, then enter a Unit Price and choose its Unit of Quantity. You can select several existing rows and use **Remove selected**. Save and Close commits your changes; Cancel discards staged changes.

The tab follows the category's [Display Tab permissions](../admin/display-tabs.md#tab-permissions): users may have edit access, view-only access, or no access to the tab.

## Use prices on quotations and sales orders

Where the document's Pricing tab is configured, choose a Price List. The document date selects an **enabled** period whose date range contains it; the period currency must match the document currency. A new line copies its item price from that period. If no enabled period or no item price matches, the line is unpriced rather than borrowing an old price.

Later edits to a Price List do not silently change saved document lines. On an editable document, deliberately saving a different Price List or date refreshes existing line prices. Issued documents need a revision before their prices can change. See [Opportunities & quotations](opportunities-quotations.md) for the surrounding sales workflow.

Each priced item has a **Unit of Quantity**. New quotation and sales-order lines copy that unit along with the price. Quantity is expressed in the line's unit, and Line Total remains Quantity × Unit Price. Changing a line to a compatible standard unit converts its stored unit price: for example, €32/m becomes €0.032/mm, so 1,673 mm costs €53.54. Refreshing prices also converts into the existing line unit.

Pieces cannot be converted to metres or kilograms without an item-specific conversion. Custom units are usable as-is, but automatic conversions between custom units are unsupported. A conversion requiring more than four price decimals is rejected rather than silently rounding the total. Existing prices with no unit must have a unit assigned before use; no unit is guessed for historical prices.

## Permissions

| Action | Required access |
|---|---|
| Open a Price List or read its periods and prices | `entity.read` and permission to read the Price List |
| Change prices or a period's Enabled status | `entity.update` and permission to modify the Price List |
| Import or replace a price snapshot | `entity.update`, `import.run`, and permission to modify the Price List |
| Export a period to Excel | `entity.read`, `reports.export`, and permission to read the Price List |
| See or edit an item's Sales tab | The category's Sales Display Tab **View** or **Edit** permission, plus applicable entity access |

The API enforces these permissions even if a control is visible in an older browser session.

## If a row is missing after import

Check that you selected the intended destination period, that the preview included the row, and that you clicked **Save and Close** rather than just closing the dialog. Reopen the same period and clear any grid filters. A blank Unit Price means that item was intentionally omitted from the full replacement.

## Related help

- [Library](library.md) — create and find definitions
- [Opportunities & quotations](opportunities-quotations.md) — use prices in sales documents
- [Display Tabs](../admin/display-tabs.md) — configure Sales tab access
