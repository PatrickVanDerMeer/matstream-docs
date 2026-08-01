# Categories

Categories are the building blocks of your MatStream configuration.
A category defines the structure of an entity type — what properties
it carries, which lifecycle applies, which revision scheme is used,
and how it is numbered. Every entity in MatStream belongs to exactly
one category.

Think of categories as templates. When a user creates a new entity,
they select a category — and everything about that entity's structure
is determined by that category's configuration.

---

## What a category defines

| Setting | What it controls |
|---|---|
| **Name** | The display name of the category (e.g. Purchased Part, Work Instruction) |
| **Description** | An optional description of the category's purpose |
| **Properties** | The data fields available on entities of this type |
| **Lifecycle definition** | The states and transitions entities move through |
| **Revision scheme** | The sequence used for formal revision labels (A, B, C or 1, 2, 3) |
| **Numbering scheme** | The template used to generate unique entity numbers |
| **Display tabs** | Which tabs appear on the entity detail page (General, Uses, Where-Used) |
| **Tab settings** | Column configuration, category filters, and link behaviour per tab |

---

## Category behaviors

Beyond the family type (Folder, Occurrence, Definition), a category can 
optionally be given a **behavior** — an extra capability layered on top 
of the plain entity model. A category without a behavior is a normal 
entity type; a category with a behavior gets extra data and extra 
built-in logic.

| Behavior | What it adds |
|---|---|
| **Project** | Marks a folder category as a project root. Anything created underneath it — parts, tasks, milestones, sub-folders — can be traced back up to the project it belongs to. |
| **Task** | Gives the category a plan (due date, assigned user, estimated hours) and its own status. Used for both work Tasks and Milestones — a Milestone is simply a Task-behavior category with a zero-duration lifecycle. |
| **Stock Line** | Marks the category as *being* a stock movement. Ordered, received, reserved and shipped quantities are computed live from the stock ledger. Use it for purchase-order and sales-order lines. |
| **Material Requirement** | Marks the category as a statement of *demand* rather than a movement. Required quantity and required-by date are stored on the line itself; nothing is posted to stock until a buyer reserves or orders against it. |
| **Stock Location** | Marks the category as a physical or logical stock location, showing on-hand, reserved, available, and on-order quantities. |
| **Filter Item** | Gives entities of this category a saved, re-runnable search instead of fixed content — used for smart, query-driven folders. |

Most categories don't need a behavior at all. Assign one only when the 
category needs the specific extra data or logic listed above — for 
example, assign **Project** to your top-level project folder category, 
and **Task** to both your Task and Milestone categories.

!!! note "Stock Line vs Material Requirement"
    The distinction matters. **Stock Line** means *"this row's numbers come from the ledger"* — true of a purchase-order line, whose ordered and received quantities are sums of stock movements. A **Material Requirement** is a statement of need: someone has said they want four of something by a date. Nothing has moved in stock, so its quantity lives on the line as a normal property and it posts no ledger entry. Using Stock Line for requirements makes stock appear to be on order when no order exists.

!!! note "Filter Item is still being finished"
    The Filter Item behavior is scaffolded but not yet fully functional 
    in the current build. Avoid relying on it until a future release.

---

## Grouping categories

Categories can be nested under a **parent category** purely to organise the 
list. In the Categories tree you might see *Part*, *Assembly*, *Weld Assembly*, 
*Instrument* and *Equipment* gathered under a parent called **Item**, or 
*Material Requirement Line* and *Purchase Order Line* under **Purchase Order**.

This grouping is presentation only:

- Children do **not** inherit properties, tabs or lifecycles from the parent. Every category stands alone.
- Filtering or searching for the parent does **not** return entities of its children.
- A parent may hold its own entities like any other category — being a parent does not make it abstract.

!!! warning "A parent must be the same family type as its children"
    A Definition category can only be nested under another Definition category, an Occurrence under an Occurrence, and so on. If you set a parent of a different family, the setting is saved but silently ignored — the category simply appears at the top of its own family instead. If a category refuses to nest where you put it, this is almost always why.

You can also drag categories onto one another in the Categories tree to re-parent them.

### Hiding a category from the New dialog

A category used purely as a heading, or one whose entities are only ever 
created by the system, should have **Show in New dialog** unticked in the 
category editor. That removes it from the New dialog, the Change Category 
dialog, document upload and data import — while leaving it fully editable in 
Configuration.

Material Requirement Line and Purchase Order Line are both hidden this way: 
their entities must be created through the procurement process so that the 
matching stock ledger entries are written. One created by hand would show zero 
quantities.

---

## Planning your categories

Before configuring categories in MatStream, it is worth spending time
planning your category structure. A well-designed category structure
makes the platform intuitive for users and reduces the need for
reconfiguration later.

### Start with your entity types

Begin by listing the types of items your organisation needs to manage.
For each type, ask:

- What information do we need to record? (→ properties)
- What approval process does it follow? (→ lifecycle definition)
- How do we number it? (→ numbering scheme)
- How do we label formal revisions? (→ revision scheme)
- Does it link to other entity types? (→ display tabs and links)

### Example category structures

**Engineering / PLM:**

| Category | Lifecycle | Revision scheme | Numbering |
|---|---|---|---|
| Raw Material | Engineering workflow | Alphabetic (A, B, C) | RM{yyyy}{0000} |
| Purchased Part | Engineering workflow | Alphabetic (A, B, C) | PP{yyyy}{0000} |
| Manufactured Part | Engineering workflow | Alphabetic (A, B, C) | MP{yyyy}{0000} |
| Assembly | Engineering workflow | Alphabetic (A, B, C) | AS{yyyy}{0000} |
| Drawing | Drawing workflow | Numeric (1, 2, 3) | DWG{yyyy}{0000} |

**Quality management:**

| Category | Lifecycle | Revision scheme | Numbering |
|---|---|---|---|
| Non-conformance | NCR workflow | Numeric (1, 2, 3) | NCR{yyyy}{0000} |
| Corrective action | CAPA workflow | Numeric (1, 2, 3) | CAPA{yyyy}{0000} |
| Audit finding | Audit workflow | Numeric (1, 2, 3) | AF{yyyy}{0000} |
| Customer complaint | Complaint workflow | Numeric (1, 2, 3) | CC{yyyy}{0000} |

**Document control:**

| Category | Lifecycle | Revision scheme | Numbering |
|---|---|---|---|
| Work instruction | Document workflow | Numeric (1, 2, 3) | WI{yyyy}{000} |
| Quality manual | Document workflow | Numeric (1, 2, 3) | QM{000} |
| Engineering spec | Document workflow | Numeric (1, 2, 3) | ES{yyyy}{000} |
| Test procedure | Document workflow | Numeric (1, 2, 3) | TP{000} |

**Project management** (seeded by the Setup Wizard's Engineering Project configuration):

| Category | Lifecycle | Numbering | Behavior |
|---|---|---|---|
| Project | Project (In Preparation → Work in Progress → On Hold → Closed / Cancelled) | PR{yyyy}{000000} | Project |
| Milestone | Milestone (Planned → At Risk → Reached / Missed) | — | Task |
| Task | — | — | Task |

!!! tip
    Numbering templates use `{yyyy}` for the year and `{0000}`-style 
    zero tokens for the auto-incrementing sequence — see 
    [Numbering schemes](numbering.md) for the full token reference.

---

## Configuring a category

Categories are configured in the **Administration** area of MatStream.
To create or edit a category:

1. Navigate to **Administration → Categories**
2. Click **New** to create a new category, or select an existing category and click **Edit**
3. The category editor dialog opens — fill in the general settings (name, lifecycle, revision scheme, numbering scheme)
4. Navigate to the **Properties** tab to configure which properties this category uses
5. Navigate to the **Display Tabs** tab to configure which tabs appear on the entity detail page
6. Click **Save** to save your changes without closing, or **Save and Close** to save and close the dialog
7. Click **Cancel** to discard all unsaved changes and close the dialog

!!! warning "All changes are held in memory until you save"
    The category editor holds all changes — property assignments, sort
    order, group names, permissions, display tab assignments — in memory
    until you click **Save** or **Save and Close**. Clicking **Cancel**
    or closing the dialog without saving discards all changes. Nothing
    is written to the database until you explicitly save.

!!! warning "Changing categories on existing entities"
    Changing a category's property set after entities have been created
    can affect existing data. Adding new properties is safe — existing
    entities will show the new property as empty. Removing properties
    is not recommended if entities already have values for that property.
    Plan your category structure carefully before creating entities.

---

## Configuring properties

Properties are configured on the **Properties** tab within the category editor.

### Adding a property

1. Open the category editor and navigate to the **Properties** tab
2. Click the **+** button in the toolbar
3. The property picker dialog opens — select one or more properties to add
4. The selected properties appear in the grid immediately (not yet saved)
5. Click **Save** or **Save and Close** to persist the assignment

### Configuring a property assignment

Once a property is assigned, you can configure it directly in the grid:

| Field | What it controls |
|---|---|
| **Group** | An optional heading to group related properties on the entity form |
| **Required** | Whether the property must be filled before an entity can be saved |
| **Default value** | An optional pre-filled value for new entities |

Drag rows to reorder properties. The sort order determines the sequence
in which properties appear on the entity detail page.

### Removing a property

Select the property row and click the **×** button. The removal is
held in memory until you save — click **Cancel** to undo the removal
before saving.

### Property permissions

Select a property in the left grid to configure which workspace groups
can view and edit it. Permissions are loaded lazily when you first
select a property, and are saved when you click **Save** or **Save and Close**.

---

## Configuring display tabs

Display tabs control what appears on the entity detail page for entities
of this category. Each category can have one or more tabs. Available
tab types include:

| Tab type | What it shows |
|---|---|
| **General** | The property fields for this entity |
| **Uses** | Entities that this entity links to (BOM children) |
| **Where-Used** | Entities that link to this entity (BOM parents) |

### Adding a display tab

1. Open the category editor and navigate to the **Display Tabs** tab
2. Click the **+** button in the toolbar
3. The display tab picker dialog opens — select one or more tabs to add
4. The selected tabs appear in the grid immediately (not yet saved)
5. Click **Save** or **Save and Close** to persist the assignment

### Reordering display tabs

Drag rows in the display tab grid to change the order in which tabs
appear on the entity detail page. The new order is held in memory
until you save.

### Removing a display tab

Select the tab row and click the **×** button. The removal is held
in memory until you save.

### Tab permissions

Select a display tab in the left grid to configure which workspace
groups can view and edit it. Permissions are loaded lazily when you
first select a tab, and are saved when you click **Save** or **Save and Close**.

!!! tip "Filter by category"
    For Uses and Where-Used tabs you can configure a category filter
    to reduce noise for users. For example, an Assembly's Uses tab
    might be filtered to show only Parts and Sub-assemblies — hiding
    documents and quality records that are linked but not relevant
    in a BOM context.

---

## Category permissions

Access to individual display tabs and properties can be restricted
per user group. This allows you to show different views of the same
entity to different roles — for example:

- Engineers see all properties and tabs
- Procurement sees only commercial properties (supplier, lead time, price)
- Quality sees only quality-relevant properties and the NCR tab
- Read-only users see released entities with a restricted property set

Tab and property permissions are configured within the category editor.
Select a property or tab in the left grid to open its permissions panel
on the right.

---

## Save, Cancel, and unsaved changes

The category editor uses a deferred save model — no changes are written
to the database until you explicitly click **Save** or **Save and Close**.

| Button | Behaviour |
|---|---|
| **Save** | Saves all changes and keeps the dialog open |
| **Save and Close** | Saves all changes and closes the dialog |
| **Cancel** | Discards all unsaved changes and closes the dialog |

The **Save** and **Save and Close** buttons are disabled when there are
no unsaved changes. The footer shows "Unsaved changes" when changes are
pending.

---

## Best practices

**Keep categories focused** — each category should represent a distinct
entity type with a consistent set of properties. Avoid creating a
single catch-all category with dozens of optional properties.

**Reuse lifecycle definitions** — if multiple categories follow the
same approval process, assign them the same lifecycle definition
rather than creating separate ones. This makes lifecycle changes
easier to manage.

**Use consistent numbering prefixes** — give each category a distinct
prefix in its numbering scheme (PP- for purchased parts, AS- for
assemblies, WI- for work instructions). This makes entity numbers
self-describing.

**Plan property names carefully** — property names are shown to users
and used in search filters. Use clear, consistent names that match
your organisation's terminology.

**Start simple** — begin with the minimum category structure needed
for your initial use case and expand as your needs become clearer.
It is easier to add properties and categories than to restructure
an established catalogue.

---

## Next steps

- [Users & groups](users-groups.md) — configure who can access and
  approve entities in each category
- [Numbering schemes](numbering.md) — configure the numbering templates
  used by your categories
- [Lifecycle](../entities/lifecycle.md) — understand how lifecycle
  definitions work
- [Properties](../entities/properties.md) — understand how properties
  work from a user perspective
- [Project Management](../solutions/project-management.md) — a worked
  example of the Project behavior in action
