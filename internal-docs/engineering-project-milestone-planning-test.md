# Milestone planning — local workflow test

Date: 15 November 2026. Local services only. Workspace 108 (`cwt3qp`), project 15858, Lube Oil Skid Project. No internet deployment or supplier order was performed.

## Implemented

- Ordinary bool property `MilestoneFixedDate`, seeded label Fixed Milestone Date, initial true, assigned to Milestone. Administrator can change initial/category defaults, label, description and assignment in existing configuration UI. Explicit record false overrides defaults.
- Fixed milestones use TaskPlan DueUTC converted to the workspace calendar date as MustFinishOn input. Type remains Milestone with zero duration. Publishing preserves the entered DueUTC. Missing fixed due date prevents calculation with a diagnostic.
- Existing Planning display tab assigned to Milestone so dates can be edited after creation. Category tab assignment remains administrator configurable.
- RequiredByTask picker includes Task and Milestone and searches Occurrences (family 10), rather than Library definitions. Other picker options/category selections are retained at initial feature installation.
- `Setup.MilestonePlanning.v1` prevents test reruns from restoring deliberately removed assignments or overwriting administrator choices.
- Planning tree reads current/unversioned parent placements and excludes removed inherited placements. Local historical placement duplicates had prevented correct calculation. Absent supplier promise now stays null, instead of becoming DateOnly.MinValue.

No redundant date-copy script was seeded: native calculation must use the milestone on every calculation. Scripts can set the ordinary policy/reference properties using the existing scripting APIs.

## Local records and results

Metadata was explicitly adopted into the existing test workspace; the full Setup wizard was not rerun. Property 8167 was created and assigned; RequiredByTask 8102 includes Task 4503 and Milestone 4529 with familyType 10. Existing Planning tab was assigned to Milestone 4529.

Milestone 15965, Skid Assembly Starts, due 22 January 2027, fixed true. Project completion remains 29 January 2027. Project-specific motor position 15966 (ITM-00021, definition 15768) and pump position 15967 (ITM-00012, definition 15750) were materialized using ProjectLineCopyService. Each is 1 pc, Supply Method Buy, RequiredByTask 15965. Motor lead/handling 20/2 working days; pump 15/1. No release transition or MR creation occurred for these positions.

| Test | Motor | Pump | Result |
| --- | --- | --- | --- |
| Milestone 22 Jan, normal PlanningService calculation | NeedBy 21 Jan; order before 22 Dec 2026 | NeedBy 21 Jan; order before 30 Dec 2026 | Pass, published run 2; DueUTC preserved exactly |
| Browser Recalculate plan | Same | Same | Pass, run 3 |
| Change milestone due date to 25 Jan using calendar, save, recalculate | NeedBy 22 Jan; order before 23 Dec | NeedBy 22 Jan; order before 31 Dec | Pass, run 4 |
| Restore start/due to 22 Jan, save, recalculate | NeedBy 21 Jan; order before 22 Dec | NeedBy 21 Jan; order before 30 Dec | Pass, run 5 |
| Required By Task search and select | Picker shows Task and Milestone; selected Skid Assembly Starts | Record displays the same milestone | Pass |

The existing task editor validates Due Date >= Planned Start. When moving the milestone earlier after a published plan, also adjust Planned Start; no bypass was added. The first restoration attempt correctly showed this validation; the saved record was restored after both fields were adjusted.

Browser screenshots 122–128 are included in `docs/workflows/engineering-milestone-planning.md`.

## Checks and remaining scope

- Planning test selection: 33 passed, no skipped tests.
- Milestone seed tests: 2 passed, including picker family/category/options preservation and no mutation after installed marker. Existing unrelated BL0005 component-test warnings remain.
- Strict Zensical documentation build passed with no issues.
- Local API calculation and browser calculation both passed. Current motor/pump/milestone each occur once in planning source; other shared historical/sample paths are not claimed to be globally deduplicated.
- The existing one-working-day material buffer is unchanged. Picker remains workspace-wide; same-project enforcement is not introduced.
- Previous assembly test MRs 15957/15961 were not backfilled. Carrying calculated NeedBy into When Required at MR creation remains a separate workflow step.
