# Project Management

MatStream includes project management as a built-in solution — Projects 
with Milestones, Tasks, People, and Documents, all built on the same 
entity and category model as the rest of the platform. Unlike PLM (which 
manages a single part's lifecycle), Project Management manages the 
initiative that wraps many parts, deliverables, and dates together.

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
| **PO Number / SO Number** | Purchase and sales order references |
| **Shipping Address** | Where deliverables ship to |
| **Client** | A link to a Company entity |

Projects follow their own lifecycle: **In Preparation → Work in 
Progress → On Hold → Closed / Cancelled**, and are numbered 
automatically via a numbering scheme (e.g. `PR.2026.000042`).

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
| **Project manager** | Creates the project, sets milestones, tracks task progress |
| **Team member** | Completes assigned tasks, updates status |
| **Sales / commercial** | Maintains PO/SO numbers, shipping details, and the linked Client |
| **Administrator** | Configures the Project category, lifecycle, and numbering scheme |

---

## Getting started with project management

1. **Run the Setup Wizard** — check Engineering Project → Engineering 
   Project Configuration (leave Engineering Project Sample Data checked 
   if you want a worked example).
2. **Create your first project** — give it a Title, PO/SO numbers, and 
   a Client. The Company library that Client points at is set up for you 
   as part of the same option.
4. **Add milestones and tasks** — use the Milestones and Tasks tabs on 
   the project to track what needs to happen and by when.

---

## Next steps

- [Workspace setup](../getting-started/workspace-setup.md) — the Setup 
  Wizard and what the Engineering Project configuration seeds
- [Tasks](../features/tasks.md) — Tasks and Milestones in detail
- [Categories](../admin/categories.md) — how the Project category and 
  its behavior work under the hood
- [PLM](plm.md) — managing the parts and assemblies a project might deliver
- [Milestones](../features/milestones.md) - dated checkpoints on a project
