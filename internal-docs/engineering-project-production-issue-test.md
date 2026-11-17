# Production BOM/header issue — local verification

Date: 17 November 2026. Local SQL/services only; no physical production, internet deployment or external messaging is claimed.

## Implemented

- `ProductionIssueSettings`, editable stored script `Warehouse: Issue production materials`, script action `matstream.actions.issueProduction(requestJson)`, authenticated API and Production Order panel.
- Uses existing admin-configured ProductionReadiness category/property/hold/document/project mappings. Additional admin settings cover issue state IDs, partial quantities, Stock Unit/predecessor demand mappings, labels and note. Predecessor demand defaults to 1 on its category assignment; engineers can edit actual quantities. New Memo editor is selectable in the property editor catalogue.
- Purchased/stocked MR inputs use their exact pinned definition and matching enabled Stock Unit. Quantity comes from current MR demand; multiple current placements are accepted only when their units agree. Existing replacement issues retain operation-specific coverage and reduce remaining demand.
- Manufactured predecessors must be completed under the readiness policy and have a receipt. Their receipt pin supplies the stock identity; cumulative predecessor issues cannot exceed received quantity. These issues use only free shelf stock and do not release another demand's reservation.
- Internal Editor, Stock Manage, Stock Reserve and entity/evidence/location reads enforced natively. Issue state, current saved order, reviewed same-project prerequisite, holds/documents, positive four-decimal quantity, remaining demand, matching unit, MR reservation and available pinned physical stock rechecked in the serializable stock transaction.
- Consumed MR reservations, physical issue, ledger and int-ID journal commit together. Partial issues retain the remainder. Request GUID is correlation only; a unique workspace/request key and locks protect retries/concurrency. Automation attempts have separate run identities so fixing a failed request does not leave an unusable claim; the stock request identity is retained for safe retry. Script must have one write action; endpoint verifies that it actually journaled the selected request.
- Readiness counts general production issues alongside replacement collections and requires the issued material to belong to the same operation/prerequisite. Completion lifecycle transitions are not newly made dependent on material consumption.
- Repeatable `20261117_production_issue.sql`, purge ordering, focused **Production Material Issue** Setup template and Engineering Project inclusion. Installed-marker test preserves all existing administrator modifications on reruns.

## Automated checks

| Check | Result |
|---|---|
| API full regression with Timesheet/automation SQL opt-in | 1,428 passed; 52 skipped (not passing evidence) |
| Manage full regression | 313 passed; existing test-only BL0005 warnings |
| Final focused script/readiness/issue checks | 5 passed |
| Expanded issue SQL check after adding both concurrency scenarios/unit/partial-policy protection | Passed |
| API, Manage, WebAssembly application builds | Passed, no warnings/errors in application builds |
| Strict MkDocs build | Passed; existing two unlisted navigation pages remain informational |

Isolated SQL tests apply the real issue migration twice and use real stock repositories. They cover missing allocation, hold, foreign prerequisite, stale request, denied Stock Manage/Reserve, unreadable shelf, excess quantity, unit mismatch, disabled partial policy, advanced Library revision, changed consumed-demand pin, recorded predecessor receipt unit, partial consumption, residual reservation, operation readiness after issue, and a journal-insert failure after stock writes (both stock and reservation restored). Two independent database contexts test the same request identity and distinct competing requests for the last demand. A predecessor blocks without receipt and cannot consume shelf stock reserved for other work.

Script test verifies request forwarding and a company-specific rejection before queuing a write. Manage preservation test verifies an installed feature returns before attempting configuration writes.

## Native engineering workflow rehearsal

Workspace 108 **Engineering Workflow Test**, actor 360, dedicated project 16002. Reused orders 16118 (ITM-00006) and 16119 (ITM-00000), with clearly synthetic current accepted inspections/materials/supplier-document evidence inside an outer rollback transaction. The manually created fixtures receive explicit known source pins/pc units; no later Library revision is silently substituted.

The stored issue script consumed 3 tees for the header, then 1 manufactured header, 1 motor and 1 pump/filter set for the final assembly. Calls repeated with the same request reused the issue journals. All MR reservations reached zero; per-operation material prerequisites remained covered by issued quantities. All temporary states, evidence, stock, reservations, journals and events in this native test rolled back.

Start-readiness active states in this workspace include Draft/Released and exclude In Progress; the test therefore verifies prerequisite coverage while work is In Progress rather than incorrectly asserting a new Ready-to-start notification. Issue states independently allow Released/In Progress.

Evidence: `tmp/production-issue-native-test.log`.

## Browser rehearsal

Used the existing signed-in account and ordinary UI at https://localhost:7053. Guarded native helper prepared disposable synthetic current prerequisites and received the header; **the material issues were posted through the actual browser buttons**. No permission/authentication bypass or disabled issue checks.

The first partial-entry attempt exposed input synchronization: the displayed 1 could still submit the original 3. Fixed the quantity binding to update on input, restored the failed fixture, and repeated the browser scenario. The verified run produced:

| Journal | Order / prerequisite | Pinned input | Issued |
|---|---|---|---|
| 7 | Header / tees | ITM-00001 version 21349, revision 2 | 1 pc |
| 8 | Header / tees | Same pin | 2 pc |
| 9 | Final assembly / header | ITM-00006 version 21644, revision 0 | 1 pc |
| 10 | Final assembly / motor | ITM-00021 version 21739, revision 0 | 1 pc |
| 11 | Final assembly / pump and filter | ITM-00012 version 21742, revision 0 | 1 pc |

All used shelf **01-A** (15682). After journal 7 the database confirmed 2 pc still reserved. After all five, MRs 16122/16009/16022 had zero reservations and issue totals 3/1/1 respectively. Shelf stock for all four pinned inputs was zero. Panel refresh retained all issued totals and removed further issue controls. Screenshots 218–221 document partial, completed fittings and final-assembly issues.

Cleanup archived disposable issue/receipt journals locally, reversed synthetic physical consumption and reservation releases through explicit stock movements, and restored original current evidence, Draft states, units/provenance and temporarily changed receipt parameters. Issue reversals retain movement type 10 with opposite quantity so issued-material totals return to baseline; reservation balances include their offsetting movements. The initial cleanup classification was corrected by explicit offsetting movements, without rewriting old ledger entries. Journals were archived and removed only for these known disposable fixtures. This is test cleanup, not a product returns feature.

Final audit: zero issue/receipt journals for both orders, both Draft, zero reserved/issued totals for all three MRs, zero synthetic pinned shelf quantities. Historical synthetic versions and ledger/reversal traces remain. The browser refreshed the restored fixture and showed ordinary Draft/missing-evidence blockers. Also cleared stale success text on refresh and suppressed a meaningless pin 0 display after this observation.

Evidence: `tmp/production-issue-browser-positive-audit.txt`, `tmp/production-issue-browser-final-audit.txt`, guarded helper backups and logs. No actual goods were manufactured, inspected or consumed.

## Remaining scope

- Fresh Manage Setup UI installation and a separate Warehouse-user rehearsal remain; local settings were installed by an idempotent guarded native helper. Source seed and customization-preservation test pass.
- The reviewed prerequisite list defines issue coverage. This rehearsal is not a recursive full-BOM or actual physical stock reconciliation. Add all relevant MRs/prerequisites before rehearsing the complete manufacturing BOM.
- General returns, scrap, substitutions, cost rollup, complete physical production, as-built/client handover and project closure remain separate work.

[Workflow guide](../docs/workflows/engineering-production-material-issue.md).
