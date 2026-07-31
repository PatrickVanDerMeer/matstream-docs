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

Each transition can be restricted to specific user groups, in the **Permissions** tab of the transition. The rule is deliberately simple, and it has one consequence that surprises people:

- **A transition with no permission rows is open to everyone.**
- **As soon as you add a single permission row, that transition becomes closed by default** — only members of a group granted **Allow** on it can make the move.

So granting a group on one transition does not just open it for that group; it simultaneously closes it for everyone else. The usual pattern is therefore to grant your administrator group on *every* transition first, then open individual paths to the groups that need them.

!!! warning "Deny does not override Allow"
    A permission row set to **Deny** does not block a user who is granted **Allow** through another group. It only fails to grant. Its real effect is to make the transition *restricted* — the same as any other row. If you need to positively block a group, remove their Allow rather than adding a Deny.

### Transition criteria

Transitions can be given **criteria** — conditions such as "all linked items are Released" or a property check.

!!! danger "Criteria are not enforced yet"
    The criteria editor saves your configuration, but **nothing evaluates it at transition time**. A transition with unmet criteria will still succeed. The predefined checks (`All children are Released`, `No children are Obsolete`, `All linked items are Released`, `Has an attached file`, `Has no open tasks`) are currently names and descriptions only — no evaluator has been implemented.

    Until that lands, use **transition permissions** to control who may move an entity, and treat criteria as documentation of intent rather than an enforced rule.

### Transition automations

Transitions can trigger **automations** — actions that happen automatically when the transition is executed, such as raising a material requirement or adding an ordered quantity to stock.

Open the transition's **Automations** tab. Every automation available for lifecycle transitions is listed; tick **On** to attach one. Automations that need configuring show a **gear** next to their name — click it to set their parameters, and a warning triangle appears while a required setting is still missing.

!!! note "A failed automation does not fail the transition"
    Each automation runs inside its own error handler. If one throws, the state change still commits and the failure is written to the API log. So a state that changed successfully is not proof that its automations ran — check the log if the expected side effect is missing.

See [Automations — Administrator Guide](automations-admin.md#feature-automations) for what each built-in automation does.

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

## Seeded lifecycles

### Default Lifecycle

Every new workspace is created with a **Default Lifecycle**:

**States:** Work in Progress *(initial)* → For Review → Approved → Released *(final)*

| From | To |
|---|---|
| Work in Progress | For Review |
| For Review | Approved |
| For Review | Work in Progress (reject) |
| Approved | Released |
| Released | Work in Progress (new revision cycle) |

It is assigned to the base categories Task, Occurrence, File Occurrence, Document, Item, Person and Company. Root and Folder deliberately have no lifecycle.

### Lifecycles added by the Setup Wizard

Depending on which options you tick, the [Setup Wizard](../getting-started/workspace-setup.md) adds more:

| Lifecycle | States | Applied to |
|---|---|---|
| **Engineering Lifecycle** | Work in Progress *(initial)* → For Review → Approved → Released *(final)* → Obsolete | Part, Assembly, Weld Assembly, Instrument, Equipment, WPS |
| **Procurement Lifecycle** | Engineering *(initial)* → Ready for Procurement → Reserved / Ordered → Delivered → Assembled *(final)* | BOM Position, Material Requirement Line |
| **Purchase Order Lifecycle** | Open *(initial)* → Issued → Received → Closed *(final)* | Purchase Order |
| **Project Lifecycle** | In Preparation *(initial)* → Work in Progress → On Hold → Closed *(final)* / Cancelled | Project |
| **Weld Lifecycle** | Work in Progress *(initial)* → Welded → NDT → Accepted *(final)* / Rejected | Weld |
| **Drawing Lifecycle** | Draft → In Review → Released → Superseded *(final)* | Drawing |
| **Milestone Lifecycle** | Planned → At Risk → Reached *(final)* / Missed | Milestone |

!!! note "Naming convention"
    Every seeded lifecycle name ends in **" Lifecycle"**. This is deliberate: without it, most lifecycles shared a name with a category (a *Purchase Order* lifecycle and a *Purchase Order* category), which made the Explorer **Lifecycle** and **Occ. State** columns ambiguous. Follow the same convention for lifecycles you create.

**Engineering Lifecycle is fully connected.** Because engineers may need to move between states in any order, every state pair has a transition — 20 in total — and access is controlled entirely by transition permissions rather than by restricting the paths. See [Users & groups](users-groups.md#engineering-lifecycle-permissions) for the groups it seeds and what each may do.

You can modify any seeded lifecycle or create your own.

!!! warning "Only one final state per lifecycle"
    A lifecycle may have exactly one state marked **Final**. In the Engineering Lifecycle, `Released` is final and `Obsolete` comes *after* it — Obsolete is not marked final. Design accordingly: "final" means the successful end of the process, not the last state in the list.

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