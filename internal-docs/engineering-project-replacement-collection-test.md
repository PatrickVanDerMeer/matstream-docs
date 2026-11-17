# Replacement stock collection — local test report

Tested 16 November 2026 in local workspace 108, separate test project 16002. No internet deployment, supplier communication, real manufacture or payment.

## Implementation

- Stored script action `matstream.actions.collectReplacement()`.
- `ReplacementWorkflow` parameters: CollectionEnabled, CollectedStateId, CollectionAutomationId, CollectionLocationPropertyId and CollectionNote. Existing MR/quantity/prerequisite/inspection/document mappings remain editable.
- The configured attached library script runs inside the normal lifecycle state transaction. Claim, stock issue, reservation release, issue journal, state/version and event commit together. The normal after-transition runner deduplicates the same execution claim.
- Stock movement type 10 is Production Issue, sourced to the allocation MR; movement type 8 releases that MR's reservation. Native checks enforce current version, Editor/stock grants, read access, pinned revision, actual shelf, sufficient physical stock/reservation and current pickup evidence.
- One issue journal per workspace/NCR retains operation, prerequisite, MR, pinned item version, shelf, quantity, collector and NCR version. New identifiers/references are int. Migration is repeatable. Workspace purge removes this workspace's journals before entity/version removal.
- Readiness counts reserved plus already issued material when CountIssuedMaterial is true, and accepts issued material only for its recorded operation/prerequisite. Open requirement/procurement coverage and additional reservations also account for issued quantity.
- The picker uses Folder family and categories with Stock Location behavior. Definitions, category defaults, permissions, filters and scripts are editable through existing application UI. No new broad stock-management grants were silently seeded.

## Seed and browser proof

Manage Setup → Non-conformance and Replacements added the location property, Warehouse: Collect replacement fitting (automation 71), collection mappings and its Ready for Pickup → Collected assignment. Existing evaluator/creation scripts and administrator choices were preserved. The location picker was verified with the ordinary administrator Property Definition filter and selected shelf 01-A.

The main browser collected NCR 16131. Status showed one issued fitting from location 15682 and original MR 16122 coverage of two reserved plus one issued against three required. The fitting production prerequisite remained held until explicitly cleared in its Uses grid; then header 16118 showed Ready to start with the issued quantity included. The script and root mappings were inspected in the ordinary Automation editor. See screenshots 186–191 in the [workflow guide](../docs/workflows/engineering-resume-production.md).

## Native and automated tests

- Missing location, warehouse folder instead of stock shelf, and an empty valid shelf each rejected the native collection transition. State and stock remained unchanged.
- A throwing editable script rejected collection without writes.
- An injected journal failure after the stock writes rolled back stock consumption, MR release, state, event and execution claim. The test trigger and temporary script change were removed/restored.
- Mismatched actor and stale version were refused. Sequential and three concurrent retries reused one journal. Additional reservation was refused for demand covered by issued material.
- The final native transaction test rebuilt the current source and exercised collection, operation/prerequisite provenance, replay, readiness and Collected → Closed. It also used the administrator's category-default shelf and verified that a broken NCR mapping did not block an unrelated production transition. The entire retest rolled back.
- Permanent SQL tests exercise repeatable journal migration/uniqueness/int keys, an automation claim enlisted in a transaction with rollback/retry, and readiness with issued quantities, an administrator opt-out and another-operation rejection.
- Full API regression: 1,412 passed, 52 skipped. Manage setup suite: 310 passed. Final focused checks and builds are recorded in tmp/collection-* logs.

## Restoration and limits

The simulated issue was reversed, its MR reservation restored and then the staged receipt/test stock/reservation and synthetic inspection/RVD acceptance were reversed/restored. Journal 2 is retained with a reversed timestamp. Native checks confirm zero net issued quantity and zero reservation for MR 16122. NCR 16131 is Awaiting Replacement; header 16118 is Draft with prerequisite 16123 held. A final current-source retest was wholly rolled back. Version, ledger and notification history remain as evidence.

This restoration used explicitly guarded local test tooling; it does not provide a production returns UI. A reversed case must not be reused to silently issue again. General whole-BOM issues, production returns, valuation reconciliation and separate departmental identity rehearsal remain separate work.

The repeat fixture receipt reported no reliable commitment price because earlier synthetic reversal postings had no valuation. Physical receipt/lifecycle checks continued; no price was invented and this report does not claim financial valuation or payment testing. The issue action itself posts quantities; it does not approve supplier payments or automatically clear production holds.
