# Inventor BOM Import

MatStream integrates with Autodesk Inventor to import drawing BOMs directly into MatStream without manual data entry. The workflow uses an iLogic rule in Inventor and the [MatStreamConnect](matstream-connect.md) desktop companion app as the bridge.

---

## Overview

```
Inventor drawing open in Inventor
  → iLogic rule triggered (manually or on save)
      → Reads iProperties (Part Number, Description, Revision, Author, …)
      → Exports BOM to a temporary Excel file
      → Launches MatStreamConnect with profile name + file path + metadata
          → MatStreamConnect calls POST /api/wid/{slug}/imports/{profileName}/run
          → MatStream creates or updates the main drawing entity + BOM children
          → Optionally attaches the drawing PDF
```

The entire flow happens in seconds and requires no manual steps after the iLogic rule fires.

---

## Prerequisites

1. **MatStreamConnect installed** on the Inventor workstation — see [MatStreamConnect](matstream-connect.md).
2. **Import Definition configured** in MatStream with `Source = "Inventor Drawing BOM"` (or whichever name your iLogic rule uses) — see [Import Definitions](../features/import-definitions.md).
3. **iLogic rule deployed** in the Inventor vault or local rules folder.
4. **User logged in** to MatStreamConnect so it has a valid session token.

---

## iLogic rule

The iLogic rule performs these steps:

1. Checks that MatStreamConnect is installed at `%APPDATA%\MatStream\MatStreamConnect\MatStreamConnect.exe`. If not found, shows an error and stops.
2. Reads iProperties from the active drawing document (Part Number, Description, Revision, Author, etc.).
3. Exports the drawing's BOM to a temporary Excel file in `%TEMP%`.
4. Builds a metadata JSON string from the iProperties.
5. Calls MatStreamConnect via `Shell()` with the following arguments:

```
MatStreamConnect.exe import --profile "ProfileName" --file "C:\Temp\bom.xlsx" --metadata '{"PN":"MAT-001","Rev":"A"}'
```

**Argument reference:**

| Argument | Description |
|---|---|
| `--profile` | Name of the Import Definition in MatStream (must match exactly) |
| `--file` | Path to the exported Excel BOM file |
| `--metadata` | JSON string of iProperties to map to the main drawing entity |
| `--pdf` | *(optional)* Path to the drawing PDF to attach to the main entity |

### VB.NET snippet

```vb
Dim gatewayPath = System.IO.Path.Combine(
    System.Environment.GetFolderPath(System.Environment.SpecialFolder.ApplicationData),
    "MatStream", "MatStreamConnect", "MatStreamConnect.exe")

If Not System.IO.File.Exists(gatewayPath) Then
    MsgBox("MatStreamConnect not found. Please install it first.", vbExclamation, "MatStream")
    Return
End If

Dim profileName = "Inventor Drawing BOM"
Dim excelPath   = System.IO.Path.Combine(System.IO.Path.GetTempPath(), "bom_export.xlsx")
Dim metadataJson = $"{{""PN"":""{iProperties.Value("Project", "Part Number")}"", " &
                    $"""Rev"":""{iProperties.Value("Project", "Revision Number")}""}}"

' Export BOM to Excel here (use Inventor BOM API)
' ...

Dim args = $"import --profile ""{profileName}"" --file ""{excelPath}"" --metadata '{metadataJson}'"
Shell("""" & gatewayPath & """ " & args)
```

---

## Configuring the Import Definition

Create an Import Definition in MatStream with these settings:

| Setting | Value |
|---|---|
| Name | Must match the `--profile` argument exactly (e.g. `Inventor Drawing BOM`) |
| Source | `Inventor Drawing BOM` |
| Configuration | `DocumentWithBOM` |
| Target category | The category for the drawing entity (e.g. Drawing) |
| Default child category | The category for BOM children (e.g. Purchased Part) |

On the **Field Mappings** tab, map:

| Source column (Excel) | MatStream property |
|---|---|
| `Part Number` | Entity number key field |
| `Description` | Description property |
| `Quantity` | Quantity property |
| `Material` | Material property |
| (from metadata JSON) `PN` | Drawing entity number |
| (from metadata JSON) `Rev` | Drawing revision |

On the **Category Assignment Rules** tab, add rules if different BOM row types (purchased, manufactured, standard) should go to different categories.

---

## What MatStreamConnect does

When invoked with `import` arguments:

1. Reads `connection.json` to get workspace credentials.
2. Posts a multipart request to `POST /api/wid/{slug}/imports/{profileName}/run` with:
   - The Excel file as a file attachment
   - The metadata JSON as a form field
   - The PDF path as an optional file attachment
3. Logs the result (insert count, update count, error count).
4. On error, shows a Windows notification with the error message.

MatStreamConnect does not require the main window to be open. It processes the import silently in the background while Inventor remains in focus.

---

## Troubleshooting

**"MatStreamConnect not found":**
Install MatStreamConnect from the MatStream downloads page. Default path: `%APPDATA%\MatStream\MatStreamConnect\MatStreamConnect.exe`.

**"Profile not found":**
The `--profile` argument does not match any Import Definition name. Check the name in Configuration → Import Definitions. Names are case-sensitive.

**"401 Unauthorized":**
MatStreamConnect's session has expired. Open the main window and log in again.

**No entities created, no error:**
The Excel export was empty or the BOM has no rows. Check that the drawing has a BOM defined in Inventor.

**CoreApiBaseUrl missing from connection.json:**
Open MatStreamConnect, log out, and log in again to regenerate `connection.json` with all required fields.

---

## Related

- [MatStreamConnect](matstream-connect.md) — installation and configuration
- [Import Definitions](../features/import-definitions.md) — configure the import profile
- [Vault sync](../vault-integration/sync.md) — alternative integration for Vault-managed data
