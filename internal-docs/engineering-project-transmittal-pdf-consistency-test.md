# Transmittal PDF consistency test

14 November 2026. Local workspace Engineering Workflow Test (108/cwt3qp) only. No deployment or external sharing.

## Implemented behavior

Generate Transmittal PDF remains an editable automation script. Its server action now records a SHA-256 content fingerprint and the generated file/version in server-owned evidence. The fingerprint covers saved header name/revision/category/lifecycle state and properties, the configured issue-line category's saved rows/properties/order/quantity/unit, resolved document version/file/revision/state, template metadata/file reference, category assignments, data map and field mappings. It excludes version counters and modification timestamps on entities, which normal version cloning changes. A pinned document remains valid when the Library advances; selecting a different version invalidates it. The check is conservative: editing a saved property can require regeneration even when that property is not printed.

Evidence is committed with the PDF; failed generation/attachment rolls it back. Evidence IDs/references use int, hashes are 64-character SHA-256 hex values, and deleting an entity version cascades deletion of its evidence.

Generated PDF matches saved contents is a predefined lifecycle criterion. Error blocks, Warning permits with a message; enabled state, severity and failure wording are administrator-editable. Predefined rows can now open the existing criterion editor; read-only views cannot edit. Setup seeds Error on every existing enabled route into Issued, then retains subsequent admin changes/removal on rerun. Newly added issue routes need the criterion configured by an administrator.

Transition automations run after the state commit, so the blocking check uses the existing pre-transition criteria mechanism. Criteria are checked again inside the state-write transaction. Fingerprint reads retain database locks through the write to prevent a stale preflight result racing a saved edit.

## Local workflow evidence

- Existing TR-2026-0001 (15914) started at version 6 with an older PDF without evidence.
- Moved to Ready to Issue: version 7. Issue was blocked with Generate the transmittal PDF; state remained Ready to Issue.
- Generated version 8, file 1080, with evidence.
- Corrected Attention to Client engineering review and saved version 9, retaining the old file. Issue was blocked with the changed-header/register/template message.
- Regenerated version 10, file 1081. Issue became available; applying succeeded and produced version 11, state Issued, retaining file 1081. Existing pin-package automation reported no failure.
- Opened the predefined criterion editor in Lifecycle Definitions and saved custom failure wording through the UI, keeping Error severity. SQL confirmed the saved wording.
- Recipient/date completeness and portal access were not asserted by this consistency test. The package was not shared with anyone.

Screenshots: 31 evidence required, 32 stale PDF blocked, 33 successful issue, and administrator criterion editor in the workflow guide.

## Checks

73 API tests passed, including SQL-backed consistency, generation rollback, selected pinned versions, Library advancement, manual replacement, template edits, lifecycle/property criteria and script lifecycle regression coverage. A concurrent SQL edit timed out while the final check held its transaction locks, then succeeded after commit and invalidated the PDF. Two seed tests passed, including preservation of deliberate removal. API and Web Assembly builds passed with zero warnings/errors; Manage test builds retain existing BL0005 warnings. Applied the repeatable evidence migration locally. Documentation strict build passed.

Portal pinned-version access, return uploads, production authority and complete issue-field policy remain separate planned work.
