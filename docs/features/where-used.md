# Uses & Where Used

Every relationship in MatStream is a link between two entities, and the link records *why* the relationship exists. **Uses** looks down from a parent to its children; **Where Used** looks up from a child to everything that depends on it.

Where Used is the question that matters before a change: *if I revise this part, what breaks?*

---

## Uses — looking down

The **Uses** tab shows what an entity contains. It comes in two forms, and an administrator chooses which one a category gets:

| Tab type | Shows |
|---|---|
| **Uses (flat list)** | Direct children only, as a single-level grid |
| **Uses (tree)** | The full multi-level structure, expandable |

Each row carries the quantity and unit of quantity of the relationship, not just the child's identity — so an assembly that uses four of a bolt reads as four.

Relationships can be created, edited and removed from the tab, subject to the lifecycle state of the parent: an entity in a state that forbids modification cannot have its structure changed.

---

## Where Used — looking up

The **Where Used** tab answers the reverse question. It gathers usage from three different places, which is why it finds things a plain BOM query misses:

1. **Direct link parents** — anything that structurally contains this entity: a folder, an assembly, a project occurrence.
2. **Occurrence roll-up** — when a project occurrence is linked to this library definition, Where Used reports the *assembly that contains the occurrence*, not the occurrence itself. The answer you want is "which assemblies are affected", not "which rows exist".
3. **Property references** — entities that point at this one through a reference property, for example a purchase order naming a vendor.

---

## Current structure vs history

Where Used has two modes, and the difference matters when reading old data.

| Mode | What you see |
|---|---|
| **Current** (default) | Only parents whose *current* version contains this entity. A link stamped against a superseded parent version is history and is left out. |
| **Version-scoped** | Usage as it stood for one specific version of this entity — what contained it at that point in time. |

Property references are follow-latest by nature, so they cannot answer a version-scoped question and are dropped from that view rather than shown against every version.

!!! note "History and the Where Used tab give different answers on purpose"
    The History view asks across every link type, including the internal ones. The Where Used tab asks about the structural relationships a user cares about. A row appearing in one and not the other is usually this distinction, not a bug.

---

## Link types

Where a row came from is part of the answer:

| Link type | Meaning |
|---|---|
| **Structural child** | User-created placement — folder to folder, folder to occurrence, occurrence to sub-occurrence, or a library assembly to a library part |
| **Inherited BOM child** | Materialised automatically beneath an occurrence from its library assembly's BOM |
| **Definition link** | The bond between a project occurrence and the library definition it represents |
| **Requirement source** | A material requirement line back to the occurrence that caused it |
| **Fulfilment link** | A purchase-order line to the requirement it fulfils |
| **Filter item** | A saved filter placed in the tree — deliberately excluded from BOM counts and Uses tabs |
| **Snapshot member** | A frozen filter membership row |

---

## Impact analysis in practice

Before revising a released part:

1. Open the part in the Library.
2. Go to **Where Used**.
3. Read the assemblies and projects listed — these are what your change reaches.
4. Check the **version reference mode** on each: occurrences set to *follow latest* will pick the change up, occurrences *pinned* to a version will not, and occurrences following *latest released* will pick it up only once you release.

That last step is the one people miss. Where Used tells you what is connected; the reference mode tells you what will actually move.

---

## Columns

Both tabs use the standard grid machinery: a column chooser, saved layouts, and the three-tier default resolution described in [Grids, columns & defaults](grid-defaults.md). Administrators can seed the starting columns for these tabs so a new user sees a useful view immediately.

---

## Related

- [Entities overview](../entities/overview.md) — definitions, occurrences and how they link
- [Explorer](explorer.md) — where inherited BOM children appear in the tree
- [Display Tabs](../admin/display-tabs.md) — enabling and configuring the Uses and Where Used tabs
- [Revisions](../entities/revisions.md) — versions, revisions and reference modes
- [Grids, columns & defaults](grid-defaults.md) — column layouts on these tabs
