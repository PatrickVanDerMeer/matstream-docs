# Editing group membership

The **Edit group membership** dialog allows administrators to assign 
a workspace user to one or more groups, and to remove them from 
groups they no longer need access to.

---

## Opening the dialog

From the **Add workspace user** or **Edit workspace user** dialog, 
click **Manage…** next to the **Group member of** field.

!!! note "New users"
    If the user has not been saved yet, MatStream saves them 
    automatically before opening this dialog so that group 
    memberships can be stored. If you cancel the user dialog 
    afterwards, the automatically saved user is removed.

---

## Layout

The dialog is split into two panels:

**Available groups (left)** — all groups in the workspace that the 
user is not currently a member of. You can filter the list by typing 
in the search box above it.

**Member of (right)** — all groups the user currently belongs to, 
along with an optional **Until** date for each membership.

---

## Adding groups

To add a user to one or more groups:

1. Select one or more groups in the **Available groups** list on the 
   left. Hold Ctrl or Shift to select multiple groups.
2. Click **Add >>** to move the selected groups to the **Member of** 
   list on the right.

To add all available groups at once, click **Add all**.

---

## Removing groups

To remove a user from one or more groups:

1. Click a row in the **Member of** grid on the right to select it.
2. Click **<< Remove** to move it back to the **Available groups** list.

To remove all group memberships at once, click **Remove all**.

---

## Temporary access (Until date)

Each group membership in the **Member of** grid has an optional 
**Until** date. If set, the user's membership in that group is 
limited to that date — after which they lose the permissions 
granted by that group.

To set an Until date:

1. Click the **Until** cell in the row for the relevant group.
2. Select a date from the date picker.
3. Leave the field empty for permanent membership (no expiry).

!!! tip
    Use the Until date for contractors, temporary staff, or 
    users who need elevated access for a specific project period. 
    This is more reliable than remembering to remove access manually.

---

## Saving

- **Save** — saves the group memberships immediately without 
  closing the dialog.
- **Save & Close** — saves and closes the dialog. The **Group member 
  of** field in the user dialog updates to show the current group 
  names.
- **Cancel** — discards any unsaved changes to group membership 
  and closes the dialog.

---

## Related

- [Add or edit a workspace user](workspace-user-dialog.md) — the 
  parent dialog where group membership is accessed
- [Users & groups](users-groups.md) — understand how groups and 
  permissions work in MatStream