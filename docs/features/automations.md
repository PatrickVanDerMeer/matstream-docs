# Automations

Automations let administrators define scripted event-driven actions that run automatically when things happen in MatStream — for example, sending a notification when an entity transitions to Released, or updating a property when an import completes.

---

## Accessing automations

Automations are managed in **Configuration → Automations**. The grid lists all automations defined in the workspace alongside the built-in system automations.

---

## How automations work

Each automation has:

- A **context type** — determines when the automation runs (e.g. on a lifecycle state transition, after an import, or triggered by a UI feature)
- A **script** — JavaScript code that executes in the automation engine
- An **enabled flag** — automations can be turned off without deleting them

When the triggering event fires, MatStream runs all enabled automations whose context type matches the event.

---

## Context types

| Context type | When it runs |
|---|---|
| Lifecycle transition | After an entity version changes lifecycle state |
| Import completed | After an import batch finishes |
| Entity created | After a new entity is created |
| Entity updated | After an entity's properties are saved |
| Feature | Triggered by a specific UI action (button, ribbon command) |

---

## System vs. custom automations

**System automations** are provided by MatStream. They are read-only — you cannot edit or delete them. They appear with a gear icon in the grid.

**Custom automations** are created and owned by your workspace. You have full control to edit, rename, enable/disable, or delete them. They appear with a person icon.

To create a custom version of a system automation, select it and click **Clone**. A copy is created in your workspace which you can edit freely.

---

## Creating an automation

1. Go to **Configuration → Automations**.
2. Click **New** in the ribbon.
3. Fill in the **Name** and optionally a **Description**.
4. Choose the **Context type**.
5. Write or paste the script in the Monaco editor.
6. Click **Test** to run the script immediately and check for errors.
7. Tick **Enabled** and click **Save**.

---

## The automation editor

The automation editor is split into three panels:

### API reference panel (left)

Lists available API methods and context objects grouped by category. Click any item to insert a code snippet at the cursor position. This means you do not need to memorise the API — browse and click to build your script.

### Monaco editor (centre)

A full-featured code editor with syntax highlighting, autocomplete, bracket matching, and inline error markers — the same engine used in VS Code.

### Output panel (bottom)

When you click **Test**, the script runs and the result is shown here. Any errors (syntax errors, runtime exceptions, API call failures) appear with line references so you can fix them quickly.

---

## Script context

Each automation script receives a `context` object whose properties depend on the context type:

**Lifecycle transition context:**
```js
context.entityId        // ID of the entity version that transitioned
context.entityNumber    // Entity number string
context.fromState       // Name of the previous lifecycle state
context.toState         // Name of the new lifecycle state
context.workspaceSlug   // Current workspace slug
```

**Import completed context:**
```js
context.batchId         // Import batch ID
context.profileName     // Import definition name
context.insertCount     // Number of entities created
context.updateCount     // Number of entities updated
context.errorCount      // Number of rows that failed
```

---

## Example: notify on release

```js
// Send a log message when an entity moves to Released
if (context.toState === "Released") {
  await api.log.info(`Entity ${context.entityNumber} was released.`);
  // Additional actions: update properties, call webhooks, etc.
}
```

---

## Feature automations

Feature automations are a special type tied to specific UI actions rather than system events. They allow workspace administrators to add custom behaviour to buttons or ribbon commands without changing the application code. Feature automations are listed alongside regular automations but have their own context type and are invoked by the UI component directly.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/wid/{slug}/automations` | List all automations |
| `GET` | `/api/wid/{slug}/automations/system` | List system automations |
| `GET` | `/api/wid/{slug}/automations/feature?contextTypeId=N` | List feature automations for a context |
| `POST` | `/api/wid/{slug}/automations` | Create an automation |
| `PUT` | `/api/wid/{slug}/automations/{id}` | Update an automation |
| `DELETE` | `/api/wid/{slug}/automations/{id}` | Delete an automation |
| `POST` | `/api/wid/{slug}/automations/{id}/clone` | Clone an automation |
| `POST` | `/api/wid/{slug}/automations/test` | Test a script |

---

## Related

- [Lifecycle definitions](../admin/lifecycle.md) — configure the states and transitions that trigger lifecycle automations
- [Import Definitions](import-definitions.md) — configure imports that trigger import automations
- [Configuration overview](../admin/configuration.md) — all configuration areas
