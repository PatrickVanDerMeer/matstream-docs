# Inventor 3D model

An iLogic rule exports the open part or assembly to GLB and hands it to [MatStreamConnect](matstream-connect.md), which attaches it as the **3D model** of the matching MatStream item. Nothing is checked out or in — MatStream keeps the item, Inventor keeps the file, and the model is just the picture that links them.

```
Inventor part / assembly
  → iLogic rule (manually, or on save)
  → Document.SaveAs(*.glb)  →  %TEMP%\<key>.glb
  → MatStreamConnect.exe --publish-model --key "<Name>" --file "<glb>"
  → MatStream: model attached to the latest version of the entity named <Name>
```

The result is viewable on the item's [3D Model tab](../features/3d-model.md) — including for older versions, which keep the model they had at the time.

---

## Automatic preview after Inventor check-in

There are two distinct GLB workflows. Use this distinction when helping a user or selecting an automation:

| Workflow | Trigger | Target version | Source cleanup |
|---|---|---|---|
| iLogic / `--publish-model` | Export a GLB and invoke the command | Latest version of the entity matching `--key`; no new entity/version or checkout | Supplied GLB deleted after successful upload |
| Automatic check-in preview | Successfully check in an Inventor `.ipt` or `.iam` through Connect | Exact entity version returned by check-in | Local checkout copy and generated GLB removed after asset processing, including when preview generation fails |

Automatic preview requires Inventor installed with a working glTF translator. Connect reuses a running Inventor instance where possible; otherwise it starts a hidden instance, which may require a licence seat and take 15–30 seconds, then shuts down the instance it started. It opens the source without saving changes to it. Drawings (`.idw`, `.dwg`) and other file extensions do not trigger this automatic export path.

File check-in is already complete before preview generation runs. Export/upload failures are recorded under `Preview3D` in the log; they do not roll back check-in or produce a file check-in failure. Enable Debug mode to inspect normal export progress, and refresh/reopen the entity to view the result. Missing references or unavailable Inventor/translator can prevent generation; inspect the logged error before retrying.

Optional embedded thumbnail extraction runs in the same post-check-in workflow but independently of GLB generation. Configure its Thumbnail property ID and target in [Connect application settings](matstream-connect.md#application-settings-and-inventor-thumbnails). A thumbnail failure does not prevent a GLB attempt. The iLogic `--publish-model` command itself does not extract or upload thumbnails.

## Prerequisites

1. **Inventor 2023 or newer.** The glTF translator ships with Inventor from 2023; older versions need Autodesk's *glTF Exporter* add-in from the App Store.
2. **MatStreamConnect installed and started at least once** on the machine, logged in to the right workspace. The rule finds the executable through the registry entry Connect writes on its first start.
3. **The item already exists in MatStream.** The rule does not create entities — that is what `--publish-entity` and `--publish-children` with an import profile are for. It matches the key against the entity **Name** and reports *No entity named "…"* when there is no match.
4. **A 3D Model display tab** assigned to the category, so there is somewhere to see the result (Configuration → Display Tabs).

No import definition is involved. The only mapping is *which iProperty holds the MatStream Name*, and that is one line in the rule.

---

## The command

```
MatStreamConnect.exe --publish-model --key "763.901.130" --file "C:\Users\me\AppData\Local\Temp\763.901.130.glb"
```

| Argument | Description |
|---|---|
| `--key` | The entity **Name** in MatStream. Matched exactly, case-insensitively. |
| `--file` | Path to the exported `.glb`. Connect deletes it after a successful upload, so write it to `%TEMP%`. |

Connect's **Activity** log reports what happened:

```
[publish-model] 763.901.130: 763.901.130.glb
[publish-model] ✔ 3D model attached to entity 11087 (file 222)
```

or `❌ No entity named "…" in this workspace` / `❌ Model file not found`.

---

## The iLogic rule

Save as an external rule named `MatStream_Publish3DModel.iLogicVb`. Adjust the one **Settings** line if your workspace keys items on a different iProperty than *Stock Number*.

```vb
' ============================================================
' iLogic Rule — MatStream_Publish3DModel
'
' Exports the active part/assembly to GLB and sends it to
' MatStreamConnect, which attaches it as the 3D model of the
' entity with the same Name (= Stock Number) — latest version,
' no check-out, no new revision, no Document entity.
' ============================================================

Sub Main()
    Dim connectPath As String = FindMatStreamConnectExe()
    If String.IsNullOrWhiteSpace(connectPath) Then Return

    Dim doc As Document = ThisDoc.Document
    If doc.DocumentType <> DocumentTypeEnum.kPartDocumentObject AndAlso
       doc.DocumentType <> DocumentTypeEnum.kAssemblyDocumentObject Then
        MsgBox("Open a part or assembly first.", vbExclamation, "MatStream")
        Return
    End If

    ' ── Settings: the iProperty that carries the MatStream entity Name ───────
    Dim key As String = If(iProperties.Value("Project", "Stock Number"), "").Trim()
    If key = "" Then
        MsgBox("Stock Number is empty — MatStream cannot tell which item this is.", vbExclamation, "MatStream")
        Return
    End If

    ' ── Step 1: Export GLB to temp (Connect deletes it after the upload) ─────
    Dim tempFolder As String = System.IO.Path.GetTempPath()
    Dim safeName   As String = String.Join("_", key.Split(System.IO.Path.GetInvalidFileNameChars()))
    Dim glbPath    As String = ExportModelToGlb(doc, tempFolder, safeName)
    If String.IsNullOrWhiteSpace(glbPath) Then Return

    ' ── Step 2: Hand it to MatStreamConnect ──────────────────────────────────
    Dim modelArgs As String = "--publish-model" &
                              " --key """ & key & """" &
                              " --file """ & glbPath & """"

    Shell("""" & connectPath & """ " & modelArgs)
End Sub

' ── Helpers ───────────────────────────────────────────────────────────────────

Function FindMatStreamConnectExe() As String
    Dim exePath As String = ""
    Try
        ' WScript.Shell rather than Microsoft.Win32.Registry: that class lives in a separate
        ' assembly on the .NET that Inventor 2027's iLogic runs on, and the rule compiler does
        ' not reference it. COM needs nothing and works in every Inventor version.
        Dim shellObj As Object = CreateObject("WScript.Shell")
        Dim regValue As Object = shellObj.RegRead("HKCU\Software\Microsoft\Windows\CurrentVersion\Run\MatStreamConnect")
        If regValue IsNot Nothing Then
            exePath = regValue.ToString().Trim(""""c)
        End If
    Catch
        ' A missing key throws — handled by the check below.
    End Try

    If String.IsNullOrWhiteSpace(exePath) OrElse Not System.IO.File.Exists(exePath) Then
        MsgBox("MatStreamConnect is not installed or has not been started at least once." & vbCrLf &
               "Please install and launch MatStreamConnect first.", vbExclamation, "MatStream")
        Return ""
    End If

    Return exePath
End Function

' Document.SaveAs(path, SaveCopyAs:=True) routes through the glTF translator that ships with
' Inventor 2023+. If that is refused on this machine, the translator is driven directly.
Function ExportModelToGlb(doc As Document, tempFolder As String, safeName As String) As String
    Dim glbPath As String = System.IO.Path.Combine(tempFolder, safeName & ".glb")
    If System.IO.File.Exists(glbPath) Then System.IO.File.Delete(glbPath)

    Try
        doc.SaveAs(glbPath, True)
        If System.IO.File.Exists(glbPath) Then Return glbPath
    Catch
    End Try

    Dim translator As TranslatorAddIn = Nothing
    For Each addIn As ApplicationAddIn In ThisApplication.ApplicationAddIns
        If addIn.AddInType = ApplicationAddInTypeEnum.kTranslationApplicationAddIn AndAlso
           addIn.DisplayName.IndexOf("gltf", StringComparison.OrdinalIgnoreCase) >= 0 Then
            translator = CType(addIn, TranslatorAddIn)
            Exit For
        End If
    Next

    If translator Is Nothing Then
        MsgBox("No glTF translator found in this Inventor (needs Inventor 2023+ or Autodesk's glTF Exporter add-in).",
               vbExclamation, "MatStream")
        Return ""
    End If

    Try
        If Not translator.Activated Then translator.Activate()
        Dim context As TranslationContext = ThisApplication.TransientObjects.CreateTranslationContext()
        context.Type = IOMechanismEnum.kFileBrowseIOMechanism
        Dim options As NameValueMap = ThisApplication.TransientObjects.CreateNameValueMap()
        Dim medium As DataMedium = ThisApplication.TransientObjects.CreateDataMedium()
        medium.FileName = glbPath
        translator.SaveCopyAs(doc, context, options, medium)
        If System.IO.File.Exists(glbPath) Then Return glbPath
    Catch ex As Exception
        MsgBox("GLB export failed: " & ex.Message, vbExclamation, "MatStream")
    End Try

    Return ""
End Function
```

To publish automatically, attach the rule to the **After Save Document** event trigger for parts and assemblies.

Assemblies export as a single GLB with every component in place, so one rule run on the top-level assembly gives MatStream the whole thing.

---

## Writing iLogic rules for Inventor 2027

Two things that bit while porting the MatStream rules from Inventor 2023 to 2027, and that apply to the older BOM rules as well:

- **Qualify `System.IO` and `System.Environment` fully.** The `Inventor` namespace has its own `Path`, `File` and `Environment` types, so bare `Path.Combine(...)` or `File.Exists(...)` is *ambiguous* and the rule will not compile. Write `System.IO.Path.Combine(...)`, `System.IO.File.Exists(...)`, `System.Environment.GetFolderPath(...)`.
- **`Microsoft.Win32.Registry` is not available.** Inventor 2027's iLogic runs on a newer .NET where the registry API lives in a separate assembly the rule compiler does not reference. Read the registry through COM instead — `CreateObject("WScript.Shell").RegRead(...)` as in the helper above.

---

## Troubleshooting

| Symptom | Cause |
|---|---|
| Rule reports *MatStreamConnect is not installed or has not been started at least once* | The `Run` registry value is missing. Start Connect once; it registers itself. |
| Rule reports *No glTF translator found* | Inventor older than 2023 without the glTF Exporter add-in. |
| Connect logs *No entity named "…" in this workspace* | The iProperty used as key does not equal any entity Name — check the Settings line in the rule and the item's Name in the Library. |
| Model attached but the tab still shows the old one | Refresh the entity; the tab loads once per open dialog. |
| Model looks right but threads are missing | Expected: Inventor threads are cosmetic, not geometry. |

---

## Related

- [3D Model viewer](../features/3d-model.md) — using the tab
- [MatStreamConnect](matstream-connect.md) — installation and the other commands
- [Inventor BOM import](inventor-bom.md) — publishing drawings and BOMs from iLogic
