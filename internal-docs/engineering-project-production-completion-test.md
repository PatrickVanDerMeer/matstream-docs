# Production completion / inspection records — local verification

Date: 17 November 2026. Local services and SQL only; no internet deployment, real manufacture, technical certification, email or payment.

## Implemented

- Ordinary Production Inspection Record occurrence category with direct evidence file, Every Change history, method/criteria, actual results, inspector/date and required/evidence flags.
- Production Order plan, plan-review confirmation, work-complete confirmation, completed quantity and live status panel; owned Uses tab with New and Choose columns.
- Administrator-defined header/final assembly plans in stored automation parameters; editable preparation/evaluation scripts, category/property/accepted-state mappings, quantity/prerequisite choices and messages.
- Native preparation action with authenticated Editor/create/modify/current-version checks, serialised order locking and create-missing-only keys; Generated From records the exact order version.
- Read-only evidence evaluator and `production_completion_ready` lifecycle criterion. Required records must belong to this order and have accepted current state, criteria/results, workspace inspector, date and required current file. A script cannot override native blockers.
- Existing readiness mappings connect current material coverage, operation holds, preceding order completion and required accepted supplier documents. In Progress is valid for completion even though readiness-to-start's active-state policy is different.
- Seeded inspection review lifecycle and permissions: accepted records read-only; Quality/Admin approve, reject and reopen. No different-person rule. Production completion remains an authorised normal lifecycle action.
- Focused Manage Setup template and inclusion in Engineering Project seed, guarded by `Setup.ProductionCompletion.v1` to preserve installed administrator customisations.

## Verification results

| Check | Result |
|---|---|
| API build | Passed, no warnings/errors |
| Manage build | Passed, no warnings/errors |
| Web Assembly build | Passed, no warnings/errors |
| API regression | 1,417 passed, 52 skipped; skips are not passing evidence |
| Manage regression | 311 passed |
| Focused completion unit + isolated SQL tests | 5 passed, including final focused rerun |
| Strict MkDocs build | Passed; existing two unlisted navigation pages remain informational |
| Signed-in browser completion walkthrough | Passed for both dedicated local orders; screenshots 192–207 |
| Fresh Manage Setup installer | Not exercised in this browser walkthrough; installed-marker preservation test passes |

The SQL test runs against an isolated fixture database and checks missing files, administrator file opt-out, rejected records, wrong-workspace inspectors, empty results, wrong keys, zero quantity, shared evidence and denied read access. Unit tests check editable messages, rejected-evidence bypass prevention and a failing preparation script. The installed-marker test verifies that reruns stop before changing definitions.

## Native local workflow rehearsal

Workspace **108 Engineering Workflow Test**; current local test account actor 360. Existing dedicated readiness orders: header **16118** and final assembly **16119**, under test project 16002. These are separate controlled fixtures, not a claim that the original project was physically manufactured.

Because no authenticated browser was available, a workspace-guarded native fixture helper installed equivalent category/property/tab/lifecycle/automation metadata locally. This did not bypass web authentication or create login tokens. The actual Manage seed installer remains subject to browser validation. The helper preserves existing configuration and uses a transaction for new metadata.

Within an outer rollback transaction, the harness used the real ScriptAutomationService/AutomationRunner, entity repositories, lifecycle permissions, lifecycle criterion, readiness/stock/document services and SQL:

1. Select Header fabrication / Final assembly plans; run stored preparation twice. Exactly three header and two final records; no duplicates.
2. Both orders blocked with incomplete inspections and material prerequisites.
3. Header Draft → Released → In Progress; normal Completed transition rejected with completion blockers.
4. Record synthetic results/criteria, inspector/date and a current file reference; normal Draft → In Review → Accepted transitions for each inspection. **The reused file reference is only an eligibility fixture, not an inspection certificate or verified upload.**
5. Accepted inspections did not override the held/missing fitting.
6. Stage three fitting stock/reservations and clear the test operation hold. Header eligibility passes.
7. Reopen an inspection Accepted → Draft: eligibility withdrawn. Review/accept again; preparation rerun preserves accepted records.
8. Header In Progress → Completed succeeds. Final assembly still blocked by motor/pump stock and supplier documents.
9. Stage motor/pump stock/reservations and synthetic accepted RVDs. Final assembly eligibility passes; normal Released → In Progress → Completed succeeds.
10. Roll back all simulated production/inspection states, property evidence, material ledger changes, reservations and database outbox rows. No notification/email worker ran during this transaction.

Log: `tmp/production-completion-native-test.log`. Subsequent audit confirms both orders are Draft; fitting MR 16122 has zero reserved/issued quantity. No synthetic inspection acceptances remain.

For the next signed-in walkthrough, the selected plans and **five unapproved Draft records 16155–16159** remain, without results, inspector/date or files. The header fitting hold and original unfulfilled prerequisites remain. The current status correctly blocks both orders. The native preparation action—not hand-inserted inspection records—created these Drafts.

## Limits and next work

- Browser layout, upload, Choose columns, inspector/result editing, accepted read-only controls, inspection review and production completion were exercised in the continuation below. Fresh Setup installation and distinct-role identities still need rehearsal.
- These tests prove software eligibility and normal lifecycle behavior, not actual inspection, certified test limits, manufacture or real supplier acceptance.
- Current criteria must be replaced with approved engineering instructions and actual results. Sample plans do not invent pressure or functional acceptance limits.
- Withdrawing evidence after an order is already Completed makes the live status blocked but does not automatically reopen it. The administrator's lifecycle correction process governs this.
- General whole-BOM consumption/returns, finished-goods receipt, as-built/client handover and project closure remain separate work.

See the [workflow guide](../docs/workflows/engineering-production-completion.md).


## Browser continuation — 17 November 2026

Signed in with the existing local test account in browser tab 22. Captured screenshots 192–207 and added the relevant views to the workflow guide.

- Header plan/preparation rerun retained exactly three records; the inspection Uses tab rendered required fields, inspector/date and Choose columns. The column chooser was opened and filtered, then cancelled without changing personal layouts.
- Corrected a **local fixture-only** missing General tab on category 4581. The real `EnsureCategoryAsync` seed already assigns General; the previous native fixture installer had omitted it. This was not an application authentication or permission bypass.
- Staged clearly labelled synthetic criteria, inspector/date and supporting field values through the native test helper. In the browser, edited and saved the header test report and final assembly functional test results, selected the current inspector account, and uploaded `LOCAL-TEST-inspection-evidence.txt` directly through Attached File/Browse for each test report. No real certificate or engineering acceptance limits were asserted.
- Browser lifecycle actions moved each of those two reports Draft → In Review → Accepted, with success confirmations. Accepted header record displayed read-only; Browse/Delete/placeholder controls were disabled and the uploaded file remained available.
- Three supporting inspection records, supplier RVD acceptance and temporary fitting/motor/pump stock/reservations were native test fixtures. These were **not** separate browser review/upload or warehouse-receipt rehearsals.
- Saved plan/work confirmations and quantity 1 using the browser numeric stepper. Automated direct input filling did not commit a numeric property; the supported increment control did, and the persisted quantity was checked. No business rule was weakened to obtain a pass.
- Found and fixed a **seed configuration gap**: Production Order permits Draft/Released/Cancelled → Completed as well as In Progress → Completed. Previously only the latter was guarded. New seed now discovers and verifies a completion criterion on every existing incoming Completed transition. Added the three missing criteria to the local test workspace without changing administrator permissions or other criteria. Browser Draft → Completed failed with current quantity/material/hold blockers. Final audit confirms all four incoming routes are guarded; only the direct Draft failure was exercised as a negative browser transition.
- With synthetic supporting requirements met, header showed all three accepted records and passed its check. Final assembly showed both accepted records but remained blocked **only** by the unfinished header.
- Browser moved header Draft → Released → In Progress → Completed, with success confirmations. Final assembly subsequently passed its own check and the browser moved it through the same sequence to Completed. This tested normal lifecycle service execution, not just display text.
- Restored current properties, file associations and states from the guarded pre-test backup; reversed all three material ledger additions. Both orders and five inspection records are Draft, no current inspection file or result/inspector/date remains, and fitting/motor/pump MR reserved and issued balances are zero. The header fitting hold and original supplier-document status remain. Historical synthetic versions/comments/uploads may remain as labelled audit evidence. The browser was reloaded and showed the expected restored blockers.

Evidence: `tmp/production-completion-browser-backup.json.restored`, `tmp/production-completion-browser-restore.log`, `tmp/production-completion-browser-final-audit.txt`; screenshots in `docs/images/engineering-project/192-...207-...png`. No internet deployment occurred. Fresh Setup UI installation, separate role identities and actual engineering/physical inspections remain separate validation.
