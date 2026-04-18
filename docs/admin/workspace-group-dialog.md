# Adding and editing groups

The **New Workspace Group** and **Edit Workspace Group** dialogs allow 
administrators to create and configure workspace user groups, manage 
their members, and assign grants.

---

## Opening the dialog

**To create a new group:**

1. Navigate to **Administration → User Access → Workspace User Groups**
2. Click **Create Group** in the toolbar

MatStream immediately creates a new group record in the database with 
the name "New Group" and opens the dialog. The name field is 
pre-selected so you can type a new name straight away.

**To edit an existing group:**

- Double-click a group row in the list, or
- Select a group and click **Edit Group** in the toolbar

---

## Group details

The top section of the dialog contains the core group fields:

**Group Name** — the name of the group as it appears throughout 
MatStream. Must be unique within the workspace.

**Description** — an optional free-text description of the group's 
purpose or the roles it is intended for.

**Enabled** — whether the group is active. Disabling a group removes 
its effect on permissions without deleting any configuration. Members 
remain assigned but their permissions from this group are suspended.

---

## Members tab

The **Members** tab shows the current members of the group in a grid 
with the following columns:

| Column | Description |
|---|---|
| Name | The user's display name |
| First Name | The user's first name |
| Last Name | The user's last name |
| Member Until | Optional date after which the membership expires |

### Adding members

Click the person icon button (top right of the tab area) to open the 
[Edit members dialog](group-members-dialog.md). This shows all workspace 
users and allows you to move users into the group using a dual-panel 
picker.

### Removing members

Select one or more rows in the members grid and click the remove icon 
button (top right of the tab area). The members are removed from the 
grid immediately but the change is not saved to the database until you 
click **Save** or **Save & Close**.

### Member Until date

To set or change the **Member Until** date for a member, click the 
cell in the **Member Until** column and select a date from the picker. 
Leave the field empty for permanent membership with no expiry.

!!! tip
    Use the Member Until date for contractors, temporary staff, or 
    users who need elevated access for a specific project. This is 
    more reliable than remembering to remove access manually.

---

## Grants tab

The **Grants** tab shows the grants currently assigned to the group, 
grouped by area. Grants are the coarse-level access flags that control 
which surfaces and administrative features the group can access.

!!! note "Administrators group"
    The built-in Administrators group always has all grants enabled. 
    The grants tab for the Administrators group is read-only and the 
    edit button is not shown.

### Editing grants

Click the shield icon button (top right of the tab area) to open the 
[Edit grants dialog](group-grants-dialog.md). This shows all available 
grants and allows you to assign or remove them using a dual-panel picker.

Grant changes are not saved to the database until you click **Save** 
or **Save & Close**.

---

## Saving

- **Save** — saves all changes (group details, members, grants) to 
  the database without closing the dialog. The group list refreshes 
  automatically.
- **Save & Close** — saves all changes and closes the dialog.
- **Cancel** — discards all unsaved changes and closes the dialog.

!!! warning "Cancelling a new group"
    If you cancel while creating a new group (before clicking Save), 
    MatStream deletes the group record that was automatically created 
    when the dialog opened. No data is left behind.

---

## Audit trail

The footer of the dialog shows who created the group and when, and 
who last modified it. This information is read-only and maintained 
automatically by MatStream.

---

## Related

- [Edit members dialog](group-members-dialog.md) — add and remove 
  members from a group
- [Edit grants dialog](group-grants-dialog.md) — assign grants to 
  a group
- [Users & groups](users-groups.md) — understand the group 
  permission model