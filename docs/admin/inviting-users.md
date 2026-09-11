# Inviting users

This page explains how to add users to your workspace — individually 
or in bulk — and what happens when an invitation is sent.

---

## Adding a single user

To add one user to your workspace:

1. Navigate to **Administration → Users**
2. Click **Add user**
3. Enter the user's email address, display name, and other details
4. Assign the user to one or more groups
5. Click **Save**

The user is added immediately. MatStream sends them an email depending 
on whether they already have a MatStream account — see 
[Invitation emails](#invitation-emails) below.

!!! note
    A user with no group memberships has no permissions in the workspace 
    and will see an empty workspace. Always assign at least one group 
    when adding a user.

!!! note "Seats are assigned automatically"
    A new user is given an editor seat if your plan still has one free, 
    and is added as a Viewer if every seat is taken. A Viewer can see 
    the workspace but cannot change anything. See 
    [Editor seats](editor-seats.md).

---

## Bulk add by email

To add multiple users at once:

1. Navigate to **Administration → Users**
2. Click **Add users by email** in the toolbar
3. Paste one or more email addresses into the text area

Addresses can be separated by semicolons, commas, or newlines. 
Outlook-style formats are also supported:

```
alice@company.com
bob@company.com
John Doe <john.doe@company.com>
```

Click **Add Users**. MatStream processes each address and shows a 
result summary:

| Result | Meaning |
|---|---|
| **Added and notified** | Email matched an existing MatStream account. User added and sent a workspace link. |
| **Invited to create an account** | Email not found in MatStream. An invitation to register was sent. |
| **Already a member** | User is already a member of this workspace. No action taken. |
| **Invalid email** | The email address format is not valid. |

After reviewing the results, click **Close**. The users grid 
refreshes automatically.

---

## Invitation emails

MatStream sends different emails depending on whether the invited 
person already has a MatStream account.

### Existing MatStream user

The user receives an email with the subject 
**You've been added to a MatStream workspace**, containing a direct 
link to your workspace. They can click the link and sign in 
immediately — no registration required.

### New user

The user receives an email with the subject 
**You've been invited to MatStream**, containing a link to the 
registration page with their email pre-filled. After registering 
and confirming their email, they are directed to your workspace 
automatically.

---

## What happens after the invitation

When a user is added to your workspace, MatStream records their 
membership. The next time they sign in:

- If they have no default workspace set, MatStream detects their 
  membership automatically
- If they belong to only one workspace, they are redirected there 
  immediately
- If they belong to multiple workspaces, a picker is shown

Users can set their preferred default workspace from the account 
menu inside any workspace.

---

## Disabling and removing users

To temporarily prevent a user from accessing the workspace without 
removing them, edit the user and set **Enabled** to off.

To permanently remove a user from the workspace, select them in the 
Users grid and click **Remove**. This removes their workspace 
membership but does not delete their MatStream account. They will 
lose access immediately.

---

## Next steps

- [Users & groups](users-groups.md) — configure groups and permissions 
  for your workspace users
- [Editor seats](editor-seats.md) — decide who can edit and who can 
  only view
- [API keys](api-keys.md) — set up access for external systems and 
  integrations