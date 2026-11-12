# Your account menu

Your email address at the right end of the page header opens the account menu. It holds everything personal: your profile, your password, display preferences, your default workspace, the colour theme and **Logout**.

| Item | What it does |
|---|---|
| **Profile** | Shows your workspace profile and lets you change it. |
| **Change Password** | Opens the password dialog. |
| **Settings** | Opens **User settings**: regional formats, the double-click choice and settings import and export. |
| **Set *workspace name* as default** | Makes the current workspace the one MatStream opens after sign-in. |
| **Theme** | Switches the look of the application. |
| **Logout** | Signs you out. |

---

## Profile

**Profile** shows the *public workspace information* other people see about you: email address, phone number, display name, first and last name and who you report to. The profile belongs to the current workspace, so the same person can have different details in different workspaces.

To change it:

1. Click the pencil beside your name.
2. Edit the fields and click **Save**. **Cancel** discards the changes.

| Field | Notes |
|---|---|
| Email address | Your sign-in identity. It cannot be changed here. |
| Phone number | Optional. |
| Display name | Required. Changing it needs an extra permission; otherwise the field is disabled with a tooltip saying so. |
| First name / Last name | Optional. |
| Reports to | Pick another workspace user. Needs an extra permission. |

**Profile thumbnail.** Click the small pencil on your avatar to upload a picture (PNG, JPEG, WebP, BMP or GIF, up to 25 MB), or the **×** to delete it. Without a picture, your initials are shown. The thumbnail also replaces the person icon in the account menu.

Which of these controls you see is decided by your group's [user profile grants](../admin/group-grants-dialog.md#user-profile). When you open another person's profile — for example from a user picker — it is read-only.

---

## Change Password

1. Choose **Change Password**.
2. Enter your current password, then the new one twice.
3. Click **Change Password**.

The same complexity rules apply as at sign-up. If you have forgotten your password, see [Reset your password](reset-password.md).

---

## User settings

**Settings** opens a dialog with two tabs.

### User tab — regional formats

| Setting | Choices |
|---|---|
| **Culture** | Dutch (nl-NL), English UK (en-GB), English US (en-US), German (de-DE) or French (fr-FR). It controls number and date formatting, including the grids. A culture change is applied after you save, with a page reload. |
| **Date Format** | `d-M-yyyy`, `dd-MM-yyyy`, `d/M/yyyy`, `dd/MM/yyyy`, `M/d/yyyy` or `yyyy-MM-dd`, each shown with an example date. |
| **Time Format** | 24-hour (`18:30`, with or without a leading zero) or AM/PM (`6:30 PM`, with or without a leading zero). |

### Workspace Settings tab

- **Open attached PDF on double-click instead of item details.** Items without an attached PDF open their details as usual, and **Open details** stays available from the menus. Administrators with the workspace-settings grant also see **Use this choice as the workspace default**; people who have already made their own choice keep it.
- **Import** and **Export** buttons are present, but in the current release they do not transfer your settings: Export writes a file with an empty settings list and Import only checks that the file is valid JSON. To keep your grid layouts, use the per-grid options in [Grids, columns & defaults](../features/grid-defaults.md).

---

## Default workspace

If you belong to several workspaces, **Set *workspace name* as default** asks you to confirm and then makes the workspace you are in the one MatStream opens after sign-in. See [Accessing a workspace](workspace-access.md) for how the default interacts with the workspace picker.

---

## Theme

**Theme** lists twelve looks, each in a light and a dark variant: Bootstrap 5, Fluent, Material, Material 3, Tailwind and Fabric. The current choice is ticked. The theme is remembered in your browser, so it applies to you only and must be chosen again on another browser or device. Clearing the browser's site data returns it to Bootstrap 5.

---

## Related

- [Reset your password](reset-password.md)
- [Accessing a workspace](workspace-access.md)
- [Grids, columns & defaults](../features/grid-defaults.md)
