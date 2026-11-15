# Direct client response states and subscriptions — local test

15 November 2026. Local services only, workspace 108 / cwt3qp (Engineering Workflow Test). Current test account acts as engineer and client. No emails, tasks or new shares were sent/created.

## Implementation and configuration

- Document reviews supports direct response lifecycle states. Choices are enabled transitions from the response category's initial state, with explicit permission for the share's external group. The signed-in account's wider administrator membership is not used to grant portal choices. State names/descriptions come from Lifecycle Definitions.
- State requirements configure required comments/PDF and instructions. The existing settings dialog exposes mode, requirements and drawing/issued-line/package subscriber scope. Direct mode neither needs nor writes Review Outcome. Old property-based responses remain readable.
- Submission rechecks share/reviewer/purpose/state/transition permissions in its serializable transaction, evaluates normal blocking transition criteria and commits response, file reference, state and lifecycle event together. Criteria failure rolls back the response. The new response's initial-to-result transition is audited; additional client submissions remain separate native response records.
- SubscriptionEventConsumer routes the committed response event to configured related entity subscribers. Each subscriber must also be able to read the response. Normal expiry, channels, own-change filtering and idempotent subscription/event matching remain active. Timeline wording identifies a client response rather than implying that the source drawing itself changed state.
- Follow-up transition automations use the existing automation runner from a portal-specific event consumer, with the stable event ID. Scripts and feature actions remain configurable in Lifecycle Definitions/Automations. They run after commit; failures are audited, not a rollback of the client response.
- Fixed an existing delivery SQL error: the first CROSS APPLY channel SELECT lacked the ChannelID alias. The native bundle could not create any channel deliveries until corrected.
- Document Control seed installs Approved, Approved with Comments and Revise and Resubmit, external transition permissions and requirements. Fresh defaults enable direct states and drawing subscriber notifications, with no additional group fallback. The response register defaults to lifecycle state. A separate installation marker preserves later changes/removals. Existing explicit settings and historic responses are preserved; the direct-state option remains administrator-editable. No schema migration/new identifier tables.

## Actual local workflow

1. Started local Core, Tenant, Manage and client services. User signed in locally. Applied equivalent guarded seed additions to this existing disposable workspace with tmp/direct-review-local --configure; did not rerun the full Setup wizard.
2. Saved subscription 1 through the drawing's Subscribe UI before returning remarks: entity 15795 / ITM-00000.pdf; user 360; State; InApp only; include own changes true; expiry 15 December 2026. Including own changes is necessary because the same test account performs both roles.
3. Reused review-enabled share 12, original TR-2026-0001 version 11, issued drawing A/v1. Opened the drawing row, selected Approved with Comments and entered LOCAL STATE TEST remarks. Submitting without the required PDF was blocked, with no response saved.
4. Uploaded tmp/document-review-local/ITM-00000-client-comments.pdf and submitted. Response 15926 / entity version 21597 saved in state 6146, Approved with Comments. Its file is 1082 (existing identical returned PDF deduplicated). Original review responses 15916/15917 were preserved; 15917 remains Ready for Check.
5. Committed lifecycle event 46d560f8-bd49-4ade-b5d5-77e390a2691f records FromState 6135, ToState 6146, transition 21767 and subscriber target 15795. The source is portal-document-review. Later background processing moved the processed outbox event to retained history; its subscription hit remains available.
6. Dispatched the event twice through the actual SubscriptionEventConsumer/repository: exactly one hit remained. Used the native subscription bundler/delivery job; the helper advanced the bundle evaluation time to avoid waiting for the normal two-minute window. Exactly one in-app notification was delivered and seen in the browser. No email/task channels enabled. Repeated verification after the final repository change retained one hit and the existing notification.
7. In the administrator dialog enabled issued-line subscribers, saved, closed and reopened: checked value persisted. Restored drawing-only scope and saved. Both configuration saves succeeded.
8. Original fixed package/evidence verification passed: share 12 still has A/v1; replacements 13/14 still have B/v3 and ITM-00006 A/v1. Current Library drawing remains B/v5 with file 1083. All five supersedes/resolution pins unchanged. No engineering release performed.

## Verification

- 35 targeted API/unit/isolated-SQL tests passed, zero skipped. Includes direct-state permission filtering, missing attachment, blocked criterion rollback, duplicate submission, corrections, exact-version preservation, disabled permission after load, scoped response routing, authorised recipient filtering, native idempotent hit/bundle creation for all selected channels and transition script identity/parameter dispatch.
- Two Manage seed tests passed, including installed direct-state marker preserving deliberate removals.
- API/Application, client and Manage builds succeeded. Manage test compilation has existing BL0005 component warnings outside this feature. Initial development test/build failures (fixture DDL batching, one test enum qualification, a recipient-filter SQL placement caught by the new native test, and running-service file locks) were corrected before the final passing runs.
- Documentation strict build recorded in completion. Browser screenshots 86–89 are linked from the client workflow guide. The admin screenshot was captured before a subsequent cosmetic cleanup of the dialog's button/apostrophe encoding; functional settings and saved values are the same.

## Limits

Browser test used the current local account with share-group ceiling enforcement, not a second real client account. Actual email/task delivery was not exercised. All three channel rows are covered by the isolated SQL test. Vault technical approval/release and manual reupload/reissue remain the next engineering work; no additional automatic MatStream production-approval gates were added.
