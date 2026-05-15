# MatStreamConnect

MatStreamConnect is a lightweight Windows desktop companion application that acts as a bridge between local desktop tools (such as Autodesk Inventor via iLogic) and the MatStream API. It enables workflows that a browser cannot support — such as detecting that a file has been saved in a local CAD application and triggering an upload or import automatically.

---

## Installation

MatStreamConnect is distributed as a Windows installer. After installation it appears as a tray icon in the Windows system tray.

**Default install path:** `%APPDATA%\MatStream\MatStreamConnect\MatStreamConnect.exe`

The iLogic rules that call MatStreamConnect check this path before launching. If the file is not found, the rule shows an error and stops.

---

## Tray icon

MatStreamConnect runs silently in the system tray after startup. You can interact with it via:

- **Double-click** the tray icon → opens the main window.
- **Right-click** the tray icon → context menu with:
  - **Open** — opens the main window (same as double-click).
  - Other menu items depending on the installed version.

Closing the main window hides it — MatStreamConnect continues running in the tray. It does not fully exit until you choose Exit from the tray menu or stop it from Task Manager.

---

## Connection configuration

MatStreamConnect connects to a MatStream workspace using a `connection.json` file:

**Location:** `%APPDATA%\MatStream\MatStreamConnect\connection.json`

**Fields:**

| Field | Description |
|---|---|
| `WorkspaceSlug` | Short workspace identifier (e.g. `a5u038`) |
| `WorkspaceId` | Numeric workspace ID |
| `TenantApiBaseUrl` | Base URL of the MatStream Tenant API (e.g. `https://api.matstream.com`) |
| `CoreApiBaseUrl` | Base URL of the MatStream Core API (e.g. `https://core.matstream.com`) |
| `WorkspaceName` | Display name of the workspace |

**Example:**
```json
{
  "WorkspaceSlug": "a5u038",
  "WorkspaceId": 3,
  "TenantApiBaseUrl": "https://api.matstream.com",
  "CoreApiBaseUrl": "https://core.matstream.com",
  "WorkspaceName": "PM Software"
}
```

The first time you use MatStreamConnect, open the main window and log in. Logging in writes `connection.json` automatically. After login, both `TenantApiBaseUrl` and `CoreApiBaseUrl` are preserved in the file. A custom `CoreApiBaseUrl` (e.g. for a test environment) is not overwritten when you re-authenticate.

---

## Triggering imports from iLogic

MatStreamConnect accepts command-line arguments from iLogic rules (or any other automation) to trigger import runs without requiring user interaction with the main window.

### iLogic call pattern (VB.NET)

```vb
Dim gatewayPath = System.IO.Path.Combine(
    System.Environment.GetFolderPath(System.Environment.SpecialFolder.ApplicationData),
    "MatStream", "MatStreamConnect", "MatStreamConnect.exe")

If Not System.IO.File.Exists(gatewayPath) Then
    MsgBox("MatStreamConnect not found. Please install it first.", vbExclamation, "MatStream")
    Return
End If

' Build arguments and call
Dim args = $"import --profile ""{profileName}"" --file ""{excelPath}"" --metadata '{metadataJson}'"
Shell(gatewayPath & " " & args)
```

The `--profile` argument must match the **Name** field of an Import Definition configured in MatStream.

### What happens on import trigger

1. MatStreamConnect receives the command-line arguments.
2. It reads `connection.json` to get the workspace credentials.
3. It calls `POST /api/wid/{workspaceSlug}/imports/{profileName}/run` with the Excel file and metadata.
4. MatStream processes the import and returns batch result counts.
5. MatStreamConnect logs the result; on error, it can show a notification.

---

## Supported workflows

| Workflow | Status |
|---|---|
| Import Inventor Drawing BOM from iLogic | ✅ Supported |
| Attach drawing PDF to main entity on import | ✅ Supported |
| Trigger import from command line / other automation | ✅ Supported |
| Open/edit local CAD file and upload on save | 🗓 Planned (native file companion feature) |

---

## Troubleshooting

**MatStreamConnect not found by iLogic:**
Check that the connect is installed at `%APPDATA%\MatStream\MatStreamConnect\MatStreamConnect.exe`. Run the installer if missing.

**CoreApiBaseUrl missing from connection.json:**
This happens if MatStreamConnect was installed before the `CoreApiBaseUrl` field was added. Open the main window, log out, and log in again — this regenerates `connection.json` with all required fields.

**Import fails with 401 Unauthorized:**
The API key or session token stored by MatStreamConnect has expired. Open the main window and re-authenticate.

**Import fails with profile not found:**
The `--profile` argument passed by iLogic does not match any Import Definition name in MatStream. Check the profile name in Configuration → Import Definitions.

---

## Related

- [Import Definitions](../features/import-definitions.md) — configure what each import profile does
- [Inventor BOM import](../integrations/inventor-bom.md) — step-by-step guide for the Inventor workflow