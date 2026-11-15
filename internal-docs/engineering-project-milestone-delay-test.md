# Milestone target versus achievable date — local test

15 November 2026. Local workspace 108 (`cwt3qp`), project 15858. No internet deployment, supplier sharing or emails.

## Change

Finish-target planning keeps the target as the backward-pass requirement, but no longer forces the forward achievable start earlier than a prerequisite allows. Negative float consequently selects the feasible later dates. Dependency validation remains enabled. Published results store TargetFinish (SQL date) and TargetDelayDays (SQL int), independently of baseline drift and overall project delay. Historical pre-change rows remain null until recalculated; existing history is not rewritten.

The Schedule view displays a target/achievable/delay comparison with a highlighted delayed row. The existing MilestoneFixedDate bool property still determines whether DueUTC is used as a target, and publishing still preserves that entered DueUTC. No new business-policy setting or date-copy script was needed. Existing admin UI controls defaults, category assignments, picker, lead/handling definitions, calendars and permissions.

Repeatable migration: `20261115_planning_target_dates.sql`; two nullable columns, no identifier conversion.

## Fixture

Existing milestone 15965 (22 Jan 2027); motor 15966 (definition 15768, handling 2 working days); pump 15967 (definition 15750). Both point to the milestone. Project deadline remains 29 Jan.

Local test PO 15978, LOCAL TEST - Motor delivery delay, and line 15979, LOCAL TEST - Motor ITM-00021 delivery, were created with the ordinary occurrence repository. A clearly labelled synthetic +1 on-order ledger entry supplies the test motor. This is a planning fixture, not an end-to-end purchase approval, order sending, or receiving test. No warehouse receipt was posted. The ordinary PO-line PromisedDelivery property supplies the real planning allocation/repository pipeline.

## Results

- Normal PlanningService calculation with motor promise 19 Jan: milestone target/achievable 22 Jan, delay 0; motor ready 21 Jan. Published run 6.
- Browser changed PO line Promised Delivery to 26 Jan and saved version 2. This is exactly one calendar week later.
- Local PlanningService and browser recalculation: motor arrival 26 Jan, ready 28 Jan; milestone target 22 Jan, achievable 29 Jan, delay 5 working days. Supplier coverage PromiseLate and 5-working-day warning. Pump arrival 7 Dec, ready/need-by 21 Jan, order-before 30 Dec unchanged.
- Milestone DueUTC remains exactly `2027-01-22T22:59:59`; scheduled forecast is stored separately. Closing/reopening the browser dialog displays the same delayed target comparison. Screenshots 129–132 added to the workflow.
- Local test workspace is deliberately left showing the delayed scenario for review.
- Final persisted-read verification passed on run 8. Earlier on-time run 6 still has delay 0. Reapplying the migration succeeded. Strict Zensical documentation build passed with no issues.
- Planning tests: 35 passed (new tests cover on-time, one-week-late, downstream dependency feasibility, target retention and unchanged pump/need-by dates). API and WASM builds passed without warnings/errors. Core API was not restarted; reference builds were used to avoid changing its running files.

Scope: this project currently has only the two supplies and assembly-start milestone in its published network. No claim is made that full manufacturing duration was tested. MR date backfill remains separate.
