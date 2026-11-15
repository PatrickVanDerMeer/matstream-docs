# Project drawing revision action fix — 14 November 2026

## Problem and behavior

Selecting ITM-00000.pdf beneath the project's inherited ITM-00000 assembly supplied an inherited link ID. EntityRevisionService treated every such row as a BOM occurrence and invoked ProjectLineCopyService. A Library drawing has no source occurrence/part-definition pair, so it was rejected as stale even though the inherited drawing link was valid.

The server now classifies the actual record using its configured category family, not its name, filename or a special Drawing category ID. Definition-family records update that same Library record through the existing save/versioning path. Occurrence-family rows retain project-line copy-on-write. The context DTO identifies a shared definition, and the revision dialog explains the update target and fixed issued versions.

Shared definitions pass their own modify guard in context and save, in addition to existing revision grant/read access and expected version/revision checks. Inherited link validation also rejects retired paths and links stamped to a previous parent version. Unrelated link IDs cannot redirect the revision action.

## Configuration

Existing administrator UI remains authoritative: Revision Schemes, workspace sequence values, category revision default/version creation policy, grants and lifecycle locks. No new hard-coded revision sequence, category name/ID rule, database identifier or configuration migration was introduced. Existing records use their saved EV_RevisionSchemeID; category defaults initialize new drawings. Manual revision input remains supported.

Browser inspection confirmed the editable workspace Document Alphabetic (A-J) scheme, its sequence values, and Drawing category's Alphabetic Revision Index / Every change defaults. These settings were inspected without changing administrator configuration.

## Automated checks

23 targeted API/SQL tests passed with SQL tests enabled and no skips: ChangeRevisionTests and NestedProjectLineCopySqlTests. Four new SQL cases cover a definition using a configurable suggestion (C-02), saving without the project-copy service; a locked inherited definition; occurrence context retaining copy semantics; and rejection of wrong-target, old-parent-version, retired-path, non-inherited links and stale drawing versions. Existing grant/read/validation/versioning/rollback checks remain green. API and client builds passed with zero warnings/errors.

## Actual local browser test

Workspace 108 / cwt3qp, Engineering Workflow Test; current test account. Local services only; Core stayed running and sign-in was retained.

1. Explorer → expand ITM-00000 → select ITM-00000.pdf → Actions → Change Revision.
2. Dialog correctly reported B and suggested C from Alphabetic Revision Index. Shared Library update explanation was visible.
3. Entered BTEST and applied. Result Saved; same drawing entity 15795 acquired version 4 / entity version 21590.
4. Reopened the action. Current revision was BTEST. Entered B and applied; result Saved, version 5 / entity version 21591.
5. Explorer History showed versions 1–5; current properties showed revision B, version 5, Work in Progress.

Temporary BTEST was solely a persistence test. The current PDF still prints B and contains M-101/P-101. Metadata and PDF are again aligned. No production release, file upload, new share or email occurred during this fix test.

## Native final verification

Read-only checks through tmp/reissue-local --verify-revision-fix --verify-packages --verify-evidence confirmed:

- Current drawing entity15795/version21591, B/v5, file1083, original lifecycle state unchanged.
- History: A/v1/file1072; A/v2/file1083; B/v3/file1083; BTEST/v4/file1083; B/v5/file1083. Saved scheme1 retained on all versions; native suggestion from B is C.
- Original issued share12 still captures A/v1/file1072; client share13 and Work Preparation share14 still capture B/v3/file1083. Work Preparation also retains ITM-00006 A/v1/file1071.
- All five final association pins remain unchanged, including response→drawing21573 and response→issued replacements21587/21585.
- Response remains Ready for Check at v15, verification flag false with no checker/date.

The test helper was used only for read verification during this fix; both actual revision changes went through the browser/API action.

## Evidence and plan status

Screenshots 73–77 and 79 in docs/images/engineering-project cover scheme suggestion, successful save/restore, workspace scheme/sequence editors and retained History. Category settings were also inspected through the UI; screenshot 78 is only a supporting general-category capture because its revision fields were below the visible scroll area. The [Engineering reissue guide](../docs/workflows/engineering-reissue-drawings.md#change-the-drawing-revision-from-the-project) now uses the corrected action and retains the earlier workaround only as historical context.

Close the inherited drawing revision gap. The separate reusable Project Number/reference and download-only portal status gaps from the reissue test remain open. Technical checking, Inventor source changes and manufacturing release remain outside this revision-action fix.
