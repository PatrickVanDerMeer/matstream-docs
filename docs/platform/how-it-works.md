# How it works

This page gives you a practical look at how MatStream works — from 
the moment an item is created to its formal release, revision, and 
eventual retirement. The same flow applies regardless of whether you 
are managing parts, documents, quality records, or any other entity type.

---

## The core loop

Every item in MatStream follows the same fundamental loop:

1. **Create** — an entity is created and assigned a category, a unique 
   number, and an initial lifecycle state
2. **Enrich** — properties are filled in, files are attached, and links 
   to other entities are established
3. **Approve** — the entity moves through lifecycle states via controlled 
   transitions, with permission gates at each step
4. **Release** — the entity is formally released with a revision label, 
   marking a milestone according to configured revision and retention rules
5. **Revise** — when changes are needed, a new revision is created and 
   the loop begins again — with the full history preserved

This loop is the same whether the entity is a mechanical part moving 
through an engineering change process, a procedure document going 
through a quality approval workflow, or a supplier record being 
reviewed by procurement.

---

## Step 1 — Create

When a user creates a new entity they select a **category**. The category 
determines everything about the entity's structure — its properties, 
lifecycle, revision scheme, and numbering scheme.

MatStream automatically assigns a unique number from the numbering 
scheme configured for that category. The entity starts in the initial 
lifecycle state — typically **Draft**.

User selects category → MatStream assigns number → Entity created in Draft state

Numbering is automatic when the category has a numbering scheme; otherwise users supply the name/identifier. Initial state names and required fields come from workspace configuration.

---

## Step 2 — Enrich

With the entity created, users fill in its **properties** — the 
structured data fields defined for that category. They can also:

- **Attach files** — drawings, datasheets, specifications, CAD files, 
  or any other relevant document
- **Add links** — relationships to other entities, such as the 
  assemblies this part belongs to, or the part a drawing describes

A qualifying save follows the category version-creation policy: it either creates a new internal **version** or updates the latest version in place. Every change is the default. See [Version-creation policy](../entities/revisions.md#version-creation-policy).

Fill properties → Attach files → Add links → Save → Category version policy applied

---

## Step 3 — Approve

When the entity is ready for review, a user triggers a **lifecycle 
transition** — for example, submitting it for approval. This moves 
the entity from one lifecycle state to the next.

Each transition is:

- **Controlled** — only permitted transitions are available. You cannot 
  skip states or move backwards unless explicitly configured.
- **Permission-gated** — only users in authorised groups can trigger 
  specific transitions. A submission might be open to all engineers, 
  while approval is restricted to the quality team.
- **Logged** — every transition is permanently recorded — who triggered 
  it, from which state, to which state, and when.

  Submit for review → In Review state → Approved or Rejected → Released or back to Draft

If the entity is rejected it returns to Draft for rework. The full 
transition history is always visible on the entity detail page.

---

## Step 4 — Release

A configured lifecycle transition moves the item into its release state. Revision labels advance only through the configured revision action or automation. State and record permissions determine who can still modify the released item; the caption Released does not itself make it read-only.

Use the released version as the agreed reference, and pin a relationship when it must continue to resolve that exact version. Configure version creation and retention to preserve the records your process needs. See [Revisions](../entities/revisions.md).

---

## Step 5 — Revise

Follow the configured lifecycle path back to an editable state, make the changes and repeat the review process. The destination need not be called Draft. Category version policy determines whether a save creates a separate version.

A configured automation or the permitted **Change Revision** command advances the revision label. Neither an edit nor a state caption alone guarantees a new revision. Previous versions can be reviewed while retained; guarded history purge is available to authorized users. See [Revisions](../entities/revisions.md).

---

## Relationships between entities

Most real-world items do not exist in isolation. A part belongs to 
assemblies. A drawing describes a part. A non-conformance relates to 
a specific part revision. A supplier supplies multiple purchased parts.

MatStream tracks these relationships as **links** between entities. 
Links are bidirectional — when you link entity A to entity B, MatStream 
automatically maintains the reverse relationship.

This powers two key views on every entity:

- **Uses** — what does this entity reference or depend on?
- **Where-used** — what other entities reference this one?

For assemblies, the Uses view expands into a full **multi-level BOM 
tree** — showing the complete assembly structure, lazy-loaded on demand 
for performance on large assemblies.
Assembly → Uses → Sub-assembly → Uses → Part
↑
Where-used ←──┘

---

## Permissions at every level

MatStream applies permissions at multiple levels, giving administrators 
fine-grained control over who can do what:

| Level | What it controls |
|---|---|
| **Workspace grants** | Broad surfaces and operations a group can use (Library, entity update, stock, procurement, administration, AI) |
| **Lifecycle state permissions** | What a group can do when an entity is in a specific state |
| **Transition permissions** | Which groups can trigger specific lifecycle transitions |
| **Property permissions** | Which groups can see specific properties |
| **Tab permissions** | Which groups can see specific display tabs |
| **Entity permission list** | Per-entity access rules for sensitive or project-scoped items |

This layered model means you can express complex real-world permission 
requirements — for example, engineers can edit Draft entities but not 
Released ones, and only the quality team can trigger the Release transition.

Workspace grants are enforced by the API; client-side hidden tabs and disabled buttons only make
the same policy easier to understand. See [Users and groups](../admin/users-groups.md#grants).

---

## The audit trail

MatStream records entity and version changes. The retained detail depends on the category version policy and any permitted historical-version purge:

| Event | What is recorded |
|---|---|
| **Save** | Property values and modification metadata; a new version when the category policy calls for one |
| **Lifecycle transition** | From state, to state, who triggered it, and when |
| **File attachment** | File name, version it was attached to, who attached it |
| **Link added or removed** | Which entities were linked, who made the change, and when |

Some policies update the current version in place, and authorised users can permanently purge eligible historical versions. Configure versioning and retention to match your traceability requirements rather than assuming an immutable record of every save.

---

## A practical example

Here is how the same core loop looks across two different use cases:

**Engineering part (PLM):**
Part created (PP-2026-0042, Purchased Part category)
→ Material, supplier, lead time filled in
→ Datasheet PDF attached
→ Submitted for review by engineer
→ Approved by quality team
→ Released as revision A
→ Linked to three assemblies that use it
→ Six months later — supplier changes
→ New revision B created, supplier updated, re-approved, re-released

**Controlled procedure (Document Control):**
Procedure created (PROC-2026-0018, Procedure category)
→ Content written, effective date filled in
→ PDF attached
→ Submitted for review by author
→ Approved by department manager
→ Released as issue 1
→ Linked to the process it supports
→ Annual review due — new issue 2 created, content updated, re-approved

Same platform. Same core loop. Different category configuration.

---

## Next steps

- [Core concepts](core-concepts.md) — the building blocks in detail
- [Solutions](../solutions/plm.md) — see how MatStream applies to 
  your specific use case
- [Quick start](../getting-started/quick-start.md) — get hands-on 
  with the platform
- [Entities](../entities/overview.md) — a deeper look at the entity model
