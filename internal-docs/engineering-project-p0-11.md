# P0-11: script-driven technical completion

Source assessment: 14 November 2026. Planning only; no implementation or live tests. Use editable automation scripts for business policy and platform APIs for safe reads/writes.

Agreed required fix: implement the script-driven technical-completion and invalidation workflow described below, including supported evidence APIs, real event attachments, guarded target transitions and integration with shared Payment Milestones. Implementation remains on hold.

## Intended result

When a PO line's required goods are acceptably delivered, its effective RVD checklist is complete and no applicable hold remains, a script marks its technical workflow Complete and signals the appropriate Payment Milestone evaluation. Completion never means payment already happened.

## Existing capabilities

- Receipt/stock services and ledger quantities, PO/MR/source relationships and versioned documents/lifecycles.
- PurchaseOrderService already moves fully received PO headers to Received when no line has outstanding On Order quantity. This is physical receipt settlement, not document-aware technical completion.
- Stored scripts, parameters, logs and run audit, with execution claims preventing repeats of the same automation/event pair.
- Current script actions for properties, revision changes, lifecycle switching, assignments and in-app notifications. Lifecycle action application uses a transaction and permission/current-version checks.
- Standard lifecycle transition permissions/criteria, enabling configurable completion/reopen paths.

These foundations are reusable. switchLifecycle selects another lifecycle; it does not establish an arbitrary same-lifecycle transition of a related PO line to Complete.

## Can configure already

PO-line states/transitions, permitted groups, local property criteria, ordinary status fields/views and stored scripts/parameters. Existing tasks/notifications can provide follow-up from supported contexts. Define Complete independently of Delivered/Received and use the shared Payment Milestone model from P0-10.

Saving a script with a context label does not attach it to receipt/document events. An Error thrown by a post-transition automation also does not roll back the original transition by default.

## Missing capability

| Need | Development/integration |
|---|---|
| Read authoritative facts | Supported script snapshot/API for scoped delivery quantities/quality holds and P0-08 documentation result/evidence |
| Correct target | Resolve affected PO line/project from each evidence event; do not accidentally complete the RVD record or header |
| Safe transition | Script action to execute the configured permitted Complete/reopen/hold transition against the correct current entity/version |
| Real triggers | Attach/run on receipt/correction, RVD decision/reopening, checklist amendment and relevant hold/quantity/cancellation changes |
| Consistent evaluation | Re-read/revalidate evidence when committing; handle goods/documents in either order and concurrent changes |
| Business idempotency | One completion/follow-up per actual eligibility cycle, not merely deduplication of a single event identifier |
| Invalidation | Authorised reopening/rejection/new requirement/receipt correction removes eligibility and holds unpaid follow-up |
| Payment linkage | Ask P0-10's milestone evaluator to reconsider the correct PO/line scope rather than create a second payment-request mechanism |

Do not duplicate P0-08's checklist evaluator or P0-07's submission event. This script combines authoritative facts exposed by those features. Receiving the last item alone must not imply accepted documentation or usable quality-cleared stock.

## Example script policy (conceptual, not executable API)

```text
facts = read current delivery, documentation and hold facts for affected PO line
if line is cancelled: do not complete
else if accepted delivery satisfies required quantity
     and documentation complete
     and no applicable blocking hold:
    apply configured technical-complete transition if needed
else:
    apply configured invalidation/hold route when previously complete
request re-evaluation of linked payment milestones when eligibility changed
```

Provide executable administrator examples only after supported API signatures and real attachments are implemented. Show both completion and invalidation cases with parameters; no hardcoded article IDs, state names or department groups.

## Administrator settings

- Script and supported event attachments, with editable descriptions and typed/documented parameters.
- Relevant PO-line categories, relationships and authoritative property/status mappings by identity.
- Delivery quantity basis, allowed tolerances if used, accepted-versus-quarantined treatment and cancellation policy.
- Documentation/hold semantics from the shared policies, not conflicting duplicate settings.
- Completion and reopen/hold transitions, authority and required reasons.
- Relevant payment-milestone scope/evaluator and recipient/action mappings through P0-09/10.
- Retry escalation owner, blocker messages and operational views.

Use existing Automations/lifecycle/category UI and extend missing configuration controls. Platform permission checks, audit integrity, stale-data protection and durable execution remain guarantees, not optional script bypasses. Later invalidation of already paid milestones raises a traceable exception rather than rewriting payment history.

## Scope and acceptance

No separate completion engine with hardcoded lube-oil rules is needed; extend scripting and shared evidence APIs. No scheduled polling is assumed necessary if reliable change events exist; reconciliation tooling can be considered after failure analysis.

Test goods-first and documents-first, partial/rejected/quarantined goods, Quality reopen, new effective requirement, cancelled/changed quantity, concurrent receipt/review, duplicate events, stale versions and script failure/retry. Verify correct target transition, one milestone eligibility update per cycle and visible blockers. Read-only Accepted supplier permissions remain effective. Header Received must never be mistaken for documentation-complete or Paid.

## Sources

`MatStream.API/MatStream.Application/Services/Tenant/Scripting/ScriptContext.cs`, `ScriptAutomationService.LifecycleActions.cs`; `MatStream.API/MatStream.Application/Services/Tenant/AutomationRunner.cs`; `MatStream.API/MatStream.Application/Services/Tenant/Procurement/PurchaseOrderService.cs`, CloseFullyReceivedOrdersAsync; lifecycle administrator help and P0-07/08/09/10 assessment dependencies.
