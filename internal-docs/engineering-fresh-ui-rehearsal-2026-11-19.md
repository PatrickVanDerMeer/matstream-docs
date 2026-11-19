# Fresh Engineering Project UI rehearsal — 19 November 2026

Status: **completed through final client handover in the local application**. This is a synthetic software rehearsal using one signed-in account for internal work and named client/supplier portal reviews. It does not establish separate-role permissions, physical manufacture, contractual acceptance, dispatch or financial settlement.

## Workspace and preservation

- Local databases: `MatStream` and `PMSoftware`; both backed up with COPY_ONLY/CHECKSUM and RESTORE VERIFYONLY before workspace changes. No remote database was changed.
- Existing active workspace 113 was deleted using the normal application service, which archives it recoverably. Historical archived workspaces were retained. Zero active workspaces were verified before the new wizard creation.
- Fresh workspace: **Engineering Project E2E 2026-11-19 rehearsal**, slug `iodum1`, workspace 114, owned by the signed-in account.
- Created through the Manage UI, selecting CRM configuration/sample and Engineering Project configuration/sample. Successful wizard run: 1,812 steps, 20 CRM and 177 Engineering sample records, no warnings.
- A previous copied-database test had reused workspace 114's physical file-store directory. Its 29 files were moved intact to `tmp/engineering-e2e-20261119/prior-acceptance-filestore/114` before retrying the wizard; no destination file was overwritten. Future copied-database tests need a separate `FileStores:RootPath`.
- Core billing job types 10–12 (job records 2, 3 and 4) were temporarily paused. Their original enabled flags were restored after stopping Core. SMTP was captured on loopback. No supplier/client invitation email was sent; all shares named only the signed-in test account.
- The new workspace and its audit history are retained. Local APIs, Manage, app and SMTP sink were stopped after the test; ports 7053, 7081, 7239, 7273 and 25259 were checked.

## End-to-end UI results

| Stage | Observed result |
|---|---|
| Quotation | Three lines, EUR 8,000 + 25,000 + 92,000 = 125,000. Issue succeeded. Accept was blocked while lines were Draft; releasing all three enabled acceptance. Exactly one PRJ-2026.0001 was generated from quotation 18203. |
| Project/BOM | Project 18207, number E2E-2026-001. Placed one ITM-00000 (1 pc), including manufactured header ITM-00006 and 21 purchased BOM positions. All 23 Library definitions passed For Review → Approved → Released. Engineering project copies were made without changing the Library definitions. |
| Drawing/client review | TR-2026-0001 issued with both A/v1 drawings. Client portal submitted Revise and Resubmit with a commented PDF. Main drawing was revised to B, reviewed and released; the original portal still displayed its fixed A/v1 files and response. |
| Production drawing issue | TR-2026-0002 issued with main B/v5 and header A/v3. Portal displayed PRJ-2026.0001 by name. For Production correctly did not allow client review under the configured purpose policy. |
| Planning | Fixed-date Header Fabrication Starts / Skid Assembly Starts milestones, January 18 / 22, 2027. All purchased source positions mapped to the appropriate task. Motor lead/handling 20/2 working days; pump 15/1. MR dates were calculated and saved. |
| Procurement | Tee MR 18473 (3 pc), pump 18502 (1 pc), motor 18503 (1 pc). Reviewed supplier source/version and agreed terms. Created exactly two original POs: Meridian PO-2026.0001 (178.50) and combined Acme PO-2026.0002 (1,124.00). Both PDF outputs and supplier-access issue gates were exercised. |
| Supplier evidence | Uploaded all eleven original synthetic RVD PDFs through the supplier portals. Submitted → Accepted passed 11/11. An Accepted certificate was read-only externally; Quality reopened it, a corrected PDF was uploaded through the portal, then reaccepted. Original history remained available. |
| Goods receipt | Received 3 tees, 1 pump and 1 motor into Inventory / Rack 01 / 01-A, with pinned item versions. The tee quantity entry accidentally appended to its prefilled value; the server capped receipt to the outstanding 3 with a warning. This is not evidence of a successful partial PO receipt. |
| Complete material demand | Prepared 18 further MRs for the stock-covered BOM positions, giving 21 original material requirements. Repeated 18 Engineering Ready → Procurement actions reused the existing records with positive, unchanged Required Qty and matching placement quantities. All 21 were reserved through the UI. Stock-covered lines became Covered from Stock; received lines stayed Delivered. |
| Production prerequisites | Header: five MR prerequisites. Final assembly: sixteen purchased MR prerequisites plus preceding completed header output (1 pc). Required accepted supplier documents for tee, pump and motor; reviewed both prerequisite lists. Header was ready; final assembly was blocked while header was incomplete. |
| Header inspections/start | Released header order 18505, preparing three inspection records. Saved synthetic PDFs, results, inspector and dates. In Review / Accepted passed 3/3 each. In Progress succeeded. |
| Partial material issue | Issued one tee; remaining demand changed 3 → 2 and only one MR reservation was released. Explicitly identified opening-stock revisions then issued successfully. Unidentified stock was correctly refused against a pinned production version. |
| Wrong fitting/replacement | Held tee prerequisite 18542, released one reservation and adjusted one unusable tee out of usable stock. NCR 18572 created one replacement MR 18573 and PO-2026.0003 (one tee, 59.50), with pinned item/RVD snapshot and unchanged original BOM demand. |
| Replacement completion | Shared PO only with the current account, generated PDF, issued, uploaded/accepted certificate through portal/internal UI, received one tee, and reserved it against the original MR. Evaluator reached Arrived; inspection and pickup evidence enabled Ready for Pickup → Collected. Collection posted exactly one stock issue/reservation release. Production showed 2 issued / 1 remaining; cleared its hold, issued the final good tee, then closed the NCR. |
| Header completion/receipt | Completed header work/quantity and accepted inspection-plan review. Completed transition passed. Finished header receipt 18582 added one pinned ITM-00006 at 01-A. |
| Final assembly | Predecessor gate then passed. Released/started order 18504. Issued all sixteen purchased requirements plus the finished header, with exact quantities/versions. Two final inspection records passed review/acceptance. Completed order and received one finished skid, receipt 18602. Another receipt was disabled. |
| As-built dossier | Created transmittal 18605 with Final Handover Package, same project, completed final order, As Built purpose and scope review. Added/classified 21 pinned files: 2 drawings, 12 supplier documents, 5 inspections, 1 BOM and 1 manuals/certificates index. Scope passed. |
| Final issue/client acknowledgement | Ready to Issue invalidated the earlier generated PDF as expected. Regenerated it, then Issued succeeded. Portal category policy initially refused the extra evidence types; enabled Required Vendor Document and Production Inspection Record through System Settings → Document package portal, then created the complete fixed share without email. Client portal displayed all 21 files and downloaded the two-page register. Whole-package Approved response changed acknowledgement 0/1 → 1/1. |
| Final handover | **Final handover recorded** against transmittal 18605, exact package version 27064 and share 46, at 2026-11-19 20:09:10 UTC. Evidence is retained in the protected ordinary Memo property; no dedicated production database object was added. |
| Payment plans | Created Project 125,000.00, original tee PO 178.50, Acme PO 1,124.00 and replacement PO 59.50 plans through Payment Milestones. Three installments each, exact 30/50/20 amounts. Repeated the Project initialization without duplicate records. Project milestones remain Not eligible for manual invoice review. |
| PO eligibility | Each of the three fully received/documented orders qualified all three installments. Acme evaluation covered both lines together. A temporary replacement Payment Hold made all installments Not eligible; clearing it restored Eligible. No invoice, approval, payment or settlement was executed. |
| Final Inventory | 01-A displays exactly **ITM-00000: On Hand 1 pc, Available 1 pc**. Its ledger has one Production Receipt. The header and all purchased components were consumed. No remaining reservation or on-order quantity. |

### Full-BOM test setup

The existing procurement automation creates shortage requirements, so it initially skipped the 18 opening-stock-covered positions. For this complete material-consumption test, their synthetic opening balances were temporarily reversed through normal audited Inventory adjustments, the 18 demand MRs were created through procurement release, then their balances were restored. This is explicit test preparation, not proof that the existing shortage automation creates requirements for stock-covered positions. Its purchasing semantics were preserved. Production prerequisite preparation still needs an MR for each material being allocated/issued.

The opening-stock UI initially stored no item version. All 18 synthetic balances were subsequently reversed and re-entered against their explicitly identified released versions using the new version choice. Both sets of corrections remain in the ledger; no history was edited or deleted.

## Issues fixed

| Issue | Correction and verification |
|---|---|
| Print-template upload retry selected a pending row with an empty path | Exclude unfinished paths; delete only a newly created unreferenced row on upload failure. Never overwrite/delete a colliding destination. Isolated SQL regression and successful wizard retry. |
| Printed date/currency missing although shown in editor | Resolve requested non-inherited exact-version defaults, then redact by property access. Stored overrides win. Quotation PDF verified with date, EUR and 125,000 total. |
| Portal showed EntityRef IDs | Capture readable same-workspace reference names in new fixed packages. Frozen-name regression and TR-2026-0002 portal verified. |
| Released Library baseline left clean followed BOM cache stale | Reconcile followed definitions on expansion; preserve pinned/historical baselines and stale-copy guards. Thirteen project-copy tests and Inventory SQL check; actual 22/22 child retry passed. |
| Large release exceeded HTTP timeout | Batch capacity days in one transactional JSON MERGE, preserving duplicate/precision/null/workspace semantics. Change State sends bounded five-entity requests, displays progress and refreshes after uncertain outcomes. SQL capacity test plus actual 18/18 UI batches. |
| Retrying procurement zeroed open MR demand | Existing MR contributes to planning coverage; forecast shortage must not overwrite its saved Required Qty. Reuse preserves demand and only synchronizes the current placement. Repaired the 18 affected test MRs through normal property edits, then repeated all 18 actions. Audit: 21 original MRs, zero invalid quantities, zero placement mismatches. |
| Stock labels showed IDs; property save left grid stale | Resolve new item/location labels with ordinary permission-checked reads; format Stock Unit as pc/mm; propagate property-save refresh to parent. UI verified. |
| Opening stock could not identify its revision | Explicit current item-version choice in Adjust Stock; automatic correction remains the default. Server validates version ownership/item match. Reversing/replacing synthetic openings retained audit and enabled production issues without relaxing revision matching. SQL regression covers mismatch, foreign versions and separate balances. |
| Hidden reservation column disabled Remove Reservation | Always enrich MR rows with their actual ledger reservation balance. Hidden-column SQL regression and actual UI release of one reserved tee passed. |
| Displayed default EUR treated as missing by payment plan | Use the editor's configured, non-inherited currency default only when no value is stored. Explicit blanks remain invalid; freeze resolved currency on the new parent version and milestones. Seven payment tests and successful Project UI retry. |
| Build dependency conflict after existing Core package upgrades | Align the API Swagger package with Core and remove the unused alternate OpenAPI package; API/Core/test build and runtime passed. No business behavior changed. |

## Final audits and validation

- `final-audit.txt`: both production orders Completed, NCR Closed, transmittal Issued. All 21 original MRs show issued = Required Qty, reserved = 0. The separate replacement MR tracks the extra purchase and is not double reserved/issued; collection is attributed to the configured original MR.
- Stock: only ITM-00000 has a nonzero balance (1 pc). Total Reserved and On Order are zero. 24 Production Issue movements and 2 Production Receipts, all explicitly version-pinned. One final receipt for the skid.
- Four plans / twelve milestones: Project 125,000 (three Not eligible); POs 178.50 / 1,124 / 59.50 (nine Eligible). No duplicate Project installments after rerun.
- No table/view/procedure remains whose name contains ProductionIssue, ProductionReceipt, ReplacementCollection, ProjectHandover, ProductionReadiness or ProductionStartNotice. Generic entity/property/ledger/evidence storage remains in use.
- Final targeted regression batch: 20 passed, none failed/skipped (`final-regression.log`). Explicit production issue/readiness/receipt and final-acknowledgement batch: 4 passed (`final-production-regression.log`; receipt test overlaps the first batch). Seven payment checks passed separately (`payment-default-test.log`). Earlier focused checks cover the remaining fixes listed above.
- Documentation build passed with **No issues found** (`docs-build.log`); whitespace checks passed in API, app and docs repositories.
- WASM build passed with zero errors. Its existing package advisory warning remains; no unrelated package/security update is claimed.
- Downloaded final client register: two pages; text extraction and both rendered pages reviewed. All 21 records are present, with main B and header A drawing revisions and Ready to Issue generation state.

Local artifacts are under `tmp/engineering-e2e-20261119`: database/file preservation audits, final stock/MR/payment audits, regression logs, synthetic fixtures and downloaded/rendered PDFs. Portal tokens and private SMTP captures should not be published.

## Practical limits

This completed the connected software path in one freshly wizard-seeded workspace with synthetic evidence. It did not use separate Sales, Engineering, Supplier, Warehouse, Quality and client identities. It did not operate Inventor/Vault, perform physical receipt/inspection/quarantine/collection, dispatch stock, issue invoices, pay suppliers or close the project. The late-motor forecast branch and three internal distribution-channel reissues were not replayed in this run; their previous feature tests are separate evidence. Actual contractual scope and business-role permissions require a practical multi-person rehearsal.
