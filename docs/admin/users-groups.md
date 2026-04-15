# Users & Groups

This page explains how to manage users in your workspace — inviting new members, handling email confirmations, and organising users into groups.

---

## User roles in MatStream

Every person who accesses a workspace is a **workspace user**. Users can be organised into **groups** to manage permissions collectively.

---

## Inviting users to your workspace

There are two ways to add users to a workspace: adding them individually or using bulk add.

### Add a single user

Navigate to **Settings → Users** and click **Add user**. Enter the user's details including their email address and click **Save**.

### Bulk add by email

Navigate to **Settings → Users** and click **Add users by email** in the toolbar.

Paste one or more email addresses into the text area, separated by semicolons, commas, or newlines. Outlook-style formats are also supported:

```
alice@company.com
bob@company.com
John Doe <john.doe@company.com>
```

Click **Add Users**. MatStream processes each address and shows a summary:

| Action | Meaning |
|---|---|
| **AddedExistingUser** | Email matched an existing MatStream account. User added and notified by email. |
| **InvitedPlaceholderCreated** | Email not found in MatStream. An invitation to create an account was sent. |
| **AlreadyInWorkspace** | User is already a member of this workspace. No action taken. |
| **InvalidEmail** | The email address format is not valid. |

After reviewing the results, click **Close** to dismiss the dialog. The users grid refreshes automatically.

---

## How invitation emails work

MatStream sends different emails depending on whether the invited person already has an account.

### Existing MatStream user

The user receives an email with a direct link to your workspace:

> **Subject:** You've been added to a MatStream workspace  
> The email contains an **Open workspace** button linking to `app.matstream.com/w/{your-workspace-slug}`.

The user can click the link and sign in immediately — no registration required.

### New user (no MatStream account yet)

The user receives an invitation to create an account:

> **Subject:** You've been invited to MatStream  
> The email contains a **Create account** button linking to the registration page with their email pre-filled.

After registering and confirming their email, they are directed to your workspace automatically.

---

## Email confirmation

All new MatStream accounts require email confirmation before the user can sign in.

After registering, the user receives a **Confirm your registration** email. They must click the **Confirm registration** button to activate their account.

If the user does not receive the confirmation email:

1. Ask them to check their spam or junk folder.
2. On the sign-in screen, they can click **Resend Confirmation Email** to request a new one.

---

## Default workspace for invited users

When a user is added to your workspace, MatStream records their membership. The next time they sign in:

- If they have no default workspace set, MatStream checks their workspace memberships automatically.
- If they belong to only one workspace, they are redirected there immediately.
- If they belong to multiple workspaces, a picker is shown so they can choose.

Users can set their preferred default workspace from the account menu inside any workspace.

---

## Managing groups

Groups allow you to assign permissions to multiple users at once.

Navigate to **Settings → Groups** to create and manage groups. You can:

- Create a new group with a name and description
- Add or remove users from a group
- Assign permissions to the group (applied to all members)

When you add a user via bulk add or individually, you can assign them to one or more groups at the same time.

---

## Disabling and removing users

To remove a user from the workspace, select them in the Users grid and click **Remove**. This removes their workspace membership but does not delete their MatStream account.

To temporarily prevent access without removing the user, edit the user and set **Enabled** to off.

---

## What's next?

- [Categories](categories.md) — control what each group can see and do per category
- [Numbering schemes](numbering.md) — set up automatic numbering for entities
