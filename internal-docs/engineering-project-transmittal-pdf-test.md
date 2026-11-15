# Transmittal PDF local workflow test

14 November 2026. Local services only; nothing deployed online or shared with clients.

## Result

Workspace Engineering Workflow Test (108, cwt3qp), project Lube Oil Skid Project (15858), Transmittal TR-2026-0001 (15914), selected Document Issue Line 15915 referencing ITM-00000.pdf version 21414.

- Installed Generate Transmittal PDF automation 50 and the register ribbon using the shared seed defaults. Enabled AllowDirect on the existing test category through the narrow local configuration helper; fresh setups seed it, existing administrator category choices are preserved.
- Saved For Approval purpose, generated the first PDF on version 2 (StoredFile 1076). No separate document entity was created.
- Regenerated without header changes: version 3 (1077); version 2 retained its original file.
- Saved project/subject corrections, creating ordinary header version 4, then regenerated to version 5 (1078). Extracted text confirmed project, subject, purpose and ITM-00000.pdf revision A; visually inspected the one-page PDF with no clipped fields or unexpanded merge placeholders.
- Changed the automation's history comment through the administrator UI to Generated approval transmittal PDF, saved, and generated version 6 (1079). SQL confirmed that exact configured comment; no source-code change was needed.
- Reopened version 2 through History and confirmed its original 10.5 KB file remained available. Current version 6 has the corrected 10.4 KB PDF. The browser download event did not complete in the automation bridge; PDF contents/layout were inspected from the local stored files instead. Download interaction is not claimed as verified.

The package remains Draft, with recipient/date fields incomplete. Issuance, portal sharing, release authority and returned client comments are separate workflow tests. Explorer's already-open History can remain cached after generation; reloading displays the new version. The edit dialog itself refreshes immediately to the generated version.

## Automated checks

29 API/script tests passed, including SQL-backed first attachment, regeneration, identical bytes, cloned links, stale source, missing lines, state restrictions, file/read permissions and render/attach rollback. Seven seed/template tests passed, including keeping existing admin template metadata/assignments and not recreating deliberately removed installed PDF configuration. API build: zero warnings/errors. Documentation strict build passed. Manage test build reports existing BL0005 warnings in linked grid helper tests.

See [workflow and screenshots](../docs/workflows/engineering-transmittal-pdf.md).
