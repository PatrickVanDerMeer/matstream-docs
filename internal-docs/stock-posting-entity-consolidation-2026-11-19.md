# Stock posting entity consolidation — 19 November 2026

> Current scope: the six table replacements remain, while the extra action engine has been removed. Dedicated compatibility views/procedures are unnecessary. See [the retained architecture and validation](production-table-storage-consolidation-2026-11-19.md).


The first consolidation replaces `tbl_ProductionIssue`, `tbl_ProductionReceipt` and `tbl_ReplacementCollection`. Readiness, start notices, handover and subscription proposal storage remain unchanged.

## Storage and configuration

- Each posting is an ordinary occurrence entity, structurally placed beneath its production order or NCR. Exact source versions use existing pinned Generated From links.
- A configured read-only Memo property holds the protected context (`MatStream.StockPosting.v1`). It preserves order/prerequisite/NCR/MR/predecessor/source references, units and request identity. Migrated records additionally retain every original column under `Data.Legacy`, including old IDs, original timestamps and reversal flags.
- The stock ledger gains a nullable `SLG_ContextPropertyValueID` reference to that exact property value. Existing MR/order source references remain unchanged. A unique index prevents two ledger rows from claiming the same posting context. Database checks also reject mismatched workspace/source/item/revision/location, invalid movement direction or issue source combinations, and rewrites of a posted physical movement.
- `vw_ProductionIssue`, `vw_ProductionReceipt` and `vw_ReplacementCollection` provide read projections, not separate storage. Quantities, item pins and locations come from the linked physical ledger movement.
- **Configuration → Workspace Defaults → Stock transaction records** controls the occurrence category, assigned read-only Memo property and issue/receipt/collection name prefixes. Normal category/property editors control labels and descriptions. Settings are seeded once and never overwritten on reruns. New workspaces receive defaults when configuration is opened or a first posting is made. Changing mappings does not invalidate historical records.

## Safeguards

The existing permission, current-version, project, prerequisite, hold, unit, revision, physical-stock and reservation validations remain in the production/replacement services. Existing serializable transactions and order/MR/predecessor locks remain. Entity context, reservation release, ledger and stock-position changes commit together.

Request identities are transaction-locked per workspace/kind/key. A repeated issue returns its existing entity only when order, prerequisite, shelf and quantity match. Conflicting reuse fails. Receipt and collection retain their existing per-order/per-NCR retry semantics. Posted context cannot be changed or deleted through the property writer; the ledger foreign key protects its exact property value. Historical source references are pinned using existing links.

The full-output receipt action remains once per order. Partial receipts and a general reversal UI are not introduced by this storage change. Existing reversed collection records retain their reversal flag; historical compensating movements remain unchanged.

## Migration

Deploy `20261119_stock_posting_entities.sql` with API/background workers stopped and a database backup available. The script creates projections and generic recording/configuration procedures, then converts legacy records in one transaction. It matches existing movements by workspace, source, item/version, location, movement direction/type, quantity and actor, with a two-second timestamp tolerance for SQL rounding. Missing, ambiguous or reused matches abort conversion. Reconcile those cases explicitly; do not bypass the check or recreate stock movements.

Conversion never calls the stock movement writer or updates stock positions. It creates context entities, links the original ledger rows and verifies the projections before removing the three legacy tables. Every original row remains captured in the context. A rerun does not duplicate records or reset administrator settings. Historical journal IDs become `Data.Legacy` identifiers; operational receipt/issue identifiers now identify their evidence entities.

## Validation

An isolated backup copy of local `MatStream` converted **45 issues, four receipts and three collections**. All 52 contexts retained their original row snapshots. Stock positions and existing ledger fields compared unchanged. Migration rerun and a workspace purge rehearsal passed; the purge rehearsal was rolled back.

SQL regression tests cover production issue and receipt retries/concurrency, pinned revisions, permissions, stale versions, demand limits and rollback after a simulated posting failure. A dedicated migration test checks all three histories, ambiguous-match rollback, preserved reversals, reruns, administrator label preservation and protection of posted context. Broader stock/Sales Order/purchasing regression results are recorded with the implementation validation.

The migration was then applied to local `MatStream` using the pre-migration COPY_ONLY backup successfully restored for the isolated rehearsal and checked with RESTORE VERIFYONLY. Post-migration checks found **zero changed stock positions, zero changes to existing ledger fields, zero legacy snapshot differences, 52 protected posting contexts and zero remaining legacy stock tables**. The ledger context index is unique. Verification output is in `tmp/stock-posting-local-verification.log`; successful backup verification is in `tmp/stock-posting-original-backup-verification.log`.

Final targeted regression run: **155 passed, zero failed, zero skipped**, with local SQL tests enabled. API and Web builds passed; Web retains its existing Microsoft.AspNetCore.WebUtilities advisory warning. Zensical documentation build passed without issues. Application services were not started for this change.

The final database guards were installed through a migration rerun. All 52 local posting contexts passed guard validation inside a rolled-back transaction; the row counts remain unchanged. See `tmp/stock-posting-local-guard-check.log`.

Follow-up: [handover, readiness and start-warning consolidation](workflow-evidence-consolidation-2026-11-19.md) subsequently replaced those three dedicated tables as well.
