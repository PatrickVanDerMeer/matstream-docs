# P0-07: supplier RVD submission

Source assessment: 14 November 2026. Planning only; no implementation or live tests.

Agreed required fixes: administrator-configurable submission-state mapping by stable identity, with pre-commit validation, and a reliable durable submission/replacement signal carrying document/file/version, supplier identity and owning PO-line context. Reuse the existing upload path. Implementation remains on hold; notification delivery is P0-09.

## What exists

- Item requirements materialise as occurrence-owned placeholders. MR creation freezes the source article; the MR-derived PO path builds its own pinned checklist before sharing (P0-01 covers project overrides).
- Named supplier recipients can open the shared PO, browse in-scope lines/requirements, download available files and upload permitted documents.
- Upload endpoint checks share scope and FileUpload capability before calling DocumentService. Modification guards and occurrence ownership checks apply; Library RVD templates cannot receive files.
- A first upload fulfilling a placeholder creates a new version, clears the placeholder and links the stored file. Changed-file replacement creates a new version, retaining previous evidence under configured history/retention.
- Uploading a different file resets an RVD to Submitted, including replacement of Accepted/Rejected evidence. Identical stored-file identity returns without a new version/reset.
- The portal logs a successful upload and refreshes its displayed requirement/file. Submission does not mean Quality acceptance.

## What is configurable already

Requirement definitions/assignments through Library and item RVD tabs; lifecycle states/transitions and permissions; category file/version settings; share recipients, external group grants, expiry and visible categories/properties; tab layout/columns and upload actions. Keep one enabled RVD occurrence category, as agreed.

Whether an accepted requirement may be modified depends on configured modification permissions as well as portal upload capability. Do not promise unrestricted supplier replacement regardless of lifecycle locks.

## Confirmed development issue: submission state is selected by name

SqlDocumentRepository selects the state whose LS_Name equals the literal Submitted. If the administrator renames it to Received for Review, the upload can attach the file while failing to set the intended review state: the SQL update simply matches no submitted state.

Required fix proposal: an administrator-editable RVD submission-state mapping by stable state identity, validated against the configured lifecycle. Renaming labels must not break submission. Missing/invalid mapping must give an actionable configuration error, not silently leave uploaded evidence in Required or Accepted. Validate before committing file/state changes so failure cannot appear as successful submission.

Use the existing category/lifecycle configuration UI with a submission-target setting if sufficient; no separate policy engine is needed. Clarify how this automatic submission operation relates to transition permissions and review-state locks.

## Missing integration: reliable submission/change event

The upload repository updates lifecycle state directly; it does not call the normal lifecycle transition service. The inspected method does not publish an explicit supplier/RVD submission event. Version creation might produce generic events elsewhere, but that does not establish an upload-specific event or a normal lifecycle-automation invocation. Trace these paths during implementation rather than assume all events are absent.

Required behavior: after a committed changed-file submission, emit durable evidence identifying workspace, RVD entity/version, file, owning PO line, submitting identity and whether first submission or replacement. Preserve the external recipient identity even where backend processing uses another actor context. Retries must not duplicate downstream work; rolled-back submissions must not notify.

P0-07 provides the trustworthy submission signal. P0-09 supplies subscription rules, recipients, channels and routing. P0-08 supplies Quality decisions/aggregate acceptance. Do not duplicate those features here.

## Small behavior to verify before adding scope

- Same file again: confirm stored-file deduplication behavior and no duplicate review notification; define a separate deliberate resubmit action only if required.
- Upload result accuracy: DocumentService computes fileUnchanged after saving by checking the current file pointer. That check can report unchanged even for a newly saved file. Verify consumers and correct reporting if confirmed; do not use that flag as the sole event/deduplication decision.
- Concurrency: competing supplier uploads/reviewer changes must preserve history and prevent stale acceptance of replaced evidence.
- File removal and pending receipt/review must follow consistent invalidation rules (decision/aggregate handling belongs to P0-08).
- End-to-end PO checklist visibility, upload permissions, retained old evidence and portal feedback remain validation requirements, not reasons to rebuild the existing upload path.

## Administrator settings for new behavior

Submission-state identity/default and validation; permitted replacement behavior within lifecycle permissions; any business file-type/size limits added within platform ceilings; submission-trigger automation attachment and its editable parameters if introduced. Reuse current rights for upload scope, suppliers and category modification. Do not hardcode state labels, Quality groups, document types or project IDs.

Core audit identities, event integrity and ownership checks are platform guarantees, not optional administrator switches. Downstream notification preferences remain configurable through P0-09.

## Minimum acceptance

Supplier uploads the correct PO-line requirement; file/version and visible state are correct. Rename Submitted through configuration and repeat successfully. A replacement cannot inherit acceptance, identical retry creates no spurious changed-file event, invalid mapping fails clearly, wrong supplier/template/out-of-scope file is denied, and a committed upload produces one traceable submission signal. Confirm reviewer/concurrent-upload behavior and accurate upload-result flags.

## Sources

`MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlDocumentRepository.cs`, UploadFileToDocumentAsync; `MatStream.API/MatStream.Application/Services/Tenant/DocumentService.cs`, UploadFileToDocumentAsync and WasFileUnchangedAsync; `MatStream.API/MatStream.API/Controllers/PortalController.cs`, Upload; `MatStream.API/MatStream.Application/Services/Tenant/SubscriptionEventConsumer.cs`; `MatStream.Web.Manage/Features/Setup/Templates/Parts/RequiredVendorDocumentsPart.cs`.
