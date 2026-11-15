# Project procurement lifecycle handover — 15 November 2026

## Cause

The seed created Engineering Ready → Procurement Lifecycle, but the normal source state at MR ordering is Release to Procurement. The motor and pump test fixtures also remained Work in Progress because the preceding MR date test had directly called the MR script.

## Changes

- Added a guarded setup composition for the engineering BOM category. It seeds missing procurement release-state switches alongside Engineering Ready, preserving existing switch flags and permissions. Its marker is category-specific. Missing release states or an unconfirmed switch creation fail before installation is marked complete.
- Existing switch creation helper accepts preserveExisting for this feature. Other callers retain their previous behavior.
- MR transition automation remains switch_source_lifecycle with administrator-managed TargetLifecycle and TargetState. Category switch permissions/criteria remain configurable in the existing UI.
- Fixed the dispatcher retry: when the source version already has the requested lifecycle and state, no self-transition is attempted. Different states/lifecycles still go through the existing lifecycle services.

## Local evidence

Workspace 108, project 15858, BOM category 4531. Added switch 88 from Engineering Lifecycle assignment 4654 / Release to Procurement state 6064 to Procurement Lifecycle 1362. Existing switches 85 and 86 were retained. Local adoption used repositories, without rerunning unrelated setup.

Normal EntityLifecycleService calls moved project positions through Engineering Ready and Release to Procurement, then retested each existing MR Requested → Ordered. Release first correctly failed because the pinned Library versions were not final. The test reviewed/released the exact pinned versions through For Review → Approved → Released using EntityLifecycleService. Latest Library versions were also reviewed/released during fixture preparation. No definition links were repinned and no PO commercial values were changed.

| Position | Item | Final lifecycle/state | MR | Final MR state | Preserved PO item version |
| --- | --- | --- | --- | --- | --- |
| 15966 | ITM-00021 | 1362 / Ordered | 15984 | Ordered | 21387 |
| 15967 | ITM-00012 | 1362 / Ordered | 15985 | Ordered | 21369 |

First handovers succeeded without errors. Repeat testing exposed an attempted Ordered → Ordered self-transition; after the dispatcher fix, both repeats succeeded without errors. Assertions confirm one MR per source, original PO prices EUR 299 / EUR 825, supplier-offer pins 21710 / 21370, and original ordered item-version pins.

Existing POs were retained. No new supplier sharing, email, receipt, payment or internet deployment occurred. The local harness has --adopt-bom-switch, --test-bom-handover and --verify-bom-handover commands.

## Validation

- Four setup tests: correct release-state seeding, preserve a disabled existing switch, installed-marker preservation, missing release state, and unconfirmed creation (some assertions share a test).
- Three dispatcher cases: already at target, same lifecycle/different state, and lifecycle switch followed by state change.
- Manage/Application builds and local lifecycle-service/SQL checks.
- Browser verification of category switch settings and permissions, and both project positions in Procurement Lifecycle / Ordered (screenshots 143–144).
- Strict Zensical documentation build passed.

The broader supplier/RVD/payment workflow is not implied complete by this test.
