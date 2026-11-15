# Local workflow test: quantity units on Library insertion

14 November 2026. Local Engineering Workflow Test workspace (`cwt3qp`, 108); no online deployment.

## Implementation

Library insertion policy is a shared workspace setting (`LibraryInsertion.Settings.v1`), edited in System Settings. PreferStockUnit, FallbackUnitId, ReviewRequired and UnitRequired are saved independently of the existing Default Stock Unit. Engineering Project Configuration seeds assigned Stock Unit first, fallback pc, review required and unit required, using the actual workspace unit ID. A transaction and shared-key lock seed only an absent policy; administrator edits, No fallback and disabled flags persist on rerun. No schema migration or new identifiers were needed.

Stock Unit resolution reads the explicitly stored integer property value on the exact definition version, identified by its system key and subject to property visibility. The entity detail's evaluated initial value is deliberately not mistaken for an assigned Stock Unit. Available units are scoped to the workspace, including availability overrides for global units. A disabled Stock Unit falls through to an available fallback; a disabled fallback yields no suggestion. Required-unit validation then requires the engineer to select an available unit.

Explorer shows Review quantity and unit before Library insertion when configured. Quantity/unit/review are sent explicitly; the API enforces positive quantity, required review, required unit and workspace unit availability before creation. A shared transaction encloses occurrence creation, links, routing/RVD setup and placement quantity/unit storage. Missing post-create detail throws so the transaction rolls back. Generic specialised stock/order/task records are excluded from the ordinary occurrence repair action.

Actions → Quantity and unit uses the same review UI to repair an ordinary occurrence. The API checks occurrence and parent modification permissions, locks and validates its single workspace-owned structural placement, updates that relationship and queues planning recalculation. The Library definition's Stock Unit and child BOM units are not written.

## Local evidence

Ran the real seed service against localhost / MatStream only, after checking root entity 15652 belonged to workspace 108 and was named Engineering Workflow Test. It resolved pc to unit 1 and saved PreferStockUnit=true, FallbackUnitId=1, ReviewRequired=true, UnitRequired=true. Repeated seed runs preserved those values. The seed implementation is wired into Engineering Project Configuration; a full Setup rerun was not needed for this unit test.

The real ITM-00000 definition (15726) has no explicitly stored Stock Unit. Its service preview returned Quantity=1, UnitId=1 and UnitSource=Workspace fallback. After local sign-in, the owner opened System Settings and saved the visible policy successfully.

On original project occurrence 15861 in Lube Oil Skid Project (15858), Actions → Quantity and unit suggested pc. Clearing the unit displayed A quantity unit is required and disabled confirmation. Selecting pc and confirming updated the existing placement to 1 pc. Explorer Refresh retained it. Expanding the original assembly showed its drawing, RVD rows and BOM intact: header 1 pc, hose 1135 mm, tubing 1673 mm, pump and motor each 1 pc. No replacement occurrence was created in the main project.

In the separate Q-2026.0001 test project, a fresh ITM-00000 insertion paused in review with Quantity=1 and pc from Workspace fallback. Confirming created a BOM Position in Engineering Lifecycle / Work in Progress with 1 pc. Earlier test occurrences retained their blank units, demonstrating that defaults do not backfill old placements. The extra test insertion is isolated from the main workflow project.

Screenshots 14–17 and instructions are in [the assembly workflow](../docs/workflows/engineering-add-assembly.md). Non-owner Engineering permissions remain a separate workflow test.

## Validation

API, Manage and UI compiled successfully. All 21 selected automated tests passed, including the eight earlier category-rule tests and 13 insertion-unit tests (SQL test enabled against an isolated disposable database). Checks cover Stock Unit/fallback precedence, disabled/missing units, missing review, invalid quantities, engineer overrides, optional blank-unit policy, assigned-versus-offered Stock Unit resolution, persisted settings preservation, placement-only SQL updates and transaction rollback. Strict documentation build and diff checks are recorded with the completed change.
