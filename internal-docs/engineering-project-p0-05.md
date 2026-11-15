# P0-05: early procurement and production authority

> Current decision, 15 November 2026: the Inventor/Vault-led workflow below supersedes the earlier automatic production-authority proposals on this page. See the final section and engineering-project-client-response-assessment.md.

Planning/source assessment, 14 November 2026. No implementation. P0-03 remains validation. P0-04 uses the agreed engineer-created, selected-document, PDF-and-portal transmittal workflow.

## What this item means

Allow explicitly authorised long-lead purchases while client review is pending, but prevent production release until the correct client decision and internal review exist. Sending a For Production transmittal does not itself release an assembly; a client response does not directly authorise an internal lifecycle transition.

## Existing administrator configuration

| Control | Already available |
|---|---|
| States and paths | Lifecycle Definitions: editable names, states, transitions and enabled flags |
| Who may approve/release | Transition group permissions and state read/modify/delete permissions |
| Simple preconditions | Property checks on the item being transitioned; configurable value, condition, message and Error/Warning severity |
| Generic related-item checks | All lifecycle children Final, all definition-linked items Final, no obsolete children, attached file and no open tasks |
| Handoff actions | Transition automations, including task assignment/notifications and material-requirement actions where prerequisites are met |
| Transmittal purposes/responses | Configurable property options and document-control lifecycle; extend seeded choices for the agreed purposes |
| Internal authority evidence | Configurable properties, attachments and tasks; an authorised reviewer can record an approval and execute a restricted transition |

Property comparisons are case-insensitive text checks, not arbitrary relational expressions. Criteria are rechecked server-side before changing state. Error blocks; Warning only advises. Transition permissions need explicit setup: without permission rows a transition is open to otherwise eligible users; Deny does not override Allow through another group.

## Can be configured now without development

An authorised Work Preparation/Procurement group can have an early-buy transition with required recorded authority. Engineering can have a separate production-release transition restricted to its approvers. Own-record properties can record readiness/client approval for a manually reviewed process.

This supports a controlled manual review. A manually ticked approval property is an assertion, not automatic proof that a matching client response exists against the precise drawing revision. Protect those fields by permissions and retain evidence; do not present that workaround as the full evidence gate.

## Missing capability if release must validate actual evidence

The inspected criteria model has PropertyCheck and PredefinedCheck, with the generic checks above. It does not establish a configurable check such as:

“For this project assembly and selected manufacturing baseline, find its issued client transmittal line, require an acceptable response for that exact revision, require disposition of applicable comments, and verify required internal review/production documents.”

All children Final is not equivalent: final may mean obsolete or another terminal outcome, the related approval may not be a direct child, and approval of an old revision must not approve a changed design. Has an attached file likewise does not prove a particular approval exists.

## Proposed development, subject to agreement

1. Extend existing transition criteria with scoped evidence checks rather than create a separate release engine.
2. Resolve project, affected occurrence/definition and baseline versions; find the relevant issued packages/lines and responses. Reject missing, ambiguous, rejected or outdated evidence with actionable blockers.
3. Evaluate comment disposition and internal review according to configured policy. Do not hardcode Approved with Comments as sufficient or insufficient.
4. Evaluate before state write through normal server-side criteria enforcement; a failing post-transition script is not a reliable blocker. Current help documents automation failures can leave the transition applied.
5. Preserve release decision evidence/version context. Later design changes must require the configured new review/release path, not inherit old approval accidentally.

No standalone policy designer is justified yet. Reuse criteria/property/lifecycle UI and add structured parameters for evidence relationships only where existing controls cannot express them.

## New settings must be administrator-configurable

- Transitions requiring early-buy or production authority and the groups authorised for each.
- Eligible package/line/document categories, purpose mappings, relationship scope and baseline matching rule, with safe explicit version semantics.
- Acceptable client response outcomes, conditional-approval treatment and required comment disposition/internal reviews.
- Required authority/reason/evidence fields and any explicitly permitted exception route, with approver permissions and audit.
- Blocker descriptions and Error/Warning choice for applicable configurable rules. Required release checks must default to blocking, not warn-only.

Use stable identities for references; labels/descriptions remain editable. Do not introduce hardcoded ITM numbers, project names or department groups. Administrative changes must not rewrite historical decisions.

## Required versus optional scope

Agree the evidence-enforcement level before classifying all of the proposed development as required. The present process can use restricted transitions plus authorised manual review. Fully automatic verification against the exact client response/revision needs the scoped evidence criteria above. Early procurement itself does not require a new feature engine.

## Acceptance

Authorised early-buy succeeds while production remains blocked. Missing/rejected/old-revision approval blocks release. Conditional approval follows the configured comment rule. Non-authorised users cannot use release or exception paths. Purpose text alone and dates/notifications cannot authorise production. A changed design needs a new applicable decision. Test via API as well as UI and verify failure leaves state unchanged for a blocking criterion.

## Sources

`matstream-docs/docs/admin/lifecycle.md`; `MatStream.API/MatStream.Shared/Enums/EnumCriteriaType.cs`, `EnumCriteriaCondition.cs`; `MatStream.API/MatStream.Domain/Models/Tenant/Lifecycle/LifecycleStateTransCriteria.cs`; `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlLifecycleCriteriaRepository.cs`; `MatStream.API/MatStream.Application/Services/Tenant/LifecycleService.cs`.


## Agreed simplification — 15 November 2026 (supersedes earlier release-gate proposals)

Technical review/approval and drawing release take place in Inventor/Vault. MatStream receives the released revision and distributes it to Client, Work Preparation and Purchasing. Do not add automatic approval-evidence gates or require a checker different from the owner. Authorised people are responsible for their decisions; permissions and history retain accountability. Existing resolution fields/links remain useful optional process tools, not a duplicate Vault release engine.

Client workflow: download the issued drawing, upload the marked-up PDF against that drawing row and choose an administrator-configured response result/state. Notify engineers/document controllers subscribed before the return. Preserve issued originals and exact-version response context. See engineering-project-client-response-assessment.md for the current capability and focused gaps. Earlier proposals for automatic exact-revision production-authority checks are superseded for this workflow.
