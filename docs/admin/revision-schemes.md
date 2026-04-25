# Revision schemes

A revision scheme defines the sequence of labels used when an entity is formally revised. Revision schemes are configured at the workspace level and assigned to categories. Different categories can use different schemes — for example, mechanical parts might use alphabetic revisions while documents use numeric ones.

---

## System revision schemes

MatStream includes a set of built-in system revision schemes that are available in every workspace. System schemes cannot be edited or deleted, but they can be copied and customised to create your own workspace-specific variants.

System schemes are marked with a lock icon in the revision schemes list.

---

## Revision sequences

Every revision scheme is built from one or more **sequences** — ordered lists of values that define the progression of revision labels. A sequence might be:

- Alphabetic: A, B, C, … Z
- Numeric: 1, 2, 3, …
- Custom: Draft, Prelim, Release, …

A scheme can combine up to three sequences — primary, secondary, and tertiary — separated by a configurable delimiter character. This allows multi-level revision labels such as `A.1`, `B.2`, or `AA.01`.

### Sequence values

Each sequence contains an ordered list of values. When an entity is revised, MatStream advances through the sequence values in order. When the primary sequence is exhausted, it resets and the secondary sequence advances — similar to how an odometer works.

System sequences (marked with a lock icon) are shared across all workspaces and cannot be modified. They can be copied to create workspace-specific variants.

---

## Configuring revision schemes

Navigate to **Administration → Revision Schemes** to manage your workspace revision schemes.

### Creating a new scheme

1. Click **New** in the toolbar
2. Enter a **Name** for the scheme
3. Optionally add a **Description**
4. Set the **Delimiter** character used between sequence levels (default: `.`)
5. Select a **Primary sequence** from the sequences panel on the right
6. Optionally select a **Secondary** and **Tertiary** sequence
7. Click **Save**

### Editing a scheme

Double-click a scheme in the list, or select it and click **Edit**. System schemes open in read-only mode — they cannot be modified.

### Copying a scheme

Select a scheme and click **Copy**. A new scheme is created with the same name suffixed with *(copy)*, pre-filled with the same settings. The copy is a workspace-specific scheme that you can modify freely.

### Deleting a scheme

Select a scheme and click **Delete**. System schemes cannot be deleted. A scheme that is assigned to one or more categories cannot be deleted until it is unassigned.

---

## Managing sequences

The right panel of the revision scheme dialog shows all sequences available in your workspace — both system sequences and workspace-specific ones.

### Creating a new sequence

Click **New** above the sequences grid. A new sequence is created immediately and opened for editing. Enter a name and add the values in order.

### Copying a sequence

Select a sequence and click **Copy**. A copy is created as a workspace-specific sequence that you can modify freely. This is the recommended way to base a custom sequence on a system sequence.

### Editing a sequence

Double-click a sequence to open it. Enter a name and manage the values list — add new values with the **Add** button, remove selected values with **Delete**. System sequences open in read-only mode.

### Deleting a sequence

Select a workspace-specific sequence and click **Delete**. System sequences cannot be deleted.

---

## Assigning a revision scheme to a category

Revision schemes are assigned per category. Navigate to **Administration → Categories**, select a category, and set the **Revision scheme** field in the General tab.

Once assigned, every entity of that category will use the configured scheme for its formal revision labels.

---

## Default revision scheme

When a new workspace is created, MatStream does not assign a revision scheme to categories automatically. Your administrator selects and assigns the appropriate scheme for each category as part of the workspace configuration.

---

## Related

- [Categories](categories.md) — assign revision schemes to categories
- [Revisions](../entities/revisions.md) — understand how revision labels are assigned to entities
- [Numbering schemes](numbering.md) — configure automatic entity numbering