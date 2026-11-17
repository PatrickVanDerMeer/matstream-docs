# P0-10: supplier payment planning

Source review: 14 November 2026; planning implementation and local validation: 16 November 2026. The original assessment below describes the wider P0-10 scope. See the implemented planning slice and remaining work at the end.

## Agreed required fix: shared Payment Milestone category

Create one reusable Payment Milestone occurrence category and a configurable Uses tab accepting those milestones. Assign the tab to Projects for customer invoice planning and Purchase Orders for supplier payment planning. Each parent owns separate milestone instances; never share one instance between unrelated records.

The category supports description, percentage, amount basis/calculated amount/currency, eligibility condition, planned/due dates, responsible person, invoice/reference documents and completion/payment evidence. Configure separate applicable lifecycles for customer invoice issuance versus supplier payment: Invoice Issued is not Paid, nor does invoice issuance prove customer cash receipt. Select/validate the appropriate lifecycle for the parent use case through administrator configuration.

Reuse standard Uses-grid editing and Choose columns, category properties, lifecycle permissions and documents. Administrator-editable scripts initialise selected schedules, evaluate conditions and request follow-up; add supported APIs and safeguards where missing. Administrators configure fields, tab assignments, default columns, schedule/defaults, parent-specific lifecycle selection, mappings, eligibility rules and permissions through application UI. Users add/adjust milestones within those rights.

This shared model replaces a PO-only milestone design and is agreed required development, including script APIs/calculations/persistence/approval safeguards needed to support it. The shared planning slice is implemented below; issued-PO automatic eligibility is implemented below; settlement and amendment safeguards remain follow-up work. Bank execution/accounting posting is outside scope.

## Existing capabilities

- Administrator-editable Payment Terms on suppliers/POs, including suggested split-payment text. Text such as 30/50/20 can describe an agreement but does not create tracked instalments.
- PO quantities, prices, currency and commercial snapshot fields, plus document/print-template mechanisms.
- Generic configurable categories, properties, lifecycle permissions, tasks, milestone tabs and calculations.
- Stored automation scripts with existing creation/task/notification foundations, subject to their actual API restrictions.

Generic Milestone currently represents a task checkpoint with Planned/At Risk/Reached/Missed. It is not a supplier payment ledger; Reached does not mean payment was approved or paid. MatStream subscription billing is unrelated.

## Can configure already

Payment Terms labels/descriptions and supplier defaults; supporting offer/invoice documents; ordinary percentage/amount/date/reference fields; payment-related categories/views and lifecycle permissions. This can support manual tracking, but does not establish automatic creation, reliable eligibility or protected financial history.

## Missing for the agreed 30/50/20 example

| Capability | Required behavior |
|---|---|
| Structured schedule | Three persisted instalments linked to the correct PO/scope, with percentage, basis/amount, currency, planned/due date and evidence |
| Eligibility | 30% on the configured authorised-order condition; 50% on accepted complete delivery of agreed scope; 20% on delivery plus accepted documentation/no blocking holds |
| Lifecycle | Keep Not eligible, Eligible, Requested, Approved and Paid distinct; record reasons for hold/rejection |
| Approval/payment evidence | Authorised approver, supplier invoice/reference, payment amount/date/reference and auditable corrections |
| Arithmetic | Reconcile percentages and amounts with explicit rounding/basis; avoid paying the same scope twice |
| Changes | Partial deliveries, PO amendments, cancellations, credits and evidence reversal must not silently rewrite approved/paid amounts |

Confirm whether milestones are PO-level or line-level. Existing plan proposes PO-level release only when all applicable lines qualify; this is a proposal, not an already configured rule. Do not release a whole-order instalment because one line arrived.

## Implementation approach

Use the agreed shared Payment Milestone category/Uses tab on Projects and POs, extending generic entities only where needed for audit and amount requirements. Reuse existing tasks/permissions; avoid a separate finance engine. Do not force the generic task milestone's Reached state to mean Paid.

Provide example administrator scripts to initialise a selected payment schedule, evaluate eligibility from supported delivery/documentation facts, and request approval/notify Procurement. Reuse P0-08 documentation results and P0-11 combined completion. Add supported APIs for idempotent child creation, evidence reads, guarded status transitions and relevant event attachments where existing primitives cannot do this. Existing createLinkedEntity has source/target restrictions and must not be assumed to initialise arbitrary payment records.

Platform guarantees must enforce access, decimal precision, consistent persisted changes, no duplicate requests and audit integrity; a script should not be able to bypass an approval or rewrite a paid record. Business conditions and amounts/defaults belong in editable configuration/scripts.

## Administrator settings

- Reusable schedule templates with editable instalment names/descriptions/percentages; 30/50/20 is a useful default, not a fixed rule.
- Basis (e.g. agreed net amount), currency, tax treatment, precision and rounding within supported financial precision.
- PO/line scope and treatment of partial deliveries, amendments and credits.
- Eligibility scripts, typed parameters, semantic state/property mappings and real trigger attachments.
- Due-date rules and required supplier invoice/evidence; timer reminders only if a supported runner is provided.
- Requester/approver/payer groups and any required separation of duties.
- Allowed overrides, hold/reopen/correction route, mandatory reasons and message templates.
- PO-specific schedule override permissions and presentation through normal fields/tabs/Choose columns.

Keep all new business settings in workspace application UI; provide script examples and reference docs. No hardcoded departments, state labels, percentages or ITM numbers. Administrator edits must not retroactively alter historical paid instalments.

## Scope boundary

Record planning, eligibility, approval and actual-payment evidence. Do not implement bank transfers, full accounts-payable posting, tax calculation or invoice matching integrations merely to support this workflow. Actual payment is separately authorised; a notification is not a payment.

## Acceptance

An illustrative EUR 10,000 net PO creates EUR 3,000/5,000/2,000 once. Delivery alone does not release the final 20%; documentation alone does not either. Multiple-line scope, changed totals, rounding, duplicate events and reversal before payment are handled explicitly. Already paid amounts remain historical and corrections are traceable. An administrator can change the schedule/defaults through UI and scripts without code changes.

## Sources

`MatStream.Web.Manage/Features/Setup/Templates/Parts/CommercialTermsPart.cs`, `PurchaseOrderFieldsPart.cs`, `MilestonesPart.cs`; existing script action implementations; original Engineering Project Workflow payment step. Targeted source search did not establish a supplier payment-schedule/ledger feature; generic configuration remains a candidate, not a verified complete solution.

## Implemented planning slice — 16 November 2026

Shared ordinary Payment Milestone category, owned Project/PO Uses tabs, separate invoice/payment lifecycles, Documents/evidence, configurable columns and total, and the stored Create payment milestone plan script are implemented. Administrator mappings/templates/rounding are stored in Automation Parameters; normal category/tab/lifecycle editors provide the remaining configuration. Fresh engineering setup includes it; a focused Payment Milestones setup option supports existing workspaces. Installed-marker reruns preserve administrator changes.

The script action initializes once from explicit parent agreed-basis, currency and anchor-date fields, using C# decimal arithmetic, final-row rounding reconciliation and one serializable transaction. A versioned, read-only parent plan record prevents accidental recreation, including after an installment is deliberately removed. Existing plan amounts/states/dates are never recalculated by rerunning initialization. Generated financial fields are read-only by default; ordinary scheduling/evidence fields remain editable. This is a controlled initial planning slice, not a completed supplier payment ledger.

Locally verified: project 16002 EUR10,000 -> 3,000/5,000/2,000; motor PO16030 EUR299 ->89.70/149.50/59.80; pump PO16036 EUR825 ->247.50/412.50/165.00. Parent-specific lifecycle IDs match each initial state. Earlier parent history remains unchanged; retries create no additional children. Automated SQL tests verify rollback after a simulated second-child failure, invalid foreign lifecycle state, altered templates and paid/removed rows. No external action was taken.

Implemented eligibility: stored workspace script and stable configurable rule keys, whole-PO stock-receipt/frozen-RVD facts, Payment Hold, event consumer and configurable periodic reconciliation, verified internal Editor executor, ordinary criteria/permission-checked transitions, PO status panel, and live preconditions before eligibility/request/approval/payment transitions. The worker automatically qualified the motor/pump order installments locally; hold withdrawal/restoration was browser-tested. Simulated full receipts, accepted documents and Paid evidence changes were rolled back.

Remaining: supplier invoice/actual-payment evidence checks, amendment/credit/cancellation reconciliation and any explicitly configured reminders. Do not label P0-10 fully complete or imply P0-11 eligibility automation has been implemented. See the public engineering-payment-milestones.md workflow guide.

Browser follow-up: saved motor milestone 16110's planned date as 2026-12-15 and invoice reference as an explicit local-test planning note, reopening PO16030 at version21897/v4 and rerunning the actual ribbon script without duplicate rows or date reset. Fixed PurchaseOrderLineCommitmentService treating ordinary PO children as stock lines; classification now uses the stored category behavior inside the transaction. Existing stock receipt/quantity tests and an ordinary-child/no-ledger test pass. The local configuration was applied with the guarded native helper; the new Manage-only installer UI still requires a signed-in Manage session for a first-run browser walkthrough. Main-app plans and editing were verified in the browser.
