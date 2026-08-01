# Tasks

Tasks are work items associated with specific entity versions. They allow team members to track review requests, approval actions, data-entry work, or any other action that needs to happen on an entity. Assigned tasks appear in the assignee's Dashboard Tasks widget.

---

## Task concepts

Each task has:

| Field | Description |
|---|---|
| Entity version | The specific entity version the task is linked to |
| Type | Category of work (e.g. Review, Approval, Data Entry) |
| Assigned to | The workspace user responsible for completing the task |
| Status | Current state of the task (Open, In Progress, Done) |
| % Complete | Optional progress indicator |
| Due date | Optional deadline |
| Linked entity | Optional secondary entity version (e.g. a change order that triggered the task) |

---

## Viewing your tasks

Your outstanding tasks appear on the **Dashboard** Tasks widget when you open the workspace. The widget shows project number, description, state, completion percentage, start date, and due date.

Click any task in the widget to navigate to the linked entity.

---

## Creating a task

Tasks can be created from an entity's detail page:

1. Open the entity.
2. Navigate to the **Tasks** display tab (if configured for that category).
3. Click **New Task**.
4. Fill in the type, assignee, optional due date, and description.
5. Save.

The task is immediately visible to the assigned user on their Dashboard.

---

## Completing a task

1. Open the linked entity.
2. Go to the Tasks tab.
3. Select the task and update its status to **Done** (or set % Complete to 100).
4. Save.

Completed tasks remain visible in the task history but no longer appear in the assignee's open task list.

---

## Task types

Task types are workspace-configured labels that distinguish between different kinds of work. Common examples:

- **Review** — someone needs to review and comment on this entity version
- **Approval** — someone needs to approve a lifecycle transition
- **Data Entry** — properties need to be filled in before the entity can progress
- **Action** — a general action item linked to this entity

Administrators define available task types in workspace settings.

---

## Milestones

Milestones are checkpoints on a Project — a specific, dated event like 
"Design Freeze" or "Delivery" that the project needs to hit. They share 
the same underlying mechanism as Tasks (a due date, a responsible 
person, and a status), but represent a single point in time rather 
than a piece of work with duration.

| Field | Description |
|---|---|
| Name | Short label for the milestone (e.g. "Design Freeze") |
| Due date | The date this milestone needs to be reached |
| Responsible | The workspace user accountable for this milestone |
| State | Current status — see below |

### Milestone states

Milestones follow their own lifecycle, separate from a Task's Open/In 
Progress/Done states:

| State | Meaning |
|---|---|
| **Planned** | The default starting state for a new milestone |
| **At Risk** | Flag a milestone that may not be reached on time |
| **Reached** | The milestone has been achieved (final state) |
| **Missed** | The milestone's due date passed without being reached |

### Creating a milestone

Milestones are created from a Project's **Milestones** tab:

1. Open the Project.
2. Navigate to the **Milestones** tab.
3. Click **New Milestone** in the tab's toolbar.
4. Enter a name, due date, and (optionally) the responsible person.
5. Save.

The new milestone appears immediately in the Milestones tab, showing 
its due date, responsible person, and state as columns.

!!! note
    Milestones are not library items — you don't search for or reuse 
    an existing milestone definition. Each one is created fresh, 
    specific to the project it belongs to.

---

## Related

- [Dashboard](dashboard.md) — the Tasks widget shows your open tasks
- [Lifecycle management](../entities/lifecycle.md) — tasks are often created as part of lifecycle workflows
- [Display Tabs](../admin/display-tabs.md) — add a Tasks or Milestones tab to a category
- [Workspace setup](../getting-started/workspace-setup.md) — the Setup Wizard's Engineering Project configuration seeds Milestones automatically
