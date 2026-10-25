# Editing group grants

The **Edit grants** dialog allows administrators to assign and remove 
grants from a workspace user group. Grants are the coarse-level access 
flags that control which surfaces and administrative features the group 
can access.

---

## Opening the dialog

From the **Edit Workspace Group** dialog, select the **Grants** tab 
and click the shield icon button in the top-right corner of the tab.

!!! note "Administrators group"
    The Administrators group always has all grants enabled. The 
    shield button is not shown for system groups — their grants 
    cannot be changed.

---

## Understanding grants

Grants operate at the feature level — they control whether a group 
can open a surface or use an administrative feature at all. They do 
not control which individual entities or records the group can see 
within that surface. That finer level of control is handled by 
category permissions and lifecycle state permissions.

For example, a group with the `library.view` grant can open the 
Library. Which entities they can see and edit inside the Library 
is then determined by the category permissions configured on each 
category.

### How MatStream evaluates a grant

For a normal workspace user, MatStream combines the grants from every enabled group membership.
A capability is available when at least one enabled group supplies its required grant. Disabled
groups and expired memberships contribute nothing.

Grant checks are enforced by the API. The web app also uses the same effective grant list to hide
navigation and tabs or disable buttons that would be refused. Hiding a control is therefore a
usability aid, not the security boundary: direct API calls without the grant return **403
Forbidden**.

The usual access decision has several layers:

1. The user must be an enabled member of the workspace with a suitable seat.
2. Their enabled groups must supply the grant required for the surface or operation.
3. The entity, property, display-tab, lifecycle-state, and transition rules must also allow the
   requested record and action.

The system **Administrators** group is the exception: its members pass every grant check. Keep
that group small and use ordinary groups for delegated administration.

---

## Layout

The dialog is split into two panels:

**Available grants (left)** — all grants that are not currently 
assigned to this group. Each grant shows its description and the 
area it belongs to. You can filter the list by typing in the 
search box.

**Assigned grants (right)** — the grants currently assigned to 
this group.

---

## Available grants

The dialog groups the active catalog by area. Deprecated grants are retained internally for
compatibility but are not offered for new assignments.

### Surfaces

| Grant | Description |
|---|---|
| `library.view` | Access the Library — the master catalogue of workspace entities |
| `explorer.view` | Access the Explorer — the hierarchical tree for projects, BOMs, stock locations, and document control |
| `tasks.view` | Access the Tasks surface |

### Tasks

| Grant | Description |
|---|---|
| `tasks.manage` | Create tasks and milestones, plan work, and reassign tasks |

### Entities

| Grant | Description |
|---|---|
| `entity.read` | Read entities |
| `entity.create` | Create entities |
| `entity.update` | Update entities |
| `entity.delete` | Delete entities |
| `entity.rename` | Rename entities |
| `entity.category.change` | Change an entity's category |
| `entity.lifecycle.change` | Change an entity's lifecycle |
| `entity.state.change` | Change an entity's lifecycle state |
| `entity.permissions.manage` | Edit an entity's permission list (catalogued for rollout; currently still limited to Administrators) |

### Files

| Grant | Description |
|---|---|
| `file.read` | Read files |
| `file.upload` | Upload files |
| `file.replace` | Replace files and manage checkouts |
| `file.delete` | Delete files |
| `files.checkout.force` | Force undo a checkout made by another user |

### BOM

| Grant | Description |
|---|---|
| `bom.read` | Read bills of materials |
| `bom.manage` | Add, change, and remove bill-of-material items |

### Administration

| Grant | Description |
|---|---|
| `admin.users.manage` | Create, edit and delete workspace users |
| `admin.groups.manage` | Create, edit and delete workspace user groups and manage memberships |
| `admin.security.manage` | Manage workspace security |
| `admin.workspace.settings` | Manage workspace-level settings |
| `admin.search-index.rebuild` | Rebuild the workspace search index |
| `admin.workspace.purge` | Run the workspace purge operation |

### Catalog

| Grant | Description |
|---|---|
| `catalog.categories.manage` | Create, edit and delete categories and category trees |
| `catalog.numbering.manage` | Create, edit and delete numbering schemes |
| `catalog.revision.manage` | Create, edit and delete revision schemes |
| `catalog.properties.manage` | Create, edit and delete custom property definitions on categories |
| `catalog.lifecycle.manage` | Create, edit and delete lifecycle definitions, states, and transitions |
| `catalog.display-tabs.manage` | Create, edit and delete display-tab definitions |

### Automation

| Grant | Description |
|---|---|
| `automation.manage` | Manage scripts and event automations |

### Stock

| Grant | Description |
|---|---|
| `stock.view` | View stock |
| `stock.manage` | Adjust stock and manage stock items |
| `stock.reserve` | Reserve stock and release reservations |

### Integration

| Grant | Description |
|---|---|
| `import.definitions.manage` | Manage import definitions |
| `import.run` | Stage, validate, commit, and publish imports |

### Reports

| Grant | Description |
|---|---|
| `reports.export` | Export BOMs and reports to Excel or other formats |

### Sharing

| Grant | Description |
|---|---|
| `share.create` | Share items with people outside the workspace |

### Time

| Grant | Description |
|---|---|
| `time.approve` | Approve and reject submitted timesheets |
| `time.viewall` | View other people's time entries |

### Printing

| Grant | Description |
|---|---|
| `print.templates.manage` | Manage print templates and their data maps |
| `print.render` | Print an entity with an assigned template |

### Procurement

| Grant | Description |
|---|---|
| `procurement.order` | Create and cancel purchase orders |
| `procurement.receive` | Receive purchase orders into stock |

### AI assistant

| Grant | Description |
|---|---|
| `ai.use` | Use the AI assistant |

### Important exceptions

- Entering, editing, and submitting your **own** hours needs an Editor seat but no grant.
  `time.approve` and `time.viewall` apply to other people's time.
- Updating progress, completing, or reopening a task assigned to you is checked by task ownership.
  `tasks.manage` is for creating tasks and milestones, changing plans, and reassigning work.
- `entity.permissions.manage` appears in the catalog, but entity permission-list editing remains
  restricted to Administrators in the current release.
- Deprecated grants such as `entity.number.change`, `entity.revision.change`, and `reports.view`
  are kept internally for compatibility and do not appear in the picker.

!!! important "Two permission layers"
    A grant is a coarse feature gate, not record access. A user must pass both the relevant
    grant and the entity, category, property, or lifecycle rule for the record. Grants from all
    enabled group memberships combine; they never override a narrower record-level rule.

!!! warning "Sensitive capabilities"
    `ai.use` is not added to any ordinary template group; only the system Administrators group can
    use it until an administrator deliberately grants it. `share.create` is seeded only for roles
    that collaborate externally, such as Project Management, Sales, Purchasing, and Document
    Control. An external-share group is a capability ceiling for outsiders and should be kept as
    narrow as possible.

---

## Adding grants

To assign one or more grants to the group:

1. Select one or more grants in the **Available grants** list on 
   the left. Hold Ctrl or Shift to select multiple items.
2. Click **Add >>** to move the selected grants to the 
   **Assigned grants** list on the right.

You can also double-click a grant in the left list to add it 
immediately without selecting first.

To assign all available grants at once, click **Add all**.

---

## Removing grants

To remove one or more grants from the group:

1. Click a row in the **Assigned grants** list on the right to 
   select it.
2. Click **<< Remove** to move it back to the **Available grants** list.

You can also double-click a grant in the right list to remove it.

To remove all grants at once, click **Remove all**.

---

## Applying changes

Clicking **Apply** closes the dialog and updates the Grants tab in 
the group dialog. Changes are not saved to the database until you 
click **Save** or **Save & Close** in the group dialog.

Clicking **Cancel** discards all changes made in this dialog.

---

## If a user still cannot perform an action

Check the following in order:

1. The user is enabled, has an appropriate seat, and has not lost workspace access.
2. The group is enabled and the user's membership is active and not expired.
3. The group holds both the surface grant and the operation grant. For example, opening the
   Library and creating an entity normally needs `library.view`, `entity.read`, and
   `entity.create`.
4. The category, property, display tab, lifecycle state, or transition does not impose a narrower
   rule.
5. For sharing, printing, imports, stock, procurement, tasks, or AI, check the feature-specific
   grant listed above.

After changing grants, reload the workspace so the client refreshes its effective grant list. A
missing navigation entry or disabled button normally indicates a missing grant; a **403** from the
API confirms that the server rejected the operation.

---

## Related

- [Edit Workspace Group dialog](workspace-group-dialog.md) — the 
  parent dialog where grants are managed
- [Users & groups](users-groups.md) — understand the group 
  permission model
