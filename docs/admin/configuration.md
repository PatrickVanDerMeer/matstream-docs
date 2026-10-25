# Configuration

The Configuration section covers the structural settings that define how your workspace behaves — what types of entities exist, what data they carry, how they are numbered, and what approval processes they follow.

Configuration is the responsibility of the workspace administrator. Most configuration is done once during initial setup and then refined over time as your process matures.

## Access to Configuration

The **Configuration** navigation item appears when you have at least one grant for a configuration area. Each tab is controlled independently, so a group can manage one area without receiving access to every workspace setting.

| Configuration tab | Required grant |
|---|---|
| Categories | `catalog.categories.manage` |
| Display Tabs | `catalog.display-tabs.manage` |
| Property Definitions | `catalog.properties.manage` |
| Lifecycle Definitions | `catalog.lifecycle.manage` |
| Revision Schemes | `catalog.revision.manage` |
| Numbering Schemes | `catalog.numbering.manage` |
| Automations | `automation.manage` |
| Print Templates | `print.templates.manage` |
| Import Definitions | `import.definitions.manage` to manage definitions; `import.run` to run them |
| System Settings | `admin.workspace.settings`, `admin.workspace.purge`, or `admin.search-index.rebuild`, depending on the action |

The API enforces these grants even if a request is made without using the MatStream interface. The interface hides or disables unavailable tabs and actions to make the user's access clear. Members of the system **Administrators** group pass all grant checks.

See [Group grants](group-grants-dialog.md) to assign grants and understand how grants from multiple groups are combined.

---

## Configuration areas

### Categories

Categories are the foundation of your workspace. A category defines a type of entity — for example, Purchased Part, Assembly, Work Instruction, or Supplier. Each category carries its own set of properties, lifecycle definition, revision scheme, and numbering scheme.

Everything about an entity's structure is determined by its category. Before creating entities, your administrator must configure the categories that match your organisation's needs.

→ [Categories](categories.md)

### Property definitions

Properties are the data fields that appear on entities. Your administrator defines which properties are available in the workspace and assigns them to categories. Properties can be simple text fields, dates, numbers, booleans, or controlled lists.

→ [Property definitions](properties.md)

### Lifecycle definitions

A lifecycle definition describes the states an entity moves through — from Draft to Released — and the transitions between those states. Each transition can be permission-gated so that only authorised user groups can trigger it.

Lifecycle definitions are reusable — one definition can be assigned to multiple categories.

→ [Lifecycle definitions](lifecycle.md)

### Revision schemes

A revision scheme defines the sequence of labels used when an entity is formally revised — for example A, B, C or 1, 2, 3. Schemes are built from configurable sequences of values and can combine up to three levels with a delimiter character.

→ [Revision schemes](revision-schemes.md)

### Numbering schemes

A numbering scheme is a template that automatically generates unique, human-readable identifiers for new entities. Templates combine static prefixes, date tokens, and auto-incrementing counters.

→ [Numbering schemes](numbering.md)

---

## Recommended configuration order

When setting up a new workspace, configure in this order:

1. **Property definitions** — define the data fields you need before assigning them to categories
2. **Lifecycle definitions** — define your approval workflows before assigning them to categories
3. **Revision schemes** — define your revision label sequences before assigning them to categories
4. **Numbering schemes** — define your numbering templates before assigning them to categories
5. **Categories** — bring everything together by creating categories and assigning the above

This order avoids having to revisit earlier steps when configuring categories.

!!! tip "Start simple"
    It is better to start with a minimal configuration and extend it as you learn what your users need. A simple lifecycle with three states and a straightforward numbering scheme is easier to operate than a complex configuration that tries to cover every edge case from day one.

---

## What is seeded automatically

When a workspace is first created, MatStream seeds a base configuration to give you a starting point — including a default lifecycle, base categories, and common properties. See [Workspace setup](../getting-started/workspace-setup.md) for the full list.

---

## Related

- [Workspace setup](../getting-started/workspace-setup.md) — what is seeded when a workspace is created
- [Categories](categories.md) — the central configuration object that ties everything together
- [Users & groups](../admin/users-groups.md) — configure who can do what in the workspace
