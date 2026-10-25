# Print Templates — Administrator Guide

## What can administrators do with Print Templates?

Administrators can create, manage, and link print templates through the Configuration page. A print template is a Word (.docx) or Excel (.xlsx) file that contains placeholders for data. When a user clicks Print on an entity, MatStream fills those placeholders with real data and downloads a finished PDF or Excel file.

## Where do I manage print templates?

Go to **Configuration** and select the **Print Templates** tab in the ribbon. The grid shows all templates available in your workspace, including system templates provided by MatStream.

The tab and its create, edit, copy, upload, and delete operations require
`print.templates.manage`. Generating output from an assigned template is a separate capability,
`print.render`, so users can print without being allowed to redesign templates.

## What is the difference between a system template and a workspace template?

System templates are provided by MatStream and are visible to all workspaces. They are read-only — you cannot edit or delete them. You can recognise them by the tick in the **System** column.

Workspace templates are templates you have created or copied into your workspace. Only your workspace can see and use them, and you have full control to edit, rename, or delete them.

## How do I create a new template?

1. Go to **Configuration → Print Templates**.
2. Click **New** in the ribbon.
3. Fill in the name, document type (for example "PurchaseOrder" or "PickList"), and choose whether it uses a Word or Excel template file and whether the output should be PDF or Excel.
4. Upload your prepared `.docx` or `.xlsx` template file.
5. Click **Save**.

See the Template Authoring Guide for instructions on how to prepare the Word or Excel file.

## How do I customise a system template?

Because system templates are read-only, you must copy one first. Select the system template in the grid and click **Copy** in the ribbon. A copy is created in your workspace with the word "(copy)" added to the name. You can then edit it freely, upload your own template file, and rename it.

## How do I link a template to a category?

After creating a template, you need to assign it to one or more categories so that it appears in the Print menu for entities of that category.

1. Go to **Configuration → Categories**.
2. Select the category you want.
3. Open the **Print Templates** tab inside the category panel.
4. Click **Add** and pick the template from the list.
5. Optionally set the sort order or mark it as the default for that category.

## What does "Default" mean on a category assignment?

When a template is marked as default for a category, it is pre-selected when the user opens the Print menu. It does not prevent the user from choosing a different template from the list.

## How do I hide a template from the Print menu without deleting it?

In the category's Print Templates tab, select the assignment and untick **Enabled** in the right-hand panel. The template stays assigned but disappears from the Print menu until you enable it again.

You can also set a template itself to **Active = No** in the template editor, which hides it from all categories at once.

## What document types are available?

The Document Type field is a free-text field, but the standard values used in MatStream are: PurchaseOrder, SalesOrder, PickList, PackList, DeliveryNote, Quotation, and Invoice. You can type any custom value if you need a different type.

## Can one template be assigned to multiple categories?

Yes. A template can be assigned to as many categories as you like. Each category assignment has its own sort order, default flag, and enabled state.

## How do I control the order templates appear in the Print menu?

In the category's Print Templates tab, select an assignment and change the **Sort Order** number in the right-hand panel. Lower numbers appear first. Templates with the same sort order are sorted alphabetically by name.

## Can I delete a template?

You can delete workspace templates using the **Delete** button in the ribbon. System templates cannot be deleted. Deleting a template also removes all its category assignments.
