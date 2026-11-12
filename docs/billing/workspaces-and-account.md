# Managing workspaces and your account

**MatStream Manage** is where you create and look after workspaces, plans, organisations and your own account. This page covers the day-to-day controls and the destructive actions: transferring a workspace, deleting a workspace or an organisation, and deleting your account.

For plans and pricing see [Upgrading your plan](upgrading.md); for invoices see [Invoices](invoices.md).

---

## The workspace card

Each workspace you belong to appears as a card on the **Workspaces** page. A card shows:

- the workspace name, its plan and, if you are not the owner, **Owner: *email*** so you know who to ask;
- a blue notice when a plan change is scheduled, for example *Upgrading to … on 14 Nov 2026*;
- usage figures — **Entities, Versions, Files, File size, Users** and **Categories**. Click the circular arrow to **Refresh stats**;
- the workspace address, which opens the workspace in a new tab;
- for the **owner** only, the actions **Setup Wizard**, **Upgrade plan** (or **Change plan** once on a paid plan), and a trash icon to **Delete workspace**.

Click **+ New workspace** to create another. The **Show the next-steps tour** checkbox beside it turns the first-workspace guidance back on.

### Workspace details

Open a workspace to edit its **Name**, **Slug** (used in the web address) and **Description**. The dialog also shows the **Plan**, **Last activity**, and who created and last modified the workspace and when.

On the **Free** plan the name and slug are locked (a padlock appears with the tooltip *Not editable on FREE*). They unlock when you move to a paid plan. A new workspace's slug is generated from its name and checked for availability as you type: a green tick means it is free, a red cross means it is taken.

### Organisation

The **Organisation** drop-down on the workspace dialog chooses which billing profile — company name, billing address, VAT number — the workspace is invoiced under. Next to it:

| Button | Action |
|---|---|
| **+** | Add a new organisation. See [Organisation settings](organisation.md) for the fields and VAT validation. |
| Pencil | Edit the selected organisation |
| **×** | Delete the selected organisation. If the delete is refused, the dialog shows the reason. |

For a brand-new workspace the organisation controls are disabled until the workspace has been saved once.

---

## Make someone else the owner

Only the **owner** can transfer a workspace. In **User Licenses**, every active member who is not already the owner has a **Make owner** link.

1. Click **Make owner** beside the member.
2. Read the confirmation: *the member will become the owner of the workspace*. It also lists what moves with it.
3. Click **Transfer ownership**.

What changes:

- **You become an Admin** of the workspace.
- **The subscription and its invoices follow the owner.** From now on the new owner is the billing contact.
- **Only the new owner can transfer it back.**

Transferring ownership is also the way to free yourself from a workspace before you [delete your account](#delete-your-account).

---

## Delete a workspace

Only the owner sees the trash icon on the card.

1. Click the trash icon. The **Delete workspace** dialog opens.
2. Read the warning: **this action cannot be undone**, and the workspace and all its configuration are archived immediately.
3. Type the workspace **slug** exactly to confirm, then click **Delete workspace**. A mismatch shows *Slug does not match — please type it exactly.*

Because deletion is permanent, export what you need first. See [Backup and restore](../admin/backup-restore.md).

!!! tip "Free plan limit"
    You can hold only one Free workspace. To move a second workspace to Free you must delete the existing Free workspace first; the plan dialog says so.

---

## Delete your account

Open **Account** in Manage (**Your account**). The **Delete your account** card first checks whether anything stands in the way.

### If you still own workspaces

An account cannot be deleted while it owns a workspace, because a workspace must always have an owner. The card lists each workspace you own and asks you to either **delete it** or **hand it to another member** (*User Licenses → Make owner*). A **Go to workspaces** button takes you there. Once you own nothing, the card shows the delete controls.

### What deleting does

- Your account, sign-in details and membership of every workspace are **permanently removed**.
- **Issued invoices are kept** for legal retention, but you can no longer reach them in Manage.
- **Work you created stays** in its workspace and remains attributed to your name in its history.
- **Files or folders shared with you stop opening.** Whoever shared them can invite you again if you return.
- **Share links sent to your address stop opening.**

### Your API keys

If you created API keys, or keys sign in as your address, the card lists them in a table with a **What happens** choice per key:

| Choice | Result |
|---|---|
| **Revoke** (default) | The key stops working immediately. |
| **Hand to *email*** | Available when another owner exists. The key keeps working under that person. |

Keys marked **signs in as you** act as your address, so leaving them on *Revoke* will break any integration that uses them. See [API keys](../admin/api-keys.md).

### Confirm

Click **Delete my account…**. In the dialog, type your email address exactly, enter your password, and click **Delete my account**. **This cannot be undone.** After it completes you are signed out.

---

## Related

- [Upgrading your plan](upgrading.md)
- [Organisation settings](organisation.md)
- [Editor seats](../admin/editor-seats.md)
- [API keys](../admin/api-keys.md)
