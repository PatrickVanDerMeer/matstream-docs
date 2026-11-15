# Local transmittal review test — 14 November 2026

## Environment and baseline

Local services only; no internet deployment. Engineering Workflow Test workspace 108 (`cwt3qp`), current account pvdmeer@pmsoftware.nl. New share 12 assigned this account as reviewer, with email invitations disabled. Previous shares remain download-only. Transmittal 15914 / TR-2026-0001, issued entity version 21552, display version 11. Selected issue line 15915 references document 15795 / ITM-00000.pdf, entity version 21414, revision A/version 1, original stored file 1072. Transmittal PDF is stored file 1081.

## Browser workflow

- The portal displayed administrator-defined outcomes and exact revision/version target.
- Approved with comments without comments returned a required-comments message.
- With comments but no file, the server required a commented PDF. Neither attempt created a response.
- Uploaded a separate locally annotated PDF; the source drawing was never edited. A visible local-test annotation requests motor M-101 and oil-pump P-101 tag numbers.
- Submission created response entity 15916, initial entity version 21553, file 1082. The portal displayed Response saved and a downloadable response.
- A corrected submission created entity 15917 / version 21554, marked IsCorrection. Both records remain visible, sharing the identical returned PDF through file deduplication.
- Downloaded the returned PDF through the portal; API returned HTTP 200. The engineer opened both responses under Transmittal → Review Responses and opened the native response details / Attached File.
- Six in-app notifications were created across the two submissions for the three enabled Engineering Authors with read access.
- The administrator changed maximum PDF size from 25 to 20 MB through Document reviews, saved, closed and reopened the dialog: 20 persisted. Restored 25 through the same UI and saved.

## Database evidence

Both audit rows retain PackageEntityVerID 21552, DocumentEntityVerID 21414 and ReviewedFileID 1072. Returned file 1082 has separate bytes, hash and storage path. Original drawing version 21414 still references file 1072 and state 6078; Transmittal version 21552 still references file 1081 and Issued state 6122. No release for manufacture was performed. Both native response entities have the response category, its Submitted initial state and mapped outcome/comments/reviewer/date/target fields.

The dedicated response download first exposed a lifecycle-permission mismatch with the generic document reader. Fixed it with an explicitly share/recipient-scoped response-file reader; the original-document write ceiling is unchanged. Retest returned HTTP 200.

## Automated checks

24 targeted API/SQL/script tests passed with SQL tests enabled and no skips. Coverage includes PDF validation, configurable decision requirements, all/any-reviewer aggregation, correction precedence, SQL migration repeatability, exact-version audit, duplicate-submit idempotency, altered replay rejection, out-of-package targets, unassigned reviewers, expired/revoked links, nonaccepting states, disabled corrections (including whole-package overlap), own/other-reviewer attachment visibility, original file preservation, package/returned/reviewed file retention and existing lifecycle notification scripting.

Four targeted Manage seed tests passed, including review installation-marker preservation of deliberate removals and existing portal/PDF seed preservation. API, Manage and client builds passed. Local configuration was installed through a workspace-identity-guarded helper using native repositories/services and matching seeded settings; a fresh full Setup wizard was not rerun against the existing workspace.

## Scope and follow-up

One PDF per response. Progress is per fixed share, calculated from its assigned reviewers. The configured group receives native submission notifications; acknowledgment uses the seeded editable internal lifecycle notification script. The script API is covered by automated tests; the acknowledgment transition itself was not exercised in the browser in this step. Review outcomes do not automatically write existing package response fields or release drawings/assemblies. Later engineering revision, reissue and production release remain their own workflow tests.

Screenshots 41–45 are linked in the [workflow chapter](../docs/workflows/engineering-transmittal-review.md).
