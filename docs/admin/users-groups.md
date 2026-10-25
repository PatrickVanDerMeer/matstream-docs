# Users and groups

MatStream uses a group-based permission model. Users are organised into 
groups, and groups are the primary mechanism for controlling what users 
can see and do in the workspace. Rather than assigning permissions to 
individuals, administrators assign permissions to groups — and users 
inherit those permissions through their group memberships.

For information on adding users to the workspace, see 
[Inviting users](inviting-users.md).

---

## Users

Every person who accesses MatStream has a **workspace user** account. 
A workspace user account is specific to the workspace — the same person 
can have different roles and permissions in different workspaces.

### User account details

Each workspace user account holds:

- **Name and email address** — used for identification and notifications
- **Group memberships** — which groups the user belongs to
- **Personal settings** — workspace-level preferences specific to this user
- **Audit identity** — every action taken in MatStream is stamped with 
  the user's identity

---

## Disabling and removing users

### Disabling a user

To temporarily prevent a user from accessing the workspace without 
removing them, edit the user and uncheck **Enabled**. The user 
immediately loses access but their data, history, and group memberships 
are preserved. You can re-enable them at any time.

A disabled user cannot open the workspace at all — it disappears from 
their workspace list, and a direct link is refused. Their editor seat 
is released so you can give it to someone else; see 
[Editor seats](editor-seats.md).

!!! note
    You cannot disable or remove your own account — you would lose the 
    workspace, and the Users grid with it. Ask another administrator.

### Deleting a user

To permanently remove a user, select them in the Users grid and click 
**Delete User**. MatStream checks whether the user has any activity in 
the workspace before deleting:

- **No activity** — the user is permanently deleted.
- **Has activity** — the user cannot be deleted because their identity 
  is part of the workspace audit trail (entity versions, tasks, etc.). 
  MatStream automatically disables the account instead and shows a 
  message explaining what happened.

!!! note
    This behaviour protects the integrity of your audit trail. A user 
    who has created or modified entities, or been assigned tasks, is 
    part of the permanent record of your workspace. Deleting them would 
    leave orphaned history with no identity attached.

### Deleting multiple users

You can select multiple users in the grid (Ctrl+click or Shift+click) 
and click **Delete User** to process them all at once. MatStream applies 
the same in-use check to each user individually and shows a summary of 
how many were deleted and how many were disabled.

---

## Groups

Groups are named collections of users that share the same permissions. 
A user can belong to multiple groups and inherits the combined 
permissions of all their groups.

### Planning your groups

Before creating groups, think about the distinct roles in your 
organisation and what each role needs to do in MatStream:

| Seeded role | Purpose |
|---|---|
| **Administrators** | Full access to all configuration and data |
| **Engineering Authors** | Create and revise definitions, drawings, files, and engineering BOMs |
| **Engineering Approvers** | Review, approve, release, and retire engineering definitions; normally combined with Engineering Authors |
| **Project Management** | Plan projects, manage tasks and reservations, review all time, and share externally |
| **Project Controls** | Read project, stock, cost, progress, and time information without approval authority |
| **Timesheet Approvers** | Approve or reject submitted timesheets within their reporting scope |
| **Sales & CRM** | Manage leads, customers, opportunities, activities, and quotations |
| **Purchasing** | Release requirements and create or cancel purchase orders |
| **Material Planning / Work Preparation** | Plan demand, reservations, and production work packages |
| **Warehouse** | Receive goods, adjust stock, and manage reservations and locations |
| **Quality Assurance & Inspection** | Review certificates and inspections and control release decisions |
| **Production Planning** | Create, plan, release, and close work orders and routings |
| **Production** | Consume released work and report shop-floor progress |
| **Document Control** | Review and release controlled documents and manage their files |

The Setup Wizard creates the relevant roles for the templates you select. Groups start empty; add
members deliberately. Some narrow roles are intended to be combined with a base role—for example,
an Engineering Approver normally also belongs to Engineering Authors.

!!! tip
    Start with the minimum number of groups needed for your initial 
    use case. You can always add more groups as your needs become 
    clearer. Too many groups with overlapping permissions become 
    difficult to manage.

### Creating a group

To create a group:

1. Navigate to **Administration → Groups**
2. Click **New group**
3. Enter a name for the group
4. Configure the group's grants
5. Save the group
6. Add users to the group

---

## Grants

Grants are the top-level access flags assigned to a group. They control 
which surfaces and administrative features the group can access at all. 
Think of grants as the doors into different areas of MatStream — a group 
that does not have a grant for a surface cannot open it, regardless of 
any other permissions.

The API enforces these grants for every protected operation and returns **403 Forbidden** when a
grant is missing. The web app mirrors that decision by hiding navigation and tabs and disabling
buttons. Users inherit the union of grants from all enabled groups; there is no group-level deny
that subtracts a grant supplied by another group.

Grants are organised by feature area. The catalog includes Surfaces, Tasks, Entities, Files,
BOM, Catalog, Automation, Administration, Stock, Integration, Reports, Sharing, Time, Printing,
Procurement, and the AI assistant.

**Surfaces** — control access to the main working areas of MatStream,
such as the Library, Explorer, and Tasks. A user whose
groups do not include a surface grant will not see that surface in the 
navigation.

**Administration** — control access to workspace configuration features 
such as managing users, managing groups, and changing workspace settings. 
Grant these only to users who genuinely need to administer the workspace.

**Catalog** — control access to the structural configuration of the 
workspace: categories, numbering schemes, revision schemes, custom 
properties, and lifecycle definitions. These are typically reserved 
for the workspace administrator and senior configuration owners.

The other areas separate each operation from its surface. For example, `stock.view` opens stock
information, `stock.manage` changes quantities, and `stock.reserve` controls reservations.
Likewise, import-definition maintenance and running an import are separate grants. This lets an
administrator give people the smallest useful capability set.

Grants are assigned to groups from the **Grants** tab in the 
[Edit Workspace Group dialog](workspace-group-dialog.md).

!!! warning
    Assign grants conservatively. Start with the minimum set of 
    surfaces and features each group genuinely needs and add more 
    only when required. It is easier to grant additional access than 
    to remove it after users have become accustomed to it.

!!! note "Grants vs. category permissions"
    Grants operate at the feature level — they determine whether a 
    group can open a surface at all. Once inside a surface, category 
    permissions and lifecycle state permissions determine which 
    specific entities and states the group can see and interact with. 
    Both layers must be configured for a group to be fully functional.

!!! warning "Sharing and AI need deliberate owners"
    The Setup Wizard adds `share.create` only to roles that collaborate externally. It adds
    `ai.use` to no ordinary role group. The system Administrators group has every grant; review
    both capabilities explicitly when adapting the template roles for your workspace.

---

## Lifecycle state permissions

Workspace grants define what a group can do in general. Lifecycle
state permissions refine those capabilities based on the entity's
current state.

For example:

- The **Engineering Authors** group has the Edit capability — but lifecycle
  state permissions prevent them from editing a Released entity
- The **Quality Assurance & Inspection** group has the Trigger transitions capability — but
  lifecycle state permissions restrict which transitions they can 
  trigger in each state

Lifecycle state permissions are configured within the lifecycle 
definition — not within the group. See [Lifecycle](../entities/lifecycle.md) 
for details.

---

## Transition permissions

Each lifecycle transition can be restricted to specific groups. A user 
without permission for a transition is not offered that state in the 
Change State dialog.

Transition permissions are configured inside the lifecycle definition — 
**Configuration → Lifecycle Definitions → [lifecycle] → [state] → 
Transitions → [transition] → Permissions**.

### How the rule works

There are only two cases, and the second one catches people out:

- **A transition with no permission rows is open to everyone.**
- **Adding a single permission row closes that transition to everyone 
  except the groups granted Allow on it.**

So permissions are not additive restrictions layered onto an open system — 
the first grant you add *switches that transition from open to closed*. 
The practical consequence is that you should grant your administrator 
group on every transition first, then open individual paths to the groups 
that need them. Otherwise the moment you grant Engineers the ability to 
submit for review, you have removed it from everyone else including 
yourself.

!!! warning "Deny does not override Allow"
    A row set to **Deny** does not block a user who is granted **Allow** 
    through some other group — it simply fails to grant. Its only real 
    effect is to make the transition restricted. To stop a group doing 
    something, remove their Allow; do not add a Deny.

### Definition Lifecycle permissions

The [Setup Wizard](../getting-started/workspace-setup.md) seeds a worked 
example. It creates **Engineering Authors** and **Engineering Approvers** and configures the
Definition Lifecycle so that every one
of its 20 transitions is granted to Administrators, with these additions:

| Transition | Engineering Authors | Engineering Approvers |
|---|---|---|
| Work in Progress → For Review | ✔ | |
| For Review → Approved | | ✔ |
| For Review → Work in Progress *(reject)* | | ✔ |
| Approved → Released | | ✔ |
| Approved → Work in Progress *(reject)* | | ✔ |
| Approved → For Review *(re-review)* | | ✔ |
| Released → Work in Progress *(revise)* | ✔ | |
| *any state* → Obsolete | | ✔ |

Authors submit and revise work; Approvers own review, release, and retirement. The remaining
paths — the shortcuts that skip review entirely — stay with administrators.

### Project Lifecycle permissions

The same wizard secures the **Project Lifecycle** more simply: every
enabled transition (start, pause, resume, close, cancel and reopen) is
granted to Administrators and to the **Project Management** group. Nobody
outside those two groups can move a project.

!!! note "The seeded groups start empty"
    Creating a group does not add anyone to it. Until you add members to
    Engineering Authors, Engineering Approvers and Project Management, only
    administrators can move definitions or projects through their lifecycles. This is the most common
    "the wizard broke my workspace" report and it is a two-minute fix — 
    add the members.

---

## Property permissions

Individual properties can be restricted to specific groups. This allows 
you to hide sensitive fields from users who do not need them — for 
example:

- **Commercial properties** (price, margin, supplier cost) — visible 
  only to Procurement and Management
- **Quality properties** (rejection rate, audit findings) — visible 
  only to Quality and Management
- **Internal properties** (internal notes, risk rating) — visible
  only to Engineering Approvers and Administrators

Property permissions are configured within the category settings. 
See [Categories](categories.md) for details.

### Property permissions seeded by the wizard

The [Setup Wizard](../getting-started/workspace-setup.md) governs two
sets of properties out of the box. A group that is not in the table
does not see the column at all.

| Property | Edit | View |
|---|---|---|
| **Unit Price** *(Purchase Order Line)* | Purchasing | Engineering Authors, Production, Project Management |
| **Is Stock Item**, **Minimum Stock Qty**, **Maximum Stock Qty** *(every stockable item category)* | Warehouse, Purchasing | Engineering Authors, Production, Project Management |

Document Control is absent from both — it releases documents and has no
reason to see pricing or stock levels — and Warehouse never sees vendor
pricing. Administrators bypass property permissions entirely.

---

## Tab permissions

Display tabs can also be restricted to specific groups. For example:

- The **NCR tab** on a part entity — visible only to Quality users
- The **Commercial tab** — visible only to Procurement and Management
- The **Engineering tab** — visible only to Engineers and above

Tab permissions are configured within the category display tab settings. 
See [Categories](categories.md) for details.

---

## The Administrators group

The **Administrators** group is a special built-in group in MatStream. 
Members of the Administrators group have full access to all workspace 
data and configuration — regardless of other permission settings.

!!! warning
    Keep the Administrators group small. Only users who genuinely need 
    full administrative access should be members. For day-to-day 
    administration tasks that do not require full access, create a 
    separate group with only the specific capabilities needed.

---

## Best practices

**Use groups, not individuals** — always assign permissions to groups, 
never to individual users. This makes permission management scalable 
and consistent as your team grows.

**Name groups by role, not by person** — call a group "Quality engineers" 
not "Anna and Tom". Groups should represent roles that persist even 
as team members change.

**Review group memberships regularly** — when a user changes role or 
leaves the organisation, review and update their group memberships 
promptly. Stale memberships are a common source of permission issues.

**Disable before you delete** — if you are unsure whether a user should 
be removed permanently, disable them first. You can always re-enable 
them later.

**Document your group structure** — keep a simple record of what each 
group is intended to do and which capabilities it has. This makes 
onboarding new administrators much easier.

**Test permissions before go-live** — before launching MatStream to 
your team, test the permission configuration with a test user account 
in each group. Verify that each group can do exactly what it should 
and nothing more.

---

## Next steps

- [Inviting users](inviting-users.md) — add users to your workspace
- [Numbering schemes](numbering.md) — configure numbering templates
- [Categories](categories.md) — configure entity types and permissions
- [Lifecycle](../entities/lifecycle.md) — configure lifecycle state 
  and transition permissions
