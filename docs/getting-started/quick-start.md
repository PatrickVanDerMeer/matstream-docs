# Quick start

Create a part, fill in its properties, link it to an assembly, and move it through your workspace's review process. Names below are examples: categories, properties, numbering and lifecycle transitions are configured by your administrator.

Before starting, you need a MatStream account, workspace access, an Editor seat and the grants for the actions you perform. Administrators can begin with [Workspace setup](workspace-setup.md).

## Step 1 — Open your workspace

Use **Library** for catalogue search and **Explorer** for folders and placed structures. **Configuration** appears for people with grants to manage its settings; **User Access** manages users and security groups.

## Step 2 — Create a new entity

1. Open **Library** and choose **New**.
2. Select a configured definition category, such as Part or your own Purchased Part category.
3. Fill in the name and required fields, then save.

A numbering scheme supplies a generated number only if assigned to the category. The item starts in its configured initial lifecycle state, which may be Work in Progress rather than Draft.

## Step 3 — Fill in properties

Open the item and its **General** tab. Enter the properties available for that category, for example Description, Manufacturer, Material or Mass. These are examples, not mandatory fields supplied to every category.

Click **Save**. Whether this produces a new internal version depends on the category's [version-creation policy](../entities/revisions.md#version-creation-policy).

## Step 4 — Attach a file

A file-carrying item has a stored file on its entity version. Supporting documents can instead be separate document entities linked through a configured **Documents** tab. A **Photo Gallery** manages multiple images. These do not require a universal Files tab.

For a folder upload, right-click the folder in **Explorer**, choose **Add Files...**, select the files and categories in the upload queue, and click **Upload**. This creates file entities in that folder. See [Files & documents](../entities/files.md) and [Folders](../features/folders.md#attaching-files-to-folders).

## Step 5 — Submit for review

Select the item and use **Change State**. Choose an available configured transition, review criteria and results, and confirm. A typical engineering process moves Work in Progress to For Review.

Only permitted transitions are offered. Error criteria can block a move; warning criteria can advise without blocking. Notification requires a configured notification or task automation; a review state alone does not notify someone.

## Step 6 — Release the entity

An authorized reviewer uses **Change State** to follow the configured approval/release path. For example, the engineering lifecycle goes through Approved before Released.

Revision advancement and read-only access depend on the category's revision scheme, lifecycle automations and permissions. A state caption called Released does not by itself guarantee a revision bump or prohibit all edits.

## Step 7 — Link to an assembly

1. Open an assembly with a configured **Uses** or **BOM** tab.
2. Choose **Add from Library** if enabled.
3. Find and select the part and review the row quantity and unit.
4. Save the containing item where changes are pending.

The relationship appears in **Where Used** on the part when that tab is configured and you have access. See [Uses & Where Used](../features/where-used.md).

## Step 8 — Make a change

Use the configured lifecycle path to return released work to an editable state. Revision labels advance through the configured automation or permitted **Change Revision** command. There is no universal New revision button that always creates Draft and advances to B on release.

Review the [Revisions](../entities/revisions.md) guide before changing historical or inherited rows. History is subject to version policy and guarded purge; do not assume every save is an immutable snapshot.

## Next steps

- [Entities](../entities/overview.md)
- [Workspace setup](workspace-setup.md)
- [Categories](../admin/categories.md)
- [Lifecycle](../entities/lifecycle.md)
