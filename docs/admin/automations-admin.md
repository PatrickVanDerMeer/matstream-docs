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
| Lifecycle transition | An entity version changes lifecycle state |
| Import completed | An import batch finishes (success or with errors) |
| Entity created | A new entity is created |
| Entity updated | An entity's properties are saved |
| Feature | A specific UI button or action is triggered |

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
