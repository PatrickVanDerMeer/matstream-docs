# Dashboard

The Dashboard is the home page of every workspace. It gives you an at-a-glance view of your work items, favourited entities, key metrics, and recent activity without navigating to individual sections.

---

## Accessing the Dashboard

The Dashboard is the default page when you open a workspace. Navigate to it at any time by clicking the workspace name or the home icon in the navigation bar.

URL: `/w/{workspaceSlug}/`

---

## Widgets

### Favourites

Shows entities you have starred for quick access. Click any entry to open the entity detail page. Star or unstar entities from the entity detail page or from the Library grid. Favourites are stored per workspace user.

### Tasks

A grid of your outstanding tasks, showing:

| Column | Description |
|---|---|
| Project Nr | The project or entity number the task is linked to |
| Description | Task description |
| State | Current task state (e.g. Open, In Progress, Done) |
| % Complete | Completion percentage |
| Start Date | When the task was started or is scheduled to start |
| Due Date | Deadline for the task |

Click a task row to open the linked entity. Tasks assigned to you across all entities in the workspace appear here. See [Tasks](../features/tasks.md) for more information.

### KPI widget

Displays configurable key performance indicators summarising workspace health. Typical KPIs include entity counts by lifecycle state, overdue tasks, or open change requests. Administrators configure which KPIs appear and their thresholds.

### Activity widget

A feed of recent events across the workspace:

- Entity created or updated
- Lifecycle state transitions (e.g. "Part ABC moved to Released by Jan")
- Import batch completions
- File check-ins

The activity feed shows the most recent events first. Click an item to navigate to the related entity.

---

## Personalisation

The Dashboard layout uses a resizable splitter. Drag the divider between widget rows to adjust how much space each section gets. Layout preferences are saved per user.

---

## Related

- [Tasks](tasks.md) — full task management documentation
- [Library](library.md) — search and browse all entities
- [Explorer](explorer.md) — hierarchical entity navigation
