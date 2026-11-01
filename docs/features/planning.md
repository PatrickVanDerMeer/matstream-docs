# Project planning

The **Planning** display tab calculates a project schedule from its tasks, dependencies, resources and procurement inputs. It publishes calculated runs so users can compare the current forecast with a baseline and see when the last calculation failed.

## Open and calculate a plan

An administrator must configure a Planning display tab for the project category. Viewing plans requires `tasks.view`; recalculating and changing dependencies, assignments, calendars or baselines requires `tasks.manage`. Item access rules still apply.

1. Open the project and its **Planning** tab.
2. Set the project's **Required Date** before calculating the first plan.
3. Choose **Calculate first plan** or **Recalculate plan**.
4. Review the published run, forecast date and any working-days-late indication.
5. Review **Runs** if calculation fails or a stale badge appears.

The engine calculates forward and backward dates using working days. The displayed schedule is calculated by MatStream; the Gantt view displays the published results. Changing inputs requires recalculation before relying on updated dates.

## Planning views

| View | What to review |
|---|---|
| **Schedule** | Gantt chart with plan items, starts, finishes, float and status. The legend distinguishes deadline-critical items, the driving path and late actions. |
| **Procurement** | Supply/service items with Order before, Need by, Arrival, Coverage, Required, Shortage and Warning. |
| **Capacity** | Resource load by ISO week, capacity hours, load hours and utilisation; overloaded rows are highlighted. |
| **Dependencies & resources** | Explicit dependencies, resource assignments, task planning inputs and non-working days. |
| **Runs** | Calculation status, time, forecast, delay, baseline name and diagnostic message. |

## Dependencies and task inputs

In **Dependencies & resources**, select the predecessor and successor, dependency type and lag in working days, then **Save dependency**. Explicit dependencies can be removed from this view. Review generated dependencies in the schedule rather than assuming every relationship is manually editable.

For a task, set **Sequence**, **Duration**, **Work hours**, **Constraint** and **Constraint date**, then **Save and recalculate**. Check the result and run messages after changing these inputs.

## Resource assignments and calendars

Select a task, resource and hours, then **Save assignment**. Review weekly load in **Capacity**. A resource planning assignment is distinct from assigning responsibility for a task.

Use **Non-working day** to choose a date, Workspace or a specific resource calendar, and a name, then **Add non-working day**. These calendar exceptions affect planning and can be removed from the list. Workspace calendar changes are shared workspace settings even though the editor is reached from a project.

Whole-day start/due boundaries use the workspace **Planning time zone** in [System Settings](../admin/system-settings.md). Users can see the corresponding moments in their own local time. The planning calendar is separate from the UTC day boundary used by Daily versioning.

## Baselines and stale runs

**Keep as baseline** records the current published run as the baseline. The Schedule view includes baseline dates for comparison. It does not freeze the underlying tasks or procurement records.

If the latest recalculation fails, the previously published plan can remain visible with **Stale — latest recalculation failed**. Treat its dates as the previous successful result. Open **Runs**, read the message, correct the inputs and recalculate; a visible chart alone does not establish a current successful forecast.

## Task Planning details

An individual task can have a separate **Planning details** tab with responsible user, planned start, due date, estimated hours, booked hours, progress, remaining hours and expected total hours.

Estimated Hours inherits from the linked Task Definition until overridden. Booked Hours comes from time entries and is read-only. Expected Total Hours is booked plus the latest remaining-hours forecast, or the estimate while no remaining-hours forecast has been reported. Save the containing editor to persist changed task fields.

## Related

- [Project management](../solutions/project-management.md)
- [Tasks](tasks.md)
- [Time tracking](time-tracking.md)
- [Procurement](procurement.md)
