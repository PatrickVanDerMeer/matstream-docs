# Distribution Channel implementation and local verification

Implemented 15 November 2026. No internet deployment or email invitations.

## Configuration and records

Document Control Setup seeds a native Distribution Channel category and a separate Distribution Record category. Channels contain contact references, extra contacts, address and telephone properties, email recipients and internal workspace group selections. Transmittals reference a channel through an EntityRef property. Definitions and resolver mappings are editable by the workspace administrator; channel records use ordinary category/lifecycle permissions and are editable by the seeded project teams.

Setup uses a feature installation marker. Existing settings, installed definitions and administrator removals are preserved on rerun. Legacy text channel property values remain historical data.

Local workspace 108, project 15858 contains Client (15928), Work Preparation (15929) and Purchasing (15930) channels. Transmittals 15931 and 15933 select Client and Purchasing respectively. Both were generated, issued and distributed in the browser.

## Verified

- Application UI builds with no warnings/errors; six targeted seed/document-control tests pass.
- 37 targeted API/SQL tests pass on the complete repeat. They cover PDF capture, direct review states, subscription behavior and the new distribution resolver/history flow.
- New SQL coverage checks contacts (including inherited Contact email), enabled group membership, property access, stale previews, native internal distribution, idempotency, immutable snapshot content and pinned versions.
- Native local resolver returns only the current test email for Client, and Sample Buyer (367) for Purchasing.
- Native permission checks confirm Engineering Authors (362), Project Management (365) and Work Preparation (368) can read/update the channel record.
- Strict documentation build passes.

The first regression run exposed an intermittent subscription fixture precision mismatch: it used datetime2(7), while the production subscription-hit column is datetime2(3). The fixture now matches the production schema. No subscription business behavior was changed.

## Browser verification completed

Local services listen on 7053, 7081, 7239 and 7273. After sign-in:

- Edited Client phone, saved/reopened channel version 2 (21608).
- Added Work Preparation to Purchasing, saved/reopened, then removed it and saved version 3 (21610). Purchasing remains the sole recipient group.
- Generated both PDFs through the existing automation and transitioned both test transmittals to Issued. Client package is version 5; Purchasing version 3.
- Client preview populated the current test account email and contact/address fields. Created share 15 without email invitations or review requests. Browser portal verified ITM-00000.pdf revision B/version 5 (21591).
- Purchasing preview resolved Sample Buyer (367). Browser send succeeded and Distribution Records showed record 15936. Read-only details retained Sample Buyer and drawing B/version 5.
- Administrator notification wording was changed, saved, reopened and verified; original wording restored. Configuration remains enabled.
- Native audit confirms records 15935/15936, six pinned association links, exactly one in-app notification for Sample Buyer and zero portal shares for Purchasing. Notification inbox was verified by database record, not by signing in as Sample Buyer.
- Screenshots 90–95 are stored with the Engineering Project images; the user guide embeds the contact, recipient, success and administrator views. Screenshot 94 is an auxiliary view of the normal two-line Memo editor, not a complete display of the audit text.

No fresh full Setup wizard was run in this existing workspace. Local definitions were applied through the guarded feature helper using native repositories; seed preservation has targeted automated coverage. Prior issued packages were not regenerated.

## Template scope

At the original channel test, the supplied historical transmittal was a reference for contact/address fields and the PDF printed only the channel name. The follow-up implementation adds the contact/address delivery block: see `engineering-project-channel-pdf-test.md`. The historical company's branding is not reproduced.

User guide: `docs/workflows/engineering-distribution-channels.md`.

## Three-channel reissue — 15 November 2026

Prepared separate disposable local drafts with native repositories and pinned source links, copying the previous template-test header. Draft creation/selection was not browser-tested in this pass. Browser-tested Ready to Issue, PDF generation, Issued transition and distribution for all three:

| Entity | Transmittal | Issued version | Cover file | Distribution record |
| --- | --- | --- | --- | --- |
| 15939 | TR-2026-0009 — Client channel reissue | 21629 / v3 | 1097 | 15945 |
| 15941 | TR-2026-0010 — Work Preparation channel reissue | 21632 / v3 | 1098 | 15946 |
| 15943 | TR-2026-0011 — Purchasing channel reissue | 21635 / v3 | 1099 | 15947 |

Client share 16 uses only the current local test account, with invitations/review requests off. Browser portal verified B/v5. Work Preparation resolves user 368; Purchasing resolves user 367. Browser success messages confirmed both internal sends; SQL verifies exactly two document.distributed channel-1 notification rows and zero internal portal shares. No separate recipient inbox login was performed.

All snapshots pin document 15795/version 21591/file 1083 (B/v5) and their own issued cover/channel version. Exported all three actual cover attachments read-only and checked one page, correct destination department and drawing register. Source drawing and prior issued entities retained their previous latest versions. There was no new Vault upload/release, response-check completion or manufacturing release.

The first Purchasing issue check reported stale saved contents. Regenerating through the browser cleared the guard and issuing succeeded. No code/permission/configuration changes or bypass were used. Exact cause of the first stale result was not isolated; do not claim a diagnosed defect or fix. Generation may still have been finishing when the dialog was closed.

The focused header retains LOCAL TEST - project reference and blank purpose/date. Supersedes associations were not added in this pass. User instructions cover these ordinary preparation steps without claiming they were tested here.

Screenshots 103–110 capture Client preview/portal, Work Preparation preview/success, Purchasing stale guard/preview/success and retained distribution. Native audit: tmp/distribution-channel-local/reissue-results.jsonl; PDF exports: output/pdf/engineering-channel/reissue-{entity}.pdf. These local exports are not published portal links. No internet deployment or invitation emails.
