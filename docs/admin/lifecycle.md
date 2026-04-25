# Lifecycle definitions

A lifecycle definition describes the states an entity moves through during its life — from initial creation to formal release and beyond. Your administrator configures lifecycle definitions and assigns them to categories. Lifecycle definitions are reusable — one definition can serve multiple categories.

---

## States

A lifecycle definition contains one or more **states**. Every entity is always in exactly one state. States represent the current position of an entity in your approval process.

Each state has:

| Setting | What it controls |
|---|---|
| **Name** | The label shown to users (e.g. Draft, In Review, Released) |
| **Initial** | Whether new entities start in this state. Exactly one state must be marked as initial. |
| **Final** | Whether this is a terminal state. Final states typically represent Released or Obsolete. |
| **Colour** | A visual indicator shown in the UI to help users identify the state at a glance |
| **Sort order** | The display order of states in configuration screens |

### Permissions per state

Each state can carry **state permissions** that control what user groups can do when an entity is in that state:

- **Read** — which groups can view the entity
- **Modify** — which groups can edit the entity
- **Delete** — which groups can delete the entity

This allows you to make a Released entity read-only to most users while still allowing administrators to edit it if needed.

---

## Transitions

Transitions define the allowed movements between states. Without a transition, an entity cannot move from one state to another — the system enforces that only configured transitions are permitted.

Each transition has:

| Setting | What it controls |
|---|---|
| **From state** | The state the entity must be in for this transition to be available |
| **To state** | The state the entity moves to when the transition is triggered |
| **Enabled** | Whether the transition is currently active |

### Transition permissions

Each transition can be restricted to specific user groups. Only users in a permitted group will see the transition button on the entity detail page. If no groups are assigned, the transition is available to all users.

### Transition criteria

Transitions can have **criteria** — conditions that must be met before the transition can be triggered. For example, a transition to Released might require that all required properties are filled in, or that at least one file is attached.

If a criterion is not met, the transition is blocked and the user sees a message explaining what is required.

### Transition automations

Transitions can trigger **automations** — actions that happen automatically when the transition is executed. For example, automatically moving linked entities to a specific state, or sending a notification.

---

## Managing lifecycle definitions

Navigate to **Configuration → Lifecycle definitions** to manage your workspace lifecycles.

### Creating a lifecycle definition

1. Click **New** in the toolbar
2. Enter a **Name** for the lifecycle
3. Click **Save** to create the definition
4. Add **states** in the States tab — at least one state must be marked as Initial
5. Add **transitions** between states in the Transitions tab
6. Configure state permissions and transition permissions as needed

### Editing a lifecycle definition

Select a lifecycle and click **Edit**, or double-click it. Changes to a lifecycle definition take effect immediately for all categories and entities assigned to it.

!!! warning
    Be careful when modifying a lifecycle that is already in use. Removing a state or transition may leave existing entities in a state with no valid transitions. Always review the impact before making structural changes to an active lifecycle.

### Deleting a lifecycle definition

A lifecycle definition that is assigned to one or more categories cannot be deleted until it is unassigned. Select the lifecycle and click **Delete**.

---

## Assigning a lifecycle to a category

Lifecycle definitions are assigned per category. Navigate to **Configuration → Categories**, select a category, and set the **Lifecycle definition** field in the General tab.

All entities of that category will follow the configured lifecycle.

---

## Default lifecycle

When a new workspace is created, MatStream seeds a **Default Lifecycle** with the following states and transitions:

**States:** Work in Progress → For Review → Approved → Released

**Transitions:**

| From | To |
|---|---|
| Work in Progress | For Review |
| For Review | Approved |
| For Review | Work in Progress (reject) |
| Approved | Released |
| Released | Work in Progress (new revision cycle) |

All base categories except Folder are assigned this lifecycle. You can modify the default lifecycle or create additional ones to match your specific approval processes.

---

## Tips for designing lifecycles

Keep lifecycles simple to start. A three or four state lifecycle covers most real-world processes and is much easier to operate than a complex one. Add states and transitions only when your process genuinely requires them.

Use a single lifecycle definition for categories that follow the same approval process — do not create separate identical lifecycles for each category. If two categories differ only in which groups can approve, use the same lifecycle and configure the transition permissions differently per category.

Design for the exception, not just the happy path. Make sure there is always a transition available — including a way to reject or send back to rework — so entities never get stuck in a state with no valid next step.

---

## Related

- [Categories](categories.md) — assign lifecycle definitions to categories
- [Configuration overview](configuration.md) — recommended configuration order
- [Lifecycle](../entities/lifecycle.md) — how users interact with lifecycle states and transitions
- [Users & groups](../admin/users-groups.md) — configure group permissions for transitions