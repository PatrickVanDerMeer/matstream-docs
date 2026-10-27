# Inventor BOM import

For a step-by-step example using existing fittings and a new part, follow the [Inventor assembly and BOM workflow](../workflows/inventor-assembly-bom.md).

MatStream imports drawings and their bills of materials straight from Autodesk Inventor: an iLogic rule exports what Inventor knows, and the [MatStreamConnect](matstream-connect.md) desktop app hands it to MatStream through an [Import Definition](../features/import-definitions.md). No manual data entry, no Excel round-trip by hand.

```
Inventor drawing
  → iLogic rule (manually, or on save)
      → writes the drawing's iProperties to a metadata JSON file
      → exports the drawing to PDF
      → exports the parts list to Excel
      → MatStreamConnect.exe --publish-drawing  --profile "Inventor Main Item"       … --file <pdf>
      → MatStreamConnect.exe --publish-children --profile "Inventor Parts List" … --bom  <xlsx>
          → MatStream creates or updates the BOM parent from metadata,
            publishes a separate <number>.pdf Drawing document linked under it,
            and upserts the BOM rows as the parent's children
```

For the part or assembly's 3D model, see [Inventor 3D model](inventor-3d-model.md) — a separate, simpler rule.

---

## Prerequisites

1. **MatStreamConnect installed and started at least once** on the Inventor workstation, logged in to the workspace. The rules find the executable through the registry entry Connect writes on first start.
2. **Import Definitions configured** in MatStream — one for the BOM parent, one for its BOM children (below). A Library **Drawing** category allowing direct file attachment is required for the separate PDF document; Inventor setup provides it. Use matching updated versions of Connect and the tenant API.
3. **iLogic rules deployed** as external rules in Inventor's rule folder.

---

## Commands

MatStreamConnect accepts these publish commands from the command line. Connect need not be visible — a running instance picks the arguments up, shows its window and reports progress in the **Activity** log.

| Command | What it does |
|---|---|
| `--publish-entity` | Creates or updates one entity from the metadata; optionally attaches `--file` to it |
| `--publish-drawing` | Creates or updates the parent from metadata, then publishes `--file` as a separate Drawing document linked under it |
| `--publish-children` | Upserts the rows of an Excel BOM as children of the entity the metadata identifies |
| `--publish-model` | Attaches a GLB as an existing entity's 3D model — see [Inventor 3D model](inventor-3d-model.md) |

| Argument | Used by | Description |
|---|---|---|
| `--profile "<name>"` | entity, drawing, children | Name of the Import Definition — must match exactly |
| `--metadata <path or JSON>` | entity, drawing, children | The iProperties: a path to a JSON file (recommended — no quoting problems) or an inline JSON object |
| `--file "<path>"` | entity, drawing | Entity command: direct attachment. Drawing command: PDF for a separate linked Drawing document named after the file |
| `--bom "<path>"` | children | The Excel parts-list export |
| `--update-latest-version` | children | Update the parent's current version in place instead of creating a new one |

Example:

```
MatStreamConnect.exe --publish-drawing --profile "Inventor Main Item" --metadata "C:\Users\me\AppData\Local\Temp\D-1042_meta.json" --file "C:\Users\me\AppData\Local\Temp\D-1042.pdf"
MatStreamConnect.exe --publish-children --profile "Inventor Parts List" --metadata "C:\Users\me\AppData\Local\Temp\D-1042_meta.json" --bom "C:\Users\me\AppData\Local\Temp\D-1042_BOM.xlsx"
```

The metadata file is a flat JSON object. Its **keys are the source column names** the Import Definition's field mappings expect:

```json
{
  "Part Number": "D-1042",
  "Description": "Skid frame, welded",
  "Revision": "B",
  "Author": "P. van der Meer",
  "Title": "Skid frame",
  "Designer": "",
  "Company": "PM Software",
  "Project": "WF-2023"
}
```

---

## Import Definitions

Two definitions in **Configuration → Import Definitions**:

**Main item / BOM parent** (used by `--publish-drawing`)

| Setting | Value |
|---|---|
| Name | Must match `--profile` exactly, e.g. `Inventor Main Item` |
| Configuration | `MainItem` — the parent created or updated from metadata; existing `File` profiles remain supported |
| Target category | The parent category, e.g. *Weld Assembly* for the engineering sample |
| Field mappings | metadata key → property; one mapping is the **key field** that finds the existing parent on re-publish |

For the engineering sample, map metadata `Part Number` to system **Name** as the parent search key. The BOM stays on `023.713.101`; the PDF is stored on a separate Drawing named `023.713.101.pdf`, linked in the parent's Uses. Re-publishing reuses that document by filename within the Drawing category. A matching PDF left directly on the parent by the old publisher is removed after the separate document is safely published and linked. Other direct attachments are preserved.

**BOM children** (used by `--publish-children`)

| Setting | Value |
|---|---|
| Name | e.g. `Inventor Parts List` |
| Configuration | `ChildrenFromExcel` — locate the parent from the metadata key, then upsert the Excel rows as its children |
| Default child category | *Part* — required unless category assignment rules cover every row; a row that matches neither fails |
| Field mappings | Excel column → property (`Part Number`, `Description`, `QTY`, `Material`, …). Do **not** map `ITEM` (the balloon number) to a part property: it describes the BOM line, and on the part it changes — and creates a new version — whenever the part sits at another position in another drawing |
| Category assignment rules | optional — route rows to different categories by a column value |

!!! note "Configuration names"
    Older documentation and profiles call these `DocumentWithBOM` and `ItemList`. The current names are `File` (or `MainItem` for an entity without a file) and `ChildrenFromExcel`; existing profiles keep working.

---

## The iLogic rules

Four external rules in Inventor's rules folder. One does the work per step; `MatStream_PublishDrawing` just runs them in order. They are written for Inventor 2027 and run on older versions too — see [Writing iLogic rules for Inventor 2027](inventor-3d-model.md#writing-ilogic-rules-for-inventor-2027) for the two habits they follow.

| Rule | Does |
|---|---|
| `MatStream_WriteMetadata` | iProperties → `%TEMP%\<drawing>_meta.json` |
| `MatStream_PublishDrawingAsPdf` | metadata → PDF export → `--publish-drawing --profile "Inventor Main Item"` |
| `MatStream_PublishDrawingPartsList` | metadata → parts-list Excel export → `--publish-children --profile "Inventor Parts List"` |
| `MatStream_PublishDrawing` | runs the two above; attach it to **After Save Document** to publish on every save |

The shared helper `FindMatStreamConnectExe` (registry lookup through `WScript.Shell`) is shown on the [MatStreamConnect](matstream-connect.md#calling-connect-from-ilogic) page and repeated in each rule so every rule stands on its own.

### `MatStream_WriteMetadata.iLogicVb`

```vb
Sub Main()
    Dim path As String = WriteMetadata()
End Sub

Function WriteMetadata() As String
    Dim drawingName As String = System.IO.Path.GetFileNameWithoutExtension(ThisDoc.Document.FullFileName)
    Dim tempFolder  As String = System.IO.Path.GetTempPath()
    Return WriteMetadataFile(tempFolder, drawingName)
End Function

Function WriteMetadataFile(tempFolder As String, drawingName As String) As String
    Dim entries As New System.Text.StringBuilder()
    AppendEntry(entries, "Part Number", SafeProperty("Project", "Part Number"))
    AppendEntry(entries, "Description", SafeProperty("Project", "Description"))
    AppendEntry(entries, "Revision",    SafeProperty("Project", "Revision Number"))
    AppendEntry(entries, "Author",      SafeProperty("Summary", "Author"))
    AppendEntry(entries, "Title",       SafeProperty("Summary", "Title"))
    AppendEntry(entries, "Designer",    SafeProperty("Summary", "Manager"))
    AppendEntry(entries, "Company",     SafeProperty("Summary", "Company"))
    AppendEntry(entries, "Project",     SafeProperty("Project", "Project"))

    Dim json As String = "{" & vbCrLf & entries.ToString() & vbCrLf & "}"
    Dim metaPath As String = System.IO.Path.Combine(tempFolder, drawingName & "_meta.json")
    System.IO.File.WriteAllText(metaPath, json, New System.Text.UTF8Encoding(False))
    Return metaPath
End Function

Sub AppendEntry(sb As System.Text.StringBuilder, key As String, value As String)
    If sb.Length > 0 Then sb.Append(",").Append(vbCrLf)
    sb.Append("  """).Append(JsonEscape(key)).Append(""": """).Append(JsonEscape(value)).Append("""")
End Sub

Function SafeProperty(category As String, propName As String) As String
    Try
        Dim raw As Object = iProperties.Value(category, propName)
        Return If(raw Is Nothing, "", raw.ToString())
    Catch
        Return ""
    End Try
End Function

' Backslash, quote and control characters — a multi-line Description would otherwise break the JSON.
Function JsonEscape(s As String) As String
    Return s.Replace("\", "\\").Replace("""", "\""").Replace(vbCr, "").Replace(vbLf, "\n").Replace(vbTab, "\t")
End Function
```

### `MatStream_PublishDrawingAsPdf.iLogicVb`

```vb
Sub Main()
    Dim connectPath As String = FindMatStreamConnectExe()
    If String.IsNullOrWhiteSpace(connectPath) Then Return

    iLogicVb.RunExternalRule("MatStream_WriteMetadata.iLogicVb")

    Dim profileName As String = "Inventor Main Item"
    Dim drawingName As String = System.IO.Path.GetFileNameWithoutExtension(ThisDoc.Document.FullFileName)
    Dim tempFolder  As String = System.IO.Path.GetTempPath()
    Dim metaPath    As String = System.IO.Path.Combine(tempFolder, drawingName & "_meta.json")
    Dim pdfPath     As String = ExportDrawingToPdf(tempFolder, drawingName)

    Dim args As String = "--publish-drawing" &
                         " --profile """ & profileName & """" &
                         " --metadata """ & metaPath & """" &
                         " --file """ & pdfPath & """"

    Shell("""" & connectPath & """ " & args)
End Sub

Function ExportDrawingToPdf(tempFolder As String, drawingName As String) As String
    Dim pdfPath As String = System.IO.Path.Combine(tempFolder, drawingName & ".pdf")
    ThisDoc.Document.SaveAs(pdfPath, True)
    Return pdfPath
End Function

' + FindMatStreamConnectExe — see the MatStreamConnect page
```

### `MatStream_PublishDrawingPartsList.iLogicVb`

```vb
Sub Main()
    Dim profileName As String = "Inventor Parts List"
    Dim updateLatestVersion As Boolean = False   ' True = update the parent in place, no version bump

    Dim connectPath As String = FindMatStreamConnectExe()
    If String.IsNullOrWhiteSpace(connectPath) Then Return
    If Not DrawingHasPartsList() Then Return

    iLogicVb.RunExternalRule("MatStream_WriteMetadata.iLogicVb")

    Dim drawingName As String = System.IO.Path.GetFileNameWithoutExtension(ThisDoc.Document.FullFileName)
    Dim tempFolder  As String = System.IO.Path.GetTempPath()
    Dim metaPath    As String = System.IO.Path.Combine(tempFolder, drawingName & "_meta.json")
    Dim bomPath     As String = ExportBomToExcel(tempFolder, drawingName)
    If String.IsNullOrWhiteSpace(bomPath) Then Return

    Dim args As String = "--publish-children" &
                         " --profile """ & profileName & """" &
                         " --metadata """ & metaPath & """" &
                         " --bom """ & bomPath & """"
    If updateLatestVersion Then args = args & " --update-latest-version"

    Shell("""" & connectPath & """ " & args)
End Sub

Function DrawingHasPartsList() As Boolean
    For Each sheet As Sheet In ThisDoc.Document.Sheets
        If sheet.PartsLists.Count > 0 Then Return True
    Next
    MsgBox("No parts list found in this drawing. Add a parts list first.", vbExclamation, "MatStream")
    Return False
End Function

Function ExportBomToExcel(tempFolder As String, drawingName As String) As String
    Dim bomPath As String = System.IO.Path.Combine(tempFolder, drawingName & "_BOM.xlsx")
    Dim partsList As PartsList = Nothing
    For Each sheet As Sheet In ThisDoc.Document.Sheets
        If sheet.PartsLists.Count > 0 Then
            partsList = sheet.PartsLists(1)
            Exit For
        End If
    Next
    If partsList Is Nothing Then Return ""

    If System.IO.File.Exists(bomPath) Then System.IO.File.Delete(bomPath)
    partsList.Export(bomPath, PartsListFileFormatEnum.kMicrosoftExcel)
    Return bomPath
End Function

' + FindMatStreamConnectExe — see the MatStreamConnect page
```

### `MatStream_PublishDrawing.iLogicVb`

```vb
Sub Main()
    If ThisDoc.Document.DocumentType <> DocumentTypeEnum.kDrawingDocumentObject Then
        MsgBox("Open a drawing first.", vbExclamation, "MatStream")
        Return
    End If

    iLogicVb.RunExternalRule("MatStream_PublishDrawingAsPdf.iLogicVb")
    iLogicVb.RunExternalRule("MatStream_PublishDrawingPartsList.iLogicVb")
End Sub
```

### `MatStream_Publish3DModel.iLogicVb`

Run on a part or assembly; documented in [Inventor 3D model](inventor-3d-model.md).

---

## Reading the result

Everything Connect does appears in its **Activity** log:

```
[publish-drawing] Profile: Inventor Main Item
[publish-drawing] Profile resolved (id: 12)
[publish-drawing] Update: Drawing D-1042 updated
[publish-drawing] Publishing drawing document: D-1042.pdf
[publish-drawing] ✔ Drawing document 501 linked to parent 500
[publish-children] Profile: Inventor Parts List
[publish-children] BOM: D-1042_BOM.xlsx
[publish-children] ✔ 3 inserted, 9 updated, 0 errors
```

Warnings (`⚠`) list rows the category rules could not place or values that did not map; row errors (`❌ Row 7 (…)`) name the offending Excel row and key.

---

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| Rule: *MatStreamConnect is not installed or has not been started at least once* | The registry `Run` entry is missing. Start Connect once; it registers itself. |
| Rule compile error: *'Path' is ambiguous* / *'Registry' is not a member of 'Microsoft.Win32'* | Inventor 2027 iLogic — see [Writing iLogic rules for Inventor 2027](inventor-3d-model.md#writing-ilogic-rules-for-inventor-2027). |
| Log: *Import profile 'X' not found* | `--profile` does not match an Import Definition name (case-insensitive, but exact). |
| Log: *401* or *Session expired* | Open Connect's window and log in again. |
| Children published but the parent was not found | The metadata key the BOM profile uses to locate the parent does not match the drawing's key field — check both profiles' field mappings. |
| Nothing inserted, no error | The parts list export was empty; check the drawing has a parts list with rows. |
| Row error: *Could not determine Library Definition category* | The BOM profile has no **Default child category** and no category assignment rule matched the row. Set the default (e.g. *Part*). |
| Row error: *Not linked to the parent: …* | The part was created or updated in the Library, but the drawing's lifecycle state does not allow its BOM to change. Move the drawing to an editable state and publish again. |
| Warning: *rows missing from this BOM were NOT unlinked* | At least one row failed, so MatStream did not remove old BOM lines this time — a failed row would otherwise look removed. Fix the row errors and publish again. |

---

## Related

- [MatStreamConnect](matstream-connect.md) — installation, configuration, all commands
- [Import Definitions](../features/import-definitions.md) — field mappings and category rules
- [Inventor 3D model](inventor-3d-model.md) — the 3D model rule
