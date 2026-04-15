# Workspaces

A workspace is a shared environment where your team manages documents, parts, and product data together. Everything in MatStream — entities, categories, users, lifecycle states — lives inside a workspace.

---

## Creating a workspace

If you are not yet part of a workspace, MatStream shows a **Welcome** screen after sign-in with a **Create a workspace** button.

Click it and follow the steps to set up your first workspace. You will become the owner and administrator of that workspace.

!!! note
    You can also be invited to an existing workspace by an administrator. In that case you don't need to create one yourself — see [Users & groups](../admin/users-groups.md).

---

## Accessing a workspace

After signing in, MatStream determines which workspace to open:

| Situation | Behaviour |
|---|---|
| Default workspace set | Automatic redirect after 5-second countdown |
| One workspace, no default | Immediate redirect |
| Multiple workspaces, no default | Workspace picker shown |
| No workspace at all | Welcome screen with Create option |

---

## Setting a default workspace

When you are inside a workspace, open the **account menu** (top right, your email address) and click **Set [workspace name] as default**.

MatStream will remember this workspace and redirect you to it automatically on your next sign-in.

!!! tip
    If you belong to multiple workspaces and want to switch, use the account menu to set a different default, or navigate directly via the URL `/w/{workspace-slug}`.

---

## Workspace URL

Each workspace has a unique URL slug, for example:

```
https://app.matstream.com/w/my-company
```

You can share this URL with colleagues who are already members of the workspace.

!!! warning
    Navigating to a workspace URL you are not a member of will show an **Access Denied** page. Contact the workspace administrator to request access.

---

## What's next?

- [Users & groups](../admin/users-groups.md) — invite colleagues to your workspace
- [Categories](../admin/categories.md) — organise your data with categories
