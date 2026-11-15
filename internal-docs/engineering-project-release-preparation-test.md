# Project BOM release preparation â€” local verification

15 November 2026. Local workspace 108 / project 15858 only. No internet deployment or invitations.

## Implementation and configuration

- Repeatable migration adds LST_MaterializeAndPin bit default 0 to transitions. No new identifiers.
- DTO/controller map policy; CatalogLifecycleManage protects saves. The endpoint reloads existing metadata and only changes preparation policy.
- Lifecycle editor checkbox saves per edge; tested off, closed/reopened false, restored true.
- ProjectLineCopyService validates selected inherited identity and structural project ownership, using existing copy-on-write service. Shared Library rows without inherited context are rejected. Pin and state mutation share the existing state transaction. Final criteria run after pinning, so they evaluate that selected definition version.
- Copy creation retains the existing separate copy transaction. A later rejected state write may leave a safe independent position in its original state; the definition pin/state write roll back. Do not claim copy creation and state change are one atomic transaction.
- Existing stored order-creation script and configured procurement action are retained. No hard-coded item/state names in runtime policy.
- Seed has separate Setup.ProjectReleasePreparation.v1 guard. Fresh install prepares transitions into production, procurement, Ready for Procurement if present, and fulfilment. Subsequent setup leaves admin changes/removals alone.
- Existing local workspace adopted the new defaults explicitly through the diagnostic helper, not by rerunning the complete Setup wizard.

## Real local lifecycle-service test

Initial source: ITM-00000 project position 15861; inherited ITM-00006 source catalogue position 15777. Initial definition versions 21345/21347 were WIP. Direct production release after Engineering Ready was blocked by the existing Definition is Final criterion.

Sample definition transitions For Review, Approved, Released succeeded via real EntityLifecycleService; resulting definition versions are 21647 (ITM-00000) and 21644 (ITM-00006). No CAD/Vault release occurred; these are local simulated review decisions. Drawings were not released or replaced.

After definition version changes, the first inherited-row request was stale. Normal MaterializeExplorerChildren refresh fixed its cached source-placement reference; no checks were bypassed.

ITM-00006's move to Engineering Ready used the existing copy-on-write behavior and created project position 15952 under 15861. Release to Production then pinned definition 15728/version 21644 and created Draft order 15953, PROD-ITM-00006-15952, 1 pc. ITM-00000 release pinned definition 15726/version 21647 and created Draft order 15954, PROD-ITM-00000-15861, 1 pc. Orders capture source versions 21650/21652 respectively. Source properties map to the correct item and project position.

Repeated real Engineering Ready â†’ Release to Production transitions succeeded using the final code. Exactly one order remains per source. Final source versions are 21665 (15952) / 21667 (15861); order source pins remain at the original release versions. Definition pins did not move. Shared position 15777 remains version 1/21396, WIP, following definition 15728, with no pin. One live project-copy trace maps it to 15952.

## Procurement browser test and remaining prerequisite

Prepared disposable independent positions 15955 (ITM-00006) and 15956 (ITM-00000), quantity 1 pc, Engineering Ready. Real browser Change State applied Release to Procurement to both. Both succeeded and pinned versions 21644/21647. Final position versions 21659/21658. No MR source links were created.

API warnings explain why: create_material_requirement could not calculate project 15858 because Required Date is unset. The existing action returns after logging; the browser still reports state success. This is a separate planning prerequisite/observability limitation, not a successful MR test. No unrelated fix or date assumption was introduced.

## Verification scope

- SQL tests cover shared-row rejection, mismatched inherited selection, copy creation/reuse, permissions, ambiguity, tenant boundaries, pin stability and pin rollback after a later failure. Existing script tests cover quantity/unit, source pinning, concurrency/idempotency and rollback.
- Seed tests verify fresh production/procurement defaults and installed-setting preservation.
- Browser screenshots 111â€“115 show admin editing, procurement success, released actual positions and Draft order. Production transitions themselves ran through native application services/repositories, not browser clicks; browser inspected their persisted results.
- Test source positions/orders from the earlier diagnostic remain clearly labelled VALIDATION ONLY. They are separate from actual project orders 15953/15954.

Final verification: 22 targeted API/SQL tests and four seed tests passed. Application and client builds passed; strict docs build passed. Existing Manage test component-analyzer warnings remain unrelated to this feature. Pin resolution/writes now consider current or unversioned definition links, leaving older version-scoped links unchanged; SQL rollback/history assertions cover this.

## Required Date and MR retest — 15 November 2026

No application code or workspace default changes. Browser project Details → General saved Required Date 2027-01-29; native audit confirms project version 2 / EntityVer 21668. This is a test record date, not a seeded policy.

The first re-release of source 15956 exposed missing dbo.tbl_PlanSupplyAllocation. Inspected and applied existing 20260919_planning_remediation.sql locally inside a transaction. It creates missing planning tables with int identifiers and preserves existing tables/settings. No new migration was authored. A fresh Engineering Ready → Release to Procurement event through the browser created MR 15957 / EntityVer 21674 for ITM-00000.

Source 15955's first browser attempt then hit SQL deadlock victim process 73. The state had changed; the dialog explicitly reported the failed create_material_requirement automation. Captured screenshot 118. Reset only this disposable source to Engineering Ready through the native lifecycle service, then repeated the real browser release. Retry succeeded without a warning and created MR 15961 / EntityVer 21681 for ITM-00006.

Both MRs are Requested, Required Qty 1 and placement quantity 1, unit ID 1 (Piece/pc). RequirementSource links identify test positions 15956 and 15955. Definition links on each source and MR are pinned: ITM-00000 → definition 15726/version 21647; ITM-00006 → definition 15728/version 21644. Exactly one MR per test source. No PO, invitation email or production start was performed.

Native property audit finds RequiredQty but no stored When Required date on either MR; buyer's UI confirms blank When Required. This remains a planning-date validation gap. Do not claim delivery-date propagation was verified or force the project date onto requirements without scheduling evidence.

Browser screenshots 116–121 record the date entry, successful releases, deadlock message, both MRs and detailed quantity/unit. Date typed through automation did not commit the widget; calendar selection followed by Save and Close did. Actual persisted date was verified separately. Local diagnostic commands: --procurement-date-audit and --mr-verify in tmp/distribution-channel-local. No application test rerun required because no application code changed; strict documentation build and scoped whitespace checks cover the docs changes.
