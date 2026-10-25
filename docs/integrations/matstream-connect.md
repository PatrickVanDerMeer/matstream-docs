# MatStreamConnect

MatStreamConnect is a small Windows desktop app that bridges local tools — Autodesk Inventor via iLogic, the browser's *Open* links — and the MatStream API. It does what a browser cannot: run commands from a CAD rule, open a stored file in its native application and check it back in, and push files that were just exported on the workstation.

---

## Installation

MatStreamConnect ships as a Windows installer and lives in the system tray after it starts.

On its first start it registers itself under `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` (the *Open on Windows logon* setting) and the `matstream://` URI scheme. **The iLogic rules locate the executable through that `Run` entry**, so Connect must have been started at least once on the workstation before a rule can call it.

---

## Setup window

The first start — or **File → Login…** later — opens the setup window:

| Field | What to enter |
|---|---|
| **Workspace** | The workspace **slug** (e.g. `cho66k`), or simply **paste the workspace address from your browser** (`https://app.matstream.com/w/cho66k/…`). A line under the field shows where Connect will sign in and which workspace it resolved. |
| **Email / Password** | Your MatStream account. |
| **Continue with Microsoft / Google** | Sign in without a password. Connect opens your browser on the provider's sign-in page; once you approve, the browser shows "Signed in" and Connect continues on its own. Requires the **Workspace** field, since that tells Connect which server to sign in at. |
| **Remember me** | On: Connect stays signed in across restarts and keeps the credentials for the day the session finally expires. Off: session tokens remain in memory only; the next start requires sign-in. Workspace history and application preferences are still saved. With Microsoft/Google there is no password to keep — when the session finally expires, click the provider button again. |

Recently used workspaces are offered in the dropdown (up to ten entries). Click the × beside an entry to remove it from history; this does not revoke workspace access or change the active connection. Developers connecting to a local environment paste the local web-app or API address the same way (`https://localhost:7053/w/<slug>`); Connect maps it to the matching Core API.

---

## Configuration file

Connection settings are stored next to the executable in `MatStreamConnect.json` and written by the setup window — there is normally no reason to edit the file.

| Field | Description |
|---|---|
| `CoreBaseUrl` | The Core API Connect signs in against (`https://api.matstream.com` for the hosted service) |
| `WorkspaceSlug` / `WorkspaceId` / `WorkspaceName` | The workspace, resolved at login |
| `TenantApiBaseUrl` | The Tenant API for that workspace, filled in by the resolve call |
| `WorkspaceEntry` / `WorkspaceHistory` | What was typed, and the recent list |
| `RememberMe`, `DebugMode`, `WindowVisible` | Session persistence, diagnostic logging, and whether the main window is restored as visible |
| `InventorThumbnailPropertyDefId` | Optional positive property definition ID; null/empty disables Inventor thumbnail upload |
| `InventorThumbnailTarget` | `LinkedDefinition` (default) or `Document`; see Application settings below |

An empty or unreadable file is treated as "not configured" and opens the setup window.

---

## Main window and tray

- **Double-click** the tray icon, or **Open** from its menu, shows the main window. Closing the window hides it again; **File → Exit** stops Connect.
- **Queue** lists publish commands waiting or running; **Activity** is the live log every command writes to.
- **File → Log Files…** opens the log folder (`%APPDATA%\MatStream\MatStreamConnect\`). **Settings → Debug mode** makes the log verbose — turn it on when something misbehaves, then send the newest file.
- **Settings → Open on Windows logon** controls the `Run` entry described above.

The tray menu also lists files currently checked out through Connect, with **Check in…**, **Undo checkout**, and **Show in Explorer** actions. Opening an already locally checked-out file again selects its local copy in Explorer.

- **File → Login…** or tray **Change workspace…** opens connection setup. Use it to choose the workspace before publishing or opening files.
- **File → Logout** or tray **Log out** clears the active session and saved tokens after confirmation. It does not clear the separately remembered password credential; do not describe logout as deleting all saved sign-in data.
- The main status bar shows connection state, workspace, and email. The tray icon and tooltip reflect authentication state.
- Tray **Start at login** controls the same startup registration as **Open on Windows logon**.
- Tray **Settings…** and **Settings → Application settings…** open the application settings dialog described below.

## Application settings and Inventor thumbnails

Connection setup and application settings are separate dialogs. In **Settings → Application settings…** (or tray **Settings…**), edit the fields and click **Save**; **Cancel** discards edits.

| Setting | Meaning and constraints |
|---|---|
| Open on Windows logon | Register or remove the per-user Windows startup entry |
| Debug mode | Enable verbose diagnostic logging; failures are also logged with debug mode off |
| Thumbnail PropertyDefinition ID | A positive whole number identifying a MatStream property definition with the **Thumbnail** datatype. Leave empty to disable thumbnail upload. The property must be assigned to the intended target category. |
| Thumbnail target: Unambiguously linked entity (recommended) | Stored as `LinkedDefinition`. The API requires exactly one linked entity with the configured property. Zero eligible links or multiple eligible links return a conflict; Connect never guesses a target. A successful changed thumbnail uses the linked entity's versioning behavior. |
| Thumbnail target: Checked-in Document | Stored as `Document`. Write the thumbnail onto the Document version returned by check-in, without creating a second Document version for the thumbnail. |

Thumbnail extraction runs after successful check-in of an Inventor `.ipt` or `.iam` file, when check-in returns a valid entity version. It reads the embedded Inventor thumbnail and uploads PNG through the thumbnail API. This is separate from `--publish-model` and from PDF/BOM publishing; those commands do not trigger thumbnail extraction.

The server enforces entity update access and property/lifecycle rules. The logged outcome can be written, unchanged, or skipped by rules. Extraction errors, ambiguous links, and permission failures are logged under `Thumbnail`; they do not roll back check-in or suppress GLB generation. See [Thumbnail properties](../admin/properties.md#thumbnail-properties) for property configuration.

## Workspace selection and session restoration

On startup Connect first tries to restore its remembered session. During normal startup it can also use the remembered email/password when token restoration fails. Microsoft/Google sign-in stores session tokens, not a provider password. Tokens and remembered password credentials are stored separately under `%APPDATA%\MatStream\MatStreamConnect`, protected with Windows DPAPI for the current Windows user.

After authentication, workspace selection follows this order:

1. Restore the saved workspace if it is among the user's active workspaces (ID first, then slug).
2. Otherwise select the user's valid `DefaultWorkspaceId`.
3. Otherwise select the sole available active workspace.
4. If none are available, or several remain without a valid default, open setup for an explicit choice.

The selected workspace is resolved again to refresh its Tenant API address. Publish commands and file links use Connect's active workspace; they do not contain a workspace selector. Confirm the workspace in the status bar before acting on IDs or names.

If normal startup cannot establish a connection and setup is cancelled, Connect remains running and retries token restoration/remembered login and workspace selection once per minute. Successful reconnection closes open setup windows. This is a startup recovery loop, not an offline publish retry service.

---

## Commands

Connect accepts commands on its command line. A running instance receives them (single instance), shows its window and processes them in order; if Connect is not running the command starts it. Everything is reported in the **Activity** log.

| Command | Purpose | Documented in |
|---|---|---|
| `--publish-entity --profile … --metadata … [--file …]` | Create or update one entity from metadata, optionally attaching a file | [Inventor BOM import](inventor-bom.md) |
| `--publish-drawing --profile … --metadata … [--file …]` | Same, for a drawing with its PDF | [Inventor BOM import](inventor-bom.md) |
| `--publish-children --profile … --metadata … --bom … [--update-latest-version]` | Upsert an Excel BOM as children of an existing entity | [Inventor BOM import](inventor-bom.md) |
| `--publish-model --key … --file …` | Attach a GLB as an existing entity's 3D model | [Inventor 3D model](inventor-3d-model.md) |
| `--setup` | Open setup when launching a fresh instance; use the UI when Connect is already running | — |
| `matstream://open?storedFileId=…` | Handled from the browser's *Open* link: download, check out and open a file natively | [Files & documents](../entities/files.md) |

### File URI parameters

```text
matstream://open?storedFileId=123&fileName=part.ipt
matstream://open?storedFileId=123&fileName=part.ipt&mode=readonly
```

`storedFileId` is a required integer in the active workspace. `fileName` is the display filename (include the extension for Windows application association); URL-encode query values. Connect sanitizes characters invalid in Windows filenames. `mode=readonly` downloads without checkout, sets the local file's read-only attribute, and never checks it in. Omitting `mode` requests checkout for editing. A checkout conflict offers read-only opening. See [Files & documents](../entities/files.md#file-check-out-and-check-in-via-matstreamconnect).

### Publish outcomes and agent guidance

- Metadata must be a flat JSON object with string values. Supply a file path to avoid shell quoting errors. Invalid or unreadable metadata currently becomes an empty dictionary; inspect the import result rather than assuming parsing succeeded.
- Command flags use the exact spellings shown above. Missing required flags can cause the parser to ignore the command. Unknown flags are not a documented extension mechanism.
- `--update-latest-version` is applied only by the children publisher. It is not a supported way to change entity, drawing, or model version behavior.
- Entity/drawing publishing creates or updates metadata first, then separately uploads an existing optional file. A failed or missing attachment does not roll back the metadata publish. Drawing publishing logs a missing PDF warning; entity publishing skips a missing optional file.
- BOM publishing requires an existing Excel file and reports inserted/updated/error rows. Inspect warnings and row errors before reporting success.
- `--publish-model` attaches to the matching entity's latest version without creating an entity or version, and deletes the source GLB after successful upload. Use a temporary copy.
- The Queue is an in-memory, sequential work queue. Entries disappear shortly after processing. Its **Done** state is not proof of success: the runner can log a failure and return normally. Use Activity messages and the resulting MatStream data to verify the outcome. Commands are not persisted for restart or automatically retried after failure.
- `--setup` is handled on a fresh launch; the running instance's forwarded-argument handler accepts file URIs and publish commands, not `--setup`. Use **File → Login…** or tray **Change workspace…** when already running.

### Calling Connect from iLogic

The pattern every MatStream rule uses — find the executable via the registry, then `Shell` it with quoted arguments:

```vb
Dim connectPath As String = FindMatStreamConnectExe()
If String.IsNullOrWhiteSpace(connectPath) Then Return

Dim args As String = "--publish-drawing" &
                     " --profile ""Inventor Main Item""" &
                     " --metadata """ & metaPath & """" &
                     " --file """ & pdfPath & """"
Shell("""" & connectPath & """ " & args)
```

```vb
Function FindMatStreamConnectExe() As String
    Dim exePath As String = ""
    Try
        ' WScript.Shell rather than Microsoft.Win32.Registry — not available to iLogic in Inventor 2027.
        Dim shellObj As Object = CreateObject("WScript.Shell")
        Dim regValue As Object = shellObj.RegRead("HKCU\Software\Microsoft\Windows\CurrentVersion\Run\MatStreamConnect")
        If regValue IsNot Nothing Then exePath = regValue.ToString().Trim(""""c)
    Catch
    End Try

    If String.IsNullOrWhiteSpace(exePath) OrElse Not System.IO.File.Exists(exePath) Then
        MsgBox("MatStreamConnect is not installed or has not been started at least once." & vbCrLf &
               "Please install and launch MatStreamConnect first.", vbExclamation, "MatStream")
        Return ""
    End If

    Return exePath
End Function
```

Inventor 2027 needs two habits in every rule — fully qualified `System.IO.…` names and the COM registry read above; see [Writing iLogic rules for Inventor 2027](inventor-3d-model.md#writing-ilogic-rules-for-inventor-2027).

---

## Supported workflows

| Workflow | Status |
|---|---|
| Publish an Inventor drawing with its PDF from iLogic | ✅ Supported |
| Publish a drawing's parts list as BOM children | ✅ Supported |
| Publish a part or assembly's 3D model from iLogic (`--publish-model`) | ✅ Supported — see [Inventor 3D model](inventor-3d-model.md) |
| Trigger publishes from any other automation via the command line | ✅ Supported |
| Open a stored file natively from the browser, check it back in on close | ✅ Supported — see [Files & documents](../entities/files.md) |

---

## Troubleshooting

**A rule reports *MatStreamConnect is not installed or has not been started at least once*:**
The `Run` registry entry is missing. Start Connect once (it registers itself), or turn *Open on Windows logon* on in Settings.

**Connect starts to the setup window every time:**
*Remember me* is off — that is by design. Tick it to stay signed in.

**Connect signs in to the wrong environment:**
A plain slug always means the hosted service. Paste the full workspace address to target another server.

**Log says *Import profile '…' not found*:**
The `--profile` argument does not match an Import Definition name in Configuration → Import Definitions.

**Log says the session expired / 401:**
Open the main window and log in again (**File → Login…**).

**Nothing happens after a rule runs:**
Open the main window — the **Activity** tab shows the command and its outcome. If it is empty, turn on **Settings → Debug mode** and check **File → Log Files…**.

---

## Browser connection detection

Connect exposes a local status service on `http://127.0.0.1:38610/` and, where supported, IPv6 loopback `::1` on the same port. The browser uses it to detect Connect and its active connection. The JSON response contains `app`, `loggedIn`, `userEmail`, `workspaceSlug`, `workspaceId`, and `workspaceName`. A status request also attempts to refresh an expired access token.

This endpoint reports status; it does not publish or check files in. It starts in the normal application startup path; a fresh launch directly with a publish command does not start this service in the current implementation. If the browser cannot detect an otherwise running Connect, inspect the log for a port-binding error and try a normal restart. A successful status response does not prove that a specific checkout or publish succeeded.

## Known limitations and recovery guidance

- Checkout tracking is in memory. Restarting Connect does not restore the tray list or file-close watchers. Local files and server locks can remain; preserve local edits and inspect the server checkout before deciding how to recover. Do not promise automatic checkout recovery after restart.
- File-close detection tests whether the file can be opened with exclusive access, rather than monitoring an application's exit. An application that releases its file lock early can trigger check-in while still running. Save edits before closing and verify the resulting version.
- A read-only download is not tracked in the checkout tray and has no automatic check-in or cleanup watcher.
- Optional Inventor assets run after check-in. Check-in success does not prove that a preview or thumbnail was uploaded. Use `Preview3D` and `Thumbnail` log entries and inspect the target entity.
- On a rejected check-in (403/409), local edits are preserved; see [Recovery after a rejected check-in](../entities/files.md#recovery-after-a-rejected-check-in). Other failures leave the tracked checkout available for a manual retry.

## Technical reference for agents

The desktop app source is in `MatStreamConnect`. Important implementation entry points:

| Source file | Responsibility |
|---|---|
| `App.xaml.cs` | Startup, workspace selection, reconnect loop, URI and forwarded-command handling |
| `SetupWindow.xaml.cs`, `SettingsWindow.xaml.cs` | Connection setup/history and application settings |
| `Auth/AuthManager.cs`, `Config/TokenStorage.cs`, `Config/CredentialStorage.cs` | Authentication, refresh, and protected persistence |
| `Checkout/CheckoutManager.cs` | Download/open, lock watcher, check-in, recovery, derived Inventor assets |
| `Inventor/InventorGlbExporter.cs`, `Inventor/InventorThumbnailExtractor.cs` | Inventor COM export and embedded thumbnail extraction |
| `Publishing/PublishArgs.cs`, `PublishRunner.cs`, `PublishQueue.cs` | Command contract, publish results, sequential queue |
| `Api/MatStreamApiClient.cs` | Core/Tenant API calls |
| `Tray/TrayManager.cs`, `MainWindow.xaml.cs`, `LocalStatusServer.cs` | Desktop controls and browser status |

Tenant routes below are relative to `/api/wid/{workspaceId}` on the resolved Tenant API and require the signed-in bearer token:

| Method | Route | Purpose |
|---|---|---|
| POST | `documents/stored-files/{storedFileId}/checkout` | Lock for editing; includes workstation name |
| GET | `documents/stored-files/{storedFileId}/content` | Download bytes |
| POST | `documents/stored-files/{storedFileId}/checkin` | Multipart file upload and file timestamps; returns entity, version, and stored file IDs |
| POST | `documents/stored-files/{storedFileId}/undo-checkout` | Release checkout |
| POST | `imports/publish` | Multipart Request JSON, plus Excel File for children; children can add `?updateLatestVersion=true` |
| POST | `documents/entities/{entityId}/assets/Viewer3D` | Upload GLB; optional `entityVerId` query targets the check-in version |
| POST | `documents/entities/{documentEntityId}/inventor-thumbnail` | Multipart `File`, `PropertyDefId`, `EntityVerId`, and `Target` |

Connect acts with the user's permissions. A configured profile, property ID, or running desktop process does not bypass API grants, checkout ownership, or lifecycle restrictions.

## Related

- [Inventor BOM import](inventor-bom.md) — the drawing and BOM rules
- [Inventor 3D model](inventor-3d-model.md) — the 3D model rule
- [Import Definitions](../features/import-definitions.md) — what each publish profile does
- [Files & documents](../entities/files.md) — opening and checking in files through Connect
