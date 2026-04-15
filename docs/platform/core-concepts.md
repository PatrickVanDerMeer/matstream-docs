# Core concepts

MatStream is built around a small set of concepts that work together 
to form a powerful, flexible platform. Understanding these building 
blocks will help you navigate MatStream confidently — regardless of 
whether you are using it for PLM, document control, quality management, 
or any other use case.

---

## Entity

An entity is the fundamental object in MatStream. It represents any 
item your organisation needs to track and manage.

An entity could be:

- A mechanical part or assembly
- A drawing or technical document
- A controlled procedure or work instruction
- A supplier or manufacturer
- A customer complaint or non-conformance
- A product in your catalogue
- Anything else your organisation needs to manage with structure and traceability

Every entity in MatStream has the same consistent foundation — a unique 
number, a category, a lifecycle state, a revision, properties, files, 
and links to other entities. What differs between entity types is the 
*configuration* — the properties, lifecycle, and relationships defined 
for each category.

!!! tip
    If you can describe it, track it, version it, or approve it — 
    it can be an entity in MatStream.

---

## Category

A category defines the *type* of an entity. It acts as a template that 
determines:

- Which **properties** the entity carries
- Which **lifecycle definition** applies
- Which **revision scheme** is used
- Which **numbering scheme** generates its identifier
- Which **display tabs** appear on its detail page

Examples of categories:

| Category | Typical use |
|---|---|
| Raw Material | A purchased raw material with material grade and supplier properties |
| Purchased Part | A bought component with manufacturer and lead time properties |
| Assembly | A multi-level assembly with BOM children |
| Drawing | A technical drawing linked to a part or assembly |
| Procedure | A controlled work instruction with issue number and effective date |
| Non-conformance | A quality record linked to an affected part revision |

Your administrator defines the categories that match your organisation's 
needs. There are no fixed or hardcoded category types in MatStream.

---

## Property

A property is a structured data field on an entity. Properties capture 
the information your organisation needs to record about each item — 
things like material grade, supplier, weight, approval date, or any 
other attribute relevant to your process.

Properties are defined per category and can be of the following types:

| Type | Description |
|---|---|
| **Text** | Free-form text |
| **Number** | Numeric value, optionally linked to a unit of measure |
| **Date** | A calendar date |
| **Boolean** | A yes/no toggle |
| **List** | A dropdown with controlled allowed values |

Properties can be marked as **required** (must be filled before saving), 
**searchable** (available as filter criteria), or restricted to specific 
user groups.

---

## Version

Every time an entity is saved, MatStream creates a new internal 
**version** record. Versions form the complete audit trail of every 
change ever made to an entity — who changed it, when, and what the 
values were at that point.

Versions are automatic and transparent. You do not need to think about 
them during normal use — MatStream handles versioning in the background.

---

## Revision

A revision is a formally labelled milestone in an entity's history. 
Where versions capture every save, revisions capture official releases 
— the points in time where an entity has been formally approved and 
released.

Revision labels follow a configurable **revision scheme** — for example:

- Alphabetic: A → B → C → ...
- Numeric: 1 → 2 → 3 → ...
- Two-level: AA → AB → ... → AZ → BA → ...

A revision label is permanent and immutable once assigned. It forms 
part of your audit trail and cannot be edited.

---

## Lifecycle state

A lifecycle state describes where an entity currently sits in your 
approval process. Every entity is always in exactly one lifecycle state.

Common lifecycle states include Draft, In Review, Released, and 
Obsolete — but your administrator configures the exact states that 
match your process.

Lifecycle states control:

- What users can **do** with the entity (read, edit, delete)
- Which **transitions** are available (e.g. Submit for review, Release, Reject)
- Who can **trigger** each transition (permission gates per user group)

---

## Lifecycle definition

A lifecycle definition is a reusable configuration of states and 
transitions that can be assigned to multiple categories. Rather than 
configuring each category separately, your administrator defines a 
lifecycle once and reuses it across all categories that follow the 
same approval process.

---

## Revision scheme

A revision scheme defines the sequence of labels used when an entity 
is formally revised. Your administrator configures revision schemes 
at the workspace level and assigns them to categories.

Different categories can use different revision schemes — for example, 
mechanical parts might use alphabetic revisions while documents use 
numeric ones.

---

## Numbering scheme

A numbering scheme is a configurable template that automatically 
generates unique, human-readable identifiers for new entities.

Templates can combine:

- Static text prefixes (e.g. `PP-` for purchased parts)
- Date tokens (e.g. `{YYYY}` for the current year)
- Auto-incrementing counters (e.g. `{NNNN}` for a zero-padded number)

Example: `PP-2026-0042` — a purchased part created in 2026, number 42.

---

## Link

A link is a relationship between two entities. Links are directional — 
entity A *uses* entity B — and MatStream automatically maintains the 
reverse relationship, so entity B knows it is *used by* entity A.

Links power:

- **Uses lists** — what does this assembly use?
- **Where-used lists** — which assemblies use this part?
- **Multi-level BOM trees** — the full assembly structure, expanded 
  on demand
- **Cross-domain relationships** — a non-conformance linked to a part 
  revision, a supplier linked to purchased parts

---

## Workspace

A workspace is your organisation's isolated environment in MatStream. 
All entities, users, configurations, and settings are scoped to the 
workspace. Multiple workspaces can exist on the same platform — 
completely independent of each other.

---

## User and group

Every person who accesses MatStream does so through a **workspace user** 
account. Users are organised into **groups**, which are the primary 
mechanism for controlling permissions. Users inherit the combined 
permissions of all groups they belong to.

---

## How the concepts fit together

Workspace
└── Categories (define structure)
├── Properties (data fields)
├── Lifecycle definition (states + transitions)
├── Revision scheme (A, B, C...)
└── Numbering scheme (PP-2026-0001)
Entity (instance of a category)
├── Versions (every save — full audit trail)
├── Revisions (formal releases — labelled milestones)
├── Lifecycle state (current position in approval process)
├── Property values (the data)
├── Files (attachments)
└── Links (relationships to other entities)
Users → Groups → Permissions

---

## Next steps

- [How it works](how-it-works.md) — see the concepts in action
- [Solutions](../solutions/plm.md) — see how the concepts apply to 
  your use case
- [Quick start](../getting-started/quick-start.md) — get hands-on 
  with the platform