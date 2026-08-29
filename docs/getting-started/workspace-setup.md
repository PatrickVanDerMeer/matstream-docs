# Workspace setup

When a new workspace is created in MatStream, the platform automatically seeds a set of base configuration to give you a meaningful starting point. This page describes what is seeded and what you need to configure yourself.

---

## What is seeded automatically

When your workspace is created, MatStream seeds the following:

### Root folder

A root folder entity is created automatically. This is the top-level container in the Explorer folder tree. It cannot be deleted and is not visible as a regular entity — it is the structural anchor for the folder hierarchy.

### Base categories

A small set of base categories is created:

| Category | Family type | Notes |
|---|---|---|
| **Root** | Root | System category for the workspace root. Not selectable. |
| **Folder** | Folder | Used for folder browser entities. No lifecycle assigned. |
| **Task** | Occurrence | Carries the **Task** behaviour — due date, assignee, estimated hours. |
| **Occurrence** | Occurrence | Generic linked occurrence. |
| **File Occurrence** | Occurrence | An occurrence that carries a file. |
| **Document** | Definition | For file-based library entities. |
| **Item** | Definition | Generic item definition. |
| **Person** | Definition | For person records. |
| **Company** | Definition | For company / supplier / customer records. |

All of these except **Root** and **Folder** are assigned the default lifecycle (see below).

!!! note
    These are starting-point categories. Your administrator can add, rename, and configure additional categories to match your organisation's specific requirements.

### Default lifecycle

A lifecycle definition named **Default Lifecycle** is created with the following states and transitions:

**States:**

| State | Role |
|---|---|
| **Work in Progress** | Initial state — entity is being worked on |
| **For Review** | Submitted for review |
| **Approved** | Approved, ready for release |
| **Released** | Final released state |

**Transitions:**

| From | To | Purpose |
|---|---|---|
| Work in Progress | For Review | Submit for review |
| For Review | Approved | Approve |
| For Review | Work in Progress | Reject back for rework |
| Approved | Released | Release |
| Released | Work in Progress | Start a new revision cycle |

All base categories (except Folder) are assigned this lifecycle by default.

!!! note
    The default lifecycle is a starting point. Your administrator can create additional lifecycle definitions and assign them to specific categories to match your approval processes.

### Administrators group

An **Administrators** user group is created and the workspace owner is added to it. This happens as part of workspace creation, so a workspace always has an administrator whether or not anyone runs the Setup Wizard. It is a system group and cannot be deleted.

### Base properties

The following properties are created and available across all categories:

| Property | Type | Searchable |
|---|---|---|
| **Description** | Text | Yes |
| **Birth Date** | Date | No |
| **Comments** | Memo | Yes |

These properties are not automatically assigned to any category's display tabs — your administrator adds them to the relevant categories via the **Properties** tab in category configuration.

---

## What you need to configure

After workspace creation, your administrator should:

1. **Review and extend categories** — add domain-specific categories for your organisation (e.g. Purchased Part, Assembly, Work Instruction)
2. **Configure properties per category** — assign the relevant properties to each category's display tabs
3. **Configure lifecycle definitions** — create additional lifecycles if different categories need different approval processes
4. **Configure revision schemes** — set up revision schemes and assign them to categories that require formal revision tracking
5. **Configure numbering schemes** — set up automatic numbering for each category
6. **Set up user groups** — configure groups with appropriate permissions and add workspace users

Most of this can be done faster with the **Setup Wizard**, described below.

---

## Setup Wizard

The Setup Wizard, available from the workspace's **Setup** page in 
MatStream Manage, seeds ready-made configuration templates instead of
building categories, lifecycles, and properties by hand. It offers three
templates — **Engineering Project**, **Document Control** and **Integrations** — each with
options you can check independently.

### Using the wizard

1. Open the workspace in **MatStream Manage** and go to **Setup**.
2. In the **Choose** step, check what you want to apply. Checking a 
   template selects everything under it; you can also check its options 
   individually.
3. Click any item in the tree to see a short explanation of what it sets 
   up in the panel on the right.
4. Click **Next** to review your selection, then **Apply**.

While it runs, a progress bar shows how far along it is and the log 
below lists each item as it is created. Anything that already exists is 
skipped silently — it advances the progress bar but stays out of the 
log, so what you see listed is what was actually added. That also makes 
the wizard safe to re-run.

### Engineering Project

**Engineering Project → Engineering Project Configuration** is the main 
option, and it sets up considerably more than its name suggests — it 
builds a complete engineering and procurement workspace.

**The project itself**

- **Engineering**, **Purchasing**, **Production** and **Document Control** 
  user groups, ready to be granted lifecycle transition permissions.
- A **Project** folder category with the **Project** behaviour, its own 
  lifecycle (In Preparation → Work in Progress → On Hold → Closed / 
  Cancelled), and core properties (Title, Description, Client PO Number, 
  SO Number, Shipping Address, Client).
- **Contact Persons**, **Documents**, **Tasks** and **Milestones** tabs, so 
  a Project immediately has somewhere to track people, files, work and 
  dated checkpoints.
- A **Company** library with Is Client / Is Supplier / Is a Lead flags — the 
  same record serves as customer and supplier.

**The engineering model**

- Definition categories **Part**, **Assembly**, **Weld Assembly**, 
  **Instrument**, **Equipment** and **WPS**, grouped in the category tree 
  under a parent called **Item**.
- A shared **Stock Number** numbering scheme, so an item's number *is* its 
  name.
- **BOM** / **Uses** tabs on Part, Assembly and Weld Assembly, plus a 
  **Welds** tab on Weld Assembly, and Documents and Drawings tabs throughout.
- An **Engineering Lifecycle** (Work in Progress → For Review → Approved → 
  Released → Obsolete) applied to all six definition categories, with 
  **Senior Engineers** and **Junior Engineers** groups and per-transition 
  permissions already configured.
- A **BOM Folder** folder category, so the placed BOM sits in its own folder 
  inside the project and can carry its own Explorer column layout.

**Procurement**

- **Purchase Order**, with **Material Requirement Line** and **Purchase 
  Order Line** grouped underneath it.
- A **Procurement Lifecycle** (Engineering → Ready for Procurement → 
  Reserved / Ordered → Delivered → Assembled) attached to BOM Position, so 
  an engineer can flag an item as ready to buy.
- A **Purchase Order Lifecycle** (Open → Issued → Received → Closed).

**Engineering Project Sample Data**, checked by default, additionally seeds a 
*Sample Engineering Project* containing a real piping BOM (a Lube Oil system 
with around 30 parts and a welded header), example milestones and tasks, and a 
client and a supplier company. It is the fastest way to see the whole model 
working before you configure anything of your own. Uncheck it if you want the 
configuration only — an empty workspace ready for your own data.

!!! tip "Expand the BOM to see it"
    In the sample project, the BOM folder contains one item — the Lube Oil 
    system. **Expand it** and its bill of materials is materialised into the 
    Explorer tree as individual parts, each one a real entity with its own 
    lifecycle that a buyer can act on.

### Document Control

**Document Control → Document Control Configuration** installs a complete,
revision-aware issue process:

- **Controlled Document**, **Transmittal**, **Submittal** and hidden
  **Document Issue Line** categories.
- Separate controlled-document, outgoing-transmittal and incoming-submittal
  lifecycles.
- Alphabetic document revisions and DOC / TR / SUB numbering schemes.
- A Documents tab on each package for adding controlled documents through
  Library Search.
- An issue automation which pins every package line to the exact document
  version visible when the transmittal is issued or the submittal is received.
- A4 landscape Word print templates for outgoing and incoming document
  registers.
- Live worklists for open packages, awaiting and overdue responses, and
  rejected document lines that require an update.

**Document Control Sample Project** adds its folders and records below
`Projects/<current year>/Sample Engineering Project`, creating that shared
project folder if it does not exist. The scenario uses project number
`P-2026-0042` and contains five
controlled documents, client / supplier / third-party companies, four outgoing
transmittals and two incoming submittals. One transmittal is issued while the
General Arrangement is revision A; the document is then advanced to revision B.
Opening or printing the original transmittal still resolves revision A. The
sample also includes received remarks, a rejected manual and overdue responses.

### Steps the wizard does not do

Two things still need a person after the wizard runs:

1. **Add users to the Senior Engineers and Junior Engineers groups.** The 
   groups are created empty, so until someone is a member, only 
   administrators can move items through the Engineering Lifecycle. See 
   [Users & groups](../admin/users-groups.md).
2. **Attach and configure the Create Material Requirement automation** on 
   the Procurement Lifecycle's *Engineering → Ready for Procurement* 
   transition, if you want requirements raised automatically. See 
   [Automations](../admin/automations-admin.md#feature-automations).

!!! tip
    Other domain templates — Quality Management,
    Inventory Management — are added over time. Check the wizard tree 
    for what's currently available in your version of MatStream.

### Re-running the wizard

Everything the wizard creates is matched **by name**, so re-running it is 
safe — existing items are skipped rather than duplicated, and the summary 
tells you how many of each were created versus skipped.

The flip side is that matching by name means the wizard will **not update** 
something that already exists. If a category, property or lifecycle is 
already present, its settings are left exactly as they are — even if the 
template would now create it differently. To pick up changed template 
settings you either adjust the item by hand or start from a new workspace.

If the wizard reports *"did not fully apply"*, fix the reported problem and 
click **Run again** — it resumes from where it stopped.

---

## Related

- [Categories](../admin/categories.md) — configure category structure, properties, and schemes
- [Users & groups](../admin/users-groups.md) — set up user groups and permissions
- [Lifecycle definitions](../admin/lifecycle.md) — states, transitions and who may move between them
- [Automations](../admin/automations-admin.md) — attach and configure built-in automations
- [Revision schemes](../admin/revision-schemes.md) — configure revision label sequences
- [Numbering schemes](../admin/numbering.md) — configure automatic entity numbering
- [Inviting users](../admin/inviting-users.md) — add users to your workspace
- [Tasks](../features/tasks.md) — how Milestones work once a Project is set up
