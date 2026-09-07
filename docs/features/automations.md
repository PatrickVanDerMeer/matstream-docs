# Automations

Automations let administrators define event-driven actions that run automatically when things happen in MatStream — for example, assigning a reviewer when an item moves to In Review, or filling in a property when it is released.

Two kinds exist side by side. **Feature automations** are built-in actions you configure on a lifecycle transition and hand parameters to — create a material requirement, bump the revision, assign a task. **Script automations** run JavaScript you write yourself, for the cases the built-in list does not cover.

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

A script talks to MatStream through a single `matstream` object:

```js
matstream.entity.getName()                  // the item's name
matstream.entity.getRevision()              // its revision letter
matstream.entity.getVersion()               // its version number
matstream.entity.getCategory()              // its category name
matstream.entity.getLifecycleState()        // its current state
matstream.entity.getProperty("Material")    // a property value, by display name
matstream.entity.setProperty("Checked", "yes")

matstream.log.info("...")                   // and .warn() / .error()
matstream.vars.set("key", value)            // scratch values within one run
matstream.vars.get("key")
matstream.env.get("REGION")                 // allow-listed settings only
```

Property names are matched without regard to case, and a property the item does
not have reads as nothing.

### Reads are a snapshot; writes are applied at the end

Everything readable is loaded **before** the script starts, and every change is
collected and applied **after** it finishes. Two things follow, and both are
useful:

- A script sees a consistent picture. Reading a property it set earlier in the
  same run gives back what it set; reading anything else gives what was true
  when the run began.
- **A script that fails part way changes nothing.** Nothing is written unless
  the whole script completes.

### There is no network access

A script cannot make HTTP requests. Scripts are written by whoever can edit an
automation rather than by whoever deploys MatStream, and a script that could
post to any address could reach anything the server can — internal services
included. Calling out to another system belongs in an integration, not in a
stored script.

`matstream.env.get` reads only settings an administrator has explicitly
allow-listed. It cannot see the server's environment, so it can never return a
connection string or an API key.

### What a script can change

Property values. `bumpRevision`, `switchLifecycle` and `setAssignee` are
accepted by the editor but not yet performed — each of them fires automations of
its own, and running them from inside a transition needs a re-entrancy decision
that has not been made. A script that calls one is reported rather than
half-applied.

---

## Example: flag a released item

```js
if (matstream.entity.getLifecycleState() === "Released") {
  matstream.log.info(matstream.entity.getName() + " was released.");
  matstream.entity.setProperty("Released By Script", "yes");
}
```

Use **Test** in the editor to run a script immediately. It runs against sample
values rather than a real item, so it proves the script parses and behaves —
not what it will do to your data.

---

## Run history

Every automation run is recorded: which automation, on what, when, how long it
took, and the error if it failed. Before this, a failing automation put a
sentence on the dialog of whoever triggered it and then vanished — so "did that
notification fire on Tuesday" had no answer, even though the state change it
followed was permanent.

A failure does not roll back the transition that triggered it. The state change
stands, the person is told, and the run is recorded for somebody to look at.

---

## Feature automations

Feature automations are **built into MatStream** rather than written in script. They perform actions the platform already knows how to do — raising a material requirement, updating on-order stock — and are shown with a **lightning bolt** icon.

You do not create or edit them. Instead you attach one to a lifecycle transition and, where it needs settings, configure it with the **gear** button beside its name.

They exist because some actions need to reach into stock, links and versioning in ways a sandboxed script cannot safely do. Where a scripted automation gives you flexibility, a feature automation gives you a supported, tested behaviour with no script to maintain.

See [Automations — Administrator Guide](../admin/automations-admin.md#feature-automations) for the full list, what each one does, and which are still unimplemented.

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
- [Properties](../entities/properties.md) — calculated properties handle simple arithmetic without a script
