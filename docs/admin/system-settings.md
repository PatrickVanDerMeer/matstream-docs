# System settings and purge

Open **Configuration → System Settings** for workspace defaults and administrative actions. The available controls depend on your grants.

## Workspace defaults

**Purge Retention Days** stores a non-negative retention value. **Planning time zone** defines the whole-day boundaries used for planned starts and due dates. Choose a supported zone and click **Save settings**; this requires `admin.workspace.settings`.

Version creation is configured on each category, not here. An unset category version policy falls back to Every change. See [Version-creation policy](../entities/revisions.md#version-creation-policy).

## Rebuild search index

**Rebuild search index** requires `admin.search-index.rebuild`. It rebuilds the selected workspace's entity search index. Wait for the result message before retrying. This is useful after a restore or index maintenance; ordinary edits already queue indexing automatically.

The System Settings API action is `POST /api/wid/{workspaceId}/system-settings/rebuild-search-index`, using the numeric workspace ID and the workspace's tenant API origin.

## Run purge: current limitation

The **Run purge** button requires `admin.workspace.purge`. In the currently reviewed implementation, its endpoint checks confirmation and returns a Started response, but does not invoke a purge job. A "Workspace purge started" message from this action is therefore not evidence that historical versions were removed. Do not rely on this button to enforce a retention policy.

Lifecycle states also offer **Purge Behavior** with **Never purge records in this state** and **Purge after (days)**. These values can be saved, but saved settings alone do not demonstrate an automatic version-retention runner. An empty days value is shown as disabling purge in that control.

## Manual history purge

To remove an eligible historical version, use **History → Purge** on the item. This is a separate implemented path, with delete permissions, current-version protection and reference checks. It is permanent. See [Working with a past version](../entities/revisions.md#working-with-a-past-version).

## Deleted-workspace cleanup

Server-side cleanup of workspaces due for deletion is another separate process. When enabled by the deployment, the workspace cleanup service asks Core for workspaces due for purge, removes their tenant records, filestore and search index, then marks them purged in Core. It is not the History Purge command or the System Settings button.

Keep a [workspace backup](backup-restore.md) before intentional data removal. Backup/restore, manual historical-version purge, and deleted-workspace cleanup serve different purposes.
