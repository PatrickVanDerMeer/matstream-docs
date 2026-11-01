# Automations

Automations let administrators define event-driven actions that run automatically when things happen in MatStream — for example, assigning a reviewer when an item moves to In Review, or filling in a property when it is released.

Two kinds exist side by side. **Feature automations** are built-in actions you configure on a lifecycle transition and hand parameters to — create a material requirement, bump the revision, assign a task. **Script automations** run JavaScript you write yourself, for the cases the built-in list does not cover.

---

## Accessing automations

Automations are managed in **Configuration → Automations**. The grid lists all automations defined in the workspace alongside the built-in system automations.

---

## How automations work

Each automation has:

- A **context type** — controls where the script is eligible to be attached (for example a lifecycle transition)
- A **script** — JavaScript code that executes in the automation engine
- An **enabled flag** — automations can be turned off without deleting them

An enabled script runs when it is attached to a supported configuration point and that event occurs. Context is an eligibility filter, not a global subscription. See [Context and attachment](../admin/automations-admin.md#context-and-attachment) for the supported paths and the distinction between context values and event runners.

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
6. Click **Run** to run the script immediately and check for errors.
7. Tick **Enabled** and click **Save**.

---

## The automation editor

The automation editor is split into three panels:

### API reference panel (left)

Lists script API methods grouped by category. Click any item to insert a code snippet at the cursor position. This means you do not need to memorise the API — browse and click to build your script.

### Monaco editor (centre)

A full-featured code editor with syntax highlighting, autocomplete, bracket matching, and inline error markers — the same engine used in VS Code.

### Output panel (bottom)

When you click **Run**, the script runs and the result is shown here. The Console output shows log messages, execution duration and any reported error. The Variables tab shows values saved with `matstream.vars`.

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
allow-listed. It cannot see the server's environment, so it does not read process settings directly. The current entity-script runner supplies an empty environment allowlist.

### What a script can change

Property values, or one new entity through `matstream.actions.createEntity`. `bumpRevision`, `switchLifecycle`, `setAssignee` and `matstream.notify.send` are
accepted by the editor but not yet performed — each of them fires automations of
its own, and running them from inside a transition needs a re-entrancy decision
that has not been made. A script that calls one is reported rather than
half-applied.

---

## Create an entity from a script

`matstream.params.get(key)` reads a configured JSON parameter as text. The generic creation action can create one Explorer folder or occurrence under an existing Root/Folder entity. It records a Generated From reference to the exact source version.

```js
var categoryId = Number(matstream.params.get("TargetCategoryId"));
var folderId = Number(matstream.params.get("TargetFolderId"));
if (!categoryId || !folderId) {
  throw new Error("Configure TargetCategoryId and TargetFolderId.");
}
matstream.actions.createEntity({
  categoryId: categoryId,
  parentFolderId: folderId,
  name: "Follow-up for " + matstream.entity.getName()
});
```

Configure real workspace IDs in Parameters (JSON), for example `{"TargetCategoryId":45,"TargetFolderId":123}`; these are illustrative IDs, not defaults. The target category must exist, be enabled, and have Folder or Occurrence family. The parent must be an existing Root or Folder in the same workspace.

One run can queue at most 500 actions; exceeding the cap refuses application. One run may create at most one entity. Creation cannot be combined with property writes in the same script. This action creates, places and links the output; it does not generically copy commercial properties or post financial data. Scripts have a 30-second execution timeout and a 10 MB engine memory limit. An empty script performs no work.

---

## Example: flag a released item

```js
if (matstream.entity.getLifecycleState() === "Released") {
  matstream.log.info(matstream.entity.getName() + " was released.");
  matstream.entity.setProperty("Released By Script", "yes");
}
```

Use **Run** in the editor to run a script immediately. It runs against sample
values rather than a real item, so it proves the script parses and behaves —
not what it will do to your data.

---

## Run history

Lifecycle automation runs record the action, trigger, item/version, actor, duration, status and failure. The runner prevents duplicate execution of the same automation/event pair. A new transition occurrence is a new event.

A failed run does not establish whether the triggering transition was rolled back. Check the current state and history. A run left Running may have been interrupted after performing an action; inspect its effects before retrying. See [Run history, failures and retries](../admin/automations-admin.md#run-history-failures-and-retries).

---

## Feature automations

Feature automations are **built into MatStream** rather than written in script. They perform actions the platform already knows how to do — raising a material requirement, updating on-order stock — and are shown with a **lightning bolt** icon.

You do not create or edit them. Instead you attach one to a lifecycle transition and, where it needs settings, configure it with the **gear** button beside its name.

They exist because some actions need to reach into stock, links and versioning in ways a sandboxed script cannot safely do. Where a scripted automation gives you flexibility, a feature automation gives you a supported, tested behaviour with no script to maintain.

See [Automations — Administrator Guide](../admin/automations-admin.md#feature-automations) for the full list, what each one does, and their prerequisites and configuration.

---

## API reference

These routes require `automation.manage`. Use the numeric workspace ID and the resolved tenant API address.

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/wid/{workspaceId}/automations` | List all automations |
| `GET` | `/api/wid/{workspaceId}/automations/system` | List system automations |
| `GET` | `/api/wid/{workspaceId}/automations/feature?contextTypeId=N` | List feature automations for a context |
| `POST` | `/api/wid/{workspaceId}/automations` | Create an automation |
| `PUT` | `/api/wid/{workspaceId}/automations/{id}` | Update an automation |
| `DELETE` | `/api/wid/{workspaceId}/automations/{id}` | Delete an automation |
| `POST` | `/api/wid/{workspaceId}/automations/{id}/clone` | Clone an automation |
| `POST` | `/api/wid/{workspaceId}/automations/test` | Test a script |

---

## Related

- [Lifecycle definitions](../admin/lifecycle.md) — configure the states and transitions that trigger lifecycle automations
- [Import Definitions](import-definitions.md) — configure imports that trigger import automations
- [Configuration overview](../admin/configuration.md) — all configuration areas
- [Properties](../entities/properties.md) — calculated properties handle simple arithmetic without a script
