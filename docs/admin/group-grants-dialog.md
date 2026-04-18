# Editing group grants

The **Edit grants** dialog allows administrators to assign and remove 
grants from a workspace user group. Grants are the coarse-level access 
flags that control which surfaces and administrative features the group 
can access.

---

## Opening the dialog

From the **Edit Workspace Group** dialog, select the **Grants** tab 
and click the shield icon button in the top-right corner of the tab.

!!! note "Administrators group"
    The Administrators group always has all grants enabled. The 
    shield button is not shown for system groups — their grants 
    cannot be changed.

---

## Understanding grants

Grants operate at the feature level — they control whether a group 
can open a surface or use an administrative feature at all. They do 
not control which individual entities or records the group can see 
within that surface. That finer level of control is handled by 
category permissions and lifecycle state permissions.

For example, a group with the `library.view` grant can open the 
Library. Which entities they can see and edit inside the Library 
is then determined by the category permissions configured on each 
category.

---

## Layout

The dialog is split into two panels:

**Available grants (left)** — all grants that are not currently 
assigned to this group. Each grant shows its description and the 
area it belongs to. You can filter the list by typing in the 
search box.

**Assigned grants (right)** — the grants currently assigned to 
this group.

---

## Available grants

Grants are organised into four areas:

### Surfaces

| Grant | Description |
|---|---|
| `library.view` | Access the Library — the master catalogue of workspace entities |
| `explorer.view` | Access the Explorer — the hierarchical tree for projects, BOMs, stock locations, and document control |
| `tasks.view` | Access the Tasks surface |
| `changes.view` | Access the Change Orders surface |

### Administration

| Grant | Description |
|---|---|
| `admin.users.manage` | Create, edit and delete workspace users |
| `admin.groups.manage` | Create, edit and delete workspace user groups and manage memberships |
| `admin.workspace.settings` | Manage workspace-level settings |

### Catalog

| Grant | Description |
|---|---|
| `catalog.categories.manage` | Create, edit and delete categories and category trees |
| `catalog.numbering.manage` | Create, edit and delete numbering schemes |
| `catalog.revision.manage` | Create, edit and delete revision schemes |
| `catalog.properties.manage` | Create, edit and delete custom property definitions on categories |
| `catalog.lifecycle.manage` | Create, edit and delete lifecycle definitions, states, and transitions |

### Reports

| Grant | Description |
|---|---|
| `reports.view` | View reports and the audit trail |
| `reports.export` | Export BOMs and reports to Excel or other formats |

---

## Adding grants

To assign one or more grants to the group:

1. Select one or more grants in the **Available grants** list on 
   the left. Hold Ctrl or Shift to select multiple items.
2. Click **Add >>** to move the selected grants to the 
   **Assigned grants** list on the right.

You can also double-click a grant in the left list to add it 
immediately without selecting first.

To assign all available grants at once, click **Add all**.

---

## Removing grants

To remove one or more grants from the group:

1. Click a row in the **Assigned grants** list on the right to 
   select it.
2. Click **<< Remove** to move it back to the **Available grants** list.

You can also double-click a grant in the right list to remove it.

To remove all grants at once, click **Remove all**.

---

## Applying changes

Clicking **Apply** closes the dialog and updates the Grants tab in 
the group dialog. Changes are not saved to the database until you 
click **Save** or **Save & Close** in the group dialog.

Clicking **Cancel** discards all changes made in this dialog.

---

## Related

- [Edit Workspace Group dialog](workspace-group-dialog.md) — the 
  parent dialog where grants are managed
- [Users & groups](users-groups.md) — understand the group 
  permission model