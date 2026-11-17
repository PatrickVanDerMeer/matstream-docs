# PO documentation result — 16 November 2026

## Delivery

New live PoDocumentationService evaluates an enabled workspace RunScript automation containing DocumentationStatus parameters. Serializable evaluation reads current owner/version/item pin, frozen PO snapshot, current owned evidence, definition/source pins, current lifecycle states and workspace stored-file records. Missing/invalid snapshots, structural mismatches and unaccepted/missing files prevent Complete. Accepted state IDs are administrator-mapped and rename-safe. Explicit empty checklist semantics and labels are configurable. No cached Boolean/property result is written; file removal/replacement cannot leave a stale server flag. Open/reopen/Refresh status performs a new evaluation; no realtime browser push is claimed.

Default JavaScript receives Requirements and Issues facts, queues no entity writes, and returns DocumentationStatus JSON through vars. Native host refuses an unsupported Complete result regardless of custom script wording. Inaccessible evidence causes refusal instead of partial completion. Expected historical PO version mismatch is rejected. Disabled/wrong-type/malformed evaluator cannot retain a previous result.

New read-only Memo editor RvdDocumentationStatus displays acceptance counts/blockers in the General tab. Editor is available in the existing Property Definitions UI. Administrators edit mappings, accepted/rejected state IDs, empty policy and wording/script through Automations. Seed adds the script/property assignment with Setup.PoDocumentation.v1 guard; preserves installed customization. No schema or bigint keys. Ordinary grid filtering/export does not receive a persisted computed value in this slice.

## Real local records

Workspace108 cwt3qp. Motor PO line16031 / owner version21787: missing 0/4, temporary all-accepted 4/4 Complete. Pump16037 / version21793: missing 0/6, temporary all-accepted 6/6 Complete. Each then tested Accepted-with-missing-file, Submitted, Rejected, actual repository replacement upload and physical removal of a required structural row: each blocks completion. All evidence/state/link/event probes rolled back and baselines returned to zero accepted. Source Library and original orders remain unchanged. No email/invitation/payment/deployment.

Browser confirmed real motor/pump General panels and motor Refresh status; screenshots151/152 show genuine missing evidence, not staged approvals. No supplier-role browser approval rehearsal or real payment approval is claimed.

## Regression evidence

Three new SQL test cases cover current-file acceptance (including foreign-workspace file refusal), absent/explicit-empty checklist distinctions and configurable empty policy, script attempts to bypass evidence, historical owner-version refusal, child access refusal, changed pinned provenance and disabled configuration. Installed seed preservation test passes. Full Infrastructure suite before the last metadata/invalid-result validation tightening:1364 passed,52 skipped,0 failed. Focused SQL tests and both native motor/pump scenarios repeated after item-pin/source/upstream metadata validation tightened; passed. Manage suite302 passed/0 failed. API, Manage and frontend build successfully. Skipped cases are not passing evidence.

## Boundaries

No cached completion flag, waiver semantics, automatic Quality/Sales subscription assignment, final PO lifecycle change or payment eligibility. This live evidence result is the building block for later guarded completion scripts, which must re-evaluate within their own business transaction and add delivery/holds. Physical storage availability is governed by normal file retrieval/retention; the evaluator checks the workspace stored-file reference rather than reading every file's bytes.
