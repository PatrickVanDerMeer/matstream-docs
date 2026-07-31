# Automations — Administrator Guide

Automations allow workspace administrators to define scripted actions that run automatically when events occur in MatStream. This page covers how to manage automations as an administrator. For end-user context and a feature overview, see [Automations](../features/automations.md).

---

## Where to manage automations

Go to **Configuration → Automations**. The grid lists all automations in the workspace:

- **System automations** (gear icon) — provided by MatStream, read-only
- **Custom automations** (person icon) — created by your workspace, fully editable

---

## Creating an automation

1. Click **New** in the ribbon.
2. Fill in **Name**, optionally **Description**.
3. Choose the **Context type** — this determines when the automation fires.
4. Write the script in the Monaco editor.
5. Use the **API Reference** panel on the left to browse available methods and click to insert snippets.
6. Click **Test** to run the script and verify it works.
7. Tick **Enabled** and click **Save**.

---

## Context types

| Context type | Fires when |
|---|---|
| Universal | Available in any context |
| Lifecycle transition | An entity version changes lifecycle state |
| Category lifecycle switch | An entity is switched from one lifecycle to another |
| Inventory | A stock event occurs |
| Scheduled | On a timed schedule |
| On create | A new entity is created |
| On save | An entity's properties are saved |
| On delete / Before delete | An entity is deleted |

---

## Feature automations

Alongside scripted automations, MatStream ships a set of **feature automations** — built-in actions written in the product rather than in script. They appear with a **lightning bolt** icon and cannot be edited or deleted; you attach them to a lifecycle transition and configure their settings.

| Automation | What it does | Status |
|---|---|---|
| **Create Material Requirement** | Raises a Material Requirement line for the occurrence that changed state, linked back to it | Working |
| **PO: Add to On Order** | When a purchase-order line enters Ordered, adds its quantity to On Order | Working |
| **PO: Release from On Order** | When a purchase-order line leaves Ordered, releases the On Order quantity | Working |
| **Bump Revision** | Bumps the entity to its next revision | Not implemented |
| **Set Assignee** | Assigns the entity to a group or user | Not implemented |
| **Notify** | Sends a notification | Not implemented |
| **Switch Lifecycle** | Moves the entity onto a different lifecycle | Not implemented |

!!! warning "Four of these do nothing yet"
    `Bump Revision`, `Set Assignee`, `Notify` and `Switch Lifecycle` are registered and can be ticked on, but their implementations are still stubs — attaching them has no effect and produces no error. Only the three marked *Working* above actually run.

    To switch an entity between lifecycles today, use the **lifecycle dropdown** in the Change State dialog, which is fully implemented — see [Lifecycle](../entities/lifecycle.md).

### Attaching and configuring a feature automation

1. Go to **Configuration → Lifecycle Definitions**, open the lifecycle and select a **state**.
2. In the **Transitions** grid, select the transition you want.
3. Open the **Automations** tab and tick **On** next to the automation.
4. If the automation needs settings, a **gear** appears next to its name — click it.
5. Fill in the settings and click **Apply**.

A **warning triangle** next to an automation means a required setting is still missing. That automation will do nothing until it is configured — it stops silently rather than reporting an error, so the triangle is your only warning.

### Settings by automation

| Automation | Setting | Notes |
|---|---|---|
| Create Material Requirement | **MR line category** *(required)* | Restricted to Occurrence categories with **Material Requirement** behaviour |
| | **Target folder** *(optional)* | Normally leave empty — the owning project is found automatically by walking up the folder tree. Only used when the entity has no project above it. |
| Set Assignee | **Assign to group** | |
| Notify | **Notify group**, **Message** | |
| Switch Lifecycle | **Target lifecycle** | |

!!! note "Folder, lifecycle and group settings show an id until re-picked"
    When you reopen the settings dialog, a previously saved folder, lifecycle or group shows as `#123` rather than its name. Click the **…** button to re-pick it and the name appears. The saved value is correct either way — only the label is missing.

---

## Editing an automation

Select a custom automation and click **Edit** (or double-click the row). System automations are read-only — click **Clone** to create an editable copy first.

---

## Enabling and disabling

Select an automation and use the **Enable** / **Disable** toggle in the toolbar. Disabled automations remain in the list but do not execute.

---

## Cloning an automation

Select any automation (including system automations) and click **Clone**. A copy is created with "(copy)" appended to the name. The copy is a custom automation you can edit freely.

---

## Deleting an automation

Select a custom automation and click **Delete**. System automations cannot be deleted.

---

## Script engine

Automation scripts run in the MatStream scripting engine (JavaScript). The engine provides:

- A `context` object with event-specific data (entity ID, state names, batch results, etc.)
- An `api` object with methods for reading and writing workspace data
- Standard JavaScript globals (`Math`, `JSON`, `Date`, etc.)

Scripts do not have access to `fetch`, `require`, or other Node.js/browser globals. All external interactions go through the `api` object.

---

## Testing scripts

The **Test** button runs the script immediately in a sandbox. The output panel below the editor shows:

- Return value of the script
- Any `api.log.info()` / `api.log.error()` messages
- Runtime errors with line numbers

Test runs do **not** commit any changes to workspace data. They are read-only executions for validation purposes.

---

## Lifecycle transition automations

Lifecycle transition automations receive a context with:

```js
context.entityId        // int — entity version ID
context.entityNumber    // string — entity number
context.fromState       // string — previous state name (null if initial)
context.toState         // string — new state name
context.workspaceSlug   // string
context.userId          // int — user who triggered the transition
```

These automations are also configurable per lifecycle transition in **Configuration → Lifecycle Definitions → [definition] → Transitions → [transition] → Automations**.

---

## Import automations

Import automations receive:

```js
context.batchId       // int
context.profileName   // string
context.insertCount   // int
context.updateCount   // int
context.skipCount     // int
context.errorCount    // int
```

---

## Best practices

- **Always test before enabling** — use the Test runner to validate your script logic.
- **Keep scripts focused** — one automation per concern; don't combine unrelated actions in a single script.
- **Use `api.log` for debugging** — log messages appear in the Test output and in the workspace job monitor.
- **Handle errors gracefully** — wrap risky operations in try/catch so a single failure does not silently abort the entire script.
- **Clone before editing system automations** — never try to re-implement system automation behaviour; clone and extend instead.

---

## Related

- [Automations feature overview](../features/automations.md) — end-user documentation
- [Lifecycle definitions](lifecycle.md) — attach automations to specific transitions
- [Background jobs](../platform/how-it-works.md) — how async automations are tracked
