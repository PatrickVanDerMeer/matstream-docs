# Capacity and Availability

Open **Capacity and Availability** in the workspace navigation to review resource availability, working patterns, calendar exceptions and planned load. This workspace view complements the project's [Planning](planning.md) tab.

## Views and access

| View | Purpose |
|---|---|
| **Availability** | Calendar grid for accessible resources, with a Team filter and a date range. |
| **Calendar** | Effective calendar entries and their sources; shown when detailed assigned-work access is available. |
| **Resources** | Resource calendar, user mapping, personal working pattern and Microsoft synchronization settings; requires `capacity.manage`. |
| **Report** | Available, planned and remaining hours per resource/week, highlighting overload. Select a permitted row for its daily explanation. |
| **Microsoft 365** | Connection, import options and synchronization controls; requires `capacity.sync.manage`. |
| **Sync history** | Resource counts, added/updated/removed entries, warnings and errors from synchronization runs; requires `capacity.sync.manage`. |

Opening the feature requires `capacity.view`. Team and resource access also limit what you can inspect; seeing availability does not automatically allow access to assigned-work details. Choose a date range and **Refresh** to reload it.

## Calendar profiles and working patterns

With `capacity.manage`, use the **Calendar** view's **Calendar profile** panel to set the profile name, time zone, country/region, meeting policy and hours for each weekday. Click **Save profile**. The **Move task dates when assigned capacity is unavailable** setting controls finite scheduling; recalculate affected project plans after changing inputs.

In **Resources**, select a resource, choose its calendar profile, review the workspace user mapping and edit its **Personal working pattern**. Click **Save resource**. Team resources use fixed team capacity. These settings are workspace configuration, so changes can affect several projects.

## Availability overrides

In **Calendar → Add availability**:

1. Choose Workspace or a resource under **Applies to**.
2. Enter the start and end, effect, reason and comment.
3. If using **SetAvailableHours**, enter the available hours per date.
4. Click **Save override** and inspect the effective calendar.

Only editable manual overrides offer **Delete**. Imported entries show their source and are not edited as manual overrides.

## Timesheet task mapping

After saving a calendar profile, its **Timesheet task mapping** panel lets an administrator map each capacity reason to a task key, such as `public-holiday`. Changes save when a mapping field changes.

Holiday and company-closure mappings can book system hours automatically. Personal-absence mappings offer a proposal for the user to accept. Leave a key empty to select a task manually. Review the resulting week in [Time tracking](time-tracking.md); capacity input is not an approval of a timesheet or leave-balance operation.

## Microsoft 365

With `capacity.sync.manage`, use **Connect Microsoft 365** and complete the Microsoft consent flow. Availability depends on the deployment's Microsoft integration configuration and consent permissions.

Map each resource's Microsoft email/Entra identity and enable **Synchronize Outlook and Teams Shifts** in Resources. In **Microsoft 365**, select Outlook and/or Teams imports, enter the selected Teams/schedule IDs where needed, then **Save and sync now**.

MatStream reads working hours, Out of Office, published shifts and approved time off; it does not edit Microsoft data. Ordinary Busy meetings are ignored by default; the calendar profile's meeting policy controls their treatment. Review **Sync history**, connection errors and last successful resource sync before relying on imported availability. **Disconnect** stops the connection.

## Related

- [Project planning](planning.md)
- [Time tracking](time-tracking.md)
- [System settings](../admin/system-settings.md)
