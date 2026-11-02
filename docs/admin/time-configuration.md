# Time configuration

Time tracking combines workspace records with capacity calendars and submission defaults. Run the applicable engineering setup to seed starting records, then review them for your organization. Default factors and leave rules are configuration choices, not a statement of employment policy.

## Where to configure each rule

| Rule | Administrator UI |
|---|---|
| Completeness mode and explicit types above target | **Configuration → System Settings**, then **Save settings**. |
| Normal working hours and user/resource mapping | **Capacity → Resources** and **Calendar**. |
| Holiday/absence reason to time-task mapping | **Capacity → Calendar → Timesheet task mapping** on a saved profile. |
| Hour types and balance rules | Edit the seeded **Hour Type** and **Time Balance** records and their configured properties. |
| Internal/absence tasks and budget year | Edit **Time Task** records under the year's **Time Budget** folder. |
| Cost and sales rates | **Hour Rate** Library records and their version histories. |
| Approver membership | **Timesheet → Timesheet Management** team toolbar. |
| Security grants and property access | **User Access** groups and **Configuration → Categories** property permissions. |

Hour Type and Time Balance are intentionally hidden from the ordinary New picker by current setup. Edit existing seeded records; do not expect a dedicated time-rule administration tab or assume a hidden category has been deleted. Category properties, permissions and **Show in New dialog** remain administrator settings.

## Hour types

Each type carries **Hour Type Effective From**, cost and sales factors, **Counts Toward Target**, applicable time kinds, suggested usage, an optional note requirement, and optional **Credits Balance** / credit factor. Review and activate the appropriate version through its lifecycle.

Setup provides Normal 100%, Overtime 140%, Overtime 200% and Time for time 100% as starting records. Extra-hour types need not count toward the normal target. Suggestions do not replace the user's responsibility to choose the correct applicable type. Keep effective dates and historical versions when changing factors.

## Budgets, tasks and balances

Setup creates the current year's **Time Budget** folder and internal/absence tasks such as Vacation, Sick, Training and Public holiday. **Time Task Key** provides a stable key for calendar mapping; **Time Kind** distinguishes Internal from Absence. **Draws From Balance** selects a balance for approved usage. Review the budget year before making tasks available for a new year.

Balance buckets configure **Balance Expiry Months**, **Then Draws From** and **Block When Empty**. Setup leaves expiry months unset until your organization's policy is confirmed. Its initial statutory vacation bucket can fall through to the extra-statutory bucket. A blocking bucket can prevent approval when insufficient hours remain; otherwise advance leave can be recorded and settled by later grants. Do not create a circular fallback chain.

Grant or adjust actual hours through [Leave and time balances](../features/time-balances.md). Editing bucket configuration is distinct from recording a person's balance operation. Expiry removal requires review and explicit confirmation.

## Rates and privacy

Hour Rate uses effective-dated versions. Current setup restricts **Cost Rate** to administrators while leaving **Sales Rate** readable under ordinary access. Review existing assignments and deliberate customizations rather than assuming rerunning setup resets them. Property permissions are editable through the category editor.

For changed rates, create the appropriate new version and effective date and approve it. Existing approved time entries keep their saved cost snapshots. See [Hour rates](../features/time-tracking.md#hour-rates).

## Related

- [Time tracking](../features/time-tracking.md)
- [Capacity and Availability](../features/capacity.md)
- [System settings](system-settings.md)
- [Workspace setup](../getting-started/workspace-setup.md)
