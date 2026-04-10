# Quick start

This guide walks you through the key concepts of MatStream with a 
practical example. By the end you will have a clear picture of how 
the platform works and be ready to start using it with your team.

The example we will use is creating and releasing a simple purchased 
part — a bearing. This touches all the core concepts: entities, 
properties, lifecycle states, and revisions.

!!! note "Administrator setup required"
    Before you can follow this guide, your workspace administrator 
    needs to have configured at least one category, one lifecycle 
    definition, and one numbering scheme. If you are the administrator, 
    start with the [Administration](../admin/categories.md) section first.

## Step 1 — Open your workspace

When you log in to MatStream you land in your workspace. The main 
navigation gives you access to:

- **Library** — the full catalogue of entities in your workspace
- **Explorer** — a tree view of entities organised in folders and 
  assembly structures
- **Administration** — workspace configuration (administrators only)

## Step 2 — Create a new entity

1. Navigate to the **Library**
2. Click **New entity**
3. Select a **category** — for this example, select *Purchased Part*
4. MatStream automatically assigns a unique number from the numbering 
   scheme configured for that category (e.g. `PP-2026-0001`)
5. Give the entity a name — for example, *Deep groove ball bearing 6205*
6. Click **Save**

The entity is created in the **Draft** lifecycle state. It now exists 
in your workspace and is ready to be filled in.

## Step 3 — Fill in properties

With the entity open, navigate to the **General** tab. This shows all 
the properties defined for the *Purchased Part* category.

Fill in the relevant fields — for example:

| Property | Value |
|---|---|
| Description | Deep groove ball bearing, 25mm bore |
| Manufacturer | SKF |
| Manufacturer part number | 6205-2RS1 |
| Material | Chrome steel |
| Mass (kg) | 0.085 |

Required properties are marked clearly — MatStream will not allow you 
to progress the entity until these are completed.

Click **Save** when done. MatStream creates a new internal version 
capturing all the values you entered.

## Step 4 — Attach a file

If you have a datasheet or drawing for the bearing, you can attach it 
to the entity:

1. Navigate to the **Files** tab on the entity detail page
2. Click **Upload file**
3. Select the file from your computer — for example, a PDF datasheet
4. Click **Save**

The file is now linked to this specific version of the entity. If the 
entity is revised in future, the file attachment history is preserved.

## Step 5 — Submit for review

When the entity is ready for approval, you trigger a lifecycle transition:

1. On the entity detail page, look for the **lifecycle action** button — 
   this shows the available transitions from the current state
2. Click **Submit for review** — this moves the entity from *Draft* to 
   *In Review*
3. MatStream records the transition in the entity's history — who 
   submitted it and when

The entity is now awaiting approval. Depending on your workspace 
configuration, the relevant users will be notified.

!!! note
    The transition button you see depends on your user group permissions. 
    If you do not see a transition option, your group may not have 
    permission to trigger that transition. Contact your administrator.

## Step 6 — Release the entity

A user with approval permissions — for example, a member of the Quality 
group — reviews the entity and approves it:

1. Open the entity in *In Review* state
2. Review the properties and attached files
3. Click **Release** — this moves the entity from *In Review* to *Released*
4. MatStream assigns a formal **revision label** — for example, revision **A**

The entity is now officially released. It becomes read-only to most 
users, and revision A is permanently recorded in the revision history.

## Step 7 — Link to an assembly

Released parts are most useful when they are linked to the assemblies 
that use them. To link the bearing to an assembly:

1. Open the assembly entity
2. Navigate to its **Uses** tab
3. Click **Add link**
4. Search for and select the bearing entity
5. Click **Save**

The bearing now appears in the assembly's Uses list. MatStream 
automatically adds the assembly to the bearing's **Where-Used** list — 
so you can always see which assemblies are affected if the bearing ever 
changes.

## Step 8 — Make a change

Suppose the bearing specification needs to change. Because it is 
Released and read-only, you cannot edit it directly — you need to 
create a new revision:

1. Open the released bearing entity
2. Click **New revision** — MatStream creates a new Draft version of 
   the entity, ready for editing
3. The revision label will advance to **B** when this new revision 
   is eventually released
4. Make your changes, attach updated files, and repeat the 
   review and release process

The full history is preserved — revision A remains permanently in the 
record, and revision B becomes the new current release.

## What you have learned

In this walkthrough you have seen:

- How entities are created and numbered automatically
- How properties capture structured data about an entity
- How lifecycle states control the approval process
- How revision labels are assigned on formal release
- How entities are linked to build BOM structures with where-used traceability
- How changes are managed through new revisions rather than overwriting history

## Next steps

- [Entities](../entities/overview.md) — a deeper look at the entity model
- [Workspaces](workspaces.md) — understand users, groups, and permissions
- [Administration](../admin/categories.md) — configure categories, 
  lifecycle definitions, and numbering schemes for your workspace