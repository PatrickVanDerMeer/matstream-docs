# Time tracking

Time tracking records who worked on what, on which day, and for how long. Hours are entered on a weekly grid, submitted as a week, and approved by somebody else — and once approved they are priced from the rate card that applied on the day the work was done.

---

## Entering your hours

Open **Timesheet** in the workspace menu.

1. The grid opens on the current week, Monday to Sunday.
2. Each row is one thing you worked on. Pick the **project** or the **task**; picking a task fills in its project for you.
3. Type hours into the day columns. Half hours are fine — `3.5`.
4. Add a description if the row needs one. It is what an approver reads.
5. **Save**.

Rows can be added and removed freely while the week is a draft. Use **Previous** and **Next** to move between weeks; the grid loads whichever week the date falls in.

> **Note:** The timesheet always shows *your* hours. There is no way to open somebody else's week from here — that is what the approvals screen is for.

---

## Submitting a week

**Submit** hands the whole week to an approver. From that moment the week is locked to you: entries cannot be edited, added or deleted while they are waiting on a decision.

A week that is sent back returns to draft on the same rows, so you correct and resubmit rather than starting again.

---

## Approving somebody's week

Open **Approvals** in the workspace menu. It lists every submitted week that has not been decided, oldest first, with the person, the period and the total.

1. Click a week to see the entries behind the total.
2. **Approve** or **Reject**, with an optional comment.

Rejecting returns the entries to draft for their owner. Approving locks them permanently.

> **Note:** You cannot approve your own timesheet, whatever permissions you hold. The point of approval is a second person.

The **Approvals** menu entry only appears if you hold the *Approve timesheets* grant, so most people never see it.

---

## Correcting approved hours

Approved entries are never edited or deleted. A correction is a new entry carrying negative hours and pointing at the one it reverses, so the record shows what was claimed, what was corrected, and when.

This is deliberate: approved hours are what cost reports and billing are built on, and a figure that could be quietly edited afterwards would make every report a snapshot of the moment it was run.

---

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

## Business rules

- One timesheet per person per period. A rejected week is corrected on the same rows rather than becoming a second submission.
- A day cannot hold negative hours unless the entry is a reversal.
- Entering your own hours needs no permission beyond an Editor seat. Everything about somebody else's time is gated.
- Approval and rejection are recorded with the deciding user, the time and the comment.

---

## Permissions

| Grant | Effect |
|---|---|
| *(none)* | Enter, edit and submit your **own** hours |
| Approve timesheets | See the Approvals screen; approve and reject other people's weeks |
| View all time | See the hours-by-project report, which aggregates everybody's hours |

---

## Known limitations

- There is no screen for editing somebody else's hours. An approver reads and decides; correcting is the owner's job.
- Rate cards are managed as ordinary Library items. There is no dedicated rate administration screen.
- A rate card's property-level permissions are not configured by the setup template, so cost rates are readable by anyone who can open the Library until an administrator restricts them.
