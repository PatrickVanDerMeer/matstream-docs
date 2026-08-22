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
| `PR.2026.000042` | Project, created in 2026, 42nd in the sequence |
| `ITEM-00007` | Item, 7th in the sequence |
| `NCR-2026-00134` | Non-conformance report, created in 2026, 134th raised |

Numbers are assigned automatically on entity creation — no user input 
required, no risk of duplicates.

---

## Numbering scheme templates

A numbering scheme is built from a **Template** that combines static text 
and dynamic tokens, plus an optional **Number Format** mask that reformats 
the result. The template is evaluated at the moment a new entity is 
created to produce the final number.

### Available tokens

Tokens are written in curly braces and are **not** case-sensitive — 
`{yyyy}`, `{YYYY}` and `{Yyyy}` all work.

| Token | What it produces | Example |
|---|---|---|
| `{yyyy}` | Four-digit current year | 2026 |
| `{week}` | Two-digit current ISO week number | 08 |
| `{0...0}` | Auto-incrementing counter, zero-padded to the number of zeros in the token | `{0000}` → 0042 |

These three are the ones offered by the **Insert variable** button. Three 
further aliases also work, though they are not in the picker: `{year}` 
(same as `{yyyy}`), `{iso-week}` (same as `{week}`), and `{month}` — a 
two-digit month, which has no equivalent in the picker at all.

The counter token can be any width — `{00}` pads to 2 digits, `{000000}` 
pads to 6 digits, and so on. Only the *first* zero-padded token in a 
template is treated as the counter.

!!! warning "Unrecognised tokens are printed literally"
    Any other `{...}` text is left in the output exactly as typed — a 
    template of `PO{yy}{0000}` produces `PO{yy}0001`, because `{yy}` is not 
    one of the recognised tokens. There is no error and no warning; the 
    **Preview** column in the numbering schemes grid is where you will see 
    it. Always check the preview after editing a template.

    `{yy}`, `{ww}` and `{weeknr}` are the plausible-looking ones that do 
    **not** print. Worse, they are not entirely inert: `{yy}` still triggers 
    the annual counter reset and `{ww}`/`{weeknr}` still trigger the weekly 
    one (see [Counter resets](#counter-resets)). A template using `{yy}` 
    therefore resets every January while printing no year at all — which 
    produces duplicate numbers. Use `{yyyy}`.

!!! tip "Insert variable"
    The Template field in the numbering scheme editor has an **Insert 
    variable** button that lists all three tokens with a short 
    description. Click one to append it to whatever you've already 
    typed — this is the easiest way to build a template without 
    memorizing the exact token syntax.

### Template examples

| Template | Raw output | Typical use |
|---|---|---|
| `PR{yyyy}{000000}` | PR2026000001 | Projects, year + 6-digit sequence |
| `ITEM-{00000}` | ITEM-00042 | Items, simple 5-digit sequence |
| `{yyyy}-{week}-{0000}` | 2026-08-0001 | Week-based numbering |
| `WI-{000}` | WI-042 | Work instructions, simple sequence |

---

## Number Format — reformatting the raw number

The **Number Format** field is optional and separate from the Template. 
Where the Template controls *what data* goes into the number (year, 
week, sequence), Number Format controls *how it's visually grouped* — 
adding separators like dots or dashes without changing the underlying 
data.

The mask uses only two kinds of characters:

- **`#`** — consumes the next character from the raw (Template) output
- Anything else — a literal character (e.g. `.`, `-`) inserted at that position

For example, a Template of `PR{yyyy}{000000}` produces the raw string 
`PR2026000001` (2 letters + 4-digit year + 6-digit sequence = 12 
characters). A Number Format of `##.####.######` (2 + 4 + 6 = 12 `#` 
placeholders) reformats that into:

```
PR.2026.000001
```

!!! note "Only `#` is supported"
    The mask must use `#` as its placeholder character — not `0`. Use 
    `\` to escape a literal `#` or `\` character if you ever need one 
    as a literal in the output.

!!! tip "The mask consumes letters too"
    `#` consumes the next character of the raw output whatever it is — 
    the prefix letters as much as the digits. That is why the mask in 
    the example above starts with `##`: those two placeholders take the 
    `P` and the `R`. Forget them and the mask will slice the number in 
    the wrong places.

    If the counter grows past the width of its token, the raw output 
    simply gets longer and the mask runs out of placeholders — the 
    leftover characters are appended unformatted (`MR-2026.0999` becomes 
    `MR-2026.10000`, not an error). Widen the counter token if you want 
    fixed-width numbers at that volume.

Leave Number Format blank to use the raw Template output unformatted.

---

## Counter management

Each numbering scheme has its own **counter** — an auto-incrementing 
number that advances each time a new entity is created using that scheme.

### Counter settings

| Setting | What it controls |
|---|---|
| **Index step** | How much the counter advances per entity (default: 1) |
| **Range start** | The first number in the sequence |
| **Range end** | An optional maximum value — MatStream warns when approaching the limit |

### Counter resets

**There is no reset setting.** Resets are decided entirely by the tokens 
in the template — if the template carries a date token, the counter 
resets for that period automatically, and if it does not, the counter 
runs indefinitely.

| Template contains | What happens |
|---|---|
| Neither a year nor a week token | The counter never resets — it increments indefinitely |
| A year token (`{yyyy}`) | The counter resets to **Range start** on the first number issued in a new calendar year |
| A week token (`{week}`) | The counter resets on the first number issued in a new ISO week |
| Both | The counter resets on either boundary |

So `MR{yyyy}{0000}` restarts at 0001 every January without any further 
configuration, while `MR{00000}` counts on forever. Nothing resets at 
midnight on 1 January — the reset happens when the *next number is 
issued*, so a scheme that sits unused until March resets then.

!!! note "Resetting is what makes year-embedded numbers short"
    An annually reset counter means both PR2025000042 and PR2026000042 
    can exist in your catalogue. That is intentional and correct — the 
    year makes them unique. It is also why you must never pair a reset 
    with a template that does not print its date token: see the warning 
    about `{yy}` above.

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
3. Enter a name for the scheme (e.g. "Projects")
4. Enter the template (e.g. `PR{yyyy}{000000}`) — use **Insert variable** 
   to build it without memorizing token syntax
5. Optionally enter a Number Format mask to add separators (e.g. `##.####.######`)
6. Configure the counter settings:
   - Index step
   - Range start / Range end (optional)
7. Save the scheme

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

**Use a distinct prefix for each category** — prefixes make entity 
numbers self-describing. A user seeing PR.2026.000042 immediately 
knows it is a Project without needing to look it up.

**Include the year for high-volume categories** — for categories where 
you expect to create many entities, including `{yyyy}` in the template 
keeps numbers meaningful and makes it easy to see at a glance when an 
item was created.

**Decide resets by choosing your tokens** — there is nothing to 
configure. Including `{yyyy}` gives you a counter that restarts each 
year and numbers that stay short; leaving it out gives you one 
continuous sequence. Pick whichever suits the category and let the 
template do the rest.

**Keep sequences simple for low-volume categories** — for categories 
where you create only a handful of entities per year, a simple 
`WI-{000}` template without a year is often cleaner and easier to 
communicate than one with a year token.

**Do not reuse prefixes** — each category should have a unique prefix. 
Reusing a prefix across categories makes it impossible to identify 
the entity type from the number alone.

**Plan your range** — if you use a narrow counter token like `{000}` 
(max 999), make sure your expected volume will not exceed that range. 
Use a wider token like `{000000}` for high-volume categories.

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
