# Engineering workflow — local transmittal selection test

Date: 14 November 2026. Local services only; nothing deployed online.

## Example and observed results

Workspace: Engineering Workflow Test (`cwt3qp`, workspace 108). Project: Lube Oil Skid Project (15858). Assembly occurrence: ITM-00000 (15861), linked Library assembly 15726.

Created draft Transmittal TR-2026-0001 (15914) under the project through Explorer New. General recipient/purpose/date fields have not yet been completed in this test record; complete them before the later approval/PDF/portal step.

| Action | Expected | Observed |
|---|---|---|
| Open Project Documents | Documents reached through the project's assembly BOM | ITM-00000.pdf (15795/version 21414) and ITM-00006.pdf (15794/version 21413) |
| Edit filter: Name starts with ITM-00000 | Only the top-level drawing | One row; Custom filter badge |
| Save and reopen | Working filter and chosen columns retained | Custom filter retained; Description column added through Columns / Save & Close |
| Check ITM-00000.pdf and Add selected documents | One persisted Document Issue Line | 1 added; issue-line entity 15915/version 21542 |
| Repeat Add | No duplicate line | 0 added, 1 already included |
| Reopen Document Issue Lines | Saved drawing and revision | ITM-00000.pdf, revision A, Work in Progress, category Document Issue Line |
| Reset working filter | Defaults restored without changing the register | Both drawings return; register still contains ITM-00000.pdf only |
| Inspect SQL reference | Exact displayed version pinned | DefinitionLink to 15795, pinned version 21414, reference mode 3 |
| Administrator editor | Settings load, save and reopen | Configuration / Display Tabs / Transmittal Project Documents / Settings works |

MatStream revision A/version 1 is separate from the revision printed inside the original PDF. This test does not certify Engineering's document-content review.

## Problems found and handled

- FilterResult Save did not commit the query-builder's pending buffer. Save now commits it before reading/sending the filter; the name filter was retested in the browser.
- The legacy shared Documents tab can omit Document Issue Lines through its category filter, showing an empty input row. A dedicated administrator-configurable Transmittal Document Issue Lines Uses tab now shows those occurrences with independent Library choices and default columns. Shared Documents configuration remains unchanged. Use Document Issue Lines for the saved register.
- Selection saves independently of General. The host refreshes matching clean issue registers after Add; pending register edits are preserved.

## Automated checks

- 20 API tests passed: ProjectDocumentsTests (three SQL scenarios) plus FilterItemConfigJsonTests. Coverage includes traversal, atomic writes, duplicates, stale versions, permissions, configurable states/pinning/filter editing, project-owned documents, workspace boundaries and filter persistence.
- ProjectDocumentsSeedTests passed using the real seed context and client Uses model builder: ID resolution, selection/register installation, separate row/Library filters, preservation of administrator changes and deliberate removal.
- API, Manage and Web Assembly builds passed. The Manage test project emits its existing BL0005 warnings from the linked grid context-menu helper.
- Strict documentation build passed.

SQL scenarios create and drop isolated MatStream_ProjectDocs_* test databases. The walkthrough uses the disposable local workspace and real repositories/API/UI.

## Remaining scope

Header completion, transmittal PDF generation/attachment, issuing, historical portal file access and commented return uploads still need their own workflow tests. No work-preparation transmittal was created by this test.
