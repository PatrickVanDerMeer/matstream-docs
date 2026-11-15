# Engineering Project Workflow: consolidated P0 scope

Agreed planning baseline, 14 November 2026. No implementation is authorised by this document. Detailed assessments are retained beside this file. This consolidation takes precedence over earlier exploratory proposals where scope was narrowed.

## Design rules

- All introduced business settings, defaults, labels/descriptions, role mappings and workflow choices are editable by a workspace administrator through application UI. Use existing category/property/lifecycle/tab/template/automation editors; add missing controls where necessary.
- Prefer editable automation scripts for business decisions and orchestration. Extend supported script APIs and real event attachments instead of hardcoding department-specific workflows. Ship documented, parameterised examples administrators can edit/clone.
- Platform services enforce access, durable events, transactional writes, current-version checks, audit and duplicate protection. Scripts cannot bypass those guarantees.
- Retain the existing entity/Uses-grid/document/subscription architecture. Prefer SQL int / C# int for new identifiers, retaining the PropertyValueID exception.

## Scope by P0 item

| Item | Agreed scope | Disposition |
|---|---|---|
| P0-01 | Effective project RVD checklist: Library defaults + project additions − authorised exclusions; freeze/provenance and exact propagation through MR to each PO line; controlled later amendments | Required fix plus catalogue/unit/version validation |
| P0-02 | Quotation acceptance script creates Project and automatically maps Client/contact and relevant selected quotation fields; configurable mappings/defaults and validation | Required fix |
| P0-03 | Validate issued package membership, pinned revisions/files, print/open consistency, permissions, failure behavior and retention | Validation only; develop only for demonstrated failure |
| P0-04 | Engineer-created Transmittal category record; project-scoped Documents candidate tab with engineer-editable filter, checkboxes and Choose columns; persistent issue lines; template PDF generation/attachment; pinned portal downloads and separate commented-PDF returns | Required integration/development, reusing existing mechanisms |
| P0-05 | Separate early-buy authority and production-release authority using existing permissions/criteria; assess scoped exact-revision approval evidence checks | Configuration/validation; automatic cross-record evidence enforcement remains a proposal, not an explicitly agreed required build |
| P0-06 | Project-selected supplier/source and winning offer/agreed terms drive MR-to-PO creation; retain exact offer provenance and prevent silent substitution/refresh | First supplier-selection/terms slice implemented and locally tested 15 November; see [test evidence](engineering-project-supplier-selection-test.md). Lifecycle handover warning resolved and retested; broader offer controls remain |
| P0-07 | Administrator-selected submission state by identity with pre-commit validation; durable supplier submission/replacement signal with file/version/owner context | Required fixes |
| P0-08 | Authoritative PO-line documentation result over effective checklist; configurable acceptance/waiver/empty-checklist semantics; missing evidence-version protection and invalidation after authorised changes | Required fixes; reuse existing Accepted/Rejected and permissions |
| P0-09 | Script-driven specific-event/state subscriptions and automatic assignment; child RVD ownership routing; API/event attachments, configurable parameters/defaults and example scripts | Required fixes, reusing existing task/email/in-app delivery |
| P0-10 | Shared Payment Milestone occurrence category and Uses tab on Projects (customer invoice planning) and POs (supplier payments); separate owned records and configured lifecycles; script-driven schedules/eligibility/follow-up | Required fix |
| P0-11 | Script combines accepted delivery, documentation status and holds; guarded technical completion/invalidation; re-evaluate linked Payment Milestones with retry/concurrency protection | Required fix |

### Additional agreed configuration usability enhancement

Automations gains a derived Used in column covering recognised actual attachments and administrator-controlled Hide from main list, with Show hidden. Hidden scripts continue executing and remain editable from attachments. Do not automatically hide scripts merely because they have one use. Coverage, export and filtered-empty messaging are included. This supports administration and is not a runtime prerequisite.

## Important scope boundaries

- One enabled RVD occurrence category is supported and documented; multiple-category routing is excluded from P0.
- Production Setup is one-time; testing reruns are excluded from these gaps. No seed-tracking/restore feature.
- Accepted RVD state is read-only for suppliers using existing permissions. Quality reopens before supplier corrections; no new supplier-locking mechanism.
- Engineer edits the transmittal document filter and chooses columns. Administrators control starting defaults/permissions; project/access boundaries remain enforced.
- Different package selections/purposes use separate transmittals. Multiple recipients may share one issued content set, with separately attributed returns.
- No separate approval, notification or tender engine; no online PDF annotation editor required. Returned marked-up PDFs suffice.
- Customer Invoice Issued, supplier Paid, physical Received and technical Complete remain different facts. No bank transfer/full accounting integration implied.

## Recommended implementation sequence

### Agreed testing approach: the workflow itself is the test

Walk through the actual engineering-project story in order, starting with quotation and project creation. At each step record the entered example, expected result, observed result and classify any problem as existing behavior that works, configuration needed, or development needed. Automated tests support this walkthrough; they do not replace it or form a separate gate before starting. Do not wait for a standalone catalogue/document-control/permission exercise to be signed off: inspect those as the workflow reaches them. Earlier automated findings remain supporting evidence, not live workspace sign-off.

The dependency order below guides development planning; it is not a separate prerequisite validation project. No implementation until authorised.

### 1. Establish the existing baseline and agreed configuration

Validate sample catalogue, drawings, units, nested project demand and existing MR/PO ownership; execute P0-03 historical-package checks. Configure Accepted read-only/Quality reopen and existing early-buy/release permissions. Record demonstrated failures. Agree any P0-05 automated evidence enforcement before adding it to required scope.

### 2. Finalise shared contracts and administrator configuration

Define effective checklist ownership/freeze, supplier award/term snapshot, issued package/file context, event identity/actor/owner context and Payment Milestone record/lifecycle meanings. Inventory existing script primitives and extend them incrementally in the features below. Avoid building a speculative general framework first.

### 3. Implement P0-02 quotation-to-project handover

Extend generic creation with validated mapped properties in the existing acceptance script. Deliver an editable example and source-version/retry checks. This creates the real project entry point and can run alongside the procurement contracts.

### 4. Implement P0-01 and P0-06 procurement provenance

Resolve effective project RVD requirements and the awarded supplier/offer/terms before MR/PO freeze. Carry exact agreed data to each PO line, with change review and independent Library defaults. These are prerequisites for truthful downstream review/payment decisions.

### 5. Implement P0-07 and P0-08 supplier evidence

Make submission-state mapping rename-safe and publish durable submission facts. Establish the evidence-bound documentation aggregate and authorised reopening/invalidation. Coordinate shared event context rather than add duplicate upload/review signals.

### 6. Implement P0-04 transmittal package workflow

Build selection-to-issue-line integration, template PDF attachment and exact-version portal resolution, then separate returned comments/evidence. Reuse the P0-03 validated baseline and shared event/access contracts. This stream can run alongside steps 4–5 once its contracts are stable. Revisit P0-05 automation only after response evidence is dependable and scope is agreed.

### 7. Implement P0-09 script-based subscriptions

Add the supported watch APIs/filters/attachments and parameterised Sales/Quality examples against real PO/RVD events. Preserve existing delivery and personal watch functionality. Deliver Used in/hide administration alongside this or earlier; it has no runtime dependency on completion.

### 8. Implement P0-10 shared milestones, then P0-11 completion

First provide Payment Milestone category/tabs/lifecycles and idempotent schedule creation for both parent types. Then add evidence-driven eligibility and technical-completion scripts using delivery/RVD facts, with shared milestone re-evaluation. P0-10 and P0-11 event integrations should be designed together to avoid loops or duplicate requests.

### 9. End-to-end validation and administrator examples

Rehearse quotation → project → project requirements/award → transmittals → PO/supplier evidence/receipts → technical completion → payment/invoice milestone actions. Test either evidence arrival order, exact revision access, independent projects, partial/rejected goods, Quality reopen, corrections, races, retries and role/access changes. Preserve historical paid/issued evidence. Publish usable administrator scripts and help only for validated behavior.

## Per-feature completion standard

Deliver usable administrator controls and descriptions, example scripts, validated real trigger attachments, permission/audit checks, actionable failure/retry behavior and acceptance evidence. A script that saves or runs against mock data is not proof that the workspace workflow works. No implementation has been performed in this planning task.

### Implemented locally: P0-04 project document selection (14 November 2026)

Project Documents now provides an engineer-editable working filter, checkboxes and Choose columns, with project scope enforced by the server. Adding selections creates version-checked Document Issue Lines transactionally and avoids duplicates. A dedicated Document Issue Lines register shows the saved content. Administrator UI controls categories, preparation states, version pinning, filter editing, button label, starting filter and columns; Setup seeds defaults and preserves later changes/removal. See [the local test record](engineering-project-transmittal-selection-test.md). The portal-baseline and client-return portions of P0-04 remain required; PDF generation is covered below.


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


## Agreed simplification — 15 November 2026 (supersedes earlier release-gate proposals)

Technical review/approval and drawing release take place in Inventor/Vault. MatStream receives the released revision and distributes it to Client, Work Preparation and Purchasing. Do not add automatic approval-evidence gates or require a checker different from the owner. Authorised people are responsible for their decisions; permissions and history retain accountability. Existing resolution fields/links remain useful optional process tools, not a duplicate Vault release engine.

Client workflow: download the issued drawing, upload the marked-up PDF against that drawing row and choose an administrator-configured response result/state. Notify engineers/document controllers subscribed before the return. Preserve issued originals and exact-version response context. See engineering-project-client-response-assessment.md for the current capability and focused gaps. Earlier proposals for automatic exact-revision production-authority checks are superseded for this workflow.


## Implemented locally — 15 November 2026

Direct client response lifecycle states replace the outcome-property mapping in the agreed workflow. Share-group transition permissions, criteria and per-state attachment requirements are enforced at submission. Existing subscriptions on configured drawing/line/package targets receive the response event, with response read-access filtering and idempotent hit matching. The local browser saved Approved with Comments and received one in-app subscriber notification. An existing missing ChannelID alias in subscription delivery SQL was fixed. See engineering-project-direct-response-state-test.md and the updated client workflow guide. Earlier automatic release-gate proposals remain superseded by the Vault-led process.
