# Local drawing update and reissue test — 14 November 2026

Workspace 108 / cwt3qp, Engineering Workflow Test. Local services only. Current account pvdmeer@pmsoftware.nl simulated both named recipients. Email invitations were disabled; review requests were disabled on the two new shares. No online deployment or real client/department delivery occurred.

## Final records

| Record | Entity/version ID | Display version | State/file |
| --- | --- | --- | --- |
| ITM-00000.pdf | 15795 / 21573 | 3, revision B | Work in Progress, file 1083 |
| Original TR-2026-0001 | 15914 / 21552 | 11 | Issued, file 1081 |
| Corrected client response | 15917 / 21589 | 15 | Ready for Check, original return file 1082 |
| TR-2026-0002 — Client reissue | 15918 / 21587 | 7 | Issued, cover file 1087 |
| TR-2026-0003 — Work Preparation | 15919 / 21585 | 6 | Issued, cover file 1086 |

Original ITM-00000 drawing version 21414 / display 1 / A / file 1072 remains. ITM-00006 remains 21413 / A/v1 / file 1071. Its sample title block still says 0, a known metadata mismatch to resolve before actual production use.

## Drawing update

Inspected original drawing and returned commented PDF. Created output/pdf/engineering-reissue/ITM-00000.pdf from the original drawing with vector equipment-tag leaders M-101 at the motor and P-101 at the pump, a revision-B note and title-block B. Preserved geometry, dimensions and materials. The note explicitly says local workflow test, PDF-only update, Inventor source unchanged and not a production release. Rendered and visually inspected the output.

Uploaded through Library → Open details → Attached File → Browse → Save and Close. This created display version 2. Project Change Revision then failed because the inherited drawing was routed through assembly-line copy handling. Used a guarded local helper with the real SqlEntityRepository.SaveNewVersionAsync to align B, producing version 3. This is a test-data workaround, not a passing browser revision-change test or production fix. Seed source PDF was not replaced.

## Browser walkthrough

- Added For Production to Issue Purpose through Property Definitions/editor-options UI; retained the five existing choices and a configurable description.
- Edited header fields/date, purpose and distribution on the two existing draft records. Renamed them to remove draft wording.
- Project Number reuse failed its uniqueness rule. Left it blank on replacement headers; subject/attention and project placement retain context. Did not disable uniqueness globally.
- Client Project Documents checkbox selection saved one pinned issue line, B/v3.
- Work Preparation selection saved two pinned lines, ITM-00000 B/v3 and unchanged ITM-00006 A/v1.
- Moved each package to Ready to Issue. Generated PDF through the existing automation. Rendered and inspected both final covers, including purpose/channel and register.
- Client issue was visibly blocked after final header/name edits made its earlier PDF stale. Regenerated to file 1087; Issued then succeeded. Work Preparation final cover 1086 also passed issue checks.
- Created shares 13 (client) and 14 (department demonstration), both read-only to the current test account, expiry 14 December 2026, no invitation email and no review request. Client Portal ceiling reused for both; no separate department identity/permissions or automatic handoff notification was claimed.
- Client portal displayed root v7 and B/v3. Work Preparation displayed root v6, B/v3 and A/v1. Client drawing download matched revised source SHA-256 17ED5D98E3B0008F2BD05FA87AC7726391A8CE91202B822CBA8D0560DB428B23.
- Refreshed existing original share 12: root v11 and A/v1, original responses and return attachments unchanged.
- Refreshed response drawing/replacement references to final versions after fixing association Unlink persistence. Updated disposition with exact results/limitations. Moved response In Progress → Ready for Check; state action succeeded. Checker/date remain absent and evidence flag false pending engineering review.

## Bug fixed during walkthrough

EntityEditDialog stages Unlink and later calls client DeleteEntityUsesAsync. This method always used DELETE /uses, which correctly rejects Association relationships. The earlier comment-resolution walkthrough exercised the visible removal but did not establish this save path; the new persisted failure corrects that earlier claim.

DeleteEntityUsesAsync now delegates Association removals to existing DeleteAssociationsAsync. The association endpoint retains its EntityUpdate/EntityRead authorization and ownership checks. Structural and RequirementOwner paths keep the existing Uses endpoint. No BOM rows or documents are deleted by the association route.

Three regression tests compile the real client service into the native test assembly and capture its HTTP method, endpoint and payload for Association, StructuralChild and RequirementOwner. All pass. Client build passes with zero warnings/errors. Signed-in browser retest successfully saved three staged removals and linked the current evidence.

## Native final verification

Guarded helper tmp/reissue-local/Program.cs verifies the local workspace identity before writes. Subsequent --verify-packages and --verify-evidence paths are read-only.

- Active shares 12/13/14 resolve for the named account. Native package projection confirms old A/v1/file1072 and new B/v3/file1083; counts 1/1/2 respectively.
- Exactly five Association pins (reference mode 3): response → drawing21573, client21587, WorkPreparation21585; both replacements → original21552.
- Response checking fields: no checker/date property rows; flag8154 false.
- Covers and original comment PDF remain attached; old issued root version unchanged.

## Remaining gaps and limits

1. Project drawing Change Revision inherited-row handling needs development; the local native workaround is recorded above.
2. Reusable transmittal project identification needs appropriate nonunique reference/mapping without removing Project uniqueness. Current cover Project and To are blank in these test packages; attention/subject carry local routing context.
3. Portal still displays Awaiting responses and “not assigned you as a reviewer” when a newly created share does not request a review. Distribution succeeds; status wording should distinguish download-only shares.
4. Purpose For Production does not enforce a released drawing/assembly. The test intentionally retains Work in Progress; technical verification, CAD update and manufacturing release remain separate pending work.
5. A distinct Work Preparation recipient, internal delivery/subscription automation and real recipient acceptance are not tested by sharing to the same local account.

No claim of production-ready engineering approval or real-world distribution is made.

## Documentation

[Update and reissue guide](../docs/workflows/engineering-reissue-drawings.md) contains screenshots 59, 61, 63–72. Screenshot 58 records the failed revision action; 60 and 62 are supporting header/register captures. The output PDF and two cover PDFs are under output/pdf/engineering-reissue at the repository root.


### Project drawing revision action fixed — 14 November 2026

Inherited shared Library drawings now update through the regular revision save path; BOM occurrences retain project-copy handling. Administrator schemes, sequences, versioning and permissions remain in control. Signed-in browser saved B → BTEST → B on ITM-00000.pdf; current B/v5 retains the same PDF and issued packages remain pinned to B/v3 (original A/v1). 23 targeted tests passed. The earlier revision-action gap and helper workaround are superseded by [this fix and test record](engineering-project-drawing-revision-test.md). Other reissue gaps remain open.


## Subsequent project reference fix — 14 November 2026

The Project Number reuse gap is closed using the nonunique Document Project EntityRef. Project Number remains unique on the project. Administrator picker/category/template/portal mappings remain configurable. Two local drafts reference the same project; the generated PDF prints its display name. Historical issued packages remain unchanged. See engineering-project-project-reference-test.md for evidence and limits.
