# Step 1 validation: catalogue, document control and permissions

Date: 14 November 2026. No application code, seed, migrations or production workspace settings changed. Tests ran against current local source, including pre-existing uncommitted work. This is not certification of the deployed production workspace.

## Test results

116 selected checks passed, zero failed. Two SQL tests initially skipped were subsequently run successfully with the explicit local SQL setting, against isolated test-created databases. One initial parallel build hit a shared-output file lock; sequential rerun passed. No test/code fixes were made.

| Run | Passed | Coverage |
|---|---:|---|
| baseline-api.trx | 65 | Property criteria/validator, lifecycle grant policy, PO vendor-document materialisation request; two SQL cases initially skipped |
| baseline-manage.trx | 9 | RVD file policy, piping thumbnail seed contracts, supplier portal seed |
| baseline-sql.trx | 2 | Current portal structural scope and historical-version purge protections |
| baseline-catalogue.trx | 23 | Engineering sample BOM, seed document metadata, lifecycle assignment and BOM fulfilment setup |
| baseline-documents.trx | 17 | Drawing document publication, initial lifecycle assignment and Library-context permission tests |

TRX files: repository-root `tmp/engineering-baseline-tests/`. Unit/source-contract tests are not equivalent to running Setup and the whole application. The two SQL checks exercise real repository behavior against minimal isolated schemas, not the entire tenant model.

## What works, with evidence limits

### Catalogue

- BOM tests pass for system quantities/positions/units and the five-row header, including 496 mm pipe, 1135 mm hose and 1673 mm tubing. Catalogue mapping resolves system/header/pump/motor to ITM-00000/00006/00012/00021.
- Both PDF assets exist, as does the transmittal Word template. The PDFs were visually reviewed during planning; this run did not regenerate/compare CAD outputs or render the Word template.
- Source seeds article-specific RVD assignments: pump six, motor four. Existing source/PO ownership and pinning paths are present. PO checklist test validates the service call/failure propagation with mocked repositories, not full installed materialisation.
- Drawing publish tests pass: named document creation/linking and replacement of legacy direct-parent PDF follow the tested behavior.

### Document control

- Seed provides controlled documents, transmittals, submittals, issue lines, numbering/revisions, response fields, worklists and print-template assignments.
- Issuance attaches Pin Document Package Contents. Source rejects empty/unresolved packages and pins all line definition references transactionally. Existing pins cannot be silently moved to another definition version by a retry.
- SQL history-purge test passes: latest, other-workspace and pinned-reference versions are protected; unused version purge and rollback on failed purge behave as tested.
- This does not prove end-to-end original package membership, historical printed output or file retention under every purge path. P0-03 remains validation, with the failure-state concern below.

### Permissions

- Selected lifecycle grant/property-criterion and Library-context tests pass. RVD client policy rejects inherited/template/other-owner/unsaved rows and requires upload/update permissions; those client checks are not a substitute for backend checks.
- DocumentService invokes EntityModifyGuard before upload; that guard checks the entity's own lifecycle Modify permission. Portal route separately requires scope and FileUpload capability. Therefore the existing Accepted read-only mechanism has a backend path, not merely a hidden button.
- Actual supplier recipient memberships/effective state grants are not validated. Entity state permissions do not automatically propagate from a parent: locking a transmittal alone does not demonstrate all issue lines and documents are protected.

## Findings needing configuration or fixes

| Finding | Classification | Recommended action before implementation |
|---|---|---|
| Seeded transmittal Documents picker allows Controlled Document only; engineering sample PDFs are Drawing entities | Confirmed seed configuration mismatch | Configure eligible Drawing category in the issue-line document picker/selection integration, rather than duplicate files. Validate actual workspace filters. Administrator-editable. |
| Transmittal FilePolicy is None | Existing design constraint, not inherently a bug | Attach generated PDF as a linked file-bearing Document through the configured PDF action; keep it distinguishable from transmitted contents. Portal must expose it. Direct attachment would instead need an explicit configurable file-policy decision. P0-04 integration. |
| DocumentControlPart and RVD seed do not establish the intended state/transition permission matrix | Configuration incomplete in inspected seed paths; live state unknown | Inspect/configure issuer/reviewer/Quality permissions; Accepted supplier read-only, authorised Quality reopening, and issued package edit restrictions. Do not assume Final means read-only. |
| Issuance pinning is an attached post-transition automation; automation failure does not undo the state change by default | Confirmed failure-mode risk from current lifecycle contract | Reproduce empty/unresolved/pin-failure issue in a full practice workspace and inspect actual state/result. This prevents declaring issuance fully validated. If misleading Issued state occurs, propose a blocking preflight/atomic issue fix under P0-03 before implementation. |
| RVD submission resolves literal Submitted state name | Confirmed existing required P0-07 fix | Keep administrator state-identity mapping in agreed development scope; no change now. |
| Current portal traverses structural children/current versions rather than issue-line pinned definition versions | Confirmed existing P0-04 gap | Retain agreed pinned-package portal development; SQL scope test passing does not resolve this. |

Permission requirements should be configured via existing UI where possible. Any new default, option/description, mapping or action setting must remain administrator-editable. No new supplier-locking engine is required.

## Not yet verified in a live/practice workspace

No specific application workspace/session was selected or modified for this validation. The following remain required before step 1 can be declared fully complete:

1. Read installed catalogue/BOM/units/sources/RVD definitions and selected versions, then confirm effective project/MR/PO ownership in a disposable practice scenario.
2. Add the actual sample Drawing documents to a transmittal and verify picker compatibility/configured restrictions.
3. Issue revision-0 package, revise drawings, reopen/print original and new packages, and verify original membership/files/revisions remain correct.
4. Exercise failed issuance and inspect state, user-visible result and automation history.
5. Test supplier upload/replace attempts in Required/Submitted/Accepted under the actual recipient identity; verify Accepted refuses changes, Quality can reopen and supplier can then correct.
6. Verify non-authorised users cannot issue, accept/reject or edit issued content through API as well as UI. Audit broad group Allows; do not rely on a Deny to override another Allow.
7. Verify permissions and retention for both transmittal contents and generated PDF attachments, including file/version purge protections and printed output.

## Conclusion

**Subsequent agreed approach:** use the complete workflow as the practical test, starting with quotation/project creation. The remaining checks above are performed when reached in that walkthrough, not as a separate prerequisite exercise. This report remains evidence of the local automated/source checks only.

The selected automated baseline is green, and no catalogue rebuild is justified. Step 1 is partially validated, not fully signed off. Configure/verify document eligibility and the permission matrix, retain the existing required P0-04/07 fixes, and investigate failed issuance before treating P0-03 as complete. No implementation was performed.
