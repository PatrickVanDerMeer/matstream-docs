# Automations — Administrator Guide

Automations combine built-in feature actions and reusable JavaScript scripts. Manage stored scripts in **Configuration → Automations**. A script's context controls where it is eligible for selection; saving an enabled script alone does not attach it to every matching event.

## Create and test a script

1. Open **Configuration → Automations** and click **New**.
2. Enter a name, optional description, and **Context**.
3. Enter optional **Parameters (JSON)** as one JSON object.
4. Write JavaScript using the `matstream` API. The API Reference panel inserts snippets into the Monaco editor.
5. Click **Run**. Review the **Console** and **Variables** output.
6. Set **Enabled** and save, then attach the script at the relevant configuration point.

System scripts (gear icon) cannot be edited or deleted. **Clone** creates an editable workspace copy (person icon). Feature automations (lightning bolt) are built into MatStream and are configured where they are attached.

**Run** uses a mock entity and the supplied parameters. It does not commit workspace changes. A successful test does not prove that target categories, folders, properties or lifecycle permissions are valid in production.

## Context and attachment

| Context | Meaning |
|---|---|
| Universal | Reusable across supported attachment contexts; not an automatic global trigger |
| Lifecycle transition | Select on a lifecycle transition's Automations tab |
| Category lifecycle switch | Select on the category's configured lifecycle switch |
| Property change | Configure on a category property; returned values remain in the editor until saved |
| Inventory, Scheduled Job, On create, On save, On delete, Before delete | Context values exist in the model/editor. Their presence alone does not establish a stock-event subscription, timer or entity-event runner. Do not assume these execute simply because a script is enabled. |

For property-change configuration, see [Property automations](category-editor-dialog.md#property-automations). A lifecycle script must be enabled and have Universal or the matching lifecycle context.

## Feature automations

The following lifecycle feature actions are implemented. Their prerequisites still apply; the action's presence in the picker does not guarantee a valid setup.

| Action | Behavior and prerequisites |
|---|---|
| **Generate and Attach PDF** | Generates a PDF using an assigned Word or Excel print template and attaches it to the entity. An empty Print template uses the category default. Attachment category must be a definition category allowing files; empty uses Document. |
| **Bump Revision** | Updates the triggering version's revision to the next value in its configured revision scheme. Fails if no scheme or next revision exists. This action is different from creating a new version. |
| **Pin Document Package Contents** | Pins direct document lines to the exact definition versions being issued. Rejects empty packages, unresolved document versions and packages over 10,000 lines. |
| **Set Assignee** | Creates an Action task on the item for a selected user, or one task per resolved group member. A user takes precedence when both User and Group are set. Requires an enabled Task-behavior category; missing assignment or category can result in no tasks and a server warning. |
| **Notify** | Sends an in-app notification to an enabled user or enabled group members who can read the item. User takes precedence over Group. Requires a message of 1–2,000 characters and at least one readable recipient. It does not send email. |
| **Recompute Task Hours** | Refreshes the planning projection for a task. Booked hours are read from time entries; planned/estimated hours are preserved. |
| **Switch Lifecycle** | Switches the triggering entity using the category's permitted lifecycle switch. Requires Target lifecycle; an optional Target state is a state name reached after the switch. |
| **Switch Source Occurrence Lifecycle** | Follows a material requirement's source occurrence and switches that occurrence, using the same lifecycle rules. |
| **Create Material Requirement** | Creates a requirement linked to the triggering occurrence. Requires MR line category with Occurrence family and Material Requirement behavior. Normally resolves the owning project; Target folder is the fallback when no Project ancestor exists. |
| **PO: Add to On Order** | Adds the linked product's ordered quantity to On Order. An already outstanding quantity is not added again. |
| **PO: Release from On Order** | Releases the outstanding On Order balance for the PO line. |
| **Capture Filter Snapshot** | Executes the saved filter and pins its membership to the triggering filter version; rejects results above 10,000 rows. |

**Copy Property Values** is a property-change feature action, not a lifecycle dispatcher action. It copies mapped properties from the referenced entity into the open editor. A mapping without a source clears its target.

## Attach a lifecycle automation

1. Open **Configuration → Lifecycle Definitions**, select the lifecycle and a state.
2. Select the relevant transition in **Transitions**.
3. Open **Automations** and enable the desired feature action or attach a library script.
4. Use the gear button for parameters, then **Apply** and save the configuration.
5. Test the transition on a suitable test item and inspect its result.

Resolve warning triangles and missing settings before use. Some actions reject invalid configuration, while others log and skip; do not assume every missing parameter produces a visible error.

## Scripting API and supported writes

Scripts use `matstream`, not a generic `api` or `context` object. See [Script context](../features/automations.md#script-context) for reads, logging and property writes, and [Create Entity scripts](../features/automations.md#create-an-entity-from-a-script) for the generic creation action.

Built-in lifecycle actions and similarly named script helpers have different support. Built-in Bump Revision, Switch Lifecycle, Set Assignee and Notify are implemented. The script runtime does not apply `matstream.lifecycle.bumpRevision`, `switchLifecycle`, `setAssignee` or `matstream.notify.send`; use a built-in action for those operations.

## Run history, failures and retries

The lifecycle automation runner records the action, trigger, entity/version, actor, timing, status and failure. It claims each automation and trigger execution before running it. Repeated delivery of the same action/event pair is skipped; a new lifecycle occurrence is a separate event.

A run left **Running** can mean execution was interrupted or the final status could not be recorded. Do not assume it is safe to replay side effects. Review the item, generated files, tasks or notifications and the server log first. A failed automation is not proof that the triggering state change was undone; inspect the current state and history.

## Related

- [Automations](../features/automations.md)
- [Lifecycle definitions](lifecycle.md)
- [Print templates](print-templates-admin.md)
