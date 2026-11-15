# Engineering Project Workflow: P0 gap assessment

Assessment date: 14 November 2026. Scope: source review and proposed requirements only. No application implementation, database changes, seed changes or workspace configuration performed. Live workspace validation remains outstanding. Suggested UI locations below are design proposals, not existing screens unless explicitly stated.

## Findings that correct the workflow plan

- RVD review is not entirely missing: Required, Submitted, Accepted and Rejected states/transitions are seeded. Existing entity lifecycle tooling can support decisions; verify Quality permissions and usability rather than build another approval engine.
- File replacement already resets an RVD to Submitted in SqlDocumentRepository. However, it resolves that state by the literal name `Submitted`. Renaming the state through configuration can break the behavior. Administrator-selectable semantic state mappings are needed.
- Current ScriptAutomationService implements bumpRevision, switchLifecycle, setAssignee and notify. The earlier help/plan statement that these helpers are unsupported is stale. This does not establish an arbitrary state-transition action or a PO-line completion evaluator.
- Document Control already seeds issue-purpose options, response fields, response states, per-line comment references and worklists. Missing client review integration must reuse these mechanisms.
- The portal follows current structural/BOM children and current entity versions; it deliberately does not traverse DefinitionLink. Issued document lines pin a definition version. Therefore, transmittal pinning alone does not establish a working revision-correct portal package.

## Cross-cutting requirement: administrator control

Every introduced business setting must be configurable in workspace application UI, with useful editable defaults, labels, descriptions, validation and permission control. Prefer existing Properties, Categories, Lifecycle Definitions, Display Tabs, Print Templates and Automations screens. Add a configuration surface only where those cannot express the requirement.

Persist references to categories, properties, states and groups by identity, rather than interpreting their display names. Renaming a state must not silently disable automation. Scripts may be editable in Automations, but required business settings need discoverable typed controls or validated parameters with descriptions. No SQL edits, special-case source changes or code deployments should be required for an administrator to change business policy.

Production Setup is a one-time initialisation; reruns are for testing and are excluded from this P0 assessment. No seed-tracking or restore feature is proposed. A preview should show affected workflows before changing rules. Apply rule changes prospectively unless an authorised re-evaluation explicitly addresses open records; never silently rewrite issued packages or paid milestones.

Use SQL int / C# int for new numeric identifiers/references where possible; retain PropertyValueID bigint / long. Prefer existing entities/links and avoid duplicating files or entire BOMs solely for the workflow.

## P0-01 — Catalogue, BOM and RVD sample

**Exists:** stock-number mapping, both BOMs, drawings, sample supplier sources, project assembly occurrence and item-specific RVD assignments. Pump ITM-00012 has six seeded requirements; motor ITM-00021 has four.

**Required fix:** support an effective project RVD checklist: Library defaults plus project additions minus authorised exclusions, frozen at the agreed boundary and carried through the MR to each PO line. Preserve provenance, files, other projects and existing frozen orders; later changes require explicit review. See `engineering-project-p0-01.md` for the agreed scope. Implementation remains on hold.

**Validation remaining:** installed workspace consistency, source/version ownership, quantity/unit propagation and clean rehearsal data with intended shortages. No catalogue rebuild is required.

**Administrator settings:** requirement sets per item/version; supply sources and lead times; units/stock units; sample-install selection; category/tab visibility. Use existing Library/RVD and setup tools, adding diagnostics only where absent. Project overrides must not overwrite shared defaults. Keep one enabled RVD occurrence category; multiple-category routing is a documented limitation, not a P0 gap.

**Done when:** a fresh installation resolves the two drawings, preserves quantities/units and materialises exactly the intended PO requirements without duplicates. Explicitly verify which occurrence owns each checklist.

## P0-02 — Accepted quotation to usable project

**Exists:** generic create/place/link automation with pinned Generated From and event retry protection. Source inspection also finds linked-entity creation and property support elsewhere in scripting; do not assume those automatically work for quotation conversion.

**Required fix:** extend the quotation acceptance automation script/action to populate Client, contact and administrator-selected relevant quotation fields on the new project automatically. Provide administrator-editable mappings, defaults, empty-value behavior and validation. Preserve the exact accepted version and transaction/retry protections. See `engineering-project-p0-02.md`. Automatic BOM conversion and full project templates are not required: assembly placement remains step 3. Implementation is on hold.

**Administrator settings:** output category/destination, naming policy, client/contact/currency/owner/date mappings, defaults for absent values, project template/initial areas, line inclusion and assembly placement, whether acceptance creates a project at all. Proposed location: acceptance automation parameters and a reusable project template/mapping editor if existing tools are insufficient.

**Done when:** accepting once creates the intended project and source link, required values/structure are populated, retries do not duplicate it, invalid mappings give actionable errors, and acceptance without project creation remains configurable. Do not silently introduce financial posting.

## P0-03 — Controlled issue and revision preservation

**Agreed disposition:** validation only for now; no assumed development. See `engineering-project-p0-03.md`.

**Exists:** transmittals, Document Issue Lines and Pin Document Package Contents at issue; controlled-document states and response tracking.

**Missing or unverified:** correct document wrappers/definition links for the supplied assembly PDFs, authority to issue, retention protecting referenced versions, production-package composition and behavior when issued contents are edited. Audit existing enforcement before proposing new locks.

**Administrator settings:** category/file model, revision scheme, issue transition/action, required package fields, valid document categories, retention rules and author/issuer permissions. Use existing configuration with an explicit warning/validation for retention incompatible with issued evidence.

**Done when:** revision-0 packages continue opening/printing revision 0 after reissue; incomplete packages cannot issue; authorised corrections use a traceable replacement/revision route. Capture internal and portal evidence separately.

## P0-04 — Client transmittal portal and response

**Agreed workflow:** engineer creates a project Transmittal with properties/purpose; a project-scoped Documents tab provides an engineer-editable filter, checkboxes and Choose columns; administrators configure starting defaults and permissions; selected documents become persistent issue lines; issue pins versions; generate/attach a template-based transmittal PDF; share with named client/department/third-party recipients; receive separate commented PDF returns. Required fixes and administrator controls are detailed in `engineering-project-p0-04.md`. Reuse existing filter/grid/template/PDF mechanisms. No implementation yet.

**Exists:** recipient-scoped sharing, download/upload routes, property allowlists and access checks. Document Control already has package/line response fields. PortalLanding exposes CanChangeState, but the inspected PortalController has no review/decision endpoint.

**Missing:** a demonstrated package resolver that presents pinned Document Issue Line definition versions without exposing the Library; client review UI/API for decisions/comments and returned evidence; exact line/package/version association and internal follow-up. General portal upload is not structured approval.

**Administrator settings:** share profile per package category, exposed properties/document categories, permitted external actions, allowed response outcomes and meanings, response due/default reminders, client reviewer identity/delegation policy, internal owner, comments/attachment requirements and whether conditional approval permits progression. Proposed location: share-profile configuration plus existing document-control properties/lifecycles.

**Done when:** client opens the exact issued revision after the underlying document changes; cannot browse unissued content or unrelated Library items; response records actor/date/package/line/version and attachment evidence; internal owner receives actionable review. Client cannot directly release production by choosing an outcome. Expiry/revocation and wrong-identity tests must pass.

## P0-05 — Early procurement and production release gates

**Exists:** configurable lifecycles, transitions, permission and property criteria, plus generic linked/child-final checks. Seed purposes include For Approval and For Construction; For Work Preparation/For Production are not the same seeded issue-purpose values. Distribution Channel separately includes Work Preparation.

**Missing or unverified:** explicit early-buy authority; release checks combining client decision, comment disposition, approved document baseline and assembly release. Generic “all children final” is not a reliable substitute: final is not synonymous with accepted, and unrelated children may be included.

**Administrator settings:** editable purpose options/semantic mappings, approval outcomes that permit release, required evidence/relationships, authorised approver groups, exception/concession rules and which operations each gate controls. Extend existing transition criteria with narrowly scoped relational checks if necessary, rather than hardcode ITM numbers.

**Done when:** authorised long-lead buying can proceed while fabrication remains blocked; dates/notifications cannot bypass release; missing/conditional/rejected approval produces a clear blocker; changing labels preserves meaning; exceptional overrides are authorised and audited.

## P0-06 — Long-lead classification and winning supplier offer

**Exists:** articles, supplier sources, prices/lead times, documents, MRs and POs. A targeted source search did not establish a dedicated long-lead or supplier-quotation award model; absence of those names alone is not proof that generic configuration cannot support it.

**Required fix:** project-specific supplier/source selection and agreed offer terms must drive MR-to-PO creation, with durable winning-offer references and controlled change handling. Do not silently substitute the current first Library supplier or refresh issued terms. See `engineering-project-p0-06.md`. Long-lead flags and ordinary offer fields/views can use existing configuration; a full tender/scoring engine is not required. Implementation remains on hold.

**Administrator settings:** manual classification versus lead-time threshold, threshold/unit and date basis, project override rights, supplier-offer category/fields, award criteria/approvers, quotation expiry rules, document visibility and copy/reference behavior to MR/PO. Use configurable categories/properties/links/views before proposing a dedicated module.

**Done when:** multiple offers can be retained, one approved choice is identifiable per purchase, the exact offer remains accessible from the PO line, expired/deviating offers require review, and another project's offer/default is preserved.

## P0-07 — Supplier RVD checklist and submission

**Exists:** requirement materialisation, occurrence-owned upload guards, supplier PO portal, Required → Submitted behavior and replacement reset to Submitted.

**Required fixes:** administrator-configurable submission-state mapping by stable identity with pre-commit validation; durable submission/replacement signal identifying evidence, supplier and owning PO line. Reuse existing uploads and coordinate notification routing with P0-09. See `engineering-project-p0-07.md`. Implementation remains on hold. Checklist propagation and actual event publication remain end-to-end validation checks.

**Administrator settings:** submission/review state identities, required document sets/overrides, permitted supplier upload/replacement policy, applicable file constraints and document visibility. Platform resource/security limits stay enforced; business limits can be configurable within them.

**Done when:** correct supplier can submit only to permitted order requirements; replacement resets review even after the state is renamed; exactly one auditable submission event identifies changed evidence. Identical-file resubmission has a defined, tested policy.

## P0-08 — Quality review and documentation completion

**Agreed required fixes:** scoped PO-line documentation aggregate, administrator-configurable acceptance/waiver/empty-checklist semantics, evidence-version/stale-review protection where missing, and re-evaluation after authorised evidence/checklist changes. Accepted is read-only for suppliers using existing state permissions; Quality reopens before supplier corrections. No new supplier-locking mechanism. See `engineering-project-p0-08.md`. Implementation remains on hold.

**Exists:** Accepted/Rejected lifecycle transitions, generic state-change tooling and replacement invalidation to Submitted. Do not implement a duplicate acceptance lifecycle.

**Missing:** proven Quality-only decision permissions/reviewer UX, required review evidence and authoritative per-line aggregate. Waiver handling, no-requirements policy and notification routing are not established by the existing states.

**Administrator settings:** reviewer/waiver groups, accepted/rejected/waived semantic state identities, mandatory reason fields, independent-review policy, required/optional/conditional requirements, zero-requirement handling, hold/reopen policy and checklist freeze/change rules after issue. Proposed UI: existing lifecycle/category configuration plus a documentation-policy editor where relationships/aggregation cannot currently be expressed.

**Done when:** Submitted never counts as Accepted; every mandatory requirement qualifies or has an authorised waiver; replacement/removal/new requirement invalidates the aggregate where applicable; supplier cannot approve its own evidence; decisions retain file/version, reviewer, time and reason.

## P0-09 — Specific notifications and automatic assignment

**Agreed required fixes:** script-first target-state/upload matching, ownership routing and automatic assignment, supported scripting APIs/event context and actual runners where missing, administrator-editable parameters/defaults and clear example scripts. Reuse existing subscription delivery with platform-enforced permissions/durability/deduplication. See `engineering-project-p0-09.md`. Implementation remains on hold.

**Exists:** subscriptions for state/version/revision, assignment to another workspace person with grant, task/email/in-app channels and expiry/first-hit options. Consumer handles lifecycle/version/revision events only. Request DTO has no target-state filter or child-event selector.

**Missing:** selected-target-state notifications, explicit upload/replacement events, routing from RVD to owning PO line, automatic creation/maintenance from project roles, and clear handling of group membership/staff changes.

**Administrator settings:** trigger/event/category, target state identities, relationship scope, project-role/group/user resolution, assignment time, fallback owner, channels, message templates, expiry, first-match/repeat policy, batching and reassignment policy. Proposed UI: reusable notification rules using existing subscription delivery, plus filter controls on individual watches where appropriate.

**Done when:** Sales receives complete-delivery messages rather than every intermediate state; Quality receives each relevant submission/replacement; no missed event merely because upload bypasses lifecycle events; missing owner/access is visible; retry and role changes do not duplicate or silently drop watches.

## P0-10 — Supplier payment milestone plan

**Agreed required fix:** shared Payment Milestone category and Uses tab on Projects (when to invoice clients) and POs (when to pay suppliers), with separate owned records and appropriately configured lifecycles. Editable scripts initialise schedules/evaluate eligibility/request actions; administrator UI configures fields, mappings, schedules, lifecycle selection, rules and permissions. See `engineering-project-p0-10.md`. Implementation remains on hold.

**Exists:** editable Payment Terms, PO values and generic entity/task configuration. No dedicated supplier payment milestone schedule/ledger was established in the inspected code. Customer billing/subscription payment features are unrelated.

**Missing:** structured schedule, eligibility evidence, line/PO aggregation, approval workflow, invoice/payment references and adjustments. Decide how far generic categories/properties can support a first version before selecting storage/UI design.

**Administrator settings:** reusable schedule templates, editable percentages (30/50/20 only a default), basis/currency/tax/rounding policy, PO versus line scope, trigger conditions, due-date calculation, required invoices, approver groups, holds, cancellation/credit behavior and project/PO override rights. Proposed UI: Procurement Payment Plans/Templates with a PO plan editor and milestone view.

**Done when:** percentages/amounts reconcile; whole-PO milestone waits for all applicable lines; paid records cannot be silently recalculated; eligibility, request, approval and actual payment remain distinct; audit supports reversal/correction. No bank execution/accounting posting is implied by this workflow.

## P0-11 — Completion evaluator and script integration

**Agreed required fix:** script-driven technical completion/invalidation combining accepted delivery, P0-08 documentation status and holds; supported evidence APIs/event runners and guarded transitions; business deduplication and P0-10 milestone re-evaluation. See `engineering-project-p0-11.md`. Implementation remains on hold.

**Exists:** transactional lifecycle script action execution, actor/grant checks, task/notification actions, stored automations and run audit/retry foundations. switchLifecycle changes lifecycle; it is not evidence of a generic “transition this PO line to Complete” action.

**Missing:** supported reads of relevant accepted receipt/RVD/hold evidence, scoped relationship traversal/aggregation, guarded target-state transition, persisted completion projection, attachments/runners for every relevant evidence change, and re-evaluation when conditions become false. Existing helpers should be reused.

**Administrator settings:** applicable categories/relationships, required quantity basis/tolerances, delivery/document acceptance mappings, required evidence, blocking hold states, completion target/transition, no-requirements rule, reopen policy, payment notification/action and retry escalation. Proposed UI: typed completion policy bound to an editable script/automation; no hardcoded article IDs or department names.

**Done when:** either arrival order works; partial/quarantined goods do not qualify; replacement/rejection/removal/quantity change re-evaluates; simultaneous/replayed events produce one transition/action; failures are visible; payment requests are held on invalidation. Do not claim exactly-once business behavior merely from same-event deduplication.

## Recommended sequence

1. Validate P0-01/02/03/07 against a practice workspace; establish exact entity/version ownership and correct stale help.
2. Specify administrator settings and semantic identity mappings before seed changes. Separate existing configuration from required new UI.
3. Resolve P0-04 revision-correct portal content and client response contract, alongside P0-05 release authority.
4. Define P0-06 offer/long-lead model and P0-08 documentation acceptance policy.
5. Define P0-09 event/recipient contract using the established ownership model.
6. Define P0-10 milestone records and approval scope.
7. Specify P0-11 evaluator and script API against those agreed facts, then perform end-to-end acceptance design.

This sequencing is a proposal. Implementation remains unauthorised in this task. P1 production timers/readiness and NCR execution remain in the original plan; P0 must expose the facts/events those later features will consume.

## Evidence index

Paths are relative to the repository root. These were read, not executed as tests:

- `MatStream.Web.Manage/Features/Setup/Templates/Parts/EngineeringSampleArticles.cs`, `EngineeringSampleCatalogue.cs`, `PipingEngineeringPart.cs`, `RequiredVendorDocumentsPart.cs`, `DocumentControlPart.cs`, `CommercialTermsPart.cs`.
- `MatStream.API/MatStream.API/Controllers/PortalController.cs`.
- `MatStream.API/MatStream.Infrastructure/Repositories/SqlServer/Tenant/SqlShareRepository.cs`, `SqlDocumentRepository.cs`.
- `MatStream.API/MatStream.Domain/Models/Tenant/Shares/PortalViews.cs`.
- `MatStream.API/MatStream.Application/Services/Tenant/SubscriptionEventConsumer.cs`, `LifecycleService.cs`, `DocumentService.cs`.
- `MatStream.API/MatStream.Web.Shared/Dtos/Subscription/EntitySubscriptionDtos.cs`.
- `MatStream.API/MatStream.Application/Services/Tenant/Scripting/ScriptAutomationService.cs`, `ScriptAutomationService.LifecycleActions.cs`, `ScriptContext.cs`.
- `MatStream.API/MatStream.Infrastructure.Tests/Automation/ScriptLifecycleActionsSqlTests.cs`, `LinkedEntityAutomationSqlTests.cs`, and `Stock/PortalCurrentStructureTests.cs`: test intent inspected; tests not run.
