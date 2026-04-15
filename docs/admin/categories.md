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
| **Properties** | The data fields available on entities of this type |
| **Lifecycle definition** | The states and transitions entities move through |
| **Revision scheme** | The sequence used for formal revision labels (A, B, C or 1, 2, 3) |
| **Numbering scheme** | The template used to generate unique entity numbers |
| **Display tabs** | Which tabs appear on the entity detail page (General, Uses, Where-Used) |
| **Tab settings** | Column configuration, category filters, and link behaviour per tab |

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
| Raw Material | Engineering workflow | Alphabetic (A, B, C) | RM-{YYYY}-{NNNN} |
| Purchased Part | Engineering workflow | Alphabetic (A, B, C) | PP-{YYYY}-{NNNN} |
| Manufactured Part | Engineering workflow | Alphabetic (A, B, C) | MP-{YYYY}-{NNNN} |
| Assembly | Engineering workflow | Alphabetic (A, B, C) | AS-{YYYY}-{NNNN} |
| Drawing | Drawing workflow | Numeric (1, 2, 3) | DWG-{YYYY}-{NNNN} |

**Quality management:**

| Category | Lifecycle | Revision scheme | Numbering |
|---|---|---|---|
| Non-conformance | NCR workflow | Numeric (1, 2, 3) | NCR-{YYYY}-{NNNN} |
| Corrective action | CAPA workflow | Numeric (1, 2, 3) | CAPA-{YYYY}-{NNNN} |
| Audit finding | Audit workflow | Numeric (1, 2, 3) | AF-{YYYY}-{NNNN} |
| Customer complaint | Complaint workflow | Numeric (1, 2, 3) | CC-{YYYY}-{NNNN} |

**Document control:**

| Category | Lifecycle | Revision scheme | Numbering |
|---|---|---|---|
| Work instruction | Document workflow | Numeric (1, 2, 3) | WI-{YYYY}-{NNN} |
| Quality manual | Document workflow | Numeric (1, 2, 3) | QM-{NNN} |
| Engineering spec | Document workflow | Numeric (1, 2, 3) | ES-{YYYY}-{NNN} |
| Test procedure | Document workflow | Numeric (1, 2, 3) | TP-{NNN} |

---

## Configuring a category

Categories are configured in the **Administration** area of MatStream. 
To create or edit a category:

1. Navigate to **Administration → Categories**
2. Click **New category** or select an existing category to edit
3. Fill in the category settings — name, lifecycle, revision scheme, 
   numbering scheme
4. Configure the **properties** for this category
5. Configure the **display tabs** for this category
6. Save the category

!!! warning "Changing categories on existing entities"
    Changing a category's property set after entities have been created 
    can affect existing data. Adding new properties is safe — existing 
    entities will show the new property as empty. Removing properties 
    is not recommended if entities already have values for that property. 
    Plan your category structure carefully before creating entities.

---

## Configuring properties

Properties are configured within each category. To add a property to 
a category:

1. Open the category in the Administration area
2. Navigate to the **Properties** tab
3. Click **Add property**
4. Configure the property:
   - **Name** — the display name shown to users
   - **Type** — text, number, date, boolean, or list
   - **Required** — whether the property must be filled before saving
   - **Searchable** — whether the property appears as a filter option
   - **Unit of measure** — for numeric properties, the physical dimension
   - **Default value** — an optional pre-filled value for new entities
5. Save the property

### List property values

For list-type properties, you also define the allowed values:

1. Open the property configuration
2. Navigate to the **List values** tab
3. Add each allowed value
4. Optionally set a default value
5. Save

Users will only be able to select from these values — no free-text 
entry is permitted for list properties.

---

## Configuring display tabs

Display tabs control what appears at the bottom of the entity detail 
page. Each category can have one or more tabs configured. Available 
tab types are:

| Tab type | What it shows |
|---|---|
| **General** | The property fields for this entity |
| **Uses** | Entities that this entity links to (BOM children) |
| **Where-Used** | Entities that link to this entity (BOM parents) |

### Configuring a Uses or Where-Used tab

1. Open the category in the Administration area
2. Navigate to the **Display tabs** tab
3. Click **Add tab** and select the tab type
4. Configure the tab settings:
   - **Name** — the label shown on the tab
   - **Default columns** — which columns appear by default
   - **Filter by category** — optionally restrict to specific entity types
   - **Bump version on link add** — automatically advance revision when 
     a link is added
   - **Bump version on link remove** — automatically advance revision 
     when a link is removed
5. Save the tab

!!! tip "Filter by category"
    Use the category filter on Uses and Where-Used tabs to reduce noise 
    for users. For example, an Assembly's Uses tab might be filtered to 
    show only Parts and Sub-assemblies — hiding documents and quality 
    records that are linked but not relevant in a BOM context.

---

## Category permissions

Access to individual display tabs and properties can be restricted 
per user group. This allows you to show different views of the same 
entity to different roles — for example:

- Engineers see all properties and tabs
- Procurement sees only commercial properties (supplier, lead time, price)
- Quality sees only quality-relevant properties and the NCR tab
- Read-only users see released entities with a restricted property set

Tab and property permissions are configured within the category 
settings in the Administration area.

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