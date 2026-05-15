# Numbering schemes

Numbering schemes automatically generate unique, human-readable 
identifiers for new entities. Rather than relying on users to manually 
assign numbers — with the risk of duplicates, inconsistencies, and gaps 
— MatStream generates numbers automatically from a configurable template 
the moment an entity is created.

---

## Why numbering schemes matter

A well-designed numbering scheme gives every entity in your catalogue 
a self-describing, unique identifier. A good entity number tells you:

- What type of item it is
- When it was created
- Where it sits in the sequence

For example:

| Number | What it tells you |
|---|---|
| `PP-2026-0042` | Purchased part, created in 2026, 42nd in the sequence |
| `AS-2026-0007` | Assembly, created in 2026, 7th in the sequence |
| `NCR-2026-0134` | Non-conformance report, created in 2026, 134th raised |
| `WI-001` | Work instruction, number 1 in a simple sequence |

Numbers are assigned automatically on entity creation — no user input 
required, no risk of duplicates.

---

## Numbering scheme templates

A numbering scheme is built from a template that combines static text 
and dynamic tokens. The template is evaluated at the moment a new 
entity is created to produce the final number.

### Available tokens

| Token | What it produces | Example |
|---|---|---|
| `{YYYY}` | Four-digit current year | 2026 |
| `{YY}` | Two-digit current year | 26 |
| `{MM}` | Two-digit current month | 04 |
| `{WW}` | Two-digit current week number | 15 |
| `{NNNN}` | Auto-incrementing counter, zero-padded to 4 digits | 0042 |
| `{NNN}` | Auto-incrementing counter, zero-padded to 3 digits | 042 |
| `{NN}` | Auto-incrementing counter, zero-padded to 2 digits | 42 |
| `{N}` | Auto-incrementing counter, no padding | 42 |

### Template examples

| Template | Example output | Typical use |
|---|---|---|
| `PP-{YYYY}-{NNNN}` | PP-2026-0042 | Purchased parts with year |
| `AS-{YYYY}-{NNNN}` | AS-2026-0007 | Assemblies with year |
| `NCR-{YYYY}-{NNNN}` | NCR-2026-0134 | Non-conformance reports |
| `WI-{NNN}` | WI-042 | Work instructions, simple sequence |
| `{YYYY}-{WW}-{NNNN}` | 2026-15-0001 | Week-based numbering |
| `PART-{NN}` | PART-07 | Simple short sequence |

---

## Counter management

Each numbering scheme has its own **counter** — an auto-incrementing 
number that advances each time a new entity is created using that scheme.

### Counter settings

| Setting | What it controls |
|---|---|
| **Start value** | The first number in the sequence (default: 1) |
| **Step** | How much the counter advances per entity (default: 1) |
| **Range end** | An optional maximum value — MatStream warns when approaching the limit |

### Period resets

Counters can be configured to reset automatically at the start of a 
new period. This is useful for date-embedded numbering schemes where 
you want the sequence to restart each year or week.

| Reset period | When the counter resets | Typical use |
|---|---|---|
| **None** | Never — the counter increments indefinitely | Simple sequences |
| **Annual** | At the start of each calendar year | Year-embedded numbers (PP-{YYYY}-{NNNN}) |
| **Weekly** | At the start of each calendar week | Week-embedded numbers ({YYYY}-{WW}-{NNNN}) |

!!! warning "Annual reset and existing numbers"
    When a counter resets annually, the sequence starts again from 
    the configured start value. This means you could have both 
    PP-2025-0042 and PP-2026-0042 in your catalogue — which is 
    intentional and correct, since the year makes them unique. 
    However, if you use a template without a year token and reset 
    annually, you will get duplicate numbers. Always include a 
    year or week token when using period resets.

---

## Assigning numbering schemes to categories

Each category is assigned one numbering scheme. When a user creates 
a new entity in that category, MatStream automatically applies the 
scheme to generate the entity's number.

A single numbering scheme can be assigned to multiple categories — 
but this means they share the same counter and number sequence, which 
is usually not desirable. It is generally better to create a separate 
numbering scheme for each category so that counters are independent.

To assign a numbering scheme to a category:

1. Navigate to **Configuration → Categories**
2. Open the category
3. Select the numbering scheme from the dropdown
4. Save the category

---

## Managing numbering schemes

Numbering schemes are managed under **Configuration → Numbering Schemes**.

### Available actions

| Action | Description |
|---|---|
| **New** | Opens the editor to create a new scheme |
| **Edit** | Opens the editor for the selected scheme |
| **Copy** | Duplicates the selected scheme and opens the editor on the copy |
| **Delete** | Deletes the selected scheme (with confirmation dialog) |
| **Refresh** | Reloads the list from the server |

### Creating a numbering scheme

1. Navigate to **Configuration → Numbering schemes**
2. Click **New**
3. Enter a name for the scheme (e.g. "Purchased parts")
4. Enter the template (e.g. `PP-{YYYY}-{NNNN}`)
5. Configure the counter settings:
   - Start value
   - Step
   - Range end (optional)
   - Period reset (none, annual, weekly)
6. Save the scheme

MatStream shows a preview of what the next generated number will 
look like based on your template and current counter value.

### Copying a numbering scheme

Select a scheme in the list and click **Copy**. MatStream creates a 
duplicate of the scheme with all settings preserved and immediately 
opens the editor on the copy. Rename it and adjust settings as needed, 
then save. The copy is selected in the list after saving.

This is the recommended way to create a new scheme that is similar to 
an existing one — copy, rename, and adjust rather than creating from scratch.

### Editing a numbering scheme

Select a scheme and click **Edit**, or **double-click** any row in the 
list — including rows that are not yet selected. The editor opens for 
the row you double-click, regardless of which row was previously selected.

### Deleting a numbering scheme

Select a scheme and click **Delete**. A confirmation dialog appears 
before the scheme is removed. Schemes that are currently assigned to 
one or more categories cannot be deleted — unassign them from all 
categories first.

---

## Multiple workspaces and numbering

Numbering schemes are scoped to the workspace — numbers generated in 
one workspace are completely independent of numbers in another workspace. 
If your organisation runs multiple workspaces, each workspace maintains 
its own counters.

---

## Best practices

**Use a distinct prefix for each category** — prefixes like PP-, AS-, 
NCR-, WI- make entity numbers self-describing. A user seeing PP-2026-0042 
immediately knows it is a purchased part without needing to look it up.

**Include the year for high-volume categories** — for categories where 
you expect to create many entities, including {YYYY} in the template 
keeps numbers shorter and makes it easy to see at a glance when an 
item was created.

**Use annual resets for year-embedded schemes** — if your template 
includes {YYYY}, configure an annual reset so the counter restarts 
each year and numbers stay short (PP-2026-0042 rather than PP-2026-5842 
after five years of use).

**Keep sequences simple for low-volume categories** — for categories 
where you create only a handful of entities per year, a simple 
`WI-{NNN}` template without a year is often cleaner and easier to 
communicate than `WI-{YYYY}-{NNN}`.

**Do not reuse prefixes** — each category should have a unique prefix. 
Reusing a prefix across categories makes it impossible to identify 
the entity type from the number alone.

**Plan your range** — if you use a fixed-width counter like {NNN} 
(max 999), make sure your expected volume will not exceed that range. 
Use {NNNN} (max 9999) or {N} (unlimited) for high-volume categories.

**Do not change schemes on active categories** — changing the numbering 
scheme on a category that already has entities will not renumber 
existing entities, but new entities will follow the new scheme. 
This creates inconsistency in your catalogue. Plan your numbering 
schemes carefully before creating entities.

---

## Next steps

- [Categories](categories.md) — assign numbering schemes to your 
  entity categories
- [Users & groups](users-groups.md) — control who can create entities 
  and trigger numbering
- [Core concepts](../platform/core-concepts.md) — understand how 
  numbering schemes fit into the broader platform