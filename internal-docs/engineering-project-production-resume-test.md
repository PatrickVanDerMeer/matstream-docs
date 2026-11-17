# Replacement collection and production restart — local evidence

This report records the earlier record-only walkthrough. Stock consumption and MR reservation release were subsequently implemented; see the [current collection test report](engineering-project-replacement-collection-test.md).

16 November 2026. Local workspace 108, test project 16002. Uses existing definitions and normal lifecycle/editing actions; no product code or seed changes were needed for these record actions.

## Native service test

`tmp/replacement-resume-test.log` records a transaction that was rolled back:

1. Current pinned replacement receipt, accepted synthetic RVD, inspection and instructions qualified for pickup. Three good tees were allocated to original MR 16122 (two existing good + one replacement).
2. NCR 16131 changed to Ready for Pickup and Collected through the normal lifecycle service. Production Order 16118 remained blocked by held prerequisite 16123.
3. With modification permission checked, the prerequisite hold was cleared; header readiness became true.
4. A separate order-level Production Hold made it false again. Clearing that hold restored readiness.
5. Native Draft → Released → In Progress transitions succeeded. Final assembly 16119 stayed blocked by its unfinished header and independent motor/pump/document prerequisites.
6. Toggling the prerequisite hold while the header was In Progress did not change its execution state. Collected → Closed succeeded. All temporary database changes, movements and events rolled back.

## Browser walkthrough

After the user signed in, browser tab 19 performed real local actions on the named fixtures:

- Change State recorded Ready for Pickup → Collected with an explicit LOCAL WORKFLOW SIMULATION comment.
- Header readiness still showed Production hold is active, with three tees reserved.
- The Prerequisites Uses grid cleared Prerequisite Hold and Save and Close persisted a new version.
- Reopened General showed Ready to start. Evaluate and publish saved the readiness result.
- Draft → Released → In Progress each reported 1 of 1 succeeded.
- Final assembly still showed the unfinished predecessor plus its own materials/documents as blockers.

New collection, hold-clear, ready, In Progress and restored-state screenshots and the practical steps are in [collect and resume production](../docs/workflows/engineering-resume-production.md).

## Restoration

`--restore-replacement-resume` returned NCR Collected → Arrived through its normal reverse transition, reversed the simulated receipt, test stock adjustments and reservation, and restored synthetic RVD/inspection evidence. Explicit annotated test-restoration versions returned the header to Draft and restored the fitting prerequisite hold. The evaluator returned the NCR to Awaiting Replacement. Both production orders were republished blocked. Browser proof confirms zero allocated fitting quantity and the restored hold. The production start, hold-clear and collection history remains.

No real collection, consumption, manufacturing, supplier communication, payment or internet deployment occurred. No new test notification subscription was created for this walkthrough.

## Limits and next work

Collected is an auditable handover state, not a production stock issue. There is currently no dedicated action to reduce physical stock and release its MR reservation atomically with traceable issued quantity. Production readiness presently requires reservation; it does not count already issued material as available. Do not substitute sales dispatch or a silent inventory adjustment. Assess that flow before completing warehouse-to-production execution.

Readiness remains advisory. Existing permitted transitions are not universally gated by readiness. A started order stays In Progress while a hold is applied/cleared; there is no new mandatory Paused state. Definitions, lifecycle/property permissions, scripts and active-state mappings remain administrator-configurable.

The repeated test receipt reported a financial-reconciliation warning because earlier manual test reversal movements have no reliable commitment price. Physical receipt succeeded, but this walkthrough makes no assertion about that fixture's financial valuation or payment eligibility. The fixture warning is retained rather than inventing historical prices.

Separate team identities, physical segregation/collection, actual manufacturing and a complete fresh-workspace rehearsal remain outstanding.

The repeat receipt on the restored fixture reported a missing reliable commitment price because the earlier test reversal had no valuation. Physical receipt/lifecycle succeeded; this run does not validate financial valuation. No historical price was invented or payment made.
