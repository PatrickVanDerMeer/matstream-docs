# Leave and time balances

Open **Leave balances** from **My Timesheet**, or visit the workspace's `/w/{workspaceSlug}/time-balances` page. It shows balances, pending usage, individual lots and balance history.

Your own statement is available without a grant for viewing other people's balances. `time.balance.view` permits the overview for other people; `time.balance.manage` permits balance operations. An administrator can assign these grants through [Users & groups](../admin/users-groups.md).

## Review balances

Select the person if the page offers a person picker. Review the remaining hours, pending hours and lots before recording leave. Expand **Balance history** for the effective date, operation, hours and reason. Pending and approved usage are distinct; a pending week is not a final deduction.

## Grant, adjust or pay out hours

With `time.balance.manage`:

1. Select the person and balance.
2. Choose **Grant**, **Adjustment**, or **Payout**.
3. Enter hours in quarter-hour increments, an effective date and a reason.
4. Click **Record operation** and check the updated statement.

Use positive hours for grants and payouts; adjustments can carry either sign. Operations require nonzero hours and a reason. They append a ledger entry rather than rewriting the old history.

## Review expiry

For a lot with remaining hours, **Review removal** prepares a removal against that specific lot. Review the amount and reason, select the explicit approval checkbox, and click **Record operation**. A displayed expiry date alone is not an instruction that the hours have already been removed.

Timesheet approval posts applicable balance usage and banked hours through the configured time rules. Manage hour types and balance configuration as workspace records; changing a caption does not rewrite past ledger entries.

## Related

- [Time tracking](time-tracking.md)
- [Capacity and Availability](capacity.md)
