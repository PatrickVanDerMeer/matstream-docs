# Accessing a workspace

This page explains what happens after you sign in — how MatStream 
determines which workspace to open, how to switch between workspaces, 
and how to set a default workspace.

---

## After sign-in

When you sign in, MatStream checks your workspace access and redirects 
you automatically based on the following rules:

| Situation | Behaviour |
|---|---|
| **Default workspace set** | Automatic redirect after a 5-second countdown. Click **Continue** to go immediately. |
| **One workspace, no default** | Immediate redirect to that workspace. |
| **Multiple workspaces, no default** | A workspace picker is shown — select which workspace to open. |
| **No workspace yet** | A welcome screen is shown with an option to create a workspace or wait for an invitation. |

---

## Creating your first workspace

If you are not yet part of any workspace, MatStream shows a welcome 
screen with a **Create a workspace** button.

Click it and follow the steps to set up your workspace. You will 
become the owner and administrator of that workspace.

!!! note
    If you are expecting to be added to an existing workspace by an 
    administrator, you do not need to create one. Once the administrator 
    adds you, MatStream will detect your membership automatically on 
    your next sign-in.

---

## Setting a default workspace

When you are inside a workspace, open the **account menu** (top right, 
your email address) and click **Set [workspace name] as default**.

MatStream remembers this workspace and redirects you to it 
automatically on every subsequent sign-in.

!!! tip
    If you belong to multiple workspaces and want to switch your 
    default, open the account menu in the workspace you want to 
    set as default and click **Set [workspace name] as default**.

---

## Workspace URL

Each workspace has a unique URL based on its slug, for example:

```
https://app.matstream.com/w/my-company
```

You can bookmark this URL or share it with colleagues who are already 
members of the workspace.

!!! warning
    Navigating to a workspace URL you are not a member of will show 
    an **Access Denied** page. Contact the workspace administrator 
    to request access.

---

## Switching between workspaces

If you have access to multiple workspaces, you can switch between them 
by navigating directly to the workspace URL, or by going to the home 
page (`/`) where the workspace picker is shown when no default is set.

To change your default workspace, open the account menu inside the 
workspace you want to switch to and click **Set [workspace name] as default**.

---

## Next steps

- [Workspaces](workspaces.md) — understand what a workspace contains 
  and how users and groups work
- [Inviting users](../admin/inviting-users.md) — add colleagues to 
  your workspace
- [Quick start](quick-start.md) — start working with entities