# Local client comment resolution test — 14 November 2026

## Scope

Local workspace Engineering Workflow Test, ID 108 / cwt3qp. Current corrected client response 15917 concerns original issued Transmittal 15914 and ITM-00000.pdf (15795 / version 21414, revision A, display version 1). This step adds resolution data and traceability; it does not modify the engineering drawing or issue/share replacement packages.

## Implementation and configuration

Response category 4574, lifecycle 1370. New property IDs: Resolution Owner 8150, Engineering Disposition 8151, Resolution Checked By 8152, Resolution Checked On 8153, Resolution Evidence Checked 8154. New In Progress, Ready for Check and Verified states retain Submitted, Acknowledged and Closed. State/property permissions and criteria are ordinary administrator configuration. Checking fields use read-only Engineering Authors grants and editable Engineering Approvers/Administrators grants.

New native association tabs: Revised Drawings, Replacement Transmittals and Supersedes Transmittals. Reverse Where Used tabs expose Client Responses, Replacement Issues and Client Comment Responses. Generic Uses settings now include PinAssociationVersions and the admin editor exposes it. DTO/service/repository support optional pinning without changing legacy defaults. Uses search carries link type/reference mode into the client; opening a pinned association selects the exact version read-only. Existing association rows remain read-only and Unlink removes only the relationship.

Setup installs once under Setup.DocumentControl.CommentResolution.v1. A guarded local helper installed matching configuration with native repositories/services and workspace-scoped SQL. Full Setup wizard was not rerun against this existing workspace.

## Automated and native checks

- 20 targeted API/SQL tests passed, including pinned association SQL behavior and existing review/PDF/package tests; SQL tests enabled, no skips.
- Five targeted association/script tests passed; the association test overlaps the earlier run. Four new script cases cover administrator-selected group/message, failure for missing parameters, clearing verification on rework, and no partial field clearing for invalid mappings. Total unique API/SQL/script cases: 24.
- Four Manage tests passed: installed-resolution seed preserves deliberate removals; true/false pin settings reach the actual client model; existing review seed marker remains preserved.
- Native lifecycle criteria against actual workspace data blocked verification with five missing fields. Within a rolled-back transaction, filling the owner, disposition, checker/date and Boolean evidence confirmation passed all checks. Native Boolean criteria compare the display value Yes.
- The actual native Uses projection continued returning drawing version 21414 after a later version was created in the same transaction. The transaction was rolled back; the real drawing remains unchanged.
- Native property permission queries confirmed author 364 cannot edit checking fields and approver 362 can.
- API, Manage and client builds passed. Documentation strict build passed.

## Initial persisted local test records

Response 15917 now has history version 2 / entity version 21560, with test owner 360 and an accurate disposition that drawing changes, final issue links and verification remain pending. It remains Submitted. Its original client audit row and returned PDF remain intact.

Pinned associations:

| Host | Target | Captured version |
| --- | --- | --- |
| Response 15917 | ITM-00000.pdf 15795 | 21414 |
| Response 15917 | Client reissue draft 15918 | 21555 |
| Response 15917 | Work Preparation draft 15919 | 21556 |
| Client draft 15918 | Original Transmittal 15914 | 21552 |
| Work Preparation draft 15919 | Original Transmittal 15914 | 21552 |

All five associations use PinnedVersion (3). Both replacement records are clearly named drafts and have no generated PDF. Original drawing remains file 1072, Work in Progress, version 1; original Transmittal remains file 1081, Issued, version 11. No email invitations were sent.

## Browser validation completed — 14 November 2026

Signed-in local browser walkthrough completed against the running services:

- Saved and reopened the engineering disposition and owner. Client outcome, comments and submission evidence stayed read-only.
- Used Link existing for the drawing; relinking an existing association did not duplicate it. Open details selected the pinned version read-only, with Browse/Delete disabled.
- Viewed both draft replacement links. Unlinked and relinked Work Preparation without deleting its record or changing the BOM.
- Verified Supersedes Transmittals, Client Responses, original Replacement Issues and drawing Client Comment Responses reverse navigation.
- Exercised Submitted → Acknowledged → In Progress → Ready for Check. Configured author/approver notifications ran.
- Confirmed Closed was blocked when checker, date and evidence confirmation were missing.
- Selected checker with the user picker, date with the calendar and evidence confirmation. Verified and Closed were exercised as explicit local control tests, not approval of actual engineering changes.
- Repeated Ready for Check → In Progress after the script fix. The checker/date were removed and evidence confirmation reset to false; owner, disposition and associations remained intact.

### Defects found and fixed

1. Uses controller omitted UsesLinkTypeId when mapping the request. Association tabs appeared empty despite saved links. The mapping now preserves the selected relationship type; three routing regression cases pass.
2. Script setProperty applied property names only, while the seeded reopen script uses stable pd_ID mappings. The runtime now resolves workspace property IDs, remains compatible with names and validates mappings before writing. SQL regressions cover renamed properties and refusal without partial writes when a mapped property is missing.
3. Change State ignored an automation error when the state change itself succeeded. Successful state results now retain and display follow-up warnings. Three result-model regression cases pass. The new warning branch was checked through model tests; an actual failing automation was observed before this UI fix.
4. Historical file Browse/Delete buttons now visibly respect read-only mode; confirmed in the browser.

### Final local state

Response 15917 is history version **13**, **In Progress**. Owner 360 and disposition remain; checker/date are absent and Evidence Checked is false. The original client audit and returned PDF are retained. The five pinned associations listed above remain. Original drawing is version 1 / revision A / Work in Progress, file 1072. Original transmittal remains version 11 / Issued, file 1081. Both replacement records remain drafts without generated PDFs. No external sharing or email invitations were performed.

Actual CAD edits, title-block revision alignment, formal replacement issue and distribution remain pending. The screenshots demonstrate the controls and traceability, not completed design changes.

### Additional verification

- Targeted API/SQL run: 15 passed, no skips (script lifecycle actions, relationship routing, comment resolution scripts and pinned associations).
- Targeted Manage/client model run: 6 passed (state-result warning handling and comment-resolution seed coverage).
- Latest API and client builds passed.

### Screenshots

Workflow images 46–57 are stored under docs/images/engineering-project. The public guide includes field editing, captured drawing versions, draft replacements, supersedes and reverse navigation, blocked closure and cleared verification after rework. Images 54–55 record temporary local checker/control tests; they are not evidence of actual engineering approval.

See [workflow instructions](../docs/workflows/engineering-comment-resolution.md).


## Subsequent reissue validation

The next [reissue walkthrough](engineering-project-reissue-test.md) found that staged Unlink failed on Save and Close even though rows disappeared from the visible register. That saved path has now been fixed and retested. This supersedes the earlier implication that visible removal alone established persistence. The response is now v15, Ready for Check, with final B/v3 and Issued replacement pins.
