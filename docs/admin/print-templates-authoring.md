# Print Templates — Template Authoring Guide

## Overview

Print templates are normal Word (.docx) or Excel (.xlsx) files that contain special placeholders. When a user clicks Print in MatStream, the system replaces those placeholders with real data from the record and produces a finished PDF or filled spreadsheet.

You design the template once in Microsoft Word or Excel, upload it to MatStream, and the system handles all the data merging automatically.

---

## Word templates

### How do I add a placeholder for a single value?

In Microsoft Word, go to **Mailings → Insert Merge Field** and type the field name. The field appears in the document surrounded by double angle brackets, like this: `«Order_PO_Number»`.

The field name has two parts separated by an underscore: the variable name (for example `Order`) and the property name (for example `PO_Number`). The exact names are defined by your developer when they set up the Print button for that record type. Ask your developer for the list of available field names for each document type.

### How do I add a table that repeats for each line item?

Create a table in Word. In the row where you want the data to repeat, add a merge field called `«TableStart:Lines»` before the first cell and `«TableEnd:Lines»` after the last cell. Inside the row, use normal merge fields for each column, for example `«Lines_L_Description»`, `«Lines_L_Qty»`, `«Lines_L_UnitPrice»`.

MatStream will repeat that row once for each item in the list.

Example row structure:

```
«TableStart:Lines» | «Lines_L_ItemNo» | «Lines_L_Description» | «Lines_L_Qty» | «Lines_L_Total» | «TableEnd:Lines»
```

### Can I use formatting, logos, and colours in the template?

Yes. Everything you see in the template — fonts, colours, borders, logos, headers, footers, page numbers — is preserved exactly as you designed it. Merge fields are just replaced with data; the rest of the document is untouched.

### What happens to unused merge fields?

Any merge field that does not match a value in the data is cleared automatically. The field disappears from the output document without leaving a blank placeholder or error.

### What file format should I save the template as?

Save it as `.docx` (Word Document). Do not use `.doc` (old format) or `.dotx` (template format). Upload the `.docx` file to MatStream.

---

## Excel templates

### How do I add a placeholder in Excel?

Type the placeholder directly in a cell using this format: `%VariableName.PropertyName%`. For example: `%Order.PO_Number%` or `%Order.Supplier_Name%`.

The variable name and property name are defined by your developer. Ask for the list of available field names.

### How do I add rows that repeat for each line item?

Put the marker fields for the line items in a single row. MatStream automatically expands that row to produce one row per item. For example:

```
| %Lines.L_ItemNo% | %Lines.L_Description% | %Lines.L_Qty% | %Lines.L_UnitPrice% | %Lines.L_Total% |
```

You do not need to mark the start and end of the repeating section as you do in Word. Any row containing `%VariableName.FieldName%` markers is treated as a repeating row.

### Do Excel formulas still work after the rows are expanded?

Yes. Formulas survive the merge and recalculate after the rows are inserted. For example, a SUM formula at the bottom of a column will correctly total all the expanded rows.

### Does conditional formatting survive?

Yes. Conditional formatting rules applied to the template row are copied to every expanded row. This is useful for highlighting rows where a quantity is zero or a value is out of range.

### Can I include charts?

Yes. Charts that reference table ranges update automatically after the rows are expanded.

### What output format should I use for Excel templates?

If you want users to receive a ready-to-print PDF, set the Output Format to **pdf**. If you want users to receive a filled spreadsheet they can edit before printing, set the Output Format to **xlsx**.

### What file format should I save the template as?

Save it as `.xlsx` (Excel Workbook). Upload the `.xlsx` file to MatStream.

---

## Common questions

### My template looks right but the output is blank. What is wrong?

The most likely cause is that the field names in the template do not exactly match the names expected by MatStream. Field names are case-sensitive. Ask your developer to confirm the exact names for each variable and property.

### Can I update a template without losing the category assignments?

Yes. Go to **Configuration → Print Templates**, select the template, click **Edit**, and upload the new file. The category assignments, sort order, and settings are all preserved. Only the template file itself is replaced.

### Can the same template file be used for different document types?

Technically yes, but it is better practice to create a separate template for each document type so that field names and layout can be tailored appropriately.

### Who defines the field names I can use?

The field names available in a template are determined by what the developer provides in the data model for that entity type. If you need a field that is not currently available, ask your developer to add it to the data provider for the Print button on that page.
