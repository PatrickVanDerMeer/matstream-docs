# Workspace setup

When a new workspace is created in MatStream, the platform automatically seeds a set of base configuration to give you a meaningful starting point. This page describes what is seeded and what you need to configure yourself.

---

## What is seeded automatically

When your workspace is created, MatStream seeds the following:

### Root folder

A root folder entity is created automatically. This is the top-level container in the Explorer folder tree. It cannot be deleted and is not visible as a regular entity — it is the structural anchor for the folder hierarchy.

### Base categories

One base category is created for each entity family type:

| Category | Family type | Notes |
|---|---|---|
| **Folder** | Folder | Used for folder browser entities. No lifecycle assigned. |
| **Task** | Task | General-purpose task category. |
| **Document** | File | For file-based entities. |
| **Occurrence** | Occurrence | For linked occurrence entities. |
| **Item** | Definition | Generic item definition. |
| **Person** | Definition | For person records. |
| **Company** | Definition | For company/supplier records. |

All categories except Folder are assigned the default lifecycle (see below).

!!! note
    These are starting-point categories. Your administrator can add, rename, and configure additional categories to match your organisation's specific requirements.

### Default lifecycle

A lifecycle definition named **Default Lifecycle** is created with the following states and transitions:

**States:**

| State | Role |
|---|---|
| **Work in Progress** | Initial state — entity is being worked on |
| **For Review** | Submitted for review |
| **Approved** | Approved, ready for release |
| **Released** | Final released state |

**Transitions:**

| From | To | Purpose |
|---|---|---|
| Work in Progress | For Review | Submit for review |
| For Review | Approved | Approve |
| For Review | Work in Progress | Reject back for rework |
| Approved | Released | Release |
| Released | Work in Progress | Start a new revision cycle |

All base categories (except Folder) are assigned this lifecycle by default.

!!! note
    The default lifecycle is a starting point. Your administrator can create additional lifecycle definitions and assign them to specific categories to match your approval processes.

### Base properties

The following properties are created and available across all categories:

| Property | Type | Searchable |
|---|---|---|
| **Description** | Text | Yes |
| **Birth Date** | Date | No |
| **Comments** | Memo | Yes |

These properties are not automatically assigned to any category's display tabs — your administrator adds them to the relevant categories via the **Properties** tab in category configuration.

---

## What you need to configure

After workspace creation, your administrator should:

1. **Review and extend categories** — add domain-specific categories for your organisation (e.g. Purchased Part, Assembly, Work Instruction)
2. **Configure properties per category** — assign the relevant properties to each category's display tabs
3. **Configure lifecycle definitions** — create additional lifecycles if different categories need different approval processes
4. **Configure revision schemes** — set up revision schemes and assign them to categories that require formal revision tracking
5. **Configure numbering schemes** — set up automatic numbering for each category
6. **Set up user groups** — configure groups with appropriate permissions and add workspace users

!!! tip "Setup wizard — coming soon"
    A guided setup wizard is planned that will walk administrators through configuring common domain templates — Document Control, PLM, Quality Management, Inventory Management, and Project Management — with pre-configured categories, lifecycles, and schemes appropriate for each domain.

---

## Related

- [Categories](categories.md) — configure category structure, properties, and schemes
- [Users & groups](users-groups.md) — set up user groups and permissions
- [Revision schemes](revision-schemes.md) — configure revision label sequences
- [Numbering schemes](numbering.md) — configure automatic entity numbering
- [Inviting users](inviting-users.md) — add users to your workspace
