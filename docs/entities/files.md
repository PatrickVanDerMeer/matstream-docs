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

When you close the application that has the file open, MatStreamConnect detects this and prompts:

> *"filename.dwg has been closed. Check it back in to MatStream?"*

Click **Yes** to upload the modified file and release the lock. The new file version is attached to the entity.

You can also check in manually from the MatStreamConnect tray icon at any time.

### Undo checkout

If you want to discard your local changes and release the lock without uploading:

1. Right-click the MatStreamConnect tray icon.
2. Select the file from the checked-out files list.
3. Click **Undo Checkout**.

The local file is deleted and the lock is released. The entity retains the original file version.

### Read-only open

If someone else has already checked out a file, clicking Open shows a prompt:

> *"This file is already checked out by [user]. Do you want to open it read-only?"*

Choosing Yes downloads the file without locking it. Read-only files cannot be checked back in.

---

## File placeholder

Entities of the **File** family type can be marked as **placeholders** — they represent a file that will exist but is not yet available. Placeholder entities show a distinct icon in the Explorer tree so the team knows a file is expected.

To mark an entity as a placeholder:

1. Open the entity edit dialog.
2. Tick the **Placeholder** checkbox (visible only for File family entities).
3. Save.

---

## Storage

Files are stored by an internal storage key, decoupling the logical attachment from the physical storage backend. The backend can be local disk, cloud blob storage, or another provider depending on your deployment. Contact your MatStream administrator for storage configuration.

---

## Related

- [MatStreamConnect](../integrations/matstream-connect.md) — desktop companion for check-out / check-in
- [Explorer](../features/explorer.md) — folder and file navigation
- [Folders](../features/folders.md) — attach files to project folders
- [Revisions](revisions.md) — how new revisions relate to file attachments
