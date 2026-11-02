# Time tracking

Use **Timesheet → My Timesheet** to record your own hours. **Timesheet Management** provides team overviews and approval decisions for authorized team owners. Approved hours retain their costing information and applicable balance postings.

## Entering your hours

1. Select a week, Monday to Sunday.
2. Add a row and choose a bookable project or task. A task determines its owning project; active project membership and task access still apply.
3. Choose an **Hour type** where needed. A blank type means normal hours; configured types control applicability, target counting and factors. Some types require a note.
4. Enter positive quarter hours, such as `0.25`, `1.5` or `8`, up to 24 hours per entry.
5. Enter a description and **Save** before submitting.

Use a separate row when the same task needs another hour type. Time-budget tasks can be limited to their budget year; a time-budget project requires a time task. Submitted and approved weeks are locked. A declined week can be corrected on the existing rows and resubmitted.

## Targets, holidays and absence

The week shows **Target**, **Counted** and **Extra** hours, warnings and leave balances. The target comes from the person's linked Person resource and normal working calendar. Without a calendar profile, the fallback uses weekly resource capacity across Monday to Friday (40 hours if no resource is linked). Hour types determine which entries count toward it; total booked hours and counted hours can differ.

Mapped public holidays and company closures can produce automatic system entries, which cannot be edited or removed like manual rows. Personal absence may appear as a proposal: review and accept it before submitting. Administrators configure these mappings in [Capacity and Availability](capacity.md#timesheet-task-mapping).

Open **Leave balances** to inspect remaining and pending hours. See [Leave and time balances](time-balances.md) for grants, adjustments, payouts and reviewed expiry removals.

## Submitting a week

**Submit** sends the whole saved week for a decision and locks its entries. Workspace administrators configure submission rules in **Configuration → System Settings**:

| Rule | Effect |
|---|---|
| **Timesheet completeness: Off** | Does not enforce a shortfall check. |
| **Warn and require shortfall confirmation** | A short week requires **Submit this short week for the approver to review**. |
| **Block incomplete weeks** | Complete the counted target before submitting. |
| **Require explicit hour types above the weekly target** | Untyped work can fill the target remaining after absence; extra working hours need an explicit type. |

A failed validation leaves the week available for correction. If another action changed it while open, reload before retrying.

## Timesheet Management and approval teams

The **Timesheet Management** tab requires `time.viewall`. It uses a team tree, a weekly overview or read-only member timesheet, and a decision pane.

1. Select the week and an accessible team.
2. Select a person in the overview or team tree.
3. Review target, booked and counted hours and the entries.
4. Enter **Approver Remarks**, then **Accept** or **Decline**.

Decisions require `time.approve`, ownership of an active timesheet approval team containing that person, and a submitted week. A grant alone does not make someone every person's approver. Decline returns the week for correction; Accept locks entries and applies costing and balance operations. The deciding person, time and remarks remain visible.

With `time.teams.manage`, use **Create team**, **Add member**, **Remove member** and **Remove team** in the team toolbar or context menu. Membership changes require ownership of that team. When adding a person already in another team, choose **Move to this team** or **Keep in both teams**. Remove members before deleting a team. These teams are entity-backed approval groups; they are separate from workspace security groups.

## Correcting approved hours

Approved entries cannot be edited or deleted in the normal timesheet. The current screen has no reversal command or negative-hours correction entry. Ask your workspace administrator how to handle a required correction; do not enter a negative ordinary row or change the original approved record.

## Hour rates

An **Hour Rate** is a Library item that says what one hour costs and what it sells for, from a given date.

| Field | Meaning |
|---|---|
| Rate Person | Whose rate this is. Leave it empty for the workspace default rate |
| Effective From | The date these rates start applying |
| Cost Rate | What the hour costs the business |
| Sales Rate | What the hour is billed at |
| Rate Currency | The currency both figures are in |
| Rate Note | Why this rate, agreed with whom |

### Changing a rate

**Do not edit a rate card to change it.** Save a new version with a new *Effective From* — the version history of the card *is* the rate history, and that is what lets last month's hours keep the rate they were costed at.

A rate card only applies once it is **Approved**. A draft card prices nothing.

### Which rate applies

When a week is approved, each entry is priced from the rate card version whose *Effective From* is the latest one on or before the day that entry was worked. Because a week can straddle a rate rise, Monday and Thursday can legitimately cost different amounts.

The person's own card is used when they have one. When they do not — or when their card starts after the day in question — the workspace default card is used instead.

The cost, the sale price, the currency and the exact rate version are **copied onto each entry** at approval. Editing a rate card next year cannot restate what last year's hours cost.

> **Note:** If no rate applies, the hours are approved with no cost rather than a cost of zero. Unknown and free are different things, and a report that showed nought for both would be misleading.

---

## Permissions

| Grant | Effect |
|---|---|
| No additional time grant | Enter, edit and submit your own hours with an Editor seat, subject to project/task access. |
| `time.viewall` | Open Timesheet Management and view permitted other-person time/report data. |
| `time.approve` | Accept or decline a submitted week for a person in a team you own. |
| `time.teams.manage` | Create approval teams and manage membership of teams you own. |
| `time.balance.view` | View other people's leave balances. |
| `time.balance.manage` | Grant, adjust, pay out and explicitly remove reviewed balance hours. |

Reading another person's timesheet does not permit editing it. Workspace security grants and approval-team membership serve different purposes.

## Administration and related guidance

Hour-type, time-task, balance and rate records are workspace configuration managed through their configured categories and properties. Review effective dates and access before changing them. Submission choices are in [System settings](../admin/system-settings.md); calendar reasons and task mappings are in [Capacity and Availability](capacity.md). Setup supplies starting configuration; administrators should review the records for their own organization.

Rate cards have no dedicated rate administration screen. Current setup seeds **Cost Rate** as administrator-only and leaves **Sales Rate** open. Review existing category property permissions, especially in older workspaces or where an administrator has customized access. See [Time configuration](../admin/time-configuration.md).
