# Calculated Need By → MR When Required — local test

15 November 2026. Local workspace 108 (`cwt3qp`), Lube Oil Skid Project 15858. No online deployment, supplier message, payment or receipt.

## Implementation

- Extend `matstream.actions.createMaterialRequirement(categoryId, datePropertyId)` and its queued action with an optional int date-property ID. The one-argument form remains compatible.
- Validate the configured destination is an enabled DateTime property assigned to the MR category in the current workspace before writing.
- Recalculate/verify the owning project plan, then read the current source position's published Need By scoped to that project. A configured mapped action reports planning failure/missing dates rather than creating a new undated MR.
- Write the mapped property in the same serializable transaction as MR creation. Reuse an existing open MR; fill only a blank mapped field, preserving a populated buyer date. Keep ordered MRs unchanged, uncovered-quantity checks and definition/version pins.
- Seed BOM: Create Material Requirement, with administrator-editable MRCategoryId and WhenRequiredPropertyId; replace the unchanged native seeded procurement-release assignment. Guard `Setup.MrPlanningDate.v1.{lifecycleId}` preserves subsequent administrator customizations/removals. A customized legacy native assignment is not overwritten; setup reports the need for review.
- Seed WhenRequiredPropertyId on the shared BOM supplier-routing script's Purchase branch. Existing one-argument scripts continue to work. Update the scripting editor's API reference/example.

No database migration or new identifier type. The ordinary Automations parameters editor and lifecycle automation assignments provide administrator configuration.

## Local fixture and adoption

Source positions: motor 15966 (ITM-00021) and pump 15967 (ITM-00012), each 1 pc. Milestone 15965 remains targeted at 22 January 2027. Calculated Need By is 21 January for each.

The previous delayed-delivery fixture had a synthetic +1 on-order motor on test PO line 15979. A labelled -1 compensating ledger entry, LOCAL SIMULATION REVERSAL - MR date test, reverses that fixture quantity without deleting its history. The test PO line is not an actual sent order. No receipt was posted. This allows the real planning allocator to identify uncovered motor demand again.

Local library automation 54 uses MR category 4544 and mapped When Required property 8091. The unchanged native procurement-release assignment was adopted; existing BOM supplier-routing/production scripts received the additional date mapping without replacing their other parameters. Setup marker saved. The full Setup wizard was not rerun.

Execute the library script through real ScriptAutomationService/ScriptEngine → FeatureAutomationDispatcher → PlanningService/SqlPlanningRepository → StockLineService. This test invokes the script action directly, rather than repeating the entire engineering state-transition review.

## Verified

| Source | MR created/reused | Final When Required |
| --- | --- | --- |
| 15966, motor ITM-00021 | 15984 | 21 January 2027 |
| 15967, pump ITM-00012 | 15985 | 21 January 2027 |

For each source:

1. Create the MR through the configured script; quantity 1 and calculated date present.
2. Set a test buyer date of 25 January; rerun and assert it remains 25 January.
3. Clear the date; rerun and assert it refills to 21 January.
4. Assert exactly one RequirementSource-linked MR exists.

Using Supply Method property 8098 as the destination fails with the enabled-date-property diagnostic before writes. No stock is posted by MR creation. The older assembly MRs 15957/15961 remain separate and undated.

Browser verification: Pending Material Requirements queue shows both dates, motor/pump detail forms show `2027-01-21`, and Configuration → Automations displays editable mapping JSON. Screenshots 133–136 are included in the workflow guide.

Checks: 45 API planning/script tests passed, no skips; one setup rerun-preservation test passed. API/Application, Manage and WASM builds passed with zero warnings/errors. The Manage unit-test compilation has existing unrelated BL0005 warnings. No Core API restart was needed.
