# Help/code review — 1 November 2026

## Scope

Reviewed help coverage against the current local MatStream working trees, including uncommitted changes. Used the earlier 30 October source audit as a baseline, checked subsequent UI/service changes, inventoried public topics against the current navigation and display/category types, and scanned the whole public corpus for contradictory capability, versioning, retention and configuration claims. Validated every public Markdown page's local links and rendered section references.

Reference HEADs: API `094d5ee`, Assembly `e130ed9`, Manage `18d1459`, Connect `9431f91`. API, Assembly and Manage have local changes, so these hashes alone do not reproduce all behavior described here. This is a source-based documentation audit, not a production deployment comparison or an end-to-end execution of every workflow. It does not certify every sentence in every guide; conceptual examples, historical release notes and installation-specific settings require their own context.

## Corrections and source evidence

Paths are relative to the named sibling repository.

| Area | Help change | Source checked |
|---|---|---|
| Time tracking | Replaced obsolete standalone Approvals instructions with My Timesheet / Timesheet Management, team ownership checks, Accept/Decline, quarter hours, targets, types, shortfall checks and capacity-generated entries. Removed unsupported screen-level reversal and unconditional self-approval claims. | Assembly: `Features/Timesheet/Pages/Timesheet.razor`, `MyTimesheet.razor`, `TimesheetManagement.razor`; API: `MatStream.Application/Services/Tenant/TimeEntryService.cs`, `TimesheetTeamQueries.cs`, `TimesheetExpectedHoursService.cs`, `TimeRules.cs` under Domain time models |
| Time administration | Added a configuration guide covering editable submission rules, resource calendars, reason mappings, seeded time tasks/types/buckets and rate privacy. | Manage: `Features/Setup/Templates/Parts/TimePart.cs`, `HourRatePart.cs`, `TemplateSeedContext.cs`; Assembly: `Features/Capacity/TimeTaskMapping.razor`, Workspace System Settings component |
| Leave balances | New guide for own/other-person statements, grants, adjustments, payouts, pending usage and explicitly reviewed lot removal. | Assembly: `Features/Timesheet/Pages/TimeBalances.razor`; API: `MatStream.Application/Services/Tenant/TimeBalanceService.cs`, `MatStream.API/Controllers/TimeBalancesController.cs` |
| Capacity | New guide for availability, calendars, resources, overrides, profiles, finite scheduling, Microsoft imports and sync history. | Assembly: `Features/Capacity/Pages/CapacityAvailability.razor`, `AvailabilityGrid.razor`; API: `MatStream.Application/Services/Tenant/Capacity/CapacityService.cs` |
| Item subscriptions | New guide for Explorer subscriptions, change kinds, channels, assignment grant, expiry, batches and delivery retry. | Assembly: `Features/Subscriptions/Dialogs/SubscribeDialog.razor`, `Pages/MySubscriptions.razor`, Dashboard `WatchingWidget.razor`; API: `EntitySubscriptionService.cs` |
| Photos | New guide for gallery sources, Photo category configuration, JPEG/PNG/WebP limits and changes staged until parent save. | Assembly: `Features/Photos/Components/PhotoGalleryDisplayTab.razor`, `Dialogs/PhotoUploadDialog.razor`, display-tab settings editor; API: `PhotoService.cs` |
| Dashboard | Corrected tile contents, fixed Workspace KPIs, recently modified rows, Watching, Customize/Arrange and administrator workspace defaults. Removed invented KPI threshold configuration and splitter instructions. | Assembly: `Features/Dashboard/Components/DashboardHost.razor`, `KpiWidget.razor`, `ActivityWidget.razor`, `WatchingWidget.razor`, `Dialogs/DashboardCustomizeDialog.razor`, `Services/DashboardLayoutStore.cs` |
| Display tabs and filters | Added missing supported tab types, definition/assignment separation, current Uses filters and configurable ribbons. Removed retired Uses Tree Grid instructions, missing sort dialogs and obsolete claim that lifecycle-state filters are ignored. | Assembly: `Features/DisplayTabs/Components/DisplayTabDefinitions.razor`, `DisplayTabSettingsEditor.razor`, `DisplayTabRibbonsEditor.razor`; `Features/Categories/Components/CategoryTabSettingsEditor.razor`; API: `MatStream.Shared/Enums/EnumDisplayTabType.cs`, `SqlEntityRepository.Explorer.cs` (`ExecuteFilterItemAsync`, `ExplorerBaseFilterColumns`) |
| Categories and properties | Extended behaviors, linked Business role from the dialog, documented numeric Format, removed old value-pipe-label editor instructions and corrected base Description datatype. | Assembly category/property dialogs; API category behavior enum and `WorkspaceSeederService.cs` |
| Units | Replaced nonexistent physical UoM configuration/editor steps with implemented System Settings quantity-unit administration. Separated converter reference data from quantity units. | Assembly: Configuration page, Workspace System Settings, property dialog and Unit Converter; API: `UomController.cs`, `WorkspaceSystemSettingsService.cs` |
| Explorer and files | Added Move To scope/guards, corrected expansion suppression to category configuration, distinguished folder file entities from the folder's own stored file and from versioned gallery attachments. | Assembly: `ExplorerTreeGridBrowser.razor`, `EntityTreeGridWebApi.razor`, `AddFolderFilesDialog.razor`; API: `SqlEntityRepository.Explorer.cs`, `FolderService.cs` |
| Task types | Replaced invented configurable Review/Approval/Data Entry enum kinds with Action/Notify/Milestone/Wait; corrected Dashboard columns. | API: `EnumTaskType.cs`, `TaskService.cs`; Assembly: `TasksWidget.razor` |
| Getting started and solution introductions | Reworked Quick start to follow actual configured actions; removed conflicting immutable revision and unconditional history-retention promises across introductions and solution pages; corrected physical-unit configuration claims. | Revision guide baseline; API `EntityRevisionService.cs`, `VersionCreationDecider.cs`, `SqlEntityRepository.VersionActions.cs`; category/lifecycle/UI settings |
| Other navigation | API-key management is in Manage's workspace API Keys tab; Collaborate is a static sample layout, with real sharing documented separately. Fixed recurring-invoice example's generation/charge date contradiction. | Manage `Features/Workspaces/Pages/Workspaces.razor`, `WorkspaceApiKeysPanel.razor`; Assembly Collaborate page; API `RecurringInvoiceGenerator.cs` |

## Application limitations exposed by the audit

- Physical dimension/conversion-unit editing and a property Dimension selector are not in the current Configuration UI. Help now describes that limitation and the available quantity-unit UI; this docs task did not add application features.
- The current timesheet screen has no approved-entry reversal command. The ordinary entry API rejects negative hours.
- Built-in task kinds and the current Dashboard KPI metrics are not extensible through dedicated administrator editors. Help no longer invents those controls.
- Collaborate remains a static sample layout; actual recipient portals and sharing are separate implemented workflows.

These are existing implementation limits, not newly introduced settings. No application code was changed.

## Validation and publication

- Strict Zensical build passed with no issues.
- All 98 public Markdown pages checked: 590 local page/image references and 65 rendered section fragments resolved.
- `git diff --check` passed after whitespace cleanup.
- Five new guides are included in navigation; existing guides and the help home link to them.
- No commit, push, website deployment, production access or live AI evaluation was performed.

As documented in the previous review, Core retrieves the GitHub `docs` corpus rather than this local checkout. Publish the reviewed docs together with compatible application changes, then allow documentation-cache refresh and verify citations against the deployed help. Historical release notes remain dated history; the linked current feature guides describe current behavior.
