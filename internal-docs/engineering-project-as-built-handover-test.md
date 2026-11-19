# As-built package and final client handover — local verification

> Storage update, 19 November 2026: dedicated handover/readiness/start-notice tables have been replaced with protected entity properties and shared automation checkpoints/claims. See [the consolidation report](workflow-evidence-consolidation-2026-11-19.md).

17 November 2026. Local services only; workspace 108, Engineering Workflow Test.

## Implemented

The final-handover profile uses the ordinary Transmittal and pinned Document Issue Lines. Workspace administrators configure section names, descriptions, minimum counts, source categories/states, production/receipt conditions, response states, responsibility groups, mappings, labels and scripts. Engineers review the scope, choose documents using editable filters and classify the issue lines. Existing installed configuration is preserved by the focused Setup feature.

The native issue/share checks protect the required scope and PDF consistency. The evaluator script can add restrictions but cannot bypass these checks. The recording script checks the exact issued PDF version, fixed share and current whole-package response state, then records an audited snapshot. Repeated requests reuse the existing journal; they cannot create duplicate handovers.

## Automated verification

- API regression: 1,432 passed, 52 skipped, zero failures.
- Focused handover tests: 7 passed. SQL tests exercise repeatable migration, missing sections, scope and pins, wrong response target/file, current response state after a new version, expired share, changed PDF, permissions, rollback and concurrent/repeated recording.
- Project document tests cover opt-in occurrence files without binary copies and procurement evidence limited to the PO line fulfilling a project MR, excluding sibling lines.
- Manage regression: 314 passed, zero failures. Tests cover preserving installed administrator configuration.
- API, Web and Manage builds passed. Strict Zensical documentation build passed. Repository diff whitespace checks passed.

## Browser workflow

1. Created clearly labelled synthetic package 16160 for project 16002. The 19 pinned documents include two drawing files, BOM and manual indexes, ten supplier documents and five inspection records.
2. Temporarily completed the two local production orders with synthetic inspection evidence and used the existing finished-output receipt service. Both production completion and receipt requirements remained enabled during the handover test.
3. Generated the actual PDF register and issued package version 22342 through the existing lifecycle/PDF consistency checks.
4. Created fixed share 17 for the current test account only, with email invitations disabled. The package panel showed acknowledgement 0/1 and disabled final recording.
5. In the portal, selected Whole package and directly submitted the Approved response state with an explicit local-test disclaimer. The application then showed acknowledgement 1/1.
6. Clicked Record final handover in the browser. The configured recording script created journal 1 for package 16160/version 22342/share 17. Native script retry reused journal 1.
7. Revoked the test share and verified the portal displayed “This link isn't available”.

Screenshots and the user procedure are in the [workflow guide](../docs/workflows/engineering-as-built-handover.md).

## Cleanup and limits

Temporary production states, inspection/RVD evidence, inventory, reservations, receipt journal and portal/review settings were restored. Orders 16118 and 16119 are Draft again, receipt count is zero and finished-output stock is zero. The labelled synthetic package, client response and historical handover journal remain traceable; share 17 is revoked.

The BOM/manual test indexes and temporary supplier/inspection attachments were synthetic. This proves the software path, not physical manufacture, real document completeness or contractual acceptance. A fresh-workspace Setup rehearsal, separate-role permission walkthrough and real complete BOM/dossier remain. Dispatch, payments and project closure are separate activities.

Final audit: journal 1 references 19 documents and accepted response state 6145; the share is revoked. Existing portal category/purpose configuration is intentionally preserved: administrators must explicitly allow any newly shared dossier categories and the As Built review purpose in established workspaces.
