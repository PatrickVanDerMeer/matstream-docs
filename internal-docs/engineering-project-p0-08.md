# P0-08: Quality review and documentation completion

Source review: 14 November 2026. Planning only; no implementation or live tests. P0-07 state-mapping and submission-signal fixes are agreed required work.

## Agreed decision and required fixes

Configure Accepted as read-only for suppliers through existing lifecycle state permissions; Quality retains the required authority. Supplier replacement of accepted evidence is not the normal path. Quality must first reopen through an authorised configured transition that permits supplier changes. No new supplier-locking mechanism is required. Verify server-side enforcement using the actual external group/share identity.

Remaining required development: authoritative PO-line documentation aggregate over the effective checklist; administrator-configurable acceptance/waiver/empty-checklist semantics; evidence-version association and stale-review protection where standard history is insufficient; and aggregate re-evaluation after authorised reopening, replacement/removal, decisions and checklist changes. Coordinate with P0-01/07/09/11. Implementation remains on hold.

## Existing behavior

- RVD lifecycle seeds Required, Submitted, Accepted and Rejected, including Submitted → Accepted/Rejected and Rejected → Submitted.
- Existing Uses grid wires Change State to the standard entity actions. Review does not require a second approval engine.
- Lifecycle configuration supports group transition permissions, state modification permissions, property criteria, messages and task/notification actions. Explicitly configure Quality rights: seeded states alone do not establish exclusive Quality approval.
- Uploaded evidence has a file/version; changed uploads create a new version and reset to Submitted (state identity correction is P0-07).
- Existing file view/download and history mechanisms provide review foundations; verify target-workspace permissions and retained evidence.

## What the administrator can configure now

Review state labels/transitions; allowed approver groups; state modification/file replacement rights; ordinary review fields and tab columns; rejection reason/comment properties and applicable transition criteria; assignment tasks/worklists. A waiver state and reason fields can be configured, but their meaning in completion evaluation is not established merely by adding a label.

Engineer/Quality users perform review within these rights. No blanket rule that administrators themselves must approve documents is intended.

## Missing 1: authoritative PO-line documentation status

No scoped aggregate was established that evaluates the effective PO-line RVD checklist and reports complete versus missing, submitted, rejected or otherwise blocked. Generic all-children-Final is insufficient: it can include unrelated children, omit applicable project requirements, and interpret terminal states without an explicit acceptance meaning.

Proposed rule: every applicable mandatory requirement has accepted evidence for its current file/version, or an explicit authorised waiver/exclusion under the agreed policy. Respect the project-specific effective checklist from P0-01. Submitted alone never qualifies. Zero requirements must distinguish intentionally no requirements from missing/uninitialised checklist data.

Expose the result and blockers on the PO line, with counts/detail links where useful. Reuse existing fields/views if they can represent the authoritative result. P0-11 consumes this result together with accepted delivery; P0-08 must not automatically mark the whole PO paid or complete.

## Missing 2: evidence-bound decision and invalidation contract

Verify standard transition history supplies actor/date/version and protect decision changes with existing permissions. Extend only missing evidence association: a decision must refer to the file/version actually reviewed, not just a current state label. Prevent a concurrent supplier replacement from leaving the replacement accepted by an old review action.

Replacement reset exists for permitted uploads, but reopening, authorised file removal, requirement addition/exclusion, revised project checklist, reversal of acceptance and waiver changes also need correct aggregate invalidation. The inspected file-removal repository clears file/placeholder fields without a corresponding state reset in that method. An Accepted label must never qualify a requirement whose current file is absent.

Recompute after committed evidence/checklist/decision changes, handle either event order and retries, and retain prior decisions/files according to retention. Coordinate shared event/aggregation infrastructure with P0-07/09/11 rather than implement multiple inconsistent evaluators.

## Optional policy, configurable if included: waiver

An authorised project exclusion before freezing requirements (P0-01) differs from waiving an applicable supplier document during review. Do not treat deleting a row as a waiver. A supported waiver records scope, reason, authorised reviewer and date, with explicit treatment of later evidence/checklist changes. If waivers are not allowed, every mandatory document must be accepted.

## Administrator settings required for new behavior

| Policy | Configuration |
|---|---|
| Acceptance semantics | Select accepted/rejected/pending states by identity; editable labels/descriptions |
| Checklist scope | Applicable RVD categories/relationships and effective project/PO requirements |
| Requirement policy | Mandatory/optional or conditional rules if used; avoid introducing unnecessary complexity for the initial all-mandatory example |
| Review authority | Quality groups, required comments/reasons and any independent-review rule |
| Waivers | Enable/disable, permitted approvers, required reason/evidence and scope |
| Empty checklist | Explicit no-documents-required declaration versus uninitialised/missing checklist |
| Invalidation/review route | Configured target state, authorised reversal and hold/review actions; new evidence cannot silently inherit old acceptance |
| Visibility | PO-line status/blocker fields, review views and default columns |

Use existing category/lifecycle/property/tab UI first; provide typed settings for aggregate semantics where absent. No hardcoded state names, Quality group, requirement count or ITM number. Decision identity, audit integrity and protection against stale-file approval are platform guarantees, not optional switches.

## Development versus configuration

Configure existing review permissions/fields/worklists first. Develop the scoped aggregate, stable semantic mappings, missing evidence-version association/concurrency protection and invalidation integration. Build a dedicated Quality screen only if normal RVD/file/state UI cannot support a usable review process; it is not automatically required.

## Acceptance example

Motor has four effective requirements. Three Accepted plus one Submitted means documentation incomplete. Accept the fourth with correct Quality authority: documentation complete. Supplier cannot replace an accepted file. Quality reopens a requirement: aggregate becomes incomplete; supplier can upload only after the configured permissions allow it, and the replacement requires review. Authorised file removal cannot leave missing evidence qualified by its Accepted label. An authorised waiver follows configured policy and retains its reason. Client/supplier cannot approve their own evidence. Project checklist changes, review/upload races and retries must not produce stale acceptance or duplicate follow-up.

## Sources

`MatStream.Web.Manage/Features/Setup/Templates/Parts/RequiredVendorDocumentsPart.cs`; `MatStream.Web.Assembly/Features/DisplayTabs/UsesDisplayTab.razor`; `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlDocumentRepository.cs`; `MatStream.API/MatStream.Application/Services/Tenant/DocumentService.cs`; `matstream-docs/docs/admin/lifecycle.md`.

## Implemented live documentation result — 16 November 2026

PO lines now expose a live administrator-scripted evaluation of their frozen requirements and accepted current evidence, with blockers and counts. It avoids a cached flag and recalculates on open/refresh, including after removal or replacement. Seed/configuration UI and local motor/pump tests are included. No waiver or payment/technical-completion automation is claimed. See [test evidence](engineering-project-po-documentation-test.md) and [workflow](../docs/workflows/engineering-po-documentation-status.md).
