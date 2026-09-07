# Project Management

MatStream includes project management as a built-in solution — Projects 
with Milestones, Tasks, People, Documents, recorded time and the 
commercial trail that led to them, all built on the same entity and 
category model as the rest of the platform. Unlike PLM (which manages a 
single part's lifecycle), Project Management manages the initiative that 
wraps many parts, deliverables, dates and costs together.

---

## What is project management in MatStream?

A **Project** is a folder category with the special **Project** 
behavior. Anything created underneath a project — parts, tasks, 
milestones, sub-folders — can always be traced back up to the project 
it belongs to. This is what lets features like backward scheduling 
(planned for a future release) work: MatStream can walk from any item 
up to its owning project automatically, without you having to tag 
things by hand.

---

## What MatStream manages in a project context

### Projects

The Project category (seeded by the Setup Wizard) carries:

| Property | Purpose |
|---|---|
| **Title** | The project's name |
| **Description** | Free-text summary |
| **Project Number** | Your own reference for it |
| **Client** | A link to a Company entity |
| **Customer Contact** | The person you deal with there |
| **Client PO Number / SO Number** | Purchase and sales order references |
| **Project Manager** | Who is running it, picked from workspace members |
| **Project Type** | Fixed Price, Time & Materials, Internal or Warranty |
| **Priority** | Low, Normal, High or Critical |
| **Start Date / Due Date** | When it runs |
| **Progress %** | The project manager's own judgement, not a calculation |
| **Project Currency** | The currency it is contracted in |
| **Contracted Revenue** | What it is worth |
| **Forecast Comment** | Why the numbers say what they say |
| **Shipping Address** | Where deliverables ship to |

Projects follow their own lifecycle: **In Preparation → Work in 
Progress → On Hold → Closed / Cancelled**.

> **Note:** *Progress %* is a field somebody fills in, not a roll-up. It 
> exists so a project manager can say what they believe when it differs 
> from what the task list implies.

> **Note:** Projects are **not** numbered automatically. *Project Number* 
> is an ordinary property you fill in — there is no numbering scheme on 
> the Project category. Quotations and opportunities do get numbers 
> allocated for them.

### Milestones

Milestones are dated checkpoints on a project — "Design Freeze", 
"Delivery" — each with a responsible owner and a status (Planned, At 
Risk, Reached, or Missed). See [Tasks — Milestones](../features/tasks.md#milestones) 
for the full detail.

### Tasks

Standard work items — due dates, assignees, status — tracked against 
the project the same way tasks attach to any other entity. See 
[Tasks](../features/tasks.md).

### People and Documents

- The **People** tab links Company and Person contacts to the project 
  — who's involved, who to call.
- The **Documents** tab holds project-related files — quotes, 
  specifications, correspondence — separate from engineering drawings 
  attached to individual parts.

### Activities

Calls, meetings, emails, site visits and notes logged against the 
project, dated by when they happened rather than when they were typed. 
See [Opportunities & quotations](../features/opportunities-quotations.md#activities-the-timeline).

---

## Where a project comes from

A project can be created directly, or it can arrive from the commercial 
side: a **Company** you are selling to, an **Opportunity** you are 
pursuing, a **Quotation** you offered, and — once the customer accepts 
it — a project carrying the client, the contact, the currency and the 
contracted revenue across.

The project records **which version of the quotation it was sold on**, 
not just which quotation. Quotations get revised after they are won, and 
a project that pointed at "the quotation" would silently start describing 
a document nobody agreed to.

See [Opportunities & quotations](../features/opportunities-quotations.md).

---

## Time and cost

People record hours on a weekly timesheet and pick the project — or a 
task, which fills in its project for them. Once a week is approved, each 
entry is priced from the hour rate that applied on the day the work was 
done, and the cost, the sale price and the currency are **copied onto the 
entry**.

That is what makes a cost report stay still. A rate card edited next year 
cannot restate what last year's hours cost, because the figures are on 
the entries rather than joined at read time.

The **hours by project** report totals approved hours and their cost 
across a date range. It counts approved hours only — draft hours are 
somebody's intention, and a report that included them would move when 
nobody had done any work.

See [Time tracking](../features/time-tracking.md).

> **Note:** Where a project has an hour with no rate behind it, the 
> report shows **no cost rather than a partial total**. A figure that 
> quietly omitted the hours it could not price would be too low and look 
> complete.

---

## Seeing the whole portfolio

Two views sit over every project in the workspace:

| View | Shows |
|---|---|
| **Project Portfolio** | Every project, with number, client, manager, type, priority, state, dates, progress and contracted revenue |
| **Active Projects** | The same, minus anything Closed or Cancelled |

Both are live views rather than saved reports, so they always show what 
is true now. Open them from the folder tree like any other folder.

A **Company** also gains tabs listing its projects, quotations, 
opportunities and purchase orders — everything that points at it, in one 
place.

---

## Setting up project management

The fastest way to get a working Project setup is the **Setup Wizard**:

1. Open the workspace in MatStream Manage and go to **Setup**.
2. Check **Engineering Project → Engineering Project Configuration**.
3. Leave **Engineering Project Sample Data** checked to seed a worked 
   example (a sample project with milestones, tasks and a real piping 
   BOM already in place), or uncheck it for the configuration only.
4. Apply.

See [Workspace setup](../getting-started/workspace-setup.md#setup-wizard) 
for the full wizard walkthrough.

---

## Who uses MatStream in a project context?

| Role | How they use MatStream |
|---|---|
| **Project manager** | Creates the project, sets milestones, tracks task progress, judges Progress % and forecast |
| **Team member** | Completes assigned tasks, records hours on the weekly timesheet |
| **Approver** | Reviews and approves other people's weeks — never their own |
| **Sales / commercial** | Runs the opportunity and quotation, turns an accepted quotation into the project |
| **Finance** | Reads hours by project, and maintains the hour rate cards |
| **Administrator** | Configures the Project category, lifecycle, properties and permissions |

---

## Getting started with project management

1. **Run the Setup Wizard** — check Engineering Project → Engineering 
   Project Configuration (leave Engineering Project Sample Data checked 
   if you want a worked example).
2. **Create your first project** — give it a Title, a Project Number, a 
   Client and a Project Manager. The Company library that Client points 
   at is set up for you as part of the same option.
3. **Add milestones and tasks** — use the Milestones and Tasks tabs on 
   the project to track what needs to happen and by when.
4. **Enter an hour rate** — create an Hour Rate in the Library with an 
   *Effective From* date and approve it, so approved hours have something 
   to be priced from. The sample data seeds one to start from.
5. **Record some time** — open Timesheet, put hours against the project, 
   submit the week, and have somebody else approve it.

---

## Next steps

- [Workspace setup](../getting-started/workspace-setup.md) — the Setup 
  Wizard and what the Engineering Project configuration seeds
- [Tasks](../features/tasks.md) — Tasks and Milestones in detail
- [Categories](../admin/categories.md) — how the Project category and 
  its behavior work under the hood
- [PLM](plm.md) — managing the parts and assemblies a project might deliver
- [Milestones](../features/milestones.md) - dated checkpoints on a project
- [Time tracking](../features/time-tracking.md) — timesheets, approval and 
  hour rates
- [Opportunities & quotations](../features/opportunities-quotations.md) — 
  the commercial trail that produces a project
