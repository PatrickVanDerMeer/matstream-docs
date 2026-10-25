# File & Document Management

MatStream stores files against entity versions. Each revision of an entity can have its own set of file attachments, so the right file is always associated with the right version of a part, assembly, or document.

---

## File attachments

Files can be attached to any entity version. Typical attachments include:

- PDF drawings and specifications
- CAD files (STEP, IGES, native formats)
- Images and photographs
- Inspection reports and certificates
- Spreadsheets and Word documents

To attach a file:

1. Open the entity detail page.
2. Go to the **Files** tab or **Documents** section.
3. Click **Upload** or drag files into the upload area.
4. Each file is linked to the current entity version.

When a new revision is created, attachments from the previous revision are preserved as history. New files can be added to the new revision independently.

---

## PDF viewer

Entity detail pages include an embedded PDF viewer (Syncfusion PdfViewer). When you click a PDF attachment, it opens inline without leaving MatStream. This is useful for reviewing drawings, certificates, and reports alongside the entity's properties.

---

## Multi-file upload

The multi-file upload dialog lets you queue and upload several files at once. A progress grid shows the upload status of each file. Files are processed in sequence and attached to the entity version on completion.

---

## File check-out and check-in (via MatStreamConnect)

For users who need to edit files locally (e.g. open a drawing in AutoCAD and save changes back), MatStream supports a check-out / check-in workflow via the [MatStreamConnect](../integrations/matstream-connect.md) desktop app.

### Checking out a file

On a file attachment, click the **Open** link. This launches `matstream://` in the browser, which MatStreamConnect handles:

1. MatStreamConnect downloads the file to a local checkout folder (`%LOCALAPPDATA%\MatStream\MatStreamConnect\Checkout`).
2. The file is locked on the server — other users are notified it is checked out.
3. The file opens with the Windows default application (e.g. AutoCAD, Excel).

### Checking in

Connect watches whether the local file is still locked by an application. Once it can open the file exclusively, it compares the bytes with the original download using SHA-256:

- **Changed:** automatically upload the file, release checkout, and create the check-in version. There is no confirmation prompt and no success popup for automatic check-in.
- **Unchanged:** automatically undo checkout and release the lock, without creating a new version.
- **Check-in failed:** keep the checkout available for manual retry, except when ownership has been removed or taken over (see recovery below).

Save the file before closing it. File-lock detection is not application-exit detection; an application that releases the lock early can trigger the watcher while its window remains open.

To check in manually, right-click the Connect tray icon, select the file, and choose **Check in…**. Manual check-in uploads the local copy and reports success; the unchanged-file shortcut above belongs to automatic close handling. **Show in Explorer** selects the local copy.

Checkout tracking and watchers are held in memory. Exiting or restarting Connect does not restore the tray list or watchers, and can leave local copies and server checkouts behind. Preserve unsaved/local edits before restarting; do not assume restart releases the server lock.

### Recovery after a rejected check-in

If the server returns 403 or 409 because checkout was removed or taken over, Connect removes its stale tray entry and attempts to move the edited local file to:

```text
Documents\MatStream Connect\Recovered\<name>_recovered_<timestamp>.<extension>
```

A warning displays the recovered path. If moving the file fails, the local copy remains in `%LOCALAPPDATA%\MatStream\MatStreamConnect\Checkout`. These edits have **not** been saved to MatStream. Keep the recovered file, resolve access/checkout ownership, and arrange a new authorized upload. Other check-in failures show an error and retain the tracked checkout for retry.

### Inventor assets after check-in

A successful check-in of `.ipt` or `.iam` starts background GLB generation for the version returned by check-in. With an Inventor Thumbnail property configured, Connect also extracts and uploads the embedded thumbnail. Asset failures are logged and do not undo the file check-in. See [Inventor automatic previews](../integrations/inventor-3d-model.md#automatic-preview-after-inventor-check-in) and [Connect application settings](../integrations/matstream-connect.md#application-settings-and-inventor-thumbnails).

### Undo checkout

If you want to discard your local changes and release the lock without uploading:

1. Right-click the MatStreamConnect tray icon.
2. Select the file from the checked-out files list.
3. Click **Undo Checkout**.

The local file is deleted and the lock is released. The entity retains the original file version.

### Read-only open

If someone else has already checked out a file, clicking Open shows a prompt:

> *"This file is already checked out by [user]. Do you want to open it read-only?"*

Choosing Yes downloads the file without locking it and sets its Windows read-only attribute. An explicit `matstream://open?storedFileId=…&fileName=…&mode=readonly` link requests the same behavior directly. Read-only downloads are not listed in the checkout tray, cannot be checked back in through Connect, and have no automatic cleanup watcher.

---

## File placeholder

Entities of the **File** family type can be marked as **placeholders** — they represent a file that will exist but is not yet available. Placeholder entities show a distinct icon in the Explorer tree so the team knows a file is expected.

To mark an entity as a placeholder:

1. Open the entity edit dialog.
2. Tick the **Placeholder** checkbox (visible only for File family entities).
3. Save.

---

## Storage

Files are stored by an internal storage key, decoupling the logical attachment from the physical file. Attachments reference a stored file; they do not own it.

### Identical files are stored once

Every upload is hashed with **SHA-256** as it is written, and matched against the workspace's existing files on both hash and byte length. If the content is already held, the new copy is discarded before it reaches permanent storage and the attachment simply points at the file that was already there.

This is why:

- Attaching the same specification to fifty items costs the storage of one file.
- Re-uploading a file you already uploaded is free, and instant to store.
- Checking a file back in **unchanged** creates no new stored content.

Deduplication is scoped to the workspace — one tenant's files are never matched against another's.

!!! note "Deduplication does not merge your attachments"
    Two entities attached to the same content still have their own attachment rows, their own names and their own version history. Only the bytes are shared, and only inside one workspace.

### Reclaiming space

Stored content that no entity version references any longer is cleaned up by a background sweep, so deleting attachments eventually returns the disk space without any manual step.

---

## Related

- [MatStreamConnect](../integrations/matstream-connect.md) — desktop companion for check-out / check-in
- [Explorer](../features/explorer.md) — folder and file navigation
- [Folders](../features/folders.md) — attach files to project folders
- [Revisions](revisions.md) — how new revisions relate to file attachments
