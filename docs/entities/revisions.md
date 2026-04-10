# Revisions

A revision is a formally labelled snapshot of an entity at a specific 
point in its lifecycle. Revisions give your team a clear, traceable 
record of every official release of a part, assembly, or document.

!!! tip "Coming from Autodesk Vault?"
    Revisions in MatStream work similarly to Vault's revision labels. 
    The key difference is that MatStream revision schemes are fully 
    configurable — you are not locked into a single lettering or 
    numbering convention.

## Versions vs revisions — a reminder

It helps to keep these two concepts distinct:

| Term | What it means | Example |
|---|---|---|
| **Version** | Every save creates a new internal version. This is the full audit trail of every change ever made to an entity. | Version 1, 2, 3, 4... |
| **Revision** | A formally labelled milestone, created when an entity completes a lifecycle cycle and is officially released. | A, B, C or 1, 2, 3 |

You may save an entity many times (creating many versions) before it 
reaches its first formal revision. The revision label only advances 
when your lifecycle process says it should.

## Revision schemes

A revision scheme defines the sequence of labels used for revisions. 
Your administrator configures revision schemes at the workspace level 
and assigns them to categories.

Common revision schemes include:

| Scheme | Example sequence | Typical use |
|---|---|---|
| **Alphabetic** | A → B → C → ... → Z | Mechanical parts, assemblies |
| **Numeric** | 1 → 2 → 3 → ... | Documents, drawings |
| **Two-level** | AA → AB → ... → AZ → BA | Long-lived items needing many revisions |

Different categories can use different schemes. For example, your 
mechanical parts might use alphabetic revisions while your engineering 
documents use numeric ones.

## When does a revision advance?

A revision label advances when a new revision is formally created. This 
typically happens as part of your lifecycle process — for example, when 
an entity transitions from Released back to Draft to begin a new change 
cycle.

Your administrator configures exactly when and how revisions advance 
as part of the lifecycle definition for each category.

## Automatic revision bumping

MatStream can optionally advance the revision automatically when certain 
events occur on an entity's links — for example, when a child component 
is added or removed from an assembly's Uses list.

This behaviour is configured per display tab by your administrator using 
the **Bump version on link add** and **Bump version on link remove** 
settings. When enabled, this ensures traceability is maintained 
automatically without relying on manual revision management.

## Viewing revision history

Every entity detail page shows the full revision history — every formal 
revision ever created, with:

- The revision label (e.g. A, B, C)
- The lifecycle state at the time of release
- Who created the revision and when
- The property values captured at that revision

This gives you a complete, immutable record of what the entity looked 
like at every official milestone.

## Revisions and files

File attachments in MatStream are linked to specific entity versions. 
This means that when you view a historical revision, you see exactly 
the files that were attached at that point in time — not the current 
files.

This is particularly important for drawings and CAD files, where the 
file content must match the revision label precisely for traceability.

!!! warning "Revision labels cannot be edited"
    Once a revision label has been assigned, it cannot be changed. 
    Revision labels are permanent identifiers and form part of your 
    audit trail. If you need to make changes to a released entity, 
    create a new revision.

## Next steps

- [Lifecycle](lifecycle.md) — understand how lifecycle states and 
  transitions control when revisions are created
- [Properties](properties.md) — understand how property values are 
  captured per version
- [Overview](overview.md) — back to the entities overview