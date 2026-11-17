# Engineering workflow — continued local tests, 15 November 2026

## Scope and result

Continued independently after the user's request to do the remaining tests. Tested existing behavior; no production feature changes, external deployment, invitations, email or payment actions. Temporary supplier-upload and exclusion probes were rolled back. Existing local motor/pump orders remain the working examples.

The executable tests pass, but the complete supplier-to-final-payment story is **not yet validated**. The remaining gaps below prevent an honest end-to-end sign-off.

## Automated regression evidence

| Suite | Passed | Skipped | Failed |
|---|---:|---:|---:|
| Full Infrastructure suite, MATSTREAM_TIMESHEET_SQL_TESTS=1 | 1357 | 52 | 0 |
| Full Manage unit suite | 298 | 0 | 0 |

The 52 skipped cases are not passing evidence. Before the full run, the scoped Stock / DocumentReview / DocumentPackagePortal / SubscriptionRules selection passed all 102 cases with SQL enabled, including 26 otherwise-skipped SQL cases. The scoped setup/permissions selection passed 21 cases. These selections are subsets, not additional unique-test counts.

## Checks against real local workspace 108

| Check | Observed result |
|---|---|
| Project-specific RVD motor/pump orders | PO lines 16031/16037 retain four/six included requirements with exact definition/source-version provenance; additions/exclusions match frozen MRs |
| Later project edit | Adding the motor pressure-test requirement after MR creation does not alter its frozen PO checklist |
| Repeated synchronization | No duplicate PO requirements; temporary uploaded-file pointer preserved; probe rolled back |
| Missing exclusion reason | MR creation refused; no partially created records remain after rollback |
| Fresh MR → PO | Compact MR snapshot without redundant upload placeholders; repeated PO copy still six rows; rollback restores entity count |
| Foreign copied baseline | New project position correctly receives its four defaults despite another position's copied snapshot metadata |
| Original procurement handover | Positions 15966/15967 and MRs 15984/15985 remain Ordered; article pins 21387/21369 preserved; no duplicate MR |
| Original supplier terms | PO-2026.0001/.0002 retain prices 299/825 EUR and original offer versions; changing the motor catalogue offer did not refresh the agreement |
| Supplier RVD upload persistence | On a real motor PO RVD placeholder, repository upload creates a new history version and changes Required to Submitted; identical stored-file ID creates no additional version |
| Supplier upload state rename | **Confirmed defect:** renaming Submitted to Vendor Evidence Received inside the transaction lets upload succeed but leaves the RVD Required |
| Upload event identity | New version emits entity.version.created from repository. No separate lifecycle-transition event observed for the automatic submission-state change |

Supplier upload probes reuse an existing valid stored-file reference and roll back; they test database persistence/history, not a new binary upload or supplier authentication. They do not certify supplier-facing UI, Quality permissions or delivered notifications. Existing portal access/review tests provide separate regression evidence, not full supplier scenario sign-off.

## What stops the remaining workflow

1. **P0-07: rename-safe submission and a reliable submission event.** The upload query chooses LS_Name='Submitted'. Replace that business-name dependency with an administrator-configured state identity and validate before saving. Example: after the admin calls the state Vendor Evidence Received, the next upload must still enter it or fail before saving. State-only subscribers currently cannot rely on a lifecycle event for this automatic state update; a version subscription is a different choice.
2. **P0-08: PO-line documentation result.** The live workspace has no documentation-complete/status property configuration. Acceptance states and generic permission tools exist, but they do not prove that all effective requirements have accepted files for the current versions. Quality review, authorised reopening and invalidation remain to be implemented/configured and tested together.
3. **P0-09: automatic department subscriptions.** Local automations 46–55 cover supplier timestamp, BOM routing/orders, quotation/project, transmittal PDF, client comment actions and supplier terms. No Sales/Quality auto-subscription example is installed for these purchase orders. Existing client-review notification tests pass; they do not prove that a motor supplier upload notifies Quality or the PO-line watchers.
4. **P0-10: shared Payment Milestone category/tabs.** The workspace contains the planning Milestone category 4529 and Payment Terms property 8001, but no Payment Milestone category. Planning milestones are not the agreed 30/50/20 payment schedule. No real invoice or payment has been marked due/paid by these tests.
5. **P0-11: completion/payment eligibility.** Without the authoritative documentation result and payment milestone setup, there is no installed end-to-end script proving accepted delivery plus accepted documentation permits final completion/payment. Do not treat Received or Submitted as that approval.
6. **Wrong fitting / replacement loop.** No matching Qcall/Non-conformity category was found in this workspace audit. A later receipt and replacement-order scenario needs that category/assignment/subscription configuration before it can be tested as the intended story.

Existing state and transition permissions should remain the mechanism for supplier read-only Accepted documents and Quality rights. They have not been certified by an administrator account impersonating a supplier. A separate supplier-role test remains necessary after the submission/event configuration is fixed. Do not introduce a new locking engine.

## Planning regression nuance

The old --verify-motor-delay helper failed its fixture-specific expectation of published target 22 January / achievable 29 January. Inspection showed published run 10 is the later, on-time procurement scenario: target and achievable both 22 January, delay zero; motor/pump finish 21 January and have on-order coverage. The old helper expects the earlier one-week-late simulation. This is a stale scenario precondition, not evidence that the delay calculator itself failed. No dates were changed to force that old assertion to pass. A fresh one-week-late real-PO scenario remains unexecuted in this continuation. The helper also reruns the existing target-date migration before asserting; it performs no PO/date edits.

## Next implementation order

First fix P0-07 using configurable state IDs and transactional submission events, then test real supplier upload and replacement. Configure/test Accepted permissions and build the P0-08 result. Add parameterised subscription scripts against those events. Add the shared Payment Milestone category and schedule scripts, then the technical-completion/eligibility script. Finally rehearse both arrival orders (goods first/documents first), rejected/missing/replaced evidence, Quality reopen, late motor, and the wrong-fitting replacement loop. Each setting remains editable through workspace configuration UI.

No implementation of these remaining features is claimed or authorised by this test report.

## Follow-up — 16 November 2026

The confirmed upload rename/notification gaps are fixed in the subsequent [RVD upload implementation and test](engineering-project-rvd-upload-test.md). The findings above remain the historical pre-fix test record.
