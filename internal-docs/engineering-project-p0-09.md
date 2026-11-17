# P0-09: precise subscriptions and automatic assignment

Source review: 14 November 2026. Planning only; no implementation or live tests.

## Agreed required fixes: script-first workflow

Use editable automation scripts wherever possible to define subscription creation, recipient resolution, event/state matching and follow-up. Extend the scripting engine with supported subscription APIs and evidence/event context where missing; reuse existing delivery services. Do not hardcode Sales/Quality workflow in application services or introduce a parallel rule engine.

Required scope includes target-state filtering, P0-07 submission/replacement matching and PO-line ownership routing, automatic script-driven assignment, administrator-editable defaults/parameters and clear example scripts/API documentation. Platform services retain access checks, transactional/durable event integrity, retry and duplicate protection. Scripts define business policy; they do not bypass permissions.

Provide an example PO-transition script assigning Sales delivery-complete watches and Quality submission watches from configured people/groups. Provide a submission-event example for the intended Quality follow-up once a supported event attachment exists; a script context label alone is not an event runner. Scripts, parameters, mappings, recipients, channels and expiry remain editable in application UI with documented examples. Implementation remains on hold.

## Existing functionality

Users can subscribe to one/multiple items for general state, version and revision changes, choosing task/email/in-app delivery, expiry/no expiry, own changes and stop after first report. SubscriptionAssign permits assignment to another named workspace person. Subscribers need read access; watches do not grant access. Subscribers/My Subscriptions/Watching, cancellation, renewal and delivery retry already exist.

The request DTO has change-kind flags and a single subscriber identity, but no target-state identities, upload event selector, group/role rule or parent-child scope. SubscriptionEventConsumer handles lifecycle transitioned/switched and version/revision events. A PO-header watch is not a supported child-upload subscription.

Lifecycle Notify and Set Assignee actions can provide some configured in-app/task handoffs today, but are not the same as automatic subscriptions with channel/expiry/filter behavior. Reuse delivery infrastructure rather than build a second notification engine.

## Missing behavior for the worked example

1. **Target-state filtering:** Sales should hear when its PO line reaches the configured delivery-complete state, not every intermediate state. Persist state identities and evaluate first-only after a matching event. This can also support replacement pickup watches once the correct accepted-receipt state is defined.
2. **Submission/replacement matching and ownership routing:** consume P0-07's durable RVD submission signal and route to its owning PO line/project for Quality. Generic version notifications cannot reliably mean supplier documentation was submitted. Keep document/file/version links in the message.
3. **Automatic assignment:** an editable script creates the required Sales and Quality watches at the selected PO lifecycle point, resolving configured project people/groups through supported APIs. Existing manual assignment does not establish this script API/runner. Add an idempotent subscription API/action; a universal rule engine is unnecessary.
4. **Policy defaults:** default expiry is currently 30 days in EntitySubscriptionService. A long-lead order may outlast it. New workflow defaults for expiry, channels and matching must be administrator-configurable and visible to users.

## Administrator UI requirements

| Setting | Proposed control |
|---|---|
| Relevant event/state | Subscription filter editor; selected state identities with editable labels |
| Automatic attachment point | Existing lifecycle transition Automations tab, with new configurable subscription action if needed |
| Recipients | Project-role/person property or selected group/user; no hardcoded Sales/Quality groups |
| Child scope | Explicit RVD → owning PO line/project relationship, not unrestricted descendant watching |
| Delivery | Task/email/in-app defaults, expiry/no expiry and own-change/first-match choices |
| Content | Editable subject/body templates with safe supported values and evidence links |
| Missing owner/access | Configurable fallback/escalation owner and visible assignment failures |
| Membership changes | Explicit snapshot versus maintained-role policy; do not silently promise live group watches |

Ordinary users continue configuring their own watches within permissions; administrators configure workflow defaults and automatic assignment. Prefer current dialogs/automation editor with typed settings. Missing identity/read access must be reported, never fixed by granting implicit access.

## Scope limits and integration

P0-07 publishes submission facts; this item matches/routes/delivers. P0-08 supplies documentation status and reopening semantics; P0-11 supplies combined completion/payment events. Accepted supplier documents remain read-only until Quality reopens them. Avoid introducing scheduled reminders here unless explicitly needed; they require separate timer support.

Do not automatically cancel first-match watches on an unrelated state. Define duplicate handling between manual and automatic subscriptions, assignment retries and future role changes. Preserve the existing user's channel choices unless the agreed administrator policy explicitly requires otherwise. Hiding an automation from the main list must not affect subscription execution.

## Acceptance

Sales watch ignores intermediate states and reports selected delivery completion once. Quality receives correct submission/replacement evidence after permitted upload, with access checks and exact PO-line links. Supplier cannot upload accepted evidence until reopened. Replayed events/automation retries produce no duplicate watch/task/email; assignment failures are visible. Long-lead defaults do not expire unexpectedly. Personal watch/assignment, renewal and cancellation continue to work.

## Sources

`MatStream.API/MatStream.Web.Shared/Dtos/Subscription/EntitySubscriptionDtos.cs`; `MatStream.API/MatStream.Shared/Enums/EnumEntitySubscription.cs`; `MatStream.API/MatStream.Application/Services/Tenant/EntitySubscriptionService.cs`, `SubscriptionEventConsumer.cs`; existing subscription delivery/repository code; `matstream-docs/docs/features/subscriptions.md`.

## Implemented upload slice — 16 November 2026

Rename-safe administrator state mapping and transactional document.uploaded events now notify eligible document/owner State-change subscribers, including replacements in the same state. Default settings and admin UI are included. See [local test evidence](engineering-project-rvd-upload-test.md). Automatic department subscription scripts and the broader completion workflow remain separate.

## Implemented personal lifecycle subscription proposals — 16 November 2026

Generic administrator-defined script rules now propose filtered subscriptions following configured lifecycle transitions. Each recipient consents and can remember Yes/No by rule/transition in personal workspace settings; User Settings can restore Ask or change the answer. Sales delivery-completion and Quality upload examples are seeded. See [test evidence](engineering-project-subscription-proposals-test.md) and [workflow guide](../docs/workflows/engineering-subscription-proposals.md). This does not implement Payment Milestones or final technical completion.
