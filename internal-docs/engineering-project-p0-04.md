# P0-04: client transmittal portal and response

Source assessment: 14 November 2026. Project Documents selection is implemented locally; no live portal tests. P0-03 remains validation only. This item concerns external access to the issued baseline and return of client decisions, not internal production release.

## Existing capabilities

## Agreed transmittal workflow and required fixes

This agreed workflow governs the scope below. Candidate selection and persistence as Document Issue Lines are now implemented; the remaining PDF/portal/response integration is still planned.

1. Engineer creates a Transmittal category record under the project and enters subject, recipients, issue purpose/expected response, dates and remarks.
2. A Project Documents tab lists candidate documents from that project using a filter the engineer can edit while preparing the transmittal. It supports checkboxes and Choose columns. Administrators can configure starting defaults and permissions; engineers control their working filter and column selection. Project scope must be enforced independently of user filters; a filter alone is not authorisation.
3. Engineer selects documents and adds them as Document Issue Lines. Candidate selection must be saved as package contents, not remain transient grid checkboxes. Later filter changes do not change an issued package.
4. Review and issue the package, pinning exact document versions. Purposes include For Approval, For Information and For Quotation, with administrator-editable labels/descriptions and expected-response rules. Recipient may be client, department, supplier or third party.
5. Generate a transmittal PDF from the configured print template and attach it to the record. It lists selected document numbers/revisions and the expected response. Internal document references and PDF output must agree.
6. Share the issued record/package with named parties through the existing portal. Recipients download the transmittal PDF and selected document files at the issued versions.
7. Recipients upload commented PDFs against the transmittal/document lines. Returned files are separate response records/evidence and never replace issued originals. Structured response fields remain configurable where required.

One transmittal represents one issued content set and purpose. Multiple recipients can receive that package; different selections/purposes use separate transmittals. Retain recipient identity on each returned response.

Required work: a project-scoped candidate-document tab with persistent selection-to-issue-line action; integration of existing template PDF generation/attachment; revision-correct portal package access; separate return-upload/response handling and internal follow-up. Existing filter, grid, print-template and PDF automation mechanisms must be reused where sufficient; confirm their composition before rebuilding them.

Administrator UI must configure transmittal fields, starting filter defaults, permitted document scope, filter-editing permissions, default columns and selection actions, issue purposes and response expectations, templates, PDF generation attachment category/action, issue permissions/required fields, recipient access and return-upload rules. Ordinary Choose columns remains available as configured. No hardcoded recipient department, purpose label, drawing number or template choice.

Acceptance: select two project documents, save/reopen the selection, issue, produce/attach the PDF, change the Library drawings, then verify recipients still get the original package revisions and can upload commented copies without altering originals. Filter changes and double-click/retry actions cannot silently change contents or duplicate lines.

### Existing foundations

The recipient portal and Share/Manage Access already provide named authenticated recipients, external ceiling-group permissions, expiry/revocation, download controls, narrowed category/property visibility, scoped child browsing, file downloads, permitted file uploads and access logging. Do not build a second portal.

Document Control already provides transmittals and Document Issue Lines, issue-version pinning, recipient/purpose/date fields, package and line response fields, comment references, response lifecycles and operational worklists. These are reusable foundations for the response workflow.

## What can be configured already

| Choice | Existing surface | Limit |
|---|---|---|
| Client recipients | Share dialog / Manage Access | Named identities, not anonymous approval links |
| External permission ceiling | External-share groups and grants | Permission is bounded by both group and share; it does not create a missing action |
| Expiry, no expiry, download blocking and message | Per-share controls | A configurable per-share value is not proof of an administrator-editable workspace default |
| Exposed categories/properties | Share visibility controls plus group configuration | Does not resolve pinned document versions or create response forms |
| Purpose, response status, due date, line comments and responsible fields | Transmittal/line properties and their definitions | Existing portal displays properties; the inspected UI does not edit them |
| Document/response lifecycle and internal permissions | Lifecycle and category configuration | Internal users can record responses manually; this is not client self-service |
| Operational views and print output | Display Tabs/worklists/Print Templates | Validate actual workspace assignments |

Source comments advertise some future capability more broadly than the implementation. In particular, PortalLanding.CanChangeState does not establish a client decision endpoint, and property-selection wording does not establish an external property editor. The inspected portal renders property values as read-only text.

## Missing capability 1: revision-correct package access

Issued Document Issue Lines pin a DefinitionLink to a document version. Current portal traversal follows structural/BOM children, not DefinitionLink, and file/header projection uses current entity versions. An issue line can therefore appear without exposing its referenced PDF; sharing the Library document separately could instead expose its later current content.

Required behavior: resolve each issued line's exact referenced document/file version within the authorised package. Use this same version context for list/open/download and scope checks. Never broaden traversal into the general Library. Keep historical package membership consistent with the P0-03 baseline validation.

This is a substantive integration gap, not solved by adding a category to the share filter. Existing share identity/group checks should be reused. No administrator option should silently turn an issued approval package into a live latest-document feed.

## Missing capability 2: structured client response

The inspected PortalController exposes landing/entity/children/download/upload routes, but no line/package review-decision or comment endpoint. The portal page has no approval/comment form. Generic file upload attaches/replaces a file on an accessible item; it is not a returned-review evidence model and must not let clients overwrite the issued source drawing.

Required behavior:

1. Present the issued drawing revision with its package/line identity and review due date.
2. Let the invited, permitted reviewer submit a configured outcome, comments/tag information and supporting attachments against that exact issue.
3. Keep returned evidence separate from the original drawing. A marked-up PDF attachment is sufficient for this workflow; an in-browser annotation editor is not required.
4. Record actor, time, package/line/version and response history; define correction/resubmission behavior without overwriting earlier decisions.
5. Reflect responses in the existing document-control fields/worklists and provide an internal owner follow-up through supported tasks/automation. Internal staff must not need to retype the response.
6. Keep client decision separate from Engineering acceptance of comments and internal production release (P0-05).

## New administrator-configurable business settings

Reuse existing property/lifecycle/share controls. Add missing controls to application UI rather than source code or SQL.

| Setting | Proposed control |
|---|---|
| Which package categories allow client review | Category/share review settings |
| Reviewer permissions and internal owner | Existing groups/roles plus explicit reviewer/owner assignment |
| Response outcomes and display descriptions | Reuse configurable line outcomes; map semantic meanings by stable identity rather than matching labels |
| Per-line responses versus package response | Review policy defining applicable scope and aggregate behavior |
| Mandatory comments/attachments by outcome | Validated review-form rules; e.g. reason required for rejection |
| Due-date defaults and any reminders | Review policy and validated automation parameters; timer-based reminders require a proven runner and need not be part of the initial delivery |
| Resubmission/correction and multiple-recipient behavior | Explicit policy: who may respond, whether one/all named reviewers are needed, and how later responses are retained |
| Visible fields and download choices | Existing share/group visibility controls; reusable client profile only if repeated manual setup needs it |
| Internal follow-up | Configured assignee/notification automation and editable messages, not a hardcoded department |

Avoid building delegation, a universal approval engine, signing infrastructure or a complex profile designer without a concrete requirement. Start with the named recipients, existing fields/lifecycles and required external response actions. Administrator changes must not rewrite historical responses or change the meaning of a recorded decision.

## Proposed minimum development

1. Package-aware pinned-version resolution and file authorisation in the existing portal.
2. Scoped client response endpoints and UI for outcome/comments/returned attachments with durable evidence and audit.
3. Administrator UI for new review rules/semantic mappings, reusing existing category/property/lifecycle configuration.
4. Integration with current response fields and internal follow-up; clear submitted/failed feedback and duplicate-submit handling.

Invitation/expiry/access mechanics are already present; validate them rather than count them as new development. Formal build-authority gating belongs to P0-05. Generic subscription enhancements belong to P0-09; do not assume they are already available for this follow-up.

## Acceptance example

Issue ITM-00000 revision 0 to a named client. Advance the Library drawing to a later revision. The original portal link must still download revision 0. Client returns tags and Approved with Comments plus a marked-up PDF against that issue; internal owner sees the exact response and evidence. The original drawing remains intact and production is not automatically released.

Test rejected/revised responses, multiple recipients under the selected policy, retry/double submit, withdrawn/expired shares and wrong identities. Verify direct file-ID access cannot reach a later unissued version or unrelated material. Historical decisions remain linked to the issue reviewed after reissue.

## Evidence

`MatStream.API/MatStream.API/Controllers/PortalController.cs`; `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlShareRepository.cs`; `MatStream.API/MatStream.Domain/Models/Tenant/Shares/Share.cs`, `ShareCreateRequest.cs`, `PortalViews.cs`; `MatStream.Web.Assembly/Features/Shares/Pages/Portal.razor`, `Dialogs/ShareDialog.razor`; `MatStream.Web.Manage/Features/Setup/Templates/Parts/DocumentControlPart.cs`.

## Local selection implementation — 14 November 2026

The reusable Filter Result tab now supports project-scoped candidates, an engineer-editable filter saved per transmittal/tab, checkbox selection and the existing Choose columns layout. The add action creates Document Issue Lines transactionally, checks versions and permissions again, and prevents repeated selections creating duplicate lines. Admin UI configures document/definition-link categories, issue-line category, preparation states, filter editing, pinning, action label, starting filter and columns. Setup installs defaults without overwriting an installed administrator configuration. See [the workflow guide](../docs/workflows/engineering-project-documents.md) and [the local test record](engineering-project-transmittal-selection-test.md). Issued portal baselines and attributed client return uploads remain outstanding; PDF generation is covered by the implementation update below.


### Implemented locally: PDF on the Transmittal (14 November 2026)

An editable Universal automation script now renders the assigned template and attaches its PDF directly to the Transmittal. First generation uses the empty current version; regeneration creates a new history version before rendering, even for identical output. Rendering/attachment failures roll back. Administrator UI configures category/state eligibility, template, filename, history comment and saved-line requirement; the ribbon action can target the opened record. Fresh Document Control Setup seeds the script, ribbon and direct-file category policy and preserves installed customizations/removal. The local test verified actual generation, earlier file access and an administrator-edited history comment. See [workflow instructions](../docs/workflows/engineering-transmittal-pdf.md) and [test evidence](engineering-project-transmittal-pdf-test.md). Issued portal baselines and client return uploads remain outstanding.


### Implemented locally: PDF consistency before issue (14 November 2026)

PDF generation records server-owned content evidence. A configurable Generated PDF matches saved contents criterion blocks issuance when the PDF is missing, untracked, replaced or stale after header/register/selected-version/template changes. Setup enables Error on existing issue routes and preserves administrator changes/removal. Generation remains script-driven; blocking uses pre-transition criteria, with a final check in the state-write transaction. TR-2026-0001 was blocked after an Attention correction, regenerated and successfully issued locally at version 11. See [workflow instructions](../docs/workflows/engineering-transmittal-pdf.md) and [test evidence](engineering-project-transmittal-pdf-consistency-test.md). Portal sharing remains outstanding.


### Implemented locally: fixed issued package portal access (14 November 2026)

An issued Transmittal can now be shared through the existing portal with its fixed PDF and pinned drawing files. New-share validation checks configured states/categories, PDF evidence and read access; portal listing/detail/download authorization uses the same captured package and refuses live Library traversal. Existing identity, group ceiling, expiry, revocation and download logging remain active. Administrator UI and Setup defaults cover package/document categories, shareable states, issue-line category, visible fields, external group, expiry and download default. File cleanup retains package references. Tested through both real workspace services and the signed-in browser with no email invitations; the active local test share is 11. See [workflow](../docs/workflows/engineering-transmittal-portal.md) and [test record](engineering-project-transmittal-portal-test.md). Client responses and commented PDF returns remain outstanding.


### Implemented locally: client review responses (14 November 2026)

Document Review Response is now a configurable file-bearing category under Transmittal. Review-enabled fixed shares assign named reviewers to submit administrator-defined decisions, comments and one optional/required PDF through the portal. Each submission/correction creates a separate native entity and exact-version audit. Issued originals remain unchanged. Administrator UI covers response scope, reviewer policy, accepting states/purposes, mappings, evidence, visibility, naming, notifications and progress labels. Submission notifications use the bounded native operation; the internal acknowledgment transition uses an editable notification script. Old links remain download-only; create a new review-enabled share. See [workflow](../docs/workflows/engineering-transmittal-review.md) and [local evidence](engineering-project-transmittal-review-test.md). Drawing/production release and mapping review progress onto existing package status fields remain separate work.


### Client comment resolution and reissue traceability — 14 November 2026

Implemented through workspace-editable response properties, lifecycle states/permissions/criteria, association/Where Used tabs and checking/reopening scripts. Owner and disposition are separate from client evidence. Seeded incoming verification/closure routes require owner, disposition, checker, date and evidence confirmation. Reopening via configured routes clears old verification. Generic Uses associations can pin target versions and open them read-only. Tabs link revised drawings, replacement transmittals and superseded issues without moving entities or changing the BOM. Administrators control labels, rules, categories, columns, permissions and scripts; installation markers preserve later edits/removal. Evidence confirmation is a human check. Formal drawing changes and reissue remain separate workflow steps. See [guide](../docs/workflows/engineering-comment-resolution.md).


### Comment-resolution browser validation — 14 November 2026

Local browser testing now covers editable resolution fields, pinned drawing and replacement links, reverse navigation, lifecycle checks and verification reset on rework. Fixed relationship request mapping and script property-ID resolution discovered by the walkthrough. Follow-up automation failures are now visible in state-change results. See [the test record](engineering-project-comment-resolution-test.md). Actual revised drawings and formal replacement issuance remain pending.


### Local revision B reissue — 14 November 2026

Uploaded the tagged test PDF, issued client and Work Preparation replacements and verified fixed portal packages while original A/v1 remains unchanged. Response now captures the final issued versions and is Ready for Check. Fixed association Unlink routing through the entity save path. Remaining gaps: inherited drawing revision action; reusable transmittal project reference without global uniqueness conflicts; download-only portal review-status wording. PDF-only test, with technical/production release pending. See [the reissue test record](engineering-project-reissue-test.md) and [workflow guide](../docs/workflows/engineering-reissue-drawings.md).


### Project drawing revision action fixed — 14 November 2026

Inherited shared Library drawings now update through the regular revision save path; BOM occurrences retain project-copy handling. Administrator schemes, sequences, versioning and permissions remain in control. Signed-in browser saved B → BTEST → B on ITM-00000.pdf; current B/v5 retains the same PDF and issued packages remain pinned to B/v3 (original A/v1). 23 targeted tests passed. The earlier revision-action gap and helper workaround are superseded by [this fix and test record](engineering-project-drawing-revision-test.md). Other reissue gaps remain open.


## Subsequent project reference fix — 14 November 2026

The Project Number reuse gap is closed using the nonunique Document Project EntityRef. Project Number remains unique on the project. Administrator picker/category/template/portal mappings remain configurable. Two local drafts reference the same project; the generated PDF prints its display name. Historical issued packages remain unchanged. See engineering-project-project-reference-test.md for evidence and limits.


## Implemented locally — 15 November 2026

Direct client response lifecycle states replace the outcome-property mapping in the agreed workflow. Share-group transition permissions, criteria and per-state attachment requirements are enforced at submission. Existing subscriptions on configured drawing/line/package targets receive the response event, with response read-access filtering and idempotent hit matching. The local browser saved Approved with Comments and received one in-app subscriber notification. An existing missing ChannelID alias in subscription delivery SQL was fixed. See engineering-project-direct-response-state-test.md and the updated client workflow guide. Earlier automatic release-gate proposals remain superseded by the Vault-led process.
