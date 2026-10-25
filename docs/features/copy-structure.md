# Copy Structure

Copy Structure creates a new Library item and lets you decide what happens to every item in its BOM. Select one Library row and choose **Copy**, directly after **New** on the ribbon.

The dialog shows the complete BOM as a tree. Each active row supports:

- **Copy** — create a new Library item at version 1 in its category's initial lifecycle state.
- **Reuse** — point the new BOM position at the existing item, preserving an explicit version pin.
- **Replace** — point the new BOM position at another existing Library item.
- **Exclude** — omit the BOM position from the new structure.

The root is always copied. Rows below a reused or excluded parent are disabled because that parent's BOM is not being changed. Use **Copy branch** to copy a row and everything below it, or use the quick actions above the tree.

## Names and properties

When a category has a numbering scheme, the dialog previews the next number and the server allocates the real number only when **Copy** is pressed. You can type a name instead by editing the preview. Unique properties are cleared automatically; expand **Properties to clear** to clear additional values on every copied item.

## Documents

A copied document gets an independent stored file, including independent checkout state. Its 3D viewer asset can remain shared because viewer files are not checked out. References embedded in Inventor `.iam` and `.idw` files are not rewritten, and the dialog warns when those file types are present.

## Safety

The database work runs in one transaction. Every BOM position under a copied parent is a new occurrence entity, so editing Pos Nr, Tag Nr, quantity, or other line data on the copy cannot change the source. Before writing, MatStream verifies the exact source versions, names, row ownership, replacements, and permissions again. If validation or execution fails, no entity or BOM change is committed.

Copied versions record a **Copied From** link to their exact source version. The previous single-item clone endpoint has been removed because it could share BOM-position entities between the source and its copy.

## Explorer projects and folders

Select one project, folder, occurrence, task, milestone, filter item, or photo in Explorer and choose **Copy**. The default destination is the source's current parent; the destination parent can be changed in the dialog. Explorer placements are copied top-down and their paths are rebuilt. Inherited BOM rows are materialized again from each occurrence's selected Library item rather than copied from the old project.

For an occurrence, the **Library item** column can reuse the existing definition, copy it as a new variant, or select a replacement. A copied definition starts at version 1 and gets fresh BOM-position entities; its immediate BOM children are reused by default. Tasks and milestones keep their assignee, duration, and estimated work but clear dates, progress history, completion, expiry, and planning constraints.

Filter settings are copied, while snapshot membership is not. Material requirements, purchase orders, stock locations, timesheet lines, and their downstream records are shown as excluded because physical, procurement, and booked-time records belong to the source project. Photos default to Exclude but can be copied explicitly.
