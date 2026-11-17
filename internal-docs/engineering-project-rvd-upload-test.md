# RVD upload fix — 16 November 2026

## Implementation

Rvd.Upload workspace configuration maps category + lifecycle → state IDs. New admin-only settings API and System Settings dialog validate enabled RVD occurrence category, workspace, assigned lifecycle and state identity. RequiredVendorDocumentsPart seeds Submitted's ID once; Setup.RvdUploads.v1 guards installed customizations. Existing saved configuration is preserved. Missing/invalid mapping blocks changed-file RVD persistence before document/version mutation; DocumentService returns a structured failed upload. File content storage can precede this persistence validation and is covered by the existing unreferenced-file cleanup.

SqlDocumentRepository locks the current document, captures its occurrence owner, attaches/version-controls evidence, updates the configured state and appends document.uploaded in the same transaction. Payload retains document/name/version/file, prior file/version/state, target state and configured notification target IDs. It does not pretend a same-state replacement is a lifecycle transition. Identical stored-file ID returns without extra event/version.

SubscriptionEventConsumer handles trusted rvd-upload events with the existing State watch kind; the subscription dialog labels this State changes / RVD uploads. Configured document and owner targets require read access to both records. Recipient expiry, own-change filtering and replay protection remain in the existing matcher. Delivery rechecks document access before including upload details. Timeline renderer describes Supplier document uploaded and the requirement name. Existing version watchers still receive normal version events; the upload event does not install automatic Quality/Sales subscriptions.

No database migration or new identifiers. State/transition permissions still guard who may modify Accepted evidence. No extra locking or Quality-acceptance engine.

## Local tests

Workspace108 cwt3qp uses category4547/lifecycle1361/submission state6095. Both notification targets enabled. Local defaults installed once and saved successfully through the browser UI. Unrelated lifecycle mapping rejected in browser; valid mapping restored and saved. Screenshot150 is in the public workflow guide.

Native motor PO line16031/RVD16032 and pump PO line16037/RVD16038 each tested first upload, identical no-op and replacement with a temporarily renamed submission state. Two upload events each; document and PO-line state subscriptions matched four hits each; replay produced zero duplicate hits. All evidence, subscriptions, state renames and event probes rolled back. Only the current test account360 and in-app channel were used. These probes use existing valid stored files, not supplier authentication/binary transfer.

Four durable SQL regression cases cover renamed state/replacement/identical upload/atomic rollback, missing-invalid mappings, configured target flags, captured target routing/read authorization, and delivery-time read loss plus rendered notification details. Seed tests cover fresh state-ID defaults, preservation of existing custom configuration, and installed-marker preservation; all three pass. Builds of API, Manage and Web.Assembly pass. Full Infrastructure run:1360 passed/52 skipped/0 failed before the additional delivery test; that added test and all four RVD tests pass afterward. Manage suite:299 passed/0 failed before the two additional seed cases; both additional cases pass. Existing skipped cases are not passing evidence.

Remaining: automatic department subscription scripts, authoritative current-evidence documentation aggregate, payment milestones/final eligibility, and a complete supplier-role browser upload rehearsal with agreed permissions. No emails, invitations, external sharing or deployment were performed.
