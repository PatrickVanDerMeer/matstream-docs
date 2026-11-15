# Issued Transmittal portal local test

14 November 2026. Local services only; no online deployment. The user chose the current local test account pvdmeer@pmsoftware.nl. No client or third-party invitation email was sent.

## Result

Engineering Workflow Test (workspace 108, cwt3qp), Lube Oil Skid Project (15858), issued TR-2026-0001 (15914/version 21552, displayed version 11). Fixed package: transmittal PDF StoredFile 1081 and Document Issue Line 15915 resolving ITM-00000.pdf at document version 21414, revision A/version 1, StoredFile 1072.

The readiness check used the actual workspace repositories and permissions inside a rolled-back transaction. It confirmed that the existing generated PDF evidence matched the issued package. A subsequent actual ShareService test created share 10 for the same test account with SendEmailInvitations=false; its email-service stub recorded zero calls. The actual external ceiling group, read-only capabilities, fixed header/list, selected file hash and rejection of Library/unselected document access passed.

The signed-in browser then verified the portal root and drawing detail. Both downloads returned HTTP 200 and produced access-log entries for files 1081 and 1072. The engineer Share dialog showed Client Portal, View and the configured expiry; the test cleared Send email invitations and created share 11 through the application. The Link ready screen explicitly confirmed creation without email. Share 11 was opened and both downloads succeeded again, with the correct file IDs in its access log.

The earlier share 10 was identified by copying its link in Manage Access, then revoked through the application. Opening it showed This link isn't available. Share 11 remains active, view-only, and expires 14 December 2026. No Library drawing was modified by the browser test.

Administrator UI: Configuration → System Settings → Document package portal. Changed expiry 30→45, saved/reopened and verified 45, then restored/saved 30. The dialog supports package/document categories, allowed states, issue-line category, visible properties, default external group and download default. The screen test caught and fixed a null-ID system-property rendering issue; only configurable property definitions are offered.

## Implementation

The ordinary portal identity, named-recipient, expiry, revocation, ceiling-group and logging mechanisms are reused. Transmittal shares resolve from a server-owned fixed package instead of live structural or Library traversal. The share and its recipients/package commit together. It captures the issued root/PDF, the pinned drawing files and permitted property values; document issue line IDs serve as bounded portal row identities. Source Library entity IDs cannot be browsed through this link.

New share creation requires configured shareable state, available generated PDF evidence matching the saved package (normalizing the expected preparation-to-issued state change), one pinned document per issue line, allowed document categories and the sharer's read/file grants. This feature does not loosen any named-recipient/group checks. Issued originals cannot be uploaded/replaced even if an old or altered share permission says Edit. Legacy live shares on managed package records fail closed until a fixed share is created.

File references are recorded separately for cleanup, so purging source history cannot make the collector remove files retained by a package. No duplicate file bytes are stored. Snapshot/file references use int identifiers. Workspace default policy and Client Portal grants are seeded once and preserve subsequent admin changes/removal.

## Automated validation

81 API tests passed, including SQL-backed package persistence across Library advancement/reissue, later/unrelated file rejection, property/category narrowing, tenant scope, read-only behavior, named identity, expiry, revocation/disabled external groups, retained files, repeatable fresh/legacy evidence migration, existing generic portal/PO sharing and generation/lifecycle regressions. Three seed tests passed, including portal policy/group installation and preservation of administrator changes and deliberate removal. API and Web Assembly builds passed with zero warnings/errors. Manage test builds retain existing BL0005 warnings. The documentation strict build passed after workflow updates.

Library advancement/reissue and alternate recipient/expiry cases were exercised in isolated SQL tests, not by changing the live workflow drawing or signing in as another account. Invitation delivery was deliberately not tested. Client review decisions, returned commented PDFs and production release authority remain separate work.

See [workflow guide and screenshots](../docs/workflows/engineering-transmittal-portal.md).
