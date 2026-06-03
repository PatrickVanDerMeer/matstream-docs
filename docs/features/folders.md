# Folders

Folders let you organise entities into a hierarchical tree structure within the workspace. They are visible in the [Explorer](explorer.md) left pane and provide a file-system-like navigation model alongside the flat [Library](library.md) search surface.

---

## What is a folder?

In MatStream, folders are first-class entities. They are stored in the same database tables as parts, assemblies, and documents — they just belong to a Folder family category. This means folders can have properties, lifecycle states, file attachments, and any other behaviour that regular entities have.

---

## Navigating folders

The **Explorer** left pane shows the workspace folder tree. Click a folder to load its contents in the centre pane. Expand a folder by clicking the arrow to see child folders.

The last folder you selected is remembered and restored when you return to the Explorer.

---

## Folder context menu

Right-click any folder in the tree to access:

| Action | Description |
|---|---|
| New Folder… | Create a child folder — you are prompted for a name |
| Add Files… | Upload one or more files directly to this folder |
| Rename | Rename the folder inline |
| Move To… | Move the folder to a different parent (see below) |
| Delete | Delete the folder and all its contents (with confirmation) |
| Change Category… | Reassign the folder entity to a different category |
| Details… | Open the full entity edit dialog to edit properties |

---

## Moving a folder

Select **Move To…** from the folder context menu to open the Move dialog. The full folder tree is shown in the dialog. Select a target folder and click **Move Here**.

**Rules:**
- A folder cannot be moved into itself.
- A folder cannot be moved into any of its descendants (prevents circular nesting).
- Moving a folder to its existing parent does nothing.

After a successful move, the folder tree refreshes to show the new position.

---

## Attaching files to folders

Use **Add Files…** from the context menu to upload files directly to a folder entity. Uploaded files are attached to the folder's current entity version. This is useful for storing project-level documents, specifications, or reference files that belong to a project folder rather than a specific part.

---

## Entity placeholder (file family)

Entities of the **File** family type support an **IsPlaceholder** flag. A placeholder entity reserves a slot in the folder tree without having an attached file yet. This is useful when you know a file will exist (e.g. a drawing is being created) but it is not ready to upload.

Visual indicators in the Explorer grid distinguish placeholders from entities that unexpectedly have no file:

- **Placeholder icon** — intentional placeholder, file not yet available
- **Warning icon** — file entity with no file attached (unintentional gap)

---

## Folder permissions

Folder access follows the same permission model as all other entities: role capabilities, lifecycle-state permissions, and entity-level grants. If a user does not have read access to a folder entity, it does not appear in their Explorer tree.

---

## Related

- [Explorer](explorer.md) — the three-pane browser where folders are visible
- [File & document management](../entities/files.md) — file attachments and check-out
- [Access control](../platform/core-concepts.md) — permission model
