# Production readiness — local verification, 16 November 2026

Scope authorized for this implementation: operation-specific blockers/readiness, ready/lost-ready notifications and scheduled start-date warnings. Later NCR/replacement and manufacturing/handover are not included. No internet deployment.

## Configuration and installation

The focused Production Readiness setup was installed through local Manage in workspace 108 (Engineering Workflow Test). It created Production Prerequisite category 4578, property mappings 8199–8205 plus the existing MR Required Qty property 8090, the owned Prerequisites Uses tab and three editable automation scripts. Evaluator automation 64 maps Production Order 4555 and Material Requirement Line 4544. Active states are Draft/Released; completed state is configured separately. No order lifecycle is changed by readiness.

The automation parameter JSON was edited and saved through Configuration, then reread from SQL. New repeat-warning and blocker-message parameters were present. Setup guards and tests preserve installed mappings, labels, definitions and consent rules. Fresh setup seeds boolean defaults false; earlier installed records with null hold values are treated as no hold. Existing administrator defaults are not overwritten.

## Fixtures and evidence

Original project 15858 and production orders 15953/15954 were retained. Separate local readiness fixtures were created under the existing RVD test project 16002:

| Record | ID | Scope |
|---|---|---|
| ITM-00006 header order | 16118 | Fitting MR prerequisite |
| ITM-00000 final assembly order | 16119 | Completed header, motor MR 16009 and pump MR 16022 |
| Header / system planning activity | 16120 / 16121 | Original start 22 January 2027, 08:00 local |
| Fitting MR | 16122 | Three tees of item 15730 |
| Prerequisite rows | 16123–16126 | Owned ordinary occurrences |

These are intentionally limited test prerequisites. They do not prove the complete BOM, usable production instructions or real Vault release.

The native evaluator and StockMovementService were tested against these records. Missing/partial reservations block readiness (partial also has isolated SQL-test coverage). Three synthetic on-hand/reserved fittings made the header ready independently while final assembly remained blocked. Holds withdrew readiness. In a rolled-back transaction, reserved motor/pump stock, a completed header and accepted current frozen RVD evidence made final assembly ready. Removing a current RVD file withdrew readiness again. All temporary document and lifecycle changes rolled back.

The browser saved prerequisite hold values, created order version 2 and reread the saved rows. New opened the configured Production Prerequisite category picker; existing grid editing/Choose columns/Open details remain available. The final build shows missing forecast/delay as Not set.

## Real scheduler and notification delivery

For this test only, two activity dates were temporarily set to the current local date and the current test account 360 manually subscribed in-app only. No other users or email recipients were added. Job 21 evaluated the saved orders; normal event consumer/delivery jobs 6/9 delivered:

- Header: Production start date reached, including its missing fittings.
- Final assembly: Production start date reached, including header, motor/pump and RVD blockers.
- Header: Ready to start after reserving the synthetic fittings.
- Header: Not ready to start after reversing that reservation.

Repeated unchanged checks produced no duplicate readiness/start messages. The rebuilt worker later succeeded with both restored orders blocked and no job error. Read access to evidence is checked during evaluation, hit routing and delivery; generic personal subscription consent is reused for the seeded future transition proposals. This live test used a manual current-account watch, not a separate Production-user consent walkthrough.

## Restoration

StockMovementService posted the exact -3 on-hand/-3 reserved compensation; the stock ledger retains both clearly labelled test movements and net balance is restored. Original activity dates/constraints were restored. Both orders remain Draft and blocked; motor/pump required documents remain unaccepted and actual receipts were not posted. Only the two LOCAL READINESS TEST watches were cancelled through the subscription repository. Notification history and the test records remain for inspection.

## Checks

- API regression with isolated local SQL tests enabled: **1,398 passed, 52 skipped**. Skipped suites are not claimed as verified.
- Manage tests: **309 passed**.
- API and browser application builds: zero errors/warnings; Manage build succeeded.
- Strict documentation build passed; git diff whitespace checks passed in the four repositories.
- Isolated tests cover stock shortage/allocation, defaults/holds, same-project checks, predecessor completion, target versus five working days' delay, timezone boundary, per-date dedup/repeats, script failure, trusted event source and evidence-read/in-app delivery restrictions.

The earlier motor-one-week-late planning scenario was not repeated end to end on the original project's POs in this readiness run. The readiness forecast display reads the existing latest published plan and does not recalculate or move targets. Full BOM/resources, separate-role permissions and NCR/quarantine integration remain practical follow-up work.

See the [public walkthrough and screenshots](../docs/workflows/engineering-production-readiness.md).
