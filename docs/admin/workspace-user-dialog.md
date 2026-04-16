# Add or edit a workspace user

The **Add workspace user** and **Edit workspace user** dialogs allow 
administrators to create new workspace user accounts and update 
existing ones.

---

## Opening the dialog

- To **add** a new user: click **Add User** in the toolbar on the 
  Users page. The dialog opens empty and ready for input.
- To **edit** an existing user: select the user in the grid and click 
  **Edit user**, or double-click the row.

---

## Fields

### Email (required)
The user's email address. This is the login identity for the user 
in MatStream.

- When **adding** a new user, enter the full email address. MatStream 
  validates the format before saving.
- When **editing** an existing user, the email address is read-only 
  and cannot be changed.

### Display name (required)
The name shown throughout MatStream for this user — in audit trails, 
the "Reports to" dropdown, and group membership lists.

When you tab from the Email field to Display name, MatStream 
pre-fills the display name with the email address. You can type 
over it to set a different name.

### First name / Last name
Optional. Used for formal identification and can be displayed in 
reports and exports.

### Enabled
Controls whether the user can sign in to the workspace. When 
unchecked, the user's account is disabled and they cannot access 
the workspace, but their data and history are preserved.

New users are enabled by default.

### Reports to
Optional. Select another workspace user that this user reports to. 
Used for organisational hierarchy and can be displayed in exports.

### Group member of
Shows which groups this user currently belongs to. Groups control 
what the user can see and do in the workspace.

Click **Manage…** to open the group membership editor. See 
[Editing group membership](group-membership-dialog.md) for details.

!!! note "New users and groups"
    If you click **Manage…** before saving a new user, MatStream 
    saves the user automatically first so that group memberships 
    can be assigned. If you then cancel the dialog, the 
    automatically saved user is deleted — no incomplete records 
    are left behind.

---

## Saving

- **Save** — saves changes and keeps the dialog open for further edits.
- **Save & Close** — saves changes and closes the dialog. The user 
  grid refreshes automatically and the saved user is selected.
- **Cancel** — discards all unsaved changes and closes the dialog.

!!! warning
    If a user with the same email address already exists in this 
    workspace, MatStream will show a conflict error. Each email 
    address can only appear once per workspace.

---

## Audit trail

The bottom of the dialog shows who created the user record and when, 
and who last modified it. This information is read-only and maintained 
automatically by MatStream.

---

## Related

- [Group membership dialog](group-membership-dialog.md) — assign 
  users to groups
- [Inviting users](inviting-users.md) — add multiple users at once 
  by email
- [Users & groups](users-groups.md) — understand the group permission 
  model