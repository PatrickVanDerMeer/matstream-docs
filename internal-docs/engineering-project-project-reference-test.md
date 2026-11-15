# Transmittal project reference — local verification

Date: 14 November 2026. Workspace 108 / cwt3qp, Engineering Workflow Test. Local services only; no deployment, emails or new portal shares.

## Implementation

- Document Project is an administrator-configurable nonunique EntityRef with EntityPicker. The default family is Folder and allowed categories have Project behavior. Its int reference replaces repeated Project Number strings in fresh Document Control sample data, category assignments, print mappings, worklist columns and portal visible-field defaults.
- New seed installation markers retain property/category IDs and prevent reruns from recreating renamed/deleted references or deliberately removed assignments. Existing print-template customizations are not overwritten. No schema migration was needed.
- Local existing configuration was explicitly updated: property 8155, Project category 4527, package category assignments, Transmittal/Submittal Header Data and new-share visible fields. Legacy Project Number 8024 remains unique. Existing property values and issued files were retained.
- GeneratedPdfConsistencyService fingerprints display names of EntityRefs used by mapped header fields. A project rename invalidates a generated draft cover. With no populated mapped header references, the prior fingerprint input remains unchanged.

## Workflow evidence

The signed-in browser selected project 15858, Lube Oil Skid Project, through the folder picker and saved both drafts. TR-2026-0004 (15923) is version 2 / 21594. TR-2026-0005 (15924) is version 2 / 21595. The latter was reopened to confirm persistence. Both have one Document Project value pointing to 15858, with no Project Number value copied onto either version.

TR-2026-0004 includes ITM-00000.pdf B/v5 and generated a cover through the application action. The PDF displays Project: Lube Oil Skid Project. Mapping source 8155 uses the existing ProjectNumber template marker. This prints the project display name, not its Project Number property. Selecting Document Project does not change Project Documents scope, which still uses the containing project.

The in-app browser PDF preview was blank and its download event timed out. The actual generated attachment was therefore exported from local StoredFile storage by the guarded verification helper, text inspected and rendered for visual inspection. The guide labels that image as a rendered generated PDF, not a browser preview. Artifact: output/pdf/project-reference/Transmittal-project-reference-test.pdf. Screenshots/render 80–85 are in docs/images/engineering-project.

## Checks

- GenerateEntityPdfTests: 13 passed, including mapped-reference rename consistency, portal/review/cleanup coverage.
- DocumentProjectReferenceSeedTests: 3 passed, covering fresh defaults, installed reference preservation and deliberate assignment removal.
- Application/API builds succeeded without errors. Manage build succeeded; its initial DLL copy retries were resolved by stopping the old local Manage process.
- tmp/project-reference-local --verify-unique: native property write and uniqueness check in a rolled-back transaction rejected a duplicate Project Number on another project. The existing projects' blank Project Number values and histories were not changed.
- --verify-references: both drafts store reference 8155 only; both template maps point to 8155. PDF consistency succeeds, fails after a transaction-local project rename, then succeeds after rollback.
- tmp/reissue-local --verify-packages --verify-evidence passed: original share 12 retains A/v1; replacement shares 13/14 retain B/v3 and ITM-00006 A/v1. All five resolution/supersedes pins remain unchanged. Response remains Ready for Check, evidence confirmation false.

Strict documentation build is recorded in the completion report. No test transmittal was issued or shared. Existing project number uniqueness was not disabled.
