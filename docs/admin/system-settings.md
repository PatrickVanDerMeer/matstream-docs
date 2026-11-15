# System settings and purge

Open **Configuration → System Settings** for workspace defaults and administrative actions. The available controls depend on your grants.

## Workspace defaults

Choose the settings and click **Save settings**; this requires `admin.workspace.settings`.

| Setting | Effect |
|---|---|
| **Planning time zone** | Whole-day boundaries for planned starts and due dates. |
| **Default Stock Unit** | Unit offered for items without one; initially Piece (pc). Saving the item commits it. Existing assigned units remain unchanged. Choose No default to leave unassigned items without a fallback. |
| **Timesheet completeness** | Off, Warn with explicit shortfall confirmation, or Block incomplete weeks. |
| **Require explicit hour types above the weekly target** | On submission, untyped working hours may fill the target remaining after absence; additional working hours need an explicit hour type. |
| **Stock replenishment folder** | Select the destination by its ID. Renaming that folder does not change the selection. Leaving it unset uses the standard Stock Replenishment discovery. Its creator must remain enabled. |
| **Enable manual history purge** | Allows the guarded manual purge action; does not schedule automatic deletion. |
| **Purge Retention Days** | Fallback age for historical versions without a lifecycle state. State-specific purge ages take precedence. |

## Units of Quantity

### Library insertion defaults

Click **Units of Quantity** on the System Settings ribbon. Its dialog contains the unit editor and **Library insertion defaults**, which control the quantity unit suggested when an engineer adds a Library item to Explorer. The **Save insertion defaults** button is separate from **Save settings** on the main page. Editing these settings requires `admin.workspace.settings`.

| Setting | Effect |
|---|---|
| **Use the item's Stock Unit first** | Suggests the selected item's assigned Stock Unit when that unit is available in this workspace. |
| **Fallback quantity unit** | Used when no available Stock Unit is selected by the policy. Choose **No fallback** to leave the suggestion empty. |
| **Engineer must review quantity and unit before adding** | Opens a review dialog before insertion. The engineer can change the quantity and unit for this placement. |
| **Quantity unit is required** | Rejects insertion without a unit, including requests sent directly to the API. |

Engineering Project Configuration seeds Stock Unit first, fallback **pc**, review required and unit required. Setup only supplies this policy when no saved insertion settings exist; later administrator changes, including No fallback and disabled requirements, are retained.

Only available workspace units can be selected. If a previously configured unit is made unavailable, the engineer must choose an available unit when required. This policy does not convert quantities, change the item's Stock Unit, rewrite existing project placements or alter child BOM quantities. For example, an engineer can review a pipe placement as **496 mm** while another item is **1 pc**.

For an existing ordinary occurrence, select it in Explorer and use **Actions → Quantity and unit**. Review and confirm its placement quantity/unit. Lifecycle and parent permissions still apply; purchase-order, stock and other specialised records keep their own process controls.

The ribbon's **Open Manage** button opens MatStream Manage in a new browser tab.

The **Units of Quantity** ribbon button opens the dialog for units used by stock and order quantities, such as pc or kg. Add a unit with a name and symbol, or edit its workspace name and availability. Click **Save unit** or **Add unit** for that row; this is separate from **Save settings**.

Names and availability are workspace-specific, including overrides for global units. Disabling a unit removes it from normal new selections; existing records keep their IDs and symbols. Symbols of existing units stay fixed because saved quantities depend on their identity. Create a new unit for a different symbol. Physical unit conversions are a separate feature.

Version creation is configured on each category, not here. An unset category version policy falls back to Every change. See [Version-creation policy](../entities/revisions.md#version-creation-policy).

## Rebuild search index

**Rebuild search index** requires `admin.search-index.rebuild`. It rebuilds the selected workspace's entity search index. Wait for the result message before retrying. This is useful after a restore or index maintenance; ordinary edits already queue indexing automatically.

The System Settings API action is `POST /api/wid/{workspaceId}/system-settings/rebuild-search-index`, using the numeric workspace ID and the workspace's tenant API origin.

## Run history purge

The action requires `admin.workspace.purge` and **Enable manual history purge** saved in System Settings. It processes up to 1,000 eligible historical versions per call.

1. Review each lifecycle state's **Purge Behavior**: **Never purge records in this state** protects its versions; a blank **Purge after (days)** disables bulk purge for that state. Set an age only for states whose history may be removed.
2. Leave **Dry run only** selected and click **Run history purge**. The result reports the eligible batch without deleting anything.
3. To delete that history, clear Dry run only and select the explicit confirmation checkbox, then run the action.
4. Read the completed result: it reports versions actually removed and those protected or changed. Run another batch only if needed.

The action protects latest versions and pinned references, and uses the existing guarded version-deletion path. Other database references can prevent deletion. It is a manual operation, not an automatic retention scheduler. Keep a workspace backup before intentional deletion.

## Manual history purge

To remove an eligible historical version, use **History → Purge** on the item. This is a separate implemented path, with delete permissions, current-version protection and reference checks. It is permanent. See [Working with a past version](../entities/revisions.md#working-with-a-past-version).

## Deleted-workspace cleanup

Server-side cleanup of workspaces due for deletion is another separate process. When enabled by the deployment, the workspace cleanup service asks Core for workspaces due for purge, removes their tenant records, filestore and search index, then marks them purged in Core. It is not the History Purge command or the System Settings button.

Keep a [workspace backup](backup-restore.md) before intentional data removal. Backup/restore, manual historical-version purge, and deleted-workspace cleanup serve different purposes.
