# MatStream help review — 30 October 2026

## Result and scope

The help was not fully aligned with the current local code. This review corrected high-impact contradictions that could cause the AI assistant to describe unsupported capabilities or miss implemented ones, and added missing planning and system-settings guidance.

The review used the local working trees, including uncommitted changes, rather than assuming the deployed service matches Git HEAD. Reference commits at review time:

- MatStream.API: `006aea0` (Automation update), with local modifications and new files.
- MatStream.Web.Assembly: `93ed11a` (Fixed Dashboard load and Purge), with local modifications and new files.
- MatStream.Web.Manage: `1dda8e7`, with the current local code also consulted for context.

This was a targeted source audit of the areas below, plus a repository-wide scan for stale capability claims and link validation across all public Markdown pages. It is not an end-to-end certification of every UI workflow or a comparison against a running production deployment. No application code was changed and no production data was accessed.

## Corrections and source map

Source paths below are relative to the named sibling repositories. They are maintenance evidence, not public help links.

| Help area | Corrected behavior | Primary code evidence |
|---|---|---|
| AI Assistant | Documentation-grounded help; no live entity or configuration access, no actions; citations only when present | API: `MatStream.Application/Services/Core/AiAgentCoreService.cs`; `MatStream.Application/Services/Tenant/AiAgentService.cs`; Assembly: `Features/AiAgent/Components/AiChatPanel.razor` |
| AI ingestion | Configured GitHub Markdown corpus, heading chunks, lexical retrieval and cached refresh; website URL is used for citations | API: `MatStream.Infrastructure/AiAgent/GitHubDocsCorpusProvider.cs`; `MatStream.Application/Services/Core/DocsRetriever.cs`; `MatStream.Shared/Settings/AiAgentCoreSettings.cs`; `MatStream.Core.API/appsettings.json` |
| Built-in automations | Removed obsolete stub warnings; documented PDF generation, revision bump, task assignment, in-app notifications, lifecycle switches, task-hour refresh, document pins and snapshots | API: `MatStream.Application/Services/Tenant/AutomationService.cs`; `FeatureAutomationDispatcher.cs`; `GeneratePrintPdfAutomation.cs` |
| Script API | `matstream` API, attachment requirement, mock Run button, parameters, generic entity creation and write limitations | API: `MatStream.Scripting/Engine/ScriptEngine.cs`; `MatStream.Application/Services/Tenant/Scripting/ScriptAutomationService.cs`; `ScriptContext.cs`; `MatStream.API/Controllers/AutomationsController.cs`; Assembly: `Features/Automations/Dialogs/AutomationEditorDialog.razor` |
| Automation retries | Durable claim for an automation/event pair; interrupted Running status is not evidence that nothing happened | API: `MatStream.Application/Services/Tenant/AutomationRunner.cs`; `EntityLifecycleService.cs` |
| Transition criteria | Error blocks, Warning allows with advisory; supported predefined/property checks; malformed checks can be skipped | API: `MatStream.Application/Services/Tenant/LifecycleCriteriaEvaluator.cs`; `EntityLifecycleService.cs`; `MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlLifecycleCriteriaRepository.cs` |
| Versions and revisions | Category-controlled version creation, UTC Daily boundary, separate revision labels, Change Revision command and inherited placement copy | API: `MatStream.Application/Services/Tenant/VersionCreationDecider.cs`; `EntityRevisionService.cs`; `MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlCategoryRepository.cs`; Assembly: `Features/Entities/Dialogs/ChangeRevisionDialog.razor` |
| History purge | Current/reference guards, deletion of owned rows, inherited asset preservation; removed blanket immutable-history claim | API: `MatStream.Application/Services/Tenant/EntityService.Versions.cs`; `MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlEntityRepository.VersionActions.cs`; Assembly: `Features/Entities/Components/HistoryTreeGrid.razor` |
| Long-text templates | Editor-based reuse, save separate from Apply/entity save, workspace scope, permissions/limits; removed unsupported general template rendering instructions | API: `MatStream.Application/Services/Tenant/LongTextTemplateService.cs`; `MatStream.API/Controllers/LongTextTemplatesController.cs`; Assembly: `Features/Shared/Dialogs/LongTextEditDialog.razor`; `Features/DisplayTabs/Components/DynamicPropertyEditor.razor` |
| Document content search | PDF, Word and Excel formats; visible cells, saved formula values and extraction limits; corrected index rebuild route | API: `MatStream.Infrastructure/Search/WordFileTextExtractor.cs`; `ExcelFileTextExtractor.cs`; `MatStream.API/Program.cs`; `MatStream.API/Controllers/WorkspaceSystemSettingsController.cs` |
| Filter items | Removed obsolete scaffold-only warning and linked operational filter/snapshot guide | API: `MatStream.Application/Services/Tenant/FeatureAutomationDispatcher.cs` (`sync_filter_snapshot`); existing Filter Items guide |
| Planning | New guide for Gantt, procurement, capacity, dependencies, resources, calendar exceptions, baselines, stale runs and task planning details | API: `MatStream.API/Controllers/PlanningController.cs`; `MatStream.Application/Services/Tenant/Planning/PlanningEngine.cs`; Assembly: `Features/DisplayTabs/PlanningDisplayTab.razor`; `TaskPlanningDisplayTab.razor` |
| System settings | Planning time zone, rebuild action, retention settings; Run purge returns Started without invoking a purge job; separate deleted-workspace cleanup | API: `MatStream.Application/Services/Tenant/WorkspaceSystemSettingsService.cs`; `MatStream.Application/Services/Shared/WorkspacePurgeBackgroundService.cs`; Assembly: `Features/WorkspaceSystemSettings/Components/WorkspaceSystemSettings.razor`; `Features/Lifecycles/Components/LifecycleStatePurgeControl.razor` |

Shared version/revision claims were corrected in Entities, Core concepts and How it works so conflicting introductions do not compete with the detailed guide during retrieval.

Existing edits to `docs/integrations/matstream-connect.md`, `docs/workflows/inventor-assembly-bom.md` and their untracked screenshots were preserved. They predated this review and are not part of its change count.

## AI corpus and publishing

The configured Core service reads Markdown from GitHub repository `PatrickVanDerMeer/matstream-docs`, branch `master`, directory `docs`. It does not read this local checkout or scrape the generated website. Current Core configuration excludes the `test-` prefix. Files under `internal-docs` are outside that corpus.

The provider splits pages at H2 headings, then H3 where necessary. Each chunk carries its page title and heading. Retrieval uses BM25 plus page/component context; default top-K is 8 and the configured target chunk length is 4,000 characters. Keep sections focused, put capability limits alongside the instructions they qualify, and use actual UI vocabulary. Do not rely on a separate index page to override contradictory feature pages.

For these changes to reach the live AI and linked help pages:

1. Review the local code and docs changes together, especially the uncommitted Change Revision and long-text-template work. Align deployment versions before publishing those instructions as generally available.
2. Commit and push the approved documentation to the configured branch. This review did not commit, push or publish.
3. Confirm the GitHub Pages workflow completes; it runs `zensical build --strict` and deploys `site`.
4. Allow Core's configured documentation cache to refresh (currently 60 minutes). The first request after expiry can still receive the old corpus while a background refresh runs. Confirm the Core log reports a newly loaded corpus; refresh failures keep the previous copy.
5. Test the AI questions below and open the cited help links. A successful website deploy alone does not prove Core has refreshed, and a refreshed corpus alone does not prove website publishing succeeded.

## AI verification questions

These are acceptance questions with expected facts, not transcripts of a live AI evaluation. Run them after publishing and cache refresh, including from the related screen to test context-based retrieval.

| Question | Expected grounded answer |
|---|---|
| Can you list our orders or inspect my category settings? | No current live-data/configuration access or action capability; ask for relevant details and explain the UI workflow. |
| Does enabling a script make it run on every save? | No; context is eligibility and the script must be attached to a supported path. |
| Can I use api.log or context.entityId in scripts? | Use the documented `matstream` API; do not invent a generic context/api surface. |
| Can a script send a notification or bump a revision? | Those script writes are not applied; configure implemented built-in Notify or Bump Revision actions. |
| How does Set Assignee handle a group? | Creates individual tasks for resolved members, requires a Task-behavior category, user takes precedence if both supplied. |
| Can one script create two entities and update the source property? | No; one entity maximum, and creation cannot be mixed with property writes. |
| Are transition criteria enforced? | Enabled recognized checks execute; Error blocks and Warning advises; malformed/unknown checks can be skipped. |
| Does every save create a version? | Depends on category policy; unset defaults to Every change, Daily uses UTC. |
| Can I change a revision on an inherited assembly line? | Newer Change Revision command, permission/modification guards, project-specific copy and row-by-row results. |
| Does saving a long-text template save the field too? | Template save is separate; Apply and containing entity save persist the field. |
| Are template tokens expanded when I select a long-text template? | Selection supplies stored starting text; do not promise automatic runtime token expansion. |
| Can I search inside Excel or Word attachments? | Supported formats, background extraction, Excel visible cells and saved formula results, with extraction limits. |
| Run purge says started. Did it remove my history? | That action currently does not invoke a purge; distinguish manual History Purge and deleted-workspace cleanup. |
| The plan shows a stale badge. Are these new dates? | Previous published run may remain; inspect Runs, correct the failure and recalculate. |
| How do I keep a planning baseline? | Planning tab, published run, Keep as baseline; does not freeze source records. |

## Validation

- `zensical build --strict`: passed with no issues.
- Relative Markdown file and image targets: all resolved across 89 public pages.
- Fragment links checked against generated HTML: all 53 resolved.
- Whitespace/diff check: passed after cleanup.
- No live assistant evaluation, production deployment or browser workflow execution was performed.

Future updates should verify behavior in the service/dispatcher as well as the DTO, enum or picker: the review found both implemented features described as stubs and selectable controls without an executing backend path.
