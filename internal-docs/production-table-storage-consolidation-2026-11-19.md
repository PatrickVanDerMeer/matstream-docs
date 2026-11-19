# Production table storage consolidation — retained scope

The task is to replace the six newly introduced production/Engineering tables using storage MatStream already has. It does not replace the Inventory/Sales implementation or introduce a new configurable workflow engine.

## What remains

| Removed dedicated table | Retained storage |
|---|---|
| `tbl_ProductionIssue` | Ordinary stock transaction entity and protected context Memo; quantities and movements in the existing stock ledger. |
| `tbl_ProductionReceipt` | Ordinary stock transaction entity and protected context Memo; the existing stock position/ledger records the receipt. |
| `tbl_ReplacementCollection` | Ordinary stock transaction entity/context, original source/version links and existing ledger, including reversal evidence. |
| `tbl_ProjectHandover` | Protected evidence Memo on the exact issued package version, retaining share/client snapshot and original identifiers. |
| `tbl_ProductionReadiness` | Version-bound published readiness Memo, retaining original snapshots and publication ordering. |
| `tbl_ProductionStartNotice` | Durable shared workspace-setting checkpoints and execution claims, retaining warning occasions and repeat state. |

`vw_StockPostingContext` and `vw_WorkflowEvidence` read these existing stores. The six specialized views and four specialized persistence procedures are absent. Small typed application queries read the stored records; ordinary parameterized persistence runs on each workflow's existing transaction. The general-purpose projection-builder abstraction added during the larger experiment was removed as unnecessary.

Existing Engineering services, script helpers, lifecycle criteria, project/predecessor checks, document acceptance, completion and exact client acknowledgement remain the normal workflows. Their presence is intentional: replacing tables does not require eliminating production business logic. Inventory and Sales continue using their existing services and stock mechanisms. `tbl_SubscriptionProposal` is unrelated to these six workflows and remains unchanged.

Administrator mapping controls for stock transaction category/context/names and workflow evidence properties remain in Workspace Defaults. Category/property names and descriptions use the normal editors. Opening mapping settings creates no properties; setup preserves customizations. The structural-path correction remains necessary because source/version traceability links are also used by the retained table replacements: link types 4/9 and other references must not become Explorer parents.

## Removed extra implementation

Removed the category action executor, execution controller, shared action model, scheduler/job registration, script command and dispatcher, administrator editor and engine-specific tests. Normal automation dispatch is restored. There is no extra stock action framework.

Migration `20261119_zz_retire_category_action_engine.sql` disables worker type 23 and definitions carrying the obsolete `EntityAction` configuration or removed script helper. It preserves their parameter/script bytes, names, references, snapshots, ledger, balances and checkpoints. The runner explicitly refuses an obsolete definition rather than silently executing a different Script field. Movement values 12–15 are reserved for historical display/reservation accounting; there are no new writers for them. These retained read paths and any guards protecting existing immutable experimental history are data-preservation compatibility only.

A normal local database with no experimental history loses the three engine-only identity/version/link triggers. Existing property and stock-journal protections remain. No historical record is reclassified, renumbered, deleted or reversed to remove the engine.

## Migration order and preservation

1. `20261119_stock_posting_entities.sql`
2. `20261119_workflow_evidence_consolidation.sql`
3. `20261119_z_workflow_storage_cleanup.sql`
4. `20261119_zz_retire_category_action_engine.sql`

The third migration replaces the former experimental filename `20261119_z_generic_entity_actions.sql`; it retains only storage cleanup, required protections and path correction. Apply/rerun the final retirement migration after cleanup. Back up and verify the database first. No remote database is modified by local acceptance.

Preservation checks compare all property rows plus exact Memo bytes, complete ledger and position rows, settings/checkpoints and automation content before/after. Native automation enabled states must remain unchanged; only obsolete action definitions/job may be disabled. The isolated copy retained **14 experimental evidence records and 10 journal rows** unchanged after retirement. Local application and rerun both passed the same preservation assertions. The normal local database contains no experimental evidence or movement records; there is no enabled worker 23, and its three unnecessary engine-only guards are removed.

## Validation

- SQL-enabled regression: **181 passed, zero failures/skips**. Covers existing Sales stock/procurement workflows, Engineering issue/receipt/replacement/readiness/handover, migration preservation, scheduler session cleanup, normal automation claims and authorization inventory.
- The production receipt regression now applies final storage cleanup and retirement, reads the original posted receipt, posts another completed order through the existing service with the old SQL objects absent, verifies retry reuse and stock totals, and rejects deleting the protected journal.
- A regression proves an obsolete definition cannot fall back to its regular Script field.
- Public API acceptance uses the isolated Sales Order-only workspace 114 and Engineering Project workspace 115 seeded by their real templates. The current removal build is tested against those profiles, including preserved experimental history. These are not workspaces added to the normal local Core database.
- Sales API acceptance: **34 assertions passed**, replaying pricing, shortage procurement, receipt, pick/pack, generated PDFs, shipment, retries and physical stock deduction. Test-only opening adjustments and the new receipt retain the reusable fixture's starting/ending balances through normal endpoints; original journals are not rewritten.
- Engineering API acceptance passed: published blocked readiness, reads issue/receipt status and rejects incomplete output/final handover without client acknowledgement, with unchanged stock. Positive Engineering posting/acknowledgement/concurrency paths are covered by the SQL regressions; this is not a claim of a complete fresh manufactured-project-to-client handover UI replay.
- Removed action endpoint returns **HTTP 404**. The client and API build after removal. Documentation is rebuilt after removing the obsolete editor/API instructions.

Logs: `tmp/production-storage-regression.log`, `tmp/production-storage-focused.log`, `tmp/production-storage-copy-migration.log`, `tmp/production-storage-local-migration.log`, and `tmp/production-storage-acceptance/`. The public workflow guides describe the retained implementation. Temporary local API processes are stopped after acceptance.


Validation limits: an initial Engineering occurrence creation was a SQL deadlock victim while the Sales API replay was running concurrently. The transaction rolled back; the sequential Engineering replay passed, and existing SQL concurrency regressions passed. This storage cleanup does not include a general database concurrency redesign. Builds retain existing package/assembly-version warnings; dependency upgrades are outside this change.
