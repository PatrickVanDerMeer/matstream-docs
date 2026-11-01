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
| **Version** | Internal snapshots are created according to the category version-creation policy; some saves update the latest version in place. | Version 1, 2, 3, 4... |
| **Revision** | A formally labelled milestone, created when an entity completes a lifecycle cycle and is officially released. | A, B, C or 1, 2, 3 |

You may save an entity many times (creating many versions) before it
reaches its first formal revision. The revision label can be advanced by a configured lifecycle automation or changed with the permitted Change Revision command.

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

## Version-creation policy

The category controls when an edit creates a new internal version. An unset category policy defaults to **Every change**; there is no workspace default version policy overriding it.

| Policy | Behavior for a qualifying save |
|---|---|
| No versioning | Update the latest version in place |
| Every change | Create a new version |
| Daily | Create a version when the latest version was created on an earlier UTC day; otherwise update in place |
| On editor change | Create a version when the current editor differs from the latest version's creator |
| Daily or editor change | Create a version when either condition applies |

Link settings named **Bump version on link add/remove** concern internal versions, not revision labels. A **Bump Revision** lifecycle feature automation advances the triggering version's revision using its revision scheme. It fails when there is no configured scheme or next value.

## Change Revision

In application versions containing **Change Revision**, select one or more items and choose that command. It requires `entity.revision.change`, read access and permission to modify the item.

1. Review each item's current revision and suggested next revision.
2. Enter a revision of 1–10 characters. The scheme supplies a suggestion; the command accepts a manually entered label.
3. Click **Apply** and review each row's result.
4. If an item changed after the dialog opened, reopen the dialog and review its current version before retrying.

Items are saved individually, so some rows can succeed while others fail. Successful rows are marked Saved. Entering the existing revision is a no-op. The command follows the category's version policy; it does not unconditionally create a new version.

An inherited assembly line is changed on a project-specific copy. This keeps the Library source from being revised through that placement. Newer command availability depends on the deployed UI and API; if the command is missing, ask an administrator about access and application version.

## Viewing revision history

Every entity detail page shows the full revision history — every formal
revision ever created, with:

- The revision label (e.g. A, B, C)
- The lifecycle state at the time of release
- Who created the revision and when
- The property values captured at that revision

This shows what retained versions recorded at each milestone, subject to the category version policy and any historical-version purge.

## Working with a past version

The History tab is not just a list to read. Right-click any row for three
actions:

| Action | What it does |
|---|---|
| **Details** | Opens that version read-only, so you can inspect it without any risk of editing it |
| **Restore** | Brings that version's content forward as a **new** current version |
| **Purge** | Permanently deletes that historic version |

### Restore

Restore never edits in place. It copies the chosen version's content forward
as a new version at the tip, stamped with the reason *"Restored from version
N"*. The versions in between are left exactly as they were, so the history
still shows what happened and when — including the restore itself.

The current version cannot be restored over itself.

### Purge

Purge is permanent and requires permission to delete the item. MatStream refuses unsafe deletion, including these cases:

- **The current version cannot be purged.** Restoring a different historical version creates a new current version; review the consequences before using Restore.
- **An entity must keep at least one version.** The last one always stays.
- **A pinned version cannot be purged.** If a parent occurrence is pinned to
  that specific version, purging it would break the reference — the message
  names how many parents are holding it.

After a purge the entity is re-queued for search indexing, because what is
current may now carry different values.

Other database references can also block deletion. The server rechecks the current-version and reference guards during deletion. Purging a historical version removes its own property and outgoing link rows; an inherited 3D asset is preserved for a later same-file version when necessary.

History may hide Purge when a version has referencing children. Refresh the view after a refusal. Saved lifecycle purge settings and the System Settings Run purge action are separate from manually purging a historical version; see [System settings and purge](../admin/system-settings.md).

## Revisions and files

File attachments in MatStream are linked to specific entity versions.
This means that when you view a historical revision, you see exactly
the files that were attached at that point in time — not the current
files.

This is particularly important for drawings and CAD files, where the
file content must match the revision label precisely for traceability.

!!! note "Revision labels and history"
    Change Revision is a supported command in newer application versions, subject to permissions and modification rules. Do not assume revision labels are immutable or that every label change creates a separate version. Use the category's version policy and lifecycle configuration to meet your traceability requirements.

## Next steps

- [Lifecycle](lifecycle.md) — understand how lifecycle states and
  transitions control when revisions are created
- [Properties](properties.md) — understand how property values are
  captured per version
- [Overview](overview.md) — back to the entities overview
