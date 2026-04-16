# Workspaces

A workspace is your organisation's isolated environment in MatStream. 
Everything in MatStream lives inside a workspace — your entities, users, 
configurations, lifecycle definitions, and settings are all scoped to 
the workspace and completely separated from any other organisation using 
the platform.

Think of a workspace as your company's private instance of MatStream, 
hosted on shared infrastructure but with a strict data boundary around it.

---

## What a workspace contains

| Area | What it includes |
|---|---|
| **Entities** | All parts, assemblies, drawings, and documents your team manages |
| **Users** | Everyone who has access to the workspace, with their roles and group memberships |
| **Groups** | User groups that control permissions and access rights |
| **Categories** | The entity types configured for your workspace |
| **Lifecycle definitions** | The approval workflows assigned to your categories |
| **Numbering schemes** | The templates that generate entity numbers |
| **Revision schemes** | The sequences used for revision labels |
| **Properties** | The custom data fields defined for your entity types |
| **Settings** | Workspace-level configuration and feature toggles |
| **API keys** | Keys for external integrations like the Vault sync plugin |

---

## Users and groups

Every person who accesses MatStream does so through a **workspace user** 
account. This account is specific to the workspace — the same person 
can have different roles and permissions in different workspaces.

Users are organised into **groups**. Groups are the primary mechanism 
for controlling what users can do in the workspace. Rather than 
assigning permissions to individuals, your administrator assigns 
permissions to groups, and users inherit those permissions through 
their group memberships.

A user can belong to multiple groups and will inherit the combined 
permissions of all their groups.

### Common group examples

| Group | Typical permissions |
|---|---|
| **Administrators** | Full access to all configuration and data |
| **Engineers** | Create and edit entities, submit for review |
| **Quality** | Approve lifecycle transitions, view all entities |
| **Procurement** | View released entities and BOM data |
| **Read only** | View all released entities, no editing |

!!! note
    The groups in your workspace are configured by your administrator 
    and may have different names and permissions than the examples above.

---

## Role capabilities

Each group in a workspace carries a set of **role capabilities** — 
broad flags that define what the group is allowed to do. These include 
things like:

- Creating new entities
- Editing entity properties
- Deleting entities
- Triggering lifecycle transitions
- Managing workspace configuration

Role capabilities set the broad rules. Lifecycle state permissions then 
refine those rules further — for example, even if a group has edit 
capability, they may not be able to edit a Released entity because the 
lifecycle state restricts modification.

---

## Multiple workspaces

MatStream supports multiple workspaces on the same platform. 
Organisations can use this to:

- Separate different business units or product lines
- Maintain a test or staging workspace alongside a production workspace
- Give subsidiaries or partners their own isolated environment

Each workspace is completely independent — entities, users, and 
configurations do not cross workspace boundaries.

!!! tip "Which workspace am I in?"
    The workspace name is always shown in the MatStream header. If you 
    have access to multiple workspaces, you can switch between them 
    from your account menu.

---

## API keys

Workspaces can issue **API keys** for service-to-service or integration 
access. This is how the Autodesk Vault sync plugin authenticates with 
MatStream — it uses an API key issued by the workspace administrator 
rather than a personal user account.

API keys are managed by administrators only. See [API keys](../admin/api-keys.md) 
for details.

---

## Next steps

- [Accessing a workspace](workspace-access.md) — how to open, switch, 
  and set a default workspace
- [Quick start](quick-start.md) — get up and running with a practical walkthrough
- [Administration](../admin/categories.md) — configure your workspace 
  categories, groups, and settings