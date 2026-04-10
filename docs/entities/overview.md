# Entities

Entities are the central objects in MatStream. An entity represents any 
item your organisation needs to track — a part, an assembly, a drawing, 
a purchased component, or any other versioned item in your product catalogue.

Everything in MatStream revolves around entities. They carry properties, 
move through lifecycle states, link to other entities, and accumulate a 
full revision history over time.

!!! tip "Coming from Autodesk Vault?"
    Entities in MatStream are the equivalent of Vault items or files. 
    The key difference is that MatStream treats all item types consistently 
    — parts, assemblies, and documents all follow the same model.

## What makes up an entity?

Every entity in MatStream has:

- A **unique number** — generated automatically from a numbering scheme 
  (e.g. `MAT-2026-0042`)
- A **category** — which defines its property set, display tabs, and 
  lifecycle (e.g. Raw Material, Purchased Part, Assembly, Drawing)
- A **revision** — a formally numbered label for the current release 
  (e.g. A, B, 1, 2)
- A **lifecycle state** — where the entity currently sits in your 
  approval process (e.g. Draft, In Review, Released)
- **Properties** — data fields specific to its category, like material, 
  weight, or description
- **Files** — attached documents or CAD files linked to the current version
- **Links** — relationships to other entities it uses or that use it

## Versions vs revisions

These two terms are related but distinct in MatStream:

| Term | What it means |
|---|---|
| **Version** | Every save creates a new internal version record. This is MatStream's audit trail — a complete history of every change. |
| **Revision** | A formally labelled snapshot, using your revision scheme (A, B, C or 1, 2, 3). A new revision is created when an entity completes a lifecycle cycle and is formally released. |

Think of versions as the full history, and revisions as the official 
milestones within that history.

## How entities are organised

Entities live in a **folder tree** — similar to a file system. You can 
browse them in two ways:

- **Library grid** — a flat, searchable table of all entities with 
  column-level filtering and sorting. Best for finding a specific item quickly.
- **Explorer tree** — a hierarchical view showing parent-child 
  relationships between assemblies and their components. Best for 
  navigating assembly structures.

## The entity lifecycle

A typical entity moves through the following stages:

1. **Created** — the entity is created with a category, number, and 
   initial properties. It starts in the Draft state.
2. **Edited** — properties are filled in, files are attached, and links 
   to other entities are established.
3. **Reviewed** — the entity is submitted for review. Only authorised 
   users can approve the transition.
4. **Released** — the entity is formally released with a revision label. 
   It becomes read-only to most users.
5. **Revised** — if changes are needed, a new revision is created and 
   the cycle begins again from Draft.
6. **Obsolete** — the entity is retired and no longer active.

!!! note "Your lifecycle may differ"
    The exact states and transitions depend on how your administrator has 
    configured the lifecycle definition for each category. The stages 
    above are a typical example, not a fixed rule.

## Audit trail

Every entity and version automatically records:

- Who created it and when
- Who last modified it and when

This information is always visible on the entity detail page and cannot 
be edited.

## Next steps

- [Properties](properties.md) — learn how to configure and use entity properties
- [Revisions](revisions.md) — understand how revision schemes work
- [Lifecycle](lifecycle.md) — understand states, transitions, and permissions