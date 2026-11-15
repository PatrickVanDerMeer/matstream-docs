# Channel contact/address covering PDF — local test

15 November 2026, local workspace 108. No internet deployment, email invitation or additional portal share.

## Implementation

- Header print sources are generated from the administrator's Distribution Channel detail mappings. Stable source keys retain property identities when labels/order change. The Print Templates Header Data picker exposes the configured labels.
- Runtime reads the selected channel, checks record/property/contact read permissions, and uses ordinary entity projections for printable values. Thumbnail storage identifiers are not printable.
- Document Control's fresh template mappings seed ChannelDetail1–5. The Word generator adds organisation, department, contact person, multiline delivery address and phone. The existing Setup installation guard preserves installed templates and administrator mappings; existing workspaces adopt the updated file/mappings explicitly.
- Generation still uses the existing automation/PDF action. No separate attachment/versioning mechanism or database migration was introduced.

## Verified

- 38 targeted API/SQL regression tests passed. New SQL coverage verifies mapped values, changed address, unreadable fields/contact and deliberately removed mappings.
- The renderer test passes: all five fields, multiline address and drawing register print without unresolved markers, on one page.
- Six targeted seed tests pass; application, API and Manage builds pass. Word OOXML validation passes (Python UTF-8 mode).
- Local template 199 adopted the generated seed Word file and five header mappings. Fresh draft TR-2026-0008 (entity 15937) uses Client channel 15928 and ITM-00000.pdf B/version 5.
- Real PrintTemplateDataMapService returned the local channel's organisation, department, contact display name, full address and phone.
- GeneratedPdfRenderService produced the PDF. GenerateEntityPdfService and DocumentService attached file 1093 to version 1 (21617), then regenerated file 1094 at version 2 (21619). Earlier PDF remains in history.
- A rollback-only address edit made MatchesModelAsync fail; rolling back restored the matching model. No channel data was left changed by that test.
- PDF text extraction and visual inspection confirm a one-page cover, complete address, correct contact and revision B register row. Preview: docs/images/engineering-project/96-transmittal-channel-pdf.png. Export: output/pdf/engineering-channel/TR-2026-0008-channel-details.pdf in the workspace root.

## Browser verification completed

The local startup loader now reloads boot resources on loopback hosts, preserving their integrity metadata and leaving the JavaScript module loader and hosted startup defaults unchanged. Source: MatStream.Web.Assembly/wwwroot/js/blazor-startup.js. The app loaded successfully after restart and again after a browser reload; three Node startup tests pass.

Browser Header Data testing revealed that duplicate sources reached a database unique constraint and replacement was not atomic. PrintTemplateDataMapService now validates duplicate markers/sources before deletion and replaces fields in a transaction. The SQL regression verifies duplicate rejection, rollback after an insert failure and successful replacement. The browser now shows a readable error and reopening retains the saved mappings.

Final regression run: 40 targeted API/SQL/renderer tests passed, with no skips; three Node startup tests passed. The documentation strict build and scoped whitespace checks passed.

The browser saved and reopened a valid Department/Phone source swap. Generate transmittal PDF attached version 3/file 1095. The original mappings were restored, saved and reopened; generating again attached version 4/file 1096. Exported actual attachment text confirms version 3 follows the swapped sources and version 4 restores Department: Client and the configured phone. Both are one-page PDFs with contact, multiline address and ITM-00000.pdf revision B. Versions 1–4 remain available. TR-2026-0008 remains Draft; this test did not send emails or create shares.

Screenshots 97–101 record the picker, duplicate-source validation, temporary saved swap, restored mapping and version 4 attachment. Image 102 renders the actual browser-generated final PDF. The embedded browser's native PDF iframe was blank and its blob-download event was not observable; attachment contents were therefore exported read-only from the local file store for text and visual inspection.

The native test harness reuses the local renderer registration without logging its license. The renderer-only unit-test export uses trial output; the published workflow preview is the actual local application-service PDF, not that unit-test export.
