# Wrong-fitting replacement: local test evidence

Tested locally on 16 November 2026 in workspace 108 (Engineering Workflow Test), using the separate readiness/RVD test project 16002. No internet deployment, supplier invitation, payment or manufacturing was performed.

## Implemented and exercised

- Focused Manage Setup installs the category, project tab, lifecycle, scripts, task assignment, status editor, subscription proposals and configurable mappings. Reinstallation preserves installed settings.
- NCR 16131 references original MR 16122, original PO line 16129 and held production prerequisite 16123. The normal request transition created replacement MR 16132 and a Work Preparation task. Repeating creation returns the same MR. Original BOM quantity and frozen RVD provenance were preserved.
- The existing MR-to-PO service created replacement PO 16133 (PO-2026.0006), line 16134. Administrator-configurable MR source handover skips the NCR when its category does not support the procurement lifecycle.
- The normal receipt service received one fitting. The scheduled worker changed the NCR to Arrived. Inspection, accepted RVD, allocation against original MR 16122 and pickup instructions then allowed Ready for Pickup.
- A real in-app-only subscription delivered Arrived and Ready for Pickup notices to the local test account. Receipt correction later delivered Awaiting Replacement too. No email was sent. The temporary watch was cancelled after delivery; personal consent preferences were untouched.
- Native tests rejected stale requests, an unheld prerequisite and excessive quantity. Pickup was blocked before evidence was complete. Transactional tests rejected partial arrival, withdrew pickup for a missing RVD, and exercised authorised Collected/Closed transitions without committing collection or issuing stock.
- The browser verified linked records, editable defect/disposition fields, live pickup facts, notifications and the ordinary automation editor. Screenshots are in the [workflow guide](../docs/workflows/engineering-fitting-replacement.md).

## Restoration and limits

Temporary receipt, stock adjustments and reservations were reversed through stock movements. Synthetic document acceptance, inspection and pickup instructions were restored with audit history retained. NCR 16131 is Awaiting Replacement, replacement MR 16132 is Ordered, and PO-2026.0006 is Issued. The production prerequisite remains held. Test notifications and version history remain as evidence.

The helper used the real PO creation/receipt services but did not register the print-PDF generator for issuing the fixture PO; the PDF side automation reported that missing helper dependency. This test does not validate supplier sharing or PO printing again.

Separate team identities, segregation/quarantine, supplier return or credit, physical collection and actual production still need a practical rehearsal. The feature leaves these decisions to authorised teams. It never automatically clears the production hold or pays a supplier.
