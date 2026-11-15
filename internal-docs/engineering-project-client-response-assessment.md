# Client remarks, response states and subscriptions

Assessment: 15 November 2026. Source inspection and existing local test evidence; no new browser submissions, implementation or runtime configuration changes.

## Agreed workflow

The client downloads the issued drawing and returns a commented PDF against that drawing row, choosing a configurable review result. The responsible engineer/document controller subscribes before the return and is notified of the response lifecycle change. Engineering discusses the remarks with the Lead Engineer/Project Manager, updates Inventor, obtains engineering review/approval and releases in Vault. Upload the new revision into MatStream and distribute new transmittals to Client, Work Preparation and Purchasing. MatStream preserves traceability and distribution; it does not duplicate Vault technical approval. No mandatory independent checker or automatic evidence-verification release gates are required.

## Existing capability

- DocumentReviewForm offers a reviewed-document selector, outcome, comments and one PDF attachment. On a drawing detail it defaults to that issued line; on the package it can offer whole-package responses. This is a response form, not an inline lifecycle change control in each grid row.
- Settings UI (Document reviews) configures response category, property mappings, allowed purposes/accepting transmittal states, document/package response scope, reviewer rules, outcome codes/labels/descriptions/meanings, required comments/PDF, size limit, visibility and notification group/message.
- Submission validates the selected fixed-package line and readable PDF. A new native response occurrence is created beneath that line, retaining exact package/document/file identities. The issued drawing file and lifecycle are not changed. Additional submissions are new response records, preserving previous evidence.
- Prior browser test on 14 November verified required comments/PDF, upload/download, additional responses, exact-version context and original preservation. Six in-app notifications reached three eligible Engineering Authors across two submissions. This proves configured group notification, not subscription delivery.
- Current outcome meanings are Approved, ApprovedWithComments, Rejected and Information. Administrators can add named outcomes, mapped to these four aggregate meanings; arbitrary new aggregation meanings require development.
- Seeded response lifecycle starts Submitted, followed by internal Acknowledged/handling states. Outcome is stored in Review Outcome; it does not select the lifecycle state. Acknowledgment has an editable notification automation script.
- Generic subscriptions already watch an existing entity's state/version/revision and support InApp, Email and Task channels, expiry and own-change settings. Subscription create/assign permissions are configurable. Matching is exact SB_EntityID, not descendants or related drawing responses.

## Required focused fixes

1. Connect the chosen client outcome to an administrator-configured response lifecycle transition/state. Keep the client's review result distinct from internal engineering handling states. Offer permitted results on the selected drawing row; do not grant general editing of the source drawing or overwrite its issued state/file. Use normal transition validation/permissions and event recording, not a silent state SQL assignment.
2. Connect response events to subscriptions registered before the response exists. A subscriber to the drawing or issued line currently does not receive a child response's event. Provide a configurable relationship/event scope, reusing subscriptions instead of inventing a second recipient list. Administrators configure the relevant response category, watched relationship/source and event rules; authorised engineers/document controllers select subscriptions through the UI.
3. Ensure the upload/result submission produces the event required for that notification, including additional/corrected responses, without duplicate alerts on retry. Creating an initial Submitted record and writing an outcome property currently does not call the lifecycle transition/event path. A subscription added to that newly created response afterward cannot notify someone about its earlier submission.

Keep category/lifecycle/property/outcome mappings, allowed client transitions, event scope, notification defaults/messages and any script parameters editable through workspace administrator UI. Seed useful defaults and preserve administrator edits/removals. Use scripts for configurable follow-up actions where possible, while native validated submission/state/event handling provides the reliable trigger. Existing group notification may remain optional; configure it to avoid duplicate alerts when subscription delivery is adopted.

## Not required

No automatic proof that every revised drawing/transmittal link is present. No mandatory different checker. No new automatic MatStream production-release evidence gate. No Vault integration required for this agreed manual export/upload flow. Existing handling fields and lifecycle can remain available without making their closure an extra prerequisite for receiving/distributing a Vault-released revision.

## Acceptance when implemented

Subscribe the engineer/document controller to the configured drawing/issued-line review event before client submission. Return Approved with Comments plus PDF against ITM-00000 A/v1. Verify the mapped response state, one notification per configured subscription/delivery channel, accessible returned PDF and unchanged original. Test another configured result, a correction, retry, unauthorised/expired link and a non-subscriber. Verify administrator changes to mappings and recipients through UI. Do not claim Email/Task delivery based only on the prior in-app group test.

## Inspected sources

- MatStream.Web.Assembly/Features/Shares/Components/DocumentReviewForm.razor
- MatStream.Web.Assembly/Features/WorkspaceSystemSettings/Components/DocumentReviewSettingsEditor.razor
- MatStream.API/MatStream.Application/Services/Tenant/DocumentReviewService.cs
- MatStream.API/MatStream.Shared/Settings/DocumentReviewSettings.cs
- MatStream.Web.Manage/Features/Setup/Templates/TemplateSeedContext.DocumentReview.cs
- MatStream.API/MatStream.Application/Services/Tenant/EntitySubscriptionService.cs
- MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlEntitySubscriptionRepository.cs
- engineering-project-transmittal-review-test.md


## Implemented locally — 15 November 2026

Direct client response lifecycle states replace the outcome-property mapping in the agreed workflow. Share-group transition permissions, criteria and per-state attachment requirements are enforced at submission. Existing subscriptions on configured drawing/line/package targets receive the response event, with response read-access filtering and idempotent hit matching. The local browser saved Approved with Comments and received one in-app subscriber notification. An existing missing ChannelID alias in subscription delivery SQL was fixed. See engineering-project-direct-response-state-test.md and the updated client workflow guide. Earlier automatic release-gate proposals remain superseded by the Vault-led process.
