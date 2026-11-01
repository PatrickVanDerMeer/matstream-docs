# Downloads and Inventor setup

## MatStreamConnect portable

[Download MatStreamConnect for Windows x64](https://github.com/PatrickVanDerMeer/matstream-docs/releases/download/connect-v1.4.0/MatStreamConnect.zip)

Portable release **1.4.0** for Windows x64. [Release notes and both downloads](https://github.com/PatrickVanDerMeer/matstream-docs/releases/tag/connect-v1.4.0).

1. Extract the ZIP into `%LOCALAPPDATA%\MatStream\MatStreamConnect`, or another folder you can write to. Avoid Program Files.
2. Run `MatStreamConnect.exe`. Keep supporting files beside it. No installer, administrator rights, or separate .NET installation is required.
3. Paste your workspace address and sign in. Confirm the active workspace in Connect.
4. Optionally enable **Settings → Open on Windows logon**. iLogic discovery works with this off.

Connect registers browser links and its executable location for your Windows user on launch. Company policies may still restrict downloaded applications.

## Download from the workspace setup wizard

In MatStream Manage, select **Integrations → Autodesk Inventor / MatStreamConnect**, then click **Next → Apply**. After setup is applied, the wizard starts one ZIP download containing Connect and all five rules. The Done screen keeps a download link and setup instructions available.

[Download the combined Connect and Inventor package](https://github.com/PatrickVanDerMeer/matstream-docs/releases/download/connect-v1.4.0/MatStream-Inventor-Integration.zip)

Extract the package to a writable folder, run `MatStreamConnect.exe`, and add its `iLogic` subfolder to Inventor's External Rule Directories. If the automatic download is blocked, use the Done screen's download link.

## Inventor external rules

[Download the five iLogic rules](../downloads/MatStream-Inventor-iLogic.zip)

1. Extract all five `.iLogicVb` files into a permanent folder, such as `%LOCALAPPDATA%\MatStream\iLogic`.
2. In Inventor, open **Tools → Options → iLogic Configuration** and add the folder to **External Rule Directories**. Find the rules on the iLogic browser's **External Rules** tab.
3. Keep all five files together: the drawing rules call each other. Launch Connect once before running them.
4. Configure [Import Definitions](inventor-bom.md#import-definitions): the default profile names are **Inventor Main Item** and **Inventor Parts List**. Match the profile names and field mappings to your workspace.
5. With a saved drawing active, run **MatStream_PublishDrawing**. With a saved part or assembly active, run **MatStream_Publish3DModel**; its configured key must identify an existing MatStream entity. See [3D prerequisites](inventor-3d-model.md#prerequisites).
6. Check Connect's **Activity** log and the resulting records. After a successful manual test, optionally assign the appropriate rule to **After Save Document**.

The package also contains **MatStream_WriteMetadata**, **MatStream_PublishDrawingAsPdf**, and **MatStream_PublishDrawingPartsList** for the individual drawing steps.

## Updates and moving folders

Exit Connect through **File → Exit** or its tray menu. Extract the new ZIP over the application files and launch Connect again. The ZIP contains no user settings, tokens, or credentials. Preserve the existing `MatStreamConnect.json` beside the executable; credentials and tokens remain in your Windows profile.

After moving the folder, launch Connect from its new location to refresh its registrations. Update the rules from the matching release, preserving local profile/key customizations separately.

See [Connect help](matstream-connect.md), [drawing and BOM publishing](inventor-bom.md), and the [assembly walkthrough](../workflows/inventor-assembly-bom.md).
