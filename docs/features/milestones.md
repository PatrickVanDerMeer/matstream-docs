# Milestones

A milestone is a dated checkpoint on something — *Design Freeze*, *Delivery*, *Client Approval*. It has a date, a responsible person and a status of its own, and it hangs off the entity it belongs to rather than living in a separate plan.

Milestones are zero-duration by design. A task asks *how long will this take*; a milestone asks *has this happened yet*.

---

## Milestones vs tasks

Both are work items and both are entities, but they answer different questions:

| | Task | Milestone |
|---|---|---|
| Duration | Has estimated hours | Zero — the due date **is** the thing |
| Question | Who is doing this, and how long? | Has this point been reached? |
| Status | Task workflow | Its own lifecycle: Planned → Reached |
| Typical use | "Draft the P&ID" | "Design freeze" |

A milestone can have its own tasks and its own documents, so *Delivery* can carry the checklist and the paperwork that prove it happened.

---

## The milestone lifecycle

Milestones ship with their own lifecycle, and the states are the point of the feature:

| State | Meaning | Colour |
|---|---|---|
| **Planned** | The default on creation — a date has been set and it is expected to hold | Blue |
| **At Risk** | The date is in doubt. This is the state that makes a milestone worth having, because it is visible before the date passes | Orange |
| **Reached** | Done. A final state | Green |
| **Missed** | The date passed without it happening | Red |

Movement between them is deliberately restricted:

```
Planned  ⇄  At Risk
   ↓          ↓
Reached / Missed
```

A milestone can slip into *At Risk* and recover to *Planned*; both can end at *Reached* or *Missed*. Nothing comes back out of a final state.

!!! note "At Risk is a status, not an alarm"
    MatStream does not move a milestone to *At Risk* on its own. Somebody sets it, which is what makes it meaningful — it records a judgement, not a date comparison.

---

## Where milestones appear

Milestones are surfaced through a **Milestones tab** on the host entity. The tab is reusable: it can be assigned to a Project, but equally to an assembly, an equipment item or any category where checkpoints make sense.

The tab shows, by default:

| Column | |
|---|---|
| Icon | The flag icon that marks a milestone |
| Name | *Design Freeze*, *Delivery*, … |
| Lifecycle state | Planned / At Risk / Reached / Missed |
| Due date | The anchor date |
| Assigned to | Who is responsible |

Columns follow the usual rules — see [Grids, columns & defaults](grid-defaults.md).

---

## Creating a milestone

1. Open the host entity — typically a Project — and go to its **Milestones** tab.
2. Click **New Milestone** in the tab's ribbon.
3. Fill in the dialog:

| Field | Required | |
|---|---|---|
| **Name** | Yes | What the checkpoint is |
| **Due date** | No | The date being committed to |
| **Responsible** | No | The workspace user accountable for it |

4. Click **Create**.

The milestone is created against the entity and version you were looking at, so it is tied to that record rather than floating free.

!!! warning "The New Milestone button only appears on a tab named “Milestones”"
    The button is driven by the tab's **name**. Rename the tab to *Key Dates* and the button disappears, even though the tab still works for viewing. If administrators cannot find the button, check the tab name first.

---

## What a milestone is, underneath

Useful to know when configuring one from scratch:

- It is an **occurrence** entity with the **Task** behaviour and a task type of *Milestone*.
- **Versioning is off.** A milestone is a checkpoint, not a controlled document — its history is its lifecycle, not a version chain.
- **File attachment is off** on the milestone itself. Attach evidence to its Documents tab instead.
- It is **hidden from the New dialog**, because the supported way to create one is the New Milestone button, which sets the type and the link correctly.
- It carries a **Description** (long text) property, plus its own **Documents** and **Tasks** tabs.

---

## Setting up milestones

The **Engineering Project** template in the Setup Wizard seeds all of this — the Milestone category, the Milestone Lifecycle with its four states and six transitions, and the Milestones tab on the Project category. If you applied that template, milestones already work.

To add milestones to another category by hand:

1. Confirm the **Milestone** category and **Milestone Lifecycle** exist.
2. Add a Uses tab to the host category, pointing at the Milestone category.
3. **Name the tab `Milestones`** — this is what enables the New Milestone button.
4. Set the tab's default columns, and turn library search off; milestones are created, not picked from a catalogue.

---

## Permissions

A milestone is an entity, so the usual rules apply: category permissions decide who can create one, and lifecycle-state permissions on the Milestone Lifecycle decide who may move it to *Reached* or *Missed*. Restricting those two transitions to a project-lead group is the common arrangement — it keeps "we hit the date" an accountable statement.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/wid/{wid}/tasks/milestones` | Create a milestone against a subject entity and version |

---

## Related

- [Tasks](tasks.md) — work items with duration and assignment
- [Project management](../solutions/project-management.md) — where milestones fit in a project
- [Lifecycle definitions](../admin/lifecycle.md) — editing the Milestone Lifecycle and its transition permissions
- [Display Tabs](../admin/display-tabs.md) — adding and naming the Milestones tab
- [Workspace setup](../getting-started/workspace-setup.md) — what the Engineering Project template seeds
