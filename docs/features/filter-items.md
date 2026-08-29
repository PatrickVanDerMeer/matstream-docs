# Filter Items & Snapshots

A **filter item** is a saved query that lives in the Explorer tree. Instead of re-building the same search every time, you place the question in the structure where it belongs — "all pressure equipment on this project", "every drawing awaiting approval" — and everyone who opens it sees the same list, presented the same way.

A **snapshot** freezes that list, so a released document register means something a year later.

---

## What a filter item is

A filter item is an entity like any other, created from a category with the **Filter Item** behaviour. Its query definition and its column layout are stored on the entity itself, so they travel with it.

In the tree it behaves differently from a normal child on purpose:

- It is **excluded from BOM counts and Uses tabs** — a filter placed in an assembly is not part of that assembly.
- Double-clicking it opens its results in a resizable grid dialog rather than expanding in the tree.

---

## Creating one

1. In the Explorer, right-click the folder or occurrence the filter should live under.
2. Choose **New** and pick a Filter Item category.
3. Name it for the question it answers.
4. Configure the criteria and scope in the dialog that opens.
5. Set the columns you want everyone to see, and save.

---

## Search scope

Scope is the setting that decides *where* the filter looks, and it is worth choosing deliberately.

| Scope | Searches |
|---|---|
| **Explorer subtree** | Placed occurrences under a configured start path. The default, and the fastest — it uses the existing Explorer path index. |
| **Definition BOM** | Follows definition links from placed occurrences, then recurses through library BOM children. Finds definitions that have not yet been materialised as occurrences. |
| **Workspace** | Ignores placement entirely and searches every entity. Useful for manager-level views across projects. |
| **Parent subtree** | Self-anchoring — searches the subtree of wherever the filter itself is placed, resolved when the query runs. |

### Why Parent subtree is useful

With **Parent subtree**, one filter category can be dropped into any project and scope itself to that project, with nothing to configure per instance. Define "open non-conformances" once, place it in every project, and each copy reports on its own project.

!!! warning "A Parent subtree filter must be placed somewhere"
    A filter using this scope that sits at the root — with no parent segment in its path — matches **nothing**, deliberately. The alternative would be an empty path prefix, which matches every placed entity in the workspace and looks convincingly like a working filter.

---

## The Filter Result display tab

The same filter mechanism is also available as a display tab. An administrator adds a **Filter Result** tab to a category, and every entity of that category shows the filtered list in its own context.

Use the tree placement when the list is a thing people navigate to. Use the display tab when the list is an aspect of a record — the documents belonging to this equipment item, the requirements raised for this project.

---

## Snapshots

By default a filter is re-evaluated every time it is opened, so it always reflects the current state. That is right for a working list and wrong for a record.

A **snapshot** captures the membership at a point in time and pins it. The entities that were in the list stay in the list, even if they are later revised, moved or would no longer match.

### Capturing one

Snapshots are captured by lifecycle transition, not by a button, so the freeze happens at a defined moment in a controlled process.

Attach the **Capture Filter Snapshot** automation to the transition that releases the filter's revision. On that transition MatStream recomputes the membership and pins it to the released revision.

The typical use is a document register: the filter lists the documents in scope while the package is being assembled, and releasing the package freezes exactly which documents were in it.

---

## Permissions

A filter item is an entity, so it obeys the same rules as any other: category permissions, lifecycle-state permissions and entity-level grants all apply. The results are filtered by what the viewer is allowed to see — two users opening the same filter can legitimately see different rows.

---

## Related

- [Explorer](explorer.md) — placing filter items in the tree
- [Display Tabs](../admin/display-tabs.md) — adding the Filter Result tab to a category
- [Categories](../admin/categories.md) — the Filter Item category behaviour
- [Automations](automations.md) — configuring Capture Filter Snapshot on a transition
- [Search](search.md) — full-text search, which filters combine with
