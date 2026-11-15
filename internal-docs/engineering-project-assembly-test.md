# Local workflow test: add ITM-00000

Latest follow-up: [quantity-unit implementation and retest](engineering-project-quantity-unit-test.md) corrected the original assembly to 1 pc and added administrator-editable insertion defaults and engineer review. Blank-unit findings below describe the earlier test stage.

Date: 14 November 2026. Local only; no online deployment. The initial test made no application changes; the category-rule repair below was subsequently implemented at the user's request.

Workspace Engineering Workflow Test (`cwt3qp`, 108). Project Lube Oil Skid Project (15858), created from the original accepted sample quotation. Used the current owner's authenticated session at localhost:7053.

## Actions and evidence

Selected the project folder, clicked Explorer Library, searched ITM-00000, selected Assembly rather than Drawing ITM-00000.pdf, and clicked Add Selected once. Closed the still-open picker using Cancel after insertion. The project had one top-level row. New occurrence ID 15861 links to definition 15726. Property browser confirmed quantity 1, Is Occurrence Yes and the correct project folder path.

Expanded the occurrence and the ITM-00006 header. Used Columns to add Description, Pos Nr, Quantity, UoQ Symbol and Definition Revision; subsequently hid Version, Category, State and Definition Revision for a readable screenshot. The definition revision was 0 on the top occurrence before hiding that column.

The system displayed ITM-00006 through ITM-00022 BOM positions, ITM-00000.pdf, three RVD rows and My Company BV supply source. Header displayed ITM-00001 through ITM-00005 with quantities 3 pc, 2 pc, 1 pc, 2 pc and 496 mm, plus ITM-00006.pdf, three RVD rows, supply source and eight welds. Pump and motor each showed 1 pc, hose 1135 mm, tubing 1673 mm.

Screenshots and user instructions: [Add the assembly](../docs/workflows/engineering-add-assembly.md).

## Gap exposed

Top insertion used Category Base, no lifecycle/state, quantity 1 and blank unit. Details editor showed only General with no category-specific properties. This is not a production-ready occurrence configuration.

Source corroboration: EntityService.InsertFromLibrary.cs calls CreateOccurrenceFromDefinitionAsync without category override; assignment resolver gets definition metadata and ParentCategoryId, unresolved assignment falls back to category 0/Base. It links the definition and synchronises occurrence-owned RVD placeholders. No category rules, settings or application implementation were changed during this test.

Follow-up: determine the intended workspace-admin-configurable occurrence assignment for an Assembly inserted under a Project, lifecycle/properties and unit default/editor. Validate independent project child materialisation, version reference behavior and permissions before demand/release. Expanded UI rows alone do not establish child ownership or snapshot behavior. Any missing administrator UI is part of the required fix; do not solve it with a hidden code-only business default.

PDF contents, client transmittal, purchasing, release and ordinary engineer permissions were not tested here.

## Implemented category rule and local retest

Added SourceCategoryId to insertion rule input. A shared criteria builder stores ParentCategoryId equals Project AND an OR group containing the four selected source IDs. No category labels or record numbers are used for matching. The UI recognises that shape and presents parent selection and source checkboxes; unrelated rules retain their existing query builder. Rule name, target, enabled state and ordering remain editable. The rule-name input now commits while typing so a new name is not lost on blur.

Engineering configuration seeds Project articles → BOM Position with the resolved Assembly, Weld Assembly, Item and Part IDs, targeting the resolved BOM Position. An installation marker preserves edits, disabling, renaming or deleting the seeded rule on reruns. The local rule was created and reopened through the UI; the Engineering Project Configuration seed then completed, retaining one rule and marking installation.

The Base repair exposed a category-dialog defect: it requested category 0 and failed. The dialog now skips that lookup and uses the entity's occurrence family to offer valid target categories. Used Change Category on the original occurrence 15861, then Change State to assign Engineering Lifecycle / Work in Progress. Lifecycle assignment created version 2. Definition 15726, quantity 1 and ownership under project 15858 remain. The editor displays occurrence fields, inherited definition properties and Documents/Spare Parts/Planning/RVD tabs.

Fresh UI insertions under the separate Q-2026.0001 project each produced BOM Position / Engineering Lifecycle / Work in Progress: ITM-00000 (Assembly), ITM-00006 (Weld Assembly), ITM-00001 (Part). Item has no sample definition; tested by the rule evaluator. The original project still has one system occurrence, and expanding it showed the system BOM, drawing and RVD rows intact. Units remain blank on new top-level insertions; no unit policy was implemented or hardcoded.

Validation: API, UI and Manage builds succeeded. Eight rule tests passed (all four sources, different parent, non-selected source, missing source, editor roundtrip); one seed test passed including preserved disabled/customised criteria and deletion. Existing Manage test compilation emits unrelated BL0005 warnings. Public screenshots 11–13 document the rule, repaired original and fresh insertion.
