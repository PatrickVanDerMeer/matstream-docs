# Backup and restore

MatStream Manage can download a complete workspace package and restore that
package into a selected workspace. A package contains the workspace-owned tenant
data and its referenced files, so it can also be used to copy a workspace to a
new test workspace or move it to another MatStream installation.

Workspace packages use the `.msworkspace` extension. Treat these files as
sensitive: they can contain users, business data, configuration, and documents.

!!! warning "Restore replaces the selected workspace"
    A restore removes the current workspace-owned tenant data before importing
    the package. It cannot be undone from MatStream. Back up the target workspace
    first if you may need its current contents.

---

## Open Backup and Restore

1. Open **MatStream Manage** and go to **Workspaces**.
2. Select the workspace card on the left.
3. Open the **Backup and Restore** tab.

The tab is available to an authenticated administrator with the **Admin workspace
settings** grant. Its toolbar contains **Backup workspace**, **Restore package**,
and **Refresh**. The history below it records previous backup and restore actions
for the selected workspace.

---

## Back up a workspace

1. Select the workspace in MatStream Manage.
2. Open **Backup and Restore**.
3. Click **Backup workspace**.
4. Keep the downloaded `.msworkspace` file in a secure location.
5. Click **Refresh** to see the final status in **Package history**.

The download is streamed from the tenant that hosts the selected workspace. A
large backup therefore does not have to be assembled in browser memory first.

The history row shows who started the backup, its start and completion times,
the number of database rows and files, the file-data size, and any failure
details. The package filename includes the source workspace and backup date.

---

## Restore a package

1. Select the **target** workspace in MatStream Manage.
2. Open **Backup and Restore** and click **Restore package**.
3. Select a `.msworkspace` file.
4. Review the source workspace, creation time, row count, file count, and size
   shown in the confirmation dialog.
5. Back up the target first if its current contents may still be needed.
6. Type the displayed confirmation exactly, for example `REPLACE-59`.
7. Click **Restore workspace**.
8. After completion, click **Refresh** to review the history entry.

MatStream validates the package before enabling the destructive restore. During
restore it remaps database identifiers and workspace-specific GUIDs, resolves
global reference data on the destination installation, and restores physical
files through the tenant filestore.

The administrator performing the restore must also be present as a workspace
user in the source package. This preserves the active administrator while the
other target workspace data is replaced.

### Copy a workspace to a new workspace

The Manage UI restores into the workspace currently selected; it does not create
a workspace from the package.

To make a test copy or clone:

1. Back up the source workspace.
2. Create and provision a new workspace in MatStream Manage.
3. Ensure the restoring administrator exists in the source workspace before its
   backup is created.
4. Select the new workspace and restore the package into it.

The source and target can be on different tenant servers. MatStream uses the
target workspace's tenant origin recorded in Core and remaps the imported data
inside that tenant.

---

## What the package contains

A version 1 workspace package is a ZIP-based, self-contained archive containing:

- a versioned `manifest.json` with source workspace and content statistics;
- workspace users, groups, grants, configuration, lifecycles, categories,
  properties, templates, automations, entities, versions, links, and other
  workspace-owned tenant records;
- referenced stored files, including entity files, print-template files, and
  import-definition sample files;
- stable logical keys for global reference data that must be resolved on the
  destination;
- the byte size and SHA-256 checksum of each physical file.

The package does not contain the workspace registration and tenant routing held
in Core, billing data, API keys, or backup/restore operation history. Those remain
properties of the destination MatStream installation or workspace.

Backup reads database records from a consistent transaction snapshot (or
repeatable-read isolation when snapshot isolation is unavailable) and streams
database sections and files into the archive. Restore validates the manifest,
required sections, archive paths, declared sizes, and file checksums before
replacing data. Database import runs in a transaction, while files are staged and
cleaned up if the import fails.

---

## Package history

Package history is retained separately from workspace business data, so a
restore does not erase its own audit trail. Status values are:

| Status | Meaning |
|---|---|
| **Running** | The tenant accepted the operation and it is still processing. |
| **Completed** | The package was created or restored successfully. |
| **Failed** | The operation stopped; hover over the status or read **Details** for the reason. |

Use **Refresh** to retrieve the latest status. Backup downloads start through a
hidden download frame, so the Workspaces page remains open while the browser
saves the file.

---

## Tenant deployment and database migration

Workspace package endpoints run in each tenant API, not in Core. The operation
history table must also exist in every tenant database that can host a workspace.

For each tenant deployment:

1. Deploy the API version containing the workspace-package endpoints.
2. Run `MatStream.API/Migrations/20260911_add_workspace_package_operations.sql`
   against that tenant database.
3. Do **not** run this migration against the Core database.
4. Confirm that each Core workspace record has the correct tenant API origin.

The migration is applied once per tenant database, not once per workspace. When
several workspaces share a tenant database, their operation rows are separated by
workspace ID.

MatStream Manage first reads the selected workspace from Core and then calls the
`TenantApiBaseUrl` returned for that workspace. Consequently, two selected
workspaces can back up from different servers without a global tenant setting in
Manage.

---

## Troubleshooting

| Message or symptom | Likely cause and action |
|---|---|
| **Not Found** | The selected workspace's tenant origin does not have the workspace-package API deployed, or Core points to the wrong tenant API URL. Check both before retrying. |
| **The workspace tenant API is not available** | The selected Core workspace record has no usable tenant API origin. Correct its tenant routing. |
| History fails to load after deployment | Run `20260911_add_workspace_package_operations.sql` against the workspace's tenant database. |
| **The importing administrator must also exist in the source workspace package** | Add the administrator to the source workspace, create a new backup, and restore that package. |
| Package version or section is unsupported | Use an unmodified package created by a compatible MatStream version. |
| File size or checksum mismatch | The package is incomplete or corrupted. Create or retrieve a new backup. |
| Restore remains **Running** | Wait for large files to finish, then click **Refresh**. If it does not finish, inspect the tenant API log and the history details. |

---

## Related

- [Workspaces](../getting-started/workspaces.md) — workspace isolation and contents
- [Workspace setup](../getting-started/workspace-setup.md) — create and provision a target workspace
- [API keys](api-keys.md) — credentials that remain outside the package

