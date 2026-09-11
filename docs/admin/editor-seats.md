# Editor seats

MatStream is licensed by **editor seat**. A seat is what allows someone 
to create and change things in your workspace. Viewers are free and 
unlimited on every paid plan, so you only pay for the people who 
actually produce work.

This page explains who holds a seat, how seats are handed out, and how 
to move one from one person to another.

For how many seats cost and how to buy more, see 
[Upgrading your plan](../billing/upgrading.md).

---

## Viewer or Editor

Every member of a workspace holds exactly one of two seat types.

| Seat | Cost | What they can do |
|---|---|---|
| **Viewer** | Free, unlimited | See anything their groups allow — browse, search, open entities, read properties and files |
| **Editor** | Consumes one purchased seat | Everything a Viewer can do, plus create, change and delete |

The workspace owner starts with an editor seat when the workspace is 
created.

!!! note "A seat is not a permission"
    A seat decides *whether* someone can change anything at all. 
    Group grants decide *what* they can see and change. Someone with 
    an editor seat but no group memberships still sees an empty 
    workspace. The two work together — see 
    [Users & groups](users-groups.md).

---

## Seats are assigned automatically when you add a user

When you add someone to the workspace, MatStream gives them an editor 
seat if your plan still has one free. If every seat is taken, they are 
added as a Viewer instead.

This applies to both **Add user** and **Add users by email**. In a bulk 
add, addresses are processed one at a time, so a batch larger than the 
number of free seats fills the remaining seats and adds the rest as 
Viewers.

!!! tip
    Check the **Editor seats** counter after a bulk add. It turns amber 
    when every seat is taken, which tells you at a glance that some of 
    the people you just added came in as Viewers.

---

## Changing someone's seat

Seats are managed from MatStream Manage, not from inside the workspace.

1. Open **MatStream Manage**
2. Select the workspace in the left-hand list
3. Open the **User Licenses** tab
4. In the **Seat assigned** column, choose **Viewer** or **Editor**

The change is saved immediately. The counter above the list — 
**Editor seats 3 / 4** — updates as you go, and turns amber when all 
seats are in use.

!!! warning "Allow up to 5 minutes"
    A seat change is saved instantly, but each workspace keeps its own 
    short-lived copy of who holds a seat. It can take **up to five 
    minutes** before the change takes effect inside MatStream itself.

    This matters most when you take a seat *away*: for those few 
    minutes the person can still edit, even though Manage already shows 
    them as a Viewer.

---

## When all seats are taken

If you try to make someone an Editor with no seats free, MatStream 
refuses the change and shows:

> No editor seats are free. Change another member to Viewer, or add 
> seats to your plan.

You have two options:

- **Move a seat** — set someone who no longer needs to edit to 
  **Viewer**, then set the new person to **Editor**. Seats are freed 
  immediately, so you can reassign straight away.
- **Buy more seats** — use **Change plan** on the workspace card. 
  Added seats are prorated from today to your next billing date. See 
  [Upgrading your plan](../billing/upgrading.md).

---

## Freeing a seat when someone leaves

A seat follows workspace access, so you do not have to manage it 
separately:

- **Removing** a user frees their seat.
- **Disabling** a user frees their seat. This includes the case where 
  MatStream disables a user automatically because they have activity in 
  the workspace and cannot be deleted without breaking the audit trail.
- **Re-enabling** a user gives them an editor seat again — but only if 
  one is free. If their old seat was given to someone else while they 
  were disabled, they come back as a **Viewer** and you can assign a 
  seat from the **User Licenses** tab.

!!! note
    A disabled user keeps their place in **User Licenses** and, if they 
    had one, their manage role. Only the seat is released. This matters 
    for the workspace owner: disabling their account frees their seat 
    but never removes their ownership.

---

## Testing another user's permissions

Because the owner's seat can be given up like anyone else's, you can 
use seats to check what another group actually sees:

1. In **User Licenses**, set **your own** seat to **Viewer**
2. Set the test account you want to check to **Editor**
3. Wait up to five minutes, then sign in to the workspace as that 
   account and confirm what they can and cannot do
4. Return to Manage, set the test account back to **Viewer**, and set 
   yourself back to **Editor**

!!! note
    Giving up your editor seat never costs you access to MatStream 
    Manage. You remain the owner, keep full control of members and 
    billing, and can always take your seat back from the same dropdown. 
    The only thing you lose in the meantime is the ability to create 
    and edit inside the workspace itself.

---

## Who can change seats

Changing a seat requires the **Admin** or **Owner** manage role in 
MatStream Manage. This is separate from the groups and permissions 
inside the workspace — a workspace administrator is not automatically 
able to change seats, and someone who manages seats does not 
automatically have rights inside the workspace.

---

## Next steps

- [Inviting users](inviting-users.md) — add people to your workspace
- [Users & groups](users-groups.md) — control what they can see and do
- [Upgrading your plan](../billing/upgrading.md) — buy more seats
- [Billing overview](../billing/overview.md) — how seats are charged
